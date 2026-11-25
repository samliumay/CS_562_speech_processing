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

> [!question]- Check (slide 2): Which of the three organ groups does a speaker mainly change to (a) speak louder, (b) say the same vowel on a higher note, (c) go from the vowel [a] to the vowel [i] on the same note?
> (a) The lungs. They are the power supply. More air pressure gives a stronger source and so a louder sound.
> (b) The vocal folds (larynx). They set how fast the puffs of air repeat. The vocal tract can stay as it is.
> (c) The vocal tract. Tongue, jaw and lips change its shape, and the shape decides how the source is "coloured". The folds can keep vibrating at the same rate.

> [!question]- Check (slide 3): The lips are closed for [m] and also for [b]. What is different in the vocal tract, and where does the air go in each case?
> The velum. For [m] the velum is in its open position, so the air and the sound leave through the nasal cavity while the mouth is closed. [m] is a nasal.
> For [b] the velum closes the nasal passage. The air cannot leave anywhere, so pressure builds up behind the lips and is released when they open.

> [!question]- Check (slide 4): Model (b) redraws the head (a) as machine parts. Say which organ each part stands for: the piston with "muscle force", the "vocal cords" valve, the "tongue hump", the "velum" flap. What does the model output when the mouth output is blocked and the velum flap is open?
> Piston with muscle force: the lungs, pressed by the breathing muscles. It is the power supply.
> Vocal cords valve: the glottis. It opens and closes and so turns the steady airflow into a source.
> Tongue hump: the tongue. It makes a narrow place between the pharynx cavity and the mouth cavity and so changes the shape of the tube.
> Velum flap: a switch that connects the nasal cavity to the tube or cuts it off.
> With the mouth blocked and the flap open, all the sound leaves through the nose output: a nasal such as [m] or [n].

## 2. The vocal folds and kinds of voicing (slides 5-6)
![[cs568_lecture_02_l2_p005.jpg]]
![[cs568_lecture_02_l2_p006.jpg]]

**Notes.** The vocal folds are two masses of flesh across the larynx. The gap between them is the **glottis**. Three states:

- **Breathing:** glottis wide, air flows freely.
- **Voicing:** folds tense and together. Pressure pushes them apart, the fast air lowers the pressure (Bernoulli effect), the folds close again, and the cycle repeats by itself. The rate is the **fundamental frequency** $F_0$: about 100 to 150 Hz for adult males, 200 to 250 Hz for adult females. One cycle lasts $1/F_0$. Its perceived correlate is **pitch**.
- **Unvoiced:** like breathing but the folds are closer, so the air is turbulent (aspiration as in [h], or whispering).

Minor voicing types: hoarse (period jitters), breathy (aspiration plus voicing), creaky (folds very tense, only part oscillates), vocal fry (folds very relaxed, a small secondary pulse before each main pulse), diplophonic (secondary pulses during the closed phase, pulses alternate small and large).

> [!info] Reading the airflow plot of slide 5 (beyond the slides)
> The blue curve is the airflow through the glottis against time. One hump is one puff of air.
> - **Open phase:** the folds are apart, air flows, the curve is above zero. The flow rises slowly and falls quickly, because the folds snap shut.
> - **Closed phase:** the folds touch, the flow is about zero, the curve is flat.
> - One open phase plus one closed phase is one period $T_0=1/F_0$.
> 
> A sound made with vibrating folds is **voiced** (ötümlü). A sound made without vibration is **unvoiced** (ötümsüz). $F_0$ is the **fundamental frequency** (temel frekans), a physical quantity in Hz. **Pitch** (perde) is how high the listener hears the sound.

