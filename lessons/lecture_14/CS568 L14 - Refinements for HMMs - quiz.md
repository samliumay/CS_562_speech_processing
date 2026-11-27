---
course: CS 568 Speech Processing
lesson: CS568 L14 - Refinements for HMMs
type: quiz
questions: 26
---

# CS568 L14 - Refinements for HMMs - quiz

Lesson note: [[CS568 L14 - Refinements for HMMs]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Types of HMM structure (slide 2)

### Q1 (slide 2)

![[cs568_lecture_14_l14_p002.jpg]]

The figure at the bottom of the slide shows a 4-state left-right HMM. Each row of its transition matrix $A$ sums to 1. How many free transition parameters does this model have?

- A) 3
- B) 5
- C) 8
- D) 9

> [!success]- Answer
> **B.** The figure draws a self-loop on every state, the steps $1\to2$, $2\to3$, $3\to4$ and the skips $1\to3$, $2\to4$, so $\Delta=2$. A row with $n$ allowed transitions has $n-1$ free values.
> Row 1: $a_{11},a_{12},a_{13}$, 2 free. Row 2: $a_{22},a_{23},a_{24}$, 2 free. Row 3: $a_{33},a_{34}$, 1 free. Row 4: $a_{44}=1$, 0 free. Total $2+2+1+0=5$.
> The ergodic model at the top has $4\cdot3=12$, so the left-right constraint removes more than half of them.
> - A: This reads $\Delta=1$ and misses the two skip arcs. Then rows 1 to 3 have 1 free value each.
> - C: This gives every row three transitions, 2 free each, $4\cdot2=8$. Rows 3 and 4 have no state two steps ahead.
> - D: This counts every drawn transition, $3+3+2+1=9$, and forgets that each row sums to 1.

## 2. Implementation issues (slides 3-4)

### Q2 (slide 3, scaling)

At time $t$ a 2-state HMM has the scaled forward values $\hat\alpha_t=(0.6,\ 0.4)$ and the scaled backward values $\hat\beta_t=(0.5,\ 2.0)$. The true values are $\alpha_t(i)=\hat\alpha_t(i)/C_t$ and $\beta_t(i)=\hat\beta_t(i)/D_t$. The scale factors $C_t$ and $D_t$ are unknown, but they are the same for both states. The state posterior is $\gamma_t(i)=\alpha_t(i)\beta_t(i)\big/\sum_k\alpha_t(k)\beta_t(k)$. What is $\gamma_t(1)$?

- A) 0.300
- B) 0.200
- C) 0.600
- D) 0.273

> [!success]- Answer
> **D.** The constant $C_tD_t$ appears in the numerator and in the denominator, so it cancels. The scaled values can be used directly.
> Products: $0.6\cdot0.5=0.30$ and $0.4\cdot2.0=0.80$, sum $1.10$. $\gamma_t(1)=0.30/1.10=0.273$.
> - A: This is the product $\hat\alpha_t(1)\hat\beta_t(1)$ without the division by the sum $1.10$.
> - B: This normalises $\hat\beta_t$ alone, $0.5/2.5$, and ignores $\hat\alpha_t$.
> - C: This normalises $\hat\alpha_t$ alone, $0.6/1.0$, and ignores $\hat\beta_t$, the part that looks at the future frames.

### Q3 (slide 3, multiple sequences)

A left-right HMM with 5 states allows only a self-loop and a step to the next state. It is trained with ONE utterance of $T$ frames that starts in $S_1$ and ends in $S_5$. How often is the transition $S_2\to S_3$ observed, and what follows?

- A) Exactly once, as a state that is left never comes back; an ergodic model can revisit states and get many counts.
- B) About $T/5$ times, and an ergodic model gets even more, as it can revisit states; so both models are fine.
- C) About $T/5$ times, as each state gets about a fifth of the frames; so one long utterance gives enough counts.
- D) Exactly once, and an ergodic model also takes each transition once per utterance, so both need equally many.

