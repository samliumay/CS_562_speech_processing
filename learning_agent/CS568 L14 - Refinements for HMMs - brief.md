# Learning brief: CS568 L14 - Refinements for HMMs

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 26 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_14/CS568 L14 - Refinements for HMMs.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_14_l14`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_14/l14.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_14/CS568_Lecture_14_HMM_Refinements.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_14/lecture_14.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Types of HMM structure (slide 2)
- 2. Implementation issues (slides 3-4)
- 3. Discrete, continuous and semi-continuous HMMs (slides 5-9)
- 4. Robustness to noise and channel (slides 10-14)
- 5. Speaker-independent recognition (slides 15-16)
- 6. Model adaptation (slides 17-20)
- 7. Discriminative training (slides 21-26)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** Pool the counts, do not average per-utterance ratios. Counts $(1.2, 2.0)$ and $(0.8, 3.0)$ give $\hat a_{ij}=2.0/5.0=0.4$, not $(0.6+0.267)/2=0.433$.
- **Watch out:** CMN removes only a channel that is constant over the window. It does not remove additive noise.
- **GPD on the slide:** The slide mixes two sign conventions. In the original formulation $g_k$ is a score where larger is better and $d_j(x)=-g_j(x)+\frac1\eta\log\big[\frac{1}{C-1}\sum_{k\ne j}e^{\eta g_k(x)}\big]$, so $d_j>0$ means an error. Also, the inner sum of $E(\theta)$ shou...

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
