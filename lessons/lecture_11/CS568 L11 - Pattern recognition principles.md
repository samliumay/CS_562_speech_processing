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

> [!info] The three parts of Bayes rule and the two kinds of risk (beyond the slides)
> - **Prior** (önsel olasılık) $P(\omega_i)$: how probable class $\omega_i$ is before we observe anything. It is a probability, so the priors sum to 1.
> - **Likelihood** (olabilirlik) $p(x\mid\omega_i)$: how well class $\omega_i$ explains the observation $x$. It is a density in $x$. For a fixed $x$ the likelihoods of the classes do not have to sum to 1.
> - **Evidence** $p(x)=\sum_k p(x\mid\omega_k)P(\omega_k)$: the sum of all the numerators. It only scales the posteriors so that they sum to 1.
> - **Posterior** (sonsal olasılık) $P(\omega_i\mid x)$: how probable class $\omega_i$ is after we have seen $x$.
>
> The evidence is the same number for every class. Dividing all the scores by the same positive number does not change which one is the largest. So the decision needs only the product $p(x\mid\omega_i)P(\omega_i)$. The evidence is needed only when we want the posterior values themselves.
>
> Slide 4 has two risks. The **conditional risk** $R(\delta_i\mid x)$ is the expected loss of decision $\delta_i$ for one given $x$. The **overall risk** $R=\int R(\delta(x)\mid x)\,p(x)\,dx$ is the average of the conditional risk over all the $x$ that can occur. Here $p(x)\ge 0$, so the integral is smallest when the integrand is smallest at every $x$. This is why choosing the smallest conditional risk for each $x$ separately gives the smallest overall risk.

> [!warning] Slide wording (slide 5)
> The last line of slide 5 says "in order to minimize classification rate". The word "error" is missing: it means the classification **error** rate. We minimise the error rate, which is the same as maximising the rate of correct classification.

> [!question]- Check (slide 2): The priors are UP 0.45, DOWN 0.35, UNCHANGED 0.20, and we have no observation. What do we predict, how often are we wrong, and why does the slide call this decision unreasonable?
> We choose the class with the largest prior: UP, every day.
> We are right with probability 0.45, so the error probability is $1-0.45=0.55$.
> It is the best we can do with the priors alone, but the prediction never changes. The index really goes down or stays the same on many days, and this rule can never say so. We need an observation $x$ that makes the decision depend on the day.

> [!question]- Check (slide 3): In $P(\omega_i\mid x)=\dfrac{p(x\mid\omega_i)P(\omega_i)}{p(x)}$ name the four terms. Why does the second $\arg\max$ on the slide have no $p(x)$?
> $P(\omega_i\mid x)$ is the posterior: the probability of class $\omega_i$ after seeing $x$.
> $p(x\mid\omega_i)$ is the likelihood: how probable the observation $x$ is when the class is $\omega_i$.
> $P(\omega_i)$ is the prior: the probability of the class before any observation.
> $p(x)=\sum_k p(x\mid\omega_k)P(\omega_k)$ is the evidence: a normalising constant.
> $p(x)$ does not depend on $i$. It divides every class score by the same positive number, so the class with the largest score stays the same. The decision can drop it.

> [!question]- Check (slide 4): The slide uses two sets, the classes $\Omega$ (size $s$) and the decisions $\Delta$ (size $t$). Why are they two different sets? In $R(\delta_i\mid x)=\sum_j l(\delta_i\mid\omega_j)P(\omega_j\mid x)$, what is summed and what is the weight?
> A decision is what we do, a class is what is true. They do not have to match one to one. Example: the classes are UP, DOWN, UNCHANGED ($s=3$), and the decisions are buy, sell, do nothing, ask for more data ($t=4$).
> The sum goes over all the possible true classes $\omega_j$. Each term is the loss of decision $\delta_i$ if the truth is $\omega_j$, weighted by the posterior probability that the truth is $\omega_j$.
> So $R(\delta_i\mid x)$ is the average loss of decision $\delta_i$ for this $x$. We choose the decision with the smallest value.

> [!question]- Check (slide 5): Three classes have posteriors 0.5, 0.3, 0.2. Write the conditional risk of each decision under the zero-one loss. Which step of the slide's derivation turns the sum into $1-P(\omega_i\mid x)$?
> Zero-one loss: the term $j=i$ has loss 0 and every other term has loss 1. So $R(\delta_i\mid x)=\sum_{j\neq i}P(\omega_j\mid x)$.
> The posteriors sum to 1, so the sum of all of them except number $i$ is $1-P(\omega_i\mid x)$.
> $R(\delta_1\mid x)=1-0.5=0.5$, $R(\delta_2\mid x)=1-0.3=0.7$, $R(\delta_3\mid x)=1-0.2=0.8$.
> The smallest risk is $\delta_1$: the class with the largest posterior. Under this loss the conditional risk is the probability of an error.

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

