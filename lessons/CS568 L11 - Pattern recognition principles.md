---
course: CS 568 Speech Processing
lecture: 11
title: Pattern recognition principles
tags: [cs568, lecture]
slides: lectures/lecture_11/l11.pdf
full_notes: lecture_notes/lecture_11/CS568_Lecture_11_Pattern_Recognition.pdf
---
# CS 568 · Lecture 11 · Pattern recognition principles

> [!summary] In short
> - MAP rule: pick $\arg\max_i p(x\mid\omega_i)P(\omega_i)$. It gives the minimum error rate. With unequal costs, pick the decision with the smallest expected loss.
> - Gaussian (quadratic) classifier: the score is the log posterior. Shared covariance gives linear boundaries, separate covariances give quadratic ones.
> - kNN: the $k$ nearest training examples vote.
> - Curse of dimensionality: many features with little data overfit. PCA keeps variance, LDA keeps class separation.
> - Unsupervised: GMM fitted with EM (soft assignment), k-means (hard assignment), vector quantisation.

## 1. Bayes decision rule and risk (slides 2-5)
![[cs568_lecture_11_l11_p002.jpg]]
![[cs568_lecture_11_l11_p003.jpg]]
![[cs568_lecture_11_l11_p004.jpg]]
![[cs568_lecture_11_l11_p005.jpg]]

**Notes.** Stock market example: predict UP, DOWN or UNCHANGED ($\omega_1,\omega_2,\omega_3$). With only the priors $P(\omega_i)$ we would always predict the same class. With an observation $x$ (interest rates and so on) and likelihoods $p(x\mid\omega_i)$:

$$P(\omega_i\mid x)=\frac{p(x\mid\omega_i)\,P(\omega_i)}{p(x)},\qquad k=\arg\max_i P(\omega_i\mid x)=\arg\max_i p(x\mid\omega_i)\,P(\omega_i)$$

**Risk.** Classes $\Omega=\{\omega_1,\dots,\omega_s\}$, decisions $\Delta=\{\delta_1,\dots,\delta_t\}$, loss $l(\delta_i\mid\omega_j)$ for deciding $\delta_i$ when the truth is $\omega_j$. The conditional risk is the expected loss:

$$R(\delta_i\mid x)=\sum_{j=1}^{s}l(\delta_i\mid\omega_j)\,P(\omega_j\mid x),\qquad k=\arg\min_i R(\delta_i\mid x)$$

With the zero-one loss (0 if correct, 1 if wrong), $R(\delta_i\mid x)=1-P(\omega_i\mid x)$, so minimum risk is the MAP rule and it gives the minimum error rate.

> [!example] Stock market with numbers
> Priors UP 0.3, DOWN 0.2, UNCH 0.5. Likelihoods 0.6, 0.5, 0.1. Products: 0.18, 0.10, 0.05. Posteriors: 0.545, 0.303, 0.152. Prior only says UNCH. MAP says UP.

> [!warning] When zero-one loss is not advisable (slide 5 question)
> When errors have very different costs: a missed medical condition, an impostor accepted by speaker verification. Example: $P(\text{genuine}\mid x)=0.7$, accepting an impostor costs 10, rejecting a genuine user costs 1. $R(\text{accept})=10\cdot0.3=3$, $R(\text{reject})=1\cdot0.7=0.7$. Reject, although MAP would accept.

## 2. Quadratic (Gaussian) classifiers (slides 6-10)
![[cs568_lecture_11_l11_p006.jpg]]
![[cs568_lecture_11_l11_p007.jpg]]
![[cs568_lecture_11_l11_p008.jpg]]
![[cs568_lecture_11_l11_p009.jpg]]
![[cs568_lecture_11_l11_p010.jpg]]

**Notes.** The likelihoods are usually unknown. Options: estimate them from data (Naive Bayes, nearest neighbours), assume a form and estimate its parameters (quadratic classifiers), or separate the data geometrically (perceptrons, SVMs).

Assume each likelihood is Gaussian with mean $\mu_i$ and covariance $\Sigma_i$. Take the log of the posterior and drop the terms that are the same for every class. The **discriminant function** is

$$d_i(x)=-\tfrac12(x-\mu_i)^{\mathsf T}\Sigma_i^{-1}(x-\mu_i)-\tfrac12\log|\Sigma_i|+\log P(\omega_i)$$

