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

> [!question]- Check (slide 2): Find the mistake: "Voiced speech is a periodic analog signal, because every glottal cycle is an exact copy of the one before."
> "Analog" is right: the sound pressure varies continuously in time, so it is written $x(t)$.
> "Periodic" is wrong. The cycles are very similar but not exact copies. The period changes a little from cycle to cycle (jitter) and so does the amplitude (shimmer).
> So voiced speech is quasi-periodic. In the figure the regular spikes on the right are such almost-repeating cycles.

> [!question]- Check (slide 3): A signal repeats every 2.5 ms. What is its frequency? Also write $\cos(t)$ as a sine.
> $F=1/T=1/0.0025=400$ Hz.
> The slide gives $\sin(t)=\cos(t-\pi/2)$. Replace $t$ by $t+\pi/2$: $\sin(t+\pi/2)=\cos(t)$.
> The cosine is the sine shifted by a quarter of a period. The shape is the same.

> [!question]- Check (slide 4): In $x(t)=A\cos(2\pi Ft+\varphi)$, say what each of $A$, $F$, $\varphi$ does to the plot. What is $\omega$ for $F=440$ Hz, and why is $\omega$ used?
> $A$ scales the height: the wave swings between $-A$ and $+A$.
> $F$ sets how many cycles fit in one second. Doubling $F$ halves the period.
> $\varphi$ slides the wave along the time axis. It changes neither height nor period.
> $\omega=2\pi F=2\pi\cdot440\approx2764.6$ rad/s. It is only a shorter way to write $2\pi F$ (one cycle is $2\pi$ rad).

## 2. Fourier series (slides 5-6)
![[cs568_lecture_04_l4_p005.jpg]]
![[cs568_lecture_04_l4_p006.jpg]]

**Notes.** A signal is periodic if $x(t)=x(t+nT)$. The smallest such $T$ is the fundamental period $T_0$, and $F_0=1/T_0$ is the fundamental frequency. Harmonics are the integer multiples $2F_0,3F_0,\dots$

Any periodic signal is a sum of sinusoids at the harmonics, with $\omega_0=2\pi F_0$:

$$x(t)=A_0+\sum_{k=1}^{\infty}a_k\cos(k\omega_0t+\varphi_k)$$

Square wave (slide 6): only odd harmonics, each with phase $-\pi/2$ and amplitude $a_k=4/(k\pi)$.

> [!warning] Watch out
> Cutting the series after finitely many terms overshoots next to a jump by about 9% of the jump height (Gibbs). More terms make the overshoot narrower, not smaller: the peak stalls near 1.179.

> [!question]- Check (slide 5): A voiced sound has the fundamental period $T_0=8$ ms. Give $F_0$ and the 4th harmonic. Is 300 Hz a harmonic of this sound? In the Fourier series, what are $A_0$, $a_k$ and $\varphi_k$?
> $F_0=1/T_0=1/0.008=125$ Hz. The 4th harmonic is $4\cdot125=500$ Hz.
> $300/125=2.4$ is not an integer, so 300 Hz is not a harmonic. The series has no component there.
> $A_0$ is the constant (DC) part, the mean of the signal. $a_k$ is the amplitude of the harmonic at $kF_0$. $\varphi_k$ is its phase.

> [!question]- Check (slide 6): For the square wave, compute the amplitude of the 7th harmonic and compare it with the fundamental. Why is there no constant term?
> $a_7=4/(7\pi)\approx0.182$ and $a_1=4/\pi\approx1.273$. The ratio is $a_7/a_1=1/7$: the amplitudes fall like $1/k$.
> The wave is $+1$ for half of the period and $-1$ for the other half. Its mean is 0, so $A_0=0$.
> The even harmonics are also 0, so the component at $2F_0$, $4F_0$, ... is missing.

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

> [!question]- Check (slide 7): Write $3\cos(\omega t+\pi/4)$ in two ways with complex exponentials: as a real part, and with the inverse Euler formula. Which frequencies appear in the second form?
> Real part: $3\cos(\omega t+\pi/4)=\mathrm{Re}\{3e^{j(\omega t+\pi/4)}\}$.
> Inverse Euler with $\theta=\omega t+\pi/4$: $3\cos\theta=\tfrac32e^{j(\omega t+\pi/4)}+\tfrac32e^{-j(\omega t+\pi/4)}$.
> The second form has two terms: one at $+\omega$ and one at $-\omega$, each with half of the amplitude. This is where negative frequencies come from.

