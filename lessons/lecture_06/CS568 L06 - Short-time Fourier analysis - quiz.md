---
course: CS 568 Speech Processing
lesson: CS568 L06 - Short-time Fourier analysis
type: quiz
questions: 25
---

# CS568 L06 - Short-time Fourier analysis - quiz

Lesson note: [[CS568 L06 - Short-time Fourier analysis]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Overview: why a short-time transform (slides 2-4)

### Q1 (slide 2)

The DTFT of $x[n]$ is $X(e^{j\hat\omega})=\sum_n x[n]e^{-j\hat\omega n}$. The DFT of size $N$ uses the index $k$. Take $F_s=8$ kHz and $N=256$. Which statement is correct?

- A) $\hat\omega$ takes only the $N$ values $2\pi k/N$, in the DTFT and in the DFT.
- B) Neighbouring DFT frequencies are $F_s/(2N)\approx15.6$ Hz apart, as the bins cover 0 to $F_s/2$.
- C) Neighbouring DFT frequencies are $F_s/N=31.25$ Hz apart, since $F_k=kF_s/N$.
- D) The DTFT sum runs over $N$ samples and the DFT sum runs over all $n$.

> [!success]- Answer
> **C.** $\hat\omega$ is continuous: the DTFT exists at every frequency. The DFT keeps $N$ samples of it, at $\hat\omega_k=2\pi k/N$, that is $F_k=kF_s/N$ Hz.
> Spacing: $F_s/N=8000/256=31.25$ Hz.
> - A: Only the DFT is limited to $N$ frequencies. In the DTFT $\hat\omega$ is continuous from $-\pi$ to $\pi$.
> - B: The $N$ bins cover a full period, 0 to $F_s$, not 0 to $F_s/2$. Dividing $F_s/2$ by $N$ gives the 15.6 Hz.
> - D: The two sums are swapped. The DTFT sums over all $n$, the DFT over the $N$ samples $n=0,\dots,N-1$.

### Q2 (slide 3)

The figure shows a signal (left), the DFT magnitude of the whole signal (middle) and its STFT (right). Which statement is correct?

![[cs568_lecture_06_l6_p003.jpg]]

- A) The right plot shows energy at about 100 Hz first, then a jump up to about 200 Hz near the middle.
- B) The middle plot shows that 100 Hz comes first, as its peak lies left of the 200 Hz peak.
- C) The middle plot shows that both tones are present at every instant of the signal.
- D) The right plot shows energy at about 200 Hz first, then a jump down to about 100 Hz near the middle.

> [!success]- Answer
> **A.** In the right plot time (frames) is on the horizontal axis. The energy sits at about 100 Hz in the first frames and moves to about 200 Hz near frame 35 to 40.
> This matches the left plot, which oscillates faster in its second half. The middle plot only says both frequencies are present, not when.
> - B: The horizontal axis of the middle plot is frequency, not time. Left means lower frequency, not earlier.
> - C: A DFT peak only says the frequency is present somewhere in the signal. Here each tone lasts about half of it.
> - D: The reading is reversed. The lower band (about 100 Hz) comes in the first frames, the upper band (about 200 Hz) later.

### Q3 (slide 4)

A 2 s recording at $f_s=16$ kHz is cut into frames with a 25 ms window and a 10 ms hop. There is no padding at the ends. How many full frames are there?

- A) 197
- B) 198
- C) 80
- D) 200

> [!success]- Answer
> **B.** $N_w=0.025\cdot16000=400$, $L=0.010\cdot16000=160$, $N_s=2\cdot16000=32000$.
> Frames $=1+\lfloor(N_s-N_w)/L\rfloor=1+\lfloor31600/160\rfloor=1+197=198$.
> - A: Forgot the $+1$: $\lfloor(32000-400)/160\rfloor=197$ counts only the frames after the first one.
> - C: Hop and window swapped: $1+\lfloor(32000-160)/400\rfloor=1+79=80$.
> - D: Divided the whole length by the hop, $32000/160=200$, and ignored that the first frame needs $N_w$ samples.

