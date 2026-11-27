---
course: CS 568 Speech Processing
lesson: CS568 L03 - Organization of speech sounds
type: quiz
questions: 34
---

# CS568 L03 - Organization of speech sounds - quiz

Lesson note: [[CS568 L03 - Organization of speech sounds]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Phonemes and notation (slides 2-3)

### Q1 (slide 2)

A friend says: "Taa has about 112 phonemes and Rotokas about 11, so Taa speakers can produce ten times more sounds." Which statement about this claim is correct?

- A) The claim is right: a phoneme count is the number of different sounds that the speakers of a language can produce.
- B) The claim is right in direction: more phonemes train the speakers to make more sounds, but not ten times more.
- C) The claim is wrong: a phoneme count is the number of contrasts a language uses to tell words apart, not what people can say.
- D) The claim is wrong: a phoneme count is the number of letters in the writing of a language, and spoken sounds are not counted.

> [!success]- Answer
> **C.** A phoneme count is the number of sound contrasts that the language uses to separate words. It says nothing about what a speaker can produce.
> Every human can produce far more sounds than any language uses. Each language picks a small set (most pick 20 to 40) and treats the other differences as not meaningful.
> - A: This mixes up the contrasts of a language with the abilities of its speakers. Both groups can produce the same range of sounds.
> - B: The phoneme count does not measure production at all, so there is no "less strong" version of the claim either.
> - D: Phonemes are units of the sound system, not letters. English has about 40 phonemes but 26 letters.

### Q2 (slide 3)

![[cs568_lecture_03_l3_p003.jpg]]

The TIMIT string `dh ax bcl b uh kcl k` spells the words "the book". English has about 40 phonemes, but TIMIT uses 61 symbols. Which statement is correct?

- A) All seven symbols are phonemes. TIMIT has 61 symbols because it also covers the phonemes of some other languages.
- B) `bcl` and `kcl` are phonemes of their own. TIMIT has 61 symbols because English really has 61 phonemes, not 40.
- C) `b` and `k` are not phonemes: they label silent closures. TIMIT has 61 symbols to add one for each such silence.
- D) `bcl` and `kcl` are not phonemes: they label silent closures. TIMIT has 61 symbols as it labels sounds, not contrasts.

> [!success]- Answer
> **D.** `bcl` and `kcl` label the closure, the silent part of the stops [b] and [k]. The symbols `b` and `k` label the release.
> TIMIT labels sounds and parts of sounds, not only contrasts. Closures, allophones such as the flap `dx`, syllabic consonants and silences all get a symbol, so the count goes above 40.
> - A: `bcl` and `kcl` are parts of one stop, not phonemes. And TIMIT covers English only.
> - B: A closure alone never separates two words, so it is not a phoneme. English has about 40 phonemes.
> - C: The roles are swapped. In the table `b` is the sound of **b**ee, the release; `bcl` is the closure.

## 2. Phones, allophones, coarticulation, branches (slides 4-6)

### Q3 (slide 4)

In English, aspirated [pʰ] (*pit*) and unaspirated [p] (*spit*) are allophones of one phoneme, but /p/ and /b/ are two phonemes. Which statement gives the right test and the right role of coarticulation?

- A) Swap test: *pit* said with [p] becomes a new word, so [pʰ] and [p] are two phonemes. Coarticulation makes them two words.
- B) Swap test: *pit* said with [p] is still *pit*, but /b/ gives *bit*. The neighbours choose the variant: that is coarticulation.
- C) Swap test: *pit* said with [p] is still *pit*, but /b/ gives *bit*. Coarticulation turns /p/ into /b/ next to some sounds.
- D) Sound test: [pʰ] and [p] sound nearly the same, but [p] and [b] sound very different. Coarticulation only blurs the timing.

> [!success]- Answer
> **B.** Say *pit* with an unaspirated [p]: it sounds odd, but it is still *pit*. No minimal pair exists, so the two sounds are variants of /p/. Swap /p/ for /b/ and you get *bit*, a new word, so /p/ and /b/ are two phonemes.
> Which variant appears is decided by the neighbours (after /s/ there is no aspiration). This influence of the neighbours is coarticulation.
> - A: The swap does not change the word. [pʰ] and [p] never separate two English words.
> - C: The test is right, but coarticulation changes how one phoneme is said. It never turns it into another phoneme.
> - D: Sounding similar is not the test. The [p] of *spit* is in fact close to a [b], yet it is still /p/. Only a change of meaning decides.

### Q4 (slide 5)

![[cs568_lecture_03_l3_p005.jpg]]

The figure shows a waveform of a sentence and two label rows under it. Blue lines mark boundaries in time. Which statement is correct?

- A) Row 1 labels the phones and row 2 labels the words. Each blue line marks where one label ends and the next begins.
- B) The nearly flat part between "Stella" and "Ask" is the closure of a stop, so it belongs to the first sound of "Ask".
- C) Inside a word the articulators move continuously, so a blue line between two phones is a choice of the labeller.
- D) Inside a word each phone is a separate burst in the waveform, so a blue line between two phones can be exact.

> [!success]- Answer
> **C.** Inside a word one sound flows into the next (coarticulation). The waveform has no gap there, so the boundary is a convention chosen by the labeller or the alignment program.
> - A: The rows are swapped. Row 1 has words (Please, call, Stella), row 2 has phones (P, IY, K, ...).
> - B: That part is labelled with a breath mark (BR) and !SIL in both rows. It is a pause, not part of "Ask", which starts with a vowel.
> - D: Phones inside a word are not separate bursts. The waveform changes smoothly from one to the next.

