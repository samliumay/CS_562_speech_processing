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

> [!info] Source, filter and what is converted (beyond the slides)
> Speech is modelled as a source (the glottal excitation, which carries pitch) that passes through a filter (the vocal tract, seen as the spectral envelope with its formants). Both carry speaker identity. Voice conversion (ses dönüştürme) in this lecture maps the spectral envelope of the source speaker (kaynak konuşmacı) to that of the target speaker (hedef konuşmacı), frame by frame. The words stay the same. Pitch and timing are changed with the prosodic methods of Lecture 19.

> [!question]- Check (slide 2): Find the two mistakes: "Voice transformation changes the linguistic content of speech. This lecture changes the source, one global transform for the whole utterance."
> 1. It controls non-linguistic information (voice quality, voice individuality). The words are kept.
> 2. This lecture changes the vocal-tract filter, not the source, and it works at the segmental level (segment by segment). Source transformations are mostly prosodic and belong to the previous lecture.
> A global filter transform such as VTLN exists, but the slide says its conversions are limited. A likely reason (not on the slide): one warp for all sounds cannot fit each sound of the target.

> [!question]- Check (slide 3): Name the term for each system. (a) It mixes the voices of two actors into one new voice. (b) It makes Turkish-accented English sound native, still in the same person's voice. (c) It chains ASR, MT and TTS.
> (a) Voice morphing: two source voices are blended into a third.
> (b) Accent conversion: the accent changes, the speaker stays.
> (c) Cross-language conversion: the utterance is translated into another language.
> None of them is voice conversion in the strict sense, because none maps one speaker's filter to a given target speaker's filter.

> [!question]- Check (slide 4): The slide gives 10 minutes of data for VC and 10 hours for a new TTS voice. What is the ratio? Why can VC plausibly live with so little data?
> $10\text{ h}=600$ min, so the ratio is $600/10=60$.
> The slide gives no reason. A plausible one (beyond the slides): a TTS voice built from scratch must cover all the sounds and contexts of the language. VC only has to learn a mapping between the spectral features of two speakers. The existing TTS system (the source voice) already produces the words, VC only changes who seems to speak.

> [!question]- Check (slide 5): The slide sorts methods along two axes. Place Abe et al. (1988) and Mouchtaris et al. (2006) on both axes.
> Axis 1 is the mapping (VQ, GMM, DFW, DFW combined with GMM or VQ). Axis 2 is the corpus (parallel or non-parallel).
> Abe et al.: VQ mapping, parallel recordings.
> Mouchtaris et al.: GMM mapping, non-parallel recordings for the source and target pair.
> The axes are independent: the first says how a source vector is turned into a target vector, the second says what data the training needs.

## 2. Building blocks (slide 6)
![[cs568_lecture_20_l20_p006.jpg]]

**Notes.** Training: extract features from source and target, align the two sequences (DTW for a parallel corpus), train the mapping on aligned pairs $(x_t,y_t)$. Here $x_t$ is the source vector of frame $t$ and $y_t$ the target vector aligned with it. Recall: extract features from a new source utterance, apply the mapping, synthesise.

DTW works because in a parallel corpus both speakers say the same sounds, only at different speeds.

> [!info] Why alignment is needed (beyond the slides)
> The two speakers say the same sentence, but not with the same timing. One recording may have 180 frames and the other 210, so frame 50 of the source is not the same sound as frame 50 of the target. Time alignment (zaman hizalama) with DTW finds a monotonic path that pairs each source frame with the target frame of the same sound. Every pair on the path is one training sample $(x_t,y_t)$. With wrong pairs the mapping would learn to turn one sound into another sound.

> [!question]- Check (slide 6): In the block diagram, which blocks are used only in training, which only in recall, and which in both? What does the arrow from "Training" down to "Mapping" carry?
> Both: feature extraction. Only training: alignment and training. Only recall: mapping and synthesis.
> The arrow carries the learned parameters of the mapping (a mapping codebook, a set of warps, or the GMM with its $\nu_i,\Gamma_i$).
> Recall has no alignment block because only the source test utterance exists there. There is no target utterance to align with.

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

