---
course: CS 568 Speech Processing
lesson: CS568 L10 - Probability and estimation theory
type: quiz
questions: 34
---

# CS568 L10 - Probability and estimation theory - quiz

Lesson note: [[CS568 L10 - Probability and estimation theory]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Probability basics (slides 2-6)

### Q1 (slide 2)

A sample space $S$ is cut into four regions $A_1,\dots,A_4$. The regions do not overlap, and together they fill $S$. A bar chart shows one probability per region. The axioms are: I: $P[A]\ge0$. II: $P[S]=1$. III: if $A_i\cap A_j=\emptyset$, then $P[A_i\cup A_j]=P[A_i]+P[A_j]$. Which axioms together force the four bars to sum to 1?

- A) Axioms I and III: every bar is non-negative, and the bars add up.
- B) Axiom III alone: the four disjoint regions add up to exactly 1.
- C) Axioms II and III: the bars add up to $P[S]$, and $P[S]$ is 1.
- D) Axioms I and II: no bar is negative, and the whole space has 1.

> [!success]- Answer
> **C.** Axiom III, used three times for the disjoint regions, gives $P[A_1]+P[A_2]+P[A_3]+P[A_4]=P[A_1\cup A_2\cup A_3\cup A_4]=P[S]$. Axiom II gives $P[S]=1$.
> Example: with $P[A_1]=0.25$, $P[A_2]=0.35$, $P[A_3]=0.30$ this gives $P[A_4]=1-0.90=0.10$.
> - A: Axiom I only says that each bar is $\ge0$. Without Axiom II nothing fixes the total at 1.
> - B: Axiom III only says that the sum equals $P[S]$. That $P[S]$ is 1 comes from Axiom II.
> - D: Without Axiom III the probability of the union cannot be written as the sum of the bars.

### Q2 (slide 3)

There are three cards. Card A is red on both sides, card B is blue on both sides, card C is red on one side and blue on the other. A card is drawn at random (each with probability $\frac13$), and one of its sides, also at random, is shown. The shown side is red. A player bets that the hidden side has the same colour as the shown side: he wins 1 dollar if it has and loses 1 dollar if not. What are $P[\text{card A}\mid\text{red shown}]$ and the player's average gain per game?

- A) $\frac12$, and the average gain is 0 dollars.
- B) $\frac13$, and the average gain is $-\frac13$ dollar.
- C) $1$, and the average gain is $+1$ dollar.
- D) $\frac23$, and the average gain is $+\frac13$ dollar.

> [!success]- Answer
> **D.** $P[\text{red}\mid A]=1$, $P[\text{red}\mid B]=0$, $P[\text{red}\mid C]=\frac12$.
> $P[\text{red}]=\frac13\cdot1+\frac13\cdot0+\frac13\cdot\frac12=\frac12$.
> $P[A\mid\text{red}]=\dfrac{1\cdot\frac13}{\frac12}=\frac23$. The hidden side has the same colour only for card A.
> Average gain: $\frac23\cdot1-\frac13\cdot1=\frac13$ dollar per game.
> - A: This assumes that A and C are still equally likely after a red side is seen. But A always shows red, C only half of the time.
> - B: The evidence is forgotten: $1\cdot\frac13$ is not divided by $P[\text{red}]=\frac12$.
> - C: $P[A\mid\text{red}]$ is taken as $P[\text{red}\mid A]=1$.

### Q3 (slide 4)

Someone claims: "$P[A]=0.6$, $P[B]=0.7$, and $A$ and $B$ are mutually exclusive ($A\cap B=\emptyset$)." Use $P[A\cup B]=P[A]+P[B]-P[A\cap B]$ and $P[A\cup B]\le1$. The claim is impossible. What is the smallest value that $P[A\cap B]$ can have?

- A) 0.30
- B) 0.42
- C) 0.10
- D) 0.60

> [!success]- Answer
> **A.** Mutually exclusive events would give $P[A\cup B]=0.6+0.7=1.3>1$.
> In general $P[A\cap B]=0.6+0.7-P[A\cup B]\ge1.3-1=0.3$. The events must overlap with probability at least 0.3.
> - B: $0.6\cdot0.7$ is the product rule of independent events. Independence is a different property, and it gives no lower bound.
> - C: $0.7-0.6$ is just the difference of the two probabilities. No rule gives this bound.
> - D: 0.6 is the largest possible overlap ($A$ inside $B$), not the smallest.

### Q4 (slide 5)

$P[A]=0.5$, $P[B]=0.2$ and $P[A\cap B]=0.15$. $P[A\mid B]$ is the probability of $A$ when we know that $B$ has happened. Find $P[A\mid B]$.

- A) 0.30
- B) 0.15
- C) 0.10
- D) 0.75

> [!success]- Answer
> **D.** $P[A\mid B]=\dfrac{P[A\cap B]}{P[B]}=\dfrac{0.15}{0.2}=0.75$.
> $B$ becomes the new sample space. The division by $P[B]$ re-normalises, so that $P[B\mid B]=1$.
> - A: $0.15/0.5$ divides by $P[A]$. That is $P[B\mid A]$: the condition is swapped.
> - B: The joint probability $P[A\cap B]$ is taken as the conditional one. It is not re-normalised.
> - C: $0.5\cdot0.2$ assumes independence and ignores the given $P[A\cap B]$.

