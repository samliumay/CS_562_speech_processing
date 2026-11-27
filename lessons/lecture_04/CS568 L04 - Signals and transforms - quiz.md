---
course: CS 568 Speech Processing
lesson: CS568 L04 - Signals and transforms
type: quiz
questions: 34
---

# CS568 L04 - Signals and transforms - quiz

Lesson note: [[CS568 L04 - Signals and transforms]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Analog signals and sinusoids (slides 2-4)

### Q1 (slide 2)

A student writes: "Voiced speech is a periodic analog signal, because every glottal cycle is an exact copy of the one before." Which correction is right?

- A) "Analog" is right. "Periodic" is wrong: voiced speech has no repeating pattern at all, like noise.
- B) "Analog" is right. "Periodic" is wrong: the cycles almost repeat, but period and amplitude vary a little.
- C) "Analog" is wrong, since speech is stored as samples. The cycles almost repeat, so it is quasi-periodic.
- D) "Analog" is wrong, since speech is stored as samples. "Periodic" is right: each cycle is the same.

> [!success]- Answer
> **B.** The sound pressure varies continuously in time, so speech is analog, written $x(t)$.
> The glottal cycles are very similar but not exact copies: the period changes a little (jitter) and so does the amplitude (shimmer). Voiced speech is quasi-periodic.
> - A: Voiced speech clearly repeats almost the same cycle. Having no pattern at all describes noise-like sounds such as /s/.
> - C: Speech itself is analog. Samples are a digital copy made by a sampler; they do not change what the sound is.
> - D: Speech is analog, and the cycles are not exact copies.

### Q2 (slide 3)

A signal repeats every 2.5 ms. What is its frequency, and how is $\cos(t)$ written as a sine? (The slide gives $\sin(t)=\cos(t-\pi/2)$.)

- A) 40 Hz, and $\cos(t)=\sin(t+\pi/2)$
- B) 400 Hz, and $\cos(t)=\sin(t-\pi/2)$
- C) 40 Hz, and $\cos(t)=\sin(t-\pi/2)$
- D) 400 Hz, and $\cos(t)=\sin(t+\pi/2)$

> [!success]- Answer
> **D.** $F=1/T=1/0.0025\ \text{s}=400$ Hz.
> Replace $t$ by $t+\pi/2$ in $\sin(t)=\cos(t-\pi/2)$: $\sin(t+\pi/2)=\cos(t)$.
> - A: 40 Hz comes from taking 2.5 ms as 0.025 s.
> - B: Wrong sign. $\sin(t-\pi/2)=-\cos(t)$.
> - C: Both slips: 2.5 ms read as 0.025 s, and the wrong sign of the shift.

### Q3 (slide 4)

A tone is $x(t)=A\cos(2\pi Ft+\varphi)$ with $F=440$ Hz. Which statement is correct?

- A) A larger amplitude $A$ makes each cycle longer, so the period of the wave grows.
- B) The phase $\varphi$ shifts the wave in time and keeps its height and period.
- C) Doubling $F$ doubles the period, because more cycles fit in one second.
- D) The angular frequency is $\omega=440/(2\pi)\approx70.0$ rad/s.

> [!success]- Answer
> **B.** $\varphi$ only moves the wave in time. $A$ sets the height ($-A$ to $+A$), $F$ sets the number of cycles per second.
> - A: $A$ only scales the height. The period is $1/F$ and does not depend on $A$.
> - C: More cycles per second means shorter cycles: doubling $F$ halves the period.
> - D: Divided instead of multiplied. $\omega=2\pi F=2\pi\cdot440\approx2764.6$ rad/s.

## 2. Fourier series (slides 5-6)

### Q4 (slide 5)

A voiced sound has the fundamental period $T_0=8$ ms. Its Fourier series is $x(t)=A_0+\sum_{k\ge1}a_k\cos(k\omega_0t+\varphi_k)$. Where is the 4th harmonic, and what is $A_0$?

- A) 625 Hz. $A_0$ is the mean (DC) value of the signal.
- B) 500 Hz. $A_0$ is the amplitude of the fundamental.
- C) 500 Hz. $A_0$ is the mean (DC) value of the signal.
- D) 625 Hz. $A_0$ is the amplitude of the fundamental.

> [!success]- Answer
> **C.** $F_0=1/0.008=125$ Hz. The $k$-th harmonic is $kF_0$, so the 4th is $4\cdot125=500$ Hz.
> $A_0$ is the constant term: the mean of the signal over a period.
> - A: 625 Hz is $5F_0$. It counts the 4th harmonic as the 4th one above the fundamental, but the fundamental itself is harmonic 1.
> - B: The fundamental has the amplitude $a_1$. $A_0$ has no cosine, so it is a constant.
> - D: Both slips: $5F_0$ instead of $4F_0$, and $A_0$ taken as $a_1$.

### Q5 (slide 6)

