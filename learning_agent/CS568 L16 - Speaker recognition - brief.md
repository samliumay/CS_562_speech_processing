# Learning brief: CS568 L16 - Speaker recognition

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 28 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_16/CS568 L16 - Speaker recognition.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_16_l16`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_16/l16.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_16/CS568_Lecture_16_Speaker_Recognition.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_16/lecture_16.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Identification, verification, text dependence (slides 2-6)
- 2. Speaker features (slides 7-9)
- 3. Non-parametric models (slides 10-12)
- 4. Parametric models: VQ, GMM, HMM (slides 13-15)
- 5. Adapting from a UBM (slides 16-17)
- 6. Decision rules, threshold, normalisation (slides 18-19)
- 7. Errors, ROC, DET, EER, DCF (slides 20-22)
- 8. Applications (slide 23)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** CMS removes everything constant over the recording, including the speaker's own long-term average spectrum. It does not cancel a channel that changes during the recording.
- **Watch out:** $s_{nn}$ is a similarity (pick the maximum). $s'_{nn}$ is a distance (pick the minimum, it can be negative). A test vector equal to an enrollment vector gives $d_{nn}=0$ and an infinite $s_{nn}$, so real systems floor $d_{nn}$.
- **Slide error:** Slide 13 writes the summand of $D$ as $(x_i-\mu_j)$. A sum-squared error needs the squared norm, as above.
- **Slide wording:** Slide 13 calls $\mu_j$ "the sample mean of test vectors assigned to the $j$-th cluster". That is how k-means defines a centroid while the codebook is trained (there the vectors are the training vectors). For testing a claim, the same slide says the VQ models o...
- **Slide typo:** Slide 14 writes the frame inside the log as $y_y$. The sum runs over $t$, so it is $\mathbf y_t$, as in the formula above.
- **Slide wording:** Slide 19 calls $c_{fa}$ and $c_{fr}$ "rates". They are costs, not error rates.
- **Slide wording:** Slide 20 says "detection error threshold" and "plotted in log scale". The name is detection error trade-off, and the axes are normal-deviate (probit) scaled, not logarithmic.

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
