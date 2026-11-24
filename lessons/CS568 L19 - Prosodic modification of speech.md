---
course: CS 568 Speech Processing
lecture: 19
title: Prosodic modification of speech
tags: [cs568, lecture]
slides: lectures/lecture_19/l19.pdf
full_notes: lecture_notes/lecture_19/CS568_Lecture_19_Prosodic_Modification.pdf
---
# CS 568 · Lecture 19 · Prosodic modification of speech

> [!summary] In short
> - A plain speed change multiplies every frequency by the same factor, so pitch and formants move together. Prosodic modification changes duration or pitch alone.
> - PSOLA: isolate pitch periods, modify, overlap-add. TD-PSOLA works on the waveform, LP-PSOLA on the LP residual, FD-PSOLA on the flattened spectrum.
> - TD-PSOLA: duplicate or drop frames to change duration, re-space frames to change pitch. Needs accurate epochs, factors within 0.5 to 2.
> - Sinusoidal models and harmonic + noise models (HNM) describe a frame by harmonics (plus shaped noise) and need less exact pitch marks.
> - STRAIGHT splits speech into a smooth spectrogram, an $F_0$ contour and an aperiodicity map, each editable alone.

## 1. Why prosodic modification, and PSOLA (slides 2-3)
![[cs568_lecture_19_l19_p002.jpg]]
![[cs568_lecture_19_l19_p003.jpg]]

**Notes.** Diphones from a fixed inventory must be re-timed and re-pitched to match the front-end. Naive methods fail. Playing $x(t)$ at speed $\alpha$ gives $y(t)=x(\alpha t)$ and

$$Y(\omega)=\frac{1}{\alpha}X\!\left(\frac{\omega}{\alpha}\right)$$

Every frequency is multiplied by $\alpha$: harmonics (pitch) and formants (vowel quality) both move.

- **Time-scale modification**: change the duration, keep the pitch.
- **Pitch-scale modification**: change the pitch, keep the duration.

**PSOLA** (pitch-synchronous overlap-add) is a family with three steps: isolate the pitch periods, modify, resynthesise by overlap-add. No explicit source/filter separation. Variants: TD-PSOLA (the most popular), LP-PSOLA, FD-PSOLA.

> [!example] Slower playback
> A vowel of 2 s with $F_0=120$ Hz and $F_1=500$ Hz at $\alpha=0.8$: duration $2/0.8=2.5$ s, $F_0=96$ Hz, $F_1=400$ Hz. The voice sounds lower and larger.

## 2. TD-PSOLA analysis and reconstruction (slides 4-6)
![[cs568_lecture_19_l19_p004.jpg]]
![[cs568_lecture_19_l19_p005.jpg]]
![[cs568_lecture_19_l19_p006.jpg]]

**Notes.** TD-PSOLA uses one analysis window per pitch period. It needs **epochs**: one marked instant per period (glottal closure, or any instant at the same relative position in every period). They must be very accurate.

A Hanning window two periods long (one before, one after) is centred on each epoch. Adding the windowed frames back at their original positions gives a waveform that sounds identical to the original. For constant period $P$ the two overlapping windows sum to exactly 1:

$$\tfrac12\Big(1+\cos\tfrac{\pi\tau}{P}\Big)+\tfrac12\Big(1+\cos\tfrac{\pi(\tau-P)}{P}\Big)=1,\qquad 0\le\tau\le P$$

Unvoiced segments use a default 10 ms window. Slide 6: two segments are joined by overlap-adding at the pitch marks.

## 3. Time-scale and pitch-scale modification (slides 7-11)
![[cs568_lecture_19_l19_p007.jpg]]
![[cs568_lecture_19_l19_p008.jpg]]
![[cs568_lecture_19_l19_p009.jpg]]
![[cs568_lecture_19_l19_p010.jpg]]
![[cs568_lecture_19_l19_p011.jpg]]