## 2. Windowing functions (slides 5-6)

### Q4 (slide 5)

The figure shows the spectrum of a window in dB. Its highest side lobe is about 13 dB below the main beam. A signal contains two sinusoids. Which statement is correct?

![[cs568_lecture_06_l6_p005.jpg]]

- A) Rectangular window. The side-lobe level decides how close the two sinusoids can be and still give two peaks.
- B) Hamming window. The main-lobe width decides how close the two sinusoids can be and still give two peaks.
- C) Rectangular window. The main-lobe width decides how close the two sinusoids can be and still give two peaks.
- D) Hamming window. The side-lobe level decides how close the two sinusoids can be and still give two peaks.

> [!success]- Answer
> **C.** A first side lobe about 13 dB down is the rectangular window.
> Main-lobe width sets the frequency resolution. The side-lobe level sets the leakage: how weak the second sinusoid can be and still be seen.
> - A: Roles swapped. The side-lobe level decides how weak a component can be and still be seen, not how close.
> - B: Wrong window. The first Hamming side lobe is about 43 dB down, not 13 dB.
> - D: Two slips: Hamming has its first side lobe about 43 dB down, and the side-lobe level sets leakage, not resolution.

### Q5 (slide 6)

The figure shows a rectangular, a Hann and a Hamming window of the same length, with their spectra in dB. Which statement is correct?

![[cs568_lecture_06_l6_p006.jpg]]

- A) A Hamming window resolves two close sinusoids of equal amplitude better than a rectangular one, as its side lobes are lower.
- B) Far from the peak the Hamming side lobes are the lowest, as its first side lobe (about $-43$ dB) is lowest.
- C) The rectangular window hides a weak, far-away component least, as its main lobe is the narrowest.
- D) Far from the peak the Hann side lobes are the lowest, so Hann hides a weak, far-away component least.

> [!success]- Answer
> **D.** In the plots the Hann side lobes keep falling and are the lowest far from the peak. The Hamming ones stay at an almost flat level, the rectangular ones decay slowly and stay high.
> - A: Lower side lobes mean less leakage, not better resolution. The Hamming main lobe is about twice as wide ($4/T_w$ against $2/T_w$), so its resolution is worse.
> - B: The first side lobe is the lowest only close to the peak. Further out the Hamming side lobes stay almost flat, and the Hann ones fall below them.
> - C: A narrow main lobe helps with close components. A far, weak component is hidden by side lobes, and the rectangular ones are the highest.

## 3. The STFT and the spectrogram (slides 7-8)

### Q6 (slide 7, symbols)

The STFT is $X(n,\omega)=\sum_{m}x[m]\,w[n-m]\,e^{-j\omega m}$, and the spectrogram is $S(n,k)=\log\lvert X(n,k)\rvert^2$. Which statement is correct?

- A) $m$ is the position of the frame, and $n$ is the running time index of the sum.
- B) $\omega$ is an integer index from 0 to $N-1$ that counts the DFT bins.
- C) $w[n-m]$ is the window moved to time $n$, and $S(n,k)$ drops the phase of $X(n,k)$.
- D) $S(n,k)$ drops the time index $n$, as it averages $\lvert X\rvert^2$ over all frames.

> [!success]- Answer
> **C.** $w[n-m]$ is the window flipped and placed at time $n$: it keeps the samples around $n$. $S(n,k)$ uses only $\lvert X(n,k)\rvert$, so the phase is lost.
> - A: Swapped. $n$ is the frame position (the time axis of the STFT), $m$ is the summation index.
> - B: That is $k$. $\omega$ is continuous, in radians per sample. $k$ appears only after sampling at $\omega=2\pi k/N$.
> - D: That would be a long-term spectrum. The spectrogram keeps one column per frame $n$.

### Q7 (slide 7, DFT size)

