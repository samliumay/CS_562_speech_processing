---
course: CS 568 Speech Processing
lesson: CS568 L11 - Pattern recognition principles
type: quiz
questions: 28
---

# CS568 L11 - Pattern recognition principles - quiz

Lesson note: [[CS568 L11 - Pattern recognition principles]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Bayes decision rule and risk (slides 2-5)

### Q1 (slide 2)

We predict the stock market index: UP, DOWN or UNCHANGED, with priors 0.45, 0.35 and 0.20. There is no observation, so we predict UP every day, the class with the largest prior. What is the probability of a wrong prediction, and why does the slide call this decision unreasonable?

- A) 0.45. The prediction is the same every day, so it can never say DOWN or UNCHANGED.
- B) 0.45. The rule is unreasonable because its error is larger than that of a uniform guess.
- C) 0.55. The rule is unreasonable because its error is larger than that of a uniform guess.
- D) 0.55. The prediction is the same every day, so it can never say DOWN or UNCHANGED.

> [!success]- Answer
> **D.** The prediction UP is right with probability 0.45, so it is wrong with probability $1-0.45=0.55$.
> It is the best rule with the priors alone, but it never changes. On many days the index goes down or stays the same, and the rule can never say so. An observation $x$ is needed to make the decision depend on the day.
> - A: 0.45 is the probability of a correct prediction, not of an error.
> - B: 0.45 is the probability of a correct prediction. Also, a uniform guess is wrong with probability $2/3\approx0.67$, more than the rule. The rule beats it.
> - C: A uniform guess is wrong with probability $2/3\approx0.67$, which is larger than 0.55. The rule beats it.

### Q2 (slide 3)

Bayes rule is $P(\omega_i\mid x)=\dfrac{p(x\mid\omega_i)\,P(\omega_i)}{p(x)}$, with $x$ the observation and $\omega_i$ class $i$. The MAP decision is written as $\arg\max_i p(x\mid\omega_i)\,P(\omega_i)$, without $p(x)$. Which statement is correct?

- A) $p(x)$ is the evidence. It is the same for every class, so dividing by it cannot change which class wins.
- B) $p(x)$ is the evidence. Once $x$ has been observed its value is 1, so dividing by it changes nothing.
- C) $p(x\mid\omega_i)$ is the posterior of class $\omega_i$, and $p(x)$ is dropped since it does not depend on $i$.
- D) $p(x)$ can be dropped because the likelihoods $p(x\mid\omega_i)$ of all the classes sum to 1 at a fixed $x$.

> [!success]- Answer
> **A.** $p(x)=\sum_k p(x\mid\omega_k)P(\omega_k)$ is the evidence. It has no index $i$. Dividing every class score by the same positive number keeps the largest score the largest, so the decision can drop it.
> - B: $p(x)$ is a density value, not the probability of an event that happened. It is in general not 1. It is dropped because it is the same for all classes.
> - C: The reason is right, but the names are swapped. $p(x\mid\omega_i)$ is the likelihood. The posterior is $P(\omega_i\mid x)$.
> - D: For a fixed $x$ the likelihoods of the classes do not have to sum to 1. The posteriors do.

### Q3 (slide 4)

Slide 4 has a set of classes $\Omega=\{\omega_1,\dots,\omega_s\}$, a set of decisions $\Delta=\{\delta_1,\dots,\delta_t\}$ and a loss $l(\delta_i\mid\omega_j)$: the cost of taking decision $\delta_i$ when the true class is $\omega_j$. $R(\delta_i\mid x)$ is the conditional risk of decision $\delta_i$ for the observation $x$. Which statement is correct?

- A) $\Delta$ is a second name for $\Omega$: decision $\delta_i$ always means "the class is $\omega_i$", so $t=s$.
- B) $R(\delta_i\mid x)$ adds the losses of all decisions $\delta_k$ for class $\omega_i$, each weighted by $P(\omega_i\mid x)$.
- C) $R(\delta_i\mid x)$ adds the losses of $\delta_i$ over all true classes $\omega_j$, each weighted by $P(\omega_j\mid x)$.
- D) $R(\delta_i\mid x)$ adds the losses of $\delta_i$ over all true classes $\omega_j$, each weighted by $P(\omega_j)$.

> [!success]- Answer
> **C.** $R(\delta_i\mid x)=\sum_j l(\delta_i\mid\omega_j)\,P(\omega_j\mid x)$. The decision $\delta_i$ is fixed. We do not know the truth, so we average its loss over all the possible true classes, weighted by how probable each class is after seeing $x$.
> - A: A decision is what we do, a class is what is true. They need not match: classes UP, DOWN, UNCHANGED ($s=3$), decisions buy, sell, wait, ask for more data ($t=4$).
> - B: The loss indices are swapped. The first index of $l$ is the decision and stays fixed. The sum runs over the second index, the true class.
> - D: The prior was used instead of the posterior. The risk is for this $x$, so the weight is $P(\omega_j\mid x)$.

### Q4 (slide 5)

