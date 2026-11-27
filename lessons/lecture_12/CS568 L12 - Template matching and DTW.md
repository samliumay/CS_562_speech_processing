---
course: CS 568 Speech Processing
lecture: 12
title: Template matching
tags: [cs568, lecture]
slides: lectures/lecture_12/l12.pdf
full_notes: lecture_notes/lecture_12/CS568_Lecture_12_Template_Matching.pdf
---
# CS 568 · Lecture 12 · Template matching

> [!summary] In short
> - ASR turns speech into text, independent of microphone, speaker and environment. History: rules, then templates with DTW, then statistical models.
> - Template matching: store example patterns per word, assign an incoming word to the closest template.
> - Features: log filter-bank powers (spectral envelope), with the mean log-power removed.
> - Dynamic time warping (DTW) finds the cheapest monotonic alignment: $D(i,j)=\min[D(i,j-1),D(i-1,j),D(i-1,j-1)]+d(i,j)$, cost $O(nN)$.
> - Refinements: distortion penalties, length normalisation, score pruning.

## 1. Introduction to ASR (slides 2-6)
![[cs568_lecture_12_l12_p002.jpg]]
![[cs568_lecture_12_l12_p003.jpg]]
![[cs568_lecture_12_l12_p004.jpg]]
![[cs568_lecture_12_l12_p005.jpg]]
![[cs568_lecture_12_l12_p006.jpg]]

**Notes.** Automatic speech recognition (ASR) converts a speech signal into a text transcription, accurately and efficiently, whatever the recording device, the speaker or the acoustic environment.

History:
- **Rule-based** (1950s on): rules from acoustic-phonetic knowledge or ad-hoc measurements. Weak, because of coarticulation and hard rule decisions.
- **Template matching** (1960s-70s): pattern matching with DTW. Fine for isolated words, but does not model the variability of speech.
- **Statistical** (1970s-80s on): acoustic and language models (HMMs, n-grams). Now used almost everywhere.

Modern architecture (slide 5): feature analysis turns speech $s[n]$ into feature vectors $X$. Pattern classification (decoding) uses an acoustic model (HMMs), a language model (n-grams) and a word lexicon. Confidence scoring attaches a confidence to each word. The decoder applies the MAP rule to word sequences:

$$\hat W=\arg\max_W P(W\mid X)=\arg\max_W p(X\mid W)\,P(W)$$

In template matching, a distance to a stored example plays the role of $p(X\mid W)$. Next lectures: HMMs, HMM refinements, large-vocabulary ASR, HTK.

> [!question]- Check (slide 2): A recogniser works well for its developer, who uses one headset in a quiet office. It fails for a child who speaks into a phone in a car. Which requirements of the slide does it break?
> All three independence requirements.
> - Device: headset against phone microphone.
> - Speaker: an adult developer against a child (age, and maybe gender and accent).
> - Acoustic environment: quiet office against a noisy car.
> The goal "accurate and efficient speech to text" is only met for one device, one speaker and one room.

> [!question]- Check (slide 3): Find the mistake: "Template-matching systems failed on isolated words because of coarticulation and hard decisions. Statistical methods then replaced them."
> The reason belongs to the wrong generation. Coarticulation and inflexible hard decisions were the weakness of the **rule-based** systems.
> Template matching with DTW worked reasonably well on isolated words. Its weakness: it did not properly use information about the **variability** of speech (a template is one example, not a model of how examples vary).
> The second sentence is correct: statistical models (HMMs, n-grams) took over.

> [!question]- Check (slide 4): In the milestones figure, which column is the subject of this lecture? What vocabulary size and tasks does it name, and where does "dynamic programming" appear?
> The second column, "Medium Vocabulary, Template-based", at about 1967 to 1977. Its tasks: isolated words, connected digits, continuous speech. Its techniques: pattern recognition, LPC analysis, clustering algorithms, level building.
> "Dynamic programming" and "time-normalization" already appear one column earlier (small vocabulary, acoustic-phonetics based, isolated words, about 1962 to 1967). So the alignment idea is older than the template systems that made it famous.
> The next column (about 1977 to 1987) is "Large Vocabulary, Statistical-based" with hidden Markov models.

> [!question]- Check (slide 5): In the block diagram, what are $X$, $\hat W$ and the numbers (0.9) and (0.8)? Which block supplies $p(X\mid W)$ and which supplies $P(W)$?
> - $X$: the sequence of feature vectors that feature analysis computes from the speech $s[n]$.
> - $\hat W$: the recognised word sequence, here "Hello World".
> - (0.9), (0.8): the confidence scores of the two words, added by the confidence scoring block (utterance verification).
> - Acoustic model (HMM): $p(X\mid W)$, how well the features fit the words.
> - Language model (N-gram): $P(W)$, how likely the word sequence is before any sound is heard.
> - Word lexicon: the list of words and how each is built from the units of the acoustic model.
> The pattern classification block searches for the $W$ with the largest product.