> [!info] How a frequency warp acts (beyond the slides)
> A warp is a function $\phi(f)$ on the frequency axis. The converted envelope is $S(\phi(f))$: its value at frequency $f$ is read from the source envelope $S$ at frequency $\phi(f)$. The function is monotonic, so the order of the formants is kept. DFW finds $\phi$ with dynamic programming so that $S(\phi(f))$ is as close as possible to the target envelope $T(f)$. Direction: a source formant at $\phi(f)$ appears at $f$ in the output. So where $\phi(f)<f$ the formants move up, and where $\phi(f)>f$ they move down. Each aligned frame pair gives one warp. Valbret et al. keep one warp per source cluster, the median of the warps of the pairs in that cluster. The slide does not say why the median is used. A likely reason: a median is not pulled away by a few badly aligned pairs.

> [!question]- Check (slide 7): Codeword $i$ of speaker A met three codewords of B with counts $h_{i1}=6$, $h_{i2}=3$, $h_{i3}=1$. $c^B_1=(2,0)$, $c^B_2=(4,2)$, $c^B_3=(0,10)$. Find the mapped codeword. Then say what output two different source frames get if both are quantised to codeword $i$.
> $$\hat c^{A\to B}_i=\frac{6\,(2,0)+3\,(4,2)+1\,(0,10)}{6+3+1}=\frac{(12+12+0,\ 0+6+10)}{10}=(2.4,\ 1.6)$$
> Both frames get exactly the same output $(2.4,1.6)$. The difference between them is lost.
> This is why the mapping is called hard: the output jumps when the source frame crosses the border between two codewords, and only $K$ outputs exist.

> [!question]- Check (slide 8): In Fig. 2 of the slide, two paths leave "LPC analysis". What does each path carry, and what does each "decode" block do?
> Upper path: the spectrum parameters. They are vector-quantised with the codebook of speaker A, which gives a codeword index.
> Lower path: the pitch frequency. It is one number per frame, so it is scalar-quantised with A's pitch codebook.
> Each "decode" block looks the index up in the A→B mapping codebook (one for the spectrum, one for the pitch) and outputs the mapped value of speaker B.
> Both decoded streams drive the synthesis filter, which gives the converted speech. Fig. 1 is the training part: it builds the mapping codebook from the histogram.

> [!question]- Check (slide 9): Both Abe et al. and Valbret et al. vector-quantise the source frames. What does each method store per source cluster, and what follows for the detail of the converted spectrum?
> Abe et al. store one target vector per cluster (the mapped codeword). The source frame is replaced by it, so the detail of the frame is thrown away.
> Valbret et al. store one frequency warp per cluster. The warp is applied to the real envelope of the frame, so the detail of that frame is kept and only the formant positions move.
> Result: Valbret gives better quality, but the formant heights stay those of the source.

> [!question]- Check (slide 10): The third panel of the figure ("Align recordings") plots a rising curve with "Reference" on the horizontal axis and "Target" on the vertical axis. What is this curve? What would it be if both speakers had exactly the same timing? Why does it never go down?
> It is the DTW alignment path: for each frame of the reference recording it gives the matching frame of the target recording.
> With the same timing it would be a straight diagonal line. The bends show where one speaker is slower or faster.
> It never goes down because the sounds come in the same order in both recordings (the path is monotonic).
> The next panels use the path: "Associate pairs" links the matched frames, and the last panel groups the source frames into clusters and builds one DFW per cluster.

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

> [!info] Hard against soft mapping (beyond the slides)
> Codebook mapping gives each frame to exactly one class and outputs one fixed vector. The GMM gives each frame to every class with a weight $P(C_i\mid x_t)$, and each class has its own straight line $\nu_i+\Gamma_i\Sigma_i^{-1}(x_t-\mu_i)$. Near the centre of one Gaussian its weight is close to 1 and its line decides. Between two Gaussians the weights change smoothly, so the output moves smoothly from one line to the other. There are no jumps, and the output is not limited to a finite set of vectors.

> [!question]- Check (slide 11): Say what each symbol in $p(x)=\sum_{i=1}^{m}\alpha_i N(x;\mu_i,\Sigma_i)$ is. For $p=20$ MFCCs, what are the sizes of $\mu_i$ and $\Sigma_i$? Which speaker's data is this GMM fitted to?
> $m$: number of Gaussian components (acoustic classes). $\alpha_i$: weight (prior probability) of class $i$, with $\sum_i\alpha_i=1$ so that $p(x)$ is a valid density. $N(x;\mu_i,\Sigma_i)$: the Gaussian density of class $i$. $\mu_i$: mean vector of the class. $\Sigma_i$: its covariance matrix. $p$: dimension of the vectors.
> For $p=20$: $\mu_i$ has 20 entries and $\Sigma_i$ is $20\times20$.
> The GMM is fitted to the source vectors $x_t$ only. The target enters later, through $\nu_i$ and $\Gamma_i$.

