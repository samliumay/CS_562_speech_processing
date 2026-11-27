---
course: CS 568 Speech Processing
lesson: CS568 L18 - Speech synthesis back-end
type: quiz
questions: 27
---

# CS568 L18 - Speech synthesis back-end - quiz

Lesson note: [[CS568 L18 - Speech synthesis back-end]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Rule-based and corpus-based back-ends (slide 2)

### Q1 (slide 2)

Four back-end methods: articulatory synthesis, formant synthesis, diphone concatenation and unit selection. Sort them into the rule-based family (an expert writes rules that drive a model) and the corpus-based family (the output is built from recorded speech). Why is the line between the families "no longer clear"?

- A) Rule-based: articulatory, formant. Corpus-based: diphone concatenation, unit selection. Unit selection falls back to rule-based formant synthesis whenever a needed unit is missing.
- B) Rule-based: articulatory, formant. Corpus-based: diphone concatenation, unit selection. Hybrids such as HMM synthesis use a parametric model but learn its parameters from recordings.
- C) Rule-based: articulatory, formant, diphone concatenation. Corpus-based: unit selection. Unit selection falls back to rule-based formant synthesis whenever a needed unit is missing.
- D) Rule-based: articulatory, formant, diphone concatenation. Corpus-based: unit selection. Hybrids such as HMM synthesis use a parametric model but learn its parameters from recordings.

> [!success]- Answer
> **B.** Articulatory and formant synthesis play back no recording: rules drive a model. Diphone concatenation and unit selection build the output from recorded speech. HMM synthesis is a parametric model, like a rule-based system, but its parameters are learned from a speech corpus, not written by an expert.
> - A: The sorting is right, but the reason is false. Unit selection falls back to a complete diphone set (safety units), which is also recorded speech.
> - C: Both parts are wrong. Diphones are recorded speech, and the fall-back of unit selection is a recorded diphone set.
> - D: Diphone concatenation plays back recorded diphones, so it is corpus-based. Changing their prosody by signal processing does not make it rule-based.

## 2. Articulatory synthesis (slides 3-5)

### Q2 (slide 3)

Six quantities: tongue height, frequency of $F_2$, jaw opening, bandwidth $B_1$, velum opening, muscle timing. Which set contains exactly the control parameters of an articulatory synthesiser?

- A) Tongue height, jaw opening, velum opening, muscle timing.
- B) Tongue height, jaw opening, velum opening, frequency of $F_2$.
- C) Tongue height, jaw opening, frequency of $F_2$, bandwidth $B_1$.
- D) Jaw opening, velum opening, muscle timing, bandwidth $B_1$.

> [!success]- Answer
> **A.** Articulatory parameters describe speech production: the geometry and dynamics of the organs and the forces and timings of the muscles. $F_2$ and $B_1$ describe the signal (resonances of the tract). They are parameters of a formant synthesiser.
> - B: $F_2$ depends on the tongue position, but it is a result of the tract shape, not a control. The synthesiser sets the shape, and the acoustics give $F_2$. Muscle timing is a real control and is missing.
> - C: $F_2$ and $B_1$ describe the signal, so they belong to a formant synthesiser. Velum opening and muscle timing are missing.
> - D: $B_1$ is a property of the signal, set directly only in a formant synthesiser. Tongue height is a real control and is missing.

### Q3 (slide 4)

An articulatory model sets the positions of tongue, jaw, lips and velum. The figure of the slide shows three plots: the area function $A_{\text{Tract}}$ (cross-section of the tube from glottis to lips), the velum opening $A_{\text{Velum}}$, and the transfer function $H_{\text{Tract}}$ in dB. The tongue of the model moves. What happens?

- A) The formant peaks stay where they are. Only $F_0$ changes, so the harmonics move under them.
- B) The formant peaks are moved first by rule. The area function is then fitted to the new peaks.
- C) The area function changes first. The transfer function is computed from it, so its formant peaks move.
- D) The area function changes, but the formant peaks stay, as they depend only on the tract length.

> [!success]- Answer
> **C.** The synthesiser never sets a formant directly. It sets a shape: the tongue changes the cross-section along the tube. The transfer function is computed from that area function, so its peaks (the formants) then move.
> - A: This mixes up formants and harmonics. $F_0$ comes from the glottis. A tongue movement changes the tract, so the formants move.
> - B: This is how a formant synthesiser thinks: it sets formants directly. An articulatory synthesiser sets the shape, and the formants follow from it.
> - D: Only a uniform tube has formants fixed by its length alone. A real tract shape changes the resonances, so the peaks move.

