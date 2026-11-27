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

> [!warning] `-l` is not the same in every tool
> The row `-l dir` of the table holds for `HLEd` and `HVite` (`-l '*'`). In `HDMan`, `-l dlog` and `-l flog` name a log file. In `HSGen`, `-l` numbers the prompts. Other lower-case options (`-m`, `-n`, `-s`, `-t`, `-b`) also change meaning from tool to tool, so read them per command.

> [!info] Training against recognition (beyond the slides)
> The figure on slide 2 has two boxes. They use two different algorithms from Lectures 13 and 14.
>
> | | Training tools | Recogniser |
> |---|---|---|
> | Input | speech and its transcription | speech only |
> | Output | HMM parameters | a transcription |
> | Algorithm | Baum-Welch (forward-backward): sums over all state paths | Viterbi: keeps the single best path |
> | HTK tool | `HERest` (also `HInit`, `HRest`) | `HVite`, `HDecode` |

> [!question]- Check (slide 2): In the figure, "Transcription" appears twice. What is its role at the top and at the bottom, and what connects the two boxes?
> At the top the transcription is an input: the training tools get speech data together with what was said.
> At the bottom it is the output: the recogniser gets unknown speech and must produce the transcription.
> The two boxes are connected by the trained HMMs. Training estimates their parameters (Baum-Welch), and the recogniser uses them to find the best word sequence (Viterbi).

> [!question]- Check (slide 3): Put each job in its tool group: (a) turn a waveform into MFCCs, (b) flat-start initialisation, (c) forced alignment, (d) compute the WER. Then find the mistake: "Forced alignment needs its own tool, because `HVite` can only recognise."
> (a) Data preparation. (b) Training (it initialises the models). (c) Testing tools: `HVite`. (d) Analysis.
> The statement is wrong. The slide says that the Viterbi recogniser `HVite` can also be used for forced alignment. Both jobs are a Viterbi search. In alignment the word sequence is known, in recognition it is not.

> [!question]- Check (slide 4): Read the figure. Besides the speech, `HVite` gets three inputs. Name them and the tools that make them. What does `HResults` receive?
> 1. The HMMs, made by the training tools (`HCompV`, `HInit`, `HRest`, `HERest`, `HSmooth`, `HHEd`, `HEAdapt`).
> 2. The dictionary, made by `HDMan`.
> 3. The networks (word networks), made by `HBuild` or `HParse`.
> `HResults` receives the transcriptions that `HVite` wrote. It compares them with the reference transcriptions and gives the scores.

> [!question]- Check (slide 5): Read the command `HFoo -C cfg -T 2 -a -s list in1 in2 in3`. Which parts are optional arguments, which are main arguments, and which option takes no value?
> Optional arguments start with a minus sign: `-C cfg`, `-T 2`, `-a`, `-s list`. That is 4, and one of them (`-C cfg`) names a configuration file.
> Main arguments have no minus sign and come last: `in1 in2 in3`, so 3.
> `-a` takes no value: it is followed directly by the next option. It is a switch (on or off). `-T` takes the number 2 and `-s` takes the name `list`.

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

> [!info] What the grammar is for (beyond the slides)
> The grammar (dilbilgisi) lists every sentence the recogniser may output. It plays the role of the language model $P(W)$ from Lecture 15, but in a simple form: a sentence is either allowed or not allowed. The figure on slide 8 and the text file `gram` are the same object. Each path from `sent-start` to `sent-end` in the figure is one allowed sentence. The arrow that goes back over the digits draws `< >` (repeat). A path that goes around a first name draws `[ ]` (optional).

> [!question]- Check (slide 6): The task has only a few words. Why does the tutorial still build phone-based (sub-word) models and not one HMM per word?
> The slide gives the reason: the recogniser "can be easily expanded".
> Phone models are shared by all words. A new word needs only a line in the grammar and a pronunciation in the dictionary. It needs no new recordings of that word.
> With one HMM per word, every new word would need its own training examples.

