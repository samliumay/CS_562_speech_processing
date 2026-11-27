---
course: CS 568 Speech Processing
lesson: CS568 L20 - Voice conversion
type: quiz
questions: 26
---

# CS568 L20 - Voice conversion - quiz

Lesson note: [[CS568 L20 - Voice conversion]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Voice transformation, terms, applications (slides 2-5)

### Q1 (slide 2)

A student writes: "Voice transformation changes the linguistic content of speech. This lecture changes the source (the glottal excitation), with one global transform for the whole utterance." Which statement is correct?

- A) Voice transformation changes the linguistic content (the words). This lecture changes the vocal-tract filter, segment by segment.
- B) Voice transformation controls non-linguistic information. This lecture changes the vocal-tract filter, segment by segment.
- C) Voice transformation controls non-linguistic information. This lecture changes the source, with one global transform.
- D) Voice transformation changes the linguistic content (the words). This lecture changes the source, with one global transform.

> [!success]- Answer
> **B.** Voice transformation controls non-linguistic information: voice quality and voice individuality. The words are kept.
> This lecture changes the vocal-tract filter at the segmental level (segment by segment). Source changes are mostly prosodic and belong to Lecture 19. A global filter transform such as VTLN exists, but the slide says its conversions are limited.
> - A: This fixes the second mistake only. The words do not change.
> - C: This fixes the first mistake only. The lecture works on the filter, segment by segment.
> - D: This is the student's claim with both mistakes.

### Q2 (slide 3)

Name the term for each system. (a) It mixes the voices of two actors into one new voice. (b) It makes Turkish-accented English sound native, still in the same person's voice.

- A) (a) voice conversion, (b) accent conversion
- B) (a) voice morphing, (b) voice conversion
- C) (a) voice morphing, (b) accent conversion
- D) (a) voice conversion, (b) voice conversion

> [!success]- Answer
> **C.** (a) Voice morphing blends two voices into a third one. (b) Accent conversion changes the accent and keeps the speaker.
> Voice conversion in the strict sense maps one speaker's filter to the filter of one given target speaker. Neither system does that.
> - A: In (a) there is no single target speaker. The result is a new voice, so it is morphing.
> - B: In (b) the speaker must stay the same. Voice conversion would change who seems to speak.
> - D: This treats every change of a voice as voice conversion. Neither (a) nor (b) has a target speaker whose voice is copied.

### Q3 (slide 4)

The slide says that voice conversion (VC) needs about 10 minutes of speech of the target speaker, while a new text-to-speech (TTS) voice needs about 10 hours, 60 times more. The slide gives no reason. Which reason is the most plausible?

- A) VC only learns a mapping between the spectral features of two speakers. An existing voice says the words.
- B) VC models only the pitch of the target voice, and a pitch range can be estimated from a few sentences.
- C) VC needs no time alignment, so it can use every recorded frame, while a TTS voice must discard most of them.
- D) A voice is fully described by its long-term average spectrum, and 10 minutes are enough to estimate it well.

> [!success]- Answer
> **A.** A TTS voice built from scratch must cover all the sounds and contexts of the language. VC starts from an existing voice (the source) that already produces the words. It only learns how the spectral features of one speaker map to those of the other.
> - B: VC in this lecture changes the vocal-tract filter (the spectral envelope), not only the pitch.
> - C: VC does need time alignment. A parallel corpus is aligned frame by frame with DTW.
> - D: One average spectrum cannot describe a voice. The mappings of this lecture are different for each acoustic class (codeword or Gaussian).

### Q4 (slide 5)

Slide 5 sorts voice conversion methods along two axes: the mapping (VQ, GMM, DFW and combinations) and the training corpus (parallel or non-parallel recordings of the source and target speakers). Where do Abe et al. (1988) and Mouchtaris et al. (2006) lie on the corpus axis?

- A) Abe et al.: non-parallel. Mouchtaris et al.: parallel.
- B) Abe et al.: parallel. Mouchtaris et al.: parallel.
- C) Abe et al.: non-parallel. Mouchtaris et al.: non-parallel.
- D) Abe et al.: parallel. Mouchtaris et al.: non-parallel.