### Q5 (slide 6)

Four studies: (a) ultrasound video of the tongue during [k], (b) measuring $F_1$ and $F_2$ of vowels on spectrograms, (c) a listening test that asks whether people hear "be" or "we", (d) deciding whether two sounds can separate two words of Turkish. The fields are articulatory, acoustic and auditory phonetics, and phonology. Which statement is correct?

- A) (a) is acoustic phonetics, because the ultrasound machine works with sound waves.
- B) (d) is phonology, because it asks about the sound system of one language.
- C) (c) is acoustic phonetics, because the listeners hear the sound waves.
- D) (b) is auditory phonetics, because a spectrogram shows what the ear hears.

> [!success]- Answer
> **B.** Whether two sounds can separate words is a question about the sound system of one language. That is phonology, not phonetics.
> The full match: (a) articulatory, (b) acoustic, (c) auditory, (d) phonology.
> - A: The tool does not decide the field. The study is about the position of the tongue, so it is articulatory phonetics.
> - C: The question is what people perceive. Perception is auditory phonetics.
> - D: A spectrogram is a measurement of the sound wave. Measuring formants on it is acoustic phonetics.

## 3. Taxonomies of phoneme classes (slides 7-8)

### Q6 (slide 7)

![[cs568_lecture_03_l3_p007.jpg]]

In this tree only the stops and the fricatives split into "Voiced" and "Unvoiced". [h] has its own class, "Whisper". Why do vowels, semivowels and nasals have no voiced and unvoiced branches?

- A) In English they are always voiced, so voicing never separates two of them and one branch would be empty.
- B) Their voicing depends on the pitch of the speaker, so it changes from one speaker to the next.
- C) They are made without a narrow constriction, so the vocal folds cannot vibrate while they are said.
- D) Their voicing is carried by $F_0$, and the tree sorts the sounds by their formants, not by $F_0$.

> [!success]- Answer
> **A.** Vowels, semivowels and nasals are always voiced in English. Voicing cannot tell two of them apart, so the tree needs no branch for it.
> [h] is noise made at the glottis with no vibration of the vocal folds, so it gets its own class.
> - B: Voicing means that the vocal folds vibrate. These sounds are voiced at any pitch and for every speaker.
> - C: The opposite is true. With an open tract the vocal folds vibrate easily, and these sounds are all voiced.
> - D: The tree does not sort by formants. It sorts by class, manner and voicing, and voicing is shown where it separates sounds.

### Q7 (slide 8)

A continuant is a sound made with one fixed shape of the vocal tract, so it can be held for a few seconds. A non-continuant has a shape that changes in time. Which statement is correct?

- A) [ng] (si**ng**) is a non-continuant, because the mouth is closed just as during a stop.
- B) [y] (**y**es) is a non-continuant, because it is a quick transition into the next vowel.
- C) [oy] (b**oy**) is a continuant, because it is a vowel sound and every vowel can be held.
- D) [g] (**g**o) is a continuant, because voicing can go on during the closure of the stop.

> [!success]- Answer
> **B.** A glide such as [y] is a movement into the next vowel. It has no fixed shape to hold.
> The full result: [z] and [ng] are continuants ("zzzz", humming), [oy], [g] and [y] are non-continuants.
> - A: The mouth is closed, but the air flows out steadily through the nose. You can hum [ng], so it is a continuant.
> - C: [oy] is a diphthong. The tongue moves from one vowel position to another, so its shape changes in time.
> - D: A stop is a closure followed by a release. Voicing during the closure does not make the shape fixed.

## 4. Consonants: place, manner, voicing (slides 9-12)

### Q8 (slide 9)

A consonant is described by three attributes: place, manner and voicing. Which statement is correct?

- A) [n] and [d] differ in voicing: [n] is voiced and [d] is voiceless.
- B) [f] and [θ] (**th**ing) differ in manner: [f] is a fricative and [θ] a stop.
- C) [b] and [s] differ in all three attributes: place, manner and voicing.
- D) [k] and [g] differ in place: [k] is velar and [g] is a palatal stop.

> [!success]- Answer
> **C.** [b] is a bilabial voiced stop. [s] is an alveolar voiceless fricative. All three attributes differ.
> - A: Both are voiced alveolars. They differ in manner: [n] is a nasal, [d] is a stop.
> - B: Both are voiceless fricatives. They differ in place: labiodental against interdental.
> - D: Both are velar stops. They differ only in voicing.

### Q9 (slides 10-11)

![[cs568_lecture_03_l3_p011.jpg]]

The drawing is a cut through the head, lips on the left. The numbers mark places of articulation. Which statement is correct for English?

- A) [t] is made at number 3, the teeth, because the tongue tip touches the upper teeth.
- B) [sh] (**sh**e) is made at number 7, the hard palate, because it is called palato-alveolar.
- C) [k] is made at number 8, the velum, and no English sound uses number 9, the uvula.
- D) [k] is made at number 9, the uvula, because the back of the tongue rises at the back end.