> [!question]- Check (slide 7): Use the three notations to write a rule for: the word REDIAL, or the word DIAL followed by one or more digits, and at the end an optional PLEASE.
> `( REDIAL | DIAL <$digit> ) [ PLEASE ]`
> The vertical bar gives the two alternatives. The angle brackets repeat `$digit` one or more times. The square brackets make PLEASE optional.
> Round brackets group the alternatives, as in `(PHONE|CALL)` on slide 8.

> [!question]- Check (slide 8): Which of these does the grammar reject: (a) `DIAL`, (b) `CALL YOUNG`, (c) `PHONE STEVE`? Which part of the figure shows why (a) fails?
> (a) Rejected. `<$digit>` means one or more digits. In the figure every path from `dial` to `sent-end` passes through one digit box. The loop back only adds more digits.
> (b) Accepted. `[ STEVE ] YOUNG`: the first name is optional, so the path may go around `Steve`.
> (c) Rejected. Only the first name is in square brackets. The surname `YOUNG` is required.

> [!question]- Check (slide 9): A new name, `[ ADA ] KING`, is added to `$name` in `gram`. What else must be done before the recogniser can output it, and what need NOT be done?
> 1. Run `HParse gram wdnet` again. The recogniser reads the compiled word network `wdnet`, not the text file `gram`.
> 2. Give the new words pronunciations: add ADA and KING to the word list `wlist` and to the names dictionary `names`, then run `HDMan` again to rebuild `dict` (Step 2).
> No new recordings of the words are needed. The models are phone models, and the new words are built from the same phones.
> One condition (an inference from Step 10, not on this slide): every triphone inside the new words must be in `tiedlist`. If one is missing, add it to `fulllist` and run the `HHEd` command of Step 10 again. The decision trees then give it a model, still without new recordings.

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

> [!info] Reading a dictionary entry (beyond the slides)
> The dictionary (sözlük) links the words of the grammar to the phone models. In `WORD [outsym] p1 p2 p3 ...`, `outsym` is optional: it is the symbol printed when the word is recognised. `p1 p2 ...` is the phone sequence. A word with two pronunciations has two lines. Step 8 picks, for each recording, the one that fits best.
> `beep` has no proper names, so the names of the grammar come from the second source dictionary `names`. The log file `dlog` reports statistics, for example words that have no pronunciation.
> In `HSGen`, `-l` numbers the prompts and `-n 200` sets how many are made.

> [!question]- Check (slide 10): In `HDMan -m -w wlist -n monophones1 -l dlog dict beep names`, which files are read and which are written? Is `-l` here the label directory of the options table?
> Read: `wlist` (the words that need a pronunciation), `beep` and `names` (the source dictionaries).
> Written: `dict` (the new dictionary), `monophones1` (the list of phones used, after `-n`), `dlog` (a log file, after `-l`).
> No. In `HDMan`, `-l dlog` names a log file. The label-directory meaning of `-l` belongs to the tools that write label files, such as `HLEd` and `HVite`.

> [!question]- Check (slide 11): Suppose a source entry is `SEVEN s eh1 v n` (the `1` is a stress mark). What does the edit script `global.ded` in the figure make of it, and which later model needs this?
> `AS sp` appends `sp`: `SEVEN s eh1 v n sp`.
> `RS cmu` removes the stress mark: `SEVEN s eh v n sp`.
> `MP sil sil sp` changes nothing, because the entry has no `sil`.
> Result: `SEVEN s eh v n sp`. Every word now ends in the short pause `sp`, the optional pause between words. Its model is built in Step 7. This is also why `sp` is in the phone list `monophones1`.

> [!question]- Check (slide 12): You want 500 test prompts, not 200. What do you change in the command? Can `HSGen` ever write the prompt `DIAL PHONE`, and which steps must be finished before it runs?
> Change `-n 200` to `-n 500`.
> It cannot write `DIAL PHONE`. `HSGen` walks at random through the word network `wdnet`, so every prompt is a path through the grammar. After `DIAL` only digits can follow.
> Step 1 must be finished (`HParse` makes `wdnet`) and Step 2 (`HDMan` makes `dict`), because both files are arguments of the command.

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

