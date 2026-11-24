---
course: CS 568 Speech Processing
lecture: 20
title: Voice conversion
tags: [cs568, lecture]
slides: lectures/lecture_20/l20.pdf
full_notes: lecture_notes/lecture_20/CS568_Lecture_20_Voice_Conversion.pdf
---
# CS 568 · Lecture 20 · Voice conversion

> [!summary] In short
> - Voice conversion (VC) changes the vocal-tract filter of a source speaker so that it sounds like a target speaker. Prosody is handled with the methods of Lecture 19.
> - Pipeline: extract features, align source and target frames with DTW, train a mapping. At recall: features, mapping, synthesis.
> - Codebook mapping (VQ) is simple but coarse. Dynamic frequency warping (DFW) keeps quality but cannot change formant heights.
> - GMM conversion blends one linear regression per Gaussian. Good similarity, but over-smoothed.
> - Without a parallel corpus: adapt a model trained on another speaker pair. Accent conversion: learner's voice plus native speaker's gestures.

## 1. Voice transformation, terms, applications (slides 2-5)
![[cs568_lecture_20_l20_p002.jpg]]
![[cs568_lecture_20_l20_p003.jpg]]
![[cs568_lecture_20_l20_p004.jpg]]
![[cs568_lecture_20_l20_p005.jpg]]

**Notes.** Voice transformation controls non-linguistic information: voice quality and individuality. Source transformations are mostly prosodic (Lecture 19). This lecture is about the vocal-tract filter, changed segment by segment.

| Term | Meaning |
|---|---|
| Voice transformation | change the filter in a general way, no target speaker |
| Voice conversion | make the source speaker's filter like a target speaker's |
| Voice morphing | blend two voices into a third |
| Accent conversion | change a regional or foreign accent into another (for example native) |
| Cross-language conversion | translate speech into another language (ASR, MT, TTS) |

Applications: custom TTS voices (10 minutes of data instead of 10 hours), dubbing, personalised interfaces, voice restoration, entertainment, testing speaker recognition.

Methods by mapping: VQ, GMM, DFW, combinations. By corpus: parallel recordings (both speakers say the same sentences) or non-parallel.

## 2. Building blocks (slide 6)
![[cs568_lecture_20_l20_p006.jpg]]

**Notes.** Training: extract features from source and target, align the two sequences (DTW for a parallel corpus), train the mapping on aligned pairs $(x_t,y_t)$. Here $x_t$ is the source vector of frame $t$ and $y_t$ the target vector aligned with it. Recall: extract features from a new source utterance, apply the mapping, synthesise.

DTW works because in a parallel corpus both speakers say the same sounds, only at different speeds.

## 3. Codebook mapping and frequency warping (slides 7-10)
![[cs568_lecture_20_l20_p007.jpg]]
![[cs568_lecture_20_l20_p008.jpg]]
![[cs568_lecture_20_l20_p009.jpg]]
![[cs568_lecture_20_l20_p010.jpg]]

**Notes.** **Abe et al. (1988)**: learn a VQ codebook per speaker ($c^A_i$ and $c^B_j$), align frames with DTW, and count in a 2-D histogram $h_{ij}$ how often codeword $i$ of A met codeword $j$ of B. The mapped codeword is the weighted average (formula from our notes):

$$\hat c^{A\to B}_i=\frac{\sum_j h_{ij}\,c^B_j}{\sum_j h_{ij}}$$

At conversion a source frame is quantised to its nearest codeword $i^*$ and replaced by $\hat c^{A\to B}_{i^*}$. Only $K$ different outputs are possible. Pitch and energy are mapped the same way.

**Valbret et al. (1992)**: LP analysis (pitch-synchronous), vector-quantise the source envelopes, and build one **dynamic frequency warp (DFW)** per cluster: the median warp over the frame pairs of that cluster. DFW is DTW along the frequency axis. It moves the formants to the target positions. Prosody is changed with LP-PSOLA.

> [!warning] Watch out
> A warp only moves energy along the frequency axis. It keeps the spectral detail (good quality) but cannot change the heights of the formants (poor similarity).

## 4. GMM-based conversion (slides 11-13)
![[cs568_lecture_20_l20_p011.jpg]]
![[cs568_lecture_20_l20_p012.jpg]]
![[cs568_lecture_20_l20_p013.jpg]]