> [!question]- Check (slide 8): A sinusoid has $A=2$ and $\varphi=\pi/3$. Give its complex amplitude $X$ in polar and rectangular form. What is gained by writing $x(t)=Xe^{j\omega t}$?
> $X=Ae^{j\varphi}=2e^{j\pi/3}=2\cos(\pi/3)+j\,2\sin(\pi/3)=1+j\,1.732$.
> $\lvert X\rvert=2$ is the amplitude and the angle of $X$ is the phase $\pi/3$.
> The gain: amplitude and phase sit in one constant $X$. The factor $e^{j\omega t}$ only carries the frequency. In the Fourier series each harmonic then needs one complex number $a_k$.

> [!question]- Check (slide 9): Name each part of the analysis equation $a_k=\frac{1}{T_0}\int_0^{T_0}x(t)e^{-jk\omega_0t}dt$. Then give all non-zero $a_k$ of $x(t)=4\cos(3\omega_0t)$.
> $x(t)$ is the signal. $e^{-jk\omega_0t}$ is the probe at harmonic $k$ (note the minus sign). The integral over one period $T_0$ measures how much of that harmonic is in the signal. $1/T_0$ turns the integral into an average.
> Inverse Euler: $4\cos(3\omega_0t)=2e^{j3\omega_0t}+2e^{-j3\omega_0t}$.
> The probe picks only the matching term, so $a_3=2$ and $a_{-3}=2$. All other $a_k$ are 0.
> The real amplitude 4 is twice the magnitude of the complex coefficient, as the note says.

## 4. Magnitude and phase (slides 10-11)
![[cs568_lecture_04_l4_p010.jpg]]
![[cs568_lecture_04_l4_p011.jpg]]

**Notes.** The ear is largely insensitive to phase, so one studies the **magnitude spectrum**. A signal with the magnitude spectrum of a square wave and arbitrary phases sounds like the square wave, but its waveform looks very different. Slide 10 says "see below" but has no figure. Demo 3 of the script supplies it.

> [!warning] Watch out
> Same magnitude spectrum does not mean same waveform. In demo 3 the peak is about 1.18 for the square wave, 3.32 with all phases 0, and 1.66 with random phases.

> [!question]- Check (slides 10-11): Exercise ex4p3 builds a second signal from the square-wave harmonics with different phases. What stays the same and what changes, in the plot and in the sound? Find the mistake: "Phase does not matter, so two signals with the same magnitude spectrum are the same signal."
> The amplitudes $a_k$ stay the same, so the magnitude spectrum is the same. The phases $\varphi_k$ change.
> Plot: the waveform looks very different, because the harmonics now add up at other time instants.
> Sound: almost the same, because the ear is largely insensitive to phase.
> The mistake: phase does not matter much for hearing, but it does matter for the waveform. A signal is fixed only by magnitude and phase together.

## 5. The Fourier transform (slides 12-13)
![[cs568_lecture_04_l4_p012.jpg]]
![[cs568_lecture_04_l4_p013.jpg]]

**Notes.** For non-periodic signals the sum over harmonics becomes an integral over all frequencies. The spectrum is a continuous function.

$$X(j\omega)=\int_{-\infty}^{\infty}x(t)\,e^{-j\omega t}\,dt,\qquad x(t)=\frac{1}{2\pi}\int_{-\infty}^{\infty}X(j\omega)\,e^{j\omega t}\,d\omega$$

Slide 13: the transform works like a prism that splits sound into its frequencies.

> [!warning] Slide error
> Slide 12 writes the inverse as $\int X(j\omega)e^{-j\omega t}\,dt$. The inverse needs $e^{+j\omega t}$, integration over $\omega$ and the factor $1/(2\pi)$.