> [!success]- Answer
> **C.** [k] is velar: the back of the tongue meets the soft palate, number 8. Number 9 is the uvula. English has no uvular sound (the French /r/ of *rouge* is made there).
> The order from front to back: [m] at the lips (1 and 2), [t] at 4, [sh] at 5, [k] at 8.
> - A: English [t] is alveolar, number 4, the ridge behind the teeth. Number 3 is dental.
> - B: Palato-alveolar means slightly behind the alveolar ridge. That is number 5 (post-alveolar), not the palate.
> - D: The velum is in front of the uvula. [k] is made at the velum, number 8.

### Q10 (slide 12)

[t], [s] and [ch] (**ch**erry) are all voiceless and made near the alveolar ridge. Which statement about their airflow and waveform is correct?

- A) [ch] shows silence, then a burst, then a stretch of noise, because it is a stop followed by a fricative.
- B) [s] shows silence, then a short burst, because the tongue closes the tract at the alveolar ridge.
- C) [t] shows steady noise that can last a long time, because air is pressed through a narrow gap.
- D) [ch] shows a stretch of noise, then silence and a burst, because it is a fricative then a stop.

> [!success]- Answer
> **A.** An affricate starts as a stop (closure, silence, then release with a burst) and is released into a fricative (noise).
> [t] gives silence and a short burst. [s] gives steady noise that can last long.
> - B: This describes a stop. In [s] the tongue comes close but does not close, so the air is turbulent all the time.
> - C: This describes a fricative. [t] blocks the air completely, so it gives silence and a burst.
> - D: The order is reversed. The closure comes first and the noise follows the release.

## 5. Vowels: frontness, height, roundness (slides 13-15)

### Q11 (slides 13-14)

Start from [iy] (b**ea**t). (a) Keep the tongue in front and open the jaw to reach [ae] (b**a**t). (b) Go back to [iy], keep the jaw high, pull the tongue back and round the lips to reach [uw] (b**oo**t). Which statement is correct?

- A) From [iy] to [ae], $F_1$ falls, because a lower vowel has a lower $F_1$.
- B) From [iy] to [uw], $F_2$ rises, because lip rounding shortens the front cavity.
- C) From [iy] to [uw], $F_2$ falls, and the lip rounding lowers it even more.
- D) From [iy] to [uw], $F_1$ changes most, because pulling the tongue back lowers the vowel.

> [!success]- Answer
> **C.** In (b) the height stays and the vowel goes from front to back. A back vowel has a low $F_2$, so $F_2$ falls (from about 2300 Hz to about 800 Hz). Rounding lowers all formants, $F_2$ most.
> In (a) only the height changes, from high to low, so $F_1$ rises.
> - A: Height and $F_1$ go inversely. A low vowel (open jaw) has a high $F_1$.
> - B: Rounding makes the tract longer at the front. A longer tube has lower resonances, so $F_2$ falls.
> - D: The jaw stays high, so the vowel stays high and $F_1$ stays low. Backness changes $F_2$.

### Q12 (slide 15)

![[cs568_lecture_03_l3_p015.jpg]]

The right-hand plot shows vowel regions with $F_1$ on the horizontal axis and $F_2$ on the vertical axis, neither axis reversed. The upper-left chart is the IPA vowel chart. Which statement is correct?

- A) "ee" and "oo" have about the same $F_2$ and differ in $F_1$, because one is a front and one a back vowel.
- B) "ee" and "oo" share an $F_1$ of about 300 Hz, because both are high vowels, and they differ in $F_2$.
- C) In the IPA chart, [y] right of [i] differs from [i] in height: it is a slightly lower front vowel.
- D) In the right plot the high front vowel lies at the bottom right, because the axes are not reversed.

> [!success]- Answer
> **B.** "ee" is at the top left ($F_1$ about 300 Hz, $F_2$ about 2500 Hz). "oo" is at the bottom left ($F_1$ about 300 Hz, $F_2$ about 800 to 1000 Hz). A low $F_1$ means both are high vowels. $F_2$ separates front from back.
> - A: It is the other way round. They share $F_1$ and differ in $F_2$, the formant of frontness.
> - C: The right-hand symbol of a pair is the rounded vowel. [y] is high and front like [i], with rounded lips (Turkish *ü*).
> - D: High front means low $F_1$ (left) and high $F_2$ (top). So it is at the top left, as "ee" shows.

## 6. Spectra and spectrograms (slides 16-21)

### Q13 (slide 16)

Two recordings of the same vowel have clearly different waveform shapes, but listeners hear no difference. Which explanation is correct?

- A) The magnitude spectra differ, but the ear averages them over time. So the waveform is still the best description.
- B) The recordings differ only in their formants. Listeners follow mainly $F_0$, so the formants can be dropped.
- C) The magnitude spectra differ, but the phases are the same. The ear hears the phase, so phase is what to keep.
- D) The magnitude spectra are the same and the phases differ. The ear hardly hears phase, so the spectrum is kept.

> [!success]- Answer
> **D.** Phase changes the shape of the waveform a lot, but the ear is not sensitive to it. So the waveform shape is a poor description of what is heard.
> The log-magnitude spectrum removes the phase and keeps what matters: the harmonics of $F_0$ (source) and the formant envelope (filter).
> - A: If the magnitude spectra differed, listeners would hear a difference. And the waveform is the poor description here.
> - B: Different formants would give a different vowel. Listeners identify vowels by their formants.
> - C: The roles are swapped. The ear is not sensitive to phase, and the magnitude spectrum is what it hears.

