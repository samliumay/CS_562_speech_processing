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

## 10. Prosody (slide 39)
![[cs568_lecture_03_l3_p039.jpg]]

**Notes.** **Prosody** is long-term variation, over more than one phoneme, in pitch (intonation), amplitude (loudness) and timing (rate, rhythm). Roles: it highlights the important words (stress), it segments the message (clause boundaries, statement against question), and it signals the state of the speaker (emotion). ASR typically ignores it: the usual features discard $F_0$. Speech synthesis must produce it (lectures 17 to 19).

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

Slides not embedded: 1 (outline).