> [!success]- Answer
> **A.** In a left-right model each forward transition happens one time per utterance. So $\hat a_{23}$ and the output density of $S_2$ rest on one visit of a few frames. An ergodic model can return to the same state many times in one long sequence.
> With $K$ utterances each state gets $K$ visits. The counts are pooled over all utterances before the division.
> - B: The step $S_2\to S_3$ happens once. The $T/5$ frames in $S_2$ are self-loops, which count for $a_{22}$.
> - C: About $T/5$ is the number of frames in $S_2$. These frames are self-loops $S_2\to S_2$. The step to $S_3$ happens once.
> - D: An ergodic model can leave a state and come back, so it can use the same transition many times in one sequence.

### Q4 (slide 4)

Find the two mistakes in this claim: "With a careful initial $B$ from segmental k-means, Baum-Welch is guaranteed to reach the global maximum of the likelihood. The initial values of $\pi$ and $A$ need the same care as $B$." Which correction is right?

- A) The first sentence is right, as k-means puts the start near the best point; random or uniform $\pi$, $A$ usually work.
- B) Baum-Welch only ensures the likelihood never falls, so it may stop in a local maximum; $\pi$, $A$ and $B$ need the same care.
- C) Baum-Welch only ensures the likelihood never falls, so it may stop in a local maximum; random or uniform $\pi$, $A$ usually work.
- D) The global maximum is guaranteed only in the discrete case; there $B$ may be random, but $\pi$ and $A$ need the care.

> [!success]- Answer
> **C.** Baum-Welch guarantees only that one iteration does not decrease the likelihood. It ends in a local maximum, and a good start only makes a good maximum more likely.
> Random or uniform $\pi$ and $A$ work in most cases. It is $B$ that needs care: helpful in the discrete case, essential in the continuous case.
> - A: This fixes only the second sentence. No initialisation guarantees the global maximum.
> - B: This fixes only the first sentence. The slide says that random or uniform $\pi$ and $A$ work in most cases.
> - D: No case has a global guarantee. It also swaps the roles: $B$ needs the care, not $\pi$ and $A$.

## 3. Discrete, continuous and semi-continuous HMMs (slides 5-9)

### Q5 (slide 5)

A discrete HMM uses a VQ codebook with 256 codewords. State $S_j$ stores a table $b_j(k)$ of 256 probabilities, one per codeword (255 free values, since they sum to 1). Each frame is replaced by a codeword index before the HMM sees it. What information about the frame is lost, and why can this hurt?

- A) The index of its nearest codeword; the state must then guess from which cell the frame came.
- B) The order of the frames in time; VQ treats the whole utterance as a bag of codeword indices.
- C) Nothing, when the codebook is large; 256 cells are enough to give each sound its own cell.
- D) Where the frame lies in its cell; close frames on two sides of a border get different symbols.

> [!success]- Answer
> **D.** The frame is replaced by the index of its nearest codeword, so its position inside the cell (the quantisation error) is lost. Two different frames in one cell look identical, and two close frames on two sides of a border look completely different.
> This is the "degraded performance as a result of the discretization" on the slide. A continuous density $b_j(\mathbf o)$ uses the vector itself.
> - A: The index is exactly what is kept: the frame is replaced by it.
> - B: VQ works on each frame alone and keeps the sequence of indices. The HMM still sees the frames in their order.
> - C: Any finite codebook maps all vectors of one cell to one symbol. The loss is smaller for a large codebook, but it is still there.

### Q6 (slide 6)

One state of a continuous HMM has the output density $b_j(\mathbf o)=\sum_{k=1}^{M}c_{jk}\,\mathcal N(\mathbf o;\boldsymbol\mu_{jk},\boldsymbol\Sigma_{jk})$ with $M=4$ components and feature dimension $D=13$. Every $\boldsymbol\Sigma_{jk}$ is a full covariance matrix. The weights sum to 1, so count $M-1$ of them as free. How many free parameters does this output density have?

- A) 731
- B) 419
- C) 107
- D) 367

> [!success]- Answer
> **B.** Weights: $M-1=3$. Means: $4\cdot13=52$. A covariance matrix is symmetric, so it has $13\cdot14/2=91$ free entries, and the 4 matrices have $364$.
> Total $3+52+364=419$. With diagonal covariances it would be $3+52+52=107$.
> - A: This counts all $13^2=169$ entries of each covariance matrix: $3+52+4\cdot169=731$. The entries below the diagonal repeat those above it.
> - C: This is the diagonal count, $3+52+4\cdot13=107$. A full covariance also has the off-diagonal terms.
> - D: This forgets the 52 means: $3+364=367$.