> [!success]- Answer
> **D.** Abe et al. align the frames of both speakers with DTW, so the two speakers must say the same sentences (parallel). Mouchtaris et al. convert without parallel recordings of the source and target pair.
> The axes are independent: the mapping says how a source vector becomes a target vector, the corpus says what data training needs.
> - A: This swaps the two methods.
> - B: Mouchtaris et al. use a parallel corpus only from another speaker pair. The source and target pair needs none.
> - C: Abe et al. build their histogram from DTW-aligned frame pairs, which needs the same sentences from both speakers.

## 2. Building blocks (slide 6)

### Q5 (slide 6)

A voice conversion system has five blocks: feature extraction, alignment, training, mapping and synthesis. Which blocks are used in the training phase and which in the recall (conversion) phase?

- A) Feature extraction and alignment: both phases. Training: training only. Mapping and synthesis: recall only.
- B) Feature extraction: both phases. Alignment and training: training only. Mapping and synthesis: recall only.
- C) Feature extraction, alignment and training: training only. Mapping: recall only. Synthesis: both phases.
- D) Alignment and synthesis: both phases. Feature extraction and training: training only. Mapping: recall only.

> [!success]- Answer
> **B.** Both phases need features. Training aligns the source and target frames and learns the mapping from the aligned pairs $(x_t,y_t)$. Recall extracts features from a new source utterance, applies the learned mapping and synthesises speech.
> - A: Recall has only the source utterance. There is no target utterance to align with.
> - C: The mapping works on features, so recall must extract them from the new source utterance too. And training makes no speech: it only learns the parameters of the mapping.
> - D: Recall has no target utterance to align with, and it needs the features of the new source utterance. Training makes no speech.

## 3. Codebook mapping and frequency warping (slides 7-10)

### Q6 (slide 7)

In codebook mapping, each speaker has a VQ codebook. After the frames are aligned, $h_{ij}$ counts how often codeword $i$ of speaker A met codeword $j$ of speaker B. The mapped codeword is

$$\hat c^{A\to B}_i=\frac{\sum_j h_{ij}\,c^B_j}{\sum_j h_{ij}}$$

Codeword $i$ met three codewords of B: $h_{i1}=6$, $h_{i2}=3$, $h_{i3}=1$, with $c^B_1=(2,0)$, $c^B_2=(4,2)$, $c^B_3=(0,10)$. What is $\hat c^{A\to B}_i$?

- A) $(2,\ 4)$
- B) $(24,\ 16)$
- C) $(2.4,\ 1.6)$
- D) $(2,\ 0)$

> [!success]- Answer
> **C.** $\hat c^{A\to B}_i=\dfrac{6\,(2,0)+3\,(4,2)+1\,(0,10)}{6+3+1}=\dfrac{(24,\ 16)}{10}=(2.4,\ 1.6)$.
> Every source frame quantised to codeword $i$ gets exactly this output, so the mapping is hard.
> - A: This is the plain average of the three codewords. The counts $h_{ij}$ were ignored.
> - B: This is the weighted sum without the division by $\sum_j h_{ij}=10$.
> - D: This takes only the most frequent partner $c^B_1$, a hard pick instead of the weighted average.

### Q7 (slide 8)

In the conversion diagram of Abe et al. (1988), LPC analysis of speaker A's speech gives two streams per frame: the spectrum parameters and the pitch frequency. Each stream is quantised with A's codebook for that stream, which gives an index. Then each stream passes a "decode" block before the synthesis filter. What does a decode block do?

- A) It looks the index up in A's own codebook and outputs A's codeword. The synthesis filter then maps it to B.
- B) It aligns the frame with B's codebook by DTW and outputs the codeword of B on the cheapest path.
- C) It looks the index up in the A→B mapping codebook and outputs the mapped value for speaker B.
- D) It weights all codewords of B by their distance to the frame and outputs their weighted average.

> [!success]- Answer
> **C.** The mapping codebook (built in training from the histogram) holds one mapped value of B for every index of A. Decoding is a table lookup: index in, B's value out. One decode block does this for the spectrum, the other for the pitch.
> - A: The synthesis filter only makes the sound. The change from A to B happens in the decode blocks.
> - B: DTW is used only in training, to pair the frames of the two speakers.
> - D: This is a soft, distance-weighted mapping. Abe et al. use one fixed mapped value per index.