> [!question]- Check (slide 5): (a) In the airflow plot, what are the folds doing where the curve is flat at the bottom, and where it is at a peak? (b) A man speaks with $F_0=125$ Hz and a woman with $F_0=250$ Hz. How long is one cycle in each case? (c) What is the difference between the unvoiced state and the voicing state?
> (a) Flat part: the folds are closed, no air passes. Peak: the folds are fully open, the airflow is largest.
> (b) $T_0=1/F_0$. Man: $1/125=0.008$ s $=8$ ms. Woman: $1/250=0.004$ s $=4$ ms. Twice the frequency gives half the period.
> (c) Voicing: the folds are tense and together, they open and close by themselves, the source is periodic. Unvoiced: the folds are close but do not vibrate, the air becomes turbulent, the source is noise without a period.

> [!question]- Check (slide 6): Both plots of slide 6 show glottal pulses with a small extra pulse. How do you tell vocal fry from diplophonia in the plots? About how far apart are the large pulses, and which $F_0$ is that?
> Vocal fry: the small pulses (one or two small bumps) sit on the rising side of each large pulse, just before it. Each cycle looks the same.
> Diplophonia: the small pulse stands alone, between two large pulses, in the time where the folds should be closed. Small and large pulses alternate.
> In both plots the large pulses are about 10 ms apart, so $F_0\approx1/0.010=100$ Hz.

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

> [!question]- Check (slide 7): In $Y(z)=U(z)\,V(z)\,R(z)$, which factor is the source and which factors are the filter? Which factor changes (a) between [a] and [i] spoken on the same note, (b) between the same vowel [a] on a low note and on a high note?
> $U(z)$ is the source (the glottal signal). $V(z)=P(z)\,O(z)$ is the vocal-tract filter (pharynx and oral cavity). $R(z)$ is the lip radiation. $Y(z)$ is the speech that comes out.
> (a) Only $V(z)$ changes. The tongue and lips give the tube another shape, so its resonances (formants) move. $U(z)$ is the same, so the harmonics stay at the same frequencies.
> (b) Only $U(z)$ changes. The folds vibrate faster, so the harmonics lie further apart. $V(z)$ is the same, so the formants stay where they are and the listener still hears [a].

> [!question]- Check (slide 8): Find the mistake: "The formants are the strongest harmonics of the source $U(s)$." Then: how many harmonics lie at or below 1 kHz for $F_0=100$ Hz and for $F_0=250$ Hz, and for which voice is the shape of $T(s)$ easier to see in $P(s)$?
> Formants are not a property of the source. They are the peaks of the filter $T(s)$, the resonances of the vocal tract. In the drawing $U(s)$ has no peaks at all, it only falls. The peaks in $P(s)$ appear because the harmonics near a resonance are amplified.
> Harmonics are at $kF_0$. For 100 Hz: $1000/100=10$ harmonics. For 250 Hz: $1000/250=4$ harmonics.
> The harmonics are the only points where $T(s)$ becomes visible in $P(s)$. With 10 lines per kHz the peaks of $T(s)$ are traced well. With 4 lines per kHz a peak can fall between two lines. So the formants of a high voice are harder to find.

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

> [!question]- Check (slides 9-10): The figure of slide 9 has a spring, a mass, a damper and a forcing function. Which two parts set $f_R$? What does the damper do, and why does the slide say the system breaks only "in the absence of other factors"? What does the bridge of slide 10 have to do with this?
> The spring (stiffness $k$) and the mass ($m$) set $f_R=\frac{1}{2\pi}\sqrt{k/m}$. A stiffer spring raises $f_R$, a heavier mass lowers it. The forcing function does not change $f_R$. It only decides at which frequency the system is pushed.
> The damper is the friction. It takes energy out in every cycle. Without it, pushing at $f_R$ adds energy in every cycle and the swing grows without limit. With it, the swing grows until the loss per cycle equals the energy added, and then stays there.
> The bridge was pushed by the wind at a rhythm near one of its own resonant frequencies, and its damping was too small. The swing grew until the bridge broke.

> [!question]- Check (slide 11): Look at the series of rope pictures on the right of slide 11. What is different about the pulse after it has hit the wall? Why can the rope not move at the wall, and what is such a point called on slide 12?
> Before the wall the pulse is a bump upwards and moves to the right. After the reflection it moves to the left and is upside down (a bump downwards).
> The end is tied to the wall, so its displacement is always zero. The forward wave and the reflected wave must cancel there, and this is why the reflected pulse is inverted.
> A point of a standing wave that never moves is a node (N).

