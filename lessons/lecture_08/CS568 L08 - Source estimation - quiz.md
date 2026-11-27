---
course: CS 568 Speech Processing
lesson: CS568 L08 - Source estimation
type: quiz
questions: 24
---

# CS568 L08 - Source estimation - quiz

Lesson note: [[CS568 L08 - Source estimation]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Review: speech as filters in series (slides 2-3)

### Q1 (slide 2)

Voiced speech is modelled as $Y(z)=U(z)V(z)R(z)$, where $U(z)$ is the glottal source, $V(z)$ the vocal tract and $R(z)$ the radiation at the lips. The course models one of these factors with linear prediction and the other two in the lecture on source estimation. Which statement is correct?

- A) The source-estimation lecture models $V(z)$ and $R(z)$, the two filters after the glottis.
- B) $U(z)$ sets the pitch, and the source-estimation lecture models $U(z)$ and $R(z)$.
- C) $U(z)$ sets the formant peaks, as the glottal pulse shape fixes the resonances.
- D) $V(z)$ sets the pitch, as the length of the vocal tract fixes the pulse rate.

> [!success]- Answer
> **B.** The pitch is the repetition rate of the glottal pulses, so it comes from $U(z)$. The formant peaks are resonances of $V(z)$.
> Linear prediction (Lecture 7) models $V(z)$. So source estimation models the other two, $U(z)$ and $R(z)$.
> - A: $V(z)$ is the factor of the linear-prediction lecture: the all-pole LP model is the vocal tract.
> - C: The pulse shape sets the overall tilt of the source spectrum. The resonances (formants) belong to the tract $V(z)$.
> - D: The tract only filters the pulses. Their rate is set by the vibration of the vocal folds, which is part of $U(z)$.

### Q2 (slide 3)

A figure builds the output spectrum of a vowel by adding three curves ("+" signs): the source spectrum, the vocal-tract gain and the lip radiation. The model itself multiplies them, $Y=U\,V\,R$. Which statement is correct?

- A) The curves are in dB, where a product becomes a sum, and doubling $F_0$ leaves the formant peaks in place.
- B) Doubling $F_0$ doubles the frequencies of the formant peaks, since the formants follow the harmonics.
- C) Doubling $F_0$ puts twice as many harmonic lines below 2 kHz, as the lines get closer together.
- D) The three blocks act in parallel, so their output spectra simply add, as the "+" signs in the figure show.

> [!success]- Answer
> **A.** The curves are drawn on a log (dB) scale, and $\log(\lvert U\rvert\lvert V\rvert\lvert R\rvert)=\log\lvert U\rvert+\log\lvert V\rvert+\log\lvert R\rvert$.
> With double $F_0$ the harmonic lines are twice as far apart. The envelope, and with it the formant peaks of $V$, stays where it is.
> - B: The formants are set by the tract shape, which did not change. Only the spacing of the harmonic lines changes.
> - C: Direction reversed. The lines are spaced $F_0$ apart, so double $F_0$ gives lines twice as far apart: half as many below 2 kHz.
> - D: The blocks are in series: the source passes through the tract and then the lips, so the spectra multiply. The sum comes only from the dB scale.

## 2. Lip radiation (slide 4)

### Q3 (slide 4)

The lip radiation is modelled as $R(z)=1-\alpha z^{-1}$ with $\alpha$ between 0.95 and 0.99, that is $y[n]=s[n]-\alpha\,s[n-1]$. Which statement is correct?

- A) Its zero at $z=\alpha$ lies near DC, so the gain falls with frequency, like a smoothing filter.
- B) Its zero at $z=\alpha$ lies near DC, so the gain rises with frequency by about 6 dB per decade.
- C) It is needed because LP gives the pressure at the lips and the microphone measures volume velocity.
- D) Its zero at $z=\alpha$ lies near DC, so the gain rises with frequency by about 6 dB per octave.