**Notes.** Stylianou (1998). Fit a GMM with $m$ components to the source vectors (dimension $p$):

$$p(x)=\sum_{i=1}^{m}\alpha_i\,\mathcal N(x;\mu_i,\Sigma_i),\qquad\sum_i\alpha_i=1$$

One Gaussian, $x$ and $y$ jointly Gaussian: the minimum mean-squared error (MMSE) estimate is a linear regression,

$$E[y\mid x=x_t]=\nu+\Gamma\Sigma^{-1}(x_t-\mu)$$

with $\mu=E[x]$, $\Sigma$ the covariance of $x$, $\nu=E[y]$ and $\Gamma=E\big[(y-\nu)(x-\mu)^{\mathsf T}\big]$ the cross-covariance of target and source.

Many Gaussians: one local regression per Gaussian, blended by the posterior $P(C_i\mid x_t)$:

$$\mathcal F(x_t)=\sum_{i=1}^{m}P(C_i\mid x_t)\Big[\nu_i+\Gamma_i\Sigma_i^{-1}(x_t-\mu_i)\Big],\qquad P(C_i\mid x_t)=\frac{\alpha_i\,\mathcal N(x_t;\mu_i,\Sigma_i)}{\sum_j\alpha_j\,\mathcal N(x_t;\mu_j,\Sigma_j)}$$

The unknowns $\nu_i,\Gamma_i$ minimise $\epsilon=\sum_{t=1}^{n}\lVert y_t-\mathcal F(x_t)\rVert^2$.

Special cases: **full** (full covariances), **diagonal** ($\Gamma_i,\Sigma_i$ diagonal, fine for cepstra, fewer parameters and decoupled equations), **VQ-type** (drop the correction term: $\mathcal F(x)=\sum_iP(C_i\mid x_t)\,\nu_i$, a weighted codebook mapping, only blends of the $\nu_i$).

> [!warning] Slide errors
> - Slide 12 writes $\Gamma=E[(y-\nu)(y-\nu)^{\mathsf T}]$, the target's own covariance. It must be the cross-covariance $E[(y-\nu)(x-\mu)^{\mathsf T}]$. If $y$ were independent of $x$, the estimate should be just $\nu$.
> - Slide 12 prints the error as $\sum(y_t-\mathcal F(x_t))$. It is the squared norm.

> [!example] Scalar conversions
> One Gaussian: $\mu=2$, $\Sigma=4$, $\nu=5$, $\Gamma=2$, $x_t=4$. Slope $2/4=0.5$, so $E[y\mid x]=5+0.5\cdot2=6$.
> Two Gaussians: $\alpha_i=0.5$, $\mu=0,4$, $\Sigma_i=1$, $\nu=1,10$, slopes $0.5$. At $x_t=2$ both posteriors are $0.5$. Local regressions: $1+0.5\cdot2=2$ and $10+0.5\cdot(-2)=9$. $\mathcal F(2)=5.5$.

## 5. Solving for the parameters (slides 14-15)
![[cs568_lecture_20_l20_p014.jpg]]
![[cs568_lecture_20_l20_p015.jpg]]

**Notes.** Stack all frames. $Y$ is $n\times p$ (target vectors as rows). $P$ is $n\times m$ with entries $p_t(i)=P(C_i\mid x_t)$. $\Delta$ is $n\times pm$ with blocks $p_t(i)\,(x_t-\mu_i)^{\mathsf T}\Sigma_i^{-1}$. Then

$$Y=P\,\boldsymbol\nu+\Delta\,\boldsymbol\Gamma$$

is an ordinary least-squares problem with the normal equations

$$\begin{bmatrix}P^{\mathsf T}P & P^{\mathsf T}\Delta\\ \Delta^{\mathsf T}P & \Delta^{\mathsf T}\Delta\end{bmatrix}\begin{bmatrix}\boldsymbol\nu\\ \boldsymbol\Gamma\end{bmatrix}=\begin{bmatrix}P^{\mathsf T}Y\\ \Delta^{\mathsf T}Y\end{bmatrix}$$

