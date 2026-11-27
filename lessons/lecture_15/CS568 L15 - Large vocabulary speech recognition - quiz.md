---
course: CS 568 Speech Processing
lesson: CS568 L15 - Large vocabulary speech recognition
type: quiz
questions: 37
---

# CS568 L15 - Large vocabulary speech recognition - quiz

Lesson note: [[CS568 L15 - Large vocabulary speech recognition]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. The task and the fundamental equation (slides 2-6)

### Q1 (slide 2)

The speaker says "I want to fly to Boston". The system outputs "I want two fly to Boston". Speech transcription wants the exact words; speech understanding wants the meaning. How is this output judged for each task?

- A) An error for both tasks. Both count every wrong word, and "two" is a wrong word in both.
- B) No error for transcription, because "to" and "two" sound the same. An error for understanding.
- C) One substitution for transcription. Most likely no error for understanding, as the meaning stays.
- D) No error for either task. The two outputs sound the same, and both tasks judge the sound only.

> [!success]- Answer
> **C.** Transcription wants the exact text. "to" became "two", which is one substitution error. Understanding wants the meaning: a flight to Boston. A word error does not matter if the meaning stays the same.
> - A: Only transcription counts word errors. Understanding judges the inferred meaning.
> - B: The direction is reversed. Transcription compares the written words, so a homophone is still an error; the meaning is unchanged, so understanding is fine.
> - D: Neither task judges the sound. Transcription compares written words, and there one word is wrong.

### Q2 (slide 3)

Recognition picks $\hat W=\arg\max_W P(Y\mid W)\,P(W)/P(Y)$, with $Y$ the feature vectors of one utterance and $W$ a word sequence. A student writes: "We drop $P(Y)$ because it is equal to 1, and $P(W)$ is learned from recorded speech." Which version of the sentence is correct?

- A) $P(Y)$ equals 1 for normalised features, so it drops out; $P(W)$ is estimated from text.
- B) $P(Y)$ is the same for every hypothesis $W$, so it drops out; $P(W)$ is learned from recorded speech.
- C) $P(Y)$ is the same for every hypothesis $W$, so it drops out; $P(W)$ is estimated from text.
- D) $P(Y)$ equals 1 for normalised features, so it drops out; $P(W)$ is learned from recorded speech.

> [!success]- Answer
> **C.** $P(Y)$ is not 1. It does not depend on $W$, so every hypothesis is divided by the same number and the position of the maximum does not change. $P(W)$ is the language model, built from text that is representative of the application.
> - A: The first part is the student's mistake: $P(Y)$ is not 1. It is dropped only because it is the same for all $W$.
> - B: The second part is the student's mistake. The term learned from recorded utterances is $P(Y\mid W)$, the acoustic model.
> - D: This is the student's sentence unchanged, with both mistakes.

### Q3 (slide 4)

"In principle" a recogniser builds and scores one sentence HMM for every word sequence. The vocabulary has 20,000 words. How many 5-word sequences exist? (Words may repeat.)

- A) $1.0\times10^{5}$
- B) $3.2\times10^{21}$
- C) $1.6\times10^{17}$
- D) $2.7\times10^{19}$

> [!success]- Answer
> **B.** Each of the 5 positions can hold any of the 20,000 words: $20{,}000^5=(2\times10^4)^5=3.2\times10^{21}$. Nobody can score that many sentence HMMs for one utterance, so the decoder is what makes the $\arg\max$ computable: it shares work between hypotheses and drops unlikely ones.
> - A: $20{,}000\times5$ multiplies by the number of positions instead of raising to that power.
> - C: $20{,}000^4$ uses one position too few.
> - D: $20{,}000^5/5!$ treats a sequence as an unordered set of words. Word order matters in a sentence.

### Q4 (slide 5)

A recogniser hears "ten pots". The front end turns the waveform into feature vectors $Y$. The word row "ten | pots" is turned into the phone row "t e n p o t s", and the phone HMMs are joined into one HMM. The decoder box multiplies two quantities and outputs "ten pots". Which two quantities enter the decoder, and where does each come from?

- A) $P(Y\mid W)$ from the acoustic models and $P(Y)$ from the language model.
- B) $P(Y\mid W)$ from the acoustic models and $P(W)$ from the language model.
- C) $P(W\mid Y)$ from the acoustic models and $P(W)$ from the language model.
- D) $P(W\mid Y)$ from the acoustic models and $P(Y)$ from the language model.

> [!success]- Answer
> **B.** The joined phone HMMs are compared with the front-end output $Y$, which gives $P(Y\mid W)$. The language model gets the word sequence $W$ and gives $P(W)$. The decoder computes $P(Y\mid W)\cdot P(W)$. (The words are turned into phones by the pronunciation dictionary.)
> - A: $P(Y)$ is not computed at all, since it is the same for every $W$. The language model works with words only, so it gives $P(W)$.
> - C: An HMM gives the likelihood of the observations given the model, $P(Y\mid W)$. $P(W\mid Y)$ is the posterior that the whole equation maximises.
> - D: This swaps both terms of Bayes' rule: the posterior for the likelihood and $P(Y)$ for the prior $P(W)$.

### Q5 (slide 6)

"grey day" and "grade A" can have almost the same acoustic information. The recogniser maximises $P(Y\mid W)\,P(W)$, with $Y$ the feature vectors and $W$ the word sequence. Which term can still choose between the two word sequences?

