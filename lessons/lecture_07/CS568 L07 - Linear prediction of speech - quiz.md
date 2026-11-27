---
course: CS 568 Speech Processing
lesson: CS568 L07 - Linear prediction of speech
type: quiz
questions: 23
---

# CS568 L07 - Linear prediction of speech - quiz

Lesson note: [[CS568 L07 - Linear prediction of speech]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Speech production and the two kinds of peaks (slides 2-3)

### Q1 (slide 2)

In the source-filter view, speech is an excitation (glottal pulses or air flow) shaped by the resonances of the tract (the formants). A speaker says the vowel /a/ first with a low voice, then with a high voice. Then the speaker whispers the same /a/. Which statement is correct?

- A) Low to high: only the excitation changes, $F_0$ rises. Whisper: the excitation becomes noise with no $F_0$, the formants stay.
- B) Low to high: the formants rise together with $F_0$. Whisper: the excitation becomes noise with no $F_0$, the formants stay.
- C) Low to high: only the excitation changes, $F_0$ rises. Whisper: the tract changes shape, so the formants of /a/ move.
- D) Low to high: the formants rise together with $F_0$. Whisper: the pulses go on with a very low $F_0$, the formants stay.

> [!success]- Answer
> **A.** The excitation carries the pitch and the voicing. The resonances carry the identity of the vowel.
> A higher voice means faster glottal pulses, so $F_0$ rises. The tract still has the shape of /a/, so the formants stay. A whisper has no glottal pulses, only continuous air flow (unvoiced), so there is no $F_0$. The tract shape, and so the formants, stay again.
> - B: The pitch part is wrong. $F_0$ belongs to the source. The formants are set by the tract shape, which is still /a/.
> - C: The whisper part is wrong. Whispering changes only the source. The vowel is still /a/, so the tract and its formants stay.
> - D: Both parts are wrong. The formants do not follow $F_0$, and a whisper has no glottal pulses at all, so it has no $F_0$.

### Q2 (slide 3)

![[cs568_lecture_07_l7_p003.jpg]]

The waveform under the spectrum repeats about every 8 ms. What spacing of the narrow peaks does this give, and which of the two curves in the spectrum is the one that linear prediction (LP) tries to produce?

- A) About 125 Hz. LP produces the jagged curve, with one peak for each harmonic.
- B) About 62.5 Hz. LP produces the smooth curve drawn through the spectrum.
- C) About 125 Hz. LP produces the smooth curve drawn through the spectrum.
- D) About 62.5 Hz. LP produces the jagged curve, with one peak for each harmonic.

> [!success]- Answer
> **C.** The period is $T_0\approx8$ ms, so $F_0=1/T_0\approx1/0.008\ \text{s}=125$ Hz. The narrow peaks ($F_0,H_1,H_2,\dots$) are about 125 Hz apart.
> The smooth curve is the spectral envelope with the wide peaks $F_1$ to $F_4$. LP produces this envelope. The harmonics stay in the residual.
> - A: The spacing is right, but LP models the resonances, not the harmonics. The jagged detail comes from the excitation.
> - B: The curve is right, but $1/(2T_0)$ is the Nyquist-style formula. The harmonic spacing is $1/T_0$.
> - D: Both parts are wrong: the spacing uses $1/(2T_0)$ in place of $1/T_0$, and LP does not produce the harmonic peaks.

## 2. The LP model and the inverse filter (slides 4-7)

### Q3 (slide 4)

Convention of the slides: $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$. The model is $Y(z)=X(z)\,\dfrac{1}{1-\sum_{k=1}^{p}a_kz^{-k}}$. Which statement is correct?

- A) The numerator is 1, so the only roots that shape the spectrum are the $p$ roots of $A_p(z)$, the poles.
- B) The poles of this filter are the coefficients $a_1,\dots,a_p$ themselves, one pole for each coefficient.
- C) $X(z)$ is the speech and $Y(z)$ is the excitation, so this fraction removes the vocal tract.
- D) $p$ is the number of formants, because each coefficient $a_k$ makes one resonance peak.