### Q7 (slide 7)

A continuous HMM has 2 states and 2 mixture components per state. At time $t$ the forward values are $\alpha_t=(0.02,\ 0.06)$ and the backward values $\beta_t=(0.5,\ 0.1)$, one per state. In state 1 the weights are $c_{11}=0.4$, $c_{12}=0.6$, and the Gaussian values at $\mathbf o_t$ are $\mathcal N_1=0.5$, $\mathcal N_2=0.1$. $\gamma_t(j,k)$ is the probability of being in state $j$ at time $t$ with component $k$ accounting for $\mathbf o_t$. What is $\gamma_t(1,1)$?

- A) 0.250
- B) 0.769
- C) 0.625
- D) 0.481

> [!success]- Answer
> **D.** State posterior: $0.02\cdot0.5=0.010$ and $0.06\cdot0.1=0.006$, so $\gamma_t(1)=0.010/0.016=0.625$.
> Component posterior in state 1: $0.4\cdot0.5=0.20$ and $0.6\cdot0.1=0.06$, so $0.20/0.26=0.769$.
> $\gamma_t(1,1)=0.625\cdot0.769=0.481$. The first factor uses the whole utterance ($\alpha$ and $\beta$), the second only the frame $\mathbf o_t$.
> - A: This is $0.625\cdot0.4$: it uses the weight $c_{11}$ in place of the component posterior, so it ignores how well each Gaussian fits $\mathbf o_t$.
> - B: This is only the component posterior inside state 1. It forgets the factor $\gamma_t(1)=0.625$.
> - C: This is only the state posterior $\gamma_t(1)$. It gives the whole share of state 1 to component 1.

### Q8 (slide 8)

One mixture component $(j,k)$ sees the scalar observations $o_1=2$, $o_2=4$, $o_3=10$ with the posteriors $\gamma_t(j,k)=0.9,\ 0.5,\ 0.1$. What is the Baum-Welch re-estimate $\hat\mu_{jk}$?

- A) 4.80
- B) 5.33
- C) 3.20
- D) 1.60

> [!success]- Answer
> **C.** $\hat\mu_{jk}=\dfrac{\sum_t\gamma_t(j,k)\,o_t}{\sum_t\gamma_t(j,k)}=\dfrac{1.8+2.0+1.0}{0.9+0.5+0.1}=\dfrac{4.8}{1.5}=3.20$
> Frame 3 has $\gamma=0.1$, so it pulls the mean only a little. The denominator $1.5$ is the expected number of frames of this component.
> - A: This is the weighted sum without the division by $\sum_t\gamma_t(j,k)$.
> - B: This is the plain mean $(2+4+10)/3$ of a hard alignment that gives all three frames to the component with weight 1.
> - D: This divides $4.8$ by the 3 frames instead of by $\sum_t\gamma_t(j,k)=1.5$.

### Q9 (slide 9)

An HMM has 5 states. The features are 2-dimensional, and every Gaussian has a full covariance matrix. In a semi-continuous (tied-mixture) HMM one pool of 4 Gaussians is shared by all states, and each state has its own 4 mixture weights; count 3 of them as free, since they sum to 1. How many output-density parameters does the model have?

- A) 35
- B) 115
- C) 23
- D) 39

> [!success]- Answer
> **A.** One 2-D Gaussian: 2 means and $2\cdot3/2=3$ covariance terms, 5 in all. The pool exists once: $4\cdot5=20$. Weights: $5\cdot3=15$. Total $35$.
> A continuous HMM with 4 own Gaussians per state has $5\cdot(20+3)=115$. The tied model shares a "codebook" like a discrete HMM but scores frames with densities, so it is a compromise.
> - B: This is the continuous-HMM count: it gives every state its own 4 Gaussians. In the semi-continuous HMM they exist once.
> - C: This counts the weights of one state only: $20+3$. Every one of the 5 states has its own weights.
> - D: This counts all $2^2=4$ covariance entries: $4\cdot(2+4)+15=39$. The off-diagonal entry is counted twice.

## 4. Robustness to noise and channel (slides 10-14)

