---
course: CS 568 Speech Processing
lesson: CS568 L13 - Hidden Markov models
type: quiz
questions: 25
---

# CS568 L13 - Hidden Markov models - quiz

Lesson note: [[CS568 L13 - Hidden Markov models]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Discrete Markov processes (slides 2-4)

### Q1 (slide 2)

A Markov chain has states $S_1,\dots,S_N$, and $q_t$ is the state at time $t$. Its $N\times N$ matrix $A$ has the entries $a_{ij}=P(q_t=S_j\mid q_{t-1}=S_i)$: row $i$ is the current state, column $j$ the next state. A student describes a first-order Markov chain in two sentences. Which version of the two sentences is correct?

- A) Given $q_{t-1}$, the state $q_t$ is independent of $q_{t-2},q_{t-3},\dots$. Each row of $A$ sums to 1.
- B) The state $q_t$ is independent of all earlier states $q_{t-1},q_{t-2},\dots$. Each row of $A$ sums to 1.
- C) Given $q_{t-1}$, the state $q_t$ is independent of $q_{t-2},q_{t-3},\dots$. Each column of $A$ sums to 1.
- D) The state $q_t$ is independent of all earlier states $q_{t-1},q_{t-2},\dots$. Each column of $A$ sums to 1.

> [!success]- Answer
> **A.** First order means: $q_t$ depends on $q_{t-1}$, and once $q_{t-1}$ is known, the older states add nothing.
> Row $i$ lists all moves out of $S_i$, and the chain must go somewhere (staying counts). So $\sum_j a_{ij}=1$ for every $i$. The columns have no such rule.
> - B: Independence of all earlier states describes independent draws with no memory. A first-order chain depends on $q_{t-1}$.
> - C: Rows and columns are swapped. A column collects the moves into one state, and these need not sum to 1.
> - D: Both slips at once: no dependence on $q_{t-1}$, and columns in place of rows.

### Q2 (slide 3)

![[cs568_lecture_13_l13_p003.jpg]]

A weather model has three states: $S_1$ rain or snow, $S_2$ cloudy, $S_3$ sunny. Its transition probabilities are $a_{ij}=P(q_{t+1}=S_j\mid q_t=S_i)$, with rows $(0.4,\ 0.3,\ 0.3)$, $(0.2,\ 0.6,\ 0.2)$ and $(0.1,\ 0.1,\ 0.8)$ for $i=1,2,3$. Today is cloudy. Tomorrow can be any weather. What is the probability that it is sunny in two days?

- A) 0.20
- B) 0.16
- C) 0.34
- D) 0.17

> [!success]- Answer
> **C.** Sum over the three possible states $S_k$ of tomorrow:
> $$P=\sum_{k=1}^{3}a_{2k}\,a_{k3}=0.2\cdot0.3+0.6\cdot0.2+0.2\cdot0.8=0.06+0.12+0.16=0.34$$
> - A: Only one step, $a_{23}=0.2$. The middle day is left out.
> - B: Only the largest term, via sunny ($0.2\cdot0.8$). Max is used where the sum over the middle state is needed.
> - D: The index order of $a_{ij}$ is swapped: $\sum_k a_{3k}a_{k2}=0.1\cdot0.3+0.1\cdot0.6+0.8\cdot0.1=0.17$. This is "cloudy in two days after a sunny day".

### Q3 (slide 4)

One state of a Markov chain has the self-loop probability $a_{ii}=0.9$. One time step is a 10 ms frame. The probability of staying in this state for exactly $T$ frames is $a_{ii}^{T-1}(1-a_{ii})$. What is the mean time spent in the state?

- A) 100.0 ms
- B) 11.1 ms
- C) 90.0 ms
- D) 10.0 ms

> [!success]- Answer
> **A.** The duration is geometric with mean $1/(1-a_{ii})=1/0.1=10$ frames, so $10\cdot10=100$ ms.
> - B: The mean is taken as $1/a_{ii}=1/0.9\approx1.11$ frames, so 11.1 ms. The leaving probability is $1-a_{ii}$, not $a_{ii}$.
> - C: $a_{ii}/(1-a_{ii})=9$ frames counts only the extra stays after the first frame. The first frame belongs to the stay too.
> - D: 1 frame is the most likely single duration (the mode of the geometric distribution), not its mean.

## 2. From Markov models to HMMs (slides 5-9)

### Q4 (slide 5)

A Markov chain of the weather has three states ($S_1$ rain, $S_2$ cloudy, $S_3$ sunny), an initial distribution $\pi$ and a transition matrix $A$. You now sit in a room with no window. Each day you only see whether a colleague comes in with an umbrella or without one. People carry an umbrella on rainy days and also on some cloudy days. How must the model change?

- A) The weather states stay. A new matrix with entries $P(\text{observation }v_k\mid S_j)$ is added, one distribution over the observations per state. $\pi$ and $A$ stay.
- B) The states become "umbrella" and "no umbrella". A new $2\times2$ matrix of moves between these two states takes the place of $A$. $\pi$ stays.
- C) The weather states stay. A new matrix with entries $P(S_j\mid\text{observation }v_k)$ is added, one distribution over the states per observation. $\pi$ and $A$ stay.
- D) Nothing new is needed. With $\pi$ and $A$ known, the umbrella tells you the weather of each day, so the state is still read off directly.

