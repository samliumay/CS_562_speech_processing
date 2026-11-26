---
course: CS 568 Speech Processing
lecture: 10
title: Probability, statistics and estimation theory
tags: [cs568, lecture]
slides: lectures/lecture_10/l10.pdf
full_notes: lecture_notes/lecture_10/CS568_Lecture_10_Probability_Estimation.pdf
---
# CS 568 · Lecture 10 · Probability, statistics and estimation theory

> [!summary] In short
> - Probability: three axioms, conditional probability, total probability.
> - Bayes theorem: posterior $\propto$ likelihood $\times$ prior. A recogniser picks the class with the largest product.
> - Random variables: cdf, pdf (a density, not a probability), mean, variance, covariance matrix.
> - Gaussian $\mathcal N(\boldsymbol\mu,\boldsymbol\Sigma)$: fixed by mean and covariance. Central Limit Theorem: sample means become Gaussian.
> - Three estimators: least squares (LSE), maximum likelihood (MLE), Bayesian (MAP).

## 1. Probability basics (slides 2-6)
![[cs568_lecture_10_l10_p002.jpg]]
![[cs568_lecture_10_l10_p003.jpg]]
![[cs568_lecture_10_l10_p004.jpg]]
![[cs568_lecture_10_l10_p005.jpg]]
![[cs568_lecture_10_l10_p006.jpg]]

**Notes.** The sample space $S$ is the set of all outcomes. An event is a set of outcomes. A probability law assigns a number to each event. Axioms: $P[A]\ge0$; $P[S]=1$; if $A_i\cap A_j=\emptyset$ then $P[A_i\cup A_j]=P[A_i]+P[A_j]$.

Consequences: $P[A^C]=1-P[A]$, $P[\emptyset]=0$, and for overlapping events $P[A_1\cup A_2]=P[A_1]+P[A_2]-P[A_1\cap A_2]$.

Conditional probability (knowing $B$ makes $B$ the new sample space):

$$P[A\mid B]=\frac{P[A\cap B]}{P[B]},\qquad P[B]>0$$

Total probability, for a partition $B_1,\dots,B_N$ of $S$:

$$P[A]=\sum_{k=1}^{N}P[A\mid B_k]\,P[B_k]$$

> [!example] Three-card warm-up (slide 3)
> Cards: red/red, blue/blue, red/blue. A red side is shown. There are three red sides, equally likely, and two of them belong to the red/red card. So $P[\text{hidden side is red}]=\frac23$, not $\frac12$. The bet on "same colour" wins two times out of three.

> [!warning] Watch out
> Mutually exclusive ($A\cap B=\emptyset$) is not the same as independent ($P[A\cap B]=P[A]P[B]$). Also $P[A\mid B]\ne P[B\mid A]$ in general.

> [!question]- Check (slide 2): In the figure the sample space is cut into four regions $A_1,\dots,A_4$ and the bar chart gives one probability per region. Which axioms force the four bars to sum to 1? If $P[A_1]=0.25$, $P[A_2]=0.35$, $P[A_3]=0.30$, find $P[A_4]$.
> The four regions do not overlap and together they fill $S$.
> Axiom III (used three times) gives $P[A_1\cup A_2\cup A_3\cup A_4]=P[A_1]+P[A_2]+P[A_3]+P[A_4]$.
> Axiom II gives $P[S]=1$, so the sum of the bars is 1.
> $P[A_4]=1-(0.25+0.35+0.30)=1-0.90=0.10$. Axiom I holds because $0.10\ge0$. These numbers keep the order of the bars in the picture: $A_2$ is the tallest, then $A_3$, then $A_1$, and $A_4$ is the shortest.

> [!question]- Check (slide 3): The slide says "the card has to be either A or C". That is true. Which extra assumption makes the "we end up even" claim wrong? Find $P[\text{card A}\mid\text{red shown}]$ with Bayes rule and the average gain per game of the person who bets on "same colour".
> The wrong assumption is that A and C are still equally likely after a red side is seen. Card A (red/red) shows red always, card C (red/blue) shows red only half of the time.
> $P[\text{red}\mid A]=1$, $P[\text{red}\mid B]=0$, $P[\text{red}\mid C]=\frac12$, and each card has prior $\frac13$.
> $P[\text{red}]=\frac13\cdot1+\frac13\cdot0+\frac13\cdot\frac12=\frac12$.
> $P[A\mid\text{red}]=\dfrac{1\cdot\frac13}{\frac12}=\frac23$.
> The "same colour" bet wins 1 dollar with probability $\frac23$ and loses 1 dollar with probability $\frac13$: average gain $\frac23-\frac13=\frac13$ dollar per game. The game is not fair.

> [!question]- Check (slide 4): Find the mistake: "$P[A]=0.6$, $P[B]=0.7$, and $A$ and $B$ are mutually exclusive."
> If they were mutually exclusive, the slide's sum rule would give $P[A\cup B]=0.6+0.7=1.3$. This breaks $P[\cdot]\le1$.
> Use $P[A\cup B]=P[A]+P[B]-P[A\cap B]$ with $P[A\cup B]\le1$:
> $P[A\cap B]=0.6+0.7-P[A\cup B]\ge1.3-1=0.3$.
> So the two events must overlap, with probability at least 0.3.

> [!question]- Check (slide 5): $P[A]=0.5$, $P[B]=0.2$, $P[A\cap B]=0.15$. Find $P[A\mid B]$ and $P[B\mid A]$. In the right-hand picture of the slide, what happened to the square $S$ and to the circle $A$, and what does the division by $P[B]$ do?
> $P[A\mid B]=\dfrac{P[A\cap B]}{P[B]}=\dfrac{0.15}{0.2}=0.75$.
> $P[B\mid A]=\dfrac{P[A\cap B]}{P[A]}=\dfrac{0.15}{0.5}=0.30$. The two are different: the numerator is the same, the denominator is not.
> In the picture the square and the part of $A$ outside $B$ are dashed (no longer possible). The circle $B$ is the new sample space, and only the part $A\cap B$ of $A$ survives.
> Dividing by $P[B]$ re-normalises: the probabilities inside $B$ must again sum to 1. Check: $P[B\mid B]=0.2/0.2=1$.

> [!question]- Check (slide 6): Three speakers $B_1,B_2,B_3$ made 50%, 30% and 20% of the recordings in a database. A recording is noisy (event $A$) with probability 0.02, 0.05 and 0.10 for the three speakers. Find $P[A]$. Why must the $B_k$ in the figure not overlap?
> $P[A]=\sum_k P[A\mid B_k]P[B_k]=0.02\cdot0.5+0.05\cdot0.3+0.10\cdot0.2$
> $=0.010+0.015+0.020=0.045$.
> In the figure the red ellipse $A$ is cut into the pieces $A\cap B_k$. The pieces can be added (Axiom III) only if they do not overlap, so the $B_k$ must be mutually exclusive. They must also cover all of $S$, or a part of $A$ would be missed.

## 2. Bayes theorem (slides 7-12)
![[cs568_lecture_10_l10_p007.jpg]]
![[cs568_lecture_10_l10_p008.jpg]]
![[cs568_lecture_10_l10_p009.jpg]]
![[cs568_lecture_10_l10_p010.jpg]]
![[cs568_lecture_10_l10_p011.jpg]]
![[cs568_lecture_10_l10_p012.jpg]]

**Notes.** For a partition $\{B_j\}$ and an observed event $A$:

$$P[B_j\mid A]=\frac{P[A\mid B_j]\,P[B_j]}{\sum_{k=1}^{N}P[A\mid B_k]\,P[B_k]}$$

For pattern recognition, with class $\omega_j$ and feature vector $x$:

$$P[\omega_j\mid x]=\frac{P[x\mid\omega_j]\,P[\omega_j]}{P[x]}$$

$P[\omega_j]$ is the **prior**, $P[x\mid\omega_j]$ the **likelihood**, $P[\omega_j\mid x]$ the **posterior**, $P[x]$ a normalisation constant. Bayes decision rule: choose the class with the highest posterior. $P[x]$ is the same for all classes, so compare $P[x\mid\omega_j]P[\omega_j]$.

Medical test terms: **sensitivity** $=P(\text{POS}\mid\text{COND})$, **specificity** $=P(\text{NEG}\mid\neg\text{COND})$.

> [!example] Slides 9-12: positive test
> 10,000 people, 1% prevalence, 90% sensitivity, 98% specificity. Sick: 100, of which 90 test positive. Healthy: 9,900, of which $9900\times0.02=198$ test positive.
> $$P[\text{cond}\mid +]=\frac{0.90\times0.01}{0.90\times0.01+0.02\times0.99}=\frac{0.009}{0.0288}=0.3125$$
> Same as $90/288$.

> [!warning] Base-rate neglect
> A positive result from a "90%/98%" test gives only about 31%, because the prior is 1%. The false positives from the large healthy group outnumber the true positives.

> [!question]- Check (slide 7): The Bayes formula on the slide has two equal signs. Which earlier result justifies each one? Then use it: speakers $B_1,B_2,B_3$ have $P[B_k]=0.5,\ 0.3,\ 0.2$ and a recording is noisy ($A$) with $P[A\mid B_k]=0.02,\ 0.05,\ 0.10$. A recording is noisy. Find $P[B_3\mid A]$.
> First equal sign: the definition of conditional probability, $P[B_j\mid A]=P[A\cap B_j]/P[A]$.
> Second equal sign: the numerator is $P[A\cap B_j]=P[A\mid B_j]P[B_j]$ (the same definition, conditioned the other way), and the denominator is the theorem of total probability.
> Denominator: $0.02\cdot0.5+0.05\cdot0.3+0.10\cdot0.2=0.045$.
> $P[B_3\mid A]=\dfrac{0.10\cdot0.2}{0.045}=\dfrac{0.020}{0.045}\approx0.444$.
> $B_3$ made the fewest recordings (prior 0.2), but it has the highest posterior, because its recordings are noisy most often. For comparison $P[B_1\mid A]=0.010/0.045\approx0.222$.

> [!question]- Check (slide 8): Two phoneme classes have priors $P[\omega_1]=0.7$, $P[\omega_2]=0.3$. For an observed MFCC vector $x$ the likelihoods are $P[x\mid\omega_1]=0.2$ and $P[x\mid\omega_2]=0.4$. Name the four terms of the formula on the slide, give the Bayes decision, and say why $P[x]$ is not needed for it.
> Prior: $P[\omega_j]$, what we believe before we see $x$. Likelihood: $P[x\mid\omega_j]$, how well class $j$ explains $x$. Posterior: $P[\omega_j\mid x]$, the belief after we see $x$. $P[x]$: the normalisation constant.
> Products: $0.2\cdot0.7=0.14$ for $\omega_1$ and $0.4\cdot0.3=0.12$ for $\omega_2$. Choose $\omega_1$.
> The likelihood alone prefers $\omega_2$ ($0.4>0.2$). The prior turns the decision around.
> $P[x]=0.14+0.12=0.26$ is the same number for both classes. Dividing by it does not change which product is larger. It only scales the posteriors so that they sum to 1: $0.14/0.26\approx0.538$ and $0.12/0.26\approx0.462$.

> [!question]- Check (slide 9): In the clinical problem, which numbers on the slide play the roles of the prior and the likelihood in Bayes rule? What are the false-positive rate and the false-negative rate of the test? Find the mistake: "Specificity 98% means that 98% of the people with a negative result are healthy."
> Prior: the prevalence, $P[\text{COND}]=0.01$. Likelihood of a positive result for a sick person: the sensitivity, $P[\text{POS}\mid\text{COND}]=0.90$.
> False-positive rate: $P[\text{POS}\mid\neg\text{COND}]=1-0.98=0.02$. False-negative rate: $P[\text{NEG}\mid\text{COND}]=1-0.90=0.10$.
> The mistake: the statement swaps the condition. Specificity is $P[\text{NEG}\mid\neg\text{COND}]$: of the healthy people, 98% test negative. "Of the people with a negative result, how many are healthy" is $P[\neg\text{COND}\mid\text{NEG}]$, a posterior that also depends on the prevalence.

> [!question]- Check (slides 10-11): Use the filled table on slide 11. What is $P[\text{POS}]$? What fraction of the positive results are false? What is $P[\text{COND}\mid\text{NEG}]$?
> Cells: true positives $100\times0.90=90$, false negatives $100\times0.10=10$, false positives $9900\times0.02=198$, true negatives $9900\times0.98=9702$.
> $P[\text{POS}]=288/10000=0.0288$ (column total over grand total).
> False among the positives: $198/288=0.6875$, about 69%.
> $P[\text{COND}\mid\text{NEG}]=10/9712\approx0.00103$, about 0.1%. A negative result is very reliable, a positive result is not.

> [!question]- Check (slide 12): Keep the prevalence at 1%. Which change improves $P[\text{cond}\mid+]$ more: sensitivity from 90% to 99%, or specificity from 98% to 99.9%? Say why, using the denominator on the slide.
> Better sensitivity: $\dfrac{0.99\times0.01}{0.99\times0.01+0.02\times0.99}=\dfrac{0.0099}{0.0297}\approx0.333$.
> Better specificity: $\dfrac{0.90\times0.01}{0.90\times0.01+0.001\times0.99}=\dfrac{0.009}{0.00999}\approx0.901$.
> The specificity helps far more. The denominator has two terms: true positives $P[+\mid\text{cond}]P[\text{cond}]$ and false positives $P[+\mid\neg\text{cond}]P[\neg\text{cond}]$. The second term is multiplied by the large number $P[\neg\text{cond}]=0.99$, so it dominates. Only a smaller false-positive rate $1-\text{specificity}$ makes it small.

## 3. Random variables, cdf and pdf (slides 13-17)
![[cs568_lecture_10_l10_p013.jpg]]
![[cs568_lecture_10_l10_p014.jpg]]
![[cs568_lecture_10_l10_p015.jpg]]
![[cs568_lecture_10_l10_p016.jpg]]
![[cs568_lecture_10_l10_p017.jpg]]

**Notes.** A random variable $X$ is a fixed function that assigns a real number $X(\xi)$ to each outcome $\xi$. It can be discrete or continuous.

- **cdf**: $F_X(x)=P[X\le x]$. It goes from 0 to 1, never decreases, and is continuous from the right.
- **pdf** (continuous): $f_X(x)=\dfrac{dF_X(x)}{dx}$, with $f_X\ge0$, $\int f_X=1$ and $P[a<X<b]=\int_a^b f_X(x)\,dx$.
- **pmf** (discrete): the jump of the cdf, a true probability. Fair die: $1/6$ per face.

