# Learning brief: CS568 L04 - Signals and transforms

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 34 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_04/CS568 L04 - Signals and transforms.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_04_l4`, `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_04_l4_demolar_py`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_04/l4.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_04/CS568_Lecture_04_Signals_Transforms.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_04/lecture_04.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Analog signals and sinusoids (slides 2-4)
- 2. Fourier series (slides 5-6)
- 3. Complex exponentials and Fourier analysis (slides 7-9)
- 4. Magnitude and phase (slides 10-11)
- 5. The Fourier transform (slides 12-13)
- 6. Digital signals and aliasing (slides 14-16)
- 7. DTFT and DFT (slides 17-19)
- 8. The DFT as a matrix, examples, FFT (slides 20-26)
- 9. The z-transform and digital spectra (slides 27-29)
- 10. Properties of the transforms (slides 30-32)
- 11. Convolution and stochastic signals (slides 33-36)
- 12. The demo script L4_demolar.py

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** Cutting the series after finitely many terms overshoots next to a jump by about 9% of the jump height (Gibbs). More terms make the overshoot narrower, not smaller: the peak stalls near 1.179.
- **Slide errors:** - Slide 8 writes the symmetry of real signals as "$a_k=-a_k$". Correct: $a_{-k}=a_k^{*}$ (same magnitude, opposite phase). - Slide 9 writes the vanishing integral with exponent $k\omega_0t$. It should be $(n-k)\omega_0t$. The conclusion is the same.
- **Watch out:** Same magnitude spectrum does not mean same waveform. In demo 3 the peak is about 1.18 for the square wave, 3.32 with all phases 0, and 1.66 with random phases.
- **Slide error:** Slide 12 writes the inverse as $\int X(j\omega)e^{-j\omega t}\,dt$. The inverse needs $e^{+j\omega t}$, integration over $\omega$ and the factor $1/(2\pi)$.
- **Watch out:** Aliasing cannot be undone after sampling. Put an anti-aliasing low-pass filter before the sampler. Sampling at exactly $2f$ can hit every zero crossing, so $F_S$ must be above $2F_{\max}$.
- **Slide errors:** - Slide 18 writes "$\omega_k=2\pi F_S/N$". That is the spacing between bins. The $k$-th frequency is $2\pi kF_S/N$. - Slide 19: the inverse DTFT is over one period $[-\pi,\pi]$, not $(-\infty,\infty)$. The inverse DFT sums over $k$, not $n$.
- **Watch out:** Slide 21 labels nine rows $X[0]$ to $X[8]$ for an 8-point matrix. The indices run 0 to 7. Slide 23 calls $F_S/2$ the "Nyquist rate". It is the Nyquist frequency.
- **Slide error:** Slide 28 says the spectrum repeats "at multiples of the Nyquist frequency". The period is $F_S$, twice the Nyquist frequency.
- **Slide error:** The table on slide 30 calls the DTFT "Discrete Fourier Transform (DTFT)". The name is discrete-time Fourier transform. The same slide writes the signals as $s_1,s_2$ and their transforms as $X_1,X_2$: these are the transforms of $s_1$ and $s_2$.
- **Watch out:** Slide 35 calls the sum "the expected value". It estimates the expected value only after dividing by the number of terms.

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