> [!success]- Answer
> **D.** $1-\alpha z^{-1}=0$ gives $z=\alpha$, just inside the unit circle near $z=1$ (DC). The gain is $1-\alpha$ at DC and $1+\alpha$ at Nyquist.
> With $\alpha=1$ it is the first difference $s[n]-s[n-1]$, a derivative. A single zero gives $+6$ dB per doubling of frequency (an octave), which is $+20$ dB per decade.
> - A: Direction reversed. A zero near $z=1$ removes DC and boosts fast changes: a derivative is a high-frequency boost, not a smoother.
> - B: dB per octave confused with dB per decade. One zero gives $20\log_{10}2\approx6$ dB per octave, that is 20 dB per decade. The slide's "per decade" is a slip.
> - C: Quantities swapped. LP relates volume velocities. The microphone measures pressure, which is close to the derivative of the lip volume velocity.

## 3. Glottal source models (slides 5-10)

### Q4 (slide 5)

A speaker has $F_0=220$ Hz and the signal is sampled at $f_s=16$ kHz. The usual male range is $F_0=80$ to 250 Hz. Which pair is correct?

- A) One period is about 72.7 samples; male periods run from 4 ms (at 80 Hz) to 12.5 ms (at 250 Hz).
- B) One period is about 72.7 samples; male periods run from 4 ms (at 250 Hz) to 12.5 ms (at 80 Hz).
- C) One period is about 0.014 samples; male periods run from 4 ms (at 80 Hz) to 12.5 ms (at 250 Hz).
- D) One period is about 0.014 samples; male periods run from 4 ms (at 250 Hz) to 12.5 ms (at 80 Hz).

> [!success]- Answer
> **B.** $T=1/F_0=1/220$ s $=4.55$ ms. In samples: $f_s/F_0=16000/220\approx72.7$.
> Male range: $1/250=4$ ms and $1/80=12.5$ ms. A high $F_0$ gives a short period.
> - A: Direction swapped: a higher $F_0$ was paired with a longer period. $1/80$ s is 12.5 ms, not 4 ms.
> - C: Two slips: $F_0/f_s$ instead of $f_s/F_0$, and a high $F_0$ paired with a long period.
> - D: Divided the wrong way: $F_0/f_s=220/16000\approx0.014$. The period in samples is $f_s/F_0$.

### Q5 (slide 6)

One cycle of the glottal flow (volume velocity through the glottis) has an open, a return and a closed phase. Which statement is correct?

- A) The return phase is the longest of the three, as the folds close slowly against the air.
- B) The tract is excited most strongly at the flow maximum, as the most air passes then.
- C) The flow falls fastest in the return phase, and the tract is excited most strongly at closure.
- D) The flow falls fastest in the closed phase, as the flow there is the smallest of the whole cycle.

> [!success]- Answer
> **C.** In the open phase the flow rises slowly to its maximum. In the return phase it drops to zero very fast: the slope is large and negative. In the closed phase it is flat at zero.
> The radiation differentiates the flow, so the tract is driven by the slope. The strongest excitation comes when the folds snap shut.
> - A: The return phase is the shortest: the folds snap shut. The slow part is the rise in the open phase.
> - B: What drives the tract is the derivative of the flow. At the flow maximum the derivative is zero.
> - D: Value confused with slope. In the closed phase the flow is zero and flat, so its slope is zero.

### Q6 (slide 7)

The LF (Liljencrants-Fant) model gives $u[n]$ over one period:

$$u[n]=\begin{cases}0, & 0\le n<T_0\\ E_0\,e^{\alpha(n-T_0)}\sin\big[\Omega_0(n-T_0)\big], & T_0\le n<T_e\\ -E_1\big[e^{\beta(n-T_e)}-e^{\beta(T_c-T_e)}\big], & T_e\le n<T_c\end{cases}$$

Lines 1 to 3 are the three rows of the formula. $T_0<T_e<T_c$ are three instants of the period, and $E_0,E_1,\alpha,\beta,\Omega_0$ are constants. Which statement is correct?

