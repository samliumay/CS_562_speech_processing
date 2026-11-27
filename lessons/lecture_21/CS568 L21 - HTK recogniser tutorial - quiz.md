---
course: CS 568 Speech Processing
lesson: CS568 L21 - HTK recogniser tutorial
type: quiz
questions: 29
---

# CS568 L21 - HTK recogniser tutorial - quiz

Lesson note: [[CS568 L21 - HTK recogniser tutorial]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. What HTK is and how its tools are called (slides 2-5)

### Q1 (slide 2)

![[cs568_lecture_21_l21_p002.jpg]]

In the figure, the word "Transcription" appears twice: once at the top, once at the bottom. Which statement about the two places, and about what connects the two boxes, is correct?

- A) Top: an input to the training tools. Bottom: a second input, which the recogniser needs. The dictionary connects the two boxes.
- B) Top: an input to the training tools. Bottom: the output of the recogniser. The trained HMMs connect the two boxes.
- C) Top: an input to the training tools. Bottom: a second input, which the recogniser needs. The trained HMMs connect the two boxes.
- D) Top: an input to the training tools. Bottom: the output of the recogniser. The dictionary connects the two boxes.

> [!success]- Answer
> **B.** The arrow at the top goes into the training tools: training needs the speech together with what was said.
> The arrow at the bottom comes out of the recogniser: it gets unknown speech and must produce the transcription.
> The HMMs drawn between the boxes are the link. Training estimates their parameters (Baum-Welch), and the recogniser uses them to find the best word sequence (Viterbi).
> - A: Two slips: the bottom arrow points out of the recogniser (it is the output), and the figure draws the HMMs between the boxes, not a dictionary.
> - C: The arrow at the bottom points away from the recogniser. A recogniser that needed the transcription of unknown speech would have nothing left to find.
> - D: The roles are right, but the figure draws the HMMs between the boxes. What the recogniser takes from training is the models.

### Q2 (slide 3)

HTK groups its tools into four groups: data preparation, training, testing and analysis. A student says: "Forced alignment needs its own tool, because `HVite` can only recognise." Which statement is correct?

- A) Forced alignment belongs to the testing tools. `HVite` does it, so the student is wrong.
- B) Flat-start initialisation belongs to data preparation, because it only computes global statistics.
- C) Computing the WER belongs to the testing tools, because `HVite` scores its own output.
- D) Turning a waveform into MFCCs belongs to training, because `HCompV` reads the features.

> [!success]- Answer
> **A.** Slide 3 says that the Viterbi recogniser `HVite` "can also be used for forced alignment". Both jobs are a Viterbi search; in alignment the word sequence is known, in recognition it is not.
> The full grouping: MFCC coding is data preparation, flat-start initialisation is training, forced alignment is testing (`HVite`), the WER is analysis.
> - B: Slide 3 lists "initialize models (e.g., bootstrap, flat start)" under training. Its tool `HCompV` is a training tool.
> - C: The WER is in the analysis group (slide 3: "evaluate model performance (e.g., WER, ROC)"). `HResults` computes it, not `HVite`.
> - D: Converting waveforms into a parametric format such as MFCC is data preparation (slide 3). `HCompV` only reads the features that were already made.

### Q3 (slide 4)

In HTK's processing stages, the recogniser `HVite` gets the speech and three more inputs: the HMMs, a dictionary and the word networks. After it, `HResults` computes the scores. Which statement is correct?

- A) `HResults` receives the HMMs from the training tools and scores how well they fit the speech.
- B) The dictionary comes from `HLEd`, the tool that converts transcriptions into label files.
- C) The HMMs come from `HCopy`, the tool that turns the training speech into models.
- D) `HResults` receives the transcriptions that `HVite` wrote, and compares them with reference transcriptions.

> [!success]- Answer
> **D.** Slide 4: `HDMan` feeds the dictionary, `HBuild`/`HParse` feed the networks, and the training tools (`HCompV`, `HInit`, `HRest`, `HERest`, `HSmooth`, `HHEd`, `HEAdapt`) make the HMMs.
> `HResults` receives the transcriptions that `HVite` wrote and compares them with the reference transcriptions.
> - A: In the figure the only arrow into `HResults` comes from the transcriptions that `HVite` wrote. It compares words with reference words; it does not score models against speech.
> - B: `HLEd` is a data-preparation tool for transcriptions. On slide 4 the dictionary comes from `HDMan`, and the networks from `HBuild` or `HParse`.
> - C: `HCopy` turns waveforms into feature files (data preparation). The HMMs come from the training tools, such as `HCompV` and `HERest`.

### Q4 (slide 5)

An HTK command has optional arguments, which start with a minus sign, and main arguments. On slide 5, `HFoo -T 1 -f 34.3 -a -s myfile file1 file2` has 4 optional and 2 main arguments. Now read `HFoo -C cfg -T 2 -a -s list in1 in2 in3`. Which statement is correct?

- A) `cfg`, `2` and `list` count as optional arguments too, so there are 7 optional arguments.
- B) `list` is the first main argument, so the command has 4 main arguments.
- C) `-a` is a switch: it takes no value, because the next token `-s` starts a new option.
- D) `-C cfg` is a configuration file and not an optional argument, so there are 3 optional arguments.