Assign $x$ to the class with the largest $d_i(x)$. With equal priors, drop $\log P(\omega_i)$.

- **Case 1**, $\Sigma_i=\sigma^2I$: $d_i(x)=-\frac{1}{2\sigma^2}\lVert x-\mu_i\rVert^2$. Nearest mean with Euclidean distance. Linear boundary.
- **Case 2**, $\Sigma_i=\Sigma$: $d_i(x)=-\frac12(x-\mu_i)^{\mathsf T}\Sigma^{-1}(x-\mu_i)$. Nearest mean with **Mahalanobis** distance. Linear boundary.
- **General case**: separate $\Sigma_i$. Quadratic boundaries (slide 10).

> [!example] Mahalanobis distance
> $\mu=(0,0)$, $\Sigma=\mathrm{diag}(4,1)$. Points $(0,2)$ and $(4,0)$ are equally far in the Mahalanobis sense: 4 units along an axis with standard deviation 2 is 2 standard deviations, the same as 2 units along an axis with standard deviation 1.

## 3. k nearest neighbours (slides 11-13)
![[cs568_lecture_11_l11_p011.jpg]]
![[cs568_lecture_11_l11_p012.jpg]]
![[cs568_lecture_11_l11_p013.jpg]]

**Notes.** To classify an unlabelled example $x_u$: find the $k$ closest training examples and take the most represented class. Needs: an integer $k$, labelled examples, a closeness measure (usually Euclidean). The vote fractions estimate the posterior: $P(\omega_i\mid x)\approx k_i/k$. On slide 11 ($k=5$): 4 neighbours of $\omega_1$, 1 of $\omega_3$, so $\omega_1$ with estimated posterior 0.8.

Advantages: simple, uses local information, easy to parallelise, nearly optimal for $n\to\infty$:

$$P[\text{error}]_{\text{Bayes}}\le P[\text{error}]_{1\text{NN}}\le 2\,P[\text{error}]_{\text{Bayes}}$$

Disadvantages: stores all the training data, slow recall, suffers from the curse of dimensionality. Larger $k$ gives smoother regions and probability estimates, but too large a $k$ destroys locality.

> [!warning] Watch out
> - The slide writes the bound with strict $<$. Equality is possible (Bayes error 0), so $\le$ is correct.
> - kNN and k-means share only the letter $k$. kNN: $k$ voting neighbours, labelled data. k-means: number of clusters, no labels.

## 4. Curse of dimensionality (slides 14-17)
![[cs568_lecture_11_l11_p014.jpg]]
![[cs568_lecture_11_l11_p015.jpg]]
![[cs568_lecture_11_l11_p016.jpg]]
![[cs568_lecture_11_l11_p017.jpg]]

**Notes.** The number of examples needed to train a classifier grows exponentially with the dimension. More features give more information in theory, but in practice they raise the risk of **overfitting**: fitting the noise in the data. For a fixed sample size there is a number of features above which performance drops (Hughes phenomenon, slide 15). Slide 16: a very flexible boundary fits every training point but generalises worse than a smooth one.

How to beat it: prior knowledge (parametric models), smoothness (regularisation), or dimensionality reduction. Two kinds of reduction:
- **Feature extraction**: new features as combinations of the old ones, $y=f(x)$. Examples: PCA, LDA.
- **Feature selection**: keep a subset of the original features.

## 5. PCA and LDA (slides 18-22)
![[cs568_lecture_11_l11_p018.jpg]]
![[cs568_lecture_11_l11_p019.jpg]]
![[cs568_lecture_11_l11_p020.jpg]]
![[cs568_lecture_11_l11_p021.jpg]]
![[cs568_lecture_11_l11_p022.jpg]]

**Notes.** Two goals for extraction: signal representation (PCA) and classification (LDA).

**PCA** (Karhunen-Loeve transform). Project the data onto the eigenvectors $v_i$ of the covariance matrix $\Sigma$ with the largest eigenvalues $\lambda_i$:

$$y=Ax,\qquad A=[v_1\ v_2\ \dots\ v_M],\qquad \Sigma v_i=\lambda_iv_i$$

$\lambda_i$ is the variance along $v_i$. PCA finds orthogonal directions of largest variance and decorrelates the features. It uses no labels. Retained variance: $\sum_{i\le M}\lambda_i/\sum_i\lambda_i$.

