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

## 3. End points and timescale variation (slides 12-13)
![[cs568_lecture_12_l12_p012.jpg]]
![[cs568_lecture_12_l12_p013.jpg]]

**Notes.** A simple level threshold can find the start and end of a word, but it fails in three cases: words that start or end with weak sounds (such as [f]), words with internal silences (stop closures), and speaker artefacts (lip clicks, breath). End-point detection is often unreliable.

Speaking rate differs between repetitions and is not uniform inside a word. One stretch factor cannot fix it. The two words must be aligned frame by frame with dynamic time warping.

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

Slides not embedded: 1