### Q4 (slide 5)

Find the mistake: "Articulatory synthesis is not used much because its speech cannot be understood." Which correction is right?

- A) The speech is intelligible and natural. It is rare only because solving the airflow equations is too slow for real time.
- B) The sentence is right. The models rest on expert guesses, so most of the output cannot be understood.
- C) The speech is intelligible but far from natural. Its parameters describe the signal, not the organs, so it misses coarticulation.
- D) The speech is intelligible but not natural. Each sub-model must copy one speaker closely, and articulatory data is hard to get.

> [!success]- Answer
> **D.** The output can be understood. The problem is naturalness: every sub-model must be very accurate for one speaker, but the models rest on expert guesses, because articulatory data is costly and invasive to collect.
> - A: The speech is not natural; that is the real problem. The slide names the modelling accuracy and the data, not the speed.
> - B: Expert guesses limit naturalness, not intelligibility. Articulatory speech can be understood.
> - C: The reason is reversed. Articulatory parameters describe the organs. Signal parameters belong to formant synthesis.

## 3. Formant synthesis (slides 6-9)

### Q5 (slide 6)

Find the mistake: "The parallel form can approximate any speech spectrum, so the series form has no use." Which correction is right?

- A) The series form is needed for nasals and fricatives, because only the parallel form puts zeros between the resonances.
- B) The series form needs one frequency and bandwidth per resonance and one common gain. It suits non-nasal vowels.
- C) The parallel form has no zeros at all, so nasal sounds with their spectral zeros need the series form instead.
- D) The series form is needed because it is the only form with a separate gain per filter, which vowels need.

> [!success]- Answer
> **B.** The parallel form pays for its flexibility: one gain per filter, each controlled over time, and spectral zeros between the resonances. For non-nasal sounds the series form gives a good spectrum with fewer parameters.
> - A: The use is reversed. The series form fails for nasals, fricatives and mixed voicing. That is where the parallel form is used.
> - C: The parallel form does put zeros between the resonances, and the series form is the one that fails for nasals.
> - D: The gains are swapped. The parallel form has one gain per filter. The series form has one common gain.

### Q6 (slide 7)

The transfer function on the left has four formants. Each resonator is set by its centre frequency and its bandwidth. The gains are as shown in the two diagrams on the right. How many parameters does each structure need to copy all four formants?

![[cs568_lecture_18_l18_p007.jpg]]

- A) Series 9, parallel 12.
- B) Series 8, parallel 13.
- C) Series 9, parallel 13.
- D) Series 8, parallel 12.

> [!success]- Answer
> **A.** Top diagram: series (cascade). One overall amplitude: $4+4+1=9$. Bottom diagram: parallel. One amplitude factor per branch: $4+4+4=12$.
> - B: Two slips: the series gain was forgotten (8), and an extra overall gain was added to the parallel form (13).
> - C: Parallel: an extra overall gain was added on top of the branch gains, $4+4+4+1=13$. The branch factors already set the level.
> - D: Series: the overall amplitude was forgotten, $4+4=8$.

### Q7 (slide 8)

Formant synthesisers are liked in speech perception research, but they are poor at copying a given speaker. Which statement explains both facts and names the main problem?

- A) The parameters are learned from a corpus, which suits research. Copying fails because the corpus of one speaker is too small.
- B) The parameters describe the organs, which suits research. Copying fails because articulatory data is invasive to collect.
- C) Every parameter can be set directly and alone. Copying fails because the resonators cannot make more than three formants.
- D) Each parameter can be set alone. But all must come from rules, and deriving the source timing and filter tracks is the main problem.

> [!success]- Answer
> **D.** The experimenter controls each formant, bandwidth and source parameter exactly, so the stimulus is exact. To copy a voice, all parameters must be set by rules, and the rules miss details of a real voice. Analysis-by-synthesis helps to find them.
> - A: This describes HMM synthesis. Formant parameters come from expert rules, not from a corpus.
> - B: This describes articulatory synthesis. Formant parameters describe the signal, not the organs.
> - C: The first part is right, but the limit is invented. One can add as many resonators as needed; the problem is setting them by rule.

