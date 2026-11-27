---
course: CS 568 Speech Processing
lecture: 5
title: Digital filters
tags: [cs568, lecture]
slides: lectures/lecture_05/l5.pdf
full_notes: lecture_notes/lecture_05/CS568_Lecture_05_Digital_Filters.pdf
---
# CS 568 · Lecture 5 · Digital filters

> [!summary] In short
> - An LTI filter is linear and time-invariant. FIR uses only inputs. IIR also feeds back past outputs.
> - The impulse response $h[n]$ describes the filter in time. Any output is the convolution $y[n]=\sum_k x[k]\,h[n-k]$.
> - The transfer function $H(z)=Y(z)/X(z)$ is a ratio of polynomials in $z^{-1}$, and the $z$-transform of $h[n]$.
> - Poles (denominator roots) make peaks, zeros (numerator roots) make dips. $H$ on the unit circle is the frequency response.
> - A pole pair $re^{\pm j\theta}$: the angle $\theta$ sets the frequency, the radius $r$ sets the sharpness. $r>1$ is unstable.
> - A formant $(F,B)$ is a pole pair with $\theta=2\pi F/F_S$, $r=e^{-\pi B/F_S}$. Cascading three gives a vowel.

## 1. Filters, LTI, FIR and IIR (slides 2-4)
![[cs568_lecture_05_l5_p002.jpg]]
![[cs568_lecture_05_l5_p003.jpg]]
![[cs568_lecture_05_l5_p004.jpg]]

**Notes.** A **filter** is a model of a system that modifies signals: $x[n]$ goes in, $y[n]$ comes out. In speech the vocal tract is a filter acting on the glottal source.

An **LTI** filter is

- **linear:** $\alpha x_1[n]+\beta x_2[n]\mapsto\alpha y_1[n]+\beta y_2[n]$,
- **time-invariant:** $x[n-n_0]\mapsto y[n-n_0]$. Delaying the input only delays the output.

Two types, written as difference equations:

$$\text{FIR:}\quad y[n]=\sum_{k=0}^{M}b_k\,x[n-k]\qquad\qquad\text{IIR:}\quad y[n]=\sum_{k=0}^{M}b_k\,x[n-k]+\sum_{l=1}^{N}a_l\,y[n-l]$$

The $b_k$ (feed-forward) and $a_l$ (feedback) are the filter coefficients. Slide 4 shows the block diagrams.

> [!warning] Slide error
> Slide 3 starts the feedback sum at $l=0$. That term would be $a_0y[n]$: the output would depend on itself. The sum starts at $l=1$.

> [!info] Reading the figures of slides 2 and 4 (beyond the slides)
> **Slide 2.** The figure shows the source-filter model as a product of spectra, $P(s)=U(s)\,T(s)\,R(s)$.
> - $U$: spectrum of the glottal source. Many harmonic lines that get weaker towards high frequencies.
> - $T$: transfer function of the vocal tract (ses yolu). Its peaks are the formants. This is the filter (süzgeç) of this lecture.
> - $R$: radiation at the lips. The dashed line rises: it boosts high frequencies.
> - $P$: the speech spectrum that comes out. It has the harmonics of $U$ under the envelope of $T$ and $R$.
>
> The spectra are multiplied because the three stages are connected one after the other. A cascade in time (convolution) is a product in frequency.
>
> **Slide 4.** $B(z)=\sum_k b_kz^{-k}$ is the feed-forward part with impulse response (dürtü yanıtı) $h_b(n)$. $A(z)=\sum_{l\ge1} a_lz^{-l}$ is the feedback (geri besleme) part with impulse response $h_a(n)$. In the FIR diagram the signal goes straight through $B(z)$. In the IIR diagram the output $y(n)$ is also sent through $A(z)$ and returned to the input side. This loop is the only difference between the two filter types.

> [!question]- Check (slide 2): The figure writes $P(s)=U(s)\,T(s)\,R(s)$. Say what each of the four terms is. Which term changes when the speaker moves the tongue to say another vowel, and which changes when the pitch is raised?
> $U$ is the spectrum of the glottal source, $T$ the transfer function of the vocal tract filter, $R$ the radiation at the lips, and $P$ the spectrum of the speech that comes out.
> Moving the tongue changes the shape of the vocal tract, so $T$ changes: its peaks (the formants) move.
> Raising the pitch changes the source $U$: its harmonic lines move further apart. $T$ stays the same.

> [!question]- Check (slide 3): In the IIR equation, say what $b_k$, $a_l$, $M$ and $N$ are. Then find the mistake: "$y[n]=x[n]+0.5\,x[n-1]$ is an IIR filter, because it uses a value from the past."
> $b_k$ are the feed-forward coefficients. They weight the current and past inputs $x[n-k]$. $M$ is the number of past inputs used.
> $a_l$ are the feedback coefficients. They weight past outputs $y[n-l]$. $N$ is the number of past outputs used.
> The mistake: the filter uses a past **input**, and that is still FIR. A filter is IIR only if it uses past **outputs** (some $a_l\neq0$). Here $b_0=1$, $b_1=0.5$, $M=1$ and all $a_l=0$.

