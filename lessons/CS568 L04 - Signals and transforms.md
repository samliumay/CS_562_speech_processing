---
course: CS 568 Speech Processing
lecture: 4
title: Signals and transforms
tags: [cs568, lecture]
slides: lectures/lecture_04/l4.pdf
full_notes: lecture_notes/lecture_04/CS568_Lecture_04_Signals_Transforms.pdf
---
# CS 568 · Lecture 4 · Signals and transforms

> [!summary] In short
> - A sinusoid is $A\cos(\omega t+\varphi)$. Any periodic signal is a sum of harmonics of $F_0$ (Fourier series).
> - The ear mostly hears the magnitude spectrum. Phase shapes the waveform.
> - Sampling at $F_S$ keeps frequencies up to $F_S/2$ (Nyquist). Anything above folds to $|F-mF_S|$ and cannot be undone.
> - DFT: $N$ samples give $N$ bins at $kF_S/N$. Resolution is $1/\text{recording length}$. The FFT computes it in $O(N\log_2N)$.
> - $z$-transform: signals become polynomials in $z^{-1}$. On the unit circle it is the DTFT.
> - Convolution in time is multiplication in frequency: this is the source-filter model.

## 1. Analog signals and sinusoids (slides 2-4)
![[cs568_lecture_04_l4_p002.jpg]]
![[cs568_lecture_04_l4_p003.jpg]]
![[cs568_lecture_04_l4_p004.jpg]]

**Notes.** A **signal** is a pattern of variation that carries information. A signal that varies continuously in time is **analog**, written $x(t)$. Voiced speech is quasi-periodic: it almost repeats.

The **period** $T$ is the time between two repetitions (s). The **frequency** is $F=1/T$ (Hz). A sinusoid has three parameters:

$$x(t)=A\cos(\omega t+\varphi),\qquad \omega=2\pi F$$

$A$ is the amplitude, $\omega$ the angular frequency (rad/s), $\varphi$ the phase (rad). Sine and cosine differ only by a phase shift: $\sin t=\cos(t-\pi/2)$.

## 2. Fourier series (slides 5-6)
![[cs568_lecture_04_l4_p005.jpg]]
![[cs568_lecture_04_l4_p006.jpg]]

**Notes.** A signal is periodic if $x(t)=x(t+nT)$. The smallest such $T$ is the fundamental period $T_0$, and $F_0=1/T_0$ is the fundamental frequency. Harmonics are the integer multiples $2F_0,3F_0,\dots$

Any periodic signal is a sum of sinusoids at the harmonics, with $\omega_0=2\pi F_0$:

$$x(t)=A_0+\sum_{k=1}^{\infty}a_k\cos(k\omega_0t+\varphi_k)$$

Square wave (slide 6): only odd harmonics, each with phase $-\pi/2$ and amplitude $a_k=4/(k\pi)$.

> [!warning] Watch out
> Cutting the series after finitely many terms overshoots next to a jump by about 9% of the jump height (Gibbs). More terms make the overshoot narrower, not smaller: the peak stalls near 1.179.

## 3. Complex exponentials and Fourier analysis (slides 7-9)
![[cs568_lecture_04_l4_p007.jpg]]
![[cs568_lecture_04_l4_p008.jpg]]
![[cs568_lecture_04_l4_p009.jpg]]

**Notes.** Euler's formula, with $j=\sqrt{-1}$:

$$e^{j\theta}=\cos\theta+j\sin\theta,\qquad \cos\theta=\frac{e^{j\theta}+e^{-j\theta}}{2},\qquad \sin\theta=\frac{e^{j\theta}-e^{-j\theta}}{2j}$$

A sinusoid becomes $x(t)=Xe^{j\omega t}$ with the **complex amplitude** $X=Ae^{j\varphi}$ (amplitude and phase in one number). The complex Fourier series and its analysis equation:

$$x(t)=\sum_{k=-\infty}^{\infty}a_ke^{jk\omega_0t},\qquad a_k=\frac{1}{T_0}\int_0^{T_0}x(t)\,e^{-jk\omega_0t}\,dt$$

