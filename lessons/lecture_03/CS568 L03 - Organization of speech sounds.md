---
course: CS 568 Speech Processing
lecture: 3
title: Organization of speech sounds
tags: [cs568, lecture]
slides: lectures/lecture_03/l3.pdf
full_notes: lecture_notes/lecture_03/CS568_Lecture_03_Speech_Sounds.pdf
---
# CS 568 · Lecture 3 · Organization of speech sounds

> [!summary] In short
> - Phoneme = abstract contrastive unit (/p/). Allophone = a context variant. Phone = one real sound ([pʰ]). Coarticulation blurs the boundaries.
> - Consonant = place + manner + voicing. Vowel = height (high vowel, low $F_1$), frontness (front vowel, high $F_2$), rounding (lowers formants).
> - A voiced spectrum is a harmonic comb (source, spacing $F_0$) under a formant envelope (filter).
> - A window of length $T$ resolves about $\Delta f\approx1/T$. Short window = wideband spectrogram, long window = narrowband.
> - Vowels are heard from formants. Consonants from transition direction and rate, locus and voice onset time (VOT).
> - Prosody = pitch, loudness, timing over several phonemes. ASR mostly ignores it.

## 1. Phonemes and notation (slides 2-3)
![[cs568_lecture_03_l3_p002.jpg]]
![[cs568_lecture_03_l3_p003.jpg]]

**Notes.** A **phoneme** is the smallest meaningful contrastive unit in the sound system of a language. Contrastive means: swapping it for another sound can change the word. Most languages have 20 to 40. General American English has about 40 (24 consonants, 16 vowels).

**Minimal-pair test.** Two words that differ in exactly one sound are a minimal pair (*pat* / *bat*, *sip* / *ship*). One minimal pair proves the two sounds are different phonemes. Not finding one proves nothing.

Notation: phonemes in slashes /p/, phones in brackets [pʰ]. The IPA has about 75 consonants, 25 vowels and 50 diacritics and covers most languages. TIMIT uses 61 ASCII phones for English only (table on slide 3). In TIMIT a stop is split into closure and release (`pcl p`), `dx` is the flap in "butter", `q` is the glottal stop, `em en eng el` are syllabic consonants.

> [!question]- Check (slide 2): A friend says: "Taa has about 112 phonemes and Rotokas about 11, so Taa speakers can produce ten times more sounds." What is wrong?
> A phoneme count is the number of sound contrasts that a language uses to separate words. It is not the number of sounds a person can produce.
> Every human can produce far more sounds than any language uses. Each language picks a small set (most pick 20 to 40) and treats the remaining differences as not meaningful.

> [!question]- Check (slide 3): Use the table to read the TIMIT string `dh ax bcl b uh kcl k`. Which of its symbols are not phonemes, and why does TIMIT need 61 symbols for about 40 phonemes?
> `dh` (**th**en), `ax` (**a**bout), `b` (**b**ee), `uh` (b**oo**k), `k` (**k**ey): the words are "the book".
> `bcl` and `kcl` are not phonemes. They label the closure, the silent part of the stops [b] and [k]. The symbols `b` and `k` label the release.
> TIMIT labels sounds, not only contrasts. Closures, allophones such as the flap `dx`, syllabic consonants and silences all get their own symbol, so the count goes above 40.

## 2. Phones, allophones, coarticulation, branches (slides 4-6)
![[cs568_lecture_03_l3_p004.jpg]]
![[cs568_lecture_03_l3_p005.jpg]]
![[cs568_lecture_03_l3_p006.jpg]]

**Notes.**

| Word | What it is | Example |
|---|---|---|
| phoneme | abstract unit, one per contrast | /p/ |
| allophone | one variant of a phoneme, used in certain contexts | aspirated [pʰ] in *pit*, unaspirated [p] in *spit* |
| phone | one actual sound, never exactly repeated | the [pʰ] you just said |

The four /t/ of American English: *tub* aspirated, *stub* unaspirated, *but* often unreleased, *butter* a flap.

**Coarticulation:** the articulation of a phoneme is affected by its neighbours, because the articulators have mass and speakers plan ahead. So sharp phoneme boundaries are hard to define (slide 5: the drawn boundaries are a convention). This is why recognisers use context-dependent models such as triphones (lecture 15).

**Phonology** asks which sounds a language treats as different. **Phonetics** studies the sounds themselves, in three branches: articulatory (the articulators), acoustic (the sound waves), auditory (perception).

> [!question]- Check (slide 4): In English, [pʰ] (*pit*) and [p] (*spit*) are allophones of one phoneme, but /p/ and /b/ are two phonemes. Which test decides this, and what does coarticulation have to do with it?
> The swap test. Say *pit* with an unaspirated [p]: it sounds odd, but it is still the word *pit*. No minimal pair exists, so the two sounds are variants of /p/.
> Swap /p/ for /b/ and you get *bit*, a different word. So /p/ and /b/ are different phonemes.
> Which variant appears is decided by the neighbours (after /s/ there is no aspiration). This influence of the neighbours is coarticulation, and it is what turns one phoneme into several allophones.

> [!question]- Check (slide 5): The figure has a waveform and two label rows. What does each row show? Which labels mark the nearly flat part between "Stella" and "Ask", and why can the blue line between two phones inside a word not be exact?
> Row 1 is the word alignment (Please, call, Stella, ...). Row 2 is the phone alignment (P, IY, K, ..., AE, ...). Each blue line is a boundary in time.
> The flat part is labelled with a breath mark (BR) and !SIL, silence: the speaker pauses there, so the amplitude is close to zero.
> Inside a word the articulators move continuously from one sound to the next (coarticulation). The waveform has no gap there, so the boundary is a convention chosen by the labeller or the alignment program.