> [!success]- Answer
> **C.** Optional arguments: `-C cfg`, `-T 2`, `-a`, `-s list`, so 4 (one of them names a configuration file). Main arguments: `in1 in2 in3`, so 3; they have no minus sign and come last.
> `-a` is followed directly by `-s`, so it is a switch (on or off). `-T` takes 2, and `-s` takes `list`, as `-s myfile` does on the slide.
> - A: This counts the values as arguments of their own. A value belongs to its option: `-C cfg` is one optional argument.
> - B: This reads `-s` as a switch. On slide 5, `-s myfile` is one optional argument, so `-s` takes a value and `list` is that value.
> - D: Slide 5 marks the `-C` files separately, but `-C cfg` still starts with a minus sign: it is an optional argument that names a configuration file.

## 2. The task and Step 1: grammar (slides 6-9)

### Q5 (slide 6)

The voice-dialling task has only a few words. Why does the tutorial still build phone-based (sub-word) models and not one HMM per word?

- A) Phone models need no transcriptions, so the training speech can be recorded freely without any prompts.
- B) Phone models are shared by all words. A new word needs only a grammar line and a pronunciation, and no new recordings.
- C) Phone models are always more accurate than word models on a small vocabulary, because each phone model has more states.
- D) Word models cannot be trained in HTK, because `HERest` accepts only phone-level transcriptions as its labels.

> [!success]- Answer
> **B.** Slide 6 gives the reason: the recogniser "can be easily expanded".
> A new word is built from the same phone models. With one HMM per word, every new word would need its own training examples.
> - A: The tutorial records the speech from prompts and builds phone transcriptions from them (Steps 3 and 4). Training needs them.
> - C: The slide gives expansion as the reason, not accuracy. Nothing on the slides says that a phone model has more states than a word model.
> - D: Nothing on the slides limits HTK to phones. The labels only have to match the model names, as the triphone steps show.

### Q6 (slide 7)

HTK grammar notation: `|` separates alternatives, `[ ]` makes something optional, `< >` means one or more repetitions, `( )` groups. Which rule allows exactly this: the word REDIAL, or the word DIAL followed by one or more digits, and at the end an optional PLEASE?

- A) `( REDIAL | DIAL <$digit> ) < PLEASE >`
- B) `REDIAL | DIAL <$digit> [ PLEASE ]`
- C) `( REDIAL | DIAL [ $digit ] ) [ PLEASE ]`
- D) `( REDIAL | DIAL <$digit> ) [ PLEASE ]`

> [!success]- Answer
> **D.** The vertical bar gives the two alternatives, and the round brackets group them, as in `(PHONE|CALL)` on slide 8.
> `<$digit>` repeats a digit one or more times. `[ PLEASE ]` makes PLEASE optional after either alternative.
> - A: Angle brackets make PLEASE required and repeatable (PLEASE PLEASE). An optional word needs `[ ]`.
> - B: Without the round brackets the bar splits REDIAL from all the rest, so PLEASE may follow only the DIAL branch. That is why slide 8 writes `(PHONE|CALL) $name`.
> - C: Square brackets make the digit optional: DIAL with no digit is accepted, and two or more digits are not. One or more is `< >`.

### Q7 (slide 8)

The voice-dialling grammar is

```text
$digit = ONE | TWO | THREE | FOUR | FIVE |
         SIX | SEVEN | EIGHT | NINE | OH | ZERO;
$name  = [ JOOP ] JANSEN | [ JULIAN ] ODELL | [ DAVE ] OLLASON |
         [ PHIL ] WOODLAND | [ STEVE ] YOUNG;
( SENT-START ( DIAL <$digit> | (PHONE|CALL) $name) SENT-END )
```

Here `[ ]` means optional, `< >` one or more repetitions, `|` alternatives. Which statement is correct?

- A) `CALL YOUNG` is rejected, because every name needs its first name before the surname.
- B) `PHONE STEVE` is accepted, because in `[ STEVE ] YOUNG` either name may be left out.
- C) `DIAL` alone is rejected, because `<$digit>` needs at least one digit after DIAL.
- D) `DIAL` alone is accepted, because `<$digit>` allows zero or more digits after DIAL.

> [!success]- Answer
> **C.** `< >` means one or more. In the figure of slide 8 every path from `dial` to `sent-end` passes through one digit box; the loop back only adds more digits.
> For the other two sentences: `CALL YOUNG` is accepted, `PHONE STEVE` is rejected.
> - A: In `[ STEVE ] YOUNG` the first name is in square brackets, so it is optional. `CALL YOUNG` is accepted.
> - B: Only the first name is in square brackets. The surname YOUNG is required, so `PHONE STEVE` is rejected.
> - D: This reads `< >` as zero or more. In HTK notation `< >` is one or more repetitions.

### Q8 (slide 9)

A new name, `[ ADA ] KING`, is added to `$name` in the grammar file `gram`. Earlier, `HParse gram wdnet` compiled the grammar into the word network `wdnet`, and the dictionary `dict` was built from the word list `wlist` and the source dictionaries `beep` and `names`. The models are phone models. Which statement is correct?

- A) Run `HParse` again, add ADA and KING to `wlist` and `names`, and rebuild `dict` with `HDMan`. No new recordings are needed.
- B) Run `HParse` again and record training examples of ADA and KING, because the models have never heard these words.
- C) Run `HParse` and `HDMan` again, and repeat the training from Step 6, because the HMMs must see every word of the grammar.
- D) Add ADA and KING to `dict` only; `HParse` need not run again, because the recogniser reads `gram` directly.

