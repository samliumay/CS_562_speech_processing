# Learning brief: CS568 L07 - Linear prediction of speech

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 23 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_07/CS568 L07 - Linear prediction of speech.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_07_l7`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_07/l7.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_07/CS568_Lecture_07_Linear_Prediction.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_07/lecture_07.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Speech production and the two kinds of peaks (slides 2-3)
- 2. The LP model and the inverse filter (slides 4-7)
- 3. Finding the coefficients: covariance method (slides 8-11)
- 4. Autocorrelation method and Levinson-Durbin (slides 12-13)
- 5. Spectral envelope and model order (slides 14-18)
- 6. Alternative representations: root pairs and LSF (slides 19-22)
- 7. Reflection coefficients and log-area ratios (slides 23-24)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** Slide 7 writes "for a given speech signal $x[n]$". The speech is $y[n]$, $x[n]$ is the excitation. Sign convention here: $A_p(z)=1-\sum_ka_kz^{-k}$. Other books use a plus.
- **Slide errors:** - Slide 9 has a factor 2 on the right-hand side of the final equation. Both terms carry the 2, so it cancels. - Slide 10 says "$\Phi$ is symmetric". The symmetric object is the matrix $\Psi$. $\Phi$ is a vector. - Slide 11 writes the regions as $0\le n<N-1$ an...
- **Slide error:** Slide 12 writes $R(n)=\sum_my[m]\,y[n-m]$. That is the convolution of $y$ with itself. The autocorrelation shifts without flipping: $\sum_my[m]\,y[m+n]$. Also, the final $a_j$ are not the $k_j$ in general (above $a_1=1.111$, $k_1=0.8$).
- **Slide error:** Slide 15 says "the broad spectral peaks (i.e. the harmonics)". The broad peaks are the formants.
- **Slide error:** Slide 22 says $P$ and $Q$ add up to $A_p(z)$. The sum is $2A_p(z)$, so $A_p(z)=\tfrac12[P(z)+Q(z)]$.
- **Watch out:** Slide 24 writes the left-hand side as $A_k$ but calls it $g_k$ in the text. Use $g_k$, to avoid a clash with $A_p(z)$.

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