> [!question]- Check (slide 6): Put each study in the right field: (a) ultrasound video of the tongue during [k], (b) measuring $F_1$ and $F_2$ of vowels on spectrograms, (c) a listening test that asks whether people hear "be" or "we", (d) deciding whether two sounds can separate two words of Turkish.
> (a) Articulatory phonetics: position and movement of the articulators.
> (b) Acoustic phonetics: properties of the sound wave.
> (c) Auditory phonetics: perception and categorisation.
> (d) Phonology: it asks about the sound system of one language, not about the physical sound.

## 3. Taxonomies of phoneme classes (slides 7-8)
![[cs568_lecture_03_l3_p007.jpg]]
![[cs568_lecture_03_l3_p008.jpg]]

**Notes.** Four general classes in American English: vowels, diphthongs, semivowels, consonants.

Alternative split:

- **Continuants:** fixed vocal-tract configuration. Vowels, fricatives, nasals. You can hold them: "sssss", "mmmmm".
- **Non-continuants:** the configuration changes over time. Diphthongs, semivowels, stops, affricates.

> [!warning] Slide errors
> - Slide 7's figure lists [w], [l] as liquids and [r], [y] as glides. The standard grouping (also on slide 12) is liquids = [l], [r] and glides = [w], [y].
> - Slide 8: the text puts affricates among the non-continuants, the figure puts them under continuant consonants. The text is right: an affricate starts with a full closure and is released into a fricative.

> [!question]- Check (slide 7): In the tree only two classes have a "Voiced" and an "Unvoiced" branch. Which two? Why do vowels, semivowels and nasals have no such branches, and where does [h] sit?
> Stops (b d g against p t k) and fricatives (v, the *th* of "that", z, zh against f, the *th* of "thing", s, sh).
> Vowels, semivowels and nasals are always voiced in English, so voicing cannot separate two of them and the branch is not needed.
> [h] has its own class, "Whisper": noise made at the glottis with no vibration of the vocal folds.

> [!question]- Check (slide 8): Use the text of the slide. Is each sound a continuant or a non-continuant: [z], [ng], [oy], [g], [y]? Give the test you used.
> Test: can the sound be held for a few seconds with the vocal tract in one fixed shape?
> [z] continuant (fricative, "zzzz"). [ng] continuant (nasal, you can hum it).
> [oy] non-continuant (diphthong, the tongue moves from one vowel position to another). [g] non-continuant (stop, closure then release). [y] non-continuant (semivowel, a transition into the next vowel).

## 4. Consonants: place, manner, voicing (slides 9-12)
![[cs568_lecture_03_l3_p009.jpg]]
![[cs568_lecture_03_l3_p010.jpg]]
![[cs568_lecture_03_l3_p011.jpg]]
![[cs568_lecture_03_l3_p012.jpg]]

**Notes.** A consonant is described by three attributes.

1. **Place:** where the narrowing is. Front to back: bilabial [b m], labiodental [f v], interdental (*th*ing, *th*at), alveolar [t n z], palato-alveolar (*sh*erry, mea*s*ure), palatal (*j*oke), velar [k], si*ng*, labiovelar [w], glottal (glottal stop in "uh-uh").
2. **Manner:** how the air flows. Stop (complete closure), fricative (near closure, turbulent air), affricate (stop then fricative, *ch*erry), nasal (mouth closed, nose open), approximant (liquids [l r], glides [y w]).
3. **Voicing:** vocal folds vibrate (voiced) or not (voiceless). *S*ue / *z*oo and *p*at / *b*at differ only in voicing.

Stops, fricatives and affricates are **obstruents**. Nasals, liquids and glides are **sonorants**, always voiced in English.

> [!example] Reading the table
> [d]: alveolar, stop, voiced. [f]: labiodental, fricative, voiceless. [m]: bilabial, nasal, voiced. [k]: velar, stop, voiceless.

> [!info] Reading the drawing on slide 11 (beyond the slides)
> The drawing is a cut through the head, lips on the left.
> - Numbers 1 to 11 lie on the lips, the teeth, the roof of the mouth, the back wall of the throat and the glottis. On the upper side these are the **passive** places, the parts that do not move much. Going from 1 to 9 you move from the lips back to the uvula. Numbers 12 and 13 lie on the epiglottis and on the root of the tongue.
> - Numbers 14 to 18 lie on the tongue, the **active** articulator: 17 is the tip (apical), 16 the blade just behind the tip (laminal), 15 and 14 the front and back of the tongue body (dorsal).
> - A place of articulation is one active part meeting one passive place. Slide 10 names only the passive place. English "alveolar" is the tongue tip or blade (17 or 16) at place 4. "Velar" is the back of the tongue (14) at place 8.
> - The list is longer than the English list on slide 10 because it covers all languages.

> [!question]- Check (slide 9): Use the table. Name the single attribute that separates each pair: [n] / [d], [f] / [θ], [k] / [g]. How many attributes separate [b] and [s]?
> [n] / [d]: manner. Both are alveolar and voiced, one is a nasal and one is a stop.
> [f] / [θ]: place. Both are voiceless fricatives, labiodental against interdental.
> [k] / [g]: voicing. Both are velar stops.
> [b] / [s]: all three. [b] is a bilabial voiced stop, [s] is an alveolar voiceless fricative.