Three classes have posteriors $P(\omega_1\mid x)=0.5$, $P(\omega_2\mid x)=0.3$, $P(\omega_3\mid x)=0.2$. Decision $\delta_i$ means "choose $\omega_i$". The loss is the zero-one loss: $l(\delta_i\mid\omega_j)=0$ if $i=j$ and 1 otherwise. What are the conditional risks $R(\delta_1\mid x)$, $R(\delta_2\mid x)$, $R(\delta_3\mid x)$, and which decision is taken?

- A) 0.5, 0.7, 0.8. Choose $\delta_1$.
- B) 0.5, 0.3, 0.2. Choose $\delta_3$.
- C) 0.5, 0.7, 0.8. Choose $\delta_3$.
- D) 2, 2, 2. All equal, so choose $\delta_1$.

> [!success]- Answer
> **A.** With the zero-one loss only the wrong classes count: $R(\delta_i\mid x)=\sum_{j\neq i}P(\omega_j\mid x)$. The posteriors sum to 1, so this is $1-P(\omega_i\mid x)$: $1-0.5=0.5$, $1-0.3=0.7$, $1-0.2=0.8$.
> The smallest risk is for $\delta_1$, the class with the largest posterior. This is the MAP rule.
> - B: The 0 and the 1 of the loss were swapped (loss 1 on the correct class). Then $R(\delta_i\mid x)=P(\omega_i\mid x)$, and the smallest one picks the least probable class.
> - C: The risks are right, but the largest one was chosen. Risk is a cost: we take the smallest.
> - D: The posteriors were left out: each risk just counts the two wrong classes. Each loss must be weighted by the posterior of its class.

## 2. Quadratic (Gaussian) classifiers (slides 6-10)

### Q5 (slide 6)

When the likelihoods $p(x\mid\omega_i)$ are unknown, slide 6 lists three ways to go on: (1) estimate the density from the data with no fixed form, (2) assume a form for the density and estimate its parameters, (3) ignore the density and separate the classes geometrically. Which statement is correct?

- A) kNN belongs to way 2: it assumes Gaussian classes and keeps only a mean and a covariance per class.
- B) The support vector machine belongs to way 1: it estimates each class density from the stored examples.
- C) kNN needs the least training data of the three ways, because it makes no assumption about the density.
- D) The quadratic classifier is way 2: it assumes a Gaussian and needs only $\mu_i$ and $\Sigma_i$ per class.

> [!success]- Answer
> **D.** The quadratic classifier assumes Gaussian likelihoods. From the data it needs only the mean $\mu_i$ and the covariance $\Sigma_i$ of each class.
> kNN is way 1: it keeps the training examples. The support vector machine is way 3: it learns the boundary directly.
> - A: This describes the quadratic classifier. kNN assumes no form and keeps all the examples.
> - B: The SVM never models a density. It places a boundary between the classes.
> - C: It is the other way round. With no assumption, kNN needs much more data. Way 2 needs the least data, but it is only as good as its assumption.

### Q6 (slide 7)

In the general Gaussian classifier each class has its own mean $\mu_i$, covariance $\Sigma_i$ and prior $P(\omega_i)$. For a $d$-dimensional $x$, the log posterior of class $i$ has four terms: $-\tfrac12(x-\mu_i)^{\mathsf T}\Sigma_i^{-1}(x-\mu_i)$, $-\tfrac12\log\lvert\Sigma_i\rvert$, $\log P(\omega_i)$ and $-\tfrac d2\log2\pi$. Which term or terms can never change the decision?

- A) $\log P(\omega_i)$, because the priors do not depend on $x$.
- B) $-\tfrac12\log\lvert\Sigma_i\rvert$, because it is the same for every class.
- C) $-\tfrac d2\log2\pi$ and $-\tfrac12\log\lvert\Sigma_i\rvert$, because neither depends on $x$.
- D) Only $-\tfrac d2\log2\pi$, because it is the same for every class.

> [!success]- Answer
> **D.** A term can be dropped from the decision only if it has the same value for every class. $-\tfrac d2\log2\pi$ has no index $i$. The other three terms all contain $i$.
> - A: Not depending on $x$ is not enough. The priors differ between classes, so they shift the scores and can change the winner.
> - B: Each class has its own $\Sigma_i$, so the determinants differ. The term is the same for all classes only when $\Sigma$ is shared.
> - C: Same slip as A for $-\tfrac12\log\lvert\Sigma_i\rvert$: it does not depend on $x$, but it depends on $i$.

### Q7 (slide 8)

Case 1: every class has covariance $\sigma^2I$ with $\sigma^2=4$, so $d_i(x)=-\dfrac{\lVert x-\mu_i\rVert^2}{2\sigma^2}$. The means are $\mu_1=(0,0)$ and $\mu_2=(4,2)$, the priors are equal, and $x=(1,2)$. $x$ goes to the class with the largest $d_i(x)$. Which result is correct?

- A) $d_1=-1.25$, $d_2=-2.25$, class 1.
- B) $d_1=-0.625$, $d_2=-1.125$, class 1.
- C) $d_1=-0.625$, $d_2=-1.125$, class 2.
- D) $d_1=0.625$, $d_2=1.125$, class 2.