> [!question]- Check (slide 12): Compare the Fourier transform $X(j\omega)=\int_{-\infty}^{\infty}x(t)e^{-j\omega t}dt$ with the analysis equation of the Fourier series. Name three differences and say why each one is there.
> 1. Limits: the series integrates over one period $[0,T_0]$. The transform integrates over $(-\infty,\infty)$, because a non-periodic signal has no period that repeats.
> 2. Frequency variable: the series has $k\omega_0$, a discrete set of harmonics. The transform has $\omega$, any real frequency. So the spectrum is a continuous function, not a set of lines.
> 3. Factor: the series divides by $T_0$ (an average over the period). The transform has no such factor.
> The same part in both: multiply by a probe $e^{-j(\cdot)t}$ and integrate.

> [!question]- Check (slide 13): In the lower row of the figure, what plays the role of the prism, and what comes out of it? Which block in that row limits the highest frequency that can come out?
> The Fourier transform is the prism. A prism splits light into colours (frequencies of light). The transform splits a sound into its frequency components: low (bass), middle, high (treble).
> The block "Digitize waveform in computer" samples the sound. After sampling at $F_S$, only frequencies up to $F_S/2$ can be represented.

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

> [!info] Sampling theorem and quantization (beyond the slides)
> Going from analog to digital has two separate steps.
> - **Sampling** (örnekleme) makes time discrete: keep $x(nT_S)$ only. The sampling theorem says: a signal with no content above $F_{\max}$ can be rebuilt exactly from its samples if $F_S>2F_{\max}$. The value $2F_{\max}$ is the **Nyquist rate**. It belongs to the signal. The **Nyquist frequency** $F_S/2$ belongs to the sampler.
> - **Quantization** (nicemleme) makes amplitude discrete: each sample is rounded to one of $2^B$ levels ($B$ = number of bits). The rounding error sounds like a weak noise. Each extra bit halves the step, which gives about 6 dB more dynamic range: $20\log_{10}2^{16}\approx96$ dB for 16 bits.
>
> Sampling limits the frequency range. Quantization limits the amplitude precision. **Aliasing** (örtüşme) is an error of the sampling step, not of the quantization step.

> [!question]- Check (slide 14): A recording uses $F_S=16$ kHz and 8 bits. Give $T_S$, the Nyquist frequency, the range of integer values, and the normalised frequency of a 2 kHz tone.
> $T_S=1/F_S=1/16000=62.5\ \mu$s.
> Nyquist frequency: $F_S/2=8$ kHz.
> 8 bits: $2^8=256$ values, from $-128$ to $127$.
> $\hat\omega=2\pi F/F_S=2\pi\cdot2000/16000=\pi/4$ rad per sample. The tone advances one eighth of a cycle per sample.

> [!question]- Check (slide 15): A signal has tones at 3 kHz, 10 kHz, 19 kHz and 35 kHz. It is sampled at $F_S=16$ kHz with no filter. Where does each tone appear? What is special about the result?
> The Nyquist frequency is 8 kHz. Use $\lvert F-mF_S\rvert$ with $m$ the integer nearest to $F/F_S$.
> 3 kHz: below 8 kHz, it stays at 3 kHz.
> 10 kHz: $\lvert10-16\rvert=6$ kHz.
> 19 kHz: $\lvert19-16\rvert=3$ kHz.
> 35 kHz: $\lvert35-2\cdot16\rvert=3$ kHz.
> Three different tones land on 3 kHz. After sampling nobody can tell them apart, so aliasing cannot be undone.

> [!question]- Check (slide 16): Read the figure. What do the samples show in case A (sampled at $f$) and in case C (sampled at $4f/3$)? Give the apparent frequency for $f=300$ Hz. Why is case B (sampled at $2f$) not a safe choice?
> Case A: one sample per period, always at the same point of the cycle. The samples are constant: the tone appears at $\lvert f-f\rvert=0$ Hz. For 300 Hz: 0 Hz (DC).
> Case C: $F_S=4f/3$ is below $2f$. The alias is $\lvert f-4f/3\rvert=f/3$. For 300 Hz: $F_S=400$ Hz and the tone appears at 100 Hz. In the figure the dotted wave makes one cycle while the original makes three.
> Case B: here the samples hit the peaks, so the frequency looks right. With another starting point the samples could hit the zero crossings and the tone would vanish. So $F_S$ must be greater than $2f$, not equal.

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

