---
course: CS 568 Speech Processing
lesson: CS568 L19 - Prosodic modification of speech
type: quiz
questions: 32
---

# CS568 L19 - Prosodic modification of speech - quiz

Lesson note: [[CS568 L19 - Prosodic modification of speech]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Why prosodic modification, and PSOLA (slides 2-3)

### Q1 (slide 2)

A vowel lasts 1.6 s with $F_0=150$ Hz and first formant $F_1=700$ Hz. Playing it 1.25 times faster would raise $F_0$ to 187.5 Hz. Instead, a pitch-scale modification gives it the same new $F_0$ of 187.5 Hz. What are the duration, $F_0$ and $F_1$ after this pitch-scale modification?

- A) 1.28 s, 187.5 Hz, 875 Hz
- B) 1.6 s, 187.5 Hz, 875 Hz
- C) 1.28 s, 187.5 Hz, 700 Hz
- D) 1.6 s, 187.5 Hz, 700 Hz

> [!success]- Answer
> **D.** Pitch-scale modification changes the pitch only. The duration stays 1.6 s, and the formants stay, so $F_1=700$ Hz. Only a plain speed change moves everything together.
> - A: This is the plain speed change: $1.6/1.25=1.28$ s and $700\times1.25=875$ Hz. It changes duration and formants too.
> - B: The formant is moved with the pitch, as in a speed change. Pitch-scale modification keeps the formants, so the vowel quality stays.
> - C: The duration is also shortened by 1.25. Pitch-scale modification keeps the duration.

### Q2 (slide 3)

PSOLA means pitch-synchronous overlap-add. Which description of the PSOLA family is correct?

- A) It separates the glottal source from the vocal-tract filter, changes the source, then filters the new source again.
- B) It isolates the pitch periods, modifies them, and resynthesises by overlap-add, with no explicit source/filter split.
- C) It fits a sum of harmonics to every frame and moves each harmonic to a multiple of the new fundamental frequency.
- D) It resamples the whole waveform at a new rate and then moves the formants back to their places with an LP filter.

> [!success]- Answer
> **B.** The slide gives three steps: isolate the pitch periods, do the modification (repeat, drop or re-space the periods), resynthesise by overlap-add. It needs no explicit source/filter separation. TD-PSOLA does this directly on the waveform.
> - A: This is the opposite of the slide. Only LP-PSOLA comes close, as it works on the LP residual.
> - C: This is the sinusoidal model, not PSOLA. PSOLA moves waveform pieces, not harmonics.
> - D: Resampling is the naive speed change that PSOLA replaces. PSOLA does not resample and then repair the formants.

## 2. TD-PSOLA analysis and reconstruction (slides 4-6)

### Q3 (slide 4)

Voiced speech is sampled at $f_s=16$ kHz and has $F_0=125$ Hz. TD-PSOLA puts one Hanning analysis window on each epoch (one marked instant per pitch period). How long is one analysis window in samples?

- A) 128 samples
- B) 512 samples
- C) 256 samples
- D) 160 samples

> [!success]- Answer
> **C.** One period is $16000/125=128$ samples. The window is two periods long, one before the epoch and one after: $2\times128=256$ samples.
> - A: This is one period. The window is two periods long.
> - B: Two periods were taken on each side of the epoch: $4\times128=512$. The window covers one period on each side.
> - D: This is the default 10 ms window of unvoiced parts: $0.010\times16000=160$. A voiced frame uses two pitch periods.

### Q4 (slide 5)

TD-PSOLA cuts speech of constant period $P$ into frames with Hanning windows centred on the epochs. The frames are added back at their original positions, and the result sounds identical to the original. Why?

- A) Each window is two periods long, so between two epochs exactly two windows overlap. One falls while the other rises, and their weights add to 1.
- B) Each window is one period long and the windows do not overlap, so every sample belongs to exactly one frame and keeps its value.
- C) Each window is two periods long, so every sample is counted twice with its full value, and the overlap-added signal is divided by 2 at the end.
- D) Each window is normalised so that its peak value is 0.5. Two overlapping windows then reach exactly 1 at the epochs, and the rest is lost.

> [!success]- Answer
> **A.** The window reaches one period before and one after the epoch. Between two epochs one window falls and the next one rises. For a constant period $P$: $\tfrac12(1+\cos\tfrac{\pi\tau}{P})+\tfrac12(1+\cos\tfrac{\pi(\tau-P)}{P})=1$. So every sample gets total weight 1.
> - B: The window is two periods long, not one, and neighbouring windows overlap.
> - C: The Hanning weights are not 1 over the whole window. They already add to 1, so no division is needed.
> - D: The Hanning window has peak 1. The sum is 1 at every sample, not only at the epochs.