> [!question]- Check (slide 4): In the IIR diagram, where does the $A(z)$ box take its input from and where does its output go? What does the diagram become if you remove this box, and why can only the IIR filter be unstable?
> The $A(z)$ box takes the output $y(n)$ and sends its result back into the $B(z)$ block on the input side. This is a feedback loop.
> Without the box only the straight path through $B(z)$ is left. That is the FIR diagram on the left.
> In a loop a signal can go around again and again. If the loop makes it larger on each round, the output grows without limit. An FIR filter has no loop: its output is a finite weighted sum of inputs, so it is always stable.

## 2. The impulse response (slides 5-6)
![[cs568_lecture_05_l5_p005.jpg]]
![[cs568_lecture_05_l5_p006.jpg]]

**Notes.** The **unit impulse** $\delta[n]$ is 1 at $n=0$ and 0 elsewhere. The output for $x[n]=\delta[n]$ is the **impulse response** $h[n]$.

- FIR: $h[n]=b_n$ for $0\le n\le M$, then zero. It lasts $M+1$ samples.
- IIR: each output is fed back, so in general $h[n]$ never becomes exactly zero.

First-order IIR $y[n]=x[n]+a_1y[n-1]$ has $h[n]=a_1^n$. It is **stable** if $|a_1|<1$ and **unstable** (grows) if $|a_1|>1$.

> [!example] $y[n]=x[n]-0.8\,y[n-1]$
> $h=[1,\ -0.8,\ 0.64,\ -0.512,\ 0.4096,\dots]=(-0.8)^n$. A decay with alternating sign.

Second-order examples: $y[n]=x[n]-1.8\,y[n-1]-y[n-2]$ gives a sustained oscillation, $h=[1,-1.8,2.24,-2.232,1.7776,\dots]$ (poles on the unit circle). $y[n]=x[n]-1.78\,y[n-1]-0.9\,y[n-2]$ gives a decaying oscillation (poles at radius $\sqrt{0.9}\approx0.949$).

> [!warning] Slide errors
> - Slide 5 swaps the two cases of $\delta[n]$.
> - "For $a_1>1$" should be $|a_1|>1$: $a_1=-1.2$ also blows up.
> - The two second-order filters are printed as $y[n]=-1.8y[n-1]+y[n-2]$ and $y[n]=-1.78y[n-1]+0.9y[n-2]$, without $x[n]$. With those plus signs one root lies outside the unit circle and both would blow up. The sine and decaying-sine behaviour needs the minus signs used above.

> [!question]- Check (slide 5): The filter is $y[n]=x[n]+a_1y[n-1]$. Describe the impulse response for $a_1=0.9$, $a_1=-0.9$ and $a_1=-1.2$. Which of the three is unstable?
> The impulse response is $h[n]=a_1^n$.
> $a_1=0.9$: $1,\ 0.9,\ 0.81,\ 0.729,\dots$ A smooth decay. Stable.
> $a_1=-0.9$: $1,\ -0.9,\ 0.81,\ -0.729,\dots$ The same decay, but the sign alternates. Stable.
> $a_1=-1.2$: $1,\ -1.2,\ 1.44,\ -1.728,\dots$ The size grows and the sign alternates. Unstable.
> The size $|a_1|$ decides stability. The sign of $a_1$ only decides if $h[n]$ alternates.

> [!question]- Check (slide 6): The script plots the impulse response of an FIR filter and of an IIR filter. How can you tell from the two plots which is which? How many non-zero samples can an FIR filter with $M=4$ have at most?
> The FIR response is exactly zero after sample $n=M$. The IIR response only gets smaller and smaller (if the filter is stable), it never becomes exactly zero.
> For FIR, $h[n]=b_n$ for $0\le n\le M$. With $M=4$ these are $b_0,\dots,b_4$: at most $M+1=5$ non-zero samples.

## 3. Convolution sum and transfer function (slides 7-8)
![[cs568_lecture_05_l5_p007.jpg]]
![[cs568_lecture_05_l5_p008.jpg]]

**Notes.** If $h[n]$ is known, the output for any input is

$$y[n]=\sum_k x[k]\,h[n-k]$$

To move to the frequency domain, take the $z$-transform of the difference equation. A delay of $k$ samples becomes a factor $z^{-k}$. Collect the $Y$ terms and divide:

$$H(z)=\frac{Y(z)}{X(z)}=\frac{b_0+b_1z^{-1}+\cdots+b_Mz^{-M}}{1-a_1z^{-1}-\cdots-a_Nz^{-N}}$$

So $Y(z)=H(z)\,X(z)$. $H(z)$ is also the $z$-transform of the impulse response, $H(z)=\sum_k h[k]z^{-k}$.

> [!example] Two ways
> $y[n]=x[n]-0.8\,y[n-1]$: $Y(1+0.8z^{-1})=X$, so $H(z)=\dfrac{1}{1+0.8z^{-1}}$. The series $\sum_n(-0.8)^nz^{-n}$ gives the same.

> [!warning] Slide errors
> Slide 8 writes the highest powers as $z^{M-1}$ and $z^{N-1}$ (they are $z^{-M}$, $z^{-N}$) and the denominator as $\sum_{l=0}^{N}a_lz^{-l}$. With our sign convention it is $1-\sum_{l=1}^{N}a_lz^{-l}$. MATLAB `filter(b,a,x)` and SciPy `lfilter` use the opposite signs: for the filter above, `a=[1, 0.8]`.