> [!question]- Check (slide 17): In the DTFT $X(e^{j\hat\omega})=\sum_n x[n]e^{-j\hat\omega n}$, say what each symbol is. Which parts of the result are the magnitude and the phase? Give two reasons why a computer cannot evaluate the DTFT as it is written.
> $x[n]$: the samples. $n$: the sample index (discrete time). $\hat\omega$: the normalised frequency in radians per sample, a continuous variable. $e^{-j\hat\omega n}$: the probe at that frequency.
> $X(e^{j\hat\omega})$ is a complex number for each $\hat\omega$. Its absolute value $\lvert X(e^{j\hat\omega})\rvert$ is the magnitude spectrum. Its angle is the phase spectrum.
> Reason 1: the sum runs over infinitely many samples. Reason 2: $\hat\omega$ is continuous, so there are infinitely many frequencies. The DFT fixes both: $N$ samples and $N$ frequencies.
> The factor $T_S$ of the rectangle rule was dropped. It only scales the spectrum.

> [!question]- Check (slide 18): A speech frame is 25 ms long at $F_S=16$ kHz. How many samples and DFT bins does it have, how far apart are the bins in Hz, and in which bin does a 1 kHz tone fall? Why not take a 1 s frame for a finer spectrum?
> $N=0.025\cdot16000=400$ samples, so 400 bins.
> Bin spacing: $F_S/N=16000/400=40$ Hz (this equals $1/0.025$ s).
> 1 kHz: $k=1000/40=25$, so bin 25.
> A 1 s frame would give 1 Hz spacing, but speech is not stationary: the sounds change within the second. The spectrum would be an average over many different sounds.

> [!question]- Check (slide 19): Compare the two inverse transforms (in their corrected forms). Why is one an integral and the other a sum, and which factor stands in front of each?
> Inverse DTFT: $x[n]=\frac{1}{2\pi}\int_{-\pi}^{\pi}X(e^{j\hat\omega})e^{j\hat\omega n}d\hat\omega$. The frequency is continuous, so the components are added with an integral. It runs over one period of length $2\pi$, and the factor is $1/(2\pi)$.
> Inverse DFT: $x[n]=\frac1N\sum_{k=0}^{N-1}X[k]e^{j2\pi kn/N}$. There are only $N$ frequencies, so the components are added with a sum over $k$, and the factor is $1/N$.
> In both, the sign of the exponent is $+$, opposite to the forward transform. Both say: the signal is a weighted sum of complex sinusoids, and the weights are the spectrum.

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

> [!info] Reading the spectrum plots of slides 23-24 (beyond the slides)
> The vertical axis is labelled $\lvert X(f)\rvert$ but runs from $-10$ to $10$. A magnitude cannot be negative, so the plots show a logarithm of the magnitude. The slide does not say which one. The peak values are consistent with a natural logarithm: about 7 on slide 23 ($\ln1000\approx6.9$), about 9 and 8 on slide 24 ($\ln10000\approx9.2$, $\ln3000\approx8.0$). A base-10 logarithm would give 3, 4 and 3.5, and decibels would give 60, 80 and 70.
> The horizontal axis of slide 23 covers all $N$ bins, from 0 to $F_S=2000$ Hz, which is $\hat\omega=2\pi$. Bins above $F_S/2$ are the negative frequencies: bin $N-k$ holds the frequency $-kF_S/N$. For a real signal they mirror the lower half and carry no new information.

> [!question]- Check (slide 20): For $N=4$, compute $W_4$ and write row 1 of the DFT matrix. Use it to get $X[1]$ for $x=[1,2,3,4]$. What does row 0 compute?
> $W_4=e^{-j2\pi/4}=e^{-j\pi/2}=-j$.
> Row 1 is $[W_4^0,W_4^1,W_4^2,W_4^3]=[1,-j,-1,j]$.
> $X[1]=1\cdot1+2\cdot(-j)+3\cdot(-1)+4\cdot j=-2+2j$.
> Row 0 is all ones, so $X[0]=1+2+3+4=10$: the sum of the samples (the DC term).

