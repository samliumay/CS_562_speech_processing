---
course: CS 568 Speech Processing
lecture: 15
title: Large vocabulary speech recognition
tags: [cs568, lecture]
slides: lectures/lecture_15/l15.pdf
full_notes: lecture_notes/lecture_15/CS568_Lecture_15_LVCSR.pdf
---
# CS 568 · Lecture 15 · Large vocabulary continuous speech recognition (LVCSR)

> [!summary] In short
> - Recognition picks $\hat W=\arg\max_W P(Y\mid W)\,P(W)$: acoustic model times language model.
> - Acoustic model: triphone HMMs (about 60k). Data sparsity is fought by tying states with a phonetic decision tree.
> - Language model: $N$-grams estimated by counting, measured by perplexity, always smoothed (discounting, backing off, interpolation).
> - Decoding: Viterbi run as token passing, made fast by beam search, tree-structured networks, multi-pass search, stack decoding or WFSTs.
> - Evaluation: word error rate $\mathrm{WER}=(S+D+I)/N$, with $N$ the number of reference words.

## 1. The task and the fundamental equation (slides 2-6)
![[cs568_lecture_15_l15_p002.jpg]]
![[cs568_lecture_15_l15_p003.jpg]]
![[cs568_lecture_15_l15_p004.jpg]]
![[cs568_lecture_15_l15_p005.jpg]]
![[cs568_lecture_15_l15_p006.jpg]]

**Notes.** Speech transcription wants the exact words (errors are counted per word). Speech understanding wants the meaning (word errors do not matter if the meaning is right). This lecture is about transcription.

With $Y$ the sequence of feature vectors (MFCCs) and $W$ a word sequence:

$$\hat W=\arg\max_W P(W\mid Y)=\arg\max_W \frac{P(Y\mid W)\,P(W)}{P(Y)}=\arg\max_W P(Y\mid W)\,P(W)$$

$P(Y)$ is the same for every hypothesis, so it is dropped. $P(Y\mid W)$ is the acoustic model (HMMs). $P(W)$ is the language model ($N$-grams).

Scoring one hypothesis, "ten pots" (slide 5): the dictionary gives the phones [t e n p o t s], the phone HMMs are joined into one HMM, its likelihood $P(Y\mid W)$ is multiplied by $P(W)$. A decoder makes the search over all hypotheses efficient.

Hard parts: coarticulation blurs word boundaries ("grey day" vs "grade A"), function words are reduced, the search is expensive, and training data is never enough.

> [!info] The four parts of a recogniser: what is what (beyond the slides)
> - **Acoustic model** (akustik model): gives $P(Y\mid W)$. It answers "how well do these feature vectors fit these sounds?". It is a set of phone HMMs trained on recorded speech with transcriptions.
> - **Language model** (dil modeli): gives $P(W)$. It answers "how likely is this word sequence in the language?". It needs only text, no audio.
> - **Pronunciation dictionary** or lexicon (telaffuz sözlüğü): a table from each word of the vocabulary (dağarcık) to its phones. It is the bridge between the two models: the language model works with words, the acoustic model works with phones.
> - **Decoder** (çözücü): the search program. It has no probabilities of its own. It uses the other three to find the $W$ with the largest $P(Y\mid W)\,P(W)$ without scoring every possible $W$.
> 
> The front end (feature extraction) comes before all of them and turns the waveform into $Y$.

> [!question]- Check (slide 2): The speaker says "I want to fly to Boston". The system outputs "I want two fly to Boston". Is this an error for speech transcription? For speech understanding?
> Transcription: yes. The goal is the exact text, and "to" became "two", which is one substitution error.
> Understanding: most likely no. The goal is the meaning (a flight to Boston). A word error does not matter if the inferred meaning stays the same.

> [!question]- Check (slide 3): A student writes: "We drop $P(Y)$ because it is equal to 1, and $P(W)$ is learned from recorded speech." Find the two mistakes.
> 1. $P(Y)$ is not 1. It is dropped because it does not depend on $W$. Every hypothesis is divided by the same number, so the position of the maximum does not change.
> 2. $P(W)$ is the language model. It is built from text material that is representative of the application. The term learned from a database of utterances is $P(Y\mid W)$, the acoustic model.

> [!question]- Check (slide 4): "In principle" the process is repeated for every word sequence. With a vocabulary of 20,000 words, how many 5-word sequences exist? What does this say about the decoder?
> Each of the 5 positions can hold any of the 20,000 words:
> $$20{,}000^5=(2\times10^4)^5=3.2\times10^{21}$$
> Nobody can build and score $3.2\times10^{21}$ sentence HMMs for one utterance. So the decoder is not an optional speed-up. It is the part that makes the $\arg\max$ computable, by sharing work between hypotheses and dropping unlikely ones.

> [!question]- Check (slide 5): In the figure, which box turns the row "ten | pots" into the row "t e n p o t s"? Which two quantities enter the Decoder box, and where does each come from?
> The pronunciation dictionary turns the words into phones. The phone row then selects the phone HMMs that are joined in the "Acoustic models" box.
> The decoder receives $P(Y\mid W)$ from the acoustic models (they are compared with the front-end output) and $P(W)$ from the language model, which gets the word sequence $W$ as input. It multiplies them, $P(Y\mid W)\cdot P(W)$, and outputs "ten pots".
> The dashed arrow "Adaptation" goes from the decoder back to the acoustic models. The slide gives only this label and does not explain it. (Beyond the slides: the recognised output can be used to adapt the acoustic models, for example to the current speaker.)

> [!question]- Check (slide 6): "grey day" and "grade A" can have almost the same acoustic information. Which term of the fundamental equation can still choose between them, and why can the other term not?
> $P(Y\mid W)$ is almost equal for both, because both word sequences give almost the same phone sequence, and coarticulation hides the word boundary. So the acoustic model cannot decide.
> $P(W)$ can decide: the language model gives a higher probability to the word sequence that fits the context better. The same holds for reduced function words: they leave almost no acoustic evidence, so the language model must supply them.

