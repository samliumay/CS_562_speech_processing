# Learning brief: CS568 L15 - Large vocabulary speech recognition

This file is for the LearnHarness teacher agent. Read it first, then read the files it lists.

## Goal

Teach me this lecture of CS 568 Speech Processing so that I understand every slide and can answer its questions without help.
Exams are conceptual: expressions and what is what, comparing methods, explaining figures. No direct solving of long numeric problems.

## Sources, in priority order

1. **Lesson note** (my notes, slide by slide, with 37 check questions and their answers): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/lessons/lecture_15/CS568 L15 - Large vocabulary speech recognition.md`
2. **Quiz note:** not written yet for this lesson. Use the check questions of the lesson note.
3. **Slide images** (one jpg per slide; the lesson note embeds them as `![[<deck>_pNNN.jpg]]`): `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/CS_568_Speech_Processing/others/slides/cs568_lecture_15_l15`
4. **Original slides (PDF):** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_15/l15.pdf`
5. **Full step-by-step notes:** `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_15/CS568_Lecture_15_LVCSR.pdf` (LaTeX source: `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_15/lecture_15.tex`)

The slides are the authority for what the course teaches. Text marked "(beyond the slides)" in the lesson note is extra explanation: use it to explain, do not present it as exam material.

## What the lecture covers

- 1. The task and the fundamental equation (slides 2-6)
- 2. Triphones and why they are hard to train (slides 7-11)
- 3. Training tied-state models (slides 12-14)
- 4. Phonetic decision trees (slides 15-17)
- 5. N-grams and perplexity (slides 18-20)
- 6. Smoothing the language model (slides 21-23)
- 7. Decoding with Viterbi and token passing (slides 24-29)
- 8. Making the search fast (slides 30-35)
- 9. Word error rate and insertion control (slides 36-38)

## Known errors and traps in the sources (do not teach the errors)

- **Slide error:** Slide 9 writes "30×10 means, 30×10 variances". With 39-dimensional features it is $39\times10$ each. Only that gives the 790 on the same slide.
- **Watch out:** Splitting never lowers the likelihood, so $\Delta L_q\ge0$ for every question. A positive gain does not justify a split. The thresholds decide.
- **Watch out:** Lower perplexity does not guarantee a lower WER. Perplexity ignores which words sound alike.
- **Two slide errors:** - Slide 21 gives "8e6" trigrams for 20k words. Correct: $(2\times10^4)^3=8\times10^{12}$. - Slide 23 calls $K$ "the number of different words". $K$ must be the total number of words (tokens) in the corpus, so that $C(w_k)/K$ sums to one. Corpus "a a b": $2/3+1...
- **Watch out:** Beam search is not exact. A pruned token may have been on the best complete path (a search error). A wider beam makes this rarer but costs more.
- **Watch out:** WER can exceed 100% because insertions count. An empty output has WER exactly 100% (all deletions).

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
