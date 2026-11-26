---
course: CS 568 Speech Processing
lecture: 14
title: Refinements for HMMs
tags: [cs568, lecture]
slides: lectures/lecture_14/l14.pdf
full_notes: lecture_notes/lecture_14/CS568_Lecture_14_HMM_Refinements.pdf
---
# CS 568 · Lecture 14 · Refinements for HMMs

> [!summary] In short
> - Topologies: ergodic (fully connected) and left-right (only stay or move forward). Speech uses left-right.
> - Implementation: scale $\alpha$ and $\beta$ to avoid underflow, train on many sequences, initialise with segmental k-means.
> - Output densities: discrete (VQ table), continuous (a GMM per state), semi-continuous (one shared pool of Gaussians).
> - Robustness: additive noise is handled in the linear spectrum, convolutional noise in the cepstrum.
> - Speakers: normalise the features (VTLN) or adapt the model (MLLR, MAP, SAT).
> - Discriminative training (MMI, corrective training, GPD) raises the correct model relative to its competitors.

## 1. Types of HMM structure (slide 2)
![[cs568_lecture_14_l14_p002.jpg]]

**Notes.** An **ergodic** HMM is fully connected: every state can be reached in one step from every other state. A **left-right** (Bakis) HMM allows no transition to a lower state index, and often no large jumps:

$$a_{ij}=0\ \ \forall j<i,\qquad a_{ij}=0\ \ \forall j>i+\Delta$$

Left-right models suit signals whose properties change over time, such as speech. They start in state 1 ($\pi_1=1$) and $A$ is an upper-triangular band. Baum-Welch keeps zeros at zero, so the structure survives training.

> [!example] Parameter count
> 5-state left-right model, $\Delta=1$, last state absorbing: one free parameter per row for rows 1 to 4, so 4 free transition parameters.

> [!question]- Check (slide 2): Look at the left-right figure at the bottom of the slide (4 states). Which transitions does it draw, what is $\Delta$, and how many free transition parameters does it have? Compare with the ergodic 4-state model at the top.
> The figure draws a self-loop on every state, a step to the next state ($1\to2$, $2\to3$, $3\to4$) and a skip over one state ($1\to3$, $2\to4$). The largest jump is 2, so $\Delta=2$.
> Each row of $A$ sums to 1, so a row with $n$ allowed transitions has $n-1$ free parameters.
> Row 1: $a_{11},a_{12},a_{13}$, 2 free. Row 2: $a_{22},a_{23},a_{24}$, 2 free. Row 3: $a_{33},a_{34}$, 1 free. Row 4: only $a_{44}=1$, 0 free. Total $2+2+1+0=5$.
> Ergodic: all 4 transitions allowed in each row, so $4\cdot3=12$ free parameters. The left-right constraint removes more than half of them and forces the state index to never decrease, as time does in speech.

## 2. Implementation issues (slides 3-4)
![[cs568_lecture_14_l14_p003.jpg]]
![[cs568_lecture_14_l14_p004.jpg]]

**Notes.**
**Scaling.** $\alpha_t(i)$ is a product of many numbers below one, so it underflows. Rescale at every step:

$$c_t=\frac{1}{\sum_{i=1}^{N}\alpha_t(i)},\qquad \hat\alpha_t(i)=c_t\,\alpha_t(i),\qquad \log P(O\mid\lambda)=-\sum_{t=1}^{T}\log c_t$$

Scale $\beta_t$ with the same $c_t$. The factors cancel in the re-estimation formulas. Alternative: work with logs and add probabilities with $\log(e^a+e^b)=\max(a,b)+\log(1+e^{-|a-b|})$.

**Multiple observation sequences.** In a left-right model one utterance gives each state only a few frames. Train on many sequences and pool the expected counts before dividing:

$$\hat a_{ij}=\frac{\sum_{k=1}^{K}\sum_{t=1}^{T_k-1}\xi_t^{(k)}(i,j)}{\sum_{k=1}^{K}\sum_{t=1}^{T_k-1}\gamma_t^{(k)}(i)}$$

**Initial estimates.** Baum-Welch finds only a local maximum. Random or uniform $\pi$ and $A$ work in most cases. Good initial values for $B$ help in the discrete case and are essential in the continuous case. They come from **segmental k-means**: cut each utterance uniformly into $N$ segments, estimate each state's output distribution from its frames, re-segment with Viterbi, repeat.

> [!warning] Watch out
> Pool the counts, do not average per-utterance ratios. Counts $(1.2, 2.0)$ and $(0.8, 3.0)$ give $\hat a_{ij}=2.0/5.0=0.4$, not $(0.6+0.267)/2=0.433$.

> [!info] Hard and soft alignment, and what Baum-Welch guarantees (beyond the slides)
> Training (eğitim) needs to know which frames belong to which state. This is the alignment (hizalama).
> - **Segmental k-means (Viterbi training)** uses a **hard** alignment: the Viterbi path gives every frame to exactly one state, with weight 1. The state's parameters are then plain averages over its own frames.
> - **Baum-Welch** uses a **soft** alignment: frame $t$ is shared between all states with the weights $\gamma_t(i)$, which sum to 1 over $i$. The updates are weighted averages.
>
> Hard alignment is cheaper and needs no good model to start: the first segmentation is simply uniform. For this reason it is used for the initialisation (başlangıç değerleri), and Baum-Welch then refines the result.
>
> What is guaranteed: one Baum-Welch iteration never decreases $P(O\mid\lambda)$. What is not guaranteed: that the final point is the global maximum. The likelihood surface has many local maxima, and the starting point decides which one is reached. In the continuous case a badly placed Gaussian gives almost zero density to every frame, so it gets almost no weight and can hardly move. This is why the initial $B$ is "essential" there.