## 2. Pattern matching and features (slides 7-11)
![[cs568_lecture_12_l12_p007.jpg]]
![[cs568_lecture_12_l12_p008.jpg]]
![[cs568_lecture_12_l12_p009.jpg]]
![[cs568_lecture_12_l12_p010.jpg]]
![[cs568_lecture_12_l12_p011.jpg]]

**Notes.** When one person repeats an isolated word, the acoustic pattern is similar. So: store templates for all words, compare an incoming word with each, pick the closest. This is a nearest-neighbour classifier over sequences.

Simple distance, assuming equal speed and known end points: split each word into frames (10 to 20 ms), compute a feature vector per frame, take the Euclidean distance between corresponding frames and sum:

$$D(X,Y)=\sum_{i=1}^{n}\lVert\mathbf x_i-\mathbf y_i\rVert$$

Good features: except in tonal languages, pitch carries little phonetic information. Ignore the harmonics and describe the spectral envelope. Use a filter bank on an auditory scale (mel, critical bands), then the log-power in each channel. The log lets weak formants count. Subtract the word's mean log-power to cancel vocal effort and microphone distance: a gain $g$ adds the constant $\log g$ to every channel, and the mean subtraction removes it.

Slide 10: 10-channel filter-bank analysis of "three" and two examples of "eight". Slide 11: the frame-by-frame distance matrix. For two examples of the same word there is a valley of small distances from bottom-left to top-right. It is not the straight diagonal, because the timing differs. For different words there is no such valley.

> [!example] Linear distance
> $X=(1,2,3)$, templates $A=(1,2,4)$ and $B=(3,3,1)$. $D(X,A)=1$, $D(X,B)=5$. $X$ is recognised as $A$.

> [!question]- Check (slide 7): The slide says the patterns are similar when "the same person" repeats "the same isolated word". What goes wrong if each of the two conditions is dropped? How do storage and work grow with the vocabulary?
> - Another person: a different vocal tract, pitch and accent give a different pattern for the same word. The stored templates fit only their speaker, so the system is speaker dependent. Each new user must record templates, or many templates per word are stored.
> - Not isolated: in connected speech the neighbours change the sounds of a word (coarticulation) and the word boundaries are unknown, so a whole-word template no longer lines up.
> - Growth: with $V$ words and $K$ templates per word, the system stores $V\cdot K$ patterns and compares the incoming word with all $V\cdot K$ of them. Both grow in proportion to the vocabulary.

> [!question]- Check (slide 8): In $D(X,Y)=\sum_{i=1}^{n}\lVert\mathbf x_i-\mathbf y_i\rVert$ name each symbol. Then compute $D$ for a word with frames $\mathbf x_1=(1,2)$, $\mathbf x_2=(3,1)$ and a template with frames $\mathbf y_1=(1,0)$, $\mathbf y_2=(0,5)$.
> - $\mathbf x_i$: feature vector of frame $i$ of the incoming word. $\mathbf y_i$: feature vector of frame $i$ of the template.
> - $\lVert\cdot\rVert$: Euclidean distance between the two vectors of the **same** frame index.
> - $\sum_{i=1}^{n}$: sum over the $n$ frames. Both words must have exactly $n$ frames.
> Frame 1: $\lVert(1-1,\ 2-0)\rVert=\sqrt{0^2+2^2}=2$.
> Frame 2: $\lVert(3-0,\ 1-5)\rVert=\sqrt{3^2+(-4)^2}=\sqrt{25}=5$.
> $D=2+5=7$.
> The formula needs the three assumptions of the slide: isolated words, the same speed, known start and end points.

> [!question]- Check (slide 9): (a) The same vowel is spoken at a pitch of 100 Hz and at 200 Hz. Why do the filter-bank features change very little? (b) A strong formant has power 100 and a weak one has power 1. Both grow by 50 %. Compare the change in power with the change in log-power (natural log).
> (a) Pitch changes the spacing of the harmonics, not the spectral envelope (the formants). One filter-bank channel is wide and sums the energy of several harmonics, so the fine harmonic structure is smoothed away and only the envelope remains.
> (b) Power: $150-100=50$ against $1.5-1=0.5$. In a distance on raw power the strong formant counts 100 times more.
> Log-power: $\ln150-\ln100=\ln1.5=0.405$ and $\ln1.5-\ln1=\ln1.5=0.405$. Both changes count the same.
> So the log lets a weak formant, which can be linguistically important, take part in the distance.

