---
course: CS 568 Speech Processing
lecture: 7
title: Linear prediction of speech
tags: [cs568, lecture]
slides: lectures/lecture_07/l7.pdf
full_notes: lecture_notes/lecture_07/CS568_Lecture_07_Linear_Prediction.pdf
---
# CS 568 · Lecture 7 · Linear prediction of speech

> [!summary] In short
> - LP model: $y[n]=e[n]+\sum_{k=1}^{p}a_k\,y[n-k]$. Each sample is predicted from the last $p$ samples. The miss $e[n]$ is the excitation.
> - $1/A_p(z)$ (synthesis filter) turns excitation into speech. $A_p(z)$ (inverse filter) turns speech back into the residual.
> - Minimising the squared error gives $p$ linear equations. Covariance method: no window, Cholesky, $O(p^3)$. Autocorrelation method: Toeplitz, Levinson-Durbin, $O(p^2)$, always stable.
> - The response of $G/A_p(z)$ is the spectral envelope: formants without pitch harmonics. Order $p\approx4+f_s/1000$.
> - The same filter can be stored as pole pairs, line spectral frequencies, reflection coefficients or log-area ratios.

## 1. Speech production and the two kinds of peaks (slides 2-3)
![[cs568_lecture_07_l7_p002.jpg]]
![[cs568_lecture_07_l7_p003.jpg]]

**Notes.** Speech is an excitation shaped by resonances. The excitation is glottal pulses (voiced, with fundamental frequency $F_0$), continuous air flow (unvoiced), or both. The resonances of the vocal, nasal and pharyngeal tracts are the **formants**.

In the spectrum of one frame there are two kinds of peaks: narrow peaks every $F_0$ (the harmonics) and wide peaks (the formants, the spectral envelope). Linear prediction models the wide peaks.

> [!example] Counting peaks
> $F_0=200$ Hz, $f_s=8$ kHz: harmonics at $200m\le4000$ Hz, so 20 narrow peaks. About one formant per kHz, so about 4 wide peaks.

> [!question]- Check (slide 2): A speaker says the vowel /a/ first with a low voice, then with a high voice. Then the speaker whispers it. Which part of the production model changes each time, and which part stays the same?
> Low to high voice: the excitation changes. The vocal folds open and close faster, so $F_0$ rises. The resonances (formants) stay, because the shape of the tract is still the shape of /a/.
> Whisper: the excitation changes again. There are no glottal pulses, only continuous air flow (unvoiced), so there is no $F_0$ at all. The formants still stay.
> So the excitation carries the pitch and the voicing, and the resonances carry the identity of the vowel.

> [!question]- Check (slide 3): On the slide the waveform under the spectrum repeats about every 8 ms. What spacing of the narrow peaks does this give? Which of the two curves in the spectrum is the one that linear prediction tries to produce?
> $F_0=1/T_0\approx1/0.008\ \text{s}=125$ Hz. The narrow peaks ($F_0,H_1,H_2,H_3,\dots$) are about 125 Hz apart.
> The spectrum has a jagged curve (the harmonics) and a smooth curve drawn through it. The smooth curve is the spectral envelope. Its four wide peaks are marked $F_1$ to $F_4$ (at about 0.6, 1.3, 2.5 and 3.8 kHz). Linear prediction produces this smooth curve, not the jagged one.

## 2. The LP model and the inverse filter (slides 4-7)
![[cs568_lecture_07_l7_p004.jpg]]
![[cs568_lecture_07_l7_p005.jpg]]
![[cs568_lecture_07_l7_p006.jpg]]
![[cs568_lecture_07_l7_p007.jpg]]

**Notes.** Source-filter model (Fant, 1960): glottis and vocal tract are uncoupled. Speech $y[n]$ is the output of an all-pole filter excited by $x[n]$:

$$Y(z)=X(z)\,\frac{1}{1-\sum_{k=1}^{p}a_kz^{-k}}=X(z)\,\frac{1}{A_p(z)}$$

The $a_k$ are the **LP coefficients**, $p$ is the **prediction order**. $1/A_p(z)$ is the **synthesis filter**, $A_p(z)$ the **inverse filter**. The excitation is a pulse train (period $T_0$, amplitude $\sigma$) or white Gaussian noise (variance $\sigma^2$).

In time:

$$y[n]=x[n]+\sum_{k=1}^{p}a_k\,y[n-k]$$

Each sample is a weighted sum of the $p$ previous samples plus an excitation term. That term is called the **error** or **residual** $e[n]$. Given the speech and the $a_k$, the residual is the output of the inverse filter:

$$e[n]=y[n]-\sum_{k=1}^{p}a_k\,y[n-k]$$

It should look like pulses for voiced speech and like white noise for unvoiced speech.

> [!example] Residual by hand
> $p=2$, $a_1=1$, $a_2=-0.5$, $y=(1,2,1)$: $e[n]=y[n]-y[n-1]+0.5\,y[n-2]$ gives $e=(1,\ 1,\ -0.5,\ 0,\ 0.5,\ 0)$.

> [!warning] Watch out
> Slide 7 writes "for a given speech signal $x[n]$". The speech is $y[n]$, $x[n]$ is the excitation. Sign convention here: $A_p(z)=1-\sum_ka_kz^{-k}$. Other books use a plus.

