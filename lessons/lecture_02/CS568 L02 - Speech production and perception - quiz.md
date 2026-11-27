---
course: CS 568 Speech Processing
lesson: CS568 L02 - Speech production and perception
type: quiz
questions: 29
---

# CS568 L02 - Speech production and perception - quiz

Lesson note: [[CS568 L02 - Speech production and perception]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. The speech organs (slides 2-4)

### Q1 (slide 2)

The speech organs form three groups: the lungs (power supply), the vocal folds in the larynx (turn the airflow into a source) and the vocal tract (tongue, jaw, lips; a filter). To speak louder, a speaker mainly uses more air pressure from the lungs. Which group does the speaker mainly change to (b) say the same vowel on a higher note, and (c) go from the vowel [a] to the vowel [i] on the same note?

- A) (b) the vocal tract, (c) the vocal folds
- B) (b) the vocal folds, (c) the vocal folds
- C) (b) the vocal tract, (c) the vocal tract
- D) (b) the vocal folds, (c) the vocal tract

> [!success]- Answer
> **D.** (b) The note is set by how fast the folds open and close. The vocal tract can keep its shape.
> (c) A new vowel needs a new shape of the tube: tongue, jaw and lips move. The folds can keep vibrating at the same rate.
> - A: The two roles are swapped. The tract shape colours the sound, the folds set the rate of the puffs.
> - B: (b) is right, but the folds only set the rate. Two vowels on the same note have the same rate and differ in the tract shape.
> - C: (c) is right, but a new tract shape moves the resonances, not the rate of the puffs. The note comes from the folds.

### Q2 (slide 3)

The lips are closed for the sound [m] and also for the sound [b]. The velum (soft palate) can open or close the way from the throat to the nasal cavity. Which statement is correct?

- A) For [m] the velum is closed, so the air leaves through the nose. For [b] it is open, so pressure builds up behind the lips.
- B) For [m] the velum is open, so the air leaves through the nose. For [b] it is closed, so pressure builds up behind the lips.
- C) The velum is closed for both. The folds vibrate for [m] but not for [b], so for [b] pressure builds up behind the lips.
- D) For [m] the velum is open, so the air leaves through the nose. For [b] it is open too, and the air leaves by the nose until the lips open.

> [!success]- Answer
> **B.** An open velum connects the nasal cavity to the tube. With closed lips the air and the sound leave through the nose: [m] is a nasal. For [b] the velum closes the nose, the air has no way out, pressure builds up behind the lips and is released when they open.
> - A: The velum positions are swapped. A closed velum cuts off the nose, so air cannot leave there.
> - C: The folds vibrate for [b] too ([b] is voiced). And with the velum and the lips both closed, [m] could not leave the head at all.
> - D: With the velum open the air escapes through the nose and no pressure builds up. That gives [m], not [b].

### Q3 (slide 4)

![[cs568_lecture_02_l2_p004.jpg]]

Model (b) redraws the head (a) as machine parts. In the model the mouth output is blocked and the velum flap is open. The "vocal cords" valve opens and closes. What does the model output?

- A) No sound at all, because the tongue hump closes the tube until the mouth opens again.
- B) Pressure builds up behind the closed mouth and is released when it opens, as in [b].
- C) Sound leaves through both outputs at the same time, as in a nasalised vowel.
- D) All the sound leaves through the nose output, as in a nasal sound such as [m] or [n].

> [!success]- Answer
> **D.** The piston is the lungs (power supply), the valve is the glottis (it turns the airflow into a source), the tongue hump narrows the tube between pharynx and mouth, and the velum flap connects or cuts off the nasal cavity.
> With the flap open the nasal cavity is connected. With the mouth blocked, the nose output is the only way out: a nasal such as [m] or [n].
> - A: The tongue hump only narrows the tube between the two cavities. It does not close it, and the way to the nose is open anyway.
> - B: Pressure builds up only when the flap is closed too. With the flap open the air escapes through the nose.
> - C: The mouth output is blocked, so nothing leaves there. Both outputs work only with an open mouth and an open flap.

## 2. The vocal folds and kinds of voicing (slides 5-6)

### Q4 (slide 5)

![[cs568_lecture_02_l2_p005.jpg]]

The blue curve is the airflow through the glottis against time. $F_0$ is the rate of vibration of the folds. Which statement is correct?

- A) Where the curve is flat at the bottom, the folds are closed and no air passes.
- B) For $F_0=250$ Hz one cycle lasts 8 ms, twice as long as for $F_0=125$ Hz.
- C) In the unvoiced state the folds vibrate too, but too slowly to give a period.
- D) Where the curve has a peak, the folds are closed and the pressure behind is largest.