> [!success]- Answer
> **A.** The weather is still the state. The observation is now a probabilistic function of the state, so each state $j$ needs a distribution $b_j(k)=P(o_t=v_k\mid q_t=S_j)$. This is $B$. Because an umbrella can come with more than one kind of weather, one observation does not tell the state: the state is hidden.
> - B: The umbrella is the observation, not the state. The weather still moves by $A$, and the umbrella depends on the weather.
> - C: The conditional is reversed. The model needs "how likely is this observation in this state", $P(v_k\mid S_j)$. The probability of the state given the observation is what we later infer.
> - D: An umbrella is also seen on cloudy days, so the observation does not fix the state. Without $B$ the model cannot link the umbrella to the weather at all.

### Q5 (slide 6)

Behind a curtain a person has two coins. Before each toss the person picks one of the two coins, and every pick is possible at every toss. Both coins can show H and T. You only hear the results $O=(H,T,T,H)$. How many coin sequences (which coin was used at each of the 4 tosses) can have produced $O$ with non-zero probability?

- A) 8
- B) 16
- C) 6
- D) 32

> [!success]- Answer
> **B.** Each of the 4 tosses used coin 1 or coin 2: $2^4=16$ sequences. Every coin can show every result, so all 16 can give $H,T,T,H$. They only differ in probability. So the coin sequence behind $O$ is not unique.
> - A: $2\cdot4=8$ adds the choices of the 4 tosses. The choices multiply: $2\cdot2\cdot2\cdot2$.
> - C: $\binom{4}{2}=6$ counts the orders of two H and two T. These are outcome sequences, not coin sequences.
> - D: $2^5$ counts one extra choice for the start. The first coin is the coin of the first toss, so there are only 4 choices.

### Q6 (slide 7)

An HMM for a coin game has two states, coin 1 and coin 2. The transitions are $a_{11},a_{12},a_{21},a_{22}$, where $a_{ij}$ is the probability of using coin $j$ next after coin $i$. Coin $j$ shows H with probability $P_j$ and T with probability $1-P_j$. How many free parameters (numbers that can be chosen independently) does this model have, not counting the initial distribution $\pi$?

- A) 6
- B) 2
- C) 4
- D) 8

> [!success]- Answer
> **C.** Each row of $A$ has 2 entries that sum to 1: 1 free number per row, so $a_{11}$ and $a_{22}$. Each coin has one free bias: $P_1$ and $P_2$. Total $2+2=4$.
> - A: The four entries of $A$ are all counted as free. The row sums $a_{11}+a_{12}=1$ and $a_{21}+a_{22}=1$ are forgotten.
> - B: Only the two biases are counted. The transitions between the coins are parameters too.
> - D: No sum-to-1 rule is used at all: 4 transitions and 4 emission probabilities $P(H),P(T)$.

### Q7 (slide 8)

Two HMMs are trained by maximum likelihood on the same sequence of coin tosses: a two-coin model (4 free parameters) and a three-coin model (9 free parameters). The three-coin model reaches the higher likelihood $P(O\mid\lambda)$. Someone concludes: "So the person behind the curtain really has three coins." Which statement is correct?

- A) The claim is right: maximum likelihood picks the model with the larger $P(O\mid\lambda)$, and that model shows the true number of coins.
- B) The claim is wrong: a model with more parameters always gets a lower likelihood on its training data, so the result points to a bug.
- C) The claim is right once the sequence has at least 9 tosses, one per free parameter. Below 9 tosses the claim is wrong.
- D) The claim is wrong: the three-coin model contains the two-coin model as a special case, so its best fit on the same data is never worse.

> [!success]- Answer
> **D.** Set the third coin equal to one of the others and the three-coin model behaves like the two-coin model. So its maximum likelihood on the same data is at least as high. A higher likelihood on the training data is no proof. The sequence must be long and rich enough to justify 9 parameters, or the larger model only fits chance patterns.
> - A: Maximum likelihood compares fits on the data. The nested larger model always fits at least as well, so the comparison alone cannot show the true number of coins.
> - B: It is the other way round: more parameters give a likelihood at least as high on the training data.
> - C: One toss per parameter is far too little to estimate 9 numbers, and even with many tosses the nested larger model still fits at least as well.

### Q8 (slide 9)

![[cs568_lecture_13_l13_p009.jpg]]

Behind a curtain there are $N=3$ urns with balls of $M=4$ colours. A person picks an urn by an internal random process, draws a ball, shows it, and puts it back. In the HMM of this experiment the urns are the states $S_1,S_2,S_3$ and the colours are the observation symbols $v_1,\dots,v_4$. $B=\{b_j(k)\}$ is the $3\times4$ emission matrix, with the state as the first index. What is $b_2(3)$?