> [!question]- Check (slide 12, one Gaussian): $\mu=10$, $\Sigma=5$, $\nu=-2$, $\Gamma=3$ (cross-covariance, scalar case). Write the conversion as a line $y=ax+b$ and convert $x_t=15$. What is the output for every $x_t$ if $\Gamma=0$?
> $E[y\mid x]=\nu+\Gamma\Sigma^{-1}(x-\mu)$, so $a=\Gamma/\Sigma=3/5=0.6$ and $b=\nu-a\mu=-2-0.6\cdot10=-8$.
> $y=0.6\cdot15-8=1$. Same result from the first form: $-2+0.6\cdot(15-10)=1$.
> If $\Gamma=0$ the slope is 0 and the output is always $\nu=-2$, the target mean. Source and target are then uncorrelated, so $x_t$ tells nothing about $y$.

> [!question]- Check (slide 12, mixture): Two Gaussians in one dimension: $\alpha_1=\alpha_2=0.5$, $\mu_1=0$, $\mu_2=2$, $\Sigma_1=\Sigma_2=1$, $\nu_1=3$, $\nu_2=8$, $\Gamma_1=0.5$, $\Gamma_2=2$. Convert $x_t=0.5$.
> Posteriors. The weights and the variances are equal, so only the exponentials count: $e^{-(0.5-0)^2/2}=e^{-0.125}=0.8825$ and $e^{-(0.5-2)^2/2}=e^{-1.125}=0.3247$.
> $P(C_1\mid x_t)=0.8825/(0.8825+0.3247)=0.731$, $P(C_2\mid x_t)=0.269$.
> Local regressions: $3+0.5\cdot(0.5-0)=3.25$ and $8+2\cdot(0.5-2)=5$.
> $\mathcal F(0.5)=0.731\cdot3.25+0.269\cdot5=3.72$.
> The frame is closer to Gaussian 1, so the result is closer to the first local regression.

> [!question]- Check (slide 13): Use the mixture of the question before ($P(C_1\mid x_t)=0.731$, $P(C_2\mid x_t)=0.269$, $\nu_1=3$, $\nu_2=8$) and compute the VQ-type conversion. Compare it with the codebook mapping of Abe et al.: what is the same and what is different?
> $\mathcal F(0.5)=0.731\cdot3+0.269\cdot8=4.34$ (the full conversion gave 3.72).
> Same: the output is built only from a fixed set of target vectors, here $\nu_1,\nu_2$. Whatever $x_t$ is, the output lies between 3 and 8.
> Different: the weights are soft posteriors, so the output changes smoothly with $x_t$. Abe's mapping picks one codeword and jumps at the cell borders.
> What is missing against the full conversion is the correction term $\Gamma_i\Sigma_i^{-1}(x_t-\mu_i)$, which moves the output with the position of $x_t$ inside the class.

## 5. Solving for the parameters (slides 14-15)
![[cs568_lecture_20_l20_p014.jpg]]
![[cs568_lecture_20_l20_p015.jpg]]

**Notes.** Stack all frames. $Y$ is $n\times p$ (target vectors as rows). $P$ is $n\times m$ with entries $p_t(i)=P(C_i\mid x_t)$. $\Delta$ is $n\times pm$ with blocks $p_t(i)\,(x_t-\mu_i)^{\mathsf T}\Sigma_i^{-1}$. Then

$$Y=P\,\boldsymbol\nu+\Delta\,\boldsymbol\Gamma$$

is an ordinary least-squares problem with the normal equations

$$\begin{bmatrix}P^{\mathsf T}P & P^{\mathsf T}\Delta\\ \Delta^{\mathsf T}P & \Delta^{\mathsf T}\Delta\end{bmatrix}\begin{bmatrix}\boldsymbol\nu\\ \boldsymbol\Gamma\end{bmatrix}=\begin{bmatrix}P^{\mathsf T}Y\\ \Delta^{\mathsf T}Y\end{bmatrix}$$

solved by Cholesky decomposition (the left matrix is symmetric positive definite). It is expensive: with $p=20$, $m=128$, $n=20{,}000$, the block $\Delta^{\mathsf T}\Delta$ alone needs about $65\times10^9$ multiplications. Diagonal conversion splits it into $p$ small systems.