> [!info] Why the convolution sum works (beyond the slides)
> Any input is a sum of shifted and scaled impulses: $x[n]=\sum_k x[k]\,\delta[n-k]$.
> - Time invariance: the shifted impulse $\delta[n-k]$ gives the shifted response $h[n-k]$.
> - Linearity: the scaled impulse $x[k]\,\delta[n-k]$ gives $x[k]\,h[n-k]$, and the sum of all impulses gives the sum of all responses.
>
> So $y[n]=\sum_k x[k]\,h[n-k]$. Each input sample starts its own copy of $h$, and the output is the sum of all copies. For this reason $h[n]$ describes an LTI filter completely.

> [!question]- Check (slide 7): In $y[n]=\sum_k x[k]\,h[n-k]$, what does the term $x[k]\,h[n-k]$ mean for one fixed $k$? Which two properties of the filter does the formula need?
> For one $k$ it is the impulse response started at time $k$ (shifted by $k$) and scaled by the input sample $x[k]$. It is the part of the output that this one input sample causes.
> The shift needs time invariance: a delayed impulse gives the same response, only delayed. The scaling and the sum over $k$ need linearity. So the formula holds only for LTI filters.

> [!question]- Check (slide 8): A filter has $H(z)=\dfrac{1+0.5z^{-1}}{1-0.3z^{-1}+0.2z^{-2}}$. Which part comes from the feed-forward coefficients and which from the feedback? Write the difference equation.
> The numerator holds the feed-forward coefficients: $b_0=1$, $b_1=0.5$. The denominator holds the feedback coefficients with the sign changed: $1-a_1z^{-1}-a_2z^{-2}$, so $a_1=0.3$ and $a_2=-0.2$.
> $Y(z)\,(1-0.3z^{-1}+0.2z^{-2})=X(z)\,(1+0.5z^{-1})$. Each $z^{-k}$ is a delay of $k$ samples:
> $$y[n]=x[n]+0.5\,x[n-1]+0.3\,y[n-1]-0.2\,y[n-2]$$
> For an FIR filter the denominator is just 1.

## 4. Poles and zeros (slides 9-13)
![[cs568_lecture_05_l5_p009.jpg]]
![[cs568_lecture_05_l5_p010.jpg]]
![[cs568_lecture_05_l5_p011.jpg]]
![[cs568_lecture_05_l5_p012.jpg]]
![[cs568_lecture_05_l5_p013.jpg]]

**Notes.** Coefficients of high-order filters are hard to read. Factor the polynomials instead. $f(x)=2x^2-6x+1=2(x-q_1)(x-q_2)$ with $q_{1,2}=(3\pm\sqrt7)/2\approx0.177,\ 2.823$. These roots are **zeros** of $f$. The inverse $1/f(x)$ blows up at the same roots: there they are **poles**. The roots capture the shape, the gain only scales.

Any LTI filter in factored form, with gain $G=b_0$:

$$H(z)=G\,\frac{(1-q_1z^{-1})(1-q_2z^{-1})\cdots(1-q_Mz^{-1})}{(1-p_1z^{-1})(1-p_2z^{-1})\cdots(1-p_Nz^{-1})}$$

The $q_i$ are the zeros and the $p_i$ the poles. A factor $(1-qz^{-1})$ vanishes at $z=q$.

> [!example] Slide 11, $a_1=1$, $a_2=-0.5$
> Poles are the roots of $z^2-z+0.5=0$: $z=0.5\pm0.5j$, radius $\sqrt{0.5}\approx0.707<1$. Slide 12 shows $|H(z)|$ over the $z$-plane with peaks at the poles.

> [!warning] Slide errors
> Slide 11 writes the second factor as $(1-p_2z^{-2})$. It is $(1-p_2z^{-1})$. Slide 13 writes $z^{M}$, $z^{N}$ for $z^{-M}$, $z^{-N}$ and drops the gain $G$.

> [!warning] Slide error
> The right plot of slide 10 is labelled $1/(2x^2-6x+1)$, but the drawn curve does not fit this label. Between the two roots $f$ is negative ($f(1.5)=-3.5$), so $1/f$ is negative there (about $-0.29$) and positive outside the roots. The plot shows the opposite: a positive value (about $0.2$) between the two blow-ups and negative values outside. The blow-ups are also drawn at about $x=-0.15$ and $x=3.15$, not at the roots $0.18$ and $2.82$. The idea of the slide is still right: $1/f$ blows up where $f$ is zero.

> [!info] Poles, zeros and stability in one place (beyond the slides)
> - A **zero** (sıfır) $q$ is a value of $z$ where $H(z)=0$. A **pole** (kutup) $p$ is a value of $z$ where $H(z)$ becomes infinite.
> - Each pole $p$ adds a term that behaves like $p^n$ to the impulse response. If $|p|<1$ this term dies out. If $|p|>1$ it grows.
> - So a causal filter (one that uses only present and past samples) is **stable** (kararlı) when all its poles are inside the unit circle (birim çember). Zeros can lie anywhere. They do not affect stability.
> - On slide 12 the left plot is $|H(z)|$ as a surface over the $z$-plane. The right plot is the same information seen from above: only the pole positions (squares) and the unit circle. The pole positions are enough to know the shape.