> [!question]- Check (slides 21-22): In the figures for $N=8$: which row is the fastest oscillation and what are its entries? Why are rows 1 and 7 complex conjugates? What do the solid and dashed curves on slide 22 show?
> Row $k=4$ (blue). The step is $2\pi\cdot4/8=\pi$, so the vector flips each sample: $1,-1,1,-1,\dots$ This is the Nyquist frequency, 2 samples per cycle.
> Row 7 has the step $2\pi\cdot7/8=2\pi-2\pi/8$. A full turn does not count, so the step is $-2\pi/8$: the same speed as row 1 but in the other direction. The same real part and the opposite imaginary part means complex conjugate.
> Solid: the real part $\cos(2\pi kn/N)$. Dashed: the imaginary part $-\sin(2\pi kn/N)$. Each row is a complex sinusoid with $k$ cycles over the $N$ samples, and $X[k]$ is its inner product with the signal.

> [!question]- Check (slide 23): The spectrum of one 10 Hz sine shows two peaks. Why? What would change in the DFT if the recording were only 0.5 s long and the sine had 50 Hz (same $F_S=2$ kHz)?
> A real sine is the sum of two complex exponentials, at $+10$ Hz and $-10$ Hz. The negative one appears at $F_S-10=1990$ Hz, because the spectrum is periodic with period $F_S$. So the plot is symmetric around $F_S/2=1000$ Hz.
> New case: $N=0.5\cdot2000=1000$ samples. Bin spacing $F_S/N=2$ Hz.
> The peaks are at bin $50/2=25$ and at bin $1000-25=975$ (that is 1950 Hz).
> Peak height: $N/2=500$, half of the old height, because there are half as many samples.

> [!question]- Check (slide 24): Why is the peak at 100 Hz lower than the peak at 10 Hz? Suppose the second term were $3\sin(2\pi\,1900\,t)$ instead. How would the magnitude plot from 0 to 200 Hz change?
> The peak height is amplitude times $N/2$: $10\cdot1000=10000$ at 10 Hz and $3\cdot1000=3000$ at 100 Hz. The ratio is the amplitude ratio $10/3$.
> 1900 Hz is above the Nyquist frequency 1000 Hz. Its alias is $\lvert1900-2000\rvert=100$ Hz.
> The samples of $3\sin(2\pi\,1900\,t)$ are the same as the samples of $-3\sin(2\pi\,100\,t)$: only the sign of the term changes.
> So the magnitude plot shows the same peaks: 10000 at 10 Hz and 3000 at 100 Hz. Only the phase at 100 Hz flips (by $\pi$). After sampling, a 1900 Hz tone cannot be told apart from a 100 Hz tone.

> [!question]- Check (slide 25): A signal has 3000 samples. What does a radix-2 FFT do with it? Compare $N^2$ and $N\log_2N$ for that $N$. Does the padding improve the frequency resolution?
> Radix-2 needs $N=2^k$. The next power of two is $2^{12}=4096$, so 1096 zeros are appended.
> Direct DFT: $N^2=4096^2=16\,777\,216$ operations.
> FFT: $N\log_2N=4096\cdot12=49\,152$ operations. That is about 341 times fewer.
> The result is the same DFT, only faster. The zeros add no information: the bins are closer together, so the plot is smoother, but two close tones are not separated better. Resolution depends on the length of the real recording.

> [!question]- Check (slide 26): The graph is a 16-point FFT. How many stages of butterflies does it need? The outputs on the right are not in the order 0, 1, 2, ... Which rule gives the order, and which $X$ comes out at position 5 (counting from 0)?
> $\log_2 16=4$ stages. Each stage combines pairs of values and multiplies by powers of $W$. This is where $N\log_2N$ comes from: $\log_2N$ stages with about $N$ operations each.
> The outputs come in bit-reversed order (the name is beyond the slides, the order is in the figure): write the position in 4 bits and read the bits backwards.
> Position 5 is $0101$. Reversed it is $1010=10$, so $X(10)$. The slide shows $X(10)$ as the sixth output.

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

