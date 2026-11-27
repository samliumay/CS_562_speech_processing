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

> [!info] What "source estimation" means (beyond the slides)
> The source is everything that is not the vocal tract shape. To estimate it for one frame we need three things: is the frame voiced (ötümlü) or unvoiced (ötümsüz), what is the fundamental frequency $F_0$ if it is voiced, and what is the shape of one glottal pulse. Sections 2 to 5 deal with the pulse shape, sections 6 to 8 with voicing, $F_0$ and the closure instants.

> [!question]- Check (slide 2): In $Y(z)=U(z)V(z)R(z)$, say what each factor is. Which factor sets the pitch, which one sets the formant peaks, and which two does this lecture model?
> $U(z)$ is the glottal source, $V(z)=P(z)O(z)$ is the vocal tract (pharynx and oral cavity together), $R(z)$ is the radiation at the lips.
> The pitch comes from $U(z)$: it is the repetition rate of the glottal pulses. The formant peaks come from $V(z)$.
> This lecture models $U(z)$ and $R(z)$. Lecture 7 modelled $V(z)$.

> [!question]- Check (slide 3): The figure adds three curves (the "+" signs) to get the output spectrum. Why can they be added, when the formula multiplies $U$, $V$ and $R$? What changes in the left output spectrum if $F_0$ doubles and the mouth shape stays the same?
> The curves are drawn on a log (dB) scale. A product of magnitudes becomes a sum of logs.
> With double $F_0$ the vertical lines (harmonics) are twice as far apart, so there are half as many lines below 2 kHz. The envelope over the lines (vocal tract gain plus source slope plus radiation slope) stays the same, so the formant peaks stay where they are.
> The right-hand "continuous" case is the unvoiced one: a noise source has no lines, so the output is a smooth curve with the same formant shape.

## 2. Lip radiation (slide 4)
![[cs568_lecture_08_l8_p004.jpg]]

**Notes.** The LP filter relates volume velocity at the lips to that at the glottis. A microphone measures pressure in the far field, which is close to the time derivative of the volume velocity. Model: an FIR filter with one zero,

$$R(z)=1-\alpha z^{-1},\qquad \alpha\in[0.95,\,0.99],\qquad y[n]=s[n]-\alpha\,s[n-1]$$

The same filter applied in software is called **pre-emphasis** (speech recognition front ends).

> [!example] Gain for $\alpha=0.95$
> DC ($z=1$): $|1-0.95|=0.05$, which is $-26.0$ dB. Nyquist ($z=-1$): $1.95$, which is $+5.8$ dB. At $f_s=16$ kHz the gain at 500, 1000, 2000, 4000 Hz is $-14.09$, $-8.32$, $-2.53$, $+2.79$ dB: close to $+6$ dB per doubling.

> [!warning] Slide error: octave, not decade
> The slide says "about 6 dB per decade". A single zero gives $+6$ dB per **octave** (a doubling), which equals $+20$ dB per decade.

> [!question]- Check (slide 4): In $R(z)=1-\alpha z^{-1}$, what does $z^{-1}$ do, where is the zero, and why is this filter called "a derivative"? Why is the radiation filter needed at all, if the LP model of Lecture 7 already describes the vocal tract?
> $z^{-1}$ is a delay of one sample, so the filter computes $y[n]=s[n]-\alpha\,s[n-1]$. The zero is at $z=\alpha$, on the real axis just inside the unit circle, close to $z=1$ (DC).
> With $\alpha=1$ it is the first difference $s[n]-s[n-1]$, the discrete form of a time derivative. A slowly changing signal gives almost zero output, a fast changing one gives a large output: a high-frequency boost.
> The LP model relates volume velocity at the lips to volume velocity at the glottis. A microphone in the far field measures pressure, and that pressure is close to the derivative of the lip volume velocity. $R(z)$ is this last step.

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

> [!question]- Check (slide 5): A speaker has $F_0=220$ Hz and the signal is sampled at $f_s=16$ kHz. Give the pitch period in ms and in samples. What range of pitch periods does the male range 80 to 250 Hz on the slide correspond to?
> Pitch (perde) period $T=1/F_0=1/220\ \text{s}=4.55$ ms. In samples: $f_s/F_0=16000/220\approx72.7$ samples.
> Male range: $1/80=12.5$ ms down to $1/250=4$ ms. A high $F_0$ means a short period.