> [!success]- Answer
> **A.** The recogniser reads the compiled network `wdnet`, not `gram`, so `HParse` must run again.
> The new words need pronunciations, so they go into `wlist` and the names dictionary `names`, and `HDMan` rebuilds `dict` (Step 2).
> No new recordings: the words are built from the same phone models. (One inference from Step 10: each triphone of the new words must be in `tiedlist`.)
> - B: The models are phone models. New words are built from phones that are already trained, so no recordings of the words are needed.
> - C: The HMMs are phone models and do not depend on the word list. Only the network and the dictionary change.
> - D: The recogniser reads `wdnet`. Without a new `HParse` run the network does not contain the new name.

## 3. Steps 2 and 3: dictionary and recording (slides 10-12)

### Q9 (slide 10)

Read the command `HDMan -m -w wlist -n monophones1 -l dlog dict beep names`. Here `wlist` is the list of required words, `beep` and `names` are source pronunciation dictionaries, and `monophones1` is the list of phones used. Which statement is correct?

- A) Read: `dict`, `beep`, `names`. Written: `wlist`, `monophones1`, `dlog`. Here `-l dlog` names a log file.
- B) Read: `wlist`, `beep`, `names`. Written: `dict`, `monophones1`, `dlog`. Here `-l dlog` names a log file.
- C) Read: `dict`, `beep`, `names`. Written: `wlist`, `monophones1`. Here `-l dlog` is the output directory for label files.
- D) Read: `wlist`, `beep`, `names`. Written: `dict`, `monophones1`. Here `-l dlog` is the output directory for label files.

> [!success]- Answer
> **B.** `HDMan` looks up each word of `wlist` in `beep` and `names`, writes the new dictionary `dict` and, after `-n`, the phone list `monophones1`.
> `-l dlog` names a log file. The label-directory meaning of `-l` belongs to tools that write label files, such as `HLEd` and `HVite` (`-l '*'`).
> - A: This swaps input and output. `dict` is the new dictionary that `HDMan` writes; `wlist` is the input word list (after `-w`).
> - C: Two slips: `dict` is written and `wlist` is read, and in `HDMan` `-l dlog` names a log file, not a label directory.
> - D: This takes the `-l` row of the general options table for every tool. `-l` changes meaning by tool; in `HDMan` it names a log file.

### Q10 (slide 11)

`HDMan` uses the edit script `global.ded` with three commands: `AS sp` appends a short pause `sp` to every pronunciation, `RS cmu` removes stress marks, `MP sil sil sp` merges `sil sp` into one `sil`. A source entry is `SEVEN s eh1 v n` (the `1` is a stress mark). What entry goes into `dict`?

- A) `SEVEN s eh v n sil`
- B) `SEVEN s eh v n`
- C) `SEVEN s eh1 v n sp`
- D) `SEVEN s eh v n sp`

> [!success]- Answer
> **D.** `AS sp` gives `SEVEN s eh1 v n sp`. `RS cmu` removes the stress mark: `SEVEN s eh v n sp`. `MP sil sil sp` changes nothing, because there is no `sil`.
> Every word now ends in `sp`, the optional pause between words. Its model is built in Step 7, and this is why `sp` is in `monophones1`.
> - A: This reads `MP sil sil sp` as "turn `sp` into `sil`". It only merges the pair `sil sp` into `sil`, and this entry has no `sil`.
> - B: This reads `MP sil sil sp` as "delete a lone `sp`". It only merges the pair `sil sp`; with no `sil` in front, the `sp` from `AS sp` stays.
> - C: This skips `RS cmu`, as if it did nothing to a `beep` entry. It removes the stress mark, so `eh1` becomes `eh`.

### Q11 (slide 12)

Step 3 makes test prompts with `HSGen -l -n 200 wdnet dict > testprompts`. Here `wdnet` is the word network compiled from the grammar in Step 1, and `dict` is the dictionary built in Step 2. In the grammar, DIAL can only be followed by digits. Which statement is correct?

- A) To get 500 prompts, change `-n 200` to `-n 500`. Steps 1 and 2 must be finished before `HSGen` runs.
- B) Only Step 1 must be finished, because `dict` is used later by the recogniser and not by `HSGen`.
- C) `HSGen` can write `DIAL PHONE`, because it picks each word at random from the words in `dict`.
- D) To get 500 prompts, change `-l` to `-l 500`, because `-l` sets the length of the prompt list.

> [!success]- Answer
> **A.** `-n 200` sets how many prompts are made, so it becomes `-n 500`. (In `HSGen`, `-l` numbers the prompts.)
> `wdnet` (Step 1) and `dict` (Step 2) are both arguments of the command, so both files must exist.
> `HSGen` walks at random through `wdnet`, so every prompt is a path through the grammar: it can never write `DIAL PHONE`.
> - B: `dict` is an argument of this `HSGen` command, so Step 2 (`HDMan`) must have made it.
> - C: The random walk is through the network `wdnet`, not through the word list. After DIAL the network allows only digits.
> - D: In `HSGen`, `-l` numbers the prompts and takes no number here. The count is set by `-n`.