> [!info] What each term of the discriminant does, and why a shared covariance gives a straight boundary (beyond the slides)
> Symbols on slide 7: $x$ is the feature (öznitelik) vector, $\mu_i$ the mean of class $i$, $\Sigma_i$ its covariance matrix (kovaryans matrisi), $|\Sigma_i|$ the determinant. The slide writes the dimension of $x$ as $n$ in the Gaussian and as $d$ in the log. It is the same number.
>
> The log posterior on slide 7 has four terms:
> - $-\tfrac12(x-\mu_i)^{\mathsf T}\Sigma_i^{-1}(x-\mu_i)$: minus half the squared Mahalanobis distance from $x$ to the class mean. A class far from $x$ gets a low score.
> - $\log P(\omega_i)$: a bonus for a class that is frequent.
> - $-\tfrac12\log|\Sigma_i|$: a penalty for a wide class. A wide Gaussian spreads its probability over a large volume, so its density at any one point is lower.
> - $-\tfrac d2\log 2\pi$: a constant. It is the same for every class, so slide 8 removes it. The evidence $p(x)$ was already removed by the sign $\propto$.
>
> Why the decision boundary (karar sınırı) is linear in Case 2. Expand the quadratic form with the shared $\Sigma$:
>
> $$d_i(x)=-\tfrac12x^{\mathsf T}\Sigma^{-1}x+\mu_i^{\mathsf T}\Sigma^{-1}x-\tfrac12\mu_i^{\mathsf T}\Sigma^{-1}\mu_i$$
>
> The first term has no $i$ in it. It is the same for every class and can be dropped. What is left is linear in $x$. The boundary between classes $i$ and $j$ is the set of points with $d_i(x)=d_j(x)$:
>
> $$(\mu_i-\mu_j)^{\mathsf T}\Sigma^{-1}x=\tfrac12\left(\mu_i^{\mathsf T}\Sigma^{-1}\mu_i-\mu_j^{\mathsf T}\Sigma^{-1}\mu_j\right)$$
>
> This is the equation of a line (a hyperplane in more dimensions). Case 1 is the same with $\Sigma=\sigma^2I$. In the general case the term $x^{\mathsf T}\Sigma_i^{-1}x$ is different for each class, it does not cancel, and the boundary is a quadratic curve (parabola, ellipse, hyperbola).
>
> On slide 8 the Case 1 formula writes $(x-\mu_i)^2$. For a vector this means the squared length $\lVert x-\mu_i\rVert^2=(x-\mu_i)^{\mathsf T}(x-\mu_i)$.

> [!question]- Check (slide 6): The slide lists three ways to go on when $p(x\mid\omega_i)$ is unknown. Put the quadratic classifier, kNN and the support vector machine in the right group, and say what each group must get from the training data.
> Group 1, estimate the density itself from the data without a fixed form: kNN (also Naive Bayes on the slide). It needs the training examples themselves.
> Group 2, assume a form for the density and estimate its parameters: the quadratic classifier. It assumes a Gaussian and needs only $\mu_i$ and $\Sigma_i$ for each class.
> Group 3, ignore the density and separate the data geometrically: the support vector machine and the perceptron. They learn the boundary directly.
> Group 2 needs the least data, but it is only as good as its assumption. Group 1 makes no assumption, but it needs much more data.

> [!question]- Check (slide 7): The log posterior on the slide has four terms. Say what each one measures. Which of them can never change the decision?
> $-\tfrac12(x-\mu_i)^{\mathsf T}\Sigma_i^{-1}(x-\mu_i)$: how far $x$ is from the mean of class $i$, measured with the covariance of that class.
> $\log P(\omega_i)$: how frequent the class is.
> $-\tfrac12\log|\Sigma_i|$: how spread out the class is. A larger spread gives a lower score.
> $-\tfrac d2\log2\pi$: a constant that comes from the normalising factor of the Gaussian.
> Only the last one can never change the decision, because it is the same for every class. The other three all contain the index $i$.

> [!question]- Check (slide 8): Case 1 with $\sigma^2=4$, $\mu_1=(0,0)$, $\mu_2=(4,2)$, equal priors, $x=(1,2)$. Compute $d_1(x)$ and $d_2(x)$ and classify. Which two terms of the general discriminant have disappeared here, and why?
> $\lVert x-\mu_1\rVert^2=1^2+2^2=5$, so $d_1(x)=-\dfrac{5}{2\cdot4}=-0.625$.
> $\lVert x-\mu_2\rVert^2=(-3)^2+0^2=9$, so $d_2(x)=-\dfrac{9}{2\cdot4}=-1.125$.
> $d_1$ is larger, so $x$ goes to class 1. It is the class with the nearest mean.
> $\log P(\omega_i)$ disappeared because the priors are equal. $-\tfrac12\log|\Sigma_i|$ disappeared because every class has the same $\Sigma_i=\sigma^2I$, so the determinant is the same for all classes.
> The factor $\frac{1}{2\sigma^2}$ is also the same for all classes. It changes the values of $d_i$ but not the decision.