> [!question]- Check (slide 6): In the figure, put the three phases in time order and say which one is the shortest. In which phase is the slope of the flow the most negative, and why does that matter?
> Order: open phase, return phase, closed phase, then the next open phase. The return phase is the shortest (the smallest arrow under the first pulse).
> In the open phase the flow rises slowly to its maximum. In the return phase it falls to zero very fast, so the slope is large and negative there. In the closed phase the flow is flat at zero.
> The radiation differentiates the flow, so the vocal tract is really driven by the slope. The strongest excitation therefore happens when the folds snap shut, not when the flow is largest.

> [!question]- Check (slide 7): In the LF formula, which line draws which part of one period, and what do $E_0$, $E_1$, $\Omega_0$ and $\alpha$ do in the second and third lines?
> Line 1 ($0\le n<T_0$): zero. The glottis is still closed.
> Line 2 ($T_0\le n<T_e$): a sine with frequency $\Omega_0$ whose amplitude grows as $e^{\alpha(n-T_0)}$, scaled by $E_0$. It gives the positive hump and then the dive to the negative minimum at $T_e$. The growing exponential makes the negative part deeper than the positive part.
> Line 3 ($T_e\le n<T_c$): an exponential recovery from the minimum back to zero, scaled by $E_1$ and with rate $\beta$. It models the return phase.
> $E_0$ and $E_1$ set heights, $\alpha$, $\beta$, $\Omega_0$ set the shape. $u[n]$ is the flow derivative, not the flow.

> [!question]- Check (slide 8): In the right-hand plot, read off about where $T_0$, $T_e$ and $T_c$ are. The curve crosses zero at about 50 ms: what does the glottal flow (left plot, trace a) do at that instant?
> $T_0$ is at about 16 ms (the curve leaves zero), $T_e$ at about 59 ms (the negative minimum, about $-1.75$), $T_c$ at about 66 ms (the curve is back at zero).
> The plotted curve is the derivative of the flow. Where the derivative is zero and goes from positive to negative, the flow has its maximum. So at about 50 ms the flow is at its peak. After that the flow falls, fastest at $T_e$.
> Left plot: trace (a) is the smooth positive flow, trace (b) is its derivative with the sharp negative "glottal pulse" in the return phase.

> [!question]- Check (slide 9): Find the mistakes: "The Quatieri model $U(z)=1/(1-\beta z)^2$ has two zeros inside the unit circle, so the glottal pulse starts abruptly and then decays slowly."
> Mistake 1: it has no zeros. It has a double pole at $z=1/\beta$, which is outside the unit circle for $\beta\approx0.95$.
> Mistake 2: the shape is the other way round. Each factor is a time-reversed (anti-causal) decay, $\beta^{-n}v[-n]$, which grows slowly up to $n=0$ and then stops. So the pulse rises slowly and ends abruptly, like the real flow that closes fast.
> The model is still a low-pass filter: two poles give a roll-off of about $-12$ dB per octave. Zeros appear only in the more realistic second formula, as the numerator $\prod_k(1-u_kz^{-1})$.

> [!question]- Check (slide 10): The radiation happens at the lips, after the vocal tract. Why is it allowed to apply $R(z)$ to the glottal flow before the vocal tract, and what do we learn from doing so?
> All three blocks are linear and time-invariant, and LTI filters in series commute: $U\,V\,R=(U\,R)\,V$. The output is the same.
> $U(z)R(z)$ is the differentiated glottal flow, the glottal-flow derivative. Its main feature is one large negative spike per period, at closure.
> So the vocal tract is excited by something close to one impulse per period. Each pitch period of speech is then close to the impulse response of the vocal tract, which is why an all-pole filter driven by impulses is a good model of voiced speech.

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