## 4. Step 4: transcription files (slides 13-14)

### Q12 (slide 13)

Step 4 runs `HLEd -l '*' -d dict -i phones0.mlf mkphones0.led words.mlf`. The script `mkphones0.led` holds `EX` (replace each word by its pronunciation from the dictionary), `IS sil sil` (insert `sil` at the start and end) and `DE sp` (delete `sp`). In Step 2, `AS sp` appended `sp` to every pronunciation in `dict`. Step 6 then trains with `HERest` only the models in `monophones0`, a phone list without `sp`. Which statement is correct?

- A) `phones0.mlf` (after `-i`) is the input, and `words.mlf` is the output phone-level MLF.
- B) `mkphones0.led` is the dictionary, and `-d dict` names the edit script that `HLEd` runs.
- C) Without `DE sp`, `sp` labels stay in `phones0.mlf`, and `HERest` in Step 6 meets labels with no model.
- D) Without `DE sp` nothing goes wrong, because `HERest` skips every label that has no model.

> [!success]- Answer
> **C.** The files: `-d dict` is the dictionary for `EX`, `-i phones0.mlf` is the output phone-level MLF, `mkphones0.led` is the edit script, `words.mlf` is the input word-level MLF.
> `AS sp` in Step 2 put `sp` at the end of every pronunciation, so `EX` brings it into the labels. `DE sp` removes it, because there is no `sp` model before Step 7.
> - A: This swaps input and output. The word-level `words.mlf` is the last main argument and is read; `-i phones0.mlf` names the output.
> - B: This swaps the two. `-d dict` names the dictionary; the edit script `mkphones0.led` is a main argument.
> - D: Training joins the models of the labels in order, so each label needs a model. `HERest` would meet the label `sp`, for which there is no model.

### Q13 (slide 14)

`HLEd` runs the script `EX` (replace each word by its pronunciation), `IS sil sil` (insert `sil` at the start and at the end of the utterance), `DE sp` (delete `sp`). The dictionary has `CALL k ao l sp`, `STEVE s t iy v sp`, `YOUNG y ah ng sp`. A training prompt is `S0005 CALL STEVE YOUNG`. Which labels does its block in `phones0.mlf` hold, between the name line `"*/S0005.lab"` and the closing dot?

- A) `sil k ao l sp s t iy v sp y ah ng sp sil`
- B) `k ao l s t iy v y ah ng`
- C) `sil k ao l s t iy v y ah ng`
- D) `sil k ao l s t iy v y ah ng sil`

> [!success]- Answer
> **D.** `EX`: `k ao l sp s t iy v sp y ah ng sp`.
> `IS sil sil`: `sil k ao l sp s t iy v sp y ah ng sp sil`.
> `DE sp`: `sil k ao l s t iy v y ah ng sil`, 12 labels, one per line. This matches slide 14: `sil w ah n v ae ...`, with no `sp` between the words.
> - A: This skips `DE sp`. The `sp` from the dictionary must be deleted, because there is no `sp` model yet.
> - B: This skips `IS sil sil`. Every utterance starts and ends with `sil`.
> - C: This reads `IS sil sil` as one `sil` at the start only. The two arguments are the label inserted at the start and the one at the end.

## 5. Step 5: coding the data (slides 15-16)

### Q14 (slide 15)

Step 5 runs `HCopy -T 1 -C config -S codetr.scp`. The command has no main arguments. How does `HCopy` know which files to read and which to write?

- A) From the script file after `-S`: each line of `codetr.scp` holds a source `.wav` file and the `.mfc` file to write.
- B) From `codetr.scp`, which lists only the `.wav` files; the features of all files go into one MLF.
- C) It codes every `.wav` file in the current directory and writes each `.mfc` file next to it.
- D) From the configuration file after `-C`: `config` lists the `.wav` files and the name of each output file.

> [!success]- Answer
> **A.** `-S` names a script file, the list of data files. For `HCopy` each line pairs an input and its output (slide 16).
> `-C config` gives the conversion parameters and `-T 1` sets the trace level. The step is the MFCC front end of Lecture 9, done once so training does not repeat it.
> - B: Each line of `codetr.scp` names the `.wav` and its `.mfc` file. An MLF holds label files (transcriptions), not features.
> - C: The slides give no such default. The list of files comes from `codetr.scp`, named after `-S`.
> - D: `config` holds the parameters (`TARGETKIND`, `TARGETRATE`, ...). The file names are in the script file after `-S`.

### Q15 (slide 16)

HTK counts time in units of 100 ns. The `HCopy` config is edited to `TARGETRATE = 50000.0` (frame shift), `WINDOWSIZE = 200000.0`, `NUMCEPS = 10`. `MFCC_0` gives the `NUMCEPS` cepstra plus $c_0$; `_D_A` adds the deltas $\Delta$ and the accelerations $\Delta^2$. How many frames per second are there, and how many values does an `MFCC_0_D_A` vector have?

- A) 20 frames per second; 33 values per vector
- B) 200 frames per second; 33 values per vector
- C) 200 frames per second; 11 values per vector
- D) 20 frames per second; 11 values per vector