> [!question]- Check (slide 9): Write $f(x)=3x^2-3x-6$ in the form $G(x-q_1)(x-q_2)$. What are the zeros of $f$ and the poles of $1/f$? What would change if $G$ were doubled?
> $f(x)=3(x^2-x-2)=3(x-2)(x+1)$. So $G=3$, $q_1=2$, $q_2=-1$.
> The zeros of $f$ are $x=2$ and $x=-1$. The poles of $1/f$ are the same two values: there $1/f$ divides by zero.
> Doubling $G$ makes every value of $f$ twice as large (and of $1/f$ half as large). The roots do not move.

> [!question]- Check (slide 10): The left plot shows several curves of $g\,(2x^2-6x+1)$. What do all curves have in common, and what differs? One curve opens downward: what does that say about its $g$? Where does the right plot blow up?
> All curves cross zero at the same two points, about $x=0.2$ and $x=2.8$. These are the roots. The gain $g$ only stretches the curve up and down.
> The curve that opens downward has a negative $g$. It still crosses zero at the same points.
> The right plot blows up at two $x$ values. They should be the same two roots, because the zeros of the polynomial are the poles of its inverse. (The drawn curve is a little off, see the warning above.)

> [!question]- Check (slide 11): Why is $H(z)=\dfrac{1}{z^2-a_1z-a_2}$ multiplied by $z^{-2}$ above and below? What does the $z^{-2}$ that stays in the numerator do to the filter?
> The form in $z^{-1}$ matches the difference equation, because $z^{-k}$ means a delay of $k$ samples. The denominator $1-a_1z^{-1}-a_2z^{-2}$ gives the feedback terms $a_1y[n-1]+a_2y[n-2]$ directly.
> The numerator $z^{-2}$ is a pure delay of two samples: $y[n]=x[n-2]+a_1y[n-1]+a_2y[n-2]$. On the unit circle $|z^{-2}|=1$, so it does not change the magnitude response. The poles are the same in both forms.

> [!question]- Check (slide 12): In the right plot, what are the two squares and what is the circle? Is this filter stable? If $a_2$ changed from $-0.5$ to $-0.9$ (with $a_1=1$), where would the squares go and how would the left plot change?
> The squares are the two poles at $0.5\pm0.5j$. The circle is the unit circle. Both poles are inside it (radius $\sqrt{0.5}\approx0.707$), so the filter is stable. In the left plot the two peaks of $|H(z)|$ stand at these positions.
> New poles: roots of $z^2-z+0.9=0$, so $z=0.5\pm j\sqrt{0.9-0.25}\approx0.5\pm0.806j$. The radius is $\sqrt{0.9}\approx0.949$.
> The squares move up and down, close to the unit circle. The filter is still stable. The peaks of the surface now stand almost on the circle, so the frequency response gets a much sharper resonance.

> [!question]- Check (slide 13): $H(z)=\dfrac{1-0.5z^{-1}}{(1-0.9z^{-1})(1+0.8z^{-1})}$. List the zeros and the poles. Is the filter stable? Near which frequencies do you expect large gain?
> Zero: $q_1=0.5$. Poles: $p_1=0.9$ and $p_2=-0.8$ (the factor $1+0.8z^{-1}$ is $1-(-0.8)z^{-1}$).
> Both poles have size below 1, so the (causal) filter is stable.
> $p_1=0.9$ lies close to $z=1$, which is $\hat\omega=0$. $p_2=-0.8$ lies close to $z=-1$, which is $\hat\omega=\pi$. So the gain is large at both ends of the spectrum and small in the middle: $|H|\approx2.78$ at $\hat\omega=0$, $\approx3.95$ at $\hat\omega=\pi$, and $\approx0.65$ at $\hat\omega=\pi/2$.

## 5. Frequency response and the one-pole resonator (slides 14-17)
![[cs568_lecture_05_l5_p014.jpg]]
![[cs568_lecture_05_l5_p015.jpg]]
![[cs568_lecture_05_l5_p016.jpg]]
![[cs568_lecture_05_l5_p017.jpg]]

**Notes.** The Fourier transform is the $z$-transform at $z=e^{j\hat\omega}$. So the **frequency response** is $H(e^{j\hat\omega})$: walk around the unit circle. Geometric rule:

$$|H(e^{j\hat\omega})|=|G|\,\frac{\prod_i|e^{j\hat\omega}-q_i|}{\prod_i|e^{j\hat\omega}-p_i|}$$

Each factor is the length of the arrow from a zero or pole to the point on the circle. Near a pole the response is large, near a zero it is small.

First-order IIR $y[n]=b_0x[n]+a_1y[n-1]$, $H(z)=\dfrac{b_0}{1-a_1z^{-1}}$: a **resonator**, the peak is a resonance. Slide 17 shows $a_1=0.8, 0.7, 0.6, 0.4$. Large $a_1$ (pole close to the circle): slow decay, narrow bandwidth, sharp peak. Small $a_1$: fast decay, broad bandwidth.

