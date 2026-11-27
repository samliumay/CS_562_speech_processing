---
course: CS 568 Speech Processing
lecture: 9
title: Cepstral analysis
tags: [cs568, lecture]
slides: lectures/lecture_09/l9.pdf
full_notes: lecture_notes/lecture_09/CS568_Lecture_09_Cepstral_Analysis.pdf
---
# CS 568 · Lecture 9 · Cepstral analysis

> [!summary] In short
> - Cepstrum: IDFT of the log magnitude of the DFT. The log turns source times filter into source plus filter.
> - Low quefrencies hold the vocal-tract envelope. A peak at the pitch period $T_0=f_s/F_0$ holds the source. Liftering keeps one part.
> - Homomorphic system: $D_*$ (convolution to addition), a linear system $L$, then $D_*^{-1}$. Liftering is the linear block.
> - A strong cepstral peak in 3 to 20 ms means voiced, and $F_0=f_s/n_{\text{peak}}$.
> - LPCC: cepstral coefficients from LP coefficients by a recursion. MFCC: STFT, mel filterbank, log, DCT.

## 1. Definition of the cepstrum (slides 2-3)
![[cs568_lecture_09_l9_p002.jpg]]
![[cs568_lecture_09_l9_p003.jpg]]

**Notes.** The cepstrum is the inverse DFT of the log magnitude of the DFT ($\mathcal F$ is the DFT):

$$c[n]=\mathcal F^{-1}\big\{\log\big|\mathcal F\{x[n]\}\big|\big\}$$

For a frame of length $N$, with time index $n$, frequency index $k$ and quefrency index $m$:

$$c[m]=\frac{1}{N}\sum_{k=0}^{N-1}\log\left(\left|\sum_{n=0}^{N-1}x[n]\,e^{-j\frac{2\pi}{N}kn}\right|\right)e^{j\frac{2\pi}{N}km}$$

Three blocks: DFT, log magnitude, IDFT. Slide 3 shows them for a voiced frame: large values at low quefrency (the envelope) and an isolated peak near sample 120 (the pitch period).

> [!example] Reading slide 3
> The frequency axis ends at 8000 Hz, so $f_s=16000$ Hz. Peak at $n=120$: $T_0=120/16000=7.5$ ms, $F_0=133.3$ Hz.

> [!warning] Slide notation
> The slide uses the same index $n$ for time, the frequency sum and quefrency, calls the frame $y[n]$ but writes $x[n]$, and omits the $1/N$ of the IDFT.

> [!question]- Check (slide 2): Name the three blocks of the diagram and the signals $X[k]$, $\hat X[k]$ and $c[n]$. Which block is not linear, and why does the formula take the magnitude before the log?
> The blocks are DFT, log and IDFT. $X[k]$ is the spectrum of the frame. $\hat X[k]=\log\lvert X[k]\rvert$ is the log magnitude spectrum. $c[n]$ is the cepstrum, a sequence on a time-like axis.
> The DFT and the IDFT are linear. The log is the nonlinear block. It is the step that turns a product of spectra into a sum.
> $X[k]$ is complex. The log of a real positive number is simple, so the formula uses $\lvert X[k]\rvert$. The price is that the phase is thrown away.

> [!question]- Check (slide 3): In the top panel the low-frequency peak is about 100 and the bump near 2500 Hz is about 20. What does the middle panel do to this difference? In the bottom panel, which part belongs to the vocal tract and which to the pitch, and where would the isolated peak be if the speaker's $F_0$ doubled?
> The log compresses the range. In the middle panel the same two regions are at about 13 and about 11 to 12, so the weak high-frequency structure becomes visible.
> Bottom panel: the large values in the first few samples come from the slow envelope (vocal tract). The isolated peak near sample 120 comes from the harmonic ripple (pitch).
> The peak sits at $n=f_s/F_0$. If $F_0$ doubles, the period halves: the peak moves from about 120 to about 60 samples. The low-quefrency part stays where it is.

## 2. Why it works: log spectrum as a waveform, deconvolution (slides 4-7)
![[cs568_lecture_09_l9_p004.jpg]]
![[cs568_lecture_09_l9_p005.jpg]]
![[cs568_lecture_09_l9_p006.jpg]]
![[cs568_lecture_09_l9_p007.jpg]]

**Notes.** The log spectrum of voiced speech has harmonics every $F_0$ Hz (a fast ripple) on top of a slow envelope. Treat it as a waveform: a Fourier transform of it gives a spike at the "period" of the ripple and some low components from the envelope. A simple window separates them (slide 5).

Speech is a convolution, $y[n]=u[n]*v[n]*r[n]$ (source, vocal tract, radiation). With $v'[n]=v[n]*r[n]$:

$$Y=U\,V' \;\Rightarrow\; \log|Y(e^{j\omega})|=\log|U(e^{j\omega})|+\log|V'(e^{j\omega})| \;\Rightarrow\; c[n]=c_u[n]+c_v[n]$$