> [!success]- Answer
> **A.** At the flat bottom the airflow is about zero: the folds touch and no air passes. At a peak the folds are fully open and the flow is largest.
> The period is $T_0=1/F_0$: $1/125=0.008$ s $=8$ ms and $1/250=0.004$ s $=4$ ms.
> - B: The two periods are swapped. A higher $F_0$ means more cycles per second, so each cycle is shorter: 4 ms at 250 Hz.
> - C: In the unvoiced state the folds do not vibrate. They are close together and the air becomes turbulent: noise without a period.
> - D: The peak is the largest airflow, so the folds are fully open there. Closed folds give the flat part.

### Q5 (slide 6)

![[cs568_lecture_02_l2_p006.jpg]]

Both plots show glottal pulses with small extra pulses. Which statement is correct?

- A) In vocal fry a small pulse stands alone between two large ones, so small and large pulses alternate.
- B) The pulses of the diplophonia plot are about 5 ms apart, so its $F_0$ is about 200 Hz.
- C) The large pulses are about 10 ms apart in both plots, so $F_0$ is about 100 Hz in both.
- D) In diplophonia the small bumps sit on the rising side, just before each large pulse.

> [!success]- Answer
> **C.** In both plots the large pulses are at about 14, 24 and 34 ms: 10 ms apart, so $F_0\approx1/0.010=100$ Hz.
> Vocal fry: small bumps on the rising side of each large pulse, every cycle the same. Diplophonia: the small pulse stands alone in the closed phase, so small and large pulses alternate.
> - A: This describes diplophonia, not vocal fry.
> - B: 5 ms is the distance from a small pulse to the next large one. One period goes from a large pulse to the next large pulse.
> - D: This describes vocal fry, not diplophonia.

## 3. The source-filter model (slides 7-8)

### Q6 (slide 7)

Speech is modelled as $Y(z)=U(z)\,V(z)\,R(z)$. $U$ is the glottal source, $V$ the vocal-tract filter, $R$ the lip radiation, $Y$ the speech that comes out. Which statement is correct?

- A) From [a] to [i] on the same note only $U(z)$ changes, because the vowel is made by the source.
- B) From a low [a] to a high [a] both $U(z)$ and $V(z)$ have to change, because the formants follow $F_0$.
- C) From [a] to [i] on the same note the harmonics move to new frequencies, and the formants stay.
- D) From [a] to [i] only $V(z)$ changes, and from a low [a] to a high [a] only $U(z)$ changes.

> [!success]- Answer
> **D.** A new vowel is a new tube shape: the resonances (formants) of $V$ move, and $U$ stays. A new note is a faster fold vibration: the harmonics of $U$ lie further apart, and $V$ stays, so the listener still hears [a].
> - A: The vowel is not a property of the source. Two vowels on the same note have the same $U$.
> - B: The formants belong to the tract. The same vowel keeps the same tube shape, so $V$ does not have to change.
> - C: It is the other way round. On the same note the harmonics stay, and the formants move.

### Q7 (slide 8)

In the frequency picture of slide 8, the source spectrum $U(s)$ is a set of lines (harmonics) at $F_0, 2F_0, 3F_0,\dots$ The vocal-tract filter $T(s)$ is a smooth curve with peaks. The output is $P(s)=U(s)\,T(s)\,R(s)$. Which statement is correct?

- A) For $F_0=100$ Hz, 10 harmonics lie at or below 1 kHz, and they trace $T(s)$ better than a 250 Hz voice.
- B) The formants are the strongest harmonics of $U(s)$, so a higher $F_0$ moves the formants up.
- C) For $F_0=250$ Hz, 4 harmonics lie at or below 1 kHz, and each is stronger, so $T(s)$ is easier to see.
- D) For $F_0=250$ Hz, only 3 harmonics lie at or below 1 kHz, so a peak of $T(s)$ can fall between them.

> [!success]- Answer
> **A.** Harmonics are at $kF_0$: $1000/100=10$ and $1000/250=4$. $T(s)$ shows in $P(s)$ only at the harmonics. 10 lines per kHz trace its peaks well, 4 lines per kHz can miss a peak. So the formants of a low voice are easier to see.
> - B: The formants are the peaks of the filter $T(s)$. $U(s)$ has no peaks, it only falls.
> - C: The count is right, but the shape of $T(s)$ is sampled only at the lines. Fewer lines give a coarser picture, however strong they are.
> - D: 250, 500, 750 and 1000 Hz are 4 harmonics. The line at exactly 1 kHz is "at or below 1 kHz".