> [!question]- Check (slide 9): In the figure, which of $x_1$ and $x_2$ is closer to $\mu$ in the Euclidean sense, and which in the Mahalanobis sense? Then compute both squared distances from $\mu=(0,0)$ for $a=(1,1)$ and $b=(1,-1)$, when both features have variance 2 and their covariance is 1.
> The covariance matrix is $\Sigma=\begin{pmatrix}2&1\\1&2\end{pmatrix}$.
> Figure: the red circle is the set of points with the same Euclidean distance to $\mu$. $x_1$ lies on it and $x_2$ lies outside it, so $x_1$ is closer in the Euclidean sense. The blue ellipse is the set of points with the same Mahalanobis distance. Both points lie on it ($x_1$ is drawn where the circle and the ellipse cross), so they are equally far in the Mahalanobis sense. $x_2$ lies near the long axis of the ellipse, where the data vary a lot, so a long step there counts less.
> Numbers: Euclidean, $\lVert a\rVert^2=1+1=2$ and $\lVert b\rVert^2=1+1=2$. They are equal.
> $\Sigma^{-1}=\dfrac13\begin{pmatrix}2&-1\\-1&2\end{pmatrix}$ (determinant $2\cdot2-1\cdot1=3$).
> $a^{\mathsf T}\Sigma^{-1}a=\tfrac13(2-1-1+2)=\tfrac23\approx0.67$.
> $b^{\mathsf T}\Sigma^{-1}b=\tfrac13(2+1+1+2)=\tfrac63=2$.
> $a$ lies along the direction in which the two features vary together (positive correlation), so it is closer in the Mahalanobis sense. The two distances always put points in the same order only when $\Sigma$ is a multiple of the identity matrix.

> [!question]- Check (slide 10): Look only at the three contour plots in the bottom row. How can you tell from the contours which panel is Case 1, Case 2 and the general case? What is different about the black boundaries in the third panel?
> Case 1: the contours of all three classes are circles of the same size ($\Sigma_i=\sigma^2I$). The boundaries are three straight lines that meet in one point.
> Case 2: the contours are ellipses with the same shape and the same orientation for all three classes (one shared $\Sigma$). The boundaries are still straight lines.
> General case: each class has its own ellipses. The green one is large and elongated, the blue and red ones are smaller with other shapes. The boundaries are curved, and one class region can appear in more than one piece.
> Reason: with a shared covariance the quadratic term in $x$ cancels between two classes. With separate covariances it stays, so the boundary is a quadratic curve.

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

> [!info] Reading the figure on slide 13 (beyond the slides)
> The slide has two rows, one test problem per row, and no text. Each problem has three classes.
> - Left column: the true layout of the classes. Top: a green disc in the centre, a blue ring around it and a red ring outside. Bottom: nine blobs on a $3\times3$ grid, three blobs for each class.
> - Middle column: the training set (eğitim kümesi). Each example is drawn as its class number 1, 2 or 3. The dotted lines are the boundaries that kNN produces.
> - Right column: the decision regions. Every point of the plane is coloured with the class that kNN gives it.
>
> The regions follow the true layout: rings in the top row and a grid in the bottom row. kNN was told nothing about rings or grids. It only looks at the nearest training examples, so it can follow a boundary of any shape. The edges of the regions are ragged because they depend on where the finite training examples happen to lie. With more training data they become smoother and closer to the true layout.

> [!question]- Check (slide 11): A kNN classifier with $k=7$ finds 4 neighbours of $\omega_2$, 2 of $\omega_1$ and 1 of $\omega_3$. Give the decision and the estimated posteriors. What does "training" mean for kNN, compared with the quadratic classifier?
> Decision: $\omega_2$, because it has the most votes.
> Estimated posteriors $k_i/k$: $P(\omega_2\mid x)\approx4/7\approx0.57$, $P(\omega_1\mid x)\approx2/7\approx0.29$, $P(\omega_3\mid x)\approx1/7\approx0.14$. They sum to 1.
> kNN has no real training step. It only stores the labelled examples, and all the work (the distances to the stored examples) is done when a new $x_u$ arrives.
> The quadratic classifier does the work once, during training: it estimates $\mu_i$ and $\Sigma_i$ and then throws the data away. Classifying a new example is then cheap.