- A) The probability that the person moves from urn 2 to urn 3 for the next draw.
- B) The probability of drawing a ball of colour 3, given that the ball comes from urn 2.
- C) The probability that the ball came from urn 2, given that its colour is colour 3.
- D) The probability that urn 2 is chosen and a ball of colour 3 is drawn, both together.

> [!success]- Answer
> **B.** $b_j(k)=P(o_t=v_k\mid q_t=S_j)$, so $b_2(3)$ is the share of colour 3 among the balls in urn 2. The ball goes back so that this share does not change in time. Every urn holds every colour, so the colour does not tell the urn: the state is hidden.
> - A: This is the transition $a_{23}$ in $A$, not an entry of $B$.
> - C: The conditional is reversed. $B$ gives the colour given the urn, not the urn given the colour.
> - D: This is the joint probability $P(q_t=S_2,\ o_t=v_3)$. $b_2(3)$ is conditioned on urn 2, so the chance of choosing urn 2 is not in it.

## 3. Elements of an HMM and the three problems (slides 10-12)

### Q9 (slide 10)

An HMM has $N$ states and $M$ observation symbols. Its emission probabilities are $b_j(k)=P(o_t=v_k\mid q_t=S_j)$ for $j=1,\dots,N$ and $k=1,\dots,M$. Which statement about the sums of the $b_j(k)$ is correct?

- A) For each state $j$, $\sum_{k=1}^{M}b_j(k)=1$. The sum $\sum_{j=1}^{N}b_j(k)$ over the states need not be 1.
- B) For each symbol $k$, $\sum_{j=1}^{N}b_j(k)=1$. The sum $\sum_{k=1}^{M}b_j(k)$ over the symbols need not be 1.
- C) For each $j$, $\sum_{k=1}^{M}b_j(k)=1$, and also for each $k$, $\sum_{j=1}^{N}b_j(k)=1$.
- D) Only the total of all entries is fixed: $\sum_{j=1}^{N}\sum_{k=1}^{M}b_j(k)=1$, like a joint distribution.

> [!success]- Answer
> **A.** For a fixed state $S_j$, $b_j(\cdot)$ is a distribution over the outcomes $v_1,\dots,v_M$, so it sums to 1. A sum over $j$ runs over the condition, not over the outcomes, so nothing fixes it. A symbol can be likely in every state.
> - B: The sum runs over the condition (the state). It would mean that each symbol is shared out among the states.
> - C: The second rule is not required. One symbol can have $b_j(k)=0.9$ in two states, which gives a column sum of 1.8.
> - D: $b_j(k)$ is conditioned on the state, so each of the $N$ rows sums to 1 and the total is $N$, not 1.

### Q10 (slide 11)

A three-coin HMM has the biases $P(H)=0.5$, $0.75$ and $0.25$ for coins 1, 2 and 3. The first coin $q_1$ is drawn from $\pi=(\frac13,\frac13,\frac13)$, and every entry of $A$ is $\frac13$, where $a_{ij}$ is the probability of coin $j$ next after coin $i$. What is $P(O=(H,H,H,H,H)\mid\lambda)$?

- A) 0.08984
- B) 0.23730
- C) 0.00013
- D) 0.03125

> [!success]- Answer
> **D.** Every row of $A$ is $(\frac13,\frac13,\frac13)$, so each toss picks a coin uniformly, whatever the last coin was. Then
> $$P(o_t=H)=\tfrac13(0.5+0.75+0.25)=0.5$$
> and the tosses are independent: $0.5^5=0.03125$.
> - A: $\frac13(0.5^5+0.75^5+0.25^5)\approx0.08984$ keeps one coin for all five tosses. That would need $A=I$, not this $A$.
> - B: $0.75^5$ takes the coin with the largest $P(H)$ for every toss. The coins must be averaged, not maximised.
> - C: $(\frac13)^5\cdot0.5^5\approx0.00013$ is one single path (coin 1 five times). The sum over all $3^5$ coin paths is missing.
>
> Note: slide 11 writes "choose an initial state $S_1$". It means the first state $q_1$, which can be any of the states.

### Q11 (slide 12)

The three HMM problems are: Problem 1, given $O$ and $\lambda$, find $P(O\mid\lambda)$. Problem 2, given $O$ and $\lambda$, find the best state sequence. Problem 3, given $O$, find the $\lambda$ that maximises $P(O\mid\lambda)$. Three tasks are: (a) ten word models exist, say which word was spoken; (b) find which frames of a recording belong to which state; (c) build the model of a word from its recordings. Which statement is correct?

- A) Task (b) is Problem 1: the Forward procedure gives the state of each frame as part of $P(O\mid\lambda)$.
- B) Task (a) is Problem 1: compute $P(O\mid\lambda_w)$ for each of the ten models and take the largest.
- C) Task (c) is Problem 2: the Viterbi algorithm returns the parameters $A$, $B$ and $\pi$ of the word.
- D) Task (a) is Problem 3: retrain each of the ten models on the test word and take the one that changes least.