- A) Line 2 is a sine with a growing envelope: it draws the positive hump and the dive to the minimum.
- B) The formula gives the glottal flow itself, so its negative minimum marks the closed phase.
- C) Line 3 models the open phase, the time in which air flows freely through the open glottis.
- D) $E_0$ and $E_1$ set the shape of the pulse, while $\alpha$ and $\Omega_0$ set the height of the pulse.

> [!success]- Answer
> **A.** Line 2 starts at $T_0$ (glottal opening). The sine goes up and then down, and the growing envelope makes the negative part deeper: the minimum is at $T_e$.
> Line 3 brings the curve back to zero at $T_c$ (closure): the return phase. $u[n]$ is the flow derivative, not the flow.
> - B: The LF model describes the flow derivative. The flow is never negative, and in the closed phase $u[n]=0$ (line 1).
> - C: Line 3 runs from the minimum $T_e$ to closure $T_c$: it is the return phase. The open phase is line 2.
> - D: Roles swapped. $E_0,E_1$ scale the heights. $\alpha,\beta,\Omega_0$ set the shape (growth, decay, oscillation).

### Q7 (slide 8)

The time derivative of the glottal flow leaves zero at about 16 ms, reaches its positive maximum at about 42 ms, crosses zero from positive to negative at about 50 ms, reaches its minimum of about $-1.75$ at about 59 ms and is back at zero at about 66 ms. What does the glottal flow do at about 50 ms?

- A) It is zero: the glottis has just closed.
- B) It is rising at its fastest rate.
- C) It is at its highest value in the cycle.
- D) It is falling at its fastest rate.

> [!success]- Answer
> **C.** Where a derivative crosses zero from positive to negative, the function stops rising and starts falling: a maximum. After that the flow falls, fastest at about 59 ms.
> - A: A zero derivative was read as zero flow. The flow returns to zero only at about 66 ms, where the curve is back at zero for good.
> - B: The fastest rise is where the derivative is largest, at about 42 ms.
> - D: The fastest fall is where the derivative is most negative, at the minimum (about 59 ms), not where it is zero.

### Q8 (slide 9)

The Quatieri glottal model is $U(z)=1/(1-\beta z)^2$ with $\beta\approx0.95$. Which statement is correct?

- A) It models the glottal-flow derivative, so its pulse has a sharp negative spike at closure.
- B) It has a double pole at $z=1/\beta\approx1.05$, so each factor of it is anti-causal.
- C) The pulse it gives starts abruptly and then decays slowly, like a causal exponential.
- D) It has a double zero at $z=\beta=0.95$, inside the unit circle, so it removes the DC part.

> [!success]- Answer
> **B.** $1-\beta z=0$ gives $z=1/\beta=1/0.95\approx1.053$. The region of convergence $\lvert z\rvert<1.053$ contains the unit circle, so each factor is anti-causal: $\beta^{-n}v[-n]$ with $v$ the unit step.
> So the pulse rises slowly and ends abruptly, like the real flow. Two poles make it low-pass, about $-12$ dB per octave.
> - A: Confused with the LF model. The Quatieri $U(z)$ is the flow itself, a positive pulse. The derivative appears only after the radiation $R(z)$.
> - C: This is the shape of a causal decay. Each factor here is time-reversed, so the pulse grows slowly and stops abruptly.
> - D: Poles read as zeros. $1/(1-\beta z)^2$ has no zeros; the factor is in the denominator. Zeros come only in the extended model, $\prod_k(1-u_kz^{-1})$.

### Q9 (slide 10)

In $Y(z)=U(z)V(z)R(z)$ the radiation $R(z)$ acts at the lips, after the vocal tract $V(z)$. Source analysis moves it next to the glottal source and studies $U(z)R(z)$. Take a glottal source $U(z)$ whose spectrum falls about 40 dB per decade. Which pair is correct?