## 4. Resonance and waves on a rope (slides 9-13)

### Q8 (slides 9-10)

A spring (stiffness $k$) holds a mass $m$. A damper and a forcing function also act on it. The resonant frequency is $f_R=\frac{1}{2\pi}\sqrt{k/m}$. Which statement is correct?

- A) The forcing function sets $f_R$: the system resonates at whatever rhythm it is pushed with.
- B) The damper removes energy in every cycle, so the swing stops growing at some size.
- C) A stiffer spring lowers $f_R$, and a heavier mass raises it, since $f_R$ grows with $m$.
- D) The Tacoma Narrows bridge broke because the wind pushed it far above its $f_R$.

> [!success]- Answer
> **B.** The damper is friction. Without it, pushing at $f_R$ adds energy every cycle and the swing grows without limit. With it, the swing grows until the loss per cycle equals the energy added.
> - A: $f_R$ contains only $k$ and $m$. The forcing function only decides at which frequency the system is pushed.
> - C: Both directions are reversed. $k$ is in the numerator, so a stiffer spring raises $f_R$. $m$ is in the denominator, so a heavier mass lowers it.
> - D: The wind pushed the bridge near one of its own resonant frequencies, with too little damping. Far from $f_R$ the swing stays small.

### Q9 (slide 11)

![[cs568_lecture_02_l2_p011.jpg]]

The rope is tied to the wall. On the right, a pulse travels to the wall and comes back upside down. Which statement is correct?

- A) The pulse turns over because any pulse that changes its direction turns over, however the end is held.
- B) In a standing wave on this rope, a point that never moves at all is called an antinode.
- C) At the wall the rope moves the most, because the forward and the reflected wave add up there.
- D) The end cannot move, so the two waves must cancel there, and the pulse comes back inverted.

> [!success]- Answer
> **D.** The end is tied, so its displacement is always zero. The incoming pulse and the reflected one must add to zero there, so the reflected pulse is upside down. A point that never moves is a node (N on slide 12).
> - A: The inversion comes from the fixed end. At a free end the pulse comes back upright.
> - B: An antinode (A) is a point of largest motion. A point that never moves is a node.
> - C: The wall holds the rope still. The two waves cancel there, so the wall is a node.

### Q10 (slide 12)

![[cs568_lecture_02_l2_p012.jpg]]

The hand moves the rope up and down, and the other end is tied to the wall. The top pattern appears when the hand moves at 2 Hz. Which statement is correct?

- A) The middle and the bottom pattern appear at 6 Hz and 10 Hz, the odd multiples of 2 Hz.
- B) At 3 Hz a fixed pattern with one and a half loops appears, between the top and the middle one.
- C) The middle and the bottom pattern appear at 4 Hz and 6 Hz, the whole multiples of 2 Hz.
- D) The middle and the bottom pattern appear at 4 Hz and 8 Hz: each new loop doubles the frequency.

> [!success]- Answer
> **C.** The patterns have 1, 2 and 3 antinodes. Each one fits one more half wavelength on the same rope, $L=n\lambda/2$. The wave speed is fixed by the rope, so $f=v/\lambda$ grows in equal steps: 2, 4, 6 Hz.
> At 3 Hz no whole number of half waves fits, and no large fixed pattern builds up. The rope is a resonator.
> - A: Odd multiples hold for a tube closed at one end and open at the other. This rope has a node at both ends, so all whole multiples appear.
> - B: One and a half loops would leave the tied end moving. The end must be a node, so this pattern cannot stand.
> - D: The frequency grows with the number of loops, $f_n=n\cdot2$ Hz. Three loops give 6 Hz, not 8 Hz.

### Q11 (slide 13)

A person moves the hand that holds the rope twice as fast as before. In speech the hand stands for the vocal folds and the rope for the vocal tract. Which statement is correct?

- A) The wave travels at the same speed. In speech, raising $F_0$ leaves the formants where they are.
- B) The wave travels twice as fast. In speech, a higher $F_0$ makes the waves in the tract travel faster.
- C) The speed is the same, but the rope now reinforces new frequencies. In speech, the formants follow $F_0$.
- D) The speed is the same. In speech, the folds set both $F_0$ and the formants, as the hand sets the pattern.

> [!success]- Answer
> **A.** The hand sets only the frequency. The speed of travel is a property of the rope, and the rope with its two ends decides which frequencies are reinforced.
> In speech the folds set $F_0$, and the tract (its length, shape and ends) sets the resonances. A faster source does not change the resonator.
> - B: A faster hand gives a shorter wavelength at the same speed. The speed of sound in the tract does not depend on $F_0$ either.
> - C: Which frequencies are reinforced depends on the rope and its ends, not on the hand. So the formants do not follow $F_0$.
> - D: The hand only drives the rope. The pattern the rope can hold is set by the rope, as the formants are set by the tract.