> [!success]- Answer
> **B.** The models are given and fixed, and each one is scored by its likelihood of the observed word. The Forward procedure computes $P(O\mid\lambda_w)$.
> - A: Task (b) is Problem 2 (Viterbi). The Forward procedure adds over all state paths and returns only one number, $P(O\mid\lambda)$.
> - C: Task (c) is Problem 3 (Baum-Welch). Viterbi finds a state sequence for a given model, it does not change the model.
> - D: In recognition the models are not changed. Only Problem 3 changes parameters, and it is used for training.

## 4. Forward and Backward procedures (slides 13-16)

### Q12 (slide 13)

An HMM has two states $S_1,S_2$ and the symbols H and T. $\pi=(0.5,\ 0.5)$. $a_{ij}=P(q_{t+1}=S_j\mid q_t=S_i)$ with $a_{11}=0.8$, $a_{12}=0.2$, $a_{21}=0.3$, $a_{22}=0.7$. $b_j(k)=P(o_t=k\mid q_t=S_j)$ with $b_1(H)=0.6$, $b_1(T)=0.4$, $b_2(H)=0.1$, $b_2(T)=0.9$. The observations are $O=(H,T,T)$. $P(O\mid\lambda)$ is a sum over all state paths $Q$ of $P(O\mid Q,\lambda)\,P(Q\mid\lambda)$. What is the term of the path $Q=(S_1,S_1,S_2)$?

- A) 0.03456
- B) 0.02592
- C) 0.08000
- D) 0.01728

> [!success]- Answer
> **D.** $P(Q\mid\lambda)=\pi_1\,a_{11}\,a_{12}=0.5\cdot0.8\cdot0.2=0.08$ (Markov assumption: one $a$ per step).
> $P(O\mid Q,\lambda)=b_1(H)\,b_1(T)\,b_2(T)=0.6\cdot0.4\cdot0.9=0.216$ (output independence: one $b$ per frame).
> Term: $0.08\cdot0.216=0.01728$.
> - A: $\pi_1=0.5$ is forgotten: $0.8\cdot0.2\cdot0.216=0.03456$.
> - B: The index order is swapped: $a_{21}=0.3$ is used for the move $S_1\to S_2$, $0.5\cdot0.8\cdot0.3\cdot0.216=0.02592$.
> - C: Only $P(Q\mid\lambda)$. The factor $P(O\mid Q,\lambda)$ is left out.
>
> Note: slide 13 writes $b_{q_1}(o_{q_1})$. The observation index is the time, so it should read $b_{q_1}(o_1)$.

### Q13 (slide 14)

![[cs568_lecture_13_l13_p014.jpg]]

Direct evaluation of $P(O\mid\lambda)$ adds one term for every state sequence $q_1,\dots,q_T$, and each term is a product that costs about $2T$ operations. An HMM has $N=3$ states, and the observation sequence has $T=4$ frames. About how many operations does direct evaluation need?

- A) 81
- B) 512
- C) 648
- D) 36

> [!success]- Answer
> **C.** There are $N^T=3^4=81$ state sequences, each about $2T=8$ operations: $2T\cdot N^T=8\cdot81=648$.
> - A: 81 is the number of sequences. The cost of $2T$ per sequence is forgotten.
> - B: The number of sequences is taken as $T^N=4^3=64$, which gives $8\cdot64=512$. Each of the $T$ times has $N$ choices, so it is $N^T$.
> - D: $N^2T=36$ is the cost of the Forward procedure on the trellis, not of direct evaluation.
>
> Note: slide 14 prints "$10^7$" for $N=5$, $T=100$. This is a typo for $10^{72}$: $2\cdot100\cdot5^{100}\approx1.6\times10^{72}$.

### Q14 (slide 15)

An HMM has two states $S_1,S_2$ and the symbols H and T. $\pi=(0.5,\ 0.5)$. $a_{ij}=P(q_{t+1}=S_j\mid q_t=S_i)$ with $a_{11}=0.8$, $a_{12}=0.2$, $a_{21}=0.3$, $a_{22}=0.7$. $b_j(k)=P(o_t=k\mid q_t=S_j)$ with $b_1(H)=0.6$, $b_1(T)=0.4$, $b_2(H)=0.1$, $b_2(T)=0.9$. Use the Forward procedure to compute $P(O\mid\lambda)$ for $O=(H,T,T)$.

- A) 0.072225
- B) 0.115125
- C) 0.230250
- D) 0.138875