> [!question]- Check (slide 12): Count the antinodes (A) in the three patterns of slide 12. If the top pattern appears when the hand moves at 2 Hz, at which frequencies do the other two appear? What happens at 3 Hz?
> Top: 1 antinode. Middle: 2. Bottom: 3. The nodes are at the wall, (almost) at the hand, and between the antinodes.
> Each pattern fits one more half wavelength on the same rope: $L=n\lambda/2$ (beyond the slides). The wave speed is fixed by the rope, so $f=v/\lambda$ grows in the same steps: 2 Hz, 4 Hz, 6 Hz.
> At 3 Hz no whole number of half waves fits. The reflected wave does not reinforce the forward wave, so no large fixed pattern builds up. The rope amplifies some frequencies and weakens others: it is a resonator.

> [!question]- Check (slide 13): The person moves the hand twice as fast. Does the wave travel down the rope faster? Translate both parts of your answer to speech.
> No. The hand sets only the frequency of the oscillation. The speed of travel is a property of the rope, and the rope together with its two ends decides which frequencies are reinforced.
> In speech the hand is the source: the vocal folds set $F_0$. The rope is the filter: the vocal tract (its length, its shape and its ends) sets the resonances. Raising $F_0$ does not move the formants, as changing the hand speed does not change the rope.

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

> [!info] Where the odd numbers in the tube formula come from (beyond the slides)
> The two ends of the tube are different, and this is the boundary condition of slide 15.
> - **Glottis end, closed:** the air cannot move through a wall, so the volume velocity is zero there. The pressure is largest there.
> - **Lip end, open:** the pressure cannot build up against the open air, so the pressure is about zero there. The volume velocity is largest there.
> 
> A standing wave needs a quarter of a wavelength $\lambda$ to go from a maximum to the next zero. So the tube length must be $L=\lambda/4$, or $3\lambda/4$, or $5\lambda/4$: an odd number of quarter wavelengths, $L=(2n-1)\lambda/4$. With $F=c/\lambda$ this gives $F_n=(2n-1)c/4L$.
> Compare with the rope of slide 12: both of its ends are nodes, so a whole number of half wavelengths fits, and its resonances are at 1, 2, 3 times the lowest one. The tube has unlike ends, so its resonances are at 1, 3, 5 times the lowest one.

> [!question]- Check (slide 14): In the figure of slide 14, do the air particles travel from the forcing function to the far end of the tube? What does the curve under the tube show, and what is the difference to the wave on the rope?
> No. Each particle only moves back and forth around its rest position. What travels is the pattern of dense places (compression) and thin places (rarefaction).
> The curve shows the displacement of the particles along the tube at one moment.
> In the tube the particles move along the direction of travel (longitudinal wave). On the rope the points move up and down, across the direction of travel (transverse wave). The mathematical model is the same.

> [!question]- Check (slide 15, equations): Name every symbol in $u(x,t)=u^{+}(t-x/c)-u^{-}(t+x/c)$ and in $p(x,t)=\frac{\rho c}{A}\left[u^{+}(t-x/c)+u^{-}(t+x/c)\right]$. What does the argument $t-x/c$ mean, and how large is $x/c$ for $x=17.5$ cm and $c=350$ m/s? What happens to $\rho c/A$ if the tube becomes half as wide in area?
> $u$: volume velocity (particle velocity times area). $p$: pressure. $x$: position along the tube. $t$: time. $u^{+}$: the wave that travels forward (towards the lips). $u^{-}$: the wave that travels backward. $c$: speed of sound. $\rho$: density of air. $A$: cross-section area. $\rho c/A$: characteristic impedance.
> $t-x/c$ means: what the forward wave does at position $x$ is what it did at $x=0$, but $x/c$ seconds earlier. $x/c$ is the travel time. Here $0.175/350=0.0005$ s $=0.5$ ms. The backward wave has $t+x/c$ because it travels the other way.
> The two waves carry air in opposite directions, so the flows subtract. Both raise the pressure where they meet, so the pressures add.
> $A$ is in the denominator. Half the area gives twice the impedance. A change of area is a change of impedance, and this causes a reflection (slide 16).

