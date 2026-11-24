---
course: CS 568 Speech Processing
lecture: 8
title: Source estimation
tags: [cs568, lecture]
slides: lectures/lecture_08/l8.pdf
full_notes: lecture_notes/lecture_08/CS568_Lecture_08_Source_Estimation.pdf
---
# CS 568 · Lecture 8 · Source estimation

> [!summary] In short
> - Voiced speech is $Y(z)=U(z)V(z)R(z)$: glottal source, vocal tract, lip radiation. Lecture 7 modelled $V(z)$; this lecture models $U(z)$ and $R(z)$.
> - Lips: $R(z)=1-\alpha z^{-1}$, about $+6$ dB/octave. Glottis: LF model (flow derivative) or Quatieri $1/(1-\beta z)^2$, about $-12$ dB/octave. Net tilt $-6$ dB/octave.
> - The LP residual estimates the excitation, but open-phase LP mixes source and tract.
> - Closed-phase analysis fits LP only where the glottis is shut, inverse filters the whole period, then integrates to get the glottal flow.
> - To do that we need voicing detection (energy, zero crossings), pitch detection (autocorrelation, cepstrum) and epoch detection (glottal closure instants).

## 1. Review: speech as filters in series (slides 2-3)
![[cs568_lecture_08_l8_p002.jpg]]
![[cs568_lecture_08_l8_p003.jpg]]

**Notes.** For vowels, speech is a source passed through filters one after the other:

$$Y(z)=U(z)\,P(z)\,O(z)\,R(z)=U(z)\,V(z)\,R(z),\qquad V(z)=P(z)\,O(z)$$

$U$ is the glottal source, $P$ the pharynx, $O$ the oral cavity, $R$ the radiation at the lips. The source sets the pitch and the overall tilt of the spectrum. The filter $V(z)$ sets the formant peaks. All blocks are linear and time-invariant, so their order can be swapped without changing $Y(z)$. Section 3 uses this.

## 2. Lip radiation (slide 4)
![[cs568_lecture_08_l8_p004.jpg]]

**Notes.** The LP filter relates volume velocity at the lips to that at the glottis. A microphone measures pressure in the far field, which is close to the time derivative of the volume velocity. Model: an FIR filter with one zero,

$$R(z)=1-\alpha z^{-1},\qquad \alpha\in[0.95,\,0.99],\qquad y[n]=s[n]-\alpha\,s[n-1]$$

The same filter applied in software is called **pre-emphasis** (speech recognition front ends).

> [!example] Gain for $\alpha=0.95$
> DC ($z=1$): $|1-0.95|=0.05$, which is $-26.0$ dB. Nyquist ($z=-1$): $1.95$, which is $+5.8$ dB. At $f_s=16$ kHz the gain at 500, 1000, 2000, 4000 Hz is $-14.09$, $-8.32$, $-2.53$, $+2.79$ dB: close to $+6$ dB per doubling.

> [!warning] Slide error: octave, not decade
> The slide says "about 6 dB per decade". A single zero gives $+6$ dB per **octave** (a doubling), which equals $+20$ dB per decade.

## 3. Glottal source models (slides 5-10)
![[cs568_lecture_08_l8_p005.jpg]]
![[cs568_lecture_08_l8_p006.jpg]]
![[cs568_lecture_08_l8_p007.jpg]]
![[cs568_lecture_08_l8_p008.jpg]]
![[cs568_lecture_08_l8_p009.jpg]]
![[cs568_lecture_08_l8_p010.jpg]]

**Notes.** One glottal cycle: folds closed, lung pressure builds, folds open and release it, tension shuts them again. The repetition rate is $F_0$: about 80 to 250 Hz for males, 120 to 400 Hz for females and children. Three phases: **open** (air flows), **return** (folds snap shut, flow drops fast), **closed** (flow is zero).

**LF model** (Liljencrants-Fant). It describes the **derivative** of the glottal flow, piece by piece:

$$u[n]=\begin{cases}0, & 0\le n<T_0\\ E_0\,e^{\alpha(n-T_0)}\sin\!\big[\Omega_0(n-T_0)\big], & T_0\le n<T_e\\ -E_1\big[e^{\beta(n-T_e)}-e^{\beta(T_c-T_e)}\big], & T_e\le n<T_c\end{cases}$$