> [!question]- Check (slide 13): Read `HLEd -l '*' -d dict -i phones0.mlf mkphones0.led words.mlf`: what is each file? What would go wrong later if the line `DE sp` were removed from the script?
> `-d dict`: the dictionary used to expand words into phones. `-i phones0.mlf`: the output, the phone-level MLF. `mkphones0.led`: the edit script. `words.mlf`: the input, the word-level MLF.
> Every pronunciation in `dict` ends in `sp` (from `AS sp` in Step 2). Without `DE sp`, the label `sp` would stay in `phones0.mlf` after every word.
> Step 6 trains only the models in `monophones0`, which has no `sp`. `HERest` would meet a label for which there is no model.

> [!question]- Check (slide 14): A new prompt is `S0005 CALL STEVE YOUNG`. Write its block in `words.mlf`. Suppose `dict` has `CALL k ao l sp`, `STEVE s t iy v sp`, `YOUNG y ah ng sp`: write its block in `phones0.mlf`.
> `words.mlf`: the name line `"*/S0005.lab"`, then `CALL`, `STEVE`, `YOUNG`, one word per line, then a line with a single dot.
> `EX` gives `k ao l sp s t iy v sp y ah ng sp`.
> `IS sil sil` gives `sil k ao l sp s t iy v sp y ah ng sp sil`.
> `DE sp` gives `sil k ao l s t iy v y ah ng sil`.
> `phones0.mlf`: the name line `"*/S0005.lab"`, these 12 labels one per line, then a dot. The same pattern is on the slide: `sil w ah n v ae ...` for `ONE VALIDATED`, with no `sp` between the words.

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

> [!question]- Check (slide 15): The command `HCopy -T 1 -C config -S codetr.scp` has no main arguments. How does `HCopy` know which files to read and write? Which part of Lecture 9 does this step carry out?
> The file names are in the script file after `-S`. Each line of `codetr.scp` has a source file and its output file, for example a `.wav` and the `.mfc` to write (slide 16).
> `-C config` gives the conversion parameters and `-T 1` sets the trace level.
> The step is the MFCC front end of Lecture 9: pre-emphasis, windowing, mel filterbank, cepstrum. It is done once and the feature vectors are saved, so training does not repeat it.

> [!question]- Check (slide 16): The config is edited to `TARGETRATE = 50000.0`, `WINDOWSIZE = 200000.0`, `NUMCEPS = 10`. Find the frame shift, the frames per second, the window length, and the vector size for `MFCC_0` and for `MFCC_0_D_A`. How many frames does a 3 s file give?
> One HTK time unit is 100 ns $=10^{-7}$ s $=10^{-4}$ ms.
> Frame shift: $50000\times10^{-4}=5$ ms. Frames per second: $1000/5=200$ (the original 10 ms gives 100).
> Window: $200000\times10^{-4}=20$ ms.
> `MFCC_0`: 10 cepstra plus $c_0$ gives $10+1=11$ values. With deltas and accelerations: $11\times3=33$.
> Frames in 3 s: the last window starts at $3000-20=2980$ ms, so $2980/5+1=596+1=597$ frames.

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

> [!info] Why a flat start works, and three options (beyond the slides)
> After `HCompV` all phone models are identical. In the first Baum-Welch pass every model explains every frame equally well, so the frames of an utterance are spread evenly over the phones of its transcription, in order. This rough split is already better than nothing: the frames near the start go mostly to the first phones. Each further pass pulls every model towards the frames of its own phone (Lectures 13 and 14).
> - `-f 0.01` sets the variance floor to 1% of the global variance. No variance may fall below its floor in later passes. This protects a Gaussian that sees little data from a variance near zero.
> - `-m` makes `HCompV` update the means too, not only the variances.
> - `-t 250.0 150.0 1000.0` in `HERest` is the pruning (budama) threshold of the forward-backward pass: start with a beam of 250. If an utterance cannot be aligned, widen the beam by 150 and try again, up to 1000.

> [!warning] Slide detail
> Slide 17 calls `train.scp` a list of the training WAV files. The data coded in Step 5 are the `.mfc` files, and slide 29 describes `test.scp` as a list of MFC files. Read `train.scp` as the list of the coded training files. This is an inference from slides 15, 16 and 29. It agrees with the HTK Book tutorial, where `train.scp` lists the `.mfc` files (beyond the slides).