> [!success]- Answer
> **B.** 100 ns $=10^{-4}$ ms, so the shift is $50000\times10^{-4}=5$ ms and there are $1000/5=200$ frames per second.
> `MFCC_0`: $10+1=11$ values. With $\Delta$ and $\Delta^2$: $11\times3=33$.
> - A: This reads 100 ns as 1 µs: $50000$ µs $=50$ ms, so $1000/50=20$. The vector size is right.
> - C: This forgets the deltas and accelerations: 11 is the `MFCC_0` size. `_D_A` triples it to 33.
> - D: Two slips: 100 ns read as 1 µs (shift 50 ms) and the deltas not counted.

## 6. Step 6: flat-start monophones (slides 17-19)

### Q16 (slide 17)

After `HCompV -C config -f 0.01 -m -S train.scp -M hmm0 proto`, every phone model has the same numbers: the global mean and variance of all training data. `-f 0.01` sets a variance floor at 1% of the global variance. The models of `aa` and `s` are identical, and the global variance of one feature is 0.35. Which statement is correct?

- A) They stay identical until Step 8 gives phone boundaries, because `HERest` cannot tell identical models apart.
- B) They can never become different: identical models get identical shares of every frame in every pass.
- C) `HERest` joins the models in transcription order, so `aa` and `s` collect different frames and move apart.
- D) The floor of that feature is 0.0035, which is also the starting variance of every state in `hmm0`.

> [!success]- Answer
> **C.** The transcriptions make the difference. A frame early in an utterance counts mostly for its first phones, a late frame for its last phones. `aa` and `s` occur in different utterances and positions, so they collect different frames, and their means and variances move apart with every pass.
> The floor of that feature is $0.01\times0.35=0.0035$. No variance may fall below it in later passes.
> - A: Embedded Baum-Welch needs no boundaries: each frame counts for each state by its posterior, and the order of the transcription already separates the phones.
> - B: The shares depend on the position in the transcription, not only on the models. `aa` and `s` sit in different places, so they get different frames.
> - D: The floor is 0.0035, but the starting variance is the global variance 0.35. The floor is only the lower limit.

### Q17 (slide 18)

![[cs568_lecture_21_l21_p018.jpg]]

The prototype `proto` says `<NumStates> 5` but has only three `<Mean>`/`<Variance>` blocks. Row 2 of `<TransP>` is `0.0 0.6 0.4 0.0 0.0`. Each emitting state has one diagonal Gaussian over 39 features. The frame shift is 10 ms. Which statement is correct?

- A) The shortest phone lasts 3 frames, 30 ms, because every path visits states 2, 3 and 4.
- B) The shortest phone lasts 5 frames, 50 ms, because `<NumStates> 5` counts five emitting states.
- C) One model has 117 Gaussian parameters: 39 means in each of its 3 emitting states.
- D) One model has 390 Gaussian parameters: 39 means and 39 variances in each of 5 states.

> [!success]- Answer
> **A.** States 1 and 5 are the entry and exit states: they emit nothing and have no Gaussian. States 2, 3 and 4 emit.
> Row 2: stay with 0.6, go to state 3 with 0.4, no skip to state 4. Without skips each path spends at least one frame in each emitting state: $3\times10=30$ ms.
> Gaussian parameters per model: $3\times(39+39)=234$.
> - B: This counts the entry and exit states as emitting. They have no `<Mean>` block, so only 3 states emit.
> - C: This forgets the variances. A diagonal Gaussian has 39 means and 39 variances: $3\times78=234$.
> - D: This counts the entry and exit states, which have no Gaussian: $5\times78=390$ instead of $3\times78=234$.

### Q18 (slide 19)

Step 6 runs `HERest -C config -I phones0.mlf -t 250.0 150.0 1000.0 -S train.scp -H hmm0/macros -H hmm0/hmmdefs -M hmm1 monophones0`. Here `-H` loads models and `-M` names the directory for the new models. `monophones0` is the phone list of Step 6, and `monophones1` is the phone list written by `HDMan` in Step 2. A student says: "For the second pass, run the same command again." Which statement is correct?

- A) Only `-M hmm1` must become `-M hmm2`; the models are still read from `hmm0`.
- B) The list is `monophones0` because `sil` is trained only from Step 7 on, when its model is made.
- C) The same command is right, because `HERest` reads the newest models from the `-M hmm1` directory.
- D) The same command reads `hmm0` again and rewrites `hmm1`, so nothing improves; pass 2 must read `hmm1`.

> [!success]- Answer
> **D.** The list is `monophones0` because no `sp` model exists yet.
> The second pass must read the new models: `-H hmm1/macros -H hmm1/hmmdefs -M hmm2`. The third reads `hmm2` and writes `hmm3`.
> `sp` gets its model only in Step 7, and `phones0.mlf` has no `sp` labels (`DE sp` in Step 4).
> - A: Then the second pass starts again from `hmm0` and writes the same models as the first pass. The `-H` options must name `hmm1`.
> - B: `hmm0/hmmdefs` has one copy of `proto` per phone, including `sil`. The model missing until Step 7 is `sp`.
> - C: `HERest` reads the models named after `-H`. `-M` only names where to write. Training never overwrites models.

## 7. Steps 7 and 8: silence models and realignment (slides 20-22)

### Q19 (slide 20)

In `HHEd`, the command `AT i j p` adds a transition from state $i$ to state $j$ with probability $p$, and rescales the old transitions out of state $i$ by $1-p$, so that the row still sums to 1. Row 2 of the `sil` transition matrix is `0.0 0.6 0.4 0.0 0.0`. What is row 2 after `AT 2 4 0.2 {sil.transP}`?

