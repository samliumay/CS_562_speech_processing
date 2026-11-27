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

> [!info] Frames, hop and the number of frames (beyond the slides)
> Speech is **quasi-stationary**: the tongue, lips and jaw move slowly, so over 20 to 30 ms the vocal tract shape and its spectrum stay about the same. One such piece is a **frame** (çerçeve). It is cut out with a **window** (pencere).
> - Window length in samples: $N_w=T_w\,f_s$. Hop (frame shift) in samples: $L=T_{hop}\,f_s$.
> - **Overlap** (çerçeve örtüşmesi) between neighbouring frames: $(N_w-L)/N_w=1-L/N_w$. Careful: "örtüşme" alone is also the Turkish word for aliasing (L04), which is a different thing.
> - A signal of $N_s$ samples, with no padding at the ends, gives
> $$\text{frames}=1+\left\lfloor\frac{N_s-N_w}{L}\right\rfloor$$
> The first frame needs $N_w$ samples. Every further frame needs $L$ new samples.

> [!question]- Check (slide 2): In the DTFT the frequency variable is $\hat\omega$. In the DFT it is the index $k$. What is the difference, and how far apart in Hz are two neighbouring DFT frequencies for $F_s=8$ kHz and $N=256$?
> $\hat\omega$ is continuous: the DTFT exists at every frequency between $-\pi$ and $\pi$, and its sum runs over all $n$. The DFT keeps only $N$ samples of it, at $\hat\omega_k=2\pi k/N$, and its sum runs over $N$ samples of the signal.
> In Hz the $k$-th frequency is $F_k=kF_s/N$, so the spacing is $F_s/N=8000/256=31.25$ Hz.

> [!question]- Check (slide 3): Look at the three plots. What does the middle plot (the DFT of the whole signal) tell you, and what does it not tell you? What does the right plot add?
> The middle plot has two peaks, at about 100 Hz and about 200 Hz. So both frequencies are present. It does not say when: the same magnitude plot would appear if the 200 Hz part came first.
> The right plot (the STFT) has time on the horizontal axis. The energy is at about 100 Hz in the first frames and jumps to about 200 Hz near the middle. This matches the left plot, where the oscillation becomes faster halfway.

> [!question]- Check (slide 4): A 2 s recording at $f_s=16$ kHz is analysed with a 25 ms window and a 10 ms hop. Give $N_w$, $L$, the overlap and the number of frames. In the figure, why do neighbouring windows overlap?
> $N_w=0.025\cdot16000=400$ samples. $L=0.010\cdot16000=160$ samples.
> Overlap: $1-160/400=0.6$, that is 60%.
> $N_s=2\cdot16000=32000$. Frames: $1+\lfloor(32000-400)/160\rfloor=1+\lfloor197.5\rfloor=1+197=198$.
> Overlap is needed because each window is close to zero at its ends. Without overlap the samples near the frame edges would almost not count, and a fast change between two frames would be missed.

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

> [!info] Main lobe, side lobes and leakage in Hz (beyond the slides)
> Multiplying by a window in time means convolving the true spectrum with the window spectrum $W(\omega)$. A pure sinusoid, which should be one line, becomes a copy of $W(\omega)$: a **main lobe** (ana lob) with **side lobes** (yan lob) around it.
> - Main-lobe width decides the **frequency resolution** (frekans çözünürlüğü): two components of similar strength that are closer than about half the main-lobe width merge into one peak.
> - Side-lobe height decides the **leakage** (sızıntı): energy of a strong component appears at frequencies where the signal has none, and can hide a weak component.
>
> Width of the main lobe from zero to zero, for a window of $N_w$ samples and duration $T_w=N_w/f_s$:
>
> | Window | Main-lobe width | 25 ms window |
> |---|---|---|
> | Rectangular | $2f_s/N_w=2/T_w$ | 80 Hz |
> | Hann, Hamming | about $4f_s/N_w=4/T_w$ | about 160 Hz |
>
> The width in Hz depends only on the duration of the window, not on $f_s$. A longer window gives a narrower main lobe. The side-lobe level in dB does not change with the length: it is a property of the window shape.