> [!question]- Check (slide 10): What do the two axes and the dot size show? What is the empty stretch inside both "eight" patterns, and what is the visible difference between the two "eight" examples?
> - Vertical axis: frequency, 0 to 5 kHz, in 10 channels (10 rows of dots). Horizontal axis: time in frames, about 0.5 s in total. Dot size: the slide has no legend. From slide 9 (log-power in each channel) the dot size is read as the log-power of that channel in that frame: a larger dot means more energy.
> - The empty stretch between "I" and "GHT" is the closure of the stop [t]: the mouth is closed and almost no sound comes out. Then the release gives a burst, mainly in the upper channels.
> - Both "eight" patterns have the same order of events (E, I, gap, GHT), but the timing differs: the burst after the gap starts clearly later in the middle pattern than in the bottom one.
> "three" looks different: a weak TH at the start, then strong energy through R and EE with no gap.

> [!question]- Check (slide 11): In the "Similar" panel, what does the blue curve follow, and what does its bend tell you? What would a frame-by-frame (linear) comparison use instead? Why is there no such curve in the "Different" panel?
> - The slide has no legend for the dots, so the meaning must be read from the figure. Look at the square block in the middle: there the silent gap of one "eight" meets the silent gap of the other. Silence against silence is a very small distance, and the dots there are large. Where the gap of one word meets the vowel of the other (large distance) the dots are tiny. So in this figure large dark dots mark frame pairs that are alike (small distance). The blue curve runs through them from the bottom-left corner to the top-right corner: for each part of one "eight" it shows the matching part of the other.
> - The curve is not straight. It is steeper in the first part and flatter near the end, so the speed relation between the two words changes inside the word. One stretch factor cannot describe that.
> - The linear distance of slide 8 uses the straight diagonal (frame $i$ with frame $i$). Where the blue curve leaves the diagonal, the linear distance adds up large distances although the words are the same.
> - "three" against "eight": the dark areas do not form a connected valley from corner to corner. Every path must cross regions of large distance, so the total is large for any alignment.
> - If the two examples had identical timing, the valley would lie exactly on the diagonal.

## 3. End points and timescale variation (slides 12-13)
![[cs568_lecture_12_l12_p012.jpg]]
![[cs568_lecture_12_l12_p013.jpg]]

**Notes.** A simple level threshold can find the start and end of a word, but it fails in three cases: words that start or end with weak sounds (such as [f]), words with internal silences (stop closures), and speaker artefacts (lip clicks, breath). End-point detection is often unreliable.

Speaking rate differs between repetitions and is not uniform inside a word. One stretch factor cannot fix it. The two words must be aligned frame by frame with dynamic time warping.

> [!info] Linear time normalisation against DTW (beyond the slides)
> The simplest fix for different lengths is **linear time normalisation**: stretch or compress one word by one constant factor. Incoming frame $i$ (of $n$) is compared with template (şablon) frame
> $$j=1+\mathrm{round}\!\left(\frac{(i-1)(N-1)}{n-1}\right)$$
> In the grid of section 4 this is the straight line from $(1,1)$ to $(n,N)$.
> It is right only if the speaker is faster or slower by the same factor everywhere. Real speech is not like that: vowels stretch a lot, stop bursts hardly at all. Dynamic time warping (dinamik zaman bükmesi) lets the stretch factor change from frame to frame and chooses the alignment that gives the smallest total distance (uzaklık).
>
> Why end-point errors hurt in both methods: the alignment is forced to start at the first frames and to end at the last frames of both words. If a detector cuts off a weak [f] or keeps a breath noise, those frames have no true partner and must be matched to wrong sounds. Their distances are added to the score of the correct template.

> [!question]- Check (slide 12): A level threshold marks "speech" when the level is above a fixed value. For each of the three problems on the slide, say what the detector does wrong. What does the slide suggest as an improvement?
> - Weak sound at the start or end (for example [f]): its level stays below the threshold, so it is cut off. The word comes out too short.
> - Silence inside the word (the closure of the [t] in "containing"): the level drops below the threshold in the middle, so the detector ends the word too early or sees two words.
> - Speaker artefacts (lip click, breath): they rise above the threshold although they are not speech. The word gets extra frames at the start or end.
> - Improvement: use the spectral properties of the background noise, not only the level. A weak [f] has a different spectrum from the room noise even when its level is similar.
> The slide still warns that end-point detection is often unreliable.

> [!question]- Check (slide 13): In the figure, what does each thin line between the solid and the dotted curve mean? Where are the lines vertical and where do they lean? What would the lines look like for a plain frame-by-frame comparison?
> - Each thin line joins one sample of the upper (solid) sequence with the sample of the lower (dotted) sequence that DTW matched to it.
> - At both ends, where the two curves are flat, the lines are vertical: sample $k$ is matched with sample $k$, the timing agrees.
> - In the middle the lines lean: the same shape comes later in the lower curve. For example the peak near 20 on the upper curve is joined to the peak near 26 on the lower curve. Where several lines meet in one point, one sample is matched with several samples of the other sequence (a local stretch).
> - Both sequences have about the same length (about 64 samples), so a frame-by-frame comparison would draw only vertical lines. It would compare the upper peak near 20 with the lower curve at 20, which is still rising, and would report a large distance for two shapes that are the same.