> [!question]- Check (slide 11): The synthesis model is $y[n]=x[n]+\sum_k a_k y[n-k]$ and the inverse filter is $x[n]=y[n]-\sum_k a_k y[n-k]$. Which one is IIR and which one is FIR, and why? Then compute the residual for $p=2$, $a_1=1.2$, $a_2=-0.5$, $y=[2,\,1,\,-1]$ (zero before $n=0$).
> The synthesis filter feeds back its own past outputs, so it is IIR (all-pole, $1/A_p(z)$). The inverse filter uses only the present and past values of its input $y$, with no feedback, so it is FIR (all-zero, $A_p(z)=1-\sum_k a_kz^{-k}$). Its zeros cancel the poles of the model.
> $x[0]=2$.
> $x[1]=1-1.2\cdot2=-1.4$.
> $x[2]=-1-1.2\cdot1-(-0.5)\cdot2=-1-1.2+1=-1.2$.
> In the block diagram, $y[n]$ goes in, the coefficients $\{a_i\}$ set the filter, and the residual $e[n]$ comes out.

> [!question]- Check (slide 12): In the lower plot, where are the two clear residual spikes inside the frame, and what does their spacing tell us? Why is there also a large spike at the very start of the frame?
> The spikes are at about sample 113 and about sample 230, a few samples before the large peaks of the speech in the upper plot (about 117 and 233). The spacing is about 117 samples, which is one pitch period. The slide gives no sampling rate, so $F_0$ in Hz cannot be read off. Only as an example: if $f_s$ were 16 kHz, it would be $16000/117\approx137$ Hz.
> Between the spikes the residual is almost zero: there the samples are well predicted from the past $p$ samples. At a glottal closure a new excitation arrives that the past cannot predict, so the error is large.
> At the start of the frame the predictor has no past samples (they are outside the frame), so the first few samples are badly predicted. That spike is an edge effect, not a glottal event.
> Note the scales: the residual (about $\pm0.005$) is much smaller than the speech (about $\pm0.05$).

> [!question]- Check (slides 13-14): List where the zeros and the poles of $U(z)V(z)R(z)$ come from. Then find the mistake: "The residual of an LP analysis over the whole frame is the glottal flow, because LP removes the vocal tract."
> Zeros: the numerator of the glottal filter $G(z)$ and the single zero of the radiation $R(z)$. Poles: the denominator of $G(z)$ and the vocal tract $V(z)$.
> The mistake: LP over the whole frame fits one all-pole $H(z)$ to the product of all of these. The poles of $H(z)$ model the vocal tract and also the glottal poles (the spectral tilt), and they also try to imitate the zeros. So the inverse filter removes the glottal filter together with the tract.
> What is left is close to the impulse train $I(z)$, not the glottal flow. This is why the residual on slide 12 is spiky and does not look like the smooth pulses of slide 6.

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

> [!question]- Check (slide 15): Compare the radiation filter $y[n]=s[n]-\alpha s[n-1]$ with the integrator $x[n]=y[n]+\alpha x[n-1]$: FIR or IIR, zero or pole, and what do they give in series? Verify with $\alpha=0.95$ and $s=[3,\,5,\,4]$.
> Radiation: FIR, one zero at $z=\alpha$, transfer function $1-\alpha z^{-1}$. Integrator: IIR (it feeds back $x[n-1]$), one pole at $z=\alpha$, transfer function $1/(1-\alpha z^{-1})$. In series the pole cancels the zero and the product is 1: the integrator undoes the radiation.
> Radiation: $y[0]=3$, $y[1]=5-0.95\cdot3=2.15$, $y[2]=4-0.95\cdot5=-0.75$.
> Integrator: $x[0]=3$, $x[1]=2.15+0.95\cdot3=5$, $x[2]=-0.75+0.95\cdot5=4$. We get $s$ back.

> [!question]- Check (slide 16): The vertical line marks one instant in all four traces. What does each trace do there, and is the glottal flow at its maximum at that instant? About how long is one pitch period in samples?
> At the line (about sample 440) the model flow derivative, the real speech and the closed-phase residual all have a sharp negative peak. This instant is the glottal closure.
> The glottal flow is not at its maximum there. Its maximum comes a little earlier (about sample 410). At the line the flow is falling steeply to zero, and a steep fall means a large negative derivative.
> The negative peaks are at about 190, 440, 690 and 940, so one period is about 250 samples.
> The closed-phase residual (bottom) has the same overall shape as the model derivative (second trace): a slow positive part and a sharp negative spike. The open-phase residual of slide 12 did not show this shape.

