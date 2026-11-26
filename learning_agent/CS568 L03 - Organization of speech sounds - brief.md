# Learning brief: CS568 L03 - Organization of speech sounds

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 34 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_03/CS568 L03 - Organization of speech sounds.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_03_l3`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_03/l3.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_03/CS568_Lecture_03_Speech_Sounds.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_03/lecture_03.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Phonemes and notation (slides 2-3)
- 2. Phones, allophones, coarticulation, branches (slides 4-6)
- 3. Taxonomies of phoneme classes (slides 7-8)
- 4. Consonants: place, manner, voicing (slides 9-12)
- 5. Vowels: frontness, height, roundness (slides 13-15)
- 6. Spectra and spectrograms (slides 16-21)
- 7. Vowels, diphthongs, semivowels (slides 22-27)
- 8. Nasals, fricatives, stops (slides 28-33)
- 9. Speech perception (slides 34-38)
- 10. Prosody (slide 39)

## Known errors and traps in the sources (do not teach the errors)

- **Slide errors:** - Slide 7's figure lists [w], [l] as liquids and [r], [y] as glides. The standard grouping (also on slide 12) is liquids = [l], [r] and glides = [w], [y]. - Slide 8: the text puts affricates among the non-continuants, the figure puts them under continuant cons...
- **Watch out:** $F_0$ is not $F_1$. $F_0$ comes from the source, $F_1$ from the filter. You can sing [i] on any pitch: $F_0$ changes, $F_1$ and $F_2$ stay.
- **Watch out:** Wideband means short window. And a longer window gives clearer harmonics only while $F_0$ stays constant inside it (up to about 30 to 40 ms of real speech).
- **Slide error:** Slide 17 labels the horizontal axes "frequency in kHz", but the tick values run from 0 to 8000. The unit is Hz (0 to 8 kHz).
- **The voice bar on slide 33:** The voice bar is hard to see in the print on slide 33. The closure gaps of *ada* and *aga* look as white as the gaps of the upper row. Only *aba* has at most a faint trace at the bottom of its gap. What the picture does show: after [b], [d], [g] the dark vowel...
- **Watch out:** Slide 35 writes the vowel of "do" as [o]. It is [u]. Also, English word-initial /b d g/ often have VOT near zero, not negative: "do" and "to" differ by short against long VOT.

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