So the cepstrum turns the convolution of source and filter into an addition. $c_v$ sits at low quefrencies, $c_u$ gives peaks at multiples of the pitch period.

Vocabulary (Bogert, Healy and Tukey): **cepstrum** from spectrum, **quefrency** for its independent variable (units of time), **liftering** for linear filtering in the cepstral domain.

> [!warning] Watch out
> - $\log(ab)=\log a+\log b$, but $\log(a+b)\ne\log a+\log b$. The cepstrum separates convolved parts, not added ones such as speech plus noise.
> - The slide dates the term to 1960. The paper was published in 1963.

> [!info] Reading the figure on slide 5 (beyond the slides)
> The slide gives no axis labels. Left panel: the cepstrum (blue) against quefrency, and the lifter (red), a window that is 1 up to about 5 and 0 after it. Right panel: the log magnitude spectrum (blue) against frequency in Hz, and the result of the lifter (red).
> The units below are an inference, the slide does not state them. The right axis ends at 8000 Hz. A spectrum is normally drawn up to $f_s/2$, so $f_s$ is most likely 16 kHz. The left axis is most likely in ms: the small blue peak near 9 is the pitch peak, and 9 ms gives $F_0\approx111$ Hz. If the axis were in samples, 9 samples would mean $F_0=16000/9\approx1778$ Hz, which is not a speech pitch.
> The red curve on the right is made in three steps: multiply the cepstrum by the lifter, take the DFT, and plot it over the log spectrum. Only the slow part of the log spectrum is left, the envelope (zarf).

> [!question]- Check (slide 4): A log spectrum has harmonics every 125 Hz, and $f_s=8$ kHz. Where does the "large spike" of the slide appear? Why does the slide take the log before the second transform?
> The ripple of the log spectrum repeats every $F_0=125$ Hz. Its "period" on the cepstral axis is $T_0=1/F_0=8$ ms, which is $f_s/F_0=8000/125=64$ samples. The spike is at sample 64.
> The harmonics of the magnitude spectrum fall quickly with frequency. Without the log the "waveform" would be a few large peaks at low frequency and almost nothing after them. The log compresses the dynamic range, so the harmonics have similar size along the whole axis and the ripple looks periodic. The log also turns the product source times filter into a sum.

> [!question]- Check (slide 5): The red lifter on the left ends at about 5 and the pitch peak is near 9 (read both as ms with $f_s=16$ kHz, as inferred in the note above). Why is the red curve on the right smooth? What would it look like if the lifter ended at 12 instead?
> The lifter keeps only quefrencies below about 5 ms ($0.005\cdot16000=80$ samples). The pitch peak at about 9 ms is outside, so it is removed. The pitch peak is what makes the harmonic ripple. Without it only the slow envelope is left, so the red curve passes through the middle of the blue ripple.
> With a cut-off of 12 ms the pitch peak is inside the lifter. The harmonic ripple comes back, and the red curve is no longer a clean envelope.

> [!question]- Check (slide 6): The slide goes from $y=u\otimes v'$ to $c=c_u+c_v$ in three steps. Say what each step does to the way the two parts are combined. Why does the last step not destroy the sum?
> Step 1, Fourier transform: convolution (evrişim) in time becomes a product, $Y=U\,V'$.
> Step 2, log of the magnitude: the product becomes a sum, $\log\lvert Y\rvert=\log\lvert U\rvert+\log\lvert V'\rvert$.
> Step 3, inverse Fourier transform: it is a linear operation, so the transform of a sum is the sum of the transforms. The sum stays a sum, $c=c_u+c_v$.
> After this, the two parts are added and sit in different places of the quefrency axis. An ordinary linear operation (a window) can separate them.

> [!question]- Check (slide 7): Find the mistake: "The cepstrum is a kind of spectrum, so quefrency is measured in Hz. A component at high quefrency is a high-frequency part of the speech signal."
> Two mistakes. Quefrency has units of time (samples or ms), because the cepstrum is an IDFT of a function of frequency.
> High quefrency does not mean high frequency of the signal. It means a fast ripple of the log spectrum along the frequency axis. A component at quefrency 5 ms is a ripple that repeats every $1/0.005=200$ Hz. That is the harmonic pattern of a 200 Hz voice.
> Low quefrency means a slow change of the log spectrum along frequency, the formant envelope.

## 3. Liftering and what it keeps (slides 8-11)
![[cs568_lecture_09_l9_p008.jpg]]
![[cs568_lecture_09_l9_p009.jpg]]
![[cs568_lecture_09_l9_p010.jpg]]
![[cs568_lecture_09_l9_p011.jpg]]

**Notes.** The cepstrum differs from DFT then IDFT in two ways: it drops the phase, and it takes the IDFT of the **log** magnitude. For the glottal excitation keep the high coefficients. For the vocal tract keep the low coefficients. Keeping the first $K$ coefficients gives a smoothed envelope; larger $K$ keeps more detail (slide 9, $K=10,20,30,50$).