### Q8 (slide 9)

Abe et al. (1988) and Valbret et al. (1992) both vector-quantise the source frames into clusters. What does each method store per cluster, and which method keeps the spectral detail of each frame?

- A) Abe et al.: one frequency warp per cluster. Valbret et al.: one target vector per cluster. So only Abe keeps the detail of each frame.
- B) Abe et al.: one target vector per cluster. Valbret et al.: one frequency warp per cluster. So only Valbret keeps the detail of each frame.
- C) Abe et al.: one target vector per cluster. Valbret et al.: one target vector per cluster. So neither method keeps the detail of each frame.
- D) Abe et al.: one frequency warp per cluster. Valbret et al.: one frequency warp per cluster. So both methods keep the detail of each frame.

> [!success]- Answer
> **B.** Abe et al. replace the frame by the mapped codeword, so the detail of the frame is lost. Valbret et al. apply the cluster's warp to the frame's own envelope, so its detail is kept and only the formant positions move.
> Result: Valbret gives better quality, but the formant heights stay those of the source.
> - A: This swaps the two methods.
> - C: Valbret et al. store a dynamic frequency warp (DFW), not a target vector.
> - D: Abe et al. store a mapping codebook of target vectors, not warps.

### Q9 (slide 10)

In the training of Valbret et al. (1992), DTW aligns the parallel recordings of the reference (source) speaker and the target speaker. The slide plots the result as a curve: reference frame index on the horizontal axis, target frame index on the vertical axis. Why does this curve never go down?

- A) Both speakers say the same sounds in the same order, so a later frame never matches an earlier one.
- B) The curve shows the accumulated distance of DTW, which only grows since each step adds a positive cost.
- C) The target speaker always talks more slowly, so every target index is larger than its reference index.
- D) DTW first sorts the frames of both recordings by energy, so the matched frame indices grow together.

> [!success]- Answer
> **A.** The curve is the DTW alignment path. In a parallel corpus both speakers say the same sounds in the same order, only at different speeds. So the path is monotonic.
> With the same timing it would be a straight diagonal. Its bends show where one speaker is faster or slower.
> - B: The axes are frame indices, not a cost. The accumulated cost is a different quantity.
> - C: The bends go both ways. A monotonic path does not need one speaker to be slower everywhere.
> - D: DTW keeps the time order of the frames. Sorting would destroy the order of the sounds.

## 4. GMM-based conversion (slides 11-13)

### Q10 (slide 11)

Stylianou's conversion first fits a Gaussian mixture $p(x)=\sum_{i=1}^{m}\alpha_i\,\mathcal N(x;\mu_i,\Sigma_i)$ with full covariance matrices, on vectors of $p=20$ MFCCs. What are the sizes of $\mu_i$ and $\Sigma_i$, and on whose vectors is this mixture fitted?

- A) $\mu_i$: 20 entries. $\Sigma_i$: $20\times20$. Fitted to the target speaker's vectors only.
- B) $\mu_i$: 20 entries. $\Sigma_i$: 20 entries. Fitted to the source speaker's vectors only.
- C) $\mu_i$: 20 entries. $\Sigma_i$: 20 entries. Fitted to the target speaker's vectors only.
- D) $\mu_i$: 20 entries. $\Sigma_i$: $20\times20$. Fitted to the source speaker's vectors only.

> [!success]- Answer
> **D.** $\mu_i$ is a mean vector of dimension $p=20$. A full covariance matrix is $p\times p=20\times20$.
> The mixture models the source vectors $x_t$. It decides which acoustic class a source frame belongs to. The target enters later, through $\nu_i$ and $\Gamma_i$.
> - A: The target speaker's data are used only to find $\nu_i$ and $\Gamma_i$.
> - B: 20 numbers are only the diagonal (the variances). A full covariance matrix has $20\times20$ entries.
> - C: Both parts are wrong: only the diagonal of $\Sigma_i$, and the wrong speaker.

