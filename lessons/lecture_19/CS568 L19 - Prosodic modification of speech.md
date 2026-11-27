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

> [!question]- Check (slide 2): A vowel lasts 1.6 s with $F_0=150$ Hz and $F_1=700$ Hz. Give duration, $F_0$ and $F_1$ after (a) playing it 1.25 times faster, (b) time-scale modification to the same new duration, (c) pitch-scale modification to the same new pitch.
> (a) Plain speed change, everything moves together: duration $1.6/1.25=1.28$ s, $F_0=150\times1.25=187.5$ Hz, $F_1=700\times1.25=875$ Hz.
> (b) Time-scale modification changes only the duration: 1.28 s, $F_0=150$ Hz, $F_1=700$ Hz.
> (c) Pitch-scale modification changes only the pitch: 1.6 s, $F_0=187.5$ Hz, $F_1=700$ Hz.
> Only (a) moves the formant, so only (a) changes the vowel quality and the apparent speaker size.

> [!question]- Check (slide 3): Find the mistake: "Every PSOLA method first separates the glottal source from the vocal-tract filter, then modifies the source and filters it again."
> The slide says the opposite: PSOLA works without any explicit source/filter separation.
> The three steps are: isolate the pitch periods in the signal, do the modification (repeat, drop or re-space the periods), and resynthesise by overlap-add.
> TD-PSOLA does this directly on the waveform. The statement only comes close to LP-PSOLA, which works on the LP residual (slide 18).

## 2. TD-PSOLA analysis and reconstruction (slides 4-6)
![[cs568_lecture_19_l19_p004.jpg]]
![[cs568_lecture_19_l19_p005.jpg]]
![[cs568_lecture_19_l19_p006.jpg]]

**Notes.** TD-PSOLA uses one analysis window per pitch period. It needs **epochs**: one marked instant per period (glottal closure, or any instant at the same relative position in every period). They must be very accurate.

A Hanning window two periods long (one before, one after) is centred on each epoch. Adding the windowed frames back at their original positions gives a waveform that sounds identical to the original. For constant period $P$ the two overlapping windows sum to exactly 1:

$$\tfrac12\Big(1+\cos\tfrac{\pi\tau}{P}\Big)+\tfrac12\Big(1+\cos\tfrac{\pi(\tau-P)}{P}\Big)=1,\qquad 0\le\tau\le P$$

Unvoiced segments use a default 10 ms window. Slide 6: two segments are joined by overlap-adding at the pitch marks.

> [!question]- Check (slide 4): Speech sampled at $f_s=16$ kHz has $F_0=125$ Hz. How long is one pitch period in samples, how long is the TD-PSOLA analysis window, and how long is the default window in an unvoiced part? Why must the epochs lie at the same relative position in every period?
> Period: $16000/125=128$ samples. Window: two periods, $2\times128=256$ samples (one period before the epoch, one after).
> Unvoiced: $0.010\times16000=160$ samples.
> Same relative position: the frames are later added on top of each other. If one frame is centred on the glottal closure and the next one a little later in the period, the overlapping parts do not line up, they partly cancel, and the output sounds rough.

> [!question]- Check (slide 5): In panel (3) every frame shows one large peak in the middle and smaller waves on both sides. Why is there more than one period in a frame, and why does adding the six frames back in panel (4) give the original waveform?
> Each Hanning window is two periods long and is centred on an epoch, so the frame holds the period before and the period after the epoch, faded out towards the edges.
> Between two neighbouring epochs exactly two windows overlap (panel 2). One falls while the other rises, and their weights add to about 1 (exactly 1 for a constant period).
> So every sample is counted with total weight 1 and the sum is (almost) the original signal.

> [!question]- Check (slide 6): Read the figure: which windowed frames are used, where is the join, and why is there no click at the join? What can this method NOT repair at the join?
> Four frames are used: periods a and b of the first segment, then periods a and b of the second segment. They are placed one after the other, one pitch period apart. The join is between b of the first segment and a of the second.
> No click: each frame fades to zero at its edges (Hanning), and the frames are added at the pitch marks, so the pulses keep a regular spacing across the join.
> Not repaired: a difference in spectral envelope (formants) between the two segments. TD-PSOLA only moves waveform pieces. It cannot change the spectrum (slide 17).

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

