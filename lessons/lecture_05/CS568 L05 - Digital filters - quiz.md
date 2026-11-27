---
course: CS 568 Speech Processing
lesson: CS568 L05 - Digital filters
type: quiz
questions: 23
---

# CS568 L05 - Digital filters - quiz

Lesson note: [[CS568 L05 - Digital filters]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Filters, LTI, FIR and IIR (slides 2-4)

### Q1 (slide 2)

![[cs568_lecture_05_l5_p002.jpg]]

The figure models speech as $P(s)=U(s)\,T(s)\,R(s)$. A speaker first changes the vowel by moving the tongue. Then the speaker keeps the vowel and raises the pitch. Which statement is correct?

- A) Tongue: $T$, the vocal-tract filter, so the formants move. Pitch: $U$, the glottal source, so the harmonics spread.
- B) Tongue: $U$, the glottal source, so the harmonics move. Pitch: $T$, the vocal-tract filter, so the formants rise.
- C) Tongue: $T$, the vocal-tract filter, so the formants move. Pitch: $T$ as well, so the formants rise with the pitch.
- D) Tongue: $U$, the glottal source, so its spectrum changes. Pitch: $U$ as well, so the harmonics spread apart.

> [!success]- Answer
> **A.** $U$ is the spectrum of the glottal source, $T$ the transfer function of the vocal tract, $R$ the radiation at the lips, $P$ the speech spectrum.
> Moving the tongue changes the shape of the tract, so $T$ changes and its peaks (the formants) move. A higher pitch changes the source $U$: its harmonic lines move further apart. $T$ stays the same.
> - B: The roles are swapped. The tongue shapes the tract, not the vibration of the vocal folds.
> - C: The tongue part is right, but pitch is set by the vocal folds. The formants do not follow the pitch.
> - D: The pitch part is right, but the tongue is above the larynx. It shapes the filter $T$, not the source.

### Q2 (slide 3)

A student says: "$y[n]=x[n]+0.5\,x[n-1]$ is an IIR filter, because it uses a value from the past." Which statement is correct?

- A) The student is right: $0.5$ multiplies a delayed term, so it is a feedback coefficient $a_1$, and $N=1$.
- B) The student is right: any delayed term in the equation makes the impulse response last for ever.
- C) The student is wrong: $0.5$ is $b_1$, a weight on a past input. IIR needs some $a_l\neq0$ on a past output.
- D) The student is wrong: the filter has only $M=1$ past term. From $M=2$ on, with more past terms, it is IIR.

> [!success]- Answer
> **C.** The $b_k$ are the feed-forward coefficients: they weight the inputs $x[n-k]$, and $M$ is the number of past inputs. The $a_l$ are the feedback coefficients: they weight past outputs $y[n-l]$, and $N$ is their number.
> Here $b_0=1$, $b_1=0.5$, $M=1$ and all $a_l=0$. A past input does not make a filter IIR. Only a past output does.
> - A: $0.5$ weights $x[n-1]$, an input. A feedback coefficient multiplies a past output $y[n-l]$.
> - B: $h=[1,\ 0.5,\ 0,\ 0,\dots]$. The response ends after two samples, so it is finite.
> - D: The length $M$ does not matter. An FIR filter can use any number of past inputs and stays FIR.

### Q3 (slide 4)

![[cs568_lecture_05_l5_p004.jpg]]

In the IIR diagram, where does the $A(z)$ box take its input from and where does its result go? And why can only the IIR filter become unstable? Which statement is correct?

- A) It takes $x(n)$ and sends its result to the output side, parallel to $B(z)$. The two paths can add up and grow.
- B) It takes $y(n)$ and sends its result back to the input side of $B(z)$. A signal can go round this loop and grow.
- C) It takes $y(n)$ and sends its result back to the input side of $B(z)$. An FIR filter is unstable too if a $b_k$ is large.
- D) It takes $x(n)$ and sends its result to the output side, parallel to $B(z)$. A signal can go round this path and grow.

> [!success]- Answer
> **B.** The arrow leaves the line of $y(n)$, goes down into $A(z)$, and comes back into $B(z)$ from below. This is a feedback loop. Remove the box and only the straight path through $B(z)$ is left: the FIR diagram.
> In a loop a signal can go round again and again. If each round makes it larger, the output grows without limit.
> - A: The arrows are read in the wrong direction. And two parallel paths without a loop give a finite sum, so they cannot grow without limit.
> - C: The loop is right, but an FIR output is a finite weighted sum of inputs. A bounded input gives a bounded output, whatever the size of the $b_k$.
> - D: A path from $x(n)$ to the output is not a loop. Nothing goes round, so nothing can grow round after round.

## 2. The impulse response (slides 5-6)

### Q4 (slide 5)