## 2. Triphones and why they are hard to train (slides 7-11)
![[cs568_lecture_15_l15_p007.jpg]]
![[cs568_lecture_15_l15_p008.jpg]]
![[cs568_lecture_15_l15_p009.jpg]]
![[cs568_lecture_15_l15_p010.jpg]]
![[cs568_lecture_15_l15_p011.jpg]]

**Notes.** One HMM per word is impractical, so models are built per phone (about 44 in English). A phone sounds different in each context, so the unit is the **triphone** ${}_{l}p_{r}$: phone $p$ with left neighbour $l$ and right neighbour $r$.

- "ten pots" with cross-word triphones: sil ${}_{sil}t_{e}$ ${}_{t}e_{n}$ ${}_{e}n_{p}$ ${}_{n}p_{o}$ ${}_{p}o_{t}$ ${}_{o}t_{s}$ ${}_{t}s_{sil}$ sil.
- Word-internal triphones mark the boundary instead: ${}_{e}n_{-}$ and ${}_{-}p_{o}$. Easier to decode, but they miss coarticulation across words. Current systems use cross-word triphones.

Size: $44^3=85{,}184$ possible triphones, about 60,000 needed. Per state: $39\times10$ means $+\ 39\times10$ variances $+\ 10$ weights $=790$ parameters. With 3 states: $790\times3\times60{,}000\approx142$M parameters. Many triphones never occur in the training set.

Three smoothing ideas: **backing off** (triphone to right-biphone to monophone), **interpolation** with a less specific model, and **parameter tying**. Tying whole models is too coarse, so tie **states**.

> [!warning] Slide error
> Slide 9 writes "30×10 means, 30×10 variances". With 39-dimensional features it is $39\times10$ each. Only that gives the 790 on the same slide.

> [!question]- Check (slide 7): A new word must be added to the recogniser. What has to be done in a whole-word system, and what in a phoneme-based system?
> Whole-word system: a new HMM must be trained, so many recordings of that word are needed. With 500k to 1M English words this is impractical, and words that share parts cannot share data.
> Phoneme-based system: only a new line in the pronunciation dictionary is needed (the word and its phones). The about 44 phone models already exist and are reused. (The language model also needs a probability for the new word.)

> [!question]- Check (slide 8): Write the cross-word triphone for the [n] of "ten" in "ten pots" and in "ten days" (first phone of "days" is [d]). What does the result show about decoding? What is the word-internal triphone in both cases?
> "ten pots": ${}_{e}n_{p}$. "ten days": ${}_{e}n_{d}$.
> The last model of "ten" depends on the following word. So the HMM sequence of a word is not fixed, and the decoder must build a different word ending for each possible next word. This is why cross-word triphones complicate decoding.
> Word-internal: ${}_{e}n_{-}$ in both cases. The model of "ten" is always the same, which is easier, but coarticulation across the word boundary is not modelled.

> [!question]- Check (slide 9): A language has 40 phones. How many triphones are possible? A system uses 10,000 triphones, 3 states each, 4 mixture components per state, 39-dimensional features, diagonal covariances. How many parameters?
> Possible triphones: one of 40 phones for the left context, the centre and the right context: $40^3=64{,}000$.
> Per state: $39\times4=156$ means, $39\times4=156$ variances, 4 weights: $156+156+4=316$.
> Per triphone: $316\times3=948$.
> Total: $948\times10{,}000=9{,}480{,}000\approx9.5$M parameters.
> The two problems of the slide remain: too many parameters for the data, and triphones that never occur in the training set.

> [!question]- Check (slide 10): The triphone ${}_{k}e_{n}$ has too little training data. Give the back-off chain. Why is the biphone a right-biphone? How is interpolation different from backing off?
> Chain: triphone ${}_{k}e_{n}$, then the right-biphone $e_{n}$ (only the right context is kept), then the monophone $e$.
> Right, because coarticulation tends to be anticipatory: the mouth already prepares the next sound, so the right neighbour changes a phone more than the left one.
> Backing off chooses one model, the less specific one. Interpolation mixes the parameters of the specific model and the less specific model, so it keeps some context information and gains robustness.

> [!question]- Check (slide 11): Take the triphones ${}_{p}o_{t}$, ${}_{p}o_{s}$ and ${}_{n}o_{t}$ (all model the phone [o]). Which states would you expect to be similar for the pair 1-2 and for the pair 1-3? Why does this speak against tying whole models?
> Pair 1-2 shares the left context [p]. So the first state (the entry into [o]) should be similar. The last states differ ([t] against [s]).
> Pair 1-3 shares the right context [t]. So the last state should be similar. The first states differ ([p] against [n]).
> Tying whole models (generalised triphones) assumes that all states are equally similar. Here that is wrong for both pairs. Tying single states can share the first state of 1 with 2 and the last state of 1 with 3.

## 3. Training tied-state models (slides 12-14)
![[cs568_lecture_15_l15_p012.jpg]]
![[cs568_lecture_15_l15_p013.jpg]]
![[cs568_lecture_15_l15_p014.jpg]]

**Notes.** The usual recipe:

1. Train monophone HMMs (one Gaussian, diagonal covariance).
2. Clone each triphone from its monophone, re-estimate, store the state occupancies.
3. Cluster the triphone states of each phone into tied states (each with enough data), re-estimate.
4. Mixture splitting: copy a Gaussian, move the two means by $\pm0.2\sigma$, weights 0.5, re-estimate. Split the component with the largest weight again, until the wanted size.

Mixtures come last because: the model set is fixed first, comparing single Gaussians is simpler, and monophone mixtures would absorb context variation (mixtures are meant for speaker variability).

> [!info] Cloning and state occupancy (beyond the slides)
> - **Cloning**: every triphone of the phone /e/ starts as an exact copy of the monophone /e/ (same means, variances, transitions). Re-estimation with the triphone transcriptions then moves each copy towards the data of its own context.
> - **State occupancy** $\gamma$: the expected number of training frames that are assigned to a state (the sum over time of the state posterior from Baum-Welch). It says how much data a state has. A state with a small occupancy has unreliable estimates, so the clustering step only accepts tied states whose occupancy is above a threshold.