Low-quefrency liftering in practice: the real cepstrum is even, $c[n]=c[N-n]$. To keep $K$ coefficients keep $c[0],\dots,c[K-1]$ and the mirror $c[N-K+1],\dots,c[N-1]$, zero the rest, and take the DFT. The cut-off must be below the pitch period.

Cepstral coefficients are compact and nearly uncorrelated. For statistical models we then store means and variances only, no covariances. That is why speech recognition uses them. MATLAB example: `ex9p1.m`.

> [!example] Lifter cut-off
> $F_0=100$ Hz at $f_s=16$ kHz: the pitch peak is at $16000/100=160$ samples. A cut-off of 30 to 50 samples keeps the envelope and removes the harmonics.

> [!question]- Check (slide 8): DFT then IDFT gives the signal back. Can you get $x[n]$ back from its (real) cepstrum $c[n]$? What can you get back?
> No. The cepstrum uses only $\lvert X\rvert$, so the phase is lost. Many different signals have the same magnitude spectrum and so the same real cepstrum.
> What comes back is the magnitude: the DFT of $c[n]$ is $\log\lvert X[k]\rvert$, and the exponential gives $\lvert X[k]\rvert$.
> This is enough for the two uses on the slide. The envelope (low coefficients) and the pitch period (position of the high-quefrency peak) do not need the phase.

> [!question]- Check (slide 9): The four curves are for $K=10,20,30,50$ kept coefficients ($f_s=16$ kHz, the axis ends at 8000 Hz). What changes from $K=10$ to $K=50$? What would the curve show for $K=150$ if the pitch period is 120 samples?
> $K=10$ is very smooth: only a few broad humps, for example one wide hump from about 2000 to 4000 Hz. $K=50$ shows more and narrower detail, for example a sharp peak near 200 to 300 Hz and several small bumps between 5000 and 7500 Hz. More coefficients means faster variations of the log spectrum are kept.
> The curves sit at different heights only so that they do not overlap.
> $K=50$ is still below the pitch period, so no harmonics appear. $K=150$ is above 120 samples: the pitch peak is kept, and the harmonic ripple returns on top of the envelope.

> [!question]- Check (slide 10): A model stores one Gaussian for a vector of 13 cepstral coefficients. How many numbers does it store with a full covariance (kovaryans) matrix, and how many if the coefficients are uncorrelated? Why does the slide care?
> Full covariance: 13 means and a symmetric $13\times13$ matrix with $13\cdot14/2=91$ free values, so $13+91=104$ numbers.
> Uncorrelated: the covariance matrix is diagonal. 13 means and 13 variances, so $26$ numbers.
> A speech recogniser has thousands of such Gaussians. Fewer parameters per Gaussian means less training data is needed and less computation. That is why nearly uncorrelated features are valuable.

## 4. Homomorphic filtering (slides 12-15)
![[cs568_lecture_09_l9_p012.jpg]]
![[cs568_lecture_09_l9_p013.jpg]]
![[cs568_lecture_09_l9_p014.jpg]]
![[cs568_lecture_09_l9_p015.jpg]]

**Notes.** A linear system obeys superposition: $L[x_1+x_2]=L[x_1]+L[x_2]$. A **homomorphic system for convolution** obeys the same rule with addition replaced by convolution:

$$H[x_1(n)*x_2(n)]=H[x_1(n)]*H[x_2(n)]$$

Every such system is a cascade of three:
1. The characteristic system $D_*$: $D_*[x_1*x_2]=\hat x_1+\hat x_2$ (convolution to addition).
2. An ordinary linear system $L$.
3. The inverse $D_*^{-1}$: addition back to convolution.

So the design reduces to designing a linear system. In the $z$ domain: $X(z)=X_1(z)X_2(z)$, so $\hat X(z)=\log X(z)=\log X_1(z)+\log X_2(z)$. The log plays $D_*$ and the exponential plays $D_*^{-1}$.

$D_*$ strictly defines the **complex cepstrum** $\hat x(n)$, which uses $\log|X|+j\angle X$ and is invertible. Speech processing uses the **real cepstrum** $c(n)$, which uses $\log|X|$ only. It is the even part: $c(n)=\frac12[\hat x(n)+\hat x(-n)]$.

Answer to the slide's question: liftering is the linear block $L$ in the middle.

> [!warning] Slide error
> Slide 13, second equation: the middle expression is printed as $D_*^{-1}[y_1(n)]*D_*^{-1}[y_2(n)]$. The inputs of $D_*^{-1}$ are the hatted signals. It should read $D_*^{-1}[\hat y_1(n)]*D_*^{-1}[\hat y_2(n)]=y_1(n)*y_2(n)$.