### Q10 (slide 10)

A different microphone and room reverberation both act as a filter on the speech: $y=x*h$, a convolution in time. Why is the cepstral domain the right place to remove such a distortion?

- A) Convolution is a product of spectra; the log makes it a sum, which the linear DCT keeps, so $c_y=c_x+c_h$.
- B) Convolution is a sum of spectra; the cepstrum is a linear transform, so the sum survives and $c_y=c_x+c_h$.
- C) Convolution is a product of spectra; the cepstrum is the DCT of the linear spectrum, which turns it into a sum.
- D) The log turns any sum of spectra into a sum of logs, so additive noise and filters both give $c_y=c_x+c_h$.

> [!success]- Answer
> **A.** $y=x*h$ gives $Y(\omega)=X(\omega)H(\omega)$. The log gives $\log\lvert Y\rvert=\log\lvert X\rvert+\log\lvert H\rvert$. The cepstrum is a linear transform (DCT) of the log spectrum, so $c_y=c_x+c_h$, and a constant added vector is easy to remove.
> - B: Convolution in time is a product of spectra, $Y=XH$, not a sum. The sum appears only after the log.
> - C: A DCT is linear, so it keeps a product a product. The cepstrum is the DCT of the log spectrum; the log makes the sum.
> - D: $\log(X+D)\ne\log X+\log D$. Additive noise is a sum in the linear spectrum, and the log destroys this sum.

### Q11 (slide 11)

A mismatch means that the conditions of use (noise, channel, microphone) differ from the training conditions. Two fixes are spectral subtraction and parallel model combination (PMC). Where does each one act?

- A) Both clean the features before recognition; spectral subtraction works in the linear spectrum and PMC in the cepstrum.
- B) Spectral subtraction changes the models and leaves the features alone; PMC cleans the features and leaves the models alone.
- C) Spectral subtraction cleans the features and leaves the models alone; PMC changes the models and leaves the features alone.
- D) Both change the models; spectral subtraction lowers the HMM means by the noise mean, and PMC adds noise to them.

> [!success]- Answer
> **C.** Spectral subtraction is feature-based: it subtracts the noise spectrum from the input before recognition. PMC is model-based: it combines clean-speech and noise HMMs into models of corrupted speech.
> Training under the conditions of use is not enough, because these conditions cannot always be predicted and they change over time.
> - A: PMC never touches the input. It builds new models.
> - B: The two roles are swapped.
> - D: Spectral subtraction works on the input power spectrum $\lvert Y(\omega)\rvert^2$, not on the HMM means.

### Q12 (slide 12)

Cepstral mean normalisation (CMN) subtracts from each cepstral frame the mean cepstral vector of a window. Over a long window this mean is $\bar c_y=\bar c_x+c_h$, where $c_h$ is the constant channel and $\bar c_x$ the mean cepstrum of the speech. What goes wrong if the window covers only one vowel?

- A) $\bar c_x$ is then the vowel's own cepstrum, so the subtraction also removes the phonetic information.
- B) One vowel has too few frames to estimate a variance, so the normalisation divides by a noisy value.
- C) The channel changes within one vowel, so the mean no longer matches $c_h$ at every frame of it.
- D) Nothing changes; the channel is constant, so any window gives the same mean and only $c_h$ is removed.

> [!success]- Answer
> **A.** Over a long window with many different sounds, $\bar c_x$ is about the same for every utterance, so the subtraction removes mainly $c_h$. Over one vowel, $\bar c_x$ is the cepstrum of that vowel, and the recogniser loses what makes it that vowel.
> The mean is taken over speech because a convolutional distortion appears only where there is speech to filter. Additive noise is present also in silence, so spectral subtraction measures it there.
> - B: CMN only subtracts a mean. It does not divide by a variance.
> - C: CMN assumes a constant channel, and over one short vowel the channel is even more constant. The problem is $\bar c_x$.
> - D: $c_h$ is the same, but $\bar c_x$ is not. Over one vowel it is the vowel's own cepstrum.

### Q13 (slide 13)

In decomposition, a separate HMM models the noise, and the search runs over the speech state and the noise state together. The speech HMM has 5 states and the noise HMM has 2 states. Which statement is correct?