### Q14 (slide 17)

![[cs568_lecture_03_l3_p017.jpg]]

The left plots show the log-magnitude spectrum of a voiced and of an unvoiced sound. The right plots add a smooth red curve. The axis label says "kHz", but the ticks run from 0 to 8000: the unit is Hz. Which statement is correct?

- A) The voiced spectrum is flat with its top near 4000 Hz, and the unvoiced one falls by about 40 dB above 4000 Hz.
- B) The red curve is a smooth line through the harmonics of $F_0$, so it describes the vibration of the vocal folds.
- C) The red curve describes the vocal-tract filter, and the fast ripple under it in the voiced plot comes from the source.
- D) The red curve describes the vocal-tract filter, and the fast ripple under it in the voiced plot comes from the formants.

> [!success]- Answer
> **C.** The red curve is the spectral envelope. Its peaks are the formants, the resonances of the vocal tract. The fast ripple under it is the comb of harmonics of $F_0$, which comes from the vocal folds.
> Visible differences: the voiced spectrum has a regular comb and most of its energy below about 4000 Hz. The unvoiced one is irregular noise, flatter, with its top near 4000 Hz.
> - A: The two spectra are swapped. The voiced one falls by about 40 dB above 4000 Hz.
> - B: The envelope smooths the harmonics away. What it keeps is the shape of the filter, not the source.
> - D: The formants are the peaks of the red curve itself. The ripple is the harmonics of $F_0$.

### Q15 (slide 18)

You want to measure (a) the exact moment at which a stop is released and (b) $F_0$ from the distance between neighbouring harmonics. Which spectrogram do you use for each?

- A) (a) wideband and (b) narrowband. A window that is short in time is wide in frequency.
- B) (a) narrowband and (b) wideband. A window that is short in time is wide in frequency.
- C) (a) wideband and (b) wideband. One short window is sharp in time and in frequency.
- D) (a) narrowband and (b) narrowband. One long window is sharp in time and in frequency.

> [!success]- Answer
> **A.** Wideband uses a short window (about 5 ms): high time resolution, so a short event stays sharp. Narrowband uses a long window (about 25 ms): high frequency resolution, so the harmonics are separate lines.
> The two trade against each other ($\Delta f\approx1/T$), so one spectrogram cannot do both well.
> - B: Both choices are swapped. "Wideband" means wide in frequency, which comes from a short window.
> - C: A short window is wide in frequency, so the harmonics merge into one band.
> - D: A long window smears the release over about 25 ms, so its moment is not sharp.

### Q16 (slide 19, voicing)

![[cs568_lecture_03_l3_p019.jpg]]

This is the phrase "Should we chase". The upper spectrogram is wideband, the lower narrowband. EY lies at about 390 to 500 ms, S at about 510 to 590 ms. Which statement is correct?

- A) In the wideband panel EY shows thin horizontal lines, one per harmonic, and S shows none of them.
- B) S has its strongest energy below about 1 kHz, the region where the noise of a fricative is made.
- C) EY shows a noisy patch above about 3.5 kHz, and S shows dark formant bands at the bottom.
- D) EY shows vertical striations, one per pitch period, and S has its energy mostly above about 3.5 kHz.

> [!success]- Answer
> **D.** In the wideband panel the voiced EY has vertical striations (one per pitch period), strong low-frequency energy and dark formant bands. S has no striations and little low-frequency energy. Its noise is strongest above about 3.5 kHz.
> - A: Horizontal harmonic lines appear in the narrowband panel. The wideband window is too short to separate harmonics.
> - B: S has little energy at low frequency. The noise of [s] is high, above about 3.5 kHz.
> - C: The two sounds are swapped. The noisy high patch is S, the dark bands are EY.

### Q17 (slide 19, formant track)

![[cs568_lecture_03_l3_p019.jpg]]

In the wideband panel a band rises from about 1 kHz to above 2 kHz between about 130 and 230 ms, during D, W, IY. Just before CH, near 260 to 300 ms, there is a blue-green column. Blue and green are low levels on the colour bar. Which statement is correct?

- A) The rising band is $F_2$. It rises because the tongue moves from the back, rounded [w] to the front vowel [iy].
- B) The rising band is $F_1$. It rises because the jaw opens as the speaker moves from [w] to the vowel [iy].
- C) The rising band is $F_0$. It rises because the pitch goes up at the end of the word "we" in this phrase.
- D) The blue-green column is the noise of the fricative part of CH, which follows the release of the stop.

> [!success]- Answer
> **A.** [w] has the tongue back and the lips rounded, so its $F_2$ is low. [iy] is a front vowel, so its $F_2$ is high. The tongue glides between them and $F_2$ follows.
> - B: [iy] is a high vowel: the jaw is nearly closed and $F_1$ is low. The band near 2 kHz is too high for $F_1$ anyway.
> - C: $F_0$ is about 100 to 200 Hz. A band between 1 and 2 kHz is a formant, not the pitch.
> - D: Low levels mean almost no sound. The column is the closure of CH. Its noise comes after, near 310 to 380 ms.

### Q18 (slide 20)

![[cs568_lecture_03_l3_p020.jpg]]

This sentence has no labels. The upper panel is wideband, the lower narrowband. Which statement is correct?