## 5. Sound waves in tubes (slides 14-17)

### Q12 (slide 14)

A forcing function at one end of a tube makes a sound wave in the air inside it. Which statement is correct?

- A) The air particles travel from the forcing function all the way to the far end of the tube.
- B) The tube wave is transverse: the air particles move across the tube, like the rope moves.
- C) The air particles stay still, and only the pressure moves from the forcing function to the far end.
- D) Each air particle moves back and forth around its rest place, along the direction of travel.

> [!success]- Answer
> **D.** Each particle only oscillates around its rest position, along the direction in which the wave travels: a longitudinal wave. What travels is the pattern of dense (compression) and thin (rarefaction) places.
> - A: A wave carries a pattern, not the air itself. The particles stay near their rest positions.
> - B: On the rope the points move across the direction of travel (transverse). In the tube they move along it (longitudinal).
> - C: The particles do move: each one oscillates around its rest place. Only the pattern of compression and rarefaction travels.

### Q13 (slide 15, equations)

In a tube, the volume velocity is $u(x,t)=u^{+}(t-x/c)-u^{-}(t+x/c)$ and the pressure is $p(x,t)=\frac{\rho c}{A}\left[u^{+}(t-x/c)+u^{-}(t+x/c)\right]$. $x$ is the position, $t$ the time, $u^{+}$ and $u^{-}$ the forward and backward waves, $c$ the speed of sound, $\rho$ the density of air, $A$ the cross-section area. How large is $x/c$ for $x=17.5$ cm and $c=350$ m/s, and what happens to $\rho c/A$ if the area is halved?

- A) $x/c=50$ ms. $\rho c/A$ doubles.
- B) $x/c=0.5$ ms. $\rho c/A$ halves.
- C) $x/c=0.5$ ms. $\rho c/A$ doubles.
- D) $x/c=50$ ms. $\rho c/A$ halves.

> [!success]- Answer
> **C.** $x/c$ is the travel time from $0$ to $x$: $0.175/350=0.0005$ s $=0.5$ ms. So $u^{+}(t-x/c)$ at $x$ is what the forward wave did at $x=0$, 0.5 ms earlier.
> $A$ is in the denominator of the characteristic impedance $\rho c/A$. Half the area gives twice the impedance. A change of impedance causes a reflection.
> - A: $17.5/350=0.05$ s uses the length in cm as if it were metres.
> - B: The time is right, but $A$ is below the fraction bar. A smaller area gives a larger impedance.
> - D: Two slips: cm used as metres, and the impedance moved the wrong way.

### Q14 (slide 15, resonances)

A uniform tube of length $L=17.5$ cm, closed at the glottis and open at the lips, has resonances $F_n=(2n-1)c/(4L)$ with $c=350$ m/s, so $F_1=500$ Hz and $F_2=1500$ Hz. What is the wavelength at $F_1$, and what are $F_1$ and $F_2$ if the tube is 10% longer?

- A) $\lambda=35$ cm $=2L$. The longer tube has $F_1\approx455$ Hz and $F_2\approx1364$ Hz.
- B) $\lambda=70$ cm $=4L$. The longer tube has $F_1=550$ Hz and $F_2=1650$ Hz.
- C) $\lambda=35$ cm $=2L$. The longer tube has $F_1=550$ Hz and $F_2=1650$ Hz.
- D) $\lambda=70$ cm $=4L$. The longer tube has $F_1\approx455$ Hz and $F_2\approx1364$ Hz.

> [!success]- Answer
> **D.** $\lambda=c/F_1=350/500=0.7$ m $=70$ cm $=4L$: the tube holds a quarter wavelength at its first resonance.
> $L$ is in the denominator, so every formant is divided by 1.1: $500/1.1\approx455$ Hz, $1500/1.1\approx1364$ Hz. A longer tract has lower formants.
> - A: The formants are right, but $\lambda=c/(2F_1)$ is a half-wave relation. $\lambda=c/F$ gives 70 cm.
> - B: The wavelength is right, but $500\times1.1$ multiplies by the length. With $L$ in the denominator the formants go down.
> - C: Two slips: the half-wave wavelength, and the formants multiplied by 1.1.

### Q15 (slide 16)

![[cs568_lecture_02_l2_p016.jpg]]