> [!info] Where the normal equations come from (beyond the slides)
> Put the two known matrices side by side, $A=[P\ \ \Delta]$, and stack the unknowns, $\Theta=[\boldsymbol\nu;\boldsymbol\Gamma]$. Row $t$ of $A\Theta$ is exactly $\mathcal F(x_t)^{\mathsf T}$, so the total squared error is $\epsilon=\lVert Y-A\Theta\rVert^2$. This is a least-squares (en küçük kareler) problem that is linear in $\Theta$, because the posteriors in $P$ and $\Delta$ are already fixed by the source GMM. Setting the gradient to zero gives $A^{\mathsf T}A\,\Theta=A^{\mathsf T}Y$. Written in blocks, this is the matrix equation of slide 15.
> The cost on slide 15 (the slide gives only the numbers, this count is our explanation): $\Delta^{\mathsf T}\Delta$ has $(pm)^2$ entries and each is a sum of $n$ products, so $n(pm)^2$ multiplications. The matrix is symmetric, so half is enough: $20{,}000\cdot2560^2/2=65.5\times10^9$, which the slide writes as $65\times10^9$.

> [!question]- Check (slide 14): Take $p=13$, $m=4$, $n=1000$. Give the sizes of $Y$, $P$, $\Delta$, $\boldsymbol\nu$ and $\boldsymbol\Gamma$, and the number of unknown scalars. What is the sum of each row of $P$?
> $Y$: $n\times p=1000\times13$. $P$: $n\times m=1000\times4$. $\Delta$: $n\times pm=1000\times52$.
> $\boldsymbol\nu$: $m\times p=4\times13$. $\boldsymbol\Gamma$: $(m\times p)\times p=52\times13$.
> Unknowns: $(m+pm)\cdot p=(4+52)\cdot13=728$.
> Each row of $P$ holds the posteriors $P(C_i\mid x_t)$ of one frame over all classes, so it sums to 1.

> [!question]- Check (slide 15): Why can the system be solved with the Cholesky decomposition? By what factor does the cost of the block $\Delta^{\mathsf T}\Delta$ grow if the number of frames doubles, and if the number of Gaussians doubles?
> Cholesky needs a symmetric positive definite matrix. The left matrix is $A^{\mathsf T}A$ with $A=[P\ \ \Delta]$, which is symmetric and (with enough frames) positive definite.
> The cost of $\Delta^{\mathsf T}\Delta$ is proportional to $n(pm)^2$. Double $n$: factor 2. Double $m$: factor $2^2=4$.
> So more Gaussians are much more expensive than more data. On the slide this block ($65\times10^9$) costs far more than solving the system ($3\times10^9$).

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

> [!question]- Check (slide 16): Find the mistakes: "DFW gives low quality because it averages many spectra. GMM conversion gives poor similarity because it cannot change formant amplitudes."
> The two reasons are swapped.
> GMM conversion is the one that averages (a posterior-weighted conditional mean). Averaging makes the formant peaks lower and broader (over-smoothing), so the quality is low. Its similarity to the target is good.
> DFW only moves the source envelope along the frequency axis. The detail is kept, so the quality is good. The formant amplitudes stay those of the source, so the similarity is poor.

> [!question]- Check (slide 17): A test frame has $p_1(x)=0.7$ and $p_2(x)=0.3$. The DFW of cluster 1 sends 1000 Hz to 1100 Hz, the DFW of cluster 2 sends it to 1300 Hz. Where does weighted frequency warping send 1000 Hz? Why does this method not over-smooth like GMM conversion, and why is a second step still needed?
> $0.7\cdot1100+0.3\cdot1300=770+390=1160$ Hz.
> The weighted average is taken over warping functions, not over spectra. The averaged warp is then applied to the frame's own envelope, so its peaks stay sharp.
> A warp cannot change formant energy. So the energy is corrected with a multiplicative term in each of the five bands. The moved formant at 1160 Hz lies in the 800-2500 Hz band.

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

> [!info] Why the covariances get these forms (beyond the slides)
> If a random vector $x$ has mean $\mu$ and covariance $\Sigma$, then $x'=\mathrm Ax+b$ has mean $\mathrm A\mu+b$ and covariance $\mathrm A\Sigma\mathrm A^{\mathsf T}$. The shift $b$ does not change a covariance. For the cross-covariance of $y'=\mathrm Cy+d$ and $x'=\mathrm Ax+b$ the same rule gives $\mathrm C\Sigma^{yx}\mathrm A^{\mathsf T}$: one matrix from each side. The index $j$ (and $\rho$) says that there can be several transforms, one per group of Gaussians.