> [!question]- Check (slide 5): In the figure the highest side lobe is about 13 dB below the top of the main beam. Which window has this pattern? A signal has two sinusoids. What does the width of the main lobe decide, and what does the side-lobe level decide?
> About $-13$ dB for the first side lobe is the rectangular window.
> Main-lobe width: how close in frequency the two sinusoids can be and still give two separate peaks. A wide main lobe merges them.
> Side-lobe level: how weak the second sinusoid can be and still be seen. If it is weaker than the side lobes of the strong one at that frequency, it is hidden by leakage.
> The nulls are the frequencies where the window spectrum is zero. Half the distance between the two nulls next to the peak is the usual measure of resolution.

> [!question]- Check (slide 6): Find the mistake: "A Hamming window of the same length gives better frequency resolution than a rectangular window, because its side lobes are much lower." Then use the right-hand plots: a weak component is far away in frequency from a strong one. Which of the three windows hides it least?
> The reason is mixed up. Lower side lobes mean less leakage, not better resolution. The Hamming main lobe is about twice as wide ($4/T_w$ against $2/T_w$), so its frequency resolution is worse. For a 30 ms window: about 133 Hz against about 67 Hz.
> In the plots the rectangular window decays slowly and is still high at the edge of the plot. The Hamming side lobes start lower but then stay at an almost flat level. The Hann side lobes keep falling and are the lowest far from the peak. So Hann hides a far-away weak component least. Close to the strong component, Hamming is better (first side lobe about $-43$ dB against about $-31$ dB).

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

> [!info] DFT size and zero padding (beyond the slides)
> The DFT size $N$ is chosen with $N\ge N_w$. If $N>N_w$, the frame is filled with zeros up to $N$ samples (zero padding). The zeros do not change $X(n,\omega)$. They only sample the same curve at more frequencies: the spacing is $f_s/N$ Hz.
> - Zero padding improves: how smooth the spectrum looks, and how exactly the position of a peak can be read.
> - Zero padding does not improve: the frequency resolution. Two components that the main lobe has merged stay merged. Only a longer window ($N_w$) separates them.

> [!info] Why the window length decides what the spectrogram shows (beyond the slides)
> Take a voiced sound with pitch $F_0$. Its spectrum has harmonics every $F_0$ Hz, and its waveform has one glottal pulse every $T_0=1/F_0$ seconds.
> - **Long window** (several pitch periods, 20 to 40 ms): the main lobe (about $4/T_w$) is narrow compared with the harmonic spacing $F_0$, so each harmonic is a separate peak. The harmonics are clearly separate when $4/T_w<F_0$ (four pitch periods or more in the window) and begin to separate at about $2/T_w<F_0$ (two pitch periods). Example: 30 ms gives $4/T_w\approx133$ Hz, enough for $F_0=150$ Hz. From frame to frame the harmonics stay, so they draw horizontal lines. The filters are narrow: **narrowband**. Time resolution is poor: the single pulses are averaged away.
> - **Short window** (shorter than one pitch period, 3 to 5 ms): the main lobe is much wider than $F_0$, so the harmonics merge and only the envelope (the formants) remains. But the window now sees either a glottal pulse (high energy) or the gap between two pulses (low energy), so the energy goes up and down once per pitch period: vertical lines. The filters are wide: **wideband**.
>
> This is the uncertainty trade-off: window duration times main-lobe width is a constant (about 4 for Hann and Hamming), so both cannot be small together.

> [!question]- Check (slide 7, symbols): In $X(n,\omega)=\sum_m x[m]\,w[n-m]\,e^{-j\omega m}$, say what each of $x[m]$, $w[n-m]$, $n$, $\omega$, $m$ is. Then: which information of $X(n,k)$ does the spectrogram $S(n,k)$ throw away?
> - $x[m]$: the speech signal. $m$ is the running time index of the sum.
> - $w[n-m]$: the analysis window, flipped and moved so that it sits at time $n$. It selects the samples around $n$ and sets the others to zero.
> - $n$: the position of the window (the frame time). It is the time axis of the STFT.
> - $\omega$: the analysis frequency, continuous, in radians per sample.
> - $e^{-j\omega m}$: the Fourier kernel. It measures how much of frequency $\omega$ is in the windowed piece.
>
> $S(n,k)=\log\lvert X(n,k)\rvert^2$ keeps only the magnitude. The phase of $X(n,k)$ is thrown away.