> [!question]- Check (slides 10-11): Give the place name (slide 10) and the number of that place on the drawing (slide 11) for [m], [t], [sh] and [k]. Why does English not use number 9?
> [m]: bilabial, the two lips (numbers 1 and 2 on the upper lip meet the lower lip).
> [t]: alveolar, number 4. [sh]: palato-alveolar, slightly behind the alveolar ridge, number 5 (post-alveolar). [k]: velar, number 8.
> Number 9 is the uvula. English has no uvular sound. The French /r/ in *rouge* is made there.
> So the order from front to back is [m], [t], [sh], [k].

> [!question]- Check (slide 12): [t], [s] and [ch] (*cherry*) are all voiceless and made near the alveolar ridge. Compare the airflow of the three. What would each look like in a waveform?
> [t], stop: the airstream is blocked completely and then released. Waveform: silence, then a short burst.
> [s], fricative: the tongue comes very close but does not close. Air is pressed through the gap and becomes turbulent. Waveform: steady noise that can last long.
> [ch], affricate: a stop followed by a fricative. Waveform: silence, a burst, then a stretch of noise.

## 5. Vowels: frontness, height, roundness (slides 13-15)
![[cs568_lecture_03_l3_p013.jpg]]
![[cs568_lecture_03_l3_p014.jpg]]
![[cs568_lecture_03_l3_p015.jpg]]

**Notes.** $F_1,F_2,F_3$ are the formants (resonances of the vocal tract). $F_0$ is the fundamental frequency (rate of the vocal folds).

- **Frontness:** where the greatest constriction is. Front: b*ea*t, b*i*t, b*e*t, b*a*t. Central: schwa, *a*bout. Back: b*oo*t, b*ou*ght, b*u*t, f*a*ther. Front vowel, high $F_2$. Back vowel, low $F_2$.
- **Height:** how close the jaws are. High vowel ([iy], [uw]), low $F_1$. Low vowel ([ae], [aa]), high $F_1$.
- **Roundness:** lips rounded or spread. Rounding lowers all formants, especially $F_2$. In English back vowels are rounded.

A plot of $F_2$ against $F_1$ with both axes reversed looks like the articulatory vowel chart: [i] at (300, 2300) Hz is high front, [u] at (350, 800) Hz is high back, [a] at (750, 1200) Hz is low.

> [!warning] Watch out
> $F_0$ is not $F_1$. $F_0$ comes from the source, $F_1$ from the filter. You can sing [i] on any pitch: $F_0$ changes, $F_1$ and $F_2$ stay.

> [!info] Why height goes with $F_1$ and frontness with $F_2$ (beyond the slides)
> This is a simplified picture from the tube model of lecture 2.
> - Opening the jaw (low vowel) widens the front of the tract and narrows the throat. This raises $F_1$. Closing the jaw (high vowel) lowers $F_1$.
> - Moving the tongue forward makes the cavity in front of the constriction shorter. A short cavity resonates at a high frequency, so $F_2$ rises. Pulling the tongue back makes the front cavity long, so $F_2$ falls.
> - Rounded (yuvarlak) lips make the tract longer at the front end. A longer tube has lower resonances, so all formants fall, $F_2$ most.
>
> "Front vowels are unrounded, back vowels are rounded" is a fact about English, not a law. Turkish *ü* and *ö* are front and rounded. In the IPA chart of slide 15 they are the right-hand symbols of the front pairs: [y] next to [i], [ø] next to [e]. Also the slide's rule is rough even for English: [aa] in f**a**ther is back but not rounded.

> [!question]- Check (slides 13-14): Start from [iy] (b**ea**t). (a) Keep the tongue in front and open the jaw until you reach [ae] (b**a**t). (b) Go back to [iy], keep the jaw high, pull the tongue back and round the lips until you reach [uw] (b**oo**t). Which formant changes most in each case, and in which direction?
> (a) Only the height changes, from high to low. Height goes with $F_1$ inversely, so $F_1$ rises. $F_2$ stays fairly high because the vowel is still front.
> (b) The height stays and the frontness changes, from front to back. $F_2$ falls strongly (from about 2300 Hz for [i] to about 800 Hz for [u]). The lip rounding lowers $F_2$ even more. $F_1$ stays low because both vowels are high.

> [!question]- Check (slide 15): (a) In the right-hand plot, where are the regions "ee" and "oo"? What do they share and what separates them? (b) This plot does not have reversed axes. Where is the high front vowel now? (c) In the IPA chart, which symbol stands directly to the right of "i", and in which feature does it differ from [i]?
> (a) "ee" is at the top left: $F_1$ about 300 Hz, $F_2$ about 2500 Hz. "oo" is at the bottom left: $F_1$ about 300 Hz, $F_2$ about 800 to 1000 Hz. They share a low $F_1$ (both are high vowels). $F_2$ separates them (front against back).
> (b) High front "ee" is at the top left and the open vowel "ah" is on the right, because $F_1$ grows to the right. The IPA chart has the open vowels at the bottom. So this plot is the vowel chart turned: the red arrows on the IPA chart show the same two directions ($F_2$ grows to the front, $F_1$ grows downward).
> (c) The symbol "y". The right-hand symbol of a pair is the rounded vowel. [y] is high and front like [i], but with rounded lips (Turkish *ü*).