solved by Cholesky decomposition (the left matrix is symmetric positive definite). It is expensive: with $p=20$, $m=128$, $n=20{,}000$, the block $\Delta^{\mathsf T}\Delta$ alone needs about $65\times10^9$ multiplications. Diagonal conversion splits it into $p$ small systems.

## 6. Quality against similarity, weighted frequency warping (slides 16-17)
![[cs568_lecture_20_l20_p016.jpg]]
![[cs568_lecture_20_l20_p017.jpg]]

**Notes.**

| Method | Good | Bad |
|---|---|---|
| VQ | simple, impressive for its simplicity | not robust, outputs limited to blends of codewords |
| GMM | converted voice is similar to the target | low quality: over-smoothing, broad formants |
| DFW | good quality | poor similarity, formant amplitudes unchanged |

GMM conversion is a conditional mean. Averaging many target spectra flattens peaks, so the speech sounds muffled.

**Weighted frequency warping** (Erro and Moreno, 2007) combines both: build a GMM on parallel data, compute one DFW per Gaussian from formant positions (from LSFs), and for each test frame $x$ use the average of the DFWs weighted by $p_i(x)$. Because DFW does not change energy, the energy is corrected in five bands (100-300, 300-800, 800-2500, 2500-3500, 3500-5000 Hz).

## 7. Conversion without a parallel corpus (slides 18-19)
![[cs568_lecture_20_l20_p018.jpg]]
![[cs568_lecture_20_l20_p019.jpg]]

**Notes.** Mouchtaris et al. (2006). Source C and target D have no parallel recordings, but another pair A and B does.

1. Train a GMM conversion model for A to B.
2. Estimate constrained adaptation transforms from A to C ($\mathrm A_j, b_j$) and from B to D ($\mathrm C_\rho, d_\rho$). They fit distributions, so no shared sentences are needed.
3. Push every Gaussian of the A to B model through the transforms:

$$\mu_i^{x'}=\mathrm A_j\mu_i^{x}+b_j,\quad \mu_i^{y'}=\mathrm C_\rho\mu_i^{y}+d_\rho,\quad \Sigma_i^{x'x'}=\mathrm A_j\Sigma_i^{xx}\mathrm A_j^{\mathsf T},\quad \Sigma_i^{y'x'}=\mathrm C_\rho\Sigma_i^{yx}\mathrm A_j^{\mathsf T}$$

> [!warning] Slide detail
> The figure on slide 19 is labelled "[Abe et al., 1988]". It shows the adaptation scheme of Mouchtaris et al. (2006). Also: the method still needs one parallel corpus, from a different speaker pair.

## 8. Foreign accent conversion (slides 20-22)
![[cs568_lecture_20_l20_p020.jpg]]
![[cs568_lecture_20_l20_p021.jpg]]
![[cs568_lecture_20_l20_p022.jpg]]

**Notes.** A foreign accent is a deviation from the acoustic norms (formants) and prosodic norms (intonation, duration, rate) of a language. Modulation theory (Traunmüller, 1994): speech is a voice-quality **carrier** (the organic part of a voice) modulated by **linguistic gestures**. To remove the accent, keep the learner's carrier and take the gestures of a native speaker.

- **Prosodic conversion**: time-scale each phoneme by the duration ratio. Replace the learner's $F_0$ contour by the native contour shifted into the learner's pitch range.
- **Segmental conversion**: combine the learner's glottal excitation with the native spectral envelope (FD-PSOLA). First apply vocal tract length normalisation (VTLN) to the native envelope to reduce the native speaker's identity cues. The VTLN warp is piecewise linear through the average formant pairs of the two speakers.

> [!example] VTLN warp and time scale
> Formant pairs (500, 550), (1500, 1650), (2500, 2700) Hz, starting at (0, 0). $f=1000$ Hz is halfway between the knots 500 and 1500, so it goes to $550+0.5\cdot1100=1100$ Hz.
> A phoneme of 120 ms (learner) and 90 ms (teacher) needs the factor target/source $=0.75$. The slide does not fix the order of the ratio.

## 9. Evaluation and results (slides 23-26)
![[cs568_lecture_20_l20_p023.jpg]]
![[cs568_lecture_20_l20_p024.jpg]]
![[cs568_lecture_20_l20_p025.jpg]]
![[cs568_lecture_20_l20_p026.jpg]]