> [!question]- Check (slide 12): Find the mistake: "A larger $k$ is always better, because the vote is taken over more examples." Use the two ends $k=1$ and $k=n$ (all the training examples) in your answer. Also: the Bayes error of a problem is 0.08. What does the slide say about the 1NN error for $n\to\infty$?
> A larger $k$ gives smoother regions and better probability estimates, but only while the $k$ neighbours are still close to $x_u$. When $k$ is too large, far examples vote too, and the estimate is no longer local.
> $k=1$: the label of the single nearest example decides. One noisy or mislabelled example changes the result, so the regions are ragged.
> $k=n$: every training example votes for every $x_u$. The answer is always the class with the most training examples. This is the "prior only" decision of slide 2, and the observation is ignored.
> A good $k$ lies between the two ends. A larger $k$ also costs more computation.
> Bound: the 1NN error lies between the Bayes error and twice the Bayes error, so between 0.08 and $2\cdot0.08=0.16$.

> [!question]- Check (slide 13): In the bottom row, why would a quadratic classifier with one Gaussian per class fail, while kNN finds the nine regions? Why are the edges of the coloured regions in the right column not smooth?
> In the bottom problem each class is three separate blobs in different places of the grid. One Gaussian has a single peak, so it cannot describe three separate blobs. The quadratic classifier can only draw one quadratic curve between two classes, not a $3\times3$ grid of regions.
> kNN makes no assumption about the shape of a class. It decides from the examples near $x_u$, so each blob is handled by its own neighbours.
> The edges are ragged because the boundary depends on the exact positions of the finite training examples near it. Other training examples would give slightly different edges.

## 4. Curse of dimensionality (slides 14-17)
![[cs568_lecture_11_l11_p014.jpg]]
![[cs568_lecture_11_l11_p015.jpg]]
![[cs568_lecture_11_l11_p016.jpg]]
![[cs568_lecture_11_l11_p017.jpg]]

**Notes.** The number of examples needed to train a classifier grows exponentially with the dimension. More features give more information in theory, but in practice they raise the risk of **overfitting**: fitting the noise in the data. For a fixed sample size there is a number of features above which performance drops (Hughes phenomenon, slide 15). Slide 16: a very flexible boundary fits every training point but generalises worse than a smooth one.

How to beat it: prior knowledge (parametric models), smoothness (regularisation), or dimensionality reduction. Two kinds of reduction:
- **Feature extraction**: new features as combinations of the old ones, $y=f(x)$. Examples: PCA, LDA.
- **Feature selection**: keep a subset of the original features.

> [!info] Reading the Hughes figure on slide 15 (beyond the slides)
> - Horizontal axis (logarithmic): the measurement complexity $n$. It is the number of different values the measurement can take. A larger $n$ means a finer, richer description of each example, like using more features.
> - Vertical axis: the mean recognition accuracy of a two-class problem. Guessing gives 0.50, and all the curves start there at $n=1$.
> - Each curve is for one training set size $m$ (the number of training examples): 2, 5, 10, 20, 50, 100, 200, 500, 1000 and $\infty$.
>
> Every curve with a finite $m$ first rises and then falls. A richer measurement helps at first. After the peak there are too many cells to fill with $m$ examples, the estimates become unreliable, and the accuracy drops. This is overfitting (aşırı öğrenme). With more training data the peak is higher and it comes at a larger $n$. Only the curve $m=\infty$ never falls.
>
> Slide 14 calls the curse "an issue of trainability". The extra features do carry information. The problem is that a finite training set cannot train the larger model well.

> [!question]- Check (slide 14): We estimate a density with a histogram that has 10 bins on each feature axis, and we have 1000 training examples. How many examples fall in one cell on average for 1, 2 and 3 features? Use this to explain the sentence "the curse of dimensionality is an issue of trainability".
> The number of cells is $10^d$: 10, 100 and 1000 cells.
> Average examples per cell: $1000/10=100$, $1000/100=10$, $1000/1000=1$.
> With 3 features there is about one example in a cell, so the estimate in each cell is very unreliable. With more features most cells are empty.
> The extra features are not bad by themselves: in theory they add information. The problem is that the same training set cannot fill the larger space, so the model learns the noise of the few examples it has. That is a problem of training, not of the features.

> [!question]- Check (slide 15): Read from the figure: about where is the peak of the curve $m=2$, and about how high is the peak of the curve $m=1000$? Which curve has no peak? What does this say about the best number of features?
> $m=2$: the peak is at about 0.585, at a small complexity (about $n=2$ to 3).
> $m=1000$: the peak is at about 0.73 to 0.74, at a much larger complexity (some tens).
> $m=\infty$ has no peak. It keeps rising and reaches about 0.75 at $n=1000$.
> So for a fixed training set there is a best complexity. Beyond it the accuracy falls. More training data moves the best point to the right and upwards. The best number of features is not a fixed number: it depends on how much training data there is.

