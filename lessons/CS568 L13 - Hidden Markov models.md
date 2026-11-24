---
course: CS 568 Speech Processing
lecture: 13
title: Hidden Markov models
tags: [cs568, lecture]
slides: lectures/lecture_13/l13.pdf
full_notes: lecture_notes/lecture_13/CS568_Lecture_13_HMMs.pdf
---
# CS 568 · Lecture 13 · Hidden Markov models

> [!summary] In short
> - A Markov chain is an initial distribution $\pi$ and a transition matrix $A$ whose rows sum to 1. State durations are geometric.
> - An HMM $\lambda=(A,B,\pi)$ adds emission probabilities $B$. Only the outputs are seen, the states are hidden.
> - Problem 1, evaluation of $P(O\mid\lambda)$: Forward and Backward procedures, cost $N^2T$.
> - Problem 2, best state sequence: Viterbi (Forward with max instead of sum, plus back-pointers).
> - Problem 3, training: Baum-Welch re-estimation (EM with expected counts $\gamma$ and $\xi$).

## 1. Discrete Markov processes (slides 2-4)
![[cs568_lecture_13_l13_p002.jpg]]
![[cs568_lecture_13_l13_p003.jpg]]
![[cs568_lecture_13_l13_p004.jpg]]

**Notes.** A system is in one of $N$ states $S_1,\dots,S_N$ and moves at regular times. $q_t$ is the state at time $t$. First-order Markov assumption: the next state depends only on the current one.

$$a_{ij}=P(q_t=S_j\mid q_{t-1}=S_i),\qquad a_{ij}\ge0,\qquad \sum_{j=1}^{N}a_{ij}=1$$

Weather model: $S_1$ rain or snow, $S_2$ cloudy, $S_3$ sunny.

$$A=\begin{bmatrix}0.4&0.3&0.3\\0.2&0.6&0.2\\0.1&0.1&0.8\end{bmatrix}$$

Staying in state $S_i$ for exactly $T$ days: $a_{ii}^{T-1}(1-a_{ii})$. This is a geometric distribution with mean $1/(1-a_{ii})$.

> [!example] Slide 4
> Day 1 is sunny. Probability of "sun, sun, rain, rain, sun, clouds, sun" on the next 7 days: $\pi_3\,a_{33}\,a_{33}\,a_{31}\,a_{11}\,a_{13}\,a_{32}\,a_{23}=1\cdot0.8\cdot0.8\cdot0.1\cdot0.4\cdot0.3\cdot0.1\cdot0.2=1.536\times10^{-4}$.
> Sunny spell of exactly 3 days: $0.8^2\times0.2=0.128$. Mean sunny spell: $1/0.2=5$ days.

> [!warning] Watch out
> - In $a_{ij}$ the first index is the state we come from. "Rain tomorrow given sun today" is $a_{31}$. Some versions of the slide write the product with swapped indices, but the numbers and the result are right.
> - The geometric duration always has its most likely value at $T=1$. Real phone durations do not, a known weakness of HMMs.

## 2. From Markov models to HMMs (slides 5-9)
![[cs568_lecture_13_l13_p005.jpg]]
![[cs568_lecture_13_l13_p006.jpg]]
![[cs568_lecture_13_l13_p007.jpg]]
![[cs568_lecture_13_l13_p008.jpg]]
![[cs568_lecture_13_l13_p009.jpg]]

**Notes.** In the weather model each state is one observable event. In a **hidden Markov model** the observation is a probabilistic function of the state: each state can produce several outputs, and each output can come from any state. The state sequence is not observable. An HMM is a doubly stochastic process.

**Coin-toss problem.** Behind a curtain a person picks one of several coins, tosses it and tells you only H or T. The coins are the hidden states, the outcomes are the observations.
- One coin: one state, nothing hidden, one parameter $P(H)$.
- Two coins: two states, each with its own $P(H)$. 4 free parameters ($a_{11},a_{22},P_1,P_2$).
- Three coins: three states, 9 free parameters (6 transitions, 3 biases).

The best model is the one that best explains the data (maximum likelihood), if the sequence is long enough to justify it. More coins always fit the training sequence at least as well, which is not the same as being better.