### Q5 (slide 6)

Two speech segments are joined with TD-PSOLA. The last two windowed periods of the first segment and the first two windowed periods of the second segment are overlap-added, one pitch period apart, at the pitch marks. Which problem at the join can this method NOT repair?

- A) A click caused by a sudden jump of the waveform at the join.
- B) A difference in the spectral envelope (formants) of the two segments.
- C) An irregular gap between the last pulse of one segment and the next.
- D) A small difference in pitch between the end of one and the start of the other.

> [!success]- Answer
> **B.** TD-PSOLA only moves waveform pieces. It cannot change the spectrum, so a formant difference between the two segments stays (slide 17).
> - A: Each frame fades to zero at its edges (Hanning), so there is no sudden jump and no click.
> - C: The frames are placed at the pitch marks, one period apart, so the pulse spacing stays regular across the join.
> - D: TD-PSOLA can re-space the frames, so it can change the pitch near the join.

## 3. Time-scale and pitch-scale modification (slides 7-11)

### Q6 (slide 7)

A voiced segment of 240 ms has a pitch period of 8 ms. TD-PSOLA makes it 1.5 times longer at the same pitch (a duration factor above 1 means longer). How many frames must be duplicated?

- A) 45 frames
- B) 10 frames
- C) 15 frames
- D) 20 frames

> [!success]- Answer
> **C.** The segment has $240/8=30$ frames. The longer segment needs $30\times1.5=45$ frames, so $45-30=15$ frames are duplicated (every second frame is used twice).
> - A: 45 is the total number of frames in the output, not the number of duplicated ones.
> - B: The factor was applied the wrong way: $30/1.5=20$, and $30-20=10$. That would shorten the segment.
> - D: $30/1.5=20$ is the frame count for a duration factor of $1/1.5$, not the number of duplicates.

### Q7 (slide 8)

In a TD-PSOLA figure, six analysis frames (1 to 6) are taken one pitch period apart. In the output, frames 1, 3 and 5 are used twice and frames 2, 4 and 6 once. The output windows have the same spacing as the analysis windows. What changes?

- A) The duration grows by a factor of 2, and the pitch is lowered.
- B) The duration grows by a factor of 1.5, and the pitch stays the same.
- C) The duration grows by a factor of 2, and the pitch stays the same.
- D) The duration grows by a factor of 1.5, and the pitch is lowered.

> [!success]- Answer
> **B.** The output has $3\times2+3\times1=9$ frames instead of 6, so the duration grows by $9/6=1.5$. The spacing of the windows is unchanged, and the spacing sets the pitch. So the pitch stays.
> - A: Two mistakes: every frame was counted twice ($12/6=2$), and lengthening was taken to lower the pitch as in slow playback.
> - C: Every frame was counted twice. Only three of the six frames are duplicated.
> - D: Lengthening was taken to lower the pitch, as in slow playback. In TD-PSOLA the pitch only follows the spacing.

### Q8 (slide 9)

Speech at 160 Hz is sampled at $f_s=16$ kHz. TD-PSOLA lowers the pitch by re-spacing the frames further apart. Each analysis window is two analysis periods long. At which new pitch do neighbouring windows only just touch, so that any further lowering leaves gaps between the frames?

- A) 160 Hz
- B) 320 Hz
- C) 40 Hz
- D) 80 Hz

> [!success]- Answer
> **D.** One period is $16000/160=100$ samples, so the window is $2\times100=200$ samples. The windows touch when the spacing is 200 samples: pitch $16000/200=80$ Hz, a factor 0.5. A larger spacing leaves stretches that no frame covers.
> - A: The window was taken as one period, 100 samples. Then the windows touch already at the original spacing.
> - B: The limiting factor 0.5 was applied the wrong way: the period was halved ($100/2=50$ samples) instead of doubled, so the pitch became $16000/50=320$ Hz. Lowering needs a longer period.
> - C: Two periods were taken on each side of the epoch (400 samples). The window covers one period on each side.

### Q9 (slide 10)

![[cs568_lecture_19_l19_p010.jpg]]

Speech has pitch period $P=8$ ms. In panel (a) of the figure the frames are placed $0.75P$ apart in the output. What is the new pitch?

- A) 93.75 Hz
- B) 83.3 Hz
- C) 166.7 Hz
- D) 156.25 Hz