A square wave is $+1$ for half of each period and $-1$ for the other half. Its Fourier series has only odd harmonics, with amplitudes $a_k=4/(k\pi)$. Which statement is correct?

- A) $a_7\approx0.182$, $1/7$ of $a_1$. There is no constant term because the mean is 0.
- B) $a_7\approx0.026$, $1/49$ of $a_1$. There is no constant term because the mean is 0.
- C) $a_7\approx0.182$, $1/7$ of $a_1$. There is no constant term because the wave is periodic.
- D) $a_7\approx0.026$, $1/49$ of $a_1$. There is no constant term because the wave is periodic.

> [!success]- Answer
> **A.** $a_7=4/(7\pi)\approx0.182$ and $a_1=4/\pi\approx1.273$: the ratio is $1/7$, the amplitudes fall like $1/k$.
> The wave spends equal time at $+1$ and $-1$, so its mean is 0 and $A_0=0$.
> - B: $4/(49\pi)\approx0.026$ uses $1/k^2$ instead of $1/k$.
> - C: A periodic wave can have a constant term. The same square wave shifted up to 0 and 2 is still periodic and has $A_0=1$.
> - D: Both slips: $1/k^2$ and the wrong reason for $A_0=0$.

## 3. Complex exponentials and Fourier analysis (slides 7-9)

### Q6 (slide 7)

Use $\cos\theta=\tfrac12\left(e^{j\theta}+e^{-j\theta}\right)$ to write $3\cos(\omega t+\pi/4)$ with complex exponentials. What do you get?

- A) $\tfrac32e^{j(\omega t+\pi/4)}+\tfrac32e^{-j(\omega t+\pi/4)}$
- B) $3e^{j(\omega t+\pi/4)}+3e^{-j(\omega t+\pi/4)}$
- C) $\tfrac32e^{j(\omega t+\pi/4)}+\tfrac32e^{-j(\omega t-\pi/4)}$
- D) $3e^{j(\omega t+\pi/4)}$, with no term at $-\omega$

> [!success]- Answer
> **A.** With $\theta=\omega t+\pi/4$: $3\cos\theta=\tfrac32e^{j\theta}+\tfrac32e^{-j\theta}$.
> Two terms, at $+\omega$ and $-\omega$, each with half of the amplitude. The $-\omega$ term has the phase $-\pi/4$. This is where negative frequencies come from.
> - B: The factor $\tfrac12$ of the formula is lost. This sum equals $6\cos\theta$.
> - C: The phase is not negated with the frequency. The term should be $e^{-j\theta}$, which has the phase $-\pi/4$; this one has $+\pi/4$.
> - D: This is complex, not $3\cos\theta$. Only its real part is the cosine.

### Q7 (slide 8)

A sinusoid has amplitude $A=2$ and phase $\varphi=\pi/3$. What is its complex amplitude $X=Ae^{j\varphi}$ in rectangular form?

- A) $1.732+j\,1$
- B) $2+j\,1.047$
- C) $1-j\,1.732$
- D) $1+j\,1.732$

> [!success]- Answer
> **D.** $X=2e^{j\pi/3}=2\cos(\pi/3)+j\,2\sin(\pi/3)=1+j\,1.732$. Its magnitude 2 is the amplitude and its angle $\pi/3$ is the phase.
> - A: $\cos$ and $\sin$ are swapped. That is the angle $\pi/6$.
> - B: The amplitude and the phase ($\pi/3\approx1.047$) are used as the real and imaginary parts.
> - C: Wrong sign of the imaginary part. That is the phase $-\pi/3$.

### Q8 (slide 9)

The analysis equation is $a_k=\frac{1}{T_0}\int_0^{T_0}x(t)e^{-jk\omega_0t}dt$. Which coefficients are non-zero for $x(t)=4\cos(3\omega_0t)$?

- A) Only $a_3=4$
- B) $a_3=2$ and $a_{-3}=2$
- C) $a_3=4$ and $a_{-3}=4$
- D) $a_3=2$ and $a_{-3}=-2$

> [!success]- Answer
> **B.** $4\cos(3\omega_0t)=2e^{j3\omega_0t}+2e^{-j3\omega_0t}$. The probe $e^{-jk\omega_0t}$ keeps only the matching term, so $a_3=a_{-3}=2$. The real amplitude 4 is twice $\lvert a_3\rvert$.
> - A: Takes the real amplitude as the coefficient and forgets the negative frequency.
> - C: Forgets the factor $\tfrac12$ of the inverse Euler formula.
> - D: For a real signal $a_{-k}=a_k^{*}$. Here $a_3=2$ is real, so $a_{-3}=2$ too. Unequal coefficients need imaginary values, as for a sine ($\mp2j$).

## 4. Magnitude and phase (slides 10-11)

### Q9 (slides 10-11)

Exercise ex4p3 builds a second signal from the harmonics of a square wave: the same amplitudes $a_k$, but different phases $\varphi_k$. Compared with the square wave, what happens?

