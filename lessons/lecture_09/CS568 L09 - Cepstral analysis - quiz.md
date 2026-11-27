---
course: CS 568 Speech Processing
lesson: CS568 L09 - Cepstral analysis
type: quiz
questions: 22
---

# CS568 L09 - Cepstral analysis - quiz

Lesson note: [[CS568 L09 - Cepstral analysis]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Definition of the cepstrum (slides 2-3)

### Q1 (slide 2)

The cepstrum is computed by three blocks in a row: a DFT, a log, and an IDFT. The signals are $x[n]\to X[k]\to\hat X[k]=\log\lvert X[k]\rvert\to c[n]$. Which block is not linear, and why does the formula take the magnitude before the log?

- A) The DFT, since it uses complex exponentials. The magnitude is taken because $X[k]$ is complex; the price is that the phase is lost.
- B) The log. The magnitude is taken because it is the step that turns source times filter into source plus filter.
- C) The DFT, since it uses complex exponentials. The magnitude is the step that turns source times filter into source plus filter.
- D) The log. The magnitude is taken because $X[k]$ is complex and the log of a positive real is simple; the phase is lost.

> [!success]- Answer
> **D.** The DFT and the IDFT are linear (a sum of weighted samples). The log is the only nonlinear block: $\log(a+b)\neq\log a+\log b$.
> $X[k]$ is complex. The log of a real positive number is simple, so the formula uses $\lvert X[k]\rvert$. The price is that the phase is thrown away.
> - A: The reason for the magnitude is right, but complex exponentials do not make a transform nonlinear. The DFT of a sum is the sum of the DFTs.
> - B: The block is right, but the magnitude does not turn a product into a sum. $\lvert UV\rvert=\lvert U\rvert\,\lvert V\rvert$ is still a product. The log does that step.
> - C: Both parts are wrong: the DFT is linear, and the product becomes a sum only in the log.

### Q2 (slide 3)

![[cs568_lecture_09_l9_p003.jpg]]

The three panels are the magnitude spectrum, the log magnitude spectrum and the cepstrum of one voiced frame, with $f_s=16$ kHz. The cepstrum (bottom) has large values in the first few samples and an isolated peak at about sample 120. Which statement is correct?

- A) The first samples come from the pitch, the isolated peak from the vocal tract. If $F_0$ doubled, the peak would move to about sample 60.
- B) The first samples come from the vocal tract, the isolated peak from the pitch. If $F_0$ doubled, the peak would move to about sample 60.
- C) The first samples come from the vocal tract, the isolated peak from the pitch. If $F_0$ doubled, the peak would move to about sample 240.
- D) The first samples come from the pitch, the isolated peak from the vocal tract. If $F_0$ doubled, the peak would move to about sample 240.

> [!success]- Answer
> **B.** The slow envelope (vocal tract) gives the large values at low quefrency. The harmonic ripple (pitch) gives the isolated peak at $n=f_s/F_0$.
> Now $F_0\approx16000/120\approx133$ Hz. If $F_0$ doubles to about 267 Hz, the peak is at $16000/267\approx60$ samples. The low-quefrency part stays where it is.
> - A: The two parts are swapped. The vocal tract changes slowly along frequency, so it sits at low quefrency, not at the isolated peak.
> - C: The peak index was doubled together with $F_0$. The peak is at the period $f_s/F_0$, so a higher $F_0$ moves it down, to about 60.
> - D: Both slips: the parts are swapped, and the peak index was doubled with $F_0$ instead of halved.

## 2. Why it works: log spectrum as a waveform, deconvolution (slides 4-7)

### Q3 (slide 4)

The log magnitude spectrum of a voiced frame has harmonics every 125 Hz, and $f_s=8$ kHz. Its IDFT (the cepstrum) is indexed in samples. At which sample is the large spike, and why is the log taken before the second transform?

- A) At sample 125. The log compresses the range, so the harmonics have similar size along the whole axis and the ripple looks periodic.
- B) At sample 64. The log removes the phase, so the second transform gets a real input and gives a real cepstrum.
- C) At sample 64. The log compresses the range, so the harmonics have similar size along the whole axis and the ripple looks periodic.
- D) At sample 125. The log removes the phase, so the second transform gets a real input and gives a real cepstrum.