The filter is $y[n]=x[n]+a_1y[n-1]$. Consider $a_1=0.9$, $a_1=-0.9$ and $a_1=-1.2$. Which statement about the impulse responses is correct?

- A) Only $a_1=0.9$ is unstable: all its samples have the same sign, so they add up and $h[n]$ grows towards 10.
- B) $a_1=-0.9$ and $a_1=-1.2$ are unstable: a negative $a_1$ makes $h[n]$ alternate in sign, and that never settles.
- C) Only $a_1=-1.2$ is unstable: $h[n]=a_1^n$, and its size $1.2^n$ grows while the sign keeps alternating.
- D) None of the three is unstable: the filter blows up only for $a_1>1$, and all three values are below 1.

> [!success]- Answer
> **C.** The impulse response is $h[n]=a_1^n$.
> $a_1=0.9$: $1,\ 0.9,\ 0.81,\ 0.729,\dots$ decays. $a_1=-0.9$: $1,\ -0.9,\ 0.81,\ -0.729,\dots$ decays with alternating sign. $a_1=-1.2$: $1,\ -1.2,\ 1.44,\ -1.728,\dots$ grows. The size $\lvert a_1\rvert$ decides stability. The sign only decides whether $h[n]$ alternates.
> - A: $10=1/(1-0.9)$ is the sum of all samples (the step response). Each sample $0.9^n$ gets smaller, so $h[n]$ decays.
> - B: An alternating sign is not growth. For $a_1=-0.9$ the size $0.9^n$ still goes to 0.
> - D: This is the slide's typo. The condition is $\lvert a_1\rvert>1$, and $\lvert-1.2\rvert=1.2>1$.

### Q5 (slide 6)

A script plots the impulse response of one FIR filter and of one IIR filter, both stable. How can you tell them apart, and how many non-zero samples can an FIR filter with $M=4$ have at most?

- A) The FIR response is the one that drops to small values sooner. It has at most 5 non-zero samples.
- B) The FIR response is exactly zero after sample $n=M$. It has at most 4 non-zero samples.
- C) The FIR response is the one that drops to small values sooner. It has at most 4 non-zero samples.
- D) The FIR response is exactly zero after sample $n=M$. It has at most 5 non-zero samples.

> [!success]- Answer
> **D.** For FIR, $h[n]=b_n$ for $0\le n\le M$ and zero after that. A stable IIR response only gets smaller and never becomes exactly zero.
> With $M=4$ the samples are $b_0,b_1,b_2,b_3,b_4$: at most $M+1=5$.
> - A: The count is right, but speed is not the test. A stable IIR filter with a small pole (say $0.1^n$) decays much faster than many FIR filters.
> - B: The test is right, but $b_0$ is forgotten. The indices run from 0 to $M$.
> - C: Both parts are wrong: speed is not the test, and $b_0$ is forgotten.

## 3. Convolution sum and transfer function (slides 7-8)

### Q6 (slide 7)

The convolution sum is $y[n]=\sum_k x[k]\,h[n-k]$. What is the single term $x[k]\,h[n-k]$ for one fixed $k$, and which two filter properties does the formula need?

- A) The input $x[k]$ times the single sample $h[k]$. The formula needs linearity and time invariance.
- B) The response $h$ started at time $k$, scaled by $x[k]$. The formula needs linearity and time invariance.
- C) The response $h$ started at time $k$, scaled by $x[k]$. The formula needs causality and BIBO stability.
- D) The input $x[k]$ times the single sample $h[k]$. The formula needs causality and BIBO stability.

> [!success]- Answer
> **B.** As a function of $n$, $h[n-k]$ is the impulse response shifted to start at time $k$. The factor $x[k]$ scales it. It is the part of the output caused by the one input sample $x[k]$.
> The shift needs time invariance (a delayed impulse gives a delayed response). The scaling and the sum over $k$ need linearity.
> - A: The index is $n-k$, not $k$. The term is a whole shifted copy of $h$, not one product of two numbers.
> - C: The meaning is right, but the formula also holds for non-causal and for unstable LTI filters. It needs the two LTI properties.
> - D: Both parts are wrong: the index is $n-k$, and causality and stability are not what the formula rests on.

### Q7 (slide 8)

Use the sign convention of the slides, in which the feedback terms are added: $y[n]=\dots+a_1y[n-1]+a_2y[n-2]$. A filter has $H(z)=\dfrac{1+0.5z^{-1}}{1-0.3z^{-1}+0.2z^{-2}}$. Which difference equation does it have?

- A) $y[n]=x[n]+0.5\,x[n-1]-0.3\,y[n-1]+0.2\,y[n-2]$
- B) $y[n]=x[n]+0.5\,x[n-1]+0.3\,y[n-1]-0.2\,y[n-2]$
- C) $y[n]=x[n]-0.3\,x[n-1]+0.2\,x[n-2]+0.5\,y[n-1]$
- D) $y[n]=x[n]-0.3\,x[n-1]+0.2\,x[n-2]-0.5\,y[n-1]$