> [!question]- Check (slide 17): After `HCompV`, the models of `aa` and `s` have exactly the same numbers. How can training ever make them different? And if the global variance of one feature is 0.35, what is its floor with `-f 0.01`?
> The transcriptions make the difference. For each utterance `HERest` joins the phone models in the order of the transcription. A frame early in the utterance counts mostly for the first phones, a late frame for the last phones.
> `aa` and `s` occur in different utterances and positions, so they collect different frames. Their means and variances move apart with every pass.
> Floor: $0.01\times0.35=0.0035$.

> [!question]- Check (slide 18): `proto` says `<NumStates> 5` but has only three `<Mean>`/`<Variance>` blocks. Why? Read row 2 of `<TransP>`. What is the shortest phone this model allows, and how many Gaussian parameters does one model have?
> States 1 and 5 are the entry and exit states. They emit nothing, so they have no Gaussian. Only states 2, 3 and 4 emit.
> Row 2 is `0.0 0.6 0.4 0.0 0.0`: from state 2 the model stays with probability 0.6 and moves to state 3 with probability 0.4. There is no skip to state 4.
> Without skips every path visits states 2, 3 and 4 at least once: 3 frames, which is $3\times10=30$ ms at a 10 ms shift.
> One state has 39 means and 39 variances, $39+39=78$. Three states: $3\times78=234$ parameters (plus the transition matrix).

> [!question]- Check (slide 19): Find the mistake: "For the second pass, run the same `HERest` command again." Also, why is the model list `monophones0` and not `monophones1`?
> The same command would read `hmm0` again and write the same result into `hmm1`. Nothing would improve.
> For the second pass change the model arguments: `-H hmm1/macros -H hmm1/hmmdefs -M hmm2`. The third pass reads `hmm2` and writes `hmm3`.
> `monophones0` has no `sp`. There is no `sp` model yet (it is made in Step 7), and `phones0.mlf` has no `sp` labels (`DE sp` in Step 4).

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

> [!info] Reading the two commands of Steps 7 and 8 (beyond the slides)
> `AT i j p` also rescales the old transitions out of state $i$ by $1-p$, so that the row still sums to 1.
> In the `HVite` command of Step 8, `-a` switches on forced alignment (zorlamalı hizalama). `HVite` then does not search the grammar. For each utterance it builds a small network from the known words (`-I words.mlf`), with all dictionary pronunciations of a word in parallel, and runs Viterbi through it. `-i aligned.mlf` is the output MLF. `-m` writes phone-level (model-level) labels. `-o SWT` leaves scores, word names and times out of the output labels.
>
> | | Baum-Welch (`HERest`) | Viterbi (`HVite`) |
> |---|---|---|
> | Paths | sum over all state paths | the single best path |
> | Frame to state | soft (posterior probabilities) | hard (one state per frame) |
> | Used for | re-estimating parameters | alignment and recognition |

> [!question]- Check (slide 20): Suppose row 2 of the `sil` transition matrix is still 0.6 (stay) and 0.4 (to state 3). What is the row after `AT 2 4 0.2 {sil.transP}`? Why is `sp` tied to the centre state of `sil` and not trained alone?
> The new transition 2 to 4 gets 0.2. The old entries are scaled by $1-0.2=0.8$.
> Stay: $0.6\times0.8=0.48$. To state 3: $0.4\times0.8=0.32$. To state 4: $0.2$. Sum: $0.48+0.32+0.2=1$.
> `sp` can be skipped, so it sees only the few frames of real pauses between words. Tied to the centre state of `sil`, it shares one output distribution with it and is trained on all the silence (sessizlik) data.

> [!question]- Check (slide 21): In the figure the HMM list is `monophones1`. In Step 6 it was `monophones0`. Why the change? What does the box "Edit sil -> sp" stand for, and which directory holds the models after the first of the two `HERest` passes?
> `monophones1` contains `sp`. From `hmm4` on, the model set has an `sp` model, so the list must name it.
> "Edit sil -> sp" is the manual edit: copy the centre state of `sil` into a new one-state model `sp` in `hmmdefs`, and save the result as `hmm4`.
> `HHEd` writes `hmm5`. The first `HERest` pass writes `hmm6`, the second writes `hmm7`. The figure shows only `hmm5` and `hmm7`.