Each row shows a vowel: the tract shape, a middle panel, and the transfer function in dB. The marked formants are [a] 750 and 1200 Hz, [i] 300 and 2300 Hz, [u] 350 and 800 Hz. Which statement is correct?

- A) [a] has $F_1$ and $F_2$ furthest apart, because its $F_1$ is the highest of the three.
- B) [i] has $F_1$ and $F_2$ furthest apart: 2000 Hz, against 450 Hz for [a] and [u].
- C) On the same note the three transfer functions are equal, since the tract length is equal.
- D) The middle panel shows the transfer function, and the formants are the peaks in it.

> [!success]- Answer
> **B.** $F_2-F_1$: [a] $1200-750=450$ Hz, [i] $2300-300=2000$ Hz, [u] $800-350=450$ Hz.
> On the same note the source stays the same ($F_0$ and the harmonics). Only the area function changes, so only the filter changes.
> - A: The highest $F_1$ is not the largest gap. [a] has a gap of only 450 Hz.
> - C: The three transfer functions have different peaks. Length alone fixes the formants only for a uniform tube. The area along the tube also matters.
> - D: The middle panel is the area function: cross-section area against distance from the larynx. The transfer function is on the right.

### Q16 (slide 17)

The vocal tract is modelled as a chain of short uniform tubes. A model with $N$ sections gives $N/2$ resonances. At a junction the reflection coefficient is $r_k=(A_{k+1}-A_k)/(A_{k+1}+A_k)$. How many sections are needed at least for four formants, and what is $r_k$ from $A_k=1\ \text{cm}^2$ to $A_{k+1}=4\ \text{cm}^2$?

- A) 8 sections. $r_k=0.6$.
- B) 4 sections. $r_k=0.6$.
- C) 8 sections. $r_k=-0.6$.
- D) 4 sections. $r_k=-0.6$.

> [!success]- Answer
> **A.** $N/2=4$ gives $N=8$. $r_k=(4-1)/(4+1)=3/5=0.6$.
> For two equal areas $r_k=0$: no change of impedance, no reflection. A uniform tube has only the fixed pattern $(2n-1)c/(4L)$, so real vowels need several sections.
> - B: One section per formant. The rule is two sections per formant.
> - C: $(1-4)/(1+4)$ swaps $A_k$ and $A_{k+1}$.
> - D: Two slips: one section per formant, and the two areas swapped.

## 6. Anatomy of the ear (slides 18-23)

### Q17 (slides 18-19)

A sound goes from the air outside the head to the brain. Which statement is correct?

- A) The three bones of the middle ear split the sound into frequencies before the cochlea.
- B) Without the three bones most of the sound would be reflected where air meets fluid.
- C) The eardrum turns the pressure wave in the air directly into spikes on the nerve.
- D) The order is: eardrum, wave in the cochlear fluid, the three bones, then the nerve.

> [!success]- Answer
> **B.** Air (pinna, canal), eardrum vibration, vibration of malleus, incus and stapes, wave in the cochlear fluid and basilar membrane vibration, nerve spikes from the hair cells.
> Fluid has a much higher impedance than air. The bones match the two (impedance matching). Without them most of the energy would be reflected and hearing would be much less sensitive.
> - A: The bones pass the whole vibration on. The frequency split happens on the basilar membrane in the cochlea.
> - C: The eardrum only vibrates. Spikes start at the hair cells in the cochlea.
> - D: The bones come before the cochlea. The stapes pushes the oval window, and this makes the wave in the fluid.

### Q18 (slide 20)

The basilar membrane is stiff and thin at the base and compliant (soft) and massive at the apex. A spring-mass system resonates at $f_R=\frac{1}{2\pi}\sqrt{k/m}$. Which statement is correct?

- A) The base is thin, so $m$ is small and $f_R$ is low: the base answers to low frequencies.
- B) Air reaches the cochlea through the oval window and bends the hair cells directly.
- C) At the base $k$ is large and $m$ small, so $f_R$ is high: it answers to high frequencies.
- D) Each place answers to one single frequency only, so a tone between two places is not heard.

> [!success]- Answer
> **C.** Large $k$ and small $m$ make $k/m$ and $f_R$ large at the base. At the apex $f_R$ is low. Each place answers best to its own frequency: a bank of band-pass filters.
> - A: $m$ is in the denominator. A small mass gives a high $f_R$, not a low one.
> - B: The air stops at the eardrum. The bones move the oval window, the fluid moves the membrane, and the membrane bends the hair cells.
> - D: Each place is a band-pass filter, not a single frequency. Neighbouring filters overlap, so every tone in the range excites some places.

### Q19 (slide 21)