**Urn-ball problem.** $N$ urns with balls of $M$ colours. The urns are the hidden states, the ball colours are the observations. The urn choice process is $A$, the colour proportions in urn $j$ are $b_j(k)$.

## 3. Elements of an HMM and the three problems (slides 10-12)
![[cs568_lecture_13_l13_p010.jpg]]
![[cs568_lecture_13_l13_p011.jpg]]
![[cs568_lecture_13_l13_p012.jpg]]

**Notes.** An HMM has $N$ states, $M$ discrete observation symbols $V=\{v_1,\dots,v_M\}$, and

$$a_{ij}=P(q_{t+1}=S_j\mid q_t=S_i),\qquad b_j(k)=P(o_t=v_k\mid q_t=S_j),\qquad \pi_j=P(q_1=S_j)$$

Compact notation: $\lambda=(A,B,\pi)$. Two assumptions: the state chain is Markov, and given the current state the observation is independent of everything else.

Generating a sequence: choose $q_1$ from $\pi$; emit $o_t$ from $b_i(k)$ of the current state; move to the next state with $a_{ij}$; repeat until $t=T$.

The three problems:

| Problem | Given | Find | Solution |
|---|---|---|---|
| 1. Evaluation | $O$, $\lambda$ | $P(O\mid\lambda)$ | Forward, Backward |
| 2. Decoding | $O$, $\lambda$ | best state sequence $Q$ | Viterbi |
| 3. Training | $O$ | $\lambda$ that maximises $P(O\mid\lambda)$ | Baum-Welch |

Isolated-word recogniser: train one HMM per word (Problem 3), then output the word whose model gives the largest $P(O\mid\lambda_w)$ (Problem 1).

## 4. Forward and Backward procedures (slides 13-16)
![[cs568_lecture_13_l13_p013.jpg]]
![[cs568_lecture_13_l13_p014.jpg]]
![[cs568_lecture_13_l13_p015.jpg]]
![[cs568_lecture_13_l13_p016.jpg]]

**Notes.** Brute force sums over every state sequence:

$$P(O\mid\lambda)=\sum_{q_1,\dots,q_T}\pi_{q_1}b_{q_1}(o_1)\,a_{q_1q_2}b_{q_2}(o_2)\cdots a_{q_{T-1}q_T}b_{q_T}(o_T)$$

There are $N^T$ sequences, about $2T\cdot N^T$ operations. Paths that reach the same state at time $t$ can be merged (trellis).

**Forward variable** $\alpha_t(i)=P(o_1,\dots,o_t,\ q_t=S_i\mid\lambda)$:

$$\begin{aligned}\alpha_1(i)&=\pi_i\,b_i(o_1)\\ \alpha_{t+1}(j)&=\Big[\sum_{i=1}^{N}\alpha_t(i)\,a_{ij}\Big]b_j(o_{t+1})\\ P(O\mid\lambda)&=\sum_{i=1}^{N}\alpha_T(i)\end{aligned}$$

Cost: about $N^2T$ operations.

**Backward variable** $\beta_t(i)=P(o_{t+1},\dots,o_T\mid q_t=S_i,\lambda)$:

$$\beta_T(i)=1,\qquad \beta_t(i)=\sum_{j=1}^{N}a_{ij}\,b_j(o_{t+1})\,\beta_{t+1}(j)$$

Check at any $t$: $P(O\mid\lambda)=\sum_i\alpha_t(i)\beta_t(i)$.

> [!example] Two-state model, $O=\{H,T\}$
> $\pi=(0.6,0.4)$, $A=\begin{bmatrix}0.7&0.3\\0.4&0.6\end{bmatrix}$, $b_1(H)=0.9$, $b_2(H)=0.2$.
> $\alpha_1=(0.54,\ 0.08)$. $\alpha_2(1)=(0.54\cdot0.7+0.08\cdot0.4)\cdot0.1=0.041$. $\alpha_2(2)=(0.54\cdot0.3+0.08\cdot0.6)\cdot0.8=0.168$. $P(O\mid\lambda)=0.209$.
> Backward: $\beta_1(1)=0.7\cdot0.1+0.3\cdot0.8=0.31$, $\beta_1(2)=0.4\cdot0.1+0.6\cdot0.8=0.52$. Check: $0.54\cdot0.31+0.08\cdot0.52=0.209$.

