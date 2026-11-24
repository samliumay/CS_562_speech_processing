---
course: CS 568 Speech Processing
lecture: 21
title: HTK recogniser tutorial
tags: [cs568, lecture]
slides: lectures/lecture_21/l21.pdf
full_notes: lecture_notes/lecture_21/CS568_Lecture_21_HTK.pdf
---
# CS 568 · Lecture 21 · HTK: building an HMM recogniser

> [!summary] In short
> - HTK is a set of command-line tools for HMMs, in four groups: data preparation, training, testing, analysis.
> - The tutorial builds a voice-dialling recogniser in 11 steps. Steps 1-5 prepare data, 6-8 train monophones, 9-10 build tied-state triphones, 11 tests.
> - Training starts flat (every phone gets the global mean and variance) and uses embedded Baum-Welch, so no phone boundaries are needed.
> - Triphones are cloned from monophones, then their states are tied with phonetic decision trees, which also give models to unseen triphones.
> - Scoring: Accuracy $=(N-D-S-I)/N$ and WER $=(S+D+I)/N$. Do not judge by %Correct.

## 1. What HTK is and how its tools are called (slides 2-5)
![[cs568_lecture_21_l21_p002.jpg]]
![[cs568_lecture_21_l21_p003.jpg]]
![[cs568_lecture_21_l21_p004.jpg]]
![[cs568_lecture_21_l21_p005.jpg]]

**Notes.** HTK estimates HMM parameters from training utterances (speech plus transcriptions) and transcribes unknown utterances (speech only).

| Group | Job | Tools |
|---|---|---|
| Data preparation | waveforms to features, transcriptions to label format | `HSLab`, `HCopy`, `HLEd`, `HDMan`, `HParse` |
| Training | define topology, initialise, train, tie, adapt | `HCompV`, `HInit`, `HRest`, `HERest`, `HHEd` |
| Testing | Viterbi recogniser and forced alignment, large-vocabulary decoder | `HVite`, `HDecode` |
| Analysis | score the output | `HResults` |

Every tool takes optional arguments (with a minus sign) and main arguments. Options with the same meaning everywhere:

| Option | Meaning |
|---|---|
| `-T n` | trace level |
| `-C cfg` | configuration file |
| `-S file.scp` | script file: the list of data files |
| `-I file.mlf` | master label file with transcriptions |
| `-H file` | load HMM definitions |
| `-M dir` | directory for the updated HMMs |
| `-l dir` | output directory for label files |

> [!warning] Watch out
> Training never overwrites models. Each pass reads `hmmK` and writes `hmm(K+1)`, from `hmm0` to `hmm15`. Case matters: `-S` (script file) is not `-s`.

## 2. The task and Step 1: grammar (slides 6-9)
![[cs568_lecture_21_l21_p006.jpg]]
![[cs568_lecture_21_l21_p007.jpg]]
![[cs568_lecture_21_l21_p008.jpg]]
![[cs568_lecture_21_l21_p009.jpg]]

**Notes.** Task: voice dialling ("Dial nine zero four one oh nine", "Phone Woodland"). The models are sub-word units: Gaussian-mixture tied-state triphones, clustered with decision trees.

Grammar notation: `|` alternatives, `[ ]` optional, `< >` one or more repetitions, `( )` grouping.

```text
$digit = ONE | TWO | THREE | FOUR | FIVE |
         SIX | SEVEN | EIGHT | NINE | OH | ZERO;
$name  = [ JOOP ] JANSEN | [ JULIAN ] ODELL | [ DAVE ] OLLASON |
         [ PHIL ] WOODLAND | [ STEVE ] YOUNG;
( SENT-START ( DIAL <$digit> | (PHONE|CALL) $name) SENT-END )
```

`HParse` compiles the grammar file into the word network the recogniser needs:

```bash
HParse gram wdnet
```

## 3. Steps 2 and 3: dictionary and recording (slides 10-12)
![[cs568_lecture_21_l21_p010.jpg]]
![[cs568_lecture_21_l21_p011.jpg]]
![[cs568_lecture_21_l21_p012.jpg]]

**Notes.** Step 2. Make a sorted word list `wlist` (training prompts plus test vocabulary). `HDMan` looks each word up in the BEEP dictionary `beep` and in `names`, and writes `dict` and the phone list `monophones1`:

```bash
HDMan -m -w wlist -n monophones1 -l dlog dict beep names
```

Each entry has the form `WORD [outsym] p1 p2 p3 ...`. The edit script `global.ded` holds three commands: `AS sp` appends a short pause `sp` to every pronunciation, `RS cmu` removes stress marks, `MP sil sil sp` merges `sil sp` into one `sil`.