> [!example] Peak height
> $H(z)=1/(1-0.8z^{-1})$. At $\hat\omega=0$ ($z=1$): $1/0.2=5$. At $\hat\omega=\pi$ ($z=-1$): $1/1.8\approx0.56$.

> [!warning] Slide error
> Slide 16 writes "$h[n]=b_0x[n]-a_1y[n-1]$" and "$b_1=1$". To match $H(z)$ it must be $y[n]=b_0x[n]+a_1y[n-1]$ with $b_0=1$.

> [!info] From the unit circle to Hz, and low-pass or high-pass (beyond the slides)
> - The normalised frequency $\hat\omega$ is the angle of the point $z=e^{j\hat\omega}$ on the unit circle. In Hz it is $F=\hat\omega\,F_S/(2\pi)$. So $z=1$ is 0 Hz, $z=j$ is $F_S/4$ and $z=-1$ is $F_S/2$ (the highest frequency).
> - The frequency response (frekans yanıtı) is the height of the surface $|H(z)|$ along this circle. Slide 15 shows it: the circle drawn on the surface on the left, cut open and laid flat, is the curve on the right.
> - A pole near $z=1$ lifts the low frequencies: a **low-pass** filter. A pole near $z=-1$ lifts the high frequencies: a **high-pass** filter. A pole pair at an angle in between lifts a band around that angle: a **band-pass** filter (a resonator).
> - Why slow decay means a narrow peak: a pole close to the circle is very near to only a few points of the circle, so only a narrow band (small bandwidth (bant genişliği)) gets a large gain. The same pole gives $h[n]=a_1^n$ with $a_1$ near 1, which decays slowly.

> [!question]- Check (slide 14): Which points of the unit circle belong to 0 Hz, to $F_S/4$ and to $F_S/2$? Why is the frequency response periodic with period $2\pi$?
> $\hat\omega=2\pi F/F_S$. 0 Hz: $\hat\omega=0$, $z=e^{j0}=1$. $F_S/4$: $\hat\omega=\pi/2$, $z=j$. $F_S/2$: $\hat\omega=\pi$, $z=-1$.
> Adding $2\pi$ to the angle goes once around the circle and lands on the same point $z$. $H$ has the same value there, so the response repeats.

> [!question]- Check (slide 15): How is the curve on the right obtained from the surface on the left? Why does it have two peaks, and why is its value at frequency 0 equal to 2? (The poles are $0.5\pm0.5j$.)
> The right curve is the height of the surface $|H(z)|$ along the unit circle (the dark ring drawn on the surface), plotted against the angle $\hat\omega$ from $-\pi$ to $\pi$.
> The circle passes close to each of the two poles once, at the pole angles $\pm\pi/4\approx\pm0.79$. Near a pole the surface is high, so there are two peaks (height about 2.8). They sit at about $\pm0.7$, a little closer to 0 than the pole angles, because the skirts of the two poles overlap.
> At $\hat\omega=0$ the point is $z=1$. The distance from $z=1$ to each pole is $|1-0.5\mp0.5j|=\sqrt{0.5}\approx0.707$. So $|H|=1/(0.707\cdot0.707)=1/0.5=2$.

> [!question]- Check (slide 16): In $H(z)=\dfrac{b_0}{1-a_1z^{-1}}$, what does $b_0$ do and what does $a_1$ do? What kind of filter do you get for $a_1=-0.8$ instead of $a_1=0.8$?
> $b_0$ is the gain. It scales the whole response and does not change its shape.
> $a_1$ is the position of the pole, $z=a_1$. It sets how fast $h[n]=b_0a_1^n$ decays and how sharp the peak is.
> $a_1=0.8$: the pole is near $z=1$, the peak is at $\hat\omega=0$. A low-pass filter.
> $a_1=-0.8$ (with $b_0=1$): the pole is near $z=-1$, the peak is at $\hat\omega=\pi$: $|H|=1/(1-0.8)=5$ there and $1/(1+0.8)\approx0.56$ at $\hat\omega=0$. A high-pass filter. Its impulse response $(-0.8)^n$ alternates in sign.

> [!question]- Check (slide 17): The four panels show the same four filters, $a_1=0.8,\ 0.7,\ 0.6,\ 0.4$. In each of the first three panels, which curve or square belongs to $a_1=0.4$? Why are all peaks at frequency 0?
> Top left (impulse responses): $a_1=0.4$ is the lowest curve, the one that reaches zero first. $a_1=0.8$ is the highest curve with the slowest decay.
> Top right (frequency responses): $a_1=0.4$ is the flattest curve with the lowest peak, about $1/(1-0.4)\approx1.67$. $a_1=0.8$ has the tallest and narrowest peak, 5.
> Bottom left (poles): the four squares lie on the positive real axis. $a_1=0.4$ is the square nearest to the origin, $a_1=0.8$ the one nearest to the unit circle.
> All poles lie on the positive real axis, so their angle is 0. The nearest point of the unit circle is $z=1$, which is $\hat\omega=0$.

## 6. Conjugate pole pairs and zeros (slides 18-21)
![[cs568_lecture_05_l5_p018.jpg]]
![[cs568_lecture_05_l5_p019.jpg]]
![[cs568_lecture_05_l5_p020.jpg]]
![[cs568_lecture_05_l5_p021.jpg]]