The analysis equation multiplies the signal by a probe and averages over one period. For $x(t)=b_ne^{jn\omega_0t}$ it gives $a_k=b_n$ if $k=n$ and 0 otherwise. The set of $a_k$ is the **spectrum**. For $k\ge1$ the real amplitude is twice the magnitude of the complex coefficient.

> [!warning] Slide errors
> - Slide 8 writes the symmetry of real signals as "$a_k=-a_k$". Correct: $a_{-k}=a_k^{*}$ (same magnitude, opposite phase).
> - Slide 9 writes the vanishing integral with exponent $k\omega_0t$. It should be $(n-k)\omega_0t$. The conclusion is the same.

## 4. Magnitude and phase (slides 10-11)
![[cs568_lecture_04_l4_p010.jpg]]
![[cs568_lecture_04_l4_p011.jpg]]

**Notes.** The ear is largely insensitive to phase, so one studies the **magnitude spectrum**. A signal with the magnitude spectrum of a square wave and arbitrary phases sounds like the square wave, but its waveform looks very different. Slide 10 says "see below" but has no figure. Demo 3 of the script supplies it.

> [!warning] Watch out
> Same magnitude spectrum does not mean same waveform. In demo 3 the peak is about 1.18 for the square wave, 3.32 with all phases 0, and 1.66 with random phases.

## 5. The Fourier transform (slides 12-13)
![[cs568_lecture_04_l4_p012.jpg]]
![[cs568_lecture_04_l4_p013.jpg]]

**Notes.** For non-periodic signals the sum over harmonics becomes an integral over all frequencies. The spectrum is a continuous function.

$$X(j\omega)=\int_{-\infty}^{\infty}x(t)\,e^{-j\omega t}\,dt,\qquad x(t)=\frac{1}{2\pi}\int_{-\infty}^{\infty}X(j\omega)\,e^{j\omega t}\,d\omega$$

Slide 13: the transform works like a prism that splits sound into its frequencies.

> [!warning] Slide error
> Slide 12 writes the inverse as $\int X(j\omega)e^{-j\omega t}\,dt$. The inverse needs $e^{+j\omega t}$, integration over $\omega$ and the factor $1/(2\pi)$.

## 6. Digital signals and aliasing (slides 14-16)
![[cs568_lecture_04_l4_p014.jpg]]
![[cs568_lecture_04_l4_p015.jpg]]
![[cs568_lecture_04_l4_p016.jpg]]

**Notes.** A **digital signal** $x[n]$ is a sequence of samples, $x[n]=x(nT_S)$. $T_S$ is the sampling period and $F_S=1/T_S$ the sampling rate. The highest frequency $x[n]$ can represent is the **Nyquist frequency** $F_S/2$. With 16 bits there are $2^{16}$ values, from $-32768$ to $32767$.

**Normalised frequency** (radians per sample):

$$\hat\omega=\frac{\omega}{F_S}=\frac{2\pi F}{F_S}$$

Nyquist maps to $\hat\omega=\pi$, the sampling frequency to $2\pi$.

**Aliasing:** a component above $F_S/2$ appears mirrored inside the Nyquist range, at $|F-mF_S|$ with $m$ the integer nearest to $F/F_S$.

> [!example] Slide 15, $F_S=100$ Hz
> 25 Hz stays. 70 Hz appears at $|70-100|=30$ Hz. 160 Hz at $|160-200|=40$ Hz. 510 Hz at $|510-500|=10$ Hz.

> [!warning] Watch out
> Aliasing cannot be undone after sampling. Put an anti-aliasing low-pass filter before the sampler. Sampling at exactly $2f$ can hit every zero crossing, so $F_S$ must be above $2F_{\max}$.

## 7. DTFT and DFT (slides 17-19)
![[cs568_lecture_04_l4_p017.jpg]]
![[cs568_lecture_04_l4_p018.jpg]]
![[cs568_lecture_04_l4_p019.jpg]]

**Notes.** Replace the Fourier integral by rectangles of width $T_S$, use $\hat\omega=\omega T_S$ and drop the factor $T_S$. This gives the **DTFT**, discrete in time and continuous in frequency:

$$X(e^{j\hat\omega})=\sum_{n=-\infty}^{\infty}x[n]\,e^{-j\hat\omega n}$$

Sampling it at $N$ frequencies $\hat\omega_k=2\pi k/N$ (that is $F_k=kF_S/N$ Hz) and summing over $N$ samples gives the **DFT**:

$$X[k]=\sum_{n=0}^{N-1}x[n]\,e^{-j\frac{2\pi}{N}kn},\qquad x[n]=\frac{1}{N}\sum_{k=0}^{N-1}X[k]\,e^{j\frac{2\pi}{N}kn}$$

Each $X[k]$ is the inner product of the signal with a complex sinusoid: a complex number giving magnitude and phase at that frequency. $N$ samples give $N$ bins, so a longer recording gives finer resolution. With speech the length is limited, because the signal is not stationary.

Inverse DTFT: $x[n]=\frac{1}{2\pi}\int_{-\pi}^{\pi}X(e^{j\hat\omega})e^{j\hat\omega n}\,d\hat\omega$.

> [!warning] Slide errors
> - Slide 18 writes "$\omega_k=2\pi F_S/N$". That is the spacing between bins. The $k$-th frequency is $2\pi kF_S/N$.
> - Slide 19: the inverse DTFT is over one period $[-\pi,\pi]$, not $(-\infty,\infty)$. The inverse DFT sums over $k$, not $n$.

## 8. The DFT as a matrix, examples, FFT (slides 20-26)
![[cs568_lecture_04_l4_p020.jpg]]
![[cs568_lecture_04_l4_p021.jpg]]
![[cs568_lecture_04_l4_p022.jpg]]
![[cs568_lecture_04_l4_p023.jpg]]
![[cs568_lecture_04_l4_p024.jpg]]
![[cs568_lecture_04_l4_p025.jpg]]
![[cs568_lecture_04_l4_p026.jpg]]

**Notes.** With $W_N=e^{-j2\pi/N}$ the DFT is $X[k]=\sum_n x[n]W_N^{kn}$: a matrix with entries $W_N^{kn}$ times the signal vector. Row $k$ is a unit vector rotating clockwise in steps of $2\pi k/N$, that is a cosine (real part) and a negative sine (imaginary part) with $k$ cycles. Rows $k$ and $N-k$ are complex conjugates.

> [!example] Slides 23-24, $F_S=2$ kHz, 1 s, $N=2000$
> Bin spacing $F_S/N=1$ Hz. $\sin(2\pi10t)$ gives a peak at bin 10 with $|X[10]|=N/2=1000$ and a mirror peak at bin 1990 (symmetry around $F_S/2$). $10\sin(2\pi10t)+3\sin(2\pi100t)$ gives peaks 10000 and 3000. Scaled by $2/N$ they read 10 and 3.

Two frequencies can be told apart only if they are at least about $F_S/N=1/\text{length}$ apart. Zero-padding smooths the plot but adds no resolution.

The **FFT** is a fast way to compute the same DFT: $O(N\log_2N)$ instead of $O(N^2)$. Radix-2 algorithms need $N=2^k$ samples. Shorter signals are zero-padded. Slide 26 shows the butterfly graph of a 16-point FFT.

> [!warning] Watch out
> Slide 21 labels nine rows $X[0]$ to $X[8]$ for an 8-point matrix. The indices run 0 to 7. Slide 23 calls $F_S/2$ the "Nyquist rate". It is the Nyquist frequency.

## 9. The z-transform and digital spectra (slides 27-29)
![[cs568_lecture_04_l4_p027.jpg]]
![[cs568_lecture_04_l4_p028.jpg]]
![[cs568_lecture_04_l4_p029.jpg]]

**Notes.**

$$X(z)=\sum_{n=-\infty}^{\infty}x[n]\,z^{-n}$$

$z$ is any complex number. For $z=e^{j\hat\omega}$ (the unit circle) it is the DTFT. Signals and filters become polynomials in $z^{-1}$, and $z^{-1}$ means a delay of one sample. The Laplace transform is the continuous-time counterpart and is not needed here.