- A) Allowed, as LTI filters in series commute; $U(z)R(z)$ falls about 20 dB per decade.
- B) Allowed, as $R(z)$ acts only above the formants; $U(z)R(z)$ falls about 20 dB per decade.
- C) Allowed, as LTI filters in series commute; $U(z)R(z)$ falls about 34 dB per decade.
- D) Allowed, as $R(z)$ acts only above the formants; $U(z)R(z)$ falls about 34 dB per decade.

> [!success]- Answer
> **A.** $U$, $V$, $R$ are linear and time-invariant, and LTI filters in series can be reordered: $UVR=(UR)V$.
> $R(z)$ has one zero: it rises about 6 dB per octave, which is $20\log_{10}10=20$ dB per decade. So $U(z)R(z)$ falls about $40-20=20$ dB per decade (6 dB per octave). It is the glottal-flow derivative, about one negative spike per period.
> - B: $R(z)$ changes the gain at every frequency ($1-\alpha$ at DC, $1+\alpha$ at Nyquist). The reason is that LTI filters commute.
> - C: Took the slide's "6 dB per decade" for the radiation: $40-6=34$. One zero gives 6 dB per octave, that is 20 dB per decade.
> - D: Two slips: a wrong reason (the radiation acts at all frequencies) and the slide's "6 dB per decade" for the radiation.

## 4. The residual and why it is not the glottal source (slides 11-14)

### Q10 (slide 11)

LP inverse filtering computes the residual $x[n]=y[n]-\sum_{k=1}^{p}a_k\,y[n-k]$. Take $p=2$, $a_1=1.2$, $a_2=-0.5$ and $y=[2,\,1,\,-1]$ for $n=0,1,2$ (zero before $n=0$). What is $x$ at $n=0,1,2$?

- A) $[2,\ -1.4,\ -3.2]$
- B) $[2,\ -1.4,\ -1.2]$
- C) $[2,\ 3.4,\ -0.8]$
- D) $[2,\ -1.4,\ 1.68]$

> [!success]- Answer
> **B.** $x[0]=2$. $x[1]=1-1.2\cdot2=-1.4$. $x[2]=-1-1.2\cdot1-(-0.5)\cdot2=-1-1.2+1=-1.2$.
> The filter uses only present and past inputs $y$, with no feedback: it is FIR, the inverse of the all-pole (IIR) synthesis filter.
> - A: Sign slip on $a_2=-0.5$: subtracted $0.5\cdot2$ instead of adding it, $-1-1.2-1=-3.2$.
> - C: Used $+\sum_k a_k y[n-k]$, the sign of the synthesis equation $y[n]=x[n]+\sum_k a_k y[n-k]$: $1+2.4=3.4$, $-1+1.2-1=-0.8$.
> - D: Fed back past residuals $x[n-k]$ instead of past inputs $y[n-k]$: $-1-1.2\cdot(-1.4)+0.5\cdot2=1.68$. That is an IIR recursion, not the FIR inverse filter.

### Q11 (slide 12)

The figure shows a 400-sample frame of voiced speech (top) and its residual from LP over the whole frame (bottom). Which statement is correct?

![[cs568_lecture_08_l8_p012.jpg]]

- A) The spikes near 113 and 230 mark one opening and one closure, so they are half a period apart.
- B) The large spike at sample 0 is the strongest glottal closure of the whole frame.
- C) The spikes near 113 and 230 are about one pitch period apart, one at each glottal closure.
- D) The residual is near zero between the spikes because the speech is silent there.

> [!success]- Answer
> **C.** Between the spikes the samples are well predicted from the past, so the residual is near zero. At each closure a new excitation arrives that the past cannot predict.
> The spacing, about 117 samples, is one pitch period.
> - A: Each period gives one spike, at the closure. Two neighbouring spikes are a full period apart.
> - B: At the start the predictor has no past samples inside the frame, so the first samples are badly predicted. It is an edge effect.
> - D: The top plot is not silent between the peaks. The residual is small there because those samples are well predicted.