> [!success]- Answer
> **B.** $\alpha_1(i)=\pi_ib_i(H)$: $\alpha_1=(0.3,\ 0.05)$.
> $\alpha_2(1)=(0.3\cdot0.8+0.05\cdot0.3)\cdot0.4=0.102$, $\alpha_2(2)=(0.3\cdot0.2+0.05\cdot0.7)\cdot0.9=0.0855$.
> $\alpha_3(1)=(0.102\cdot0.8+0.0855\cdot0.3)\cdot0.4=0.0429$, $\alpha_3(2)=(0.102\cdot0.2+0.0855\cdot0.7)\cdot0.9=0.072225$.
> $P(O\mid\lambda)=0.0429+0.072225=0.115125$.
> - A: Max is used in the termination: only $\alpha_3(2)=0.072225$ is kept. The model can end in any state, so the last column is added.
> - C: $\pi$ is forgotten at $t=1$: $\alpha_1=(0.6,\ 0.1)$, and every later value doubles.
> - D: The index order of $a_{ij}$ is swapped ($a_{ji}$ used): $\alpha_2=(0.1,\ 0.1125)$, $\alpha_3=(0.041,\ 0.097875)$, sum 0.138875.
>
> Cost: about $N^2T$ operations. Slide 15 says 3000 for $N=5$, $T=100$, while $N^2T=2500$; both are rough counts of the same size.

### Q15 (slide 16)

An HMM has two states $S_1,S_2$ and the symbols H and T, with $a_{11}=0.8$, $a_{12}=0.2$, $a_{21}=0.3$, $a_{22}=0.7$ with $a_{ij}=P(q_{t+1}=S_j\mid q_t=S_i)$, $b_1(T)=0.4$, $b_2(T)=0.9$, and $O=(H,T,T)$, $T=3$. The Backward variable is $\beta_t(i)=P(o_{t+1},\dots,o_T\mid q_t=S_i,\lambda)$, with $\beta_3(i)=1$. What is $\beta_1(1)$?

- A) 0.3805
- B) 0.1134
- C) 0.2550
- D) 0.2950

> [!success]- Answer
> **D.** $\beta_t(i)=\sum_j a_{ij}\,b_j(o_{t+1})\,\beta_{t+1}(j)$.
> $t=2$ ($o_3=T$): $\beta_2(1)=0.8\cdot0.4+0.2\cdot0.9=0.5$, $\beta_2(2)=0.3\cdot0.4+0.7\cdot0.9=0.75$.
> $t=1$ ($o_2=T$): $\beta_1(1)=0.8\cdot0.4\cdot0.5+0.2\cdot0.9\cdot0.75=0.16+0.135=0.295$.
> - A: The index order of $a_{ij}$ is swapped ($a_{ji}$ used): $\beta_2=(0.59,\ 0.71)$, then $0.8\cdot0.4\cdot0.59+0.3\cdot0.9\cdot0.71=0.3805$.
> - B: Max is used in place of the sum: $\beta_2=(0.32,\ 0.63)$, then $\max(0.1024,\ 0.1134)=0.1134$.
> - C: The emission of the current time $o_t$ is used in place of $o_{t+1}$: for $\beta_1$ this takes $o_1=H$, $0.8\cdot0.6\cdot0.5+0.2\cdot0.1\cdot0.75=0.255$.

## 5. The Viterbi algorithm (slides 17-22)

### Q16 (slide 17)

There are two ways to define the "optimal" state sequence of an HMM for given $O$ and $\lambda$. Criterion 1: at each time $t$ separately, take the state with the largest $\gamma_t(i)=P(q_t=S_i\mid O,\lambda)$. Criterion 2: take the one complete sequence $Q$ with the largest $P(Q,O\mid\lambda)$ (the Viterbi path). Criterion 2 is the most widely used. Why does the lecture still work out criterion 1?

- A) Criterion 1 always gives a valid state sequence, while Viterbi can return a step with $a_{ij}=0$.
- B) Criterion 1 gives the same sequence as Viterbi, and it costs fewer operations.
- C) Viterbi needs the values $\gamma_t(i)$ of criterion 1 as its input, so they must come first.
- D) Its variable $\gamma_t(i)$ is needed again in Baum-Welch training, so it is defined here.

> [!success]- Answer
> **D.** The slide says so: $\gamma_t(i)$ is used again in the solution of Problem 3. Baum-Welch counts expected state visits with it.
> - A: It is the other way round. Criterion 1 can join states with $a_{ij}=0$ between them. Viterbi scores whole paths, so a zero transition never wins.
> - B: The two criteria can give different sequences, because criterion 1 maximises each time alone.
> - C: Viterbi uses its own variable $\delta_t(i)$ and back-pointers. It does not use $\gamma$.

### Q17 (slide 18)

For an HMM with two states and an observation sequence $O$, the Forward variables at $t=2$ are $\alpha_2(1)=0.12$, $\alpha_2(2)=0.06$, where $\alpha_t(i)=P(o_1,\dots,o_t,\ q_t=S_i\mid\lambda)$. The Backward variables are $\beta_2(1)=0.4$, $\beta_2(2)=0.5$, where $\beta_t(i)=P(o_{t+1},\dots,o_T\mid q_t=S_i,\lambda)$. What is $\gamma_2(1)=P(q_2=S_1\mid O,\lambda)$?

- A) 0.048
- B) 0.667
- C) 0.615
- D) 0.444