### Q11 (slide 12, one Gaussian)

Source $x$ and target $y$ are jointly Gaussian (scalar case). The minimum mean-squared error estimate is $E[y\mid x]=\nu+\Gamma\Sigma^{-1}(x-\mu)$, with $\mu=E[x]$, $\Sigma$ the variance of $x$, $\nu=E[y]$ and $\Gamma=E[(y-\nu)(x-\mu)]$ the cross-covariance. Take $\mu=10$, $\Sigma=5$, $\nu=-2$, $\Gamma=3$. Convert $x_t=15$.

- A) $3$
- B) $73$
- C) $1$
- D) $7$

> [!success]- Answer
> **C.** Slope $\Gamma\Sigma^{-1}=3/5=0.6$. Then $E[y\mid x_t]=-2+0.6\cdot(15-10)=-2+3=1$.
> As a line: $y=0.6x-8$. If $\Gamma=0$, the output is always $\nu=-2$.
> - A: This forgets the target mean $\nu$: $0.6\cdot5=3$.
> - B: This multiplies by $\Sigma$ instead of $\Sigma^{-1}$: $-2+3\cdot5\cdot5=73$.
> - D: This uses $x_t$ instead of $x_t-\mu$: $-2+0.6\cdot15=7$.

### Q12 (slide 12, mixture)

GMM conversion: $\mathcal F(x)=\sum_{i}P(C_i\mid x)\big[\nu_i+\Gamma_i\Sigma_i^{-1}(x-\mu_i)\big]$ with the posterior $P(C_i\mid x)=\dfrac{\alpha_i\,\mathcal N(x;\mu_i,\Sigma_i)}{\sum_j\alpha_j\,\mathcal N(x;\mu_j,\Sigma_j)}$. Two Gaussians in one dimension: $\alpha_1=\alpha_2=0.5$, $\mu_1=0$, $\mu_2=2$, $\Sigma_1=\Sigma_2=1$, $\nu_1=3$, $\nu_2=8$, $\Gamma_1=0.5$, $\Gamma_2=2$. Convert $x_t=0.5$.

- A) $3.72$
- B) $4.49$
- C) $3.25$
- D) $4.13$

> [!success]- Answer
> **A.** Equal weights and variances, so only the exponentials count: $e^{-0.5^2/2}=0.8825$ and $e^{-1.5^2/2}=0.3247$.
> Posteriors: $0.8825/1.2072=0.731$ and $0.269$.
> Local regressions: $3+0.5\cdot0.5=3.25$ and $8+2\cdot(0.5-2)=5$.
> $\mathcal F(0.5)=0.731\cdot3.25+0.269\cdot5=2.376+1.345=3.72$.
> - B: The posteriors are not normalised: $0.8825\cdot3.25+0.3247\cdot5=4.49$.
> - C: This is a hard choice of the closer Gaussian (its local regression only) instead of the soft sum.
> - D: This uses the priors $\alpha_i=0.5$ instead of the posteriors: $0.5\cdot3.25+0.5\cdot5=4.125$.

### Q13 (slide 13)

The VQ-type special case of GMM conversion drops the correction term: $\mathcal F(x)=\sum_i P(C_i\mid x)\,\nu_i$. For a frame with $P(C_1\mid x_t)=0.8$ and $P(C_2\mid x_t)=0.2$, and with $\nu_1=3$, $\nu_2=8$, what is $\mathcal F(x_t)$?

- A) $3$
- B) $4.0$
- C) $7.0$
- D) $5.5$

> [!success]- Answer
> **B.** $\mathcal F(x_t)=0.8\cdot3+0.2\cdot8=2.4+1.6=4.0$.
> Like the codebook mapping of Abe et al., the output is built only from a fixed set of target vectors, so it always lies between 3 and 8. Unlike it, the weights are soft, so the output changes smoothly with $x_t$ and has no jumps.
> - A: This is a hard VQ mapping: the most likely class gives $\nu_1$ alone.
> - C: The posteriors are swapped: $0.2\cdot3+0.8\cdot8=7.0$.
> - D: This is the plain average of $\nu_1$ and $\nu_2$, as if both posteriors were 0.5.