> [!question]- Check (slide 12): A state has one Gaussian with mean 5 and standard deviation 2. Give the two components right after one mixture split. After re-estimation the weights are 0.6 and 0.4: which component is split next, and what are the three weights right after that split?
> Split: copy the Gaussian and move the means by $\pm0.2\sigma=\pm0.2\times2=\pm0.4$. Means $4.6$ and $5.4$. Both keep standard deviation 2. Both weights are 0.5.
> Next split: the component with the largest weight, 0.6 (this is on the slide).
> The weights after it are beyond the slides. The slide and the lecture notes give the weights only for the first split (0.5 and 0.5). The usual rule (HTK) for a component inside a mixture: each copy gets half of the weight of the split component, and the two means move by $\pm0.2$ of the standard deviation of that component. So the weights are $0.6/2=0.3$, $0.3$, and the other component keeps $0.4$. Check: $0.3+0.3+0.4=1$. Setting the two copies to 0.5 each would be wrong here, because the sum would be $1.4$. Then all parameters are re-estimated again.

> [!question]- Check (slide 13): The figure shows four /e/ triphones with three states each. How many separate output distributions exist in the row "Single-Gaussian triphones", and how many in the row "Tied-state single-Gaussian triphones"? What changes in the last row?
> Before tying: $4\times3=12$ Gaussians, one box under every state.
> After tying: 6 boxes. The lines show that states of different triphones point to the same box, so they share one distribution and its training data.
> Last row: the sharing is the same, but each of the 6 tied distributions is now a Gaussian mixture (several curves in each box).

> [!question]- Check (slide 14): Someone proposes: "Train monophones with 10-component mixtures first, then clone them into triphones and tie the states." Give two problems of this order.
> 1. The mixture components of a monophone would learn the context variation (an /e/ before a nasal against an /e/ before a stop). That job belongs to the triphones. The components are needed for speaker variability.
> 2. State tying must measure how similar two states are. With one Gaussian per state this is a simple comparison. With mixtures it is much harder.
> Also, mixtures should be trained only when the model inventory is fixed, so that each state is known to have enough data for its many parameters.

## 4. Phonetic decision trees (slides 15-17)
![[cs568_lecture_15_l15_p015.jpg]]
![[cs568_lecture_15_l15_p016.jpg]]
![[cs568_lecture_15_l15_p017.jpg]]

**Notes.** Bottom-up clustering merges similar states and must back off for unseen triphones. Top-down clustering starts with all triphones of one phone in one group and splits it with yes/no questions about the left or right context ("is the right phone a nasal?"). All states at one leaf are tied. An unseen triphone answers the same questions, so it always gets a model.

Building the tree, with $S$ the set of states at a node:

$$\Delta L_q=L\big(S_y(q)\big)+L\big(S_n(q)\big)-L(S)$$

Pick the question $q$ with the largest gain, split, repeat. Stop when a child would have too little occupancy, or when $\Delta L_q$ is below a threshold.

$L(S)$ needs no training data, only the stored occupancy $\gamma(S)$ (frames) and the pooled covariance $\Sigma(S)$ in $d$ dimensions:

$$L(S)\approx-\tfrac12\,\gamma(S)\big[d\ln(2\pi)+\ln|\Sigma(S)|+d\big]$$

> [!example] Reading the tree on slide 16
> Centre state of ${}_{m}e_{t}$: right = stop? [t] yes. Left = nasal? [m] yes. Leaf e2-5.
> Centre state of ${}_{d}e_{l}$: right = stop? no. Right = /l/? yes. Leaf e2-3.

> [!warning] Watch out
> Splitting never lowers the likelihood, so $\Delta L_q\ge0$ for every question. A positive gain does not justify a split. The thresholds decide.

> [!question]- Check (slide 15): The triphone ${}_{z}e_{k}$ never occurs in the training set. What does a system with bottom-up clustering do with it, and what does a system with a phonetic decision tree do? Which gives the better model?
> Bottom-up: there is no trained model to merge, so the system backs off to a biphone or a monophone. The context is lost partly or fully.
> Decision tree: the questions only need the names of the left and right phones, not data. ${}_{z}e_{k}$ answers them, reaches a leaf, and uses the tied state of that leaf, which was trained on seen triphones with a similar context.
> The slide says the tree builds more accurate models than backing off.

> [!question]- Check (slide 16): Use the tree on the slide. Which leaf does the centre state of ${}_{t}e_{n}$ reach? And ${}_{s}e_{p}$? Which of the triphones drawn above the tree share the centre state of ${}_{t}e_{n}$?
> ${}_{t}e_{n}$: right = stop? [n] is a nasal, no. Right = /l/? No. Left = stop? [t] yes. Leaf e2-2.
> ${}_{s}e_{p}$: right = stop? [p] yes. Left = nasal? [s] no. Leaf e2-4.
> ${}_{p}e_{n}$ gives the same three answers as ${}_{t}e_{n}$ (no, no, yes), so both centre states are tied at e2-2. ${}_{n}e_{l}$ goes to e2-3 (right = /l/).

> [!question]- Check (slide 17): A node has $L(S)=-1000$. Question A gives $L(S_y)=-520$, $L(S_n)=-430$. Question B gives $L(S_y)=-480$, $L(S_n)=-500$. Which question is chosen? The gain threshold is 30: is the node split? Name the second stopping rule.
> $\Delta L_A=-520+(-430)-(-1000)=50$.
> $\Delta L_B=-480+(-500)-(-1000)=20$.
> Question A has the larger gain, so it is chosen. $50>30$, so the node is split, if both children also have enough occupancy.
> Second rule: no split if a child would have fewer examples than the occupancy threshold. The gain threshold stops splits of nodes whose states are acoustically similar. The occupancy threshold stops splits that leave too little data.