- A) The waveform looks very different, but it sounds almost the same.
- B) The waveform looks very different, and it sounds clearly different.
- C) The waveform looks almost the same, and it sounds almost the same.
- D) The waveform looks almost the same, but it sounds clearly different.

> [!success]- Answer
> **A.** The magnitude spectrum is the same. The harmonics now add up at other time instants, so the waveform changes a lot (demo 3: peak about 1.66 instead of 1.18). The ear is largely insensitive to phase, so the sound is almost the same.
> - B: Phase matters for the waveform, but hardly for hearing.
> - C: Same magnitudes do not give the same waveform. A signal is fixed by magnitude and phase together.
> - D: The effects are reversed: the eye sees the phase change, the ear hardly hears it.

## 5. The Fourier transform (slides 12-13)

### Q10 (slide 12)

Compare the Fourier transform $X(j\omega)=\int_{-\infty}^{\infty}x(t)e^{-j\omega t}dt$ with the analysis equation of the Fourier series, $a_k=\frac{1}{T_0}\int_0^{T_0}x(t)e^{-jk\omega_0t}dt$. Which statement is correct?

- A) The transform integrates over all time, because a non-periodic signal has no period.
- B) The transform keeps the factor $1/T_0$, now with $T_0$ the length of the signal.
- C) The transform uses the probe $e^{+j\omega t}$, the opposite sign of the series.
- D) The transform still gives lines at multiples of $\omega_0$, only more of them.

> [!success]- Answer
> **A.** A non-periodic signal has no period that repeats, so the integral runs over $(-\infty,\infty)$. The other differences: $\omega$ is any real frequency (a continuous spectrum), and there is no factor $1/T_0$.
> - B: There is no averaging factor in the transform.
> - C: Both use the probe $e^{-j(\cdot)t}$. The $+$ sign belongs to the inverse transform.
> - D: There is no $\omega_0$ without a period. The spectrum is a continuous function, not a set of lines.

### Q11 (slide 13)

![[cs568_lecture_04_l4_p013.jpg]]

The lower row of the figure splits a sound into bass, midrange and treble, like the prism above it. Which block limits the highest frequency that can come out on the right, and why?

- A) The "Fourier Transform" block: it splits a sound into only three bands, so treble is the top band.
- B) The "Digitize waveform" block: after sampling at $F_S$, frequencies up to $F_S$ can be represented.
- C) The "Digitize waveform" block: after sampling at $F_S$, frequencies up to $F_S/2$ can be represented.
- D) The "Fourier Transform" block: a transform loses the top frequencies, as a prism loses ultraviolet.

> [!success]- Answer
> **C.** The digitizing block samples the sound. A sampled signal can only represent frequencies up to $F_S/2$; anything above folds back. The Fourier transform splits what it receives and loses nothing.
> - A: The three bands are only a picture. The transform gives a full continuous spectrum.
> - B: The limit is $F_S/2$, not $F_S$. A tone above $F_S/2$ appears at a lower, wrong frequency.
> - D: The transform is invertible: the signal can be rebuilt from it, so nothing is lost there.

## 6. Digital signals and aliasing (slides 14-16)

### Q12 (slide 14)

A recording uses the sampling rate $F_S=16$ kHz. What is the highest frequency it can represent, and what is the normalised frequency $\hat\omega$ of a 2 kHz tone?

- A) 16 kHz, and $\hat\omega=\pi/4$ rad per sample
- B) 8 kHz, and $\hat\omega=1/8$ rad per sample
- C) 16 kHz, and $\hat\omega=1/8$ rad per sample
- D) 8 kHz, and $\hat\omega=\pi/4$ rad per sample

> [!success]- Answer
> **D.** The highest frequency is the Nyquist frequency $F_S/2=8$ kHz.
> $\hat\omega=2\pi F/F_S=2\pi\cdot2000/16000=\pi/4$ rad per sample: one eighth of a cycle per sample.
> - A: 16 kHz is the sampling rate itself. Up to $F_S$ would need only one sample per cycle.
> - B: $2000/16000=1/8$ is in cycles per sample. In radians it needs the factor $2\pi$.
> - C: Both slips: $F_S$ instead of $F_S/2$, and the missing $2\pi$.

### Q13 (slide 15)

A signal has tones at 3 kHz, 10 kHz, 19 kHz and 35 kHz. It is sampled at $F_S=16$ kHz with no anti-aliasing filter. At which frequencies do the four tones appear (in the same order)?

- A) 3, 10, 3 and 3 kHz
- B) 3, 6, 3 and 19 kHz
- C) 3, 6, 3 and 3 kHz
- D) 3, 10, 3 and 19 kHz

> [!success]- Answer
> **C.** A tone $F$ appears at $\lvert F-mF_S\rvert$ with $m$ the integer nearest to $F/F_S$:
> 3 stays. $\lvert10-16\rvert=6$. $\lvert19-16\rvert=3$. $\lvert35-32\rvert=3$.
> Three different tones land on 3 kHz, so after sampling they cannot be told apart.
> - A: Treats 10 kHz as safe because it is below $F_S$. The limit is $F_S/2=8$ kHz.
> - B: Subtracts $F_S$ only once ($m=1$) for 35 kHz. 19 kHz is still above 8 kHz; it needs $m=2$.
> - D: Both slips: 10 kHz kept because it is below $F_S$, and $F_S$ subtracted only once from 35 kHz.