### Q8 (slide 9)

Klatt's hybrid synthesiser has three sources (voicing, aspiration, frication) and two resonator branches: a cascade branch and a parallel branch. Which assignment does it use, and why?

- A) Voicing feeds the cascade; aspiration and frication feed the parallel branch. A cascade of resonators cannot filter a noise source.
- B) Voicing feeds the cascade; aspiration and frication feed the parallel branch. A fricative needs its own amplitude for each resonance.
- C) Voicing and aspiration feed the cascade; frication feeds the parallel branch. A fricative needs its own amplitude for each resonance.
- D) Voicing and aspiration feed the cascade; frication feeds the parallel branch. A cascade of resonators cannot filter a noise source.

> [!success]- Answer
> **C.** The two laryngeal sources (voicing and aspiration) are added and go through the cascade, which suits voiced non-nasal sounds. The frication spectrum needs an individual amplitude per resonance, so frication gets the parallel branch.
> - A: Both parts are wrong. Aspiration goes with voicing into the cascade, and a cascade can filter noise.
> - B: Aspiration is noise, but it is made at the larynx and shaped by the whole tract like voicing. In Klatt's design it is added to voicing and goes through the cascade.
> - D: The assignment is right, but the reason is false. Aspiration noise does go through the cascade. The reason is the need for one amplitude per resonance.

## 4. Concatenative synthesis and diphones (slides 10-12)

### Q9 (slide 10)

A student wants to store whole phones, cut at the phone boundaries, and glue them together. Why is this a bad choice?

- A) Whole phones are too short to hold a pitch period, so their prosody could not be modified later.
- B) The boundaries lie inside transitions, which hold the coarticulation. Cuts should fall in the stable middle parts.
- C) The boundaries lie in the stable parts, so the joins become too smooth and lose the natural jitter and shimmer.
- D) Whole phones would need about $N^2$ stored units, while diphones need only about $N$, so the inventory grows.

> [!success]- Answer
> **B.** A phone boundary is a transient. Transients carry the coarticulation and matter most for intelligibility. Cutting there breaks them, and the pieces do not fit a new neighbour. So the transitions are kept whole and the joins go in the stable parts (the diphone).
> - A: A phone (tens of ms) holds several pitch periods. The problem is where the cut falls, not the length.
> - C: This is reversed. The stable parts are in the middle of a phone; the boundaries are in the transitions.
> - D: The counts are swapped. $N$ phonemes give $N$ phone units, but up to $N^2$ diphones.

### Q10 (slide 11)

A diphone runs between two neighbouring phones, in a given order. A language has $N=45$ phonemes. What is the upper bound for the size of its diphone inventory?

- A) 45
- B) 91125
- C) 990
- D) 2025

> [!success]- Answer
> **D.** Every ordered pair of phonemes can give one diphone: $N^2=45^2=2025$. The real number is smaller, because some phone pairs never occur in the language.
> - A: 45 is the number of phonemes, one unit per phone. A diphone is a pair of phones.
> - B: $45^3=91125$ is the bound for triphones (three phones), not diphones.
> - C: $\binom{45}{2}=45\cdot44/2=990$ counts unordered pairs. Order matters: /d o/ and /o d/ are different diphones.

### Q11 (slide 12)

For a fixed diphone inventory, the recording rules ask for a speaker who avoids variations, even large pitch variations, and they exclude strongly stressed or strongly reduced contexts. Why?

- A) Pitch marks can only be placed in steady speech. Stressed and reduced vowels have no clear pitch periods.
- B) The front-end predicts only neutral prosody, so every stored unit must have that same prosody.
- C) One copy must serve every context, and a neutral copy is on average closest to all requests.
- D) A neutral copy already sounds natural in every context, so no prosody modification is needed at run time.

> [!success]- Answer
> **C.** With one copy per unit, the neutral one needs the least prosody modification on average, and two units from different words then match better in pitch and timbre at the join. A very stressed or reduced copy is far from most requests.
> - A: Stressed and reduced vowels are voiced and have pitch periods. The rule is about fitting every context, not about pitch marking.
> - B: The front-end predicts varied durations and $F_0$ contours. That is exactly why units must be modified.
> - D: Prosody modification is still needed: the front-end asks for many durations and pitch contours. Neutral copies only reduce how much must change.

## 5. Running a diphone synthesiser (slides 13-19)