> [!success]- Answer
> **C.** The new spacing is $0.75\times8=6$ ms, so the pitch is $1000/6\approx166.7$ Hz. The pitch factor is $1/0.75\approx1.33$.
> - A: The spacing factor was used as the pitch factor: $125\times0.75=93.75$ Hz. A smaller spacing gives a higher pitch.
> - B: The window length $2P=16$ ms was taken as the period: $1000/(0.75\times16)\approx83.3$ Hz.
> - D: The pitch factor was taken as $1+(1-0.75)=1.25$: $125\times1.25=156.25$ Hz. The pitch factor is $1/0.75$.

### Q10 (slide 11)

Six TD-PSOLA frames of speech at 100 Hz are each used once, but are placed 12.5 ms apart instead of 10 ms. The pitch drops to 80 Hz (pitch factor 0.8; a pitch factor below 1 means lower). The span is the time from the first to the last frame centre, 50 ms before. What is the span after?

- A) 62.5 ms
- B) 40 ms
- C) 75 ms
- D) 50 ms

> [!success]- Answer
> **A.** Six frames have five gaps: $5\times12.5=62.5$ ms. Wider spacing means a lower pitch, and as a side effect the same frames cover more time. To keep the duration, frames must be dropped.
> - B: The period was multiplied by the pitch factor instead of divided: $10\times0.8=8$ ms, $5\times8=40$ ms.
> - C: The frames were counted instead of the gaps: $6\times12.5=75$ ms.
> - D: Re-spacing was taken to keep the duration. Only time-scale changes keep the spacing.

## 4. Epochs, mapping function, interactions (slides 12-16)

### Q11 (slide 12)

Analysis epochs lie at samples 400, 528 and 660, with $f_s=16$ kHz. The local pitch period at epoch $m$ is $p^a_m=(t^a_{m+1}-t^a_{m-1})/2$. What is the local $F_0$ at the middle epoch?

- A) 125.0 Hz
- B) 61.5 Hz
- C) 121.2 Hz
- D) 123.1 Hz

> [!success]- Answer
> **D.** $p^a_m=(660-400)/2=130$ samples, the mean of the periods 128 and 132. $F_0=16000/130\approx123.1$ Hz. The Hanning window $w_m[n]$ then reaches one local period to each side, from sample 398 to 658.
> - A: Only the period before the epoch was used: $16000/128=125$ Hz.
> - B: The distance $660-400=260$ was not halved: $16000/260\approx61.5$ Hz.
> - C: Only the period after the epoch was used: $16000/132\approx121.2$ Hz.

### Q12 (slides 13-14)

![[cs568_lecture_19_l19_p014.jpg]]

In the lower panel ("Pitch modification by 1.5", a pitch factor above 1 means higher), 7 analysis marks lie 9 ms apart, so they span 54 ms. Some marks are duplicated to give 10 synthesis marks, and the duration must stay the same. What is the spacing of the synthesis marks?

- A) 13.5 ms
- B) 9 ms
- C) 6 ms
- D) 5.4 ms

> [!success]- Answer
> **C.** A pitch factor of 1.5 divides the period by 1.5: $9/1.5=6$ ms. Ten marks have nine gaps: $9\times6=54$ ms, the old span. The pitch becomes $1000/6\approx166.7$ Hz.
> - A: The period was multiplied by the pitch factor instead of divided: $9\times1.5=13.5$ ms. That lowers the pitch.
> - B: This keeps the analysis spacing, as in the upper panel (time-scale modification). The span would then grow to 81 ms.
> - D: The span was divided by the number of marks instead of the number of gaps: $54/10=5.4$ ms.

### Q13 (slide 15)

Nine TD-PSOLA frames of speech at 100 Hz must be raised to 125 Hz without changing the duration. The span (first to last frame centre) must stay the same. What must happen to the frames?

- A) 2 frames are removed.
- B) No frame changes. Re-spacing keeps the duration.
- C) 1 frame is duplicated.
- D) 2 frames are duplicated.

> [!success]- Answer
> **D.** Span before: $(9-1)\times10=80$ ms. New spacing $1000/125=8$ ms. Re-spacing alone gives $8\times8=64$ ms, too short. To cover 80 ms: $80/8=10$ gaps, so 11 frames. Two frames are duplicated.
> - A: The period was multiplied by the pitch factor: $10\times1.25=12.5$ ms gives $80/12.5=6.4\approx6$ gaps, so 7 frames and 2 removed. A higher pitch needs a shorter spacing and more frames.
> - B: Re-spacing alone shortens the segment to 64 ms. Pitch change always interacts with duration.
> - C: The gaps were counted as frames: $80/8=10$ frames, so $10-9=1$. Ten gaps need 11 frames.