> [!success]- Answer
> **A.** $Y(z)$ is the speech (output), $X(z)$ the excitation (input), the $a_k$ are the LP coefficients and $p$ is the order. The numerator is the constant 1, so there are no zeros away from $z=0$. All spectral shape comes from the $p$ roots of the denominator $A_p(z)$: the poles. That is why the filter is "all-pole".
> - B: The poles are the roots of $A_p(z)$, not its coefficients. Each pole depends on all the $a_k$.
> - C: Input and output are swapped. The fraction is the synthesis filter: it adds the tract to the excitation.
> - D: A formant needs one complex pole pair, so $p$ poles give at most $p/2$ formants. No single $a_k$ belongs to one formant.

### Q4 (slide 5)

![[cs568_lecture_07_l7_p005.jpg]]

The block diagram has four controls: $T_0$, the V/UV switch, $\sigma$ and $\{a_i\}$. Convention: $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$. A speaker goes from the vowel /a/ to the vowel /i/ at the same pitch and the same loudness. Which statement is correct?

- A) Only $T_0$ changes. The spacing of the pulses decides which resonances are excited, so it sets the vowel.
- B) $\{a_i\}$ and $\sigma$ change. $\sigma$ sets the height of each formant peak, so it must follow the vowel.
- C) $\{a_i\}$ and V/UV change. The switch selects the group of the vowel, front vowel or back vowel.
- D) Only $\{a_i\}$ changes. $T_0$ sets the pitch, $\sigma$ the loudness, and both vowels are voiced.

> [!success]- Answer
> **D.** $T_0$ is the distance between the pulses, so $F_0=1/T_0$. V/UV selects the pulse train or the noise. $\sigma$ is the gain of the excitation, so it sets the loudness. $\{a_i\}$ are the coefficients of $1/A_p(z)$, so they set the formants. /a/ and /i/ differ only in their formants.
> - A: $T_0$ is the pitch period. It sets the harmonic spacing, not the vowel.
> - B: $\sigma$ scales the whole excitation by one number. It cannot raise one formant more than another.
> - C: The switch chooses voiced or unvoiced. /a/ and /i/ are both voiced, so it stays on V.

### Q5 (slide 6)

Convention: $y[n]=e[n]+\sum_{k=1}^{p}a_k\,y[n-k]$, so the error is $e[n]=y[n]-\sum_{k=1}^{p}a_k\,y[n-k]$ (true sample minus prediction). Take $p=2$, $a_1=1.2$, $a_2=-0.5$, $y[n-1]=2$, $y[n-2]=1$ and the true sample $y[n]=2.1$. What is $e[n]$?

- A) $-0.2$
- B) $1.9$
- C) $0.2$
- D) $-0.8$

> [!success]- Answer
> **C.** Prediction: $1.2\cdot2+(-0.5)\cdot1=2.4-0.5=1.9$. Error: $e[n]=2.1-1.9=0.2$.
> - A: This is prediction minus true sample, $1.9-2.1$. The sign of the error is reversed.
> - B: This is the prediction itself, not the error.
> - D: The sign of $a_2$ was dropped: $1.2\cdot2+0.5\cdot1=2.9$, then $2.1-2.9=-0.8$.

### Q6 (slide 7)

Convention: $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$. The inverse filter $A_p(z)$ gets one voiced speech frame as input. Which statement is correct?

- A) It is an all-pole filter, so it can be unstable. Its output is close to a train of pulses, one per pitch period.
- B) It is an all-zero (FIR) filter, so it is always stable. Its output is close to a train of pulses, one per pitch period.
- C) It is an all-zero (FIR) filter, so it is always stable. Its output is a smaller copy of the speech with the same formants.
- D) It is an all-pole filter, so it can be unstable. Its output is a smaller copy of the speech with the same formants.

> [!success]- Answer
> **B.** $A_p(z)$ is a polynomial in $z^{-1}$: its output uses only the present and $p$ past input samples. So it is FIR and always stable. The filter that can be unstable is the synthesis filter $1/A_p(z)$.
> The output of $A_p(z)$ is the residual. The resonances are removed, so for a voiced frame what is left is close to a pulse train. Its spectrum has an about flat envelope.
> - A: The output part is right, but $A_p(z)$ has zeros, not poles. The all-pole filter is $1/A_p(z)$.
> - C: The filter part is right, but the inverse filter removes the formants. It does not keep them.
> - D: Both parts are wrong: $A_p(z)$ is FIR, and its output is the residual, not a copy of the speech.