> [!info] Zeros, poles and the region of convergence (beyond the slides)
> The slides only define $X(z)$. Three more terms belong to it. They are used from [[CS568 L05 - Digital filters]] on.
> - A **zero** (sıfır) is a value of $z$ where $X(z)=0$. A **pole** (kutup) is a value of $z$ where $X(z)$ becomes infinite. A filter is described by the positions of its zeros and poles in the complex $z$-plane.
> - The **region of convergence** (ROC) is the set of $z$ for which the sum $\sum_n x[n]z^{-n}$ is finite. For a finite sequence it is the whole plane, except possibly $z=0$ or $z=\infty$. For an infinite sequence it is only a part of the plane.
> - The **unit circle** (birim çember) is $\lvert z\rvert=1$, that is $z=e^{j\hat\omega}$. Walking once around it, from $\hat\omega=0$ to $2\pi$, reads off the DTFT. This also shows why the spectrum is periodic: after $2\pi$ you are at the same point again. The DTFT exists only if the unit circle lies inside the ROC.
>
> Rule of thumb: a pole close to the unit circle gives a peak in the magnitude spectrum at that angle. A zero close to the unit circle gives a dip.

> [!question]- Check (slide 27): Let $x[0]=2$, $x[1]=0$, $x[2]=-1$ and 0 elsewhere. Write $X(z)$. Then set $z=e^{j\hat\omega}$ and give the value at $\hat\omega=0$, $\pi/2$ and $\pi$. What did the substitution do?
> $X(z)=2z^{0}+0\cdot z^{-1}-1\cdot z^{-2}=2-z^{-2}$. Each sample is the coefficient of $z^{-n}$, and $z^{-2}$ marks "two samples later".
> On the unit circle: $X(e^{j\hat\omega})=2-e^{-j2\hat\omega}$.
> $\hat\omega=0$: $2-1=1$. $\hat\omega=\pi/2$: $2-e^{-j\pi}=2+1=3$. $\hat\omega=\pi$: $2-e^{-j2\pi}=2-1=1$.
> The substitution turned the $z$-transform into the DTFT, the spectrum of the sequence. Here the magnitude is largest at $\hat\omega=\pi/2$, which is $F_S/4$.

> [!question]- Check (slide 28): A real signal sampled at $F_S=8$ kHz has a spectral peak at 1 kHz. At which other frequencies up to 20 kHz does the same peak appear in its spectrum? Why is the spectrum written $X(e^{j\hat\omega})$ and not $X(\hat\omega)$?
> A real signal has the peak at $+1$ kHz and $-1$ kHz. The spectrum repeats with period $F_S=8$ kHz, so the peak is at $8k\pm1$ kHz: 7, 9, 15 and 17 kHz.
> None of these is new information. Everything is already in the range 0 to $F_S/2=4$ kHz.
> $\hat\omega$ enters only through $e^{j\hat\omega}$, and $e^{j(\hat\omega+2\pi)}=e^{j\hat\omega}$. The notation shows the period $2\pi$ directly.

> [!question]- Check (slide 29): The table has four cases. For each one, say whether time and frequency are continuous or discrete, and periodic or aperiodic. Which single rule explains the pattern? In which case is the DTFT?
> 1. Continuous and periodic in time (Fourier series): the spectrum is discrete and aperiodic.
> 2. Continuous and aperiodic in time (Fourier transform): the spectrum is continuous and aperiodic.
> 3. Discrete and periodic in time (the DFT view): the spectrum is discrete and periodic.
> 4. Discrete and aperiodic in time (DTFT): the spectrum is continuous and periodic.
> Rule: discrete in one domain means periodic in the other domain, and the reverse. The DTFT is case 4: sampling in time makes its spectrum periodic, and the spectrum stays continuous because the signal is not periodic.

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

> [!warning] Slide error
> The table on slide 30 calls the DTFT "Discrete Fourier Transform (DTFT)". The name is discrete-time Fourier transform. The same slide writes the signals as $s_1,s_2$ and their transforms as $X_1,X_2$: these are the transforms of $s_1$ and $s_2$.

> [!question]- Check (slide 30): Use the table: which transform has discrete time and continuous frequency, and which has discrete time and discrete frequency? Complete: "the z-transform is to the DTFT what the ... is to the ...". What is the Fourier transform of $2s_1(t)-3s_2(t)$?
> Discrete time, continuous frequency: the DTFT. Discrete time, discrete frequency: the DFT.
> The z-transform is to the DTFT what the Laplace transform is to the Fourier transform. In both pairs the first one uses a general complex variable and the second one is a special case of it.
> Linearity: $2S_1(j\omega)-3S_2(j\omega)$. Scale and add in time gives the same scale and add in frequency.

