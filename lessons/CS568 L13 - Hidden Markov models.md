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

> [!question]- Check (slide 2): Find the two mistakes: "In a first-order Markov process the state at time $t$ is independent of all earlier states. Each column of $A$ sums to 1."
> Mistake 1: the state $q_t$ depends on $q_{t-1}$. It is independent of the older states $q_{t-2},q_{t-3},\dots$ only once $q_{t-1}$ is known.
> Mistake 2: each **row** sums to 1, $\sum_j a_{ij}=1$. Row $i$ lists all the moves out of $S_i$, and the system must go somewhere (staying counts). The columns have no such constraint.
> The slide also assumes that $a_{ij}$ does not change with $t$. So one matrix $A$ describes every time step.

> [!question]- Check (slide 3): Which three numbers are on the arrows that leave $S_3$ in the diagram, and where are they in $A$? Today is cloudy. What is the probability that it is sunny in two days, when tomorrow can be anything?
> The arrows out of $S_3$ carry 0.8 (self-loop), 0.1 (to $S_1$) and 0.1 (to $S_2$). They are row 3 of $A$, and they sum to 1.
> Two days: sum over the three possible states $S_k$ of tomorrow.
> $$P=\sum_{k=1}^{3}a_{2k}\,a_{k3}=0.2\cdot0.3+0.6\cdot0.2+0.2\cdot0.8=0.06+0.12+0.16=0.34$$
> This sum over the middle state is the same idea as the Forward procedure later: all paths into one state are added.

> [!question]- Check (slide 4): In $a_{ii}^{T-1}(1-a_{ii})$, what does each factor stand for? A state has self-loop $a_{ii}=0.9$ and one step is a 10 ms frame. Find $P(\text{exactly 3 frames})$ and the mean duration. Which $a_{ii}$ gives a mean of 4 frames?
> $a_{ii}^{T-1}$: the process stays $T-1$ more times after the first step in $S_i$. $(1-a_{ii})$: then it leaves, to any other state.
> $P(3)=0.9^{2}\cdot0.1=0.081$.
> Mean $=1/(1-a_{ii})=1/0.1=10$ frames, so 100 ms.
> Mean 4 frames: $1/(1-a_{ii})=4$, so $a_{ii}=1-1/4=0.75$.
> A larger self-loop probability means a longer stay on average. The shape stays geometric: the most likely single duration is still 1 frame.

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

> [!info] What this means for speech (beyond the slides)
> The slides explain the hidden Markov model (saklı Markov modeli) with coins and urns. In speech the mapping is:
> - A state (durum) is "which part of which phone is being produced". Nobody can observe it directly.
> - An observation (gözlem) is the feature vector of one frame, for example the MFCCs of Lecture 9.
> - The same phone gives a different vector each time, and different phones can give similar vectors. So the link from state to observation must be a probability distribution. This is $B$, the model of variability in the spectrum.
> - The transition probabilities (geçiş olasılığı) in $A$ decide how long the model stays in each state and in which order the states come. This is the model of variability in time, the job that DTW did in Lecture 12.
>
> A template of Lecture 12 is one stored example of a word. An HMM is a statistical summary of many examples, so it also knows how much each part of the word can vary.

> [!question]- Check (slide 5): Take the weather model of slide 3, but now you sit in a room with no window. You only see if a colleague comes in with an umbrella or without one. What are the states and the observations, which new parameters are needed, and why is the state no longer "trivially retrieved"?
> States: the weather $S_1,S_2,S_3$, as before. Observations: "umbrella" or "no umbrella".
> New parameters: for each state $j$ a distribution over the observations, for example $P(\text{umbrella}\mid S_j)$. This is $B$. $A$ and $\pi$ stay.
> Each observation can come from every state (people also carry an umbrella on a cloudy day). So one observation does not tell the state. We can only give probabilities for the states. In the model of slide 3 the observation was the state itself.

