# Learning brief: CS568 L08 - Source estimation

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 24 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_08/CS568 L08 - Source estimation.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_08_l8`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_08/l8.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_08/CS568_Lecture_08_Source_Estimation.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_08/lecture_08.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Review: speech as filters in series (slides 2-3)
- 2. Lip radiation (slide 4)
- 3. Glottal source models (slides 5-10)
- 4. The residual and why it is not the glottal source (slides 11-14)
- 5. Closed-phase analysis (slides 15-17)
- 6. Voiced/unvoiced detection (slides 18-21)
- 7. Pitch detection (slides 22-23)
- 8. Epoch detection (slides 24-26)

## Known errors and traps in the sources (do not teach the errors)

- **Slide error: octave, not decade:** The slide says "about 6 dB per decade". A single zero gives $+6$ dB per **octave** (a doubling), which equals $+20$ dB per decade.
- **Watch out:** - The slide says "$-12$ dB/decade" for the glottal roll-off. A double pole gives $-12$ dB per **octave** ($-40$ dB/decade). - The slide spells the name "Lijencrants". It is Liljencrants. - The LF $\alpha,\beta$ are shape parameters. They are not the radiation ...
- **Slide notation:** The slide writes the residual as $\sum_{k=0}^{p}a_k\,y[n-k]$. That holds only with $a_0=1$ and the other signs flipped. On slide 13 the glottal filter is a ratio of sums (direct form), as written above.
- **Watch out:** The integrator pole is at $z=\alpha$. It is stable only for $\alpha<1$. With $\alpha=1$ a DC offset grows without bound ($x[n]=n+1$ for input 1); with $\alpha=0.95$ it settles at $1/(1-\alpha)=20$.
- **Watch out:** Each sign change adds 2 to the sum in $Z$ (many texts use $\frac{1}{2L}$). Silence has low energy **and** low zero-crossing rate, so one feature alone does not separate silence, voiced and unvoiced. Use both.
- **Octave errors:** A periodic signal also matches itself after two periods. If that peak wins, the PDA reports $F_0/2$. Raw PDA output also has spurious values in unvoiced regions. So it is smoothed and interpolated (slide 23).

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