- A) $P(Y\mid W)$, because the acoustic model hears the short pause at the word boundary.
- B) $P(Y)$, because it measures how likely the sound itself is in the language.
- C) $P(Y\mid W)$, because it is conditioned on $W$, and the two word sequences differ.
- D) $P(W)$, because the language model scores which word sequence fits the context.

> [!success]- Answer
> **D.** Both word sequences give almost the same phone sequence, and coarticulation hides the word boundary. So $P(Y\mid W)$ is almost equal for both. The language model gives a higher probability to the sequence that fits the context better.
> - A: Coarticulation blurs word boundaries. In fluent speech there is usually no pause between words.
> - B: $P(Y)$ does not depend on $W$, so it cannot prefer one word sequence. It is dropped from the maximisation.
> - C: Being conditioned on $W$ is not enough. Both word sequences give almost the same phones [g r ey d ey], so the acoustic models predict almost the same $Y$ and the two likelihoods are almost equal.

## 2. Triphones and why they are hard to train (slides 7-11)

### Q6 (slide 7)

A new word must be added to a recogniser. What has to be done in a whole-word system (one HMM per word), and what in a phoneme-based system (about 44 phone HMMs and a pronunciation dictionary)?

- A) Whole-word: add one dictionary line. Phoneme-based: retrain the phone HMMs on recordings of the word.
- B) Whole-word: train a new word HMM on many recordings. Phoneme-based: add one dictionary line.
- C) Whole-word: train a new word HMM on many recordings. Phoneme-based: retrain the phone HMMs on recordings of the word.
- D) Whole-word: add one dictionary line. Phoneme-based: add one dictionary line.

> [!success]- Answer
> **B.** A whole-word system needs a new HMM for the word, so many recordings of that word. A phoneme-based system reuses the phone models that already exist; it only needs a new dictionary line with the word and its phones. (The language model also needs a probability for the new word.)
> - A: This reverses the two systems. A whole-word system has no phone dictionary, and the phone HMMs already cover the sounds of the new word.
> - C: The phone HMMs are shared by all words. A new word made of known phones needs no new acoustic training.
> - D: A whole-word system has no phone units to look up. Its model is the word HMM itself, and that has to be trained.

### Q7 (slide 8)

Cross-word triphones are written ${}_{l}p_{r}$: phone $p$ with left neighbour $l$ and right neighbour $r$, also across a word boundary. Compare the last phone [n] of "ten" in "ten pots" and in "ten days" (the first phone of "days" is [d]). What does this show about decoding?

- A) The last model of "ten" depends on the next word, so the decoder needs a different ending of "ten" for each possible next word.
- B) The last model of "ten" depends on the next word, so the decoder needs a separate language model for each possible next word.
- C) The model of "ten" is the same in both phrases, so the decoder can keep one fixed HMM sequence for each word.
- D) Only the first model of the next word depends on "ten", because coarticulation acts forward and never backward.

> [!success]- Answer
> **A.** "ten pots" gives ${}_{e}n_{p}$, "ten days" gives ${}_{e}n_{d}$. The HMM sequence of a word is not fixed: its last model depends on the following word. This is why cross-word triphones complicate decoding.
> - B: The language model works with words and is not changed. The extra cost is in the acoustic models of the word ending.
> - C: This holds for word-internal triphones (${}_{e}n_{-}$ in both phrases), not for cross-word triphones.
> - D: Coarticulation is mostly anticipatory: the right neighbour [p] or [d] changes the [n] of "ten".

### Q8 (slide 9)

A language has 40 phones. A system uses 10,000 triphones, 3 emitting states each, 4 Gaussian mixture components per state, 39-dimensional features and diagonal covariances. How many triphones are possible, and how many parameters does the system have?

- A) 64,000 triphones and 9,360,000 parameters
- B) 1,600 triphones and 9,480,000 parameters
- C) 64,000 triphones and 9,480,000 parameters
- D) 1,600 triphones and 9,360,000 parameters

> [!success]- Answer
> **C.** Possible triphones: 40 choices for the left phone, the centre and the right phone, $40^3=64{,}000$.
> Per state: $39\times4=156$ means, $156$ variances, 4 weights: $316$. Per triphone: $316\times3=948$. Total: $948\times10{,}000=9{,}480{,}000$.
> - A: $(156+156)\times3\times10{,}000=9{,}360{,}000$ forgets the 4 mixture weights of each state.
> - B: $40^2=1{,}600$ counts only one neighbour. A triphone has a left and a right context.
> - D: Both slips: $40^2$ triphones and no mixture weights.

### Q9 (slide 10)

The triphone ${}_{k}e_{n}$ (phone [e], left neighbour [k], right neighbour [n]) has too little training data. Which statement about the remedies is correct?

- A) The back-off chain goes through the left-biphone ${}_{k}e$, because the sound just made still lingers in the vowel.
- B) Backing off and interpolation are the same remedy: both replace the triphone by the less specific model.
- C) Interpolation is used only for unseen triphones, while backing off mixes the parameters of seen ones.
- D) The back-off chain goes through the right-biphone $e_{n}$, because the mouth already prepares the next sound.