**Notes.** A resonance has amplitude, frequency and bandwidth. The pole **radius** $r$ controls amplitude and bandwidth. The pole **angle** $\theta$ controls frequency. A pole on the real axis peaks at $\hat\omega=0$. For a resonance at another frequency, move the pole off the real axis and add its conjugate, so the coefficients stay real:

$$H(z)=\frac{1}{(1-re^{j\theta}z^{-1})(1-re^{-j\theta}z^{-1})}=\frac{1}{1-2r\cos(\theta)\,z^{-1}+r^2z^{-2}}$$

So $a_1=2r\cos\theta$, $a_2=-r^2$, and $y[n]=x[n]+2r\cos\theta\,y[n-1]-r^2y[n-2]$.

- Constant $\theta$: sharper as $r\to1$. For small $r$ the skirts of the two poles overlap and shift the peak.
- Constant $r$: the resonance moves away from $\hat\omega=0$ as $\theta$ grows.

**Zeros** do the opposite: they add anti-resonances (dips). A factor $(1-b_1z^{-1})$ puts a zero at $z=b_1$: $b_1=1$ kills $\hat\omega=0$, $b_1=-1$ kills $\hat\omega=\pm\pi$. Any transfer function can be built by placing poles and zeros, but poles that are close together interact.

> [!warning] Slide errors
> Slide 18 ends the denominator with $r^2z^{-1}$. It is $r^2z^{-2}$. The table on slide 19 has truncated values that do not fit the stated $r$ and $\theta$ (for $r=0.7$: $a_1=0.76$, $a_2=-0.49$, not $0.75$ and $-0.48$).

> [!info] The zero filter $1-az^{-1}$ and pre-emphasis (beyond the slides)
> - On slide 20, "a zero at the origin" means the origin of the **frequency axis** ($\hat\omega=0$, the point $z=1$), not the point $z=0$. The term $b_1=1$ is the numerator factor $1-z^{-1}$, that is $y[n]=x[n]-x[n-1]$. The term $b_1=-1$ is $1+z^{-1}$ with its zero at $z=-1$ ($\hat\omega=\pm\pi$).
> - A filter with only this numerator, $H(z)=1-az^{-1}$ with $a$ a little below 1, is the **pre-emphasis filter** used on speech: $y[n]=x[n]-a\,x[n-1]$. It is FIR, with $h=[1,\ -a]$.
> - Its zero at $z=a$ is close to $z=1$. So the gain is small at low frequencies and large at high frequencies. For $a=0.97$: $|H|=1-0.97=0.03$ at $\hat\omega=0$ (about $-30$ dB) and $1+0.97=1.97$ at $\hat\omega=\pi$ (about $+6$ dB). It is a high-pass filter.
> - Why on speech: the spectrum of voiced speech falls towards high frequencies. Pre-emphasis lifts the high part, so the higher formants are not hidden behind the strong low frequencies in the later analysis.

> [!question]- Check (slide 18): Why does a pole away from the real axis need a conjugate partner? Do the expansion for $r=0.9$, $\theta=\pi/3$ and write the difference equation.
> One complex pole alone gives a complex coefficient $a_1=re^{j\theta}$, and then a real input gives a complex output. With the conjugate pair the imaginary parts cancel and the coefficients are real.
> $(1-re^{j\theta}z^{-1})(1-re^{-j\theta}z^{-1})=1-r(e^{j\theta}+e^{-j\theta})z^{-1}+r^2z^{-2}=1-2r\cos\theta\,z^{-1}+r^2z^{-2}$.
> $a_1=2r\cos\theta=2\cdot0.9\cdot0.5=0.9$. $a_2=-r^2=-0.81$.
> $y[n]=x[n]+0.9\,y[n-1]-0.81\,y[n-2]$. The resonance is near $\hat\omega=\pi/3\approx1.05$ rad.

> [!question]- Check (slide 19): Which column of the slide changes $r$ and which changes $\theta$? How do you see this in the pole plots and in the spectra below them?
> Left column: $\theta=1.0$ is fixed, $r=0.9,\ 0.8,\ 0.7,\ 0.6$. The squares lie on one straight line through the origin (same angle, different distance). In the spectra all peaks stay at about $\pm1$, but the peak gets taller and narrower as $r$ grows: about 6 for $r=0.9$, below 2 for $r=0.6$.
> Right column: $r=0.8$ is fixed, $\theta=0.75,\ 1.0,\ 1.25,\ 1.5$. The squares lie on an arc at the same distance from the origin. In the spectra the peaks move outward, from about $\pm0.75$ to about $\pm1.5$.
> The pole plots show only the lower pole of each pair. The partner is the mirror image above the real axis.

> [!question]- Check (slide 20): Which plot has zero gain at frequency 0 and which at the ends $\pm\pi$? Give the numerator factor and the place of the zero in the $z$-plane for each. Why are the peaks taller in the right plot (about 11) than in the left plot (about 6)?
> Left plot ($b_1=1$): the curve touches 0 at frequency 0. Factor $1-z^{-1}$, zero at $z=1$.
> Right plot ($b_1=-1$): the curve goes to 0 at $\pm\pi$. Factor $1+z^{-1}$, zero at $z=-1$.
> The gain of a zero factor is the distance from the zero to the point on the circle. The resonance is at about $\hat\omega=1$. This point is close to the zero at $z=1$ (distance about 0.96) and far from the zero at $z=-1$ (distance about 1.76). So the right plot multiplies the peak by a larger number.