### Q5 (slide 6)

Three speakers $B_1,B_2,B_3$ made 50%, 30% and 20% of the recordings in a database. Each recording comes from exactly one speaker. A recording of speaker $k$ is noisy (event $A$) with probability $P[A\mid B_k]=0.02,\ 0.05,\ 0.10$ for $k=1,2,3$. What is $P[A]$, the share of noisy recordings in the database?

- A) 0.057
- B) 0.045
- C) 0.170
- D) 0.069

> [!success]- Answer
> **B.** Total probability: $P[A]=\sum_kP[A\mid B_k]P[B_k]=0.02\cdot0.5+0.05\cdot0.3+0.10\cdot0.2=0.010+0.015+0.020=0.045$.
> The pieces $A\cap B_k$ can be added because the $B_k$ do not overlap and together cover $S$.
> - A: $0.17/3$ is the plain average of the three conditional probabilities. It treats the speakers as equally frequent.
> - C: $0.02+0.05+0.10$ adds the conditional probabilities without the weights $P[B_k]$.
> - D: The weights are paired in reverse order: $0.02\cdot0.2+0.05\cdot0.3+0.10\cdot0.5$.

## 2. Bayes theorem (slides 7-12)

### Q6 (slide 7)

Three speakers $B_1,B_2,B_3$ have $P[B_k]=0.5,\ 0.3,\ 0.2$ (each recording comes from exactly one of them). A recording of speaker $k$ is noisy (event $A$) with $P[A\mid B_k]=0.02,\ 0.05,\ 0.10$. A recording is noisy. What is $P[B_3\mid A]$, the probability that speaker 3 made it?

- A) 0.020
- B) 0.100
- C) 0.444
- D) 0.118

> [!success]- Answer
> **C.** Bayes: $P[B_3\mid A]=\dfrac{P[A\mid B_3]P[B_3]}{\sum_kP[A\mid B_k]P[B_k]}$.
> Denominator (total probability): $0.010+0.015+0.020=0.045$. Numerator: $0.10\cdot0.2=0.020$.
> $P[B_3\mid A]=0.020/0.045\approx0.444$. Speaker 3 has the smallest prior but the largest posterior.
> - A: The evidence is forgotten: the numerator $0.020$ is not divided by $P[A]$.
> - B: $P[B_3\mid A]$ is taken as $P[A\mid B_3]=0.10$.
> - D: $0.020/0.17$ divides by the sum of the likelihoods instead of the total probability $P[A]$.

### Q7 (slide 8)

Two phoneme classes have priors $P[\omega_1]=0.7$ and $P[\omega_2]=0.3$. For an observed feature vector $x$ the likelihoods are $P[x\mid\omega_1]=0.2$ and $P[x\mid\omega_2]=0.4$. The Bayes decision rule picks the class with the larger posterior $P[\omega_j\mid x]=P[x\mid\omega_j]\,P[\omega_j]/P[x]$. Which statement is correct?

- A) Choose $\omega_2$: its likelihood 0.4 is larger, and priors only matter for equal likelihoods.
- B) Choose $\omega_1$: $0.2\cdot0.7=0.14>0.4\cdot0.3=0.12$, and $P[x]$ is the same for both.
- C) Choose $\omega_1$, but only after $P[x]$ is known: without it the products cannot be compared.
- D) Choose $\omega_2$: $0.4/0.3\approx1.33>0.2/0.7\approx0.29$, so its posterior is larger.

> [!success]- Answer
> **B.** Compare likelihood times prior: $0.14$ for $\omega_1$ and $0.12$ for $\omega_2$. Choose $\omega_1$.
> $P[x]=0.14+0.12=0.26$ is the same positive number for both classes. Dividing by it does not change which product is larger. It only makes the posteriors ($\approx0.538$ and $\approx0.462$) sum to 1.
> - A: The prior is ignored. Here the prior turns the decision around.
> - C: The decision is right, but the reason is wrong. Both products are divided by the same $P[x]$, so they can be compared directly.
> - D: The likelihood is divided by the prior instead of multiplied by it.

### Q8 (slide 9)

A test for a condition (COND) has sensitivity 90% and specificity 98%. The prevalence is 1%. POS and NEG are the two test results, and $\neg$COND means healthy. Which statement is correct?

- A) Specificity is $P[\text{NEG}\mid\neg\text{COND}]=0.98$, so the false-positive rate is $0.02$.
- B) Specificity is $P[\neg\text{COND}\mid\text{NEG}]=0.98$: 98% of the negatives are healthy.
- C) The false-positive rate is $P[\text{POS}\mid\neg\text{COND}]=1-0.90=0.10$.
- D) In Bayes rule the prior is the sensitivity 0.90, the likelihood is the prevalence 0.01.