### Q14 (slide 16)

In a TD-PSOLA figure, six analysis frames give 8 synthesis windows: frames 1 and 6 are used twice, frames 2 to 5 once. The synthesis windows do not have the analysis spacing, and together they cover a longer time than the analysis frames. What does the figure show?

- A) A time- and pitch-scale change done together. The number and spacing of frames set the duration, the spacing sets the pitch.
- B) A pure time-scale change. Only the number of frames changes, so the pitch stays as it is and only the duration grows.
- C) A time- and pitch-scale change done together. The number of frames sets the pitch, the spacing sets the duration.
- D) A pure pitch-scale change. The two extra frames are only there to keep the duration as it was before.

> [!success]- Answer
> **A.** Two things differ from the analysis row: the number of frames (8 instead of 6) and the spacing. The spacing sets the pitch, and the number of frames with the spacing sets the duration. Both are written in the mapping $M[i]$ with the synthesis epochs $T^s$.
> - B: The spacing differs from the analysis spacing, so the pitch changes too.
> - C: The roles are swapped. Re-spacing changes the pitch, and the number of frames changes the length.
> - D: The windows cover a longer time than before, so the duration also changes.

## 5. TD-PSOLA performance (slide 17)

### Q15 (slide 17)

TD-PSOLA slows down unvoiced speech by a factor of about 2. The same noise frame is repeated regularly, and the result sounds tonal. Which remedy does the slide give, and why does it work?

- A) Use LP-PSOLA. The residual is spectrally flat, so repeated residual frames do not sound tonal.
- B) Use a longer default window of 20 ms, so that the repetition rate is too low to hear as a tone.
- C) Reverse the time axis of the repeated frames. The magnitude spectrum stays, but the copies differ.
- D) Use FD-PSOLA. Time reversal would damage the periodic part that unvoiced speech still contains.

> [!success]- Answer
> **C.** A noise frame played backwards is still noise with the same magnitude spectrum, but its waveform is different. The repeated frames are no longer identical copies, so no regular period and no tone appears.
> - A: LP-PSOLA is the remedy for storage (compression). A residual frame repeated regularly would still give a period.
> - B: A 20 ms frame repeated regularly gives a 50 Hz buzz, still audible. The slide does not propose this.
> - D: FD-PSOLA is the remedy for voiced fricatives, which have a periodic part. Unvoiced speech has no periodic part.

## 6. LP-PSOLA and FD-PSOLA (slides 18-21)

### Q16 (slide 18)

LP-PSOLA inverse-filters speech with an LP filter $A(z)$, applies TD-PSOLA to the residual, and filters the result with $1/A(z)$. The slide says it uses "independent time frames for spectral envelope estimation and for prosodic modification". What does this mean?

- A) The two halves of each Hanning window are taken from different pitch periods, one for the envelope and one for the pitch.
- B) The LP coefficients can come from fixed frames, for example every 10 ms, while the residual is cut at the epochs.
- C) Time scaling and pitch scaling are done in two separate passes, each with its own frames and its own window length.
- D) The analysis frames and the synthesis frames are different, and the mapping function $M[i]$ connects the two sets.

> [!success]- Answer
> **B.** The envelope (the LP filter) is estimated on ordinary fixed frames. The residual is cut pitch-synchronously at the epochs. In TD-PSOLA there is only one kind of frame, the pitch-synchronous one.
> - A: Each Hanning window is one piece of signal around one epoch. Its halves are not used for different purposes.
> - C: LP-PSOLA does both changes in one TD-PSOLA pass on the residual, through one mapping.
> - D: This is true of every PSOLA method, TD-PSOLA too. It is not what makes LP-PSOLA different.

### Q17 (slide 19)

FD-PSOLA divides the short-time spectrum by its envelope, re-spaces the harmonics of the flattened spectrum, and multiplies by the unchanged envelope. $F_0$ is raised from 100 Hz to 125 Hz (a pitch factor above 1 means higher). A formant lies at 500 Hz. After the change, how many harmonics lie up to 4 kHz, and which harmonic sits on the formant?

- A) 32 harmonics; the 4th harmonic sits on the formant.
- B) 50 harmonics; the 4th harmonic sits on the formant.
- C) 32 harmonics; the 5th harmonic sits on the formant.
- D) 50 harmonics; the 5th harmonic sits on the formant.