> [!question]- Check (slide 3, scaling): At some time $t$ a 2-state model has the scaled values $\hat\alpha_t=(0.6,\ 0.4)$ and $\hat\beta_t=(0.5,\ 2.0)$. The true $\alpha_t$ and $\beta_t$ are these values divided by unknown scale factors. Can you still compute $\gamma_t(1)$ and $\gamma_t(2)$? Do it.
> Yes. The scale factors are the same for all states at time $t$: $\hat\alpha_t(i)\hat\beta_t(i)=K_t\,\alpha_t(i)\beta_t(i)$ with one constant $K_t$. In $\gamma_t(i)=\dfrac{\alpha_t(i)\beta_t(i)}{\sum_i\alpha_t(i)\beta_t(i)}$ the constant is in the numerator and in the denominator, so it cancels.
> Products: $0.6\cdot0.5=0.30$ and $0.4\cdot2.0=0.80$. Sum $1.10$.
> $\gamma_t(1)=0.30/1.10=0.273$, $\gamma_t(2)=0.80/1.10=0.727$.
> This is what the slide means by "the scaling coefficients cancel out exactly".
> Where the constant comes from (beyond the slides): in the formula for $c_t$ above, $\alpha_t(i)$ is the value computed in one step from the already scaled $\hat\alpha_{t-1}$. So $\hat\alpha_t(i)=(c_1c_2\cdots c_t)\cdot$ true $\alpha_t(i)$. At $t=T$ the scaled values sum to 1, so $(c_1\cdots c_T)\,P(O\mid\lambda)=1$, which gives $\log P(O\mid\lambda)=-\sum_t\log c_t$.

> [!question]- Check (slide 3, multiple sequences): A left-right model without skips has 5 states. You train it with ONE utterance. How many times is the transition $S_2\to S_3$ observed? Why is this a problem, and why is it less of a problem in an ergodic model?
> Exactly once. In a left-right model a state that has been left is never visited again, so each forward transition happens one time per utterance.
> $\hat a_{23}$ and the output distribution of $S_2$ would be estimated from one visit with a few frames. The estimate is very unreliable.
> An ergodic model can return to the same state many times inside one long sequence, so one sequence can give many counts per state.
> With $K$ utterances the left-right model gets $K$ visits per state. The counts are pooled over all utterances before the division.

> [!question]- Check (slide 4): Find the two mistakes: "With a careful initial $B$ from k-means, Baum-Welch is guaranteed to reach the global maximum. The initial values of $\pi$ and $A$ need the same care as $B$."
> Mistake 1: nothing guarantees the global maximum. Baum-Welch only guarantees that the likelihood does not decrease from one iteration to the next, so it ends in a local maximum. A good start only makes a good maximum "more likely", as the slide says.
> Mistake 2: random or uniform values for $\pi$ and $A$ work well in most cases. It is $B$ that needs care: helpful in the discrete case, essential in the continuous case.
> The initial $B$ comes from segmenting the sequences into states and clustering the frames of each segment with k-means.

## 3. Discrete, continuous and semi-continuous HMMs (slides 5-9)
![[cs568_lecture_14_l14_p005.jpg]]
![[cs568_lecture_14_l14_p006.jpg]]
![[cs568_lecture_14_l14_p007.jpg]]
![[cs568_lecture_14_l14_p008.jpg]]
![[cs568_lecture_14_l14_p009.jpg]]

**Notes.** **Discrete HMMs** need symbols. Speech frames are continuous vectors, so they are first turned into codeword indices by vector quantisation (k-means codebook). The discretisation may hurt performance.

**Continuous HMMs** model each state's output with a Gaussian mixture ($c_{jk}$ weight, $\boldsymbol\mu_{jk}$ mean, $\boldsymbol\Sigma_{jk}$ covariance of component $k$ in state $S_j$):

$$b_j(\mathbf o)=\sum_{k=1}^{M}c_{jk}\,\mathcal N(\mathbf o;\boldsymbol\mu_{jk},\boldsymbol\Sigma_{jk})$$

$\gamma_t(j)$ generalises to $\gamma_t(j,k)$: in state $S_j$ at time $t$ with component $k$ accounting for $\mathbf o_t$. It is the state posterior times the component posterior:

$$\gamma_t(j,k)=\left[\frac{\alpha_t(j)\beta_t(j)}{\sum_{j}\alpha_t(j)\beta_t(j)}\right]\left[\frac{c_{jk}\,\mathcal N(\mathbf o_t;\boldsymbol\mu_{jk},\boldsymbol\Sigma_{jk})}{\sum_{m=1}^{M}c_{jm}\,\mathcal N(\mathbf o_t;\boldsymbol\mu_{jm},\boldsymbol\Sigma_{jm})}\right]$$

Re-estimation (the $a_{ij}$ formula is unchanged):

$$\hat c_{jk}=\frac{\sum_t\gamma_t(j,k)}{\sum_t\sum_k\gamma_t(j,k)},\qquad \hat{\boldsymbol\mu}_{jk}=\frac{\sum_t\gamma_t(j,k)\,\mathbf o_t}{\sum_t\gamma_t(j,k)},\qquad \hat{\boldsymbol\Sigma}_{jk}=\frac{\sum_t\gamma_t(j,k)(\mathbf o_t-\boldsymbol\mu_{jk})(\mathbf o_t-\boldsymbol\mu_{jk})^{\mathsf T}}{\sum_t\gamma_t(j,k)}$$

Issues: many mixture components are needed to beat discrete HMMs, so more computation, more parameters and more training data.

**Semi-continuous HMMs** (tied mixtures): one pool of Gaussians $(\boldsymbol\mu_k,\boldsymbol\Sigma_k)$ shared by all states, with state-specific weights $c_{jk}$:

$$b_j(\mathbf o)=\sum_{k=1}^{M}c_{jk}\,\mathcal N(\mathbf o;\boldsymbol\mu_k,\boldsymbol\Sigma_k)$$

> [!info] Counting the free parameters (beyond the slides)
> Slide 9 says that the number of free parameters "increases significantly". Here is the count for feature vectors of dimension $D$.
> - One Gaussian with a **full** covariance (tam kovaryans): $D$ numbers for the mean, and $D(D+1)/2$ for the covariance, because the matrix is symmetric (diagonal plus upper triangle).
> - One Gaussian with a **diagonal** covariance (köşegen kovaryans): $D$ means and $D$ variances, so $2D$. All off-diagonal terms are fixed to zero.
> - One state with a mixture (karışım) of $M$ Gaussians: $M$ times the count above, plus $M-1$ free weights. The weights obey $c_{jk}\ge0$ and $\sum_kc_{jk}=1$, so the last one is fixed by the others.
>
> For $D=39$: full $39+780=819$ per Gaussian, diagonal $78$. Diagonal covariances are the usual choice. Cepstral coefficients are nearly uncorrelated, and several diagonal Gaussians together can still model a correlated cloud of points.
>
> The three model types differ in what a state owns:
>
> | | codebook / Gaussians | per state |
> |---|---|---|
> | Discrete | one VQ codebook, hard decision | table $b_j(k)$ |
> | Semi-continuous | one shared pool of Gaussians, soft decision | weights $c_{jk}$ |
> | Continuous | no sharing | weights $c_{jk}$ and its own $\boldsymbol\mu_{jk},\boldsymbol\Sigma_{jk}$ |
>
> So a semi-continuous HMM is a discrete HMM whose codewords are Gaussians: a frame is not given to one codeword, every Gaussian gets a share.