> [!question]- Check (slide 7): A voiced segment of 240 ms has a pitch period of 8 ms. How many frames must be duplicated to make it 1.5 times longer, and how many removed to make it 0.8 times as long? What is the pitch in both cases?
> Number of periods: $240/8=30$ frames.
> Longer: $30\times1.5=45$ frames, so 15 frames are duplicated (every second frame is used twice). New duration $45\times8=360$ ms.
> Shorter: $30\times0.8=24$ frames, so 6 frames are removed (every fifth frame). New duration $24\times8=192$ ms.
> The spacing stays 8 ms in both cases, so the pitch stays $1000/8=125$ Hz. Both factors are inside the factor-of-two rule.

> [!question]- Check (slide 8): Count in the figure: how many windows are in the top row and how many in the bottom row? Which frames are used twice? What is the same in both rows, and what does that mean for the pitch?
> Top: 6 windows (frames 1 to 6). Bottom: 9 windows.
> Frames 1, 3 and 5 have two arrows each, so they are used twice. Frames 2, 4 and 6 are used once: $3\times2+3\times1=9$.
> The distance between neighbouring windows is the same in both rows. So the pitch is unchanged and only the number of periods grows, by $9/6=1.5$.

> [!question]- Check (slide 9): With $f_s=16$ kHz, speech at 160 Hz must be raised to 200 Hz. What is the old and the new frame spacing in samples? Then explain with the window length why lowering stops at a factor 0.5.
> Old spacing: $16000/160=100$ samples. New spacing: $16000/200=80$ samples. Pitch factor $200/160=1.25$.
> Lowering by 0.5 means 80 Hz, a spacing of $16000/80=200$ samples. The window is two periods long, $2\times100=200$ samples, so at this spacing neighbouring windows only just touch.
> For a larger spacing there are stretches that no frame covers. The output would have gaps of silence between the pulses.

> [!question]- Check (slide 10): The figure places the frames $0.75P$ apart in (a) and $1.25P$ apart in (b). For $P=8$ ms give the new pitch in both cases. Which part of the picture shows that the formants do not move?
> Original pitch: $1000/8=125$ Hz.
> (a) Spacing $0.75\times8=6$ ms, pitch $1000/6\approx166.7$ Hz (factor $1/0.75\approx1.33$, higher).
> (b) Spacing $1.25\times8=10$ ms, pitch $1000/10=100$ Hz (factor $1/1.25=0.8$, lower).
> Formants: the damped oscillation after each pulse has the same shape in the original and in the modified waveform. This ringing is the response of the vocal tract, and its frequencies are the formants. Only the distance between the pulses changes.

> [!question]- Check (slide 11): Compare this figure with the one on slide 8. How many windows are in the bottom row here, how do they lie, and what happens to the duration as a side effect?
> Here every frame has one arrow, so the bottom row has 6 windows, the same number as the top row. They are spaced wider and hardly overlap. On slide 8 the spacing stayed and the number grew.
> Wider spacing means a longer period, so a lower pitch.
> Side effect: the same six frames now cover more time. Example: at 10 ms the span is $5\times10=50$ ms, at 12.5 ms (80 Hz) it is $5\times12.5=62.5$ ms. To keep the duration, frames must be dropped (slide 15).

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

> [!info] Reading the mapping figures on slides 13 and 14 (beyond the slides)
> Both figures have the analysis time axis on top (marks = analysis epochs) and the synthesis time axis at the bottom (marks = synthesis epochs). A dashed line joins a synthesis epoch to the place in the original signal it takes its frame from.
> - **Slide 13, left**: the wanted time warping (zaman ölçekleme, time scaling) as a continuous function. Each synthesis epoch points back to a time in the original. This time usually falls between two analysis epochs, where no frame exists.
> - **Slide 13, right**: the same lines, each moved to the nearest analysis epoch. This is the mapping $M[i]$. Two analysis epochs get two lines each: these two frames are used twice.
> - **Slide 14** builds $M[i]$ in two steps with a helper axis in the middle, the virtual time axis. Step 1: copy the analysis marks to the virtual axis and duplicate (or drop) some, so that the number of marks is right. Step 2: place the virtual marks on the synthesis axis at the wanted spacing. Same spacing as the analysis marks gives a time-scale change. A different spacing with the same total length gives a pitch change.