The spectrum of a digital signal is periodic, with period $2\pi$ in $\hat\omega$. That is why it is written $X(e^{j\hat\omega})$. For real signals $0\le\hat\omega\le\pi$ holds all the information. Slide 29: discrete in one domain means periodic in the other.

> [!warning] Slide error
> Slide 28 says the spectrum repeats "at multiples of the Nyquist frequency". The period is $F_S$, twice the Nyquist frequency.

## 10. Properties of the transforms (slides 30-32)
![[cs568_lecture_04_l4_p030.jpg]]
![[cs568_lecture_04_l4_p031.jpg]]
![[cs568_lecture_04_l4_p032.jpg]]

**Notes.**

| Property | Time | Frequency |
|---|---|---|
| Linearity | $\alpha s_1(t)+\beta s_2(t)$ | $\alpha S_1(j\omega)+\beta S_2(j\omega)$ |
| Delay | $x(t-t_d)$ | $X(j\omega)\,e^{-j\omega t_d}$: same magnitude, linear phase |
| Frequency shift | $x(t)\,e^{j\omega_0t}$ | $X(j(\omega-\omega_0))$: modulation |
| Scaling | $x(at)$ | $\frac{1}{\lvert a\rvert}X(j\omega/a)$: shorter in time, wider in frequency |
| Impulse | $\delta(t)$ | $1$ at all frequencies |
| Constant | $1$ | $\delta(\omega)$ (up to a factor $2\pi$) |

Duality: the forward and inverse transforms have almost the same form, so each pair also holds with time and frequency exchanged. The frequency-shift property is used for the STFT in [[CS568 L06 - Short-time Fourier analysis]].

## 11. Convolution and stochastic signals (slides 33-36)
![[cs568_lecture_04_l4_p033.jpg]]
![[cs568_lecture_04_l4_p034.jpg]]
![[cs568_lecture_04_l4_p035.jpg]]
![[cs568_lecture_04_l4_p036.jpg]]

**Notes.** **Convolution** is the overlap of two functions when one is flipped and passed over the other:

$$f(t)=g(t)\otimes h(t)=\int_{-\infty}^{\infty}g(\tau)\,h(t-\tau)\,d\tau\quad\Longleftrightarrow\quad F(\omega)=G(\omega)\,H(\omega)$$

Convolution in time is multiplication in frequency. Speech is the source convolved with the vocal-tract impulse response, so the spectra multiply.

Noise-like signals (fricatives) have no usable Fourier transform. They are described by averages, through the **autocorrelation**

$$R(j)=\sum_{n=-\infty}^{\infty}x[n]\,x[n-j]$$

Its Fourier transform is the **power spectral density** (PSD). White noise has $R(j)=\sigma^2\delta[j]$ and a flat PSD.

> [!example] Discrete convolution
> $[1,-1]\otimes[1,1]=[1,0,-1]$. As polynomials: $(1-z^{-1})(1+z^{-1})=1-z^{-2}$.

> [!warning] Watch out
> Slide 35 calls the sum "the expected value". It estimates the expected value only after dividing by the number of terms.

## 12. The demo script L4_demolar.py
The instructor's Python script (8-page PDF, Turkish comments) replaces the MATLAB exercises `ex4p1.m` to `ex4p4.m` named on the slides. Run one demo with `python L4_demolar.py 1` (1 to 7). It needs NumPy, SciPy and Matplotlib.