Step 3. `HSGen` walks the word network at random and writes numbered prompts. Then record the training files (`S0001.wav`, ...) and test files (`T0001.wav`, ...) with `HSLab` or any recorder.

```bash
HSGen -l -n 200 wdnet dict > testprompts
```

## 4. Step 4: transcription files (slides 13-14)
![[cs568_lecture_21_l21_p013.jpg]]
![[cs568_lecture_21_l21_p014.jpg]]

**Notes.** A **master label file (MLF)** holds all transcriptions in one file: per utterance a name line (`"*/S0001.lab"`), one label per line, and a line with a single dot.

```bash
prompts2mlf words.mlf trainingprompts
HLEd -l '*' -d dict -i phones0.mlf mkphones0.led words.mlf
```

The first command makes the word-level MLF. The second makes the phone-level MLF with the edit script `mkphones0.led`:

```text
EX
IS sil sil
DE sp
```

`EX` replaces each word by its pronunciation from `dict`. `IS sil sil` inserts `sil` at the start and at the end of every utterance. `DE sp` deletes the short pauses, because the `sp` model does not exist until Step 7.

## 5. Step 5: coding the data (slides 15-16)
![[cs568_lecture_21_l21_p015.jpg]]
![[cs568_lecture_21_l21_p016.jpg]]

**Notes.** `HCopy` turns each `.wav` listed in `codetr.scp` into an `.mfc` file of MFCCs:

```bash
HCopy -T 1 -C config -S codetr.scp
```

Reading `config` (HTK counts time in units of 100 ns):

| Parameter | Meaning |
|---|---|
| `TARGETKIND = MFCC_0` | MFCCs plus $c_0$ (energy): 13 values per frame |
| `TARGETRATE = 100000.0` | frame shift 10 ms |
| `WINDOWSIZE = 250000.0` | window 25 ms |
| `USEHAMMING = T` | Hamming window |
| `PREEMCOEF = 0.97` | pre-emphasis $y[n]=x[n]-0.97\,x[n-1]$ |
| `NUMCHANS = 26` | 26 mel filterbank channels |
| `NUMCEPS = 12`, `CEPLIFTER = 22` | 12 cepstra, liftering 22 |
| `ENORMALISE = F` | no energy normalisation |

In training, `_D_A` adds $\Delta$ and $\Delta^2$: $13\times3=39$ values, hence `<VecSize> 39 <MFCC_0_D_A>` in the prototype.

> [!example] Frames in an utterance
> 1 s of speech, 25 ms window, 10 ms shift: the last window starts at 970 ms, so $970/10+1=98$ frames.

## 6. Step 6: flat-start monophones (slides 17-19)
![[cs568_lecture_21_l21_p017.jpg]]
![[cs568_lecture_21_l21_p018.jpg]]
![[cs568_lecture_21_l21_p019.jpg]]

**Notes.** A **flat start** gives every phone the same parameters: the global mean and variance of all training data. It is used when there are phone transcriptions but no phone boundaries.

1. Write the prototype `proto`: 5 states, of which 3 emit (states 2 to 4), left to right, no skips, 39-dimensional vectors.
2. `HCompV` computes the global mean and variance, writes them into the prototype, and writes a variance floor (`-f 0.01`):

```bash
HCompV -C config -f 0.01 -m -S train.scp -M hmm0 proto
```

3. By hand make `hmm0/macros` (global options plus variance floor) and `hmm0/hmmdefs` (one copy of `proto` per phone, including `sil`).
4. Re-estimate with `HERest`, three times (`hmm1`, `hmm2`, `hmm3`):

```bash
HERest -C config -I phones0.mlf -t 250.0 150.0 1000.0 -S train.scp \
       -H hmm0/macros -H hmm0/hmmdefs -M hmm1 monophones0
```

`monophones0` is `monophones1` without `sp`. `HERest` does **embedded** Baum-Welch: for each utterance it joins the phone HMMs of the transcription into one long HMM and runs forward-backward over the whole utterance. Each frame counts for each state in proportion to its posterior. That is why no boundaries are needed.

In `<TransP>`, row $i$ column $j$ is $a_{ij}$. A state with self-loop $a$ lasts $1/(1-a)$ frames on average.

## 7. Steps 7 and 8: silence models and realignment (slides 20-22)
![[cs568_lecture_21_l21_p020.jpg]]
![[cs568_lecture_21_l21_p021.jpg]]
![[cs568_lecture_21_l21_p022.jpg]]

**Notes.** Step 7. Add by hand a one-state `sp` model (a copy of the centre state of `sil`) and save as `hmm4`. Then run `HHEd` with `sil.hed`, and `HERest` twice (`hmm6`, `hmm7`):

```bash
HHEd -H hmm4/macros -H hmm4/hmmdefs -M hmm5 sil.hed monophones1
```

