---
course: CS 568 Speech Processing
lecture: 2
title: Speech production and perception
tags: [cs568, lecture]
slides: lectures/lecture_02/l2.pdf
full_notes: lecture_notes/lecture_02/CS568_Lecture_02_Speech_Production_Perception.pdf
---
# CS 568 · Lecture 2 · Speech production and perception

> [!summary] In short
> - Lungs supply air, the larynx turns it into a periodic, noisy or impulsive source, the vocal tract filters it.
> - Source-filter model: $Y(z)=U(z)\,V(z)\,R(z)$. In dB the parts add. $F_0$ (source) and formants (filter) are set independently.
> - Area changes in the vocal tract cause reflections, standing waves and so formants. Uniform tube: $F_n=(2n-1)c/4L$.
> - The basilar membrane is a filter bank: high frequencies at the base, low at the apex.
> - Perception is non-linear: dB SPL, loudness $S\propto I^{0.3}$, masking, critical bands, Bark and mel scales.

## 1. The speech organs (slides 2-4)
![[cs568_lecture_02_l2_p002.jpg]]
![[cs568_lecture_02_l2_p003.jpg]]
![[cs568_lecture_02_l2_p004.jpg]]

**Notes.** Three groups of organs.

- **Lungs:** power supply, give the airflow.
- **Vocal folds (larynx):** turn the airflow into a **source**: periodic puffs (voicing), noisy turbulent flow, or an impulse (sudden release of pressure behind a closure).
- **Vocal tract:** a **filter** that boosts some frequencies and weakens others.

Parts of the vocal tract: the velum (soft palate) opens the nasal cavity for nasals such as [n], [m]. The tongue stays away from the palate for vowels and goes close to it for consonants. The lips round, spread or close ([p], [b], [m]). Slide 4 shows a side cut of the head next to an engineering model: lungs as a piston, the velum as a valve, two outputs (mouth and nose).

> [!example] The word "shop"
> "sh" is noisy (air through a narrow gap). "o" is periodic (folds vibrate). "p" is impulsive (lips close, pressure is released at once).

## 2. The vocal folds and kinds of voicing (slides 5-6)
![[cs568_lecture_02_l2_p005.jpg]]
![[cs568_lecture_02_l2_p006.jpg]]

**Notes.** The vocal folds are two masses of flesh across the larynx. The gap between them is the **glottis**. Three states:

- **Breathing:** glottis wide, air flows freely.
- **Voicing:** folds tense and together. Pressure pushes them apart, the fast air lowers the pressure (Bernoulli effect), the folds close again, and the cycle repeats by itself. The rate is the **fundamental frequency** $F_0$: about 100 to 150 Hz for adult males, 200 to 250 Hz for adult females. One cycle lasts $1/F_0$. Its perceived correlate is **pitch**.
- **Unvoiced:** like breathing but the folds are closer, so the air is turbulent (aspiration as in [h], or whispering).

Minor voicing types: hoarse (period jitters), breathy (aspiration plus voicing), creaky (folds very tense, only part oscillates), vocal fry (folds very relaxed, a small secondary pulse before each main pulse), diplophonic (secondary pulses during the closed phase, pulses alternate small and large).

## 3. The source-filter model (slides 7-8)
![[cs568_lecture_02_l2_p007.jpg]]
![[cs568_lecture_02_l2_p008.jpg]]

**Notes.** Treat every stage as a linear time-invariant (LTI) system. In the $z$-domain a chain of filters is a product:

$$Y(z)=U(z)\,P(z)\,O(z)\,R(z)=U(z)\,V(z)\,R(z)$$

$U$ is the glottal source. $P$, $O$, $R$ are the transfer functions of the pharynx, oral cavity and lips. $V=PO$ is the vocal-tract filter.

Slide 8 shows the same in frequency. The source is a line spectrum with **harmonics** at $F_0, 2F_0, 3F_0,\dots$ The vocal tract has broad resonance peaks, the **formants**. The lips give a gentle high-frequency boost, often modelled as $R(z)\approx1-z^{-1}$, with gain $|R|=2\,|\sin(\omega/2)|$ (0 at DC, 2 at half the sampling rate).

Two consequences of the product form:

1. $\log|Y|=\log|U|+\log|V|+\log|R|$. On a dB scale source and filter add. Cepstral analysis (lecture 9) uses this.
2. $F_0$ and the formants are largely independent: the same vowel can be sung on different notes.