### Q14 (slide 16)

![[cs568_lecture_04_l4_p016.jpg]]

A tone of frequency $f=300$ Hz is sampled as in the figure. Which statement is correct?

- A) In case C ($F_S=400$ Hz) the tone appears at 100 Hz. Case B is unsafe because the samples may hit the zero crossings.
- B) In case C ($F_S=400$ Hz) the tone appears at 200 Hz. Case B is unsafe because the samples may hit the zero crossings.
- C) In case C ($F_S=400$ Hz) the tone appears at 100 Hz. Case B is unsafe because a rate of $2f$ folds the tone to $f/2$.
- D) In case C ($F_S=400$ Hz) the tone appears at 200 Hz. Case B is unsafe because a rate of $2f$ folds the tone to $f/2$.

> [!success]- Answer
> **A.** Case C: $F_S=4f/3=400$ Hz is below $2f$. The alias is $\lvert300-400\rvert=100$ Hz: the dotted wave makes one cycle while the original makes three.
> Case B: the samples happen to hit the peaks. With another starting point they could hit the zero crossings and the tone would vanish. So $F_S$ must be greater than $2f$, not equal.
> - B: A tone above $F_S/2$ is not clipped to $F_S/2=200$ Hz. It folds to $\lvert f-F_S\rvert$.
> - C: At exactly $2f$ the frequency is not halved. The figure shows the samples at the right rate, 2 per cycle.
> - D: Both slips: clipping to $F_S/2$, and a halved frequency at $2f$.

## 7. DTFT and DFT (slides 17-19)

### Q15 (slide 17)

The DTFT is $X(e^{j\hat\omega})=\sum_{n=-\infty}^{\infty}x[n]e^{-j\hat\omega n}$, with $\hat\omega$ in radians per sample. Which two reasons show that a computer cannot evaluate it as written?

- A) The sum runs over infinitely many samples, and the result is complex, which cannot be stored.
- B) $\hat\omega$ is continuous, and the factor $T_S$ of the integral was dropped, so the scale is lost.
- C) The result is complex, which cannot be stored, and the factor $T_S$ was dropped, so the scale is lost.
- D) The sum runs over infinitely many samples, and $\hat\omega$ is continuous: infinitely many frequencies.

> [!success]- Answer
> **D.** Infinitely many samples and infinitely many frequencies. The DFT fixes both: $N$ samples and $N$ frequencies.
> - A: A computer stores complex numbers easily, as two real numbers (or magnitude and phase).
> - B: Dropping $T_S$ only scales the spectrum. It does not stop the computation.
> - C: Neither reason is a real obstacle: complex numbers are stored as pairs, and $T_S$ is only a scale.

### Q16 (slide 18)

A speech frame is 25 ms long at $F_S=16$ kHz. How far apart are its DFT bins, and why not take a 1 s frame instead?

- A) 0.025 Hz. A 1 s frame would mix many different sounds, because speech is not stationary.
- B) 40 Hz. A 1 s frame would make the bins wider, so the spectrum would be coarser.
- C) 40 Hz. A 1 s frame would mix many different sounds, because speech is not stationary.
- D) 0.025 Hz. A 1 s frame would make the bins wider, so the spectrum would be coarser.

> [!success]- Answer
> **C.** $N=0.025\cdot16000=400$ samples, so the spacing is $F_S/N=16000/400=40$ Hz ($=1/0.025$ s).
> A 1 s frame would give 1 Hz spacing, but the sounds change within that second: the spectrum would be an average over many sounds.
> - A: $0.025=N/F_S$ is the frame length in seconds. The spacing is its inverse, $F_S/N$.
> - B: A longer frame gives more bins and finer spacing, not coarser.
> - D: Both slips: $N/F_S$ instead of $F_S/N$, and the wrong direction for a longer frame.

### Q17 (slide 19)

Both inverse transforms rebuild $x[n]$ from its spectrum: the inverse DTFT with an integral, the inverse DFT with a sum. Over what does each one integrate or sum?

- A) DTFT: an integral over $(-\infty,\infty)$. DFT: a sum over the frequency index $k$.
- B) DTFT: an integral over $[-\pi,\pi]$. DFT: a sum over the frequency index $k$.
- C) DTFT: an integral over $[-\pi,\pi]$. DFT: a sum over the time index $n$.
- D) DTFT: an integral over $(-\infty,\infty)$. DFT: a sum over the time index $n$.