- A) The tall narrow columns that reach 7 kHz, for example near 0.5 s, are vowels, because vowels have the most energy.
- B) The white gaps in the wideband panel are silence, for example pauses or the closures of stops.
- C) The dark blocks below about 4 kHz with fine vertical stripes are fricatives, and the stripes are noise bursts.
- D) The thin horizontal lines in the narrowband panel are the formants, which bend when the tongue moves.

> [!success]- Answer
> **B.** A white region has almost no energy at any frequency: no sound, so a pause or the closure of a stop.
> The dark blocks with fine vertical stripes are voiced sounds, mostly vowels (stripes = pitch periods). The tall narrow columns are high-frequency noise: fricatives or stop bursts.
> - A: Vowels have their energy below about 4 kHz and show striations. Energy up to 7 kHz with no voicing at the bottom is noise.
> - C: Regular vertical stripes are pitch periods, so these blocks are voiced. Fricative noise has no regular stripes.
> - D: The thin lines are the harmonics of $F_0$. They bend where the pitch changes. Formants are the broader dark bands.

### Q19 (slide 21)

![[cs568_lecture_03_l3_p021.jpg]]

The sampling rate is $f_s=16$ kHz. In the 512-sample waveform the large peaks are about 120 samples apart. The 64-sample spectrum shows no harmonics. $\Delta f$ is the frequency resolution of a window. Which line is correct?

- A) $F_0\approx133$ Hz. The 64-sample window has $\Delta f\approx250$ Hz, more than $F_0$, so the harmonics merge.
- B) $F_0\approx67$ Hz. The 64-sample window has $\Delta f\approx125$ Hz, more than $F_0$, so the harmonics merge.
- C) $F_0\approx133$ Hz. The 64-sample window has $\Delta f\approx125$ Hz, about equal to $F_0$, so the harmonics merge.
- D) $F_0\approx67$ Hz. The 64-sample window has $\Delta f\approx250$ Hz, more than $F_0$, so the harmonics merge.

> [!success]- Answer
> **A.** $T_0\approx120/16000\ \text{s}=7.5$ ms, so $F_0=1/T_0\approx133$ Hz.
> 64 samples last $64/16000\ \text{s}=4$ ms, less than one period. $\Delta f\approx1/0.004=250$ Hz $>133$ Hz, so neighbouring harmonics merge and only the envelope is left.
> 512 samples (32 ms) hold $512/120\approx4.3$ periods, with $\Delta f\approx31$ Hz: the harmonics are resolved.
> - B: Uses 8 kHz (the top of the spectrum axis) as the sampling rate in both places: $8000/120\approx67$ Hz and $8000/64=125$ Hz.
> - C: Uses 8 kHz for the window only: $8000/64=125$ Hz. With the true rate the window lasts 4 ms, so $\Delta f\approx250$ Hz, about twice $F_0$.
> - D: Uses 8 kHz for the period only: $8000/120\approx67$ Hz. The period is $120/16000$ s, so $F_0\approx133$ Hz.

## 7. Vowels, diphthongs, semivowels (slides 22-27)

### Q20 (slide 22)

A classmate says: "Vowels are always voiced, and one vowel is told apart from another by its $F_0$." Which judgement is correct?

- A) The first part is right and the second is wrong. Vowels are always voiced, but they are told apart by their formants.
- B) The first part is wrong and the second is right. Whispered vowels are not voiced, but voiced vowels differ in $F_0$.
- C) Both parts are wrong. A whispered vowel is not voiced but still recognised, and vowels are told apart by formants.
- D) Both parts are right. Vowels are produced with vibrating vocal folds, and each vowel has its own typical $F_0$.

> [!success]- Answer
> **C.** A whispered vowel has no voicing, and you can still hear which vowel it is. So vowels are not always voiced.
> Vowels are told apart mainly by the first three formants, which come from the shape of the vocal tract. $F_0$ is the pitch of the source: the same vowel can be said on any pitch.
> - A: The second part is fixed correctly, but the whisper shows that a vowel need not be voiced.
> - B: The first part is fixed correctly, but $F_0$ does not identify a vowel. A whisper has no $F_0$ and is still understood.
> - D: Two slips: the whisper is a voiceless vowel, and $F_0$ is set by the speaker, not by the vowel.

### Q21 (slide 23)

![[cs568_lecture_03_l3_p023.jpg]]

In the top spectrograms the dots mark the formants of ten vowels. The lower plot shows the $F_1$-$F_2$ values of many talkers, with an ellipse around each vowel. Which statement is correct?

- A) From [i] to [æ] the lowest dot moves down and the second dot moves up, so the two dots move apart.
- B) For the back vowels on the right the two lowest dots are far apart, because their $F_2$ is high.
- C) The ellipses overlap because the measurements are noisy. A better formant tracker would separate them.
- D) The ellipses overlap, so a point can belong to two vowels and $F_1$, $F_2$ alone cannot classify every vowel.

> [!success]- Answer
> **D.** Different talkers produce the same vowel with different formants. A point in an overlap region fits two vowels, so more cues are needed: $F_3$, bandwidths, the talker, the context.
> - A: The directions are reversed. From [i] to [æ], $F_1$ moves up and $F_2$ moves down, so the dots come closer.
> - B: Back vowels have a low $F_2$. Their two lowest dots are low and close together.
> - C: The spread comes from real differences between talkers (vocal-tract length, accent), not from measurement noise.