- A) The search considers 10 state pairs; a 1-state noise HMM is enough for changing noise, such as music.
- B) The search considers 7 state pairs; a 1-state noise HMM is enough for changing noise, such as music.
- C) The search considers 7 state pairs; a 1-state noise HMM is enough for stationary noise, such as a fan.
- D) The search considers 10 state pairs; a 1-state noise HMM is enough for stationary noise, such as a fan.

> [!success]- Answer
> **D.** Every speech state can occur with every noise state: $5\cdot2=10$ pairs. With a 1-state noise model it is $5\cdot1=5$, the size of the speech model alone.
> One state describes one fixed noise spectrum, which is enough for stationary noise. Noise that changes (music, a door, another speaker) needs several states.
> - A: One state has one fixed output distribution, so it cannot follow noise whose statistics change over time.
> - B: $5+2=7$ adds the state counts instead of multiplying them, and one state cannot follow changing noise.
> - C: $5+2=7$ adds the state counts. The search needs every combination, $5\cdot2$.

### Q14 (slide 14)

Parallel model combination (PMC) starts from a clean-speech HMM and a noise HMM whose means are cepstral vectors. An inverse DCT turns a cepstral mean into a log spectrum (natural log of the power). In one log-spectral bin the speech value is $3.0$ and the noise value is $1.0$. The noise is additive, with gain $g=1$. What is the log-spectral value of the combined (noisy speech) model in this bin?

- A) 3.13
- B) 1.39
- C) 4.00
- D) 2.43

> [!success]- Answer
> **A.** Additive noise adds powers in the linear spectrum. Take the exponential, add, take the log again:
> $\log(e^{3.0}+e^{1.0})=\log(20.09+2.72)=\log(22.80)=3.13$, a little above the larger value.
> The inverse DCT alone reaches only the log spectrum, not the linear spectrum; the exponential is a separate step.
> - B: This is $\log(3.0+1.0)$: it treats the log values as linear powers and skips the exponential.
> - C: This adds the log values. A sum of logs is a product of powers, which models a filter, not additive noise.
> - D: This is $\log\big((e^{3.0}+e^{1.0})/2\big)$: it averages the two powers instead of adding them.

## 5. Speaker-independent recognition (slides 15-16)

### Q15 (slide 15)

One way to handle many speakers is to train one recogniser on data from all of them. Each Gaussian must then cover the same sound spoken by all speakers, so it becomes broader. Why does this hurt recognition?

- A) The correct state gives the frame a lower density, and any drop in the correct density causes an error.
- B) Gaussians of different phonemes overlap more, so competing states score a frame alike and separate it poorly.
- C) A broad Gaussian needs a full covariance matrix, so the number of parameters and the training time explode.
- D) A broad Gaussian has a lower peak, so all likelihoods become small and the forward variables underflow.

> [!success]- Answer
> **B.** Recognition compares the densities $b_j(\mathbf o)$ of competing phoneme states. Where two densities overlap, both give a similar value for the same frame, so the frame cannot separate the two phonemes well.
> The other simple option, one recogniser per speaker type, has its own price: the type must be identified first, or all recognisers run in parallel.
> - A: A lower density is harmless if the competitors drop more. The problem is that the competitors come closer.
> - C: The width is set by the variances. A diagonal Gaussian can be broad, and its parameter count does not change.
> - D: Underflow is handled by scaling or logs. Only the comparison between states decides, not the size of the values.

### Q16 (slide 16)

A VTLN warping function is plotted with the actual frequency on the horizontal axis and the warped frequency on the vertical axis. A dotted diagonal means no change. The warp is a straight line through the origin. The line for male speakers lies above the diagonal, the line for female speakers below it. Which explanation is correct?

- A) Men have on average a longer vocal tract and lower formants, so the line above the diagonal moves their spectrum down.
- B) Men have on average a longer vocal tract and lower formants, so their frequencies are moved up to the common scale.
- C) Men have on average a lower pitch, so the warp moves their harmonics up until the pitch matches the female pitch.
- D) Men have on average a shorter vocal tract and higher formants, so their frequencies are moved up to the common scale.