> [!success]- Answer
> **D.** Chain: ${}_{k}e_{n}$, then $e_{n}$ (only the right context kept), then the monophone $e$. Coarticulation tends to be anticipatory, so the right neighbour changes a phone more than the left one.
> - A: This keeps the wrong side. The anticipatory effect of the next sound is the stronger one.
> - B: Backing off chooses one model. Interpolation mixes the parameters of the specific and the less specific model, so some context information stays.
> - C: The roles are swapped. Mixing parameters is interpolation; choosing the less specific model is backing off.

### Q10 (slide 11)

Three triphones model the phone [o]: (1) ${}_{p}o_{t}$, (2) ${}_{p}o_{s}$, (3) ${}_{n}o_{t}$, with the notation ${}_{l}p_{r}$ (left neighbour $l$, right neighbour $r$). Each has 3 states: the first is the entry into [o], the last the exit. Which statement is correct?

- A) (1) and (3) should be similar in the last state but not the first, so tying whole models fits this pair badly.
- B) (1) and (2) should be similar in the last state but not the first, because the left context [p] shapes the exit.
- C) (1) and (3) should be similar in all three states, because the right context decides the whole phone.
- D) (2) and (3) should be similar in the first state, because both contain the same vowel [o].

> [!success]- Answer
> **A.** (1) and (3) share the right context [t], so their last states should be similar; their first states differ ([p] against [n]). Tying whole models assumes that all states are equally similar, which is wrong here. Tying single states can share the last state of (1) with (3) and the first state of (1) with (2).
> - B: The left context [p] affects the entry into [o], so (1) and (2) are similar in the first state. Their last states differ ([t] against [s]).
> - C: The left contexts differ, so the first states differ. Context changes mainly the part of the phone next to it.
> - D: (2) and (3) share no context: left [p] against [n], right [s] against [t]. All three triphones contain [o].

## 3. Training tied-state models (slides 12-14)

### Q11 (slide 12)

Mixture splitting: a state has one Gaussian. It is split: two copies, means moved by $\pm0.2\sigma$, weight 0.5 each. After re-estimation the weights are 0.6 and 0.4. The next step splits the component with the largest weight. What are the three weights right after that split, before re-estimation?

- A) 0.5, 0.5 and 0.4
- B) 0.6, 0.6 and 0.4
- C) 0.2, 0.2 and 0.6
- D) 0.3, 0.3 and 0.4

> [!success]- Answer
> **D.** The component with weight 0.6 is split. Its two copies share its weight: $0.6/2=0.3$ each (beyond the slides: the usual HTK rule; the slide gives only the first split). The other component keeps 0.4. Check: $0.3+0.3+0.4=1$. Then all parameters are re-estimated again.
> - A: This copies the 0.5 of the first split. That worked only because the split component had weight 1. Here the sum would be $1.4$.
> - B: Each copy keeps the full weight of the split component. The sum would be $1.6$.
> - C: This splits the component with weight 0.4, the smaller one. The rule splits the one with the largest weight.

### Q12 (slide 13)

![[cs568_lecture_15_l15_p013.jpg]]

The figure shows four /e/ triphones with three states each. How many separate output distributions exist in the row "Tied-state single-Gaussian triphones"?

- A) 12
- B) 4
- C) 6
- D) 3

> [!success]- Answer
> **C.** The lines show that states of different triphones point to the same box. There are 6 boxes, so 6 distributions, each shared with its training data by the states tied to it.
> - A: $4\times3=12$ is the row before tying, one Gaussian under every state.
> - B: 4 would be one model per triphone. Tying is done for states, not whole models.
> - D: 3 is the monophone, one distribution per state position, with all context lost.

### Q13 (slide 14)

Someone proposes: "Train monophones with 10-component Gaussian mixtures first, then clone them into triphones and tie the states." Which statement gives the problems of this order?

- A) Baum-Welch cannot re-estimate monophone mixtures, so mixtures must wait until the states are tied.
- B) The components would capture speaker variability too early, so the triphones could not learn it later.
- C) The order changes only the training time. The final models are the same in both orders.
- D) The components would learn context variation, and tying would have to compare mixtures, not single Gaussians.

> [!success]- Answer
> **D.** The mixture components of a monophone would learn the context variation (an /e/ before a nasal against an /e/ before a stop). That job belongs to the triphones. Also, tying measures how similar two states are, which is simple with one Gaussian per state and much harder with mixtures.
> - A: Baum-Welch re-estimates mixtures without problem. The reason for the order is what the components learn, not whether they can be trained.
> - B: Speaker variability is exactly what the mixture components are meant for. Triphones model context, not speakers.
> - C: The order changes what the components model and which states are tied, so the final models differ.

## 4. Phonetic decision trees (slides 15-17)

### Q14 (slide 15)

The triphone ${}_{z}e_{k}$ never occurs in the training set. What does a system with bottom-up clustering do with it, and what does a system with a phonetic decision tree do?

- A) Both systems back off to the monophone $e$, since there is no data for ${}_{z}e_{k}$ in either system.
- B) Bottom-up backs off to a biphone or monophone; the tree places ${}_{z}e_{k}$ at a leaf from its context.
- C) Bottom-up merges ${}_{z}e_{k}$ with its nearest trained state; the tree system backs off to a biphone.
- D) Both systems back off, since the tree questions are answered by the training frames of the triphone.

