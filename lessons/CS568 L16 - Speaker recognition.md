---
course: CS 568 Speech Processing
lecture: 16
title: Speaker recognition
tags: [cs568, lecture]
slides: lectures/lecture_16/l16.pdf
full_notes: lecture_notes/lecture_16/CS568_Lecture_16_Speaker_Recognition.pdf
---
# CS 568 · Lecture 16 · Speaker recognition

> [!summary] In short
> - Identification picks one speaker out of many and gets harder with more speakers. Verification tests one claimed identity and does not.
> - Features: MFCCs with $\Delta$, $\Delta^2$ and cepstral mean subtraction (CMS). High-level cues (words, prosody) need long recordings.
> - Models: DTW templates and nearest neighbour (non-parametric), VQ, GMM, HMM (parametric). Speaker GMMs are adapted from a universal background model (UBM).
> - Verification thresholds a likelihood ratio against a background model.
> - Performance: false acceptance against false rejection on ROC and DET curves, summarised by the EER or the DCF.

## 1. Identification, verification, text dependence (slides 2-6)
![[cs568_lecture_16_l16_p002.jpg]]
![[cs568_lecture_16_l16_p003.jpg]]
![[cs568_lecture_16_l16_p004.jpg]]
![[cs568_lecture_16_l16_p005.jpg]]
![[cs568_lecture_16_l16_p006.jpg]]

**Notes.**
- **Speaker identification (SID)**: match an unknown voice to one of several speaker models. No identity is claimed. Closed-set: the speaker is one of the enrolled ones, so the answer is always a name. Open-set: the answer may be "no match". Accuracy drops as the number of speakers grows.
- **Speaker verification**: the user claims an identity. The sample is compared only with the claimed model (and with a general impostor model, slide 4). One comparison, so performance does not depend on the population size.
- Two phases (slide 5): **enrollment** trains one model ("voiceprint") per speaker. **Verification** runs the same feature extraction on a new utterance, fetches the claimed model, scores, decides.
- **Text-dependent**: the system knows the text (fixed password or prompted phrase). Random prompts stop replay attacks, so it suits security. **Text-independent**: the text is unknown. More flexible, harder, suits uncooperative speakers.

## 2. Speaker features (slides 7-9)
![[cs568_lecture_16_l16_p007.jpg]]
![[cs568_lecture_16_l16_p008.jpg]]
![[cs568_lecture_16_l16_p009.jpg]]

**Notes.** Low-level features: short-time spectra (MFCCs, the same as in speech recognition), their derivatives $\Delta$ and $\Delta^2$, pitch and energy averages. High-level features: word usage, phone sequences, pronunciation, pitch and energy contours. These need long recordings and are hard to extract.

**Cepstral mean subtraction (CMS)**: subtract the average cepstral vector over the recording, with $\mathbf c(t)$ the cepstrum of frame $t$ and $T$ frames:

$$\tilde{\mathbf c}(t)=\mathbf c(t)-\bar{\mathbf c},\qquad \bar{\mathbf c}=\frac1T\sum_{t=1}^{T}\mathbf c(t)$$

A fixed channel $h[n]$ convolves the signal, so it adds a constant in the cepstrum: $\mathbf c_y(t)=\mathbf c_x(t)+\mathbf c_h$. Subtracting the mean removes it: $\mathbf c_y(t)-\bar{\mathbf c}_y=\mathbf c_x(t)-\bar{\mathbf c}_x$.

> [!warning] Watch out
> CMS removes everything constant over the recording, including the speaker's own long-term average spectrum. It does not cancel a channel that changes during the recording.

## 3. Non-parametric models (slides 10-12)
![[cs568_lecture_16_l16_p010.jpg]]
![[cs568_lecture_16_l16_p011.jpg]]
![[cs568_lecture_16_l16_p012.jpg]]

**Notes.** Non-parametric models keep the enrollment data. Parametric models summarise it (VQ, GMM, HMM, SVM).