$$E[X]=\mu=\int x\,f_X(x)\,dx,\qquad \mathrm{Var}[X]=\sigma^2=E\big[(X-\mu)^2\big]=E[X^2]-(E[X])^2$$

The standard deviation is $\sigma$. The $N$-th moment is $E[X^N]$.

> [!warning] A density is not a probability (slide 16)
> The pdf value at 124.876 lb is not the probability of weighing exactly that. Any single value of a continuous variable has probability zero. Integrate over an interval: $P\approx f_X(x_0)\,\Delta$. A pdf can be larger than 1. The weight curve on the slide is only a sketch: heights like 0.62 cannot be per-lb densities.

> [!warning] Slide error (slide 15)
> The slide lists the property $f_X(x)>0$. The correct property is $f_X(x)\ge0$: a pdf can be zero, for example a uniform pdf outside its interval.
> Two small notation slips with the same cause: the axis label of both plots on slide 14 is "P(X<x)", and the last line of slide 15 writes $\{X<x\}$. The definition on slide 14 is $F_X(x)=P[X\le x]$. For a continuous variable (the weight) the two are equal. For the die they differ exactly at $x=1,\dots,6$, and the plotted die cdf (filled dots at the left end of each step) follows $P[X\le x]$.

> [!info] The conditional pdf on slide 15 (beyond the slides)
> The last line of slide 15 repeats the definitions for the case that an event $A$ is known to have happened. $F_X(x\mid A)=P[\{X\le x\}\cap A]/P[A]$ (the slide prints $X<x$, see the warning above) is the cdf inside the new sample space $A$ (the conditional probability formula of slide 5). Its derivative $f_X(x\mid A)$ is the conditional pdf. The class-conditional likelihood $p(x\mid\omega_j)$ of the Bayes classifier is such a pdf: the density of the features when the class is $\omega_j$.

> [!question]- Check (slide 13): Take the slide's rule "count the number of heads in three coin tosses" with a fair coin. How many outcomes $\xi$ does the sample space have, which values can $X$ take, and with which probabilities? Where is the randomness: in the function $X$ or in $\xi$?
> $S$ has $2^3=8$ equally likely outcomes (HHH, HHT, ..., TTT).
> $X$ takes the values 0, 1, 2, 3. Count the outcomes per value: 1, 3, 3, 1.
> $P[X=0]=\frac18$, $P[X=1]=\frac38$, $P[X=2]=\frac38$, $P[X=3]=\frac18$. Sum: 1.
> The function is fixed: the same outcome always gives the same number (HHT always gives 2). The randomness is in which outcome $\xi$ the experiment produces.

> [!question]- Check (slide 14): Use the die cdf on the slide. Read off $F_X(3.5)$ and $F_X(4)$, and compute $P[2<X\le5]$ from the cdf. Why does the die cdf have steps but the weight cdf is a smooth curve?
> $F_X(3.5)=3/6$: the step between 3 and 4 is at height 3/6.
> $F_X(4)=4/6$: the filled dot is at the left end of each step, so the value at the jump is the upper one (the cdf is continuous from the right, the last property on the slide).
> $P[2<X\le5]=F_X(5)-F_X(2)=\frac56-\frac26=\frac36=0.5$ (the faces 3, 4, 5).
> A discrete variable has probability on single values, so the cdf jumps there by that probability ($1/6$ per face). For a continuous variable no single value has probability, so the cdf rises without jumps.

> [!question]- Check (slide 15): $X$ is uniform on $[0,\ 0.25]$. What is the value of its pdf inside the interval? Is a value above 1 allowed? Find $P[0.10<X<0.15]$.
> The area under the pdf must be 1: height $\times\,0.25=1$, so $f_X(x)=4$ for $0\le x\le0.25$ and 0 outside.
> A value of 4 is allowed. A pdf is a density (probability per unit of $x$). Only its integral must stay at or below 1.
> $P[0.10<X<0.15]=\int_{0.10}^{0.15}4\,dx=4\times0.05=0.20$.

> [!question]- Check (slide 16): A realistic weight pdf has the value $0.008$ per lb at 150 lb. What is the probability of a weight within 150 lb plus or minus 2 lb (take the pdf as constant there)? What is the probability of exactly 150 lb? Why does the die pmf on the slide not need this step?
> Interval width $\Delta=4$ lb. $P\approx f_X(150)\,\Delta=0.008\times4=0.032$.
> Exactly 150 lb: the interval has width 0, so the probability is 0.
> The unit shows the difference: the pdf is in "per lb", and it becomes a probability only after it is multiplied by a width in lb.
> A pmf value is already a probability: $P[X=3]=1/6$ for the die. There is nothing to integrate.

> [!question]- Check (slide 17): $X$ is uniform on $[0,6]$, so $f_X(x)=\frac16$ there. Compute $E[X]$, $\mathrm{var}[X]$ and $\mathrm{std}[X]$ with the integrals on the slide. The slide says mean and variance characterise a r.v. only PARTIALLY: what does that mean?
> $E[X]=\int_0^6 x\,\frac16\,dx=\frac16\cdot\frac{6^2}{2}=3$.
> $E[X^2]=\int_0^6 x^2\,\frac16\,dx=\frac16\cdot\frac{6^3}{3}=12$ (the second moment).
> $\mathrm{var}[X]=E[X^2]-\mu^2=12-9=3$, and $\mathrm{std}[X]=\sqrt3\approx1.73$.
> Partially: many different pdfs have the same mean and variance. A Gaussian with $\mu=3$, $\sigma^2=3$ has the same two numbers as this uniform pdf but a different shape. Only the cdf or the pdf tells everything.

## 4. Random vectors and covariance (slides 18-20)
![[cs568_lecture_10_l10_p018.jpg]]
![[cs568_lecture_10_l10_p019.jpg]]
![[cs568_lecture_10_l10_p020.jpg]]

**Notes.** A random vector $\mathbf X=[X_1,\dots,X_N]^{\mathsf T}$ has a joint cdf and a joint pdf. A **marginal** pdf integrates out the dimensions of no interest: $f_{X_1}(x_1)=\int f_{X_1X_2}(x_1,x_2)\,dx_2$.

$$E[\mathbf X]=\boldsymbol\mu,\qquad \mathrm{Cov}[\mathbf X]=\boldsymbol\Sigma=E\big[(\mathbf X-\boldsymbol\mu)(\mathbf X-\boldsymbol\mu)^{\mathsf T}\big]$$

Entry $c_{ik}$ of $\boldsymbol\Sigma$: positive if $x_i$ and $x_k$ increase together, negative if one decreases when the other increases, zero if uncorrelated. Diagonal: $c_{ii}=\sigma_i^2$. Off-diagonal: $c_{ik}=\rho_{ik}\,\sigma_i\sigma_k$ with correlation coefficient $\rho_{ik}\in[-1,1]$.

Speech features with a diagonal covariance need far fewer parameters. This is one reason decorrelated features such as MFCCs are popular.

> [!warning] Slide error (slide 20)
> The slide writes $\lvert c_{ik}\rvert\le\sigma_1\sigma_k$. The index 1 is a typo for $i$: the bound is $\lvert c_{ik}\rvert\le\sigma_i\sigma_k$. The same line of the slide goes on with "where $\sigma_i$ is the standard deviation of $x_i$", and the panels below use $\sigma_i\sigma_k$. The bound is the same statement as $\lvert\rho_{ik}\rvert\le1$.