> [!question]- Check (slide 12): Analysis epochs lie at samples 400, 528 and 660 ($f_s=16$ kHz). Compute the local pitch period at the middle epoch and the local $F_0$. Then say what $w_m[n]$, $x^a_m[n]$, $T^s$ and $M[i]$ are.
> $p^a_m=(660-400)/2=130$ samples (the two neighbouring periods are 128 and 132 samples, this is their mean). $F_0=16000/130\approx123.1$ Hz.
> $w_m[n]$: the Hanning window centred on epoch $t^a_m$, reaching one local period to each side (here $528\pm130$, from sample 398 to 658, so about from the epoch before to the epoch after).
> $x^a_m[n]=w_m[n]\,x[n]$: analysis frame $m$, the windowed piece of speech around epoch $m$.
> $T^s$: the synthesis epochs, the instants where frames are placed in the output. Their spacing gives the target $F_0$, their number gives the target duration.
> $M[i]$: for synthesis epoch $i$, the index of the analysis frame that is put there.

> [!question]- Check (slides 13-14): On slide 14 count the analysis, virtual and synthesis marks in both panels. Take an analysis spacing of 9 ms. Give spacing, total span and pitch of the synthesis marks for "time-scale modification by 1.5" and for "pitch modification by 1.5".
> Both panels: 7 analysis marks, 10 virtual marks, 10 synthesis marks. Analysis marks 2, 4 and 6 are duplicated on the virtual axis.
> Original: span $6\times9=54$ ms, pitch $1000/9\approx111.1$ Hz.
> Time-scale by 1.5: spacing stays 9 ms, span $9\times9=81$ ms $=1.5\times54$ ms, pitch unchanged 111.1 Hz.
> Pitch by 1.5: spacing $9/1.5=6$ ms, span $9\times6=54$ ms (unchanged), pitch $1000/6\approx166.7$ Hz.
> The same duplication serves both. Only the spacing on the synthesis axis differs.

> [!question]- Check (slide 15): Nine frames of 100 Hz speech must be raised to 125 Hz without changing the duration. Give the span before, the span after re-spacing only, and the number of frames that must be duplicated.
> Before: spacing 10 ms, span $(9-1)\times10=80$ ms.
> Re-spacing only: spacing $1000/125=8$ ms, span $(9-1)\times8=64$ ms. The segment got shorter.
> To cover 80 ms at 8 ms spacing: $80/8=10$ gaps, so 11 frames. Two frames are duplicated: $(11-1)\times8=80$ ms.
> Raising the pitch needs more frames, lowering needs fewer. That is why pitch change always interacts with duration.

> [!question]- Check (slide 16): In the figure, how many synthesis windows are made from the six frames, and which frames are used twice? Name the two things that differ between the top row and the bottom row, and say what each one changes.
> The bottom row has 8 windows. Frames 1 and 6 have two arrows each, frames 2 to 5 one arrow: $2+4+2=8$.
> Difference 1: the number of frames (8 instead of 6). Together with the spacing this sets the duration.
> Difference 2: the spacing of the windows is not the original spacing (it also varies along the row). The spacing sets the pitch.
> Both are written in one object, the mapping $M[i]$ together with the synthesis epochs $T^s$.

## 5. TD-PSOLA performance (slide 17)
![[cs568_lecture_19_l19_p017.jpg]]

**Notes.** Quality is extremely high if the epochs are accurate and the factors stay within two. It is also very fast: window, shift, add.

Limits: only time and pitch scaling (no spectral change), no compression (the whole waveform is stored).

> [!warning] Tonal noise
> Slowing unvoiced speech by about 2 repeats the same noise frame regularly, which sounds tonal (a 10 ms frame repeated gives a 100 Hz buzz). Reversing the time axis of every other frame fixes it. For voiced fricatives time reversal does not help, and FD-PSOLA is needed.

> [!question]- Check (slide 17): Match each TD-PSOLA problem with the remedy named on the slide: (1) the whole waveform must be stored, (2) tonal noise when unvoiced speech is slowed down, (3) tonal noise in voiced fricatives, (4) no spectral modification. Why does reversing the time axis help in case (2)?
> (1) LP-PSOLA: it stores filter parameters and a residual, which can be compressed.
> (2) Reverse the time axis of consecutive (repeated) frames.
> (3) FD-PSOLA. Time reversal does not solve it.
> (4) Not solved inside TD-PSOLA. FD-PSOLA (and LP-PSOLA for the envelope) allow it.
> Why reversal helps: a noise frame played backwards is still noise with the same magnitude spectrum, but its waveform is different. The repeated frames are no longer identical copies, so no regular period (no tone) appears. A voiced fricative also has a periodic part that must stay as it is, and reversal would damage that part.

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

