# Learning brief: CS568 L19 - Prosodic modification of speech

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 32 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_19/CS568 L19 - Prosodic modification of speech.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_19_l19`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_19/l19.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_19/CS568_Lecture_19_Prosodic_Modification.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_19/lecture_19.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Why prosodic modification, and PSOLA (slides 2-3)
- 2. TD-PSOLA analysis and reconstruction (slides 4-6)
- 3. Time-scale and pitch-scale modification (slides 7-11)
- 4. Epochs, mapping function, interactions (slides 12-16)
- 5. TD-PSOLA performance (slide 17)
- 6. LP-PSOLA and FD-PSOLA (slides 18-21)
- 7. Sinusoidal models (slides 22-25)
- 8. Harmonic + noise models (slides 26-31)
- 9. STRAIGHT (slides 32-35)

## Known errors and traps in the sources (do not teach the errors)

- **Tonal noise:** Slowing unvoiced speech by about 2 repeats the same noise frame regularly, which sounds tonal (a 10 ms frame repeated gives a 100 Hz buzz). Reversing the time axis of every other frame fixes it. For voiced fricatives time reversal does not help, and FD-PSOLA i...
- **Slide error:** Slides 22, 23 and 27 write the argument as $\omega_0 l+\phi_l$. The time variable is missing: it is $\omega_0 l\,t+\phi_l$.

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