> [!question]- Check (slide 18): A 2D random vector has the joint pdf $f_{X_1X_2}(x_1,x_2)=x_1+x_2$ on the unit square $0\le x_1,x_2\le1$ (zero outside). Find the marginal pdf of $x_1$ and check that it is a valid pdf. What does "integrating out" $x_2$ mean in words?
> $f_{X_1}(x_1)=\int_0^1(x_1+x_2)\,dx_2=x_1+\frac12$ for $0\le x_1\le1$.
> Check: $\int_0^1\big(x_1+\frac12\big)dx_1=\frac12+\frac12=1$, and it is never negative.
> In words: for a fixed $x_1$ we add up the density over all values that $x_2$ can take. We keep the behaviour of $x_1$ and no longer care about $x_2$.

> [!question]- Check (slide 19): For a random vector with $N=3$ components, what is the size of $\boldsymbol\Sigma$? What do the entries $(2,2)$ and $(2,3)$ mean? The slide writes $c_{1N}$ in both corners of the matrix: why is that correct? How many different numbers must be stored?
> $\boldsymbol\Sigma$ is $3\times3$.
> Entry $(2,2)$ is $E[(x_2-\mu_2)^2]=\sigma_2^2$, the variance of $x_2$. Entry $(2,3)$ is $E[(x_2-\mu_2)(x_3-\mu_3)]=c_{23}$, the covariance of $x_2$ and $x_3$.
> The product $(x_1-\mu_1)(x_N-\mu_N)$ does not depend on the order of its factors, so $c_{N1}=c_{1N}$. The matrix is symmetric.
> Different numbers: 3 on the diagonal plus 3 above it, $N(N+1)/2=3\cdot4/2=6$.

> [!question]- Check (slide 20): Two features have $\sigma_i=2$, $\sigma_k=3$ and $\rho_{ik}=-0.5$. Compute $c_{ik}$ and write $\boldsymbol\Sigma$. Which of the five panels on the slide shows this case? How would the cloud look for $\rho_{ik}=+0.9$?
> $c_{ik}=\rho_{ik}\sigma_i\sigma_k=-0.5\cdot2\cdot3=-3$.
> $$\boldsymbol\Sigma=\begin{bmatrix}4&-3\\-3&9\end{bmatrix}$$
> It is the second panel from the left ($\rho_{ik}=-\frac12$): an ellipse that falls from the upper left to the lower right. When $x_i$ increases, $x_k$ tends to decrease.
> For $\rho_{ik}=+0.9$ the ellipse rises to the upper right and is thinner than in the fourth panel ($+\frac12$), but it is not yet the straight line of the last panel ($+1$).
> The sign of $\rho$ gives the direction of the tilt. The size of $\lvert\rho\rvert$ says how close the points are to a line.

## 5. The Gaussian and the Central Limit Theorem (slides 21-23)
![[cs568_lecture_10_l10_p021.jpg]]
![[cs568_lecture_10_l10_p022.jpg]]
![[cs568_lecture_10_l10_p023.jpg]]

**Notes.** Multivariate Normal in $n$ dimensions, and the 1D case:

$$f_{\mathbf X}(\mathbf x)=\frac{1}{(2\pi)^{n/2}|\boldsymbol\Sigma|^{1/2}}\exp\Big(-\tfrac12(\mathbf x-\boldsymbol\mu)^{\mathsf T}\boldsymbol\Sigma^{-1}(\mathbf x-\boldsymbol\mu)\Big),\qquad f_X(x)=\frac{1}{\sqrt{2\pi}\,\sigma}\,e^{-\frac{(x-\mu)^2}{2\sigma^2}}$$

Why Gaussians are popular: $(\boldsymbol\mu,\boldsymbol\Sigma)$ fix the distribution; uncorrelated Gaussian variables are also independent (then $\boldsymbol\Sigma$ is diagonal); marginals and conditionals are Gaussian; a linear map $\mathbf Y=\mathbf A\mathbf X$ stays Gaussian, $\mathbf Y\sim\mathcal N(\mathbf A\boldsymbol\mu,\ \mathbf A\boldsymbol\Sigma\mathbf A^{\mathsf T})$.

**Central Limit Theorem.** For any distribution with mean $\mu$ and variance $\sigma^2$, the distribution of the mean of $N$ samples approaches $\mathcal N(\mu,\sigma^2/N)$ as $N$ grows. Slide 23: 500 experiments with a uniform distribution, $N=1,4,7,10$.

> [!warning] Watch out
> - Uncorrelated implies independent only for jointly Gaussian variables.
> - In the CLT, $N$ is the size of each averaged sample, not the number of experiments (500).

> [!info] The terms of the Gaussian formula (beyond the slides)
> Slide 21 gives the formula without naming its parts.
> - $n$: the dimension of $\mathbf x$. $\boldsymbol\mu$: the mean vector, the position of the peak.
> - $\boldsymbol\Sigma$: the covariance matrix (kovaryans matrisi), the size and orientation of the bell.
> - $(\mathbf x-\boldsymbol\mu)^{\mathsf T}\boldsymbol\Sigma^{-1}(\mathbf x-\boldsymbol\mu)$: the squared Mahalanobis distance from $\mathbf x$ to the mean. It is a distance measured in units of the spread. In 1D it is $(x-\mu)^2/\sigma^2$. All points with the same value have the same density, so the contours are ellipses.
> - $\exp(-\frac12\cdot)$: turns a large distance into a small density.
> - $\dfrac{1}{(2\pi)^{n/2}\lvert\boldsymbol\Sigma\rvert^{1/2}}$: the normalisation constant. $\lvert\boldsymbol\Sigma\rvert$ is the determinant. It makes the total volume under the pdf equal to 1. A wider bell (larger determinant) is lower.
>
> Three kinds of covariance matrix and their contours, in $d$ dimensions:
>
> | Type | $\boldsymbol\Sigma$ | Contours | Parameters of $\boldsymbol\Sigma$ |
> |---|---|---|---|
> | Full | any symmetric positive definite matrix | ellipses with any tilt | $d(d+1)/2$ |
> | Diagonal | zeros off the diagonal | ellipses along the coordinate axes | $d$ |
> | Spherical | $\sigma^2\mathbf I$ | circles (spheres) | 1 |
>
> The mean always adds $d$ parameters. The surface plot on slide 21 looks nearly round: about the same spread along $x$ and $y$ and no clear tilt. This is close to the spherical case. The slide gives no values for $\boldsymbol\Sigma$, so this is only a visual impression.

> [!question]- Check (slide 21, formula): The 1D plot on the slide peaks at about 0.4 at $x=0$. Which $\mu$ and $\sigma$ give this curve? What are the peak height and the shape if $\sigma$ is changed to 2? Which term of the multivariate formula plays the role of $\sqrt{2\pi}\,\sigma$?
> The peak is at $x=\mu$, so $\mu=0$. The peak height is $\dfrac{1}{\sqrt{2\pi}\,\sigma}$. With $\sigma=1$ this is $\dfrac{1}{\sqrt{2\pi}}\approx0.399$, about 0.4. So the plot is $\mathcal N(0,1)$.
> With $\sigma=2$: peak $\dfrac{1}{2\sqrt{2\pi}}\approx0.199$. The curve is half as high and twice as wide. The area stays 1.
> In $n$ dimensions the normalisation is $(2\pi)^{n/2}\lvert\boldsymbol\Sigma\rvert^{1/2}$. For $n=1$, $\boldsymbol\Sigma=\sigma^2$ and it becomes $\sqrt{2\pi}\,\sigma$.