> [!success]- Answer
> **B.** Bottom-up clustering merges trained states, and ${}_{z}e_{k}$ has none, so the system backs off and loses context. The tree questions need only the names of the left and right phones. ${}_{z}e_{k}$ answers them, reaches a leaf, and uses the tied state trained on seen triphones with a similar context. The slide says this is more accurate than backing off.
> - A: The tree system needs no data for the triphone itself, so it does not back off.
> - C: There is no trained state of ${}_{z}e_{k}$ to merge, and the tree is the system that avoids backing off.
> - D: The questions ask about the context ("is the right phone a stop?"). The phone names answer them, not frames.

### Q15 (slide 16)

A decision tree clusters the centre state of /e/ triphones ${}_{l}e_{r}$ (left neighbour $l$, right neighbour $r$). Stops are [p t k b d g]; nasals are [m n]. Root: "right = stop?". Yes: "left = nasal?" (yes: leaf e2-5, no: leaf e2-4). No: "right = /l/?" (yes: leaf e2-3, no: "left = stop?", yes: leaf e2-2, no: leaf e2-1). Which triphone has its centre state tied with that of ${}_{t}e_{n}$?

- A) ${}_{s}e_{p}$
- B) ${}_{s}e_{n}$
- C) ${}_{m}e_{t}$
- D) ${}_{p}e_{n}$

> [!success]- Answer
> **D.** ${}_{t}e_{n}$: right = stop? [n] is a nasal, no. Right = /l/? No. Left = stop? [t] yes. Leaf e2-2. ${}_{p}e_{n}$ gives the same answers (no, no, yes), so it is tied at e2-2.
> - A: ${}_{s}e_{p}$ reaches e2-4. ${}_{t}e_{n}$ reaches it only if the nasal [n] is counted as a stop.
> - B: ${}_{s}e_{n}$ has the same right neighbour, but its left [s] is not a stop, so it reaches e2-1. The left context also matters.
> - C: ${}_{m}e_{t}$ reaches e2-5. ${}_{t}e_{n}$ reaches it only if left and right are swapped (right [t] a stop, left [n] a nasal).

### Q16 (slide 17)

A node has log likelihood $L(S)=-1000$. The gain of a question $q$ is $\Delta L_q=L(S_y)+L(S_n)-L(S)$. Question A gives $L(S_y)=-520$, $L(S_n)=-430$. Question B gives $L(S_y)=-480$, $L(S_n)=-500$. The gain threshold is 30, and there is also a minimum occupancy for each child. Which statement is correct?

- A) Question A is chosen (gain 50), and the node is split if both children also keep enough occupancy.
- B) Question B is chosen (gain 20), because its two children have the more equal likelihoods.
- C) Question A is chosen (gain 50), and the node is split, since the gain threshold is the only stopping rule.
- D) No question is chosen: both gains, $-950$ and $-980$, are negative, so a split lowers the likelihood.

> [!success]- Answer
> **A.** $\Delta L_A=-520-430+1000=50$ and $\Delta L_B=-480-500+1000=20$. The larger gain wins, so A. $50>30$, so the node is split, provided no child falls below the occupancy threshold.
> - B: The tree picks the largest gain. Balanced children are not a criterion.
> - C: There are two stopping rules. The occupancy threshold stops splits that leave a child with too little data.
> - D: $-950$ and $-980$ are $L(S_y)+L(S_n)$ without subtracting $L(S)$. A split never lowers the likelihood, so $\Delta L_q\ge0$.

## 5. N-grams and perplexity (slides 18-20)

### Q17 (slide 18)

Which expression is the trigram ($N=3$) approximation of $P(w_1,w_2,w_3,w_4)$?

- A) $P(w_1)\,P(w_2\mid w_1)\,P(w_3\mid w_2)\,P(w_4\mid w_3)$
- B) $P(w_1)\,P(w_2\mid w_1)\,P(w_3\mid w_1,w_2)\,P(w_4\mid w_2,w_3)$
- C) $P(w_1)\,P(w_2\mid w_1)\,P(w_3\mid w_1,w_2)\,P(w_4\mid w_1,w_2,w_3)$
- D) $P(w_1)\,P(w_2\mid w_1)\,P(w_3\mid w_1,w_2)\,P(w_4\mid w_1,w_2)$

> [!success]- Answer
> **B.** An $N$-gram keeps the last $N-1=2$ words as history. The first three factors of the chain rule already have at most two words of history. Only the last one changes: $w_1$ is dropped. The assumption: a word depends only on the previous two words.
> - A: This keeps one word of history, the bigram ($N=2$).
> - C: This is the exact chain rule, with three words of history in the last factor. Nothing is approximated.
> - D: This keeps the first two words instead of the last two. The history is the words right before $w_4$.

### Q18 (slide 19)

In a text corpus, "speech" occurs 200 times and "recognition" 80 times. The word pair "speech recognition" (in this order) occurs 50 times, the pair "recognition speech" 5 times. Estimate the bigram probability $\hat P(\text{recognition}\mid\text{speech})$ by relative counts.

- A) 0.25
- B) 0.025
- C) 0.625
- D) 0.0625