> [!question]- Check (slide 31): A signal has a spectral peak at 300 Hz. It is multiplied by $e^{j2\pi\,1000\,t}$. Where is the peak now? Where are the peaks if it is multiplied by $\cos(2\pi\,1000\,t)$ instead? Compare with what a time delay does.
> Frequency shift: $x(t)e^{j\omega_0t}\leftrightarrow X(j(\omega-\omega_0))$. The whole spectrum moves up by 1000 Hz, so the peak is at 1300 Hz.
> The cosine is $\tfrac12e^{j2\pi1000t}+\tfrac12e^{-j2\pi1000t}$. It gives two copies with half of the size: one shifted up to $300+1000=1300$ Hz and one shifted down to $300-1000=-700$ Hz.
> If the signal is real, it also has a peak at $-300$ Hz, and that peak moves in the same way. The magnitude plot then shows peaks at 700 Hz and 1300 Hz.
> Delay and frequency shift are duals. A delay multiplies the spectrum by $e^{-j\omega t_d}$: the magnitude stays and the phase changes linearly with frequency. A multiplication by $e^{j\omega_0t}$ in time moves the spectrum and changes nothing else.

> [!question]- Check (slide 32): A recording is played at double speed, $x(2t)$. What happens to a component at 200 Hz and to the width of the spectrum? Why does an impulse have a flat spectrum, and what is the dual statement?
> Scaling with $a=2$: $x(2t)\leftrightarrow\tfrac12X(j\omega/2)$. The spectrum is stretched by 2, so 200 Hz moves to 400 Hz and the spectrum is twice as wide. The factor $\tfrac12$ lowers its height.
> An impulse is the limit of a pulse that gets shorter and shorter. Each compression in time stretches the spectrum more. In the limit the spectrum is 1 at all frequencies.
> Dual: a constant in time (infinitely wide) has a spectrum that is an impulse at $\omega=0$. A DC signal has energy only at frequency zero.

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

> [!info] Discrete convolution and windowing (beyond the slides)
> For digital signals the **convolution** (evrişim) integral becomes a sum:
> $$y[n]=\sum_{k}x[k]\,h[n-k]$$
> Read it as: put a copy of $h$ at every sample of $x$, scale the copy by that sample, and add all copies. If $h$ is the response of a filter to a single unit sample (its impulse response), then $y$ is the output of the filter for the input $x$. Two sequences of lengths $L_1$ and $L_2$ give an output of length $L_1+L_2-1$.
> By duality the convolution theorem also holds the other way round: multiplication in time is convolution in frequency. Cutting a frame out of a signal is a multiplication by a window, so the spectrum of the frame is the true spectrum convolved with (smeared by) the spectrum of the window. This is used in [[CS568 L06 - Short-time Fourier analysis]].

> [!question]- Check (slide 33): In $f(t)=\int g(\tau)h(t-\tau)d\tau$, what are $\tau$ and $t$, and which part is the "flip"? In the source-filter model of speech, what are $g$, $h$, $F$, $G$ and $H$? Why is the frequency side easier?
> $\tau$ is the integration variable: it runs over the whole axis. $t$ is the shift: the output is computed for one $t$ at a time. $h(t-\tau)$ as a function of $\tau$ is $h$ mirrored (the minus sign) and moved to position $t$. Without the mirror the operation would be a correlation.
> Speech: $g$ is the source (glottal pulses or noise), $h$ is the impulse response of the vocal tract, $f$ is the speech signal. $G$ is the source spectrum, $H$ the vocal-tract frequency response (formants), $F$ the speech spectrum.
> In frequency the integral becomes a product, $F(\omega)=G(\omega)H(\omega)$. Each frequency is handled alone: the filter just scales the source spectrum.