**Template matching**: for fixed passwords. Align the test utterance with each enrollment utterance by DTW, accept if the distance is below a threshold. Rarely used today.

**Nearest neighbour**: with $X=\{\mathbf x_j\}$ the enrollment vectors, $\mathbf y$ a test vector, $d_{nn}(\mathbf y,X)=\min_{\mathbf x_j\in X}\lVert\mathbf y-\mathbf x_j\rVert$ and $V[r]\propto r^D$ the volume of a $D$-dimensional sphere:

$$p_{nn}(\mathbf y;X)=\frac{1}{V\big[d_{nn}(\mathbf y,X)\big]},\qquad s_{nn}(Y;X)=-\sum_{\mathbf y_j\in Y}\ln d_{nn}(\mathbf y_j,X)$$

The speaker with the largest $s_{nn}$ is identified. The measure $s'_{nn}$ on slide 12 works better: average squared nearest-neighbour distance from $Y$ to $X$ and from $X$ to $Y$, minus the same averages inside $Y$ and inside $X$.

> [!warning] Watch out
> $s_{nn}$ is a similarity (pick the maximum). $s'_{nn}$ is a distance (pick the minimum, it can be negative). A test vector equal to an enrollment vector gives $d_{nn}=0$ and an infinite $s_{nn}$, so real systems floor $d_{nn}$.

## 4. Parametric models: VQ, GMM, HMM (slides 13-15)
![[cs568_lecture_16_l16_p013.jpg]]
![[cs568_lecture_16_l16_p014.jpg]]
![[cs568_lecture_16_l16_p015.jpg]]

**Notes.**
- **VQ**: a codebook of centroids $\boldsymbol\mu_j$ built with LBG. Start with $k=1$, split the cluster with the largest variance into $\boldsymbol\mu\pm\boldsymbol\epsilon$, re-run k-means, repeat. Score: the sum-squared error of the test vectors against the claimed speaker's codebook (small is good),

$$D=\sum_{j=1}^{J}\ \sum_{\mathbf x_i\to\boldsymbol\mu_j}\lVert\mathbf x_i-\boldsymbol\mu_j\rVert^2$$

- **GMM**: $p(\mathbf y\mid\lambda)=\sum_{m=1}^{M}w_m\,\mathcal N(\mathbf y;\boldsymbol\mu_m,\Sigma_m)$, trained with EM. Score: the average log-likelihood, so that utterances of different lengths compare,

$$s(Y\mid\lambda)=\frac1T\sum_{t=1}^{T}\log p(\mathbf y_t\mid\lambda)$$

- **Likelihood-ratio test** for verification: accept if $\dfrac{p(Y\mid\lambda)}{p(Y\mid\lambda_{BG})}>\theta$, where $\lambda_{BG}$ is a background model trained on a large independent database.
- **HMM**: best for text-dependent systems (phone, word or sentence models, Baum-Welch). For text-independent systems ergodic HMMs (all transitions allowed) add little over GMMs.

> [!warning] Slide error
> Slide 13 writes the summand of $D$ as $(x_i-\mu_j)$. A sum-squared error needs the squared norm, as above.

> [!info] GMMs ignore frame order
> Each term of $s(Y\mid\lambda)$ uses only its own frame, so shuffling the frames does not change the score.

## 5. Adapting from a UBM (slides 16-17)
![[cs568_lecture_16_l16_p016.jpg]]
![[cs568_lecture_16_l16_p017.jpg]]

**Notes.** Enrollment data is too little to train a model from scratch, and conditions differ between enrollment and test (handset, network, noise). So train a large GMM on many speakers, the **universal background model (UBM)**, and adapt it to each speaker.

MAP adaptation of the means (from our notes, after Reynolds et al., 2000). $\gamma_m(t)$ is the UBM posterior of component $m$ for speaker frame $\mathbf x_t$, $r$ is the relevance factor (about 16):

$$n_m=\sum_t\gamma_m(t),\qquad E_m(\mathbf x)=\frac{1}{n_m}\sum_t\gamma_m(t)\,\mathbf x_t$$