> [!success]- Answer
> **C.** $\alpha_2(i)\beta_2(i)=P(O,\ q_2=S_i\mid\lambda)$: $0.12\cdot0.4=0.048$ and $0.06\cdot0.5=0.03$.
> Their sum is $P(O\mid\lambda)=0.078$. So $\gamma_2(1)=0.048/0.078\approx0.615$.
> - A: $\gamma$ is not normalised: 0.048 is the joint probability $P(O,q_2=S_1\mid\lambda)$, not the posterior.
> - B: Only $\alpha$ is normalised, $0.12/0.18$. The future observations in $\beta$ are left out.
> - D: Only $\beta$ is normalised, $0.4/0.9$. The past observations in $\alpha$ are left out.

### Q18 (slide 19)

An HMM has $a_{21}=0$: it can never move from $S_2$ to $S_1$. A decoder picks, for each time $t$ separately, the state with the largest posterior $\gamma_t(i)=P(q_t=S_i\mid O,\lambda)$. It returns $q_4^*=S_2$ and $q_5^*=S_1$. Which statement is correct?

- A) The answer is not a valid path, because $S_2\to S_1$ has probability 0. Each $\gamma_t$ is maximised alone, so no step between two times is checked.
- B) The answer cannot occur: $\gamma_5(1)$ contains the factor $a_{21}=0$, so $\gamma_5(1)$ is 0 whenever the decoder chose $S_2$ at $t=4$.
- C) The answer is a valid path: $\gamma_4$ and $\gamma_5$ come from one Forward-Backward pass, so the chosen states always lie on one common path.
- D) The answer is not a valid path, and Viterbi has the same flaw: it also takes the best state at each time from $\delta_t$ and then joins them.

> [!success]- Answer
> **A.** $\gamma_4$ and $\gamma_5$ each add over many paths, and the winners at the two times can come from different paths. The step $S_2\to S_1$ has probability 0, so the model can never produce this sequence.
> - B: $\gamma_5(1)$ adds over all paths into $S_1$ at $t=5$, also those from $S_1$ at $t=4$. It does not depend on the state the decoder chose at $t=4$.
> - C: Each time is decided alone. Nothing makes the two winners lie on one common path.
> - D: Viterbi scores whole paths and follows back-pointers. A path with a zero transition gets $\delta=0$ and never wins.

### Q19 (slide 20)

For an HMM, $\alpha_t(i)=P(o_1,\dots,o_t,\ q_t=S_i\mid\lambda)$, and $\delta_t(i)$ is the largest value of $P(q_1,\dots,q_{t-1},\ q_t=S_i,\ o_1,\dots,o_t\mid\lambda)$ over the earlier states $q_1,\dots,q_{t-1}$. A model has two states and $t=3$. The four state paths that end in $S_2$ at $t=3$ have the joint probabilities (path and $o_1,o_2,o_3$ together) 0.012, 0.030, 0.004 and 0.018. What are $\alpha_3(2)$ and $\delta_3(2)$?

- A) $\alpha_3(2)=0.064$ and $\delta_3(2)=0.064$
- B) $\alpha_3(2)=0.064$ and $\delta_3(2)=0.030$
- C) $\alpha_3(2)=0.030$ and $\delta_3(2)=0.064$
- D) $\alpha_3(2)=0.030$ and $\delta_3(2)=0.030$

> [!success]- Answer
> **B.** $\alpha_3(2)$ adds all paths into $S_2$: $0.012+0.030+0.004+0.018=0.064$. $\delta_3(2)$ keeps only the best one: $\max=0.030$. So $\delta_t(i)\le\alpha_t(i)$ always. The back-pointer $\Psi$ stores which predecessor gave the max, so that the path can be read back at the end.
> - A: Sum is used in Viterbi: $\delta$ is computed like $\alpha$.
> - C: The two definitions are swapped: $\alpha$ takes the max and $\delta$ the sum.
> - D: Max is used in the Forward procedure: $\alpha$ is computed like $\delta$.

### Q20 (slide 21)

![[cs568_lecture_13_l13_p021.jpg]]

The figure shows the Viterbi grid of an HMM with 4 states and 10 time steps. The red line is the decoded state path. $\delta_t(i)$ is the largest probability of a path that ends in $S_i$ at time $t$, together with $o_1,\dots,o_t$, and $a_{ij}=P(q_{t+1}=S_j\mid q_t=S_i)$. $\Psi_t(j)$ is the Viterbi back-pointer stored at the node of state $S_j$ at time $t$. Which expression is the label $\Psi_{t=5}(S_4)$ by definition?

- A) $\arg\max_{1\le i\le4}\,[\delta_4(i)\,a_{i4}]$
- B) $\max_{1\le i\le4}\,[\delta_4(i)\,a_{i4}]$
- C) $\arg\max_{1\le i\le4}\,\delta_4(i)$
- D) $\arg\max_{1\le i\le4}\,[\delta_4(i)\,a_{4i}]$

