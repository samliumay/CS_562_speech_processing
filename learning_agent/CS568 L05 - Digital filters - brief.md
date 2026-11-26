# Learning brief: CS568 L05 - Digital filters

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 23 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_05/CS568 L05 - Digital filters.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_05_l5`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_05/l5.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_05/CS568_Lecture_05_Digital_Filters.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_05/lecture_05.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Filters, LTI, FIR and IIR (slides 2-4)
- 2. The impulse response (slides 5-6)
- 3. Convolution sum and transfer function (slides 7-8)
- 4. Poles and zeros (slides 9-13)
- 5. Frequency response and the one-pole resonator (slides 14-17)
- 6. Conjugate pole pairs and zeros (slides 18-21)
- 7. Example: synthesising the vowel [ih] (slides 22-24)

## Known errors and traps in the sources (do not teach the errors)

- **Slide error:** Slide 3 starts the feedback sum at $l=0$. That term would be $a_0y[n]$: the output would depend on itself. The sum starts at $l=1$.
- **Slide errors:** - Slide 5 swaps the two cases of $\delta[n]$. - "For $a_1>1$" should be $|a_1|>1$: $a_1=-1.2$ also blows up. - The two second-order filters are printed as $y[n]=-1.8y[n-1]+y[n-2]$ and $y[n]=-1.78y[n-1]+0.9y[n-2]$, without $x[n]$. With those plus signs one root...
- **Slide errors:** Slide 8 writes the highest powers as $z^{M-1}$ and $z^{N-1}$ (they are $z^{-M}$, $z^{-N}$) and the denominator as $\sum_{l=0}^{N}a_lz^{-l}$. With our sign convention it is $1-\sum_{l=1}^{N}a_lz^{-l}$. MATLAB `filter(b,a,x)` and SciPy `lfilter` use the opposite...
- **Slide errors:** Slide 11 writes the second factor as $(1-p_2z^{-2})$. It is $(1-p_2z^{-1})$. Slide 13 writes $z^{M}$, $z^{N}$ for $z^{-M}$, $z^{-N}$ and drops the gain $G$.
- **Slide error:** The right plot of slide 10 is labelled $1/(2x^2-6x+1)$, but the drawn curve does not fit this label. Between the two roots $f$ is negative ($f(1.5)=-3.5$), so $1/f$ is negative there (about $-0.29$) and positive outside the roots. The plot shows the opposite: ...
- **Slide error:** Slide 16 writes "$h[n]=b_0x[n]-a_1y[n-1]$" and "$b_1=1$". To match $H(z)$ it must be $y[n]=b_0x[n]+a_1y[n-1]$ with $b_0=1$.
- **Slide errors:** Slide 18 ends the denominator with $r^2z^{-1}$. It is $r^2z^{-2}$. The table on slide 19 has truncated values that do not fit the stated $r$ and $\theta$ (for $r=0.7$: $a_1=0.76$, $a_2=-0.49$, not $0.75$ and $-0.48$).
- **Slide error:** The pole column of the slide table reads $0.963+0.116j$, $0.619+0.719j$, $0.370+0.874j$. The first has radius 0.970, not 0.952. The values above are recomputed from the formulas.

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