> [!success]- Answer
> **B.** The numerator holds the feed-forward coefficients: $b_0=1$, $b_1=0.5$. The denominator is $1-a_1z^{-1}-a_2z^{-2}$, so $a_1=0.3$ and $a_2=-0.2$.
> $Y(z)\,(1-0.3z^{-1}+0.2z^{-2})=X(z)\,(1+0.5z^{-1})$. Each $z^{-k}$ is a delay of $k$ samples. Move the $y$ terms to the right side and their signs change.
> - A: The denominator coefficients are copied with their printed signs. That is the MATLAB/SciPy convention (`a=[1, -0.3, 0.2]`), not the one of the slides.
> - C: Numerator and denominator are swapped (this is $1/H$), and the feedback sign is also not changed. Two mistakes.
> - D: Numerator and denominator are swapped. This filter has the inverse transfer function $1/H(z)$.

## 4. Poles and zeros (slides 9-13)

### Q8 (slide 9)

Write $f(x)=3x^2-3x-6$ as $G\,(x-q_1)(x-q_2)$. Which statement about the zeros of $f$, the poles of $1/f$ and the gain $G$ is correct?

- A) $f=3(x+2)(x-1)$. Zeros of $f$ and poles of $1/f$: $x=-2$ and $x=1$. Doubling $G$ leaves them in place.
- B) $f=3(x-2)(x+1)$. Zeros of $f$ and poles of $1/f$: $x=2$ and $x=-1$. Doubling $G$ halves both values.
- C) $f=3(x-2)(x+1)$. Zeros of $f$ and poles of $1/f$: $x=2$ and $x=-1$. Doubling $G$ leaves them in place.
- D) $f=3(x+2)(x-1)$. Zeros of $f$ and poles of $1/f$: $x=-2$ and $x=1$. Doubling $G$ halves both values.

> [!success]- Answer
> **C.** $f=3(x^2-x-2)=3(x-2)(x+1)$, so $G=3$, $q_1=2$, $q_2=-1$. Check: $f(2)=12-6-6=0$.
> $1/f$ divides by zero at the same two points, so they are its poles. Doubling $G$ doubles every value of $f$, but $2\cdot0=0$: the roots do not move.
> - A: The signs of the roots are flipped. $3(x+2)(x-1)=3x^2+3x-6$, not $f$. Check: $f(-2)=12+6-6=12\neq0$.
> - B: The roots are right, but the gain only stretches the curve up and down. It never moves a root.
> - D: Both mistakes: the root signs are flipped and the gain is said to move the roots.

### Q9 (slide 10)

![[cs568_lecture_05_l5_p010.jpg]]

The left plot shows several curves $g\,(2x^2-6x+1)$ for different gains $g$, and one of them opens downward. The right plot is labelled $1/(2x^2-6x+1)$, but its drawn curve is known to be wrong: reason from the label, not from the drawing. Which statement is correct?

- A) The downward curve has $0<g<1$. The right plot must blow up at $x=1.5$, where $f$ has its minimum.
- B) The downward curve has $g<0$. The right plot must blow up at the zero crossings, about $x=0.2$ and $x=2.8$.
- C) The downward curve has $g<0$. The right plot must blow up at $x=1.5$, where $f$ has its minimum.
- D) The downward curve has $0<g<1$. The right plot must blow up at the zero crossings, about $x=0.2$ and $x=2.8$.

> [!success]- Answer
> **B.** All curves cross zero at the same two points, the roots $(3\pm\sqrt7)/2\approx0.18$ and $2.82$. The gain only stretches the curve, and a negative $g$ also flips it, so it opens downward.
> $1/f$ divides by zero exactly where $f=0$, so it blows up at the roots. (Slide error: the drawn curve is off. It blows up at about $-0.15$ and $3.15$ and has the wrong sign between and outside the roots. Follow the label, not the drawing.)
> - A: A gain between 0 and 1 makes the curve flatter but it still opens upward. And at $x=1.5$, $f=-3.5$, so $1/f\approx-0.29$: no blow-up.
> - C: The sign of $g$ is right, but at the minimum $f=-3.5\neq0$. A pole needs $f=0$.
> - D: The blow-up points are right, but a positive $g$ never turns the parabola upside down.

### Q10 (slide 11)

$H(z)=\dfrac{1}{z^2-a_1z-a_2}$ is multiplied by $z^{-2}$ above and below, which gives $H(z)=\dfrac{z^{-2}}{1-a_1z^{-1}-a_2z^{-2}}$. Which statement is correct?