> [!info] Why only poles, and where this is weak (beyond the slides)
> A pole (kutup) of the filter gives a peak in the spectrum, a zero (sıfır) gives a dip. A tube that is closed at the glottis and open at the lips has only resonances, so for vowels a filter with only poles is a good model.
> The model is weak for two groups of sounds:
> - **Nasals** (/m/, /n/): the mouth is closed and the sound leaves through the nose. The closed mouth is a side branch that absorbs energy at some frequencies. This gives zeros (anti-resonances) in the spectrum.
> - **Fricatives** (/s/, /f/): the noise source is at a narrow place inside the tract, not at the glottis. The cavity behind the source also gives zeros.
>
> Poles are still used for these sounds, for two reasons. First, a zero can be approximated by several poles, so a slightly higher $p$ helps. Second, with only poles the equations for the $a_k$ are linear (section 3). With zeros in the model they are not linear. The ear is also more sensitive to spectral peaks than to dips.

> [!question]- Check (slide 4): In $Y(z)=X(z)\,\dfrac{1}{1-\sum_{k=1}^{p}a_kz^{-k}}$ name $Y(z)$, $X(z)$, $a_k$, $p$ and the fraction. Why is the filter called "all-pole"?
> $Y(z)$: z-transform of the speech signal (the output). $X(z)$: z-transform of the excitation (the input). $a_k$: the LP coefficients, they set the resonances. $p$: the prediction order, the number of coefficients and the number of poles. The fraction is the synthesis filter $1/A_p(z)$.
> The numerator is the constant 1, so the filter has no zeros away from $z=0$. All of its spectral shape comes from the $p$ roots of the denominator $A_p(z)$, which are the poles.

> [!question]- Check (slide 5): In the block diagram, say what each of the four controls $T_0$, V/UV, $\sigma$ and $\{a_i\}$ does. Which of them change when a speaker goes from the vowel /a/ to the vowel /i/ at the same pitch and loudness?
> $T_0$: the distance between the pulses of the upper source, so it sets the pitch ($F_0=1/T_0$). V/UV: the switch that selects the pulse train (voiced) or the noise source (unvoiced). $\sigma$: the gain of the amplifier, so it sets the loudness of the excitation $\tilde e(n)$. $\{a_i\}$: the coefficients of the filter $1/A_p(z)$, so they set the formants.
> From /a/ to /i/ only $\{a_i\}$ changes. Both are voiced, and pitch and loudness stay the same.

> [!question]- Check (slide 6): Name each symbol in $y[n]=x[n]+\sum_{k=1}^{p}a_k\,y[n-k]$. Then take $p=2$, $a_1=1.2$, $a_2=-0.5$, $y[n-1]=2$, $y[n-2]=1$ and the true sample $y[n]=2.1$. Find the predicted value and the error.
> $y[n]$: the present speech sample. $y[n-k]$: the sample $k$ steps in the past. $a_k$: the weight of that past sample. $p$: how many past samples are used. The sum is the prediction. $x[n]$: the part that the past cannot explain, the excitation, also called error or residual $e[n]$.
> Prediction: $1.2\cdot2+(-0.5)\cdot1=2.4-0.5=1.9$.
> Error: $e[n]=2.1-1.9=0.2$.

> [!question]- Check (slide 7): Find the mistake: "The inverse filter $A_p(z)$ is an all-pole filter, so it can be unstable. For a voiced frame its output looks like the speech waveform, only smaller."
> Two mistakes. $A_p(z)=1-\sum_ka_kz^{-k}$ is all-zero (FIR): the output uses only the present and $p$ past input samples, so it is always stable. The filter that can be unstable is the synthesis filter $1/A_p(z)$.
> The output is the residual, not a small copy of the speech. The inverse filter removes the resonances, so for a voiced frame what is left is close to a train of pulses, one per pitch period. For an unvoiced frame it is close to white noise. In both cases the envelope of the residual spectrum is about flat.

## 3. Finding the coefficients: covariance method (slides 8-11)
![[cs568_lecture_07_l7_p008.jpg]]
![[cs568_lecture_07_l7_p009.jpg]]
![[cs568_lecture_07_l7_p010.jpg]]
![[cs568_lecture_07_l7_p011.jpg]]

**Notes.** Choose the $a_k$ that minimise the energy of the residual over a frame of $N$ samples:

$$E=\sum_{n=0}^{N-1}e^2[n]=\sum_{n=0}^{N-1}\left(y[n]-\sum_{k=1}^{p}a_k\,y[n-k]\right)^{2}$$

Setting $\partial E/\partial a_j=0$ for $j=1,\dots,p$ gives the **normal equations**:

$$\sum_{n=0}^{N-1}y[n]\,y[n-j]=\sum_{k=1}^{p}a_k\sum_{n=0}^{N-1}y[n-k]\,y[n-j]$$

With $\phi(j,k)=\sum_{n=0}^{N-1}y[n-j]\,y[n-k]$ this is

$$\phi(j,0)=\sum_{k=1}^{p}\phi(j,k)\,a_k,\qquad j=1,\dots,p$$

or $\boldsymbol\Phi=\boldsymbol\Psi\,\mathbf a$ in matrix form. The matrix $\boldsymbol\Psi$ is symmetric, so the system is solved with the Cholesky decomposition in $O(p^3)$.

Properties: the error is computed over $0\le n\le N-1$ but uses samples $-p\le n\le N-1$ ($N+p$ samples). No window is needed. If the signal really is all-pole, the solution is exact. Stability of $1/A_p(z)$ is not guaranteed.