> [!success]- Answer
> **A.** $\hat P(w_k\mid w_{k-1})=C(w_{k-1},w_k)/C(w_{k-1})$, with the pair counted in text order: $w_{k-1}$ then $w_k$. (Slide 19 writes $C(w_k,w_{k-1})$ but means the same pair.) So $50/200=0.25$. The denominator is the count of the history, so the estimates of all words after "speech" sum to one.
> - B: $5/200$ counts the pair in the wrong order, "recognition speech".
> - C: $50/80$ divides by the count of the predicted word instead of the history word.
> - D: $5/80$ is $\hat P(\text{speech}\mid\text{recognition})$: the conditional is turned around.

### Q19 (slide 20)

A model gives the 4 words of a test sentence the probabilities 0.1, 0.25, 0.1 and 0.4. Perplexity is $PP=P(w_1,\dots,w_K)^{-1/K}$, and here $K$ counts only the four words (no sentence-end symbol). What is the perplexity?

- A) 3.98
- B) 1000
- C) 4.71
- D) 5.62

> [!success]- Answer
> **D.** $P=0.1\times0.25\times0.1\times0.4=0.001$ and $PP=0.001^{-1/4}\approx5.62$. The model is as unsure as a choice between about 5.6 equally likely words. (A model with $1/50$ for every word has $PP=50$, so this one is better.)
> - A: $0.001^{-1/5}\approx3.98$ counts a sentence-end symbol, $K=5$, although the stem says not to.
> - B: $1/P=1000$ forgets the $K$-th root, so the length is not removed.
> - C: $1/0.2125\approx4.71$ uses the arithmetic mean of the probabilities. Perplexity uses the geometric mean.

## 6. Smoothing the language model (slides 21-23)

### Q20 (slide 21)

A vocabulary has $V=5{,}000$ words, and the training corpus has 100 million ($10^8$) words. A trigram is an ordered sequence of three vocabulary words. At most which fraction of the possible trigrams can occur in the corpus?

- A) 0.08%
- B) 0.48%
- C) 80.00%
- D) 99.92%

> [!success]- Answer
> **A.** Possible trigrams: $V^3=5{,}000^3=1.25\times10^{11}$. A corpus of $10^8$ words holds at most about $10^8$ trigrams, even if all are different: $10^8/(1.25\times10^{11})=8\times10^{-4}=0.08\%$. All the others get count 0 and relative-count probability 0. Words are thousands, phones about 44, so this is far worse than for triphones.
> - B: $10^8/(V^3/6)=0.48\%$ counts unordered word sets. Word order matters in a trigram.
> - C: $10^8/(1.25\times10^{8})=80\%$ cubes $10^3$ to $10^6$ instead of $10^9$.
> - D: $99.92\%$ is the fraction that can never occur, the opposite of what is asked.

### Q21 (slide 22)

The word "red" occurs 10 times in a corpus. It is followed 6 times by "car", 3 times by "wine" and once by "tape". Absolute discounting subtracts $D=0.5$ from every seen bigram count: $\hat P(w\mid\text{red})=\max\big(C(\text{red},w)-D,0\big)/C(\text{red})$. How much probability mass is freed?

- A) 0.85
- B) 0.05
- C) 0.15
- D) 0.50

> [!success]- Answer
> **C.** $(6-0.5)/10=0.55$, $(3-0.5)/10=0.25$, $(1-0.5)/10=0.05$. Sum $0.85$, freed $1-0.85=0.15$. Check: $D\,n_+/C=0.5\times3/10=0.15$ with $n_+=3$ seen successors. The 0.15 goes to the words that never followed "red".
> - A: 0.85 is the mass the three seen words keep, not the mass freed.
> - B: $0.5/10=0.05$ subtracts $D$ only once. It is subtracted from each of the 3 seen bigrams.
> - D: 0.5 is $D$ itself, a count, not a probability. It must be divided by $C(\text{red})=10$ for each seen word.

### Q22 (slide 23)

Interpolation: $P=\lambda_3\,\hat P_{\text{tri}}+\lambda_2\,\hat P_{\text{bi}}+\lambda_1\,\hat P_{\text{uni}}$, with $\lambda_3=0.6$, $\lambda_2=0.3$, $\lambda_1=0.1$. The trigram was never seen ($\hat P_{\text{tri}}=0$), the bigram estimate is 0.2 and the unigram estimate is 0.01. What is the interpolated probability?

- A) 0.061
- B) 0.066
- C) 0.210
- D) 0.153

> [!success]- Answer
> **A.** $0.6\times0+0.3\times0.2+0.1\times0.01=0+0.06+0.001=0.061$. The unseen trigram gets a non-zero probability. (The $\lambda$'s are tuned on held-out data: on the counting data the trigram counts fit best by construction, which would give $\lambda_3\to1$.)
> - B: $0.6\times0.01+0.3\times0.2+0.1\times0=0.066$ pairs $\lambda_3$ with the unigram and $\lambda_1$ with the trigram.
> - C: $0+0.2+0.01=0.21$ adds the estimates without the weights.
> - D: $(0.06+0.001)/0.4\approx0.153$ drops the trigram term and renormalises the other two weights. Interpolation keeps the fixed weights; the lost 0.6 share is the price of the unseen trigram.

## 7. Decoding with Viterbi and token passing (slides 24-29)

### Q23 (slide 24)

The search space has three levels: states form a triphone, triphones form a word, words form a sentence. Fact (a): "zen" is the phone sequence z eh n. Fact (b): after "the", the word "cat" is more likely than "of". Fact (c): in the middle state of ${}_{z}eh_{n}$ the feature vector has a certain Gaussian mixture density. Which statement is correct?

- A) Fact (b) belongs to the middle level, the triphones that form a word, and comes from the lexicon.
- B) Fact (c) belongs to the highest level, the words that form a sentence, and comes from the language model.
- C) Fact (a) belongs to the lowest level, the states that form a triphone, and comes from the acoustic model.
- D) Fact (a) belongs to the middle level, the triphones that form a word, and comes from the lexicon.