**Notes.**
- **Time scaling**: duplicate frames to lengthen, remove frames to shorten. The frames stay one period apart, so the pitch does not change. Only the number of periods changes.
- **Pitch scaling**: put the frames at a different spacing. New pitch $F_0'=1/\text{spacing}$. Speech at 100 Hz (10 ms): frames 9 ms apart give $1/0.009=111$ Hz, frames 11 ms apart give 91 Hz. The formants stay, because each frame keeps its shape.
- The window is two periods long so that, when frames are moved apart (down to a factor 0.5), there is still speech to add at the edges.
- Rule of thumb: factors up to two, in time or pitch, are hardly noticeable.

## 4. Epochs, mapping function, interactions (slides 12-16)
![[cs568_lecture_19_l19_p012.jpg]]
![[cs568_lecture_19_l19_p013.jpg]]
![[cs568_lecture_19_l19_p014.jpg]]
![[cs568_lecture_19_l19_p015.jpg]]
![[cs568_lecture_19_l19_p016.jpg]]

**Notes.** The procedure:

1. Find the analysis epochs $T^a=\{t^a_1,\dots,t^a_M\}$.
2. Local pitch period: $p^a_m=\dfrac{t^a_{m+1}-t^a_{m-1}}{2}$.
3. Analysis frames: $x^a_m[n]=w_m[n]\,x[n]$, with $w_m$ a Hanning window centred at $t^a_m$.
4. Synthesis epochs $T^s$ from the target $F_0$ and timing.
5. Mapping function $M[i]$: which analysis frame goes to synthesis epoch $i$.
6. Output: $y[n]=\sum_i x^a_{M[i]}\big[n-t^s_i+t^a_{M[i]}\big]$.

Duration can be changed without touching pitch. Pitch cannot be changed without touching duration. Slide 15: 5 frames at 100 Hz span $(5-1)\times10=40$ ms. At 150 Hz the spacing is 6.6 ms and the span drops to about 26 ms. Duplicating two frames gives $(7-1)\times6.6\approx40$ ms again. A pitch change by factor $\beta$ at constant duration needs about $\beta$ times as many frames, so both are always done together through $M[i]$.

> [!example] Mapping for half duration
> Six epochs at 0, 10, ..., 50 ms, shorten to half at the same pitch. Synthesis epochs: 0, 10, 20 ms. Map back with $t=2t^s$: 0, 20, 40 ms. $M=[1,3,5]$.

## 5. TD-PSOLA performance (slide 17)
![[cs568_lecture_19_l19_p017.jpg]]

**Notes.** Quality is extremely high if the epochs are accurate and the factors stay within two. It is also very fast: window, shift, add.

Limits: only time and pitch scaling (no spectral change), no compression (the whole waveform is stored).

> [!warning] Tonal noise
> Slowing unvoiced speech by about 2 repeats the same noise frame regularly, which sounds tonal (a 10 ms frame repeated gives a 100 Hz buzz). Reversing the time axis of every other frame fixes it. For voiced fricatives time reversal does not help, and FD-PSOLA is needed.

## 6. LP-PSOLA and FD-PSOLA (slides 18-21)
![[cs568_lecture_19_l19_p018.jpg]]
![[cs568_lecture_19_l19_p019.jpg]]
![[cs568_lecture_19_l19_p020.jpg]]
![[cs568_lecture_19_l19_p021.jpg]]

**Notes.**
**LP-PSOLA**: inverse-filter the speech with an LP filter, apply TD-PSOLA to the residual, pass the result through the LP synthesis filter. Advantages: compression (reflection coefficients, residual as a pulse train at lower quality), joint change of pitch and envelope, separate frames for envelope and prosody, fewer distortions because the residual is spectrally flat.

**FD-PSOLA**, three stages:

1. Analysis: complex short-time (ST) spectrum at the pitch marks. Estimate the envelope (LP, cepstral, or peak-picking such as SEEVOC). Divide to get a flattened spectrum.
2. Frequency modification: re-space the harmonics to the new pitch, by compression-expansion or by harmonic elimination-repetition.
3. Synthesis: multiply by the envelope, inverse DFT, overlap-add.