> [!success]- Answer
> **B.** A longer vocal tract gives lower formants. Male formants lie below the common scale, so they must move up: warped frequency larger than actual frequency, a line above the diagonal. Female formants are 10 to 20% higher, so they move down, a line below the diagonal.
> - A: A line above the diagonal means warped > actual: every frequency moves up, not down.
> - C: VTLN warps the formants (the vocal tract), not the pitch, and it maps every speaker to a common scale, not to the female one.
> - D: A shorter tract gives higher formants, but men have the longer tract. Moving high formants further up would also widen the gap.

## 6. Model adaptation (slides 17-20)

### Q17 (slide 17)

In unsupervised adaptation the text of the adaptation data is not known. What is used in its place, and what happens if the initial model recognises the data poorly?

- A) The initial model's recognition output; frames go to wrong models, these move towards wrong data, and the errors grow.
- B) No text at all; every Gaussian moves towards the overall mean of the data, so a poor initial model does not matter.
- C) The initial model's recognition output; the errors only slow the adaptation down, it still reaches the right model.
- D) A human transcribes a small part of the data; a poor initial model only lowers the speed of the adaptation.

> [!success]- Answer
> **A.** The initial model first recognises the adaptation data, and its output is used as if it were the true text. With many recognition errors, frames are assigned to the wrong models, which are then adapted towards the wrong data. The errors are reinforced, so the initial recognition must be accurate enough.
> - B: Adaptation must know which frames belong to which Gaussian. Moving every Gaussian to one mean would destroy the model.
> - C: Wrong labels give wrong targets, and nothing corrects them later. The errors are reinforced.
> - D: A known text is supervised adaptation, not unsupervised.

### Q18 (slide 18)

MLLR adapts each Gaussian mean by $\hat{\boldsymbol\mu}_{jm}=\mathbf A_c\boldsymbol\mu_{jm}+\mathbf b_c$, where $\mathbf A_c$ is a full $D\times D$ matrix and $\mathbf b_c$ a bias vector, both shared by all Gaussians of regression class $c$. For $D=13$, how many transform parameters must be estimated with two regression classes?

- A) 182
- B) 208
- C) 338
- D) 364

> [!success]- Answer
> **D.** One transform: $13^2+13=169+13=182$. Two classes have one transform each: $2\cdot182=364$.
> These numbers are shared by all Gaussians of a class, also by Gaussians of sounds that never occurred in the data. This is why about 15 s of data can be enough.
> - A: This is the count for one class only.
> - B: This counts $\mathbf A_c$ as symmetric, like a covariance matrix: $2\cdot(91+13)=208$. A regression matrix has no symmetry.
> - C: This forgets the bias vectors: $2\cdot169=338$.

### Q19 (slide 19)

MAP adaptation of a mean uses $\hat\mu=\frac{N}{N+\tau}\,\bar\mu+\frac{\tau}{N+\tau}\,\mu_{\mathrm{SI}}$, where $\mu_{\mathrm{SI}}$ is the speaker-independent mean, $\bar\mu$ the mean of the adaptation frames of this Gaussian, $N$ its occupation count and $\tau$ the prior weight. With $\tau=10$, $\mu_{\mathrm{SI}}=1.0$, $N=30$ and $\bar\mu=3.0$, what is $\hat\mu$?

- A) 1.50
- B) 3.00
- C) 2.50
- D) 2.00

> [!success]- Answer
> **C.** Data weight $30/40=0.75$, prior weight $10/40=0.25$. $\hat\mu=0.75\cdot3.0+0.25\cdot1.0=2.50$.
> A Gaussian with $N=0$ keeps $\hat\mu=\mu_{\mathrm{SI}}$. MAP moves every Gaussian only with its own data, so it needs much more data than MLLR before most of the model has moved.
> - A: This swaps the weights: $0.25\cdot3.0+0.75\cdot1.0$.
> - B: This ignores the prior. It is the limit $\tau\to0$, the ML estimate from the adaptation data.
> - D: This is the plain average of the two means. Equal weights hold only when $N=\tau$.

### Q20 (slide 20)

Speaker adaptive training (SAT) starts from an SI model, estimates an MLLR transform for each training speaker, re-estimates the model with each speaker's data and transform, and repeats. Why is the final model less influenced by speaker-specific characteristics than a plain SI model?

