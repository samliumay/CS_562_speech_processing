---
course: CS 568 Speech Processing
lecture: 7
title: Linear prediction of speech
tags: [cs568, lecture]
slides: lectures/lecture_07/l7.pdf
full_notes: lecture_notes/lecture_07/CS568_Lecture_07_Linear_Prediction.pdf
---
# CS 568 · Lecture 7 · Linear prediction of speech

> [!summary] In short
> - LP model: $y[n]=e[n]+\sum_{k=1}^{p}a_k\,y[n-k]$. Each sample is predicted from the last $p$ samples. The miss $e[n]$ is the excitation.
> - $1/A_p(z)$ (synthesis filter) turns excitation into speech. $A_p(z)$ (inverse filter) turns speech back into the residual.
> - Minimising the squared error gives $p$ linear equations. Covariance method: no window, Cholesky, $O(p^3)$. Autocorrelation method: Toeplitz, Levinson-Durbin, $O(p^2)$, always stable.
> - The response of $G/A_p(z)$ is the spectral envelope: formants without pitch harmonics. Order $p\approx4+f_s/1000$.
> - The same filter can be stored as pole pairs, line spectral frequencies, reflection coefficients or log-area ratios.

## 1. Speech production and the two kinds of peaks (slides 2-3)
![[cs568_lecture_07_l7_p002.jpg]]
![[cs568_lecture_07_l7_p003.jpg]]

**Notes.** Speech is an excitation shaped by resonances. The excitation is glottal pulses (voiced, with fundamental frequency $F_0$), continuous air flow (unvoiced), or both. The resonances of the vocal, nasal and pharyngeal tracts are the **formants**.

In the spectrum of one frame there are two kinds of peaks: narrow peaks every $F_0$ (the harmonics) and wide peaks (the formants, the spectral envelope). Linear prediction models the wide peaks.

> [!example] Counting peaks
> $F_0=200$ Hz, $f_s=8$ kHz: harmonics at $200m\le4000$ Hz, so 20 narrow peaks. About one formant per kHz, so about 4 wide peaks.

## 2. The LP model and the inverse filter (slides 4-7)
![[cs568_lecture_07_l7_p004.jpg]]
![[cs568_lecture_07_l7_p005.jpg]]
![[cs568_lecture_07_l7_p006.jpg]]
![[cs568_lecture_07_l7_p007.jpg]]

**Notes.** Source-filter model (Fant, 1960): glottis and vocal tract are uncoupled. Speech $y[n]$ is the output of an all-pole filter excited by $x[n]$:

$$Y(z)=X(z)\,\frac{1}{1-\sum_{k=1}^{p}a_kz^{-k}}=X(z)\,\frac{1}{A_p(z)}$$

The $a_k$ are the **LP coefficients**, $p$ is the **prediction order**. $1/A_p(z)$ is the **synthesis filter**, $A_p(z)$ the **inverse filter**. The excitation is a pulse train (period $T_0$, amplitude $\sigma$) or white Gaussian noise (variance $\sigma^2$).

In time:

$$y[n]=x[n]+\sum_{k=1}^{p}a_k\,y[n-k]$$

Each sample is a weighted sum of the $p$ previous samples plus an excitation term. That term is called the **error** or **residual** $e[n]$. Given the speech and the $a_k$, the residual is the output of the inverse filter:

$$e[n]=y[n]-\sum_{k=1}^{p}a_k\,y[n-k]$$

It should look like pulses for voiced speech and like white noise for unvoiced speech.

> [!example] Residual by hand
> $p=2$, $a_1=1$, $a_2=-0.5$, $y=(1,2,1)$: $e[n]=y[n]-y[n-1]+0.5\,y[n-2]$ gives $e=(1,\ 1,\ -0.5,\ 0,\ 0.5,\ 0)$.

> [!warning] Watch out
> Slide 7 writes "for a given speech signal $x[n]$". The speech is $y[n]$, $x[n]$ is the excitation. Sign convention here: $A_p(z)=1-\sum_ka_kz^{-k}$. Other books use a plus.

## 3. Finding the coefficients: covariance method (slides 8-11)
![[cs568_lecture_07_l7_p008.jpg]]
![[cs568_lecture_07_l7_p009.jpg]]
![[cs568_lecture_07_l7_p010.jpg]]
![[cs568_lecture_07_l7_p011.jpg]]

**Notes.** Choose the $a_k$ that minimise the energy of the residual over a frame of $N$ samples:

$$E=\sum_{n=0}^{N-1}e^2[n]=\sum_{n=0}^{N-1}\left(y[n]-\sum_{k=1}^{p}a_k\,y[n-k]\right)^{2}$$

Setting $\partial E/\partial a_j=0$ for $j=1,\dots,p$ gives the **normal equations**:

$$\sum_{n=0}^{N-1}y[n]\,y[n-j]=\sum_{k=1}^{p}a_k\sum_{n=0}^{N-1}y[n-k]\,y[n-j]$$

With $\phi(j,k)=\sum_{n=0}^{N-1}y[n-j]\,y[n-k]$ this is

$$\phi(j,0)=\sum_{k=1}^{p}\phi(j,k)\,a_k,\qquad j=1,\dots,p$$

or $\boldsymbol\Phi=\boldsymbol\Psi\,\mathbf a$ in matrix form. The matrix $\boldsymbol\Psi$ is symmetric, so the system is solved with the Cholesky decomposition in $O(p^3)$.

Properties: the error is computed over $0\le n\le N-1$ but uses samples $-p\le n\le N-1$ ($N+p$ samples). No window is needed. If the signal really is all-pole, the solution is exact. Stability of $1/A_p(z)$ is not guaranteed.

> [!warning] Slide errors
> - Slide 9 has a factor 2 on the right-hand side of the final equation. Both terms carry the 2, so it cancels.
> - Slide 10 says "$\Phi$ is symmetric". The symmetric object is the matrix $\Psi$. $\Phi$ is a vector.

## 4. Autocorrelation method and Levinson-Durbin (slides 12-13)
![[cs568_lecture_07_l7_p012.jpg]]
![[cs568_lecture_07_l7_p013.jpg]]

**Notes.** Window the frame (for example Hann), so every value outside $0\le n<N$ is zero. Then the sums can run over all $n$ and $\phi(j,k)$ depends only on $j-k$:

$$\phi(j,k)=\sum_{n=0}^{N-1-(j-k)}y[n]\,y[n+j-k]=R(j-k),\qquad R(n)=\sum_{m}y[m]\,y[m+n]$$

The normal equations become the **Yule-Walker equations**:

$$R(j)=\sum_{k=1}^{p}R(j-k)\,a_k\qquad\Longleftrightarrow\qquad
\begin{bmatrix}R(1)\\R(2)\\\vdots\\R(p)\end{bmatrix}=
\begin{bmatrix}R(0)&R(1)&\cdots&R(p-1)\\R(1)&R(0)&\cdots&R(p-2)\\\vdots&\vdots&\ddots&\vdots\\R(p-1)&R(p-2)&\cdots&R(0)\end{bmatrix}
\begin{bmatrix}a_1\\a_2\\\vdots\\a_p\end{bmatrix}$$

The matrix is **Toeplitz** (symmetric, equal elements on each diagonal). The **Levinson-Durbin recursion** solves it in $O(p^2)$. Start with $E^{(0)}=R(0)$. For $i=1,\dots,p$:

$$k_i=\frac{R(i)-\sum_{j=1}^{i-1}a_j^{(i-1)}R(i-j)}{E^{(i-1)}},\qquad a_i^{(i)}=k_i,\qquad a_j^{(i)}=a_j^{(i-1)}-k_i\,a_{i-j}^{(i-1)},\qquad E^{(i)}=(1-k_i^2)\,E^{(i-1)}$$

By-products: the **reflection coefficients** $k_i$ and the error energy of every order. For the autocorrelation method $|k_i|<1$ always, so the synthesis filter is stable.

> [!example] Levinson-Durbin, $p=2$
> $R(0)=1$, $R(1)=0.8$, $R(2)=0.5$. Order 1: $k_1=0.8$, $E^{(1)}=0.36$. Order 2: $k_2=(0.5-0.64)/0.36=-7/18\approx-0.389$, $a_2=-0.389$, $a_1=0.8-(-0.389)(0.8)=1.111$, $E^{(2)}=11/36\approx0.306$.

| | Covariance | Autocorrelation |
|---|---|---|
| Window | none | yes |
| Matrix | symmetric $\phi(j,k)$ | symmetric Toeplitz $R(j-k)$ |
| Solver | Cholesky, $O(p^3)$ | Levinson-Durbin, $O(p^2)$ |
| Exact for an all-pole signal | yes | no, biased by the window |
| Stable $1/A_p(z)$ | not guaranteed | guaranteed |