> [!info] Complex cepstrum, phase and inversion (beyond the slides)
> The complex log is $\log X=\log\lvert X\rvert+j\angle X$. The phase $\angle X$ is only known up to multiples of $2\pi$. A computer returns it between $-\pi$ and $\pi$, with jumps. For the sum rule $\log(X_1X_2)=\log X_1+\log X_2$ to hold, the phase must be a continuous curve, so the jumps must be removed first. This step is called phase unwrapping. It is numerically delicate.
> - Complex cepstrum $\hat x(n)$: keeps magnitude and phase, needs phase unwrapping, can be inverted back to $x(n)$ with $D_*^{-1}$.
> - Real cepstrum $c(n)$: keeps only $\log\lvert X\rvert$, needs no unwrapping, is robust, cannot be inverted back to $x(n)$.
> 
> For a minimum-phase (minimum fazlı) signal the complex cepstrum is zero for $n<0$. Then it can be built from the real cepstrum: $\hat x(0)=c(0)$ and $\hat x(n)=2c(n)$ for $n>0$. Compare "Check yourself" item 3: $c(1)=-0.45$ and $\hat x(1)=-0.9$.

> [!question]- Check (slide 12): A system doubles its input: $H[x(n)]=2x(n)$. Is it linear? Is it a homomorphic system for convolution?
> Linear: yes. $2(x_1+x_2)=2x_1+2x_2$.
> Homomorphic for convolution: no. The left side is $H[x_1*x_2]=2\,(x_1*x_2)$. The right side is $H[x_1]*H[x_2]=(2x_1)*(2x_2)=4\,(x_1*x_2)$. They are not equal.
> The two definitions have the same form, but the operation is different (addition against convolution). A system can satisfy one and fail the other.

> [!question]- Check (slide 13): In the bottom diagram, small signs ($*$ or $+$) stand at the input and output of each box. What do they say about $D_*$, $L$ and $D_*^{-1}$? Which box does the designer choose?
> The sign shows how the two parts of the signal are combined at that point.
> $D_*$: input $*$, output $+$. It turns $x_1*x_2$ into $\hat x_1+\hat x_2$.
> $L$: input $+$, output $+$. It is an ordinary linear system and keeps sums as sums.
> $D_*^{-1}$: input $+$, output $*$. It turns $\hat y_1+\hat y_2$ back into $y_1*y_2$.
> $D_*$ and $D_*^{-1}$ are fixed. Only $L$ is chosen, so the design of a homomorphic system is the design of a linear system.

> [!question]- Check (slide 14): In the diagram with $\log$ and $\exp$, what is the output if $L$ does nothing? What is the output if $L$ multiplies its input by 2?
> $L$ does nothing: $\hat Y(z)=\hat X(z)$, so $Y(z)=\exp(\log X(z))=X(z)$. The signal comes back unchanged.
> (The diagram prints $\hat Y(z)$ at the output of the $\exp$ box. It is $Y(z)$, as the product $Y_1(z)\cdot Y_2(z)$ under it shows.)
> $L$ multiplies by 2: $\hat Y(z)=2\log X(z)$, so $Y(z)=\exp(2\log X(z))=X(z)^2$. In time this is $y(n)=x(n)*x(n)$.
> A plain gain in the log domain is a convolution of the signal with itself. Operations in the log domain act on the convolved parts, not on the amplitude.

> [!question]- Check (slide 15): Name the three boxes inside $D_*$ in the top diagram. What must change to get the cepstrum of slide 2? Which of the two can be inverted by the bottom diagram?
> $D_*$ is: z-transform, $\log$, inverse z-transform. On the unit circle this is Fourier transform, complex log, inverse Fourier transform. The output $\hat x(n)$ is the complex cepstrum.
> Slide 2 uses $\log\lvert X\rvert$ in the middle box, so the phase is dropped. This gives the real cepstrum $c[n]$.
> Only the complex cepstrum can go through $D_*^{-1}$ (transform, $\exp$, inverse transform) and return $x(n)$. The real cepstrum returns only the magnitude spectrum.

## 5. Voicing and pitch detection (slides 16-17)
![[cs568_lecture_09_l9_p016.jpg]]
![[cs568_lecture_09_l9_p017.jpg]]

**Notes.** Slide 17: short-time log spectra and cepstra for 15 windows (50 ms window, 12.5 ms shift). Frames 1 to 5 are unvoiced, 8 to 15 voiced, 6 to 7 mixed. The voiced frames show a strong cepstral peak near 11 to 12 ms.

Simple detector: compute the cepstrum, find the largest peak in 3 to 20 ms, and if it passes a threshold declare the frame voiced with $F_0=1/\tau_0=f_s/n_{\text{peak}}$. A peak at 11.5 ms gives about 87 Hz. The range 3 to 20 ms covers 333 Hz down to 50 Hz.

> [!warning] One direction only
> A strong peak means voiced. No peak does **not** prove unvoiced: the window must hold at least 2 to 3 pitch periods, and $F_0$ may change inside the window.