> [!question]- Check (slide 34): Why is $h(t-\tau)$ drawn mirrored, and what is the output while the two curves do not overlap (third row of the figure, where $h(t-\tau)$ is still left of $g$)? Then compute $[1,2,1]\otimes[1,-1]$ by hand and check it with polynomials.
> As a function of $\tau$, $h(t-\tau)$ is $h$ flipped left to right and shifted by $t$. Increasing $t$ slides it to the right (the arrows). The output at each $t$ is the area under the product of the two curves. With no overlap the product is 0 everywhere, so the output is 0.
> Put a scaled copy of $[1,-1]$ at each sample of $[1,2,1]$:
> $1\cdot[1,-1,0,0]+2\cdot[0,1,-1,0]+1\cdot[0,0,1,-1]=[1,1,-1,-1]$.
> Length: $3+2-1=4$.
> Polynomials: $(1+2z^{-1}+z^{-2})(1-z^{-1})=1+z^{-1}-z^{-2}-z^{-3}$. The coefficients agree.

> [!question]- Check (slide 35): Why can the Fourier transform not be applied directly to a noise-like sound such as /s/? Compute $R(0)$, $R(1)$ and $R(2)$ for $x=[1,2,-1]$ (zero elsewhere). What does $R(0)$ measure, and what is the Fourier transform of $R(j)$ called?
> Noise is random and goes on without decaying. The infinite sum does not give a finite, repeatable result: each stretch of noise gives a different spectrum. So one describes averages instead.
> $R(0)=1^2+2^2+(-1)^2=6$.
> $R(1)=x[1]x[0]+x[2]x[1]=2\cdot1+(-1)\cdot2=0$.
> $R(2)=x[2]x[0]=(-1)\cdot1=-1$.
> $R(0)$ is the energy of the signal and is the largest value: a signal is most similar to itself with no shift. Also $R(-j)=R(j)$.
> The Fourier transform of $R(j)$ is the power spectral density (PSD): how the power is spread over frequency. Here $j$ is the lag in samples, not $\sqrt{-1}$.

> [!question]- Check (slide 36): The exercise computes the autocorrelation and the PSD of a noisy signal. What do the two plots look like for white noise? How do they change if the noise first passes through a low-pass filter?
> White noise: each sample is unrelated to the others. $R(j)$ is one spike at lag 0 (height $\sigma^2$ after dividing the sum by the number of samples) and about 0 at all other lags. The PSD is flat: equal power at all frequencies.
> Low-pass filtered ("coloured") noise (beyond the slides, shown in demo 7): neighbouring samples are now similar, so $R(j)$ decays slowly on both sides of lag 0. The PSD is no longer flat: it is high at low frequencies and falls with frequency.
> The pattern is the scaling idea again: a narrow $R(j)$ goes with a wide PSD, and a wide $R(j)$ with a narrow PSD.

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

Page 1 of the listing (embedded under Links) has no demo yet. It holds the imports and two helpers. `goster(ad)` ("show") opens the figure on the screen, or saves it as `sekiller/<ad>.png` when the script runs with `--kaydet` ("save"). `cal(x, fs, ad, kazanc=None)` ("play") scales the signal, writes it as a 16-bit file `sesler/<ad>.wav`, and plays it if the `sounddevice` package works.

> [!question]- Check (demo page 1): In `cal()`, what does `kazanc = 0.1 / np.sqrt(np.mean(x ** 2))` do, and why is the signal multiplied by 32767 before `astype(np.int16)`? Two sounds differ in level and you want to hear that difference. Why does the default fail, and how does the argument `kazanc` (gain) help?
> `np.sqrt(np.mean(x ** 2))` is the RMS value of the signal. Dividing 0.1 by it gives the gain that brings the RMS to 0.1. So every sound is played at the same level, whatever its amplitude was.
> A 16-bit WAV file stores integers from $-32768$ to $32767$. The signal is a float with full scale $\pm1$, so it is multiplied by 32767 to use that range. This is the quantization step: `astype(np.int16)` keeps only the integer part.
> With the default (`kazanc=None`), each sound gets its own gain, so a very weak signal is amplified to the same loudness as a strong one. The level difference is lost. The fix: compute one gain and pass it as `kazanc` to both calls. Then `cal()` skips the RMS line and both sounds are scaled by the same factor.
> Not on page 1 (from a later page of the script and the full notes): demo 4 does exactly this for the 5 kHz whistle resampled without and with the anti-aliasing filter, so that the filtered version is heard as almost silent.

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