> [!success]- Answer
> **A.** The harmonics are now 125 Hz apart: $4000/125=32$. The envelope is unchanged, so the peak stays at 500 Hz, where now the 4th harmonic lies ($4\times125=500$ Hz). Flattening first lets only the harmonic positions move, and the envelope gives each harmonic its new amplitude.
> - B: The period was multiplied instead of divided: spacing $100/1.25=80$ Hz gives $4000/80=50$.
> - C: The 5th harmonic kept its large amplitude and moved to $5\times125=625$ Hz. That would move the formant (the resampling effect), which the division by the envelope prevents.
> - D: Both slips: spacing $100/1.25=80$ Hz, and the 5th harmonic carrying the formant.

### Q18 (slide 20)

FD-PSOLA pitch scaling, spectral compression method. The flattened spectrum (spectrum divided by envelope) of a frame at $F_0=200$ Hz originally reaches 8 kHz. After the modification, its content ends at about 6 kHz. What is the new pitch?

- A) About 266.7 Hz
- B) About 150 Hz
- C) About 200 Hz
- D) About 50 Hz

> [!success]- Answer
> **B.** The whole flattened spectrum is squeezed towards 0 Hz, so the factor is about $6/8=0.75$. All harmonics move by this factor: $0.75\times200=150$ Hz. The envelope stays, so the formants stay.
> - A: The factor was applied the wrong way: $200/0.75\approx266.7$ Hz. Compression brings the harmonics closer, so the pitch drops.
> - C: Compression was taken to change only the band. It also moves every harmonic closer to 0 Hz.
> - D: The empty band $2/8=0.25$ was used as the factor: $0.25\times200=50$ Hz.

### Q19 (slide 21)

Which comparison of the three PSOLA variants is correct?

- A) TD-PSOLA overlap-adds pieces of the LP residual and is the cheapest; FD-PSOLA changes the envelope through an LP filter.
- B) TD-PSOLA overlap-adds pieces of the waveform and cannot change the envelope; FD-PSOLA can, at a high computation cost.
- C) LP-PSOLA overlap-adds pieces of the waveform and changes the envelope by re-spacing them; FD-PSOLA is the cheapest one.
- D) FD-PSOLA overlap-adds pieces of the LP residual and cannot change the envelope; TD-PSOLA needs the most memory of all.

> [!success]- Answer
> **B.** TD-PSOLA moves windowed pieces of the speech waveform: no spectral change, cheapest, but exact epochs and the whole waveform are needed. FD-PSOLA rebuilds frames by inverse DFT from a modified spectrum: free spectral changes, but much computation and memory.
> - A: The residual belongs to LP-PSOLA, and the LP filter too. FD-PSOLA works on the flattened spectrum.
> - C: LP-PSOLA works on the residual and changes the envelope through the filter. Re-spacing changes the pitch, not the envelope. FD-PSOLA is the most expensive.
> - D: FD-PSOLA works on the spectrum and can change the envelope. FD-PSOLA, not TD-PSOLA, needs the most memory.

## 7. Sinusoidal models (slides 22-25)

### Q20 (slide 22)

A periodic frame is modelled as $x(t)=\sum_{l=1}^{L}A_l\cos(\omega_0 l\,t+\phi_l)$, with $\omega_0=2\pi F_0$, amplitudes $A_l$ and phases $\phi_l$. All harmonics lie below the Nyquist frequency. For $F_0=150$ Hz and $f_s=16$ kHz, how many numbers describe one frame?

- A) 107
- B) 106
- C) 109
- D) 159

> [!success]- Answer
> **A.** Nyquist is 8000 Hz. $8000/150\approx53.3$, so $L=53$ (the 53rd harmonic is at 7950 Hz). One frame needs 53 amplitudes, 53 phases and one $\omega_0$: $2\times53+1=107$.
> - B: $\omega_0$ was forgotten: $2\times53=106$. It is one number shared by all harmonics.
> - C: 53.3 was rounded up to 54 harmonics: $2\times54+1=109$. The 54th harmonic, 8100 Hz, lies above Nyquist.
> - D: A separate frequency was stored for each harmonic: $3\times53=159$. All frequencies are $l\,\omega_0$.

### Q21 (slide 23)

The sinusoidal model is fitted by minimising $E=\sum_n w(n)^2\big(s(n)-\hat s(n)\big)^2$, with $s(n)$ the speech, $\hat s(n)$ the sum of harmonics and $w(n)$ the analysis window. Which statement is correct?