> [!success]- Answer
> **A.** Specificity is the share of healthy people who test negative: $P[\text{NEG}\mid\neg\text{COND}]=0.98$. The false-positive rate is $P[\text{POS}\mid\neg\text{COND}]=1-0.98=0.02$.
> - B: The condition is swapped. $P[\neg\text{COND}\mid\text{NEG}]$ is a posterior. It also depends on the prevalence (here it is about 0.999).
> - C: $1-\text{sensitivity}=P[\text{NEG}\mid\text{COND}]=0.10$ is the false-negative rate, not the false-positive rate.
> - D: The roles are swapped. The prior is the prevalence 0.01. The likelihood of a positive result for a sick person is the sensitivity 0.90.

### Q9 (slides 10-11)

10,000 people are tested. 90 sick people test positive, 10 sick people test negative, 198 healthy people test positive and 9702 healthy people test negative. What fraction of the positive results are false (they come from healthy people)?

- A) 0.0200
- B) 0.6875
- C) 0.3125
- D) 0.0288

> [!success]- Answer
> **B.** Positive results: $90+198=288$. False ones: 198. Fraction: $198/288=0.6875$, about 69%.
> - A: $198/9900$ divides by the healthy people, not by the positives. That is the false-positive rate $P[\text{POS}\mid\neg\text{COND}]$.
> - C: $90/288$ is the fraction of true positives, $P[\text{COND}\mid\text{POS}]$.
> - D: $288/10000$ is $P[\text{POS}]$, the share of all tests that are positive.

### Q10 (slide 12)

A test has prevalence 1%, sensitivity 90% and specificity 98%. Keep the prevalence. Change 1: sensitivity from 90% to 99%. Change 2: specificity from 98% to 99.9%. Which single change gives the larger $P[\text{cond}\mid+]$, and what is its value?

- A) Sensitivity to 99%: $P[\text{cond}\mid+]$ becomes about 0.99.
- B) Specificity to 99.9%: $P[\text{cond}\mid+]$ becomes about 0.999.
- C) Specificity to 99.9%: $P[\text{cond}\mid+]$ becomes about 0.90.
- D) Sensitivity to 99%: $P[\text{cond}\mid+]$ becomes about 0.33.

> [!success]- Answer
> **C.** $P[\text{cond}\mid+]=\dfrac{P[+\mid\text{cond}]P[\text{cond}]}{P[+\mid\text{cond}]P[\text{cond}]+P[+\mid\neg\text{cond}]P[\neg\text{cond}]}$.
> Change 2: $\dfrac{0.90\cdot0.01}{0.009+0.001\cdot0.99}=\dfrac{0.009}{0.00999}\approx0.901$.
> Change 1: $\dfrac{0.99\cdot0.01}{0.0099+0.02\cdot0.99}=\dfrac{0.0099}{0.0297}\approx0.333$.
> The false-positive term is multiplied by the large $P[\neg\text{cond}]=0.99$ and dominates. Only a smaller false-positive rate makes it small.
> - A: $P[\text{cond}\mid+]$ is taken as $P[+\mid\text{cond}]$, the sensitivity.
> - B: $P[\text{cond}\mid+]$ is taken as the specificity $P[-\mid\neg\text{cond}]$.
> - D: 0.33 is the right value for change 1, but change 2 gives 0.90, which is larger.

## 3. Random variables, cdf and pdf (slides 13-17)

### Q11 (slide 13)

A fair coin is tossed three times. The random variable $X$ counts the heads. An outcome $\xi$ is one sequence, such as HHT. Which statement is correct?

- A) $S$ has 8 equally likely outcomes, and $P[X=1]=P[X=2]=\frac38$.
- B) $S$ has 4 outcomes, one per value 0 to 3, so $P[X=1]=\frac14$.
- C) $X$ is random: one outcome $\xi$, such as HHT, can give different values.
- D) $P[X=1]=\frac18$, because one outcome, HTT, has exactly one head.

> [!success]- Answer
> **A.** $S$ has $2^3=8$ equally likely outcomes. One head: HTT, THT, TTH, so $P[X=1]=\frac38$. Two heads: HHT, HTH, THH, so $P[X=2]=\frac38$.
> - B: The values of $X$ are taken as the outcomes. The values 0 to 3 are not equally likely.
> - C: $X$ is a fixed function: HHT always gives 2. The randomness is in which outcome $\xi$ occurs.
> - D: THT and TTH are missed. The order of the tosses matters.

### Q12 (slide 14)

$X$ is the result of rolling a fair die (faces 1 to 6). Its cdf is $F_X(x)=P[X\le x]$, a staircase with one jump at each face. What is $F_X(4)$?

- A) $\frac36$
- B) $\frac16$
- C) $\frac46$
- D) $\frac26$

> [!success]- Answer
> **C.** $F_X(4)=P[X\le4]$ counts the faces 1, 2, 3, 4: $\frac46$. At a jump the cdf takes the upper value (it is continuous from the right).
> - A: This is $P[X<4]$, the lower value of the step. The axis label "P(X<x)" on slide 14 is a slip. The definition is $P[X\le x]$.
> - B: This is the pmf $P[X=4]$, the height of one jump, not the accumulated probability.
> - D: This is $P[X>4]=1-F_X(4)$, the complement.

### Q13 (slide 15)

$X$ is uniform on $[0,\ 0.25]$: its pdf $f_X$ is a constant on this interval and 0 outside. Find $P[0.10<X<0.15]$.

- A) 0.05
- B) 0.20
- C) 0.60
- D) 0.0125