## 3. Finding the coefficients: covariance method (slides 8-11)

### Q7 (slide 8)

Convention: $e[n]=y[n]-\sum_{k=1}^{p}a_k\,y[n-k]$. The $a_k$ are chosen to minimise $E=\sum_n e^2[n]$. Which statement is correct?

- A) Without the square the errors cancel. With $\lvert e[n]\rvert$ instead, the same linear equations come out, so the square is only a habit.
- B) Without the square the errors cancel. The square also makes $E$ quadratic in the $a_k$, so its zero derivatives are linear equations.
- C) The square weights large errors less than small ones. So the fit follows the formants more closely than the pitch pulses.
- D) A small $E$ is the goal because then the residual holds the resonances, and the filter keeps only the excitation part.

> [!success]- Answer
> **B.** Positive and negative errors cancel in $\sum e[n]$, so a bad predictor can give a sum near zero. The square counts every error as positive. $E$ is then quadratic in the $a_k$, its derivatives are linear, and the minimum comes from $p$ linear equations.
> - A: $\lvert e[n]\rvert$ has no derivative at $e=0$, and its minimum is not given by linear equations.
> - C: The square does the opposite: an error twice as large costs four times as much.
> - D: The roles are swapped. A small residual means the filter holds the predictable part (the resonances), and only the excitation is left in $e[n]$.

### Q8 (slide 9)

Convention: $e[n]=y[n]-\sum_{k=1}^{p}a_k\,y[n-k]$, $E=\sum_{n=0}^{N-1}e^2[n]$. Setting $\partial E/\partial a_j=0$ gives $\sum_{n=0}^{N-1}e[n]\,y[n-j]=0$ for $j=1,\dots,p$. Which statement is correct?

- A) The factor comes from $\partial e[n]/\partial a_j=-y[n-j]$. The residual is uncorrelated with its own past values $e[n-j]$.
- B) The factor comes from $\partial e[n]/\partial a_j=-y[n-j]$. The residual is uncorrelated with the present sample $y[n]$.
- C) The factor comes from $\partial e[n]/\partial a_j=-y[n-j]$. The residual is uncorrelated with every past sample $y[n-j]$.
- D) The factor comes from $\partial e[n]/\partial a_j=-y[n-j]$. The residual is zero at each $n$ where $y[n-j]$ is not zero.

> [!success]- Answer
> **C.** Chain rule: $\partial e^2[n]/\partial a_j=2e[n]\,\partial e[n]/\partial a_j=-2e[n]\,y[n-j]$. So the condition says: at the optimum the residual is uncorrelated with each of the $p$ past samples $y[n-1],\dots,y[n-p]$. If some correlation were left, a different $a_j$ would make $E$ smaller.
> - A: The equation pairs $e[n]$ with past samples of $y$, not of $e$. A white residual needs a large enough order. It does not follow from these equations.
> - B: $j$ starts at 1, so $y[n]$ is not in the equations. In fact $\sum e[n]\,y[n]=E$, which is not zero.
> - D: The equation sets a sum to zero, not each term. The residual is not zero at every sample.

### Q9 (slide 10)

Covariance method, convention $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$, with $\phi(j,k)=\sum_{n=0}^{N-1}y[n-j]\,y[n-k]$. For $p=10$ the system $\Phi=\Psi a$ has 10 unknowns and 10 equations, and $\Psi$ has the elements $\phi(j,k)$. Compare $\phi(1,1)$ and $\phi(2,2)$. Which statement is correct?

- A) $\phi(1,1)=\phi(2,2)$, since both sum the squares of $N$ samples of the same frame. So $\Psi$ is Toeplitz.
- B) $\phi(1,1)\neq\phi(2,2)$ in general. So $\Psi$ is not symmetric, and a general solver must be used.
- C) $\phi(1,1)-\phi(2,2)=y^2[N-1]-y^2[-1]$. So $\Psi$ is symmetric but not Toeplitz.
- D) $\phi(1,1)-\phi(2,2)=y^2[N-2]-y^2[-2]$. So $\Psi$ is symmetric but not Toeplitz.