> [!success]- Answer
> **D.** The lexicon (pronunciation dictionary) says which phones make a word. So (a) is the middle level. (b) is the highest level (language model), (c) the lowest level (acoustic model). Slide 24 calls $\hat W$ the "most likely state sequence": Viterbi finds the best state sequence through this network, and the words on it are the word sequence $\hat W$.
> - A: Which word follows "the" is a fact about word sequences: the language model at the highest level.
> - B: An output density of an HMM state is part of the acoustic model at the lowest level.
> - C: The phones of a word are listed in the lexicon. The acoustic model only says how each phone sounds.

### Q24 (slide 25)

A recogniser has an acoustic model (phone HMMs), a pronunciation model and a language model (word N-grams). The pronunciation model lists "tomato" as t ah0 m ey1 t ow2 and "tomato (1)" as t ah0 m aa1 t ow2. A new pronunciation variant of a word must be accepted; it uses only phones that already have HMMs. Which model changes?

- A) The acoustic model: a new phone HMM is trained for the vowel of the variant.
- B) Only the pronunciation model: one more entry is added for the same word.
- C) The pronunciation and language models: the variant is a new word with its own N-grams.
- D) All three models: the phones, the word list and the HMM states all change.

> [!success]- Answer
> **B.** The two "tomato" entries differ only in the stressed vowel (ey1 against aa1): one word, two accepted pronunciations. A new variant is one more entry of this kind. The phone HMMs already exist, and the language model works with words, and the word is the same.
> - A: The variant uses phones that already have HMMs, so nothing is trained.
> - C: "tomato" and "tomato (1)" are the same word. The language model gives one probability to the word, whatever its pronunciation.
> - D: Only the mapping from the word to phones changes. The phone set and the word list stay the same.

### Q25 (slide 26)

Viterbi: $\phi_j(t)=\max_i\big(\phi_i(t-1)\,a_{ij}\big)\,b_j(\mathbf y_t)$. At time $t-1$: $\phi_1=0.02$ and $\phi_2=0.05$. Transitions into state 2: $a_{12}=0.5$, $a_{22}=0.3$. Output: $b_2(\mathbf y_t)=0.4$. What is $\phi_2(t)$?

- A) 0.010
- B) 0.004
- C) 0.006
- D) 0.015

> [!success]- Answer
> **C.** From state 1: $0.02\times0.5=0.010$. From state 2: $0.05\times0.3=0.015$. $\phi_2(t)=\max(0.010,\,0.015)\times0.4=0.006$. The predecessor (state 2, the self-loop) is stored, so the traceback can find the best state sequence at the end.
> - A: $(0.010+0.015)\times0.4=0.010$ sums over the predecessors. That is the forward algorithm, not Viterbi.
> - B: $0.010\times0.4=0.004$ takes the smaller of the two, the path from state 1.
> - D: $0.015$ forgets to multiply by the output probability $b_2(\mathbf y_t)$.

### Q26 (slide 27)

In token passing, each state holds one token, a pair (log score, link). What does the link point to, and what is it used for?

- A) A record of the earlier words and their times; it is used at the end to read out the word sequence.
- B) The state the token will enter next; it is used to choose the best transition at the next frame.
- C) The language-model entry of the current word; it is used to add $\log P(W)$ at the word end.
- D) The best competing token in the same state; it is used to recover tokens lost by pruning.

> [!success]- Answer
> **A.** When a token leaves a word, a record (time, previous word) is written and the link points to it. At time $T$ the chain of records of the best token gives the words and their boundary times. The log score compares hypotheses.
> - B: The next state is not known in advance; the max over incoming tokens decides it at each frame. The link looks back, not forward.
> - C: The language-model score is added to the log score at the word exit. The link stores history, not model entries.
> - D: Each state keeps only its best token. The losers are discarded, and pruned tokens are gone for good.

### Q27 (slide 28)

![[cs568_lecture_15_l15_p028.jpg]]

In this token-passing network of an LVCSR system, what could the recogniser output if the arrow at the bottom (from the End side back to the Start side) were removed?

- A) Sentences of any length, but each word could appear only once in a sentence.
- B) Sentences of any length, because every word is still connected to the End token.
- C) Only single words, such as "a", "abort", "about" or "zen".
- D) Sentences without language-model scores, because this arrow adds $P(W)$.

> [!success]- Answer
> **C.** The arrow closes the loop: a token that leaves a word can enter the start of every word again. Without it a token passes through only one word from Start to End, so only single-word outputs are possible.
> - A: There is no second path into any word. Without the loop even two different words cannot follow each other.
> - B: Reaching the End token ends the utterance. Only the loop leads from one word to the next.
> - D: Without the loop no second word can follow, so there is no sentence to score at all.

### Q28 (slide 29)