## 6. Spectra and spectrograms (slides 16-21)
![[cs568_lecture_03_l3_p016.jpg]]
![[cs568_lecture_03_l3_p017.jpg]]
![[cs568_lecture_03_l3_p018.jpg]]
![[cs568_lecture_03_l3_p019.jpg]]
![[cs568_lecture_03_l3_p020.jpg]]
![[cs568_lecture_03_l3_p021.jpg]]

**Notes.** Waveforms are rarely studied directly, because phase changes the shape a lot but matters little for perception. The log-magnitude spectrum of a voiced phone shows a **comb** (harmonics of $F_0$, the source) under a **broad envelope** (formants, the filter). Linear prediction (lecture 7) and cepstral analysis (lecture 9) separate the two.

A **spectrogram** is a spectrum moving over time: time horizontal, frequency vertical, colour = log magnitude.

| | Wideband | Narrowband |
|---|---|---|
| Window | short (5 ms) | long (25 ms) |
| Time resolution | high | poor |
| Frequency resolution | poor | high |
| Voiced phones show | vertical striations (pitch periods) | horizontal lines (harmonics) |

Rule: a window of length $T$ has frequency resolution about $\Delta f\approx1/T$. Harmonics are seen separately only if $F_0>\Delta f$.

> [!example] $F_0=150$ Hz
> Pitch period $T_0=1/150\approx6.67$ ms. A 5 ms window: $\Delta f\approx200$ Hz $>150$, harmonics merge, striations appear. A 25 ms window: $\Delta f\approx40$ Hz $<150$, harmonics are resolved.

Slide 21 ($f_s=16$ kHz): 64 and 128 samples (4 and 8 ms) show only the envelope, 512 samples (32 ms) shows the harmonics best, 1024 samples (64 ms) blurs them again.

> [!warning] Watch out
> Wideband means short window. And a longer window gives clearer harmonics only while $F_0$ stays constant inside it (up to about 30 to 40 ms of real speech).

> [!warning] Slide error
> Slide 17 labels the horizontal axes "frequency in kHz", but the tick values run from 0 to 8000. The unit is Hz (0 to 8 kHz).

> [!question]- Check (slide 16): Two recordings of the same vowel have clearly different waveform shapes, but listeners hear no difference. How is this possible, and what does it mean for the choice of representation?
> The two signals can have the same magnitude spectrum and different phases. Phase changes the shape of the waveform a lot, but the ear is not sensitive to it.
> So the waveform shape is a poor description of what is heard. The log-magnitude spectrum removes the phase and keeps what matters: the harmonics of $F_0$ (source) and the formant envelope (filter).

> [!question]- Check (slide 17): Look at the two left-hand plots. Give two visible differences between the voiced and the unvoiced spectrum. What is the red curve in the right-hand plots, and which part of speech production does it describe?
> Voiced: a regular comb of narrow peaks (the harmonics of $F_0$), clearest at low frequency. Most energy lies below about 4000 Hz, and above it the level is about 40 dB lower.
> Unvoiced: no regular comb, only irregular noise. The level is flatter, with its highest part near 4000 Hz.
> The red curve is the spectral envelope, a smooth line through the fine structure. It describes the vocal-tract filter (the formants). The fast ripple under it comes from the source.

> [!question]- Check (slide 18): You want to measure (a) the exact moment at which a stop is released and (b) $F_0$ from the distance between harmonics. Which type of spectrogram do you use for each, and why can one spectrogram not do both well?
> (a) Wideband: the short window (about 5 ms) gives high time resolution, so a short event stays sharp in time.
> (b) Narrowband: the long window (about 25 ms) gives high frequency resolution, so the harmonics appear as separate lines.
> Time and frequency resolution trade against each other ($\Delta f\approx1/T$): a window that is short in time is wide in frequency, and the other way round.

> [!question]- Check (slide 19, voicing): In the wideband spectrogram, how can you tell that EY (about 390 to 500 ms) is voiced and S (about 510 to 590 ms) is unvoiced? Where is the energy of S? How does the narrowband picture of EY differ?
> EY: vertical striations (one per pitch period), strong energy at the bottom and dark formant bands. The waveform above it is large and periodic.
> S: no striations and little energy at low frequency. The energy is a noisy patch that is strongest above about 3.5 kHz. The waveform is small and irregular.
> Narrowband: EY shows thin horizontal lines, the harmonics, in place of the vertical striations.

> [!question]- Check (slide 19, formant track): In the wideband spectrogram a band rises from about 1 kHz to above 2 kHz between about 130 and 230 ms (D, W, IY). Which formant is it, and why does it rise? What is the blue-green column just before CH, near 260 to 300 ms?
> It is $F_2$. [w] is made with the tongue back and the lips rounded, so $F_2$ is low. [iy] is a front vowel, so $F_2$ is high. The tongue glides from one position to the other and $F_2$ follows it.
> The blue-green column is the closure of the affricate CH: the airstream is stopped, so there is almost no energy (blue and green are the low levels on the colour bar). The noise that follows it (about 310 to 380 ms) is the fricative part.