> [!question]- Check (slide 16): The detector searches for a peak between 3 and 20 ms. For $f_s=10$ kHz, give the search range in samples. Why does the search not start at 0 ms? A peak is found at sample 115: what is $F_0$?
> Samples $=$ time $\times f_s$. Lower limit: $0.003\cdot10000=30$ samples. Upper limit: $0.020\cdot10000=200$ samples.
> Below about 3 ms the cepstrum holds the large vocal-tract values. They are there for voiced and unvoiced frames, so a "largest value" search from 0 would always pick them and say nothing about pitch.
> $F_0=f_s/n_{\text{peak}}=10000/115\approx87$ Hz.

> [!question]- Check (slide 17): Compare window 3 and window 12 in both panels. Then say what the red curves and the blue lines in the left panel show, and why the blue lines start only near window 6.
> Window 3 (unvoiced): the log spectrum on the left is irregular, with no regular ripple. The cepstrum on the right is flat after the first few ms.
> Window 12 (voiced): the log spectrum has a regular fine ripple (the harmonics). The cepstrum has one clear peak at about 11 to 12 ms. Both show the same fact: a ripple with spacing $F_0$ on the left is a peak at $1/F_0$ on the right. A peak at 12 ms means a spacing of about $1/0.012\approx83$ Hz.
> The red curves are the cepstrally smoothed spectra (low-quefrency liftering), the envelope of each frame. The blue lines join the envelope peaks from window to window: the formant tracks $F_1$, $F_2$, $F_3$. They start near window 6 because clear formant peaks appear only when voicing starts.

## 6. Linear prediction cepstral coefficients (slide 18)
![[cs568_lecture_09_l9_p018.jpg]]

**Notes.** LP coefficients are too sensitive to numerical precision. Convert them to cepstral coefficients (LPCC). For $H(z)=G/A_p(z)$, $A_p(z)=1-\sum_k a_kz^{-k}$:

$$c_n=\begin{cases}\ln G, & n=0\\ a_n+\dfrac1n\displaystyle\sum_{k=1}^{n-1}k\,c_k\,a_{n-k}, & 1\le n\le p\\ \dfrac1n\displaystyle\sum_{k=n-p}^{n-1}k\,c_k\,a_{n-k}, & n>p\end{cases}$$

> [!example] $p=1$, $a_1=0.9$
> $c_1=0.9$, $c_2=\frac12(1\cdot0.9\cdot0.9)=0.405$, $c_3=\frac13(2\cdot0.405\cdot0.9)=0.243$. Same as the series $a_1,\ a_1^2/2,\ a_1^3/3$.

> [!warning] Slide detail
> The slide gives the middle case for $1<n\le p$. It also holds for $n=1$ (empty sum, $c_1=a_1$). The third case ($n>p$) is not on the slide.

> [!info] What the LPCC looks like (beyond the slides)
> The LPCC is the cepstrum of the LP model $H(z)=G/A_p(z)$, not of the speech frame. The model holds only the envelope, so the LPCC has no pitch peak at any quefrency. No liftering is needed.
> The model is minimum phase (all poles inside the unit circle). For one pole at $z=a$ the cepstrum is $c_n=a^n/n$ for $n\ge1$. With several poles it is a sum of such terms. So the coefficients fall at least as fast as $1/n$, and the first 12 to 20 hold almost all of the envelope.

> [!question]- Check (slide 18): In the recursion, say what $a_n$, $G$, $c_0$ and $p$ are. Then compute $c_1$ and $c_2$ for $p=2$, $a_1=0.5$, $a_2=0.2$. Does an LPCC vector of a voiced frame show a pitch peak?
> $a_n$ are the LP (predictor) coefficients, $p$ is the LP order, $G$ is the gain of the model $H(z)=G/A_p(z)$. $c_0=\ln G$ holds only the level (energy) of the frame. The shape of the envelope is in $c_1,c_2,\dots$
> $c_1=a_1=0.5$ (the sum is empty).
> $c_2=a_2+\frac12\,(1\cdot c_1\cdot a_1)=0.2+\frac12(0.5\cdot0.5)=0.2+0.125=0.325$.
> No pitch peak. The LP model describes only the vocal-tract envelope. The excitation is left in the prediction error, so it never enters the recursion.

## 7. Mel frequency cepstral coefficients (slides 19-21)
![[cs568_lecture_09_l9_p019.jpg]]
![[cs568_lecture_09_l9_p020.jpg]]
![[cs568_lecture_09_l9_p021.jpg]]

**Notes.** MFCCs are the most common feature in speech recognition: a cepstrum on a perceptual frequency scale. Steps: STFT, group the DFT bins with triangular filters (constant bandwidth below 1 kHz, growing above), log, DCT.

Mel-frequency spectrum at frame $n$, filter $r$ (bins $L_r$ to $U_r$, triangular weights $V_r[k]$):

$$MF[r]=\frac{1}{A_r}\sum_{k=L_r}^{U_r}\big|V_r[k]\,X(n,k)\big|,\qquad A_r=\sum_{k=L_r}^{U_r}|V_r[k]|^2$$

$A_r$ normalises so that a flat spectrum gives a flat mel spectrum. Then