## 4. Dynamic time warping (slides 14-18)
![[cs568_lecture_12_l12_p014.jpg]]
![[cs568_lecture_12_l12_p015.jpg]]
![[cs568_lecture_12_l12_p016.jpg]]
![[cs568_lecture_12_l12_p017.jpg]]
![[cs568_lecture_12_l12_p018.jpg]]

**Notes.** Incoming pattern with $n$ frames, template with $N$ frames. $d(i,j)$ is the **local distance** between incoming frame $i$ and template frame $j$. Goal: the path from $(1,1)$ to $(n,N)$ with the smallest sum of local distances. There are far too many paths to list, so use dynamic programming. It applies because the problem has overlapping subproblems and optimal substructure.

Assumptions: the path always goes forward in time, and no frame is skipped. Let $D(i,j)$ be the **cumulative distance** along the best path from $(1,1)$ to $(i,j)$. A path can reach $(i,j)$ only from three neighbours, so

$$D(i,j)=\min\big[D(i,j-1),\ D(i-1,j),\ D(i-1,j-1)\big]+d(i,j)$$

Fill the matrix:
1. $D(1,1)=d(1,1)$.
2. First column: $D(1,j)=D(1,j-1)+d(1,j)$.
3. Columns $i=2,\dots,n$ from the bottom up: $D(i,1)=D(i-1,1)+d(i,1)$, then the recursion.
4. $D(n,N)$ is the score. Store a back-pointer in each cell and trace back from $(n,N)$ for the path.

Cost: $O(nN)$. A horizontal step means one template frame covers several incoming frames. A vertical step is the reverse. A diagonal step means equal speed. MATLAB: `ex12p1.m`.

> [!example] $X=(1,2,3,3)$, $Y=(1,3,3)$, $d(i,j)=|x_i-y_j|$
> Cumulative matrix (rows $j=3,2,1$ from the top, columns $i=1..4$):
> $$\begin{bmatrix}4&2&1&1\\2&1&1&1\\0&1&3&5\end{bmatrix}$$
> $D(4,3)=1$. One optimal path: $(1,1)\to(2,1)\to(3,2)\to(4,3)$, with local distances $0+1+0+0=1$.

> [!warning] Watch out
> - Slide 16 lists the three neighbours as "$(i,j-1)$, $(i,j-1)$, $(i-1,j-1)$" (one repeated). The correct three are $(i,j-1)$, $(i-1,j)$, $(i-1,j-1)$.
> - Slide 17 writes "backtrack from $D(n,m)$". It means $D(n,N)$.
> - DTW always returns a path and a finite score, even for different words. Only a small score compared with the other templates means "same word".

> [!info] How many paths, and why DP may forget most of them (beyond the slides)
> Slide 14 only says that trying every path works "for very small $n$ and $N$". Take a square grid of $n\times n$ cells ($n$ frames in each word) and the three steps of slide 16 (one cell right, one cell up, or one cell diagonally). The number of allowed paths from $(1,1)$ to $(n,n)$ is then 13 for $n=3$, 63 for $n=4$, $1\,462\,563$ for $n=10$ and about $6.6\times10^{28}$ for $n=40$ (the Delannoy numbers). Dynamic programming (dinamik programlama) computes one value per cell: $40\times40=1600$ cells for $n=N=40$.
>
> The "optimal substructure" property of slide 14 is also called **Bellman's principle of optimality**: every part of an optimal path is itself optimal. So each cell needs to remember only one number, the cost of the best path that reaches it. All the worse ways to reach that cell can be forgotten, because they can never become part of the best full path.
>
> The assumptions of the slides are the usual DTW path constraints:
> - **End-point constraint**: the path starts at $(1,1)$ and ends at $(n,N)$. The first frames match each other and so do the last frames.
> - **Monotonicity** ("always forward in time"): $i$ and $j$ never decrease. Time order is kept.
> - **Local continuity** ("no jumps"): $i$ and $j$ grow by at most 1 per step, so no frame is skipped. Together these two leave exactly the three steps of the recursion.
>
> The slides use no fixed global band around the diagonal. Score pruning (section 5) has a similar effect: it keeps the search inside a narrow region.

