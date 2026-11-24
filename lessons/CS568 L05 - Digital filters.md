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

Slides not embedded: 1 (outline).