$f_s=8$ kHz, Hamming window with $N_w=200$, DFT size $N=512$. Two sinusoids are 20 Hz apart. Which statement is correct?

- A) The main lobe is about 80 Hz wide, so they give one peak. With $N=4096$ they would separate.
- B) The main lobe is about 160 Hz wide, so they give one peak. $N=4096$ would not change this.
- C) The main lobe is about 160 Hz wide, so they give one peak. With $N=4096$ they would separate.
- D) The main lobe is about 80 Hz wide, so they give one peak. $N=4096$ would not change this.

> [!success]- Answer
> **B.** Main lobe of Hamming: about $4f_s/N_w=4\cdot8000/200=160$ Hz, far more than 20 Hz: one peak.
> The bins are $8000/512=15.6$ Hz apart, but the bin spacing is not the resolution. A larger $N$ is zero padding. It samples the same smeared curve more densely. Only a longer window separates the two.
> - A: Two slips: the rectangular width $2f_s/N_w=80$ Hz, and zero padding claimed to improve resolution.
> - C: Zero padding claimed to improve resolution. $N=4096$ gives bins every 1.95 Hz of the same 160 Hz wide lobe.
> - D: Used the rectangular width $2f_s/N_w=80$ Hz. Hamming has about $4f_s/N_w=160$ Hz.

### Q8 (slide 8, figure)

The figure shows a narrowband and a wideband spectrogram of the same voiced sound. How is $F_0$ read from them?

![[cs568_lecture_06_l6_p008.jpg]]

- A) In the narrowband plot, $F_0$ is the width in Hz of one single horizontal line.
- B) In the wideband plot, $F_0$ is the time between two neighbouring vertical stripes.
- C) In the narrowband plot, $F_0$ is the spacing in Hz between neighbouring horizontal lines.
- D) In the wideband plot, $F_0$ is the top of the bright energy band, about 1000 Hz.

> [!success]- Answer
> **C.** Each horizontal line is one harmonic $kF_0$, so neighbouring lines are $F_0$ apart.
> In the wideband plot each vertical stripe is one glottal pulse. The time between two stripes is $T_0$, and $F_0=1/T_0$.
> - A: The width of a line is the main-lobe width of the window, not the pitch.
> - B: Forgot the reciprocal. That time is the pitch period $T_0$; $F_0=1/T_0$.
> - D: The broad band is where the formants are (the vocal-tract envelope). It says nothing about the pitch.

### Q9 (slide 8, window length)

A voice has $F_0=125$ Hz. Hamming windows of 4 ms and of 40 ms are used. Which statement is correct?

- A) 40 ms gives a narrowband plot: its main lobe, about 100 Hz, is narrower than $F_0$, so the harmonics separate.
- B) 40 ms gives a narrowband plot: it holds about 5 pitch periods, so it resolves the single glottal pulses.
- C) 4 ms gives a wideband plot: it holds about 2 pitch periods, so each frame shows two glottal pulses.
- D) 4 ms gives a wideband plot: its main lobe, about 250 Hz, covers about 2 harmonics, so they merge.

> [!success]- Answer
> **A.** Main lobe about $4/T_w=4/0.040=100$ Hz $<125$ Hz, so each harmonic is its own peak: horizontal lines, narrowband.
> The 4 ms window gives about $4/0.004=1000$ Hz, covering about 8 harmonics: wideband.
> - B: The count is right ($40/8=5$), the effect is reversed. 5 periods in one frame are averaged together, so the pulses are not resolved. The pulses are resolved by the short window.
> - C: Divided the wrong way, $8/4=2$. $T_0=1/125=8$ ms, so 4 ms is half a pitch period: each frame sees a pulse or a gap.
> - D: Used $1/T_w=250$ Hz and forgot the factor 4 of Hamming. The main lobe is about 1000 Hz and covers about 8 harmonics.

## 4. Filtering view and filter bank (slides 9-14)

### Q10 (slide 9)

