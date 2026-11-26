---
course: CS 568 Speech Processing
lecture: 18
title: Speech synthesis back-end
tags: [cs568, lecture]
slides: lectures/lecture_18/l18.pdf
full_notes: lecture_notes/lecture_18/CS568_Lecture_18_TTS_Back_End.pdf
---
# CS 568 · Lecture 18 · Speech synthesis: the back-end

> [!summary] In short
> - The back-end turns the front-end's symbols (phones, durations, $F_0$) into a waveform.
> - Rule-based: articulatory synthesis (models the organs) and formant synthesis (second-order resonators, in series or in parallel).
> - Fixed-inventory concatenation: one recording per diphone, then prosody modification and smoothing. Intelligible, not natural.
> - Unit selection: many recordings per unit. A Viterbi search minimises target cost plus concatenation cost.
> - HMM-based synthesis: HMMs used as generators, with a smooth maximum-likelihood trajectory. Small and adaptable, but buzzy.

## 1. Rule-based and corpus-based back-ends (slide 2)
![[cs568_lecture_18_l18_p002.jpg]]

**Notes.** Front-end and back-end are independent: only the interface matters. Two traditional families:

- **Rule-based**: speech comes from a model whose parameters are driven by expert rules (articulatory, formant).
- **Corpus-based**: speech is assembled or learned from recordings (concatenative).

The line is blurred by hybrids. HMM synthesis is a parametric model whose parameters are learned from a corpus.

> [!warning] Slide wording
> Slide 2 is headed "TTS front end", but its content introduces the back-end.

> [!question]- Check (slide 2): Put each method in the rule-based or the corpus-based family: articulatory, formant, diphone concatenation, unit selection. Why does the slide say the distinction "is no longer clear"?
> Rule-based: articulatory and formant synthesis. An expert writes rules that drive the parameters of a model. No recording is played back.
> Corpus-based: diphone concatenation and unit selection. The output is built from recorded speech.
> Hybrid methods mix both ideas. HMM synthesis produces speech from a parametric model (like a rule-based system), but the parameter values are learned from a speech corpus, not written by an expert.

## 2. Articulatory synthesis (slides 3-5)
![[cs568_lecture_18_l18_p003.jpg]]
![[cs568_lecture_18_l18_p004.jpg]]
![[cs568_lecture_18_l18_p005.jpg]]

**Notes.** Mechanical and acoustic models of the speech organs. The control parameters describe production, not the signal: geometry and dynamics of jaw, tongue, lips, velum and glottis, muscle forces and timings. Slide 4: an articulatory model gives the vocal-tract area function, and from it the transfer function.

The output is intelligible but far from natural. Every sub-model would have to be very accurate for one speaker, but they rest on expert guesses, and articulatory data is costly and invasive to collect.

> [!info] Reading the figure of slide 4 (beyond the slides)
> The slide is only a figure. It shows a chain of three steps.
> 1. Left: a mid-sagittal articulatory model. This is a side view of the head, cut down the middle. It places the tongue, jaw, lips and velum.
> 2. Top right: the area function $A_{\text{Tract}}$. It is the cross-section area of the tube (in cm$^2$) at each position $i$ from the glottis to the lips. Next to it is $A_{\text{Velum}}$, the opening of the velum. This opening connects the nasal tract.
> 3. Bottom right: the transfer function $H_{\text{Tract}}$ in dB against frequency. It is computed from the area function. Its peaks are the formants.
>
> So the synthesiser never sets a formant directly. It sets a shape. The acoustics then give the formants.

> [!question]- Check (slide 3): Tongue height, frequency of $F_2$, jaw opening, bandwidth $B_1$, velum opening, muscle timing. Which of these are control parameters of an articulatory synthesiser? What do the others describe?
> Articulatory: tongue height, jaw opening, velum opening, muscle timing. They describe speech production (geometry and dynamics of the organs, forces and timings of the muscles).
> $F_2$ and $B_1$ describe the signal (the resonances of the tract). They are parameters of a formant synthesiser.
> The slide gives the range of such models: from the simple straight tube model to solving the Navier-Stokes equations for the airflow.

> [!question]- Check (slide 4): Name the three plots on the right of the figure. How many formants does the bottom plot show below 4 kHz? If the tongue moves, which plot changes first, and what follows?
> Top left: the vocal-tract area function $A_{\text{Tract}}$ from glottis to lips. Top right: the velum opening $A_{\text{Velum}}$, here small and constant. Bottom: the transfer function $H_{\text{Tract}}$ in dB.
> The bottom plot has four peaks below 4 kHz (at about 0.3, 0.9, 1.9 and 3.5 kHz). These are four formants.
> A tongue movement changes the area function first. The transfer function is computed from it, so the formant peaks then move.