> [!success]- Answer
> **C.** The ripple repeats every $F_0=125$ Hz. Its period on the cepstral axis is $T_0=1/F_0=8$ ms, which is $f_s/F_0=8000/125=64$ samples.
> Without the log the harmonics fall quickly with frequency, and the "waveform" is a few large peaks at low frequency. The log compresses the range, so the ripple looks periodic along the whole axis. (The log also turns source times filter into a sum.)
> - A: The reason is right, but the spike was put at $F_0$ in Hz. It is at the period $f_s/F_0$ in samples.
> - B: The position is right, but the log does not remove the phase. The magnitude $\lvert X\rvert$, taken before the log, removes it.
> - D: Both slips: the spike at $F_0$ instead of $f_s/F_0$, and the phase loss given to the log instead of the magnitude.

### Q4 (slide 5)

![[cs568_lecture_09_l9_p005.jpg]]

Left: the blue curve is a cepstrum, the red curve is a lifter that is 1 up to about 5 and 0 after, and a small blue peak is near 9. Read this axis in ms, with $f_s=16$ kHz. Right: the blue curve is the log spectrum, the red curve is the log spectrum after liftering. Why is the red curve on the right smooth, and what would happen if the lifter ended at about 12 instead?

- A) The lifter removes quefrencies above 5, which hold the high-frequency parts of the speech. With a cut-off at 12 the curve would stay just as smooth.
- B) The lifter removes quefrencies above 5, which hold the high-frequency parts of the speech. With a cut-off at 12 the harmonic ripple would come back.
- C) The pitch peak near 9 lies outside the lifter, so it is removed. With a cut-off at 12 the curve would stay just as smooth.
- D) The pitch peak near 9 lies outside the lifter, so it is removed. With a cut-off at 12 the harmonic ripple would come back.

> [!success]- Answer
> **D.** The lifter keeps quefrencies below about 5 ms ($0.005\cdot16000=80$ samples). The pitch peak at about 9 ms is outside, so it is removed. The pitch peak makes the harmonic ripple, so only the slow envelope is left.
> With a cut-off at 12 ms the pitch peak is inside the lifter. The ripple comes back, and the red curve is no longer a clean envelope.
> - A: Two slips. High quefrency is not high frequency: the red curve still runs up to 8000 Hz. And at 12 the pitch peak (near 9) is kept, so the curve is not smooth.
> - B: The effect at 12 is right, but the reason confuses high quefrency with high frequency. Quefrency is a time on the cepstral axis.
> - C: The reason is right, but a cut-off at 12 lies above the pitch peak near 9. Then the peak is kept, and the ripple returns.

### Q5 (slide 6)

Speech is $y=u*v'$ (source convolved with vocal tract and radiation). The slide reaches $c=c_u+c_v$ in three steps: Fourier transform, log of the magnitude, inverse Fourier transform. Which statement is correct?

- A) The Fourier transform turns the convolution into a sum. The log only compresses the range. The inverse transform is linear, so the sum stays.
- B) The Fourier transform turns the convolution into a product. The log turns the product into a sum. The inverse transform is linear, so the sum stays.
- C) The Fourier transform turns the convolution into a product. The log turns the product into a sum. The inverse transform turns it back into a product.
- D) The Fourier transform turns the convolution into a sum. The log only compresses the range. The sum stays because $c_u$ and $c_v$ lie at other quefrencies.

> [!success]- Answer
> **B.** Step 1: $Y=U\,V'$, a product. Step 2: $\log\lvert Y\rvert=\log\lvert U\rvert+\log\lvert V'\rvert$, a sum. Step 3: the inverse transform is linear, the transform of a sum is the sum of the transforms, so $c=c_u+c_v$.
> - A: The Fourier transform turns a convolution into a product, not a sum. The sum appears only in the log step, so the log does more than compress.
> - C: The first two steps are right. The inverse transform is linear and keeps the sum. Turning a sum back into a convolution needs $\exp$ (the inverse system $D_*^{-1}$).
> - D: The first two steps are wrong as in A. Also, the sum would stay even if $c_u$ and $c_v$ overlapped: linearity keeps it. The different quefrencies only make the later separation easy.

### Q6 (slide 7)

A student writes: "The cepstrum is a kind of spectrum, so quefrency is measured in Hz. A component at high quefrency is a high-frequency part of the speech signal." Which statement is correct?

- A) Quefrency has units of time. A high quefrency is a fast ripple of the log spectrum along the frequency axis.
- B) Quefrency has units of time. A high quefrency is a high-frequency part of the speech signal.
- C) Quefrency has units of Hz. A high quefrency is a fast ripple of the log spectrum along the frequency axis.
- D) Quefrency has units of Hz. A high quefrency is a high-frequency part of the speech signal.