> [!question]- Check (slide 20): This sentence has no labels. In the wideband panel, find three kinds of regions and say which kind of sound each one is: (a) dark blocks below about 4 kHz with fine vertical stripes, (b) white gaps, (c) tall narrow columns that reach 7 kHz, for example near 0.5 s and 0.75 s. What do the thin horizontal lines in the narrowband panel show?
> (a) Voiced sounds, mostly vowels: the stripes are pitch periods and the darker bands are formants.
> (b) Silence: pauses or the closures of stops.
> (c) Noise with energy at high frequency and no voicing at the bottom: fricatives or the bursts of stops.
> The horizontal lines are the harmonics of $F_0$. Where they bend, the pitch is changing.

> [!question]- Check (slide 21): In the 512-sample waveform the large peaks are about 120 samples apart ($f_s=16$ kHz). Estimate the pitch period and $F_0$. Why does the 64-sample spectrum show no harmonics, and how many periods does the 512-sample window hold?
> Pitch period: $T_0\approx120/16000\ \text{s}=7.5$ ms. $F_0=1/T_0\approx16000/120\approx133$ Hz.
> 64 samples last $64/16000=4$ ms, less than one period. The resolution is $\Delta f\approx1/0.004=250$ Hz, which is more than 133 Hz, so neighbouring harmonics merge and only the envelope is left.
> 512 samples hold $512/120\approx4.3$ periods ($32$ ms, $\Delta f\approx31$ Hz), so the harmonics are resolved.

## 7. Vowels, diphthongs, semivowels (slides 22-27)
![[cs568_lecture_03_l3_p022.jpg]]
![[cs568_lecture_03_l3_p023.jpg]]
![[cs568_lecture_03_l3_p024.jpg]]
![[cs568_lecture_03_l3_p025.jpg]]
![[cs568_lecture_03_l3_p026.jpg]]
![[cs568_lecture_03_l3_p027.jpg]]

**Notes.** **Vowels** are voiced, have the greatest intensity and last 50 to 400 ms. They are told apart mainly by the first three formants. Slide 23: each vowel takes a region of the $F_1$-$F_2$ plane, and the regions of different talkers overlap.

Slides 24-25: text without vowels is still readable, text without consonants is not. In writing the consonants carry the information. In speech the vowels carry most of the energy and duration, so recognisers rely on them.

A **diphthong** is a vowel in which the tongue and lips move between two vowel positions: [ay] b*i*te, [oy] b*oy*, [aw] b*ou*t. On a spectrogram the formant bands slide.

**Glides and liquids** (semivowels) look like vowels but are weaker, because the constriction is narrower. They are transitional and depend on context. Liquids ([l], [r]) colour the preceding vowel. Glides ([y], [w]) lengthen vowels into diphthongs.

> [!question]- Check (slide 22): Find the two mistakes: "Vowels are always voiced, and one vowel is told apart from another by its $F_0$."
> Mistake 1: a whispered vowel is not voiced, and you can still hear which vowel it is.
> Mistake 2: vowels are told apart mainly by the first three formants, which come from the shape of the vocal tract. $F_0$ is the pitch of the source. The same vowel can be said on any pitch, and a whisper has no $F_0$ at all.

> [!question]- Check (slide 23): (a) In the top spectrograms the dots mark the formants. How do the two lowest dots move from [i] to [æ], and how do they lie for the back vowels on the right? (b) In the lower plot the ellipses of neighbouring vowels overlap. What does this mean for a recogniser?
> (a) From [i] to [æ] the lowest dot ($F_1$) moves up and the second dot ($F_2$) moves down, so they come closer. For the back vowels both dots are low and close together (low $F_2$).
> (b) Different talkers produce the same vowel with different formants. A point in an overlap region can belong to two vowels, so $F_1$ and $F_2$ alone cannot classify every vowel correctly. More cues are needed: $F_3$, bandwidths, the talker, the context.

> [!question]- Check (slides 24-25): The first text has no vowels, the second has no consonants, and only the first can be read. Does this show that vowels are unimportant for speech recognition?
> No. The experiment is about written text. There are few vowel letters and many consonant letters, so the consonants narrow down the word much more.
> In the speech signal it is different. Vowels are the longest and strongest sounds and have clear formants. Their formant transitions also carry cues for the consonants next to them (section 9). So ASR systems rely heavily on vowels.

> [!question]- Check (slide 26): [ay] (b**i**te) and [aw] (b**ou**t) both start near [a]. Use the right-hand plot to say what $F_1$ and $F_2$ do in each. How do you tell the two apart in a spectrogram? What does the line $F_1=F_2$ mean?
> [ay] moves toward [i]: $F_1$ falls and $F_2$ rises. In the spectrogram the two lowest bands move apart.
> [aw] moves toward [u]: $F_1$ falls and $F_2$ also falls. The two bands stay close and both go down.
> So the direction of $F_2$ separates them. The line $F_1=F_2$ is a limit: $F_2$ is by definition the higher of the two, so every vowel lies above this line.

> [!question]- Check (slide 27): The glide [y] in "yes" and the vowel [iy] in "beet" have almost the same tongue position. Give three differences. Why is "semivowel" a good name?
> The glide has a narrower constriction, so it is weaker than the vowel.
> The glide is a transition: it has no steady part, and its formants move at once toward the next vowel. The vowel can be held.
> The glide depends strongly on its neighbours. It also cannot be the centre of a syllable (beyond the slides).
> "Semivowel": like a vowel it is periodic, has formants and has most of its energy at low frequency. Like a consonant it is short and sits at the edge of a syllable.