> [!warning] Slide errors
> - Slide 9 has a factor 2 on the right-hand side of the final equation. Both terms carry the 2, so it cancels.
> - Slide 10 says "$\Phi$ is symmetric". The symmetric object is the matrix $\Psi$. $\Phi$ is a vector.
> - Slide 11 writes the regions as $0\le n<N-1$ and $-p\le n<N-1$. The sum on slide 9 runs up to $n=N-1$, so the regions are $0\le n\le N-1$ and $-p\le n\le N-1$ (the same as $0\le n<N$, which is what slide 12 writes).

> [!question]- Check (slide 8): The slide minimises the energy $e^2(n)$ of the residual. Why the square, and not the sum of $e[n]$ itself? And why is a small residual the right goal?
> Without the square, positive and negative errors cancel, so a bad predictor could give a sum near zero. The square counts every error as positive. It also makes $E$ a quadratic function of the $a_k$, so the derivative is linear in the $a_k$ and the minimum comes from a set of linear equations.
> A small residual means that the past samples explain as much of $y[n]$ as possible. Then the filter holds the predictable part (the resonances) and only the unpredictable part (the excitation) is left in $e[n]$.

> [!question]- Check (slide 9): The middle line of the slide can be read as $\sum_{n=0}^{N-1}e[n]\,y[n-j]=0$ for $j=1,\dots,p$. Where does the factor $y[n-j]$ come from, and what does the equation say in words?
> Chain rule: $\partial e^2[n]/\partial a_j=2\,e[n]\,\partial e[n]/\partial a_j$, and $e[n]=y[n]-\sum_ka_ky[n-k]$ gives $\partial e[n]/\partial a_j=-y[n-j]$. So $\partial E/\partial a_j=-2\sum_ne[n]\,y[n-j]$.
> In words: at the optimum the residual is uncorrelated with each of the $p$ past samples that the predictor uses. If some correlation were left, that past sample could still explain a part of the error, and a different $a_j$ would make $E$ smaller.

> [!question]- Check (slide 10): For $p=10$, how many unknowns and how many equations does $\Phi=\Psi a$ have? Write $\phi(1,1)$ and $\phi(2,2)$ as sums over the samples. Are they equal?
> 10 unknowns $a_1,\dots,a_{10}$ and 10 equations ($j=1,\dots,10$). $\Psi$ is $10\times10$.
> $\phi(1,1)=\sum_{n=0}^{N-1}y^2[n-1]=y^2[-1]+\dots+y^2[N-2]$. $\phi(2,2)=\sum_{n=0}^{N-1}y^2[n-2]=y^2[-2]+\dots+y^2[N-3]$.
> They are not equal in general: they differ by $y^2[N-2]-y^2[-2]$. So the diagonal of $\Psi$ is not constant. $\Psi$ is symmetric ($\phi(j,k)=\phi(k,j)$) but not Toeplitz, and that is why the fast Levinson-Durbin recursion cannot be used here.

> [!question]- Check (slide 11): A frame comes exactly from an order-2 all-pole filter, and the excitation is zero inside the frame. Which value of $E$ does the covariance method reach with $p=2$? Why does it need no window, and what is the price of the method?
> $E=0$. Inside the frame every sample is exactly $a_1y[n-1]+a_2y[n-2]$, and the method uses the true samples $y[-2]$, $y[-1]$ before the frame. So the true coefficients give zero error at every $n$, and the minimum finds them. This is the "exact solution" of the slide.
> No window is needed because the method never assumes that the signal is zero outside the frame. It takes the $p$ real samples before the frame.
> Price: the matrix is only symmetric, so the solver is Cholesky with $O(p^3)$, and the poles of $1/A_p(z)$ may fall outside the unit circle. Stability is not guaranteed.

## 4. Autocorrelation method and Levinson-Durbin (slides 12-13)
![[cs568_lecture_07_l7_p012.jpg]]
![[cs568_lecture_07_l7_p013.jpg]]

**Notes.** Window the frame (for example Hann), so every value outside $0\le n<N$ is zero. Then the sums can run over all $n$ and $\phi(j,k)$ depends only on $j-k$:

$$\phi(j,k)=\sum_{n=0}^{N-1-(j-k)}y[n]\,y[n+j-k]=R(j-k),\qquad R(n)=\sum_{m}y[m]\,y[m+n]$$

The normal equations become the **Yule-Walker equations**:

$$R(j)=\sum_{k=1}^{p}R(j-k)\,a_k\qquad\Longleftrightarrow\qquad
\begin{bmatrix}R(1)\\R(2)\\\vdots\\R(p)\end{bmatrix}=
\begin{bmatrix}R(0)&R(1)&\cdots&R(p-1)\\R(1)&R(0)&\cdots&R(p-2)\\\vdots&\vdots&\ddots&\vdots\\R(p-1)&R(p-2)&\cdots&R(0)\end{bmatrix}
\begin{bmatrix}a_1\\a_2\\\vdots\\a_p\end{bmatrix}$$

The matrix is **Toeplitz** (symmetric, equal elements on each diagonal). The **Levinson-Durbin recursion** solves it in $O(p^2)$. Start with $E^{(0)}=R(0)$. For $i=1,\dots,p$:

$$k_i=\frac{R(i)-\sum_{j=1}^{i-1}a_j^{(i-1)}R(i-j)}{E^{(i-1)}},\qquad a_i^{(i)}=k_i,\qquad a_j^{(i)}=a_j^{(i-1)}-k_i\,a_{i-j}^{(i-1)},\qquad E^{(i)}=(1-k_i^2)\,E^{(i-1)}$$