> [!question]- Check (slide 5): Find the mistake: "Articulatory synthesis is not used much because its speech cannot be understood."
> The speech is intelligible. The problem is that it is far from natural.
> Reason: every sub-model must copy one given speaker very accurately. The models depend mostly on expert guesses, because articulatory data is costly and invasive to collect.
> So the method stays specialised. It is attractive for science, because every parameter has a physical meaning.

## 3. Formant synthesis (slides 6-9)
![[cs568_lecture_18_l18_p006.jpg]]
![[cs568_lecture_18_l18_p007.jpg]]
![[cs568_lecture_18_l18_p008.jpg]]
![[cs568_lecture_18_l18_p009.jpg]]

**Notes.** The vocal tract is a black box. Its resonances are copied by second-order filters (resonators).

| | Series (cascade) | Parallel |
|---|---|---|
| Needs | frequency and bandwidth per resonance, one common gain | also one gain per filter |
| Good for | non-nasal sounds (vowels) | any spectrum |
| Problem | not for nasals, fricatives, mixed voicing | spectral zeros appear between resonances |

One resonator for a formant with frequency $F$ and bandwidth $B$ (Hz), sampling period $T=1/f_s$ (from our notes): poles at $z=re^{\pm j\theta}$ with

$$r=e^{-\pi BT},\qquad\theta=2\pi FT,\qquad H(z)=\frac{1-2r\cos\theta+r^2}{1-2r\cos\theta\,z^{-1}+r^2z^{-2}}$$

The numerator makes the gain 1 at zero frequency.

Formant synthesisers are cheap (embedded use), very intelligible, and give fine control (perception research). The main problem is deriving the rules for the source timing and for every filter parameter over time. Analysis-by-synthesis helps. Klatt's synthesiser (slide 9, 19 parameters) is a hybrid: cascade resonators for voiced sources, parallel resonators for frication.

> [!warning] Watch out
> A formant is a resonance of the tract. A harmonic is a multiple of $F_0$. Changing $F_0$ moves the harmonics, not the formants.

> [!info] Analysis-by-synthesis and the labels of Klatt's diagram (beyond the slides)
> **Analysis-by-synthesis**: record a natural utterance. Synthesise the same utterance with a first guess of the parameters. Compare the two (spectrogram, listening). Change the parameters and repeat until the copy is close. The parameter tracks found in this way become the rules.
>
> **Slide 9, sources.** The voicing source has four controls: TO (fundamental period), AV (amplitude of voicing), OO (open quotient: the part of the period in which the glottis is open) and TL (spectral tilt). The aspiration source has the amplitude AH. The frication source has the amplitude AF.
> **Slide 9, filters.** Voicing and aspiration are added and go through the cascade resonators, with the formant frequencies $F_1,F_2,F_3$, the bandwidths $B_1,B_2,B_3$ and a nasal zero $F_Z$. Frication goes through the parallel resonators, with the amplitudes $A_2,\dots,A_6,A_8$. The arrows from $F_2$ and $F_3$ go to both boxes, so these two frequencies are shared. $F_1$ goes only to the cascade. The two outputs are added and pass through the radiation characteristic (the lips).
> **Slide 9, top.** One period of the voicing waveform. In the open phase (length OO$\times$TO) the pulse is the polynomial $aT^2-bT^3$ with peak AV$\times$TO. A low-pass resonator set by TL smooths it. Breathiness noise set by OO is added.

> [!question]- Check (slide 6): Find the mistake: "The parallel form can approximate any speech spectrum, so the series form has no use."
> The parallel form pays for its flexibility. It needs one gain per filter, and each gain must be controlled over time. It also puts spectral zeros between the resonances, which a vowel spectrum does not have.
> The series form needs only the frequency and bandwidth of each resonance and one common gain. For non-nasal sounds it gives a good spectrum with fewer parameters.
> The series form fails for nasals, fricatives and mixed voicing. That is where the parallel form is needed.

> [!question]- Check (slide 7): Name the two block diagrams on the right. The transfer function on the left has four formants. How many parameters does each structure need to copy it, by the rule of slide 6?
> Top: series (cascade). The signal passes through $F_1$, then $F_2$. Only the overall amplitude is set (for example gain 1 at zero frequency).
> Bottom: parallel. The same input feeds $F_1$ and $F_2$. Each output has its own amplitude factor $g_1$, $g_2$, and the outputs are added.
> Series, four formants: 4 frequencies + 4 bandwidths + 1 common gain = 9 parameters.
> Parallel, four formants: 4 frequencies + 4 bandwidths + 4 gains = 12 parameters.

> [!question]- Check (slide 8): Formant synthesisers are liked in speech perception research but are poor at copying a given speaker. Explain both facts. What is the main problem named on the slide?
> Research: every parameter (a formant frequency, a bandwidth, the source) can be set directly and alone. So the experimenter controls the stimulus exactly.
> Copying a speaker: all these parameters must be set by rules. A real voice has details that the rules do not capture, so matching a target voice is very difficult.
> Main problem: deriving the rules for the timing of the source and for the changing values of all filter parameters. Analysis-by-synthesis is one way to find them.