### Q12 (slide 13)

A diphone synthesiser runs three steps: (1) take the diphones from the database, (2) prosodic modification, (3) smoothing of the joins. What do you hear if step 2 is skipped, and if step 3 is skipped?

- A) Without step 2, the phones are right but rhythm and intonation follow the recordings. Without step 3, the joins click or change timbre.
- B) Without step 2, the phones are right but rhythm and intonation follow the recordings. Without step 3, nothing changes at the joins.
- C) Without step 2, the speech is fine, as each unit already has the requested prosody. Without step 3, the joins click or change timbre.
- D) Without step 2, the speech is fine, as each unit already has the requested prosody. Without step 3, nothing changes at the joins.

> [!success]- Answer
> **A.** Without prosodic modification each diphone keeps its recorded duration and pitch, so the front-end's rhythm and intonation are lost. Without smoothing, amplitude and spectral envelope do not match at the joins, which are heard as clicks or sudden timbre changes.
> - B: The first part is right. But the joins are not smooth by themselves: units from different words differ in amplitude, phase and envelope.
> - C: The second part is right. But one stored copy cannot already have every duration and pitch the front-end asks for.
> - D: Both parts are wrong. Stored units rarely match the requested prosody, and the joins do not match without smoothing.

### Q13 (slide 14)

The front-end asks for the word "dog" with these durations: silence 50 ms, /d/ 80 ms, /o/ 160 ms, /g/ 70 ms, silence 50 ms. The diphones use the usual diphone cut points. How long must the unit `do` be after prosody modification?

![[cs568_lecture_18_l18_p014.jpg]]

- A) 120 ms
- B) 240 ms
- C) 102.5 ms
- D) 160 ms

> [!success]- Answer
> **A.** A diphone runs from the middle of one phone to the middle of the next. `do` takes the second half of /d/ and the first half of /o/: $80/2+160/2=40+80=120$ ms.
> - B: $80+160=240$ ms takes both phones whole, as if the cuts were at the phone boundaries.
> - C: $410/4=102.5$ ms shares the total duration equally among the four diphones. The units have different lengths.
> - D: 160 ms is the duration of /o/ alone. `do` holds only half of /o/ and also half of /d/.

### Q14 (slide 15)

A stored unit lasts 90 ms with $F_0=120$ Hz. The front-end wants 120 ms at the same pitch. You simply play the unit back more slowly. What pitch do you hear?

- A) 160 Hz
- B) 120 Hz
- C) 67.5 Hz
- D) 90 Hz

> [!success]- Answer
> **D.** Speed factor $90/120=0.75$, so the unit lasts $90/0.75=120$ ms. Every frequency is multiplied by the same factor: $0.75\times120=90$ Hz. The formants move down too, so the vowel changes.
> - A: $120\times120/90=160$ Hz uses the inverted factor. Slower playback lowers the pitch.
> - B: Slower playback does not change the duration alone. It scales all frequencies, the pitch too.
> - C: $0.75^2\times120=67.5$ Hz applies the factor twice. The pitch is scaled once.

### Q15 (slide 16)

TD-PSOLA. In (a) the original pitch period $T_0$ is about 7.5 ms. In (b) the frames are put down at a new spacing $T$ of about 11 ms. (The time axis is in seconds; its first printed label "0.05" means 0.005.) About what pitch does the modified speech (c) have?

![[cs568_lecture_18_l18_p016.jpg]]

- A) About 133 Hz
- B) About 91 Hz
- C) About 196 Hz
- D) About 11 Hz

> [!success]- Answer
> **B.** The spacing of the frames sets the new period: $F_0=1/T=1/0.011\approx91$ Hz. The old pitch was $1/0.0075\approx133$ Hz, so the pitch was lowered. The frames carry the spectral envelope, which stays the same.
> - A: $1/T_0\approx133$ Hz is the old pitch. Moving the frames apart changes the period, so the pitch changes.
> - C: $133\times11/7.5\approx196$ Hz scales the pitch up with the spacing. A larger spacing is a longer period, so the pitch goes down.
> - D: This takes the period in ms as the frequency ($F_0=T$). The frequency is $1/T$.

### Q16 (slide 17)

Find the mistake: "A phase mismatch at a join means that the two units have different pitch. The fix is to record at constant pitch." Which correction is right?