> [!question]- Check (slide 21, parameters, beyond the slides): A speech feature vector has $d=39$ dimensions. How many parameters does one Gaussian $\mathcal N(\boldsymbol\mu,\boldsymbol\Sigma)$ need with a full, a diagonal and a spherical covariance? What does each choice assume?
> Mean: 39 numbers in all three cases.
> Full: $d(d+1)/2=39\cdot40/2=780$ covariance numbers, total $39+780=819$. Any pair of features may be correlated.
> Diagonal: 39 variances, total $39+39=78$. The features are taken as uncorrelated, each with its own variance.
> Spherical: 1 variance, total $39+1=40$. Uncorrelated and all with the same variance.
> Fewer parameters need less training data. This is why decorrelated features (MFCCs) with diagonal covariances are common.

> [!question]- Check (slide 22, independence): Find the mistake: "$X\sim\mathcal N(0,1)$ and $Y=X^2$ are uncorrelated, so by slide 22 they are independent."
> They are uncorrelated: $\mathrm{Cov}(X,Y)=E[X\cdot X^2]-E[X]E[X^2]=E[X^3]-0=0$, because the pdf is symmetric about 0.
> They are not independent: $Y$ is completely fixed by $X$.
> The slide's rule holds only when the variables are jointly Gaussian. $Y=X^2$ is never negative, so it is not Gaussian, and the rule cannot be used.
> Independent always implies uncorrelated. The other direction needs the Gaussian assumption.

> [!question]- Check (slide 22, linear map): $X_1$ and $X_2$ are independent zero-mean Gaussians with variances 1 and 4. Let $Y_1=X_1+X_2$ and $Y_2=X_1-X_2$. Is $\mathbf Y$ Gaussian? Find its covariance matrix. Are $Y_1$ and $Y_2$ independent?
> $\mathbf Y=\mathbf A\mathbf X$ with $\mathbf A=\begin{bmatrix}1&1\\1&-1\end{bmatrix}$, which is invertible ($\lvert\mathbf A\rvert=-2$). By the slide, $\mathbf Y$ is jointly Gaussian.
> $\boldsymbol\Sigma_X=\begin{bmatrix}1&0\\0&4\end{bmatrix}$ and $\boldsymbol\Sigma_Y=\mathbf A\boldsymbol\Sigma_X\mathbf A^{\mathsf T}$:
> $$\boldsymbol\Sigma_Y=\begin{bmatrix}1+4&1-4\\1-4&1+4\end{bmatrix}=\begin{bmatrix}5&-3\\-3&5\end{bmatrix}$$
> $\rho=-3/\sqrt{5\cdot5}=-0.6$. The off-diagonal entry is not zero, so $Y_1$ and $Y_2$ are correlated and not independent.
> A linear map keeps the Gaussian shape but can create or remove correlation.

> [!question]- Check (slide 23): The example uses a uniform distribution. Take it on $[0,1]$: mean $0.5$, variance $\frac1{12}$. What does the CLT give for the mean and standard deviation of the histogram for $N=10$? How does this show in the four histograms on the slide? What would change with 5000 experiments instead of 500?
> Mean of the sample mean: $\mu=0.5$ for every $N$. All four histograms are centred at the same place.
> $N=1$: std $=\sqrt{1/12}\approx0.289$. $N=10$: variance $\dfrac{1/12}{10}=\dfrac1{120}\approx0.0083$, std $\approx0.091$.
> The width drops by the factor $\sqrt{10}\approx3.16$. On the slide the $N=1$ histogram is flat over the whole range, and the histograms for $N=4,7,10$ become narrower and more bell-shaped.
> With 5000 experiments each histogram is smoother (less ragged), but its width and shape stay the same. The width depends on $N$, the number of samples per mean, not on the number of experiments.

## 6. Estimation: least squares (slides 24-31)
![[cs568_lecture_10_l10_p024.jpg]]
![[cs568_lecture_10_l10_p025.jpg]]
![[cs568_lecture_10_l10_p026.jpg]]
![[cs568_lecture_10_l10_p027.jpg]]
![[cs568_lecture_10_l10_p028.jpg]]
![[cs568_lecture_10_l10_p029.jpg]]
![[cs568_lecture_10_l10_p030.jpg]]
![[cs568_lecture_10_l10_p031.jpg]]

**Notes.** An **estimator** is a function of iid data $X_1,\dots,X_N$ that gives a value for the unknown parameter $\Phi$ of $p(x\mid\Phi)$. Three kinds: MMSE/LSE, MLE, Bayesian.

Predict $Y$ from $X$ with $\hat Y=g(X,\Phi)$:

$$\hat\Phi_{\mathrm{MMSE}}=\arg\min_\Phi E\big(Y-g(X,\Phi)\big)^2,\qquad \hat\Phi_{\mathrm{LSE}}=\arg\min_\Phi\sum_{i=1}^{n}\big(y_i-g(x_i,\Phi)\big)^2$$

MMSE averages over the true joint pdf. LSE sums over the training samples.

- **Constant** $g(x)=c$: $c_{\mathrm{MMSE}}=E(Y)$, the error is $\mathrm{Var}(Y)$. LSE: the sample mean.
- **Linear** $g(x)=ax+b$: $a=\dfrac{\mathrm{Cov}(X,Y)}{\mathrm{Var}(X)}=\rho_{xy}\dfrac{\sigma_y}{\sigma_x}$, $\ b=E(Y)-a\,E(X)$.
- **Linear in $d$ dimensions**: add a constant feature 1, write $\hat Y=XA$ with $X$ of size $n\times(d+1)$. Setting the gradient $2X^{\mathsf T}(XA-Y)$ to zero gives

$$A_{\mathrm{LSE}}=(X^{\mathsf T}X)^{-1}X^{\mathsf T}Y$$

$(X^{\mathsf T}X)^{-1}X^{\mathsf T}$ is the pseudo-inverse. If $X^{\mathsf T}X$ is singular, minimise $\lVert XA-Y\rVert^2+\alpha\lVert A\rVert^2$ instead (**ridge regression**): $A=(X^{\mathsf T}X+\alpha I)^{-1}X^{\mathsf T}Y$.

MATLAB: `ex10p1.m` (1D model), `ex10p2.m` (3D model).

> [!example] Fit $y=a_0+a_1x$ to $(0,1),(1,3),(2,5)$
> The normal equations give $a_0=1$, $a_1=2$. Check: $1+2\cdot0=1$, $1+2\cdot1=3$, $1+2\cdot2=5$.

> [!warning] Slide error (slide 28)
> The slide writes $a=\mathrm{Cov}(X,Y)/\mathrm{Var}(Y)=\rho_{xy}\sigma_x/\sigma_y$ and says "estimate for $c$". The correct slope divides by $\mathrm{Var}(X)$: $a=\rho_{xy}\sigma_y/\sigma_x$. Check with $Y=3X$: the correct formula gives 3, the slide formula gives $\frac13$.

> [!info] How good is an estimator? (beyond the slides)
> An estimator (kestirici) $\theta(X_1,\dots,X_N)$ is computed from random data, so it is itself a random variable: another data set gives another value. Three words describe its quality.
> - **Bias** (yanlılık): $E[\theta]-\Phi$, the average error over many data sets. Unbiased means bias 0.
> - **Variance** (varyans): $\mathrm{Var}[\theta]$, how much the value changes from one data set to the next.
> - **Mean squared error**: $E[(\theta-\Phi)^2]=\mathrm{Var}[\theta]+\text{bias}^2$.
>
> An estimator is **consistent** (tutarlı) if it converges to $\Phi$ as $N\to\infty$. Example: the sample mean of $N$ iid samples has bias 0 and variance $\sigma^2/N$, so it is unbiased and consistent.