> [!question]- Check (slide 18): The method has three steps: (1) build a GMM conversion model, (2) build the adaptation models, (3) adapt the conversion model. Which speakers' data does each step use, and which step needs parallel recordings?
> Step 1 uses speakers A and B and needs their parallel recordings (aligned pairs $(x_t,y_t)$).
> Step 2 uses A with C, and B with D. It needs only speech of C and of D, with any sentences, because it fits distributions and not frame pairs.
> Step 3 uses no speech data. It only transforms the parameters of the model of step 1.
> So C and D never have to say the same sentences.

> [!question]- Check (slide 19): One Gaussian in one dimension of the A to B model has $\mu^x=2$, $\mu^y=5$, $\Sigma^{xx}=4$, $\Sigma^{yx}=3$. The adaptation gives $\mathrm A=1.5$, $b=1$ (A to C) and $\mathrm C=0.5$, $d=2$ (B to D). Find the four adapted parameters and convert the frame $x'=7$ of speaker C.
> $\mu^{x'}=1.5\cdot2+1=4$ and $\mu^{y'}=0.5\cdot5+2=4.5$.
> $\Sigma^{x'x'}=1.5\cdot4\cdot1.5=9$ and $\Sigma^{y'x'}=0.5\cdot3\cdot1.5=2.25$.
> Slope: $\Sigma^{y'x'}/\Sigma^{x'x'}=2.25/9=0.25$ (it was $3/4=0.75$ for A to B).
> Conversion: $y'=4.5+0.25\cdot(7-4)=5.25$.
> In the figure these are the right-hand expressions. The horizontal arrows are the two adaptations, the vertical arrows are the spectral conversions.

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

> [!info] Naming, and shifting a pitch contour (beyond the slides)
> On slide 21 "source" is the learner (foreign-accented, L2 speaker) and "target" is the native teacher. Slide 22 draws the same scheme with the labels "foreign-accented" and "native-accented". The output must keep the learner's voice. So the roles are the opposite of voice conversion: the identity comes from the source, the gestures from the target.
> "Shifted" $F_0$ contour: the teacher's contour keeps its shape (rises and falls) but is moved so that its mean is the learner's mean. A shift in Hz adds a constant. A shift in $\log F_0$ multiplies all values by a constant, so the ratios between the values are kept. This fits hearing better, because pitch is heard on a logarithmic scale. The slide does not say which of the two is used.

> [!question]- Check (slide 20): According to modulation theory speech is a carrier modulated by linguistic gestures. Which of the two is kept from the learner in accent conversion, and which is replaced? Which is replaced in voice conversion?
> The carrier holds the organic aspects of a voice (set by the anatomy), so it carries who speaks. The gestures carry what is said and how it is pronounced, so also the accent.
> Accent conversion keeps the learner's carrier and takes the gestures of a native speaker. The result is the learner's voice with a native accent.
> Voice conversion is the opposite: it keeps the source speaker's gestures (words and accent) and changes the carrier to that of the target speaker.

> [!question]- Check (slide 21): The teacher's $F_0$ contour over three frames is 180, 220, 200 Hz. The learner's mean $F_0$ is 120 Hz. Build the shifted contour (shift in Hz). Why is the teacher's contour not used directly, and why is VTLN applied to the teacher's envelope?
> Teacher mean: $(180+220+200)/3=200$ Hz. Shift: $120-200=-80$ Hz.
> Shifted contour: $100,\ 140,\ 120$ Hz. The rise and fall of the teacher are kept, the mean is now the learner's 120 Hz.
> Used directly, the contour would be in the teacher's pitch range, which is a cue for the teacher's identity.
> VTLN has the same purpose for the filter: the teacher's envelope also holds identity cues (formant positions set by the vocal-tract length). The warp moves them towards the learner's average formants.

> [!question]- Check (slide 22): In the diagram, which speaker gives the glottal excitation and which gives the spectral envelope at the ⊗ node? What does the dashed arrow "Vocal-tract length" between the two "Spectral envelope" boxes stand for, and what would change without it?
> The excitation comes from the foreign-accented speech (upper path): the upper box takes the envelope away and the excitation is left. The spectral envelope comes from the native-accented speech (lower path).
> The ⊗ node puts them together as source and filter (with FD-PSOLA) and gives the accent-converted speech.
> The dashed arrow is VTLN: information about the foreign speaker's vocal-tract length (the average formants) is used to warp the native envelope before it is combined.
> Without it the native envelope would be used as it is, and the output would sound more like the native speaker.

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

