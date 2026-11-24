---
course: CS 568 Speech Processing
lecture: 6
title: Short-time Fourier analysis and synthesis
tags: [cs568, lecture]
slides: lectures/lecture_06/l6.pdf
full_notes: lecture_notes/lecture_06/CS568_Lecture_06_STFT.pdf
---
# CS 568 · Lecture 6 · Short-time Fourier analysis and synthesis

> [!summary] In short
> - Speech changes every few tens of ms, so we take DFTs of short, overlapping, windowed pieces: the STFT.
> - $X(n,\omega)$ is the DTFT of the section $x[m]\,w[n-m]$. Sampling $\omega$ at $2\pi k/N$ gives $X(n,k)$. $\log|X(n,k)|^2$ is the spectrogram.
> - Filtering view: the discrete STFT is a bank of $N$ band-pass filters centred at $2\pi k/N$.
> - Two ways back to the signal: filter bank summation (FBS) and overlap-add (OLA), each exact under a window condition.
> - From the magnitude alone, an iterative least-squares method (Griffin-Lim) estimates the signal. It stops at a local minimum.
> - Window length $N_w$, hop $L$ and DFT size $N$ are three different numbers.

## 1. Overview: why a short-time transform (slides 2-4)
![[cs568_lecture_06_l6_p002.jpg]]
![[cs568_lecture_06_l6_p003.jpg]]
![[cs568_lecture_06_l6_p004.jpg]]

**Notes.** Recap: the DTFT is $X(e^{j\hat\omega})=\sum_n x[n]e^{-j\hat\omega n}$ and the DFT samples it at $\hat\omega_k=2\pi k/N$.

Speech is non-stationary: the formants move with each phoneme. A DFT over a long window tells which frequencies are present, not when (slide 3: a signal that jumps from about 100 Hz to 200 Hz). Over a short window speech is about stationary, so we apply the DFT to short pieces.

Recipe (slide 4):

1. Choose the window length (30 ms narrowband, 5 ms wideband).
2. Choose the overlap between windows (for example 30%).
3. Choose a windowing function (Hann, Gaussian).
4. Multiply the signal by the shifted window.
5. Apply the FFT to each windowed segment.

A short window gives good time resolution and poor frequency resolution. A long window gives the opposite.

> [!example] Frames
> $f_s=8$ kHz, 20 ms window, 10 ms hop: $N_w=160$ samples, $L=80$ samples, overlap $80/160=50\%$.

> [!warning] Slide error
> Slide 2 writes the DFT frequencies as $\omega_k=2\pi F_s/N$, with no $k$. The $k$-th frequency is $\hat\omega_k=2\pi k/N$, that is $F_k=kF_S/N$ Hz.

## 2. Windowing functions (slides 5-6)
![[cs568_lecture_06_l6_p005.jpg]]
![[cs568_lecture_06_l6_p006.jpg]]

**Notes.** A window $w[n,\tau]$ localises the signal in time. It is tapered at the ends to avoid artificial jumps. For a window of length $N_w$ starting at $\tau$:

$$\text{Hamming:}\ w[n,\tau]=0.54-0.46\cos\!\left(\frac{2\pi(n-\tau)}{N_w-1}\right)\qquad\text{Hann:}\ w[n,\tau]=0.5\left(1-\cos\!\left(\frac{2\pi(n-\tau)}{N_w-1}\right)\right)$$

Any window changes the spectrum computed on it. The choice trades the **width of the main lobe** (frequency resolution) against the **height of the side lobes** (leakage from strong components).

| Window | Main lobe | Side lobes |
|---|---|---|
| Rectangular | narrowest | first one only about 13 dB down: strong leakage |
| Hann | twice as wide | start at about $-31$ dB, decay fast |
| Hamming | twice as wide | first one about $-43$ dB, slower decay further out |

Hamming is 1 at the centre and 0.08 at the ends. Hann is 1 at the centre and 0 at the ends.

> [!warning] Slide error
> Slide 5 gives the Hamming coefficient as 0.4. It is 0.46. The Hann formula on the slide divides by $N-1$: it is the same $N_w-1$.