> [!question]- Check (slide 14): Count the allowed paths from $(1,1)$ to $(3,3)$ (steps: right, up, diagonal). Compare with the number of cells that DP evaluates. In this count, where do you see "overlapping subproblems"?
> Let $P(i,j)$ be the number of paths that reach $(i,j)$. A path arrives from the left, from below or diagonally, so $P(i,j)=P(i-1,j)+P(i,j-1)+P(i-1,j-1)$. On the bottom row and the left column there is one path only.
> - $P(2,2)=1+1+1=3$
> - $P(3,2)=P(2,2)+P(3,1)+P(2,1)=3+1+1=5$, and $P(2,3)=5$ by symmetry
> - $P(3,3)=P(2,3)+P(3,2)+P(2,2)=5+5+3=13$
> So 13 paths against $3\times3=9$ cells. For a $4\times4$ grid it is already 63 paths against 16 cells, and the gap grows very fast.
> Overlapping subproblems: the result for cell $(2,2)$ was used three times, by $(3,2)$, $(2,3)$ and $(3,3)$. In DTW the best cost $D(2,2)$ is reused in the same way. It is computed once and stored, not once per path.

> [!question]- Check (slide 15): Read the path of the figure cell by cell (incoming word "pPaTerRn" along the bottom, template "PATTERN" on the left). How many horizontal, vertical and diagonal steps does it have? What do the horizontal step at the start and the vertical step in column 4 mean?
> Path: $(1,1)\to(2,1)\to(3,2)\to(4,3)\to(4,4)\to(5,5)\to(6,6)\to(7,6)\to(8,7)$.
> Steps: horizontal, diagonal, diagonal, vertical, diagonal, diagonal, horizontal, diagonal. So 2 horizontal, 1 vertical, 5 diagonal.
> Check: horizontal + diagonal $=7=n-1$ (with $n=8$) and vertical + diagonal $=6=N-1$ (with $N=7$).
> - Horizontal step $(1,1)\to(2,1)$: the incoming frames "p" and "P" are both matched to the one template frame "P". The incoming word is slower here. The same happens for "r", "R" against the template "R".
> - Vertical step $(4,3)\to(4,4)$: the single incoming frame "T" is matched to the two template frames "T", "T". The incoming word is faster here.
> - The dashed lines at the point marked $i,j$ show its three possible predecessors $(i-1,j)$, $(i-1,j-1)$ and $(i,j-1)$.

> [!question]- Check (slide 16): Name each term of $D(i,j)=\min[D(i,j-1),D(i-1,j),D(i-1,j-1)]+d(i,j)$. Which assumption of the slide forbids $(i+1,j-1)$ as a predecessor, and which forbids $(i-2,j-1)$? Why is it enough to keep the minimum?
> - $D(i,j)$: cumulative distance of the best path from $(1,1)$ to $(i,j)$. It contains the local distances of all cells on that path, from $d(1,1)$ to $d(i,j)$.
> - $d(i,j)$: local distance between incoming frame $i$ and template frame $j$. Every path that enters the cell pays it once, so it stands outside the $\min$.
> - $D(i,j-1)$: best cost if the path arrives by a vertical step. $D(i-1,j)$: by a horizontal step. $D(i-1,j-1)$: by a diagonal step.
> - $(i+1,j-1)$ would need a step back in incoming time. "The path always goes forward in time" forbids it.
> - $(i-2,j-1)$ would skip incoming frame $i-1$. "We cannot skip frames (no jumps)" forbids it.
> - Minimum: suppose the best path to $(i,j)$ came through a predecessor by a path that is not the cheapest one to that predecessor. Replace that part by the cheapest one. The rest of the path stays the same and the total gets smaller. That contradicts "best". So only the best cost of each predecessor matters.

