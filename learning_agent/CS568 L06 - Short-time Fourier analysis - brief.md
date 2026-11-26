# Learning brief: CS568 L06 - Short-time Fourier analysis

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 25 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_06/CS568 L06 - Short-time Fourier analysis.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_06_l6`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_06/l6.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_06/CS568_Lecture_06_STFT.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_06/lecture_06.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Overview: why a short-time transform (slides 2-4)
- 2. Windowing functions (slides 5-6)
- 3. The STFT and the spectrogram (slides 7-8)
- 4. Filtering view and filter bank (slides 9-14)
- 5. When is the STFT invertible? (slides 15-17)
- 6. Synthesis: filter bank summation (slides 18-19)
- 7. Synthesis: overlap-add (slides 20-22)
- 8. STFT magnitude and its inversion (slides 23-26)

## Known errors and traps in the sources (do not teach the errors)

- **Slide error:** Slide 2 writes the DFT frequencies as $\omega_k=2\pi F_s/N$, with no $k$. The $k$-th frequency is $\hat\omega_k=2\pi k/N$, that is $F_k=kF_S/N$ Hz.
- **Slide error:** Slide 5 gives the Hamming coefficient as 0.4. It is 0.46. The Hann formula on the slide divides by $N-1$: it is the same $N_w-1$.
- **Slide error:** Slides 7 and 15 write the exponential as $e^{-j\omega n}$. The sum runs over $m$, so it must be $e^{-j\omega m}$.
- **Slide errors:** - Slide 9 says $x[n]e^{-j\omega_0n}$ modulates $x[n]$ "up to" $\omega_0$. With the minus sign the band at $\omega_0$ moves down to 0. - Slide 12 writes the filter as $w[n]e^{-j\frac{2\pi}{N}kn}$. The band-pass filter has the plus sign. The demodulator has the ...
- **Watch out:** One direction only. $L\le N_w$ does not by itself make a resynthesis method exact: OLA still needs its window condition. And if the window is placed on $0,\dots,N_w-1$, then $w[0]$ is its first sample, which is 0 for Hann.
- **Slide errors:** Slide 18 writes the sum over $m=-\infty..\infty$. It runs over the channels $k=0..N-1$. Slide 19 writes condition 1 as "$N_w>N$": it must be $N_w\le N$. "BFS constraint" should read FBS.
- **Slide error:** The OLA formula on slide 20 omits the $1/N$ of the inverse DFT.
- **Watch out:** "Converges" means the error stops decreasing, not that the original signal comes back. Different starting noises can end in different local minima.
- **Slide error:** Slide 24 writes the left side of the magnitude step as $X^1(m,\omega)$ while the right side has the index $i$. It is $X^i(m,\omega)$, as on slide 25.

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