> [!success]- Answer
> **B.** The area under the pdf is 1: height $\times\,0.25=1$, so $f_X=4$ on the interval. A pdf above 1 is allowed, since it is a density.
> $P[0.10<X<0.15]=4\times0.05=0.20$. Outside the interval the pdf is 0, so the property is $f_X\ge0$, not $f_X>0$ as slide 15 prints.
> - A: The height is taken as 1, as if a pdf could not exceed 1. Then only the width 0.05 counts.
> - C: $F_X(0.15)=4\cdot0.15=0.60$. The value $F_X(0.10)=0.40$ is not subtracted.
> - D: The height is taken as 0.25, the width of the interval: $0.25\times0.05$.

### Q14 (slide 16)

A weight pdf has the value $f_X(150)=0.008$ per lb, and it is nearly constant between 148 lb and 152 lb. About how large is the probability of a weight between 148 lb and 152 lb?

- A) 0.008
- B) 0.016
- C) 0.002
- D) 0.032

> [!success]- Answer
> **D.** $P\approx f_X(150)\,\Delta$ with the width $\Delta=152-148=4$ lb: $0.008\times4=0.032$.
> - A: The density value is taken as a probability. A pdf in "per lb" must be multiplied by a width in lb.
> - B: Only the half-width 2 lb is used.
> - C: The density is divided by the width instead of multiplied.

### Q15 (slide 17)

$X$ is uniform on $[0,6]$, so $f_X(x)=\frac16$ there and 0 outside. Use $E[X]=\int x\,f_X(x)\,dx$ and $\mathrm{var}[X]=E[X^2]-(E[X])^2$. What is $\mathrm{var}[X]$?

- A) 12
- B) 1.73
- C) 3
- D) 0.5

> [!success]- Answer
> **C.** $E[X]=\int_0^6x\,\frac16\,dx=\frac16\cdot\frac{36}{2}=3$. $E[X^2]=\int_0^6x^2\,\frac16\,dx=\frac16\cdot\frac{216}{3}=12$.
> $\mathrm{var}[X]=12-3^2=3$.
> - A: This is $E[X^2]$, the second moment. The term $(E[X])^2=9$ is not subtracted.
> - B: $\sqrt3\approx1.73$ is the standard deviation, not the variance.
> - D: The uniform shortcut $(b-a)^2/12$ with the square forgotten: $6/12$.

## 4. Random vectors and covariance (slides 18-20)

### Q16 (slide 18)

Two random variables have the joint pdf $f_{X_1X_2}(x_1,x_2)=x_1+x_2$ on the unit square $0\le x_1,x_2\le1$ (0 outside). The marginal pdf of $x_1$ integrates out $x_2$. What is $f_{X_1}(x_1)$ for $0\le x_1\le1$?

- A) $x_1$
- B) $x_1+1$
- C) $\frac{x_1^2}{2}+\frac12$
- D) $x_1+\frac12$

> [!success]- Answer
> **D.** $f_{X_1}(x_1)=\int_0^1(x_1+x_2)\,dx_2=x_1\cdot1+\frac12$.
> Check: $\int_0^1\big(x_1+\frac12\big)dx_1=\frac12+\frac12=1$, and it is never negative.
> - A: $x_2$ is set to 0 instead of integrated out. The area is only $\frac12$.
> - B: $\int_0^1x_2\,dx_2$ is taken as 1 instead of $\frac12$. The area is $\frac32$.
> - C: $x_1$ is integrated as if it were the variable. For $dx_2$ it is a constant, so its integral is $x_1\cdot1$. The area is $\frac23$.

### Q17 (slide 19)

A random vector has $N=3$ components. Its covariance matrix $\boldsymbol\Sigma$ has the entries $c_{ik}=E[(x_i-\mu_i)(x_k-\mu_k)]$ for $i,k=1,2,3$. How many different numbers must be stored to know $\boldsymbol\Sigma$ in general?

- A) 9
- B) 6
- C) 3
- D) 12

> [!success]- Answer
> **B.** The product $(x_i-\mu_i)(x_k-\mu_k)$ does not depend on the order of its factors, so $c_{ki}=c_{ik}$: $\boldsymbol\Sigma$ is symmetric. Store 3 numbers on the diagonal and 3 above it: $N(N+1)/2=6$.
> - A: $N^2=9$ ignores the symmetry.
> - C: Only the diagonal (the variances). This takes the covariances as zero.
> - D: $N(N+1)=12$, with the division by 2 forgotten.

### Q18 (slide 20)

Two features $x_i$ and $x_k$ have standard deviations $\sigma_i=2$ and $\sigma_k=3$ and correlation coefficient $\rho_{ik}=-0.5$. What is their covariance $c_{ik}$?

- A) $-3$
- B) $-18$
- C) $-\frac1{12}$
- D) $-1.5$