By-products: the **reflection coefficients** $k_i$ and the error energy of every order. For the autocorrelation method $|k_i|<1$ always, so the synthesis filter is stable.

> [!example] Levinson-Durbin, $p=2$
> $R(0)=1$, $R(1)=0.8$, $R(2)=0.5$. Order 1: $k_1=0.8$, $E^{(1)}=0.36$. Order 2: $k_2=(0.5-0.64)/0.36=-7/18\approx-0.389$, $a_2=-0.389$, $a_1=0.8-(-0.389)(0.8)=1.111$, $E^{(2)}=11/36\approx0.306$.

| | Covariance | Autocorrelation |
|---|---|---|
| Window | none | yes |
| Matrix | symmetric $\phi(j,k)$ | symmetric Toeplitz $R(j-k)$ |
| Solver | Cholesky, $O(p^3)$ | Levinson-Durbin, $O(p^2)$ |
| Exact for an all-pole signal | yes | no, biased by the window |
| Stable $1/A_p(z)$ | not guaranteed | guaranteed |

> [!warning] Slide error
> Slide 12 writes $R(n)=\sum_my[m]\,y[n-m]$. That is the convolution of $y$ with itself. The autocorrelation shifts without flipping: $\sum_my[m]\,y[m+n]$. Also, the final $a_j$ are not the $k_j$ in general (above $a_1=1.111$, $k_1=0.8$).

> [!info] What the Levinson-Durbin recursion tells you (beyond the slides)
> Slide 13 only names the recursion. Three things are worth knowing about it.
> - **It grows the order.** Step $i$ builds the best predictor of order $i$ from the best predictor of order $i-1$. So one run gives the predictors of all orders $1,\dots,p$.
> - **$k_i$ is a partial correlation (PARCOR).** The numerator of $k_i$ is the part of $R(i)$ that the order $i-1$ predictor does not explain. Dividing by $E^{(i-1)}$ normalises it. So $k_i$ measures how much new information the sample $y[n-i]$ brings. With the sign convention of this note $k_i=a_i^{(i)}$, the last coefficient of the order-$i$ predictor. These $k_i$ are the reflection coefficients (yansıma katsayıları) of section 7.
> - **The error can only fall.** $E^{(i)}=(1-k_i^2)E^{(i-1)}$ and $0\le1-k_i^2\le1$, so $E^{(0)}\ge E^{(1)}\ge\dots\ge E^{(p)}\ge0$. A higher order never gives a larger error. If $k_i\approx0$, order $i$ brings almost nothing.
>
> Why $\lvert k_i\rvert<1$: $R(n)$ comes from a real windowed signal, so every $E^{(i)}$ is a sum of squares and is positive. Then $1-k_i^2>0$. And $\lvert k_i\rvert<1$ for all $i$ is exactly the condition for all poles of $1/A_p(z)$ to lie inside the unit circle. In the covariance method there is no such chain, so there is no guarantee.
> The same $k_i$ are the multipliers of the lattice form of the filter, which is the signal-flow picture of a tube made of $p$ sections.

> [!question]- Check (slide 12): Why must the frame be windowed before $R(n)$ is computed? The slide says the error is non-zero up to $n=N-1+p$. Why is there an error after the end of the window, where the signal is zero?
> The sums run over all $n$ from $-\infty$ to $\infty$, but only $N$ samples are available. The window sets everything outside $0\le n<N$ to zero, so the infinite sum becomes a finite one. A tapered window (Hann) also makes the two edges smooth.
> After the window $y[n]=0$, but the prediction $\sum_ka_ky[n-k]$ still uses the last non-zero samples for $p$ more steps. So $e[n]=0-\text{prediction}\ne0$ for $n=N,\dots,N-1+p$. In the same way, at the start the first samples are predicted from zeros. These edge errors are part of $E$. They are the reason why the method is not exact even for a true all-pole signal.

> [!question]- Check (slide 13, matrix): Compare the matrix on this slide with $\Psi$ of the covariance method. What is the same and what is new? How many different numbers must be computed to fill each matrix for $p=10$?
> Same: both are $p\times p$ and symmetric.
> New: the element depends only on the distance $j-k$, so every diagonal holds one value ($R(0)$ on the main diagonal, $R(1)$ next to it, and so on). This is the Toeplitz structure. It comes from the window: with zeros outside the frame, shifting both factors does not change the sum.
> Numbers: the Toeplitz matrix needs only $R(0),\dots,R(9)$, 10 values (plus $R(10)$ for the left side). The covariance matrix needs every $\phi(j,k)$ with $j\le k$: $10\cdot11/2=55$ values (plus 10 for the left side).