> [!success]- Answer
> **D.** $\phi(1,1)=y^2[-1]+\dots+y^2[N-2]$ and $\phi(2,2)=y^2[-2]+\dots+y^2[N-3]$. The difference is $y^2[N-2]-y^2[-2]$, not zero in general. So the diagonal of $\Psi$ is not constant: $\Psi$ is symmetric ($\phi(j,k)=\phi(k,j)$) but not Toeplitz, and Levinson-Durbin cannot be used.
> - A: The two sums cover different sets of samples, shifted by one. Equal energy holds only after windowing, in the autocorrelation method.
> - B: Unequal diagonal elements do not break symmetry. $\phi(j,k)=\phi(k,j)$ still holds, so Cholesky works.
> - C: Index slip: this is $\phi(0,0)-\phi(1,1)$, the ranges shifted by one.

### Q10 (slide 11)

Covariance method, convention $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$. A frame comes exactly from an order-2 all-pole filter, and the excitation is zero inside the frame. The analysis uses $p=2$. Which statement is correct?

- A) $E=0$, since the method uses the real samples $y[-2]$, $y[-1]$. Price: Cholesky, $O(p^3)$, and $1/A_p(z)$ may be unstable.
- B) $E>0$, since the samples before the frame count as zero. So the first two samples of the frame always leave an error.
- C) $E=0$. The only price is the $O(p^3)$ solver, since the covariance method always gives a stable $1/A_p(z)$.
- D) $E=0$. Its price: a window must taper the frame edges first, and this window biases the coefficients.

> [!success]- Answer
> **A.** Inside the frame every sample is exactly $a_1y[n-1]+a_2y[n-2]$. The method takes the true samples $y[-2]$, $y[-1]$ before the frame, so the true coefficients give zero error at every $n$. No window is needed, because nothing is set to zero outside the frame. Price: $\Psi$ is only symmetric, so the solver is Cholesky, $O(p^3)$, and stability is not guaranteed.
> - B: That is the autocorrelation method, which sets the signal to zero outside the window. The covariance method uses the real past samples.
> - C: Stability is guaranteed only for the autocorrelation method. In the covariance method the poles may fall outside the unit circle.
> - D: The covariance method needs no window. The window and its bias belong to the autocorrelation method.

## 4. Autocorrelation method and Levinson-Durbin (slides 12-13)

### Q11 (slide 12)

Autocorrelation method, convention $e[n]=y[n]-\sum_{k=1}^{p}a_k\,y[n-k]$. The frame is windowed, so $y[n]=0$ outside $0\le n<N$. The slide says that the error can still be non-zero after the end of the window, where the signal is zero. Why?

- A) The window makes the frame periodic, so after the end the prediction wraps around to the first samples.
- B) For $p$ more samples the prediction still uses the last non-zero samples, so $e[n]=0-\text{prediction}\neq0$.
- C) The error there is the impulse response of $1/A_p(z)$, which rings on after the last sample of the frame.
- D) The excitation goes on after the window ends, and the predictor cannot know it from the past samples.

> [!success]- Answer
> **B.** For $n\ge N$, $y[n]=0$, but the prediction $\sum_ka_ky[n-k]$ still reaches back into the frame for $p$ steps. So $e[n]\ne0$ for $n=N,\dots,N-1+p$. The same happens at the start, where samples are predicted from zeros. These edge errors are why the method is not exact even for a true all-pole signal.
> - A: The autocorrelation method sets everything outside the window to zero. Nothing wraps around.
> - C: The residual is the output of the FIR filter $A_p(z)$, not of $1/A_p(z)$. It stops after $p$ samples.
> - D: After windowing, the method only sees $y[n]$, which is zero there. The true excitation is not part of the computation.

### Q12 (slide 13, matrix)

Convention $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$, $p=10$. The autocorrelation method fills its $10\times10$ matrix with $R(j-k)$, where $R(n)=\sum_my[m]\,y[m+n]$. The covariance method fills $\Psi$ with $\phi(j,k)=\sum_{n=0}^{N-1}y[n-j]\,y[n-k]$. How many different numbers must be computed to fill each matrix (the matrix only, not the left-hand side)?

- A) Autocorrelation: 19. Covariance: 100.
- B) Autocorrelation: 10. Covariance: 100.
- C) Autocorrelation: 10. Covariance: 55.
- D) Autocorrelation: 19. Covariance: 55.