The STFT is $X(n,\omega)=\sum_m x[m]\,w[n-m]\,e^{-j\omega m}$. In the Fourier view, $n$ is fixed and $\omega$ varies. In the filtering view the frequency is fixed at $\omega_0$. Which statement is correct?

- A) $X(n,\omega_0)$ is one spectrum: for each $n$ it lists all frequencies of the frame.
- B) $w[n]$ acts as a high-pass filter: it removes the slowly varying part of $x[n]e^{-j\omega_0n}$.
- C) $w[n]$ acts as a band-pass filter centred at $\omega_0$ on $x[n]e^{-j\omega_0n}$.
- D) $X(n,\omega_0)$ is one signal in time, and $w[n]$ acts as a low-pass filter on $x[n]e^{-j\omega_0n}$.

> [!success]- Answer
> **D.** With $\omega_0$ fixed and $n$ varying, $X(n,\omega_0)=\left(x[n]e^{-j\omega_0n}\right)*w[n]$: a filter output. $W(\omega)$ is the main lobe around 0, so $w[n]$ is low-pass.
> - A: That is the Fourier view. With $\omega_0$ fixed, only $n$ varies, so the result is a time signal.
> - B: The window spectrum is a main lobe around $\omega=0$. It keeps the slowly varying part: low-pass.
> - C: After the multiplication the wanted band is already at 0, so the filter there is low-pass. The band-pass filter is $w[n]e^{j\omega_0n}$ of the other form.

### Q11 (slide 10)

$x[n]$ is first multiplied by $e^{-j\omega_0n}$ and then filtered by the window $w[n]$, used as a filter. In $X(\omega)$ a band of interest lies around $\omega_0$. Which statement is correct?

- A) The multiplication shifts the whole spectrum right by $\omega_0$, so the band is at $2\omega_0$. The filter keeps it.
- B) The multiplication moves only the band to 0 and leaves the rest in place. The filter then keeps the band.
- C) The multiplication shifts the whole spectrum left by $\omega_0$, so the band is at 0. The filter keeps only this band.
- D) The multiplication shifts the whole spectrum left by $\omega_0$. The filter then moves the band back to $\omega_0$.

> [!success]- Answer
> **C.** Multiplying by $e^{-j\omega_0n}$ gives $X(\omega+\omega_0)$: everything moves left by $\omega_0$.
> The low-pass filter leaves $W(\omega)X(\omega+\omega_0)$. The rest of the spectrum is removed.
> - A: Sign slip (slide 9 says 'up to'). With $e^{-j\omega_0n}$ the shift is to the left. A low-pass filter at 0 would not keep a band at $2\omega_0$ anyway.
> - B: A multiplication by $e^{-j\omega_0n}$ shifts the whole spectrum. It cannot pick out one band.
> - D: A filter only weights frequencies, it does not shift them. Moving the band back up is done at synthesis, with $e^{+j\omega_0n}$.

### Q12 (slide 11)

Form 1: multiply $x[n]$ by $e^{-j\omega_0n}$, then filter with $w[n]$. Form 2: filter $x[n]$ with $w[n]e^{j\omega_0n}$, then multiply by $e^{-j\omega_0n}$. Which statement is correct?

- A) The filter of form 2 is low-pass like $w[n]$, since $e^{j\omega_0n}$ has magnitude 1 and changes no band.
- B) The filter of form 2 is a band-pass centred at $\omega_0$, so form 2 ends with the band still at $\omega_0$.
- C) Modulation and filtering do not commute, so the two forms differ unless $\omega_0=0$.
- D) The filter of form 2 is a band-pass centred at $\omega_0$, and both forms give the same $X(n,\omega_0)$.