> [!question]- Check (slide 16): The figure has a black boundary and a green boundary between the red and the blue points. Which one makes fewer errors on these training points? Which one do you expect to make fewer errors on new points, and why?
> The green boundary makes fewer errors on the training points. It bends around single points, so almost every red point is on the red side and every blue point on the blue side. The black boundary leaves some points on the wrong side.
> On new points the black boundary is expected to be better. The small loops of the green line follow single points that are probably noise. A new point near such a loop will usually belong to the other class.
> This is overfitting: a small training error with a large error on new data. A fair test of a classifier must use examples that were not used in training.

> [!question]- Check (slide 17): A system has 39 features per frame. Method A keeps 12 of the 39 features. Method B computes 12 new features, each a weighted sum of all 39. Which one is feature selection and which is feature extraction? Give one difference that matters in practice. Which of the three ways to beat the curse does a Gaussian classifier use?
> A is feature selection: a subset of the original features. B is feature extraction: new features $y=f(x)$ made from combinations of the old ones. PCA and LDA are of type B.
> Difference: with A the kept features keep their meaning, and the other 27 do not have to be computed at all. With B all 39 features must still be measured, and a new feature is a mixture with no direct meaning. But B can keep information from all 39.
> A Gaussian classifier uses the first way, prior knowledge. It assumes the form of the density, so it needs only a mean and a covariance for each class.

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

> [!info] Reading the figures on slides 18 and 20 (beyond the slides)
> **Slide 18.** The points marked 1 (blue) and 2 (red) are two classes. Both clouds are long and thin, they run in the same direction, and they lie side by side. Two axes are drawn, and on each axis there is a histogram of the projected points.
> - The axis "Signal representation (PCA)" runs along the long direction of the clouds. This is the direction of largest variance (varyans). On this axis the blue and red histograms lie on top of each other: the classes are mixed.
> - The axis "Classification (LDA)" runs across the clouds. The total variance is smaller there, but the blue and red histograms stand next to each other: the classes are separated.
>
> **Slide 20.** $\mu_1,\mu_2,\mu_3$ are the class means and the cross $\mu$ is the mean of all the data. The arrows $S_{W1},S_{W2},S_{W3}$ show the spread of each class around its own mean (the size of each ellipse). The arrows $S_{B1},S_{B2},S_{B3}$ go between each class mean and the overall mean $\mu$.
> - $S_W$ (within-class scatter) adds the spreads of all the classes. Small $S_W$: each class is compact.
> - $S_B$ (between-class scatter) measures how far the class means are from the overall mean. Each class counts with its number of examples $N_i$. Large $S_B$: the classes are far apart.
> - $J(W)$ is the ratio of the two after the projection $W$. The columns of $W$ are the projection directions. $W^{\mathsf T}S_BW$ and $W^{\mathsf T}S_WW$ are the scatter matrices of the projected data, and the determinant turns each matrix into one number. LDA looks for the projection in which the class means are far apart and each class is compact, both at the same time.
>
> PCA has no labels in its formula: it needs only the covariance of all the data. LDA needs the labels, because $\mu_i$, $S_W$ and $S_B$ are computed class by class.

> [!question]- Check (slide 18): In the figure, the data are projected on two axes. On which axis do the two histograms overlap, and on which are they separated? Why does PCA choose the axis that is worse for classification here?
> On the PCA axis ("signal representation") the blue and red histograms overlap almost completely. On the LDA axis ("classification") they stand side by side.
> PCA looks for the direction with the largest variance of all the data, and it does not use the labels. Both clouds are long in the same direction, so that direction has the largest variance. But the two classes differ in the direction across the clouds, where the variance is small.
> LDA uses the labels. It looks for the direction in which the class means are far apart compared with the spread inside each class.

> [!question]- Check (slide 19): Name $y$, $A$, $v_i$ and $\lambda_i$ in the slide's three equations. Then, for two features that both have variance 3 and covariance 1, find the first principal direction, the variance along it, and the fraction of the variance kept with $M=1$.
> The covariance matrix is $\Sigma=\begin{pmatrix}3&1\\1&3\end{pmatrix}$.
> $y$: the new, shorter feature vector. $A$: the projection matrix built from the $M$ chosen eigenvectors (özvektör). $v_i$: an eigenvector of the covariance matrix $\Sigma$, one of the new axes. $\lambda_i$: its eigenvalue (özdeğer), which is the variance of the data along $v_i$.
> Eigenvalues: $\det(\Sigma-\lambda I)=(3-\lambda)^2-1=0$, so $3-\lambda=\pm1$: $\lambda_1=4$, $\lambda_2=2$.
> Eigenvector for $\lambda_1=4$: $(\Sigma-4I)v=0$ gives $-v_a+v_b=0$, so $v_1=\tfrac{1}{\sqrt2}(1,1)$. For $\lambda_2=2$: $v_2=\tfrac{1}{\sqrt2}(1,-1)$.
> The first principal direction is the diagonal $(1,1)/\sqrt2$, with variance 4.
> Fraction kept with $M=1$: $4/(4+2)=0.667$, about 67%.
> In the new axes the covariance is $\mathrm{diag}(4,2)$: the new features are uncorrelated.