## 5. Solving for the parameters (slides 14-15)

### Q14 (slide 14)

The unknowns $\boldsymbol\nu$ and $\boldsymbol\Gamma$ of GMM conversion minimise $\epsilon=\sum_t\lVert y_t-\mathcal F(x_t)\rVert^2$. With all frames stacked this is the least-squares problem $Y=P\boldsymbol\nu+\Delta\boldsymbol\Gamma$, where $Y$ is $n\times p$ (target vectors), $P$ is $n\times m$ (posteriors) and $\Delta$ is $n\times pm$. Take $p=13$, $m=4$, $n=1000$. How many unknown scalars are in $\boldsymbol\nu$ and $\boldsymbol\Gamma$ together?

- A) $676$
- B) $2704$
- C) $56$
- D) $728$

> [!success]- Answer
> **D.** $P\boldsymbol\nu$ must be $n\times p$, so $\boldsymbol\nu$ is $m\times p=4\times13=52$. $\Delta\boldsymbol\Gamma$ must be $n\times p$, so $\boldsymbol\Gamma$ is $pm\times p=52\times13=676$.
> Total: $(m+pm)\,p=(4+52)\cdot13=728$. (Each row of $P$ holds the posteriors of one frame, so it sums to 1.)
> - A: This counts only $\boldsymbol\Gamma$ and forgets $\boldsymbol\nu$.
> - B: This is $(pm)^2=52^2$, the size of the block $\Delta^{\mathsf T}\Delta$, not the number of unknowns.
> - C: This counts only the rows $m+pm$ and forgets that each row has $p$ entries.

### Q15 (slide 15)

The normal equations contain the block $\Delta^{\mathsf T}\Delta$, where $\Delta$ is $n\times pm$. Each entry of $\Delta^{\mathsf T}\Delta$ is the dot product of two columns of $\Delta$. The block is symmetric, so only half of its entries are computed. With $p=20$, $m=128$, $n=20{,}000$, about how many multiplications does this block need?

- A) $65.5\cdot10^{9}$
- B) $51.2\cdot10^{6}$
- C) $131\cdot10^{9}$
- D) $3.3\cdot10^{6}$

> [!success]- Answer
> **A.** $pm=2560$, so the block has $2560^2=6.55\cdot10^6$ entries. Half of them is $3.28\cdot10^6$. Each needs $n=20{,}000$ products: $n(pm)^2/2=65.5\cdot10^9$, the slide's $65\cdot10^9$.
> The cost grows with $n$ but with the square of $m$: twice the Gaussians cost four times as much.
> - B: This is $n\cdot p\cdot m$, the number of entries of $\Delta$, instead of $n(pm)^2/2$.
> - C: This is $n(pm)^2$ without the half that symmetry saves.
> - D: This is $(pm)^2/2$, the number of entries computed, without the $n$ products for each.

## 6. Quality against similarity, weighted frequency warping (slides 16-17)

### Q16 (slide 16)

A student writes: "DFW gives low quality because it averages many spectra. GMM conversion gives poor similarity because it cannot change formant heights." Which statement is correct?

- A) DFW averages many spectra, so its quality is low. GMM conversion cannot change formant heights, so its similarity is poor.
- B) GMM conversion averages many spectra, so its quality is low. DFW cannot change formant heights, so its similarity is poor.
- C) GMM conversion averages many spectra, so its quality is low. It cannot change formant heights either, so its similarity is poor too.
- D) DFW averages many spectra, so its quality is low. It cannot change formant heights either, so its similarity is poor too.

> [!success]- Answer
> **B.** GMM conversion is a posterior-weighted conditional mean. Averaging target spectra makes the formant peaks lower and broader (over-smoothing), so quality is low, but similarity is good.
> DFW only moves the source envelope along the frequency axis. The detail is kept (good quality), but the formant heights stay those of the source (poor similarity).
> - A: This is the student's claim. The two reasons are swapped.
> - C: GMM conversion does change the spectrum towards the target, so its similarity is good.
> - D: DFW does not average spectra. It warps the frame's own envelope, so its quality is good.

### Q17 (slide 17)