**LDA.** Within-class and between-class scatter ($N_i$ examples in class $i$, mean $\mu_i$, overall mean $\mu$):

$$S_W=\sum_{i=1}^{C}\sum_{x\in\omega_i}(x-\mu_i)(x-\mu_i)^{\mathsf T},\qquad S_B=\sum_{i=1}^{C}N_i(\mu_i-\mu)(\mu_i-\mu)^{\mathsf T}$$

LDA maximises $J(W)=\dfrac{|W^{\mathsf T}S_BW|}{|W^{\mathsf T}S_WW|}$. The solution: eigenvectors with the largest eigenvalues of $(S_B-\lambda_iS_W)v_i=0$. At most $C-1$ projections, because $\operatorname{rank}S_B\le C-1$. For two classes $w\propto S_W^{-1}(\mu_1-\mu_2)$.

Limits of LDA: can overfit, fails when the class information is not in the means, weak for strongly non-Gaussian classes. MATLAB: `ex11p1.m`.

> [!warning] Watch out
> - High variance does not mean good class separation. PCA never looks at the labels.
> - Slide 21 writes the mean constraint as $\sum_i\mu_i=\mu$. The correct weighted form is $\frac1N\sum_iN_i\mu_i=\mu$.
> - Strictly the projection is $y=A^{\mathsf T}(x-\mu)$, with the eigenvectors as rows. The slide's $y=Ax$ is shorthand.

## 6. Gaussian mixture models and EM (slides 23-29)
![[cs568_lecture_11_l11_p023.jpg]]
![[cs568_lecture_11_l11_p024.jpg]]
![[cs568_lecture_11_l11_p025.jpg]]
![[cs568_lecture_11_l11_p026.jpg]]
![[cs568_lecture_11_l11_p027.jpg]]
![[cs568_lecture_11_l11_p028.jpg]]
![[cs568_lecture_11_l11_p029.jpg]]

**Notes.** Supervised: patterns are pairs $(x,\omega)$. Unsupervised: only $x$, no labels.

A **GMM** models a pdf as a weighted sum of Gaussians with parameters $\theta_c=\{\mu_c,\Sigma_c\}$ and mixing weights $p(\theta_c)$ that sum to 1:

$$p(x\mid\theta)=\sum_{c=1}^{C}p(x\mid\theta_c)\,p(\theta_c)$$

Maximum likelihood: maximise $\sum_{i=1}^{n}\log\sum_{c}p(x_i\mid\theta_c)p(\theta_c)$. Setting derivatives to zero gives equations with the posterior $p(\theta_c\mid x_i)$ (the **responsibility**) on the right-hand side, so there is no closed form. Solve by iteration, the **EM algorithm**:

- **E-step**: with the old parameters, compute the responsibilities by Bayes rule,
$$p(\theta_c\mid x_i)=\frac{p(x_i\mid\theta_c)\,p(\theta_c)}{\sum_{c'}p(x_i\mid\theta_{c'})\,p(\theta_{c'})}$$
- **M-step**: weighted ML estimates,
$$p(\theta_c)=\frac1n\sum_{i}p(\theta_c\mid x_i),\qquad \mu_c=\frac{\sum_ip(\theta_c\mid x_i)\,x_i}{\sum_ip(\theta_c\mid x_i)},\qquad \Sigma_c=\frac{\sum_ip(\theta_c\mid x_i)(x_i-\mu_c)(x_i-\mu_c)^{\mathsf T}}{\sum_ip(\theta_c\mid x_i)}$$

Each iteration never decreases the log-likelihood. Slides 28-29: 900 points in an annulus, 30 components, covariances regularised at each iteration, components with tiny weights removed.

> [!warning] Watch out
> EM reaches only a local maximum, so the result depends on the initialisation. A component can collapse onto one point ($\Sigma_c\to0$), so implementations regularise or floor the covariances. The slide says the likelihood "increases" at each step. Strictly it increases or stays the same.

## 7. k-means and vector quantisation (slides 30-32)
![[cs568_lecture_11_l11_p030.jpg]]
![[cs568_lecture_11_l11_p031.jpg]]
![[cs568_lecture_11_l11_p032.jpg]]

**Notes.** k-means groups unlabelled examples into $C$ clusters by minimising

$$J_{\mathrm{MSE}}=\sum_{c=1}^{C}\sum_{x\in\omega_c}\lVert x-\mu_c\rVert^2,\qquad \mu_c=\frac{1}{n_c}\sum_{x\in\omega_c}x$$

Algorithm: choose $C$; initialise the clusters or the centres; repeat (compute each cluster mean, reassign each example to the nearest mean) until no assignment changes. It is EM with hard assignments, and it also stops at a local optimum.

**Vector quantisation.** Instead of quantising each channel alone, replace the whole vector by the index of its nearest prototype. Each centre is a **codeword**, the set is the **codebook**. With $b$ bits a codebook holds $2^b$ codewords. MATLAB: `ex11p2.m`.

> [!example] k-means on $\{1,2,4,7,8\}$, $C=2$
> Starting from centres 1 and 2, the algorithm ends with clusters $\{1,2\}$ and $\{4,7,8\}$, means 1.5 and 6.33, boundary at 3.92.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| MAP rule | $k=\arg\max_i p(x\mid\omega_i)P(\omega_i)$ |
| Minimum risk | $k=\arg\min_i\sum_j l(\delta_i\mid\omega_j)P(\omega_j\mid x)$ |
| Gaussian discriminant | $d_i(x)=-\frac12(x-\mu_i)^{\mathsf T}\Sigma_i^{-1}(x-\mu_i)-\frac12\log|\Sigma_i|+\log P(\omega_i)$ |
| Special cases | $\Sigma_i=\sigma^2I$: Euclidean; shared $\Sigma$: Mahalanobis; both linear |
| kNN | vote of $k$ neighbours, $P(\omega_i\mid x)\approx k_i/k$ |
| PCA | top eigenvectors of $\Sigma$ |
| LDA | $S_Bv=\lambda S_Wv$, at most $C-1$ directions |
| GMM | $p(x\mid\theta)=\sum_cp(x\mid\theta_c)p(\theta_c)$ |
| EM | E: responsibilities; M: weighted mean, covariance, weight |
| k-means, VQ | hard-assignment EM; codebook of $2^b$ codewords |

## Check yourself
> [!question]- 1. Equal priors, $p(x\mid\omega_1)=0.2$, $p(x\mid\omega_2)=0.6$. Posteriors and MAP decision?
> Products: 0.1 and 0.3, sum 0.4. Posteriors 0.25 and 0.75. Choose $\omega_2$.

> [!question]- 2. Posteriors 0.6 and 0.4. Losses $l(\delta_1\mid\omega_2)=3$, $l(\delta_2\mid\omega_1)=1$. Which decision?
> $R(\delta_1)=3\cdot0.4=1.2$. $R(\delta_2)=1\cdot0.6=0.6$. Choose $\delta_2$, although $\omega_1$ is more probable.

> [!question]- 3. $\mu=(0,0)$, $\Sigma=\mathrm{diag}(9,1)$, $x=(3,1)$. Squared Euclidean and Mahalanobis distances?
> Euclidean: $9+1=10$. Mahalanobis: $3^2/9+1^2/1=2$.

> [!question]- 4. Eigenvalues 5, 3, 1, 1. Smallest $M$ that keeps 80% of the variance?
> Total 10. $M=1$: 50%. $M=2$: 80%. So $M=2$.

> [!question]- 5. How many LDA projections for 4 classes and 39-dimensional features?
> At most $C-1=3$.

> [!question]- 6. k-means on $\{0,1,5,6\}$, $C=2$, initial centres 0 and 1. Final clusters and $J_{\mathrm{MSE}}$?
> First assignment $\{0\}$, $\{1,5,6\}$ with means 0 and 4. Then 1 moves: $\{0,1\}$, $\{5,6\}$, means 0.5 and 5.5. No more changes. $J_{\mathrm{MSE}}=4\times0.25=1$.

## Links
- Course index: [[CS568 Home]]
- Previous lecture: [[CS568 L10 - Probability and estimation theory]]
- Full step-by-step notes (PDF): [CS568_Lecture_11_Pattern_Recognition.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_11/CS568_Lecture_11_Pattern_Recognition.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_11/CS568_Lecture_11_Pattern_Recognition.pdf`
- Original slides (PDF): [l11.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_11/l11.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_11/l11.pdf`

Slides not embedded: 1