> [!success]- Answer
> **C.** $j-k$ runs from $-9$ to $9$, and $R(-n)=R(n)$, so only $R(0),\dots,R(9)$ are needed: 10 numbers, one per diagonal. $\phi(j,k)=\phi(k,j)$, so only $j\le k$ is needed: $10\cdot11/2=55$ numbers.
> - A: Symmetry was ignored in both: $2p-1=19$ diagonals and $p^2=100$ elements.
> - B: Symmetry of $\Psi$ was ignored: all $p^2=100$ elements were counted.
> - D: $R(-n)=R(n)$ was ignored: the $2p-1=19$ diagonals were counted as different.

### Q13 (slide 13, Levinson-Durbin)

Autocorrelation method, convention $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$, $R(n)=\sum_my[m]\,y[m+n]$. The windowed frame is $y=(1,2,1)$, $p=2$. Run the Levinson-Durbin recursion. What is the final coefficient $a_1$ of the order-2 predictor?

- A) $1.000$
- B) $0.667$
- C) $0.852$
- D) $0.333$

> [!success]- Answer
> **A.** $R(0)=6$, $R(1)=4$, $R(2)=1$. Order 1: $k_1=4/6=2/3$, $E^{(1)}=(1-4/9)\cdot6=10/3$.
> Order 2: $k_2=\dfrac{R(2)-a_1^{(1)}R(1)}{E^{(1)}}=\dfrac{1-8/3}{10/3}=-0.5=a_2$.
> Update: $a_1=a_1^{(1)}-k_2a_1^{(1)}=\tfrac23+0.5\cdot\tfrac23=1$. Solving the $2\times2$ system directly gives the same $a_1=1$, $a_2=-0.5$.
> - B: This is $k_1=2/3\approx0.667$. The final $a_1$ is not the first reflection coefficient: it is updated at order 2.
> - C: $k_2$ was divided by $E^{(0)}=6$ instead of $E^{(1)}$: $k_2=-5/18$, then $a_1=\tfrac23(1+\tfrac5{18})=46/54\approx0.852$.
> - D: Plus sign in the update: $\tfrac23+(-0.5)\cdot\tfrac23=1/3\approx0.333$.

## 5. Spectral envelope and model order (slides 14-18)

### Q14 (slide 14)

Convention $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$. The LP spectrum is $\lvert H\rvert^2=\left\lvert G/(1-\sum_{k=1}^{p}a_ke^{-j2\pi kf/f_s})\right\rvert^2$. Only $G$ is doubled; the $a_k$ stay. Which statement is correct?

- A) $\lvert H\rvert^2$ becomes 2 times larger. The curve moves up by about 3 dB at every frequency, and the peaks stay.
- B) $\lvert H\rvert^2$ becomes 4 times larger. The curve moves up by about 6 dB at every frequency, and the peaks stay.
- C) The curve moves up by about 6 dB, and the formant peaks get narrower, since they rise more than the valleys.
- D) Only the formant peaks rise by about 6 dB, and the valleys stay, since $G$ scales the resonances.

> [!success]- Answer
> **B.** $G$ is a constant gain, outside the frequency-dependent denominator. $\lvert H\rvert^2$ contains $G^2$, so doubling $G$ multiplies it by 4: $10\log_{10}4\approx6$ dB at every frequency. The shape, the peak positions and the bandwidths stay.
> - A: The square was forgotten: $\lvert H\rvert^2$ grows with $G^2$, not with $G$.
> - C: A constant dB shift raises peaks and valleys equally. The bandwidths depend only on the $a_k$.
> - D: $G$ does not depend on $f$, so it cannot treat peaks and valleys differently.

### Q15 (slides 15-16)

![[cs568_lecture_07_l7_p016.jpg]]

Plot (a) compares the STFT of a frame with the LP spectrum for $p=12$ (thick) and $p=40$ (thin). The axis label says "kHz", but the ticks 0 to 4000 are in Hz (slide error). Plot (b) is the normalised prediction error against $p$. Convention $A_p(z)=1-\sum_ka_kz^{-k}$. Which statement is correct?

- A) The $p=40$ curve is the better envelope, since its error in plot (b) is less than half the error at $p=12$.
- B) The $p=12$ curve follows each harmonic peak of the STFT, and the $p=40$ curve smooths these peaks away.
- C) At $f_s=16$ kHz the rule $p=4+f_s/1000$ gives $p=12$, the same order as the thick curve.
- D) From $p=12$ to $p=40$ the error only falls from about 0.035 to 0.015. The extra poles follow the harmonics.