> [!success]- Answer
> **A.** The cepstrum is an inverse transform of a function of frequency, so its axis has units of time (samples or ms). A component at quefrency 5 ms is a ripple of the log spectrum that repeats every $1/0.005=200$ Hz: the harmonic pattern of a 200 Hz voice. Low quefrency is the slow change along frequency, the formant envelope.
> - B: The unit is right, but high quefrency was read as high frequency. It says how fast the log spectrum changes along the frequency axis, not where in frequency the energy is.
> - C: The meaning is right, but the unit is wrong. An inverse transform of a function of frequency lives on a time axis.
> - D: This is the student's claim. Both parts are wrong, as explained for B and C.

## 3. Liftering and what it keeps (slides 8-11)

### Q7 (slide 8)

A DFT followed by an IDFT gives the signal back. The real cepstrum is $c[n]=\mathcal F^{-1}\{\log\lvert\mathcal F\{x[n]\}\rvert\}$. What can be recovered from $c[n]$?

- A) All of $x[n]$: take the DFT, then the exponential, then the IDFT. Each step undoes one step of the cepstrum.
- B) Only the phase of $X[k]$: the log destroys the magnitude, and the phase passes through the IDFT unchanged.
- C) Only $\lvert X[k]\rvert$: the DFT of $c[n]$ gives $\log\lvert X[k]\rvert$, and the exponential undoes the log.
- D) Only the envelope: the DFT of $c[n]$ brings back the formants, but the pitch information is lost.

> [!success]- Answer
> **C.** The DFT of $c[n]$ is $\log\lvert X[k]\rvert$, and the exponential gives $\lvert X[k]\rvert$. The phase was dropped when the magnitude was taken. Many signals have the same magnitude spectrum and so the same real cepstrum, so $x[n]$ itself cannot come back.
> - A: The steps undo the log and the transforms, but nothing undoes $\lvert\cdot\rvert$. The IDFT of $\lvert X[k]\rvert$ is not $x[n]$.
> - B: It is the other way round: the magnitude survives and the phase is lost.
> - D: The whole magnitude comes back, with the harmonics. The cepstrum keeps the pitch peak too; only liftering would drop it.

### Q8 (slide 9)

![[cs568_lecture_09_l9_p009.jpg]]

The four curves are log spectra smoothed by keeping the first $K=10,20,30,50$ cepstral coefficients ($f_s=16$ kHz). They are shifted up so that they do not overlap. The pitch period of this frame is 120 samples. Which statement is correct?

- A) More coefficients keep faster variations, so the detail grows with $K$. With $K=150$ the harmonic ripple would come back.
- B) More coefficients average over more, so the curve gets smoother with $K$. With $K=150$ the harmonic ripple would come back.
- C) More coefficients keep faster variations, so the detail grows with $K$. With $K=150$ the curve would still be free of harmonics.
- D) More coefficients average over more, so the curve gets smoother with $K$. With $K=150$ the curve would still be free of harmonics.

> [!success]- Answer
> **A.** $K=10$ shows only a few broad humps. $K=50$ shows narrower detail, for example a sharp peak near 200 to 300 Hz. More coefficients keep faster variations of the log spectrum.
> $K=150$ is above the pitch period of 120 samples. The pitch peak is kept, and the harmonic ripple returns on top of the envelope.
> - B: The direction is wrong. Each extra coefficient is a faster cosine along frequency, so it adds detail. The figure shows it: $K=50$ is the most detailed curve.
> - C: The trend is right, but $K=150>120$ keeps the pitch peak. A lifter gives a clean envelope only when it ends below the pitch period.
> - D: Both parts are wrong, as in B and C.

### Q9 (slide 10)

A model stores one Gaussian for a vector of 13 cepstral coefficients: a mean vector and a covariance matrix. How many numbers does it store with a full covariance matrix, and how many if the coefficients are uncorrelated (diagonal covariance)?

- A) 104 with full covariance, 26 with diagonal covariance
- B) 182 with full covariance, 13 with diagonal covariance
- C) 104 with full covariance, 13 with diagonal covariance
- D) 182 with full covariance, 26 with diagonal covariance