> [!success]- Answer
> **B.** $\lVert x-\mu_1\rVert^2=1^2+2^2=5$, so $d_1=-5/8=-0.625$. $\lVert x-\mu_2\rVert^2=(-3)^2+0^2=9$, so $d_2=-9/8=-1.125$.
> $-0.625>-1.125$, so $x$ goes to class 1, the nearest mean. The terms $\log P(\omega_i)$ and $-\tfrac12\log\lvert\Sigma_i\rvert$ are gone because they are equal for both classes.
> - A: $\sigma=2$ was used in place of $\sigma^2=4$: $-5/4$ and $-9/4$.
> - C: The values are right, but the more negative one was taken as the larger. $-1.125$ is smaller than $-0.625$.
> - D: The minus sign was dropped. Then the largest "score" is the largest distance, the far class.

### Q8 (slide 9)

Two features both have variance 2 and covariance 1, so $\Sigma=\begin{pmatrix}2&1\\1&2\end{pmatrix}$. The mean is $\mu=(0,0)$. The points $a=(1,1)$ and $b=(1,-1)$ have the same squared Euclidean distance from $\mu$, namely 2. The squared Mahalanobis distance is $(x-\mu)^{\mathsf T}\Sigma^{-1}(x-\mu)$. Which result is correct?

- A) Mahalanobis: 6 for $a$ and 2 for $b$, so $b$ is closer.
- B) Mahalanobis: 1 for $a$ and 1 for $b$, so they are equally far.
- C) Mahalanobis: 0.67 for $a$ and 2 for $b$, so $a$ is closer.
- D) Mahalanobis: 2 for $a$ and 6 for $b$, so $a$ is closer.

> [!success]- Answer
> **C.** $\det\Sigma=2\cdot2-1\cdot1=3$, so $\Sigma^{-1}=\tfrac13\begin{pmatrix}2&-1\\-1&2\end{pmatrix}$.
> $a^{\mathsf T}\Sigma^{-1}a=\tfrac13(2-1-1+2)=\tfrac23\approx0.67$. $b^{\mathsf T}\Sigma^{-1}b=\tfrac13(2+1+1+2)=2$.
> $a$ lies along the direction in which the two features vary together, so a step there counts less. The Euclidean distance cannot see this: it gives 2 for both.
> - A: $\Sigma$ was used in place of $\Sigma^{-1}$: $a^{\mathsf T}\Sigma a=6$, $b^{\mathsf T}\Sigma b=2$.
> - B: The covariance 1 was ignored, so $\Sigma^{-1}=\tfrac12I$. That is still Euclidean up to a scale.
> - D: The factor $\tfrac13=1/\det\Sigma$ of the inverse was forgotten. The order is right, but the values are 3 times too large.

### Q9 (slide 10)

A three-class Gaussian classifier is shown as contour plots of $p(x\mid\omega_i)$. In one panel the contours of the three classes are ellipses (not circles) with the same shape, size and orientation. Only their centres differ. Which statement is correct?

- A) This is Case 1 ($\Sigma_i=\sigma^2I$), and the boundaries are straight lines, because all classes have the same covariance.
- B) This is the general case (a $\Sigma_i$ for each class), and the boundaries are curves, because the contours are not circles.
- C) This is Case 2 (one shared $\Sigma$), and the boundaries are curves, because the Mahalanobis distance is quadratic in $x$.
- D) This is Case 2 (one shared $\Sigma$), and the boundaries are straight lines, because the term $x^{\mathsf T}\Sigma^{-1}x$ cancels.

> [!success]- Answer
> **D.** Same ellipses for all classes means one shared covariance $\Sigma$: Case 2. Expanding $-\tfrac12(x-\mu_i)^{\mathsf T}\Sigma^{-1}(x-\mu_i)$ gives $-\tfrac12x^{\mathsf T}\Sigma^{-1}x$, which is the same for every class and cancels in $d_i(x)=d_j(x)$. What is left is linear in $x$.
> - A: $\sigma^2I$ gives circles. Ellipses need different variances along different directions, so this is not Case 1.
> - B: Ellipses alone do not make the general case. The general case needs different ellipses for different classes.
> - C: Each $d_i$ is quadratic, but the quadratic part is the same for all classes and cancels in the boundary.

## 3. k nearest neighbours (slides 11-13)

### Q10 (slide 11)

A kNN classifier with $k=7$ finds 4 neighbours of class $\omega_2$, 2 of $\omega_1$ and 1 of $\omega_3$. kNN also gives an estimate of the posteriors. Which statement is correct?

- A) Decision $\omega_2$. The estimated posteriors are about 0.67, 0.33 and 0.17.
- B) Decision $\omega_2$. The estimated posteriors are about 0.57, 0.29 and 0.14.
- C) Training kNN means computing the mean of each class; $x$ then goes to the nearest mean.
- D) The vote is hard, so the posterior estimate is 1 for $\omega_2$ and 0 for the other classes.