In weighted frequency warping, each Gaussian has its own frequency warp (DFW), and a test frame uses the average of the warps weighted by its posteriors. A frame has $p_1(x)=0.7$ and $p_2(x)=0.3$. Warp 1 moves a formant at 1000 Hz to 1100 Hz. Warp 2 moves it to 1300 Hz. Where does the averaged warp put this formant?

- A) 840 Hz
- B) 1100 Hz
- C) 1160 Hz
- D) 1240 Hz

> [!success]- Answer
> **C.** $0.7\cdot1100+0.3\cdot1300=770+390=1160$ Hz.
> The average is taken over warps, not over spectra, and the warp is applied to the frame's own envelope, so the peaks stay sharp. A warp cannot change formant energy, so the energy is corrected in five bands.
> - A: The warps are applied in the wrong direction (shifts of $-100$ and $-300$ Hz): $0.7\cdot900+0.3\cdot700=840$.
> - B: This uses only the most likely warp, a hard choice instead of the weighted average.
> - D: The weights are swapped: $0.3\cdot1100+0.7\cdot1300=1240$.

## 7. Conversion without a parallel corpus (slides 18-19)

### Q18 (slide 18)

Source speaker C and target speaker D have no parallel recordings, but another pair, A and B, has. The method has three steps: (1) build a GMM conversion model from A to B, (2) build adaptation models from A to C and from B to D, (3) adapt the conversion model. Which step needs parallel recordings (both speakers say the same sentences)?

- A) Steps 1 and 2: step 2 aligns the frames of C with A and of D with B by DTW. Step 3 uses no speech.
- B) Only step 2: A must share sentences with C, and B with D. Step 1 fits separate GMMs to A and B.
- C) No step: step 1 also fits distributions only, so A and B need no shared sentences either.
- D) Only step 1, for A and B. Step 2 fits distributions of C and D, and step 3 uses no speech.

> [!success]- Answer
> **D.** Step 1 trains a GMM conversion, which needs aligned frame pairs $(x_t,y_t)$ of A and B, so a parallel corpus.
> Step 2 moves A's model to fit C's frames (and B's to fit D's). It fits distributions, so any sentences of C and D work. Step 3 only transforms model parameters.
> - A: The adaptation needs no frame pairs, so no DTW and no shared sentences.
> - B: Same reason. And step 1 needs pairs of A and B, not two separate GMMs.
> - C: The conversion of step 1 is trained on aligned pairs, which need the same sentences.

### Q19 (slide 19)

One Gaussian (one dimension) of an A to B conversion model has $\mu^x=2$, $\mu^y=5$, variance $\Sigma^{xx}=4$ and cross-covariance $\Sigma^{yx}=3$. Adaptation maps speaker A to C by $x'=1.5\,x+1$ and speaker B to D by $y'=0.5\,y+2$. The Gaussian is pushed through these linear maps, and then the frame $x'=7$ of speaker C is converted with $y'=\mu^{y'}+\Sigma^{y'x'}(\Sigma^{x'x'})^{-1}(x'-\mu^{x'})$. What is the result?

- A) $5.25$
- B) $6.75$
- C) $6.0$
- D) $6.25$

> [!success]- Answer
> **A.** Means: $\mu^{x'}=1.5\cdot2+1=4$, $\mu^{y'}=0.5\cdot5+2=4.5$.
> Covariances: $\Sigma^{x'x'}=1.5\cdot4\cdot1.5=9$, $\Sigma^{y'x'}=0.5\cdot3\cdot1.5=2.25$. Slope $2.25/9=0.25$.
> $y'=4.5+0.25\cdot(7-4)=5.25$.
> - B: The old slope $3/4=0.75$ is kept: $4.5+0.75\cdot3=6.75$.
> - C: The covariances are moved like means ($\Sigma^{x'x'}=1.5\cdot4+1=7$, $\Sigma^{y'x'}=0.5\cdot3+2=3.5$): $4.5+0.5\cdot3=6.0$.
> - D: The means are not adapted: $5+0.25\cdot(7-2)=6.25$.

## 8. Foreign accent conversion (slides 20-22)

### Q20 (slide 20)