> [!question]- Check (slide 5): A discrete HMM uses a VQ codebook with 256 codewords. What does state $S_j$ store as its output distribution, and how many free parameters is that? What information about a frame is lost, and why can this hurt?
> The state stores a table $b_j(k)$, $k=1,\dots,256$: one probability per codeword. The probabilities sum to 1, so there are $256-1=255$ free parameters per state.
> The frame is replaced by the index of its nearest codeword. Where the vector lies inside the cell is lost (the quantisation error). Two different vectors in the same cell look identical, and two close vectors on different sides of a cell border look completely different.
> This is the "degraded performance as a result of the discretization" on the slide. A continuous density $b_j(\mathbf o)$ uses the vector itself.

> [!question]- Check (slide 6): In $b_j(\mathbf o)=\sum_{k=1}^{M}c_{jk}\,\mathcal N(\mathbf o;\boldsymbol\mu_{jk},\boldsymbol\Sigma_{jk})$ say what each symbol is. Then count the free parameters of ONE state for $D=13$ and $M=4$, with full and with diagonal covariances.
> $\mathbf o$: the observation (feature) vector. $j$: the state $S_j$. $k$: the index of the Gaussian component, $M$ of them. $c_{jk}$: mixture coefficient (weight) of component $k$ in state $j$, with $\sum_kc_{jk}=1$. $\boldsymbol\mu_{jk}$, $\boldsymbol\Sigma_{jk}$: mean vector and covariance matrix of that component. $b_j(\mathbf o)$ is a density value, not a probability, so it can be larger than 1.
> Full: weights $M-1=3$. Means $4\cdot13=52$. Covariances $4\cdot\frac{13\cdot14}{2}=4\cdot91=364$. Total $3+52+364=419$.
> Diagonal: weights $3$, means $52$, variances $4\cdot13=52$. Total $107$.
> The full model has about 4 times more parameters per state, so it needs much more training data.

> [!question]- Check (slide 7): 2 states, 2 components. At time $t$: $\alpha_t=(0.02,\ 0.06)$, $\beta_t=(0.5,\ 0.1)$. In state 1: $c_{11}=0.4$, $c_{12}=0.6$, and the Gaussian values at $\mathbf o_t$ are $\mathcal N_1=0.5$, $\mathcal N_2=0.1$. Compute $\gamma_t(1,1)$ and $\gamma_t(1,2)$. Which bracket knows about the rest of the utterance?
> First bracket (state posterior): $\alpha_t(1)\beta_t(1)=0.02\cdot0.5=0.010$, $\alpha_t(2)\beta_t(2)=0.06\cdot0.1=0.006$. $\gamma_t(1)=0.010/0.016=0.625$.
> Second bracket (component posterior inside state 1): $c_{11}\mathcal N_1=0.4\cdot0.5=0.20$, $c_{12}\mathcal N_2=0.6\cdot0.1=0.06$. Posteriors $0.20/0.26=0.769$ and $0.06/0.26=0.231$.
> $\gamma_t(1,1)=0.625\cdot0.769=0.481$, $\gamma_t(1,2)=0.625\cdot0.231=0.144$. Their sum is $0.625=\gamma_t(1)$.
> The first bracket uses $\alpha$ (the past) and $\beta$ (the future), so it depends on the whole utterance. The second bracket uses only the frame $\mathbf o_t$. Note that component 2 has the larger weight but still loses, because its Gaussian fits the frame badly.

> [!question]- Check (slide 8): One component $(j,k)$, scalar observations $o_1=2$, $o_2=4$, $o_3=10$ with $\gamma_t(j,k)=0.9,\ 0.5,\ 0.1$. Compute $\hat\mu_{jk}$. What would the result be with a hard alignment that gives all three frames to this component, and why do the two differ?
> $\hat\mu_{jk}=\dfrac{\sum_t\gamma_t(j,k)\,o_t}{\sum_t\gamma_t(j,k)}=\dfrac{0.9\cdot2+0.5\cdot4+0.1\cdot10}{0.9+0.5+0.1}=\dfrac{1.8+2.0+1.0}{1.5}=\dfrac{4.8}{1.5}=3.2$
> Hard alignment (all weights 1): the plain mean $(2+4+10)/3=5.33$.
> The update is a weighted average. Frame 3 probably belongs to another state or component ($\gamma=0.1$), so it pulls the mean only a little. The denominator $\sum_t\gamma_t(j,k)=1.5$ is the expected number of frames that this component accounts for.

> [!question]- Check (slide 9): A model has 5 states, $M=4$ Gaussians, 2-dimensional features, full covariances. Count the output-density parameters of a C-HMM (4 Gaussians per state) and of an SC-HMM (4 shared Gaussians). In what sense is the SC-HMM "a compromise"?
> One 2-D full Gaussian: $2$ means $+\ \frac{2\cdot3}{2}=3$ covariance terms $=5$. Free weights per state: $4-1=3$.
> C-HMM: every state owns its Gaussians. $5\cdot(4\cdot5+3)=5\cdot23=115$.
> SC-HMM: the Gaussians exist once, only the weights are per state. $4\cdot5+5\cdot3=20+15=35$.
> Compromise: like a D-HMM, all states share one "codebook" and each state only has a table of weights, so there are few parameters and the Gaussians are evaluated once per frame. Like a C-HMM, the frame is scored by continuous densities, so there is no hard quantisation.
> Sharing parameters between states like this is called tying (bağlama). That is why the slide also says "tied-mixture HMM".

## 4. Robustness to noise and channel (slides 10-14)
![[cs568_lecture_14_l14_p010.jpg]]
![[cs568_lecture_14_l14_p011.jpg]]
![[cs568_lecture_14_l14_p012.jpg]]
![[cs568_lecture_14_l14_p013.jpg]]
![[cs568_lecture_14_l14_p014.jpg]]