- A) The $z^{-1}$ form matches the difference equation. The $z^{-2}$ on top is a two-sample delay; the magnitude does not change.
- B) The step moves the poles inside the unit circle. The $z^{-2}$ on top is a two-sample delay; the magnitude does not change.
- C) The $z^{-1}$ form matches the difference equation. The $z^{-2}$ on top adds two zeros that cut the low frequencies.
- D) The step moves the poles inside the unit circle. The $z^{-2}$ on top adds two zeros that cut the low frequencies.

> [!success]- Answer
> **A.** $z^{-k}$ means a delay of $k$ samples, so the denominator $1-a_1z^{-1}-a_2z^{-2}$ gives the terms $a_1y[n-1]+a_2y[n-2]$ directly.
> The full equation is $y[n]=x[n-2]+a_1y[n-1]+a_2y[n-2]$. On the unit circle $\lvert z^{-2}\rvert=1$, so the magnitude response is the same.
> - B: Multiplying above and below by the same factor does not change $H$. The poles stay where they are.
> - C: The first part is right, but a factor $z^{-2}$ has no zero on or near the unit circle. Its size there is 1 at every frequency.
> - D: Both parts are wrong: the poles do not move, and $z^{-2}$ does not cut any frequency.

### Q11 (slide 12)

![[cs568_lecture_05_l5_p012.jpg]]

The filter is $y[n]=x[n]+a_1y[n-1]+a_2y[n-2]$ with $a_1=1$ and $a_2=-0.5$. The right plot shows two squares and a circle of radius 1. Now $a_2$ changes to $-0.9$, with $a_1=1$ as before. Which statement is correct?

- A) The squares are zeros. They move to $0.57$ and $-1.57$, and the one outside the circle makes the filter unstable.
- B) The squares are poles. They move to $0.57$ and $-1.57$, and the one outside the circle makes the filter unstable.
- C) The squares are poles. They move to about $0.5\pm0.81j$, radius about $0.95$: still stable, with sharper peaks.
- D) The squares are zeros. They move to about $0.5\pm0.81j$, radius about $0.95$: the dips of the response get deeper.

> [!success]- Answer
> **C.** The squares are the poles, the roots of $z^2-z+0.5=0$: $0.5\pm0.5j$, radius $\sqrt{0.5}\approx0.707$. The circle is the unit circle. The left plot has its peaks right above them.
> New poles: $z^2-z+0.9=0$, so $z=0.5\pm j\sqrt{0.65}\approx0.5\pm0.806j$, radius $\sqrt{0.9}\approx0.949<1$. Still stable. The poles are near the circle, so the resonance is much sharper.
> - A: Zeros make dips, but the surface has peaks over the squares. And zeros never decide stability.
> - B: Poles is right, but the polynomial $z^2+z-0.9$ has the feedback signs flipped. The right one is $z^2-a_1z-a_2=z^2-z+0.9$.
> - D: The new positions are right, but the squares are poles: the surface rises to peaks over them, it does not dip.

### Q12 (slide 13)

$H(z)=\dfrac{1-0.5z^{-1}}{(1-0.9z^{-1})(1+0.8z^{-1})}$, a causal filter. Which statement is correct?

- A) Poles at $0.9$ and $-0.8$, near $z=1$ and $z=-1$: stable, with large gain at both ends of the spectrum.
- B) Poles at $0.9$ and $0.8$, both near $z=1$: stable, with large gain only near $\hat\omega=0$, so low-pass.
- C) Poles at $0.9$ and $-0.8$: unstable, as the negative pole makes $h[n]$ alternate in sign without end.
- D) Zero at $0.5$, the root nearest $z=1$: stable, but the gain is small near $\hat\omega=0$, so high-pass.

> [!success]- Answer
> **A.** Zero $0.5$. Poles $0.9$ and $-0.8$: the factor $1+0.8z^{-1}$ is $1-(-0.8)z^{-1}$. Both have size below 1, so the filter is stable.
> $0.9$ is near $z=1$ ($\hat\omega=0$) and $-0.8$ is near $z=-1$ ($\hat\omega=\pi$). $\lvert H\rvert\approx2.78$ at $\hat\omega=0$, $\approx3.95$ at $\hat\omega=\pi$, only $\approx0.65$ at $\hat\omega=\pi/2$.
> - B: The sign of $1+0.8z^{-1}$ is ignored. That filter would have $\lvert H\rvert=25$ at $\hat\omega=0$ and $0.44$ at $\pi$.
> - C: An alternating sign is not growth. The term $(-0.8)^n$ dies out because $0.8<1$.
> - D: The pole $0.9$ is closer to $z=1$ than the zero $0.5$ and wins: $\lvert H\rvert\approx2.78$ at $\hat\omega=0$, not small.