Two tokens enter the same state at time $t$. Token A: log score $-40$, $\log a_{ij}=-0.7$. Token B: log score $-39$, $\log a_{ij}=-2.3$. The output term is $\log b_j(\mathbf y_t)=-5$. The surviving token later leaves the word at once, and the language model gives $\log P=-4.6$ for the next word. What is its new log score?

- A) $-50.9$
- B) $-50.3$
- C) $-45.3$
- D) $-41.1$

> [!success]- Answer
> **B.** A: $-40-0.7-5=-45.7$. B: $-39-2.3-5=-46.3$. The state keeps the best one, A. Word exit: $-45.7-4.6=-50.3$. A record (time, word just left) is written, and the link points to it.
> - A: $-46.3-4.6=-50.9$ keeps token B, the better one at time $t-1$. The comparison is made after adding the transition and output terms.
> - C: $-40-0.7-4.6=-45.3$ forgets the output term $\log b_j(\mathbf y_t)$.
> - D: $-45.7+4.6=-41.1$ subtracts the log probability. A log probability is negative and is added.

## 8. Making the search fast (slides 30-35)

### Q29 (slide 30)

In a tree-structured network, words that start with the same phones share those phone nodes. Why does this save so much computation in a beam-search decoder?

- A) Most computation goes into the last phones of the words, and the tree merges the word endings.
- B) Sharing saves only memory; with beam search the computation per frame stays the same.
- C) Each word's LM probability is known at its start, so unlikely words are pruned at once.
- D) Most work is in the first phones, and many words share them, so they are computed only once.

> [!success]- Answer
> **D.** With beam pruning about 90% of the computation is spent on the first two phones of the words; after them most tokens are pruned. Thousands of words start with the same phones, so the tree computes them once, not once per word.
> - A: The tree shares word beginnings, not endings, and the endings are cheap because few tokens survive there.
> - B: Shared nodes are evaluated once per frame instead of once per word, so the time drops too.
> - C: This is the price of the tree, turned around: inside a shared node the word is not yet known. The decoder uses the maximum LM probability of the words still possible.

### Q30 (slide 31)

![[cs568_lecture_15_l15_p031.jpg]]

In this tree network, the language model gives $P(\text{a})=0.05$, $P(\text{abort})=0.001$, $P(\text{about})=0.01$. A token uses, in each node, a language-model value for the words still possible below that node. Which value does a token use in node "b"?

- A) 0.050
- B) 0.001
- C) 0.010
- D) 0.011

> [!success]- Answer
> **C.** Below "b" only "abort" and "about" are possible: $\max(0.001,\,0.01)=0.01$. In "ax" it is $\max(0.05,0.001,0.01)=0.05$, in "ow" $0.01$. The value can only go down or stay as the token moves on, so no token is pruned early for a language-model reason.
> - A: 0.05 keeps the value of node "ax". The value is updated when the tree branches and "a" is no longer possible.
> - B: 0.001 takes the minimum. That would score the token too badly and could prune the path of "about".
> - D: $0.001+0.01=0.011$ adds the probabilities. The decoder needs an optimistic bound for one word, the maximum.

### Q31 (slide 32)

Two partial paths end in the same state at the end of the word "have". One came through "I have", the other through "you have". With a bigram language model the worse token can be discarded safely. Why is this not safe with a trigram model?

- A) With a trigram the two paths get different acoustic scores later, so both must be kept.
- B) With a trigram the next word $x$ costs $P(x\mid\text{I},\text{have})$ on one path and $P(x\mid\text{you},\text{have})$ on the other.
- C) With a trigram both paths get the same next-word cost $P(x\mid\text{have})$, so it is safe there too.
- D) With a trigram the language model is added at every frame, so the max cannot be taken.

> [!success]- Answer
> **B.** With a bigram the next word costs $P(x\mid\text{have})$ on both paths, so the worse path can never overtake. With a trigram the costs differ, and the path that is worse now can win later. The state no longer holds all the needed information. Fix: keep several tokens per state, one per history.
> - A: Both paths are in the same state at the same time, so all later acoustic scores are equal for them.
> - C: $P(x\mid\text{have})$ is the bigram. A trigram conditions on the two previous words.
> - D: The language-model score is added once per word, at the word exit, with a bigram or a trigram.

### Q32 (slide 33)

In multi-pass decoding, the first pass uses word-internal triphones and a bigram and outputs a word lattice (or an N-best list). The second pass rescores it with cross-word triphones and a trigram. Why does the first pass use the simpler models?

- A) It searches the whole vocabulary, and these models keep that search simple and cheap.
- B) Cross-word triphones and trigrams are less accurate, so they are used only to check the first result.
- C) The first pass must output one best sentence, and simpler models give a more reliable single answer.
- D) The second pass searches the whole vocabulary again, so the first pass only needs the word boundaries.

> [!success]- Answer
> **A.** With word-internal triphones a word's HMM sequence does not depend on its neighbours, and with a bigram one token per state is exact. The second pass only rescores the few alternatives in the lattice, so it can afford the expensive models.
> - B: The second-pass models are the more accurate ones. That is why they rescore.
> - C: The first pass keeps many alternatives (lattice or N-best list), so the second pass can still change the result.
> - D: The second pass searches only the lattice, a few hundred arcs, not the whole vocabulary.

### Q33 (slide 34)