> [!success]- Answer
> **D.** In (b) the error falls fast at low orders (about 0.095 at $p=1$) and flattens above $p\approx12$. From 12 to 40 it only goes from about 0.035 to about 0.015. In (a) the thin $p=40$ curve goes up and down with every harmonic: the extra poles model the pitch, not the vocal tract.
> - A: A smaller error is not a better envelope. The extra error drop comes from fitting the harmonics, which belong to the excitation.
> - B: The roles are swapped. The thick $p=12$ curve is smooth, the thin $p=40$ curve follows the harmonics.
> - C: $f_s/2=8$ kHz was used in place of $f_s$. The rule gives $4+16=20$.

### Q16 (slide 17)

The slide builds the output spectrum from three parts: a glottal source spectrum (a set of lines when periodic, falling with frequency), a vocal-tract gain with formant peaks, and a lip radiation that rises with frequency. Convention $A_p(z)=1-\sum_ka_kz^{-k}$, rule of thumb $p=4+f_s/1000$ ($f_s$ in Hz). Which statement is correct?

- A) The term $f_s/1000$ gives a pole pair per formant, about one formant per kHz up to $f_s/2$. The lines stay in the residual.
- B) The term $f_s/1000$ gives one pole per formant, about one formant per kHz up to $f_s$. The lines stay in the residual.
- C) The term 4 gives two pole pairs for the pitch harmonics. The slopes of the glottis and the lips stay in the residual.
- D) The term 4 gives two pole pairs for the glottal and lip slopes. The lines go into the poles close to the unit circle.

> [!success]- Answer
> **A.** The band is $0$ to $f_s/2$, with about one formant per kHz, so $f_s/2000$ formants. Each needs one complex pole pair, so $f_s/1000$ poles. The term 4 is 2 pole pairs for the overall slope of the glottal spectrum (falling) and the radiation (rising). The line structure of the periodic source is not in the filter. It stays in the residual as a pulse train.
> - B: A real filter needs a complex pole pair for each resonance, and the band only goes to $f_s/2$. The count is the same, but the reason is wrong.
> - C: The harmonics are not modelled by the filter. The 4 poles model the smooth slope, which is part of the envelope.
> - D: The term 4 is right, but the lines are not modelled. The poles near the circle are the formants.

### Q17 (slide 18)

Convention $A_p(z)=1-\sum_ka_kz^{-k}$. A demo first does inverse filtering on one voiced frame and then synthesises the frame "with simple excitation models". Which statement is correct?

- A) Synthesis sends the true residual through $1/A_p(z)$. The result differs from the original only by rounding of the $a_k$.
- B) Inverse filtering turns the speech into the $a_k$. Synthesis then sends the residual through $A_p(z)$ to give speech.
- C) The frame differs because $1/A_p(z)$ with only $p$ poles cannot make the formants. The excitation is not the cause.
- D) Synthesis uses the $a_k$, the gain, "voiced" and $T_0$. Ideal pulses replace the true residual, so the waveform differs.

> [!success]- Answer
> **D.** Inverse filtering: input the frame $y[n]$ and the $a_k$, output the residual $e[n]$. Synthesis: a pulse train with period $T_0$, scaled by the gain, goes through $1/A_p(z)$. If the true residual were used, the output would be exactly $y[n]$. The simple model loses the details of the residual: same envelope and pitch, different waveform.
> - A: With the true residual, $1/A_p(z)$ undoes $A_p(z)$ exactly. "Simple excitation models" means the residual is replaced.
> - B: The $a_k$ come from the analysis, not from the inverse filter. And speech comes out of $1/A_p(z)$, not $A_p(z)$.
> - C: The envelope is the same in both frames. The difference comes from the replaced excitation.

## 6. Alternative representations: root pairs and LSF (slides 19-22)

### Q18 (slide 19)

Convention $A_p(z)=1-\sum_ka_kz^{-k}$. The slide says the LP coefficients $a_k$ are not a good final representation and lists root pairs, line spectral frequencies, reflection coefficients and log-area ratios. Which statement is correct?