> [!success]- Answer
> **A.** $\Psi_t(j)=\arg\max_i[\delta_{t-1}(i)\,a_{ij}]$ is the best predecessor of $S_j$ at time $t$. So $\Psi_5(S_4)=S_2$ says: the best path into ($t=5$, $S_4$) comes from $S_2$ at $t=4$. In the figure the path is $S_2,S_2,S_3,S_2,S_4$, then $S_1$ for $t=6,\dots,9$, then $S_3$ at $t=10$. It is found backwards: first the best state at $t=10$, then the back-pointers.
> - B: Max in place of argmax. This is the best score (a probability, which $\delta_5(4)$ uses), not the state it comes from. The label stores a state, $S_2$.
> - C: The move into $S_4$ is left out. This is the state that is best at $t=4$ alone, which can have a small $a_{i4}$.
> - D: The index order of $a_{ij}$ is swapped: $a_{4i}$ is the move from $S_4$ to $S_i$, forward in time. The back-pointer needs the move from $S_i$ into $S_4$.

### Q21 (slide 22)

An HMM has two states $S_1,S_2$ and the symbols H and T. $\pi=(0.5,\ 0.5)$. $a_{ij}=P(q_{t+1}=S_j\mid q_t=S_i)$ with $a_{11}=0.8$, $a_{12}=0.2$, $a_{21}=0.3$, $a_{22}=0.7$. $b_j(k)=P(o_t=k\mid q_t=S_j)$ with $b_1(H)=0.6$, $b_1(T)=0.4$, $b_2(H)=0.1$, $b_2(T)=0.9$. The observations are $O=(H,T,T)$. Run the Viterbi algorithm. What is $P^*$, the probability of the single best state path together with $O$?

- A) 0.06474
- B) 0.06804
- C) 0.03402
- D) 0.05103

> [!success]- Answer
> **C.** $\delta_1=(0.3,\ 0.05)$.
> $\delta_2(1)=\max(0.24,\ 0.015)\cdot0.4=0.096$, $\Psi_2(1)=1$. $\delta_2(2)=\max(0.06,\ 0.035)\cdot0.9=0.054$, $\Psi_2(2)=1$.
> $\delta_3(1)=\max(0.0768,\ 0.0162)\cdot0.4=0.03072$, $\Psi_3(1)=1$. $\delta_3(2)=\max(0.0192,\ 0.0378)\cdot0.9=0.03402$, $\Psi_3(2)=2$.
> $P^*=\max(0.03072,\ 0.03402)=0.03402$. Backtracking gives the path $S_1,S_2,S_2$.
> - A: Sum is used in the Viterbi termination: $0.03072+0.03402=0.06474$. That mixes two different paths.
> - B: $\pi$ is forgotten at $t=1$: $\delta_1=(0.6,\ 0.1)$, and every later value doubles.
> - D: The index order of $a_{ij}$ is swapped ($a_{ji}$ used): $\delta_2=(0.096,\ 0.081)$, $\delta_3(2)=\max(0.0288,\ 0.0567)\cdot0.9=0.05103$.

### Q22 (slide 22, log domain)

An HMM has two states. $\pi=(0.5,\ 0.5)$. $a_{ij}=P(q_{t+1}=S_j\mid q_t=S_i)$ with $a_{11}=0.7$, $a_{12}=0.3$, $a_{21}=0.5$, $a_{22}=0.5$. For the first observation $b_1(o_1)=0.4$ and $b_2(o_1)=0.08$; for the second $b_1(o_2)=0.3$. The Viterbi algorithm is run with logarithms: $\ln\delta_t(j)=\max_i[\ln\delta_{t-1}(i)+\ln a_{ij}]+\ln b_j(o_t)$, with $\ln\delta_1(i)=\ln\pi_i+\ln b_i(o_1)$. What is $\ln\delta_2(1)$ (natural log)?

- A) $-3.037$
- B) $-1.966$
- C) $-2.477$
- D) $-3.170$

> [!success]- Answer
> **D.** $\ln\delta_1(1)=\ln0.2\approx-1.609$, $\ln\delta_1(2)=\ln0.04\approx-3.219$.
> Candidates: $-1.609+\ln0.7=-1.966$ and $-3.219+\ln0.5=-3.912$. Max: $-1.966$ (from $S_1$).
> Add $\ln0.3\approx-1.204$: $\ln\delta_2(1)\approx-3.170$. Check: $\ln(0.2\cdot0.7\cdot0.3)=\ln0.042\approx-3.170$.
> The log keeps the order of the candidates, so the same predecessor wins and the path does not change.
> - A: Sum is used in Viterbi: $\ln((0.14+0.02)\cdot0.3)=\ln0.048\approx-3.037$. This is the Forward value.
> - B: $\ln b_1(o_2)$ is left out: only the max, $-1.966$.
> - C: $\pi$ is forgotten at $t=1$: $\ln(0.4\cdot0.7\cdot0.3)=\ln0.084\approx-2.477$.

## 6. Baum-Welch re-estimation (slides 23-25)

### Q23 (slide 23)

For an HMM the Backward variable is $\beta_t(i)=P(o_{t+1},\dots,o_T\mid q_t=S_i,\lambda)$. The transition posterior $\xi_t(i,j)=P(q_t=S_i,\ q_{t+1}=S_j\mid O,\lambda)$ has the numerator $\alpha_t(i)\,a_{ij}\,b_j(o_{t+1})\,\beta_{t+1}(j)$. Which observations does the factor $\beta_{t+1}(j)$ account for?