> [!question]- Check (slide 6): You hear $O=\{H,T,T,H\}$ and you assume two coins. How many coin sequences could have produced it, and why does the slide say that the inferred state sequence "will not be unique"?
> Each of the 4 tosses used coin 1 or coin 2, so there are $2^4=16$ coin sequences.
> If both coins can show H and T, and the person can pick either coin at every toss, all 16 can produce $H,T,T,H$. They only differ in probability.
> So the observations never prove one sequence. We can only say which one is most likely (this becomes Problem 2), and two sequences can even have the same probability.

> [!question]- Check (slide 7): The top figure has two states called HEADS and TAILS. Both arrows that enter state 1 carry $P(H)$. Why? And why is the bottom figure a hidden model with 4 free parameters while the top one has 1?
> Top figure: the states are the outcomes themselves, so nothing is hidden. One coin has no memory: the next toss is heads with probability $P(H)$, whatever the last outcome was. So the self-loop of HEADS and the arrow from TAILS to HEADS both carry $P(H)$. One number, $P(H)$, fixes the whole model.
> Bottom figure: the states are the two coins. Each state can give H or T, so the outcome does not tell the coin.
> Free parameters: each row of $A$ has 2 entries that sum to 1, so 1 free number per row: $a_{11}$ and $a_{22}$. Each coin has one bias: $P_1$ and $P_2$. Total 4.
> The slide does not count the initial distribution $\pi$. With $\pi$ there is 1 more free number.

> [!question]- Check (slide 8): Count the 9 free parameters of the three-coin model from the figure and the table. Then find the mistake: "The three-coin model gives a higher likelihood on my sequence than the two-coin model, so the person really has three coins."
> $A$ is $3\times3$ and each row sums to 1: $3\times2=6$ free numbers. The table has one free bias per state ($P_1,P_2,P_3$): 3. Total 9. In general $N(N-1)+N(M-1)$, and $N-1$ more if $\pi$ is counted.
> The mistake: a model with more parameters can always fit the training sequence at least as well, because it contains the smaller model as a special case. A higher likelihood on the same data is not proof. The sequence must be long and rich enough to estimate 9 numbers, or the larger model only fits chance patterns.

> [!question]- Check (slide 9): There are $N=3$ urns and $M=4$ colours. Give the sizes of $A$ and $B$ and say in words what $b_2(3)$ is. Why does the person put the ball back, and why can you not tell the urn from the colour?
> $A$ is $3\times3$ (urn to next urn). $B$ is $3\times4$ (one row of colour probabilities per urn).
> $b_2(3)$: the probability of drawing colour 3 when the ball comes from urn 2, so the share of colour 3 in urn 2.
> The ball goes back so that the colour shares of an urn stay the same. Then $b_j(k)$ does not depend on time.
> In the picture every urn holds balls of every colour, only in different proportions. A red ball can come from any urn, so the urn (the state) stays hidden.

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

> [!info] What the two assumptions ignore in speech (beyond the slides)
> - First-order Markov: the next state depends only on the current state, not on how long the model has been there. The result is the geometric duration of section 1, which is a poor model of real phone durations.
> - Output independence: given the state, the frame $o_t$ does not depend on the frames next to it. Real speech frames overlap and the vocal tract moves slowly, so neighbouring frames are strongly correlated.
>
> Both assumptions are wrong for speech. They are kept because they make the three problems solvable with cheap recursions.

> [!warning] Slide notation (slide 11)
> Step 1 says "choose an initial state $S_1$". It means the first state $q_1$, which can be any of the $N$ states. In the same way "$S_{t+1}$" in step 4 means the next state $q_{t+1}$. Steps 3 and 4 use $b_j(k)$ and $a_{ij}$ of the state the model is in at time $t$.