## 5. N-grams and perplexity (slides 18-20)
![[cs568_lecture_15_l15_p018.jpg]]
![[cs568_lecture_15_l15_p019.jpg]]
![[cs568_lecture_15_l15_p020.jpg]]

**Notes.** For $K$ words, the chain rule is cut to the last $N-1$ words:

$$P(W)=\prod_{k=1}^{K}P(w_k\mid w_1,\dots,w_{k-1})\approx\prod_{k=1}^{K}P(w_k\mid w_{k-N+1},\dots,w_{k-1})$$

Unigram $N=1$, bigram $N=2$, trigram $N=3$. Estimates are relative counts, with $C(\cdot)$ the count in a text corpus:

$$\hat P(w_k\mid w_{k-1})=\frac{C(w_{k-1},w_k)}{C(w_{k-1})},\qquad \hat P(w_k\mid w_{k-2},w_{k-1})=\frac{C(w_{k-2},w_{k-1},w_k)}{C(w_{k-2},w_{k-1})}$$

**Perplexity** removes the effect of length:

$$PP(W)=\big[P(w_1,\dots,w_K)\big]^{-1/K}$$

It is the average branching factor: if every word is one of $V$ equally likely words, $PP=V$. It lies between 1 and $\infty$. Lower is better, measured on unseen text.

> [!warning] Watch out
> Lower perplexity does not guarantee a lower WER. Perplexity ignores which words sound alike.

> [!info] Reading the perplexity formula (beyond the slides)
> $P(W)$ is a product of $K$ factors, so it gets smaller for every extra word. A long sentence always looks "worse" than a short one. The $K$-th root removes this: $P(W)^{1/K}$ is the geometric mean of the $K$ word probabilities, the typical probability the model gives to one word. Perplexity is one over this value.
> $$PP(W)=\frac{1}{\big(\prod_{k=1}^{K}P(w_k\mid\text{history})\big)^{1/K}}=\exp\Big(-\frac1K\sum_{k=1}^{K}\ln P(w_k\mid\text{history})\Big)$$
> A typical word probability of $1/50$ gives $PP=50$: the model is as unsure as if it had to choose between 50 equally likely words at every position. This is the "average branching factor". Note on slide 19: the slide writes the count as $C(w_k,w_{k-1})$. It means the number of times the pair occurs in text order, $w_{k-1}$ then $w_k$.

> [!question]- Check (slide 18): Write $P(w_1,w_2,w_3,w_4)$ exactly with the chain rule, then with the trigram approximation. Which factor changes, and what is assumed?
> Exact: $P(w_1)\,P(w_2\mid w_1)\,P(w_3\mid w_1,w_2)\,P(w_4\mid w_1,w_2,w_3)$.
> Trigram ($N=3$, the last $N-1=2$ words): $P(w_1)\,P(w_2\mid w_1)\,P(w_3\mid w_1,w_2)\,P(w_4\mid w_2,w_3)$.
> Only the last factor changes: $w_1$ is dropped from its history. The assumption is that a word depends only on the previous two words. The first three factors already have at most two words of history.

> [!question]- Check (slide 19): In a corpus, "speech" occurs 200 times, "speech recognition" 50 times and "speech signal" 30 times. Estimate $P(\text{recognition}\mid\text{speech})$ and $P(\text{signal}\mid\text{speech})$. Then compute the probability of a 3-word sentence whose bigram factors are 0.1, 0.2 and 0.05.
> $\hat P(\text{recognition}\mid\text{speech})=C(\text{speech recognition})/C(\text{speech})=50/200=0.25$.
> $\hat P(\text{signal}\mid\text{speech})=30/200=0.15$.
> Sentence: $P(W)=0.1\times0.2\times0.05=0.001$.
> The denominator is the count of the history, not the size of the corpus. So the estimates for all words that can follow "speech" sum to one.

> [!question]- Check (slide 20): Model A gives each of the 4 words of a test sentence the probability $1/50$. Model B gives them 0.1, 0.25, 0.1 and 0.4. Compute both perplexities. Which model is better? What happens to B if its third probability is 0?
> A: $P=(1/50)^4=1.6\times10^{-7}$, $PP=\big((1/50)^4\big)^{-1/4}=50$.
> B: $P=0.1\times0.25\times0.1\times0.4=0.001$, $PP=0.001^{-1/4}\approx5.62$.
> B is better: lower perplexity. It is as unsure as a choice between about 5.6 equally likely words, A between 50.
> Here $K=4$ counts only the four words. The slide formula has no sentence-end symbol, so none is counted.
> With one zero factor $P(W)=0$ and $PP=\infty$. One unseen event ruins the whole measure. This is one reason for smoothing in the next section.

## 6. Smoothing the language model (slides 21-23)
![[cs568_lecture_15_l15_p021.jpg]]
![[cs568_lecture_15_l15_p022.jpg]]
![[cs568_lecture_15_l15_p023.jpg]]

**Notes.** $V$ words give $V^2$ bigrams and $V^3$ trigrams. Most never occur in the corpus and would get probability zero. Smoothing raises zero or low probabilities and lowers high ones.

- **Discounting**: take a little mass from seen events and give it to unseen ones. Absolute discounting subtracts a fixed $D$ ($0<D<1$) from each count: $\hat P(w_k\mid w_{k-1})=\max\big(C(w_{k-1},w_k)-D,0\big)/C(w_{k-1})$. If $n_+$ distinct words followed $w_{k-1}$, the freed mass is $D\,n_+/C(w_{k-1})$.
- **Backing off**: unseen trigram, use the bigram. Unseen bigram, use the unigram. Unseen word, uniform.
- **Interpolation**: a weighted average, with $\lambda_1+\lambda_2+\lambda_3=1$:

$$P(w_k\mid w_{k-2},w_{k-1})=\lambda_3\frac{C(w_{k-2},w_{k-1},w_k)}{C(w_{k-2},w_{k-1})}+\lambda_2\frac{C(w_{k-1},w_k)}{C(w_{k-1})}+\lambda_1\frac{C(w_k)}{K}$$