> [!question]- Check (slide 7, DFT size): $f_s=8$ kHz, Hamming window with $N_w=200$, DFT size $N=512$. What is the spacing of the frequency samples? Can this STFT separate two sinusoids that are 20 Hz apart? Would $N=4096$ help?
> Spacing: $f_s/N=8000/512=15.625$ Hz.
> Main-lobe width: about $4f_s/N_w=4\cdot8000/200=160$ Hz. The two sinusoids are only 20 Hz apart, far less than the main lobe, so they give one peak.
> $N=4096$ does not help. It gives more samples (every 1.95 Hz) of the same smeared curve. A longer window is needed.

> [!question]- Check (slide 8, figure): In the narrowband plot there are horizontal lines between 0 and about 1200 Hz. In the wideband plot there are vertical stripes. What is one horizontal line, what is one vertical stripe, and how would you get $F_0$ from each plot?
> A horizontal line is one harmonic of the pitch, $kF_0$. The spacing in Hz between two neighbouring lines is $F_0$.
> A vertical stripe is one glottal pulse. The spacing in time between two neighbouring stripes is the pitch period $T_0$, and $F_0=1/T_0$.
> The wideband plot shows no single harmonics. It shows one broad band of energy (in this plot below about 1000 Hz), which is where the formants are.

> [!question]- Check (slide 8, window length): A voice has $F_0=125$ Hz. Hamming windows of 4 ms and of 40 ms are used. For each, compare the window with the pitch period and the main lobe with $F_0$, and say which spectrogram results.
> Pitch period: $T_0=1/125=8$ ms.
> 40 ms window: it holds $40/8=5$ pitch periods. Main lobe about $4/0.040=100$ Hz, narrower than $F_0=125$ Hz. The harmonics are separated: narrowband, horizontal lines.
> 4 ms window: half a pitch period. Main lobe about $4/0.004=1000$ Hz, which covers $1000/125=8$ harmonics. The harmonics merge, the pulses are separated in time: wideband, vertical stripes.

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

> [!info] The second form, step by step (beyond the slides)
> Slide 11 gives the second form "without proof". It takes two lines. Write $e^{-j\omega_0m}=e^{-j\omega_0n}\,e^{j\omega_0(n-m)}$ and take the factor that does not depend on $m$ out of the sum:
> $$X(n,\omega_0)=\sum_m x[m]\,w[n-m]\,e^{-j\omega_0m}=e^{-j\omega_0n}\sum_m x[m]\,\Big(w[n-m]\,e^{j\omega_0(n-m)}\Big)$$
> The sum is the convolution of $x[n]$ with $h[n]=w[n]\,e^{j\omega_0n}$. The window $w[n]$ is a low-pass filter (its spectrum $W(\omega)$ is the main lobe around 0). Multiplying it by $e^{j\omega_0n}$ shifts its spectrum to $W(\omega-\omega_0)$: a **band-pass filter** (bant geçiren filtre) centred at $\omega_0$. A set of such filters at $\omega_0=2\pi k/N$ is the **filter bank** (filtre bankası).

> [!question]- Check (slide 9): In the Fourier view $n$ is fixed and $\omega$ varies. What is fixed and what varies in the filtering view? What kind of filter is $w[n]$, and what decides its bandwidth?
> In the filtering view $\omega=\omega_0$ is fixed and $n$ varies. $X(n,\omega_0)$ is then one signal in time: the output of a filter.
> $w[n]$ is the impulse response of a low-pass filter. Its frequency response $W(\omega)$ is the main lobe around $\omega=0$. The bandwidth is the main-lobe width, so it is set by the window length (and shape): a long window is a narrow filter.

> [!question]- Check (slide 10): Follow the shaded band in the three spectra of part (b). Where is it before modulation, after modulation and after the filter? What has happened to the rest of the spectrum?
> Before: the shaded band is around $\omega_0$ in $X(\omega)$.
> After multiplying by $e^{-j\omega_0n}$: the whole spectrum is shifted to the left by $\omega_0$, giving $X(\omega+\omega_0)$. The shaded band is now around $\omega=0$.
> After the filter $w[n]$: only what lies under the low-pass response $W(\omega)$ stays, $W(\omega)X(\omega+\omega_0)$. The rest of the spectrum is removed.
> So $X(n,\omega_0)$ is a slowly varying complex signal that carries only the band of $x[n]$ around $\omega_0$.