```text
AT 2 4 0.2 {sil.transP}
AT 4 2 0.2 {sil.transP}
AT 1 3 0.3 {sp.transP}
TI silst {sil.state[3],sp.state[2]}
```

`AT i j p` adds a transition from $i$ to $j$ with probability $p$. `TI` ties states into one shared state. The backward transition 4 to 2 lets `sil` absorb long or impulsive noise. The transition 1 to 3 in `sp` goes from entry to exit, so `sp` can be skipped: an optional pause. Tying trains `sp` on all the silence data.

Step 8. **Forced alignment**: the recogniser gets the known words and only chooses, for each word, the dictionary pronunciation that fits the acoustics best, with the phone boundaries.

```bash
HVite -l '*' -o SWT -b silence -C config -a -H hmm7/macros \
      -H hmm7/hmmdefs -i aligned.mlf -m -t 250.0 -y lab \
      -I words.mlf -S train.scp dict monophones1
```

Then `HERest` twice (`hmm8`, `hmm9`).

> [!warning] Watch out
> Before Step 8 add the line `silence sil` to `dict`. The option `-b silence` puts the word `silence` at both ends of each utterance, and every word needs a pronunciation.

## 8. Step 9: triphones from monophones (slides 23-25)
![[cs568_lecture_21_l21_p023.jpg]]
![[cs568_lecture_21_l21_p024.jpg]]
![[cs568_lecture_21_l21_p025.jpg]]

**Notes.** HTK writes a triphone as `l-p+r`: phone `p` after `l`, before `r`. Three commands (slide 25 shows the flow):

```bash
HLEd -n triphones1 -l '*' -i wintri.mlf mktri.led aligned.mlf
HHEd -B -H hmm9/macros -H hmm9/hmmdefs -M hmm10 mktri.hed monophones1
HERest -B -C config -I wintri.mlf -t 250.0 150.0 1000.0 -s stats \
       -S train.scp -H hmm11/macros -H hmm11/hmmdefs -M hmm12 triphones1
```

1. `HLEd` with `mktri.led` (`WB sp`, `WB sil`, `TC`) converts the phone transcriptions to triphones. `WB` marks `sp` and `sil` as word boundaries, so the triphones are **word-internal**: a phone at a word edge becomes a biphone (`p+r` or `l-p`).
2. `HHEd` with `mktri.hed` clones each monophone into its triphones (`CL`) and ties the transition matrices of all triphones of one phone (`TI`).
3. `HERest` twice (`hmm11`, `hmm12`). The second pass writes the state occupation statistics `stats` for Step 10.

> [!example] Conversion
> `sil k ae t sp` becomes `sil k+ae k-ae+t ae-t sp`.

## 9. Step 10: tied-state triphones (slides 26-28)
![[cs568_lecture_21_l21_p026.jpg]]
![[cs568_lecture_21_l21_p027.jpg]]
![[cs568_lecture_21_l21_p028.jpg]]

**Notes.** A **tied state** is one output distribution shared by the same state position of several triphones of one phone. Their data is pooled.

```bash
HDMan -b sp -n fulllist -g global.ded -l flog beep-tri beep
HHEd -B -H hmm12/macros -H hmm12/hmmdefs -M hmm13 tree.hed triphones1 > log
```

First `HDMan` lists every triphone of the whole dictionary (`fulllist`), not only those in the training set, because test words may need unseen triphones. Then `HHEd` runs `tree.hed`, which has four parts:

1. `RO 100 stats`: load the occupation statistics. No cluster may have fewer than 100 frames.
2. `QS` questions about the context, for example `QS "L_Nasal" {m-*,n-*,ng-*}`.
3. One `TB` per phone and state, for example `TB 350 "ST_ah_2_" {(ah,*-ah,*-ah+*,ah+*).state[2]}`: start with all those states in one cluster, split with the question that raises the log-likelihood most, stop when the gain is below 350 or the occupancy rule would break. Each leaf is one tied state. See [[CS568 L15 - Large vocabulary speech recognition]] for the theory.
4. `AU "fulllist"` builds models for unseen triphones by walking down the trees. `CO "tiedlist"` merges identical models. `ST "trees"` saves the trees.

Then `HERest` twice (`hmm14`, `hmm15`).

> [!warning] Slide detail
> Slide 27 lists `tiedlist` and `trees` under the `HDMan` command. They are outputs of the `HHEd` run with `tree.hed`.

## 10. Step 11: recognition and scoring (slide 29)
![[cs568_lecture_21_l21_p029.jpg]]

**Notes.**

```bash
HVite -C config -H hmm15/macros -H hmm15/hmmdefs -S test.scp \
      -l '*' -i recout.mlf -w wdnet -p 0.0 -s 5.0 dict tiedlist
HResults -I testref.mlf tiedlist recout.mlf
```