The counts come from the larger part of the data, the $\lambda$'s are tuned on a held-out part.

> [!warning] Two slide errors
> - Slide 21 gives "8e6" trigrams for 20k words. Correct: $(2\times10^4)^3=8\times10^{12}$.
> - Slide 23 calls $K$ "the number of different words". $K$ must be the total number of words (tokens) in the corpus, so that $C(w_k)/K$ sums to one. Corpus "a a b": $2/3+1/3=1$, but dividing by 2 different words gives $1.5$.

> [!question]- Check (slide 21): A vocabulary has $V=5{,}000$ words and the training corpus has 100 million words. How many bigrams and trigrams are possible? At most which fraction of the possible trigrams can occur in the corpus? Why is this worse than for triphones?
> Bigrams: $V^2=5{,}000^2=2.5\times10^{7}$. Trigrams: $V^3=5{,}000^3=1.25\times10^{11}$.
> A corpus of $10^8$ words holds at most about $10^8$ trigrams, even if all are different: $10^8/(1.25\times10^{11})=8\times10^{-4}=0.08\%$. More than 99.9% of the trigrams have count 0, and the relative-count estimate gives them probability 0.
> The inventory is words (thousands), not phones (about 44). So the number of possible units is far larger than for triphones ($44^3=85{,}184$), and sparsity is a much larger issue.

> [!question]- Check (slide 22): The word "red" occurs 10 times. It is followed 6 times by "car", 3 times by "wine" and once by "tape". Use absolute discounting with $D=0.5$. Give the three probabilities and the freed mass. Who gets the freed mass?
> $P(\text{car}\mid\text{red})=(6-0.5)/10=0.55$.
> $P(\text{wine}\mid\text{red})=(3-0.5)/10=0.25$.
> $P(\text{tape}\mid\text{red})=(1-0.5)/10=0.05$.
> Sum: $0.55+0.25+0.05=0.85$. Freed mass: $1-0.85=0.15$ (check: $D\,n_+/C=0.5\times3/10=0.15$).
> The 0.15 is given to all words that never followed "red" (the unseen events of the slide), so the whole distribution after "red" sums to one again: $0.85+0.15=1$.
> Beyond the slides: the 0.15 is usually shared in proportion to a backed-off estimate (the unigram). This is how discounting and backing off work together.

> [!question]- Check (slide 23): The trigram was never seen (relative count 0), the bigram estimate is 0.2, the unigram estimate is 0.01. With $\lambda_3=0.6$, $\lambda_2=0.3$, $\lambda_1=0.1$, compute the interpolated probability. Why are the $\lambda$'s tuned on a second data set and not on the set that gave the counts?
> $P=0.6\times0+0.3\times0.2+0.1\times0.01=0+0.06+0.001=0.061$. The unseen trigram gets a non-zero probability.
> On the counting set the trigram relative counts fit best by construction, so tuning there would give $\lambda_3\to1$ and no smoothing at all. A held-out set contains trigrams that the counting set did not have. Only there the less specific estimates show their value.

## 7. Decoding with Viterbi and token passing (slides 24-29)
![[cs568_lecture_15_l15_p024.jpg]]
![[cs568_lecture_15_l15_p025.jpg]]
![[cs568_lecture_15_l15_p026.jpg]]
![[cs568_lecture_15_l15_p027.jpg]]
![[cs568_lecture_15_l15_p028.jpg]]
![[cs568_lecture_15_l15_p029.jpg]]

**Notes.** The search space has three levels: states form a triphone (acoustic model), triphones form a word (lexicon), words form a sentence (language model). Viterbi keeps, for state $j$ at time $t$, the best probability over all state sequences ending in $j$:

$$\phi_j(t)=\max_i\big(\phi_i(t-1)\,a_{ij}\big)\,b_j(\mathbf y_t)$$

Start with $\phi_j(0)=1$ for the initial state and 0 elsewhere (slide 26 prints $\phi_j(t)=1$). The best path has probability $\max_j\phi_j(T)$, and a traceback gives the words.

**Token passing** is the same algorithm seen differently. A token is a pair (log score, link to a history record). Each state holds one token.

1. Moving between states: add $\log a_{ij}$ and $\log b_j(\mathbf y_t)$.
2. Each state keeps only its best incoming token (the max).
3. Leaving a word: add the language-model log probability, write a record (time, previous word), point the link to it.
4. At time $T$: trace back the best token at a valid exit. This gives the words and their boundary times.

> [!info] Why tokens carry log scores (beyond the slides)
> $\phi_j(t)$ is a product of one transition probability and one output density per frame. An utterance has hundreds of frames, so the product becomes too small for a computer number (underflow). With logarithms the products become sums: $\log\phi_j(t)=\max_i\big(\log\phi_i(t-1)+\log a_{ij}\big)+\log b_j(\mathbf y_t)$. The $\max$ is unchanged because $\log$ is increasing. This is why a token holds $\log P$ and why step 1 above adds.
> 
> Wording on slide 24: it calls $\hat W$ the "most likely state sequence". $\hat W$ is a word sequence (slide 3). This is loose wording, not wrong content: Viterbi finds the most likely state sequence through the network, and the words on that path are the output $\hat W$.

> [!question]- Check (slide 24): Which level of the search space (and which knowledge source) holds each fact? (a) "zen" is the phone sequence z eh n. (b) After "the", the word "cat" is more likely than "of". (c) In the middle state of ${}_{z}eh_{n}$, the feature vector has a certain Gaussian mixture density.
> (a) Middle level: a network of triphones represents a word. Source: the lexicon (pronunciation dictionary).
> (b) Highest level: a network of words forms a sentence. Source: the language model.
> (c) Lowest level: a network of states (an HMM) represents a triphone. Source: the acoustic model.