![[cs568_lecture_02_l2_p021.jpg]]

On the uncoiled cochlea the labels 1600, 800, 400, 200, 100, 50, 25 Hz are about equally far apart. The upper-right plot shows the tuning curves of several places. Which statement is correct?

- A) Equal distances are equal frequency ratios, so the map from frequency to place is about logarithmic.
- B) Equal distances are equal steps in Hz, so the map from frequency to place is about linear.
- C) Each tuning curve is steeper on its low-frequency side than on its high-frequency side.
- D) Low frequencies peak near the base, close to the stapes, and high frequencies near the apex.

> [!success]- Answer
> **A.** Each label is half the one before: one octave per step. So 100 to 200 Hz get as much membrane as 800 to 1600 Hz. The ear resolves low frequencies finely in Hz and high ones coarsely.
> - B: From 1600 to 800 Hz is 800 Hz, from 50 to 25 Hz only 25 Hz. Only the ratio (2) is the same.
> - C: It is the other way round. Each curve rises slowly on the low side and falls steeply on the high side.
> - D: 1600 Hz is at the base, next to the stapes, and 25 Hz at the apex. High frequencies peak near the base.

### Q20 (slide 22)

![[cs568_lecture_02_l2_p022.jpg]]

The two spectra are the vowels [AH] and [EE]. Below them, a drawing of the basilar membrane shows where each vowel excites it. Which statement is correct?

- A) On the same note both vowels have the same harmonics, so they excite the same places.
- B) The excited places come from the formant peaks, so the vowels differ even on the same note.
- C) The peak of [EE] near 3000 Hz excites the place nearest the low-frequency end.
- D) Each vowel excites only one place, the place that belongs to its highest peak.

> [!success]- Answer
> **B.** Each formant peak excites the place of its frequency, so each vowel gives its own pattern of places. The note is $F_0$, a property of the source. The formants belong to the filter, so the pattern stays.
> - A: The harmonic frequencies are the same, but their strengths differ, because the formants differ. So the excited places still differ.
> - C: 3000 Hz is high and excites the high-frequency end (the base). The lowest [EE] peak, a few hundred Hz, is nearest the low-frequency end (the apex).
> - D: Every peak excites its own place. Both drawings show several excited regions for each vowel.

### Q21 (slide 23)

![[cs568_lecture_02_l2_p023.jpg]]

The block diagram goes from the sound through the ear to pattern recognition. Which statement is correct?

- A) The cochlea box splits the sound by frequency, like a filter bank, into parallel channels.
- B) LO and HI in the cochlea box are levels: the box turns the sound up from quiet to loud.
- C) Each line behind the cochlea box is one moment of time, and the lines follow each other.
- D) In a speech recogniser, the HMM plays the role of the cochlea box in this diagram.

> [!success]- Answer
> **A.** "LO ... HI" is a frequency axis ("Frequency" is written in the box). Each parallel line is one frequency channel, and its short strokes are the nerve spikes in that channel.
> A recogniser has the same chain: spectral analysis (often a mel filter bank), feature vectors, then a pattern recogniser such as an HMM.
> - B: LO and HI are low and high frequencies, not levels.
> - C: All lines run at the same time. They differ in frequency, not in time.
> - D: The HMM matches the last block, pattern recognition. The cochlea matches the spectral front end.

## 7. Thresholds and loudness (slides 24-25)

### Q22 (slide 24)

![[cs568_lecture_02_l2_p024.jpg]]

The sound pressure level is $L_p=20\log_{10}(p/p_0)$ with $p_0=20\ \mu\text{Pa}$. In the figure the threshold of hearing is at about 40 dB at 100 Hz and a little below 0 dB near 3 kHz. Which statement is correct?

- A) 0 dB SPL means zero pressure, so the parts of the threshold curve below 0 dB are measuring errors.
- B) A level of $-5$ dB SPL is a pressure of about $6.3\ \mu\text{Pa}$, below the reference pressure.
- C) A 3 kHz tone at 20 dB SPL is heard, but a 100 Hz tone at the same 20 dB SPL is not heard.
- D) A 100 Hz tone at 20 dB SPL is heard, but a 3 kHz tone at the same 20 dB SPL is not heard.

> [!success]- Answer
> **C.** At 100 Hz the threshold (about 40 dB) is above 20 dB, so the tone is not heard. At 3 kHz the threshold is below 0 dB, so 20 dB is well above it. The same SPL does not mean the same audibility.
> - A: 0 dB means $p=p_0=20\ \mu\text{Pa}$, not silence. A negative level means $p<p_0$, which the ear can hear near 3 to 4 kHz.
> - B: $20\cdot10^{-5/10}\approx6.3$ uses the intensity form $/10$ for a pressure. With $/20$: $20\cdot10^{-5/20}\approx11.2\ \mu\text{Pa}$.
> - D: The curve is read the wrong way round. The threshold is high at 100 Hz and lowest near 3 to 4 kHz.