**Notes.** Real recordings are corrupted, and speakers change their speech in noise (**Lombard effect**).
- **Additive noise** (machinery, other speakers, noisy channels): best handled in the linear spectral domain.
- **Convolutional noise** (reverberation, microphone): best handled in the log-spectral or cepstral domain, where it becomes additive ($c_y=c_x+c_h$).

The result is a mismatch between training and use. Two families of fixes.

**Feature-based methods** (clean the input):
- **Spectral subtraction** for additive noise: average the noise spectrum over non-speech segments, subtract it, set negative values to zero: $|\hat X(\omega)|^2=\max\big(|Y(\omega)|^2-\overline{|D(\omega)|^2},\ 0\big)$.
- **Cepstral mean normalisation** (CMN/CMS) for convolutional noise: subtract the mean cepstral vector over a long window. Assumes a constant channel.
- **RASTA**: band-pass filter each log-spectral trajectory over time to remove slowly varying components. Usually used with PLP.

**Model-based methods** (change the models):
- **Noise masking**: replace filter-bank channel levels below the noise level by the noise estimate, in both observation and model.
- **Decomposition**: a separate HMM for the noise, and a search over speech state and noise state together.
- **Parallel model combination** (PMC): build clean-speech and noise HMMs in the cepstral domain, convert to linear spectra, add, convert back.

> [!example] Spectral subtraction and CMN
> Power $(5,2,10)$, noise $(1,3,1)$: result $(4,0,9)$.
> Cepstral frames $(2,0),(4,2),(0,1)$: mean $(2,1)$, CMN output $(0,-1),(2,1),(-2,0)$.

> [!warning] Watch out
> CMN removes only a channel that is constant over the window. It does not remove additive noise.

> [!info] The domains in parallel model combination (beyond the slides)
> The HMM means are cepstral vectors. The cepstrum is the DCT of the log spectrum, so three domains are involved:
> $$\text{cepstrum}\ \xrightarrow{\ \text{inverse DCT}\ }\ \text{log spectrum}\ \xrightarrow{\ \exp\ }\ \text{linear spectrum}$$
> Additive noise adds in the linear spectrum only: $S_{\text{noisy}}=S_{\text{speech}}+g\,S_{\text{noise}}$, where $g$ sets the noise level. In the log spectrum the same rule reads $\log\big(e^{s}+g\,e^{n}\big)$ for a speech value $s$ and a noise value $n$, which is not $s+n$.
> The slide text says the state parameters are "converted into linear spectra (i.e., by means of an inverse DCT) and added". The box in the figure says "Model combination (in log-spectral domain)". The text is not exact: the inverse DCT alone gives the log spectrum, and an exponential is still needed to reach the linear spectrum. The figure fits the chain above: the inverse DCT arrives in the log spectrum, and the combination box then has to do the exponential, the sum and the logarithm before the DCT block.
> The mapping is non-linear, so the Gaussians cannot be converted exactly and practical PMC uses approximations. The advantage: no noisy training data is needed. A new noise condition needs only a new small noise model.

> [!question]- Check (slide 10): Classify each case as additive noise, convolutional noise or neither: (a) a car engine during a phone call, (b) the same speaker recorded with a different microphone, (c) room reverberation, (d) the speaker talks louder because the room is noisy. For the convolutional cases, why is the cepstral domain the right place?
> (a) additive. (b) convolutional. (c) convolutional. (d) neither: this is the Lombard effect. The speech itself changes, so it is not a corruption added to the signal afterwards.
> Convolution in time, $y=x*h$, is a product of spectra, $Y(\omega)=X(\omega)H(\omega)$. The logarithm turns the product into a sum: $\log|Y|=\log|X|+\log|H|$. The cepstrum is a linear transform of the log spectrum, so $c_y=c_x+c_h$. A constant added vector is easy to remove.
> Additive noise, $y=x+d$, is already a sum in the linear spectrum, and the logarithm would destroy this: $\log(X+D)\ne\log X+\log D$.

> [!question]- Check (slide 11): What is the "mismatch", and why does the slide not accept "train under the conditions of use" as the full solution? For each method say where it acts: spectral subtraction, parallel model combination.
> Mismatch: the conditions in which the recogniser is used (noise, channel, microphone) differ from the conditions of the training data. The models describe clean features, the input is corrupted, so the likelihoods are wrong.
> Matched training is not enough because the operating conditions cannot always be predicted, and they change over time.
> Spectral subtraction is feature-based: it changes the features before recognition and leaves the models alone. Parallel model combination is model-based: it leaves the features alone and changes the models so that they describe corrupted speech.

> [!question]- Check (slide 12): Spectral subtraction estimates the noise in silent segments. Cepstral mean subtraction computes its mean over speech. Why this difference? And what goes wrong if the mean is taken over a very short window, for example one vowel?
> Additive noise is present also when nobody speaks, so silence shows the noise alone and its average spectrum can be measured there.
> A convolutional distortion is a filter applied to the speech. Without speech there is nothing to filter, so the distortion only appears in speech regions. The slide says this: "speech must be present for convolutional distortions to appear".
> The long-window cepstral mean is $\bar c_y=\bar c_x+c_h$. Over a long window with many different sounds, $\bar c_x$ is about the same for every utterance, so subtracting the mean removes the channel $c_h$. Over one vowel, $\bar c_x$ is the cepstrum of that vowel. Subtracting it removes the phonetic information that the recogniser needs.
> RASTA follows the same idea with a filter: the channel is a very slowly varying component of each log-spectral trajectory, and it is filtered out.

> [!question]- Check (slide 13): In decomposition, the speech HMM has 5 states and the noise HMM has 2 states. How many state pairs must the search consider? How many with a 1-state noise HMM, and when is one state enough? How does noise masking differ in what it needs?
> Every speech state can occur together with every noise state: $5\cdot2=10$ pairs. With a 1-state noise model: $5\cdot1=5$, the same size as the speech model alone.
> One state is enough for stationary noise, whose statistics do not change over time (a fan, a steady hum). Noise that changes (a door, music, another speaker) needs several states, and the search space grows by that factor.
> Noise masking needs no noise HMM, only an estimate of the noise level in each filter-bank channel. Channel values below that level are replaced by the noise estimate in the observation and in the model. Then both show the same value in those channels, so the channels cannot create a difference between models.