> [!success]- Answer
> **B.** The class with the most votes wins: $\omega_2$. The estimates are $k_i/k$: $4/7\approx0.57$, $2/7\approx0.29$, $1/7\approx0.14$. They sum to 1.
> - A: The votes were divided by $k-1=6$, so $k$ was counted wrong. The values sum to 1.17, which is impossible for posteriors.
> - C: That is the nearest-mean classifier. kNN has no real training: it stores the labelled examples and does all the work when a new $x$ arrives.
> - D: The decision is hard, but the vote fractions $k_i/k$ are kept as soft posterior estimates.

### Q11 (slide 12)

A kNN classifier has $n$ training examples. Someone says: "A larger $k$ is always better, because the vote is taken over more examples." In another problem the Bayes error is 0.08. Which statement is correct?

- A) With $k=n$ every new example gets the class with the most training examples, as with the priors alone.
- B) With $k=n$ the vote uses all the data, so the posterior estimates and the decisions are the best possible.
- C) For $n\to\infty$ the 1NN error lies between 0.04 and 0.08, half the Bayes error up to the Bayes error.
- D) With $k=1$ the decision regions are the smoothest, because a single example decides each point.

> [!success]- Answer
> **A.** With $k=n$ every training example votes for every new example, so the answer is always the largest class. The observation is ignored, as in the "prior only" rule. A larger $k$ helps only while the neighbours stay close to $x$.
> For $n\to\infty$: $P_{\text{Bayes}}\le P_{1\text{NN}}\le2P_{\text{Bayes}}$, so the 1NN error lies between 0.08 and 0.16.
> - B: Far examples vote too, so the estimate is no longer local. It is the same for every $x$.
> - C: The bound was halved instead of doubled. No classifier beats the Bayes error, so the 1NN error is at least 0.08.
> - D: It is the other way round. With $k=1$ one noisy example changes the result, so the regions are the most ragged.

### Q12 (slide 13)

![[cs568_lecture_11_l11_p013.jpg]]

Look at the bottom row. Left: the true layout of three classes (green, red, blue) as nine blobs on a $3\times3$ grid. Middle: the training examples, drawn as their class numbers 1, 2, 3. Right: the decision regions of kNN. Which statement is correct?

- A) One Gaussian per class has a single peak and cannot describe three separate blobs. The edges are ragged because Euclidean distance always gives jagged boundaries.
- B) The quadratic classifier fails because the classes share one covariance, so its boundaries must be straight. The edges are ragged because they depend on where the finite training examples lie.
- C) The quadratic classifier fails because it can separate only two classes. The edges are ragged because Euclidean distance always gives jagged boundaries.
- D) One Gaussian per class has a single peak and cannot describe three separate blobs. The edges are ragged because they depend on where the finite training examples lie.

> [!success]- Answer
> **D.** Each class is three blobs in different places. A Gaussian has one peak, so one Gaussian per class cannot fit it. kNN makes no assumption about the shape: each blob is handled by its own neighbours.
> The edges follow the exact positions of the finite training examples near the boundary. Other examples would give other edges, and more data would make them smoother.
> - A: The first part is right. The distance is not the cause: with more data the same Euclidean kNN gives smoother edges.
> - B: The second part is right. The quadratic classifier has a $\Sigma_i$ for each class, and its boundaries can be curved. The problem is the single peak per class.
> - C: Both parts are wrong. A Gaussian classifier handles any number of classes, and the ragged edges come from the finite data, not from the distance.

## 4. Curse of dimensionality (slides 14-17)

### Q13 (slide 14)

A density is estimated with a histogram that has 10 bins on each feature axis. There are 1000 training examples. Which statement is correct?

- A) With 3 features there are 30 cells, so each cell still holds about 33 examples on average.
- B) With 3 features there are 1000 cells, about 1 example each; the real problem is 100 times more memory.
- C) With 3 features there are 1000 cells, about 1 example each, so the same data cannot train the model.
- D) With 3 features there are 1000 cells; extra features make the classes overlap, even with unlimited data.

> [!success]- Answer
> **C.** The number of cells is $10^d$: 10, 100, 1000 for $d=1,2,3$. Examples per cell: $1000/10=100$, $1000/100=10$, $1000/1000=1$.
> With one example per cell the estimate in each cell is very unreliable. The features are not bad: the same training set cannot fill the larger space. This is why the slide calls the curse "an issue of trainability".
> - A: The cells were counted as $10\cdot d$. They multiply: $10\cdot10\cdot10$.
> - B: The count is right, but the curse is not about memory or speed. It is about too few examples per cell.
> - D: Extra features do not hurt with unlimited data. They can only add information. The loss comes from the finite training set.

### Q14 (slide 15)

![[cs568_lecture_11_l11_p015.jpg]]

The figure shows the mean recognition accuracy of a two-class problem against the measurement complexity $n$, with one curve for each training set size $m$. Which reading is correct?