> [!question]- Check (slide 20): One feature, two classes: $\omega_1=\{1,3\}$ and $\omega_2=\{6,8\}$. Compute $S_W$, $S_B$ and the ratio $S_B/S_W$. In the slide's figure, what happens to this kind of ratio if $\mu_3$ moves further away from $\mu$ while the ellipses keep their size?
> Means: $\mu_1=2$, $\mu_2=7$, overall $\mu=(1+3+6+8)/4=4.5$.
> $S_W=(1-2)^2+(3-2)^2+(6-7)^2+(8-7)^2=4$.
> $S_B=N_1(\mu_1-\mu)^2+N_2(\mu_2-\mu)^2=2(-2.5)^2+2(2.5)^2=12.5+12.5=25$.
> Ratio: $25/4=6.25$.
> If $\mu_3$ moves away, the between-class scatter $S_B$ grows and the within-class scatter $S_W$ stays the same (same ellipses). The ratio grows: the classes are easier to separate. The ratio also grows when the ellipses shrink while the means stay where they are.

> [!question]- Check (slide 21): Two classes have exactly the same mean, but class 1 has a small variance and class 2 a large variance. What is $S_B$, and what can LDA do with these data? Which limitation on the slide is this? Explain also in words why three classes give at most two LDA directions.
> Both class means are equal to the overall mean, so every term $(\mu_i-\mu)$ is zero and $S_B=0$. Then $J(W)=0$ for every projection. LDA finds no useful direction.
> This is the limitation "information not in the mean of the data". Here the classes differ in their variance, and LDA only looks at the distances between the means. The ring problem of slide 13 is of this kind.
> Three classes: $S_B$ is built from the three vectors $\mu_i-\mu$. They are tied together by the overall mean (their weighted sum is zero), so only two of them are independent. Three points always lie in one plane, and a plane has two directions. So $S_B$ has rank at most $C-1=2$, and only two eigenvalues can be different from zero.

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

> [!info] The formulas on slides 25-27 and the figure on slide 29 (beyond the slides)
> **Slide 25.** The case on this slide is a spherical Gaussian for each component: $\Sigma_c=\sigma_c^2I$, one variance $\sigma_c^2$ for all $d$ dimensions. The slide writes it as $\Sigma_i=I\sigma_i$ (index $i$ where the component index $c$ is meant, and $\sigma_i$ without the square). The three results are all weighted averages, and the weight of example $x_i$ is the responsibility $p(\theta_c\mid x_i)$:
> - $\hat\mu_c$: the weighted mean of the examples. An example that belongs mostly to component $c$ pulls its mean strongly.
> - $\hat\sigma_c^2$: the weighted mean of the squared distances $\lVert x_i-\hat\mu_c\rVert^2$, divided by $d$. The squared distance is a sum over $d$ coordinates, and each coordinate has the variance $\sigma_c^2$, so we divide by $d$ to get the variance of one coordinate.
> - $\hat p(\theta_c)$: the mean responsibility. It is the fraction of the data that component $c$ explains.
>
> If every responsibility were 0 or 1, these would be the usual sample mean, sample variance and class frequency of the examples in each group. The responsibilities play the role of the labels that we do not have.
>
> **Slide 26.** The responsibility is itself computed from $\mu_c$, $\Sigma_c$ and $p(\theta_c)$ by Bayes rule. So the unknowns stand on both sides of each equation, and we cannot solve for them in one step. The fixed-point idea: put the old parameters on the right-hand side, read the new parameters on the left, and repeat. Computing the responsibilities with the old parameters is the E-step. Computing the new parameters from them is the M-step.
>
> **Slide 27.** In the slide image the superscripts are cut off: "$(k$" means iteration $(k)$ (new), "$(k-1$" means iteration $(k-1)$ (old), and the "$(i$" on $\Sigma_c$ should also be $(k)$. The responsibilities always come from the old parameters. The new covariance uses the new mean $\mu_c^{(k)}$, so the mean is computed first.
>
> **Slide 29.** Six panels show the same 900 points (blue) at iterations 0, 25, 50, 75, 275 and 300. Each red ellipse is one Gaussian component (a contour of its covariance). At iteration 0 there are 30 large circles that overlap, because the variances start large (note that this panel has a wider axis range). Then the ellipses shrink, move onto the ring and become fewer, because components with a very small weight are removed. Seven ellipses are left at iteration 275 and six at iteration 300, spread around the ring. Each one is elongated along the ring, and none sits in the empty centre.