> [!success]- Answer
> **A.** $c_{ik}=\rho_{ik}\,\sigma_i\sigma_k=-0.5\cdot2\cdot3=-3$. It satisfies the bound $\lvert c_{ik}\rvert\le\sigma_i\sigma_k=6$. So $\boldsymbol\Sigma=\begin{bmatrix}4&-3\\-3&9\end{bmatrix}$. The negative sign means that $x_k$ tends to decrease when $x_i$ increases.
> - B: The variances 4 and 9 are used instead of the standard deviations: $-0.5\cdot36$.
> - C: $\rho_{ik}$ is divided by $\sigma_i\sigma_k$ instead of multiplied: $-0.5/6$.
> - D: Only one standard deviation is used: $-0.5\cdot3$.

## 5. The Gaussian and the Central Limit Theorem (slides 21-23)

### Q19 (slide 21, formula)

![[cs568_lecture_10_l10_p021.jpg]]

The 1D Gaussian pdf is $f_X(x)=\dfrac{1}{\sqrt{2\pi}\,\sigma}\,e^{-(x-\mu)^2/(2\sigma^2)}$. The right-hand curve peaks at about 0.4 at $x=0$. Keep the same $\mu$ and change $\sigma$ to 2. About how high is the new peak?

- A) 0.40
- B) 0.10
- C) 0.20
- D) 0.80

> [!success]- Answer
> **C.** The peak is at $x=\mu$, with height $\dfrac{1}{\sqrt{2\pi}\,\sigma}$. A peak of about 0.4 at $x=0$ means $\mu=0$ and $\sigma=1$ ($1/\sqrt{2\pi}\approx0.399$).
> With $\sigma=2$: $\dfrac{1}{2\sqrt{2\pi}}\approx0.20$. The curve is half as high and twice as wide, so the area stays 1.
> - A: Only the width is changed. Then the area would double and the pdf would no longer integrate to 1.
> - B: The peak is divided by $\sigma^2=4$ instead of $\sigma=2$.
> - D: The peak is multiplied by $\sigma$ instead of divided by it.

### Q20 (slide 21, parameters, beyond the slides)

A speech feature vector has $d=39$ dimensions. One Gaussian $\mathcal N(\boldsymbol\mu,\boldsymbol\Sigma)$ with a full covariance matrix is used: $\boldsymbol\Sigma$ may be any symmetric positive definite $d\times d$ matrix. How many free parameters do $\boldsymbol\mu$ and $\boldsymbol\Sigma$ have together?

- A) 1560
- B) 780
- C) 78
- D) 819

> [!success]- Answer
> **D.** Mean: 39 numbers. Full symmetric covariance: $d(d+1)/2=39\cdot40/2=780$. Total $39+780=819$.
> - A: $d^2+d=1521+39$. The symmetry of $\boldsymbol\Sigma$ is ignored.
> - B: Only the covariance is counted. The 39 numbers of the mean are forgotten.
> - C: $39+39$ is the count for a diagonal covariance. It assumes uncorrelated features, not a full matrix.

### Q21 (slide 22, independence)

$X\sim\mathcal N(0,1)$ and $Y=X^2$. Slide 22 says that uncorrelated Gaussian variables are also independent. The covariance is $\mathrm{Cov}(X,Y)=E[XY]-E[X]\,E[Y]$. Which statement is correct?

- A) They are correlated: $Y$ is fixed by $X$, and dependent variables always have non-zero covariance.
- B) They are independent: $X$ is Gaussian and $\mathrm{Cov}(X,Y)=0$, and the rule needs only one Gaussian.
- C) They are uncorrelated but dependent: the rule needs jointly Gaussian variables, and $Y$ is not Gaussian.
- D) They are independent: $Y$ is Gaussian too, because a function of a Gaussian always stays Gaussian.

> [!success]- Answer
> **C.** $\mathrm{Cov}(X,Y)=E[X^3]-0\cdot E[X^2]=0$, because the pdf of $X$ is symmetric about 0. So they are uncorrelated.
> But $Y$ is completely fixed by $X$, so they are dependent. $Y\ge0$ is not Gaussian, so the rule of slide 22 cannot be used.
> - A: Dependence does not force a non-zero covariance. Covariance measures only the linear relation, and here $E[X^3]=0$.
> - B: The rule needs the two variables to be jointly Gaussian, not only $X$.
> - D: Only linear maps keep the Gaussian shape. $Y=X^2$ is never negative, so it cannot be Gaussian.

### Q22 (slide 22, linear map)

$X_1$ and $X_2$ are independent zero-mean Gaussians with variances 1 and 4. Let $Y_1=X_1+X_2$ and $Y_2=X_1-X_2$. What is $\mathrm{Cov}(Y_1,Y_2)$?

- A) $-3$
- B) $0$
- C) $5$
- D) $-1$

> [!success]- Answer
> **A.** $\mathbf Y=\mathbf A\mathbf X$ with $\mathbf A=\begin{bmatrix}1&1\\1&-1\end{bmatrix}$, so $\boldsymbol\Sigma_Y=\mathbf A\boldsymbol\Sigma_X\mathbf A^{\mathsf T}=\begin{bmatrix}5&-3\\-3&5\end{bmatrix}$.
> $\mathrm{Cov}(Y_1,Y_2)=\mathrm{Var}(X_1)-\mathrm{Var}(X_2)=1-4=-3$. $\mathbf Y$ is jointly Gaussian and correlated, so $Y_1$ and $Y_2$ are not independent.
> - B: This assumes that a linear map of independent variables keeps them uncorrelated. Here that holds only if the two variances are equal.
> - C: $1+4=5$ is $\mathrm{Var}(Y_1)$, a diagonal entry, not the off-diagonal one.
> - D: The standard deviations 1 and 2 are used instead of the variances: $1-2$.