> [!success]- Answer
> **A.** Full: 13 means and a symmetric $13\times13$ matrix with $13\cdot14/2=91$ free values, so $13+91=104$.
> Diagonal: 13 means and 13 variances, so $26$. Fewer parameters per Gaussian need less training data, which is why nearly uncorrelated features help.
> - B: Two slips: all $13\cdot13=169$ matrix entries were counted (the matrix is symmetric), and the diagonal case forgot the 13 means.
> - C: The full case is right, but the diagonal case counts only the 13 variances and forgets the 13 means.
> - D: The diagonal case is right, but the full case counts all 169 entries: $13+169=182$. The values above and below the diagonal are equal.

## 4. Homomorphic filtering (slides 12-15)

### Q10 (slide 12)

A system is linear if $L[x_1+x_2]=L[x_1]+L[x_2]$. It is homomorphic for convolution if $H[x_1*x_2]=H[x_1]*H[x_2]$. Take $H[x(n)]=2x(n)$. Which statement is correct?

- A) Linear: no, since $2(x_1+x_2)\neq x_1+x_2$. Homomorphic: no, since $2(x_1*x_2)\neq x_1*x_2$.
- B) Linear: yes, $2(x_1+x_2)=2x_1+2x_2$. Homomorphic: no, since $(2x_1)*(2x_2)=4(x_1*x_2)$.
- C) Linear: yes, $2(x_1+x_2)=2x_1+2x_2$. Homomorphic: yes, both sides are $2(x_1*x_2)$.
- D) Linear: no, since $2(x_1+x_2)\neq x_1+x_2$. Homomorphic: yes, both sides are $2(x_1*x_2)$.

> [!success]- Answer
> **B.** Linear: $H[x_1+x_2]=2x_1+2x_2=H[x_1]+H[x_2]$.
> Homomorphic: the left side is $H[x_1*x_2]=2(x_1*x_2)$. The right side is $H[x_1]*H[x_2]=(2x_1)*(2x_2)=4(x_1*x_2)$. They differ.
> - A: The output was compared with the input ($H[x]=x$?). The definitions compare $H$ of the combination with the combination of the $H$ values.
> - C: The gain was applied only once on the right side. Each of $H[x_1]$ and $H[x_2]$ carries a 2, so the convolution carries 4.
> - D: Two slips: the output compared with the input for linearity, and the gain applied once on the right side.

### Q11 (slide 13)

A homomorphic system for convolution is a cascade of three boxes: $D_*$, then a linear system $L$, then $D_*^{-1}$. The input is $x(n)=x_1(n)*x_2(n)$. $D_*$ gives $\hat x(n)$, $L$ gives $\hat y(n)$, and $D_*^{-1}$ gives the output $y(n)$. Which statement is correct?

- A) $L$ takes the sum $\hat x_1+\hat x_2$ and returns a convolution, and $D_*^{-1}$ then passes this convolution on.
- B) The inputs of $D_*^{-1}$ are $y_1$ and $y_2$, so the output is $y(n)=D_*^{-1}[y_1(n)]*D_*^{-1}[y_2(n)]$.
- C) $D_*$ is chosen for each task, while $L$ is fixed: it is the same linear system in every application.
- D) $L$ takes a sum and returns a sum, and it is the only one of the three boxes that the designer chooses.

> [!success]- Answer
> **D.** $D_*$: convolution in, sum out ($\hat x_1+\hat x_2$). $L$: sum in, sum out, an ordinary linear system. $D_*^{-1}$: sum in, convolution out, $D_*^{-1}[\hat y_1(n)+\hat y_2(n)]=D_*^{-1}[\hat y_1(n)]*D_*^{-1}[\hat y_2(n)]=y_1(n)*y_2(n)$. $D_*$ and $D_*^{-1}$ are fixed, so the design is the design of $L$.
> - A: $L$ is linear, so it keeps sums as sums. The step from a sum back to a convolution is the job of $D_*^{-1}$.
> - B: The hats are missing. $y_1$ and $y_2$ are the outputs of $D_*^{-1}$, not its inputs. Its inputs are the hatted signals $\hat y_1$, $\hat y_2$ that come out of $L$. (Slide 13 prints this wrong form by mistake; the correct form is in the answer above.)
> - C: The roles are swapped. $D_*$ (and its inverse) is fixed for convolution; $L$ is what changes from task to task, for example a lifter.

### Q12 (slide 14)

In the $z$ domain, $D_*$ maps $X(z)$ to $\hat X(z)=\log X(z)$, and $D_*^{-1}$ maps $\hat Y(z)$ to $Y(z)=\exp\hat Y(z)$. Between them, a linear system $L$ maps $\hat X(z)$ to $\hat Y(z)$. If $L$ does nothing, the output is $x(n)$ again. What is the output $y(n)$ if $L$ multiplies its input by 2?

