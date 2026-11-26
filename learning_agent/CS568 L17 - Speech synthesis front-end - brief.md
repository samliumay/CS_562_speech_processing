# Learning brief: CS568 L17 - Speech synthesis front-end

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 20 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_17/CS568 L17 - Speech synthesis front-end.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_17_l17`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_17/l17.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_17/CS568_Lecture_17_TTS_Front_End.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_17/lecture_17.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. TTS, front-end and back-end (slides 2-5)
- 2. Text processing (slides 6-7)
- 3. Phonetic analysis (slides 8-9)
- 4. Prosodic analysis (slides 10-13)
- 5. History and Klatt's duration model (slides 14-15)
- 6. Pierrehumbert and ToBI (slides 16-17)
- 7. Tilt model (slide 18)
- 8. Fujisaki model (slide 19)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** Normalisation is not a lookup table: the reading depends on context. Most TTS systems do not fully parse the input, because parsing is costly and the input is often fragments.
- **Slide error:** The formula on slide 15 is missing the "+" between $MinDur$ and the fraction.
- **Slide detail:** The slide writes absolute-value bars on the amplitudes and also on the durations. A fall is often given a negative amplitude, and the bars make the formula work either way. A duration is never negative, so the bars on $D_{rise}$, $D_{fall}$ change nothing and ...
- **Watch out:** The sum is in the log domain. On the linear scale the components multiply. Phrase commands are impulses, accent commands are steps: do not swap them.

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