### Q23 (slide 23)

500 experiments are done. In each one, $N$ samples are drawn from the uniform distribution on $[0,1]$ (mean 0.5, variance $\frac1{12}$), and their mean is recorded. A histogram of the 500 means is drawn. By the Central Limit Theorem, what is the standard deviation of this histogram for $N=10$?

- A) about 0.091
- B) about 0.0083
- C) about 0.029
- D) about 0.013

> [!success]- Answer
> **A.** The mean of $N$ samples has variance $\sigma^2/N=\dfrac{1/12}{10}=\dfrac1{120}$. Standard deviation $\sqrt{1/120}\approx0.091$. The centre stays at 0.5.
> - B: $1/120\approx0.0083$ is the variance. The square root is not taken.
> - C: The standard deviation $\sqrt{1/12}\approx0.289$ is divided by $N=10$ instead of $\sqrt{10}$.
> - D: The number of experiments, 500, is used as $N$: $\sqrt{(1/12)/500}\approx0.013$. $N$ is the size of each averaged sample.

## 6. Estimation: least squares (slides 24-31)

### Q24 (slide 24)

An estimator $\theta(X_1,\dots,X_N)$ is computed from iid data drawn from a pdf $p(x\mid\Phi)$ with an unknown parameter $\Phi$. Someone says: "We collect the data once, compute $\theta$, and this number is $\Phi$." Which statement is correct?

- A) The claim holds for iid data: iid samples make the computed estimate exact.
- B) $\theta$ depends on random data: new data give a new value, so in general $\theta\ne\Phi$.
- C) The roles are swapped: $\Phi$ is the random variable, and $\theta$ is a fixed number.
- D) The claim holds for an unbiased estimator: its value on any data set equals $\Phi$.

> [!success]- Answer
> **B.** $\theta$ is a function of random data, so it is itself a random variable. It is a guess for $\Phi$, close to it but in general not equal. Example with the sample mean: $(2,4,6,8)$ gives 5, another draw $(3,4,7,8)$ gives 5.5.
> - A: iid only means the same pdf for every sample and no influence between samples. The data are still random.
> - C: In this view $\Phi$ is a fixed number, and $\theta$ changes from one data set to the next.
> - D: Unbiased means $E[\theta]=\Phi$, an average over many data sets, not equality for each one.

### Q25 (slide 25)

The MMSE estimate is $\hat\Phi_{\mathrm{MMSE}}=\arg\min_\Phi E\big(Y-g(X,\Phi)\big)^2$. $Y$ is the variable to predict, $g(X,\Phi)$ is a predictor with parameters $\Phi$, and $E$ is the expectation. What must be known to evaluate this expression?

- A) The joint pdf $f_{xy}(x,y)$ of $X$ and $Y$, which in practice is usually unknown.
- B) Only a set of training pairs $(x_i,y_i)$: $E$ is the average over these pairs.
- C) Only the pdf of $Y$, because the prediction error is measured on $Y$ alone.
- D) The true value of $\Phi$, because the error is measured at the true parameter.

> [!success]- Answer
> **A.** The expectation averages the squared error over all pairs $(X,Y)$, weighted with the joint pdf $f_{xy}$. This pdf is usually unknown, which leads to the LSE.
> - B: This describes the LSE of the next slide, which replaces $E$ by a sum over training samples.
> - C: The error contains $g(X,\Phi)$, so $X$ enters too. The joint pdf is needed.
> - D: $\arg\min_\Phi$ searches over $\Phi$. The parameter is the result, not an input.

### Q26 (slide 26)

A training set has the pairs $(x,y)$: $(1,\ 2.5)$, $(2,\ 3.5)$, $(3,\ 6.5)$. Two models are compared: $g_1(x)=2x$ and $g_2(x)=2x+0.5$. The error of a sample is $e_i=y_i-g(x_i)$. Which model does the least-squares (LSE) criterion prefer, and why?

- A) $g_2$: its sum of absolute errors is 1, against 1.5 for $g_1$.
- B) $g_1$: its sum of errors is 0.5, closer to 0 than $-1$ for $g_2$.
- C) $g_2$: it fits two of the three points exactly, and $g_1$ fits none.
- D) $g_1$: its sum of squared errors is 0.75, against 1 for $g_2$.

> [!success]- Answer
> **D.** $g_1$: predictions 2, 4, 6, errors $0.5,\ -0.5,\ 0.5$, SSE $=0.75$.
> $g_2$: predictions 2.5, 4.5, 6.5, errors $0,\ -1,\ 0$, SSE $=1$. The smaller SSE wins: $g_1$.
> - A: The square is forgotten. With absolute errors $g_2$ would win, but LSE squares the errors.
> - B: The errors are added with their signs, so they can cancel. The square prevents this. The choice is right, the reason is not.
> - C: Exact hits are not the criterion. The one large error of $g_2$ is squared and costs more.

### Q27 (slide 27)