> [!question]- Check (slide 22): Which parts of the Step 8 command make it an alignment and not a recognition run? What does `aligned.mlf` contain that `phones0.mlf` did not?
> `-a` (align) together with `-I words.mlf`: the word sequence of each utterance is given. In recognition (Step 11) there is no `-a`. `HVite` gets the word network with `-w wdnet` and must find the words itself.
> `phones0.mlf` was made by `HLEd` from the dictionary alone, without looking at the audio.
> `aligned.mlf` is made with the audio: for a word with several pronunciations it holds the one with the best Viterbi score in that recording. It can also contain `sp` between words, because the `sp` model now exists.
> The training passes that follow (`hmm8`, `hmm9`) use these better transcriptions. Slide 22 does not say this directly. It is implied by slide 25, where `aligned.mlf` is the starting point of Step 9.

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

> [!info] Why triphones, and why they must be tied (beyond the slides)
> A phone sounds different depending on its neighbours (coarticulation, Lecture 15). A triphone is a model of one phone in one left and right context. It is still a model of ONE phone, not of three.
> The cost is the number of models. With 40 phones there are $40^3=64{,}000$ possible triphones. Most of them never occur in a small corpus, and many others occur only a few times.
>
> | | Monophone | Triphone | Tied-state triphone |
> |---|---|---|---|
> | One model per | phone | phone in a context | phone in a context |
> | Own parameters per state | yes | yes | no: a state is shared by many |
> | Data per state | plenty | often too little | pooled, enough |
>
> Slide 23 gives the plan in two parts: Step 9 makes the triphones, Step 10 ties similar states "to ensure robust estimation".

> [!question]- Check (slide 23): Convert the labels `n ay n sp ow sp` (NINE OH) with `mktri.led`. What goes into `wintri.mlf`, and which names does this add to the list `triphones1`?
> `sp` is a word boundary, so each word is converted alone.
> NINE: `n+ay`, `n-ay+n`, `ay-n`.
> OH has one phone. It has no neighbour inside the word, so it stays `ow`. The slides show no one-phone word. This follows from the rule that a context never crosses a word boundary (an inference).
> `wintri.mlf`: `n+ay n-ay+n ay-n sp ow sp`.
> `triphones1` (the `-n` output) lists each different model name once: `n+ay`, `n-ay+n`, `ay-n`, `sp`, `ow`.

> [!question]- Check (slide 24): Right after `HHEd` (in `hmm10`), how do the models `n+ay` and `n-ay+n` compare with their monophones `n` and `ay`? Why does the `HERest` command use `wintri.mlf` and `triphones1`? What breaks in Step 10 if `-s stats` is left out?
> `n+ay` is an exact copy of the monophone `n`, and `n-ay+n` is an exact copy of `ay`. Cloning only copies. The triphones become different in the two `HERest` passes.
> The transition matrices of all triphones of one phone stay tied. The output distributions are separate until Step 10.
> The labels and the model names must match. The models are now called `n+ay`, ..., so the transcriptions must use these names (`wintri.mlf`), and the model list must be `triphones1`.
> `stats` holds the state occupation counts. `tree.hed` starts with `RO 100 stats`. Without the file the clustering cannot check how much data each cluster has.

> [!question]- Check (slide 25): Find the mistake: "In the figure, `HLEd` turns the monophone models into triphone models." Which model directory is missing in the figure?
> `HLEd` edits label files. It is on the right branch: `aligned.mlf` to `wintri.mlf` (transcriptions).
> The models are on the left branch: `HHEd` turns `hmm9` into `hmm10`.
> The two branches meet at `HERest (x2)`, which needs both the models and the matching transcriptions. It has two outputs: the models `hmm12` and the state occupation file `stats`.
> Missing: `hmm11`, the result of the first of the two passes.

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

> [!warning] Slide error
> In the figure on slide 28 the re-estimation box reads "HREST (x2)". The tool is `HERest`, as the text of slide 27 says ("Repeat HERest twice more"). `HRest` is a different tool: it re-estimates one model at a time from segmented data.