> [!success]- Answer
> **B.** $x[n]=\frac{1}{2\pi}\int_{-\pi}^{\pi}X(e^{j\hat\omega})e^{j\hat\omega n}d\hat\omega$ and $x[n]=\frac1N\sum_{k=0}^{N-1}X[k]e^{j2\pi kn/N}$.
> The DTFT is periodic with period $2\pi$, so one period holds everything. The inverse DFT adds up the $N$ frequency components, so it sums over $k$.
> - A: The slide writes $(-\infty,\infty)$, but that is a slide error: the integral over infinitely many periods diverges.
> - C: The slide writes the sum over $n$, but that is a slide error: $n$ is the output index, the sum runs over the frequencies.
> - D: Both slide errors at once.

## 8. The DFT as a matrix, examples, FFT (slides 20-26)

### Q18 (slide 20)

For $N=4$ the DFT is $X[k]=\sum_{n=0}^{3}x[n]W_4^{kn}$ with $W_4=e^{-j2\pi/4}$. What is $X[1]$ for $x=[1,2,3,4]$?

- A) $-2+2j$
- B) $-2-2j$
- C) $-2$
- D) $-0.5+0.5j$

> [!success]- Answer
> **A.** $W_4=e^{-j\pi/2}=-j$, so row 1 is $[1,-j,-1,j]$.
> $X[1]=1\cdot1+2\cdot(-j)+3\cdot(-1)+4\cdot j=-2+2j$.
> - B: Uses $W_4=+j$, the sign of the inverse DFT. Row 1 becomes $[1,j,-1,-j]$.
> - C: Uses row 2, $[1,-1,1,-1]$: the row index is off by one.
> - D: Puts the factor $1/N$ in the forward DFT. It belongs to the inverse.

### Q19 (slides 21-22)

The 8-point DFT matrix has the rows $k=0,\dots,7$. Row $k$ holds $e^{-j2\pi kn/8}$ for $n=0,\dots,7$: a unit vector that turns by a fixed step per sample. Which row oscillates fastest?

- A) Row 7. Its step $7\pi/4$ is the largest, so it turns fastest.
- B) Row 4. Its step is $\pi$, so the entries are $1,-1,1,-1,\dots$
- C) Row 4. Its step is $\pi/2$, so the entries are $1,-j,-1,j,\dots$
- D) Row 7. Its step is $\pi$, so the entries are $1,-1,1,-1,\dots$

> [!success]- Answer
> **B.** The step is $2\pi k/8$. For $k=4$ it is $\pi$: the vector flips every sample. That is 2 samples per cycle, the Nyquist frequency.
> - A: A full turn does not count. $7\pi/4=2\pi-\pi/4$, so row 7 turns as slowly as row 1, only in the other direction (its complex conjugate).
> - C: Forgets the 2 in $2\pi k/N$. A step of $\pi/2$ belongs to row 2.
> - D: Takes the last row as the highest frequency. Row 7 has the step $7\pi/4$, the same as $-\pi/4$, not $\pi$.

### Q20 (slide 23)

At $F_S=2$ kHz, a 1 s recording of $\sin(2\pi\,10\,t)$ gives DFT peaks at bins 10 and 1990, each of height 1000. Now the recording is only 0.5 s long and the sine has 50 Hz ($F_S$ unchanged). Where are the peaks and how high are they?

- A) At bins 50 and 950, height 500
- B) At bins 25 and 975, height 1000
- C) At bins 25 and 975, height 500
- D) At bins 50 and 950, height 1000

> [!success]- Answer
> **C.** $N=0.5\cdot2000=1000$, so the bin spacing is $F_S/N=2$ Hz. 50 Hz is bin $50/2=25$, the mirror is bin $1000-25=975$.
> The peak height is $N/2=500$: half as many samples give half the height.
> - A: Keeps the old 1 Hz spacing, so the bin index equals the frequency in Hz.
> - B: Keeps the old height. The height is (amplitude) $\cdot N/2$ and $N$ halved.
> - D: Both slips: the old spacing and the old height.

### Q21 (slide 24)

At $F_S=2$ kHz, a 1 s recording of $10\sin(2\pi\,10\,t)+3\sin(2\pi\,100\,t)$ gives a magnitude plot (0 to 200 Hz) with a peak at 10 Hz and a lower peak at 100 Hz. Suppose the second term were $3\sin(2\pi\,1900\,t)$ instead. How does the magnitude plot from 0 to 200 Hz change?

- A) Only the 10 Hz peak is left, because 1900 Hz is above the Nyquist frequency.
- B) It does not change. Only the phase at 100 Hz flips by $\pi$.
- C) The second peak moves from 100 Hz to 900 Hz, out of the plotted range.
- D) The peaks stay, but the peak at 100 Hz now points downward.

> [!success]- Answer
> **B.** 1900 Hz is above $F_S/2=1000$ Hz. Its alias is $\lvert1900-2000\rvert=100$ Hz. The samples of $3\sin(2\pi\,1900\,t)$ equal those of $-3\sin(2\pi\,100\,t)$, so the magnitude is the same and only the sign (phase $\pi$) changes.
> - A: Sampling does not remove a tone above $F_S/2$. Without a filter it folds into the range.
> - C: Subtracts the Nyquist frequency instead of $F_S$: $1900-1000=900$.
> - D: A magnitude cannot be negative. The sign change goes into the phase.