> [!question]- Check (slide 13, Levinson-Durbin): The windowed frame is $y=(1,2,1)$. Compute $R(0)$, $R(1)$, $R(2)$. Solve the $p=2$ system of the slide directly. Then run Levinson-Durbin and compare.
> $R(0)=1+4+1=6$. $R(1)=1\cdot2+2\cdot1=4$. $R(2)=1\cdot1=1$.
> System: $4=6a_1+4a_2$ and $1=4a_1+6a_2$. Multiply the first by 3 and the second by 2 and subtract: $12-2=(18-8)a_1$, so $a_1=1$. Then $4a_2=4-6$, so $a_2=-0.5$.
> Levinson-Durbin: $E^{(0)}=6$. Order 1: $k_1=4/6=2/3$, $E^{(1)}=(1-4/9)\cdot6=10/3$. Order 2: $k_2=\dfrac{R(2)-a_1^{(1)}R(1)}{E^{(1)}}=\dfrac{1-8/3}{10/3}=-0.5$, $a_2=-0.5$, $a_1=\tfrac23-(-0.5)\cdot\tfrac23=1$, $E^{(2)}=(1-0.25)\cdot\tfrac{10}{3}=2.5$.
> Same $a_1=1$, $a_2=-0.5$. Extra results: $\lvert k_1\rvert,\lvert k_2\rvert<1$ (stable), and the error energy falls $6\to3.33\to2.5$. Check with the formula $E^{(p)}=R(0)-\sum_ka_kR(k)$ of section 5: $6-1\cdot4-(-0.5)\cdot1=6-4+0.5=2.5$.

## 5. Spectral envelope and model order (slides 14-18)
![[cs568_lecture_07_l7_p014.jpg]]
![[cs568_lecture_07_l7_p015.jpg]]
![[cs568_lecture_07_l7_p016.jpg]]
![[cs568_lecture_07_l7_p017.jpg]]
![[cs568_lecture_07_l7_p018.jpg]]

**Notes.** Evaluate the filter on the unit circle at angles $2\pi f/f_s$, with a gain $G$:

$$\left|H\!\left(e^{j2\pi f/f_s}\right)\right|^2=\left|\frac{G}{1-\sum_{k=1}^{p}a_k\,e^{-j2\pi kf/f_s}}\right|^2,\qquad G^2=E^{(p)}=R(0)-\sum_{k=1}^{p}a_kR(k)$$

The all-pole filter models the vocal-tract resonances. The glottal excitation stays in the residual. So this response is smooth and free of pitch harmonics: the **spectral envelope**.

How many coefficients? Slide 16: at $p=12$ the envelope follows the broad peaks. At $p=40$ it also follows the harmonics. The error curve flattens above about $p=12$. Counting poles: about one resonance per kHz, one complex pole pair each, plus 1 to 2 pole pairs for the glottal spectrum and lip radiation (slide 17). Rule of thumb, $f_s$ in Hz:

$$p=4+\frac{f_s}{1000}$$

That is 10 to 12 coefficients at $f_s=8$ kHz.

> [!warning] Slide error
> Slide 15 says "the broad spectral peaks (i.e. the harmonics)". The broad peaks are the formants.
>
> Slide 16, plot (a): the axis label says "frequency in kHz", but the ticks 0 to 4000 are in Hz.

> [!info] Why the envelope fits the peaks better than the valleys (beyond the slides)
> By Parseval's theorem the error energy can be written in the frequency domain. With $Y$ the spectrum of the frame and $H=G/A_p$:
> $$E=\frac{1}{2\pi}\int_{-\pi}^{\pi}\lvert Y(e^{j\omega})\rvert^2\,\lvert A_p(e^{j\omega})\rvert^2\,d\omega=\frac{G^2}{2\pi}\int_{-\pi}^{\pi}\frac{\lvert Y(e^{j\omega})\rvert^2}{\lvert H(e^{j\omega})\rvert^2}\,d\omega$$
> So LP minimises the average **ratio** of the signal spectrum to the model spectrum. Where the signal is above the model the ratio is larger than 1 and costs a lot. Where the signal is below the model the ratio is between 0 and 1 and costs little. The result: the envelope is much closer to the tops of the harmonic peaks than to the valleys between them, and it lies above the valleys and above deep dips. This is also why zeros (nasals) are modelled badly.
>
> The order $p$ decides how much detail the envelope has:
> - $p$ too low: too few pole pairs. Two close formants merge into one wide bump, or a formant is lost.
> - $p$ too high: there are enough pole pairs to put one on each harmonic. The "envelope" then follows the pitch harmonics, so it changes with $F_0$ and no longer describes only the vocal tract. This starts earlier for high-pitched voices, because their harmonics are far apart.

> [!question]- Check (slide 14): In $\lvert H\rvert^2=\left\lvert G/(1-\sum_ka_ke^{-j2\pi kf/f_s})\right\rvert^2$, what do $G$, the denominator and the substitution $z=e^{j2\pi f/f_s}$ each do? What changes in the curve if only $G$ is doubled? Why does the curve show no pitch harmonics?
> $G$: the gain. It moves the whole curve up or down so that the model has the same energy as the frame ($G^2=E^{(p)}$). The denominator is $A_p$ on the unit circle: where it is small, the curve has a peak (a formant). The substitution evaluates the transfer function on the unit circle, where the angle $2\pi f/f_s$ stands for the frequency $f$.
> $G$ doubled: $\lvert H\rvert^2$ becomes 4 times larger, a shift of about $+6$ dB at every frequency. The shape and the formant positions stay.
> No harmonics: the periodicity of the excitation is not in the $a_k$. It stays in the residual $e[n]$. With only $p$ poles the filter can make at most $p/2$ peaks. For the usual $p$ of 10 to 12 this is far fewer than the number of harmonics.