> [!success]- Answer
> **D.** Multiplying $w[n]$ by $e^{j\omega_0n}$ shifts its response to $W(\omega-\omega_0)$: band-pass at $\omega_0$.
> The final multiplication moves the selected band down to 0. From $e^{-j\omega_0m}=e^{-j\omega_0n}e^{j\omega_0(n-m)}$ the two forms are equal.
> - A: Magnitude 1 in time does not mean no change in frequency. $w[n]e^{j\omega_0n}$ has the response $W(\omega-\omega_0)$.
> - B: Forgot the last block. The multiplication by $e^{-j\omega_0n}$ after the filter moves the band down to 0.
> - C: Form 2 does not use the same filter in the other order. It uses the shifted filter, and with it the two forms are equal.

### Q13 (slide 12)

A discrete STFT uses $f_s=8$ kHz and DFT size $N=256$, so its filter bank has 256 filters. The window length $N_w$ is doubled and $N$ stays the same. What happens to the filters?

- A) The centres stay at the same frequencies, and each filter becomes half as wide.
- B) The centres move twice as close together, and each filter keeps its width.
- C) Nothing changes, because the filter bank is set by the DFT size $N$ alone.
- D) The centres stay at the same frequencies, and each filter becomes twice as wide.

> [!success]- Answer
> **A.** The centres are $kf_s/N$, set by $N$ only. Every filter is the window spectrum shifted, so its width is set by $N_w$. A window twice as long has a main lobe half as wide.
> - B: Roles of $N$ and $N_w$ swapped. $N$ sets the centres, $N_w$ the width.
> - C: $N$ sets only the centres. The bandwidth comes from the window length.
> - D: Direction reversed. A longer window gives a narrower main lobe, so narrower filters.

### Q14 (slide 13)

In channel $k$ of the STFT filter bank, $x[n]$ goes through the filter $w[n]e^{j(2\pi/N)kn}$ and is then multiplied by $e^{-j(2\pi/N)kn}$. The synthesis side multiplies each channel by $e^{+j(2\pi/N)kn}$. Which statement is correct?

- A) The first block is low-pass for every $k$; the exponential in it only changes the phase of the output.
- B) In channel $k=0$ both exponentials equal 1, so the channel is only the low-pass filter $w[n]$.
- C) In channel $k=0$ both exponentials equal 1, so the channel passes $x[n]$ unchanged.
- D) The synthesis exponent is positive to take the complex conjugate, so that the output is real.

> [!success]- Answer
> **B.** For $k=0$, $e^{\pm j(2\pi/N)\cdot0\cdot n}=1$. What remains is the filter $w[n]$, which is low-pass.
> - A: $w[n]e^{j(2\pi/N)kn}$ has the response $W(\omega-2\pi k/N)$: a band-pass filter centred at $2\pi k/N$.
> - C: Forgot the window. The filter $w[n]$ is still there, so channel 0 is a low-pass filter, not a wire.
> - D: $e^{+j(2\pi/N)kn}$ undoes the demodulation: it moves each channel back up to its centre $2\pi k/N$.

## 5. When is the STFT invertible? (slides 15-17)

### Q15 (slide 15)

For a fixed $n$, the inverse DTFT of $X(n,\omega)$ gives the section $f_n[m]=x[m]\,w[n-m]$. The synthesis equation is $x[n]=\frac{1}{2\pi\,w[0]}\int_{-\pi}^{\pi}X(n,\omega)e^{j\omega n}d\omega$. Which statement is correct?

- A) The integral is the section at $m=n$, where $n-m=0$ for all $n$. It uses one sample per section.
- B) Dividing by $w[n]$ would also be exact; $w[0]$ is used only because it is a constant.
- C) $w[0]$ here is the sum $\sum_n w[n]$ of all window samples, its gain at frequency 0.
- D) The integral averages all $N_w$ samples of the section, and $w[0]$ then removes the window.

> [!success]- Answer
> **A.** The integral evaluates the inverse DTFT at $m=n$: $f_n[n]=x[n]\,w[0]$. The window argument is always 0 there, so the factor is always $w[0]$.
> Only that one sample of each section is used. This is why the STFT is so redundant.
> - B: At $m=n$ the window value is $w[n-n]=w[0]$. $w[n]$ is a different number, so dividing by it is wrong.
> - C: That is $W(0)$, used in OLA. $w[0]$ is one sample of the window, at time 0.
> - D: The integral returns only the sample at $m=n$. The other samples of the section are not used.