> [!question]- Check (slide 9): In Klatt's diagram, which sources feed the cascade branch and which source feeds the parallel branch? Why this split? Count the control parameters shown in the lower diagram.
> Cascade: the voicing source plus the aspiration source (laryngeal sources). Parallel: the frication source.
> Reason (slide 6): the cascade is good for non-nasal voiced sounds. A fricative needs individual amplitudes per resonance, so it gets the parallel form.
> Count: sources TO, AV, OO, TL, AH, AF = 6. Cascade only: $F_1,B_1,B_2,B_3,F_Z$ = 5. Shared by both boxes: $F_2,F_3$ = 2. Parallel only: $A_2,A_3,A_4,A_5,A_6,A_8$ = 6.
> Total: $6+5+2+6=19$, the "19 parameters" of the title.

## 4. Concatenative synthesis and diphones (slides 10-12)
![[cs568_lecture_18_l18_p010.jpg]]
![[cs568_lecture_18_l18_p011.jpg]]
![[cs568_lecture_18_l18_p012.jpg]]

**Notes.** Concatenative synthesis glues recorded chunks and so keeps natural coarticulation, shimmer, jitter and inharmonic content. Transitions matter most for intelligibility, so keep them whole and join in the stable parts.

- **Fixed inventory**: one sample per unit, plus prosody modification (some signal damage).
- **Unit selection**: several instances per unit, pick the best at run time.

A **diphone** runs from the middle of one phone to the middle of the next. A language with $N$ phonemes needs up to $N^2$ diphones (an upper bound). English: about 1,500 units, about 3 minutes, about 5 MB at 16 kHz and 16 bits.

Building the inventory: list the diphones, write words that contain each at least once, avoid strongly stressed or reduced positions, record a professional speaker with steady pitch, segment (by hand or automatically), store the waveforms.

> [!info] Words of slide 10 (beyond the slides)
> Concatenative synthesis (birleştirmeli sentez) plays back pieces of real recordings, so the small irregularities of a real voice come for free.
> - **Coarticulation**: a phone sounds different next to different neighbours, because the organs are already moving to the next phone.
> - **Jitter**: small changes of the pitch period from one cycle to the next.
> - **Shimmer**: small changes of the amplitude from one cycle to the next.
> - **Inharmonic content**: the noise-like part of the signal, which is not at multiples of $F_0$.
>
> A rule-based synthesiser must model each of these on purpose. Without them the voice sounds like a machine.

> [!question]- Check (slide 10): A student wants to store whole phones, cut at the phone boundaries, and glue them. Use the two grey bullets under "Basic concept" to say why this is a bad choice. Then compare the two types of concatenative synthesis in one sentence each.
> A phone boundary lies inside a transition (a transient). Transients matter most for intelligibility, and they hold the coarticulation. Cutting there breaks them, and the cut pieces do not fit a new neighbour.
> It is better to keep transitions whole and to join in the stable parts. This is the idea of the diphone (slide 11).
> Fixed inventory: one sample per unit, so prosody must be changed by signal processing, which degrades the signal.
> Unit selection (birim seçimi): several instances per unit, so a well-matched one can often be found.

> [!question]- Check (slide 11): A language has $N=45$ phonemes. What is the upper bound for the diphone inventory, and why is the real number smaller? Then check the slide's figures for English: how long is one diphone on average, and is "about 5 MB" right?
> Upper bound: $N^2=45^2=2025$ diphones. Some phone pairs never occur in the language, so the real inventory is smaller.
> For comparison (beyond the slides): 45 phones would be a much smaller inventory, but the joins would fall in the transitions. Triphones would need up to $45^3=91125$ units.
> English: 3 minutes $=180$ s for 1,500 units, so $180/1500=0.12$ s per diphone.
> Size: $180\ \text{s}\times16000\ \text{samples/s}\times2\ \text{bytes}=5{,}760{,}000$ bytes $\approx5.8$ MB. So "about 5 MB" has the right order of size, but it is a little low.

> [!question]- Check (slide 12): The recording rules ask for a speaker who avoids variations, even large pitch variations, and they exclude strongly stressed or strongly reduced contexts. Why are these rules needed for a fixed inventory?
> There is only one copy of each diphone, and it must serve in every context.
> A neutral copy (medium stress, steady pitch) is on average closest to what is needed. So prosody modification has less to change, and two units from different words have similar pitch and timbre at the join.
> A very stressed or very reduced copy would be far from most requests, and it would not match its neighbours.