Compression by $\beta<1$ leaves the band above $\beta f_s/2$ empty. Elimination-repetition keeps the full band covered. FD-PSOLA allows spectral changes (smoothing joins, vocal tract length, morphing) but costs much computation and memory.

## 7. Sinusoidal models (slides 22-25)
![[cs568_lecture_19_l19_p022.jpg]]
![[cs568_lecture_19_l19_p023.jpg]]
![[cs568_lecture_19_l19_p024.jpg]]
![[cs568_lecture_19_l19_p025.jpg]]

**Notes.** A periodic frame is a sum of $L$ harmonics of the fundamental $\omega_0=2\pi F_0$, with amplitudes $A_l$ and phases $\phi_l$:

$$x(t)=\sum_{l=1}^{L}A_l\cos(\omega_0 l\,t+\phi_l)$$

The parameters are fitted by weighted least squares, with $w(n)$ the window and $s(n)$ the speech:

$$E=\sum_n w(n)^2\Big(s(n)-\sum_{l=1}^{L}A_l\cos(\omega_0 l\,n+\phi_l)\Big)^2$$

Why not plain Fourier analysis: the window concentrates accuracy in the centre, and short frames are enough. Pitch marks need not be as exact as for PSOLA.

Pitch change: find the envelope (peak-picking), move the harmonics to $l\,\omega_0'$, read the new amplitudes from the envelope, synthesise and overlap-add.

> [!warning] Slide error
> Slides 22, 23 and 27 write the argument as $\omega_0 l+\phi_l$. The time variable is missing: it is $\omega_0 l\,t+\phi_l$.

## 8. Harmonic + noise models (slides 26-31)
![[cs568_lecture_19_l19_p026.jpg]]
![[cs568_lecture_19_l19_p027.jpg]]
![[cs568_lecture_19_l19_p028.jpg]]
![[cs568_lecture_19_l19_p029.jpg]]
![[cs568_lecture_19_l19_p030.jpg]]
![[cs568_lecture_19_l19_p031.jpg]]

**Notes.** Speech is not perfectly periodic, and high frequencies are noisy (breath, turbulence). So add a stochastic part $s(t)_r$ to the harmonic part:

$$\hat s(t)=\sum_{l=1}^{L}A_l\cos(\omega_0 l\,t+\phi_l)+s(t)_r,\qquad s(t)_r=e(t)\,\big[h(t,\tau)\otimes b(t)\big]$$

$b(t)$ is white Gaussian noise, $h(t,\tau)$ a time-varying (all-pole) filter, $\otimes$ convolution, $e(t)$ a time envelope. HNM (Stylianou) differs from other harmonic/stochastic models (MBE) by this temporal pattern of the noise, which matters in stops.

**Analysis**: estimate the pitch (no accurate epochs needed), fit a harmonic model per frame, classify voiced/unvoiced from the residual error (unvoiced gives a higher error). For voiced frames find the highest harmonic frequency (cutoff). Estimate amplitudes, phases, $h$ and $e$. Finally adjust the phases so that frames align.

**Synthesis**: frames and $M[i]$ as in PSOLA. For pitch scaling: adjust the harmonics, pass white noise through $h$, high-pass the noise above the cutoff in voiced frames, modulate the noise in time in synchrony with the harmonics (so one sound is heard, not two), overlap-add.

## 9. STRAIGHT (slides 32-35)
![[cs568_lecture_19_l19_p032.jpg]]
![[cs568_lecture_19_l19_p033.jpg]]
![[cs568_lecture_19_l19_p034.jpg]]
![[cs568_lecture_19_l19_p035.jpg]]

**Notes.** STRAIGHT (Kawahara; the slides misspell it "Hawahara") is a high-quality vocoder with three terms:

1. a smooth spectrogram, free from periodicity in time and frequency,
2. an $F_0$ contour,
3. an aperiodicity map (spectral shape and time envelope of the noise).