Modulation theory says that speech is a voice-quality carrier (the organic aspects of a voice) modulated by linguistic gestures. Which statement is correct?

- A) Accent conversion keeps the learner's gestures and replaces the carrier. Voice conversion replaces the gestures.
- B) Accent conversion keeps the learner's carrier and replaces the gestures. Voice conversion also replaces the gestures.
- C) Accent conversion keeps the learner's carrier and replaces the gestures. Voice conversion replaces the carrier.
- D) Accent conversion keeps the learner's gestures and replaces the carrier. Voice conversion also replaces the carrier.

> [!success]- Answer
> **C.** The carrier is set by the anatomy, so it says who speaks. The gestures carry what is said and how it is pronounced, so also the accent.
> Accent conversion keeps the learner's voice (carrier) and takes a native speaker's gestures. Voice conversion keeps the source's words and accent (gestures) and changes the carrier to the target's.
> - A: Both roles are swapped.
> - B: Voice conversion keeps the gestures. It changes who seems to speak.
> - D: Accent conversion must keep the learner's voice, so it keeps the carrier.

### Q21 (slide 21)

The teacher's $F_0$ contour over three frames is 180, 220, 200 Hz. The learner's mean $F_0$ is 120 Hz. The contour is shifted in Hz (a constant is added) so that its mean becomes the learner's mean. What is the shifted contour?

- A) 108, 132, 120 Hz
- B) 260, 300, 280 Hz
- C) 120, 160, 140 Hz
- D) 100, 140, 120 Hz

> [!success]- Answer
> **D.** Teacher mean: $(180+220+200)/3=200$ Hz. Shift: $120-200=-80$ Hz. Contour: 100, 140, 120 Hz.
> The rises and falls of the teacher are kept, but in the learner's pitch range, which is a cue of the learner's identity.
> - A: This scales by $120/200=0.6$ (a shift in $\log F_0$) instead of adding a constant.
> - B: The shift has the wrong sign: $+80$ Hz.
> - C: The shift puts the first frame at 120 Hz ($-60$ Hz) instead of the mean.

### Q22 (slide 22)

In the segmental part of accent conversion, a learner (foreign-accented) and a native speaker say the same sentence. A glottal excitation and a spectral envelope are combined as source and filter (FD-PSOLA). Before that, one envelope is warped with vocal tract length normalisation (VTLN), using the average formants of the two speakers. Which statement is correct?

- A) Excitation from the native speaker. Envelope from the learner, warped towards the native speaker's average formants.
- B) Excitation from the learner. Envelope from the learner, warped towards the native speaker's average formants.
- C) Excitation from the native speaker. Envelope from the native speaker, warped towards the learner's average formants.
- D) Excitation from the learner. Envelope from the native speaker, warped towards the learner's average formants.

> [!success]- Answer
> **D.** The learner gives the glottal excitation (voice quality). The native speaker gives the spectral envelope (native articulation). VTLN moves the native envelope towards the learner's average formants, so fewer cues of the native speaker's identity remain.
> Without VTLN the output would sound more like the native speaker.
> - A: Both roles are swapped. The output would have the native speaker's voice and the learner's articulation.
> - B: The learner's own envelope still holds the foreign articulation. A warp moves formants but does not change the pronunciation.
> - C: The native excitation would bring the native speaker's voice quality, and the learner's voice would be lost.

## 9. Evaluation and results (slides 23-26)

### Q23 (slide 23)

In the identity test, a listener says whether two utterances come from the same speaker and gives a confidence $c$ from 1 to 7 (7 = very confident). The answer is unfolded to a score from 0 (surely the same) to 14 (surely different). Unfold "same, $c=6$" and "different, $c=2$".

- A) 13 and 5
- B) 1 and 9
- C) 1 and 5
- D) 13 and 9

> [!success]- Answer
> **B.** "Same" gives $7-c$, so $7-6=1$. "Different" gives $7+c$, so $7+2=9$. 1 is almost surely the same speaker, 9 is a weak "different" (7 is the neutral middle).
> - A: The two rules are swapped: $7+6=13$ and $7-2=5$.
> - C: $7-c$ is used for both answers.
> - D: $7+c$ is used for both answers.