$$\mathrm{MFCC}[m]=\frac{1}{R}\sum_{r=1}^{R}\log\big(MF[r]\big)\cos\left[\frac{2\pi}{R}\left(r+\frac12\right)m\right]$$

Typical for $F_s=8$ kHz: $N_{\mathrm{MFCC}}=13$ coefficients, $R=22$ filters. Coefficient 0 is the mean log energy. A common mel formula: $\mathrm{mel}(f)=2595\log_{10}(1+f/700)$.

Typical front end: pre-emphasis, 20 to 30 ms Hamming frames every 10 ms, FFT magnitude, mel filterbank, log, DCT, keep about 13, often add deltas and delta-deltas ($3\times13=39$ values).

> [!warning] Formula variant
> The slide formula (Rabiner and Schafer) has $2\pi/R$ in the cosine. Most toolkits use the DCT-II with $\pi/R$. The idea is the same.

> [!warning] Slide detail: the normalisation on slide 20
> The slide sums $\lvert V_r[k]X(n,k)\rvert$ (no square) but defines $A_r$ with a square. The two formulas do not match each other (this may be only a choice of notation). Then "a flat spectrum gives a flat mel spectrum" is only roughly true. Take $\lvert X\rvert=1$. A narrow filter with weights $(0.5,\,1,\,0.5)$ gives $2/1.5=1.33$. A wider filter with weights $(\frac13,\frac23,1,\frac23,\frac13)$ gives $3/2.11=1.42$.
> With the squared magnitude in the sum, $\frac{1}{A_r}\sum\lvert V_r[k]X(n,k)\rvert^2$, both filters give exactly 1. This squared (power) form is the one that matches $A_r$.

> [!info] Reading the comparison on slide 21 (beyond the slides)
> The grey curve is the short-time spectrum of one voiced frame, with its harmonics. The three other curves are three ways to smooth it.
> - **LPC, $p=12$ (green)**: an all-pole model. It has sharp peaks at the formants (about 500, 1500, 2200 and 3100 Hz) and follows the tops of the harmonics. It does not go down into the valleys, because poles can make peaks but not deep dips.
> - **Homomorphic, $n_{co}=13$ (blue, dashed)**: low-quefrency liftering with cut-off quefrency $n_{co}=13$ samples. It is a smoothed copy of the log spectrum, so it runs through the middle of the ripple. Its peaks are lower and wider, and it is lower than the LPC curve almost everywhere.
> - **Mel cepstrum, $N_{\mathrm{mfcc}}=13$ (red dots)**: the smoothed mel spectrum, drawn at the centre frequencies of the mel filters. The dots are dense at low frequency and far apart at high frequency. So the resolution is fine at low frequency and coarse at high frequency.

> [!question]- Check (slide 19, figure): In the figure, how do the width and the height of the triangles change along the frequency axis, and why? Use $\mathrm{mel}(f)=2595\log_{10}(1+f/700)$ (a common formula, not on the slides) to compare the steps 1000 to 2000 Hz and 3000 to 4000 Hz in mel.
> Below 1000 Hz all triangles have the same width and the same height (0.01). Above 1000 Hz each triangle is wider than the one before, and lower. The last one covers about 3200 to 4000 Hz and its height is only about 0.0025.
> Width: the filters follow the critical bands of hearing. The ear resolves low frequencies finely and high frequencies coarsely. Height: a wide filter adds up more DFT bins, so its weights are scaled down. Then a flat spectrum gives the same output in every filter.
> $\mathrm{mel}(1000)=2595\log_{10}(2.429)=1000$, $\mathrm{mel}(2000)=2595\log_{10}(3.857)=1521$: a step of 521 mel.
> $\mathrm{mel}(3000)=2595\log_{10}(5.286)=1876$, $\mathrm{mel}(4000)=2595\log_{10}(6.714)=2146$: a step of 270 mel.
> The same 1000 Hz is about half as large in mel at the top of the band. So one filter (equal width in mel) must be about twice as wide in Hz there.

> [!question]- Check (slide 19, pipeline): Give the reason for each step of the MFCC front end: pre-emphasis, short Hamming frames, magnitude of the FFT, triangular mel filter bank (filtre bankası), log, DCT.
> The slide names only the STFT and the mel filters. Pre-emphasis and the frame values are beyond the slides.
> Pre-emphasis: voiced speech falls with frequency. A first-order high-pass lifts the high frequencies, so the weak upper formants are not lost.
> Frames of 20 to 30 ms with a Hamming window: speech is roughly stationary only over such a short time. The smooth window reduces leakage in the spectrum.
> FFT magnitude: gives the short-time spectrum. The phase is dropped, as in the real cepstrum.
> Mel filter bank: groups the bins on a perceptual scale and smooths the harmonics. About 20 to 40 numbers are left in place of hundreds of bins.
> Log: compresses the range like the ear, and turns source times filter (and a fixed channel) into a sum.
> DCT: takes the log filter outputs to the cepstral domain, decorrelates them and packs the envelope into the first coefficients.