> [!question]- Check (slide 17): Fill the cumulative matrix with the procedure of the slide for this local-distance grid ($n=4$ incoming frames, $N=4$ template frames). Give the score and backtrack the path.
>
> | $d(i,j)$ | $i=1$ | $i=2$ | $i=3$ | $i=4$ |
> |---|---|---|---|---|
> | $j=4$ | 2 | 1 | 5 | 3 |
> | $j=3$ | 2 | 6 | 0 | 2 |
> | $j=2$ | 3 | 4 | 1 | 5 |
> | $j=1$ | 3 | 0 | 5 | 1 |
>
> **Start.** $D(1,1)=d(1,1)=3$.
> **Left column** (vertical steps only): $D(1,2)=3+3=6$, $D(1,3)=6+2=8$, $D(1,4)=8+2=10$.
> **Bottom row** (horizontal steps only): $D(2,1)=3+0=3$, $D(3,1)=3+5=8$, $D(4,1)=8+1=9$.
> **Column 2**, in the order $\min[D(i,j-1),D(i-1,j),D(i-1,j-1)]+d(i,j)$:
> - $D(2,2)=\min[3,6,3]+4=7$
> - $D(2,3)=\min[7,8,6]+6=12$
> - $D(2,4)=\min[12,10,8]+1=9$
>
> **Column 3:**
> - $D(3,2)=\min[8,7,3]+1=4$
> - $D(3,3)=\min[4,12,7]+0=4$
> - $D(3,4)=\min[4,9,12]+5=9$
>
> **Column 4:**
> - $D(4,2)=\min[9,4,8]+5=9$
> - $D(4,3)=\min[9,4,4]+2=6$
> - $D(4,4)=\min[6,9,4]+3=7$
>
> | $D(i,j)$ | $i=1$ | $i=2$ | $i=3$ | $i=4$ |
> |---|---|---|---|---|
> | $j=4$ | 10 | 9 | 9 | **7** |
> | $j=3$ | 8 | 12 | **4** | 6 |
> | $j=2$ | 6 | 7 | **4** | 9 |
> | $j=1$ | **3** | **3** | 8 | 9 |
>
> **Score.** $D(4,4)=7$.
> **Backtrack** (follow the predecessor that gave each minimum):
> - $(4,4)$: the minimum 4 came from $(3,3)$, a diagonal step.
> - $(3,3)$: the minimum 4 came from $(3,2)$, a vertical step.
> - $(3,2)$: the minimum 3 came from $(2,1)$, a diagonal step.
> - $(2,1)$ is on the bottom row, so it came from $(1,1)$, a horizontal step.
>
> Path: $(1,1)\to(2,1)\to(3,2)\to(3,3)\to(4,4)$. Local distances on it: $3+0+1+0+3=7$, equal to $D(4,4)$.
> Ties: two cells have two equal smallest predecessors, $(2,2)$ (3 and 3) and $(4,3)$ (4 and 4). Neither cell is on the backtracked path, so the best path is unique here. If a tie lies on the path, there are several best paths with the same score, and a fixed rule (for example "prefer the diagonal") picks one.
> The path is known only after the whole matrix is filled. This is why the slide asks for extra book-keeping: each cell stores which predecessor won.

## 5. Refinements to DTW (slides 19-21)
![[cs568_lecture_12_l12_p019.jpg]]
![[cs568_lecture_12_l12_p020.jpg]]
![[cs568_lecture_12_l12_p021.jpg]]

**Notes.**
**Distortion penalties.** Long horizontal or vertical runs are suspicious. Penalise them, and weight the diagonal by 2 so that it is not favoured:

$$D(i,j)=\min\begin{bmatrix}D(i-1,j)+d(i,j)+\mathit{hdp}\\ D(i-1,j-1)+2\,d(i,j)\\ D(i,j-1)+d(i,j)+\mathit{vdp}\end{bmatrix}$$

$\mathit{hdp}$ and $\mathit{vdp}$ are the horizontal and vertical distortion penalties, found empirically.

**Length normalisation.** The cumulative distance grows with length, so short templates are favoured. Divide the total distance by the template length.

**Score pruning.** Do not extend paths from cells whose $D(i,j)$ is far above the minimum of the column. Optimality is traded for a speed-up of 5 to 10. Pruning is most likely to lose the best path when the words are different, and then the result does not change. This idea becomes beam search in HMM decoders.

Slide 21: alignments of "eight" with "eight" (without and with a small distortion penalty) and of "three" with "eight".

> [!example] Normalisation
> Score 12 against a 10-frame "yes", score 9 against a 6-frame "no". Raw: "no" wins. Per frame: $1.2$ against $1.5$, so "yes" wins.

> [!info] Why template matching was replaced (partly beyond the slides)
> Slide 3 gives the main reason: templates do not properly use information about the variability of speech. In more detail:
> - A template is one recorded example. It does not say which frames vary a lot between repetitions and which are stable, so every frame and every channel counts the same in the distance.
> - The templates fit the speaker who recorded them (speaker dependence).
> - Storage and recognition time grow with the vocabulary: every new word adds templates, and the incoming word is aligned with all of them.
> - The method needs end points, which are unreliable (slide 12).
>
> A hidden Markov model keeps the idea of an alignment found by dynamic programming, but replaces the stored example by states with probability distributions learned from many examples. The local distance becomes a (negative log) probability.