## 3. The STFT and the spectrogram (slides 7-8)
![[cs568_lecture_06_l6_p007.jpg]]
![[cs568_lecture_06_l6_p008.jpg]]

**Notes.** The **discrete-time STFT** is the Fourier transform of the windowed signal:

$$X(n,\omega)=\sum_{m=-\infty}^{\infty}x[m]\,w[n-m]\,e^{-j\omega m}$$

$f_n[m]=x[m]\,w[n-m]$ is the **short-time section** at time $n$. The **discrete STFT** samples the frequency axis at $N$ points, and the **spectrogram** is its log magnitude:

$$X(n,k)=X(n,\omega)\Big|_{\omega=\frac{2\pi}{N}k},\qquad S(n,k)=\log|X(n,k)|^2$$

- Long window: **narrowband** spectrogram, harmonics appear as horizontal lines. Use it to measure $F_0$.
- Short window: **wideband** spectrogram, glottal pulses appear as vertical lines. Use it to read formants.

> [!warning] Slide error
> Slides 7 and 15 write the exponential as $e^{-j\omega n}$. The sum runs over $m$, so it must be $e^{-j\omega m}$.

## 4. Filtering view and filter bank (slides 9-14)
![[cs568_lecture_06_l6_p009.jpg]]
![[cs568_lecture_06_l6_p010.jpg]]
![[cs568_lecture_06_l6_p011.jpg]]
![[cs568_lecture_06_l6_p012.jpg]]
![[cs568_lecture_06_l6_p013.jpg]]
![[cs568_lecture_06_l6_p014.jpg]]

**Notes.** Fix the frequency at $\omega_0$. The window then acts as the impulse response of a filter. Two equivalent forms:

$$X(n,\omega_0)=\left(x[n]\,e^{-j\omega_0n}\right)*w[n]=e^{-j\omega_0n}\left(x[n]*w[n]\,e^{j\omega_0n}\right)$$

- First form: modulate (shift the band at $\omega_0$ down to 0), then low-pass with $w[n]$.
- Second form: band-pass with $w[n]e^{j\omega_0n}$ (the window shifted up to $\omega_0$), then demodulate.

With $\omega_0=2\pi k/N$ the discrete STFT is the output of a **filter bank**:

$$X(n,k)=e^{-j\frac{2\pi}{N}kn}\left(x[n]*w[n]\,e^{j\frac{2\pi}{N}kn}\right),\qquad k=0,\dots,N-1$$

Channel $k$ is a band-pass filter centred at $2\pi k/N$, that is $kf_s/N$ Hz. Slide 13 shows the analysis side and the synthesis side.

> [!example] Channel centres
> $f_s=16$ kHz, $N=512$: channel 64 is at $64\cdot16000/512=2000$ Hz. 3 kHz is channel $3000\cdot512/16000=96$.

> [!warning] Slide errors
> - Slide 9 says $x[n]e^{-j\omega_0n}$ modulates $x[n]$ "up to" $\omega_0$. With the minus sign the band at $\omega_0$ moves down to 0.
> - Slide 12 writes the filter as $w[n]e^{-j\frac{2\pi}{N}kn}$. The band-pass filter has the plus sign. The demodulator has the minus sign.

## 5. When is the STFT invertible? (slides 15-17)
![[cs568_lecture_06_l6_p015.jpg]]
![[cs568_lecture_06_l6_p016.jpg]]
![[cs568_lecture_06_l6_p017.jpg]]

**Notes.** For fixed $n$, the inverse DTFT of $X(n,\omega)$ returns the section $f_n[m]$. At $m=n$ it equals $x[n]\,w[0]$. If $w[0]\ne0$:

$$x[n]=\frac{1}{2\pi\,w[0]}\int_{-\pi}^{\pi}X(n,\omega)\,e^{j\omega n}\,d\omega$$

This uses one sample of each section, so the STFT is very redundant. It is therefore **decimated in time**: computed only at $n=L,2L,3L,\dots$ ($L$ is the hop).