| Demo | Slides | Topic |
|---|---|---|
| 1 | 3-4 | amplitude, frequency, phase |
| 2 | 6 | square wave by Fourier synthesis, Gibbs |
| 3 | 10-11 | same magnitude, different phase |
| 4 | 15-16 | aliasing: 25/70/160/510 Hz, a 5 kHz whistle sampled at 8 kHz |
| 5 | 18-25 | DFT/FFT, Nyquist, resolution $F_S/N$, DFT matrix, FFT speed |
| 6 | 31-34 | convolution, source-filter vowel, delay, short pulse |
| 7 | 35-36 | autocorrelation and PSD of noise |

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Sinusoid | $A\cos(\omega t+\varphi)$, $\omega=2\pi F$, $F=1/T$ |
| Fourier series | $x(t)=A_0+\sum_k a_k\cos(k\omega_0t+\varphi_k)$ |
| Euler | $e^{j\theta}=\cos\theta+j\sin\theta$ |
| Analysis | $a_k=\frac{1}{T_0}\int_0^{T_0}x(t)e^{-jk\omega_0t}dt$. Real signals: $a_{-k}=a_k^{*}$ |
| Fourier transform | $X(j\omega)=\int x(t)e^{-j\omega t}dt$ |
| Nyquist frequency | $F_S/2$ |
| Normalised frequency | $\hat\omega=2\pi F/F_S$ |
| Alias | $\lvert F-mF_S\rvert$, $m$ = nearest integer to $F/F_S$ |
| DTFT | $X(e^{j\hat\omega})=\sum_n x[n]e^{-j\hat\omega n}$ |
| DFT | $X[k]=\sum_{n=0}^{N-1}x[n]e^{-j2\pi kn/N}$. Bin $k$ is at $kF_S/N$ |
| FFT | the DFT in $O(N\log_2N)$, $N=2^k$ |
| $z$-transform | $X(z)=\sum_n x[n]z^{-n}$. $z^{-1}$ = one sample of delay |
| Convolution theorem | $g\otimes h\leftrightarrow G\,H$ |
| Autocorrelation / PSD | $R(j)=\sum_n x[n]x[n-j]$ / its Fourier transform |

## Check yourself
> [!question]- 1. Write $x(t)=5\sin(2\pi\,250\,t)$ as a cosine and give $A$, $F$, $T$, $\varphi$.
> $5\cos(2\pi\,250\,t-\pi/2)$. $A=5$, $F=250$ Hz, $T=4$ ms, $\varphi=-\pi/2$.

> [!question]- 2. A 6 kHz tone is sampled at 8 kHz. Where does it appear? And a 9.5 kHz tone?
> $|6000-8000|=2000$ Hz. $|9500-8000|=1500$ Hz. Both lie below the Nyquist frequency 4 kHz.

> [!question]- 3. What is the bin spacing of a 256-point DFT at $F_S=8$ kHz? Which bin is 1 kHz?
> $8000/256=31.25$ Hz. $1000/31.25=32$, so bin 32.

> [!question]- 4. Compute the 4-point DFT of $[1,0,0,0]$ and of $[1,1,1,1]$.
> $[1,1,1,1]$ and $[4,0,0,0]$. An impulse has a flat spectrum, a constant has only DC.

> [!question]- 5. At $F_S=2$ kHz, how long must a recording be to separate 100 Hz and 105 Hz?
> Resolution $1/\text{length}\le5$ Hz, so length $\ge0.2$ s, that is $N=400$ samples.

> [!question]- 6. By how much does a 1 ms delay change the phase of a 500 Hz component? What does it do to the magnitude?
> $-\omega t_d=-2\pi\cdot500\cdot0.001=-\pi$ rad. The magnitude does not change.

## Links
- Course index: [[CS568 Home]]
- Previous: [[CS568 L03 - Organization of speech sounds]]. Next: [[CS568 L05 - Digital filters]]
- Full step-by-step notes (PDF): [CS568_Lecture_04_Signals_Transforms.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_04/CS568_Lecture_04_Signals_Transforms.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_04/CS568_Lecture_04_Signals_Transforms.pdf`
- Original slides (PDF): [l4.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_04/l4.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_04/l4.pdf`
- Demo code (PDF, 8 pages): [L4_demolar.py.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_04/lms.tedu.edu.tr_pluginfile.php_83676_mod_resource_content_0_L4_demolar.py.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_04/lms.tedu.edu.tr_pluginfile.php_83676_mod_resource_content_0_L4_demolar.py.pdf`. Its pages are also in `others/slides/cs568_lecture_04_l4_demolar_py/`, first page: ![[cs568_lecture_04_l4_demolar_py_p001.jpg]]

Slides not embedded: 1 (outline). The 8 pages of the demo-code deck are code listings: only page 1 is embedded.