- A) That is a pitch mismatch. A phase mismatch is a loudness difference between the two units, fixed by scaling the amplitude.
- B) That is a spectral envelope mismatch. A phase mismatch comes from units of different contexts and needs envelope interpolation.
- C) That is a pitch mismatch. A phase mismatch can occur at equal pitch: the OLA frames sit at different places in the period.
- D) The sentence is right. Recording at constant pitch also lines up the pitch pulses of the two units at every join.

> [!success]- Answer
> **C.** The pitch pulses of the two units then do not line up when the frames overlap. Fixes: accurate pitch marks (TD-PSOLA), shifting frames to the maximum cross-correlation (WSOLA), or measuring and correcting the phase (MBROLA, HNS).
> - A: The first part is right, but phase is not loudness. It is the position of the frames in the pitch period.
> - B: Different contexts cause the spectral envelope mismatch. Phase is about where the frames sit in the period.
> - D: Equal pitch does not place the frames at the same point of the period. The pulses can still be shifted.

### Q17 (slide 18)

At a join the left unit ends with $F_0=110$ Hz and the right unit starts with $F_0=140$ Hz. The difference is split into 6 equal steps, one per frame. How large is each step?

- A) 5 Hz
- B) 30 Hz
- C) 180 Hz
- D) 41.7 Hz

> [!success]- Answer
> **A.** $(140-110)/6=30/6=5$ Hz per frame, instead of one jump of 30 Hz.
> - B: 30 Hz is the whole difference in one jump, the mismatch that spreading avoids.
> - C: $30\times6=180$ Hz multiplies by the number of frames instead of dividing.
> - D: $(110+140)/6\approx41.7$ Hz divides the sum of the two values. The step uses the difference.

### Q18 (slide 19)

A fixed diphone inventory sounds unnatural for two reasons. Which statement names them and says how storing several instances per unit helps?

- A) The diphone set is incomplete, and the front-end prosody is wrong. Several instances fill the missing pairs and correct the front-end prosody.
- B) One copy per unit forces over-articulated speech, and prosody modification degrades it. Several instances give natural units with nearly the right prosody.
- C) The units are recorded at a low sampling rate, and prosody modification degrades the signal. Several instances let the system average the noise away.
- D) One copy per unit forces over-articulated recordings, and the joins are not smoothed. Several instances remove the need for any smoothing at the joins.

> [!success]- Answer
> **B.** With several instances, each can be natural speech from a real context, and one with nearly the right pitch and duration can often be chosen, so little or no signal processing is needed.
> - A: A fixed inventory is complete by design, and the front-end is not the cause. The causes are one copy per unit and the signal processing.
> - C: The sampling rate is not a cause on the slide, and unit selection picks one instance; it does not average instances.
> - D: Smoothing is part of every fixed-inventory system. The second cause is the damage done by prosody modification.

## 6. Unit selection (slides 20-21)

### Q19 (slide 20)

In unit selection the target cost $C^t(t_i,u_i)$ compares a candidate unit $u_i$ with the target $t_i$ from the front-end. Why can the target cost alone not choose the units?

- A) It measures only pitch and duration, so the phonetic context must come from another cost.
- B) It is zero for units that were consecutive in the corpus, so it cannot tell such units apart.
- C) It cannot be computed before the neighbouring units are chosen, so the joins must be chosen first.
- D) It looks at each unit alone. Two units can both match their targets well and still join badly.

> [!success]- Answer
> **D.** The quality of a join depends on two neighbouring units, which the target cost never sees. So the concatenation cost $C^c(u_{i-1},u_i)$ is added, and the sum of both costs is minimised.
> - A: The target cost also has symbolic features such as phonetic context and stress.
> - B: The zero rule belongs to the concatenation cost, not to the target cost.
> - C: The target cost needs only one target and one candidate. It does not depend on the neighbours.

### Q20 (slide 21)

Unit selection with three targets. The total cost is the sum of all target costs and all concatenation costs, including the joins with the silence $S$ at both ends. Path P has target costs 2, 1, 3 and join costs $C^c(S,u_1)=1$, $C^c(u_1,u_2)=0$, $C^c(u_2,u_3)=0$, $C^c(u_3,S)=1$. Path Q has target costs 1, 1, 1 and join costs 1, 3, 2, 1 in the same order. What is the total cost of the winning path?

- A) 8
- B) 3
- C) 6
- D) 9