- **Too large a hop:** if $L>N_w$, some samples lie in no window. They can take any value without changing the STFT. Not invertible.
- **Too coarse a frequency grid:** if the window has bandwidth $B$ and the spacing $2\pi/N>B$, some frequencies pass through no filter. Not invertible.

> [!warning] Watch out
> One direction only. $L\le N_w$ does not by itself make a resynthesis method exact: OLA still needs its window condition. And if the window is placed on $0,\dots,N_w-1$, then $w[0]$ is its first sample, which is 0 for Hann.

## 6. Synthesis: filter bank summation (slides 18-19)
![[cs568_lecture_06_l6_p018.jpg]]
![[cs568_lecture_06_l6_p019.jpg]]

**Notes.** FBS uses the filtering view. Each channel is modulated back up with a complex exponential and all channels are added:

$$y[n]=\frac{1}{N\,w[0]}\sum_{k=0}^{N-1}X(n,k)\,e^{j\frac{2\pi}{N}nk}$$

$y[n]=x[n]$ if either

1. the window is not longer than the number of filters, $N_w\le N$, or
2. for $N_w>N$, the **FBS constraint** holds: the shifted filter responses add to a constant,

$$\sum_{k=0}^{N-1}W\!\left(\omega-\frac{2\pi}{N}k\right)=N\,w[0]$$

Here $W(\omega)$ is the Fourier transform of the window.

> [!warning] Slide errors
> Slide 18 writes the sum over $m=-\infty..\infty$. It runs over the channels $k=0..N-1$. Slide 19 writes condition 1 as "$N_w>N$": it must be $N_w\le N$. "BFS constraint" should read FBS.

## 7. Synthesis: overlap-add (slides 20-22)
![[cs568_lecture_06_l6_p020.jpg]]
![[cs568_lecture_06_l6_p021.jpg]]
![[cs568_lecture_06_l6_p022.jpg]]

**Notes.** OLA uses the Fourier view. Take the inverse DFT of each frame, then add the overlapping sections. Dividing each section by the window is not used, because small errors would be amplified. With hop $L$ and $W(0)=\sum_nw[n]$:

$$x[n]=\frac{L}{W(0)}\sum_{p=-\infty}^{\infty}\left[\frac{1}{N}\sum_{k=0}^{N-1}X(pL,k)\,e^{j\frac{2\pi}{N}kn}\right]$$

This is exact when the windows shifted by $L$ add to a constant:

$$\sum_{p=-\infty}^{\infty}w[pL-n]=\frac{W(0)}{L}$$

It holds when the window has maximum frequency $\omega_c<2\pi/L$, or when the shifted windows sum to a constant (periodic Hann or Hamming at 50% overlap, rectangle with no overlap).

Slide 22 compares the two: FBS adds frequency components for each $n$, OLA adds time components for each $n$.

> [!example] Hann, length 8, hop 4
> $w=[0, 0.1464, 0.5, 0.8536, 1, 0.8536, 0.5, 0.1464]$, $W(0)=4$, $W(0)/L=1$. And $w[n]+w[n+4]=1$ for every $n$. Exact.

> [!warning] Slide error
> The OLA formula on slide 20 omits the $1/N$ of the inverse DFT.

## 8. STFT magnitude and its inversion (slides 23-26)
![[cs568_lecture_06_l6_p023.jpg]]
![[cs568_lecture_06_l6_p024.jpg]]
![[cs568_lecture_06_l6_p025.jpg]]
![[cs568_lecture_06_l6_p026.jpg]]

**Notes.** The spectrogram has no phase, yet people can read speech from it. Can $x[n]$ be recovered from $|X(n,\omega)|$ alone? Least-squares iteration (Griffin-Lim):

1. Start from an arbitrary $x_e^1[n]$ (usually white noise).
2. Compute its STFT $X_e^i(m,\omega)$. Put in the known magnitude and keep the phase of the estimate:
$$X^i(m,\omega)=|X(m,\omega)|\,\frac{X_e^i(m,\omega)}{|X_e^i(m,\omega)|}$$
3. New estimate by least-squares overlap-add, with $g_m^i[n]$ the inverse transform of $X^i(m,\omega)$:
$$x_e^{i+1}[n]=\frac{\sum_m w[m-n]\,g_m^i[n]}{\sum_m w^2[m-n]}$$
4. Repeat until convergence.