- A) $y(n)=x(n)*x(n)$, because $\exp(2\log X(z))=X(z)^2$, and a product in $z$ is a convolution in time.
- B) $y(n)=2x(n)$, because a gain of 2 passes through the exponential and the inverse transform unchanged.
- C) $y(n)=x(n)^2$, because $\exp(2\log X(z))=X(z)^2$, and squaring in $z$ squares each sample in time.
- D) $y(n)=e^2x(n)$, because $\exp(2\log X(z))=e^2\exp(\log X(z))=e^2X(z)$ in the $z$ domain.

> [!success]- Answer
> **A.** $\hat Y(z)=2\log X(z)$, so $Y(z)=\exp(2\log X(z))=X(z)^2=X(z)\cdot X(z)$. A product of $z$-transforms is a convolution in time: $y(n)=x(n)*x(n)$. A gain in the log domain acts on the convolved parts, not on the amplitude.
> - B: The exponential is not linear. A factor 2 inside it becomes a power, not a factor.
> - C: The $z$-domain result is right, but a product in $z$ is a convolution in time, not a product of samples.
> - D: Algebra slip: $\exp(2a)=(\exp a)^2$, not $e^2\exp a$. The 2 multiplies the exponent, it is not added to it.

### Q13 (slide 15)

The system $D_*$ uses the complex log, $\log X=\log\lvert X\rvert+j\angle X$, and gives the complex cepstrum $\hat x(n)$. The cepstrum of slide 2 uses only $\log\lvert X\rvert$ and gives the real cepstrum $c(n)$. Which statement is correct?

- A) The real cepstrum can be sent back through $D_*^{-1}$ to give $x(n)$, because it needs no phase unwrapping.
- B) Both cepstra can be sent back through $D_*^{-1}$ to give $x(n)$, because the exponential undoes the log.
- C) Only the complex cepstrum gives $x(n)$ back through $D_*^{-1}$. The real one gives back only $\lvert X\rvert$.
- D) Neither cepstrum gives $x(n)$ back through $D_*^{-1}$, because the log of a spectrum always drops the phase.

> [!success]- Answer
> **C.** The complex cepstrum keeps magnitude and phase, so $D_*^{-1}$ (transform, $\exp$, inverse transform) returns $x(n)$. The real cepstrum keeps only $\log\lvert X\rvert$, so the way back ends at $\lvert X\rvert$.
> - A: It is true that the real cepstrum needs no phase unwrapping, but that is why it is robust, not why it is invertible. Its phase is gone.
> - B: The exponential undoes the log, but for the real cepstrum it gives back $\lvert X\rvert$ only. Nothing gives back the phase.
> - D: The complex log keeps the phase as $j\angle X$. Only $\log\lvert X\rvert$ drops it.

## 5. Voicing and pitch detection (slides 16-17)

### Q14 (slide 16)

A cepstral pitch detector searches for the largest peak between 3 and 20 ms. Let $f_s=10$ kHz. What is the search range in samples? A peak is found at sample 115: what is $F_0$?

- A) From 3 to 20 samples; $F_0\approx87$ Hz
- B) From 30 to 200 samples; $F_0\approx87$ Hz
- C) From 30 to 200 samples; $F_0=115$ Hz
- D) From 3 to 20 samples; $F_0=115$ Hz

> [!success]- Answer
> **B.** Samples $=$ time $\times f_s$: $0.003\cdot10000=30$ and $0.020\cdot10000=200$ samples.
> The peak is at the period: $F_0=f_s/n_{\text{peak}}=10000/115\approx87$ Hz.
> (The search starts at 3 ms because below it the large vocal-tract values would always win.)
> - A: $F_0$ is right, but the range was copied from ms to samples without multiplying by $f_s$.
> - C: The range is right, but the peak position was read as $F_0$. The peak sits at the period $f_s/F_0$, not at $F_0$.
> - D: Both slips: ms taken as samples, and the peak position read as $F_0$.

### Q15 (slide 17)

![[cs568_lecture_09_l9_p017.jpg]]

The slide shows short-time log spectra (left) and cepstra (right) for 15 windows of one utterance. In window 12 the cepstrum has one clear peak at about 12 ms. About how far apart are the fine ripples in the log spectrum of window 12, and what are the red curves on the left?