> [!question]- Check (slide 10): Say what each of $a_{ij}$, $b_j(k)$ and $\pi_j$ is the probability of, and which sums must be 1. Is $\sum_{j=1}^{N}b_j(k)=1$ also required?
> $a_{ij}$: the next state is $S_j$, given that the current state is $S_i$. Each row sums to 1: $\sum_j a_{ij}=1$.
> $b_j(k)$: the observation is symbol $v_k$, given that the current state is $S_j$. For each state the symbols sum to 1: $\sum_{k=1}^{M}b_j(k)=1$.
> $\pi_j$: the first state is $S_j$. $\sum_j\pi_j=1$.
> $\sum_j b_j(k)=1$ is **not** required. That sum runs over the condition (the state), not over the outcomes. A symbol can be likely in every state.

> [!question]- Check (slide 11): Use the three-coin example on the slide ($A$ and $\pi$ all $1/3$). What is $P(o_t=H)$ at any time $t$, and what is $P(O=\{H,H,H,H,H\}\mid\lambda)$? What is special about this $A$?
> Every row of $A$ is $(1/3,1/3,1/3)$, so the next coin does not depend on the current coin. Each toss picks a coin uniformly.
> $$P(o_t=H)=\tfrac13(0.5+0.75+0.25)=0.5$$
> The tosses are then independent of each other, so $P(H,H,H,H,H)=0.5^5=0.03125$.
> With this $A$ the model has no memory. An HMM is only useful when the rows of $A$ differ, so that the current state says something about the next one.

> [!question]- Check (slide 12): Which of the three problems is used in each task? (a) Ten word models exist and you must say which word was spoken. (b) You must find which frames of a recording belong to which state. (c) You have recordings of a word and need its model.
> (a) Problem 1, evaluation: compute $P(O\mid\lambda_w)$ for each of the ten models and take the largest. Forward procedure.
> (b) Problem 2, decoding: find the best state sequence $Q$ for the given $O$ and $\lambda$. Viterbi algorithm.
> (c) Problem 3, training: adjust $\lambda=(A,B,\pi)$ to maximise $P(O\mid\lambda)$. Baum-Welch.
> In Problems 1 and 2 the model is given and fixed. Only Problem 3 changes the parameters.

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

> [!info] Why the Forward induction step holds (beyond the slides)
> The slide gives the induction formula without the steps. Read $\alpha_{t+1}(j)=\big[\sum_i\alpha_t(i)\,a_{ij}\big]\,b_j(o_{t+1})$ from left to right:
> 1. $\alpha_t(i)$: the model has produced $o_1,\dots,o_t$ and is in $S_i$ at time $t$.
> 2. Times $a_{ij}$: and it moves to $S_j$. By the Markov assumption this move depends only on $S_i$.
> 3. Sum over $i$: the model can reach $S_j$ from any state. These are different, exclusive events, so their probabilities add. This is where all paths into one trellis node are merged.
> 4. Times $b_j(o_{t+1})$: and state $S_j$ emits $o_{t+1}$. By the output-independence assumption this depends only on $S_j$, so it is the same factor for every $i$ and can stand outside the sum.
>
> Cost: each of the $N$ nodes of a column needs $N$ products, and there are $T$ columns, so about $N^2T$ operations. The likelihood (olabilirlik) $P(O\mid\lambda)$ is the sum of the last column, because the model must end in some state.