- A) $w(n)^2$ makes a misfit at the frame edges cost more, so frames join smoothly in overlap-add. The fit needs longer frames than a DFT.
- B) $w(n)^2$ normalises the frame energy to 1, so the fit becomes a plain Fourier analysis that needs exact pitch marks.
- C) $w(n)^2$ removes the samples outside the frame. The fit is a fixed transform, so it is faster than a DFT of the same frame.
- D) $w(n)^2$ makes a misfit in the frame centre cost more. The fit works on short frames, where a DFT has poor frequency resolution.

> [!success]- Answer
> **D.** The window is large in the centre and small at the edges, so the squared weights concentrate the accuracy in the centre. The fit also works on short frames, which follow the changes of speech better. It is a regression problem, not a transform.
> - A: The direction is reversed: the edges get small weights. The fit works with short frames.
> - B: The weights do not normalise the energy, and the method is chosen instead of plain Fourier analysis. Its pitch marks need not be exact.
> - C: The fit is a least-squares regression, not a fixed transform. Cutting the frame is not what the squared weights add.

### Q22 (slide 24)

At $F_0=200$ Hz the spectral envelope found by peak-picking is 1.0 at 200 Hz and 0.6 at 400 Hz. The pitch is lowered to 150 Hz, and the new harmonic amplitudes are read from the envelope by linear interpolation. What amplitude does the new second harmonic get?

- A) 0.8
- B) 0.6
- C) 0.7
- D) 0.9

> [!success]- Answer
> **A.** The new second harmonic lies at $2\times150=300$ Hz, halfway between 200 and 400 Hz: $(1.0+0.6)/2=0.8$.
> - B: The harmonic kept its old amplitude. Then the 0.6 of 400 Hz sits at 300 Hz, and the formants shift down by 0.75 as in resampling.
> - C: The pitch ratio $150/200=0.75$ was used as the interpolation weight: $1.0-0.75\times0.4=0.7$. The weight is $(300-200)/(400-200)=0.5$.
> - D: The weight was taken as $1-0.75=0.25$: $1.0-0.25\times0.4=0.9$.

### Q23 (slide 25)

The sinusoids found by a sinusoidal model are drawn as lines over a spectrogram. In some regions the lines are long, parallel and evenly spaced. In others they are short and scattered. Which reading is correct?

- A) The long lines are the formants, and their spacing is the formant bandwidth. The short lines are pitch-tracking errors.
- B) The long lines are harmonics of voiced speech, spaced by $F_0$. The short lines are noise, which has no stable frequencies.
- C) The long lines are steady noise, which the model fits well. The short lines are harmonics that change too fast to follow.
- D) The long lines are harmonics of voiced speech, spaced by the formants. The short lines are pauses between the words.

> [!success]- Answer
> **B.** In voiced regions the lines are the harmonics, and their vertical distance is $F_0$. In noisy regions the model finds components that appear and disappear. So a sum of sinusoids fits periodic speech well and noise badly, which is the reason for adding a noise part.
> - A: Formants are broad envelope peaks, not thin lines. The scattered lines appear in noisy regions, not because of pitch errors.
> - C: The roles are reversed. Noise has no stable frequency, so the model cannot follow it with long lines.
> - D: The spacing of harmonics is $F_0$, not the formants. In a pause there is almost no energy, so few lines are found.

## 8. Harmonic + noise models (slides 26-31)

### Q24 (slide 26)

The spectrum of a voiced frame shows regular, evenly spaced harmonic peaks at low frequencies and irregular, weaker peaks at high frequencies. What does a purely sinusoidal model do wrong in the high band?

- A) It sets the high band to zero, because a sum of harmonics can only describe frequencies below the first formant.
- B) It describes the noise by a time envelope only, so the noise keeps its timing but loses its spectral shape.
- C) It adds noise on top of the harmonics there, so the energy in the high band is counted twice and the voice is hoarse.
- D) It places exact harmonics there, so breath and turbulence noise become periodic, and the high band sounds buzzy.

> [!success]- Answer
> **D.** A purely sinusoidal model puts exact harmonics in every band. The noise from breath and turbulence is then replaced by periodic components, so the high band sounds buzzy and not breathy. The fit error is also large there.
> - A: Harmonics can lie at any multiple of $F_0$ up to Nyquist, also above the formants. The model fills the band with harmonics, not with zeros.
> - B: A time envelope belongs to the noise part of HNM. A purely sinusoidal model has no noise part.
> - C: Double energy is the risk of an HNM noise that is not high-pass filtered. A purely sinusoidal model has no noise to add.

