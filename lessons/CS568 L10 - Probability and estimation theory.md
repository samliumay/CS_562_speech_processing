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

## 4. Random vectors and covariance (slides 18-20)
![[cs568_lecture_10_l10_p018.jpg]]
![[cs568_lecture_10_l10_p019.jpg]]
![[cs568_lecture_10_l10_p020.jpg]]

**Notes.** A random vector $\mathbf X=[X_1,\dots,X_N]^{\mathsf T}$ has a joint cdf and a joint pdf. A **marginal** pdf integrates out the dimensions of no interest: $f_{X_1}(x_1)=\int f_{X_1X_2}(x_1,x_2)\,dx_2$.

$$E[\mathbf X]=\boldsymbol\mu,\qquad \mathrm{Cov}[\mathbf X]=\boldsymbol\Sigma=E\big[(\mathbf X-\boldsymbol\mu)(\mathbf X-\boldsymbol\mu)^{\mathsf T}\big]$$

Entry $c_{ik}$ of $\boldsymbol\Sigma$: positive if $x_i$ and $x_k$ increase together, negative if one decreases when the other increases, zero if uncorrelated. Diagonal: $c_{ii}=\sigma_i^2$. Off-diagonal: $c_{ik}=\rho_{ik}\,\sigma_i\sigma_k$ with correlation coefficient $\rho_{ik}\in[-1,1]$.

Speech features with a diagonal covariance need far fewer parameters. This is one reason decorrelated features such as MFCCs are popular.

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

## 7. Maximum likelihood estimation (slides 32-33)
![[cs568_lecture_10_l10_p032.jpg]]
![[cs568_lecture_10_l10_p033.jpg]]

**Notes.** For independent samples the likelihood is a product. MLE picks the $\Phi$ that maximises it. In practice maximise the log-likelihood (same maximiser, no underflow):

$$\Phi_{\mathrm{MLE}}=\arg\max_\Phi\prod_{k=1}^{n}p(x_k\mid\Phi),\qquad l(\Phi)=\sum_{k=1}^{n}\log p(x_k\mid\Phi)$$

For a univariate Gaussian, $l(\mu,\sigma^2)=-\frac n2\log(2\pi\sigma^2)-\frac{1}{2\sigma^2}\sum_k(x_k-\mu)^2$. Setting the derivatives to zero:

$$\mu_{\mathrm{MLE}}=\frac1n\sum_{k=1}^{n}x_k,\qquad \sigma^2_{\mathrm{MLE}}=\frac1n\sum_{k=1}^{n}(x_k-\mu_{\mathrm{MLE}})^2$$

> [!warning] Biased variance
> $\sigma^2_{\mathrm{MLE}}$ divides by $n$. Its expected value is $\frac{n-1}{n}\sigma^2$. The unbiased estimator divides by $n-1$. Example: $x=(2,4,6,8)$ gives $\mu=5$, $\sigma^2_{\mathrm{MLE}}=5$, unbiased $6.67$.

## 8. Bayesian (MAP) estimation (slides 34-35)
![[cs568_lecture_10_l10_p034.jpg]]
![[cs568_lecture_10_l10_p035.jpg]]

**Notes.** MLE treats $\Phi$ as fixed but unknown. Bayesian estimation treats $\Phi$ as a random variable with a prior $p(\Phi)$. Maximum A Posteriori (MAP):

$$\hat\Phi_{\mathrm{MAP}}=\arg\max_\Phi p(\Phi\mid\mathbf x)=\arg\max_\Phi p(\mathbf x\mid\Phi)\,p(\Phi)$$

With little data MAP relies on the prior. With more data it balances prior and likelihood. For large $n$ it approaches MLE. With a constant (non-informative) prior it equals MLE.

Beyond the slides, for a Gaussian mean with known $\sigma^2$ and prior $\mu\sim\mathcal N(\mu_0,\sigma_0^2)$:

$$\hat\mu_{\mathrm{MAP}}=\frac{n\sigma_0^2}{n\sigma_0^2+\sigma^2}\,\bar x+\frac{\sigma^2}{n\sigma_0^2+\sigma^2}\,\mu_0$$

A weighted average of the sample mean and the prior mean. This is the idea of MAP speaker adaptation.

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