> [!info] Reading the figure on slide 20 (beyond the slides)
> All three panels show the log magnitude spectrum of one short-time frame from 0 to 8 kHz.
> - **Top**: the original spectrum (thin line, the fast ripple is the pitch harmonics) and its spectral envelope (spektral zarf, thick line). The envelope carries the formants.
> - **Middle**: the flattened spectrum, spectrum divided by envelope. All harmonics are now at about the same level (near 0 on the log axis), so only the pitch information is left. It has already been compressed along the frequency axis: it ends at about 6 kHz instead of 8 kHz, so the factor is about $6/8=0.75$. The harmonics lie closer together, which means a lower pitch.
> - **Bottom**: the modified flattened spectrum multiplied by the original envelope. The thick envelope is the same as in the top panel, the ripple under it is denser. The curve is drawn up to 8 kHz again, so the empty band above 6 kHz has been filled. The slide does not say how. Repeating harmonics is the method named on slide 19.

> [!question]- Check (slide 18): Name the three steps of LP-PSOLA and say what each one does to the spectrum. Then explain the phrase "independent time frames for spectral envelope estimation and for prosodic modification".
> 1. Inverse-filter the speech with the LP filter $A(z)$: the spectral envelope is removed, and the residual has a flat spectrum with the pitch pulses in it.
> 2. TD-PSOLA on the residual: repeat, drop or re-space the residual periods. This changes duration and pitch only.
> 3. Filter the new residual with $1/A(z)$: the envelope (formants) is put back.
> Independent frames: the LP coefficients can be computed on ordinary fixed frames (for example every 10 ms), while the residual is cut pitch-synchronously at the epochs. In TD-PSOLA there is only one kind of frame, the pitch-synchronous one.
> A further gain: the envelope is a separate object, so it can be changed too, and overlap-adding a flat residual gives fewer distortions than overlap-adding the speech itself.

> [!question]- Check (slide 19): Why does FD-PSOLA divide the spectrum by the envelope before it moves the harmonics, and multiply by it afterwards? Use this case: $F_0$ goes from 100 Hz to 125 Hz and a formant lies at 500 Hz. How many harmonics lie up to 4 kHz before and after?
> If the harmonics were moved with their amplitudes, the formant peak would move with them. That is the resampling effect, and the vowel quality would change.
> Flattening removes the envelope, so only the harmonic positions are moved. Multiplying by the unchanged envelope then gives each harmonic the amplitude that the envelope has at its new frequency.
> Example: at 100 Hz the 5th harmonic sits on the formant ($5\times100=500$ Hz). At 125 Hz it is the 4th harmonic ($4\times125=500$ Hz). The peak stays at 500 Hz.
> Count: $4000/100=40$ harmonics before, $4000/125=32$ after.

> [!question]- Check (slide 20): The flattened spectrum in the middle panel ends at about 6 kHz, the other two panels reach 8 kHz. Which of the two methods of slide 19 was applied, with which factor, and what is the new pitch if the original was 200 Hz? What is the same in the top and bottom panels, and what is different?
> It is spectral compression: the whole flattened spectrum is squeezed towards 0 Hz. The old top, 8 kHz, now lies at about 6 kHz, so the factor is about $6/8=0.75$.
> New pitch: $0.75\times200=150$ Hz (lower).
> Same: the thick envelope, so the formants are where they were.
> Different: the harmonic ripple under the envelope is denser in the bottom panel, because the harmonics are now 150 Hz apart instead of 200 Hz.

> [!question]- Check (slide 21): Compare TD-PSOLA, LP-PSOLA and FD-PSOLA: for each one, what is overlap-added, can the spectral envelope be changed, and what is the main cost?
> TD-PSOLA: windowed pieces of the speech waveform. No spectral change. Cheapest, but the whole waveform is stored and the epochs must be exact.
> LP-PSOLA: windowed pieces of the LP residual, followed by the LP synthesis filter. The envelope can be changed through the filter. Cost: LP analysis and filtering. With a pulse-train residual the quality drops.
> FD-PSOLA: frames rebuilt by inverse DFT from a modified spectrum. Free spectral changes: smoothing the envelope across diphone joins, changing the vocal tract length, morphing between voices. Cost: very much computation and memory.

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