- A) SAT averages the MLLR transforms of all speakers into one and applies it to the model, which removes the average speaker.
- B) The per-speaker transforms absorb the speaker differences, so the shared Gaussians only model sounds and get tighter.
- C) SAT drops the training speakers whose data fit the model worst, so the data that remain are more uniform.
- D) SAT trains a separate model for each speaker and averages their Gaussians, so the speaker traits cancel out.

> [!success]- Answer
> **B.** In plain SI training the Gaussians must absorb the differences between speakers, so they become broad. In SAT these differences go into the per-speaker transforms. The shared model only has to describe the differences between sounds.
> Beyond the slides: a new speaker then also needs a transform, estimated from some of that speaker's data.
> - A: The transforms stay separate, one per speaker, and the model is re-estimated with them.
> - C: SAT keeps every speaker. It gives each one a transform instead of removing them.
> - D: There is one shared model. Averaging speaker models would give broad Gaussians again.

## 7. Discriminative training (slides 21-26)

### Q21 (slide 21)

Find the mistake: "MLE makes $p(x\mid\omega_i)$ as large as possible for the training data of class $\omega_i$, so after MLE training every training utterance is recognised correctly." Which correction is right?

- A) The claim holds for training utterances; MLE overfits, so only unseen test utterances can be misrecognised.
- B) MLE finds only a local maximum; at the global maximum of each $p(x\mid\omega_i)$ every training utterance is correct.
- C) Recognition needs the correct likelihood above every wrong one; MLE never compares them, so a wrong one can win.
- D) The claim holds when all class priors are equal; it fails only because unequal priors shift the decision.

> [!success]- Answer
> **C.** Each $\theta_i$ is trained only on data of class $\omega_i$, and the wrong models only on their own data. Nothing stops a wrong model from giving $x$ an even higher likelihood. A large likelihood is not the same as a larger likelihood than the competitors; discriminative training optimises the second.
> - A: Training utterances can fail too, because MLE never compares the correct model with the others.
> - B: Even the global maximum of each model alone says nothing about the comparison with the other models.
> - D: With equal priors the decision compares the likelihoods, and MLE never optimises this comparison.

### Q22 (slide 22)

For an utterance $x$ of class $\omega_i$, $P(\omega_i\mid x)=\dfrac{p(x\mid\omega_i)P(\omega_i)}{p(x\mid\omega_i)P(\omega_i)+\sum_{j\ne i}p(x\mid\omega_j)P(\omega_j)}$. Before a training step the correct term $p(x\mid\omega_i)P(\omega_i)$ is $0.06$ and the competitor terms sum to $0.04$. After the step the correct term is $0.12$ and the competitor terms sum to $0.12$. What is $P(\omega_i\mid x)$ after the step?

- A) 0.50
- B) 0.75
- C) 1.00
- D) 0.60

> [!success]- Answer
> **A.** After: $0.12/(0.12+0.12)=0.50$. Before: $0.06/(0.06+0.04)=0.60$.
> The correct likelihood doubled, but the posterior fell, because the competitors grew by a factor of 3. In training $\theta$ changes, so the denominator $p(x\mid\theta)$ cannot be ignored: only the ratio of competitors to correct term decides.
> - B: This keeps the old competitor sum: $0.12/(0.12+0.04)$.
> - C: This drops the correct term from the denominator: $0.12/0.12$.
> - D: This uses the values before the step: $0.06/(0.06+0.04)$.

### Q23 (slide 23)

MMI maximises $I(x,\omega_i\mid\theta)=\log\dfrac{p(x\mid\omega_i,\theta)}{\sum_{c=1}^{C}p(x\mid\omega_c,\theta)P(\omega_c\mid\theta)}$. There are $C=3$ models with equal priors $1/3$ and likelihoods $p(x\mid\omega_c)=0.06,\ 0.02,\ 0.04$. The correct model is the first. What is $I(x,\omega_1\mid\theta)$ (natural log)?

- A) -0.405
- B) 0.405
- C) 1.500
- D) -0.693