> [!warning] Slide notation (slides 13-15)
> - Slide 13, last formula: the slide writes $b_{q_1}(o_{q_1})$. The observation index is the time, so it should read $b_{q_1}(o_1)$, $b_{q_2}(o_2)$, and so on.
> - Slide 14 prints the order of computations as "$10^7$". This is a typo for $10^{72}$: for $N=5$, $T=100$ the value is $2T\cdot N^T=2\cdot100\cdot5^{100}\approx1.6\times10^{72}$, and slide 15 writes $10^{72}$.
> - Slide 15 gives 3000 operations for the Forward procedure, while $N^2T=5^2\cdot100=2500$. Both are rough counts of the same order of magnitude. (Beyond the slides: the more exact count of multiplications in Rabiner's paper is $N(N+1)(T-1)+N=2975$, which is where "3000" comes from.)

> [!info] Trace model for the checks below
> The checks of sections 4, 5 and 6 use one small model with new numbers. Two states, symbols H and T, observation sequence $O=(H,T,T)$, so $T=3$.
> $$\pi=(0.5,\ 0.5),\qquad A=\begin{bmatrix}0.8&0.2\\0.3&0.7\end{bmatrix},\qquad b_1(H)=0.6,\ b_1(T)=0.4,\qquad b_2(H)=0.1,\ b_2(T)=0.9$$
> As on the slides, $a_{ij}$ is "from $S_i$ to $S_j$", and there are no extra entry or exit states: the model starts with $\pi$ and stops after $o_T$.

> [!question]- Check (slide 13): In $P(O\mid\lambda)=\sum_Q P(O\mid Q,\lambda)\,P(Q\mid\lambda)$, what does each factor measure and which assumption makes it a simple product? Compute the term of the single path $Q=(S_1,S_1,S_2)$ for the trace model.
> $P(Q\mid\lambda)$: how likely the state path is. It uses only $\pi$ and $A$. It is a product of one $a$ per step because of the first-order Markov assumption.
> $P(O\mid Q,\lambda)$: how well this path explains the observations. It uses only $B$. It is a product of one $b$ per frame because of the output-independence assumption.
> $P(Q\mid\lambda)=\pi_1\,a_{11}\,a_{12}=0.5\cdot0.8\cdot0.2=0.08$.
> $P(O\mid Q,\lambda)=b_1(H)\,b_1(T)\,b_2(T)=0.6\cdot0.4\cdot0.9=0.216$.
> Term of this path: $0.08\cdot0.216=0.01728$. The sum over all $2^3=8$ paths gives $P(O\mid\lambda)$.

> [!question]- Check (slide 14): In the trellis figure, what is a column, a dot and an arrow? How many arrows go from one column to the next? For $N=3$, $T=4$ compare the number of paths, the brute-force cost $2T\cdot N^T$ and the Forward cost $N^2T$.
> A column is one time $t$ (horizontal axis "observation, t"). A dot is one state at that time. An arrow is one allowed transition $a_{ij}$ from a state at $t$ to a state at $t+1$.
> Every one of the $N$ dots connects to every one of the $N$ dots of the next column: $N^2$ arrows per step.
> Paths: $N^T=3^4=81$. Brute force: $2\cdot4\cdot81=648$ operations. Forward: $3^2\cdot4=36$ operations.
> The gain comes from merging. Many paths pass through the same dot, and what happens after that dot depends only on the state, not on how the path got there. So one number per dot is enough.

> [!question]- Check (slide 15): Trace the Forward procedure for the trace model and $O=(H,T,T)$. Show every $\alpha_t(i)$ and $P(O\mid\lambda)$. What does $\alpha_2(2)$ mean in words?
> Initialisation, $o_1=H$:
> $\alpha_1(1)=\pi_1b_1(H)=0.5\cdot0.6=0.3$, $\quad\alpha_1(2)=\pi_2b_2(H)=0.5\cdot0.1=0.05$.
> Induction, $o_2=T$:
> $\alpha_2(1)=(0.3\cdot0.8+0.05\cdot0.3)\cdot0.4=(0.24+0.015)\cdot0.4=0.102$
> $\alpha_2(2)=(0.3\cdot0.2+0.05\cdot0.7)\cdot0.9=(0.06+0.035)\cdot0.9=0.0855$
> Induction, $o_3=T$:
> $\alpha_3(1)=(0.102\cdot0.8+0.0855\cdot0.3)\cdot0.4=(0.0816+0.02565)\cdot0.4=0.0429$
> $\alpha_3(2)=(0.102\cdot0.2+0.0855\cdot0.7)\cdot0.9=(0.0204+0.05985)\cdot0.9=0.072225$
> Termination: $P(O\mid\lambda)=0.0429+0.072225=0.115125$.
> $\alpha_2(2)=0.0855$ is the probability that the model produces $H,T$ **and** is in $S_2$ at $t=2$. It already contains both ways to get there (from $S_1$ and from $S_2$).

> [!question]- Check (slide 16): Compute every $\beta_t(i)$ for the trace model and check $\sum_i\alpha_t(i)\beta_t(i)$ at $t=1$ and $t=2$ against the Forward result. Why is $\beta_T(i)=1$ a sensible start, and how does the direction of the figure differ from the Forward figure?
> Initialisation: $\beta_3(1)=\beta_3(2)=1$.
> $t=2$ (uses $o_3=T$): $\beta_2(1)=0.8\cdot0.4\cdot1+0.2\cdot0.9\cdot1=0.5$, $\quad\beta_2(2)=0.3\cdot0.4+0.7\cdot0.9=0.75$.
> $t=1$ (uses $o_2=T$): $\beta_1(1)=0.8\cdot0.4\cdot0.5+0.2\cdot0.9\cdot0.75=0.16+0.135=0.295$
> $\beta_1(2)=0.3\cdot0.4\cdot0.5+0.7\cdot0.9\cdot0.75=0.06+0.4725=0.5325$
> Check $t=1$: $0.3\cdot0.295+0.05\cdot0.5325=0.0885+0.026625=0.115125$.
> Check $t=2$: $0.102\cdot0.5+0.0855\cdot0.75=0.051+0.064125=0.115125$. Both equal $P(O\mid\lambda)$.
> $\beta_T(i)=1$: after time $T$ nothing is left to observe, and "nothing more happens" has probability 1. With this value the check also holds at $t=T$, where it becomes the Forward termination.
> Figure: in the Forward figure many states at $t$ go **into** one $S_j$ at $t+1$ (sum over $i$). In the Backward figure one $S_i$ at $t$ fans **out** to all states at $t+1$ (sum over $j$), and the computation runs from $t=T$ down to 1.

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

> [!info] Why $\alpha_t(i)\beta_t(i)$ is the numerator of $\gamma_t(i)$ (beyond the slides)
> Slide 18 says it without the step. Split the observations at time $t$:
> $$P(O,q_t=S_i\mid\lambda)=\underbrace{P(o_1,\dots,o_t,\ q_t=S_i\mid\lambda)}_{\alpha_t(i)}\cdot\underbrace{P(o_{t+1},\dots,o_T\mid q_t=S_i,\lambda)}_{\beta_t(i)}$$
> The second factor does not need $o_1,\dots,o_t$ in its condition: once the state at time $t$ is known, the future does not depend on the past (the two HMM assumptions). Summing over $i$ gives $P(O\mid\lambda)$, the denominator.

> [!info] Viterbi in the log domain (beyond the slides)
> $\delta_t(i)$ is a product of about $2t$ probabilities, all below 1. For a real utterance it becomes smaller than the smallest number a computer can store and is rounded to 0 (underflow). Example: 300 frames with a factor of about 0.05 per frame give $0.05^{300}\approx10^{-390}$, below the double-precision limit of about $10^{-308}$.
> So implementations store $\log\delta_t(i)$. The products become sums:
> $$\log\delta_t(j)=\max_i\big[\log\delta_{t-1}(i)+\log a_{ij}\big]+\log b_j(o_t)$$
> The logarithm is an increasing function, so the same $i$ wins the max and the back-pointers and the best path do not change. The Forward procedure cannot do this so simply, because the log of a sum is not a sum of logs. It uses scaling (Lecture 14).

> [!question]- Check (slide 17): The slide names two optimality criteria. What does each one maximise, and why does the lecture work out the first one although the second is "the most widely used"?
> Criterion 1: at each time $t$ separately, pick the state with the largest $P(q_t=S_i\mid O,\lambda)$. This maximises the expected number of correct single states.
> Criterion 2: pick the one complete sequence $Q$ with the largest probability together with $O$. This is the Viterbi path.
> The first criterion needs the variable $\gamma_t(i)$. The slide says this variable is used again in the solution of Problem 3 (Baum-Welch), so it is defined here.

> [!question]- Check (slide 18): Compute $\gamma_2(1)$ and $\gamma_2(2)$ for the trace model (values of $\alpha$ and $\beta$ from the checks of slides 15 and 16). Which state is individually most likely at $t=2$? Why is the denominator needed?
> $\alpha_2(1)\beta_2(1)=0.102\cdot0.5=0.051$, $\quad\alpha_2(2)\beta_2(2)=0.0855\cdot0.75=0.064125$.
> Denominator: $0.051+0.064125=0.115125=P(O\mid\lambda)$.
> $\gamma_2(1)=0.051/0.115125\approx0.443$, $\quad\gamma_2(2)=0.064125/0.115125\approx0.557$. So $q_2^*=S_2$.
> The products $\alpha\beta$ are joint probabilities with $O$ and are very small. Dividing by their sum, $P(O\mid\lambda)$, turns them into a distribution over the states that sums to 1.

> [!question]- Check (slide 19): A model has $a_{21}=0$ (no way back from $S_2$ to $S_1$). The $\gamma$ criterion returns $q_4^*=S_2$ and $q_5^*=S_1$. What is wrong with this answer, how can it happen, and what does Viterbi give up to avoid it?
> The sequence contains the step $S_2\to S_1$, which has probability 0. The model can never produce this path, so the answer is not a valid state sequence.
> It can happen because each $\gamma_t$ is chosen alone. $\gamma_4$ and $\gamma_5$ each sum over many paths, and the winners at the two times can come from different paths. Nothing checks the transition between them.
> Viterbi scores whole paths. A path with a zero transition gets $\delta=0$ and can never win. The price: a state on the Viterbi path is not always the individually most likely state at that time.

> [!question]- Check (slide 20): Compare the definitions of $\alpha_t(i)$ and $\delta_t(i)$. For the trace model, the four paths that end in $S_2$ at $t=3$ have joint probabilities 0.01728, 0.03402, 0.00108 and 0.019845. Give $\alpha_3(2)$ and $\delta_3(2)$ from these numbers. What is $\Psi$ for?
> Both look at the first $t$ observations and at paths that end in $S_i$. $\alpha_t(i)$ **adds** the probabilities of all such paths. $\delta_t(i)$ keeps only the **largest** one.
> $\alpha_3(2)=0.01728+0.03402+0.00108+0.019845=0.072225$ (the value of the Forward trace).
> $\delta_3(2)=\max(\dots)=0.03402$.
> One term of a sum of non-negative numbers is never larger than the sum, so $\delta_t(i)\le\alpha_t(i)$ always.
> $\delta$ only stores a score. $\Psi_{t+1}(j)$ stores **which** state $i$ at time $t$ gave the maximum, so that the path itself can be read back at the end.

> [!question]- Check (slide 21): Read the figure. What does the label $\Psi_{t=5}(S_4)=S_2$ say? Which states does the drawn path visit at $t=1,\dots,10$? In which direction is this path found?
> The label: the best path that is in $S_4$ at $t=5$ comes from $S_2$ at $t=4$. It is the back-pointer stored at the node ($t=5$, $S_4$).
> Path in the figure: $S_2,S_2,S_3,S_2,S_4$ for $t=1,\dots,5$, then $S_1$ for $t=6,\dots,9$, then $S_3$ at $t=10$.
> It is found backwards. First the best final state at $t=10$ is chosen, then the back-pointers are followed to $t=9,8,\dots,1$.
> Every node of the grid stores a back-pointer, not only the nodes on the red path. Before the last column is finished, we do not know which pointers will be used.

> [!question]- Check (slide 22): Trace the Viterbi algorithm for the trace model and $O=(H,T,T)$: every $\delta_t(i)$ and $\Psi_t(i)$, then $P^*$ and the best path. Compare $P^*$ with the Forward result $0.115125$ and say where the two computations differ.
> Initialisation: $\delta_1(1)=0.5\cdot0.6=0.3$, $\delta_1(2)=0.5\cdot0.1=0.05$, $\Psi_1=0$.
> $t=2$, $o_2=T$:
> $\delta_2(1)=\max(0.3\cdot0.8,\ 0.05\cdot0.3)\cdot0.4=\max(0.24,\ 0.015)\cdot0.4=0.096$, $\Psi_2(1)=1$
> $\delta_2(2)=\max(0.3\cdot0.2,\ 0.05\cdot0.7)\cdot0.9=\max(0.06,\ 0.035)\cdot0.9=0.054$, $\Psi_2(2)=1$
> $t=3$, $o_3=T$:
> $\delta_3(1)=\max(0.096\cdot0.8,\ 0.054\cdot0.3)\cdot0.4=\max(0.0768,\ 0.0162)\cdot0.4=0.03072$, $\Psi_3(1)=1$
> $\delta_3(2)=\max(0.096\cdot0.2,\ 0.054\cdot0.7)\cdot0.9=\max(0.0192,\ 0.0378)\cdot0.9=0.03402$, $\Psi_3(2)=2$
> Termination: $P^*=\max(0.03072,\ 0.03402)=0.03402$, $q_3^*=S_2$.
> Backtracking: $q_2^*=\Psi_3(2)=S_2$, $q_1^*=\Psi_2(2)=S_1$. Best path: $S_1,S_2,S_2$.
> Comparison: the two traces use the same products. Forward adds the two incoming terms ($0.24+0.015$), Viterbi keeps the larger one ($0.24$) and remembers where it came from. At the end Forward adds the last column, Viterbi takes its maximum.
> $P^*=0.03402$ is the probability of one path. $P(O\mid\lambda)=0.115125$ is the sum over all 8 paths. So $P^*\le P(O\mid\lambda)$; here the best path carries $0.03402/0.115125\approx30\%$ of the total.

> [!question]- Check (slide 22, log domain): In the Viterbi trace above, compute $\ln\delta_2(1)$ with the log recursion and check it against $\delta_2(1)=0.096$. Does the best path change when logs are used?
> $\ln\delta_1(1)=\ln0.3\approx-1.204$, $\ln\delta_1(2)=\ln0.05\approx-2.996$.
> Candidates: $-1.204+\ln0.8=-1.204-0.223=-1.427$ and $-2.996+\ln0.3=-2.996-1.204=-4.200$. The max is $-1.427$ (from $S_1$, as before).
> Add $\ln b_1(T)=\ln0.4\approx-0.916$: $\ln\delta_2(1)\approx-2.343$. Check: $\ln0.096\approx-2.343$.
> The path does not change. The logarithm keeps the order of the candidates, so every max picks the same predecessor. Only the stored numbers change, from tiny products to moderate negative sums.

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

> [!question]- Check (slide 23): The figure is cut into three parts by two dashed lines. Name the quantity of each part and say which observations it accounts for. Why can we not simply count the transitions $S_i\to S_j$ in the training data?
> Left part, up to time $t$: $\alpha_t(i)$. It accounts for $o_1,\dots,o_t$ and for being in $S_i$ at time $t$.
> Middle part, from $t$ to $t+1$: $a_{ij}\,b_j(o_{t+1})$. The move from $S_i$ to $S_j$ and the emission of $o_{t+1}$ in $S_j$.
> Right part, after $t+1$: $\beta_{t+1}(j)$. It accounts for $o_{t+2},\dots,o_T$, given $S_j$ at time $t+1$.
> The product of the three is $P(q_t=S_i,\ q_{t+1}=S_j,\ O\mid\lambda)$, the numerator of $\xi_t(i,j)$.
> The states are hidden, so nobody knows which transitions really happened. $\xi_t(i,j)$ is the probability that this transition happened at time $t$. Baum-Welch counts with these probabilities, not with 0 or 1.

> [!question]- Check (slide 24): For the trace model compute the four values $\xi_1(i,j)$ (use $\alpha_1=(0.3,0.05)$, $\beta_2=(0.5,0.75)$, $o_2=T$, $P(O\mid\lambda)=0.115125$). Check that they sum to 1 and that $\sum_j\xi_1(i,j)=\gamma_1(i)$. Why do the sums over time stop at $T-1$?
> Numerators $\alpha_1(i)\,a_{ij}\,b_j(T)\,\beta_2(j)$:
> $(1,1)$: $0.3\cdot0.8\cdot0.4\cdot0.5=0.048$, $\quad(1,2)$: $0.3\cdot0.2\cdot0.9\cdot0.75=0.0405$
> $(2,1)$: $0.05\cdot0.3\cdot0.4\cdot0.5=0.003$, $\quad(2,2)$: $0.05\cdot0.7\cdot0.9\cdot0.75=0.023625$
> Their sum is $0.115125=P(O\mid\lambda)$, as the double sum in the slide's denominator says.
> Divide by $0.115125$: $\xi_1(1,1)\approx0.417$, $\xi_1(1,2)\approx0.352$, $\xi_1(2,1)\approx0.026$, $\xi_1(2,2)\approx0.205$. Sum: 1.
> Row sums: $\gamma_1(1)\approx0.417+0.352=0.769$, $\gamma_1(2)\approx0.026+0.205=0.231$. The same values come from $\alpha_1(i)\beta_1(i)/P$: $0.0885/0.115125$ and $0.026625/0.115125$.
> The sums stop at $T-1$ because a transition needs a next time step. With $T$ frames there are only $T-1$ transitions, and $\xi_T$ does not exist.

> [!question]- Check (slide 25): One Baum-Welch step for the trace model. Given $\gamma_1=(0.769,0.231)$, $\gamma_2=(0.443,0.557)$, $\gamma_3=(0.373,0.627)$, $\xi_1(1,2)=0.352$, $\xi_2(1,2)=0.159$: compute $\hat\pi$, $\hat a_{12}$ and $\hat b_1(H)$. Say in words what each numerator and denominator counts.
> $\hat\pi=\gamma_1=(0.769,\ 0.231)$: the expected share of starts in each state. (Old value: $(0.5,0.5)$. The first symbol $H$ fits $S_1$ much better.)
> $\hat a_{12}$: expected number of transitions $S_1\to S_2$, divided by the expected number of transitions out of $S_1$.
> $$\hat a_{12}=\frac{\xi_1(1,2)+\xi_2(1,2)}{\gamma_1(1)+\gamma_2(1)}=\frac{0.352+0.159}{0.769+0.443}=\frac{0.511}{1.212}\approx0.42$$
> $\hat b_1(H)$: expected number of times in $S_1$ while $H$ is observed, divided by the expected number of times in $S_1$. $H$ is observed only at $t=1$.
> $$\hat b_1(H)=\frac{\gamma_1(1)}{\gamma_1(1)+\gamma_2(1)+\gamma_3(1)}=\frac{0.769}{0.769+0.443+0.373}=\frac{0.769}{1.585}\approx0.49$$
> Note the two denominators: the one for $\hat a$ stops at $T-1$ (transitions), the one for $\hat b$ runs to $T$ (every frame emits a symbol).
> All values on the right side come from the old model. With the complete new model the likelihood of $O$ rises from $0.115$ to about $0.199$, as $P(O\mid\lambda^{new})\ge P(O\mid\lambda^{old})$ promises.

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
