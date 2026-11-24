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

## 2. Articulatory synthesis (slides 3-5)
![[cs568_lecture_18_l18_p003.jpg]]
![[cs568_lecture_18_l18_p004.jpg]]
![[cs568_lecture_18_l18_p005.jpg]]

**Notes.** Mechanical and acoustic models of the speech organs. The control parameters describe production, not the signal: geometry and dynamics of jaw, tongue, lips, velum and glottis, muscle forces and timings. Slide 4: an articulatory model gives the vocal-tract area function, and from it the transfer function.

The output is intelligible but far from natural. Every sub-model would have to be very accurate for one speaker, but they rest on expert guesses, and articulatory data is costly and invasive to collect.

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

## 4. Concatenative synthesis and diphones (slides 10-12)
![[cs568_lecture_18_l18_p010.jpg]]
![[cs568_lecture_18_l18_p011.jpg]]
![[cs568_lecture_18_l18_p012.jpg]]

**Notes.** Concatenative synthesis glues recorded chunks and so keeps natural coarticulation, shimmer, jitter and inharmonic content. Transitions matter most for intelligibility, so keep them whole and join in the stable parts.

- **Fixed inventory**: one sample per unit, plus prosody modification (some signal damage).
- **Unit selection**: several instances per unit, pick the best at run time.

A **diphone** runs from the middle of one phone to the middle of the next. A language with $N$ phonemes needs up to $N^2$ diphones (an upper bound). English: about 1,500 units, about 3 minutes, about 5 MB at 16 kHz and 16 bits.

Building the inventory: list the diphones, write words that contain each at least once, avoid strongly stressed or reduced positions, record a professional speaker with steady pitch, segment (by hand or automatically), store the waveforms.

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