- A) `0.0 0.4 0.4 0.2 0.0`
- B) `0.0 0.48 0.32 0.2 0.0`
- C) `0.0 0.6 0.4 0.2 0.0`
- D) `0.0 0.5 0.3 0.2 0.0`

> [!success]- Answer
> **B.** The old entries are scaled by $1-0.2=0.8$: stay $0.6\times0.8=0.48$, to state 3 $0.4\times0.8=0.32$. To state 4: $0.2$.
> Sum: $0.48+0.32+0.2=1$.
> In the same script, `TI silst {sil.state[3],sp.state[2]}` ties `sp` to the centre state of `sil`, so `sp` is trained on all the silence data.
> - A: This takes all 0.2 from the self-loop. `AT` scales every old transition by $1-p$, not only the stay.
> - C: This adds 0.2 without rescaling. The row then sums to 1.2, which is not a probability distribution.
> - D: This takes 0.1 from each old entry. The old entries are multiplied by 0.8, not reduced by equal amounts.

### Q20 (slide 21)

![[cs568_lecture_21_l21_p021.jpg]]

The figure shows Step 7. In Step 6 the HMM list was `monophones0`. Which statement is correct?

- A) The list changes to `monophones1` because it adds `sil`, which Step 6 did not train.
- B) "Edit sil -> sp" means that `HLEd` renames every `sil` label to `sp` in `phones0.mlf`.
- C) The first `HERest` pass writes `hmm6`, a directory that the figure does not show.
- D) The first `HERest` pass writes `hmm7`, and the second pass overwrites `hmm7`.

> [!success]- Answer
> **C.** `HHEd` writes `hmm5`. The first `HERest` pass writes `hmm6`, the second writes `hmm7`.
> The list is now `monophones1` because it contains `sp`: from `hmm4` on, the model set has an `sp` model.
> "Edit sil -> sp" is the manual edit: copy the centre state of `sil` into a new one-state model `sp` and save as `hmm4`.
> - A: `sil` was trained in Step 6. What `monophones1` adds is `sp`.
> - B: The box is a manual edit of the models: the centre state of `sil` is copied into a new model `sp`. No labels are renamed.
> - D: Training never overwrites models: each pass reads `hmmK` and writes `hmm(K+1)`. The first pass writes `hmm6`.

### Q21 (slide 22)

Step 8 runs

```bash
HVite -l '*' -o SWT -b silence -C config -a -H hmm7/macros \
      -H hmm7/hmmdefs -i aligned.mlf -m -t 250.0 -y lab \
      -I words.mlf -S train.scp dict monophones1
```

`phones0.mlf` was made in Step 4 by `HLEd` from the dictionary alone. Which statement is correct?

- A) `-I words.mlf` alone makes it an alignment; `-a` only adds the times to the output labels.
- B) `-b silence` makes it an alignment: it fixes the words at both ends, so `HVite` need not search.
- C) `aligned.mlf` holds the same phones as `phones0.mlf`, because both take the first pronunciation in `dict`.
- D) `-a` with `-I words.mlf` makes it an alignment, and `aligned.mlf` can hold a different pronunciation than `phones0.mlf`.

> [!success]- Answer
> **D.** `-a` switches on forced alignment, and `-I words.mlf` gives the known words of each utterance. Recognition (Step 11) has no `-a` and searches the network `-w wdnet`.
> `aligned.mlf` is made with the audio: for a word with several pronunciations it holds the one with the best Viterbi score. It can also contain `sp`, because the `sp` model now exists.
> - A: `-a` is the switch that turns on alignment. (`-o SWT` actually leaves times out of the output labels.)
> - B: `-b silence` only puts the word `silence` at both ends of each utterance. The words in between are fixed by `-a` with `-I words.mlf`.
> - C: `phones0.mlf` ignores the audio. In alignment `HVite` puts all pronunciations of a word in parallel and keeps the best one for that recording.

## 8. Step 9: triphones from monophones (slides 23-25)

### Q22 (slide 23)

HTK writes a triphone as `l-p+r`: phone `p` after `l`, before `r`. A biphone has one context only: `p+r` or `l-p`. The script `mktri.led` holds `WB sp`, `WB sil` (define `sp` and `sil` as word boundaries) and `TC` (convert the phones to triphones). Convert the labels `n ay n sp ow sp` (the words NINE OH). What goes into `wintri.mlf`?

- A) `n+ay n-ay+n ay-n+ow sp n-ow sp`
- B) `n+ay n-ay+n ay-n sp ow sp`
- C) `n n-ay+n n sp ow sp`
- D) `n+ay n-ay+n ay-n ow`

> [!success]- Answer
> **B.** `sp` is a word boundary, and a context never crosses a word boundary, so each word is converted alone and its edge phones become biphones. NINE: `n+ay`, `n-ay+n`, `ay-n`.
> OH has one phone and no neighbour inside the word, so it stays `ow` (an inference from the boundary rule; the slides show no one-phone word).
> `triphones1` lists each model name once: `n+ay`, `n-ay+n`, `ay-n`, `sp`, `ow`.
> - A: This carries the context across the word boundary `sp`. The triphones here are word-internal, so `n` and `ow` do not see each other.
> - C: This leaves the edge phones as monophones. A phone at a word edge keeps its context inside the word: `n+ay` and `ay-n`.
> - D: This deletes the boundary labels. `WB` only marks `sp` and `sil` as boundaries; they stay in the transcription.