### Q23 (slide 25)

Sound pressure level: $L_p=20\log_{10}(p/p_0)=10\log_{10}(I/I_0)$. Stevens' law for loudness: $S\propto p^{0.6}\propto I^{0.3}$. The level of a tone is raised by 20 dB. By which factors do the pressure $p$, the intensity $I$ and the loudness $S$ grow?

- A) $p\times10$, $I\times100$, $S\times2.0$
- B) $p\times10$, $I\times100$, $S\times4.0$
- C) $p\times100$, $I\times10$, $S\times2.0$
- D) $p\times100$, $I\times10$, $S\times4.0$

> [!success]- Answer
> **B.** $p$: $10^{20/20}=10$. $I$: $10^{20/10}=100$. $S$: $100^{0.3}\approx3.98$, about 4. From the pressure: $10^{0.6}\approx3.98$, the same.
> 100 times the intensity is only about 4 times as loud. Along one equal-loudness contour (phon) the loudness is constant and the SPL changes.
> - A: $10^{0.3}\approx2.0$ puts the intensity exponent 0.3 on the pressure ratio.
> - C: $/10$ used for pressure and $/20$ for intensity. Then $10^{0.3}\approx2.0$ follows from the wrong intensity.
> - D: Two slips: the $/10$ and $/20$ swapped, and the exponent 0.3 put on the pressure ratio ($100^{0.3}\approx4.0$).

## 8. Masking and critical bands (slides 26-29)

### Q24 (slide 26)

A loud tone A and a quiet tone B are played together. Slide 26 says masking prevents us from treating perception "as a summation of responses". Which statement is correct?

- A) A summing system would let A cover B, so B is lost. The ear always hears B, because it separates the tones by frequency.
- B) In a summing system the part due to B is the same as when B is alone, so B can be found. In the ear A can make B inaudible.
- C) A summing system would add A and B into one tone of a new frequency. The ear keeps both tones and hears each of them.
- D) A summing system and the ear both lose B whenever it is quieter than A, so masking adds nothing new to the model.

> [!success]- Answer
> **B.** In a linear (summing) system the response to A plus B is the response to A plus the response to B. B's part does not change, so it can always be found.
> In the ear A raises the threshold for B. B may be audible alone but inaudible with A. The response to B depends on what else is there: the ear is non-linear.
> - A: The roles are reversed. A linear sum keeps B's part intact. It is the ear that can lose B.
> - C: Adding two tones gives no new frequency. The sum still holds both tones at their own frequencies.
> - D: A summing system never loses B. And the ear does not lose every quieter tone: it depends on the distance in frequency and time.

### Q25 (slide 27)

![[cs568_lecture_02_l2_p027.jpg]]

In the left figure two grey tones have the same height. The one close to the masking tone is inaudible, the one further away is audible. Which statement is correct?

- A) The inaudible tone becomes audible if it is moved closer to the masking tone in frequency.
- B) In time, the masked area lasts longer before the masking tone than it lasts after it.
- C) The inaudible tone becomes audible if moved further away, where the masked area is lower.
- D) The ear is more sensitive at the higher tone, and the masker plays no part in the difference.

> [!success]- Answer
> **C.** The shaded area is the raised threshold around the masker. It is highest near the masker, so the near tone lies under its edge. Further away the area is low and the tone sticks out. Raising the tone or weakening the masker also works.
> - A: Closer to the masker the masked area is even higher, so the tone stays inaudible.
> - B: Pre-masking lasts about 5 ms, post-masking 50 to 300 ms. The masked area lasts longer after the masker.
> - D: The figure draws the masked area around the masker. The near tone lies under it, so the masker causes the difference.

### Q26 (slide 28)

A tone at $f_c$ has the critical band $\Delta f_c$ around it. A masker with a triangular spectrum is centred at $f_c$ and is wider than $\Delta f_c$. On a frequency axis the critical bands get wider in Hz as the frequency rises, but each band is about 1.5 mm of basilar membrane. Which statement is correct?

- A) The whole triangle masks the tone, because the peak of the triangle lies at $f_c$.
- B) The high bands cover more membrane, and 1.5 mm is only the average band length.
- C) Only the part of the triangle inside $\Delta f_c$ masks the tone at $f_c$.
- D) The bands widen in Hz because the ear has fewer hair cells at high frequencies.