> [!question]- Check (slide 22): In $x(t)=\sum_{l=1}^{L}A_l\cos(\omega_0 l\,t+\phi_l)$ name every symbol. For $F_0=150$ Hz and $f_s=16$ kHz, how many harmonics fit below the Nyquist frequency, and how many numbers describe one frame?
> $l$: harmonic number. $L$: number of harmonics. $\omega_0=2\pi F_0$: fundamental (angular) frequency, the same for all terms. $l\,\omega_0$: frequency of harmonic $l$. $A_l$: its amplitude. $\phi_l$: its phase.
> Nyquist: $16000/2=8000$ Hz. $8000/150\approx53.3$, so $L=53$ (the 53rd harmonic is at 7950 Hz, the 54th would be at 8100 Hz).
> One frame: 53 amplitudes, 53 phases and one $\omega_0$, so $2\times53+1=107$ numbers.
> All frequencies come from the single value $\omega_0$. This is what makes a pitch change easy: change $\omega_0$ and every harmonic follows.

> [!question]- Check (slide 23): In the error $E=\sum_n w(n)^2\big(s(n)-\hat s(n)\big)^2$ say what $s(n)$, $\hat s(n)$ and $w(n)$ are and what the factor $w(n)^2$ does. Give the two reasons of the slide for using this fit and not a plain Fourier analysis.
> $s(n)$: the real speech samples of the frame. $\hat s(n)$: the model, the sum of $L$ harmonics. $w(n)$: the analysis window, large in the middle of the frame and small at the edges.
> $w(n)^2$ weights the squared error. A misfit in the centre of the frame costs much, a misfit at the edges costs little. So the parameters describe the centre of the frame best.
> Reason 1: this concentration of accuracy in the centre. Reason 2: the fit works on short frames. Short frames follow the changes of speech better, while a DFT of a short frame has poor frequency resolution.
> The fit is a regression problem (Quatieri, 2002), not a simple transform.

> [!question]- Check (slide 24): At $F_0=200$ Hz the envelope found by peak-picking is 1.0 at 200 Hz and 0.6 at 400 Hz. The pitch is lowered to 150 Hz. What amplitude does the new second harmonic get (linear interpolation)? What goes wrong if each harmonic simply keeps its old amplitude?
> The new second harmonic lies at $2\times150=300$ Hz, halfway between 200 and 400 Hz. Amplitude $(1.0+0.6)/2=0.8$.
> If the old amplitudes were kept, the value 0.6 that belonged to 400 Hz would now sit at 300 Hz. Every envelope feature would move by the factor $150/200=0.75$: the formants shift down, as with plain resampling.
> So the envelope must be sampled again at the new harmonic frequencies. The envelope stays, the sampling points move.

> [!question]- Check (slide 25): The red lines are the sinusoids found by the model, drawn over a spectrogram. Where are the red lines long, parallel and evenly spaced, and where are they short and scattered? What does each pattern tell you?
> Long, parallel and evenly spaced: in the voiced regions, for example below about 1500 Hz between about 0.25 and 0.5 s. The lines are the harmonics. Their vertical distance is $F_0$. Where the lines bend up or down together, the pitch rises or falls.
> Short and scattered: in the noisy regions, for example above about 2500 Hz around 0.6 s. Noise has no stable frequencies, so the model finds components that appear and disappear.
> Conclusion: a sum of sinusoids fits periodic speech well and noise badly. This is the reason for adding a noise part (slide 26).

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

> [!info] Reading the block diagram on slide 28 (beyond the slides)
> The diagram shows how one HNM frame is generated.
> - $u(t)$ at the left is the white noise (gürültü), called $b(t)$ in the formula.
> - $1/A_p(z)$ is an all-pole filter of order $p$. It is the filter $h(t,\tau)$ of the formula and gives the noise its spectral shape.
> - Two switches choose the path. **UV** (unvoiced frame): the filtered noise goes straight to the output. **V** (voiced frame): the filtered noise first passes the block "Envelope $e(t)$", which makes it louder and softer inside each pitch period, and is then combined with the "Sum of harmonics" block.
> - "To OLA synthesis": the finished frame is overlap-added with its neighbours, as in PSOLA.
> - In short: an unvoiced frame is filtered noise only. A voiced frame is harmonics (low band) plus filtered, time-shaped noise (high band).