### Q22 (slide 25)

A signal has 3000 samples and you use a radix-2 FFT, which needs $N=2^k$. What does it do with the signal, and what happens to the frequency resolution?

- A) It appends 1096 zeros to reach 4096. The resolution improves, because the bins are closer.
- B) It drops 952 samples to reach 2048. The resolution is that of the full recording.
- C) It drops 952 samples to reach 2048. The resolution improves, because the bins are closer.
- D) It appends 1096 zeros to reach 4096. The resolution is that of the real recording.

> [!success]- Answer
> **D.** The next power of two is $2^{12}=4096$, so 1096 zeros are appended. The FFT costs $4096\cdot12=49\,152$ operations instead of $4096^2=16\,777\,216$ (about 341 times fewer).
> The zeros add no information. The bins get closer and the plot looks smoother, but two close tones are not separated better.
> - A: Closer bins are not finer resolution. Resolution is about $1/$(length of the real recording).
> - B: The FFT pads up, it does not throw data away. With fewer real samples the resolution would get worse.
> - C: Both slips: dropping samples, and taking closer bins for better resolution.

### Q23 (slide 26)

A 16-point radix-2 FFT is drawn as a butterfly graph. Its outputs on the right are not in the order $X(0),X(1),X(2),\dots$ How many butterfly stages does it have, and which output stands at position 5 (counting from 0)?

- A) 8 stages, and position 5 holds $X(10)$
- B) 4 stages, and position 5 holds $X(5)$
- C) 4 stages, and position 5 holds $X(10)$
- D) 8 stages, and position 5 holds $X(5)$

> [!success]- Answer
> **C.** $\log_2 16=4$ stages, each with about $N$ operations: this gives $N\log_2N$.
> The outputs come in bit-reversed order. Position 5 is $0101$; reversed it is $1010=10$, so $X(10)$.
> - A: 8 is $N/2$, the number of butterflies in one stage, not the number of stages.
> - B: Position 5 would hold $X(5)$ only in natural order, which the graph does not give.
> - D: Both slips: $N/2$ stages, and the natural order.

## 9. The z-transform and digital spectra (slides 27-29)

### Q24 (slide 27)

A sequence has $x[0]=2$, $x[1]=0$, $x[2]=-1$ and is 0 elsewhere. Its $z$-transform is $X(z)=\sum_n x[n]z^{-n}$. Which line gives $X(z)$ and the values of $X(e^{j\hat\omega})$ at $\hat\omega=0,\ \pi/2,\ \pi$?

- A) $X(z)=2+z^{-2}$; values $3,\ 1,\ 3$
- B) $X(z)=2-z^{-1}$; values $1,\ 2+j,\ 3$
- C) $X(z)=2z^{-2}-1$; values $1,\ -3,\ 1$
- D) $X(z)=2-z^{-2}$; values $1,\ 3,\ 1$

> [!success]- Answer
> **D.** Each sample is the coefficient of $z^{-n}$: $X(z)=2-z^{-2}$. On the unit circle $X(e^{j\hat\omega})=2-e^{-j2\hat\omega}$.
> $\hat\omega=0$: $2-1=1$. $\hat\omega=\pi/2$: $2-e^{-j\pi}=3$. $\hat\omega=\pi$: $2-e^{-j2\pi}=1$. This is the DTFT.
> - A: The minus sign of $x[2]=-1$ is lost.
> - B: $x[2]$ is put at $z^{-1}$: the delay is off by one sample.
> - C: The order is reversed: $x[0]$ is put at $z^{-2}$ and $x[2]$ at $z^{0}$.

### Q25 (slide 28)

A real signal is sampled at $F_S=8$ kHz. Its spectrum has a peak at 1 kHz. At which other frequencies up to 20 kHz does the same peak appear in the spectrum of the sampled signal?

- A) 7, 9, 15 and 17 kHz
- B) 9 and 17 kHz
- C) 3, 5, 7, 9, 11, 13, 15, 17 and 19 kHz
- D) 15 and 17 kHz

> [!success]- Answer
> **A.** A real signal has the peak at $+1$ and $-1$ kHz. The spectrum repeats with the period $F_S=8$ kHz, so the peaks are at $8k\pm1$ kHz: 7, 9, 15 and 17 kHz. Nothing new: all is already in 0 to 4 kHz.
> - B: Forgets the peak at $-1$ kHz of a real signal.
> - C: Uses the period 4 kHz, the Nyquist frequency (the slide's wording). The period is $F_S$.
> - D: Uses the period $2F_S=16$ kHz.

### Q26 (slide 29)

The table of slide 29 sorts signals by time: continuous or discrete, periodic or aperiodic. A discrete-time, aperiodic signal has the DTFT. What is its spectrum, and which rule explains it?

- A) Discrete and periodic: sampling in time also samples the spectrum.
- B) Continuous and periodic: discrete in one domain means periodic in the other.
- C) Continuous and aperiodic: an aperiodic signal has an aperiodic spectrum.
- D) Discrete and aperiodic: a sequence of samples has a spectrum of lines.