> [!example] Adding in dB
> Source $-20$ dB, vocal tract $+15$ dB, lips $+6$ dB at one frequency. Output: $-20+15+6=1$ dB.

> [!warning] Watch out
> The letters change. In the equation $P(z)$ is the pharynx. In the figure on slide 8, $P(s)$ is the output speech and the vocal tract is called $T(s)$.

## 4. Resonance and waves on a rope (slides 9-13)
![[cs568_lecture_02_l2_p009.jpg]]
![[cs568_lecture_02_l2_p010.jpg]]
![[cs568_lecture_02_l2_p011.jpg]]
![[cs568_lecture_02_l2_p012.jpg]]
![[cs568_lecture_02_l2_p013.jpg]]

**Notes.** A mass $m$ (kg) on a spring of stiffness $k$ (N/m), pushed at the right rhythm, swings more and more. That rhythm is the **resonant frequency**:

$$f_R=\frac{1}{2\pi}\sqrt{\frac{k}{m}}$$

The Tacoma Narrows Bridge (1940) is the classic picture of resonance.

A jerk on a rope fixed to a wall travels down, reflects at the wall and comes back. With a repeated movement the forward and reflected waves interfere. At some frequencies they reinforce each other and the rope shows a fixed pattern: a **standing wave**. Points that never move are **nodes**, points of maximal motion are **antinodes**. The rope is a resonator.

What controls it: the hand sets the frequency, the rope sets the speed of travel, the boundary conditions (fixed or free ends) decide which frequencies are reinforced. For speech: the hand is the source (glottal pulses), the rope is the filter (vocal tract).

> [!example] A resonant frequency
> $m=0.1$ kg, $k=400$ N/m: $\sqrt{4000}\approx63.25$ rad/s, $f_R\approx63.25/6.283\approx10.07$ Hz. A spring four times stiffer gives $20.13$ Hz, twice as high, not four times.

## 5. Sound waves in tubes (slides 14-17)
![[cs568_lecture_02_l2_p014.jpg]]
![[cs568_lecture_02_l2_p015.jpg]]
![[cs568_lecture_02_l2_p016.jpg]]
![[cs568_lecture_02_l2_p017.jpg]]

**Notes.** Sound in a tube behaves like the wave on the rope. Air particles bunch up (compression) and spread out (rarefaction). Sound waves are longitudinal, rope waves are transverse, the maths is the same.

**Volume velocity** $u(x,t)$ is particle velocity times cross-section area (m³/s). With forward wave $u^+$ and backward wave $u^-$, speed of sound $c$, air density $\rho$, area $A$:

$$u(x,t)=u^{+}\!\left(t-\tfrac{x}{c}\right)-u^{-}\!\left(t+\tfrac{x}{c}\right),\qquad p(x,t)=\frac{\rho c}{A}\left[u^{+}\!\left(t-\tfrac{x}{c}\right)+u^{-}\!\left(t+\tfrac{x}{c}\right)\right]$$

$\rho c/A$ is the **characteristic impedance**. The waves subtract for volume velocity and add for pressure.

Chain of cause and effect: area change, impedance change, reflections, standing waves, formants. Slide 16: [a] has $F_1=750$, $F_2=1200$ Hz. [i] has 300 and 2300 Hz. [u] has 350 and 800 Hz.

A uniform tube of length $L$, closed at the glottis and open at the lips, resonates at

$$F_n=\frac{(2n-1)\,c}{4L},\qquad n=1,2,3,\dots$$

The vocal tract is modelled as a chain of short uniform tubes. At each junction part of the wave is reflected, with reflection coefficient

$$r_k=\frac{A_{k+1}-A_k}{A_{k+1}+A_k},\qquad |r_k|\le1$$

A model with $N$ sections gives $N/2$ resonances (lecture 7).

> [!example] Uniform tube
> $c=350$ m/s, $L=17.5$ cm: $c/4L=350/0.7=500$ Hz, so $F_1,F_2,F_3=500, 1500, 2500$ Hz. One formant per kHz. A child with $L=14$ cm: 625, 1875, 3125 Hz.

## 6. Anatomy of the ear (slides 18-23)
![[cs568_lecture_02_l2_p018.jpg]]
![[cs568_lecture_02_l2_p019.jpg]]
![[cs568_lecture_02_l2_p020.jpg]]
![[cs568_lecture_02_l2_p021.jpg]]
![[cs568_lecture_02_l2_p022.jpg]]
![[cs568_lecture_02_l2_p023.jpg]]