- A) About 42 Hz apart. The red curves are LP (all-pole) envelopes, one fitted to each window.
- B) About 83 Hz apart. The red curves are cepstrally smoothed spectra (low-quefrency liftering).
- C) About 83 Hz apart. The red curves are LP (all-pole) envelopes, one fitted to each window.
- D) About 42 Hz apart. The red curves are cepstrally smoothed spectra (low-quefrency liftering).

> [!success]- Answer
> **B.** A ripple with spacing $F_0$ on the left is a peak at $1/F_0$ on the right. A peak at about 12 ms gives a spacing of about $1/0.012\approx83$ Hz.
> The red curves come from the same cepstra: keep the low quefrencies of each window and transform back. They are the envelopes of the frames.
> - A: Two slips: the spacing used $1/(2T_0)$ in place of $1/T_0$, and the curves were taken for LP envelopes. The slide is about cepstrum analysis; no LP model is fitted.
> - C: The spacing is right, but the curves are not LP envelopes. They are low-quefrency liftered log spectra.
> - D: The curves are right, but $1/(2T_0)\approx42$ Hz halves the spacing. The harmonic spacing is $1/T_0$.

## 6. Linear prediction cepstral coefficients (slide 18)

### Q16 (slide 18)

The LP model is $H(z)=G/A_p(z)$ with $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$: $a_k$ are the LP coefficients, $p$ is the order, $G$ the gain. Its cepstrum (LPCC) is $c_0=\ln G$ and, for $1\le n\le p$, $c_n=a_n+\frac1n\sum_{k=1}^{n-1}k\,c_k\,a_{n-k}$. Take $p=2$, $a_1=0.5$, $a_2=0.2$. What is $c_2$, and does the LPCC vector of a voiced frame show a pitch peak?

- A) $c_2=0.45$. No: the LP model holds only the envelope, and the excitation stays in the prediction error.
- B) $c_2=0.325$. Yes: it is the cepstrum of a voiced frame, so it has a peak at the pitch period.
- C) $c_2=0.325$. No: the LP model holds only the envelope, and the excitation stays in the prediction error.
- D) $c_2=0.45$. Yes: it is the cepstrum of a voiced frame, so it has a peak at the pitch period.

> [!success]- Answer
> **C.** $c_1=a_1=0.5$ (the sum is empty). $c_2=a_2+\frac12(1\cdot c_1\cdot a_1)=0.2+\frac12(0.5\cdot0.5)=0.2+0.125=0.325$.
> The LPCC is the cepstrum of the model, not of the frame. The model is only the vocal-tract envelope, so no pitch peak appears.
> - A: The reason is right, but the factor $\frac1n=\frac12$ was dropped: $0.2+0.25=0.45$.
> - B: $c_2$ is right, but the LPCC is the cepstrum of $G/A_p(z)$. The excitation never enters the recursion.
> - D: Both slips: the $\frac1n$ dropped, and the LPCC taken for the cepstrum of the frame itself.

## 7. Mel frequency cepstral coefficients (slides 19-21)

### Q17 (slide 19, figure)

The filters of a mel filter bank have equal width on the mel scale. Use $\mathrm{mel}(f)=2595\log_{10}(1+f/700)$ (a common formula, not on the slides). How many mel are the steps from 1000 to 2000 Hz and from 3000 to 4000 Hz, and what does this mean for the width of one filter in Hz?

- A) 521 mel and 270 mel. So one filter covers more Hz near 4000 Hz than near 1000 Hz.
- B) 1201 mel and 621 mel. So one filter covers more Hz near 4000 Hz than near 1000 Hz.
- C) 521 mel and 270 mel. So one filter covers fewer Hz near 4000 Hz than near 1000 Hz.
- D) 1201 mel and 621 mel. So one filter covers fewer Hz near 4000 Hz than near 1000 Hz.

> [!success]- Answer
> **A.** $\mathrm{mel}(1000)=2595\log_{10}(2.429)=1000$ and $\mathrm{mel}(2000)=2595\log_{10}(3.857)=1521$: a step of 521 mel.
> $\mathrm{mel}(3000)=2595\log_{10}(5.286)=1876$ and $\mathrm{mel}(4000)=2595\log_{10}(6.714)=2146$: a step of 270 mel.
> The same 1000 Hz is about half as many mel at the top. So a filter of fixed mel width is about twice as wide in Hz there. This follows the critical bands: the ear resolves high frequencies coarsely.
> - B: The natural log $\ln$ was used in place of $\log_{10}$: $2595\ln(3.857/2.429)=1201$ and $2595\ln(6.714/5.286)=621$.
> - C: The steps are right, but the conclusion is reversed. Fewer mel per 1000 Hz means more Hz per mel, so the filters get wider.
> - D: Two slips: $\ln$ in place of $\log_{10}$, and the width conclusion reversed.