> [!success]- Answer
> **C.** The amount of masking equals the masker energy inside the critical band of the tone (the dark part on slide 28). The parts outside the band excite other places and do not mask this tone.
> The membrane maps frequency about logarithmically, so the same 1.5 mm covers few Hz at low frequencies and many Hz at high ones.
> - A: Energy outside the critical band excites other places, wherever the peak is. It does not mask this tone.
> - B: Every band is about 1.5 mm. The width in Hz grows because of the logarithmic map, not the length.
> - D: The hair cells sit evenly along the membrane. The bands widen in Hz because of the place map.

### Q27 (slide 29)

A 2 kHz tone is played in noise centred at 2 kHz with a fixed level per Hz. For noise bandwidths of 50, 100, 200, 400 and 800 Hz the tone is just heard at 40, 43, 46, 48 and 48 dB (invented numbers). Which statement is correct?

- A) The threshold rises 3 dB per step because the noise pressure doubles, and $10\log_{10}2\approx3$ dB.
- B) The critical band is about 320 Hz, since the last 2 dB rise gives $200\cdot10^{2/10}\approx317$ Hz.
- C) The critical band is 400 Hz, the first noise width at which the threshold reaches 48 dB.
- D) The critical band is 800 Hz, the widest noise in which the tone is still heard at 48 dB.

> [!success]- Answer
> **B.** Each step doubles the bandwidth, so the noise power in the band doubles: $10\log_{10}2\approx3$ dB, as long as all the noise is inside the critical band.
> From 200 Hz to the final value the rise is $48-46=2$ dB, so the band is $200\cdot10^{2/10}\approx317$ Hz, about 320 Hz.
> - A: Doubling the pressure would give $20\log_{10}2\approx6$ dB. It is the power that doubles.
> - C: A 400 Hz band would give $46+3=49$ dB at 400 Hz. The measured 48 dB shows the band is narrower.
> - D: The threshold stops rising at 400 Hz. Noise beyond the band adds nothing, so 800 Hz only repeats 48 dB.

## 9. Bark, mel and pitch (slides 30-31)

### Q28 (slide 30)

The mel scale is $m=2595\log_{10}(1+f/700)$ with $f$ in Hz. A mel filter bank up to 4000 Hz has triangular filters that are narrow and close together on the left and wide on the right. Which statement is correct?

- A) 700 Hz sets where the scale bends from about linear to about logarithmic.
- B) 2595 sets where the scale bends, and 700 only sets the size of the unit.
- C) The filters are wide at high frequencies because the ear is less sensitive there.
- D) The filters are equally spaced in Hz, and only their widths grow to the right.

> [!success]- Answer
> **A.** For $f\ll700$ Hz, $\log_{10}(1+f/700)$ grows about in proportion to $f$. For $f\gg700$ Hz it grows like $\log_{10}f$. The factor 2595 only scales the result so that $2595\log_{10}(2.4286)\approx1000$ mel at 1000 Hz.
> - B: The roles are swapped. A factor in front of the log cannot move the bend. The 700 inside the log does.
> - C: The width comes from equal spacing on the mel scale, which copies the critical bands, not from sensitivity.
> - D: The filters are equally spaced in mel. In Hz both their spacing and their widths grow.

### Q29 (slide 31)

With $m=2595\log_{10}(1+f/700)$, the step 300 to 400 Hz is about 107 mel and the step 900 to 1000 Hz about 68 mel. Which statement is correct?

- A) The step from 2000 to 2100 Hz is about 55 mel, smaller than the step from 900 to 1000 Hz.
- B) Below 1 kHz the scale is linear, so going from 200 to 400 Hz exactly doubles the mel value.
- C) Equal steps in Hz give equal steps in mel above 1 kHz, so 2000 to 2100 Hz is also 68 mel.
- D) The step from 2000 to 2100 Hz is about 41 mel, smaller than the step from 900 to 1000 Hz.

> [!success]- Answer
> **D.** $m(2000)=2595\log_{10}(3.857)\approx1521.4$, $m(2100)=2595\log_{10}4\approx1562.3$. The step is about 41 mel.
> Doubling $F_0$ does not double the mel value: 200 Hz is about 283 mel, 400 Hz about 509 mel, a ratio of 1.80.
> - A: $2595\log_{10}(2100/2000)\approx55$ forgets the "1+" inside the log.
> - B: The scale is only about linear there. The numbers give $509/283\approx1.80$, not 2.
> - C: Above 1 kHz the scale is about logarithmic. Equal steps in Hz give smaller and smaller steps in mel.