> [!question]- Check (slide 26): In the figure one region is circled "Harmonic" and one "Noisy". What do you see in each region, and in which frequency range does each lie? What does a purely sinusoidal model do wrong in the noisy region?
> "Harmonic" is the left, low-frequency part: regular, evenly spaced peaks at a high level. These are the pitch harmonics.
> "Noisy" is the right, high-frequency part: a lower level and irregular peaks without even spacing.
> A purely sinusoidal model also puts exact harmonics there. Noise from breath and turbulence is then replaced by periodic components, so the high band sounds buzzy and not breathy. The fit error is also large there.

> [!question]- Check (slide 27): In $\hat s(t)=\hat s(t)_p+\hat s(t)_r$ say what the two parts are and which properties of speech each one represents. Which two models of this kind does the slide name, and why does the lecture choose HNM?
> $\hat s(t)_p$: the periodic part, the sum of $L$ harmonics of $\omega_0$. It represents the voiced, periodic energy, mainly at low frequencies.
> $\hat s(t)_r$: the stochastic (random) part, assumed to be Gaussian noise. It represents what is not periodic: breath noise at the glottis, turbulence in the vocal tract, the noisy high band.
> Models: multiband excitation (MBE, Griffin and Lim 1988) and harmonic + noise models (HNM, Stylianou 1998). HNM is chosen because it was developed for text-to-speech.

> [!question]- Check (slide 28): In $s(t)_r=e(t)\,[h(t,\tau)\otimes b(t)]$ one operation shapes the noise in frequency and one shapes it in time. Which is which? Why does a model with uniform noise over the frame fail for a stop? In the block diagram, which path does an unvoiced frame take?
> $h\otimes b$ is a convolution, that is a filtering. It shapes the spectrum of the white noise $b(t)$ (which frequencies are strong).
> $e(t)\cdot[\dots]$ is a multiplication in time. It shapes the loudness of the noise over time.
> Stop: the noise of the burst starts suddenly and changes fast. Uniform noise over the frame smears the burst over the whole frame, so the sharp onset is lost.
> Unvoiced frame: both switches are at UV. The noise $u(t)$ passes $1/A_p(z)$ and goes directly to OLA synthesis, without the envelope block and without harmonics.

> [!question]- Check (slides 29-30): Put the HNM analysis steps in order and say why each is needed: adjust phases, find the highest harmonic frequency, classify V/UV, estimate amplitudes and phases. For a voiced frame with refined $F_0=120$ Hz and a cutoff of 4 kHz, how many harmonics does the harmonic part have?
> 1. Classify V/UV: estimate the pitch, fit a harmonic model, look at the residual error. Needed because only voiced frames get a harmonic part.
> 2. Find the highest harmonic frequency (cutoff) of a voiced frame. Needed to know where the harmonic part ends and the noise part begins.
> 3. Estimate the parameters: refine the pitch using only the band below the cutoff, find $A_l$ and $\phi_l$ by minimising $E$, find $h$ and $e$ of the noise.
> 4. Adjust the phases. The analysis did not use exact epochs, so the frames are not at the same position inside the pitch period. A time shift of the waveform inside each frame aligns them, or the overlap-add would partly cancel.
> Harmonics: $4000/120\approx33.3$, so 33 (the 33rd is at 3960 Hz, the 34th would be at 4080 Hz).

> [!question]- Check (slide 31): In HNM synthesis of a voiced frame, why is the noise high-pass filtered above the cutoff, and why is it modulated in time? With a cutoff of 4 kHz, how many harmonics does the harmonic part have before and after raising $F_0$ from 200 Hz to 250 Hz?
> High-pass: below the cutoff the frame is already described by the harmonics. Noise in that band would add energy twice and make the voice hoarse. So the noise keeps only the band above the cutoff.
> Modulation: in real voiced speech the noise comes in bursts, one in every pitch period, in step with the glottal pulses. If the noise is steady, the ear separates it from the voice and hears two sounds, a voice and a hiss. With the modulation one sound is heard.
> Harmonics: $4000/200=20$ before, $4000/250=16$ after. The cutoff stays, the spacing grows, so fewer harmonics fit. Their amplitudes are read from the envelope at the new frequencies.

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