### Q24 (slide 24)

The experiment has five conditions with the same 20 sentences each (100 utterances): (1) learner, unconverted; (2) prosodic conversion; (3) segmental conversion; (4) both; (5) teacher, unconverted. Why are conditions 1 and 5 included?

- A) They are references: the learner shows the starting accent, quality and identity, the teacher the native end.
- B) They are the training data of the conversions, so the listeners must confirm that both recordings are clean.
- C) They fix the units of the identity plot: the learner-teacher distance is defined as one unit of identity.
- D) Their ratings are subtracted from those of the conversions, so that only the differences are reported.

> [!success]- Answer
> **A.** The three conversions are read between two references: the unconverted learner (starting accent, quality and identity) and the native teacher (the goal for accent, the far end for identity).
> - B: The listening test rates stimuli. It does not check training data.
> - C: The ISOMAP axes have no unit. Only distances between points count, and no distance is fixed in advance.
> - D: The results show raw mean ratings for all five conditions, with the references as bars of their own.

### Q25 (slide 25)

![[cs568_lecture_20_l20_p025.jpg]]

The plots give the mean perceived accent (0 = none, 6 = extreme) and the perceived quality (mean opinion score) of the five conditions. Compare conditions 1 and 2: why does condition 2 lose quality?

- A) The prosodic conversion also replaces the learner's glottal excitation with the teacher's excitation.
- B) The teacher's $F_0$ contour is used without a shift, so it lies outside the learner's natural pitch range.
- C) Changing durations and pitch by signal processing leaves artefacts, even when the accent stays the same.
- D) The prosodic conversion also warps the learner's envelope with VTLN, which makes the formants broader.

> [!success]- Answer
> **C.** Condition 2 only changes durations and the $F_0$ contour (with PSOLA-type processing). Its accent stays about 4.8, as for condition 1, but its quality falls from about 4.0 to about 3.0. The processing leaves artefacts.
> - A: The excitation is never replaced. In segmental conversion it is the learner's own, and prosodic conversion does not touch it.
> - B: The teacher's contour is shifted into the learner's pitch range.
> - D: VTLN belongs to segmental conversion, and it warps the native envelope, not the learner's.

### Q26 (slide 26)

Listeners judged pairs of stimuli (same speaker or not). ISOMAP placed the stimuli in a plane so that distances follow the perceived differences (the axes have no unit). Normal speech: the teacher is at ISOMAP$_1$ about 2.2, the other four conditions at about $-0.7$ to $-0.4$. Along ISOMAP$_2$, learner and prosodic lie at about $-0.6$ to $-0.3$, segmental and combined at about $-0.1$ to $0.7$. Reversed speech: the teacher is at ISOMAP$_1$ about $-1.6$, the others at about 0 to 0.9. Along ISOMAP$_2$, learner and prosodic lie at about $-1.1$ to $0.2$, segmental and combined at about 0 to 0.75. Which conclusion is correct?

- A) With normal speech all four lie together near the learner. Only with reversed speech do the segmental and combined move away.
- B) In both panels all four lie far from the teacher. With normal speech the segmental and combined already form a separate group.
- C) The segmental and combined lie closer to the teacher than to the learner, so the native envelope brings the teacher's identity.
- D) With normal speech the prosodic points form a group apart from the learner, so pitch and timing change the voice the most.

> [!success]- Answer
> **B.** In both panels the four conditions are far from the teacher, so the learner's identity is mostly kept. With normal speech there is a gap along ISOMAP$_2$ (from $-0.3$ to $-0.1$) between learner and prosodic on one side and segmental and combined on the other. So changing the envelope changes the perceived voice a little. With reversed speech the two groups touch and partly overlap (about 0 to 0.2).
> (The slide repeats the title "perceived accent and quality", but these plots show speaker identity.)
> - A: The gap is in the normal-speech panel. In the reversed-speech panel the groups overlap.
> - C: All four conditions are about 2.6 to 2.9 units from the teacher along ISOMAP$_1$ with normal speech, much farther than from the learner.
> - D: The prosodic points lie on the learner's points. Prosodic conversion does not move the perceived voice.