$T_0$ is glottal opening, $T_e$ the negative minimum, $T_c$ glottal closure. $\alpha,\beta,\Omega_0$ set the shape, $E_0,E_1$ the heights. At $n=T_c$ the bracket is zero, so the curve joins the closed phase with no jump.

**Quatieri model.** The glottal flow is two time-reversed exponential decays convolved ($v[n]$ is the unit step):

$$u[n]=\big(\beta^{-n}v[-n]\big)*\big(\beta^{-n}v[-n]\big),\qquad U(z)=\frac{1}{(1-\beta z)^2},\quad \beta\approx0.95$$

Each factor is anti-causal (pole at $z=1/\beta=1.053$, outside the unit circle), so the pulse rises slowly and ends abruptly. It is a low-pass filter applied to an impulse stream. Zeros can be added in the numerator, $\prod_{k=1}^{M}(1-u_kz^{-1})$, for more realism.

**Combining.** Because the blocks are LTI, $UVR=(UR)V$. $U(z)R(z)$ is the **glottal-flow derivative**, the effective excitation. Its main feature is one large negative impulse at closure, so each pitch period of the output is close to the impulse response of the vocal tract. Net spectral tilt: $-12+6=-6$ dB/octave. Pre-emphasis roughly cancels it. Glottal shape depends on the speaker, so it is useful for speaker recognition.

> [!warning] Watch out
> - The slide says "$-12$ dB/decade" for the glottal roll-off. A double pole gives $-12$ dB per **octave** ($-40$ dB/decade).
> - The slide spells the name "Lijencrants". It is Liljencrants.
> - The LF $\alpha,\beta$ are shape parameters. They are not the radiation $\alpha$ or the Quatieri $\beta$.
> - The flow is a smooth positive hump. The LF curve is its derivative, with a sharp negative spike at $T_e$. The residual and the epoch detectors see the spike.

## 4. The residual and why it is not the glottal source (slides 11-14)
![[cs568_lecture_08_l8_p011.jpg]]
![[cs568_lecture_08_l8_p012.jpg]]
![[cs568_lecture_08_l8_p013.jpg]]
![[cs568_lecture_08_l8_p014.jpg]]

**Notes.** LP model from Lecture 7: $y[n]=x[n]+\sum_{k=1}^{p}a_k\,y[n-k]$, with $x[n]$ the excitation. Once the $a_k$ are known, solve for $x[n]$ (**inverse filtering**, an FIR filter):

$$x[n]=y[n]-\sum_{k=1}^{p}a_k\,y[n-k]$$

The output $x[n]$ is the **residual**. LP over the whole frame is called **open-phase** analysis. Its residual is near zero except at the instants of glottal excitation.

Model the source as an impulse train $I(z)$ through a glottal filter $G(z)$:

$$U(z)=I(z)\,G(z)=\frac{\sum_{k=0}^{M}b_k^{G}z^{-k}}{1-\sum_{l=1}^{N}a_l^{G}z^{-l}}\;I(z)$$

Then $U(z)V(z)R(z)$ has zeros (glottal numerator, radiation) and poles (glottal denominator, vocal tract). LP fits one all-pole $H(z)$ to all of it, so the open-phase residual does not separate source and filter.

> [!example] Residual by hand
> $p=1$, $a_1=0.9$, $y[n]=0.9^n$ for $n\ge0$. Then $x[0]=1$, $x[1]=0.9-0.9\cdot1=0$, and all later samples are 0. The residual is $\delta[n]$: the filter is removed and only the excitation is left.

> [!warning] Slide notation
> The slide writes the residual as $\sum_{k=0}^{p}a_k\,y[n-k]$. That holds only with $a_0=1$ and the other signs flipped. On slide 13 the glottal filter is a ratio of sums (direct form), as written above.

## 5. Closed-phase analysis (slides 15-17)
![[cs568_lecture_08_l8_p015.jpg]]
![[cs568_lecture_08_l8_p016.jpg]]
![[cs568_lecture_08_l8_p017.jpg]]

**Notes.** Idea: use only the samples where the folds are closed. There $G(z)$ contributes nothing and the signal contains only $V(z)R(z)$. Remove $R(z)$ with an IIR integrator:

$$x[n]=y[n]+\alpha\,x[n-1],\qquad X(z)=\frac{Y(z)}{1-\alpha z^{-1}}=\frac{Y(z)}{R(z)}$$

Steps:
1. Locate the glottal closure instants (section 8).
2. Fit an all-pole model on the closed-phase samples only (covariance method, works on short segments). This is $V_{CP}(z)$.
3. Inverse filter the **whole** pitch period with $V_{CP}(z)$.
4. Integrate to undo the radiation. The result estimates the glottal flow.

Slide 16 shows model flow, model derivative, real speech and the closed-phase residual: all have a sharp negative peak at closure. Slide 17 is the full production model: impulse train (pitch period $P$) through $G(z)$ for voiced, noise for unvoiced, then vocal tract $H(z)$ and radiation $R(z)$.

> [!warning] Watch out
> The integrator pole is at $z=\alpha$. It is stable only for $\alpha<1$. With $\alpha=1$ a DC offset grows without bound ($x[n]=n+1$ for input 1); with $\alpha=0.95$ it settles at $1/(1-\alpha)=20$.

## 6. Voiced/unvoiced detection (slides 18-21)
![[cs568_lecture_08_l8_p018.jpg]]
![[cs568_lecture_08_l8_p019.jpg]]
![[cs568_lecture_08_l8_p020.jpg]]
![[cs568_lecture_08_l8_p021.jpg]]

**Notes.** Three problems in order: voicing, pitch, epochs. For voicing, split $x[n]$ into blocks of 10 to 20 ms ($L$ samples) and compute two features:

$$P_{av}=\frac{1}{L}\sum_{n=1}^{L}x^2[n],\qquad Z=\frac{1}{L}\sum_{n=1}^{L}\big|\operatorname{sign}(x[n])-\operatorname{sign}(x[n-1])\big|$$

Voiced speech has higher power, and its energy sits below about 3 kHz, so it has few zero crossings. Unvoiced speech has low power and many crossings. The threshold is a maximum-likelihood one: fit a density to each class and cut where the two likelihoods are equal (Lecture 11). The histograms on slide 20 are centred near 13 crossings per 10 ms (voiced) and 49 (unvoiced).

> [!example] Two blocks, $L=8$
> A $=[2,1,-1,-2,-1,1,2,1]$, previous sample 1: $P_{av}=17/8=2.125$, 2 sign changes, $Z=4/8=0.5$. Voiced.
> B $=[0.3,-0.2,0.1,-0.3,0.2,-0.1,0.3,-0.2]$, previous sample $-0.1$: $P_{av}=0.41/8=0.05125$, 8 sign changes, $Z=2$. Unvoiced.

> [!warning] Watch out
> Each sign change adds 2 to the sum in $Z$ (many texts use $\frac{1}{2L}$). Silence has low energy **and** low zero-crossing rate, so one feature alone does not separate silence, voiced and unvoiced. Use both.

## 7. Pitch detection (slides 22-23)
![[cs568_lecture_08_l8_p022.jpg]]
![[cs568_lecture_08_l8_p023.jpg]]

**Notes.** Finding $F_0$ is pitch detection; the algorithm is a PDA. Four families:
- **Cepstrum**: pitch shows as a peak (Lecture 9).
- **Autocorrelation**: $R[k]=\sum_n x[n]\,x[n+k]$ peaks at the lag of one period, $f_s/F_0$ samples. So $F_0=f_s/\text{lag}$.
- **Frequency domain**: DFT harmonics are spaced $F_0$ apart.
- **Residual analysis**: the LP residual has little vocal-tract information, so the periodicity is clearer.

Accurate tools: Talkin's `get_f0()` and RAPT; PRAAT and SFS include PDAs.

> [!example] Autocorrelation PDA
> $f_s=16$ kHz, search 60 to 400 Hz: lags from $16000/400=40$ to $16000/60\approx267$ samples. Largest peak at lag 128: $F_0=16000/128=125$ Hz.

> [!warning] Octave errors
> A periodic signal also matches itself after two periods. If that peak wins, the PDA reports $F_0/2$. Raw PDA output also has spurious values in unvoiced regions. So it is smoothed and interpolated (slide 23).