> [!question]- Check (slides 15-16): In plot (a), describe how the $p=12$ curve and the $p=40$ curve differ. Use plot (b) to say what is gained by going from 12 to 40. Then give the rule-of-thumb order for $f_s=16$ kHz and for $f_s=22.05$ kHz.
> $p=12$ (thick line): a smooth curve with a few wide peaks, the strongest at about 750 Hz and another at about 3.1 kHz. It does not follow the single harmonics: the harmonic peaks of the STFT stick out above it, and the valleys lie much further below it. $p=40$ (thin line): it goes up and down with every harmonic peak of the STFT, so it shows the pitch structure, not only the envelope.
> Plot (b): the normalised error falls fast at low orders (from about 0.095 at $p=1$ to about 0.035 at $p=12$). From 12 to 40 it only falls to about 0.015. So the extra 28 coefficients buy little, and what they model is the harmonics.
> Rule $p=4+f_s/1000$: at 16 kHz $p=4+16=20$. At 22.05 kHz $p=4+22.05=26.05$, so about 26.

> [!question]- Check (slide 17): The figure shows three spectra that are combined into the output spectrum. Name them and say how each one changes with frequency. Which of them does each part of the rule $p=4+f_s/1000$ count? Where does the left "periodic" source spectrum end up in the LP model?
> Glottal source spectrum: falls with frequency (low-pass). It is a set of lines when periodic and a smooth curve when continuous. Vocal tract gain: the curve with peaks, the formants. Radiation impedance at the lips: rises with frequency (high-pass).
> The term $f_s/1000$ counts the formants: about one per kHz in the band $0$ to $f_s/2$, so $f_s/2000$ formants, each with one pole pair (2 poles). The term 4 counts the poles for the overall slope from the glottal spectrum and the radiation: the slide says 1 to 2 pole pairs, and the rule takes 2 pairs (4 poles).
> The line structure (the harmonics) is not in the filter. It stays in the residual as a train of pulses. Only the smooth shape of the three curves goes into $G/A_p(z)$.

> [!question]- Check (slide 18): The demo ex7p1 first does inverse filtering and then synthesises speech "with simple excitation models". For one voiced frame, which signals or numbers go into each of the two steps? Why does the synthetic frame not equal the original?
> Inverse filtering: input the speech frame $y[n]$ and the $a_k$, output the residual $e[n]=y[n]-\sum_ka_ky[n-k]$.
> Synthesis: input the $a_k$, the gain, the decision voiced, and the pitch period $T_0$. A pulse train with period $T_0$ goes through $1/A_p(z)$.
> If the true residual were sent through $1/A_p(z)$, the output would be exactly $y[n]$. The simple model replaces the residual by ideal pulses (or white noise). The details of the real residual are lost, so the result has the same envelope and pitch but is not the same waveform and sounds more artificial.

## 6. Alternative representations: root pairs and LSF (slides 19-22)
![[cs568_lecture_07_l7_p019.jpg]]
![[cs568_lecture_07_l7_p020.jpg]]
![[cs568_lecture_07_l7_p021.jpg]]
![[cs568_lecture_07_l7_p022.jpg]]

**Notes.** The $a_k$ are hard to interpret and sensitive to numerical precision. Equivalent forms are used instead.

**Root pairs.** Factor $A_p(z)$. Each complex pair of roots (poles of the LP filter) is one resonance. For a pole $z_i=\rho_ie^{j\theta_i}$:

$$F_i=\frac{\theta_i}{2\pi}\,f_s,\qquad B_i\approx-\frac{f_s}{\pi}\ln\rho_i$$

Slide 21: in a 12th-order model, 4 pairs are close to the unit circle (formants) and 2 pairs lie well inside (overall spectral shaping from glottis and radiation).

**Line spectral frequencies (LSF).** Build the line spectrum pair polynomials

$$P(z)=A(z)+z^{-(p+1)}A(z^{-1}),\qquad Q(z)=A(z)-z^{-(p+1)}A(z^{-1})$$

All roots of $P$ and $Q$ lie on the unit circle. Their angles are the LSFs. Two close LSFs indicate a strong resonance. LSFs are stable and not very sensitive to quantisation, so they are widely used to quantise LP filters.

> [!example] Poles of the example filter
> $a_1=10/9$, $a_2=-7/18$, $f_s=8$ kHz: poles $0.556\pm0.283j$, $\rho\approx0.624$, $\theta\approx0.4715$ rad, so $F\approx600$ Hz and $B\approx1203$ Hz. A broad bump, not a sharp formant.

> [!warning] Slide error
> Slide 22 says $P$ and $Q$ add up to $A_p(z)$. The sum is $2A_p(z)$, so $A_p(z)=\tfrac12[P(z)+Q(z)]$.

> [!question]- Check (slide 19): The slide gives two reasons why the $a_k$ themselves are not a good final representation. Explain each reason with one sentence. Are the four alternatives different filters?
> Hard to interpret: one $a_k$ does not belong to one formant. Every coefficient changes the whole spectrum, so the numbers say nothing directly about frequencies or bandwidths.
> Sensitive to numerical precision: a small rounding error in one $a_k$ can move the poles a lot, even outside the unit circle, and then the synthesis filter is unstable.
> No. Root pairs, LSFs, reflection coefficients and log-area ratios all describe the same filter $1/A_p(z)$. Each can be converted to the $a_k$ and back. They differ in how easy they are to read, to quantise and to interpolate.