> [!question]- Check (slide 17): In the block diagram, which blocks together make up $U(z)$ for voiced speech? What does the switch choose, which single parameter sets $F_0$, and what is $F_0$ for $P=100$ samples at $f_s=8$ kHz? Which block does the noise path skip?
> Voiced: the impulse train generator followed by the glottal model $G(z)$ and the gain $G_1$ make the source. Then come the vocal tract $H(z)$ and the radiation $R(z)$.
> The switch chooses the excitation of the vocal tract: the glottal pulse train (voiced) or random noise with gain $G_2$ (unvoiced). The tract and radiation blocks are shared.
> $F_0$ is set by the pitch period $P$, the spacing of the impulses: $F_0=f_s/P=8000/100=80$ Hz.
> The noise path skips $G(z)$: in unvoiced speech the vocal folds do not vibrate, so there is no glottal pulse shape.
> Source estimation is the reverse problem: from $s(n)$ find the switch position, $P$, and the pulse shape.

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

> [!question]- Check (slide 18): The slide lists voiced/unvoiced detection, pitch estimation and epoch detection. Why are they needed for closed-phase analysis, and why in this order?
> Closed-phase analysis fits LP only on the samples where the glottis is closed, so it needs the instants of glottal closure (epochs).
> Epochs exist only in voiced speech, so the voiced segments must be found first. The pitch then says how far apart the closures are (one per pitch period), which tells the epoch detector where to look for the next one.

> [!question]- Check (slide 19): In the waveform of "erase", which part is voiced and which part is the unvoiced /s/, and how do you see it? How many samples is a 10 to 20 ms block at $f_s=16$ kHz, and what does each symbol in $P_{av}$ mean?
> From about 0.1 to 0.45 s the amplitude is large (up to about 12): the voiced part. From about 0.5 to 0.7 s the amplitude is small (about $\pm1$): the /s/. Before 0.1 s and after 0.7 s there is silence.
> Block length: $0.010\cdot16000=160$ to $0.020\cdot16000=320$ samples.
> $P_{av}=\frac1L\sum x^2[n]$: $x[n]$ is a speech sample, squaring makes every sample count as positive energy, the sum runs over the $L$ samples of the block, and $1/L$ turns the energy into average power, so blocks of different length can be compared.

> [!question]- Check (slide 20): The histograms peak at about 13 (voiced) and about 49 (unvoiced) zero crossings per 10 ms. A pure sine of frequency $f$ crosses zero $2f$ times per second. Which sine frequencies give 13 and 49 crossings per 10 ms, and does that fit the slide's first bullet? Where do the two classes overlap?
> 13 crossings in 10 ms is 1300 per second, so $f=650$ Hz. 49 crossings in 10 ms is 4900 per second, so $f=2450$ Hz.
> It fits: voiced speech has most of its energy at low frequencies (below 3 kHz, in fact mostly below 1 kHz), unvoiced speech at higher frequencies. The zero-crossing rate is a cheap measure of where the energy is on the frequency axis.
> The two fitted curves overlap between about 20 and 35 crossings. A block in that range cannot be classified safely from $Z$ alone. The maximum-likelihood threshold lies in this region, where the two curves have the same height.

> [!question]- Check (slide 21): In the figure, describe the red dashed curve ($Z_{\hat n}$) and the green dash-dot curve ($E_{\hat n}$) before 50 ms and after 70 ms. Which sound type is each part? What are the black bell curves, and what changes if they are made twice as wide?
> Before 50 ms the waveform is small and noise-like: zero crossings are high and energy is low. This part is unvoiced. After 70 ms the waveform has large regular pulses: energy is high and zero crossings are low. This part is voiced. The two curves cross just before 70 ms, at the transition.
> The black bells are the analysis window at two positions (centred at about 12 ms and about 92 ms). Each point of the curves is computed from the samples under one window.
> A window twice as wide averages more samples: the curves become smoother, but the change from unvoiced to voiced is smeared over a longer time, so the boundary is located less exactly.

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