### Q12 (slides 13-14)

Voiced speech is $U(z)V(z)R(z)$ with source $U(z)=I(z)G(z)$: $I(z)$ an impulse train and $G(z)$ a glottal filter with a numerator and a denominator polynomial. $V(z)$ is the vocal tract and $R(z)=1-\alpha z^{-1}$. Open-phase LP fits one all-pole model to the whole frame. Which statement is correct?

- A) The residual is the glottal flow, because LP over the whole frame removes only the vocal tract.
- B) The residual is the vocal-tract impulse response, because LP removes the glottal source.
- C) The zeros of $U(z)V(z)R(z)$ come from the vocal tract $V(z)$, so an all-pole model cannot fit it.
- D) The LP poles also take up the glottal poles, so the residual is close to the impulse train.

> [!success]- Answer
> **D.** Zeros: the numerator of $G(z)$ and the zero of $R(z)$. Poles: the denominator of $G(z)$ and $V(z)$.
> One all-pole fit to all of this models the tract and the glottal tilt together. The inverse filter removes both, so what is left is close to $I(z)$: spiky, unlike the smooth flow.
> - A: LP cannot tell tract poles from glottal poles. It removes the glottal filter together with the tract.
> - B: Reversed. The inverse filter removes the modelled filter (tract and tilt) and keeps the excitation.
> - C: $V(z)$ gives poles (formants). The zeros come from the numerator of $G(z)$ and from $R(z)$.

## 5. Closed-phase analysis (slides 15-17)

### Q13 (slide 15)

The radiation filter is $y[n]=s[n]-\alpha\,s[n-1]$ and the integrator is $x[n]=y[n]+\alpha\,x[n-1]$, with $\alpha=0.95$. The input $s=[3,\,5,\,4]$ (zero before $n=0$) goes through the radiation filter and then the integrator. What is $x$ at $n=0,1,2$?

- A) $[3,\ 5,\ 4]$
- B) $[3,\ 5,\ 1.29]$
- C) $[3,\ -0.70,\ -0.085]$
- D) $[3,\ 10.7,\ 18.9]$

> [!success]- Answer
> **A.** Radiation: $y=[3,\ 5-0.95\cdot3,\ 4-0.95\cdot5]=[3,\ 2.15,\ -0.75]$.
> Integrator: $x[1]=2.15+0.95\cdot3=5$, $x[2]=-0.75+0.95\cdot5=4$. The integrator's pole at $z=\alpha$ cancels the radiation zero at $z=\alpha$, so $s$ comes back.
> - B: Fed back $y[n-1]$ instead of $x[n-1]$: $-0.75+0.95\cdot2.15=1.29$. That is an FIR filter; the integrator must be IIR.
> - C: Integrator run with $-\alpha$: $x[n]=y[n]-\alpha x[n-1]$ gives $2.15-2.85=-0.70$ and $-0.75+0.665=-0.085$.
> - D: Radiation with the wrong sign, $s[n]+\alpha s[n-1]$: $y=[3,\ 7.85,\ 8.75]$, then $x=[3,\ 10.7,\ 18.9]$.

### Q14 (slide 16)

A plot shows a model glottal flow, its derivative, real speech and the residual of closed-phase analysis. A vertical line marks an instant where the model flow derivative and the closed-phase residual both have a sharp negative peak. Which statement about the glottal flow at that instant is correct?

- A) The flow is starting to rise, as the glottis opens at this instant.
- B) The flow is rising at its fastest rate, as the slope is largest in size.
- C) The flow is falling steeply to zero; its maximum came a little earlier.
- D) The flow is at its maximum, as the folds are open widest at this instant.

> [!success]- Answer
> **C.** A sharp negative peak of the derivative means the flow drops fast there. This is the glottal closure.
> The flow maximum is where the derivative crosses zero, a little before the line.
> - A: The opening gives a slow positive derivative. The sharp negative peak is the closure.
> - B: Sign slip: a fast rise gives a large positive derivative. A negative peak means a fast fall.
> - D: At a flow maximum the derivative is zero, not at a negative peak.