> [!question]- Check (slide 25): In the lower figure, the pronunciation model lists "tomato" and "tomato (1)". What is the difference between the two entries? If a new pronunciation variant of a word must be accepted, which of the three models changes?
> The fourth phone, the stressed vowel, differs: "t ah0 m **ey1** t ow2" against "t ah0 m **aa1** t ow2". The word has two accepted pronunciations.
> Only the pronunciation model changes: one more entry for the same word. The acoustic model keeps its phone HMMs (the variant uses phones that exist already). The language model is not touched, because it works with words, and the word is the same.
> In the upper figure the decoder is the only box that gets all three sources. It turns the feature vectors $Y$ into the words $W$.

> [!question]- Check (slide 26): At time $t-1$: $\phi_1=0.02$ and $\phi_2=0.05$. Transitions into state 2: $a_{12}=0.5$, $a_{22}=0.3$. Output: $b_2(\mathbf y_t)=0.4$. Compute $\phi_2(t)$. Which predecessor is recorded, and why is it recorded?
> From state 1: $0.02\times0.5=0.010$. From state 2: $0.05\times0.3=0.015$.
> $\phi_2(t)=\max(0.010,\,0.015)\times0.4=0.015\times0.4=0.006$.
> The recorded predecessor is state 2 (the self-loop). The decision is stored because $\phi$ is only a number. The traceback at time $T$ follows the stored decisions backwards to get the best state sequence, and from it the words.

> [!question]- Check (slide 27): What are the two fields of a token and what is each used for? How does the recognition network of an LVCSR system differ from that of a task-oriented system?
> $\log P$: the log likelihood (score) of the best partial path that ends in this state now, $\log\phi_j(t)$. It is used to compare hypotheses.
> $link$: a pointer to a record of history information (which words came before, and when). It is used at the end to read out the word sequence.
> Task-oriented system: the network represents all allowable utterances (a fixed grammar). LVCSR: all vocabulary words in parallel, in a loop, so any word can follow any word. The language model decides how likely each sequence is.

> [!question]- Check (slide 28): In the network on the slide, what does the arrow at the bottom (from the End side back to the Start side) do? What could the recogniser output if this arrow were removed? What does the dashed bubble above the node "ax" show?
> The arrow closes the loop: a token that leaves a word can enter the start of every word again. So the network accepts word sequences of any length.
> Without it a token could pass through only one word, so only single-word outputs ("a", "abort", "about", ..., "zen") would be possible.
> The bubble shows that each phone node, here "ax", is itself a small HMM with several states. The picture has three levels: states inside a phone, phones in a row for a word, words in parallel in a loop.

> [!question]- Check (slide 29): Two tokens enter the same state at time $t$. Token A: score $-40$, $\log a_{ij}=-0.7$. Token B: score $-39$, $\log a_{ij}=-2.3$. The output cost is $\log b_j(\mathbf y_t)=-5$. Which token stays, with which score? Later it leaves the word, and the language model gives $\log P=-4.6$ for the next word. What is the new score, and what is written at that moment?
> A: $-40-0.7-5=-45.7$. B: $-39-2.3-5=-46.3$.
> The state keeps the best one: A with $-45.7$. B is discarded, although it had the better score one frame earlier. This is the $\max$ of Viterbi.
> Word exit: $-45.7-4.6=-50.3$. A record $R$ is written with a copy of the token, the current time and the identity of the word just left. The link of the token then points to $R$. The chain of records gives the words and the boundary times in the traceback.

## 8. Making the search fast (slides 30-35)
![[cs568_lecture_15_l15_p030.jpg]]
![[cs568_lecture_15_l15_p031.jpg]]
![[cs568_lecture_15_l15_p032.jpg]]
![[cs568_lecture_15_l15_p033.jpg]]
![[cs568_lecture_15_l15_p034.jpg]]
![[cs568_lecture_15_l15_p035.jpg]]

**Notes.**
- **Beam search**: drop every token whose log score is more than the beam width below the best token at that time.
- **Tree-structured network**: words that start with the same phones share those nodes (slide 31: "a", "abort", "about"). The word is not known until late, so the LM score used is the maximum over the words still possible, updated as the choices narrow.
- **N-grams**: one token per state is exact only for bigrams. For trigrams, keep several tokens per state.
- **Multi-pass**: a cheap first pass (word-internal triphones, bigram) outputs an N-best list or a word lattice. A second pass rescores it (cross-word triphones, trigram).
- **Stack decoding**: depth-first. Keep an ordered stack of hypotheses and extend the best one. Scores are normalised by the number of frames. Costly in memory.
- **WFST**: a finite-state transducer has an input symbol, an output symbol and a weight on each transition. All knowledge sources are compiled into one optimised network $H\circ C\circ L\circ G$ (states to context-dependent phones, to phones, to words, plus the LM), searched with plain Viterbi.

> [!warning] Watch out
> Beam search is not exact. A pruned token may have been on the best complete path (a search error). A wider beam makes this rarer but costs more.

> [!info] N-best list and word lattice (beyond the slides)
> Both keep more than the single best answer of the first pass, so that a second pass with better models can still change the result.
> - **N-best list**: the $N$ best complete sentences, ranked by score. Simple, but neighbouring entries often differ in only one word, so the list repeats itself.
> - **Word lattice** (word graph): a network. A node is a point in time, an arc is one word hypothesis with its start time, end time and scores. Every path from the first node to the last is one sentence. Sentences share arcs, so a lattice holds far more alternatives than an N-best list of the same size.
> 
> Rescoring a lattice is cheap because the search space is now a few hundred arcs, not the whole vocabulary at every frame. That is why the expensive models (cross-word triphones, trigram) are kept for the second pass.

> [!question]- Check (slide 30): Why does sharing the word-initial phones save so much computation? What is the price, and how is it reduced?
> With beam search (pruning, budama) about 90% of the computation is spent on the first two phones of the words. After them most tokens are pruned. Thousands of words start with the same phones, so in a tree these first phones are computed once, not once per word.
> Price: inside a shared node the word is not known yet, so the language model probability of "the" word cannot be added at the word start.
> Fix: use the maximum language model probability over all words that are still possible below the node. Each time the tree branches, fewer words remain and the value is updated.