### Q22 (slides 24-25)

A text with all vowel letters removed can still be read. A text with all consonant letters removed cannot. Does this show that vowels are unimportant for speech recognition?

- A) No. The test is about writing, where consonant letters are many. In speech vowels are long and strong and carry cues.
- B) Yes. The consonants carry the identity of a word in text and in speech, so a recogniser can drop the vowel frames.
- C) Yes. Vowels carry most of the energy of speech, but energy says nothing about the words, so it can be ignored.
- D) No. Recognisers use only the vowels, because consonants are too short to be found in the speech signal at all.

> [!success]- Answer
> **A.** There are few vowel letters and many consonant letters, so in writing the consonants narrow down the word much more.
> In the speech signal, vowels are the longest and strongest sounds and have clear formants. Their formant transitions also carry cues for the neighbouring consonants. So ASR systems rely heavily on vowels.
> - B: The experiment is about letters. In speech the vowels carry most of the signal and also cues for the consonants.
> - C: The vowel frames carry formants, which identify the vowels and, through the transitions, the consonants.
> - D: Recognisers use consonants too. Bursts, noise and transitions are clearly visible in the signal.

### Q23 (slide 26)

![[cs568_lecture_03_l3_p026.jpg]]

[ay] (b**i**te) and [aw] (b**ou**t) both start near [a]. The right-hand plot shows how $F_1$ (horizontal) and $F_2$ (vertical) move during the diphthongs, and a line $F_1=F_2$. Which statement is correct?

- A) In [ay], $F_1$ rises and $F_2$ falls, because the tongue moves from [a] toward [i].
- B) In [aw], $F_2$ rises, because the lips become rounded as the vowel moves toward [u].
- C) Both lower $F_1$. $F_2$ rises in [ay] and falls in [aw], and this tells the two apart.
- D) The line $F_1=F_2$ marks vowels whose two formants merge into one peak, such as [u].

> [!success]- Answer
> **C.** [ay] moves toward [i]: $F_1$ falls and $F_2$ rises, so the two lowest bands move apart. [aw] moves toward [u]: $F_1$ falls and $F_2$ also falls, so both bands go down together. The direction of $F_2$ separates them.
> - A: Both directions are reversed. [i] is high (low $F_1$) and front (high $F_2$).
> - B: Rounding and moving back both lower $F_2$. In [aw] it falls.
> - D: $F_2$ is by definition the higher of the two, so no vowel can lie below the line. It is a limit, and [u] lies above it.

### Q24 (slide 27)

The glide [y] in "yes" and the vowel [iy] in "beet" have almost the same tongue position. Which statement about their difference is correct?

- A) The glide has a narrower constriction, so it is weaker, and it has no steady part: its formants move at once.
- B) The glide is voiceless and noisy, while the vowel is voiced and periodic, although the tongue is in one place.
- C) The glide has a wider constriction than the vowel, so it is louder, and it can be held for a longer time.
- D) The glide has a fixed tract shape like the vowel, so it can be held, and it differs only by being shorter.

> [!success]- Answer
> **A.** The narrower constriction makes the glide weaker. It is a transition: its formants move at once toward the next vowel, so it cannot be held. It also depends strongly on its neighbours.
> "Semivowel": like a vowel it is periodic and has formants; like a consonant it is short and sits at the edge of a syllable.
> - B: [y] is voiced. Glides are periodic like vowels.
> - C: The constriction is narrower, not wider, so the glide is weaker. And it cannot be held.
> - D: A glide has no fixed shape. It is a movement, not a short vowel.

## 8. Nasals, fricatives, stops (slides 28-33)

### Q25 (slide 28)

[m], [n] and [ng] are all voiced nasals: the mouth is closed and the air leaves through the nose. Which statement is correct?

- A) They are told apart by the velum: it is lowered for [m] and raised for [ng], which changes the airflow.
- B) They differ in place. The closed mouth is a side cavity that traps energy, which gives zeros in the spectrum.
- C) They differ in place. The nasal cavity acts as the side branch and cancels the formants of the open mouth.
- D) Their spectrum is weak because most of their energy lies at high frequency, where the ear is less sensitive.

> [!success]- Answer
> **B.** Place separates them: lips for [m], behind the teeth for [n], near the velum for [ng]. The closed mouth is a side cavity. It traps energy at its own resonances, and these are missing in the output: zeros (anti-resonances) of the transfer function.
> The nose absorbs much energy, so formants are broad and the level is low.
> - A: The velum is lowered for all three, otherwise no air would go through the nose.
> - C: In a nasal consonant the mouth is closed, so the mouth is the side branch. The air flows out through the nose.
> - D: Nasals have most of their energy at low frequency. They are weak because the nose absorbs energy.

### Q26 (slide 29)

![[cs568_lecture_03_l3_p029.jpg]]

The upper curve is the spectral envelope of a normal [a], the lower one of a nasalised [a]. "Z" marks a zero. Which statement is correct?

- A) The $F_1$ peak near 0.7 kHz becomes taller, because the zero near 0.5 kHz pushes energy into it.
- B) The $F_2$ peak near 1 kHz stays at about 31 dB, and $F_1$ becomes a lower shoulder next to a zero.
- C) The $F_3$ peak near 2.6 kHz becomes taller, and the $F_2$ peak near 1 kHz is removed by a zero.
- D) The zeros sit at the three formant frequencies, so all three formant peaks are completely removed.