$$\hat{\boldsymbol\mu}_m=\alpha_m E_m(\mathbf x)+(1-\alpha_m)\,\boldsymbol\mu_m^{UBM},\qquad \alpha_m=\frac{n_m}{n_m+r}$$

Components that saw much speaker data move to the data. The others stay at the UBM. The UBM itself serves as $\lambda_{BG}$ in the likelihood ratio.

## 6. Decision rules, threshold, normalisation (slides 18-19)
![[cs568_lecture_16_l16_p018.jpg]]
![[cs568_lecture_16_l16_p019.jpg]]

**Notes.**
- Identification: $\hat S=\arg\max_j s(Y\mid\lambda_j)$.
- Verification: accept if $s(Y\mid\lambda_i)\ge\theta$ for the claimed speaker $i$.
- Open-set identification: closed-set identification first, then verification of the winner.

Bayes threshold on the likelihood ratio, with $c_{fa}$ the cost of a false acceptance, $c_{fr}$ the cost of a false rejection, $p_{imp}$ the prior probability of an impostor:

$$\theta^*=\frac{c_{fa}}{c_{fr}}\cdot\frac{p_{imp}}{1-p_{imp}}$$

Real scores do not behave as the theory says, so they are normalised: $\Lambda_{norm}=(\Lambda-\mu)/\sigma$. **Z-norm**: $\mu,\sigma$ from scoring the target model offline against impostor utterances. **T-norm**: from scoring the test utterance against a cohort of impostor models. **H-norm**: Z-norm per handset type.

> [!warning] Slide wording
> Slide 19 calls $c_{fa}$ and $c_{fr}$ "rates". They are costs, not error rates.

## 7. Errors, ROC, DET, EER, DCF (slides 20-22)
![[cs568_lecture_16_l16_p020.jpg]]
![[cs568_lecture_16_l16_p021.jpg]]
![[cs568_lecture_16_l16_p022.jpg]]

**Notes.** Verification has two errors: false acceptance (impostor accepted, probability $p_{fa}$) and false rejection (genuine speaker rejected, $p_{fr}$). Moving $\theta$ lowers one and raises the other.