> [!question]- Check (slide 31): Count the phone nodes that the three words "a", "abort", "about" need in the linear network of slide 28 and in the tree of slide 31. Then: the language model gives $P(\text{a})=0.05$, $P(\text{abort})=0.001$, $P(\text{about})=0.01$. Which value does a token use in node "ax", in node "b", and in node "ow"?
> Linear (slide 28): "a" has 1 node (ax), "abort" has 4 (ax b oa t), "about" has 4 (ax b ow t): $1+4+4=9$.
> Tree (slide 31): ax, b, oa, t, ow, t: 6 nodes. "ax" is shared by all three words, "b" by two.
> Node "ax": all three words are possible, $\max(0.05,\,0.001,\,0.01)=0.05$.
> Node "b": "abort" or "about", $\max(0.001,\,0.01)=0.01$.
> Node "ow": only "about", $0.01$.
> The value can only go down or stay as the token moves forward. So a token is never scored too badly early, and it is not pruned for a language model reason that later turns out to be wrong.

> [!question]- Check (slide 32): Two partial paths end in the same state at the end of the word "have". One came through "I have", the other through "you have". With a bigram model the worse token can be discarded safely. Why is this not safe with a trigram model?
> Bigram: the probability of the next word $x$ is $P(x\mid\text{have})$. It is the same for both paths. Everything that happens later is equal for both, so the worse path can never overtake the better one. One token per state is enough.
> Trigram: the next word costs $P(x\mid\text{I},\text{have})$ on one path and $P(x\mid\text{you},\text{have})$ on the other. The path that is worse now can get the better language model score later and win. The state alone no longer holds all the information needed, which breaks the dynamic programming principle.
> Solution on the slide: keep several tokens in each state, one for each different history, so several histories stay alive.

