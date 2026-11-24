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

Slides not embedded: 1