> [!info] Why the ripple must be removed (beyond the slides)
> A voiced short-time spectrum is a smooth envelope multiplied by a comb of harmonics at $F_0, 2F_0, 3F_0, \dots$ An ordinary spectrogram contains both. If such a spectrum is used again at another pitch, the old comb is still in it and disturbs the new harmonics. STRAIGHT uses the known $F_0$ to smooth exactly this comb away, along frequency and along time. What stays is an envelope that does not depend on the pitch, so any new $F_0$ contour can be put on it. It is the "flattened spectrum" idea of FD-PSOLA seen from the other side: FD-PSOLA keeps the comb and the envelope as two parts, STRAIGHT keeps the envelope and makes a new comb in synthesis.

> [!question]- Check (slide 32): The slide lists three terms but shows two pictures. Which term has no picture? In the left picture (the smooth spectrogram), what do you see, and what is missing compared with an ordinary narrowband spectrogram?
> The $F_0$ contour has no picture. Left is the smooth spectrogram, right is the third term, the map that says for each time and frequency how the noise part behaves.
> Left picture: broad bands of high energy that move slowly in time, mostly below about 4 kHz. These are the formants.
> Missing: the thin horizontal stripes of the pitch harmonics. STRAIGHT has removed the periodicity, so the picture holds the envelope only.
> Name of the right picture: slide 32 writes "periodicity map", slide 35 writes "aperiodicity" for the same term. This note says aperiodicity map everywhere.
> The slide does not say what a high value means. What can be seen: the scale runs from about 0.1 to 0.9, and the low values (dark blue) lie in the middle of the utterance (about 280 to 450 ms) and at low frequencies, where the left picture shows strong voiced energy. The high values lie at the start, at the end and at high frequencies. This fits the reading "high value = more noise, less periodic" (beyond the slides).

> [!question]- Check (slide 33): Find the two mistakes: "The STRAIGHT spectrogram contains only the vocal-tract filter. In synthesis STRAIGHT uses the phases that were measured in analysis."
> Mistake 1: the smooth spectrogram captures the vocal-tract and the glottal filters (underlined on the slide). Only the influence of $F_0$ is removed.
> Mistake 2: STRAIGHT extracts no phase. The source is pulses or noise with a flat spectrum, made from the voicing information and $F_0$. The phase comes from a minimum-phase assumption for the envelope.
> All-pass filters are then applied. An all-pass filter changes the phase and leaves the magnitude as it is, so the pulses become less sharp and the buzzy timbre is reduced.

> [!question]- Check (slide 34): Both plots show the same piece of speech as a surface over frequency (0 to 3000 Hz) and time (about 325 to 350 ms). What is the visible difference, where does the ripple of the left surface come from, and what is kept in the right surface?
> Left (rectangular window): the surface is full of sharp ridges and valleys, along the frequency axis and along the time axis. Right (STRAIGHT): a smooth surface with a few broad hills.
> Ripple along frequency: the harmonics of $F_0$. Ripple along time: the window moves over the pitch pulses, so the result changes with the position inside the pitch period.
> Kept on the right: the broad peaks, which are the spectral envelope (formants), and their slow change in time.

> [!question]- Check (slide 35): A 1.8 s utterance must be stretched. What is the longest result that the rule of thumb allows for TD-PSOLA, and the longest that the slide claims for STRAIGHT? Then choose a method for each need: (a) lowest computation, (b) smoothing the spectral envelope at diphone joins, (c) extreme changes, (d) pitch marks that are not exact.
> TD-PSOLA: factor 2, so $2\times1.8=3.6$ s. STRAIGHT: 600%, a factor 6, so $6\times1.8=10.8$ s.
> (a) TD-PSOLA: only window, shift and add.
> (b) FD-PSOLA (or LP-PSOLA through the filter): TD-PSOLA cannot change the spectrum.
> (c) STRAIGHT: the three terms are changed independently. The price is heavy computation.
> (d) Sinusoidal models and HNM (the phases are adjusted afterwards), or STRAIGHT, which needs an accurate $F_0$ but no epochs.

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
- Quiz: [[CS568 L19 - Prosodic modification of speech - quiz]]

Slides not embedded: 1