### Q15 (slide 17)

A speech production model has two excitation paths joined by a voiced/unvoiced switch. Voiced: an impulse train with period $P$ samples, a glottal model $G(z)$ and a gain. Unvoiced: a random noise generator and a gain. The switch feeds a vocal tract $H(z)$ and a radiation $R(z)$. Take $P=100$ and $f_s=8$ kHz. Which statement is correct?

- A) $F_0=f_s/P=80$ Hz is set by $P$ alone, and the noise path skips $G(z)$.
- B) $F_0$ is set by the glottal model $G(z)$, as its pulse shape fixes the pulse rate.
- C) The noise path skips the vocal tract $H(z)$, as unvoiced sounds have no formants.
- D) $F_0=1000\,P/f_s=12.5$ Hz, since one period lasts $P/f_s$ seconds.

> [!success]- Answer
> **A.** The impulses are $P$ samples apart, so $F_0=f_s/P=8000/100=80$ Hz.
> Unvoiced sounds have no vibrating vocal folds, so there is no glottal pulse shape: the noise goes straight to the switch. $H(z)$ and $R(z)$ are shared by both paths.
> - B: $G(z)$ shapes each pulse (spectral tilt). The rate is set by the impulse spacing $P$.
> - C: Unvoiced sounds are still shaped by the tract (an /s/ and an /f/ differ). Only $G(z)$ is skipped.
> - D: $1000\,P/f_s=12.5$ is the period in ms, not a frequency. $F_0=f_s/P=80$ Hz.

## 6. Voiced/unvoiced detection (slides 18-21)

### Q16 (slide 18)

Closed-phase analysis of speech relies on three detection steps: voiced/unvoiced decision, pitch estimation and epoch detection. Which statement is correct?

- A) It needs only the $F_0$ value; the epochs are found last, as a check on the estimated pitch.
- B) It needs the closure instants, which exist only in voiced speech and are one pitch period apart.
- C) Voicing comes first because unvoiced frames have the glottis closed during the whole of the frame.
- D) Voicing is decided from the epochs, so epoch detection would have to run before the others.

> [!success]- Answer
> **B.** Closed-phase LP uses only the samples where the glottis is shut, so it needs the instants of glottal closure (epochs).
> Epochs exist only in voiced speech, so voicing comes first. Closures are one pitch period apart, so the pitch guides the epoch search.
> - A: $F_0$ gives no position in time. The LP fit needs the closure instants themselves.
> - C: In unvoiced speech the folds do not vibrate and the glottis is open. There is no closed phase at all.
> - D: Order reversed. Voicing comes from simple features (energy, zero crossings) and must come first.

### Q17 (slide 19)

A recording of the word "erase" at $f_s=16$ kHz: from about 0.1 to 0.45 s the amplitude is large (peaks up to about 12), from about 0.5 to 0.7 s it is small (about $\pm1$). A voicing detector splits it into blocks of 10 to 20 ms and computes $P_{av}=\frac1L\sum_{n=1}^{L}x^2[n]$ in each block of $L$ samples. Which pair is correct?

- A) The 0.1 to 0.45 s part is voiced; a block has $L=1600$ to 3200 samples.
- B) The 0.5 to 0.7 s part is voiced; a block has $L=1600$ to 3200 samples.
- C) The 0.5 to 0.7 s part is voiced; a block has $L=160$ to 320 samples.
- D) The 0.1 to 0.45 s part is voiced; a block has $L=160$ to 320 samples.

> [!success]- Answer
> **D.** Voiced speech has the higher power $P_{av}$: the large-amplitude part. The small part at the end is the unvoiced /s/.
> $L=0.010\cdot16000=160$ to $0.020\cdot16000=320$ samples.
> - A: 10 ms read as 0.1 s: $0.1\cdot16000=1600$. 10 ms is 0.010 s.
> - B: Two slips: the power reversed, and 10 ms read as 0.1 s.
> - C: Reversed: voiced speech has the higher power, so it is the large-amplitude part.