> [!question]- Check (slides 20-21, z-plane): In the left plot, how many red crosses are there, and why does the number have to be even? One cross is near $(-0.62,\,0.73)$ and another near $(-0.22,\,0.5)$. Which of the two is a formant? The figure gives no sampling rate: assume $f_s=8$ kHz and estimate $F$ and $B$ for both.
> 12 crosses, the order of the model. $A_p(z)$ has real coefficients, so complex roots come in conjugate pairs: 6 above the real axis and their mirror images below.
> Cross 1 (with the assumed $f_s=8$ kHz): $\rho=\sqrt{0.62^2+0.73^2}\approx0.96$, angle $\approx130^\circ$. $F\approx\frac{130}{360}\cdot8000\approx2900$ Hz, $B\approx-\frac{8000}{\pi}\ln0.96\approx110$ Hz. Close to the circle, narrow: a formant.
> Cross 2: $\rho\approx0.55$, angle $\approx114^\circ$. $F\approx2500$ Hz, $B\approx-\frac{8000}{\pi}\ln0.55\approx1500$ Hz. Far inside the circle, very wide: no visible peak, it only shapes the overall slope.

> [!question]- Check (slides 20-21, LSP plot): In the right plot, vertical lines (solid and dashed) are drawn over an LPC spectrum. What are these lines? Where do a solid and a dashed line stand close together, and where far apart? What does this tell you?
> The lines are the line spectral frequencies: the angles of the roots of $P(z)$ and $Q(z)$, one kind solid and the other dashed. Along the frequency axis solid and dashed lines alternate.
> Close pairs stand at the three sharp peaks of the spectrum, at about 550, 1750 and 2350 Hz. Around the flat region near 1 kHz and in the falling part above about 2.8 kHz the lines are far apart.
> So the distance between neighbouring LSFs shows the resonances: two close LSFs mean a pole of $A_p(z)$ near the unit circle, a strong and narrow formant. Wide spacing means no sharp resonance there.

> [!question]- Check (slide 22): Take $A(z)=1-z^{-1}+0.5z^{-2}$ ($p=2$). Compute $P(z)$ and $Q(z)$ and check their sum. Why are LSFs preferred to pole positions for quantisation?
> $z^{-(p+1)}A(z^{-1})=z^{-3}(1-z+0.5z^{2})=0.5z^{-1}-z^{-2}+z^{-3}$. This is $A$ with the coefficient order reversed and delayed by one sample.
> $P(z)=1-0.5z^{-1}-0.5z^{-2}+z^{-3}$. $Q(z)=1-1.5z^{-1}+1.5z^{-2}-z^{-3}$.
> $P+Q=2-2z^{-1}+z^{-2}=2A(z)$, so $A=\tfrac12(P+Q)$ (see the warning above).
> Roots (beyond the slides): $P=(1+z^{-1})(1-1.5z^{-1}+z^{-2})$ has roots at $z=-1$ and at the angles $\pm41.4^\circ$. $Q=(1-z^{-1})(1-0.5z^{-1}+z^{-2})$ has roots at $z=+1$ and at $\pm75.5^\circ$. All are on the unit circle, and on the upper half they alternate: $0^\circ$ ($Q$), $41.4^\circ$ ($P$), $75.5^\circ$ ($Q$), $180^\circ$ ($P$). $Q$ always has the root $z=+1$, and $P$ has the root $z=-1$ when $p$ is even (on slide 21: the mark at $+1$ and the mark at $-1$). The LSFs are the other $p$ angles, here $41.4^\circ$ and $75.5^\circ$, on both sides of the pole angle $45^\circ$.
> $P$ is symmetric and $Q$ is antisymmetric. When $1/A(z)$ is stable (here the poles are $0.5\pm0.5j$, radius $0.71$), all their roots lie on the unit circle. Each root is described by one number, its angle. These angles are ordered along the frequency axis, and as long as the quantised values keep this order and alternate between $P$ and $Q$, the filter stays stable. A pole needs two numbers (radius and angle), and a small error in the radius of a pole near the circle can make the filter unstable.

## 7. Reflection coefficients and log-area ratios (slides 23-24)
![[cs568_lecture_07_l7_p023.jpg]]
![[cs568_lecture_07_l7_p024.jpg]]

**Notes.** **Reflection coefficients** give the fraction of energy reflected at each section of a non-uniform tube model of the vocal tract ([[CS568 L02 - Speech production and perception]]). They come for free from Levinson-Durbin, are robust to quantisation, and can be interpolated. From the $a_k$, by backward recursion: start with $a_i^{(p)}=a_i$, then for $i=p,\dots,1$

$$r_i=a_i^{(i)},\qquad a_j^{(i-1)}=\frac{a_j^{(i)}+a_i^{(i)}\,a_{i-j}^{(i)}}{1-r_i^2},\quad 1\le j<i$$

The synthesis filter is stable if and only if $|r_i|<1$ for every $i$.

**Log-area ratios (LAR)** are the natural log of the ratio of the areas of adjacent tube sections:

$$g_k=\ln\!\left(\frac{1-r_k}{1+r_k}\right)$$

Only the ratios of areas can be found, not the absolute areas.

> [!warning] Watch out
> Slide 24 writes the left-hand side as $A_k$ but calls it $g_k$ in the text. Use $g_k$, to avoid a clash with $A_p(z)$.