> [!question]- Check (slide 15, resonances): Slide 15 says the resonances are "determined by the length of the tube and the speed of sound". For a uniform tube with $L=17.5$ cm and $c=350$ m/s, what is the wavelength at $F_1=500$ Hz, and how does it compare with $L$? What happens to all formants if the tube is 10% longer?
> $\lambda=c/F_1=350/500=0.7$ m $=70$ cm. That is $4L$: the tube holds a quarter of a wavelength at its first resonance.
> $F_n=(2n-1)c/4L$ has $L$ in the denominator, so every formant is divided by 1.1. $F_1$: $500/1.1\approx455$ Hz. $F_2$: $1500/1.1\approx1364$ Hz. A longer vocal tract has lower formants. This is why adults have lower formants than children.

> [!question]- Check (slide 16): Each row of slide 16 shows a vowel in three forms. What are the three panels? Compare $F_1$ and $F_2$ of [a], [i], [u] with the uniform tube (500 and 1500 Hz). Which vowel has $F_1$ and $F_2$ furthest apart? What stays the same in all three rows if the speaker keeps the same note?
> Left: the shape of the vocal tract. Middle: the area function, the cross-section area against the distance from the larynx. Right: the transfer function of the tube in dB against frequency. Its peaks are the formants.
> [a]: 750 and 1200 Hz, so $F_1$ is higher and $F_2$ lower than in the uniform tube. [i]: 300 and 2300 Hz, $F_1$ lower and $F_2$ higher. [u]: 350 and 800 Hz, both lower.
> Distance $F_2-F_1$: [a] 450 Hz, [i] 2000 Hz, [u] 450 Hz. [i] has them furthest apart.
> The source stays the same: the same $F_0$ and the same harmonics. Only the area function changes, so only the filter changes. The area function decides the impedance pattern, and the impedance pattern decides the resonances.

> [!question]- Check (slide 17): (a) How many tube sections are needed at least to model four formants? (b) Compute the reflection coefficient at a junction from $A_k=1\ \text{cm}^2$ to $A_{k+1}=4\ \text{cm}^2$, and for two equal areas. (c) Why is the uniform tube not enough to model a real vowel?
> (a) $N$ sections give $N/2$ resonances, so $N=2\cdot4=8$ sections.
> (b) $r_k=(A_{k+1}-A_k)/(A_{k+1}+A_k)=(4-1)/(4+1)=3/5=0.6$. For equal areas $r_k=0$: no change of impedance, no reflection, the wave passes through.
> (c) A uniform tube has one fixed pattern, $F_n=(2n-1)c/4L$, which fits only a neutral vowel. Real vowels have an area that changes along the tract (slide 16). A chain of short tubes with different areas copies this area function, and each junction adds a reflection.

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

> [!info] Why the ear needs a middle ear (beyond the slides)
> Sound arrives in air, but the cochlea is filled with fluid. Fluid is much harder to move than air: its impedance is much higher. As at a tube junction with very different areas (slide 16), most of the sound would be reflected at the border and would not enter the fluid.
> The middle ear reduces this loss in two ways. The eardrum is much larger than the oval window, so the force collected on a large area is put on a small area. The three bones also work as a small lever. Typical textbook values are an area ratio of about 17 and a lever factor of about 1.3. Pressure is force per area, so the pressure grows by $17\times1.3=22.1$, which is $20\log_{10}22.1\approx27$ dB.
> This is called impedance matching (empedans uyumlama).