> [!success]- Answer
> **B.** Denominator: $(0.06+0.02+0.04)/3=0.04$. $I=\log(0.06/0.04)=\log1.5=0.405$.
> Positive: the correct model explains $x$ better than the average model. The denominator makes the criterion discriminative: the other likelihoods must stay small.
> - A: This inverts the ratio: $\log(0.04/0.06)$.
> - C: This forgets the logarithm: $0.06/0.04$.
> - D: This drops the priors in the denominator: $\log(0.06/0.12)$.

### Q24 (slide 24)

Corrective training. Here $\omega_c$ is the correct model and $\omega_i$ the best incorrect model (slides 21 to 23 used $\omega_i$ for the correct class). The parameters are corrected for an utterance $x$ only if $p(x\mid\omega_i,\theta)\ge p(x\mid\omega_c,\theta)+\Delta$, with the margin $\Delta=0.01$. The pairs $\big(p(x\mid\omega_c),\ p(x\mid\omega_i)\big)$ are $u_1$: $(0.050,\ 0.030)$, $u_2$: $(0.030,\ 0.035)$, $u_3$: $(0.020,\ 0.050)$. Which utterances change the parameters?

- A) Utterances $u_2$ and $u_3$
- B) Utterance $u_1$ only
- C) Utterance $u_3$ only
- D) Utterances $u_1$ and $u_3$

> [!success]- Answer
> **C.** $u_1$: $0.030\ge0.060$ is false. $u_2$: $0.035\ge0.040$ is false: misrecognised, but by only $0.005$, less than the margin. $u_3$: $0.050\ge0.030$ is true.
> For $u_3$, $p(x\mid\omega_c)$ is moved up and $p(x\mid\omega_i)$ down. All other utterances cost no work.
> - A: This ignores the margin and corrects every misrecognised utterance.
> - B: This reads $\omega_i$ as the correct model, as on slides 21 to 23, and tests $0.050\ge0.030+0.01$.
> - D: This tests only the size of the gap, $\lvert p(x\mid\omega_i)-p(x\mid\omega_c)\rvert\ge\Delta$, and ignores its sign.

### Q25 (slide 25)

GPD, in the corrected sign convention: $g_k(x)=\log p(x\mid\omega_k,\theta)$ is a score where larger is better, and the misclassification measure of the correct class $j$ is $d_j=-g_j+\frac1\eta\log\Big[\frac{1}{C-1}\sum_{k\ne j}e^{\eta g_k}\Big]$, so $d_j>0$ means an error. The correct class has $g_j=-4$, and the $C-1=2$ competitors have $g_k=-3$ and $-7$. What is $d_j$ for $\eta=1$?

- A) -1.000
- B) 0.325
- C) 1.000
- D) 1.018

> [!success]- Answer
> **B.** $\frac12(e^{-3}+e^{-7})=\frac12(0.04979+0.00091)=0.02535$, and $\log0.02535=-3.675$. $d_j=4-3.675=0.325>0$: an error.
> $\eta$ sets how the competitors are combined; for $\eta\to\infty$ only the best one counts and $d_j=1$. The sigmoid turns the step "error or not" into a smooth function, so gradient descent can minimise it.
> - A: This averages the scores, $(-3-7)/2=-5$, instead of the values $e^{g_k}$.
> - C: This keeps only the best competitor, $-g_j+\max(-3,-7)$: the limit $\eta\to\infty$, not $\eta=1$.
> - D: This forgets the factor $\frac{1}{C-1}$: $4+\log(e^{-3}+e^{-7})$.

### Q26 (slide 26)

A recogniser has 1000 word models. For ONE training utterance, which models does MMI training update?

- A) Only the correct model, because the numerator is the same likelihood that MLE maximises.
- B) Only the 999 competing models, because the numerator is fixed and only the denominator can change.
- C) The correct model and the models that beat it by a margin, because only those cause errors.
- D) All 1000 models, because the denominator of the criterion contains the likelihood of every model.

> [!success]- Answer
> **D.** The MMI denominator (like the competing term in GPD) contains all models, so every parameter is updated for every example, with many small gradient steps. MLE updates only the correct model. Corrective training updates nothing for a well recognised utterance.
> So MMI and GPD are affordable only with few classes or little data; otherwise the slide recommends corrective training.
> - A: This is MLE. MMI also lowers the competitors through the denominator.
> - B: The numerator depends on the parameters of the correct model, and it is raised as well.
> - C: This is corrective training, not MMI.