The training outputs are $y=3,\ 5,\ 10$. The best constant predictor $c_{\mathrm{LSE}}$ minimises $\sum_i(y_i-c)^2$. What is the mean squared error $\frac1n\sum_i(y_i-c_{\mathrm{LSE}})^2$ at this best constant? (The factor is $\frac1n$, with $n=3$.)

- A) 8.67
- B) 9.67
- C) 13.00
- D) 26.00

> [!success]- Answer
> **A.** $c_{\mathrm{LSE}}$ is the mean: $(3+5+10)/3=6$. SSE $=9+1+16=26$. Divided by $n=3$: $\approx8.67$, the variance of $y$ with $\frac1n$.
> - B: The median 5 is used as the constant: SSE $=4+0+25=29$, and $29/3\approx9.67$.
> - C: $26/2$ divides by $n-1$ (the unbiased variance), but the stem asks for $\frac1n$.
> - D: The SSE is not divided by $n$.

### Q28 (slide 28)

$E(X)=1$, $E(Y)=10$, $\mathrm{Var}(X)=4$, $\mathrm{Var}(Y)=16$ and $\mathrm{Cov}(X,Y)=6$. $\hat Y=aX+b$ is the line with the smallest mean squared error $E(Y-aX-b)^2$. What does this line predict for $X=3$?

- A) 10.75
- B) 11.50
- C) 13.00
- D) 16.00

> [!success]- Answer
> **C.** $a=\dfrac{\mathrm{Cov}(X,Y)}{\mathrm{Var}(X)}=\dfrac64=1.5$, $b=E(Y)-a\,E(X)=10-1.5=8.5$. So $\hat Y=1.5\cdot3+8.5=13$.
> - A: $a=\mathrm{Cov}/\mathrm{Var}(Y)=0.375$, the misprint on slide 28 (it is the slope for predicting $X$ from $Y$). Then $b=9.625$ and $\hat Y=10.75$.
> - B: The correlation $\rho_{xy}=6/(2\cdot4)=0.75$ is used as the slope. Then $b=9.25$ and $\hat Y=11.5$.
> - D: The covariance is divided by $\sigma_x=2$ instead of $\mathrm{Var}(X)=4$: $a=3$, $b=7$, $\hat Y=16$.

### Q29 (slide 29)

Linear LSE with $n=50$ training samples and $d=3$ features. The model has an intercept $a_0$ and one weight per feature, written $\hat Y=XA$ as on the slide, and $Y$ holds the 50 outputs. The solution is $A=(X^{\mathsf T}X)^{-1}X^{\mathsf T}Y$. What is the size of the pseudo-inverse $(X^{\mathsf T}X)^{-1}X^{\mathsf T}$?

- A) $4\times50$
- B) $3\times50$
- C) $50\times4$
- D) $4\times4$

> [!success]- Answer
> **A.** $X$ has a first column of ones for $a_0$, so it is $50\times4$. $X^{\mathsf T}X$ is $4\times4$, and $(X^{\mathsf T}X)^{-1}X^{\mathsf T}$ is $4\times50$. It turns the 50 outputs into the 4 parameters.
> - B: The column of ones for the intercept is forgotten.
> - C: This is the size of $X$. The transpose is forgotten.
> - D: This is the size of $X^{\mathsf T}X$. The factor $X^{\mathsf T}$ is forgotten.

### Q30 (slide 30)

Two features are identical. The training samples are $(1,1)$ with output 3 and $(2,2)$ with output 6, with no constant column: $X=\begin{bmatrix}1&1\\2&2\end{bmatrix}$, $Y=\begin{bmatrix}3\\6\end{bmatrix}$, and $X^{\mathsf T}X$ is singular. Ridge regression uses $A=(X^{\mathsf T}X+\alpha I)^{-1}X^{\mathsf T}Y$. What are the two weights for $\alpha=1$?

- A) Both weights are 15.00.
- B) Both weights are 1.364.
- C) Both weights are 2.500.
- D) Both weights are 1.500.

> [!success]- Answer
> **B.** $X^{\mathsf T}X=\begin{bmatrix}5&5\\5&5\end{bmatrix}$, so $X^{\mathsf T}X+I=\begin{bmatrix}6&5\\5&6\end{bmatrix}$ with determinant $36-25=11$. $X^{\mathsf T}Y=\begin{bmatrix}15\\15\end{bmatrix}$.
> $A=\dfrac1{11}\begin{bmatrix}6&-5\\-5&6\end{bmatrix}\begin{bmatrix}15\\15\end{bmatrix}=\begin{bmatrix}15/11\\15/11\end{bmatrix}\approx\begin{bmatrix}1.364\\1.364\end{bmatrix}$.
> - A: The factor $\frac1{11}$ (one over the determinant) of the inverse is forgotten.
> - C: The off-diagonal 5 is ignored: $15/6$, as if the matrix were diagonal.
> - D: The penalty is ignored. $(1.5,1.5)$ fits the data exactly (the limit $\alpha\to0$). With $\alpha=1$ ridge shrinks the weights below it.

## 7. Maximum likelihood estimation (slides 32-33)

### Q31 (slide 32)