- **ROC**: true-positive rate $1-p_{fr}$ against $p_{fa}$ as $\theta$ sweeps.
- **DET**: $p_{fr}$ against $p_{fa}$ on normal-deviate axes, $z=\Phi^{-1}(p)$. If impostor scores are $\mathcal N(0,1)$ and genuine scores $\mathcal N(d',1)$, then $\Phi^{-1}(p_{fr})=-\Phi^{-1}(p_{fa})-d'$: a straight line of slope $-1$. Better systems lie closer to the bottom-left corner.
- **EER**: the error at the threshold where $p_{fa}=p_{fr}$. One number, no costs.
- **DCF**: choose the threshold that minimises $C=p_{imp}\,c_{fa}\,p_{fa}+(1-p_{imp})\,c_{fr}\,p_{fr}$.

> [!warning] Slide wording
> Slide 20 says "detection error threshold" and "plotted in log scale". The name is detection error trade-off, and the axes are normal-deviate (probit) scaled, not logarithmic.

## 8. Applications (slide 23)
![[cs568_lecture_16_l16_p023.jpg]]

**Notes.** Transaction authentication and access control use verification. Monitoring, information retrieval (audio indexing, speaker diarisation) and forensics mostly use identification. A voice changes with health, age and channel, and it can be replayed or synthesised, so secure systems use random prompts and liveness checks.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Closed-set / open-set | answer is always a name / may be "no match" |
| CMS | $\tilde{\mathbf c}(t)=\mathbf c(t)-\bar{\mathbf c}$, cancels a fixed channel |
| Nearest-neighbour score | $s_{nn}(Y;X)=-\sum_j\ln d_{nn}(\mathbf y_j,X)$, maximise |
| VQ distortion | $D=\sum_j\sum_{\mathbf x_i\to\boldsymbol\mu_j}\lVert\mathbf x_i-\boldsymbol\mu_j\rVert^2$, minimise |
| GMM score | $s(Y\mid\lambda)=\frac1T\sum_t\log p(\mathbf y_t\mid\lambda)$ |
| Likelihood-ratio test | $p(Y\mid\lambda)/p(Y\mid\lambda_{BG})$ compared with $\theta$ |
| UBM | large GMM trained on many speakers |
| MAP adaptation | $\hat{\boldsymbol\mu}_m=\alpha_mE_m(\mathbf x)+(1-\alpha_m)\boldsymbol\mu_m^{UBM}$, $\alpha_m=n_m/(n_m+r)$ |
| Bayes threshold | $\theta^*=(c_{fa}/c_{fr})\,p_{imp}/(1-p_{imp})$ |
| Z-norm, T-norm | $(\Lambda-\mu)/\sigma$ with impostor utterances / cohort models |
| EER | error where $p_{fa}=p_{fr}$ |
| DCF | $C=p_{imp}c_{fa}p_{fa}+(1-p_{imp})c_{fr}p_{fr}$ |

## Check yourself
> [!question]- 1. Why does identification accuracy drop with 1000 enrolled speakers instead of 10, while verification accuracy does not?
> In identification the correct model must beat every wrong model, and 999 wrong models give many more chances of an accidental win. Verification makes one comparison with the claimed model, so the other speakers never enter.

> [!question]- 2. Two frames have 1-D cepstra 4 and 6 after a channel with $c_h=3$. Apply CMS. Compare with CMS on the clean frames.
> Received: mean 5, so $-1$ and $+1$. Clean frames: $4-3=1$ and $6-3=3$, mean 2, so $-1$ and $+1$. The same: CMS output does not depend on the channel.

> [!question]- 3. Codebook $\mu_1=(0,0)$, $\mu_2=(2,2)$. Test vectors $(0,1)$, $(2,1)$, $(3,3)$. Compute the VQ distortion $D$.
> $(0,1)$ goes to $\mu_1$ with error 1. $(2,1)$ goes to $\mu_2$ with error 1. $(3,3)$ goes to $\mu_2$ with error 2. $D=1+1+2=4$.

> [!question]- 4. 1-D, $r=16$, $\mu^{UBM}_A=1.0$. Frames $x=1,2,3$ have posteriors $\gamma_A=0.9,\ 0.8,\ 0.3$. Find the adapted mean.
> $n_A=2.0$. $E_A(x)=(0.9+1.6+0.9)/2.0=1.7$. $\alpha_A=2/18\approx0.111$. $\hat\mu_A=0.111\cdot1.7+0.889\cdot1.0\approx1.078$. With little data the prior wins.

> [!question]- 5. Impostor scores $\mathcal N(0,1)$, genuine scores $\mathcal N(3,1)$. Find the EER.
> $p_{fa}=\Phi(-\theta)$, $p_{fr}=\Phi(\theta-3)$. Equal when $-\theta=\theta-3$, so $\theta=1.5$. $\mathrm{EER}=\Phi(-1.5)=0.0668\approx6.7\%$.

> [!question]- 6. $p_{imp}=0.1$, $c_{fa}=5$, $c_{fr}=1$, $p_{fa}=2\%$, $p_{fr}=10\%$. Compute the DCF.
> $0.1\cdot5\cdot0.02=0.01$ and $0.9\cdot1\cdot0.10=0.09$. $C=0.10$.

## Links
- Course index: [[CS568 Home]]
- Previous lecture: [[CS568 L15 - Large vocabulary speech recognition]]
- Quizzes and labs that use this lecture: none (the midterm booklet covers Lectures 1 to 6).
- Full step-by-step notes (PDF): [CS568_Lecture_16_Speaker_Recognition.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_16/CS568_Lecture_16_Speaker_Recognition.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_16/CS568_Lecture_16_Speaker_Recognition.pdf`
- Original slides (PDF): [l16.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_16/l16.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_16/l16.pdf`

Slides not embedded: 1