> [!question]- Check (slide 14): Follow the figure from top to bottom: which blocks does each model pass through, and how many emitting states do the three HMMs in the drawing have? Why can the cepstral means of speech and noise not simply be added? Combine one log-spectral bin with speech value $3.0$ and noise value $1.0$ (natural log, $g=1$).
> Figure: the "clean" speech HMM (3 states in the drawing) and the noise HMM (1 state) each go through an inverse DCT. The two results enter the box "Model combination (in log-spectral domain)". Its output goes through a DCT and gives the "corrupted" speech HMM, again with 3 states.
> Cepstral values are (a DCT of) logarithms of the spectrum. Adding logarithms means multiplying spectra, which would model a convolution. Additive noise adds powers in the linear spectrum.
> Bin: $\log(e^{3.0}+e^{1.0})=\log(20.09+2.72)=\log(22.80)=3.13$. The result is a little above the larger value. It is not $3.0+1.0=4.0$.
> The number of speech states does not change because the noise model has one state: each speech state is combined with the same noise state.

## 5. Speaker-independent recognition (slides 15-16)
![[cs568_lecture_14_l14_p015.jpg]]
![[cs568_lecture_14_l14_p016.jpg]]

**Notes.** Speakers differ physically, in accent and in style. Options: train on many speakers (the Gaussians become broad and overlap), train separate recognisers per speaker type (male, female), normalise the speaker, or adapt the model.

**Vocal tract length normalisation** (VTLN). A shorter vocal tract gives higher formants. Female formants are 10 to 20% higher than male ones. Warp the frequency axis so that all speakers lie on a common scale. Two approaches: a piece-wise linear warp from average formant frequencies, or a warping function whose parameter is optimised from data (pick the warp factor that maximises the likelihood).

> [!example] Warp factor
> Speaker's average F2 is 1650 Hz, population average 1500 Hz: factor $1650/1500=1.10$. Check: $1650/1.10=1500$ Hz.

> [!info] Reading the warping figure on slide 16 (beyond the slides)
> Both plots show the warping function: horizontal axis the actual frequency, vertical axis the warped frequency. The dotted diagonal means "no change".
> - **(a) Linear warp.** A straight line through the origin. Its slope is the warp factor, the only parameter. A line above the diagonal stretches the spectrum (every frequency moves up). A line below the diagonal compresses it. In the figure a curved arrow joins the two lines: its upper end is labelled "male" (the line above the diagonal) and its lower end "female" (the line below). Note the convention: the figure's warp factor is the slope, warped / actual. The example above defines the factor as speaker / population and divides by it, so the slope is its inverse (factor $0.90$ gives slope $1/0.90=1.11$, above the diagonal).
> - **(b) Piece-wise linear warp.** Each line now has three straight pieces with two bends, one near $f_{\text{low}}$ and one near $f_{\text{high}}$ (both marked on the horizontal axis). The middle piece carries the warp, as in (a). The last piece is bent so that both lines end in the same top-right corner. The band edge stays fixed. With the pure linear warp of (a), a stretched spectrum would leave the analysis band at the top and a compressed one would leave the top of the band empty.
>
> In the data-driven approach no formant is measured. The recogniser tries several warp factors and keeps the one that gives the highest likelihood of the speaker's data under the model.

> [!question]- Check (slide 15): The slide lists two simple ways to handle many speakers before normalisation and adaptation. For each, give the price that the slide names. Why do broad Gaussians hurt recognition?
> Option 1, train one recogniser on many speakers. Price: each Gaussian must cover the same sound from all speakers, so it becomes broader, and the Gaussians of different phonemes overlap more.
> Option 2, a separate recogniser per speaker type (for example male and female). Price: the type must be identified first, which is never perfectly accurate, or all recognisers run in parallel, which costs more computation.
> Why overlap hurts: recognition compares the densities $b_j(\mathbf o)$ of competing phoneme states. Where two densities overlap, both give a similar value for the same frame, so the frame cannot separate the two phonemes well.

> [!question]- Check (slide 16): In plot (a), the line marked "male" lies above the dotted diagonal and the line marked "female" below it. Explain why, using vocal tract length. What does plot (b) change, and what are $f_{\text{low}}$ and $f_{\text{high}}$?
> A longer vocal tract gives lower formants. Male speakers have on average the longer tract, so their formants are lower than the common scale. Their frequencies must be moved up: warped frequency larger than actual frequency, a line above the diagonal. Female formants are 10 to 20% higher, so they are moved down: a line below the diagonal.
> Plot (b) uses a piece-wise linear warp. Each line starts at the origin and has three straight pieces. It bends twice, near $f_{\text{low}}$ and near $f_{\text{high}}$, so that both lines meet in the same top-right corner and the upper band edge maps to itself. $f_{\text{low}}$ and $f_{\text{high}}$ are the frequencies marked on the horizontal axis that fix where the bends are. Between them the male line stays above the diagonal and the female line below it, as in (a).
> In (a) the male line reaches the top of the band before the right edge, and the female line ends below the top at the right edge: the warped spectrum does not fill the same band for all speakers.

## 6. Model adaptation (slides 17-20)
![[cs568_lecture_14_l14_p017.jpg]]
![[cs568_lecture_14_l14_p018.jpg]]
![[cs568_lecture_14_l14_p019.jpg]]
![[cs568_lecture_14_l14_p020.jpg]]

**Notes.** Adaptation changes the model to fit a new speaker, channel or condition, with much less data than training. It sits between speaker-dependent (SD) and speaker-independent (SI) systems. **Supervised**: the text of the adaptation data is known. **Unsupervised**: the initial model recognises it first.

**MLLR** (maximum likelihood linear regression), the most widely used. A linear transform on the Gaussian means, shared by a class $c$ of Gaussians and learned with EM:

$$\hat{\boldsymbol\mu}_{jm}=\mathbf A_c\,\boldsymbol\mu_{jm}+\mathbf b_c$$

With about 15 s of data it improves recognition by 7 to 10%. Constrained MLLR uses the same transform for the covariance: $\hat{\boldsymbol\Sigma}_{jm}=\mathbf A_c\boldsymbol\Sigma_{jm}\mathbf A_c^{\mathsf T}$.