> [!info] The skipped derivative steps on slides 27-28 (beyond the slides)
> **Constant.** $e(c)=E(Y-c)^2$. Derivative: $\dfrac{de}{dc}=-2E(Y-c)=-2\big(E(Y)-c\big)=0$, so $c=E(Y)$. The error at this point is $E(Y-E(Y))^2=\mathrm{Var}(Y)$. For the LSE, $\dfrac{d}{dc}\sum_i(y_i-c)^2=-2\sum_i(y_i-c)=0$ gives $nc=\sum_iy_i$, the sample mean.
>
> **Line.** $e(a,b)=E(Y-aX-b)^2$.
> $\dfrac{\partial e}{\partial b}=-2E(Y-aX-b)=0$ gives $b=E(Y)-aE(X)$.
> $\dfrac{\partial e}{\partial a}=-2E\big[X(Y-aX-b)\big]=0$ gives $E(XY)-aE(X^2)-bE(X)=0$.
> Put $b$ in: $\big(E(XY)-E(X)E(Y)\big)-a\big(E(X^2)-E(X)^2\big)=0$, that is $\mathrm{Cov}(X,Y)-a\,\mathrm{Var}(X)=0$.
> So $a=\mathrm{Cov}(X,Y)/\mathrm{Var}(X)$. The line passes through the point of the means $(E(X),E(Y))$.

> [!question]- Check (slide 24): Find the mistake: "We collect the data once, compute $\theta(X_1,\dots,X_N)$, and this number is the parameter $\Phi$." Also say what "iid" requires and why an estimator is called a random variable.
> $\Phi$ is the true, unknown parameter of the pdf $p(x\mid\Phi)$. $\theta$ is a function of the data that gives a guess for $\Phi$. The guess is close to $\Phi$ but in general not equal to it.
> iid: every $X_k$ is drawn from the same pdf $p(x\mid\Phi)$ (identically distributed), and no sample influences another (independent).
> The data are random, so $\theta$ changes with the data set. Example with the sample mean as estimator: the data $(2,4,6,8)$ give 5, another draw $(3,4,7,8)$ from the same source gives 5.5.

> [!question]- Check (slide 25): In $\hat\Phi_{\mathrm{MMSE}}=\arg\min_\Phi E\big(Y-g(X,\Phi)\big)^2$, say what $Y$, $g$, $\Phi$, the square, $E$ and $\arg\min$ are. What must be known to evaluate this expression?
> $Y$: the variable we want to predict. $g(X,\Phi)$: the predictor $\hat Y$, a function with a known form (for example a line) and unknown parameters $\Phi$.
> $Y-g(X,\Phi)$: the prediction error. The square makes positive and negative errors count the same and punishes large errors more.
> $E$: the average over all possible pairs $(X,Y)$, weighted with the joint pdf $f_{xy}(x,y)$. The result is the mean squared error.
> $\arg\min_\Phi$: the result is not the smallest error but the parameter value that gives it.
> The expectation needs the true joint pdf $f_{xy}$. In practice it is usually unknown, which leads to the LSE on the next slide.

> [!question]- Check (slide 26): What replaces the expectation of the MMSE criterion in the LSE criterion, and why? Then compare two candidate models on the training set $(1,\ 2.5),\ (2,\ 3.5),\ (3,\ 6.5)$: $g_1(x)=2x$ and $g_2(x)=2x+0.5$. Which one does the criterion prefer?
> The expectation over the unknown joint pdf is replaced by a sum over the $n$ training samples. The sum divided by $n$ is the sample estimate of the mean squared error, and the factor $\frac1n$ does not move the minimum.
> $g_1$: predictions 2, 4, 6. Errors $0.5,\ -0.5,\ 0.5$. SSE $=0.25+0.25+0.25=0.75$.
> $g_2$: predictions 2.5, 4.5, 6.5. Errors $0,\ -1,\ 0$. SSE $=0+1+0=1$.
> The criterion prefers $g_1$ (smaller SSE). $g_2$ hits two points exactly, but its one large error is squared.
> Careful with the names on the slide: "MSE" is used for "minimum squared error" here and for "mean squared error" on slide 25.

> [!question]- Check (slide 27): The training outputs are $y=3,\ 5,\ 10$. Find the LSE constant $c_{\mathrm{LSE}}$ and its SSE. Compare with the SSE of $c=5$ (the middle value). How does this illustrate "the MSE is the variance of $Y$"?
> $c_{\mathrm{LSE}}=\frac13(3+5+10)=6$.
> SSE at $c=6$: $(3-6)^2+(5-6)^2+(10-6)^2=9+1+16=26$.
> SSE at $c=5$: $4+0+25=29$, which is larger. No constant does better than the mean.
> SSE$/n=26/3\approx8.67$ is the average squared distance from the mean. This is the variance of the three values (with $\frac1n$). The best constant predictor leaves exactly the spread of $Y$ as error.

> [!question]- Check (slide 28): $E(X)=1$, $E(Y)=10$, $\mathrm{Var}(X)=4$, $\mathrm{Var}(Y)=16$, $\mathrm{Cov}(X,Y)=6$. Find $\rho_{xy}$, the slope $a$ and the intercept $b$ of the MMSE line (use the corrected formula from the warning above). What would the formula printed on the slide give for $a$? What happens to the line if $\rho_{xy}=0$?
> $\sigma_x=2$, $\sigma_y=4$. $\rho_{xy}=\dfrac{6}{2\cdot4}=0.75$.
> $a=\dfrac{\mathrm{Cov}(X,Y)}{\mathrm{Var}(X)}=\dfrac64=1.5$. Same result with $\rho_{xy}\dfrac{\sigma_y}{\sigma_x}=0.75\cdot\dfrac42=1.5$.
> $b=E(Y)-a\,E(X)=10-1.5\cdot1=8.5$. Line: $\hat Y=1.5X+8.5$.
> The slide's formula would give $6/16=0.375$, which is the slope for predicting $X$ from $Y$.
> If $\rho_{xy}=0$: $a=0$ and $b=E(Y)$. The line becomes the constant predictor of slide 27, because $X$ carries no linear information about $Y$.

> [!question]- Check (slide 29): A training set has $n=50$ samples with $d=3$ features. Give the sizes of $X$, $A$, $Y$, $X^{\mathsf T}X$ and of the pseudo-inverse. Why does $X$ have a first column of ones? What does the condition $X^{\mathsf T}(XA-Y)=0$ say about the errors?
> $X$: $50\times4$ ($d+1=4$ columns). $A$: $4\times1$ ($a_0,\dots,a_3$). $Y$: $50\times1$.
> $X^{\mathsf T}X$: $4\times4$. Pseudo-inverse $(X^{\mathsf T}X)^{-1}X^{\mathsf T}$: $4\times50$. It turns the 50 outputs into the 4 parameters.
> The column of ones multiplies $a_0$, so $a_0$ is the intercept $b$. This writes "line plus offset" as one matrix product.
> $XA-Y$ is the vector of the 50 errors. The condition says that the error vector is orthogonal to every column of $X$: no feature (and no constant) can explain any more of the error.