Each iteration does not increase the distance between $|X_e|$ and $|X|$. The process converges to a local minimum, not necessarily the global one.

> [!example] One sample of the update
> Two frames cover sample $n$ with $w=0.5,\ 1$ and $g=0.4,\ 1.1$. $x_e^{i+1}[n]=(0.5\cdot0.4+1\cdot1.1)/(0.25+1)=1.3/1.25=1.04$.

> [!warning] Watch out
> "Converges" means the error stops decreasing, not that the original signal comes back. Different starting noises can end in different local minima.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| STFT | $X(n,\omega)=\sum_m x[m]\,w[n-m]\,e^{-j\omega m}$ |
| Discrete STFT | $X(n,k)$ at $\omega=2\pi k/N$ |
| Spectrogram | $S(n,k)=\log\lvert X(n,k)\rvert^2$ |
| Hamming / Hann | $0.54-0.46\cos(\cdot)$ / $0.5(1-\cos(\cdot))$ |
| $N_w$, $L$, $N$ | window length, hop, DFT size |
| Filter-bank view | $X(n,k)=e^{-j\frac{2\pi}{N}kn}\left(x[n]*w[n]e^{j\frac{2\pi}{N}kn}\right)$ |
| Inversion | $x[n]=f_n[n]/w[0]$. Lost if $L>N_w$ or $2\pi/N>B$ |
| FBS | $y[n]=\frac{1}{Nw[0]}\sum_kX(n,k)e^{j\frac{2\pi}{N}nk}$. Exact if $N_w\le N$ or the FBS constraint holds |
| OLA | $x[n]=\frac{L}{W(0)}\sum_p\text{IDFT}_p$. Exact if $\sum_pw[pL-n]=W(0)/L$ |
| Griffin-Lim | alternate "impose $\lvert X\rvert$" and "least-squares overlap-add" |

## Check yourself
> [!question]- 1. What are the values of the Hamming and the Hann window at the centre and at the ends?
> Hamming: 1 at the centre, $0.54-0.46=0.08$ at the ends. Hann: 1 at the centre, 0 at the ends.

> [!question]- 2. Which spectrogram would you use to measure $F_0$? Which to read formants?
> Narrowband for $F_0$ (the spacing of the horizontal harmonic lines). Wideband for formants (one band per formant).

> [!question]- 3. A window has $N_w=320$ samples. For which hops $L$ is the decimated STFT certainly not invertible?
> $L>320$. With $L=321$ sample 320 lies in no window.

> [!question]- 4. Does a rectangular window with hop $L=N_w$ satisfy the OLA constraint?
> Yes. Every sample is in exactly one window, so the sum is 1, and $W(0)/L=N_w/N_w=1$.

> [!question]- 5. A periodic Hamming window is used with $L=N_w/2$. What constant do the shifted windows add up to?
> $W(0)=0.54N_w$, so $W(0)/L=1.08$. Check: $(0.54-0.46c)+(0.54+0.46c)=1.08$.

> [!question]- 6. In the least-squares update, what is returned if the sections are consistent, $g_m[n]=w[m-n]\,x[n]$?
> $x[n]\sum_mw^2[m-n]/\sum_mw^2[m-n]=x[n]$. A consistent STFT is a fixed point.

## Links
- Course index: [[CS568 Home]]
- Previous: [[CS568 L05 - Digital filters]]. Next: [[CS568 L07 - Linear prediction of speech]]
- Background: [[CS568 L04 - Signals and transforms]] (DFT, frequency shift), [[CS568 L03 - Organization of speech sounds]] (wideband and narrowband spectrograms)
- Full step-by-step notes (PDF): [CS568_Lecture_06_STFT.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_06/CS568_Lecture_06_STFT.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_06/CS568_Lecture_06_STFT.pdf`
- Original slides (PDF): [l6.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_06/l6.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_06/l6.pdf`

Slides not embedded: 1 (outline).