**MAP adaptation.** Use the SI parameters as priors: $\theta_{\mathrm{MAP}}=\arg\max_\theta P(X\mid\theta)P(\theta)$. For a mean:

$$\hat{\boldsymbol\mu}_{jm}=\frac{N_{jm}}{N_{jm}+\tau}\,\bar{\boldsymbol\mu}_{jm}+\frac{\tau}{N_{jm}+\tau}\,\boldsymbol\mu_{jm}$$

$\boldsymbol\mu_{jm}$ is the SI mean, $\bar{\boldsymbol\mu}_{jm}$ the mean of the adaptation data, $N_{jm}$ the occupation count, $\tau$ the weight of the prior. With little data the mean stays near the SI value. MAP needs much more data than MLLR, because only Gaussians seen in the adaptation data move.

**Speaker adaptive training** (SAT). Put MLLR inside training: estimate a transform per training speaker, re-estimate the model on the transformed data, repeat. Error rates drop a further 5 to 10% relative to MLLR.

> [!example] MAP mean
> SI mean 2, data mean 4, $\tau=10$. $N=10$: $\hat\mu=0.5\cdot4+0.5\cdot2=3$. $N=90$: $\hat\mu=0.9\cdot4+0.1\cdot2=3.8$.

> [!question]- Check (slide 17): Compare SD and SI recognisers as the slide does, and say where adaptation sits. In unsupervised adaptation, what is used in place of the known text, and what goes wrong if the initial model is poor?
> SD (speaker-dependent): trained on one speaker, easier to train and more accurate for that speaker, but it needs much data from that speaker. SI (speaker-independent): trained on many speakers, longer training, lower accuracy, but it works for a new speaker at once.
> Adaptation starts from the SI model and moves it towards one speaker (or channel, or recording condition) with only a fraction of the data that training from scratch needs. It is a trade-off between the two.
> Unsupervised: the initial model recognises the adaptation data, and its output is used as if it were the true transcription. If that recognition has many errors, frames are assigned to the wrong models, and the adaptation moves these models towards wrong data. The errors are reinforced. So the initial recognition must be accurate enough.

> [!question]- Check (slide 18): In $\hat{\boldsymbol\mu}_{jm}=\mathbf A_c\boldsymbol\mu_{jm}+\mathbf b_c$ name each symbol. For $D=13$, how many parameters does one full transform have, and how many with two classes? Apply $\mathbf A=\mathrm{diag}(1.1,\ 0.9)$, $\mathbf b=(0.5,\ -0.2)$ to $\boldsymbol\mu=(2,\ 3)$. Why can MLLR work with 15 seconds of data?
> $\boldsymbol\mu_{jm}$: the SI mean of mixture component $m$ in state $j$. $\hat{\boldsymbol\mu}_{jm}$: the adapted mean. $\mathbf A_c$: a $D\times D$ regression matrix. $\mathbf b_c$: a bias vector. $c$: the class of Gaussians that share this transform (the slide says "broad class"; the usual name is regression class, beyond the slides).
> Parameters: $D^2+D=169+13=182$ for one transform, $2\cdot182=364$ for two classes.
> $\hat{\boldsymbol\mu}=(1.1\cdot2+0.5,\ 0.9\cdot3-0.2)=(2.7,\ 2.5)$.
> The transform is shared: the 182 numbers are estimated from all frames of the class together, and then they move every Gaussian of the class, also Gaussians of sounds that never occurred in the adaptation data. With one class ($c=1$) all Gaussians of the model move. More data allows more classes, so the transforms become more specific.

> [!question]- Check (slide 19): $\tau=10$, SI mean $\mu_{jm}=1.0$. Gaussian A was never seen in the adaptation data ($N=0$). Gaussian B has $N=30$ and data mean $\bar\mu=3.0$. Give both adapted means. What do $\tau\to0$ and $\tau\to\infty$ do? Why does MAP need more data than MLLR although it is "more flexible"?
> A: the data weight is $\frac{0}{0+10}=0$, so $\hat\mu=\mu_{jm}=1.0$. The Gaussian does not move.
> B: the data weight is $\frac{30}{30+10}=0.75$, the prior weight $\frac{10}{40}=0.25$. $\hat\mu=0.75\cdot3.0+0.25\cdot1.0=2.5$.
> $\tau\to0$: no trust in the prior, $\hat\mu\to\bar\mu$ (the ML estimate from the adaptation data). $\tau\to\infty$: $\hat\mu\to\mu_{jm}$, no adaptation.
> MAP updates every Gaussian separately with its own data. This is the "more degrees of freedom": with much data each Gaussian converges to the speaker's own value. But a Gaussian without data stays where it was (A above), so a large amount of data is needed before most of the model has moved. MLLR moves all Gaussians with a few shared numbers.

> [!question]- Check (slide 20): Put the SAT steps in order and say what is repeated. Why is the final SI model "less influenced by speaker-specific characteristics"? What does a SAT model need at recognition time that a plain SI model does not need (beyond the slides)?
> Order: (1) start with a default SI recogniser. (2) Estimate an MLLR transform for each training speaker. (3) Re-estimate the model using each speaker's data together with that speaker's transform. (4) Repeat 2 and 3 until convergence or a stop condition.
> In plain SI training the Gaussians must absorb the differences between speakers, so they become broad. In SAT these differences are taken up by the per-speaker transforms. The shared model only has to describe the differences between sounds, so its Gaussians are tighter.
> Beyond the slides: the model was trained together with transforms, so a new speaker also needs a transform. It is estimated from some adaptation data of that speaker, as in MLLR.

## 7. Discriminative training (slides 21-26)
![[cs568_lecture_14_l14_p021.jpg]]
![[cs568_lecture_14_l14_p022.jpg]]
![[cs568_lecture_14_l14_p023.jpg]]
![[cs568_lecture_14_l14_p024.jpg]]
![[cs568_lecture_14_l14_p025.jpg]]
![[cs568_lecture_14_l14_p026.jpg]]

**Notes.** MLE trains each model only on its own class. Nothing guarantees that the correct model then beats the wrong ones, because HMMs are not the true model of speech. Discriminative training improves the correct model relative to the others. Write the posterior as

$$P(\omega_i\mid x)=\frac{p(x\mid\omega_i)P(\omega_i)}{p(x\mid\omega_i)P(\omega_i)+\sum_{j\ne i}p(x\mid\omega_j)P(\omega_j)}$$