> [!question]- Check (slide 21): Find the mistake: "A filter built from two pole pairs with $r=0.8$ at $\theta=1.0$ and $\theta=1.3$ has two resonance peaks, exactly at 1.0 and 1.3 rad." What can you do to see two peaks?
> The poles are close together and far from the unit circle, so each one has a broad peak. The two broad peaks merge. This filter has only **one** peak, at about 1.1 rad, between the two pole angles.
> This is the warning of the slide: the final resonances cannot always be predicted from the single poles.
> Moving the poles closer to the circle makes each peak narrower. With $r=0.9$ the same angles give two separate peaks, at about 1.03 and 1.25 rad. They are still shifted a little from 1.0 and 1.3.

## 7. Example: synthesising the vowel [ih] (slides 22-24)
![[cs568_lecture_05_l5_p022.jpg]]
![[cs568_lecture_05_l5_p023.jpg]]
![[cs568_lecture_05_l5_p024.jpg]]

**Notes.** A formant of frequency $F$ and bandwidth $B$ (Hz) at sampling rate $F_S$ is a conjugate pole pair $re^{\pm j\theta}$ with

$$\theta=\frac{2\pi F}{F_S},\qquad r=e^{-\pi B/F_S}$$

For [ih]: $F_1,F_2,F_3=300, 2200, 3000$ Hz, all $B=250$ Hz, $F_S=16$ kHz.

| Formant | $\theta$ (rad) | $r$ | Upper pole | $a_1=2r\cos\theta$ | $a_2=-r^2$ |
|---|---|---|---|---|---|
| 300 Hz | 0.1178 | 0.9521 | $0.9455+0.1119j$ | 1.891 | $-0.9065$ |
| 2200 Hz | 0.8639 | 0.9521 | $0.6183+0.7240j$ | 1.237 | $-0.9065$ |
| 3000 Hz | 1.1781 | 0.9521 | $0.3644+0.8796j$ | 0.729 | $-0.9065$ |

One resonator per formant, $H_n(z)=\dfrac{1}{(1-p_nz^{-1})(1-p_n^{*}z^{-1})}$, cascaded: $H(z)=H_1(z)H_2(z)H_3(z)$. Exciting it with a pulse train gives a synthetic vowel (slide 24).

> [!warning] Slide error
> The pole column of the slide table reads $0.963+0.116j$, $0.619+0.719j$, $0.370+0.874j$. The first has radius 0.970, not 0.952. The values above are recomputed from the formulas.

> [!info] Where $r=e^{-\pi B/F_S}$ comes from, and the all-pole model (beyond the slides)
> - The impulse response of a two-pole resonator decays like $r^n$. A resonance (rezonans) of bandwidth $B$ Hz decays in continuous time like $e^{-\pi Bt}$. Sampling at $t=n/F_S$ gives $e^{-\pi Bn/F_S}=(e^{-\pi B/F_S})^n$. Comparing with $r^n$ gives $r=e^{-\pi B/F_S}$.
> - For a small bandwidth $r\approx1-\pi B/F_S$. So the bandwidth is proportional to the distance $1-r$ between the pole and the unit circle. A narrow formant is a pole very close to the circle.
> - The vowel filter $H(z)=H_1H_2H_3$ has only poles (its numerator is 1). This is an **all-pole** model of the vocal tract: three pole pairs, order 6. Linear prediction (Lecture 7) estimates exactly such a filter from real speech.
> - In the plot of slide 23 all three formants have the same bandwidth, but the peaks have different heights. Two reasons. First, the two poles of the $F_1$ pair are very close to each other, so both lift the response near 300 Hz. Alone, the three resonators have peaks of about 89, 14 and 12. Second, each peak is also multiplied by the gains of the other two resonators at that frequency. The $F_2$ and $F_3$ poles are close, so they lift each other (factor about 1.9). The $F_1$ resonator has gain about 1.5 at 2200 Hz but only about 0.86 at 3000 Hz. Together: about $89\cdot1.5\cdot0.86\approx115$, $14\cdot1.5\cdot1.9\approx41$ and $12\cdot1.9\cdot0.86\approx19$.

> [!question]- Check (slide 22): In the table all three formants have the same $r$ but different $\theta$. Why? What happens to $\theta$ and $r$ of the 2200 Hz formant if the sampling rate is doubled to $F_S=32$ kHz?
> $r=e^{-\pi B/F_S}$ depends only on the bandwidth, and all three use $B=250$ Hz. $\theta=2\pi F/F_S$ depends on the formant frequency, which differs.
> At 32 kHz: $\theta=2\pi\cdot2200/32000\approx0.432$ rad, half of $0.864$. The same frequency in Hz is now a smaller part of the circle.
> $r=e^{-\pi\cdot250/32000}=e^{-0.0245}\approx0.976$, closer to 1 than $0.952$. The filter does the same job in Hz, but its poles sit at a smaller angle and nearer to the unit circle.