> [!success]- Answer
> **B.** Discrete in one domain means periodic in the other, and the reverse. Sampling in time makes the spectrum periodic. The signal is not periodic, so the spectrum stays continuous.
> - A: Periodicity in time makes the spectrum discrete. This signal is aperiodic.
> - C: Aperiodic is true for continuous-time signals. Discrete time makes the spectrum periodic.
> - D: Lines need a periodic signal, and the period of the spectrum is missing.

## 10. Properties of the transforms (slides 30-32)

### Q27 (slide 30)

Which transform has discrete time and continuous frequency, and how does the sentence "the $z$-transform is to the DTFT what the ... is to the ..." end?

- A) The DTFT (discrete-time Fourier transform). The Laplace transform is to the Fourier transform.
- B) The DFT (discrete Fourier transform). The Laplace transform is to the Fourier transform.
- C) The DTFT (discrete-time Fourier transform). The Fourier transform is to the Fourier series.
- D) The DFT (discrete Fourier transform). The Fourier transform is to the Fourier series.

> [!success]- Answer
> **A.** Discrete time with continuous $\hat\omega$ is the DTFT. (Slide 30 calls it "Discrete Fourier Transform (DTFT)"; the correct name is discrete-time Fourier transform.)
> The $z$-transform uses any complex $z$; on the unit circle it is the DTFT. The Laplace transform uses any complex $s$; on the imaginary axis it is the Fourier transform.
> - B: The DFT has discrete frequency too: only $N$ bins.
> - C: The Fourier transform is not a special case of the Fourier series. It is the extension to non-periodic signals.
> - D: Both slips: DFT for continuous frequency, and the wrong pair.

### Q28 (slide 31)

A real signal has a spectral peak at 300 Hz. It is multiplied by $\cos(2\pi\,1000\,t)$. Where are the peaks in the magnitude plot (positive frequencies)?

- A) At 1300 Hz only
- B) At 300 Hz and 1300 Hz
- C) At 700 Hz and 1300 Hz
- D) At 700 Hz only

> [!success]- Answer
> **C.** $\cos(2\pi\,1000\,t)=\tfrac12e^{j2\pi1000t}+\tfrac12e^{-j2\pi1000t}$: two copies of the spectrum, shifted up and down by 1000 Hz.
> The real signal has peaks at $\pm300$ Hz. They move to $1300,\ -700$ and $700,\ -1300$ Hz. On the positive axis: 700 and 1300 Hz.
> - A: Treats the cosine as the single exponential $e^{j2\pi1000t}$.
> - B: The original peak does not stay. Every component is shifted.
> - D: Only shifts down. The cosine shifts both ways.

### Q29 (slide 32)

A recording is played at double speed, $x(2t)$. Scaling gives $x(at)\leftrightarrow\frac{1}{\lvert a\rvert}X(j\omega/a)$. Which statement is correct?

- A) A 200 Hz component moves to 100 Hz: shorter in time means narrower in frequency.
- B) A 200 Hz component moves to 400 Hz, and the spectrum becomes twice as wide.
- C) A 200 Hz component stays at 200 Hz. Only the duration of the sound halves.
- D) A 200 Hz component moves to 400 Hz, and the spectrum becomes twice as high.

> [!success]- Answer
> **B.** With $a=2$ the spectrum is $\tfrac12X(j\omega/2)$: a value at $\omega$ now appears at $2\omega$. So 200 Hz moves to 400 Hz and the spectrum is twice as wide. The factor $\tfrac12$ lowers it.
> - A: Wrong direction. Shorter in time means wider in frequency.
> - C: Each cycle is twice as short, so each frequency doubles.
> - D: The factor is $1/\lvert a\rvert=\tfrac12$, so the height halves.

## 11. Convolution and stochastic signals (slides 33-36)

### Q30 (slide 33)

In the source-filter model, speech is $f(t)=\int g(\tau)h(t-\tau)d\tau$: $g$ is the source (glottal pulses or noise) and $h$ is the impulse response of the vocal tract. What is the speech spectrum $F(\omega)$, and why is the frequency side easier?

- A) $F=G+H$: the vocal tract adds its formant peaks to the source spectrum.
- B) $F=G\otimes H$: the same flip and slide, but on the spectra instead.
- C) $F=G\cdot H$: at each frequency the vocal tract only scales the source.
- D) $F=H$ alone: the vocal tract sets the spectrum, the source only the level.