To raise it, raise $p(x\mid\omega_i)$ and lower the $p(x\mid\omega_{j\ne i})$. Only the ratio matters: doubling all likelihoods changes nothing.

- **MMI** (maximum mutual information): maximise $I(x,\omega_i\mid\theta)=\log\dfrac{p(x,\omega_i\mid\theta)}{p(x\mid\theta)\,P(\omega_i\mid\theta)}$. The numerator pushes the correct model up, the denominator pushes all competitors down.
- **Corrective training**: change parameters only for utterances where an incorrect model beats the correct one by a margin $\Delta$. Then move the correct model's probabilities up and the incorrect ones down.
- **GPD** (generalised probabilistic descent): a misclassification measure $d_j(x\mid\theta)$ compares the correct class with a soft combination of the competitors, controlled by $\eta$ (for $\eta\to\infty$ only the best competitor counts). Pass it through a sigmoid $S$ and minimise $E(\theta)=\sum_j\sum_{x\in\omega_j}S(d_j(x;\theta))$ by gradient descent.

MMI and GPD cost much more than MLE, because every model is updated for every training example. They are used for tasks with few classes or little data. Otherwise corrective training is the pragmatic choice.

> [!warning] GPD on the slide
> The slide mixes two sign conventions. In the original formulation $g_k$ is a score where larger is better and $d_j(x)=-g_j(x)+\frac1\eta\log\big[\frac{1}{C-1}\sum_{k\ne j}e^{\eta g_k(x)}\big]$, so $d_j>0$ means an error. Also, the inner sum of $E(\theta)$ should use $d_j$ for $x\in\omega_j$; the slide writes $d_i$.

> [!info] The MMI criterion step by step (slide 23)
> Mutual information (karşılıklı bilgi) measures how much the observation $x$ tells about the class label $\omega$. For one pair $(x,\omega_i)$:
> $$I(x,\omega_i\mid\theta)=\log\frac{p(x,\omega_i\mid\theta)}{p(x\mid\theta)\,P(\omega_i\mid\theta)}$$
> Step 1. Write the joint as likelihood times prior: $p(x,\omega_i\mid\theta)=p(x\mid\omega_i,\theta)\,P(\omega_i\mid\theta)$.
> Step 2. The prior cancels: $I=\log\dfrac{p(x\mid\omega_i,\theta)}{p(x\mid\theta)}$.
> Step 3. Expand the denominator over the $C$ models:
> $$I(x,\omega_i\mid\theta)=\log\frac{p(x\mid\omega_i,\theta)}{\sum_{c=1}^{C}p(x\mid\omega_c,\theta)\,P(\omega_c\mid\theta)}$$
> The numerator is the likelihood of the correct model, which MLE also maximises. The denominator is the prior-weighted average likelihood of ALL models. $I$ grows when the correct model explains $x$ better than the average model does. Compared with the posterior on slide 22, the numerator has no prior and there is a logarithm. The slide updates the parameters by gradient descent. The reason (beyond the slides): the simple closed-form Baum-Welch update does not apply to this criterion.

> [!question]- Check (slide 21): Find the mistake: "MLE makes $p(x\mid\omega_i)$ as large as possible for the training data of class $\omega_i$, so after MLE training every training utterance is recognised correctly."
> Recognition needs $p(x\mid\omega_i)$ to be larger than the likelihood of every wrong model, $p(x\mid\omega_j)$, $j\ne i$. MLE never looks at this comparison. The parameters $\theta_i$ are trained only on data of class $\omega_i$, and the wrong models are trained on their own data. Nothing stops a wrong model from giving $x$ an even higher likelihood.
> A large likelihood is not the same as a larger likelihood than the competitors. Discriminative training optimises the second: the correct model relative to the incorrect ones.

> [!question]- Check (slide 22): For an utterance $x$ of class $\omega_i$: $p(x\mid\omega_i)P(\omega_i)=0.06$, and the two competitors give $0.01$ and $0.03$. Compute $P(\omega_i\mid x)$ with the last form on the slide. After a training step the correct term is $0.12$ and the competitors sum to $0.12$. Did the step help? Why can $p(x\mid\theta)$ not be ignored during training?
> $P(\omega_i\mid x)=\dfrac{1}{1+\frac{\sum_{j\ne i}p(x\mid\omega_j)P(\omega_j)}{p(x\mid\omega_i)P(\omega_i)}}=\dfrac{1}{1+\frac{0.04}{0.06}}=\dfrac{1}{1.667}=0.60$
> After the step: $\dfrac{1}{1+\frac{0.12}{0.12}}=0.50$. The likelihood of the correct model doubled, but the posterior fell from 0.60 to 0.50, because the competitors grew by a factor of 3.
> In recognition $\theta$ is fixed and $p(x\mid\theta)$ is the same for all classes, so it does not change which class wins. In training $\theta$ changes, and with it the denominator $p(x\mid\theta)$, which contains all the competitors. Only the ratio in the last form decides. It must go down.

> [!question]- Check (slide 23): $C=3$ models with equal priors $1/3$ and likelihoods $p(x\mid\omega_c)=0.06,\ 0.02,\ 0.04$. The correct model is the first. Compute $I(x,\omega_1\mid\theta)$ (natural log). What does the sign mean? Which part of the expression makes the criterion discriminative?
> Denominator: $\sum_cp(x\mid\omega_c)P(\omega_c)=\frac{0.06+0.02+0.04}{3}=0.04$.
> $I=\log\dfrac{0.06}{0.04}=\log1.5=0.405$.
> Positive: the correct model explains $x$ better than the average model. If the correct model were the second one, $I=\log\frac{0.02}{0.04}=-0.693$, negative.
> The denominator makes it discriminative. To increase $I$ it is not enough to raise the numerator. The likelihoods of the other models, which sit in the denominator, must stay small. MLE would maximise the numerator alone.