> [!question]- Check (slide 33): In the word lattice on the slide, what does the horizontal axis show? Why do words such as "have", "very" and "sil" appear on several arcs? Why does the first pass use word-internal triphones and a bigram?
> The axis is time. The lattice runs from the start of the utterance on the left to its end on the right.
> The same word is hypothesised more than once: with different start or end times, or after different earlier words. Each arc is one such hypothesis, and every path from left to right is one possible sentence.
> The first pass must search the whole vocabulary, so it uses cheap models that are easy to decode: word-internal triphones (a word's HMM sequence does not depend on its neighbours) and a bigram (one token per state is exact). The second pass only rescores the few alternatives in the lattice, so it can afford cross-word triphones and a trigram.

> [!question]- Check (slide 34): A stack decoder holds hypothesis A (covers 20 frames, log score $-60$) and hypothesis B (covers 50 frames, log score $-120$). Which one is on top with the raw scores? Which one after normalising by the number of frames? Why is the normalisation needed here but not in Viterbi?
> Raw: $-60>-120$, so A is on top.
> Per frame: A $=-60/20=-3.0$, B $=-120/50=-2.4$. Now B is on top: it explains each frame better.
> The score is a product of probabilities, so it falls with every frame. Raw scores favour short hypotheses. In stack decoding (depth-first) the hypotheses in the stack have different lengths, so they must be made comparable. Viterbi (breadth-first) compares all tokens at the same time $t$, after the same number of frames.

> [!question]- Check (slide 35): What is on a transition of a finite automaton, of an FST and of a WFST? Which problem of classical decoders do WFSTs solve?
> Finite automaton: one symbol. FST: an input symbol and an output symbol, so a path maps an input sequence to an output sequence. WFST: input symbol, output symbol and a weight (a probability or cost).
> In a classical decoder the knowledge sources (acoustic models, lexicon, language model) are hardwired into the program, so changing one of them is not trivial. With WFSTs each source is a transducer of the same kind. They are combined into one very large but highly optimised network, and the search itself stays the same.

## 9. Word error rate and insertion control (slides 36-38)
![[cs568_lecture_15_l15_p036.jpg]]
![[cs568_lecture_15_l15_p037.jpg]]
![[cs568_lecture_15_l15_p038.jpg]]

**Notes.** Three error types: substitution (wrong word), deletion (word missing), insertion (extra word).

$$\mathrm{WER}=\frac{C(\text{subs})+C(\text{del})+C(\text{ins})}{N}$$

$N$ is the number of words in the reference transcription. The errors are counted on the minimum-edit-distance alignment.

The acoustic model dominates the language model, which causes many inserted short function words. Two fixes, combined in the score real decoders maximise:

$$\log P(Y\mid W)+s\,\log P(W)+|W|\,\log p$$

$s>1$ is the LM scale factor, $|W|$ the number of words, $p<1$ the word insertion penalty. Both are tuned on development data. Slide 38 is the NIST history of benchmark WERs (read, broadcast, conversational, meeting speech).

> [!warning] Watch out
> WER can exceed 100% because insertions count. An empty output has WER exactly 100% (all deletions).

> [!info] Why the acoustic model dominates (beyond the slides)
> $\log P(Y\mid W)$ is a sum of one term per frame, so a few hundred terms for one sentence. The HMM treats the frames as independent, although neighbouring frames are very similar. The same evidence is counted many times, and differences between hypotheses become far too large. $\log P(W)$ has only one term per word. In the plain sum the language model is almost ignored.
> - The **scale factor** $s>1$ multiplies $\log P(W)$ (the slide says a "multiplicative term greater than one") and brings the two terms into balance. Typical values are 10 to 15.
> - The **word insertion penalty** $p<1$ multiplies the probability once for each word ("multiplicative term less than one"). In logs it adds $\log p<0$ for each word, so a hypothesis with many short words pays more.
> 
> Direction: both make every extra word more expensive. A larger $s$ or a stronger penalty (smaller $p$) gives fewer insertions but more deletions. Values that are too weak give the opposite: many insertions and few deletions. This is why both are tuned on development data.

> [!question]- Check (slide 36): The reference has 8 words. The alignment gives 2 substitutions, 1 deletion and 3 insertions. Compute the WER. Second case: the reference is "stop that" and the output has 5 words, none of them correct. What is the smallest possible WER?
> $\mathrm{WER}=(2+1+3)/8=6/8=75\%$.
> Second case: $N=2$. The cheapest alignment puts 2 output words against the 2 reference words (2 substitutions). The other 3 are insertions. $\mathrm{WER}=(2+0+3)/2=250\%$.
> The WER can be above 100% because insertions are counted in the numerator but $N$ counts only the reference words.

> [!question]- Check (slide 37): Hypothesis A (2 words): $\log P(Y\mid W)=-1000$, $\log P(W)=-8$. Hypothesis B (5 short words): $\log P(Y\mid W)=-980$, $\log P(W)=-20$. Which one wins with the plain sum? Which one with $s=12$ and $\log p=-2$? Which kind of error is avoided?
> Plain sum: A $=-1000-8=-1008$. B $=-980-20=-1000$. B wins, although the language model finds it much less likely.
> With scale and penalty, $\log P(Y\mid W)+s\log P(W)+\lvert W\rvert\log p$:
> A $=-1000+12\times(-8)+2\times(-2)=-1000-96-4=-1100$.
> B $=-980+12\times(-20)+5\times(-2)=-980-240-10=-1230$.
> Now A wins. B was a chain of short function words that matched the acoustics a little better. Choosing it would give insertion errors.

> [!question]- Check (slide 38): Look at the chart. What kind of scale does the WER axis use? Where is the "range of human error in transcription"? Compare the curves for read speech, conversational speech and meeting speech.
> The axis is logarithmic: the labelled lines are 1%, 2%, 4%, 10%, 100%.
> The human range is the band between the 2% and 4% lines.
> Read speech (black curves, left) fell fast: the 1k-word task ends a little below 4%, inside the human band. The curves labelled 5k and 20k lie higher, so a larger vocabulary is harder.
> Conversational speech (red, Switchboard) starts at about 90% and falls to about 20%. Meeting speech (magenta, right) stays between about 25% and about 50%.
> The speaking style matters more than the year: spontaneous speech with reduced, coarticulated words is much harder than read speech, and in 2009 it was still far from the human band.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| LVCSR rule | $\hat W=\arg\max_W P(Y\mid W)\,P(W)$ |
| Triphone ${}_{l}p_{r}$ | phone $p$ with left context $l$, right context $r$; cross-word or word-internal |
| State tying | states of different triphones share one output distribution |
| Decision-tree gain | $\Delta L_q=L(S_y)+L(S_n)-L(S)\ge0$ |
| Mixture splitting | copy a Gaussian, means $\pm0.2\sigma$, weights 0.5, re-estimate |
| Bigram estimate | $\hat P(w_k\mid w_{k-1})=C(w_{k-1},w_k)/C(w_{k-1})$ |
| Perplexity | $PP=P(W)^{-1/K}$, average branching factor, lower is better |
| Absolute discounting | subtract $D$ from each count; freed mass $D\,n_+/C$ |
| Viterbi | $\phi_j(t)=\max_i\big(\phi_i(t-1)a_{ij}\big)b_j(\mathbf y_t)$ |
| Token | (log score, link to history record) |
| Beam width | largest allowed gap to the best token |
| WER | $(S+D+I)/N$, $N$ = reference words |
| Decoder score | $\log P(Y\mid W)+s\log P(W)+\lvert W\rvert\log p$ |

## Check yourself
> [!question]- 1. Write the cross-word triphone sequence of "pots ten" (silence at both ends).
> Phones: [p o t s t e n]. Sequence: sil ${}_{sil}p_{o}$ ${}_{p}o_{t}$ ${}_{o}t_{s}$ ${}_{t}s_{t}$ ${}_{s}t_{e}$ ${}_{t}e_{n}$ ${}_{e}n_{sil}$ sil. Seven phones give seven triphones. ${}_{t}s_{t}$ and ${}_{s}t_{e}$ cross the word boundary.

> [!question]- 2. Two states, each one Gaussian with variance 1 and occupancy 100, means 0 and 4 (1-D). What is the gain of splitting them?
> Pooled mean $=2$. Pooled mean of $x^2$ $=\big((1+0)+(1+16)\big)/2=9$. Pooled variance $=9-4=5$. The children keep variance 1, so $\Delta L=\tfrac12\cdot200\cdot\ln5=100\ln5\approx160.9$.

> [!question]- 3. A 3-word sentence gets probabilities 0.5, 0.1, 0.2. Compute its perplexity.
> $P=0.5\cdot0.1\cdot0.2=0.01$. $PP=0.01^{-1/3}=100^{1/3}\approx4.64$.

> [!question]- 4. Absolute discounting with $D=0.5$. A word was seen 20 times, followed by 8 distinct words. How much mass is freed?
> $D\,n_+/C=0.5\cdot8/20=0.2$. So 20% of the probability goes to unseen successors.

> [!question]- 5. Tokens have log scores -50, -53, -58, -61, -70. Which survive a beam width of 10?
> Threshold: $-50-10=-60$. Survivors: $-50$, $-53$, $-58$.

> [!question]- 6. Reference "a b c d", output "a x c d e". Find the errors and the WER.
> b/x is a substitution, e is an insertion, no deletion. $\mathrm{WER}=(1+0+1)/4=50\%$.

## Links
- Course index: [[CS568 Home]]
- Quizzes and labs that use this lecture: none (the midterm booklet covers Lectures 1 to 6).
- Full step-by-step notes (PDF): [CS568_Lecture_15_LVCSR.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_15/CS568_Lecture_15_LVCSR.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_15/CS568_Lecture_15_LVCSR.pdf`
- Original slides (PDF): [l15.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_15/l15.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_15/l15.pdf`
- Quiz: [[CS568 L15 - Large vocabulary speech recognition - quiz]]

Slides not embedded: 1