**Notes.**

| Part | Consists of | Job |
|---|---|---|
| Outer ear | pinna, auditory canal, eardrum | sound pressure to eardrum vibration |
| Middle ear | malleus, incus, stapes | carry the vibration to the oval window, matching air to fluid |
| Inner ear | cochlea | vibration to nerve spikes along the basilar membrane |

The **cochlea** is a fluid-filled coiled tube. Along it runs the **basilar membrane** (BM) with about 10,000 inner hair cells. Chain: eardrum, oval window, compression wave in the fluid, vertical vibration of the BM, hair cells bend and fire.

The BM is stiff and thin at the **base** (near the stapes) and compliant and massive at the **apex**. So travelling waves peak at different places: high frequencies near the base, low near the apex. The BM is a **filter bank**. Slide 22: the vowels [AH] and [EE] excite different places. Slide 23: block diagram from sound to pattern recognition.

> [!warning] Watch out
> "Base" sounds like "bass", but the base hears the high frequencies. Also, slide 20 says "snake-shaped spiral": the cochlea is snail-shaped.

## 7. Thresholds and loudness (slides 24-25)
![[cs568_lecture_02_l2_p024.jpg]]
![[cs568_lecture_02_l2_p025.jpg]]

**Notes.** The ear hears about 16 Hz to 18 kHz. Intensity is measured as **sound pressure level** with reference $p_0=20\ \mu\text{Pa}$:

$$L_p=20\log_{10}\frac{p}{p_0}=10\log_{10}\frac{I}{I_0}\ \text{dB}$$

The two forms agree because $I\propto p^2$. Doubling the pressure is about $+6$ dB, doubling the intensity about $+3$ dB. The hearing threshold is nearly constant over 700 Hz to 7 kHz and rises outside. Pain is around 120 to 140 dB.

**Stevens' power law** for loudness $S$ (in sones):

$$S\propto p^{0.6}\propto I^{0.3}$$

Doubling the loudness needs $I_\text{new}/I=2^{1/0.3}\approx10$, that is $+10$ dB. Equal-loudness contours are labelled in **phons**: the dB SPL of a 1 kHz tone that sounds equally loud. The ear is most sensitive around 4 kHz.

> [!warning] Watch out
> Slide 24 gives two rough ranges for where the threshold rises ("below 1 kHz or above 5 kHz" and "700 Hz to 7 kHz"). Slide 25 says "logarithmically" but the formula is a power law. Twice the intensity ($+3$ dB) is only $2^{0.3}\approx1.23$ times as loud.

## 8. Masking and critical bands (slides 26-29)
![[cs568_lecture_02_l2_p026.jpg]]
![[cs568_lecture_02_l2_p027.jpg]]
![[cs568_lecture_02_l2_p028.jpg]]
![[cs568_lecture_02_l2_p029.jpg]]

**Notes.** **Masking:** one sound raises the hearing threshold of another.

- **Frequency masking:** a lower-frequency sound masks a higher one more than the reverse.
- **Temporal masking:** pre-masking (masked sound before the masker) lasts about 5 ms. Post-masking (masked sound after) lasts 50 to 300 ms.

The **critical band** (CB) of a frequency is the smallest band around it that activates the same part of the BM. CBs are about 1.5 mm apart on the BM, so about 24 band-pass filters with bandwidth growing with frequency model the BM. Only masker energy inside the CB of the signal masks it.

Measuring a CB (slide 29): play a 2 kHz tone in band-limited noise centred at 2 kHz. Find the level at which the tone is just heard. Widen the noise and repeat. The threshold rises, then stops rising. That bandwidth is the critical bandwidth.

> [!warning] Watch out
> "Without turning up its level" on slide 29 means the level per Hz stays fixed, so a wider band has more total power.

## 9. Bark, mel and pitch (slides 30-31)
![[cs568_lecture_02_l2_p030.jpg]]
![[cs568_lecture_02_l2_p031.jpg]]

**Notes.** One **Bark** equals one critical band. With $f$ in kHz and $\tan^{-1}$ in radians:

$$z=13\tan^{-1}(0.76\,f)+3.5\tan^{-1}\!\left(\left(\frac{f}{7.5}\right)^{2}\right)$$

The **mel** scale is about linear up to 1 kHz and logarithmic above, with $f$ in Hz:

$$m=2595\log_{10}\!\left(1+\frac{f}{700}\right),\qquad f=700\left(10^{m/2595}-1\right)$$

**Pitch** is the perceived attribute related to $F_0$. $F_0$ is measured in Hz, pitch in mel. The same 100 Hz step is a smaller pitch step at high frequency: 300 to 400 Hz is 107 mel, 900 to 1000 Hz is 68 mel.

> [!example] Conversions
> 1000 Hz: $2595\log_{10}(2.4286)\approx1000$ mel, and $13(0.6499)+3.5(0.01778)\approx8.51$ Bark. 4000 Hz: $\approx2146$ mel.

> [!warning] Slide error
> Slide 30 writes the second Bark term without the square, $3.5\tan^{-1}(f/7.5\,\text{kHz})$. The squared form (Zwicker and Terhardt, 1980) is the usual one. At 1 kHz the squared form gives 8.5 Bark, the slide form 8.9 Bark.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Source-filter model | $Y(z)=U(z)V(z)R(z)$, with $V=PO$ |
| Lip radiation | $R(z)\approx1-z^{-1}$, a high-frequency boost |
| $F_0$ | rate of vocal-fold vibration. Period $=1/F_0$ |
| Harmonic / formant | lines at $kF_0$ from the source / broad peaks from the vocal tract |
| Resonant frequency | $f_R=\frac{1}{2\pi}\sqrt{k/m}$ |
| Tube waves | $u=u^+-u^-$, $p=\frac{\rho c}{A}(u^++u^-)$ |
| Uniform tube | $F_n=(2n-1)c/4L$ |
| Reflection coefficient | $r_k=(A_{k+1}-A_k)/(A_{k+1}+A_k)$. $N$ sections give $N/2$ formants |
| SPL | $L_p=20\log_{10}(p/p_0)$, $p_0=20\ \mu$Pa |
| Stevens' law | $S\propto p^{0.6}\propto I^{0.3}$. $+10$ dB sounds twice as loud |
| Phon / sone | loudness level (dB SPL of an equally loud 1 kHz tone) / loudness on a ratio scale |
| Masking | pre about 5 ms, post 50 to 300 ms, low masks high |
| Critical band | about 24 bands, 1.5 mm apart on the BM. 1 Bark = 1 CB |
| Mel | $m=2595\log_{10}(1+f/700)$ |

## Check yourself
> [!question]- 1. Name the source type of every sound in "sip" and in "ha".
> "s" noisy, "i" periodic, "p" impulsive. "h" noisy (aspiration at the glottis), "a" periodic.

> [!question]- 2. Find the first three resonances of a uniform tube of length 15 cm ($c=350$ m/s).
> $4L=0.6$ m, $F_1=350/0.6\approx583$ Hz. $F_2=3F_1=1750$ Hz, $F_3=5F_1\approx2917$ Hz.

> [!question]- 3. A spring-mass system resonates at 50 Hz. What happens if the mass is quadrupled?
> $f_R\propto1/\sqrt m$, so it halves: 25 Hz.

> [!question]- 4. A sound has an SPL of 60 dB. What is its pressure?
> $p=p_0\,10^{60/20}=20\times10^{-6}\times10^{3}=0.02$ Pa.

> [!question]- 5. A 200 Hz tone and a 3 kHz tone reach the ear. Which peaks nearer the base of the basilar membrane?
> The 3 kHz tone. The base is stiff and light, so it resonates at high frequencies.

> [!question]- 6. Convert 500 Hz to mel. Can a tone 100 ms after a loud masker be masked? 100 ms before?
> $2595\log_{10}(1.7143)\approx607$ mel. After: yes, post-masking lasts 50 to 300 ms. Before: no, pre-masking lasts only about 5 ms.

## Links
- Course index: [[CS568 Home]]
- Previous: [[CS568 L01 - Introduction]]. Next: [[CS568 L03 - Organization of speech sounds]]
- Used later: [[CS568 L07 - Linear prediction of speech]] (tube model, $N/2$ rule)
- Full step-by-step notes (PDF): [CS568_Lecture_02_Speech_Production_Perception.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_02/CS568_Lecture_02_Speech_Production_Perception.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_02/CS568_Lecture_02_Speech_Production_Perception.pdf`
- Original slides (PDF): [l2.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_02/l2.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_02/l2.pdf`

Slides not embedded: 1 (outline).