- A) The $a_k$ need $O(p^3)$ operations to compute. The four other forms are cheaper approximations of the filter.
- B) One $a_k$ does not belong to one formant, and rounding can move poles outside the circle. All forms are the same filter.
- C) Each $a_k$ sets one formant, so they are easy to read, but rounding makes them unstable. All forms are the same filter.
- D) The $a_k$ are hard to read and very sensitive to rounding. The four forms are different filters with the same formants.

> [!success]- Answer
> **B.** Hard to interpret: every $a_k$ changes the whole spectrum. Sensitive to precision: a small rounding error in one $a_k$ can move the poles a lot, even outside the unit circle. Root pairs, LSFs, reflection coefficients and LARs all describe the same $1/A_p(z)$ and convert back to the $a_k$.
> - A: Levinson-Durbin gives the $a_k$ in $O(p^2)$. The other forms are exact, not approximations.
> - C: No single $a_k$ belongs to one formant. That is exactly why they are hard to read.
> - D: The two reasons are right, but the forms are exact conversions of one filter, not different filters.

### Q19 (slides 20-21, z-plane)

![[cs568_lecture_07_l7_p021.jpg]]

In the left plot the red crosses are the poles of a 12th-order LP model. One cross lies near $(-0.62,\,0.73)$. The figure gives no sampling rate: assume $f_s=8$ kHz. Use $F=\theta f_s/(2\pi)$ and $B\approx-(f_s/\pi)\ln\rho$ for a pole $\rho e^{j\theta}$. Convention $A_p(z)=1-\sum_ka_kz^{-k}$. What are $F$ and $B$ of this pole?

- A) $F\approx1450$ Hz, $B\approx110$ Hz
- B) $F\approx2900$ Hz, $B\approx110$ Hz
- C) $F\approx1450$ Hz, $B\approx55$ Hz
- D) $F\approx2900$ Hz, $B\approx55$ Hz

> [!success]- Answer
> **B.** $\rho=\sqrt{0.62^2+0.73^2}\approx0.96$, $\theta\approx130^\circ$. $F\approx\frac{130}{360}\cdot8000\approx2900$ Hz. $B\approx-\frac{8000}{\pi}\ln0.96\approx110$ Hz. The pole is close to the circle and narrow: a formant. The cross near $(-0.22,\,0.5)$ has $\rho\approx0.55$ and $B\approx1500$ Hz: no peak, only overall shaping.
> - A: In $F$ the Nyquist frequency 4 kHz was used in place of $f_s$.
> - C: The Nyquist frequency 4 kHz was used in place of $f_s$ in both formulas.
> - D: $B$ was computed with $f_s/(2\pi)$, the factor of the $F$ formula, in place of $f_s/\pi$.

### Q20 (slides 20-21, LSP plot)

![[cs568_lecture_07_l7_p021.jpg]]

In the right plot the vertical lines are the line spectral frequencies (LSFs): the angles of the roots of $P(z)$ and $Q(z)$, one kind of line solid, the other dashed. Convention $A_p(z)=1-\sum_ka_kz^{-k}$. Which statement is correct?

- A) Close pairs stand in the flat part near 1 kHz and above about 2.8 kHz. A close pair means a strong resonance.
- B) Close pairs stand at the sharp peaks, near 550, 1750 and 2350 Hz. A close pair marks the edges of a wide formant.
- C) The lines are about evenly spaced, so the spacing tells nothing about the formants. Only their number gives $p$.
- D) Close pairs stand at the sharp peaks, near 550, 1750 and 2350 Hz. A close pair means a pole near the circle.

> [!success]- Answer
> **D.** Solid and dashed lines alternate. They stand close together at the three sharp peaks (about 550, 1750 and 2350 Hz) and far apart near 1 kHz and above about 2.8 kHz. Two close LSFs mean a pole of $1/A_p(z)$ near the unit circle: a strong, narrow formant.
> - A: The reading is reversed. In the flat regions the lines are far apart.
> - B: The location is right, but a close pair means a narrow formant, not a wide one.
> - C: The spacing is clearly uneven, and it is exactly what shows the resonances.

### Q21 (slide 22)