## 5. Frequency response and the one-pole resonator (slides 14-17)

### Q13 (slide 14)

On the unit circle $z=e^{j\hat\omega}$. Which points belong to 0 Hz, to $F_S/4$ and to $F_S/2$, and why does the frequency response repeat every $2\pi$?

- A) $z=1$, $z=e^{j\pi/4}$, $z=j$. It repeats because adding $2\pi$ to $\hat\omega$ lands on the same point $z$.
- B) $z=1$, $z=j$, $z=-1$. It repeats because the impulse response $h[n]$ of every filter is periodic.
- C) $z=1$, $z=e^{j\pi/4}$, $z=j$. It repeats because the impulse response $h[n]$ of every filter is periodic.
- D) $z=1$, $z=j$, $z=-1$. It repeats because adding $2\pi$ to $\hat\omega$ lands on the same point $z$.

> [!success]- Answer
> **D.** $\hat\omega=2\pi F/F_S$. 0 Hz: $\hat\omega=0$, $z=1$. $F_S/4$: $\hat\omega=\pi/2$, $z=j$. $F_S/2$: $\hat\omega=\pi$, $z=-1$.
> $e^{j(\hat\omega+2\pi)}=e^{j\hat\omega}$: one more turn lands on the same $z$, and $H$ has the same value there.
> - A: The reason is right, but the points come from $\hat\omega=\pi F/F_S$, with the 2 of $2\pi$ lost.
> - B: The points are right, but $h[n]$ is not periodic (for example $0.8^n$). The repetition comes from the circle.
> - C: Both parts are wrong: the 2 of $2\pi$ is lost, and $h[n]$ is not periodic.

### Q14 (slide 15)

![[cs568_lecture_05_l5_p015.jpg]]

The filter is $y[n]=x[n]+y[n-1]-0.5\,y[n-2]$. At a point $z$ on the unit circle, $\lvert H\rvert$ is 1 divided by the product of the distances from $z$ to the two poles. How is the right curve obtained from the left surface, and why is its value at frequency 0 equal to 2?

- A) It is $\lvert H\rvert$ along the real axis. Frequency 0 is $z=0$, at distance $0.707$ from each pole, so $\lvert H\rvert=1/0.5=2$.
- B) It is $\lvert H\rvert$ along the unit circle. Frequency 0 is its centre $z=0$, $0.707$ from each pole, so $\lvert H\rvert=1/0.5=2$.
- C) It is $\lvert H\rvert$ along the unit circle. Frequency 0 is $z=1$, at distance $0.707$ from each pole, so $\lvert H\rvert=1/0.5=2$.
- D) It is $\lvert H\rvert$ along the real axis. Frequency 0 is $z=1$, at distance $0.707$ from each pole, so $\lvert H\rvert=1/0.5=2$.

> [!success]- Answer
> **C.** The right curve is the height of the surface along the dark ring, the unit circle, laid flat against $\hat\omega$ from $-\pi$ to $\pi$.
> The poles are the roots of $z^2-z+0.5=0$: $0.5\pm0.5j$. $\hat\omega=0$ is $z=e^{j0}=1$. $\lvert1-(0.5\pm0.5j)\rvert=\sqrt{0.5}\approx0.707$, so $\lvert H\rvert=1/(0.707\cdot0.707)=2$. The two peaks (about 2.8) are where the circle passes nearest to each pole.
> - A: The frequency response is taken on the unit circle, not on the real axis. $z=0$ is not on the circle, so the distance rule does not apply there (in fact $H(0)=0$).
> - B: The centre $z=0$ is not on the circle, so it is no frequency and the distance rule does not apply there. Frequency 0 is the point $z=1$.
> - D: The point $z=1$ is right, but the curve follows the whole circle. The real axis meets the circle only at $z=\pm1$.

### Q15 (slide 16)

The filter is $H(z)=\dfrac{b_0}{1-a_1z^{-1}}$ with $b_0=1$. What do $b_0$ and $a_1$ do, and what do you get with $a_1=-0.8$ instead of $a_1=0.8$?

- A) $b_0$ scales; the pole sits at $z=+0.8$, near $z=1$: still low-pass, with $\lvert H\rvert=5$ at $\hat\omega=0$.
- B) $b_0$ scales; the pole sits at $z=-0.8$, near $z=-1$: high-pass, with $\lvert H\rvert=5$ at $\hat\omega=\pi$.
- C) $b_0$ sets the pole and $a_1$ only scales: $-0.8$ flips the sign, and the shape stays low-pass.
- D) $b_0$ scales; the pole sits at $z=1/a_1=-1.25$, outside the circle: the filter becomes unstable.