> [!question]- Check (slide 23): How many poles does the pole plot show, and what is the order of $H(z)$? Which squares belong to $F_1$? Read the three peak positions off the left plot and say why the frequency axis ends near 8000 Hz.
> There are six squares: three conjugate pairs, one pair per formant. So $H(z)$ has order 6 (three second-order resonators multiplied).
> The $F_1$ pair is the one nearest to the positive real axis (smallest angle, near $z=1$). The pair with the largest angle belongs to $F_3$.
> The peaks are at about 300, 2200 and 3000 Hz, the three formants. Their heights are about 115, 40 and 20.
> The highest frequency of a signal sampled at $F_S=16$ kHz is $F_S/2=8000$ Hz. It is the point $z=-1$, half way around the circle.

> [!question]- Check (slide 24): The filter is excited with a pulse train. Which part of the system sets the pitch and which sets the vowel? What changes if the pulse period goes from 160 to 128 samples at $F_S=16$ kHz?
> The pulse train is the source. Its period sets the pitch: $F_0=F_S/\text{period}$. The filter $H(z)$ sets the formants, so it decides which vowel we hear.
> Period 160: $F_0=16000/160=100$ Hz. Period 128: $F_0=16000/128=125$ Hz. The pitch goes up.
> The output spectrum has lines at the multiples of $F_0$, and their heights follow $|H|$. With the new period the lines are further apart, but the envelope with its peaks at 300, 2200 and 3000 Hz stays the same. It is still [ih].

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| LTI | linear and time-invariant |
| FIR | $y[n]=\sum_{k=0}^{M}b_kx[n-k]$. Impulse response = the $b_k$ |
| IIR | adds $\sum_{l=1}^{N}a_ly[n-l]$. Impulse response never ends |
| Impulse response | $h[n]$ = output for $\delta[n]$ |
| Convolution sum | $y[n]=\sum_kx[k]h[n-k]$ |
| Transfer function | $H(z)=\dfrac{\sum_kb_kz^{-k}}{1-\sum_la_lz^{-l}}=\sum_kh[k]z^{-k}$ |
| Pole / zero | root of the denominator (peak) / of the numerator (dip) |
| Frequency response | $H(e^{j\hat\omega})$, $H$ on the unit circle |
| Stability | all poles inside the unit circle. First order: $\lvert a_1\rvert<1$ |
| Two-pole resonator | $1/(1-2r\cos\theta\,z^{-1}+r^2z^{-2})$ |
| Formant to pole | $\theta=2\pi F/F_S$, $r=e^{-\pi B/F_S}$ |

## Check yourself
> [!question]- 1. Linear? Time-invariant? (a) $y[n]=2x[n]+x[n-1]$, (b) $y[n]=x[n]^2$, (c) $y[n]=n\,x[n]$.
> (a) Both: an FIR filter. (b) Not linear (input 2 gives 4, not 2), but time-invariant. (c) Linear, not time-invariant ($\delta[n]$ gives 0, $\delta[n-1]$ gives 1 at $n=1$).

> [!question]- 2. Compute $h[0..3]$ for $y[n]=x[n]+0.5\,y[n-1]$. Is it stable?
> $1,\ 0.5,\ 0.25,\ 0.125$, so $h[n]=0.5^n$. $|0.5|<1$: stable.

> [!question]- 3. Convolve $x=[2,1]$ with $h=[1,\ 0.5,\ 0.25]$.
> $y=[2,\ 2,\ 1,\ 0.25]$. Check: sums $3\times1.75=5.25$.

> [!question]- 4. Give $H(z)$ for $y[n]=x[n]-x[n-2]+0.9\,y[n-1]$.
> $Y(1-0.9z^{-1})=X(1-z^{-2})$, so $H(z)=\dfrac{1-z^{-2}}{1-0.9z^{-1}}$.

> [!question]- 5. A resonator has $r=0.7$, $\theta=\pi/2$. Give $a_1$, $a_2$ and the difference equation.
> $a_1=2\cdot0.7\cdot\cos(\pi/2)=0$, $a_2=-0.49$. $y[n]=x[n]-0.49\,y[n-2]$. Poles at $\pm0.7j$.

> [!question]- 6. Find $\theta$ and $r$ for a formant at 1000 Hz with bandwidth 80 Hz at $F_S=8$ kHz.
> $\theta=2\pi\cdot1000/8000=\pi/4\approx0.785$ rad. $r=e^{-\pi\cdot80/8000}=e^{-0.0314}\approx0.969$.

## Links
- Course index: [[CS568 Home]]
- Previous: [[CS568 L04 - Signals and transforms]]. Next: [[CS568 L06 - Short-time Fourier analysis]]
- Used later: [[CS568 L07 - Linear prediction of speech]] (all-pole filter)
- Full step-by-step notes (PDF): [CS568_Lecture_05_Digital_Filters.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_05/CS568_Lecture_05_Digital_Filters.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_05/CS568_Lecture_05_Digital_Filters.pdf`
- Original slides (PDF): [l5.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_05/l5.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_05/l5.pdf`
- Quiz: [[CS568 L05 - Digital filters - quiz]]

Slides not embedded: 1 (outline).