### Q16 (slide 16)

$f_s=16$ kHz, window 20 ms, hop 25 ms. What fraction of the signal lies in no window?

- A) 20%
- B) 25%
- C) 0%
- D) 80%

> [!success]- Answer
> **A.** $N_w=0.020\cdot16000=320$, $L=0.025\cdot16000=400$. In every 400 samples, $400-320=80$ lie between two windows: $80/400=20\%$.
> These samples can change freely without changing the STFT, so it cannot be inverted.
> - B: Divided the 80 lost samples by the window length 320 instead of the hop 400.
> - C: Hop and window swapped: with a 20 ms hop and a 25 ms window the windows would overlap and nothing would be lost.
> - D: Took the covered fraction $320/400=80\%$ as the lost fraction.

### Q17 (slide 17)

Each filter of a discrete STFT passes a band of width $B=100$ Hz around its centre, and $f_s=8$ kHz. With $N=64$ some frequencies pass through no filter. What is the smallest DFT size $N$ with no lost region?

- A) 40
- B) 160
- C) 128
- D) 80

> [!success]- Answer
> **D.** The centres are $f_s/N$ apart. No gap needs $f_s/N\le B$, so $N\ge8000/100=80$.
> Check: at $N=64$ the spacing is 125 Hz, so 25 Hz between two bands is lost.
> - A: Took $B=100$ Hz as half the band ($\pm100$ Hz), so allowed a spacing of 200 Hz: $8000/200=40$.
> - B: Required a spacing of at most $B/2=50$ Hz: $8000/50=160$. Touching bands are enough.
> - C: Doubled $N=64$ instead of solving $f_s/N\le B$. 128 has no gap but is not the smallest.

## 6. Synthesis: filter bank summation (slides 18-19)

### Q18 (slide 18)

Filter bank summation (FBS): $y[n]=\frac{1}{N\,w[0]}\sum_{k=0}^{N-1}X(n,k)\,e^{j\frac{2\pi}{N}nk}$. Which statement is correct?

- A) The sum over $k$ adds frames taken at different times, so the windows must overlap in time.
- B) The exponential moves channel $k$ down to 0 before the channels are added together.
- C) The factor $1/(N\,w[0])$ is needed only when $N_w>N$; for $N_w\le N$ the sum is $x[n]$.
- D) The exponential moves channel $k$ back up to $2\pi k/N$; $1/(N\,w[0])$ removes the sum's gain.

> [!success]- Answer
> **D.** $X(n,k)$ is the band of channel $k$, moved down to 0. $e^{j\frac{2\pi}{N}nk}$ moves it back to its centre.
> The sum over $k$ gives $N\,w[0]\,x[n]$, so the factor $1/(N\,w[0])$ returns $x[n]$.
> - A: All terms use the same time $n$. The sum runs over the frequency channels, not over frames: that is OLA.
> - B: Direction reversed. $X(n,k)$ is already at 0; the positive exponent moves it up to $2\pi k/N$.
> - C: The sum is $N\,w[0]\,x[n]$ for any $N_w$ that gives exact FBS. The gain is always there.

### Q19 (slide 19)

A window $w[n]$ is nonzero only for $0\le n\le N_w-1$, with $N_w=400$. FBS is used with $N=512$ or with $N=256$ channels. The FBS constraint is $\sum_{k=0}^{N-1}W(\omega-2\pi k/N)=N\,w[0]$ for every $\omega$. Which statement is correct?

- A) $N=512$: exact for any window. $N=256$: exact only if the FBS constraint holds.
- B) $N=512$: exact only if the FBS constraint holds. $N=256$: exact for any window.
- C) $N=512$: exact for any window. $N=256$: never exact, whatever the window.
- D) Neither is exact for a general window: FBS always needs the FBS constraint.