> [!question]- Check (slides 18-19): Follow a sound from the air to the brain. Name the stations in order (use the labels of slide 19) and say in which form the signal exists at each one. What would be lost if the eardrum sat directly on the cochlea, without the three bones?
> Pinna and auditory canal: a pressure wave in air. Eardrum: a mechanical vibration of a membrane. Malleus, incus, stapes: a mechanical vibration of bones, passed on to the oval window. Cochlea: a wave in the fluid and a vibration of the basilar membrane. Hair cells and nerve: spike trains (neuron firings) that go to the brain.
> Outer ear: pressure to vibration. Middle ear: transport of the vibration. Inner ear: vibration to spikes.
> Without the bones there is no impedance matching between air and fluid. Most of the sound energy would be reflected and hearing would be much less sensitive.

> [!question]- Check (slide 20): Slide 20 says the basilar membrane is stiff and thin at the basal end and compliant and massive at the apex. Use $f_R=\frac{1}{2\pi}\sqrt{k/m}$ of slide 9 to explain why this makes the membrane a filter bank. Also: is it the air that bends the hair cells?
> Stiff means large $k$, thin means small $m$, so $k/m$ is large and the resonant frequency is high. This is the base. Compliant means small $k$, massive means large $m$, so the resonant frequency is low. This is the apex.
> Each place along the membrane answers best to its own frequency. Many places with different frequencies next to each other are a bank of band-pass filters. The hair cells at one place report how much energy the sound has near that frequency. This is place coding (yer kodlaması).
> No. The air stops at the eardrum. The bones move the oval window, this makes a wave in the cochlear fluid, the fluid moves the basilar membrane, and the moving membrane bends the hair cells.

> [!question]- Check (slide 21): On the uncoiled cochlea the labels 1600, 800, 400, 200, 100, 50, 25 Hz are about equally far apart. What does this say about how frequency is mapped to place? In the upper-right plot, which side of each tuning curve is steeper?
> From one label to the next the frequency is halved, that is one octave. Equal distances on the membrane are equal frequency ratios, not equal differences in Hz. The map is about logarithmic. The 100 Hz between 100 and 200 Hz get as much membrane as the 800 Hz between 800 and 1600 Hz.
> So the ear resolves low frequencies finely in Hz and high frequencies coarsely. The critical bands and the mel and Bark scales of slides 28 to 30 describe the same fact.
> Each curve rises slowly on the low-frequency side and falls quickly on the high-frequency side. A place on the membrane still answers to tones somewhat below its best frequency, but hardly to tones above it.

> [!question]- Check (slide 22): The two spectra of slide 22 are [AH] and [EE]. According to the drawing of the membrane below them, how does the ear tell the two vowels apart? Would it still work if both vowels were sung on the same note? Which peak of [EE] lies nearest the apex?
> Each vowel has its own formant peaks: [AH] has two peaks close together near 1000 Hz and a smaller one near 2500 Hz, [EE] has one very low peak and two peaks above 2000 Hz. Each peak excites the place on the membrane that belongs to its frequency. So each vowel gives its own pattern of excited places, and the nerve fibres from these places fire.
> Yes. The note is $F_0$, a property of the source. The pattern of places comes from the formants, a property of the filter.
> The lowest peak of [EE] (a few hundred Hz). Low frequencies are at the apex.

> [!question]- Check (slide 23): In the block diagram of slide 23, what does the cochlea box with "LO ... HI" do, and what runs on the parallel lines behind it? Which blocks of a speech recogniser play the same roles?
> The cochlea box splits the sound by frequency, from low to high: it is the filter bank. Each parallel line is one frequency channel. The short strokes on the lines are the spikes of the auditory nerve in that channel.
> Central processing builds several representations from these channels, and the last block recognises patterns in them.
> A recogniser has the same chain: a spectral analysis of the signal (often a filter bank on a mel scale), feature vectors, and then a pattern recogniser such as an HMM.

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