> [!question]- Check (slide 19, penalties): Use the recursion of the slide with $\mathit{hdp}=\mathit{vdp}=1$ on this grid ($n=4$ incoming frames, $N=3$ template frames). Fill the cumulative matrix, give the score, the path and the length-normalised score. On the edges only one line of the recursion exists. Slide 19 does not say how to start, so use $D(1,1)=d(1,1)$ from slide 17.
>
> | $d(i,j)$ | $i=1$ | $i=2$ | $i=3$ | $i=4$ |
> |---|---|---|---|---|
> | $j=3$ | 3 | 1 | 4 | 1 |
> | $j=2$ | 2 | 0 | 4 | 1 |
> | $j=1$ | 3 | 1 | 1 | 1 |
>
> **Start.** $D(1,1)=3$.
> **Left column** (vertical line, $D(1,j-1)+d+\mathit{vdp}$): $D(1,2)=3+2+1=6$, $D(1,3)=6+3+1=10$.
> **Bottom row** (horizontal line, $D(i-1,1)+d+\mathit{hdp}$): $D(2,1)=3+1+1=5$, $D(3,1)=5+1+1=7$, $D(4,1)=7+1+1=9$.
> **Inner cells.** H is $D(i-1,j)+d+1$, D is $D(i-1,j-1)+2d$, V is $D(i,j-1)+d+1$.
>
> | cell | $d$ | H | D | V | min |
> |---|---|---|---|---|---|
> | $(2,2)$ | 0 | $6+0+1=7$ | $3+0=3$ | $5+0+1=6$ | 3 |
> | $(2,3)$ | 1 | $10+1+1=12$ | $6+2=8$ | $3+1+1=5$ | 5 |
> | $(3,2)$ | 4 | $3+4+1=8$ | $5+8=13$ | $7+4+1=12$ | 8 |
> | $(3,3)$ | 4 | $5+4+1=10$ | $3+8=11$ | $8+4+1=13$ | 10 |
> | $(4,2)$ | 1 | $8+1+1=10$ | $7+2=9$ | $9+1+1=11$ | 9 |
> | $(4,3)$ | 1 | $10+1+1=12$ | $8+2=10$ | $9+1+1=11$ | 10 |
>
> | $D(i,j)$ | $i=1$ | $i=2$ | $i=3$ | $i=4$ |
> |---|---|---|---|---|
> | $j=3$ | 10 | 5 | 10 | **10** |
> | $j=2$ | 6 | **3** | **8** | 9 |
> | $j=1$ | **3** | 5 | 7 | 9 |
>
> **Score.** $D(4,3)=10$.
> **Backtrack** (no inner cell has a tie, so the path is unique). $(4,3)$ came by D from $(3,2)$. $(3,2)$ came by H from $(2,2)$. $(2,2)$ came by D from $(1,1)$.
> Path: $(1,1)\to(2,2)\to(3,2)\to(4,3)$. Check: $3+2\cdot0+(4+1)+2\cdot1=10$.
> **Length normalisation** (as the slide says, divide by the length of the template, here $N=3$, the $j$ direction): $10/3\approx3.33$ per template frame.
>
> For comparison, the plain recursion of slide 16 on the same grid gives the score 7 with the path $(1,1)\to(2,1)\to(3,1)\to(4,2)\to(4,3)$: two horizontal steps and one vertical step through cells with $d=1$. With the penalties and the weight 2 this path loses, and the winner has only one horizontal step. The two scores (7 and 10) are not comparable with each other. Only scores from the same recursion may be compared across templates.

> [!question]- Check (slide 19, choice of penalties): What happens to the chosen path if $\mathit{hdp}$ and $\mathit{vdp}$ are made very large? Why is that bad, and why is zero not ideal either? Why does the diagonal line carry $2\,d(i,j)$?
> - Very large penalties: horizontal and vertical steps become too expensive, so the path is pushed onto the diagonal. DTW then behaves like the frame-by-frame comparison of slide 8 and cannot follow real timing differences. If $n\neq N$, at least $\lvert n-N\rvert$ non-diagonal steps are needed anyway, and each one pays the large penalty.
> - Zero penalties: a long horizontal or vertical run costs nothing extra. The path may match one frame to many frames of the other word, which is acoustically implausible, and wrong templates can get scores that are too good.
> - So the values lie in between and are found empirically (by trying values on data).
> - The factor 2 (the slide gives no reason, this is beyond the slides): a diagonal step advances both words by one frame, the other steps advance only one word. A horizontal step plus a vertical step also advance both words by one frame, and together they add two local distances. With the factor 2 the diagonal step also carries the weight 2, so the diagonal is not favoured only because it uses fewer steps.

> [!question]- Check (slide 20): One column of the matrix holds $D=(7,\,5,\,12,\,21,\,6)$. The rule is "prune (budama) every cell that is more than 6 above the column minimum". Which cells survive? Can pruning make a template's final score smaller than the true $D(n,N)$? Why does it rarely change the recognised word?
> - Column minimum: $\min(7,5,12,21,6)=5$. Threshold: $5+6=11$.
> - $7\le11$ keep, $5\le11$ keep, $12>11$ prune, $21>11$ prune, $6\le11$ keep. Three of five cells survive, and only paths through them are extended into the next column.
> - No. Pruning only removes candidate paths. The minimum over fewer paths is equal to the true minimum or larger. Pruning can make a template look worse, never better.
> - The slide says the best path is most likely to be pruned when the two words are different. A likely reason (not on the slide): no path fits well, so the best one does not stay close to the column minimum. That template would lose anyway, so a score that is too large does not change the decision. For the correct template the best path stays near the column minimum and survives.
> - Price: the result is no longer guaranteed optimal. Gain: a speed-up of about 5 to 10.