> [!question]- Check (slide 11): Compare the block diagram on this slide with the one on slide 10. What is the order of the two operations in each, and where is the filter centred in each? Do they end with the same spectrum?
> Slide 10: multiply by $e^{-j\omega_0n}$ first (move the band down to 0), then filter with the low-pass $w[n]$, centred at 0.
> Slide 11: filter first with $w[n]e^{j\omega_0n}$, a band-pass filter centred at $\omega_0$ (the plot shows $W(\omega-\omega_0)$ on top of $X(\omega)$). Then multiply by $e^{-j\omega_0n}$ to move the selected band down to 0.
> Both end with $W(\omega)X(\omega+\omega_0)$. The two forms give the same $X(n,\omega_0)$.

> [!question]- Check (slide 12): A discrete STFT uses $f_s=8$ kHz, DFT size $N=256$ and a window of $N_w$ samples. How many filters does the bank have and how far apart are their centres? Which of $N$ and $N_w$ sets the spacing, and which sets the bandwidth of each filter?
> There are $N=256$ filters, $k=0,\dots,255$. The centres are $kf_s/N$, so the spacing is $8000/256=31.25$ Hz. The channels above $k=N/2$ are the negative frequencies.
> $N$ sets the spacing of the centres. $N_w$ (with the window shape) sets the bandwidth: every filter is the same window spectrum, shifted. Doubling $N_w$ with the same $N$ gives filters half as wide at the same centres.

> [!question]- Check (slide 13): In channel $k$ of the analysis side (a), name the two blocks and say what each does. What is special about channel $k=0$? On the synthesis side (b), why is the exponent positive?
> First block: the filter $w[n]e^{j(2\pi/N)kn}$, a band-pass filter centred at $2\pi k/N$. It keeps the band of $x[n]$ around that frequency. Second block: multiplication by $e^{-j(2\pi/N)kn}$, the demodulator. It moves that band down to 0. The result is $X(n,k)$.
> For $k=0$ both exponentials are 1. The channel is only the low-pass filter $w[n]$.
> Synthesis undoes the demodulation: multiplying by $e^{+j(2\pi/N)kn}$ moves each channel back up to its own frequency $2\pi k/N$. Then all channels are added and scaled by $1/(Nw[0])$.

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

> [!question]- Check (slide 15): The synthesis equation divides by $w[0]$. Why by $w[0]$ and not by $w[n]$? How many samples of the section $f_n[m]$ does the equation use?
> The integral is the inverse DTFT of $X(n,\omega)$, evaluated at $m=n$. It returns $f_n[n]=x[n]\,w[n-n]=x[n]\,w[0]$. The window argument is $n-m$, which is 0 at $m=n$, for every $n$. So the factor to remove is always $w[0]$.
> It uses one sample of each section, the one at $m=n$. All the other samples of the section are not needed. This is why the STFT with a hop of one sample is very redundant.

> [!question]- Check (slide 16): $f_s=16$ kHz, window 20 ms, hop 25 ms. Which part of the signal is "unaccounted" as in the figure? Also: with a hop of one sample and $N=512$, how many STFT values are stored per input sample?
> $N_w=0.020\cdot16000=320$ samples, $L=0.025\cdot16000=400$ samples. $L>N_w$, so in every 400 samples, $400-320=80$ samples lie between two windows, as the gaps in the figure show. That is $80/400=20\%$ of the signal. These samples can be changed freely without changing the STFT, so the STFT cannot be inverted.
> With $L=1$ there is one frame per sample, so 512 complex values per input sample. This redundancy is why the STFT is computed only every $L$ samples.

> [!question]- Check (slide 17): The figure looks like the one on slide 16, but the axis is $\omega$. What is lost here? A window has bandwidth $B=100$ Hz, $f_s=8$ kHz, $N=64$. Is there a lost region? What is the smallest $N$ without one?
> Here the gaps are in frequency. Each channel passes a band of width $B$ around $2\pi k/N$. If the centres are further apart than $B$, the frequencies between two bands pass through no filter. They can have any value without changing $X(n,k)$.
> Spacing: $f_s/N=8000/64=125$ Hz. This is more than 100 Hz, so 25 Hz between every two neighbouring bands is lost.
> No gap needs $f_s/N\le B$: $N\ge8000/100=80$.

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