> [!warning] Watch out
> $\alpha_t(i)$ is a joint probability (the state is inside the event). $\beta_t(i)$ is conditioned on the state.

## 5. The Viterbi algorithm (slides 17-22)
![[cs568_lecture_13_l13_p017.jpg]]
![[cs568_lecture_13_l13_p018.jpg]]
![[cs568_lecture_13_l13_p019.jpg]]
![[cs568_lecture_13_l13_p020.jpg]]
![[cs568_lecture_13_l13_p021.jpg]]
![[cs568_lecture_13_l13_p022.jpg]]

**Notes.** "Optimal" can mean two things.

(a) Individually most likely states. Define the state posterior

$$\gamma_t(i)=P(q_t=S_i\mid O,\lambda)=\frac{\alpha_t(i)\,\beta_t(i)}{\sum_{i=1}^{N}\alpha_t(i)\,\beta_t(i)},\qquad q_t^*=\arg\max_i\gamma_t(i)$$

Problem: the resulting sequence may be invalid (two consecutive states with $a_{ij}=0$).

(b) The single best path. Define $\delta_t(i)$ as the highest probability of one path that explains the first $t$ observations and ends in $S_i$:

$$\begin{aligned}\text{Initialisation:}\quad&\delta_1(i)=\pi_i\,b_i(o_1),\quad\Psi_1(i)=0\\ \text{Recursion:}\quad&\delta_t(j)=\max_i\big[\delta_{t-1}(i)\,a_{ij}\big]\,b_j(o_t),\quad\Psi_t(j)=\arg\max_i\big[\delta_{t-1}(i)\,a_{ij}\big]\\ \text{Termination:}\quad&P^*=\max_i\delta_T(i),\quad q_T^*=\arg\max_i\delta_T(i)\\ \text{Backtracking:}\quad&q_t^*=\Psi_{t+1}(q_{t+1}^*),\quad t=T-1,\dots,1\end{aligned}$$

$\Psi_t(j)$ is the back-pointer: the best predecessor of $S_j$ at time $t$ (slide 21). Viterbi is the Forward procedure with max instead of sum, so $P^*\le P(O\mid\lambda)$.

> [!example] Same two-state model, $O=\{H,T\}$
> $\delta_1=(0.54,0.08)$. Into $S_1$: $\max(0.378,0.032)\cdot0.1=0.0378$, $\Psi_2(1)=1$. Into $S_2$: $\max(0.162,0.048)\cdot0.8=0.1296$, $\Psi_2(2)=1$. $P^*=0.1296$, best path $S_1,S_2$.

> [!warning] Slide wording
> The slide says the second criterion maximises "the posterior $P(O\mid Q,\lambda)$". That expression is a likelihood. Viterbi maximises the posterior $P(Q\mid O,\lambda)$, which is the same as maximising the joint $P(Q,O\mid\lambda)$.

## 6. Baum-Welch re-estimation (slides 23-25)
![[cs568_lecture_13_l13_p023.jpg]]
![[cs568_lecture_13_l13_p024.jpg]]
![[cs568_lecture_13_l13_p025.jpg]]

**Notes.** Training by maximum likelihood has no closed-form solution. Baum-Welch is an iterative EM procedure. Define the transition posterior

$$\xi_t(i,j)=P(q_t=S_i,\,q_{t+1}=S_j\mid O,\lambda)=\frac{\alpha_t(i)\,a_{ij}\,b_j(o_{t+1})\,\beta_{t+1}(j)}{\sum_{i}\sum_{j}\alpha_t(i)\,a_{ij}\,b_j(o_{t+1})\,\beta_{t+1}(j)}$$

Then $\gamma_t(i)=\sum_j\xi_t(i,j)$. Summed over time these are expected counts: $\sum_{t=1}^{T-1}\gamma_t(i)$ is the expected number of transitions from $S_i$, and $\sum_{t=1}^{T-1}\xi_t(i,j)$ the expected number of transitions from $S_i$ to $S_j$.

Re-estimation by counting events (old parameters on the right, new on the left):