## 8. Nasals, fricatives, stops (slides 28-33)
![[cs568_lecture_03_l3_p028.jpg]]
![[cs568_lecture_03_l3_p029.jpg]]
![[cs568_lecture_03_l3_p030.jpg]]
![[cs568_lecture_03_l3_p031.jpg]]
![[cs568_lecture_03_l3_p032.jpg]]
![[cs568_lecture_03_l3_p033.jpg]]

**Notes.**

- **Nasals:** voiced, mouth closed, velum lowered, air through the nose. The closed mouth is a side cavity that traps energy at some frequencies. These appear as **anti-resonances**, zeros of the transfer function (dips in the spectrum). Formant bandwidths are broad. Place: [m] lips, [n] behind the teeth, [ng] at the velum. An all-pole model (linear prediction) has no zeros, so it fits nasals less well.
- **Fricatives:** turbulent air at a constriction. Unvoiced [f], [th], [s], [sh] have a noisy spectrum. Voiced [v], [dh], [z], [zh] have a noisy part plus a harmonic part at low frequency.
- **Stops:** transient. Pressure builds behind a closure and is released. On a spectrogram: a gap (closure), a thin vertical line (burst), then the vowel formants. Voiced [b], [d], [g] show a low-frequency **voice bar** during the closure. Unvoiced [p], [t], [k] do not.

> [!warning] The voice bar on slide 33
> The voice bar is hard to see in the print on slide 33. The closure gaps of *ada* and *aga* look as white as the gaps of the upper row. Only *aba* has at most a faint trace at the bottom of its gap. What the picture does show: after [b], [d], [g] the dark vowel bands start at once, and after [p], [t], [k] the vowel starts with a paler, noisy stretch.

> [!info] Where the noise of a fricative lies (beyond the slides)
> In a fricative (sürtünmeli) the noise is made at the constriction and is then shaped by the small cavity in front of it.
> - [s]: the constriction is far forward, so the front cavity is very short. A short cavity resonates high: most energy is above about 4 kHz.
> - [sh]: the constriction is further back, so the front cavity is longer and the noise starts lower, from about 2 kHz.
> - [f] and the *th* of "thing": the constriction is at the teeth or lips, so there is almost no front cavity. The noise is weak and flat.
>
> The spectrogram on slide 31 stops just below 4 kHz, so it shows only the lower edge of the [s] noise. A voiced (ötümlü) fricative adds a band at the very bottom, because the vocal folds vibrate at the same time.

> [!info] The three phases of a stop (beyond the slides)
> A plosive (patlamalı) is a sequence in time: (1) **closure**, the tract is shut and pressure builds up, almost silent; (2) **release**, a burst of a few ms with energy at many frequencies; (3) **transition**, the formants move to the next vowel. After a voiceless stop there can be a stretch of aspiration noise before the vowel starts. The place of the stop is read from the burst and the transitions, the voicing from the closure and from the time until voicing starts (VOT, section 9).

> [!question]- Check (slide 28): [m], [n] and [ng] have the same manner and the same voicing. What separates them? Why does a nasal have anti-resonances, and why is its spectrum weak compared with a vowel?
> The place of the closure in the mouth separates them: lips for [m], behind the teeth for [n], near the velum for [ng].
> The mouth is closed at the front and open at the back, so it is a side cavity. It traps energy at its own resonance frequencies, and these are missing in the output: zeros of the transfer function. (Beyond the slides: the cavity is longest for [m] and shortest for [ng], so the lowest zero is lowest for [m] and highest for [ng].)
> The sound leaves through the nose. The nasal cavity has a large surface that absorbs energy, so the formants are broad and the level is low. Most energy stays at low frequency.

> [!question]- Check (slide 29): Compare the two envelopes. Where are the three "Z" marks in the nasalised [a]? What happened to the $F_1$ and $F_3$ peaks, and which peak is almost unchanged?
> The zeros are at about 0.5 kHz, about 2.1 kHz and about 2.7 kHz. At each one the curve has a dip.
> $F_1$ (about 0.7 kHz) was a clear peak of about 32 dB. In the nasalised vowel it is only a lower shoulder, because the zero at 0.5 kHz sits next to it.
> $F_3$ (about 2.6 kHz) falls from about 14 dB to below 10 dB and is split by a zero.
> The $F_2$ peak near 1 kHz stays at about 31 dB.

> [!question]- Check (slides 30-31): (a) Pair each unvoiced fricative with its voiced partner. What is the same in a pair and what differs? (b) In the spectrogram, what visible feature do the four voiced fricatives share? Which fricatives are darkest, and where is the energy of [ʃ]?
> (a) [f]/[v], [θ]/[ð], [s]/[z], [ʃ]/[ʒ]. The place of the constriction is the same. Only the voicing differs: in the voiced one the glottis vibrates while the air is turbulent at the constriction.
> (b) The voiced ones have a band at the very bottom (below about 0.5 kHz), the harmonic part. The unvoiced ones have very little there. So a voiced fricative has two components: low-frequency voicing and noise.
> [ʃ] and [ʒ] are darkest, then [s] and [z]. [f], [θ], [v], [ð] are weak. The energy of [ʃ] is strongest between about 2 and 3.5 kHz.