The config contains `FORCECXTEXP = T` and `ALLOWXWRDEXP = F`: expand to triphones, but not across words, to match the word-internal training. `HVite` ranks a word sequence $W$ with $K$ words by

$$\log p(\text{acoustics}\mid W)+s\cdot\log P(W)+K\cdot p$$

with $s$ the grammar scale (`-s`) and $p$ the word insertion penalty (`-p`, a log value per word).

`HResults` aligns output and reference by minimum edit distance and counts substitutions $S$, deletions $D$, insertions $I$ out of $N$ reference words:

$$\%\text{Correct}=\frac{N-D-S}{N}\times100,\qquad \text{Accuracy}=\frac{N-D-S-I}{N}\times100,\qquad \text{WER}=\frac{S+D+I}{N}\times100$$

> [!example] Scoring one utterance
> Reference `DIAL OH NINE FOUR` ($N=4$), output `DIAL OH FIVE FOUR FOUR`. One substitution (NINE/FIVE), one insertion (FOUR). %Correct $=3/4=75\%$, Accuracy $=2/4=50\%$, WER $=50\%$.

> [!warning] Watch out
> %Correct ignores insertions. Report Accuracy or WER. Accuracy can be negative and WER can exceed 100%. Tune `-p` and `-s` on development data, not on the test set.

> [!info] Adaptation
> The outline on slide 1 lists "Adapting the HMMs", but the slides end with Step 11. Our notes add a sketch: MLLR moves all means of a class by one shared affine map $\boldsymbol\mu'=A\boldsymbol\mu+\mathbf b$, estimated with `HERest` from a few sentences of the new speaker.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| `HParse gram wdnet` | grammar to word network |
| `HDMan` | builds `dict` and the phone list from `wlist` and `beep` |
| MLF | master label file: all transcriptions in one file |
| `HLEd` | edits label files (`EX`, `IS sil sil`, `DE sp`, `TC`) |
| `HCopy` | waveform to MFCC: 25 ms window, 10 ms shift, 13 values, 39 with $\Delta,\Delta^2$ |
| Flat start | all phones start with the global mean and variance (`HCompV`) |
| `HERest` | embedded Baum-Welch re-estimation |
| `HHEd` | edits HMMs: `AT`, `TI`, `CL`, `TB`, `AU` |
| Forced alignment | `HVite -a`: known words, find pronunciations and boundaries |
| `l-p+r` | triphone, word-internal in this tutorial |
| Tied state | one output distribution shared by several states |
| Scores | %Correct $=\frac{N-D-S}{N}$, Accuracy $=\frac{N-D-S-I}{N}$, WER $=\frac{S+D+I}{N}$ |

## Check yourself
> [!question]- 1. Is "PHONE JULIAN ODELL" allowed by the grammar? "DIAL OH OH OH"?
> Both are allowed. PHONE is one of `(PHONE|CALL)` and `[ JULIAN ] ODELL` is a `$name`. `<$digit>` allows one or more digits, and OH is a digit.

> [!question]- 2. In `proto`, the self-loop of state 4 is changed to 0.8. What is the expected stay in state 4?
> $1/(1-0.8)=5$ frames, which is 50 ms at a 10 ms shift.

> [!question]- 3. Why can `sp` be skipped while `sil` cannot?
> `sp` has a transition from entry to exit (`AT 1 3 0.3`), so a path can pass without emitting a frame. `sil` has no such arc: every path emits frames in its states.

> [!question]- 4. Why must `fulllist` cover the whole dictionary and not only the training data?
> Test words may need triphones never seen in training. `AU "fulllist"` makes models only for the triphones in the list. A missing triphone has no model, and `HVite` cannot build the network.

> [!question]- 5. $N=200$, $S=6$, $D=4$, $I=10$. Find %Correct, Accuracy and WER.
> %Correct $=190/200=95\%$. Accuracy $=180/200=90\%$. WER $=20/200=10\%$.

> [!question]- 6. Why must `ALLOWXWRDEXP` be `F` for these models?
> The models were trained as word-internal triphones, with biphones at word edges. Cross-word expansion would ask for triphones across word boundaries that were never trained in that role.

## Links
- Course index: [[CS568 Home]]
- Theory behind triphones, state tying, decoding and WER: [[CS568 L15 - Large vocabulary speech recognition]]
- Quizzes and labs that use this lecture: none (the midterm booklet covers Lectures 1 to 6).
- Full step-by-step notes (PDF): [CS568_Lecture_21_HTK.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_21/CS568_Lecture_21_HTK.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_21/CS568_Lecture_21_HTK.pdf`
- Original slides (PDF): [l21.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_21/l21.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_21/l21.pdf`

Slides not embedded: 1