## 8. Epoch detection (slides 24-26)
![[cs568_lecture_08_l8_p024.jpg]]
![[cs568_lecture_08_l8_p025.jpg]]
![[cs568_lecture_08_l8_p026.jpg]]

**Notes.** An epoch detection algorithm (EDA) finds one instant per period, the **pitch mark** or **epoch**. Usually it is the **instant of glottal closure** (IGC): the large negative spike in the glottal-flow derivative. Closed-phase analysis needs the IGCs. Finding them from the speech signal is hard (tool: DYPSA in VOICEBOX). An alternative is a **laryngograph** (electroglottograph, EGG): it measures the electrical impedance across the larynx, high when the glottis is open, low when closed. The Lx signal measures fold contact, not air flow, but it makes the IGCs easy to see (slide 25).

Pitch detection gives one number per frame. Epoch detection gives one time instant per period.

MATLAB examples of the lecture: `ex8p1.m` (voicing), `ex8p2.m` (pitch), `ex8p3.m` (pitch marking), `ex8p4.m` (closed-phase analysis).

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Speech chain | $Y(z)=U(z)V(z)R(z)$: glottis, vocal tract, lips |
| Radiation, pre-emphasis | $R(z)=1-\alpha z^{-1}$, $\alpha\in[0.95,0.99]$, $+6$ dB/octave |
| Glottal cycle | open, return, closed phases; $F_0$ 80 to 250 Hz (male), 120 to 400 Hz (female, child) |
| LF model | piecewise model of the glottal-flow derivative; sharp negative peak at $T_e$ |
| Quatieri model | $U(z)=1/(1-\beta z)^2$, $\beta\approx0.95$, $-12$ dB/octave |
| Residual | $x[n]=y[n]-\sum_k a_k\,y[n-k]$ (inverse filter output) |
| Closed-phase analysis | LP on closed phase gives $V_{CP}(z)$; inverse filter whole period; integrator $x[n]=y[n]+\alpha x[n-1]$ |
| Voicing | $P_{av}$ high and $Z$ low means voiced |
| Pitch (autocorrelation) | $F_0=f_s/\text{lag}$ |
| Epoch | instant of glottal closure, from the residual spike or a laryngograph |

## Check yourself
> [!question]- 1. Pre-emphasis $R(z)=1-0.97z^{-1}$: difference equation and DC gain in dB?
> $y[n]=x[n]-0.97\,x[n-1]$. For $x[n]=1$: $y=0.03$, and $20\log_{10}0.03=-30.5$ dB. DC is almost removed.

> [!question]- 2. Where is the pole of one Quatieri factor for $\beta=0.95$? Causal or anti-causal?
> $1-0.95z=0$ gives $z=1.0526$, outside the unit circle. The region of convergence $|z|<1.0526$ contains the unit circle, so the factor is anti-causal.

> [!question]- 3. Net tilt of source plus radiation, and the ideal drop from 500 Hz to 4 kHz?
> $-12+6=-6$ dB/octave. 500 to 4000 Hz is 3 octaves: $-18$ dB.

> [!question]- 4. Why does closed-phase analysis inverse filter the whole period and not only the closed phase?
> The closed phase is used only to estimate the tract filter $V_{CP}(z)$ without the source. The source itself lives in the open and return phases, so the inverse filter must run over the whole period to show it.

> [!question]- 5. At $f_s=8$ kHz the autocorrelation peak is at lag 40. $F_0$? Which lag gives an octave error?
> $F_0=8000/40=200$ Hz. Half the pitch is 100 Hz, period $8000/100=80$ samples, twice the true lag.

> [!question]- 6. Residual spikes at samples 194, 322, 450 with $f_s=16$ kHz. Epochs, period, $F_0$?
> Epochs: 12.125, 20.125, 28.125 ms. Spacing 128 samples, which is 8 ms. $F_0=16000/128=125$ Hz.

## Links
- Course index: [[CS568 Home]]
- Full step-by-step notes (PDF): [CS568_Lecture_08_Source_Estimation.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_08/CS568_Lecture_08_Source_Estimation.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_08/CS568_Lecture_08_Source_Estimation.pdf`
- Original slides (PDF): [l8.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_08/l8.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_08/l8.pdf`

Slides not embedded: 1