$$\hat\pi_i=\gamma_1(i),\qquad \hat a_{ij}=\frac{\sum_{t=1}^{T-1}\xi_t(i,j)}{\sum_{t=1}^{T-1}\gamma_t(i)},\qquad \hat b_j(k)=\frac{\sum_{t:\,o_t=v_k}\gamma_t(j)}{\sum_{t=1}^{T}\gamma_t(j)}$$

One iteration: Forward and Backward passes, compute $\gamma$ and $\xi$ (E-step), update $\pi$, $A$, $B$ (M-step). Each iteration does not decrease $P(O\mid\lambda)$. A parameter that starts at zero stays at zero, so the topology is preserved.

> [!warning] Slide error
> The slide says the procedure runs "until a local minimum is found". The likelihood increases, so it converges to a local **maximum**. The result depends on the initial model. The slide title spells "Baum-Welsh"; the name is Baum-Welch.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Markov chain | $\pi$ and $A$; duration $P(T)=a_{ii}^{T-1}(1-a_{ii})$, mean $1/(1-a_{ii})$ |
| HMM | $\lambda=(A,B,\pi)$, $b_j(k)=P(o_t=v_k\mid q_t=S_j)$ |
| Forward | $\alpha_{t+1}(j)=[\sum_i\alpha_t(i)a_{ij}]\,b_j(o_{t+1})$; $P=\sum_i\alpha_T(i)$ |
| Backward | $\beta_T(i)=1$; $\beta_t(i)=\sum_ja_{ij}b_j(o_{t+1})\beta_{t+1}(j)$ |
| State posterior | $\gamma_t(i)=\alpha_t(i)\beta_t(i)/P(O\mid\lambda)$ |
| Transition posterior | $\xi_t(i,j)=\alpha_t(i)a_{ij}b_j(o_{t+1})\beta_{t+1}(j)/P(O\mid\lambda)$ |
| Viterbi | $\delta_t(j)=\max_i[\delta_{t-1}(i)a_{ij}]\,b_j(o_t)$, back-pointers $\Psi_t(j)$ |
| Baum-Welch | $\hat\pi_i=\gamma_1(i)$, $\hat a_{ij}=\sum\xi/\sum\gamma$, $\hat b_j(k)=\sum_{o_t=v_k}\gamma/\sum\gamma$ |

## Check yourself
> [!question]- 1. Weather model, day 1 cloudy. Probability of "cloudy, cloudy, sunny, rain"?
> $\pi_2\,a_{22}\,a_{23}\,a_{31}=1\cdot0.6\cdot0.2\cdot0.1=0.012$.

> [!question]- 2. Probability that a rainy spell lasts exactly 2 days?
> $a_{11}^{1}(1-a_{11})=0.4\times0.6=0.24$.

> [!question]- 3. Free parameters of a 4-state HMM with $M=2$ symbols?
> $A$: $4\times3=12$. $B$: $4\times1=4$. $\pi$: 3. Total 19.

> [!question]- 4. Two-state model above, $O=\{H,T\}$: $P(O\mid\lambda)$ with the Forward procedure?
> $\alpha_1=(0.54,0.08)$, $\alpha_2=(0.041,0.168)$, so $P=0.209$.

> [!question]- 5. Same model: $\gamma_1(1)$ and $\gamma_1(2)$?
> $\alpha_1(i)\beta_1(i)$: $0.54\cdot0.31=0.1674$ and $0.08\cdot0.52=0.0416$. Divide by 0.209: $\gamma_1=(0.801,\ 0.199)$.

> [!question]- 6. An HMM starts with $a_{13}=0$. What is $a_{13}$ after 50 Baum-Welch iterations?
> Still 0. Every $\xi_t(1,3)$ contains the factor $a_{13}=0$, so the numerator of $\hat a_{13}$ is 0 at every iteration.

## Links
- Course index: [[CS568 Home]]
- Previous lecture: [[CS568 L12 - Template matching and DTW]]
- Next lecture: [[CS568 L14 - Refinements for HMMs]]
- Full step-by-step notes (PDF): [CS568_Lecture_13_HMMs.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_13/CS568_Lecture_13_HMMs.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_13/CS568_Lecture_13_HMMs.pdf`
- Original slides (PDF): [l13.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_13/l13.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_13/l13.pdf`

Slides not embedded: 1