### Q18 (slide 19, pipeline)

The MFCC front end has these steps: pre-emphasis, short Hamming frames, FFT magnitude, triangular mel filter bank, log, DCT. Which step is given with its correct reason?

- A) Pre-emphasis: it removes the pitch harmonics, so that the mel filters see only the envelope of the frame.
- B) Mel filter bank: it gives finer resolution at high frequencies, where the upper formants lie close together.
- C) DCT: it turns the log mel spectrum back into a time waveform, so that the pitch period can be read from it.
- D) Log: it compresses the range like the ear, and turns source times filter (and a fixed channel) into a sum.

> [!success]- Answer
> **D.** The log compresses the dynamic range as hearing does. It also turns the product of source, vocal tract and a fixed channel into a sum, so they can be separated or subtracted later.
> - A: Pre-emphasis is a first-order high-pass. It lifts the high frequencies so the weak upper formants are not lost. It does not remove harmonics.
> - B: The mel filters are narrow at low frequency and wide at high frequency, so the resolution at high frequency is coarser, not finer.
> - C: The DCT moves the log filter outputs to the cepstral domain, decorrelates them and packs the envelope into the first coefficients. It does not rebuild a waveform.

### Q19 (slide 20)

The mel spectrum of filter $r$ (bins $L_r$ to $U_r$, triangular weights $V_r[k]$) is $MF[r]=\frac{1}{A_r}\sum_{k=L_r}^{U_r}\lvert V_r[k]X(n,k)\rvert^2$ with $A_r=\sum_{k=L_r}^{U_r}\lvert V_r[k]\rvert^2$. Then $\mathrm{MFCC}[m]=\frac1R\sum_{r=1}^{R}\log(MF[r])\cos\left[\frac{2\pi}{R}\left(r+\frac12\right)m\right]$, where $R=22$ is the number of filters and $m$ the coefficient index. Only $N_{\mathrm{MFCC}}=13$ coefficients are kept. Which statement is correct?

- A) $\mathrm{MFCC}[0]$ is the mean of the first 13 log filter outputs. Small $m$ gives slow cosines over $r$, so the kept values hold the smooth envelope.
- B) $\mathrm{MFCC}[0]$ is the mean of all 22 log filter outputs. Small $m$ gives fast cosines over $r$, so the kept values hold the fine detail.
- C) $\mathrm{MFCC}[0]$ is the mean of all 22 log filter outputs. Small $m$ gives slow cosines over $r$, so the kept values hold the smooth envelope.
- D) $\mathrm{MFCC}[0]$ is the mean of the first 13 log filter outputs. Small $m$ gives fast cosines over $r$, so the kept values hold the fine detail.

> [!success]- Answer
> **C.** For $m=0$ the cosine is 1, so $\mathrm{MFCC}[0]=\frac1R\sum_{r=1}^{22}\log(MF[r])$: the mean of all 22 log filter outputs, the overall level of the frame.
> Small $m$ are slow cosines over the filter index: the smooth envelope. Large $m$ describe small differences between neighbouring filters (remains of harmonics, noise). Keeping 13 of 22 is a low-quefrency lifter.
> - A: The 13 kept coefficients were confused with the 22 filters. Every coefficient, also $m=0$, sums over all $R=22$ filters.
> - B: $\mathrm{MFCC}[0]$ is right, but the direction is reversed. The cosine over $r$ gets faster as $m$ grows, so small $m$ is the slow part.
> - D: Two slips: coefficient count confused with filter count, and the direction of the cosines reversed.

### Q20 (slide 21)

![[cs568_lecture_09_l9_p021.jpg]]

The slide compares three smoothed versions of the log spectrum of one voiced frame ($f_s=8$ kHz): LPC with $p=12$, homomorphic smoothing (low-quefrency liftering) with cut-off $n_{co}=13$ samples, and mel-cepstrum smoothing with 13 MFCCs (red dots at the filter centres). Which statement is correct?

- A) The LPC curve has the sharpest peaks because it keeps the pitch harmonics of the frame on top of the formants.
- B) The red dots are dense on the left because the DFT bins lie closer together at low frequencies than at high ones.
- C) With $n_{co}=100$ the dashed curve would get smoother, because more coefficients average over more of the spectrum.
- D) With $n_{co}=100$ the dashed curve would likely start to follow the harmonics, because the pitch peak would be kept.