> [!success]- Answer
> **B.** The zeros are at about 0.5, 2.1 and 2.7 kHz. $F_1$ (about 0.7 kHz, about 32 dB) is only a lower shoulder in the nasalised vowel, because the zero at 0.5 kHz sits next to it. The $F_2$ peak near 1 kHz stays at about 31 dB.
> - A: A zero removes energy. $F_1$ becomes lower, not taller.
> - C: $F_3$ falls from about 14 dB to below 10 dB and is split by a zero. $F_2$ is the peak that is almost unchanged.
> - D: The zeros lie next to the formants, not on them. $F_2$ is still a clear peak.

### Q27 (slides 30-31)

![[cs568_lecture_03_l3_p031.jpg]]

The spectrogram shows four unvoiced fricatives [f], [θ], [s], [ʃ] and their voiced partners [v], [ð], [z], [ʒ]. Which statement is correct?

- A) In each pair the two fricatives differ in place: the voiced one is made a little further back.
- B) The unvoiced ones have a band at the very bottom, below about 0.5 kHz, which the voiced ones lack.
- C) [f] and [θ] are the darkest, because the constriction at the lips and teeth is the narrowest.
- D) The voiced ones share a band at the very bottom, and [ʃ] is strongest between about 2 and 3.5 kHz.

> [!success]- Answer
> **D.** A voiced fricative has two parts: voicing at low frequency (the band at the bottom) and noise from the constriction. The darkest are [ʃ] and [ʒ], with [ʃ] strongest between about 2 and 3.5 kHz.
> - A: The pairs [f]/[v], [θ]/[ð], [s]/[z], [ʃ]/[ʒ] have the same place. Only the voicing differs.
> - B: The two groups are swapped. The low band is voicing, so it belongs to the voiced ones.
> - C: [f], [θ], [v], [ð] are the weakest. There is almost no cavity in front of the constriction to shape the noise.

### Q28 (slides 32-33)

![[cs568_lecture_03_l3_p033.jpg]]

The spectrograms show the six English stops between two vowels: *apa, ata, aka* (upper row) and *aba, ada, aga* (lower row). The picture shows almost nothing below about 0.3 kHz. Which statement is correct?

- A) The voice bar of [b], [d], [g] is clearly visible as a dark band at the bottom of the gap, missing in [p], [t], [k].
- B) All six have a white gap, the closure, and after [t] and [k] a pale, noisy stretch comes before the dark vowel.
- C) A stop can be held like a fricative, so one steady spectrum describes it, and the gap is a pause between syllables.
- D) After [b], [d], [g] a pale, noisy stretch of aspiration comes first, and after [p], [t], [k] the vowel starts at once.

> [!success]- Answer
> **B.** Every stop has a closure with almost no sound: the white gap. After [t] and [k] a thin burst line and a paler, noisy stretch (aspiration) come before the vowel is strong. After [b], [d], [g] the dark bands start at once.
> A stop is a transient (closure, then sudden release), so no single steady spectrum describes it.
> - A: Slide detail: in this print the voice bar is hardly visible. The gaps of *ada* and *aga* look as white as those of the upper row.
> - C: A stop cannot be held. The gap is its closure, part of the stop itself.
> - D: The rows are swapped. Aspiration follows the voiceless stops [p], [t], [k].

## 9. Speech perception (slides 34-38)

### Q29 (slide 34)

A student writes: "A nasalised vowel is recognised by a narrower $F_1$ peak." Which statement is correct?

- A) The direction is wrong: nasalisation makes the $F_1$ peak wider and lower, and zeros appear in the spectrum.
- B) The cue is wrong: the $F_1$ peak stays the same, and nasalisation is heard only as a lower pitch, $F_0$.
- C) Consonant perception is more complex because consonants are louder than vowels and so mask their neighbours.
- D) Consonant perception is more complex because a consonant has one steady formant pattern, shorter than a vowel's.

> [!success]- Answer
> **A.** Nasalisation widens the $F_1$ bandwidth and adds zeros. The peak becomes wider and lower.
> Consonants are harder because they are short and have several cues at once: some in the neighbouring vowel (formant transitions), some in timing (voice onset time).
> - B: Nasalisation changes the spectral envelope, not the pitch. The cues are the wider $F_1$ and the zeros.
> - C: Consonants are weaker than vowels, not louder.
> - D: A consonant often has no steady part at all. Its cues are changes in time, not one steady pattern.

### Q30 (slide 35)

[b] and [w] are both voiced and both made at the lips. Which cue separates them, and which attribute (place, manner or voicing) does that cue mainly signal?

- A) The direction of the formant transition, and it mainly signals manner.
- B) The direction of the formant transition, and it mainly signals place.
- C) The rate of the formant transition, and it mainly signals place.
- D) The rate of the formant transition, and it mainly signals manner.

> [!success]- Answer
> **D.** A stop is released suddenly, so the formants reach the vowel in about 10 ms. A glide is a slow movement of the lips, so its transition takes longer than about 30 ms. Stop against glide is a difference in manner.
> Direction and locus signal place, voice onset time signals voicing.
> - A: Two slips: both sounds are labial, so the direction is the same, and direction signals place, not manner.
> - B: Direction does signal place, but [b] and [w] have the same place, so it cannot separate them.
> - C: Rate is the right cue, but the place is the same here. Rate signals manner.