> [!question]- Check (slide 23): For each method say whether it is supervised or unsupervised: quadratic classifier, kNN, PCA, LDA, GMM fitted with EM, k-means. What is the single test that decides?
> The test: does the method use the class label $\omega$ of the training examples?
> Supervised (they use labels): quadratic classifier (a mean and covariance for each class), kNN (the neighbours vote with their labels), LDA ($S_W$ and $S_B$ need the classes).
> Unsupervised (only the $x$ values): PCA (covariance of all the data), GMM with EM, k-means.
> An unsupervised method cannot name the groups it finds. It only describes the structure of the data.

> [!question]- Check (slide 24): In $p(x\mid\theta)=\sum_{c=1}^{C}p(x\mid\theta_c)\,p(\theta_c)$ name the two factors. A mixture has two components with weights 0.3 and 0.7. At a point $x$ the component densities are 0.2 and 0.05. Compute $p(x\mid\theta)$. Why is the model called semi-parametric?
> $p(x\mid\theta_c)$ is the density of component $c$, a Gaussian with parameters $\theta_c=\{\mu_c,\Sigma_c\}$. $p(\theta_c)$ is the mixing weight: the probability that an example comes from component $c$. The weights sum to 1, so the mixture is again a density.
> $p(x\mid\theta)=0.3\cdot0.2+0.7\cdot0.05=0.06+0.035=0.095$.
> Semi-parametric: each component has a fixed parametric form (Gaussian), like a parametric model. But with enough components the sum can take almost any shape, like a non-parametric estimate. It lies between the two.
> The formula has the same form as the evidence $p(x)=\sum_k p(x\mid\omega_k)P(\omega_k)$ of slide 3. The components play the role of classes that nobody has labelled.

> [!question]- Check (slide 25): One-dimensional data $x_1=0$, $x_2=2$, $x_3=6$. The responsibilities of component 1 are 0.9, 0.6 and 0.1. Compute the new mean and the new mixing weight of component 1 with the slide's formulas. What would the mean be with hard assignments (responsibilities 1, 1, 0)?
> Sum of the responsibilities: $0.9+0.6+0.1=1.6$.
> $\hat\mu_1=\dfrac{0.9\cdot0+0.6\cdot2+0.1\cdot6}{1.6}=\dfrac{1.8}{1.6}=1.125$.
> $\hat p(\theta_1)=\dfrac{1.6}{3}\approx0.533$.
> Hard assignments: $\hat\mu_1=(0+2)/2=1$. That is the plain mean of the two examples in the group, as in k-means.
> The soft mean 1.125 is a little larger, because the far example $x_3=6$ still has a small weight 0.1.

> [!question]- Check (slide 26): The equations of slide 25 give $\hat\mu_c$, $\hat\sigma_c^2$ and $\hat p(\theta_c)$ with an equal sign. Why are they still not a solution? What are the three steps of the fixed-point algorithm, and what does the slide promise about it?
> The right-hand sides contain the responsibility $p(\theta_c\mid x_i)$. By Bayes rule it is computed from the same parameters $\mu_c$, $\Sigma_c$, $p(\theta_c)$ that we are looking for. So the unknowns are on both sides, for all components at once: a coupled non-linear system.
> Steps: 1. Start with some parameter values (the "old" values). 2. Put them into the right-hand side and compute the "new" values. 3. Call the new values old and repeat.
> Promise: the log-likelihood of the data does not go down from one iteration to the next. This is the EM algorithm. The slide does not promise the best possible solution. The result is a local maximum and depends on the starting values.

> [!question]- Check (slide 27): In the update for $\Sigma_c$, which quantities come from the old iteration $(k-1)$ and which from the new iteration $(k)$? In which order must the three updates be computed? How do these equations differ from those of slide 25?
> Old, $(k-1)$: all the responsibilities $p(\theta_c\mid x_i)$. They are computed once, in the E-step, with the old parameters.
> New, $(k)$: the mean $\mu_c^{(k)}$ inside the covariance formula.
> Order: first the responsibilities (E-step). Then the new weight and the new mean. Then the new covariance, because it needs the new mean.
> Difference from slide 25: there each component had one variance $\sigma_c^2$, from the squared distance divided by $d$. Here each component has a full covariance matrix, from the outer product $(x_i-\mu_c)(x_i-\mu_c)^{\mathsf T}$. The weight and the mean have the same form on both slides.