> [!success]- Answer
> **C.** Convolution in time is multiplication in frequency: $F(\omega)=G(\omega)H(\omega)$. Each frequency is handled alone, with no integral.
> - A: The spectra multiply. (A sum appears only after taking the logarithm, which is a different quantity.)
> - B: Convolution in frequency belongs to multiplication in time, for example windowing.
> - D: The source spectrum matters too: voiced and noise sources through the same tract give different sounds.

### Q31 (slide 34)

Discrete convolution is $y[n]=\sum_k x[k]h[n-k]$: $h$ is flipped and slid along $x$. What is $[1,2,1]\otimes[1,-1]$?

- A) $[-1,-1,1,1]$
- B) $[1,1,-1]$
- C) $[1,3,3,1]$
- D) $[1,1,-1,-1]$

> [!success]- Answer
> **D.** Put a scaled copy of $[1,-1]$ at each sample of $[1,2,1]$:
> $1\cdot[1,-1,0,0]+2\cdot[0,1,-1,0]+1\cdot[0,0,1,-1]=[1,1,-1,-1]$. Length $3+2-1=4$.
> Check: $(1+2z^{-1}+z^{-2})(1-z^{-1})=1+z^{-1}-z^{-2}-z^{-3}$.
> - A: Slides $h$ without flipping it. That is the correlation.
> - B: Cuts the output to the length of the input. The full output has $L_1+L_2-1$ samples.
> - C: Drops the minus sign of $h$: this is $[1,2,1]\otimes[1,1]$.

### Q32 (slide 35)

The autocorrelation is $R(j)=\sum_n x[n]x[n-j]$. For $x[0]=1$, $x[1]=2$, $x[2]=-1$ (zero elsewhere), what are $R(0)$, $R(1)$ and $R(2)$?

- A) $2,\ 0,\ -0.33$
- B) $6,\ 0,\ -1$
- C) $6,\ 2,\ -1$
- D) $2,\ 0.67,\ -0.33$

> [!success]- Answer
> **B.** $R(0)=1^2+2^2+(-1)^2=6$, the energy.
> $R(1)=x[1]x[0]+x[2]x[1]=2-2=0$. $R(2)=x[2]x[0]=-1$.
> - A: Divides each sum by the number of samples, 3. The definition here has no such factor.
> - C: $R(1)=2$ keeps only the first product $x[1]x[0]$.
> - D: Both slips: only the first product in $R(1)$, and every sum divided by 3.

### Q33 (slide 36)

For white noise the autocorrelation $R(j)$ is one spike at lag 0 and the power spectral density (PSD) is flat. The noise now first passes through a low-pass filter. How do the two plots change?

- A) $R(j)$ decays slowly on both sides of lag 0. The PSD rises with frequency.
- B) $R(j)$ stays one spike at lag 0. The PSD falls with frequency.
- C) $R(j)$ stays one spike at lag 0. The PSD rises with frequency.
- D) $R(j)$ decays slowly on both sides of lag 0. The PSD falls with frequency.

> [!success]- Answer
> **D.** After low-pass filtering, neighbouring samples are similar, so $R(j)$ gets wider. The filter removes high frequencies, so the PSD falls with frequency. A wide $R(j)$ goes with a narrow PSD.
> - A: Wrong direction for the PSD. A low-pass filter keeps the low frequencies and removes the high ones.
> - B: A falling PSD means correlated neighbours, so $R(j)$ cannot stay a single spike.
> - C: Both slips: $R(j)$ gets wider, and a low-pass filter makes the PSD fall, not rise.

## 12. The demo script L4_demolar.py

### Q34 (demo page 1)

The demo script plays sounds with this function (`kazanc` means gain):

```python
def cal(x, fs, ad, kazanc=None):
    if kazanc is None:
        kazanc = 0.1 / np.sqrt(np.mean(x ** 2))
    x = x * kazanc
    wavfile.write(f"sesler/{ad}.wav", int(fs), (x * 32767).astype(np.int16))
```

Two sounds differ in level and you want to hear that difference. Why does the default fail, and what is the fix?

- A) Each call scales its sound to RMS 0.1, so both play equally loud. Pass one shared gain as `kazanc` to both calls.
- B) Each call scales its sound to RMS 0.1, so both play equally loud. Pass each sound its own gain from its own RMS.
- C) The factor 32767 clips the louder sound, so the levels meet. Pass one shared gain as `kazanc` to both calls.
- D) The factor 32767 clips the louder sound, so the levels meet. Pass each sound its own gain from its own RMS.

> [!success]- Answer
> **A.** `np.sqrt(np.mean(x ** 2))` is the RMS. With `kazanc=None` every sound is scaled to RMS 0.1, so a weak sound becomes as loud as a strong one. With one shared gain both are scaled by the same factor and the level difference stays.
> The factor 32767 only maps the full scale $\pm1$ to the 16-bit range $-32768$ to $32767$.
> - B: A gain from each sound's own RMS is exactly what the default does.
> - C: After scaling to RMS 0.1 the signal is far below $\pm1$, so nothing clips. The level is lost in the RMS line.
> - D: Both slips: the wrong cause, and the default behaviour as the fix.