- A) The curve $m=1000$ peaks at about 0.75, at about $n=1000$.
- B) The curve $m=1000$ peaks at about 0.73, at $n$ of some tens.
- C) All finite-$m$ curves peak at about the same $n\approx5$.
- D) The curve $m=2$ has no peak: it falls from $n=1$ on.

> [!success]- Answer
> **B.** The curve $m=1000$ rises to about 0.73 to 0.74 near $n\approx20$ to 50 and then falls to about 0.67 at $n=500$.
> Every finite-$m$ curve has a peak, and more data moves the peak up and to the right. So the best number of features is not fixed: it depends on the size of the training set. Only $m=\infty$ never falls.
> - A: This reads the top curve, $m=\infty$, which keeps rising to about 0.75 at $n=1000$.
> - C: The peaks move: about $n=2$ to 3 for $m=2$, about $n=5$ for $m=20$, some tens for $m=1000$.
> - D: All the curves start at 0.50 at $n=1$. The curve $m=2$ first rises to about 0.585 near $n\approx2$ to 3.

### Q15 (slide 16)

A figure shows red and blue training points of two classes. A smooth black curve separates them but leaves a few points on the wrong side. A green curve bends around single points, so almost every training point is on its own side. Which boundary do you expect to make fewer errors on new points, and why?

- A) The black one. The small loops of the green one follow single points that are probably noise.
- B) The green one. It makes fewer training errors, and the training error estimates the error on new points.
- C) The green one. A more flexible boundary has more parameters, so it holds more information about the classes.
- D) Neither. Both were fitted to the same training points, so on average they make the same errors on new points.

> [!success]- Answer
> **A.** The green loops go around single points that are probably noise. A new point near such a loop will usually belong to the other class. This is overfitting: a small training error with a large error on new data.
> - B: The training error is a biased estimate for a flexible boundary. A fair test uses examples that were not used in training.
> - C: More parameters let the boundary fit the noise too. With a fixed training set, more flexibility can make the error on new points worse.
> - D: The same training data do not give the same behaviour on new data. The flexible boundary has learned the noise.

### Q16 (slide 17)

A system has 39 features per frame. Method A keeps 12 of the 39 features. Method B computes 12 new features, each a weighted sum of all 39. Which statement is correct?

- A) A is feature extraction and B is feature selection. With A only 12 of the 39 features have to be measured.
- B) A is feature selection and B is feature extraction. With A only 12 of the 39 features have to be measured.
- C) A is feature selection and B is feature extraction. With B only 12 of the 39 features have to be measured.
- D) A is feature extraction and B is feature selection. With B only 12 of the 39 features have to be measured.

> [!success]- Answer
> **B.** Selection keeps a subset of the original features (A). Extraction makes new features $y=f(x)$ from combinations of the old ones (B, like PCA and LDA).
> With A the other 27 features are never computed, and the kept ones keep their meaning. With B all 39 must still be measured, because every new feature uses all of them.
> - A: The names are swapped. Keeping a subset is selection.
> - C: The names are right, but B needs all 39 inputs to form its weighted sums.
> - D: The names are swapped, and B needs all 39 inputs.

## 5. PCA and LDA (slides 18-22)

### Q17 (slide 18)

Two classes form two long, thin clouds of points. Both clouds run in the same direction and lie side by side. The data are projected on the first PCA axis and on the LDA axis, and a histogram of each class is drawn on each axis. Which statement is correct?

- A) On the PCA axis the two histograms are separated, because the direction of largest variance is where the class means differ most.
- B) On the PCA axis the two histograms overlap. PCA keeps the direction of largest total variance and does not use the labels.
- C) On the PCA axis the two histograms are separated, because PCA keeps the direction of smallest variance, across the clouds.
- D) On the PCA axis the two histograms overlap, because PCA rescales the data so that every direction has the same variance.

> [!success]- Answer
> **B.** Both clouds are long in the same direction, so the total variance is largest along the clouds. PCA picks that axis without looking at the labels, and there the two classes lie on top of each other. The classes differ across the clouds, where the variance is small. LDA uses the labels and picks that direction.
> - A: High variance does not mean class separation. Here the long direction is the spread inside each class.
> - C: PCA keeps the largest variance, not the smallest.
> - D: PCA rotates the data onto the eigenvectors. It does not make all variances equal.

### Q18 (slide 19)

Two features both have variance 3 and covariance 1, so $\Sigma=\begin{pmatrix}3&1\\1&3\end{pmatrix}$. PCA keeps $M=1$ direction. What are the first principal direction and the fraction of the total variance it keeps?

- A) $(1,1)/\sqrt2$, about 67%.
- B) $(1,-1)/\sqrt2$, about 33%.
- C) $(1,1)/\sqrt2$, about 33%.
- D) $(1,-1)/\sqrt2$, about 67%.