### Q25 (slide 27)

A harmonic/stochastic model writes speech as $\hat s(t)=\hat s(t)_p+\hat s(t)_r$, with $\hat s(t)_p$ a sum of harmonics of $\omega_0$. What does $\hat s(t)_r$ represent?

- A) Random, Gaussian noise: the part that is not periodic, such as breath noise at the glottis and turbulence in the vocal tract.
- B) The error of the least-squares fit of the harmonics, which goes to zero when enough harmonics are used in every frame.
- C) The spectral envelope of the vocal tract, which is multiplied onto the harmonics to give them the right amplitudes.
- D) The low-frequency voiced energy below the cutoff, which carries most of the periodic information of the frame.

> [!success]- Answer
> **A.** $\hat s(t)_r$ is the stochastic (random) part, assumed to be Gaussian noise. It stands for what is not periodic: breath, turbulence, the noisy high band. The slide names MBE (Griffin and Lim) and HNM (Stylianou); HNM is chosen because it was developed for text-to-speech.
> - B: The noise part is a modelled signal with its own filter and envelope, not a fit error. More harmonics do not make breath noise periodic.
> - C: The envelope sets the harmonic amplitudes inside $\hat s(t)_p$. It is not an added signal.
> - D: The voiced energy at low frequencies is the harmonic part $\hat s(t)_p$.

### Q26 (slide 28)

In HNM the noise part is $s(t)_r=e(t)\,[h(t,\tau)\otimes b(t)]$, with $b(t)$ white Gaussian noise, $h(t,\tau)$ a time-varying all-pole filter, $\otimes$ convolution and $e(t)$ a function of time. Why does a model with uniform noise over the frame fail for a stop consonant?

- A) The burst starts suddenly and changes fast. Uniform noise smears it over the frame; the time envelope $e(t)$ keeps the onset.
- B) A stop has no noise at all in its burst. Any noise part adds a hiss, so the switch of the frame must be set to voiced.
- C) A stop burst has a perfectly flat spectrum, so the all-pole filter cannot shape it, and the noise $b(t)$ is used unfiltered.
- D) A stop is periodic, so its noise must come once in every pitch period; $e(t)$ keeps it in step with the harmonics.

> [!success]- Answer
> **A.** The noise of a stop burst starts suddenly. Spread uniformly over the frame, the sharp onset is lost. $e(t)$ multiplies the noise in time and shapes its loudness. The convolution with $h$ shapes its spectrum. This temporal pattern is what HNM adds.
> - B: A stop burst is noise. It is exactly the case where the noise part matters.
> - C: Burst noise has a spectral shape, which the filter $h$ gives it. The problem is in time, not in frequency.
> - D: Pitch-synchronous modulation is for voiced frames. A stop burst is not periodic.

### Q27 (slides 29-30)

Which order of the HNM analysis steps is correct?

- A) Find the highest harmonic frequency, classify voiced/unvoiced, estimate amplitudes and phases, adjust the phases.
- B) Adjust the phases, classify voiced/unvoiced, find the highest harmonic frequency, estimate amplitudes and phases.
- C) Classify voiced/unvoiced, find the highest harmonic frequency, estimate amplitudes and phases, adjust the phases.
- D) Classify voiced/unvoiced, estimate amplitudes and phases, find the highest harmonic frequency, adjust the phases.

> [!success]- Answer
> **C.** First classify the frame from the residual error of a harmonic fit, because only voiced frames get a harmonic part. Then find the cutoff, where the harmonic part ends. Then estimate $A_l$ and $\phi_l$ (the pitch is refined in the band below the cutoff). Last, adjust the phases so the frames line up, since no exact epochs were used.
> - A: The cutoff is searched only in voiced frames, so the classification must come first.
> - B: Phases can only be adjusted after they have been estimated.
> - D: The estimation uses only the band below the cutoff, so the cutoff must be known first.

### Q28 (slide 31)

In HNM synthesis of a voiced frame, the noise is high-pass filtered above the cutoff (the maximum voiced frequency). Why?

- A) Noise below the cutoff would mask the harmonics, so the pitch tracker would fail when the frame is synthesised.
- B) The all-pole filter $h(t,\tau)$ is unstable at low frequencies, so its output must be removed below the cutoff.
- C) High-pass filtering lets the noise follow the glottal pulses in time, so the ear hears one sound and not two.
- D) Below the cutoff the harmonics already describe the frame. Noise there would add energy twice and sound hoarse.