> [!success]- Answer
> **A.** P: $(2+1+3)+(1+0+0+1)=6+2=8$. Q: $(1+1+1)+(1+3+2+1)=3+7=10$. P wins with 8, although each of its units matches its target less well.
> - B: This chooses by target cost alone: Q has the target sum 3. Its joins add 7, so Q costs 10.
> - C: The two joins with silence were left out: P $=6+0=6$, Q $=3+5=8$. They are part of the total cost.
> - D: The start join $C^c(S,u_1)=1$ was counted twice: P $=9$, Q $=11$. Each join is counted once.

## 7. Costs and the speech corpus (slides 22-24)

### Q21 (slide 22)

A target cost is a weighted sum $C^t=\sum_{j=1}^{3}w_j\,C_j^t$ of three sub-costs: phonetic context, pitch, duration, with weights $w=(2,5,1)$. Candidate 1 has sub-costs $(1,\ 0.2,\ 0.5)$. Candidate 2 has $(0,\ 0.8,\ 0.5)$. Which candidate has the lower target cost, and what is it?

- A) Candidate 2, with 1.3
- B) Candidate 1, with 3.5
- C) Candidate 1, with 3.0
- D) Candidate 2, with 0.66

> [!success]- Answer
> **B.** Candidate 1: $2\cdot1+5\cdot0.2+1\cdot0.5=2+1+0.5=3.5$. Candidate 2: $0+5\cdot0.8+0.5=4.5$. Candidate 1 wins although its phonetic context is wrong: with these weights a pitch error counts more.
> - A: The weights were left out: $1+0.2+0.5=1.7$ and $0+0.8+0.5=1.3$.
> - C: The weights were applied in reverse order $(1,5,2)$: $1+1+1=3.0$ and $0+4+1=5.0$.
> - D: The sub-costs were divided by the weights: $0.5+0.04+0.5=1.04$ and $0+0.16+0.5=0.66$.

### Q22 (slide 23)

The previous unit is fixed. Candidate X directly followed it in the corpus and has target cost 2.0. Candidate Y comes from another sentence: target cost 0.9, join sub-costs 0.4 and 0.1 with weights $w^c=(3,2)$. Which candidate is chosen, and what is its total cost (target plus concatenation)?

- A) Y, with 0.9
- B) Y, with 1.4
- C) X, with 2.0
- D) X, with 0

> [!success]- Answer
> **C.** X followed the previous unit in the corpus, so its concatenation cost is 0: total $2.0$. Y: $C^c=3\cdot0.4+2\cdot0.1=1.4$, total $0.9+1.4=2.3$. X wins: the free join beats the better target match. Too much of this hurts intelligibility.
> - A: This chooses by target cost alone. Y's join adds 1.4.
> - B: The join weights were left out: $0.9+0.4+0.1=1.4$. With the weights Y's join costs 1.4, and its total is 2.3.
> - D: This chooses by concatenation cost alone. X's target cost 2.0 must be added.

### Q23 (slide 24)

Zipf's law says that the frequency of an item is inversely proportional to its rank. The most frequent item of a corpus occurs 6000 times. About how often does the item of rank 100 occur?

- A) About 0.6 times
- B) About 3000 times
- C) About 600,000 times
- D) About 60 times

> [!success]- Answer
> **D.** $f(r)\propto1/r$, so $f(100)\approx6000/100=60$. A few items are very frequent and very many are rare, so a corpus must be huge to cover a random sentence.
> - A: $6000/100^2=0.6$ uses $1/r^2$. Zipf's law has $1/r$.
> - B: $6000/\log_{10}100=3000$ divides by the logarithm of the rank instead of the rank.
> - C: $6000\times100$ makes the frequency grow with the rank, as the slide's wording "proportional to its rank" suggests. The law is inverse.

## 8. HMM-based synthesis (slides 25-29)

### Q24 (slide 25)

In HMM-based synthesis each phoneme is a three-state HMM, and each state emits from a Gaussian mixture at a leaf of a context clustering tree. A word appears in a phonetic context that never occurred in the training data. Why can the system still synthesise it?

- A) The tree's yes/no questions send the new context to a leaf trained on similar contexts.
- B) It falls back to the complete diphone set kept as safety units, as a unit-selection system does.
- C) It trains a new HMM for the context at run time from the frames of the nearest seen context.
- D) It searches the training recordings with Viterbi for the closest stretch and plays that stretch back.