> [!warning] Slide error
> Slide 12 writes $R(n)=\sum_my[m]\,y[n-m]$. That is the convolution of $y$ with itself. The autocorrelation shifts without flipping: $\sum_my[m]\,y[m+n]$. Also, the final $a_j$ are not the $k_j$ in general (above $a_1=1.111$, $k_1=0.8$).

## 5. Spectral envelope and model order (slides 14-18)
![[cs568_lecture_07_l7_p014.jpg]]
![[cs568_lecture_07_l7_p015.jpg]]
![[cs568_lecture_07_l7_p016.jpg]]
![[cs568_lecture_07_l7_p017.jpg]]
![[cs568_lecture_07_l7_p018.jpg]]

**Notes.** Evaluate the filter on the unit circle at angles $2\pi f/f_s$, with a gain $G$:

$$\left|H\!\left(e^{j2\pi f/f_s}\right)\right|^2=\left|\frac{G}{1-\sum_{k=1}^{p}a_k\,e^{-j2\pi kf/f_s}}\right|^2,\qquad G^2=E^{(p)}=R(0)-\sum_{k=1}^{p}a_kR(k)$$

The all-pole filter models the vocal-tract resonances. The glottal excitation stays in the residual. So this response is smooth and free of pitch harmonics: the **spectral envelope**.

How many coefficients? Slide 16: at $p=12$ the envelope follows the broad peaks. At $p=40$ it also follows the harmonics. The error curve flattens above about $p=12$. Counting poles: about one resonance per kHz, one complex pole pair each, plus 1 to 2 pole pairs for the glottal spectrum and lip radiation (slide 17). Rule of thumb, $f_s$ in Hz:

$$p=4+\frac{f_s}{1000}$$

That is 10 to 12 coefficients at $f_s=8$ kHz.

> [!warning] Slide error
> Slide 15 says "the broad spectral peaks (i.e. the harmonics)". The broad peaks are the formants.

## 6. Alternative representations: root pairs and LSF (slides 19-22)
![[cs568_lecture_07_l7_p019.jpg]]
![[cs568_lecture_07_l7_p020.jpg]]
![[cs568_lecture_07_l7_p021.jpg]]
![[cs568_lecture_07_l7_p022.jpg]]

**Notes.** The $a_k$ are hard to interpret and sensitive to numerical precision. Equivalent forms are used instead.

**Root pairs.** Factor $A_p(z)$. Each complex pair of roots (poles of the LP filter) is one resonance. For a pole $z_i=\rho_ie^{j\theta_i}$:

$$F_i=\frac{\theta_i}{2\pi}\,f_s,\qquad B_i\approx-\frac{f_s}{\pi}\ln\rho_i$$

Slide 21: in a 12th-order model, 4 pairs are close to the unit circle (formants) and 2 pairs lie well inside (overall spectral shaping from glottis and radiation).

**Line spectral frequencies (LSF).** Build the line spectrum pair polynomials

$$P(z)=A(z)+z^{-(p+1)}A(z^{-1}),\qquad Q(z)=A(z)-z^{-(p+1)}A(z^{-1})$$

All roots of $P$ and $Q$ lie on the unit circle. Their angles are the LSFs. Two close LSFs indicate a strong resonance. LSFs are stable and not very sensitive to quantisation, so they are widely used to quantise LP filters.

> [!example] Poles of the example filter
> $a_1=10/9$, $a_2=-7/18$, $f_s=8$ kHz: poles $0.556\pm0.283j$, $\rho\approx0.624$, $\theta\approx0.4715$ rad, so $F\approx600$ Hz and $B\approx1203$ Hz. A broad bump, not a sharp formant.

> [!warning] Slide error
> Slide 22 says $P$ and $Q$ add up to $A_p(z)$. The sum is $2A_p(z)$, so $A_p(z)=\tfrac12[P(z)+Q(z)]$.

## 7. Reflection coefficients and log-area ratios (slides 23-24)
![[cs568_lecture_07_l7_p023.jpg]]
![[cs568_lecture_07_l7_p024.jpg]]

**Notes.** **Reflection coefficients** give the fraction of energy reflected at each section of a non-uniform tube model of the vocal tract ([[CS568 L02 - Speech production and perception]]). They come for free from Levinson-Durbin, are robust to quantisation, and can be interpolated. From the $a_k$, by backward recursion: start with $a_i^{(p)}=a_i$, then for $i=p,\dots,1$