> [!info] The autocorrelation method in more detail (beyond the slides)
> The slide gives only one sentence for each method. For the autocorrelation (özilinti) method, a frame of $N$ samples is used:
> 
> $$R[k]=\sum_{n=0}^{N-1-k}x[n]\,x[n+k]$$
> 
> - $x[n]$ is the frame (the $N$ samples cut out by the window), $k$ is the **lag**: the shift in samples between the frame and its copy. $R[0]$ is the frame energy and is always the largest value.
> - If the signal repeats every $P$ samples, the copy shifted by $P$ lines up with the original, every product is $x^2[n]\ge0$, and $R[k]$ has a peak at $k=P$. Also at $2P$, $3P$, ...
> - The slide says the peaks are at shifts of $1/F_0$. That is the lag in seconds. In samples it is $f_s/F_0$.
> - **Search range.** Only lags between $f_s/F_{0,\max}$ and $f_s/F_{0,\min}$ are searched. This also keeps the search away from the large values near lag 0.
> - **Frame length.** The sum has only $N-k$ terms. To compare one period with the next one, the frame must hold at least two of the longest pitch periods.
> - **Peaks fall with lag.** Fewer terms at larger $k$ means smaller peaks. For an exactly periodic signal, a rectangular window and $N$ a multiple of $P$: $R[P]/R[0]=1-P/N$. Example $N=32$, $P=8$: the peaks at lags 8, 16, 24 are 0.75, 0.50, 0.25 of $R[0]$.
> - **Voicing from the peak.** The normalised peak $R[P]/R[0]$ is high for voiced frames and low for noise-like frames, so it can serve as a third voicing feature next to energy and zero crossings.
> - **Two typical errors.** Pitch halving: the peak at $2P$ wins, the method reports $F_0/2$. Pitch doubling: a peak near $P/2$ wins, the method reports $2F_0$. This happens when the second harmonic is very strong, for example when a formant sits on it. The formants add their own fast oscillation to $R[k]$. This is the reason for the residual method on the slide: the LP residual has the formants removed, so its autocorrelation shows mainly the pitch peaks.
> - **Smoothing.** A wrong frame gives an isolated jump in the $F_0$ track. A median filter (replace each value by the middle value of its neighbourhood) removes such a jump completely. A mean filter would spread it over the neighbours.

> [!question]- Check (slide 22, lags): An autocorrelation pitch detector works at $f_s=10$ kHz and searches $F_0$ from 70 to 350 Hz. Which lags must it search? The largest peak in that range is at lag 64: what is $F_0$? Which wrong lags would give pitch halving and pitch doubling, and are they inside the search range?
> Smallest lag: $f_s/F_{0,\max}=10000/350\approx28.6$, so 29. Largest lag: $10000/70\approx142.9$, so 142. Search lags 29 to 142.
> $F_0=10000/64=156.25$ Hz.
> Halving: lag $2\cdot64=128$, reported $F_0=10000/128\approx78$ Hz. Doubling: lag $64/2=32$, reported $F_0=10000/32=312.5$ Hz. Both lags are inside 29 to 142, so the search range alone does not protect against either error here.

> [!question]- Check (slide 22, methods): For each of the four methods on the slide, say in which domain it looks and what it looks for. Why can the residual show the periodicity more clearly than the speech signal itself?
> Cepstrum: in the cepstral domain, a peak at the pitch period (Lecture 9). Autocorrelation: in the lag domain, a peak at the lag of one period, $f_s/F_0$ samples. Frequency domain: in the DFT, the spacing of the harmonics, which is $F_0$. Residual analysis: in the time domain after inverse filtering, the spacing of the excitation spikes (or the autocorrelation of the residual).
> The speech signal is the periodic excitation filtered by the vocal tract. The formants ring inside each period and can make the waveform look similar to itself at shifts other than the pitch period. Inverse filtering removes most of the vocal tract, and what is left is close to one spike per period.