**Notes.** Three listening tests: acoustic quality (MOS), degree of accent (7-point scale, 0 not at all to 6 extremely), and identity (pairs: same speaker or not, plus confidence 1 to 7). The identity answers are unfolded to 15 points: "same" with confidence $c$ gives $7-c$, "different" gives $7+c$. So 0 is surely the same, 14 surely different.

Experiment (Felps and Gutierrez-Osuna, 2009): 191 native listeners, two CMU ARCTIC speakers (Indian male as learner, US male as teacher), five conditions, 20 sentences each.

Results (slide 25): accent about 4.8 for the learner and for prosodic conversion alone, about 2.0 with segmental conversion, about 1.8 with both. Quality falls from about 4.0 (learner) to 2.7-3.0 for all conversions (teacher about 4.85). Identity (slide 26, ISOMAP): with normal speech the conversions stay near the learner. With reversed speech the segmental conversions move away from the learner, so some teacher identity remains in the envelope.

> [!warning] Slide detail
> Slide 26 repeats the title "perceived accent and quality". It shows the speaker identity results.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Parallel corpus | both speakers read the same sentences, aligned with DTW |
| Codebook mapping | $\hat c^{A\to B}_i=\sum_jh_{ij}c^B_j/\sum_jh_{ij}$ |
| DFW | DTW along frequency, moves formants, keeps their heights |
| MMSE regression | $E[y\mid x]=\nu+\Gamma\Sigma^{-1}(x-\mu)$, $\Gamma$ = cross-covariance |
| GMM conversion | $\mathcal F(x)=\sum_iP(C_i\mid x)[\nu_i+\Gamma_i\Sigma_i^{-1}(x-\mu_i)]$ |
| VQ-type conversion | $\mathcal F(x)=\sum_iP(C_i\mid x)\nu_i$ |
| Training | least squares on $Y=P\boldsymbol\nu+\Delta\boldsymbol\Gamma$, normal equations, Cholesky |
| Weighted frequency warping | GMM-weighted DFW plus band-energy correction |
| Non-parallel adaptation | $\mathrm A\mu+b$, $\mathrm A\Sigma\mathrm A^{\mathsf T}$, $\mathrm C\Sigma^{yx}\mathrm A^{\mathsf T}$ |
| Accent conversion | learner's excitation + VTLN-warped native envelope + native prosody |

## Check yourself
> [!question]- 1. A system makes a voice sound "older" without any target speaker. Voice conversion or voice transformation?
> Voice transformation. Voice conversion needs a target speaker by definition.

> [!question]- 2. $c^B_1=(1,0)$, $c^B_2=(0,2)$, histogram row $h_{21}=1$, $h_{22}=1$. Find $\hat c^{A\to B}_2$.
> $\big(1\cdot(1,0)+1\cdot(0,2)\big)/2=(0.5,\ 1)$.

> [!question]- 3. The target's second formant is stronger than the source's. Can a pure DFW reproduce this?
> No. $S(\phi(f))$ only moves values that the source spectrum already has. Peak heights are copied, never raised. This is why Erro and Moreno add a band-energy correction.

> [!question]- 4. One Gaussian with $\mu=2$, $\Sigma=4$, $\nu=5$, $\Gamma=2$. Convert $x_t=0$.
> $5+0.5\cdot(0-2)=4$.

> [!question]- 5. Why can the transform from A to C be estimated without shared sentences?
> It moves A's GMM so that it fits C's frames (maximum likelihood). That needs only a set of C frames, never a pairing of frames.

> [!question]- 6. Which result shows that prosody alone does not remove the accent?
> Condition 2 (prosodic only) has an accent rating of about 4.8, the same as the unconverted learner (condition 1). Segmental conversion drops it to about 2.0.

## Links
- Course index: [[CS568 Home]]
- Prosody methods used here (PSOLA): [[CS568 L19 - Prosodic modification of speech]]
- Quizzes and labs that use this lecture: none (the midterm booklet covers Lectures 1 to 6).
- Full step-by-step notes (PDF): [CS568_Lecture_20_Voice_Conversion.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_20/CS568_Lecture_20_Voice_Conversion.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_20/CS568_Lecture_20_Voice_Conversion.pdf`
- Original slides (PDF): [l20.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_20/l20.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_20/l20.pdf`

Slides not embedded: 1