> [!success]- Answer
> **A.** FBS is exact when $w[rN]=0$ for every $r\ne0$. With $N_w\le N$ (400 $\le$ 512) the window has ended before sample $N$: true for any shape.
> With $N_w>N$ (400 $>$ 256) the window must be designed for it: the FBS constraint $\sum_kW(\omega-2\pi k/N)=N\,w[0]$.
> - B: Condition reversed (as in the slide's misprint '$N_w>N$'). Any window works when $N_w\le N$.
> - C: For $N_w>N$ FBS can still be exact: a window that meets the FBS constraint works.
> - D: For $N_w\le N$ the window is already zero at $\pm N,\pm2N,\dots$, so no extra constraint is needed.

## 7. Synthesis: overlap-add (slides 20-22)

### Q20 (slide 20)

Overlap-add (OLA): $x[n]=\frac{L}{W(0)}\sum_{p}\left[\frac{1}{N}\sum_{k=0}^{N-1}X(pL,k)\,e^{j\frac{2\pi}{N}kn}\right]$. Which statement is correct?

- A) The bracket is $x[n]$ itself, without the window, so there is nothing to divide by.
- B) The bracket is frame $p$'s windowed section. Dividing is avoided only because adding is faster.
- C) The bracket is frame $p$'s windowed section. Dividing by the window would blow up small errors.
- D) The bracket is the spectrum of frame $p$, and $p$ counts the frequency channels.

> [!success]- Answer
> **C.** The bracket is the inverse DFT of frame $p$: the signal multiplied by the window at that position. $p$ counts the frames.
> Near its ends the window is close to 0. Dividing by it multiplies any small error (noise, a modification, rounding) by a large number.
> - A: The inverse DFT returns the windowed section, $x[n]$ times the window at frame $p$.
> - B: Speed is not the reason. Division amplifies small errors where the window is near 0, and fails where it is 0.
> - D: The bracket is a time signal (an inverse DFT), and $p$ counts frames in time.

### Q21 (slide 21)

OLA is exact when $\sum_p w[pL-n]=W(0)/L$ for every $n$, with $W(0)=\sum_n w[n]$. The output is scaled by $L/W(0)$. A periodic Hann window $w[n]=0.5\left(1-\cos(2\pi n/N_w)\right)$, $n=0,\dots,N_w-1$, has $N_w=400$ and the hop is $L=100$. What do the shifted windows add to, and what is the scale factor?

- A) They add to 4; scale factor 0.25
- B) They add to 2; scale factor 0.5
- C) They add to 0.5; scale factor 2
- D) They add to 1; scale factor 1

> [!success]- Answer
> **B.** $W(0)=0.5\cdot400=200$. Sum: $W(0)/L=200/100=2$ (four windows cover every sample).
> Scale factor: $L/W(0)=100/200=0.5$.
> - A: Took $W(0)=N_w=400$, as for a rectangular window: $400/100=4$ and $100/400=0.25$.
> - C: Swapped: used $L/W(0)$ as the sum and $W(0)/L$ as the scale factor.
> - D: Used the Hann sum at 50% overlap, which is 1, for every hop. At $L=100$ (75% overlap) it is 2.

### Q22 (slide 22)

FBS and OLA are duals: each item of FBS has a partner in OLA. Which pair is correct?

- A) the sum over channels $k$ $\leftrightarrow$ the sum over the samples of one frame
- B) the number of channels $N$ $\leftrightarrow$ the window length $N_w$
- C) the value $w[0]$ $\leftrightarrow$ the peak value $\max_n w[n]$ of the window
- D) the number of channels $N$ $\leftrightarrow$ the hop $L$

> [!success]- Answer
> **D.** $N$ is the sampling step in frequency ($2\pi/N$); $L$ is the sampling step in time. The other partners: sum over $k$ $\leftrightarrow$ sum over frames $p$, $w[0]\leftrightarrow W(0)$, $N_w<N\leftrightarrow\omega_c<2\pi/L$.
> - A: OLA sums over the frames $p$ that cover $n$, not over the samples inside one frame.
> - B: $N_w$ belongs to both methods. The time partner of the frequency step $2\pi/N$ is the time step $L$.
> - C: The partner of $w[0]$ (window at time 0) is $W(0)$ (window spectrum at frequency 0).