> [!question]- Check (slide 20): In the two formulas, say what $r$, $R$, $V_r[k]$, $L_r$, $U_r$, $A_r$ and $m$ are. What does $\mathrm{MFCC}[0]$ give? Why is $N_{\mathrm{MFCC}}=13$ smaller than $R=22$?
> $r$ is the index of a mel filter and $R$ is the number of filters. $V_r[k]$ is the triangular weight of filter $r$ at DFT bin $k$. $L_r$ and $U_r$ are its first and last bin. $A_r$ is the normalisation of filter $r$. $m$ is the index of the cepstral coefficient (a quefrency index on the mel axis).
> For $m=0$ the cosine is 1, so $\mathrm{MFCC}[0]=\frac1R\sum_r\log(MF[r])$: the mean of the log filter outputs, a measure of the overall level of the frame.
> Low $m$ are slow cosines over the filter index: they describe the smooth envelope. High $m$ are fast cosines: they describe small differences between neighbouring filters, mostly remains of the harmonics and noise. Keeping 13 of 22 is a low-quefrency lifter and gives a short feature vector.

> [!question]- Check (slide 21): Which curve has the sharpest peaks, and which one lies lower between the peaks? Why are the red dots close together on the left and far apart on the right? What would the dashed curve do if $n_{co}$ were raised from 13 to 100?
> The LPC curve (green) has the sharpest and highest peaks, about 2.3 near 500 Hz against about 0.4 for the dashed curve. The homomorphic curve (dashed) is lower in the valleys, for example about $-1.5$ near 1000 Hz, where the LPC curve stays near $-0.5$.
> Reason: the all-pole model fits the peaks of the harmonics. Liftering averages the log spectrum, and the deep dips between harmonics pull the average down.
> The dots stand at the centres of the mel filters. These are dense at low frequency and sparse at high frequency.
> The axis ends at 4000 Hz, so $f_s=8$ kHz. Voiced speech has pitch periods of roughly 24 to 160 samples at this rate (3 to 20 ms). With $n_{co}=100$ the lifter would most likely keep the pitch peak, and the dashed curve would start to follow the grey harmonics.

## 8. MFCC notes: homomorphic or not, DCT (slides 22-24)
![[cs568_lecture_09_l9_p022.jpg]]
![[cs568_lecture_09_l9_p023.jpg]]
![[cs568_lecture_09_l9_p024.jpg]]

**Notes.** The MFCC is not exactly homomorphic, because it takes the log of a sum. It would be if it used

$$\frac{1}{A_r}\sum_{k=L_r}^{U_r}\log\big|V_r[k]X(n,k)\big|\quad\text{instead of}\quad\log\left(\frac{1}{A_r}\sum_{k=L_r}^{U_r}\big|V_r[k]X(n,k)\big|\right)$$

It is approximately homomorphic when the transfer function is smooth across each filter. Summing first makes the filter energies more robust to noise.

Why DCT and not IDFT: the DCT is close to the Karhunen-Loeve transform (the basis of PCA), so it decorrelates the log mel energies. It is real and compacts energy better. DCT-II:

$$X_{\mathrm{DCT}}(k)=\sum_{n=0}^{N-1}x[n]\cos\left[\frac{\pi}{N}\left(n+\frac12\right)k\right],\qquad X_{\mathrm{DFT}}(k)=\sum_{n=0}^{N-1}x[n]\,e^{-j\frac{2\pi}{N}kn}$$

MATLAB example: `ex9p2.m` (computing MFCCs).

> [!warning] Slide error
> The slide writes the DFT kernel as $e^{+j\frac{2\pi}{N}kn}$. That is the IDFT kernel (without $1/N$).

> [!info] Cepstral mean subtraction (beyond the slides)
> A fixed channel (a microphone, a telephone line) is one more filter convolved with the speech: $Y=X\,H_{ch}$. In the log domain it is an added term $\log\lvert H_{ch}\rvert$, and it is the same in every frame. So the channel adds one constant vector to the cepstral vectors of all frames.
> Cepstral mean subtraction: compute the mean of each cepstral coefficient over all frames of the utterance and subtract it from every frame. The constant channel term is removed. The parts of speech that change in time stay.
> This works exactly for a true cepstrum. For the MFCC it works approximately, under the same condition as on slide 22: the channel must be smooth across each mel filter.

