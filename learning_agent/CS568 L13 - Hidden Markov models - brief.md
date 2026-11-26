# Learning brief: CS568 L13 - Hidden Markov models

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 25 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_13/CS568 L13 - Hidden Markov models.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_13_l13`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_13/l13.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_13/CS568_Lecture_13_HMMs.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_13/lecture_13.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. Discrete Markov processes (slides 2-4)
- 2. From Markov models to HMMs (slides 5-9)
- 3. Elements of an HMM and the three problems (slides 10-12)
- 4. Forward and Backward procedures (slides 13-16)
- 5. The Viterbi algorithm (slides 17-22)
- 6. Baum-Welch re-estimation (slides 23-25)

## Known errors and traps in the sources (do not teach the errors)

- **Watch out:** - In $a_{ij}$ the first index is the state we come from. "Rain tomorrow given sun today" is $a_{31}$. Some versions of the slide write the product with swapped indices, but the numbers and the result are right. - The geometric duration always has its most like...
- **Slide notation (slide 11):** Step 1 says "choose an initial state $S_1$". It means the first state $q_1$, which can be any of the $N$ states. In the same way "$S_{t+1}$" in step 4 means the next state $q_{t+1}$. Steps 3 and 4 use $b_j(k)$ and $a_{ij}$ of the state the model is in at time ...
- **Watch out:** $\alpha_t(i)$ is a joint probability (the state is inside the event). $\beta_t(i)$ is conditioned on the state.
- **Slide notation (slides 13-15):** - Slide 13, last formula: the slide writes $b_{q_1}(o_{q_1})$. The observation index is the time, so it should read $b_{q_1}(o_1)$, $b_{q_2}(o_2)$, and so on. - Slide 14 prints the order of computations as "$10^7$". This is a typo for $10^{72}$: for $N=5$, $T=...
- **Slide wording:** The slide says the second criterion maximises "the posterior $P(O\mid Q,\lambda)$". That expression is a likelihood. Viterbi maximises the posterior $P(Q\mid O,\lambda)$, which is the same as maximising the joint $P(Q,O\mid\lambda)$.
- **Slide error:** The slide says the procedure runs "until a local minimum is found". The likelihood increases, so it converges to a local **maximum**. The result depends on the initial model. The slide title spells "Baum-Welsh"; the name is Baum-Welch.

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