> [!info] Four quantities that are easy to mix up (beyond the slides)
> - **Intensity** $I$ (şiddet): physical, sound power per area. It does not depend on the listener.
> - **Sound pressure level** in dB SPL: the same physical quantity on a logarithmic scale, relative to $p_0$.
> - **Loudness level** in phon: perceptual. It says which 1 kHz tone sounds equally loud. Two tones on the same contour of slide 25 have the same number of phons but different dB SPL.
> - **Loudness** $S$ in sone (gürlük): perceptual, on a ratio scale. Twice the sones sounds twice as loud.
> 
> The same split exists for frequency: $F_0$ in Hz is physical, pitch in mel is perceptual (slide 31).

> [!question]- Check (slide 24): (a) Is 0 dB SPL silence? Which pressure is it? (b) In the figure the threshold of hearing goes below 0 dB near 3 to 4 kHz. What does a negative level mean? (c) A 100 Hz tone and a 3 kHz tone both have 20 dB SPL. Use the threshold curve to say which one is heard.
> (a) No. $L_p=20\log_{10}(p/p_0)=0$ means $p=p_0=20\ \mu\text{Pa}$. It is the reference pressure, about the weakest sound a person hears at 1 kHz.
> (b) A negative level means $p<p_0$. For example $-5$ dB is $p=20\cdot10^{-5/20}\approx11.2\ \mu\text{Pa}$. The ear is so sensitive near 3 to 4 kHz that it hears pressures below the reference.
> (c) In the figure of slide 24 the threshold curve at 100 Hz is at about 40 dB, above 20 dB, so the 100 Hz tone is not heard. At 3 kHz the curve is a little below 0 dB, so the 3 kHz tone is heard. The same SPL does not mean the same audibility.
> The figure of slide 25 comes from other measurements: its lowest curve (MAF, the threshold) is at about 25 dB at 100 Hz. The two figures differ by about 15 dB there, but the answer is the same: 20 dB is below the threshold at 100 Hz in both.

> [!question]- Check (slide 25): (a) Along one contour of slide 25, what is constant and what changes? Why do the contours rise steeply on the left? (b) The level of a tone is raised by 20 dB. By which factor do the pressure, the intensity and the loudness grow?
> (a) Along one contour the loudness level (phon) is constant: all tones on it sound equally loud. The SPL changes. On the left (low frequencies) the ear is less sensitive, so a low tone needs many more dB SPL to sound as loud as the 1 kHz tone. The contours are lowest near 4 kHz, where the ear is most sensitive.
> (b) $+20$ dB: pressure $\times10^{20/20}=10$, intensity $\times10^{20/10}=100$. Loudness: $S\propto I^{0.3}$, so $\times100^{0.3}\approx3.98$, about 4 times. The same from pressure: $10^{0.6}\approx3.98$.
> So 100 times the intensity is only about 4 times as loud. Loudness is strongly compressed.

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

> [!question]- Check (slide 26): Slide 26 says masking prevents us from treating perception "as a summation of responses". What would a system that sums responses do with a quiet tone B played together with a loud tone A, and what does the ear do?
> In a system that sums (a linear system) the response to A plus B is the response to A plus the response to B. The part that belongs to B would be the same as when B is played alone, so B could always be found.
> In the ear A raises the threshold for B. B is audible alone but can be inaudible together with A. The response to B depends on what else is present, so the ear is non-linear.

> [!question]- Check (slide 27): (a) In the left figure two grey tones have the same height, but one is inaudible and one is audible. Why? Name two changes that would make the inaudible tone audible. (b) In the right figure, compare the masked area before and after the masking tone.
> (a) The shaded area is the raised threshold around the masking tone. The first grey tone is close to the masker in frequency and lies below the edge of this area, so it is inaudible. The second one is further away, where the area is already low, and sticks out of it.
> To make the first tone audible: raise its level above the edge of the area, or move it further away in frequency, or make the masker weaker.
> (b) The masked area starts only a short time before the masker (pre-masking, about 5 ms) and stays long after it has ended (post-masking, 50 to 300 ms). A tone just before or just after the masker is inaudible. A tone long before or long after is audible.

