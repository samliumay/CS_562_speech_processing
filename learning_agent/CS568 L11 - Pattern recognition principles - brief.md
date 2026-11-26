# Learning brief: CS568 L11 - Pattern recognition principles

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 28 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_11/CS568 L11 - Pattern recognition principles.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_11_l11`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_11/l11.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_11/CS568_Lecture_11_Pattern_Recognition.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_11/lecture_11.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Bayes decision rule and risk (slides 2-5)
- 2. Quadratic (Gaussian) classifiers (slides 6-10)
- 3. k nearest neighbours (slides 11-13)
- 4. Curse of dimensionality (slides 14-17)
- 5. PCA and LDA (slides 18-22)
- 6. Gaussian mixture models and EM (slides 23-29)
- 7. k-means and vector quantisation (slides 30-32)

## Known errors and traps in the sources (do not teach the errors)

- **When zero-one loss is not advisable (slide 5 question):** When errors have very different costs: a missed medical condition, an impostor accepted by speaker verification. Example: $P(\text{genuine}\mid x)=0.7$, accepting an impostor costs 10, rejecting a genuine user costs 1. $R(\text{accept})=10\cdot0.3=3$, $R(\text...
- **Slide wording (slide 5):** The last line of slide 5 says "in order to minimize classification rate". The word "error" is missing: it means the classification **error** rate. We minimise the error rate, which is the same as maximising the rate of correct classification.
- **Watch out:** - The slide writes the bound with strict $<$. Equality is possible (Bayes error 0), so $\le$ is correct. - kNN and k-means share only the letter $k$. kNN: $k$ voting neighbours, labelled data. k-means: number of clusters, no labels.
- **Watch out:** - High variance does not mean good class separation. PCA never looks at the labels. - Slide 21 writes the mean constraint as $\sum_i\mu_i=\mu$. The correct weighted form is $\frac1N\sum_iN_i\mu_i=\mu$. - Strictly the projection is $y=A^{\mathsf T}(x-\mu)$, wit...
- **Watch out:** EM reaches only a local maximum, so the result depends on the initialisation. A component can collapse onto one point ($\Sigma_c\to0$), so implementations regularise or floor the covariances. The slide says the likelihood "increases" at each step. Strictly it ...

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