Analysis: estimate $F_0$ accurately, then use it to smooth the periodicity out of the ST spectrum ($F_0$-adaptive filter). The result captures the vocal-tract and glottal filters without $F_0$. Synthesis: generate flat-spectrum pulses or noise from the voicing and $F_0$, shape them with the smooth spectrum, inverse FFT and overlap-add. No phase is extracted: minimum phase is assumed, with all-pass filters to reduce buzz.

Modification is simple: duplicate or remove time slices (duration), edit the $F_0$ contour (pitch), resynthesise. Changes up to 600% stay natural. The cost is heavy computation.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Speed change | $y(t)=x(\alpha t)\Rightarrow Y(\omega)=\frac1\alpha X(\omega/\alpha)$, all frequencies times $\alpha$ |
| Epoch | one marked instant per pitch period |
| PSOLA | isolate periods (two-period Hanning), modify, overlap-add |
| Time scaling | duplicate or remove frames, spacing unchanged |
| Pitch scaling | re-space frames, $F_0'=1/\text{spacing}$ |
| Local period | $p^a_m=(t^a_{m+1}-t^a_{m-1})/2$ |
| Mapping $M[i]$ | analysis frame used at synthesis epoch $i$ |
| LP-PSOLA / FD-PSOLA | PSOLA on the LP residual / on the flattened spectrum |
| Sinusoidal model | $x(t)=\sum_l A_l\cos(\omega_0lt+\phi_l)$, weighted least squares |
| HNM | harmonics $+\ e(t)[h(t,\tau)\otimes b(t)]$ above the maximum voiced frequency |
| STRAIGHT | smooth spectrogram + $F_0$ + aperiodicity, up to 600% |

## Check yourself
> [!question]- 1. A 100 Hz segment is re-spaced at 12.5 ms. What is the new pitch? Is it within the factor-of-two rule?
> $F_0'=1/0.0125=80$ Hz. Factor $0.8$, inside $0.5$ to $2$, so little degradation.

> [!question]- 2. Epochs at 10, 18, 27, 35 ms. Find $p^a_2$ and $p^a_3$.
> $p^a_2=(27-10)/2=8.5$ ms. $p^a_3=(35-18)/2=8.5$ ms.

> [!question]- 3. Five frames at 100 Hz (span 40 ms). How many synthesis frames lower the pitch to 50 Hz at the same duration?
> New spacing 20 ms. $40/20=2$ gaps, so 3 frames (at 0, 20, 40 ms). Two analysis frames are dropped.

> [!question]- 4. With $f_s=16$ kHz, which band is empty after FD-PSOLA compression with $\beta=0.5$?
> Nyquist is 8 kHz. The new top is $0.5\times8=4$ kHz, so 4 to 8 kHz is empty. Elimination-repetition would fill it.

> [!question]- 5. In HNM, how is a frame classified as voiced or unvoiced?
> Fit a harmonic model to the pitch-synchronous frame and look at the residual error. High error means unvoiced, low error means voiced.

> [!question]- 6. How does STRAIGHT lengthen an utterance? How does it raise the pitch?
> Lengthen: duplicate time slices of the smooth spectrogram and of the aperiodicity map. Raise the pitch: scale up the $F_0$ contour, leaving the spectrogram (formants) untouched. Then run STRAIGHT synthesis.

## Links
- Course index: [[CS568 Home]]
- Previous lecture (where prosody modification is needed): [[CS568 L18 - Speech synthesis back-end]]
- Quizzes and labs that use this lecture: none (the midterm booklet covers Lectures 1 to 6).
- Full step-by-step notes (PDF): [CS568_Lecture_19_Prosodic_Modification.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_19/CS568_Lecture_19_Prosodic_Modification.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_19/CS568_Lecture_19_Prosodic_Modification.pdf`
- Original slides (PDF): [l19.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_19/l19.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_19/l19.pdf`

Slides not embedded: 1
