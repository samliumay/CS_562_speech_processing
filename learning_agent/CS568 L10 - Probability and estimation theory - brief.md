# Learning brief: CS568 L10 - Probability and estimation theory

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 34 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_10/CS568 L10 - Probability and estimation theory.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_10_l10`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_10/l10.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_10/CS568_Lecture_10_Probability_Estimation.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_10/lecture_10.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Probability basics (slides 2-6)
- 2. Bayes theorem (slides 7-12)
- 3. Random variables, cdf and pdf (slides 13-17)
- 4. Random vectors and covariance (slides 18-20)
- 5. The Gaussian and the Central Limit Theorem (slides 21-23)
- 6. Estimation: least squares (slides 24-31)
- 7. Maximum likelihood estimation (slides 32-33)
- 8. Bayesian (MAP) estimation (slides 34-35)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** Mutually exclusive ($A\cap B=\emptyset$) is not the same as independent ($P[A\cap B]=P[A]P[B]$). Also $P[A\mid B]\ne P[B\mid A]$ in general.
- **Base-rate neglect:** A positive result from a "90%/98%" test gives only about 31%, because the prior is 1%. The false positives from the large healthy group outnumber the true positives.
- **A density is not a probability (slide 16):** The pdf value at 124.876 lb is not the probability of weighing exactly that. Any single value of a continuous variable has probability zero. Integrate over an interval: $P\approx f_X(x_0)\,\Delta$. A pdf can be larger than 1. The weight curve on the slide is o...
- **Slide error (slide 15):** The slide lists the property $f_X(x)>0$. The correct property is $f_X(x)\ge0$: a pdf can be zero, for example a uniform pdf outside its interval. Two small notation slips with the same cause: the axis label of both plots on slide 14 is "P(X<x)", and the last l...
- **Slide error (slide 20):** The slide writes $\lvert c_{ik}\rvert\le\sigma_1\sigma_k$. The index 1 is a typo for $i$: the bound is $\lvert c_{ik}\rvert\le\sigma_i\sigma_k$. The same line of the slide goes on with "where $\sigma_i$ is the standard deviation of $x_i$", and the panels below...
- **Watch out:** - Uncorrelated implies independent only for jointly Gaussian variables. - In the CLT, $N$ is the size of each averaged sample, not the number of experiments (500).
- **Slide error (slide 28):** The slide writes $a=\mathrm{Cov}(X,Y)/\mathrm{Var}(Y)=\rho_{xy}\sigma_x/\sigma_y$ and says "estimate for $c$". The correct slope divides by $\mathrm{Var}(X)$: $a=\rho_{xy}\sigma_y/\sigma_x$. Check with $Y=3X$: the correct formula gives 3, the slide formula giv...
- **Biased variance:** $\sigma^2_{\mathrm{MLE}}$ divides by $n$. Its expected value is $\frac{n-1}{n}\sigma^2$. The unbiased estimator divides by $n-1$. Example: $x=(2,4,6,8)$ gives $\mu=5$, $\sigma^2_{\mathrm{MLE}}=5$, unbiased $6.67$.

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
