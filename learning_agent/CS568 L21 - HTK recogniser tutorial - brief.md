# Learning brief: CS568 L21 - HTK recogniser tutorial

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 29 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_21/CS568 L21 - HTK recogniser tutorial.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_21_l21`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_21/l21.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_21/CS568_Lecture_21_HTK.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_21/lecture_21.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. What HTK is and how its tools are called (slides 2-5)
- 2. The task and Step 1: grammar (slides 6-9)
- 3. Steps 2 and 3: dictionary and recording (slides 10-12)
- 4. Step 4: transcription files (slides 13-14)
- 5. Step 5: coding the data (slides 15-16)
- 6. Step 6: flat-start monophones (slides 17-19)
- 7. Steps 7 and 8: silence models and realignment (slides 20-22)
- 8. Step 9: triphones from monophones (slides 23-25)
- 9. Step 10: tied-state triphones (slides 26-28)
- 10. Step 11: recognition and scoring (slide 29)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** Training never overwrites models. Each pass reads `hmmK` and writes `hmm(K+1)`, from `hmm0` to `hmm15`. Case matters: `-S` (script file) is not `-s`.
- **`-l` is not the same in every tool:** The row `-l dir` of the table holds for `HLEd` and `HVite` (`-l '*'`). In `HDMan`, `-l dlog` and `-l flog` name a log file. In `HSGen`, `-l` numbers the prompts. Other lower-case options (`-m`, `-n`, `-s`, `-t`, `-b`) also change meaning from tool to tool, so ...
- **Slide detail:** Slide 17 calls `train.scp` a list of the training WAV files. The data coded in Step 5 are the `.mfc` files, and slide 29 describes `test.scp` as a list of MFC files. Read `train.scp` as the list of the coded training files. This is an inference from slides 15,...
- **Watch out:** Before Step 8 add the line `silence sil` to `dict`. The option `-b silence` puts the word `silence` at both ends of each utterance, and every word needs a pronunciation.
- **Slide detail:** Slide 27 lists `tiedlist` and `trees` under the `HDMan` command. They are outputs of the `HHEd` run with `tree.hed`.
- **Slide error:** In the figure on slide 28 the re-estimation box reads "HREST (x2)". The tool is `HERest`, as the text of slide 27 says ("Repeat HERest twice more"). `HRest` is a different tool: it re-estimates one model at a time from segmented data.
- **Watch out:** %Correct ignores insertions. Report Accuracy or WER. Accuracy can be negative and WER can exceed 100%. Tune `-p` and `-s` on development data, not on the test set.

## How to run a session

1. Ask which part I want, or start from section 1. Ask two or three short questions to see what I already know.
2. Go section by section. Explain one slide or one idea, then ask the **check question** of that slide from the lesson note (the callouts `> [!question]- Check (slide N): ...`). Do not show the answer. Wait for mine, then compare it with the answer in the note and tell me what is missing.
3. At the end of a section, or in Practice mode, ask the **multiple-choice questions** of that section from the quiz note. Show the four options, wait for my letter, then explain why the right option is right and why my choice was wrong, using the answer callout.
4. Make new questions of the same kind (new numbers, a changed condition) for the points I miss.
5. Finish with a short list: what I know, what I should repeat, and which slides to read again.

## About me

MS student at TED University (CS/CMPE). I study alone, often on a phone. English is not my first language.

- Probe my starting point first.
- One idea at a time, short explanations, a check question after each step. Never skip steps.
- Give the Turkish equivalent of a key term at its first use.
- Ask me to think out loud. When I am wrong, show me which step went wrong.
- For graded homework or labs: hints and explanations only, never the submission.