### Q18 (slide 20)

Two zero-crossing histograms peak at about 13 and at about 49 zero crossings per 10 ms. A pure sine of frequency $f$ crosses zero $2f$ times per second. Which sine frequencies give 13 and 49 crossings per 10 ms?

- A) About 2600 Hz and 9800 Hz
- B) About 65 Hz and 245 Hz
- C) About 650 Hz and 2450 Hz
- D) About 1300 Hz and 4900 Hz

> [!success]- Answer
> **C.** 13 per 10 ms is 1300 per second, so $f=1300/2=650$ Hz. 49 per 10 ms is 4900 per second, so $f=2450$ Hz.
> - A: Multiplied by 2 instead of dividing: $2\cdot1300$ and $2\cdot4900$.
> - B: 10 ms read as 0.1 s: $13/0.1=130$ crossings per second, $f=65$ Hz.
> - D: Forgot to divide by 2: these are the crossings per second, not $f$.

### Q19 (slide 21)

In the figure the red dashed curve is the zero-crossing rate $Z$, the green dash-dot curve is the short-time energy $E$, and the black bells show the analysis window at two positions. Which statement is correct?

![[cs568_lecture_08_l8_p021.jpg]]

- A) A window twice as wide would locate the boundary more exactly, as it uses twice as many samples.
- B) Before 50 ms $Z$ is high and $E$ low: voiced, as the vocal folds vibrate very fast there.
- C) Before 50 ms $Z$ is high and $E$ low: unvoiced; a window twice as wide would blur the boundary.
- D) After 70 ms $E$ is high and $Z$ low: unvoiced, as a fricative is louder than a vowel.

> [!success]- Answer
> **C.** Unvoiced speech is noise-like with energy at high frequencies: many crossings, low energy. After 70 ms the large regular pulses give high $E$ and low $Z$: voiced.
> A wider window averages more samples. The curves get smoother, but the change near 60 to 70 ms is spread over a longer time.
> - A: More samples give smoother curves, but each value mixes a longer stretch of time, so the boundary is less exact.
> - B: Many zero crossings come from noise (energy at high frequencies), not from fast fold vibration. $F_0$ is only 80 to 400 Hz.
> - D: Reversed: vowels (voiced) have the higher energy. Fricatives are weak and noise-like.

## 7. Pitch detection (slides 22-23)

### Q20 (slide 22, lags)

An autocorrelation pitch detector works at $f_s=10$ kHz and searches $F_0$ from 70 to 350 Hz. The largest peak in its lag range is at lag 64 samples, so it reports $F_0=10000/64=156.25$ Hz. Which pair is correct?

- A) It searches lags 29 to 142; a peak picked at lag 32 instead would be pitch halving.
- B) It searches lags 2.9 to 14.3; a peak picked at lag 32 instead would be pitch doubling.
- C) It searches lags 2.9 to 14.3; a peak picked at lag 32 instead would be pitch halving.
- D) It searches lags 29 to 142; a peak picked at lag 32 instead would be pitch doubling.

> [!success]- Answer
> **D.** Lags: $f_s/F_{0,\max}=10000/350\approx28.6$, so 29, to $f_s/F_{0,\min}=10000/70\approx142.9$, so 142.
> Lag 32 reports $10000/32=312.5$ Hz, twice the true $F_0$: pitch doubling. The names refer to $F_0$, not to the lag.
> - A: Named after the lag, which is halved. The error is named after $F_0$, which doubles to 312.5 Hz.
> - B: These are the periods $1/F_0$ in ms ($1/350$ s and $1/70$ s), not lags in samples. Multiply the period in seconds by $f_s$.
> - C: Two slips: periods in ms used as lags, and the error named after the lag instead of $F_0$.