- A) $o_{t+2},\dots,o_T$
- B) $o_{t+1},\dots,o_T$
- C) $o_1,\dots,o_{t+1}$
- D) $o_{t+2}$ only

> [!success]- Answer
> **A.** Put $t+1$ for $t$ in the definition: $\beta_{t+1}(j)=P(o_{t+2},\dots,o_T\mid q_{t+1}=S_j,\lambda)$. The numerator has three parts: $\alpha_t(i)$ for $o_1,\dots,o_t$, $a_{ij}b_j(o_{t+1})$ for the move and $o_{t+1}$, $\beta_{t+1}(j)$ for the rest. Each observation is used once.
> The states are hidden, so the real transitions cannot be counted. $\xi_t(i,j)$ is the probability that $S_i\to S_j$ happened at time $t$, and Baum-Welch counts with it.
> - B: The index is not shifted: $o_{t+1}$ is already in $b_j(o_{t+1})$ and would be counted twice.
> - C: This is the past side, which $\alpha$ covers. $\beta$ looks forward in time.
> - D: $\beta$ covers all remaining observations up to $o_T$, not only the next one.

### Q24 (slide 24)

For a two-state HMM and an observation sequence $O$: $\alpha_1(1)=0.2$, $\alpha_1(2)=0.1$; $a_{ij}=P(q_{t+1}=S_j\mid q_t=S_i)$ with $a_{11}=0.7$, $a_{12}=0.3$, $a_{21}=0.4$, $a_{22}=0.6$; $b_1(o_2)=0.5$, $b_2(o_2)=0.2$; $\beta_2(1)=0.6$, $\beta_2(2)=0.5$. Here $\alpha$ and $\beta$ are the Forward and Backward variables. What is $\xi_1(2,1)=P(q_1=S_2,\ q_2=S_1\mid O,\lambda)$?

- A) 0.012
- B) 0.667
- C) 0.182
- D) 0.138

> [!success]- Answer
> **C.** Numerators $\alpha_1(i)\,a_{ij}\,b_j(o_2)\,\beta_2(j)$:
> $(1,1)$: $0.2\cdot0.7\cdot0.5\cdot0.6=0.042$, $(1,2)$: $0.2\cdot0.3\cdot0.2\cdot0.5=0.006$, $(2,1)$: $0.1\cdot0.4\cdot0.5\cdot0.6=0.012$, $(2,2)$: $0.1\cdot0.6\cdot0.2\cdot0.5=0.006$.
> Their sum is $P(O\mid\lambda)=0.066$. So $\xi_1(2,1)=0.012/0.066\approx0.182$. The four $\xi_1$ values sum to 1.
> - A: $\xi$ is not normalised: 0.012 is the joint $P(q_1=S_2,q_2=S_1,O\mid\lambda)$.
> - B: Normalised over row $i=2$ only, $0.012/(0.012+0.006)$. That is $P(q_2=S_1\mid q_1=S_2,O,\lambda)$, a conditional on $q_1$.
> - D: The index order of $a_{ij}$ is swapped ($a_{ji}$ used): numerators $0.042,\ 0.008,\ 0.009,\ 0.006$, then $0.009/0.065\approx0.138$.

### Q25 (slide 25)

An HMM with two states was run on $O=(H,T,T)$, so $T=3$. The posteriors of the old model are $\gamma_t(i)=P(q_t=S_i\mid O,\lambda)$: $\gamma_1=(0.769,\ 0.231)$, $\gamma_2=(0.443,\ 0.557)$, $\gamma_3=(0.373,\ 0.627)$. The transition posteriors $\xi_t(i,j)=P(q_t=S_i,\ q_{t+1}=S_j\mid O,\lambda)$ include $\xi_1(1,2)=0.352$ and $\xi_2(1,2)=0.159$. What is the Baum-Welch estimate $\hat a_{12}$ of $P(q_{t+1}=S_2\mid q_t=S_1)$?

- A) 0.32
- B) 0.42
- C) 0.65
- D) 0.46

> [!success]- Answer
> **B.** Expected number of moves $S_1\to S_2$, divided by the expected number of moves out of $S_1$. A move needs a next time step, so both sums stop at $T-1=2$:
> $$\hat a_{12}=\frac{\xi_1(1,2)+\xi_2(1,2)}{\gamma_1(1)+\gamma_2(1)}=\frac{0.511}{1.212}\approx0.42$$
> - A: The denominator runs to $T$: $0.511/1.585\approx0.32$. At $t=3$ there is no move out of $S_1$. Only $\hat b_j(k)$ sums to $T$.
> - C: The denominator uses the states $j=2$: $0.511/(0.231+0.557)\approx0.65$. The moves are counted out of $S_i$, the first index.
> - D: Only $t=1$ is used: $0.352/0.769\approx0.46$. Both times with a next step must be added.