> [!info] Where the two FBS conditions come from (beyond the slides)
> Put the definition of $X(n,k)$ into the sum over the channels and change the order of the sums:
> $$\sum_{k=0}^{N-1}X(n,k)\,e^{j\frac{2\pi}{N}nk}=\sum_m x[m]\,w[n-m]\sum_{k=0}^{N-1}e^{j\frac{2\pi}{N}k(n-m)}$$
> The inner sum is $N$ when $n-m$ is a multiple of $N$ and 0 otherwise. Writing $m=n-rN$:
> $$\sum_{k=0}^{N-1}X(n,k)\,e^{j\frac{2\pi}{N}nk}=N\sum_r x[n-rN]\,w[rN]$$
> The term $r=0$ is $N\,x[n]\,w[0]$: the wanted signal. The other terms are copies of $x$ shifted by multiples of $N$. They vanish when $w[rN]=0$ for every $r\ne0$.
> - If $N_w\le N$, the window is already zero at $\pm N,\pm2N,\dots$, so the condition holds for any window shape.
> - If $N_w>N$, the window must be designed to be zero at these samples. In the frequency domain this is the FBS constraint: the shifted copies of $W(\omega)$ add to the constant $N\,w[0]$.

> [!question]- Check (slide 18): Name the parts of $y[n]=\frac{1}{N\,w[0]}\sum_{k=0}^{N-1}X(n,k)\,e^{j\frac{2\pi}{N}nk}$: what is $X(n,k)$ here, what does the exponential do, what does the sum do, and why the factor $1/(N\,w[0])$?
> $X(n,k)$: the output of channel $k$ of the filter bank at time $n$, a band of $x[n]$ moved down to 0.
> $e^{j\frac{2\pi}{N}nk}$: moves this channel back up to its centre frequency $2\pi k/N$.
> The sum over $k$: adds the $N$ bands to rebuild the full spectrum. All terms use the same time $n$, so no overlap in time is needed.
> $1/(N\,w[0])$: the sum gives $N\,w[0]\,x[n]$, so this factor removes the gain.

> [!question]- Check (slide 19): In the left part of the figure, the window $w[n]$ has length $N_w$ and there are marks at $n=N$ and further. A window has $N_w=400$. Is FBS exact for $N=512$? For $N=256$?
> $N=512$: $N_w\le N$. The window has ended before sample $N$, as in the figure, so $w[rN]=0$ for $r\ne0$. FBS is exact for any window shape.
> $N=256$: $N_w>N$, so the window is still running at $n=256$. FBS is exact only if the FBS constraint holds, that is if this window happens to be zero at $n=256$ (and the shifted $W(\omega-2\pi k/N)$ add to a constant, as in the right part of the figure). For a general window it is not exact.

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

> [!info] Why OLA works, and why $\omega_c<2\pi/L$ is enough (beyond the slides)
> The inverse DFT of frame $p$ returns the section $x[n]\,w[pL-n]$ (for $N\ge N_w$). Adding all sections:
> $$\sum_p x[n]\,w[pL-n]=x[n]\sum_p w[pL-n]$$
> If the sum of the shifted windows is the same constant $W(0)/L$ for every $n$, multiplying by $L/W(0)$ returns $x[n]$. No division by a small window value is needed.
> Condition 1: the sum $\sum_pw[pL-n]$ repeats every $L$ samples in $n$. A sequence with period $L$ contains only the frequencies $2\pi r/L$, and here the size of each one is $W(2\pi r/L)/L$. If the window spectrum is zero at $2\pi/L$ and all its multiples (true when $\omega_c<2\pi/L$), only $r=0$ is left: the constant $W(0)/L$.

> [!question]- Check (slide 20): What is inside the square brackets, and what does the index $p$ count? Why is the section not simply divided by the analysis window to get the signal back?
> Inside the brackets is the inverse DFT of one frame: the short-time section of that frame, that is the signal multiplied by the window at that position. $p$ counts the frames.
> Near its ends the window is close to 0. Dividing by it multiplies any small error in the STFT (noise, a modification, rounding) by a very large number. Overlap-add only adds the sections. The window must then be chosen so that the added windows give a constant.