> [!success]- Answer
> **B.** $b_0$ is the gain: it scales the whole response. $1-a_1z^{-1}=0$ at $z=a_1$, so $a_1$ is the pole position. It sets the decay of $h[n]=b_0a_1^n$ and the sharpness of the peak.
> $a_1=-0.8$: the pole is near $z=-1$, $\hat\omega=\pi$. $\lvert H\rvert=1/\lvert-1+0.8\rvert=5$ there and $1/1.8\approx0.56$ at $\hat\omega=0$. High-pass. $h[n]=(-0.8)^n$ alternates.
> - A: The feedback sign is flipped: with $-a_1z^{-1}$ in the denominator the pole is at $z=a_1=-0.8$, not $+0.8$.
> - C: The roles are swapped. $b_0$ is in the numerator and has no root; $a_1$ is the pole and moves the peak.
> - D: $1/a_1$ is the root in the variable $z^{-1}$. In $z$ the pole is at $a_1=-0.8$, inside the circle.

### Q16 (slide 17)

![[cs568_lecture_05_l5_p017.jpg]]

The panels show the same four filters $H(z)=1/(1-a_1z^{-1})$ with $a_1=0.8,\ 0.7,\ 0.6,\ 0.4$: impulse responses (top left), frequency responses (top right) and poles (bottom left). Which statement is correct?

- A) $a_1=0.4$: the slowest decay, the tallest peak (about 5), the square nearest the circle. All peaks are at 0 because every $h[n]$ starts at $h[0]=1$.
- B) $a_1=0.4$: the fastest decay, the flattest curve (peak about 1.67), the square nearest the origin. All peaks are at 0 because every $h[n]$ starts at $h[0]=1$.
- C) $a_1=0.4$: the slowest decay, the tallest peak (about 5), the square nearest the circle. All peaks are at 0 because all poles lie at angle 0, near $z=1$.
- D) $a_1=0.4$: the fastest decay, the flattest curve (peak about 1.67), the square nearest the origin. All peaks are at 0 because all poles lie at angle 0, near $z=1$.

> [!success]- Answer
> **D.** $h[n]=0.4^n$ dies out first. The peak is $1/(1-0.4)\approx1.67$, the lowest of the four ($a_1=0.8$ gives 5). The pole at $z=0.4$ is the square nearest the origin.
> All four poles lie on the positive real axis, angle 0. The nearest point of the unit circle is $z=1$, which is $\hat\omega=0$.
> - A: This describes $a_1=0.8$, the pole nearest the circle. And $h[0]=1$ is about time 0, not frequency 0.
> - B: The curves are right, but $h[0]=1$ holds for every pole position. It says nothing about where the peak is.
> - C: The reason is right, but this describes $a_1=0.8$. A larger $a_1$ puts the pole nearer the circle.

## 6. Conjugate pole pairs and zeros (slides 18-21)

### Q17 (slide 18)

A resonator has the conjugate pole pair $re^{\pm j\theta}$ with $r=0.9$ and $\theta=\pi/3$, and numerator 1. With the sign convention $y[n]=x[n]+a_1y[n-1]+a_2y[n-2]$, which difference equation is right?

- A) $y[n]=x[n]+0.9\,y[n-1]-0.81\,y[n-2]$
- B) $y[n]=x[n]+0.9\,y[n-1]-0.9\,y[n-2]$
- C) $y[n]=x[n]+0.45\,y[n-1]-0.9\,y[n-2]$
- D) $y[n]=x[n]+0.45\,y[n-1]-0.81\,y[n-2]$

> [!success]- Answer
> **A.** $(1-re^{j\theta}z^{-1})(1-re^{-j\theta}z^{-1})=1-2r\cos\theta\,z^{-1}+r^2z^{-2}$. So $a_1=2r\cos\theta=2\cdot0.9\cdot0.5=0.9$ and $a_2=-r^2=-0.81$.
> The conjugate partner is needed so that the imaginary parts cancel and the coefficients are real. The resonance is near $\hat\omega=\pi/3\approx1.05$ rad.
> - B: $a_2=-r$ instead of $-r^2$. The product of the two poles is $re^{j\theta}\cdot re^{-j\theta}=r^2$.
> - C: Two mistakes: the 2 of $2r\cos\theta$ is lost, and $a_2=-r$ instead of $-r^2$.
> - D: $a_1=r\cos\theta$, the 2 is lost. $e^{j\theta}+e^{-j\theta}=2\cos\theta$, not $\cos\theta$.

### Q18 (slide 19)

Slide 19 compares two families of two-pole resonators. Family 1: $\theta=1.0$ fixed, $r=0.9,\ 0.8,\ 0.7,\ 0.6$. Family 2: $r=0.8$ fixed, $\theta=0.75,\ 1.0,\ 1.25,\ 1.5$. Which statement about their pole plots and spectra is correct?