### Q23 (slide 24)

Step 9 clones models with `HHEd -B -H hmm9/macros -H hmm9/hmmdefs -M hmm10 mktri.hed monophones1` (`CL` clones each monophone into its triphones, `TI` ties the transition matrices of all triphones of one phone). Then `HERest` runs twice with `-I wintri.mlf ... -s stats ... triphones1`. Step 10 starts with `RO 100 stats`. Which statement is correct?

- A) Right after cloning, `n+ay` is an exact copy of `n`. The triphones become different only in the `HERest` passes.
- B) `n+ay` is an average of the models `n` and `ay`, because a triphone describes both phones.
- C) Without `-s stats` Step 10 still works, because `RO` then gives each state a default count.
- D) `HERest` could use `aligned.mlf` and `monophones1`, because it maps each triphone back to its monophone.

> [!success]- Answer
> **A.** `CL` only copies: `n+ay` is a copy of `n` and `n-ay+n` a copy of `ay`. The output distributions separate in the two `HERest` passes.
> The labels and the model names must match, so `HERest` uses `wintri.mlf` and `triphones1`.
> `-s stats` writes the state occupation counts. Without that file `RO 100 stats` has no counts, and the clustering cannot check how much data each cluster has.
> - B: A triphone is a model of ONE phone (`n`) in a context. Cloning copies the monophone `n`; nothing is averaged.
> - C: `RO 100 stats` loads the counts from the file `stats`. Without it the occupancy rule cannot be checked.
> - D: The models are now named `n+ay`, ... The transcriptions and the model list must use the same names: `wintri.mlf` and `triphones1`.

### Q24 (slide 25)

The Step 9 flow chart has two branches. Left: Monophones (`hmm9`) → tool X → Triphones (`hmm10`). Right: Monophone Transcriptions (`aligned.mlf`) → tool Y → Triphone Transcriptions (`wintri.mlf`). Both go into `HERest (x2)`, which outputs Triphones (`hmm12`) and State Occs (`stats`). A student says: "`HLEd` turns the monophone models into triphone models." Which statement is correct?

- A) X is `HHEd` and Y is `HLEd`, so the student is wrong. No directory is missing.
- B) X is `HLEd` and Y is `HHEd`, so the student is right. No directory is missing.
- C) X is `HHEd` and Y is `HLEd`, so the student is wrong. The chart leaves out `hmm11`.
- D) X is `HLEd` and Y is `HHEd`, so the student is right. The chart leaves out `hmm11`.

> [!success]- Answer
> **C.** `HHEd` edits models: it turns `hmm9` into `hmm10`. `HLEd` edits label files: `aligned.mlf` into `wintri.mlf`.
> Each pass writes a new directory: `hmm10` to `hmm11`, then `hmm11` to `hmm12`. The chart shows only the final `hmm12`.
> - A: Training never overwrites models, so the two passes cannot both write `hmm12`. The first pass writes `hmm11`.
> - B: Two slips: the tools are swapped (`HHEd` edits models, `HLEd` edits labels), and the first pass writes `hmm11`, which the chart leaves out.
> - D: This swaps the two tools. `HLEd` edits label files (right branch); `HHEd` edits HMM definitions (left branch).

## 9. Step 10: tied-state triphones (slides 26-28)

### Q25 (slide 26)

A phone set has 45 phones. Each triphone model has 3 emitting states, and each state has one diagonal Gaussian over 39 features (39 means and 39 variances). How many Gaussian parameters would all possible triphones need without tying?

- A) 7,107,750
- B) 10,661,625
- C) 21,323,250
- D) 35,538,750

> [!success]- Answer
> **C.** Triphones: $45^3=91{,}125$. States: $91{,}125\times3=273{,}375$.
> Parameters: $273{,}375\times(39+39)=21{,}323{,}250$, about 21 million.
> A small corpus cannot estimate that many numbers. Tying lets states "share data and make robust parameter estimates" (slide 26).
> - A: This counts one state per triphone: $91{,}125\times78$. Each model has 3 emitting states.
> - B: This counts only the means: $273{,}375\times39$. A diagonal Gaussian also has 39 variances.
> - D: This counts 5 states per model: $91{,}125\times5\times78$. The entry and exit states have no Gaussian.

### Q26 (slide 27)

Steps 9 to 11 use three model lists: `triphones1`, `fulllist` and `tiedlist`. The tying script `tree.hed` of Step 10 ends with `AU "fulllist"` (build models for the triphones of the list that have none) and `CO "tiedlist"` (merge identical models and write the new list). Which statement is correct?

- A) `tiedlist` holds only the triphones seen in training, because unseen ones have no data.
- B) `tiedlist` covers all triphones of `fulllist`, and models that became identical are merged.
- C) `fulllist` holds only the triphones seen in training, and `HLEd` writes it in Step 9.
- D) `triphones1` holds every triphone of the dictionary, and `HDMan` writes it in Step 10.