> [!question]- Check (slide 24): Margin $\Delta=0.01$. For three utterances the pair (likelihood of the correct model, likelihood of the best incorrect model) is A: $(0.050,\ 0.030)$, B: $(0.030,\ 0.035)$, C: $(0.020,\ 0.050)$. By the rule on the slide, which utterances change the parameters, and in which direction?
> Notation of this slide: $\omega_c$ is the correct model and $\omega_i$ an incorrect model. This is the reverse of slides 21 to 23, where $\omega_i$ is the correct class.
> Rule: correct the models if $p(x\mid\omega_i,\theta)\ge p(x\mid\omega_c,\theta)+\Delta$.
> A: $0.030\ge0.060$ is false. Recognised correctly, no change.
> B: $0.035\ge0.040$ is false. The utterance is misrecognised, but the incorrect model wins by only $0.005$, less than the margin. No change.
> C: $0.050\ge0.030$ is true. The parameters change: $p(x\mid\omega_c)$ is moved up (towards the example) and $p(x\mid\omega_i)$ is moved down.
> So only clearly wrong utterances cost work. All others are skipped, which is why the method is "pragmatic". A larger $\Delta$ means fewer corrections.

> [!question]- Check (slide 25): Use the convention of the warning above (larger $g$ is better, $d_j>0$ means an error). The correct class has $g_j=-4$, the two competitors have $g_k=-3$ and $-7$. Compute $d_j$ for $\eta=1$ and for $\eta\to\infty$. What does $\eta$ control, and why is $d$ passed through a sigmoid $S$?
> In this convention $g_k=+\log p(x\mid\omega_k,\theta)$ and $d_j=-g_j+\frac1\eta\log\big[\frac{1}{C-1}\sum_{k\ne j}e^{\eta g_k}\big]$. The slide itself writes $g_i=-\log p(x\mid\omega_i,\theta)$ and $d_j=g_j-\log\{\dots\}^{1/\eta}$. With that $g$ a larger value is worse, and the "maximum" for $\eta\to\infty$ would be the weakest competitor. This is why the warning corrects the signs.
> Competing term for $\eta=1$, $C-1=2$: $\log\big[\frac12(e^{-3}+e^{-7})\big]=\log(0.02535)=-3.675$. Then $d_j=-g_j+(-3.675)=4-3.675=0.325>0$: an error.
> $\eta\to\infty$: the competing term tends to the best competitor, $\max(-3,-7)=-3$. $d_j=4-3=1>0$.
> $\eta$ controls how the competitors are combined: a small $\eta$ lets all of them contribute, a large $\eta$ keeps only the strongest one. Note that the "average" at $\eta=1$ is not the plain mean $-5$. It is an average of the $e^{g_k}$, so it stays close to the largest value.
> The number of errors is a step function of $d$ (0 for $d<0$, 1 for $d>0$). It has no useful gradient. The sigmoid is a smooth version of this step, so $E(\theta)$ is a differentiable count of the errors and gradient descent can minimise it.

> [!question]- Check (slide 26): A recogniser has 1000 word models. For ONE training utterance, which models are updated by MLE, by MMI or GPD, and by corrective training? Use this to explain the recommendation on the slide.
> MLE: only the 1 correct model, with the closed-form Baum-Welch update.
> MMI or GPD: the criterion contains all competing models (the denominator in MMI, the competing term in GPD). So all 1000 models are evaluated and every parameter is updated for every example, and this is done with many small gradient-descent steps.
> Corrective training: nothing if the utterance is recognised well enough. Otherwise only the correct model (moved up) and the incorrect models that beat it by the margin $\Delta$ (moved down).
> So MMI and GPD are affordable only with few classes or few data samples. For the other cases the slide recommends corrective training.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Left-right HMM | $a_{ij}=0$ for $j<i$ and $j>i+\Delta$ |
| Scaling | $\hat\alpha_t=c_t\alpha_t$, $\log P=-\sum_t\log c_t$ |
| Segmental k-means | uniform segmentation, estimate $B$, Viterbi re-segmentation, repeat |
| C-HMM | $b_j(\mathbf o)=\sum_kc_{jk}\mathcal N(\mathbf o;\boldsymbol\mu_{jk},\boldsymbol\Sigma_{jk})$ |
| SC-HMM | shared Gaussians, state-specific weights $c_{jk}$ |
| Spectral subtraction | $\max(|Y|^2-\overline{|D|^2},0)$, additive noise |
| CMN, RASTA | remove convolutional (channel) effects in the cepstrum |
| VTLN | warp the frequency axis per speaker |
| MLLR | $\hat{\boldsymbol\mu}=\mathbf A_c\boldsymbol\mu+\mathbf b_c$ |
| MAP | $\hat{\boldsymbol\mu}=\frac{N}{N+\tau}\bar{\boldsymbol\mu}+\frac{\tau}{N+\tau}\boldsymbol\mu$ |
| Discriminative training | MMI, corrective training, GPD |

## Check yourself
> [!question]- 1. 300 frames, each multiplies $\alpha$ by about $10^{-3}$. Can $P(O\mid\lambda)$ be stored as a double?
> $P\approx10^{-900}$, far below $10^{-308}$, so it underflows to 0. With scaling or logs we store $\log_{10}P\approx-900$.

> [!question]- 2. $\gamma_t(j)=0.5$ and component posteriors $(0.2,0.3,0.5)$. Give $\gamma_t(j,k)$.
> $0.10,\ 0.15,\ 0.25$. They sum to $0.5=\gamma_t(j)$.

> [!question]- 3. Frame power $(8,1,4)$, average noise $(2,2,2)$. Spectral subtraction?
> $(6,-1,2)$, floored at zero: $(6,0,2)$.

> [!question]- 4. A male speaker's average F2 is 1350 Hz, population 1500 Hz. Warp factor?
> $1350/1500=0.90$. Dividing his frequencies by 0.90 moves them up to the common scale.

> [!question]- 5. MAP with $\tau=20$: how many frames to move 80% of the way to the data mean?
> $N/(N+20)=0.8$ gives $N=80$.

> [!question]- 6. Correct class has $p(x\mid\omega_c)P(\omega_c)=0.05$, competitors 0.03 and 0.02. Posterior? What if all likelihoods double?
> $0.05/0.10=0.5$. Doubling everything leaves it at 0.5: only the ratio matters.

## Links
- Course index: [[CS568 Home]]
- Previous lecture: [[CS568 L13 - Hidden Markov models]]
- Full step-by-step notes (PDF): [CS568_Lecture_14_HMM_Refinements.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_14/CS568_Lecture_14_HMM_Refinements.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_14/CS568_Lecture_14_HMM_Refinements.pdf`
- Original slides (PDF): [l14.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_14/l14.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_14/l14.pdf`

Slides not embedded: 1