### Q31 (slide 36)

![[cs568_lecture_03_l3_p036.jpg]]

Each box is a drawn spectrogram of consonant + [a]: the lower bar is $F_1$, the upper bar is $F_2$, the bent left end is the transition. Compare "ba", "da", "ga" in the top row, then "da", "ta", "na" in the middle column. Which statement is correct?

- A) Along the row the place changes, seen in the $F_1$ bend. Down the column the $F_2$ bend changes, $F_1$ keeps its shape.
- B) Along the row the place changes, seen in the $F_2$ bend. Down the column the $F_2$ bend keeps its shape, $F_1$ changes.
- C) Along the row the place changes, seen in the $F_1$ bend. Down the column the $F_2$ bend keeps its shape, $F_1$ changes.
- D) Along the row the place changes, seen in the $F_2$ bend. Down the column the $F_2$ bend changes, $F_1$ keeps its shape.

> [!success]- Answer
> **B.** Along the row only the place changes. $F_2$ rises for "ba", starts a little above the vowel for "da" and falls steeply for "ga". $F_1$ rises in all three.
> Down the column the place is the same, so the $F_2$ bend is the same. What changes: "da" has a rising $F_1$, "ta" a late $F_1$ without a bend, "na" thin lines in front (the nasal murmur).
> - A: The roles of $F_1$ and $F_2$ are swapped in both parts. $F_2$ carries the place.
> - C: The column is right, but along the row $F_1$ rises the same way in all three. Only $F_2$ differs.
> - D: The row is right, but the column has one place, so the $F_2$ bend cannot change there.

### Q32 (slide 37)

![[cs568_lecture_03_l3_p037.jpg]]

Each row shows drawn $F_1$ and $F_2$ bars for one voiced stop followed by seven vowels. Which statement is correct?

- A) For [g], $F_2$ rises into every vowel, because the locus of [g] lies below all the vowels.
- B) For [d], $F_2$ is flat in "di", because there the $F_2$ of the vowel equals the locus.
- C) $F_1$ rises in every syllable, because all the vowels after these stops are low vowels.
- D) For [d], $F_2$ is flat in "dɛ", which puts the locus of [d] at about 1800 Hz.

> [!success]- Answer
> **D.** The $F_2$ transitions of [d] point back to one starting frequency, the locus. Where the vowel's $F_2$ equals the locus, $F_2$ is flat: in "dɛ", at about 1800 Hz. $F_2$ rises for "di" and "de" and falls for "da", "dɔ", "do", "du".
> - A: For [g], $F_2$ falls into every vowel. Its start is above the vowel.
> - B: In "di" $F_2$ rises, from below the high $F_2$ of [i]. The flat one is "dɛ".
> - C: "bi" and "bu" have high vowels too. $F_1$ rises because the closed tract has a very low $F_1$, which rises when it opens.

### Q33 (slide 38)

![[cs568_lecture_03_l3_p038.jpg]]

The wavy line marks voicing. The three drawings differ in where voicing starts compared with the release. Which statement is correct?

- A) The [k] of *skin* fits the middle drawing, and in the bottom drawing the gap is filled with aspiration noise.
- B) The [k] of *skin* fits the bottom drawing, because every voiceless English stop is aspirated before a vowel.
- C) In the bottom drawing the gap between release and voicing is silent, because the tract is still closed.
- D) In the top drawing voicing starts after the release, and this late start is what negative VOT means.

> [!success]- Answer
> **A.** In *skin* the [k] follows /s/, so it is unaspirated: voicing starts at the release (zero VOT, middle drawing). In the bottom drawing ([kʰ] of *kin*) voicing starts later. The gap is filled with aspiration: noise from air through the open glottis.
> - B: Not every voiceless stop is aspirated. After /s/ there is no aspiration, so voicing starts at the release.
> - C: After the release the tract is open. The air flows and makes aspiration noise.
> - D: In the top drawing voicing starts during the blockage, before the release. That is negative VOT.

## 10. Prosody (slide 39)

### Q34 (slide 39)

Three cases: (a) "You bought a car." against "You bought a car?", (b) "I said the RED one", (c) the same sentence from a calm and from an angry speaker. Which statement is correct?

- A) (a) uses pitch to mark a question. ASR must keep $F_0$, because without it the two sentences give different words.
- B) (b) uses intonation to mark a question. Its role is to segment the message into a statement and a question.
- C) (c) changes the phonemes of the sentence, so a recogniser needs separate word models for calm and angry speech.
- D) (a) uses pitch to mark a question. ASR can ignore this, because the words are carried by the spectral envelope.

> [!success]- Answer
> **D.** In (a) the phonemes are the same and only the pitch (intonation) differs: segmenting the message. In English the identity of a word is carried by its phonemes, that is by the spectral envelope. So the usual ASR features discard $F_0$ and still find the words.
> (b) is stress (louder, longer, higher "red"): highlighting. (c) is $F_0$ and amplitude: the state of the speaker.
> - A: The two sentences have the same words. Pitch changes the type of sentence, not the words.
> - B: (b) is a statement with stress on "red". Its role is to highlight the important word.
> - C: Emotion changes $F_0$ and loudness, not the phonemes. The same word models work.