> [!question]- Check (slide 23): A predictor has $a_1=1$, $a_2=-0.5$ ($p=2$). Use the backward recursion of the slide to find $r_2$ and $r_1$. Is the synthesis filter stable? Why are reflection coefficients easier to check for stability than the $a_k$?
> Start: $a_1^{(2)}=1$, $a_2^{(2)}=-0.5$.
> $i=2$: $r_2=a_2^{(2)}=-0.5$. Step down: $a_1^{(1)}=\dfrac{a_1^{(2)}+a_2^{(2)}a_1^{(2)}}{1-r_2^2}=\dfrac{1-0.5}{0.75}=\dfrac23$.
> $i=1$: $r_1=a_1^{(1)}=2/3\approx0.667$.
> Both $\lvert r_i\rvert<1$, so the filter is stable. These are the same values as $k_1$, $k_2$ of the Levinson-Durbin check in section 4: the backward recursion undoes the forward one.
> With the $a_k$, stability needs the roots of a polynomial of degree $p$. With the $r_i$ it is one test per coefficient: $\lvert r_i\rvert<1$. A quantiser can keep each $r_i$ inside $(-1,1)$, so the decoded filter is always stable.

> [!question]- Check (slide 24): Compute the log-area ratio for $r=0.5$ and for $r=-0.5$. Then compare the change of the LAR when $r$ goes from $0$ to $0.09$ with the change when $r$ goes from $0.90$ to $0.99$. What is this good for?
> $r=0.5$: $g=\ln\frac{0.5}{1.5}=\ln\frac13\approx-1.10$. $r=-0.5$: $g=\ln\frac{1.5}{0.5}=\ln3\approx+1.10$. The sign of $r$ only flips the sign of $g$: the area ratio is inverted.
> $r:0\to0.09$: $g$ goes from $0$ to $\ln\frac{0.91}{1.09}\approx-0.18$, a change of 0.18.
> $r:0.90\to0.99$: $g$ goes from $\ln\frac{0.10}{1.90}\approx-2.94$ to $\ln\frac{0.01}{1.99}\approx-5.29$, a change of 2.35.
> The same step in $r$ is stretched about 13 times more near $\lvert r\rvert=1$. This is useful because the spectrum is very sensitive to $r$ there (beyond the slides). A uniform quantiser on the LAR gives fine steps where they are needed. Also every real $g$ maps back to $\lvert r\rvert<1$, so the filter stays stable.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| LP model | $y[n]=e[n]+\sum_{k=1}^{p}a_ky[n-k]$ |
| Inverse filter | $A_p(z)=1-\sum_ka_kz^{-k}$. Output for speech input: the residual $e[n]$ |
| Synthesis filter | $1/A_p(z)$, all-pole |
| Covariance method | $\phi(j,0)=\sum_k\phi(j,k)a_k$, Cholesky $O(p^3)$ |
| Autocorrelation method | $R(j)=\sum_kR(j-k)a_k$ (Yule-Walker, Toeplitz), stable |
| Levinson-Durbin | $k_i$, $a_j^{(i)}=a_j^{(i-1)}-k_ia_{i-j}^{(i-1)}$, $E^{(i)}=(1-k_i^2)E^{(i-1)}$, $O(p^2)$ |
| Spectral envelope | $\lvert H\rvert^2=G^2/\lvert A_p(e^{j2\pi f/f_s})\rvert^2$, $G^2=R(0)-\sum_ka_kR(k)$ |
| Order | $p=4+f_s/1000$ |
| Pole to formant | $F=\theta f_s/(2\pi)$, $B\approx-(f_s/\pi)\ln\rho$ |
| LSF | angles of the roots of $P$, $Q$ on the unit circle |
| Reflection coefficient | $r_i=a_i^{(i)}$. Stable iff all $\lvert r_i\rvert<1$ |
| LAR | $g_k=\ln\frac{1-r_k}{1+r_k}$ |

## Check yourself
> [!question]- 1. For $p=1$, $R(0)=2$ and $R(1)=1.5$. Find $a_1$ and $E^{(1)}$.
> $a_1=k_1=1.5/2=0.75$. $E^{(1)}=(1-0.5625)\cdot2=0.875$.

> [!question]- 2. A covariance analysis with $p=10$ computes the error over $0\le n\le239$. Which samples of $y$ are used?
> $y[-10]$ to $y[239]$: $N+p=250$ samples.

> [!question]- 3. Which LP order does the rule of thumb give at $f_s=10$ kHz?
> $p=4+10=14$: about 5 formants in 0 to 5 kHz (10 poles) plus 4 poles for the spectral tilt.

> [!question]- 4. At $f_s=8$ kHz a pole pair has $\rho=0.9$, $\theta=\pi/4$. Find $F$ and $B$.
> $F=\frac{\pi/4}{2\pi}\cdot8000=1000$ Hz. $B\approx-\frac{8000}{\pi}\ln0.9=2546.5\times0.1054\approx268$ Hz.

> [!question]- 5. A coder transmits $r_1=0.95$, $r_2=1.02$. Is the synthesis filter stable?
> No: $|r_2|>1$. It cannot come from a correct autocorrelation analysis, because $E^{(2)}=(1-r_2^2)E^{(1)}$ would be negative.

> [!question]- 6. Compute the LAR for $r=0$. What does it mean?
> $g=\ln1=0$: the two adjacent areas are equal, nothing is reflected.

## Links
- Course index: [[CS568 Home]]
- Previous: [[CS568 L06 - Short-time Fourier analysis]]
- Background: [[CS568 L05 - Digital filters]] (poles, all-pole filters), [[CS568 L02 - Speech production and perception]] (tube model, reflection coefficients)
- Full step-by-step notes (PDF): [CS568_Lecture_07_Linear_Prediction.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_07/CS568_Lecture_07_Linear_Prediction.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_07/CS568_Lecture_07_Linear_Prediction.pdf`
- Original slides (PDF): [l7.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_07/l7.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_07/l7.pdf`

Slides not embedded: 1 (outline).
