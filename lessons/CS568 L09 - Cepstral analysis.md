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

## 5. Voicing and pitch detection (slides 16-17)
![[cs568_lecture_09_l9_p016.jpg]]
![[cs568_lecture_09_l9_p017.jpg]]

**Notes.** Slide 17: short-time log spectra and cepstra for 15 windows (50 ms window, 12.5 ms shift). Frames 1 to 5 are unvoiced, 8 to 15 voiced, 6 to 7 mixed. The voiced frames show a strong cepstral peak near 11 to 12 ms.

Simple detector: compute the cepstrum, find the largest peak in 3 to 20 ms, and if it passes a threshold declare the frame voiced with $F_0=1/\tau_0=f_s/n_{\text{peak}}$. A peak at 11.5 ms gives about 87 Hz. The range 3 to 20 ms covers 333 Hz down to 50 Hz.

> [!warning] One direction only
> A strong peak means voiced. No peak does **not** prove unvoiced: the window must hold at least 2 to 3 pitch periods, and $F_0$ may change inside the window.

## 6. Linear prediction cepstral coefficients (slide 18)
![[cs568_lecture_09_l9_p018.jpg]]

**Notes.** LP coefficients are too sensitive to numerical precision. Convert them to cepstral coefficients (LPCC). For $H(z)=G/A_p(z)$, $A_p(z)=1-\sum_k a_kz^{-k}$:

$$c_n=\begin{cases}\ln G, & n=0\\ a_n+\dfrac1n\displaystyle\sum_{k=1}^{n-1}k\,c_k\,a_{n-k}, & 1\le n\le p\\ \dfrac1n\displaystyle\sum_{k=n-p}^{n-1}k\,c_k\,a_{n-k}, & n>p\end{cases}$$

> [!example] $p=1$, $a_1=0.9$
> $c_1=0.9$, $c_2=\frac12(1\cdot0.9\cdot0.9)=0.405$, $c_3=\frac13(2\cdot0.405\cdot0.9)=0.243$. Same as the series $a_1,\ a_1^2/2,\ a_1^3/3$.

> [!warning] Slide detail
> The slide gives the middle case for $1<n\le p$. It also holds for $n=1$ (empty sum, $c_1=a_1$). The third case ($n>p$) is not on the slide.

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

Slides not embedded: 1