> [!question]- Check (slides 28-29): The training procedure regularises the covariances at every iteration and removes components with a very small weight. Why is each of these two steps needed? Where do you see the effect of the second one in the figure? Why do the last ellipses lie along the ring?
> Regularisation: a component can move onto one or a few examples. Then its covariance shrinks towards zero, the matrix can no longer be inverted and the likelihood grows without limit (a singularity). Adding a small multiple of the identity matrix keeps every variance above zero.
> Removal: a component with a weight near zero explains almost no data. Its parameters are estimated from almost nothing, and it only costs computation.
> In the figure the number of ellipses falls: 30 at iteration 0, clearly fewer at iterations 25 to 75, seven at iteration 275 and six at iteration 300.
> The data fill a ring. One Gaussian cannot be ring-shaped, so each component covers a piece of the ring. A piece of a ring is longer along the ring than across it, so the ellipse is elongated along the ring. No component stays in the hole, because there are no data there.

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

> [!info] k-means as a special case of EM, and the figure on slide 31 (beyond the slides)
> **Slide 30.** The slide says that k-means is a special case of the GMM-EM algorithm without showing it. Take a GMM in which every component has the same spherical covariance $\sigma^2I$ and the same weight. Then the responsibility of a component depends only on the distance from the example to its mean. Now replace the soft responsibility by a hard one: 1 for the nearest mean and 0 for all the others.
> - Step 4 of k-means (reassign each example to the nearest mean) is this hard E-step.
> - Step 3 (compute the sample mean of each cluster) is the M-step for the means. With weights 0 and 1 the weighted mean of EM becomes the plain mean of the cluster.
>
> k-means does not estimate covariances or weights. Each of its two steps can only lower $J_{\mathrm{MSE}}$ or leave it the same, so the algorithm stops. Like EM, it stops at a local optimum that depends on the initialisation.
>
> **Slide 31.** Left plot: scalar quantisation of one signal. The red curve is the continuous signal. The green staircase is the quantised signal, which can take only a few fixed levels. The blue curve at the bottom is the quantisation noise: the difference between the two. Right plot: vector quantisation in two dimensions. The crosses are the data vectors and the red dots are the codewords (the cluster centres from k-means). The blue lines cut the plane into **Voronoi regions**. One region (shaded orange) holds all the points that are closer to its codeword than to any other codeword. Every vector in a region is replaced by the index of the codeword of that region. The error of this replacement is the distance from the vector to its codeword. $J_{\mathrm{MSE}}$ is the sum of these squared errors, so k-means builds the codebook with the smallest quantisation error it can find.

> [!question]- Check (slide 30): Points $(0,0)$, $(0,2)$, $(4,0)$, $(4,2)$, $(5,0)$, $C=2$, initial centres $(0,0)$ and $(0,2)$. Do one pass of steps 4 and 3 (assign, then compute the means) and give $J_{\mathrm{MSE}}$. Does the algorithm stop here? Which GMM step does each of the two k-means steps correspond to?
> Assign, with squared distances to the two centres: $(0,0)$: 0 and 4, cluster 1. $(0,2)$: 4 and 0, cluster 2. $(4,0)$: 16 and 20, cluster 1. $(4,2)$: 20 and 16, cluster 2. $(5,0)$: 25 and 29, cluster 1.
> Means: cluster 1 $=\{(0,0),(4,0),(5,0)\}$, $\mu_1=(3,0)$. Cluster 2 $=\{(0,2),(4,2)\}$, $\mu_2=(2,2)$.
> $J_{\mathrm{MSE}}=(9+1+4)+(4+4)=22$.
> It does not stop. With the new means, $(0,0)$ is at squared distance 9 from $\mu_1$ and 8 from $\mu_2$, so it changes cluster.
> If we continue, the algorithm ends with $\{(0,0),(0,2)\}$ and $\{(4,0),(4,2),(5,0)\}$, means $(0,1)$ and $(4.33,0.67)$, $J_{\mathrm{MSE}}=5.33$. The cost went 22, 13.83, 5.33: it never rises.
> The assignment step is a hard E-step (responsibility 1 for the nearest mean, 0 for the others). The mean step is the M-step for the means.

> [!question]- Check (slide 31): In the right plot, what are the crosses, the red dots and the orange area? A two-channel signal is coded (a) with 4 bits for each channel separately, (b) as a vector with a codebook of 64 codewords. How many bits per vector in each case? What is transmitted in (b), and what is lost?
> Crosses: the data vectors. Red dots: the codewords, the cluster centres found by k-means. Orange area: one Voronoi region, all the points that are nearest to one codeword. All the codewords together are the codebook.
> (a) $4+4=8$ bits per vector. (b) $64=2^6$ codewords, so the index needs 6 bits per vector.
> In (b) only the index of the nearest codeword is transmitted. The receiver has the same codebook and looks the codeword up.
> What is lost: the difference between the vector and its codeword, the quantisation error. All the vectors in one Voronoi region become the same codeword.
> Vector quantisation can use fewer bits because it places the codewords where the data really are. Separate quantisation of each channel also spends levels on combinations of values that never occur.

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
- Quiz: [[CS568 L11 - Pattern recognition principles - quiz]]

Slides not embedded: 1