> [!success]- Answer
> **D.** At 8 kHz, pitch periods of 3 to 20 ms are about 24 to 160 samples. A cut-off of 100 samples will most likely lie above the pitch period, so the pitch peak is kept and the curve follows the grey harmonics.
> - A: The LPC peaks (about 2.3 near 500 Hz) are sharp because an all-pole model makes sharp resonance peaks. It models only the envelope, not the harmonics.
> - B: DFT bins are equally spaced. The dots are dense on the left because the mel filter centres are dense at low frequency.
> - C: More cepstral coefficients keep faster variations of the log spectrum, so the curve gets more detailed, not smoother.

## 8. MFCC notes: homomorphic or not, DCT (slides 22-24)

### Q21 (slide 22)

The MFCC takes the log of a sum over each filter. A homomorphic version would take the mean of the logs. One filter covers two DFT bins with $\lvert X\rvert=1$ and $4$. Use weights 1, the mean in place of $1/A_r$, and the natural log $\ln$. Then a channel multiplies the two bins by gains 2 and 0.5. Which statement is correct?

- A) Log of the mean 0.916, mean of the logs 0.693. With the channel, only the mean of the logs is still speech term plus channel term.
- B) Log of the mean 0.398, mean of the logs 0.301. With the channel, only the mean of the logs is still speech term plus channel term.
- C) Log of the mean 0.916, mean of the logs 0.693. With the channel, only the log of the mean is still speech term plus channel term.
- D) Log of the mean 0.398, mean of the logs 0.301. With the channel, only the log of the mean is still speech term plus channel term.

> [!success]- Answer
> **A.** Log of the mean: $\ln\frac{1+4}{2}=\ln2.5=0.916$. Mean of the logs: $\frac{\ln1+\ln4}{2}=0.693$.
> With the channel the bins are $2$ and $2$. The channel term is $\frac{\ln2+\ln0.5}{2}=0$.
> Mean of the logs: $\frac{\ln2+\ln2}{2}=0.693=0.693+0$, it separates. Log of the mean: $\ln2=0.693$, but $0.916+0=0.916$, it does not.
> - B: $\log_{10}$ was used in place of $\ln$: $\log_{10}2.5=0.398$ and $\frac{\log_{10}4}{2}=0.301$.
> - C: The values are right, but the verdict is reversed. The log of a sum does not split into a sum of logs unless the channel is flat across the filter.
> - D: Two slips: $\log_{10}$ in place of $\ln$, and the verdict reversed.

### Q22 (slide 23)

For $x=(1,2,3,4)$ the DCT-II $X_{\mathrm{DCT}}(k)=\sum_{n=0}^{3}x[n]\cos\left[\frac{\pi}{4}\left(n+\frac12\right)k\right]$ gives $(10,\,-3.15,\,0,\,-0.22)$. The DFT magnitudes are $(10,\,2.83,\,2,\,2.83)$. Energy is the sum of squares. Which statement is correct?

- A) The first two DCT values hold 98.3% of the energy, against 72.7% for the DFT. The DCT is real and close to the Karhunen-Loeve transform.
- B) The first two DCT values hold 99.95% of the energy, against 90% for the DFT. The DCT is real and close to the Karhunen-Loeve transform.
- C) The first two DCT values hold 99.95% of the energy, against 90% for the DFT. The DCT is real and equals the Karhunen-Loeve transform of any frame.
- D) The first two DCT values hold 98.3% of the energy, against 72.7% for the DFT. The DCT is real and equals the Karhunen-Loeve transform of any frame.

> [!success]- Answer
> **B.** DCT: $100+9.95+0+0.05=110.0$, the first two hold $109.95/110.0\approx99.95\%$. DFT: $100+8+4+8=120$, the first two hold $108/120=90\%$. This is energy compaction.
> The DCT is also real, and it is close to the Karhunen-Loeve transform, so it nearly decorrelates the log mel energies.
> - A: The magnitudes were summed without squaring: $13.15/13.38=98.3\%$ and $12.83/17.66=72.7\%$.
> - C: The numbers are right, but the DCT only approximates the Karhunen-Loeve transform. That transform depends on the statistics of the data.
> - D: Two slips: magnitudes summed without squaring, and the DCT taken as exactly the Karhunen-Loeve transform.
