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

## 5. Speaker-independent recognition (slides 15-16)
![[cs568_lecture_14_l14_p015.jpg]]
![[cs568_lecture_14_l14_p016.jpg]]

**Notes.** Speakers differ physically, in accent and in style. Options: train on many speakers (the Gaussians become broad and overlap), train separate recognisers per speaker type (male, female), normalise the speaker, or adapt the model.

**Vocal tract length normalisation** (VTLN). A shorter vocal tract gives higher formants. Female formants are 10 to 20% higher than male ones. Warp the frequency axis so that all speakers lie on a common scale. Two approaches: a piece-wise linear warp from average formant frequencies, or a warping function whose parameter is optimised from data (pick the warp factor that maximises the likelihood).

> [!example] Warp factor
> Speaker's average F2 is 1650 Hz, population average 1500 Hz: factor $1650/1500=1.10$. Check: $1650/1.10=1500$ Hz.

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
