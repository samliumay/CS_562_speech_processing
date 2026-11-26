# Learning brief: CS568 L09 - Cepstral analysis

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 22 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_09/CS568 L09 - Cepstral analysis.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_09_l9`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_09/l9.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_09/CS568_Lecture_09_Cepstral_Analysis.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_09/lecture_09.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Definition of the cepstrum (slides 2-3)
- 2. Why it works: log spectrum as a waveform, deconvolution (slides 4-7)
- 3. Liftering and what it keeps (slides 8-11)
- 4. Homomorphic filtering (slides 12-15)
- 5. Voicing and pitch detection (slides 16-17)
- 6. Linear prediction cepstral coefficients (slide 18)
- 7. Mel frequency cepstral coefficients (slides 19-21)
- 8. MFCC notes: homomorphic or not, DCT (slides 22-24)

## Known errors and traps in the sources (do not teach the errors)

- **Slide notation:** The slide uses the same index $n$ for time, the frequency sum and quefrency, calls the frame $y[n]$ but writes $x[n]$, and omits the $1/N$ of the IDFT.
- **Watch out:** - $\log(ab)=\log a+\log b$, but $\log(a+b)\ne\log a+\log b$. The cepstrum separates convolved parts, not added ones such as speech plus noise. - The slide dates the term to 1960. The paper was published in 1963.
- **Slide error:** Slide 13, second equation: the middle expression is printed as $D_*^{-1}[y_1(n)]*D_*^{-1}[y_2(n)]$. The inputs of $D_*^{-1}$ are the hatted signals. It should read $D_*^{-1}[\hat y_1(n)]*D_*^{-1}[\hat y_2(n)]=y_1(n)*y_2(n)$.
- **One direction only:** A strong peak means voiced. No peak does **not** prove unvoiced: the window must hold at least 2 to 3 pitch periods, and $F_0$ may change inside the window.
- **Slide detail:** The slide gives the middle case for $1<n\le p$. It also holds for $n=1$ (empty sum, $c_1=a_1$). The third case ($n>p$) is not on the slide.
- **Formula variant:** The slide formula (Rabiner and Schafer) has $2\pi/R$ in the cosine. Most toolkits use the DCT-II with $\pi/R$. The idea is the same.
- **Slide detail: the normalisation on slide 20:** The slide sums $\lvert V_r[k]X(n,k)\rvert$ (no square) but defines $A_r$ with a square. The two formulas do not match each other (this may be only a choice of notation). Then "a flat spectrum gives a flat mel spectrum" is only roughly true. Take $\lvert X\rver...
- **Slide error:** The slide writes the DFT kernel as $e^{+j\frac{2\pi}{N}kn}$. That is the IDFT kernel (without $1/N$).

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