### Q21 (slide 22, methods)

A pitch detector can use one of four methods: the cepstrum, the autocorrelation, the frequency domain (DFT), or the LP residual. Which statement is correct?

- A) The frequency-domain method takes $F_0$ as the frequency of the largest DFT peak.
- B) The autocorrelation method takes the period as the lag of the first zero of $R[k]$.
- C) The residual method works because the LP residual is the glottal flow, one smooth pulse per period.
- D) The residual shows the period more clearly, as inverse filtering removes most formant ringing.

> [!success]- Answer
> **D.** Speech is the periodic excitation filtered by the tract. The formants ring inside each period and can make the waveform look like itself at other shifts.
> Inverse filtering removes most of the tract, and about one spike per period is left.
> - A: The largest peak is often a harmonic near a formant. The method uses the spacing of the harmonics, which is $F_0$.
> - B: It uses the lag of a peak: there the shifted copy lines up with the signal. A zero of $R[k]$ marks no period.
> - C: The residual is close to the impulse train (spikes), not the smooth flow. It helps because the formants have been removed.

### Q22 (slide 23)

A raw $F_0$ track from a pitch detector is $[150,\,152,\,300,\,154,\,156]$ Hz. A 3-point median filter replaces each value by the middle value of itself and its two neighbours. What does it give at the three middle positions?

- A) $[152,\ 300,\ 154]$
- B) $[152,\ 154,\ 156]$
- C) $[154,\ 154,\ 154]$
- D) $[200.7,\ 202,\ 203.3]$

> [!success]- Answer
> **B.** $\mathrm{med}(150,152,300)=152$, $\mathrm{med}(152,300,154)=154$, $\mathrm{med}(300,154,156)=156$.
> The outlier 300 (about twice its neighbours, a pitch-doubling error) is removed, and the other values hardly change.
> - A: Took the middle element of each window without sorting it first.
> - C: Took the median of all five values ($154$) at every position, as if the window were the whole track.
> - D: These are 3-point means ($602/3$, $606/3$, $610/3$). A mean spreads the outlier over three frames.

## 8. Epoch detection (slides 24-26)

### Q23 (slide 24)

A pitch detector and an epoch detector both process voiced speech. A laryngograph (electroglottograph) gives a signal called Lx. Which statement is correct?

- A) From the $F_0$ values of a pitch detector the epochs follow, as they are one period apart.
- B) Lx is the glottal air flow, measured electrically, so it has the shape of the flow pulse.
- C) A pitch detector marks each glottal closure, and an epoch detector gives one $F_0$ per frame.
- D) A pitch detector gives one $F_0$ per frame, an epoch detector one instant per period.

> [!success]- Answer
> **D.** An epoch is a time instant, usually the instant of glottal closure. The pitch follows from the epochs (their spacing), but not the other way round.
> Lx measures the electrical impedance across the larynx, which depends on fold contact. It is not the air flow.
> - A: $F_0$ gives the spacing but not the position in time. The epochs give the pitch, not the reverse.
> - B: Lx measures impedance (fold contact), not air flow. It is useful because the closure shows as a sudden change.
> - C: Outputs swapped. The pitch detector gives one number per frame; the epoch detector gives the instants.

### Q24 (slide 25)

The figure shows speech, a laryngograph signal, the integrated residual and the residual. About how far apart are neighbouring negative spikes of the residual (bottom trace)?

![[cs568_lecture_08_l8_p025.jpg]]

- A) About 140 samples
- B) About 280 samples
- C) About 50 samples
- D) About 90 samples

> [!success]- Answer
> **A.** The spikes are at about 6305, 6445, 6585, ...: about 140 samples apart, one pitch period. Each spike is the closure that the laryngograph also shows.
> - B: Measured from one spike to the next but one: two periods.
> - C: This is the distance from the opening line to the next closure: the open and return phases only.
> - D: This is the distance from the closure line to the opening line: the closed phase, not the period.