> [!success]- Answer
> **D.** Below the cutoff the frame is described by the harmonics. Noise in that band would add the energy a second time and make the voice hoarse. So the noise keeps only the band above the cutoff.
> - A: No pitch tracking is done in synthesis. The pitch is already known.
> - B: Stability of an all-pole filter does not depend on a frequency band. This is not the reason.
> - C: This is the reason for the time modulation of the noise, a different step.

## 9. STRAIGHT (slides 32-35)

### Q29 (slide 32)

STRAIGHT splits speech into a smooth spectrogram, an $F_0$ contour and an aperiodicity map (slide 32 calls it a "periodicity map"). Compared with an ordinary narrowband spectrogram, what is missing in the smooth spectrogram?

- A) The broad formant bands. Only the thin harmonic stripes are left.
- B) The change over time. Each frequency has one value for the utterance.
- C) The thin horizontal stripes of the harmonics. The formant bands stay.
- D) The energy above about 4 kHz. Only the low band of speech is kept.

> [!success]- Answer
> **C.** STRAIGHT removes the periodicity, so the harmonic stripes are gone. The broad bands of high energy that move slowly in time are the formants, and they stay. The $F_0$ is kept separately in the contour.
> - A: The reverse: the envelope is kept and the harmonic structure is removed.
> - B: It is still a spectrogram. The formants move in time, and that change is kept.
> - D: Nothing is cut off in frequency. Most energy lies below about 4 kHz simply because voiced energy does.

### Q30 (slide 33)

A student writes: "The STRAIGHT spectrogram contains only the vocal-tract filter. In synthesis STRAIGHT uses the phases that were measured in analysis." Which corrected version is right?

- A) It contains only the vocal-tract filter. The phases measured in analysis are used, then all-pass filters reduce the buzz.
- B) It contains the vocal-tract and the glottal filters. No phase is measured: minimum phase is assumed, with all-pass filters.
- C) It contains the vocal-tract and the glottal filters. The phases measured in analysis are used, then all-pass filters reduce the buzz.
- D) It contains only the vocal-tract filter. No phase is measured: minimum phase is assumed, with all-pass filters.

> [!success]- Answer
> **B.** The smooth spectrogram captures the vocal-tract and the glottal filters; only the influence of $F_0$ is removed. STRAIGHT extracts no phase. The source is flat-spectrum pulses or noise, the phase comes from a minimum-phase assumption, and all-pass filters make the pulses less sharp.
> - A: This is the student's sentence with both mistakes.
> - C: The second mistake is kept: no phase is extracted in analysis.
> - D: The first mistake is kept: the glottal filter is in the smooth spectrogram too.

### Q31 (slide 34)

Two surfaces show the same piece of speech over frequency (0 to 3000 Hz) and time (about 325 to 350 ms). The one from a rectangular window has sharp ridges along the frequency axis and along the time axis. The STRAIGHT surface is smooth with a few broad hills. Where does the ripple along the time axis come from?

- A) From the harmonics of $F_0$, which lie at evenly spaced positions.
- B) From the formants, which rise and fall as the vowel changes.
- C) From the window moving over the pitch pulses of the waveform.
- D) From the side lobes of the rectangular window in frequency.

> [!success]- Answer
> **C.** As the window slides, it covers a pitch pulse or the decay between pulses, so the result changes with the position inside the pitch period. STRAIGHT uses $F_0$ to smooth this away and keeps the envelope and its slow change.
> - A: The harmonics give the ripple along the frequency axis.
> - B: The formants change slowly. They are the broad hills that STRAIGHT keeps.
> - D: Side lobes spread energy along frequency. They do not make a periodic ripple in time.

### Q32 (slide 35)

A 1.8 s utterance must be stretched (a duration factor above 1 means longer). The rule of thumb for TD-PSOLA allows factors up to 2. The slide claims that STRAIGHT changes stay natural up to 600%. What is the longest result that the slide claims for STRAIGHT?

- A) 3.6 s
- B) 12.6 s
- C) 21.6 s
- D) 10.8 s

> [!success]- Answer
> **D.** 600% is a factor 6: $6\times1.8=10.8$ s. The price is heavy computation.
> - A: This is the TD-PSOLA limit: $2\times1.8=3.6$ s.
> - B: 600% was added to the original, a factor 7: $7\times1.8=12.6$ s.
> - C: Both factors were multiplied: $2\times6\times1.8=21.6$ s. The TD-PSOLA rule does not apply to STRAIGHT.