> [!question]- Check (slide 22): One filter covers two bins with $\lvert X\rvert=1$ and $4$ (weights 1, mean in place of $1/A_r$). Compute the log of the mean and the mean of the logs. Then multiply the two bins by a channel with gains 2 and 0.5. Which of the two forms still gives "speech term plus channel term"?
> Log of the mean (MFCC form): $\ln\frac{1+4}{2}=\ln2.5=0.916$. Mean of the logs (homomorphic form): $\frac{\ln1+\ln4}{2}=0.693$.
> With the channel the bins are $2\cdot1=2$ and $0.5\cdot4=2$. The channel's own mean log is $\frac{\ln2+\ln0.5}{2}=0$.
> Mean of the logs: $\frac{\ln2+\ln2}{2}=0.693=0.693+0$. Speech term plus channel term: it separates.
> Log of the mean: $\ln\frac{2+2}{2}=0.693$, but speech plus channel would be $0.916+0=0.916$. It does not separate.
> If the channel gain is the same in both bins (say 2 and 2), the log of the mean gives $\ln5=1.609=0.916+\ln2$, and it separates again. This is the slide's condition: a transfer function that is smooth (nearly constant) across the filter.

> [!question]- Check (slide 23): For $x=(1,2,3,4)$ the slide's DCT-II gives $(10,\,-3.15,\,0,\,-0.22)$. The DFT magnitudes are $(10,\,2.83,\,2,\,2.83)$. Which property of the DCT does this show? Give the two other reasons the slide has for the DCT.
> Energy compaction. Sum of squares for the DCT: $100+9.95+0+0.05=110.0$, and the first two values hold $109.95/110.0\approx99.95\%$. For the DFT: $100+8+4+8=120$, and the first two hold $108/120=90\%$. The DCT puts a smooth sequence into fewer coefficients.
> So the first few DCT values of the log mel energies describe the envelope well, and the rest can be dropped.
> Other reasons: the DCT is real (no imaginary part to store). It is close to the Karhunen-Loeve transform, so it decorrelates the log mel energies, and diagonal covariances are enough in the models.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Cepstrum | $c[n]=\mathcal F^{-1}\{\log|\mathcal F\{x[n]\}|\}$; $c=c_u+c_v$ |
| Quefrency | independent variable of the cepstrum, units of time |
| Liftering | keep low $n$ (and the mirror half) for the envelope; cut-off below $T_0=f_s/F_0$ |
| Homomorphic system | $D_*$, linear $L$, $D_*^{-1}$; liftering is $L$ |
| Real vs complex cepstrum | real uses $\log|X|$; it is the even part of the complex cepstrum |
| Cepstral pitch | peak at $\tau_0$ in 3 to 20 ms means voiced, $F_0=1/\tau_0$ |
| LPCC | $c_0=\ln G$, $c_n=a_n+\frac1n\sum_{k=1}^{n-1}k\,c_k\,a_{n-k}$ for $n\le p$ |
| Mel scale | $\mathrm{mel}(f)=2595\log_{10}(1+f/700)$ |
| MFCC | STFT, triangular mel filterbank, log, DCT; 13 coefficients, 22 filters at 8 kHz |

## Check yourself
> [!question]- 1. A cepstral peak is at quefrency 80 samples with $f_s=8$ kHz. $T_0$ and $F_0$?
> $T_0=80/8000=10$ ms, $F_0=100$ Hz.

> [!question]- 2. $N=512$, keep $K=20$ coefficients. Which indices does the low-quefrency lifter keep?
> $c[0],\dots,c[19]$ and the mirror $c[493],\dots,c[511]$ ($N-K+1=493$). All others are set to 0.

> [!question]- 3. Complex and real cepstrum of $x(n)=\delta(n)-0.9\,\delta(n-1)$ at $n=1,2$?
> $\log(1-0.9z^{-1})=-0.9z^{-1}-0.405z^{-2}-\dots$, so $\hat x(1)=-0.9$, $\hat x(2)=-0.405$. Real cepstrum is the even part: $c(1)=-0.45$, $c(2)=-0.2025$.

> [!question]- 4. A speaker has $F_0=80$ Hz. Is a 50 ms window long enough for cepstral pitch detection?
> $T_0=12.5$ ms. Two to three periods need 25 to 37.5 ms. The 50 ms window holds 4 periods, so yes.

> [!question]- 5. LPCC recursion with $p=2$, $a_1=1$, $a_2=-0.5$: find $c_1,c_2,c_3$.
> $c_1=a_1=1$. $c_2=a_2+\frac12(1\cdot c_1a_1)=-0.5+0.5=0$. $c_3=\frac13(1\cdot c_1a_2+2\cdot c_2a_1)=\frac13(-0.5)=-0.167$.

> [!question]- 6. Convert 700 Hz to mel. How long is a feature vector of 13 MFCCs with deltas and delta-deltas?
> $2595\log_{10}(2)=781$ mel. Vector length $13+13+13=39$.

## Links
- Course index: [[CS568 Home]]
- Previous lecture: [[CS568 L08 - Source estimation]]
- Full step-by-step notes (PDF): [CS568_Lecture_09_Cepstral_Analysis.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_09/CS568_Lecture_09_Cepstral_Analysis.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_09/CS568_Lecture_09_Cepstral_Analysis.pdf`
- Original slides (PDF): [l9.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_09/l9.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_09/l9.pdf`
- Quiz: [[CS568 L09 - Cepstral analysis - quiz]]

Slides not embedded: 1