> [!success]- Answer
> **A.** $\det(\Sigma-\lambda I)=(3-\lambda)^2-1=0$ gives $\lambda_1=4$, $\lambda_2=2$. For $\lambda_1=4$: $-v_a+v_b=0$, so $v_1=(1,1)/\sqrt2$.
> The variance along $v_1$ is 4. The fraction kept is $4/(4+2)\approx0.667$.
> - B: The eigenpair of the smaller eigenvalue, $\lambda_2=2$, was taken: $2/6\approx33\%$.
> - C: The direction is right, but the share was computed from the wrong eigenvalue: $\lambda_2/(\lambda_1+\lambda_2)$.
> - D: The share is right, but $-v_a+v_b=0$ was solved as $v_a=-v_b$. That gives the eigenvector of $\lambda_2$.

### Q19 (slide 20)

One feature, two classes: $\omega_1=\{1,3\}$ and $\omega_2=\{6,8\}$. The scatters are $S_W=\sum_i\sum_{x\in\omega_i}(x-\mu_i)^2$ and $S_B=\sum_iN_i(\mu_i-\mu)^2$, with $\mu_i$ the class means, $N_i$ the class sizes and $\mu$ the overall mean. What is $S_B/S_W$? In a three-class problem, what happens to this kind of ratio if one class mean moves further from the overall mean while every class keeps its spread?

- A) $S_W=4$, $S_B=12.5$, ratio 3.125. The ratio grows.
- B) $S_W=4$, $S_B=25$, ratio 6.25. The ratio falls.
- C) $S_W=4$, $S_B=25$, ratio 6.25. The ratio grows.
- D) $S_W=4$, $S_B=12.5$, ratio 3.125. The ratio falls.

> [!success]- Answer
> **C.** $\mu_1=2$, $\mu_2=7$, $\mu=4.5$. $S_W=1+1+1+1=4$. $S_B=2(2-4.5)^2+2(7-4.5)^2=12.5+12.5=25$. Ratio $25/4=6.25$.
> If a class mean moves away from $\mu$, $S_B$ grows. $S_W$ measures only the spread of each class around its own mean, so it stays the same. The ratio grows: the classes are easier to separate.
> - A: The class sizes $N_i=2$ were forgotten in $S_B$: $6.25+6.25=12.5$.
> - B: The numbers are right. But a far class mean does not enter $S_W$, so the ratio cannot fall.
> - D: Two slips: $N_i$ forgotten in $S_B$, and the ratio said to fall.

### Q20 (slide 21)

Two classes have exactly the same mean. Class 1 has a small variance and class 2 a large variance. LDA maximises $J(W)=\lvert W^{\mathsf T}S_BW\rvert/\lvert W^{\mathsf T}S_WW\rvert$, with $S_B$ the between-class scatter and $S_W$ the within-class scatter. Which statement is correct?

- A) $S_B$ is large because the variances differ, so LDA finds a direction that separates the classes well.
- B) $S_B=0$, so LDA picks the direction with the smallest $S_W$, the one where class 1 is most compact.
- C) With three classes instead of two, LDA could give up to three directions, one for each class mean.
- D) $S_B=0$, so $J(W)=0$ for every $W$. The class information is in the variances, which LDA does not use.

> [!success]- Answer
> **D.** Every $\mu_i-\mu$ is zero, so $S_B=0$ and $J(W)=0$ for every projection. LDA finds no useful direction. This is the slide's limit "information not in the mean of the data".
> - A: $S_B$ is built only from the class means. Different variances enter $S_W$, not $S_B$.
> - B: With $S_B=0$ every direction gives $J=0$. A small $S_W$ cannot help when the numerator is zero.
> - C: The vectors $\mu_i-\mu$ have a weighted sum of zero, so only $C-1$ of them are independent. Three classes give at most two directions.

## 6. Gaussian mixture models and EM (slides 23-29)

### Q21 (slide 23)

The methods are: quadratic classifier, kNN, PCA, LDA, GMM fitted with EM, k-means. Which split into supervised and unsupervised methods is correct?

- A) Supervised: quadratic classifier, kNN, LDA. Unsupervised: PCA, GMM with EM, k-means.
- B) Supervised: quadratic classifier, PCA, LDA. Unsupervised: kNN, GMM with EM, k-means.
- C) Supervised: quadratic classifier, kNN, PCA, LDA. Unsupervised: GMM with EM, k-means.
- D) Supervised: quadratic classifier, LDA. Unsupervised: kNN, PCA, GMM with EM, k-means.

> [!success]- Answer
> **A.** The test: does the method use the class labels of the training examples? The quadratic classifier needs a mean and a covariance per class. kNN neighbours vote with their labels. LDA needs the classes for $S_W$ and $S_B$. PCA, GMM-EM and k-means use only the $x$ values.
> - B: kNN and PCA are swapped. kNN cannot vote without labels, and PCA uses only the covariance of all the data.
> - C: PCA was put with the supervised methods because it is often used before a classifier. It never looks at the labels.
> - D: kNN was confused with k-means. They share only the letter $k$: kNN uses labelled neighbours.

### Q22 (slide 24)

A mixture $p(x\mid\theta)=\sum_c p(x\mid\theta_c)\,p(\theta_c)$ has two Gaussian components with mixing weights $p(\theta_1)=0.3$ and $p(\theta_2)=0.7$. At a point $x$ the component densities are $p(x\mid\theta_1)=0.2$ and $p(x\mid\theta_2)=0.05$. What is $p(x\mid\theta)$, and why is a GMM called semi-parametric?