Three independent samples are observed. Under the parameter value $\Phi_a$ their densities are $0.2,\ 0.5,\ 0.1$. Under $\Phi_b$ they are $0.3,\ 0.2,\ 0.2$. Which value does maximum likelihood (MLE) prefer, and why?

- A) $\Phi_a$: the sum of its densities is 0.8, against 0.7 for $\Phi_b$.
- B) $\Phi_b$: its smallest density, 0.2, is larger than the 0.1 of $\Phi_a$.
- C) $\Phi_a$: its log-likelihood is larger in size, 4.61 against 4.42.
- D) $\Phi_b$: its likelihood is 0.012, against 0.010 for $\Phi_a$.

> [!success]- Answer
> **D.** For independent samples the likelihood is the product: $0.2\cdot0.5\cdot0.1=0.010$ and $0.3\cdot0.2\cdot0.2=0.012$. The log-likelihoods $-4.605$ and $-4.423$ give the same order, because the log is increasing.
> - A: The densities are added instead of multiplied.
> - B: Only the worst sample is compared. The choice is right, but MLE uses all samples through the product.
> - C: The sign of the log is ignored. $-4.42>-4.61$: the larger log-likelihood is the one closer to zero.

### Q32 (slide 33)

Five samples $x=3,\ 7,\ 8,\ 4,\ 8$ come from a Gaussian. The ML estimates are $\mu_{\mathrm{MLE}}=\frac1n\sum_kx_k$ and $\sigma^2_{\mathrm{MLE}}=\frac1n\sum_k(x_k-\mu_{\mathrm{MLE}})^2$, with the factor $\frac1n$ and $n=5$. What is $\sigma^2_{\mathrm{MLE}}$?

- A) 5.50
- B) 4.40
- C) 2.10
- D) 5.40

> [!success]- Answer
> **B.** $\mu_{\mathrm{MLE}}=30/5=6$. Deviations: $-3,\ 1,\ 2,\ -2,\ 2$. Squares: $9,\ 1,\ 4,\ 4,\ 4$, sum 22. $\sigma^2_{\mathrm{MLE}}=22/5=4.4$.
> - A: $22/4$ divides by $n-1$: the unbiased variance, not the MLE with $\frac1n$.
> - C: $\sqrt{4.4}\approx2.10$ is the standard deviation.
> - D: The deviations are taken from the median 7: $16+0+1+9+1=27$, and $27/5=5.4$.

## 8. Bayesian (MAP) estimation (slides 34-35)

### Q33 (slide 34)

Bayes rule for a parameter is $p(\Phi\mid\mathbf x)=p(\mathbf x\mid\Phi)\,p(\Phi)/p(\mathbf x)$. Someone says: "MLE and MAP differ only in the optimiser. Both treat $\Phi$ as a fixed unknown number." Which statement is correct?

- A) Both treat $\Phi$ as fixed. MAP also divides by $p(\mathbf x)$, and this moves the maximum.
- B) MAP treats $\Phi$ as random with prior $p(\Phi)$ and maximises $p(\mathbf x\mid\Phi)\,p(\Phi)$.
- C) MAP maximises the prior $p(\Phi)$ alone, so the data do not enter its estimate.
- D) MLE treats $\Phi$ as random with a prior, and MAP treats it as fixed but unknown.

> [!success]- Answer
> **B.** MLE treats $\Phi$ as fixed but unknown and maximises $p(\mathbf x\mid\Phi)$. Bayesian (MAP) estimation treats $\Phi$ as a random variable with its own pdf $p(\Phi)$ and maximises the posterior, which is proportional to $p(\mathbf x\mid\Phi)\,p(\Phi)$.
> - A: $p(\mathbf x)$ does not contain $\Phi$. Dividing by it scales the curve but does not move its maximum. And MAP treats $\Phi$ as random.
> - C: MAP multiplies the prior by the likelihood, so the data enter.
> - D: The roles are swapped.

### Q34 (slide 35)

A Gaussian mean is estimated. The data variance is known, $\sigma^2=4$, and the prior is $\mu\sim\mathcal N(\mu_0,\sigma_0^2)$ with $\mu_0=0$, $\sigma_0^2=1$. The MAP estimate is $\hat\mu_{\mathrm{MAP}}=\dfrac{n\sigma_0^2}{n\sigma_0^2+\sigma^2}\,\bar x+\dfrac{\sigma^2}{n\sigma_0^2+\sigma^2}\,\mu_0$. With $n=16$ samples and sample mean $\bar x=5$, what is $\hat\mu_{\mathrm{MAP}}$?

- A) 1.00
- B) 4.44
- C) 5.00
- D) 4.00

> [!success]- Answer
> **D.** Weight of the data: $\dfrac{16\cdot1}{16\cdot1+4}=0.8$. $\hat\mu_{\mathrm{MAP}}=0.8\cdot5+0.2\cdot0=4.0$. The data already weigh more than the prior, but MAP is not yet the MLE $\bar x=5$.
> - A: The two weights are swapped: $\frac4{20}\cdot5=1.0$.
> - B: $\sigma=2$ is used in place of $\sigma^2=4$: $\frac{16}{18}\cdot5\approx4.44$.
> - C: The prior is ignored, which gives the MLE $\bar x$. MAP reaches it only for a very wide prior or very large $n$.