> [!question]- Check (slide 30): Two features are identical. The two training samples are $(1,1)$ with output 3 and $(2,2)$ with output 6 (no constant column). Show that the plain LSE fails and compute the ridge solution for $\alpha=1$. What does $\alpha$ do when it is made very small or very large?
> $X=\begin{bmatrix}1&1\\2&2\end{bmatrix}$, $Y=\begin{bmatrix}3\\6\end{bmatrix}$.
> $X^{\mathsf T}X=\begin{bmatrix}5&5\\5&5\end{bmatrix}$ with determinant $25-25=0$: singular, no inverse. Many pairs fit the data exactly, for example $(3,0)$, $(0,3)$ or $(1.5,1.5)$.
> Ridge: $X^{\mathsf T}X+\alpha I=\begin{bmatrix}6&5\\5&6\end{bmatrix}$, determinant $36-25=11$, so it is invertible. $X^{\mathsf T}Y=\begin{bmatrix}15\\15\end{bmatrix}$.
> $A=\dfrac1{11}\begin{bmatrix}6&-5\\-5&6\end{bmatrix}\begin{bmatrix}15\\15\end{bmatrix}=\begin{bmatrix}15/11\\15/11\end{bmatrix}\approx\begin{bmatrix}1.36\\1.36\end{bmatrix}$.
> Prediction for the first sample: $1.36+1.36\approx2.73$ instead of 3. The penalty $\alpha\lVert A\rVert^2$ picks the solution with small weights and shrinks it a little.
> $\alpha\to0$: back to the plain LSE (and to the singular problem here). Very large $\alpha$: the penalty dominates and $A$ goes to zero, so the fit to the data gets worse.

## 7. Maximum likelihood estimation (slides 32-33)
![[cs568_lecture_10_l10_p032.jpg]]
![[cs568_lecture_10_l10_p033.jpg]]

**Notes.** For independent samples the likelihood is a product. MLE picks the $\Phi$ that maximises it. In practice maximise the log-likelihood (same maximiser, no underflow):

$$\Phi_{\mathrm{MLE}}=\arg\max_\Phi\prod_{k=1}^{n}p(x_k\mid\Phi),\qquad l(\Phi)=\sum_{k=1}^{n}\log p(x_k\mid\Phi)$$

For a univariate Gaussian, $l(\mu,\sigma^2)=-\frac n2\log(2\pi\sigma^2)-\frac{1}{2\sigma^2}\sum_k(x_k-\mu)^2$. Setting the derivatives to zero:

$$\mu_{\mathrm{MLE}}=\frac1n\sum_{k=1}^{n}x_k,\qquad \sigma^2_{\mathrm{MLE}}=\frac1n\sum_{k=1}^{n}(x_k-\mu_{\mathrm{MLE}})^2$$

> [!warning] Biased variance
> $\sigma^2_{\mathrm{MLE}}$ divides by $n$. Its expected value is $\frac{n-1}{n}\sigma^2$. The unbiased estimator divides by $n-1$. Example: $x=(2,4,6,8)$ gives $\mu=5$, $\sigma^2_{\mathrm{MLE}}=5$, unbiased $6.67$.

> [!info] The derivative steps of slide 33 and the reason for the bias (beyond the slides)
> The log-likelihood (log-olabilirlik) is $l(\mu,\sigma^2)=-\frac n2\log(2\pi\sigma^2)-\frac{1}{2\sigma^2}\sum_k(x_k-\mu)^2$. The first term comes from the $n$ normalisation constants, the second from the $n$ exponents.
> Mean: only the second term contains $\mu$. $\dfrac{\partial l}{\partial\mu}=\dfrac{1}{\sigma^2}\sum_k(x_k-\mu)=0$ gives $\sum_kx_k=n\mu$, so $\mu=\frac1n\sum_kx_k$. The value of $\sigma^2$ does not matter for this step.
> Variance: $\dfrac{\partial l}{\partial\sigma^2}=-\dfrac{n}{2\sigma^2}+\dfrac{1}{2\sigma^4}\sum_k(x_k-\mu)^2=0$. Multiply by $2\sigma^4$: $n\sigma^2=\sum_k(x_k-\mu)^2$.
> Why the result is biased: the deviations are measured from $\mu_{\mathrm{MLE}}$, and $\mu_{\mathrm{MLE}}$ was fitted to the same samples. The sample mean is the point that makes $\sum_k(x_k-c)^2$ smallest (slide 27), so this sum is smaller than the sum around the true $\mu$. On average the loss is one sample's worth: $E[\sigma^2_{\mathrm{MLE}}]=\frac{n-1}{n}\sigma^2$. The bias $-\sigma^2/n$ goes to 0 for large $n$. The variance of the estimator goes to 0 as well, so the estimator is still consistent.
> The slide calls $\sigma^2_{\mathrm{MLE}}$ "the sample variance". Many books keep this name for the version with $n-1$.

> [!question]- Check (slide 32): Three independent samples are observed. Under parameter value $\Phi_a$ their densities are $0.2,\ 0.5,\ 0.1$. Under $\Phi_b$ they are $0.3,\ 0.2,\ 0.2$. Compute the likelihood and the log-likelihood (natural log) of both. Which value does MLE prefer, and why do both criteria agree? Why is the joint likelihood a product at all?
> $\Phi_a$: $0.2\cdot0.5\cdot0.1=0.010$. Log: $-1.609-0.693-2.303=-4.605$.
> $\Phi_b$: $0.3\cdot0.2\cdot0.2=0.012$. Log: $-1.204-1.609-1.609=-4.423$.
> MLE prefers $\Phi_b$: $0.012>0.010$ and $-4.423>-4.605$.
> The logarithm is monotonically increasing, so a larger product always has a larger log. The position of the maximum is the same, and a sum is easier to differentiate than a product.
> The product needs independence: for independent samples the joint pdf is the product of the single pdfs.
> Wording on the slide: "parameters that maximize the observations" means "parameters that maximise the likelihood of the observations". The data are fixed, $\Phi$ is varied.

> [!question]- Check (slide 33): Five samples $x=3,\ 7,\ 8,\ 4,\ 8$ come from a Gaussian. Compute $\mu_{\mathrm{MLE}}$, $\sigma^2_{\mathrm{MLE}}$ and the unbiased variance. What is $E[\sigma^2_{\mathrm{MLE}}]$ for $n=5$ in terms of the true $\sigma^2$?
> $\mu_{\mathrm{MLE}}=\frac15(3+7+8+4+8)=\frac{30}{5}=6$.
> Deviations: $-3,\ 1,\ 2,\ -2,\ 2$. Squares: $9,\ 1,\ 4,\ 4,\ 4$. Sum: 22.
> $\sigma^2_{\mathrm{MLE}}=\frac{22}{5}=4.4$. Unbiased: $\frac{22}{4}=5.5$.
> $E[\sigma^2_{\mathrm{MLE}}]=\frac{n-1}{n}\sigma^2=\frac45\sigma^2=0.8\,\sigma^2$. On average the MLE is 20% too small for five samples. Check with the numbers: $4.4/5.5=0.8$.

## 8. Bayesian (MAP) estimation (slides 34-35)
![[cs568_lecture_10_l10_p034.jpg]]
![[cs568_lecture_10_l10_p035.jpg]]

**Notes.** MLE treats $\Phi$ as fixed but unknown. Bayesian estimation treats $\Phi$ as a random variable with a prior $p(\Phi)$. Maximum A Posteriori (MAP):