- A) Family 1 poles lie on an arc, family 2 on a ray. Family 1 peaks stay near $\pm1$ and sharpen as $r$ grows; family 2 peaks move outward.
- B) Family 1 poles lie on a ray, family 2 on an arc. Family 1 peaks move outward as $r$ grows; family 2 peaks stay in place and only sharpen.
- C) Family 1 poles lie on an arc, family 2 on a ray. Family 1 peaks move outward as $r$ grows; family 2 peaks stay in place and only sharpen.
- D) Family 1 poles lie on a ray, family 2 on an arc. Family 1 peaks stay near $\pm1$ and sharpen as $r$ grows; family 2 peaks move outward.

> [!success]- Answer
> **D.** Same angle, different distance: the poles lie on one ray from the origin. Same distance, different angle: they lie on an arc around the origin.
> The angle sets the frequency of the peak, the radius its height and width. Family 1 keeps its peaks at about $\pm1$, from about 6 high ($r=0.9$) to below 2 ($r=0.6$). Family 2 moves its peaks from about $\pm0.75$ to about $\pm1.5$.
> - A: The geometry is swapped. A fixed angle gives a straight line through the origin, a fixed radius gives a circle.
> - B: The geometry is right, but the radius does not set the peak frequency. The angle does.
> - C: Both are swapped: the geometry, and the roles of radius and angle.

### Q19 (slide 20)

![[cs568_lecture_05_l5_p020.jpg]]

Slide 20 adds a zero factor $1-b_1z^{-1}$ to a two-pole resonator: $b_1=1$ in the left plot, $b_1=-1$ in the right plot. Where are the two zeros in the $z$-plane, and why are the peaks taller on the right (about 11) than on the left (about 6)?

- A) Left zero at $z=0$, right zero at $z=-1$. The right peaks are taller because its poles sit closer to the unit circle.
- B) Left zero at $z=1$, right zero at $z=-1$. The peak point $\hat\omega\approx1$ is 1.76 from $z=-1$ but only 0.96 from $z=1$.
- C) Left zero at $z=1$, right zero at $z=-1$. The right peaks are taller because its poles sit closer to the unit circle.
- D) Left zero at $z=0$, right zero at $z=-1$. The peak point $\hat\omega\approx1$ is 1.76 from $z=-1$ but only 1 from $z=0$.

> [!success]- Answer
> **B.** Left: factor $1-z^{-1}$, zero at $z=1$, so the gain is 0 at frequency 0. Right: factor $1+z^{-1}$, zero at $z=-1$, gain 0 at $\pm\pi$.
> A zero factor multiplies $\lvert H\rvert$ by the distance from the zero to the point on the circle. At $\hat\omega\approx1$: $\lvert e^{j}-1\rvert\approx0.96$, $\lvert e^{j}+1\rvert\approx1.76$. The ratio $1.83$ matches $11/6$.
> - A: "Zero at the origin" on the slide means the origin of the frequency axis, $z=1$. A zero at $z=0$ would not change $\lvert H\rvert$ on the circle. And the poles are the same in both plots: the peaks sit at the same place.
> - C: The zeros are right, but both plots use the same poles. Only the zero factor differs.
> - D: The distance idea is right, but a zero at $z=0$ is 1 away from every point of the circle. It could not make the left curve touch 0 at frequency 0.

### Q20 (slide 21)

A filter has two pole pairs with $r=0.8$, one at $\theta=1.0$ and one at $\theta=1.3$ rad. A student says: "It has two resonance peaks, exactly at 1.0 and 1.3 rad." Which statement is correct?

- A) Wrong: the two broad peaks merge into one, at about 1.1 rad. With $r=0.9$ two peaks appear, at about 1.03 and 1.25 rad.
- B) Right: each pole pair adds its own peak at its own angle $\theta$, whatever the other pole pair of the filter does.
- C) Wrong: the two broad peaks merge into one, at about 1.1 rad. With $r=0.6$ each peak gets narrower and two peaks appear.
- D) Wrong: there are two peaks, but each pole pair pushes the other away, so they sit outside 1.0 and 1.3 rad.

> [!success]- Answer
> **A.** The poles are close together and far from the circle, so each gives a broad peak. The two merge: the filter has one peak, at about 1.11 rad.
> With $r=0.9$ each peak is narrower and two peaks appear, at about 1.03 and 1.25 rad: still pulled a little towards each other. Poles that are close interact.
> - B: This ignores the interaction that slide 21 warns about. At $r=0.8$ there is only one peak.
> - C: The first part is right, but a smaller $r$ makes each peak broader. With $r=0.6$ there is still one peak, at about 1.07 rad.
> - D: The skirts of the two poles add up between them. They pull the peaks together, not apart.

## 7. Example: synthesising the vowel [ih] (slides 22-24)