> [!question]- Check (slide 23): Name two visible differences between the raw and the smoothed track in the figure. Then take the raw values $[150,\,152,\,300,\,154,\,156]$ Hz: what kind of error is the 300, and what do a 3-point median and a 3-point mean give at the three middle positions?
> Differences: (1) the raw track has gaps (for example near 1.1 s and near 2.1 s), where the frames are unvoiced or silent and there is no $F_0$; the lower curve is interpolated across them. (2) The raw track has isolated points away from their neighbours (for example the point near 200 Hz at about 1.2 s); the lower curve is smooth.
> The 300 is about twice its neighbours: a pitch doubling error (the detector picked half the true lag).
> Median: $\text{med}(150,152,300)=152$, $\text{med}(152,300,154)=154$, $\text{med}(300,154,156)=156$. The outlier is gone and the other values are almost unchanged.
> Mean: $602/3\approx200.7$, $606/3=202$, $610/3\approx203.3$. The outlier is not removed. It is spread over three frames, and all three are now about 50 Hz too high.

## 8. Epoch detection (slides 24-26)
![[cs568_lecture_08_l8_p024.jpg]]
![[cs568_lecture_08_l8_p025.jpg]]
![[cs568_lecture_08_l8_p026.jpg]]

**Notes.** An epoch detection algorithm (EDA) finds one instant per period, the **pitch mark** or **epoch**. Usually it is the **instant of glottal closure** (IGC): the large negative spike in the glottal-flow derivative. Closed-phase analysis needs the IGCs. Finding them from the speech signal is hard (tool: DYPSA in VOICEBOX). An alternative is a **laryngograph** (electroglottograph, EGG): it measures the electrical impedance across the larynx, high when the glottis is open, low when closed. The Lx signal measures fold contact, not air flow, but it makes the IGCs easy to see (slide 25).

Pitch detection gives one number per frame. Epoch detection gives one time instant per period.

MATLAB examples of the lecture: `ex8p1.m` (voicing), `ex8p2.m` (pitch), `ex8p3.m` (pitch marking), `ex8p4.m` (closed-phase analysis).

> [!question]- Check (slide 24): What is the difference between the output of a pitch detector and of an epoch detector? Then find the mistake: "The laryngograph signal Lx is the glottal air flow, measured electrically."
> A pitch detector gives one number per frame: $F_0$ (or the period). It does not say where in time each period starts. An epoch detector gives one time instant per period, usually the instant of glottal closure (IGC). From the epochs the pitch follows (the spacing), but not the other way round.
> The mistake: Lx measures the electrical impedance across the larynx, which depends on how much the vocal folds touch. Impedance is high with the glottis open and low with it closed. It is not the air flow and does not have the shape of the flow. It is useful because the closure shows as a sudden change, so the IGCs are easy to find.

> [!question]- Check (slide 25): What does each of the four traces do at the line "instant of glottal closure"? Which part of the cycle lies between the closure line and the opening line? About how many samples apart are the residual spikes?
> At the closure line (about sample 6305): the speech waveform starts a large sharp swing, the laryngograph trace rises almost vertically, the integrated residual falls steeply, and the residual has a sharp negative spike.
> About the direction of the laryngograph trace: in this plot it is high just after closure and falls to its lowest point at the opening line. Slide 24 says the impedance is low when the glottis is closed. So the trace is not drawn as "up = high impedance"; it seems to be drawn the other way round (up = more fold contact). The slide does not state this. What marks the closure is the sudden change, not its direction.
> Between the closure line and the opening line (about sample 6395) the glottis is shut: this is the closed phase, about 90 samples long here. Closed-phase analysis fits its LP model on such stretches.
> The residual spikes are about 140 samples apart, which is one pitch period. The spike is the same closure event that the laryngograph shows, so it can be used as the pitch mark when no laryngograph is available.
> The integrated residual (third trace) is an estimate of the glottal flow: it rises slowly and drops fast at closure, like the pulses of slide 6. Its baseline also wanders (down in the middle of the plot, up at the end). The slide does not explain this. A likely cause is that the integrator adds up small slow errors in the residual.

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
- Quiz: [[CS568 L08 - Source estimation - quiz]]

Slides not embedded: 1