## 5. Running a diphone synthesiser (slides 13-19)
![[cs568_lecture_18_l18_p013.jpg]]
![[cs568_lecture_18_l18_p014.jpg]]
![[cs568_lecture_18_l18_p015.jpg]]
![[cs568_lecture_18_l18_p016.jpg]]
![[cs568_lecture_18_l18_p017.jpg]]
![[cs568_lecture_18_l18_p018.jpg]]
![[cs568_lecture_18_l18_p019.jpg]]

**Notes.** At run time (slide 14, the word "dog"):

1. Get phonemes, durations and pitch from the front-end.
2. **Prosody modification**: stored diphones rarely match the request. Amplitude is easy. Pitch and duration are not: slower playback is longer and also lower. Methods: TD-PSOLA (time domain, slide 16) and HNM (frequency domain), both in Lecture 19.
3. **Smoothing** the joins, which otherwise click. Three mismatches:
   - **Phase**: OLA frames not centred at the same place. Fix by exact pitch marks (TD-PSOLA), by shifting frames to maximise cross-correlation (WSOLA), or by correcting the phase (MBROLA, HNS).
   - **Pitch**: frames with very different $F_0$. Record with constant pitch, or spread the difference over several frames.
   - **Spectral envelope**: frames from different contexts. Interpolate the envelopes, or move the unit boundaries.

Result: intelligible, not natural. One copy per unit pushes the recording to over-articulated speech, and the signal processing degrades the signal.

> [!info] Reading the TD-PSOLA figure of slide 16 (beyond the slides)
> The slide is only a figure. The details come in Lecture 19. The idea in two steps:
> 1. Row (a), left: the original speech with pitch period $T_0$. Windows of length $L$ (two periods) are centred on the pitch marks. Each window cuts out one frame.
> 2. Row (b), left: the frames are put down again at a new spacing $T$ and added (overlap-add, OLA). Row (c), left: the sum is the pitch-modified speech. Its period is $T$.
>
> The right column shows the amplitude spectrum of each row. Row (a): a spectrum with fine ripples, the harmonics of the old pitch, under an envelope. Row (b): the spectrum of the single frames. It is about the spectral envelope alone. Row (c): harmonics at the new pitch under the same envelope.
> So the frames carry the envelope (the formants), and the spacing sets the pitch. A larger $T$ gives a lower pitch. A smaller $T$ gives a higher pitch.

> [!question]- Check (slide 13): The slide lists three steps. What would you hear if step 2 (prosodic modification) were skipped? What if step 3 (smoothing) were skipped?
> Without prosodic modification: every diphone keeps the duration and pitch of its recording. The phones are right, but rhythm and intonation do not follow the front-end.
> Without smoothing: the end of one diphone and the start of the next do not match in amplitude or spectral envelope. The joins are heard as clicks or sudden timbre changes.

> [!question]- Check (slide 14): Read the figure for the word "dog". Which diphones are taken from the database, and why four of them? What is the total duration? What does the $F_0$ curve ask of the prosody-modification block?
> Diphones: `_d`, `do`, `og`, `g_`. The sequence has five segments (silence, d, o, g, silence). A diphone covers each pair of neighbours, so $5-1=4$ diphones.
> Total duration: $50+80+160+70+50=410$ ms.
> The $F_0$ curve is high and flat at first, then falls through "o" and "g". So each stored diphone must be shifted to that pitch and stretched or shortened to the given durations. If the cut is in the middle of each phone, `do` should last about $80/2+160/2=120$ ms.
> After that the block "Smooth joints" gives the final waveform at the bottom.

> [!question]- Check (slide 15): A stored unit lasts 90 ms with $F_0=120$ Hz. The front-end wants 120 ms at the same pitch. You simply play it back slower. What speed factor do you need, and what goes wrong?
> Speed factor: $90/120=0.75$. The duration becomes $90/0.75=120$ ms.
> But every frequency is multiplied by the same factor. The pitch becomes $0.75\times120=90$ Hz.
> The formants move too: a formant at 600 Hz goes to $0.75\times600=450$ Hz, so the vowel changes.
> Duration and pitch cannot be set separately in this way. TD-PSOLA and HNM can change one without the other.

> [!question]- Check (slide 16): In the left column, compare the spacing $T$ of the frames in (b) with the period $T_0$ in (a). Was the pitch raised or lowered? What stays the same between (a) and (c), and which plot shows it?
> $T_0$ is about 7 to 8 ms in (a). $T$ is about 11 ms in (b). The arrows from (a) to (b) spread out: the frames are put down further apart.
> A longer period means a lower pitch. So the pitch was lowered, from about $1/0.0075\approx133$ Hz to about $1/0.011\approx91$ Hz.
> The spectral envelope stays the same. The right column shows it: the spectra of (a) and (c) have the same overall shape, and the spectrum of the frames in (b) is about that envelope. Only the fine harmonic structure changes.