- A) 0.155. Each component has a fixed Gaussian form, but enough components can give almost any shape.
- B) 0.095. Each component has a fixed Gaussian form, but enough components can give almost any shape.
- C) 0.095. The means are estimated from the data, but the covariances and the weights are set by hand.
- D) 0.155. The means are estimated from the data, but the covariances and the weights are set by hand.

> [!success]- Answer
> **B.** $p(x\mid\theta)=0.3\cdot0.2+0.7\cdot0.05=0.06+0.035=0.095$.
> Each component is parametric (a Gaussian), but a sum of enough components can take almost any shape, like a non-parametric estimate. The model lies between the two.
> - A: The weights were paired with the wrong densities: $0.7\cdot0.2+0.3\cdot0.05=0.155$.
> - C: EM estimates the means, the covariances and the weights from the data. "Semi" refers to the flexible shape, not to fixed parameters.
> - D: Two slips: the weights paired with the wrong densities, and the wrong reason for "semi-parametric".

### Q23 (slide 25)

EM for a one-dimensional GMM. The data are $x_1=0$, $x_2=2$, $x_3=6$. The E-step gives the responsibilities of component 1: $p(\theta_1\mid x_i)=0.9$, 0.6 and 0.1. What does the M-step give for the new mean $\hat\mu_1$ and the new mixing weight $\hat p(\theta_1)$?

- A) $\hat\mu_1=0.6$, $\hat p(\theta_1)\approx0.533$.
- B) $\hat\mu_1=1.125$, $\hat p(\theta_1)=1$.
- C) $\hat\mu_1=1.125$, $\hat p(\theta_1)\approx0.533$.
- D) $\hat\mu_1=0.6$, $\hat p(\theta_1)=1$.

> [!success]- Answer
> **C.** The sum of the responsibilities is $0.9+0.6+0.1=1.6$.
> $\hat\mu_1=\dfrac{0.9\cdot0+0.6\cdot2+0.1\cdot6}{1.6}=\dfrac{1.8}{1.6}=1.125$, a weighted average. $\hat p(\theta_1)=1.6/3\approx0.533$, the mean responsibility.
> - A: The weighted sum 1.8 was divided by $n=3$, not by the sum of the responsibilities 1.6. Then the weights are not normalised.
> - B: The mean is right, but the weight was divided by the sum of the responsibilities, like the mean: $1.6/1.6=1$. Then every component would get weight 1.
> - D: The two denominators were swapped: the mean was divided by $n=3$ ($1.8/3=0.6$) and the weight by the sum 1.6 ($1.6/1.6=1$).

### Q24 (slide 26)

For a GMM, setting the derivatives of the log-likelihood to zero gives equations for $\hat\mu_c$, $\hat\sigma_c^2$ and $\hat p(\theta_c)$ with an equal sign. Their right-hand sides contain the responsibilities $p(\theta_c\mid x_i)$. Which statement is correct?

- A) The responsibilities are computed from the same unknown parameters, so the equations are coupled. Iterating them never lowers the log-likelihood.
- B) The responsibilities are fixed by the data, but the sums have no closed form. Iterating the equations never lowers the log-likelihood.
- C) The responsibilities are computed from the same unknown parameters, so the equations are coupled. Iterating them reaches the global maximum from any start.
- D) The responsibilities are fixed by the data, but the sums have no closed form. Iterating them reaches the global maximum from any start.

> [!success]- Answer
> **A.** By Bayes rule, $p(\theta_c\mid x_i)$ is computed from $\mu_c$, $\Sigma_c$ and $p(\theta_c)$, the unknowns themselves. So the unknowns stand on both sides. The fix: put the old values on the right, read new values on the left, repeat. This is EM, and the log-likelihood does not go down from one iteration to the next.
> - B: The responsibilities are not data. They change whenever the parameters change.
> - C: EM promises only a non-decreasing likelihood. It stops at a local maximum that depends on the start.
> - D: Two slips: the responsibilities change whenever the parameters change, and EM reaches only a local maximum that depends on the start.

### Q25 (slide 27)

EM for a GMM with full covariances. $(k-1)$ marks the old iteration and $(k)$ the new one. The covariance update is $\Sigma_c=\dfrac{\sum_ip(\theta_c\mid x_i)\,(x_i-\mu_c)(x_i-\mu_c)^{\mathsf T}}{\sum_ip(\theta_c\mid x_i)}$, written here without iteration labels. Which statement is correct?

- A) Every quantity in this update comes from iteration $(k-1)$, so the weight, the mean and the covariance can be updated in any order.
- B) The responsibilities are computed again with the new mean $\mu_c^{(k)}$, and then $\Sigma_c^{(k)}$ is computed with them.
- C) The responsibilities come from iteration $(k-1)$ and the mean inside the update from $(k)$, so the new mean is computed first.
- D) This update has the same form as for one variance per component: the squared distance divided by the dimension $d$.