> [!info] The decision tree in one picture (beyond the slides)
> A decision tree (karar ağacı) is built for each phone and state position, for example for state 2 of all `ah` triphones. Each node asks a yes/no question about the context, such as "is the left phone a nasal?" (`L_Nasal`). Each leaf is one tied state.
> A triphone that never occurred in training can still answer the questions. For example `ng-ah+k`: the left context `ng` is a nasal, so it goes to the "yes" side, and so on down to a leaf. It gets the tied state of that leaf. This is how the recogniser gets models for words that were never in the training data.

> [!question]- Check (slide 26): A phone set has 45 phones. How many triphones are possible? With 3 emitting states and one diagonal Gaussian over 39 features per state, how many Gaussian parameters would untied triphones need? What does the slide say tying is for?
> Triphones: $45^3=45\times45\times45=91{,}125$.
> States: $91{,}125\times3=273{,}375$.
> Parameters per state: $39+39=78$. Total: $273{,}375\times78=21{,}323{,}250$, about 21 million.
> A small corpus cannot estimate that many numbers. Tying lets the states "share data and make robust parameter estimates": one distribution is trained on the pooled frames of many similar states.

> [!question]- Check (slide 27): Three model lists appear in Steps 9 to 11: `triphones1`, `fulllist`, `tiedlist`. What does each one hold, and which one does the recogniser use?
> `triphones1`: the triphones that occur in the training transcriptions (output of `HLEd` in Step 9).
> `fulllist`: all triphones of the whole dictionary, seen or not (the `-n` output of this `HDMan` run, where `global.ded` holds the command `TC`).
> `tiedlist`: the final model list after tying. It covers the triphones of `fulllist`, and models that became identical are merged.
> The last two `HERest` passes and the recogniser in Step 11 use `tiedlist`.

> [!question]- Check (slide 28): Three states in one cluster have occupation counts of 40, 35 and 60 frames. With `RO 100 stats`, may a question split them into {40, 35} and {60}? What do the three arrows into `HHEd` and the two arrows out of it carry?
> No. The two sides would have $40+35=75$ and $60$ frames. Both are below 100, so the split is not allowed.
> No other split works either: two clusters of at least 100 frames each need $100+100=200$ frames, and there are only $40+35+60=135$.
> The three states stay one tied state, trained on these 135 frames.
> In: the triphone models (`hmm12`), the state occupation counts (`stats`), the edit script (`tree.hed`).
> Out: the tied-state models (`hmm13`) and the new HMM list (`tiedlist`), which the re-estimation box below then uses.

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

> [!question]- Check (slide 29, command): In the `HVite` command, which earlier step made each of `wdnet`, `dict`, `hmm15` and `tiedlist`? The output has many extra short words. In which direction should `-p` move, and why?
> `wdnet`: Step 1 (`HParse`). `dict`: Step 2 (`HDMan`). `hmm15` and `tiedlist`: Step 10 (tying, then two `HERest` passes). The files in `test.scp` are the test recordings coded as in Step 5.
> Extra words are insertions. The score of a sentence with $K$ words contains the term $K\cdot p$.
> Make $p$ negative (below the 0.0 on the slide). Then every word costs something, and sentences with many short words lose score. If $p$ is too negative, words start to be deleted.

> [!question]- Check (slide 29, scoring): Two systems are scored on $N=250$ reference words. System A: $D=5$, $S=15$, $I=20$. System B: $D=10$, $S=20$, $I=2$. Find %Correct and Accuracy for both. Which system is better?
> A: %Correct $=(250-5-15)/250=230/250=92\%$. Accuracy $=(250-5-15-20)/250=210/250=84\%$.
> B: %Correct $=(250-10-20)/250=220/250=88\%$. Accuracy $=(250-10-20-2)/250=218/250=87.2\%$.
> A wins on %Correct, B wins on Accuracy. B is better: its word error rate (kelime hata oranı) is $32/250=12.8\%$ against $40/250=16\%$ for A.
> %Correct hides the 20 insertions of A.

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
- Quiz: [[CS568 L21 - HTK recogniser tutorial - quiz]]

Slides not embedded: 1