Convention $A(z)=1-\sum_{k=1}^{p}a_kz^{-k}$ with $p=2$, $a_1=0.6$, $a_2=-0.3$. The line spectrum pair polynomial is $P(z)=A(z)+z^{-(p+1)}A(z^{-1})$. What is $P(z)$?

- A) $1-0.9z^{-1}+0.9z^{-2}-z^{-3}$
- B) $1+0.3z^{-1}+0.3z^{-2}+z^{-3}$
- C) $1-0.3z^{-1}-0.3z^{-2}+z^{-3}$
- D) $1.3-1.2z^{-1}+1.3z^{-2}$

> [!success]- Answer
> **C.** $A(z)=1-0.6z^{-1}+0.3z^{-2}$. $z^{-3}A(z^{-1})=z^{-3}(1-0.6z+0.3z^2)=0.3z^{-1}-0.6z^{-2}+z^{-3}$: the coefficients reversed and delayed by one sample. Sum: $1-0.3z^{-1}-0.3z^{-2}+z^{-3}$.
> Check: $Q(z)=1-0.9z^{-1}+0.9z^{-2}-z^{-3}$ and $P+Q=2A$. The roots of $P$ ($\pm49.5^\circ$, $180^\circ$) and $Q$ ($0^\circ$, $\pm92.9^\circ$) lie on the unit circle.
> - A: This is $Q(z)$: the reversed polynomial was subtracted, not added.
> - B: The plus convention $A(z)=1+\sum_ka_kz^{-k}$ was used: $A=1+0.6z^{-1}-0.3z^{-2}$.
> - D: The delay $z^{-p}$ was used in place of $z^{-(p+1)}$.

## 7. Reflection coefficients and log-area ratios (slides 23-24)

### Q22 (slide 23)

Convention $A_p(z)=1-\sum_{k=1}^{p}a_kz^{-k}$. A predictor has $p=2$, $a_1=1.2$, $a_2=-0.5$. The backward recursion is $r_i=a_i^{(i)}$ and $a_j^{(i-1)}=\dfrac{a_j^{(i)}+a_i^{(i)}\,a_{i-j}^{(i)}}{1-r_i^2}$. What are $r_2$ and $r_1$, and is the synthesis filter stable?

- A) $r_2=-0.5$, $r_1=0.8$. Stable.
- B) $r_2=-0.5$, $r_1=1.2$. Unstable.
- C) $r_2=-0.5$, $r_1=0.6$. Stable.
- D) $r_2=-0.5$, $r_1=2.4$. Unstable.

> [!success]- Answer
> **A.** $r_2=a_2^{(2)}=-0.5$. Step down: $a_1^{(1)}=\dfrac{1.2+(-0.5)(1.2)}{1-0.25}=\dfrac{0.6}{0.75}=0.8$, so $r_1=0.8$. Both $\lvert r_i\rvert<1$, so $1/A_p(z)$ is stable (its poles have radius $0.71$).
> - B: $r_1=a_1$ was taken without the step down. Only the last coefficient of each order is a reflection coefficient.
> - C: The division by $1-r_2^2=0.75$ was forgotten.
> - D: A minus sign in the numerator: $a_1-a_2a_1=1.2+0.6=1.8$, then $1.8/0.75=2.4$.

### Q23 (slide 24)

Log-area ratio: $g=\ln\dfrac{1-r}{1+r}$, with $r$ a reflection coefficient (convention $A_p(z)=1-\sum_ka_kz^{-k}$). When $r$ goes from $0$ to $0.09$, $g$ changes by about $0.18$. By how much does $g$ change when $r$ goes from $0.90$ to $0.99$?

- A) About $0.18$
- B) About $1.02$
- C) About $2.35$
- D) About $0.048$

> [!success]- Answer
> **C.** $g(0.90)=\ln\frac{0.10}{1.90}\approx-2.94$, $g(0.99)=\ln\frac{0.01}{1.99}\approx-5.29$, a change of about 2.35, about 13 times the change near $r=0$. The spectrum is very sensitive to $r$ near $\lvert r\rvert=1$, so a uniform quantiser on $g$ puts fine steps where they are needed.
> - A: The small-$r$ approximation $g\approx-2r$ was used outside its range.
> - B: $\log_{10}$ was used in place of $\ln$.
> - D: The logarithm was forgotten: $\frac{0.10}{1.90}-\frac{0.01}{1.99}\approx0.048$.