### Q21 (slide 22)

In the [ih] synthesis each formant (frequency $F$, bandwidth $B=250$ Hz for all three) becomes a pole pair $re^{\pm j\theta}$. At $F_S=16$ kHz the 2200 Hz formant has $\theta\approx0.864$ rad and $r\approx0.952$. What are $\theta$ and $r$ of this formant if $F_S$ is doubled to 32 kHz?

- A) $\theta\approx0.432$ rad, half of before, and $r\approx0.976$, closer to 1.
- B) $\theta\approx0.864$ rad, the same as before, and $r\approx0.976$, closer to 1.
- C) $\theta\approx0.432$ rad, half of before, and $r\approx0.952$, the same as before.
- D) $\theta\approx0.864$ rad, the same as before, and $r\approx0.952$, the same as before.

> [!success]- Answer
> **A.** $\theta=2\pi F/F_S=2\pi\cdot2200/32000\approx0.432$ rad. The same frequency in Hz is a smaller part of the circle.
> $r=e^{-\pi B/F_S}=e^{-\pi\cdot250/32000}=e^{-0.0245}\approx0.976$. At 16 kHz all three formants share $r\approx0.952$ because $r$ depends only on $B$ and $F_S$, and $B$ is the same.
> - B: $\theta$ depends on $F/F_S$, not on $F$ alone. With twice the sampling rate the angle halves.
> - C: $r$ also has $F_S$ in it. A larger $F_S$ makes the exponent smaller, so $r$ moves towards 1.
> - D: The formant in Hz stays the same, but both pole coordinates are measured relative to $F_S$, so both change.

### Q22 (slide 23)

![[cs568_lecture_05_l5_p023.jpg]]

The vowel filter is $H(z)=H_1(z)H_2(z)H_3(z)$, one resonator per formant, at $F_S=16$ kHz. Which statement about the pole plot and the frequency axis is correct?

- A) Six squares, but $H(z)$ has order 3, one per formant. The axis ends at 8000 Hz, the point $z=j$ at angle $\pi/2$.
- B) Six squares, so $H(z)$ has order 6. The axis ends at 8000 Hz, the point $z=j$ at angle $\pi/2$.
- C) Six squares, so $H(z)$ has order 6. The axis ends at 8000 Hz, the point $z=-1$ at angle $\pi$.
- D) Six squares, but $H(z)$ has order 3, one per formant. The axis ends at 8000 Hz, the point $z=-1$ at angle $\pi$.

> [!success]- Answer
> **C.** Three conjugate pairs, one per formant: six poles. Each resonator has order 2, so the product has order 6.
> $F=\hat\omega F_S/(2\pi)$. 8000 Hz $=F_S/2$ is $\hat\omega=\pi$, the point $z=-1$, the highest frequency. The pair nearest $z=1$ (smallest angle) is $F_1$. The peaks are at about 300, 2200 and 3000 Hz.
> - A: Two mistakes: each formant needs two poles, so the order is 6; and $\pi/2$ comes from $F=\hat\omega F_S/\pi$, the 2 of $2\pi$ lost.
> - B: The order is right, but $z=j$ is $F_S/4=4000$ Hz. The angle $\pi/2$ comes from losing the 2 of $2\pi$.
> - D: The point is right, but the order counts poles, not formants. Three pairs give order 6.

### Q23 (slide 24)

The vowel filter has formants at 300, 2200 and 3000 Hz. It is excited with a pulse train at $F_S=16$ kHz, and the pulse period changes from 160 to 128 samples. Which statement is correct?

- A) $F_0$ goes from 100 to 80 Hz. The formants stay at 300, 2200 and 3000 Hz, so the vowel is still [ih].
- B) $F_0$ goes from 100 to 125 Hz. The formants scale with it, to 375, 2750 and 3750 Hz, so the vowel changes.
- C) $F_0$ goes from 100 to 80 Hz. The formants scale with it, to 240, 1760 and 2400 Hz, so the vowel changes.
- D) $F_0$ goes from 100 to 125 Hz. The formants stay at 300, 2200 and 3000 Hz, so the vowel is still [ih].

> [!success]- Answer
> **D.** The pulse train is the source and sets the pitch: $F_0=F_S/\text{period}$. $16000/160=100$ Hz, $16000/128=125$ Hz. A shorter period is a higher pitch.
> The filter $H(z)$ sets the formants. The spectral lines move further apart, but their envelope keeps its peaks at 300, 2200 and 3000 Hz.
> - A: $100\cdot128/160=80$ treats $F_0$ as proportional to the period. It is inversely proportional.
> - B: The pitch is right, but the source does not move the poles of the filter. The formants stay.
> - C: Two mistakes: the pitch goes the wrong way, and the formants are moved with it.