> [!question]- Check (slide 21): What does the hatched area in each panel show? Why is it a narrow band in the two "eight"/"eight" panels and almost the whole matrix in the "three"/"eight" panel? What does the small distortion penalty change between the left and the middle panel?
> - The slide has no legend for the hatching. Read from the figure and the captions (an inference): the thin lines are the partial paths that were kept, so the hatched area marks the cells that were really evaluated. In the left and middle panels the heavy line through the band, from the bottom-left to the top-right corner, is the chosen path.
> - Left and middle: the words are the same and score pruning is on. The cells near the true alignment have much smaller cumulative distances than the rest, so everything far from it is pruned. Only a band along the diagonal direction is computed.
> - Right: the words are different. No path is clearly better than the others, and pruning would have thrown away the best one (the slide says the result "would have been seriously suboptimal"). So pruning was switched off and nearly all cells were computed. This shows the cost of full DTW.
> - Left to middle: without the penalty the path may contain long horizontal or vertical runs wherever they are cheap. With a small penalty such runs cost extra, so the path follows the two timescales more evenly. The slide calls this "the more plausible matching of the two timescales".

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| ASR | speech to text, independent of microphone, speaker, environment |
| MAP for ASR | $\hat W=\arg\max_W p(X\mid W)P(W)$ |
| Template | stored example pattern of a word |
| Features | log filter-bank powers, mean log-power removed |
| Local distance $d(i,j)$ | distance between incoming frame $i$ and template frame $j$ |
| Cumulative distance $D(i,j)$ | best total over a path from $(1,1)$ to $(i,j)$ |
| DTW recursion | $D(i,j)=\min[D(i,j-1),D(i-1,j),D(i-1,j-1)]+d(i,j)$, cost $O(nN)$ |
| Penalties | diagonal $2d(i,j)$; horizontal $+\mathit{hdp}$; vertical $+\mathit{vdp}$ |
| Normalisation | divide $D(n,N)$ by the template length |
| Score pruning | drop cells far above the column minimum |

## Check yourself
> [!question]- 1. Why can the linear distance not compare $X=(1,2,3,3)$ with $Y=(1,3,3)$?
> It pairs frame $i$ with frame $i$. $X$ has 4 frames and $Y$ has 3, so $x_4$ has no partner. The sum is not defined.

> [!question]- 2. Log filter-bank vectors $(2.0,1.5,0.5)$ and $(2.7,2.2,1.2)$. What separates them?
> The difference is 0.7 in every channel: a gain $g=e^{0.7}\approx2.0$. After mean subtraction both become $(0.667,0.167,-0.833)$.

> [!question]- 3. DTW score for $X=(0,1,1,2)$, $Y=(0,1,2)$ with $d=|x_i-y_j|$?
> $D(4,3)=0$. Path $(1,1)\to(2,2)\to(3,2)\to(4,3)$: the repeated 1 is absorbed by a horizontal step.

> [!question]- 4. DTW score for $X=(0,2,2,1)$, $Y=(0,1,2)$?
> $D(4,3)=2$. Path $(1,1)\to(2,2)\to(3,3)\to(4,3)$ with local distances $0+1+0+1$.

> [!question]- 5. Without diagonal weighting, why is DTW biased towards diagonal steps?
> Going $(i-1,j-1)\to(i,j-1)\to(i,j)$ adds $d(i,j-1)+d(i,j)$. The diagonal adds only $d(i,j)$. So the diagonal is never dearer. With weight 2 on the diagonal all paths carry the same total step weight $(n-1)+(N-1)$.

> [!question]- 6. A 40-frame word against 100 templates of 40 frames. Cells for full DTW, and after 5 times pruning?
> $100\times40\times40=160{,}000$ cells. After pruning: $32{,}000$.

## Links
- Course index: [[CS568 Home]]
- Previous lecture: [[CS568 L11 - Pattern recognition principles]]
- Full step-by-step notes (PDF): [CS568_Lecture_12_Template_Matching.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_12/CS568_Lecture_12_Template_Matching.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_12/CS568_Lecture_12_Template_Matching.pdf`
- Original slides (PDF): [l12.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_12/l12.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_12/l12.pdf`
- Quiz: [[CS568 L12 - Template matching and DTW - quiz]]

Slides not embedded: 1