$$r_i=a_i^{(i)},\qquad a_j^{(i-1)}=\frac{a_j^{(i)}+a_i^{(i)}\,a_{i-j}^{(i)}}{1-r_i^2},\quad 1\le j<i$$

The synthesis filter is stable if and only if $|r_i|<1$ for every $i$.

**Log-area ratios (LAR)** are the natural log of the ratio of the areas of adjacent tube sections:

$$g_k=\ln\!\left(\frac{1-r_k}{1+r_k}\right)$$

Only the ratios of areas can be found, not the absolute areas.

> [!warning] Watch out
> Slide 24 writes the left-hand side as $A_k$ but calls it $g_k$ in the text. Use $g_k$, to avoid a clash with $A_p(z)$.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| LP model | $y[n]=e[n]+\sum_{k=1}^{p}a_ky[n-k]$ |
| Inverse filter | $A_p(z)=1-\sum_ka_kz^{-k}$. Output for speech input: the residual $e[n]$ |
| Synthesis filter | $1/A_p(z)$, all-pole |
| Covariance method | $\phi(j,0)=\sum_k\phi(j,k)a_k$, Cholesky $O(p^3)$ |
| Autocorrelation method | $R(j)=\sum_kR(j-k)a_k$ (Yule-Walker, Toeplitz), stable |
| Levinson-Durbin | $k_i$, $a_j^{(i)}=a_j^{(i-1)}-k_ia_{i-j}^{(i-1)}$, $E^{(i)}=(1-k_i^2)E^{(i-1)}$, $O(p^2)$ |
| Spectral envelope | $\lvert H\rvert^2=G^2/\lvert A_p(e^{j2\pi f/f_s})\rvert^2$, $G^2=R(0)-\sum_ka_kR(k)$ |
| Order | $p=4+f_s/1000$ |
| Pole to formant | $F=\theta f_s/(2\pi)$, $B\approx-(f_s/\pi)\ln\rho$ |
| LSF | angles of the roots of $P$, $Q$ on the unit circle |
| Reflection coefficient | $r_i=a_i^{(i)}$. Stable iff all $\lvert r_i\rvert<1$ |
| LAR | $g_k=\ln\frac{1-r_k}{1+r_k}$ |

## Check yourself
> [!question]- 1. For $p=1$, $R(0)=2$ and $R(1)=1.5$. Find $a_1$ and $E^{(1)}$.
> $a_1=k_1=1.5/2=0.75$. $E^{(1)}=(1-0.5625)\cdot2=0.875$.

> [!question]- 2. A covariance analysis with $p=10$ computes the error over $0\le n\le239$. Which samples of $y$ are used?
> $y[-10]$ to $y[239]$: $N+p=250$ samples.

> [!question]- 3. Which LP order does the rule of thumb give at $f_s=10$ kHz?
> $p=4+10=14$: about 5 formants in 0 to 5 kHz (10 poles) plus 4 poles for the spectral tilt.

> [!question]- 4. At $f_s=8$ kHz a pole pair has $\rho=0.9$, $\theta=\pi/4$. Find $F$ and $B$.
> $F=\frac{\pi/4}{2\pi}\cdot8000=1000$ Hz. $B\approx-\frac{8000}{\pi}\ln0.9=2546.5\times0.1054\approx268$ Hz.

> [!question]- 5. A coder transmits $r_1=0.95$, $r_2=1.02$. Is the synthesis filter stable?
> No: $|r_2|>1$. It cannot come from a correct autocorrelation analysis, because $E^{(2)}=(1-r_2^2)E^{(1)}$ would be negative.

> [!question]- 6. Compute the LAR for $r=0$. What does it mean?
> $g=\ln1=0$: the two adjacent areas are equal, nothing is reflected.

## Links
- Course index: [[CS568 Home]]
- Previous: [[CS568 L06 - Short-time Fourier analysis]]
- Background: [[CS568 L05 - Digital filters]] (poles, all-pole filters), [[CS568 L02 - Speech production and perception]] (tube model, reflection coefficients)
- Full step-by-step notes (PDF): [CS568_Lecture_07_Linear_Prediction.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_07/CS568_Lecture_07_Linear_Prediction.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_07/CS568_Lecture_07_Linear_Prediction.pdf`
- Original slides (PDF): [l7.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_07/l7.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_07/l7.pdf`

Slides not embedded: 1 (outline).