> [!success]- Answer
> **A.** The parameters are tied by context clustering trees. Every context, seen or not, answers the questions and reaches a leaf. That leaf was trained on similar contexts. Unit selection can only play units that exist in its corpus.
> - B: Safety units belong to unit selection. HMM synthesis stores no recorded units at all.
> - C: No training happens at synthesis time. Tying through the tree already covers unseen contexts.
> - D: This is unit selection. HMM synthesis generates parameters from models; it plays back no recording.

### Q25 (slide 26)

An HMM-based synthesiser is trained on a speech database: excitation parameter extraction gives $F_0$, spectral parameter extraction gives the mel-cepstrum, and context-dependent HMMs are trained. Which statement describes the synthesis part correctly?

- A) The labels select stretches of the speech database. The HMMs only smooth the $F_0$ and mel-cepstrum at the joins.
- B) Only the trained HMMs are used. Their mel-cepstrum drives excitation generation, and the MLSA filter applies the $F_0$.
- C) Only the trained HMMs are used. $F_0$ drives excitation generation; the MLSA filter applies the mel-cepstrum envelope.
- D) Only the trained HMMs are used. They output waveform samples, and the MLSA filter removes the buzz from them.

> [!success]- Answer
> **C.** The labels from text analysis select the HMMs, and parameter generation outputs $F_0$ and mel-cepstrum tracks. Excitation generation makes the source (pulses or noise) from $F_0$. The MLSA filter shapes it with the spectral envelope. The database is used only in training.
> - A: This mixes in unit selection. No recorded waveform is used at synthesis time.
> - B: The two streams are swapped. $F_0$ sets the source; the mel-cepstrum is the envelope applied by the filter.
> - D: The HMMs output parameters, not samples. The waveform is built by the vocoder: excitation plus MLSA filter.

### Q26 (slide 27)

Parameter generation with deltas. Two frames, all variances 1. The static means are 2 and 5. The delta $\Delta c=c_2-c_1$ has mean 1. The generated $c_1,c_2$ minimise $(c_1-2)^2+(c_2-5)^2+(c_2-c_1-1)^2$. What are they?

- A) $c_1=0,\ c_2=3$
- B) $c_1=2,\ c_2=5$
- C) $c_1\approx2.67,\ c_2\approx4.33$
- D) $c_1\approx3.33,\ c_2\approx3.67$

> [!success]- Answer
> **C.** Set the derivatives to zero. For $c_1$: $(c_1-2)-(c_2-c_1-1)=0$, so $2c_1-c_2=1$. For $c_2$: $(c_2-5)+(c_2-c_1-1)=0$, so $-c_1+2c_2=6$. Then $c_1=8/3$, $c_2=13/3$. The jump $5/3$ lies between the static jump 3 and the delta mean 1, so the track is smoother.
> - A: The minus sign from the chain rule was lost in the $c_1$ equation: $(c_1-2)+(c_2-c_1-1)=0$ gives $c_2=3$, then $c_1=0$.
> - B: This takes the mean of each Gaussian and ignores the delta term. The jump of 3 contradicts the delta mean of 1.
> - D: The delta was taken as $c_1-c_2$ (wrong sign). That gives $10/3$ and $11/3$.

### Q27 (slide 28)

Three cases: (a) a device with 2 MB of storage, (b) a new voice from 8 minutes of recordings, (c) flight announcements, where the most natural sound is wanted. For which cases is unit selection the better choice than HMM synthesis?

- A) For (b) and (c)
- B) For (c) only
- C) For none of them
- D) For (a) and (c)

> [!success]- Answer
> **B.** (a) HMM synthesis: its footprint is about 1 MB, a unit-selection corpus is far larger. (b) HMM synthesis with adaptation needs only 5 to 10 minutes; unit selection needs hours. (c) Unit selection: a limited domain, real recordings, while HMM speech is smooth but buzzy.
> - A: 8 minutes is far too little for unit selection (1 to 10 hours). HMM adaptation works with 5 to 10 minutes.
> - C: HMM speech is smooth but of poor voice quality (buzzy, muffled). For the most natural sound in a limited domain, unit selection wins.
> - D: Unit selection stores hours of speech, far more than 2 MB. HMM synthesis fits in about 1 MB.