$$\hat\Phi_{\mathrm{MAP}}=\arg\max_\Phi p(\Phi\mid\mathbf x)=\arg\max_\Phi p(\mathbf x\mid\Phi)\,p(\Phi)$$

With little data MAP relies on the prior. With more data it balances prior and likelihood. For large $n$ it approaches MLE. With a constant (non-informative) prior it equals MLE.

Beyond the slides, for a Gaussian mean with known $\sigma^2$ and prior $\mu\sim\mathcal N(\mu_0,\sigma_0^2)$:

$$\hat\mu_{\mathrm{MAP}}=\frac{n\sigma_0^2}{n\sigma_0^2+\sigma^2}\,\bar x+\frac{\sigma^2}{n\sigma_0^2+\sigma^2}\,\mu_0$$

A weighted average of the sample mean and the prior mean. This is the idea of MAP speaker adaptation.

> [!question]- Check (slide 34): Name the four terms of $p(\Phi\mid\mathbf x)=p(\mathbf x\mid\Phi)\,p(\Phi)/p(\mathbf x)$ and say why the slide may replace the division by $p(\mathbf x)$ with "$\propto$". Then find the mistake: "MLE and MAP differ only in the optimiser. Both treat $\Phi$ as a fixed unknown number."
> $p(\Phi)$: the prior (önsel), the belief about the parameter before any data. $p(\mathbf x\mid\Phi)$: the likelihood (olabilirlik) of the observed sequence. $p(\Phi\mid\mathbf x)$: the posterior (sonsal), the belief after the data. $p(\mathbf x)$: the evidence, a normalisation constant.
> $p(\mathbf x)$ does not contain $\Phi$. Dividing by it scales the whole curve but does not move its maximum, so $\arg\max_\Phi$ can ignore it.
> The mistake: only MLE treats $\Phi$ as fixed but unknown. Bayesian estimation treats $\Phi$ as a random variable with its own pdf $p(\Phi)$. The criteria differ too: MLE maximises $p(\mathbf x\mid\Phi)$, MAP maximises $p(\mathbf x\mid\Phi)\,p(\Phi)$.

> [!question]- Check (slide 35): Use the Gaussian-mean formula of the note above with $\sigma^2=4$, prior $\mu_0=0$, $\sigma_0^2=1$ and sample mean $\bar x=5$. Compute $\hat\mu_{\mathrm{MAP}}$ for $n=1$, $n=16$ and $n=400$. Which statements of the slide do the three numbers show? What happens with a very wide prior?
> Weight of the data: $w=\dfrac{n\sigma_0^2}{n\sigma_0^2+\sigma^2}=\dfrac{n}{n+4}$. With $\mu_0=0$: $\hat\mu_{\mathrm{MAP}}=w\,\bar x$. The MLE is $\bar x=5$ in all cases.
> $n=1$: $w=\frac15=0.2$, $\hat\mu_{\mathrm{MAP}}=1.0$. Little data: the estimate stays near the prior mean 0.
> $n=16$: $w=\frac{16}{20}=0.8$, $\hat\mu_{\mathrm{MAP}}=4.0$. Prior and likelihood are balanced, the data already weigh more.
> $n=400$: $w=\frac{400}{404}\approx0.990$, $\hat\mu_{\mathrm{MAP}}\approx4.95$. For large $n$ MAP approaches the MLE.
> Very wide prior ($\sigma_0^2\to\infty$): $w\to1$ for every $n$, so $\hat\mu_{\mathrm{MAP}}=\bar x$. A flat prior is the non-informative prior of the slide, and MAP becomes MLE.
> The opposite case (beyond the slides): a very narrow prior ($\sigma_0^2\to0$) gives $w\to0$, so $\hat\mu_{\mathrm{MAP}}$ stays at $\mu_0$ whatever the data say.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Conditional probability | $P[A\mid B]=P[A\cap B]/P[B]$ |
| Total probability | $P[A]=\sum_k P[A\mid B_k]P[B_k]$ |
| Bayes | $P[\omega_j\mid x]=P[x\mid\omega_j]P[\omega_j]/P[x]$ |
| cdf, pdf | $F_X(x)=P[X\le x]$, $f_X=dF_X/dx$ |
| Variance | $\mathrm{Var}[X]=E[X^2]-(E[X])^2$ |
| Covariance | $c_{ik}=\rho_{ik}\sigma_i\sigma_k$ |
| Gaussian | $\mathcal N(\boldsymbol\mu,\boldsymbol\Sigma)$; closed under marginals, conditionals, linear maps |
| CLT | mean of $N$ samples $\sim\mathcal N(\mu,\sigma^2/N)$ |
| LSE | constant: mean; line: $a=\mathrm{Cov}(X,Y)/\mathrm{Var}(X)$; $A=(X^{\mathsf T}X)^{-1}X^{\mathsf T}Y$ |
| Ridge | $A=(X^{\mathsf T}X+\alpha I)^{-1}X^{\mathsf T}Y$ |
| MLE | $\arg\max\sum_k\log p(x_k\mid\Phi)$; Gaussian: sample mean, variance with $1/n$ |
| MAP | $\arg\max p(\mathbf x\mid\Phi)p(\Phi)$ |

## Check yourself
> [!question]- 1. Fair die, $A=\{X\ge5\}$, $B=$ odd. Find $P[A\cap B]$, $P[A\cup B]$, $P[A\mid B]$.
> $A\cap B=\{5\}$: $\frac16$. Union: $\frac26+\frac36-\frac16=\frac23$. Conditional: $\frac{1/6}{3/6}=\frac13$.

> [!question]- 2. Repeat the medical test with 10% prevalence. $P[\text{cond}\mid+]$?
> $\dfrac{0.90\times0.10}{0.09+0.02\times0.90}=\dfrac{0.09}{0.108}\approx0.833$. A larger prior makes the same test far more convincing.

> [!question]- 3. $E[X]=2$, $E[X^2]=5$. Variance and standard deviation?
> $\mathrm{Var}=5-2^2=1$, std $=1$.

> [!question]- 4. $X\sim\mathcal N(0,1)$, $Y=3X+2$. Mean and variance of $Y$?
> $E[Y]=2$. $\mathrm{Var}[Y]=3^2\cdot1=9$. So $Y\sim\mathcal N(2,9)$.

> [!question]- 5. Fit $y=a_0+a_1x$ to $(0,1),(1,2),(2,2),(3,4)$ by least squares.
> $X^{\mathsf T}X=\begin{bmatrix}4&6\\6&14\end{bmatrix}$, $X^{\mathsf T}Y=\begin{bmatrix}9\\18\end{bmatrix}$. Solving: $a_1=0.9$, $a_0=0.9$. Line: $\hat y=0.9+0.9x$.

> [!question]- 6. 1000 frames, each with likelihood 0.01. Why add logs instead of multiplying?
> The product is $10^{-2000}$, which underflows to 0 in double precision. The log-likelihood is $1000\times(-4.6052)=-4605.2$. The log is increasing, so the best hypothesis is the same.

## Links
- Course index: [[CS568 Home]]
- Previous lecture: [[CS568 L09 - Cepstral analysis]]
- Full step-by-step notes (PDF): [CS568_Lecture_10_Probability_Estimation.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_10/CS568_Lecture_10_Probability_Estimation.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_10/CS568_Lecture_10_Probability_Estimation.pdf`
- Original slides (PDF): [l10.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_10/l10.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_10/l10.pdf`

Slides not embedded: 1