> [!question]- Check (slide 17): Find the mistake: "A phase mismatch means that the two units have different pitch. The fix is to record at constant pitch."
> That describes a pitch mismatch (slide 18). A phase mismatch can happen with equal pitch.
> It occurs when the OLA frames are not centred at the same place of the pitch period. Then the pitch pulses of the two units do not line up when the frames overlap.
> Fixes on the slide: mark the pitch accurately on all boundary frames (TD-PSOLA), move the frames to the position with maximum cross-correlation (WSOLA), or measure and correct the phase (MBROLA, HNS).

> [!question]- Check (slide 18): At a join the left unit ends with $F_0=110$ Hz and the right unit starts with 140 Hz. The difference is spread evenly over 6 frames. How large is the step per frame? Then say when a spectral envelope mismatch occurs and name the two partial fixes.
> Step: $(140-110)/6=30/6=5$ Hz per frame, instead of one jump of 30 Hz.
> Spectral envelope mismatch: the overlapped frames come from rather different contexts, so the same phone has different formants on the two sides.
> Partial fixes: interpolate linearly between the two envelopes (HNS does this), or move the unit boundaries on the fly to find a smoother join.

> [!question]- Check (slide 19): The slide gives two causes for the unnatural sound of a fixed inventory. Name them, and say how storing several instances per unit (slide 10) helps with each.
> Cause 1: only one unit is stored, so it is recorded as over-articulated speech that fits most contexts. With several instances, each one can be natural speech from a real context.
> Cause 2: signal processing is needed to adjust the prosody, and it always degrades the signal. With several instances, one with nearly the right pitch and duration can often be chosen, so little or no processing is needed.

## 6. Unit selection (slides 20-21)
![[cs568_lecture_18_l18_p020.jpg]]
![[cs568_lecture_18_l18_p021.jpg]]

**Notes.** For each target $t_i$ from the front-end there is a list of candidate units $u_i$.

- **Target cost** $C^t(t_i,u_i)$: how far the candidate is from the target.
- **Concatenation cost** $C^c(u_{i-1},u_i)$: how bad the join is.

The best sequence minimises the total cost, with $S$ the silence at both ends:

$$C(t_1^n,u_1^n)=\sum_{i=1}^{n}C^t(t_i,u_i)+\sum_{i=2}^{n}C^c(u_{i-1},u_i)+C^c(S,u_1)+C^c(u_n,S)$$

Viterbi search, with $D_i(u)$ the best cost ending in candidate $u$ at target $i$:

$$D_1(u)=C^c(S,u)+C^t(t_1,u),\qquad D_i(u)=C^t(t_i,u)+\min_{u'}\big[D_{i-1}(u')+C^c(u',u)\big]$$

End with $\min_u[D_n(u)+C^c(u,S)]$ and back-track. With $K$ candidates per target the cost is $O(nK^2)$. Prosody modification is needed only when no candidate has the right pitch and duration.

> [!example] Three targets, candidates a and b
> $C^t$: a = 1, 2, 1 and b = 3, 1, 2 for $t_1,t_2,t_3$. $C^c$: (S,a)=0, (S,b)=1, (a,a)=2, (a,b)=1, (b,a)=3, (b,b)=0, (a,S)=1, (b,S)=0.
> $D_1(a)=1$, $D_1(b)=4$. $D_2(a)=2+\min(3,7)=5$, $D_2(b)=1+\min(2,4)=3$. $D_3(a)=1+\min(7,6)=7$, $D_3(b)=2+\min(6,3)=5$. End: $\min(7+1,5+0)=5$. Best sequence: (a, b, b).

> [!question]- Check (slide 20): In the diagram, what are the top row and the bottom row of boxes? Which arrows carry the target cost and which the concatenation cost? Why can the target cost alone not choose the units?
> Top row: the targets $t_{i-1},t_i,t_{i+1}$, the specification from the front-end. Bottom row: the units chosen from the database.
> Vertical arrows: target cost $C^t(t_i,u_i)$, between one target and one candidate. It says how different the candidate is from what was asked.
> Horizontal arrows: concatenation cost $C^c(u_{i-1},u_i)$, between two neighbouring units. It says how good the joint is. The bullet above the diagram prints it once with a lowercase letter, $c^c$. It is the same cost.
> The target cost looks at each unit alone. Two units can both match their targets well and still join badly. So the sum of both costs is minimised.

> [!question]- Check (slide 21): Three targets. Path P has target costs 2, 1, 3 and join costs $C^c(S,u_1)=1$, then 0, 0, and $C^c(u_3,S)=1$. Path Q has target costs 1, 1, 1 and join costs 1, 3, 2, 1. Which path wins? How many terms does the total cost have for $n=3$?
> Terms for $n=3$: 3 target costs, 2 joins between units, 2 joins with silence. That is 7 terms.
> Path P: $(2+1+3)+(1+0+0+1)=6+2=8$.
> Path Q: $(1+1+1)+(1+3+2+1)=3+7=10$.
> P wins with cost 8, although each of its units matches its target less well. Its joins cost nothing, as for units that were consecutive in the recording.
> Because the join terms link neighbours, the best unit cannot be chosen target by target. The whole sequence is searched with Viterbi.

## 7. Costs and the speech corpus (slides 22-24)
![[cs568_lecture_18_l18_p022.jpg]]
![[cs568_lecture_18_l18_p023.jpg]]
![[cs568_lecture_18_l18_p024.jpg]]

**Notes.** Both costs are weighted sums of sub-costs:

$$C^t(t_i,u_i)=\sum_{j=1}^{p}w_j^t\,C_j^t(t_i,u_i),\qquad C^c(u_{i-1},u_i)=\sum_{j=1}^{q}w_j^c\,C_j^c(u_{i-1},u_i)$$

Target features: symbolic (phonetic context, stress) and numerical (pitch, duration). The weights are trained per voice. Concatenation features: spectral (formants, LP spectra, LSFs, MFCCs) with Euclidean or Mahalanobis distance. **The concatenation cost is zero for units that were consecutive in the corpus**, so the search prefers long natural stretches.

**Zipf's law**: the frequency of an item is roughly inversely proportional to its rank, $f(r)\propto1/r$. Speech is made of many rare events. Covering a random English sentence with probability 0.75 needs about 150,000 diphone units (5 hours). Commercial systems use 1 to 10 hours. Data sparsity is the main problem, so a complete diphone set is kept as safety units: quality is then never worse than a fixed inventory.

> [!warning] Slide wording
> Slide 24 says the frequency is "roughly proportional to its rank". Zipf's law is inverse: $f(r)\propto1/r$, as in our notes.

> [!warning] Watch out
> The zero join cost can beat a better target match. Too much weight on joins gives fluent speech with wrong stress. Too much weight on targets gives choppy speech.

> [!warning] Slide typesetting
> On slide 23 the limits of the sum are printed together under the sum sign as "$j=1q$". This is a typesetting slip. It means $j=1$ to $q$, as in the formula above.

> [!question]- Check (slide 22): The target cost has $p=3$ sub-costs: phonetic context, pitch, duration, with weights $w^t=(2,5,1)$. Candidate A has sub-costs $(1,\ 0.2,\ 0.5)$. Candidate B has $(0,\ 0.8,\ 0.5)$. Which has the lower target cost? Where do the weights and the target feature values come from?
> A: $2\cdot1+5\cdot0.2+1\cdot0.5=2+1+0.5=3.5$.
> B: $2\cdot0+5\cdot0.8+1\cdot0.5=0+4+0.5=4.5$.
> A is better, although its phonetic context is wrong. With these weights a pitch error counts more.
> The weights are trained when the voice is built, so that the cost follows the perceptual distance. The target feature values are predicted by the front-end. The candidate's values are stored with the unit.

> [!question]- Check (slide 23): The previous unit is fixed. Candidate X followed it directly in the corpus and has target cost 2.0. Candidate Y is from another sentence: target cost 0.9, join sub-costs 0.4 (spectral distance) and 0.1 (second feature), weights $w^c=(3,2)$. Which is chosen? Which risk does the last bullet of the slide name?
> X: its concatenation cost is 0, because the two units were consecutive in the corpus. Total $2.0+0=2.0$.
> Y: $C^c=3\cdot0.4+2\cdot0.1=1.2+0.2=1.4$. Total $0.9+1.4=2.3$.
> X is chosen ($2.0<2.3$). The search keeps the natural stretch of speech.
> Risk: the free join wins even though X matches the target less well. If target costs get too high in this way, intelligibility suffers.

> [!question]- Check (slide 24): Use Zipf's law in its correct form. The most frequent item of a corpus occurs 6000 times. About how often do the items of rank 10 and rank 100 occur? Then compare the 150,000 units of this slide with the diphone inventory of slide 11.
> $f(r)\propto1/r$, so $f(10)\approx6000/10=600$ and $f(100)\approx6000/100=60$.
> A few items are very frequent and very many are rare. Any sentence is likely to need some rare item, so the corpus must be huge.
> Slide 11: 1,500 units in 3 minutes. Here: 150,000 units in 5 hours $=300$ minutes. Both numbers are $100$ times larger ($150000/1500=100$, $300/3=100$).
> Even then a random sentence is fully covered only with probability 0.75. This is data sparsity, the reason for the safety units.

## 8. HMM-based synthesis (slides 25-29)
![[cs568_lecture_18_l18_p025.jpg]]
![[cs568_lecture_18_l18_p026.jpg]]
![[cs568_lecture_18_l18_p027.jpg]]
![[cs568_lecture_18_l18_p028.jpg]]
![[cs568_lecture_18_l18_p029.jpg]]

**Notes.** HMMs are used as generators. Each phoneme is a 3-state HMM. Each state emits spectral vectors (MFCCs) from a GMM at a leaf of a context-clustering tree. Pitch and duration have their own trees. Tying lets the system handle unseen contexts, which unit selection cannot.

Generation: the front-end gives the phonemes with stress and syntax. The context-dependent HMMs are joined into a sentence HMM. State durations are chosen to maximise their probability. Then the spectral parameters are generated.

Taking the mean of each Gaussian frame by frame gives a staircase, which contradicts the $\Delta$ and $\Delta^2$ the model also predicts. The fix is the **trajectory HMM** (formula from our notes). Stack the static features of all frames in $\mathbf c$. The full observation is $\mathbf o=W\mathbf c$, where $W$ computes the dynamic features. With mean $\boldsymbol\mu$ and covariance $U$ from the state sequence:

$$\mathbf c^{*}=\big(W^{\mathsf T}U^{-1}W\big)^{-1}W^{\mathsf T}U^{-1}\boldsymbol\mu$$

> [!example] Two frames
> Static means 0 and 1, one delta $\Delta c=c_2-c_1$ with mean 0, all variances 1. The system is $2c_1-c_2=0$, $-c_1+2c_2=1$, so $\mathbf c^*=(1/3,\ 2/3)$: smoother than the staircase $(0,1)$.

Performance: smooth but poor voice quality (buzzy from the simple excitation, muffled from averaging). Advantages over unit selection: flexibility, better coverage, about 1 MB footprint, new voices from 5 to 10 minutes of data with adaptation, a natural frame for voice conversion. Slide 29 is a MATLAB TTS demo (TTSBOX).

> [!info] Reading the system diagram of slide 26 (beyond the slides)
> The dashed line splits the figure into a training part (done once) and a synthesis part (done for every sentence).
> - **Training.** Two extractors work on the speech database. Excitation parameter extraction gives $F_0$. Spectral parameter extraction gives the mel-cepstrum (the spectral envelope). With the labels (phonemes and their context) they train the context-dependent HMMs.
> - **Synthesis.** Text analysis (the front-end) turns the text into labels. The labels select the HMMs. "Parameter generation from HMM" gives an $F_0$ track and a mel-cepstrum track. Excitation generation makes the source signal from $F_0$ (pulses for voiced frames, noise for unvoiced frames). The MLSA filter (mel log spectrum approximation) shapes the source with the spectral envelope of the mel-cepstrum.
>
> The last two blocks are a vocoder (ses kodlayıcı): they build the waveform from parameters. This is the source-filter model again. The excitation is the source and the MLSA filter is the vocal tract.
> No recorded waveform is used at synthesis time. Only the trained models are stored. This is why the footprint is small, and why the voice quality depends on the excitation model.
> "Context clustering tree" on slide 25 means a decision tree (karar ağacı). Its yes/no questions about the context ("is the next phone a nasal?") send every context to a leaf. All contexts in one leaf share one GMM.

> [!question]- Check (slide 25): How is the HMM used differently in recognition and in synthesis? A word has 5 phonemes: how many HMM states does it use? Why can HMM synthesis handle a phonetic context that never occurred in the training data?
> Recognition: the observations are given, and the most likely models are searched. Synthesis: the models are given (chosen by the front-end), and the most likely observations are generated.
> States: each phoneme is a three-state HMM, so $5\times3=15$ states.
> Unseen context: the parameters are tied by context clustering trees. The questions of the tree send the new context to a leaf that was trained on similar contexts, so a GMM is always available.
> Unit selection can only play units that exist in its corpus.

> [!question]- Check (slide 26): In the diagram, which two parameter streams are extracted in training, and where do they appear again in synthesis? Which block makes the source signal and which block applies the spectral envelope? Is the speech database used below the dashed line?
> Streams: $F_0$ (from excitation parameter extraction) and the mel-cepstrum (from spectral parameter extraction).
> In synthesis the block "Parameter generation from HMM" outputs the same two streams.
> $F_0$ goes to "Excitation generation", which makes the source signal. The mel-cepstrum goes to the MLSA filter, which applies the spectral envelope to the source. The output is the synthesised speech.
> The speech database is used only above the line. Below it only the context-dependent HMMs are used, selected by the labels from text analysis.

> [!question]- Check (slide 27): Two frames, all variances 1. The static means are 2 and 5. The delta $\Delta c=c_2-c_1$ has mean 1. Find the generated $c_1,c_2$ and compare them with the "mean of each Gaussian" solution.
> Maximising the likelihood here means minimising $(c_1-2)^2+(c_2-5)^2+(c_2-c_1-1)^2$.
> Derivative for $c_1$: $(c_1-2)-(c_2-c_1-1)=0$, so $2c_1-c_2=1$.
> Derivative for $c_2$: $(c_2-5)+(c_2-c_1-1)=0$, so $-c_1+2c_2=6$.
> Solution: $c_1=8/3\approx2.67$, $c_2=13/3\approx4.33$. Their difference is $5/3\approx1.67$.
> The means alone give $(2,5)$ with a jump of 3, which ignores the delta mean of 1. The generated values are a compromise between the static means (jump 3) and the delta (jump 1). So the trajectory is smoother.

> [!question]- Check (slide 28): Choose unit selection or HMM synthesis for each case and give the reason from the slides. (a) A device with 2 MB of storage. (b) A new voice from 8 minutes of recordings. (c) Flight announcements, where the most natural sound is wanted.
> (a) HMM synthesis. Its footprint is about 1 MB. A unit-selection corpus needs 150 to 1500 MB (slide 24).
> (b) HMM synthesis with adaptation. A new voice needs only 5 to 10 minutes. Unit selection needs 1 to 10 hours of one speaker.
> (c) Unit selection. This is a limited domain, and slide 24 says unit selection is the best option there. It plays real recordings, while HMM speech is smooth but of poor voice quality.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Rule-based / corpus-based | expert rules drive a model / speech taken or learned from recordings |
| Formant resonator | $r=e^{-\pi BT}$, $\theta=2\pi FT$, two poles at $re^{\pm j\theta}$ |
| Series / parallel | one gain, vowels / gain per filter, any spectrum |
| Diphone | middle of one phone to middle of the next; at most $N^2$ |
| Smoothing | fixes phase, pitch and spectral envelope mismatches |
| Total cost | $\sum C^t+\sum C^c+C^c(S,u_1)+C^c(u_n,S)$, Viterbi, $O(nK^2)$ |
| Join rule | $C^c=0$ for units consecutive in the corpus |
| Zipf's law | $f(r)\propto1/r$ |
| Safety units | a complete diphone set as fall-back |
| Trajectory HMM | $\mathbf c^*=(W^{\mathsf T}U^{-1}W)^{-1}W^{\mathsf T}U^{-1}\boldsymbol\mu$ |

## Check yourself
> [!question]- 1. A formant has $F=1500$ Hz, $B=90$ Hz at $f_s=8$ kHz. Find $r$, $\theta$ and $H(z)$.
> $r=e^{-\pi\cdot90/8000}=e^{-0.03534}\approx0.9653$. $\theta=2\pi\cdot1500/8000=3\pi/8\approx1.178$ rad, $\cos\theta\approx0.3827$. $2r\cos\theta\approx0.7388$, $r^2\approx0.9318$, gain $1.1930$. $H(z)\approx\dfrac{1.1930}{1-0.7388z^{-1}+0.9318z^{-2}}$.

> [!question]- 2. Series or parallel resonators for the fricative /s/? Why?
> Parallel. A fricative spectrum has resonances and zeros, which an all-pole cascade cannot match. The parallel bank has a gain per resonance.

> [!question]- 3. List the diphones of "cat" /k ae t/ with silence on both sides. What is the maximum number of diphones for 30 phonemes?
> _k, k-ae, ae-t, t_: 5 phones give 4 diphones. Maximum $30^2=900$.

> [!question]- 4. In the example of section 6, change $C^c(b,b)$ from 0 to 3. Which sequence wins?
> $D_2(a)=5$, $D_2(b)=1+\min(2,7)=3$. $D_3(a)=1+\min(7,6)=7$, $D_3(b)=2+\min(6,6)=8$. End: $\min(8,8)=8$. Three sequences tie at cost 8: (a,b,a), (a,a,b), (a,b,b).

> [!question]- 5. Why do unit-selection systems keep a complete diphone set?
> Data sparsity: a needed unit may be missing from the corpus. The diphone set is a fall-back, so quality is never worse than a fixed-inventory system.

> [!question]- 6. Name two reasons why HMM synthesis sounds buzzy or muffled.
> A simple pulse/noise excitation (buzzy), and statistical averaging that over-smooths the spectra (muffled).

## Links
- Course index: [[CS568 Home]]
- Previous lecture: [[CS568 L17 - Speech synthesis front-end]]
- Next lecture (TD-PSOLA, HNM): [[CS568 L19 - Prosodic modification of speech]]
- Quizzes and labs that use this lecture: none (the midterm booklet covers Lectures 1 to 6).
- Full step-by-step notes (PDF): [CS568_Lecture_18_TTS_Back_End.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_18/CS568_Lecture_18_TTS_Back_End.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_18/CS568_Lecture_18_TTS_Back_End.pdf`
- Original slides (PDF): [l18.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_18/l18.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_18/l18.pdf`

Slides not embedded: 1