> [!warning] Slide 26 against the sentence above
> The plots do not show that the segmental conversions "move away from the learner" only with reversed speech. In the left plot (normal speech) the segmental and combined points already form their own group, with a clear gap to the learner and prosodic points. In the right plot (reversed speech) the two groups touch and partly overlap. In both plots all four are far from the teacher.

> [!info] Reading the ISOMAP plots (beyond the slides)
> The paired test gives, for two stimuli, a score that says how different the speakers sound (0 to 14). ISOMAP is a multidimensional scaling method: it places the stimuli as points in a plane so that the distances between points are close to these perceived differences. The axes ISOMAP$_1$ and ISOMAP$_2$ have no physical unit. Only distances count: points close together were judged as the same speaker.
> Reversed speech (played backwards) cannot be understood, so the listener can no longer use the words and the accent. What is left is mostly voice quality. The right panel therefore tests identity without the help of the accent.

> [!question]- Check (slide 23): One listener answers "same speaker" with confidence 6, another "different speaker" with confidence 2 (7 = very confident). Unfold both answers. Then say what each of the three tests on the slide measures.
> "Same", $c=6$: $7-6=1$. "Different", $c=2$: $7+2=9$. On the 15-point score, 1 is almost surely the same speaker and 9 is weakly "different" (7 is the neutral middle).
> MOS: acoustic quality of the signal (how natural and clean it sounds). Accent rating (0 to 6): how strong the foreign accent is. Identity rating: whether two utterances seem to come from the same speaker.
> The three are separate: a conversion can remove the accent and still lose quality or change the identity.

> [!question]- Check (slide 24): How does the slide get "100 unique utterances"? Why are the unconverted learner and the teacher included as conditions, and why are the same 20 sentences used in all conditions?
> $5\text{ conditions}\times20\text{ sentences}=100$ utterances.
> Learner and teacher are the two reference points: the learner shows the starting accent, quality and identity, the teacher shows the native end. The three conversions (prosodic, segmental, both) are read between them.
> With the same sentences in every condition, a difference in the ratings comes from the processing and not from the text.

> [!question]- Check (slide 25): Read the right plot (perceived quality). Which condition has the lowest quality? Compare conditions 1 and 4 in both plots: what is gained and what is lost? Why does condition 2 lose quality although its accent does not change?
> Lowest quality: condition 3 (segmental only), about 2.7. Condition 4 is about the same, condition 2 is about 3.0.
> From 1 to 4 the accent falls from about 4.8 to about 1.8 (gain), and the quality falls from about 4.0 to about 2.7 (loss). Accent reduction is paid with signal quality.
> Condition 2 changes durations and pitch with signal processing, which leaves artefacts. So the quality drops by about 1 point even without any change of accent.
> The teacher (condition 5) is near 0.15 in accent and near 4.85 in quality: the unprocessed native reference.

> [!question]- Check (slide 26): In the left plot (normal speech), where is the teacher and where are the other four conditions? Which conditions lie together? What does this say about the speaker identity after accent conversion?
> The teacher is alone on the far right, at ISOMAP$_1$ of about 2.2. All other points are on the left, at ISOMAP$_1$ of about $-0.4$ to $-0.7$.
> On the left there are two groups along ISOMAP$_2$: the learner with the prosodic conversion (about $-0.3$ to $-0.6$), and the segmental and combined conversions (about $-0.1$ to $0.7$). There is a gap between the two groups.
> So every conversion is much closer to the learner than to the teacher: the identity of the learner is mostly kept. The prosodic conversion lies on the learner. The segmental and combined conversions do not, so changing the envelope changes the perceived voice a little.
> The right plot (reversed speech): the teacher is alone on the far left (ISOMAP$_1$ of about $-1.5$ to $-1.7$). The other four are on the right (about 0 to 0.9), again with the segmental and combined points in the upper part and the learner and prosodic points in the lower part. Here the two groups have no gap: they touch and partly overlap near ISOMAP$_2$ of 0 to 0.2.

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
- Quiz: [[CS568 L20 - Voice conversion - quiz]]

Slides not embedded: 1