> [!question]- Check (slide 28): (a) In the figure the masker (the triangle) is wider than the critical band $\Delta f_c$ around the tone at $f_c$. Which part of it masks the tone? (b) The bands on the frequency axis get wider to the right, but the slide says each band is about 1.5 mm of basilar membrane. How do the two fit together?
> (a) Only the dark part inside $\Delta f_c$. The amount of masking equals the masker energy inside the critical band of the tone. The light parts outside excite other places on the membrane and do not mask this tone.
> (b) The membrane maps frequency about logarithmically (slide 21). The same length of membrane covers few Hz at low frequencies and many Hz at high frequencies. So bands of equal length on the membrane are narrow in Hz at the low end and wide in Hz at the high end. About 24 such bands cover the hearing range.

> [!question]- Check (slide 29): In the experiment of slide 29 the noise has a fixed level per Hz. With noise bandwidths of 50, 100, 200, 400 and 800 Hz the tone is just heard at 40, 43, 46, 48 and 48 dB (invented numbers, not from the slide). Why does the threshold first rise by 3 dB per step? Estimate the critical bandwidth.
> Each step doubles the bandwidth. With a fixed level per Hz, the noise power inside the band doubles, and $10\log_{10}2\approx3$ dB. As long as all the noise lies inside the critical band, all of it masks the tone, so the threshold rises by 3 dB.
> From 400 Hz on the threshold stays at 48 dB. The extra noise is outside the critical band and does not mask.
> The total rise from 200 Hz to the final value is $48-46=2$ dB, so the critical band is wider than 200 Hz by the factor $10^{2/10}\approx1.585$: about $200\cdot1.585\approx317$ Hz. The critical bandwidth is about 300 Hz, between 200 and 400 Hz.

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

> [!question]- Check (slide 30): (a) In $m=2595\log_{10}(1+f/700)$, what do the two constants do? (b) The lower figure of slide 30 shows triangular filters up to 4000 Hz. Why are they narrow and close together on the left and wide on the right? (c) What do the Bark and the mel scale have in common, and what is one difference?
> (a) 700 Hz sets where the curve bends. For $f$ much smaller than 700 Hz, $\log_{10}(1+f/700)$ grows about in proportion to $f$, so the scale is about linear. For $f$ much larger than 700 Hz it grows like $\log_{10}f$. The factor 2595 fixes the size of the unit: it makes 1000 Hz equal to 1000 mel ($2595\log_{10}(2.4286)\approx1000$).
> (b) The filters are equally spaced and equally wide on the mel scale. Drawn on a Hz axis, equal mel steps are small in Hz at low frequencies and large in Hz at high frequencies. This copies the critical bands of the ear, which also get wider with frequency.
> (c) Both are perceptual frequency scales that grow more and more slowly with frequency (the two curves in the upper figure have the same kind of shape). The Bark scale counts critical bands, with one Bark for one band. The mel scale measures perceived pitch.

> [!question]- Check (slide 31): (a) The red lines in the figure mark the steps 300 to 400 Hz and 900 to 1000 Hz. What do they show? Compute the size of the step 2000 to 2100 Hz in mel. (b) Find the mistake: "If $F_0$ is doubled, the pitch in mel is doubled."
> (a) The two steps have the same width in Hz, but the step at 300 to 400 Hz covers a larger piece of the mel axis (107 mel) than the step at 900 to 1000 Hz (68 mel). The frequency axis of the figure is logarithmic, so equal steps in Hz look narrower on the right.
> $m(2000)=2595\log_{10}(1+2000/700)\approx1521.4$ and $m(2100)=2595\log_{10}(1+2100/700)=2595\log_{10}4\approx1562.3$. The step is about 41 mel, smaller again.
> (b) The mel scale is not proportional to frequency. 200 Hz is about 283 mel and 400 Hz is about 509 mel: the ratio is 1.8, not 2. 2000 Hz is about 1521 mel and 4000 Hz about 2146 mel: the ratio is only 1.41. $F_0$ is the physical quantity, pitch is what is heard, and the relation between them is non-linear.

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