> [!success]- Answer
> **C.** The responsibilities are computed once, in the E-step, with the old parameters $(k-1)$. The covariance update uses the new mean $\mu_c^{(k)}$. So the order is: responsibilities, then the new weight and mean, then the new covariance.
> - A: The mean inside the update is the new one, $\mu_c^{(k)}$. The covariance must wait for it.
> - B: The responsibilities are not recomputed inside one M-step. They are updated only in the next E-step.
> - D: With one variance per component the update uses $\lVert x_i-\mu_c\rVert^2/d$. Here it uses the outer product $(x_i-\mu_c)(x_i-\mu_c)^{\mathsf T}$, a full matrix.

### Q26 (slides 28-29)

![[cs568_lecture_11_l11_p029.jpg]]

EM fits a GMM with 30 components to 900 points that fill a ring. At every iteration the covariances are regularised (a small multiple of $I$ is added) and components with a very small weight are removed. The figure shows the components (red ellipses) at six iterations. Which statement is correct?

- A) The covariances are regularised only to make EM converge in fewer iterations; the result would be the same without it.
- B) Without regularisation a component can shrink onto a few points: $\Sigma_c\to0$ and the likelihood grows without limit.
- C) Components with a small weight are removed because EM needs all the components to keep equal mixing weights.
- D) The final ellipses are long along the ring because the regularisation adds the same variance in every direction.

> [!success]- Answer
> **B.** A component that sits on one or a few points gets a covariance near zero. Its density there grows without limit, so the likelihood does too, and $\Sigma_c$ can no longer be inverted. Adding a small multiple of $I$ keeps every variance above zero.
> - A: Regularisation is there to prevent this collapse, not for speed. Without it EM can run into the singularity.
> - C: EM estimates unequal weights. A component with a tiny weight is removed because it explains almost no data and costs computation. In the figure the number of ellipses falls from 30 to six.
> - D: Adding the same variance in every direction makes the ellipses rounder. They are long along the ring because each component covers a piece of the ring.

## 7. k-means and vector quantisation (slides 30-32)

### Q27 (slide 30)

k-means with $C=2$ on the points $(0,0)$, $(0,2)$, $(4,0)$, $(4,2)$, $(5,0)$. The initial centres are $(0,0)$ and $(0,2)$. Do one pass: assign each point to the nearest centre, then compute the cluster means. What is $J_{\mathrm{MSE}}=\sum_c\sum_{x\in\omega_c}\lVert x-\mu_c\rVert^2$ after this pass, and does the algorithm stop here?

- A) $J_{\mathrm{MSE}}=57$. It does not stop: a point changes cluster.
- B) $J_{\mathrm{MSE}}=22$. It stops: no point changes cluster.
- C) $J_{\mathrm{MSE}}=10$. It stops: no point changes cluster.
- D) $J_{\mathrm{MSE}}=22$. It does not stop: a point changes cluster.

> [!success]- Answer
> **D.** Squared distances to the two centres: $(0,0)$: 0, 4. $(0,2)$: 4, 0. $(4,0)$: 16, 20. $(4,2)$: 20, 16. $(5,0)$: 25, 29. Clusters $\{(0,0),(4,0),(5,0)\}$ and $\{(0,2),(4,2)\}$, means $(3,0)$ and $(2,2)$.
> $J_{\mathrm{MSE}}=(9+1+4)+(4+4)=22$. With the new means, $(0,0)$ is at 9 from $(3,0)$ and 8 from $(2,2)$, so it changes cluster.
> - A: $J$ was computed with the old centres: $(0+16+25)+(0+16)=57$.
> - B: The check was made against the old centres, so the same assignment came back. It must use the new means.
> - C: The city-block distance $\lvert\Delta x_1\rvert+\lvert\Delta x_2\rvert$ was used in place of the squared Euclidean one: $J=3+1+2+2+2=10$, and $(0,0)$ stays (3 against 4).

### Q28 (slide 31)

A two-channel signal is coded in two ways: (a) each channel separately with 4 bits; (b) as one 2-D vector, with a codebook of 64 codewords built by k-means. Which statement is correct?

- A) (b) costs 12 bits per vector: a 6-bit codebook index for each of the two channels.
- B) In (b) only the index is sent, and nothing is lost, because the receiver has the same codebook.
- C) (a) costs 8 bits and (b) 6 bits per vector; (b) loses the difference between a vector and its codeword.
- D) (b) saves bits because it quantises each channel with 3 bits, that is 8 levels, instead of 4 bits.

> [!success]- Answer
> **C.** (a) $4+4=8$ bits per vector. (b) $64=2^6$, so the index of the nearest codeword needs 6 bits per vector. The receiver looks the codeword up. Every vector in one Voronoi region becomes the same codeword, so the quantisation error is lost.
> - A: One index codes the whole vector, not each channel. The 6 bits are for both channels together.
> - B: The receiver gets the codeword, not the original vector. The difference between them cannot be recovered.
> - D: (b) does not quantise the channels separately. It saves bits by placing the codewords where the data really are.