> [!question]- Check (slide 21): The figure shows triangular windows at $L$, $2L$, $3L$ that add to a flat line. A periodic Hann window has $N_w=400$. For hop $L=100$: what constant do the shifted windows add to, and what is the scale factor $L/W(0)$? Is OLA exact for $L=300$?
> $W(0)=\sum_nw[n]=0.5\cdot400=200$.
> $L=100$ (75% overlap): the sum is $W(0)/L=200/100=2$. Four windows cover every sample and their values always add to 2. Scale factor: $L/W(0)=100/200=0.5$.
> $L=300$: not exact. The windows overlap only by 100 samples, where both are small, so the sum is not constant: it is 1 at a window centre and falls to about 0.29 in the middle of an overlap.

> [!question]- Check (slide 22): The slide puts FBS and OLA side by side. Give the partner in OLA of each FBS item: the sum over $k$, the number $N$, the value $w[0]$, the condition $N_w<N$.
> - Sum over the $N$ frequency channels for one $n$ $\leftrightarrow$ sum over the frames $p$ (time sections) that cover $n$.
> - $N$, the number of frequency samples $\leftrightarrow$ $L$, the hop in time.
> - $w[0]$, the window at time 0 $\leftrightarrow$ $W(0)$, the window spectrum at frequency 0.
> - $N_w<N$: the window is short enough in time $\leftrightarrow$ $\omega_c<2\pi/L$: the window is narrow enough in frequency.
>
> The FBS constraint is a sum of shifted $W(\omega)$ in frequency. The OLA constraint is a sum of shifted $w[n]$ in time. The two methods are duals.

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

> [!warning] Slide error
> Slide 24 writes the left side of the magnitude step as $X^1(m,\omega)$ while the right side has the index $i$. It is $X^i(m,\omega)$, as on slide 25.

> [!question]- Check (slide 23): What is the STFTM, and what is missing in it compared with the STFT? Give one change of $x[n]$ that leaves $\lvert X(n,\omega)\rvert$ exactly the same.
> The STFTM is the magnitude $\lvert X(n,\omega)\rvert$ of the STFT, the quantity that the spectrogram displays. The phase of every time-frequency point is missing.
> Example: $-x[n]$. Its STFT is $-X(n,\omega)$, with the same magnitude. So from the magnitude alone the signal can be found at best up to its sign.

> [!question]- Check (slide 24): In $X^i=\lvert X\rvert\,X_e^i/\lvert X_e^i\rvert$, what is each of the two factors? Why is the new signal not simply the inverse transform of $X^i$, and what does the division by $\sum_mw^2[m-n]$ do?
> $\lvert X(m,\omega)\rvert$ is the known (target) magnitude. $X_e^i/\lvert X_e^i\rvert$ has magnitude 1: it is only the phase of the current estimate.
> A known magnitude combined with a guessed phase is in general not the STFT of any signal. The inverse transforms $g_m^i[n]$ of neighbouring frames give different values for the same sample $n$ where they overlap. The least-squares step picks the one signal whose STFT is closest to $X^i$: a weighted average of the sections, with weights $w[m-n]$.
> $\sum_mw^2[m-n]$ normalises this average. Each section already contains the window once and is weighted by it once more, so the total weight on sample $n$ is the sum of $w^2$.

> [!question]- Check (slide 25): In the one-line formula, which part is "impose the magnitude", which part is "inverse transform", and which part is "overlap-add"? What does the slide promise about the error, and what does it not promise?
> $X^i(m,\omega)=\lvert X\rvert\,X_e^i/\lvert X_e^i\rvert$: impose the known magnitude, keep the phase of the estimate.
> $\frac{1}{2\pi}\int_{-\pi}^{\pi}X^i(m,\omega)e^{j\omega n}d\omega$: the inverse transform of frame $m$ at sample $n$, called $g_m^i[n]$ on slide 24.
> $\sum_mw[m-n](\cdot)\,/\sum_mw^2[m-n]$: the least-squares overlap-add over all frames that cover $n$.
> Promise: the distance between $\lvert X_e\rvert$ and $\lvert X\rvert$ does not grow from one iteration to the next. Not promised: that it reaches zero. The iteration can stop in a local minimum.

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
- Quiz: [[CS568 L06 - Short-time Fourier analysis - quiz]]

Slides not embedded: 1 (outline).