## 8. STFT magnitude and its inversion (slides 23-26)

### Q23 (slide 23)

The STFTM $\lvert X(n,\omega)\rvert$ keeps only the magnitude of the STFT. Which change of $x[n]$ leaves it exactly the same for every $n$ and $\omega$?

- A) $x[n-1]$
- B) $-x[n]$
- C) $x[-n]$
- D) $(-1)^n\,x[n]$

> [!success]- Answer
> **B.** The STFT of $-x[n]$ is $-X(n,\omega)$, with the same magnitude. So from the magnitude alone the signal is known at best up to its sign.
> - A: A delay moves the frames: the new STFT at $n$ is the old one at $n-1$ (times $e^{-j\omega}$). The magnitude at a given $n$ changes.
> - C: Time reversal mirrors the frames in time, so the magnitude at a given $n$ changes.
> - D: $(-1)^n=e^{j\pi n}$ shifts the spectrum by $\pi$. The magnitude at $\omega$ moves to $\omega+\pi$. It is not a sign change of the whole signal.

### Q24 (slide 24)

The magnitude step of the iteration is $X^i(m,\omega)=\lvert X(m,\omega)\rvert\,\frac{X_e^i(m,\omega)}{\lvert X_e^i(m,\omega)\rvert}$. The update is $x_e^{i+1}[n]=\frac{\sum_m w[m-n]\,g_m^i[n]}{\sum_m w^2[m-n]}$, with $g_m^i[n]$ the inverse transform of $X^i(m,\omega)$. $X_e^i$ is the STFT of the estimate $x_e^i[n]$ after $i$ iterations. Which statement is correct?

- A) $\lvert X\rvert$ is the known target magnitude, and $X_e^i/\lvert X_e^i\rvert$ is the phase of the current estimate.
- B) $\lvert X\rvert$ is the magnitude of the current estimate, and $X_e^i/\lvert X_e^i\rvert$ is the true phase.
- C) The new signal is just the inverse transform of $X^i$, since magnitude and phase are now known.
- D) The denominator could be $\sum_m w[m-n]$; the square only keeps it positive.

> [!success]- Answer
> **A.** $\lvert X(m,\omega)\rvert$ is the given magnitude. $X_e^i/\lvert X_e^i\rvert$ has magnitude 1: only the phase of the estimate is kept.
> - B: Swapped. The true phase is unknown; the magnitude is the known part.
> - C: A known magnitude with a guessed phase is in general not the STFT of any signal. Overlapping frames disagree, so a least-squares overlap-add is used.
> - D: Each section contains the window once and is weighted by it once more, so the total weight is $\sum_m w^2[m-n]$. With $w$ the scale is wrong.

### Q25 (slide 25)

A signal is rebuilt from a given STFT magnitude $\lvert X\rvert$. The iteration alternates 'impose the known magnitude' and 'least-squares overlap-add'. $X_e$ is the STFT of the current estimate. What does the slide promise about the distance between $\lvert X_e\rvert$ and $\lvert X\rvert$?

- A) It reaches zero after enough iterations, so the original $x[n]$ comes back.
- B) It does not grow from one iteration to the next, but it may stop at a local minimum.
- C) It may grow in single iterations, but it falls on average, like a random search.
- D) It ends at the same value for any starting noise, since the magnitude fixes the solution.

> [!success]- Answer
> **B.** Each iteration does not increase the distance. The process converges, but to a local minimum, not necessarily zero.
> - A: Not promised. The iteration can stop at a local minimum with a nonzero distance.
> - C: The promise is stronger: the distance does not grow in any iteration.
> - D: Different starting noises can end in different local minima. Even at best the sign stays unknown.