> [!question]- Check (slides 32-33): (a) Why can a stop not be described by one steady-state spectrum? (b) In the spectrograms, what do all six stops share? Name two visible differences between the upper row (p, t, k) and the lower row (b, d, g).
> (a) A stop is a transient: a closure, then a sudden release. It cannot be held. What identifies it is the order of events in time.
> (b) All six have a white gap between the two vowels. This is the closure, with almost no sound.
> Difference 1: after [t] and [k] there is a thin dark vertical line (the burst) and then a paler, noisy stretch (aspiration) before the vowel becomes strong. After [b], [d], [g] the dark formant bands start at once.
> Difference 2: in the lower row the bands bend clearly next to the gap (formant transitions). They are easiest to see in *ada* and *aga*.
> The voice bar of the voiced stops is hardly visible in this print, because the picture shows almost nothing below about 0.3 kHz.

## 9. Speech perception (slides 34-38)
![[cs568_lecture_03_l3_p034.jpg]]
![[cs568_lecture_03_l3_p035.jpg]]
![[cs568_lecture_03_l3_p036.jpg]]
![[cs568_lecture_03_l3_p037.jpg]]
![[cs568_lecture_03_l3_p038.jpg]]

**Notes.** **Vowels:** formant frequencies are the main cue. Formants scale with vocal-tract length, so listeners normalise by formant spacing (ratios). Nasalisation is cued by a wider $F_1$ bandwidth and by zeros.

**Consonants,** four cues:

- **Direction of formant transition** into the vowel: depends on the place of articulation.
- **Rate of transition:** in "be" the [b] to [i] transition takes about 10 ms. Stretched beyond about **30 ms**, "be" becomes "we".
- **Formant locus:** all $F_2$ transitions of one stop point back to a common starting frequency set by the place of closure. For [d] it is near 1800 Hz (slide 37).
- **Voice onset time (VOT):** time from the release of the closure to the start of voicing. In "do", when the VOT exceeds about **25 ms** the word is heard as "to".

VOT categories (slide 38): negative ([b d g], voicing starts before the release), zero (unaspirated [p t k]), positive (aspirated [pʰ tʰ kʰ]).

> [!example] Ratios survive
> Uniform tube, $F_n=(2n-1)c/4L$. Adult ($L=17.5$ cm): 500, 1500, 2500 Hz. Child ($L=14$ cm): 625, 1875, 3125 Hz. Every child formant is 25% higher, but $F_2/F_1=3$ for both: $L$ cancels.

> [!example] Locus
> [d] locus 1800 Hz. Into [i] ($F_2\approx2300$): $F_2$ rises by 500 Hz. Into [u] ($F_2\approx800$): $F_2$ falls by 1000 Hz.

> [!warning] Watch out
> Slide 35 writes the vowel of "do" as [o]. It is [u]. Also, English word-initial /b d g/ often have VOT near zero, not negative: "do" and "to" differ by short against long VOT.

> [!info] Reading the schematic on slide 36 (beyond the slides)
> Each box is a drawn spectrogram of consonant + [a]: time runs to the right, the lower bar is $F_1$, the upper bar is $F_2$. The bent left end of a bar is the transition out of the consonant.
> - **Columns = place.** The $F_2$ bend is the same down a column: it rises for the lip sounds (ba, pa, ma), starts a little above the vowel for the alveolar sounds (da, ta, na) and falls steeply for the velar sounds (ga, ka, ŋa).
> - **Rows = manner and voicing.** Voiced stops: $F_1$ rises from a low start. Unvoiced stops: the $F_1$ bar starts later and has no bend, because during the aspiration there is no voicing to excite $F_1$. Nasals (genizsil): the same bends as the voiced stops, with short thin lines in front. These lines are the nasal murmur, the weak steady sound while the mouth is closed.

> [!question]- Check (slide 34): Find the mistake: "A nasalised vowel is recognised by a narrower $F_1$ peak." Then say why the perception of consonants is called more complex than that of vowels.
> The $F_1$ bandwidth increases (the peak becomes wider and lower), and zeros appear in the spectrum.
> A vowel has a steady part, and its formant frequencies identify it. A consonant is short and has several cues at once. Some of them lie in the neighbouring vowel (the formant transitions) and some are timing cues (voice onset time).

> [!question]- Check (slide 35): [b] and [w] are both voiced and both made at the lips. Which cue separates them, and why does this fit the way they are produced? Which attribute (place, manner, voicing) does each of the cues "direction of transition and locus", "rate of transition" and "VOT" mainly signal?
> The rate of the formant transition. A stop is released suddenly, so the formants reach the vowel in about 10 ms. A glide is a slow movement of the lips and tongue, so the transition takes longer than about 30 ms.
> Direction and locus: place. Rate: manner (stop against semivowel). VOT: voicing.

> [!question]- Check (slide 36): Compare "ba", "da" and "ga" in the top row. What changes? Then compare "da", "ta" and "na" in the middle column. What stays the same and what changes?
> Along the row only the place changes, and with it the $F_2$ transition: rising for "ba", starting slightly above the vowel for "da", falling steeply for "ga". $F_1$ rises in all three.
> Down the column the place is the same, so the $F_2$ transition has the same shape in all three.
> What changes is the rest: "da" has a rising $F_1$, "ta" has a late $F_1$ without a bend, "na" has extra thin lines in front (the nasal murmur).

> [!question]- Check (slide 37): Look at the direction of the upper band ($F_2$) in each row. What is the pattern for b, for g and for d? In which d syllable is $F_2$ flat, and what does that tell you? Why does the lower band ($F_1$) rise in every syllable?
> b: $F_2$ rises into the vowel (or is nearly flat for the back vowels). It never falls. The locus of [b] is low.
> g: $F_2$ falls into every vowel. Its starting point is above the vowel.
> d: $F_2$ rises for "di" and "de", is flat for "dɛ", and falls for "da", "dɔ", "do", "du". It is flat where the $F_2$ of the vowel equals the locus, at about 1800 Hz.
> $F_1$: during the closure the tract is shut, and a closed tract has a very low $F_1$. When it opens, $F_1$ rises to the value of the vowel.

