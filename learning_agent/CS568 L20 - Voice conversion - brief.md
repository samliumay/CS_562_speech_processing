# Learning brief: CS568 L20 - Voice conversion

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 26 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_20/CS568 L20 - Voice conversion.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_20_l20`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_20/l20.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_20/CS568_Lecture_20_Voice_Conversion.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_20/lecture_20.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Voice transformation, terms, applications (slides 2-5)
- 2. Building blocks (slide 6)
- 3. Codebook mapping and frequency warping (slides 7-10)
- 4. GMM-based conversion (slides 11-13)
- 5. Solving for the parameters (slides 14-15)
- 6. Quality against similarity, weighted frequency warping (slides 16-17)
- 7. Conversion without a parallel corpus (slides 18-19)
- 8. Foreign accent conversion (slides 20-22)
- 9. Evaluation and results (slides 23-26)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** A warp only moves energy along the frequency axis. It keeps the spectral detail (good quality) but cannot change the heights of the formants (poor similarity).
- **Slide errors:** - Slide 12 writes $\Gamma=E[(y-\nu)(y-\nu)^{\mathsf T}]$, the target's own covariance. It must be the cross-covariance $E[(y-\nu)(x-\mu)^{\mathsf T}]$. If $y$ were independent of $x$, the estimate should be just $\nu$. - Slide 12 prints the error as $\sum(y_t-...
- **Slide detail:** The figure on slide 19 is labelled "[Abe et al., 1988]". It shows the adaptation scheme of Mouchtaris et al. (2006). Also: the method still needs one parallel corpus, from a different speaker pair.
- **Slide detail:** Slide 26 repeats the title "perceived accent and quality". It shows the speaker identity results.
- **Slide 26 against the sentence above:** The plots do not show that the segmental conversions "move away from the learner" only with reversed speech. In the left plot (normal speech) the segmental and combined points already form their own group, with a clear gap to the learner and prosodic points. I...

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