A stack decoder holds hypothesis A (covers 20 frames, log score $-60$) and hypothesis B (covers 50 frames, log score $-120$). Which one is on top of the stack with the raw scores, and which one after normalising each score by its number of frames?

- A) A with the raw scores, and A after normalising.
- B) A with the raw scores, and B after normalising.
- C) B with the raw scores, and A after normalising.
- D) B with the raw scores, and B after normalising.

> [!success]- Answer
> **B.** Raw: $-60>-120$, so A. Per frame: A $=-60/20=-3.0$, B $=-120/50=-2.4$, and $-2.4>-3.0$, so B. Raw scores fall with every frame and favour short hypotheses. Viterbi compares tokens at the same time $t$, so it needs no normalisation.
> - A: This reads $-3.0$ as larger than $-2.4$ after normalising.
> - C: This takes the larger magnitude as the better score in both comparisons ($-120$ over $-60$, $-3.0$ over $-2.4$).
> - D: This takes the larger magnitude as better in the raw comparison only ($-120$ over $-60$).

### Q34 (slide 35)

What is on each transition of a weighted finite-state transducer (WFST), and which problem of classical decoders do WFSTs solve?

- A) One symbol and a weight; the search becomes exact, so no pruning is needed.
- B) An input symbol and a weight; the language model is no longer needed.
- C) Input, output and weight; all sources form one network, so the decoder stays the same.
- D) Input, output and weight; the sources are hard-wired into the decoder program.

> [!success]- Answer
> **C.** A WFST transition has an input symbol, an output symbol and a weight. Each knowledge source (HMMs, context, lexicon, language model) is a transducer of the same kind. They are compiled into one optimised network, and plain Viterbi searches it. In a classical decoder the sources are hard-wired into the program, so changing one is not trivial.
> - A: One symbol per transition is a finite automaton (a weighted acceptor). The network is still searched with Viterbi and pruning.
> - B: The output symbol is missing, and the language model is one of the composed transducers ($G$).
> - D: Hard-wired knowledge sources are the problem of the classical decoders, not what WFSTs do.

## 9. Word error rate and insertion control (slides 36-38)

### Q35 (slide 36)

The reference transcription has 8 words. The minimum-edit-distance alignment gives 2 substitutions, 1 deletion and 3 insertions. What is the word error rate?

- A) 37.5%
- B) 60.0%
- C) 25.0%
- D) 75.0%

> [!success]- Answer
> **D.** $\mathrm{WER}=(S+D+I)/N$ with $N$ the number of reference words: $(2+1+3)/8=6/8=75.0\%$. Because insertions count, the WER can exceed 100%.
> - A: $(2+1)/8=37.5\%$ leaves out the insertions.
> - B: $6/10=60.0\%$ divides by the hypothesis length ($8-1+3=10$ words) instead of the reference length.
> - C: $1-0.75=25.0\%$ is the word accuracy, not the error rate.

### Q36 (slide 37)

Decoders maximise $\log P(Y\mid W)+s\log P(W)+\lvert W\rvert\log p$, with $s$ the LM scale factor, $\lvert W\rvert$ the number of words and $p$ the word insertion penalty. The plain sum has $s=1$ and no penalty. Hypothesis A (2 words): $\log P(Y\mid W)=-1000$, $\log P(W)=-8$. Hypothesis B (5 short words): $\log P(Y\mid W)=-980$, $\log P(W)=-20$. Which hypothesis wins with the plain sum, and which with $s=12$ and $\log p=-2$?

- A) A wins the plain sum, and A wins with scale and penalty.
- B) B wins the plain sum, and B wins with scale and penalty.
- C) B wins the plain sum, and A wins with scale and penalty.
- D) A wins the plain sum, and B wins with scale and penalty.

> [!success]- Answer
> **C.** Plain: A $=-1008$, B $=-1000$, so B. Scaled: A $=-1000+12(-8)+2(-2)=-1100$, B $=-980+12(-20)+5(-2)=-1230$, so A. B is a chain of short words that fits the acoustics a little better; choosing it gives insertion errors.
> - A: In the plain sum this compares only the language-model terms ($-8>-20$). The acoustic term is part of the sum.
> - B: This puts the scale on the acoustic term: A $=12(-1000)-8-4=-12012$, B $=12(-980)-20-10=-11790$. The scale multiplies $\log P(W)$.
> - D: Both slips together: LM terms only in the plain sum, and the scale on the acoustic term.

### Q37 (slide 38)

![[cs568_lecture_15_l15_p038.jpg]]

Which reading of this NIST benchmark chart is correct?

- A) Conversational speech (red, Switchboard) falls from about 90% to about 20% WER.
- B) The range of human transcription error is the band between about 4% and about 10%.
- C) The WER axis is linear, with equal steps between 1%, 2%, 4%, 10% and 100%.
- D) Meeting speech (magenta) reaches the human band by 2009, as read speech did.

> [!success]- Answer
> **A.** The red Switchboard curve starts at about 90% (early 1990s) and falls to about 20%. The speaking style matters more than the year: spontaneous speech stays far above read speech.
> - B: The human band lies between the 2% and the 4% lines.
> - C: The axis is logarithmic: the step from 1% to 10% is as tall as the step from 10% to 100%.
> - D: Meeting speech stays between about 25% and about 50%. Only read speech (the 1k-word task) reaches the human band.