> [!question]- Check (slide 38): In the three drawings, where does the wavy line (voicing) start compared with the release? Which drawing fits the [p] of *spit*, the [pʰ] of *pit* and a fully voiced [b]? What fills the time between release and voicing in the third drawing?
> Top: voicing starts during the blockage, before the release. Negative VOT. This is the fully voiced [b].
> Middle: voicing starts at the release. Zero VOT. This is the unaspirated [p] of *spit*.
> Bottom: voicing starts some time after the release. Positive VOT. This is the aspirated [pʰ] of *pit*.
> The gap is filled with aspiration: noise from air that flows through the open glottis.

## 10. Prosody (slide 39)
![[cs568_lecture_03_l3_p039.jpg]]

**Notes.** **Prosody** is long-term variation, over more than one phoneme, in pitch (intonation), amplitude (loudness) and timing (rate, rhythm). Roles: it highlights the important words (stress), it segments the message (clause boundaries, statement against question), and it signals the state of the speaker (emotion). ASR typically ignores it: the usual features discard $F_0$. Speech synthesis must produce it (lectures 17 to 19).

> [!question]- Check (slide 39): Which prosodic quantity and which role from the slide is used in each case: (a) "You bought a car." against "You bought a car?", (b) "I said the RED one", (c) the same sentence spoken by a calm and by an angry speaker? Why can ASR ignore all of this and still find the words?
> (a) Pitch (intonation). Role: segmenting the message, here statement against question. The phonemes are the same.
> (b) Stress: "red" is louder, longer and often higher. Role: highlighting the word that the speaker finds important.
> (c) $F_0$ and amplitude patterns. Role: a cue to the state of the speaker.
> In English the identity of a word is carried by its phonemes, that is by the spectral envelope. Prosody lies above the phoneme level, so the words can be found without it, and the usual ASR features discard $F_0$.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Phoneme / allophone / phone | abstract unit / context variant / one real sound |
| Minimal pair | two words differing in one sound. Proves two phonemes |
| Coarticulation | neighbours change how a phoneme is articulated |
| Continuant | fixed tract: vowels, fricatives, nasals |
| Place, manner, voicing | the three attributes of a consonant |
| Height and $F_1$ | inverse: high vowel, low $F_1$ |
| Frontness and $F_2$ | front vowel, high $F_2$ |
| Frequency resolution | $\Delta f\approx1/T$ for a window of length $T$ |
| Pitch period | $T_0=1/F_0$ |
| Wideband / narrowband | short window (about 5 ms) / long window (about 25 ms) |
| Anti-resonance | a zero of the transfer function, a dip. Nasals have them |
| Locus | common starting frequency of a stop's $F_2$ transitions |
| VOT | release to start of voicing. do/to boundary about 25 ms |
| be/we boundary | transition longer than about 30 ms |
| Prosody | pitch, loudness, timing over several phonemes |

## Check yourself
> [!question]- 1. Give a minimal pair showing that /f/ and /v/ are different phonemes.
> *fan* / *van* (or *fine* / *vine*): only the first sound differs and the meaning changes.

> [!question]- 2. A vowel has $F_1=300$ Hz and $F_2=2300$ Hz. High or low? Front or back?
> Low $F_1$ means high vowel. High $F_2$ means front vowel. It is [i] as in "beat".

> [!question]- 3. Which pair differs only in voicing: [t]/[d], [t]/[s] or [p]/[m]?
> [t]/[d]: both alveolar stops. [t]/[s] differ in manner, [p]/[m] in manner and voicing.

> [!question]- 4. $F_0=100$ Hz. Does a 5 ms window resolve the harmonics? A 30 ms window?
> 5 ms: $\Delta f\approx200$ Hz $>100$, not resolved (striations instead). 30 ms: $\Delta f\approx33$ Hz $<100$, resolved. The window holds 3 pitch periods.

> [!question]- 5. Why does an all-pole model fit [m] worse than [a]?
> [a] is an unbranched tube with only resonances (poles). For [m] the closed mouth is a side branch that adds zeros. An all-pole model can only approximate the dips.

> [!question]- 6. A [d] in "do" is delayed so the VOT is 40 ms. The [b] to [i] transition of "be" is stretched to 50 ms. What is heard?
> $40>25$ ms: "to". $50>30$ ms: "we".

## Links
- Course index: [[CS568 Home]]
- Previous: [[CS568 L02 - Speech production and perception]]. Next: [[CS568 L04 - Signals and transforms]]
- Used later: [[CS568 L06 - Short-time Fourier analysis]] (spectrograms), [[CS568 L07 - Linear prediction of speech]] (all-pole envelope)
- Full step-by-step notes (PDF): [CS568_Lecture_03_Speech_Sounds.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_03/CS568_Lecture_03_Speech_Sounds.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_03/CS568_Lecture_03_Speech_Sounds.pdf`
- Original slides (PDF): [l3.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_03/l3.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_03/l3.pdf`
- Quiz: [[CS568 L03 - Organization of speech sounds - quiz]]

Slides not embedded: 1 (outline).