> [!success]- Answer
> **B.** `triphones1`: the triphones in the training transcriptions (`HLEd -n`, Step 9). `fulllist`: all triphones of the whole dictionary, seen or not (`HDMan -n`, Step 10).
> `tiedlist`: after `AU` gives models to unseen triphones and `CO` merges identical models. The last two `HERest` passes and `HVite` in Step 11 use it.
> - A: `AU "fulllist"` builds models for unseen triphones by walking down the trees, so `tiedlist` covers them too.
> - C: That describes `triphones1`. `fulllist` is written by `HDMan` from the whole dictionary, so it also holds unseen triphones.
> - D: That describes `fulllist`. `triphones1` is the `-n` output of `HLEd` in Step 9, made from the training transcriptions, so it holds only seen triphones.

### Q27 (slide 28)

In `tree.hed`, `RO 100 stats` loads the state occupation counts (frames per state) from the file `stats` and sets the minimum occupancy of a cluster to 100 frames. Each `TB` command, for example `TB 350 ...`, splits clusters with yes/no questions about the context. Three states in one cluster have 40, 35 and 60 frames. A question would split them into {40, 35} and {60}. Which statement is correct?

- A) This split is not allowed, and no other split of these three states into two clusters is allowed either.
- B) This split is allowed if its likelihood gain is above the `TB` threshold 350, whatever the frame counts.
- C) This split is not allowed, but {40, 60} and {35} would be, because one side has exactly 100 frames.
- D) This split is allowed, because the cluster that is split holds $135\ge100$ frames.

> [!success]- Answer
> **A.** The sides would have $40+35=75$ and $60$ frames, both below 100.
> Two clusters of at least 100 frames need $200$ frames; there are only $40+35+60=135$. The three states stay one tied state.
> In the figure, `HHEd` gets `hmm12`, `stats` and `tree.hed`, and gives `hmm13` and `tiedlist`. The box "HREST (x2)" below is `HERest`, as the text of slide 27 says.
> - B: The gain is only one test. `RO 100` sets the minimum occupancy, so each side also needs at least 100 frames.
> - C: This checks only one side. The other side has 35 frames, below 100, so this split breaks the rule too.
> - D: This checks the rule on the cluster before the split. The rule holds for every cluster after the split: 75 and 60 are both below 100.

## 10. Step 11: recognition and scoring (slide 29)

### Q28 (slide 29, command)

Step 11 runs `HVite -C config -H hmm15/macros -H hmm15/hmmdefs -S test.scp -l '*' -i recout.mlf -w wdnet -p 0.0 -s 5.0 dict tiedlist`. `HVite` ranks a word sequence $W$ with $K$ words by $\log p(\text{acoustics}\mid W)+s\cdot\log P(W)+K\cdot p$, where $s$ is the grammar scale (`-s`) and $p$ the word insertion penalty (`-p`). The output has many extra short words. Which statement is correct?

- A) Move `-p` below 0.0: each word then adds a negative term, so sentences with many words lose score.
- B) Move `-p` above 0.0: a positive penalty makes each extra word cost score.
- C) Lower `-s` to 0.0 and keep `-p`, so that the grammar can no longer add extra words.
- D) `tiedlist` and `hmm15` come from Step 9, where the monophones are cloned into triphones.

> [!success]- Answer
> **A.** Extra words are insertions. With $p<0$ the term $K\cdot p$ makes every word cost something. If $p$ is too negative, words start to be deleted.
> The files: `wdnet` from Step 1 (`HParse`), `dict` from Step 2 (`HDMan`), `hmm15` and `tiedlist` from Step 10.
> - B: In the score the term is $+K\cdot p$. A positive $p$ adds score for every word, so it gives even more insertions.
> - C: The grammar does not add words: $\log P(W)\le0$, so $s\cdot\log P(W)$ can only cost score. Setting $s=0$ removes a cost; it does not penalise extra words.
> - D: Step 9 ends with `hmm12` and `triphones1`. `tiedlist` and `hmm15` come from Step 10 (tying, then two `HERest` passes).

### Q29 (slide 29, scoring)

`HResults` counts substitutions $S$, deletions $D$ and insertions $I$ against $N$ reference words. %Correct $=(N-D-S)/N\times100$ and Accuracy $=(N-D-S-I)/N\times100$. Two systems are scored on $N=250$ words. System B has $D=10$, $S=20$, $I=2$: %Correct 88%, Accuracy 87.2%. System A has $D=5$, $S=15$, $I=20$. What are %Correct and Accuracy of system A?

- A) %Correct 92%, Accuracy 92%
- B) %Correct 84%, Accuracy 84%
- C) %Correct 84%, Accuracy 92%
- D) %Correct 92%, Accuracy 84%

> [!success]- Answer
> **D.** %Correct $=(250-5-15)/250=230/250=92\%$. Accuracy $=(250-5-15-20)/250=210/250=84\%$.
> A wins on %Correct (92% against 88%), but B wins on Accuracy (87.2% against 84%). B is better: WER $32/250=12.8\%$ against $40/250=16\%$. %Correct hides the 20 insertions of A.
> - A: This leaves the insertions out of Accuracy. Accuracy is $(N-D-S-I)/N=210/250=84\%$.
> - B: This includes the insertions in %Correct. %Correct is $(N-D-S)/N$ and ignores insertions: 92%.
> - C: This swaps the two measures. Accuracy subtracts the insertions too, so it can never be above %Correct.
