---
course: CS 568 Speech Processing
lesson: CS568 L12 - Template matching and DTW
type: quiz
questions: 19
---

# CS568 L12 - Template matching and DTW - quiz

Lesson note: [[CS568 L12 - Template matching and DTW]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Introduction to ASR (slides 2-6)

### Q1 (slide 2)

ASR should work whatever the recording device, the speaker and the acoustic environment. A recogniser was built and tested by its developer, an adult who uses one headset in a quiet office. It fails for a child who speaks into a phone in a car. Which of these independence requirements does the case break?

- A) Only speaker independence. Mean log-power subtraction removes the car noise, and both microphones record the same sound.
- B) Only device independence. A child says the same words as an adult, and car noise lies outside the speech band.
- C) Only environment independence. The features ignore pitch, so a child's voice and a phone microphone change nothing.
- D) All three: the device (headset against phone), the speaker (adult against child) and the room (office against car).

> [!success]- Answer
> **D.** The device changes (a headset against a phone microphone), the speaker changes (an adult against a child) and the acoustic environment changes (a quiet office against a noisy car). The system meets its goal only for one device, one speaker and one room.
> - A: Mean subtraction removes only a constant gain ($\log g$ in every channel). Car noise adds energy that changes the spectrum shape, and a phone has another frequency response than a headset.
> - B: A child has a shorter vocal tract, so the formants and the pattern differ. Car noise covers the speech band.
> - C: The features ignore pitch, but not the vocal tract. A child's formants are higher, and the phone changes the spectrum.

### Q2 (slide 3)

Find the mistake in this sentence: "Template-matching systems failed on isolated words because of coarticulation and hard decisions. Statistical methods then replaced them." Which correction is right?

- A) The reason belongs to the rule-based systems. Template matching worked on isolated words; its weakness was that it ignored speech variability.
- B) The second sentence is wrong. Rule-based systems replaced template matching, because phonetic rules can describe the effects of coarticulation.
- C) Nothing is wrong. Coarticulation and hard decisions are exactly the reasons why template matching was given up in favour of HMMs.
- D) The reason belongs to the statistical systems. HMMs make a hard decision on every frame, while templates use soft distances.

> [!success]- Answer
> **A.** Coarticulation and inflexible hard decisions were the weakness of the rule-based systems (1950s on). Template matching with DTW came after them and worked reasonably on isolated words. Its weakness: one stored example does not describe how examples vary. The second sentence is correct.
> - B: The order is reversed. Rules came first, templates second, statistical models third.
> - C: This keeps the misplaced reason. It belongs to the rule-based generation, not to templates.
> - D: Statistical models avoid hard decisions; they score every hypothesis with a probability.

### Q3 (slide 4)

![[cs568_lecture_12_l12_p004.jpg]]

A student says: "Dynamic programming was invented for the template-based systems, and it was dropped when the statistical (HMM) systems came." Use the figure and what you know about HMM decoding. Which correction is right?

- A) Only the second part is wrong. Dynamic programming was invented for template matching, and HMM decoders still use it.
- B) Only the first part is wrong. Dynamic programming is older, but HMMs replaced the alignment search by probabilities.
- C) Both parts are wrong. Dynamic programming already appears one column earlier, and HMM decoders still align by it.
- D) Nothing is wrong. The figure lists dynamic programming under template-based and HMMs in the next column.

> [!success]- Answer
> **C.** "Dynamic programming" and "time-normalization" are listed in the first column (small vocabulary, acoustic-phonetics based, about 1962 to 1967). The template-based column (about 1967 to 1977) comes after it. So the idea is older than the template systems.
> An HMM keeps the alignment found by dynamic programming. It replaces the stored example by states with probability distributions, and the local distance becomes a (negative log) probability.
> - A: The first part is also wrong: the figure puts dynamic programming one column before the template-based one.
> - B: HMMs change what is scored, not how the alignment is searched. The search is still dynamic programming.
> - D: This misreads the figure. The template-based column lists pattern recognition, LPC, clustering and level building, not dynamic programming.

### Q4 (slide 5)

![[cs568_lecture_12_l12_p005.jpg]]

The decoder picks $\hat W=\arg\max_W p(X\mid W)\,P(W)$, where $X$ is the sequence of feature vectors and $W$ is a word sequence. Which blocks supply the two factors?

- A) The language model supplies $p(X\mid W)$, how well the features fit the words. The acoustic model supplies $P(W)$, how likely the sounds are.
- B) The acoustic model supplies $p(X\mid W)$, how well the features fit the words. The language model supplies $P(W)$, how likely the word sequence is.
- C) The acoustic model supplies both: it scores how well the features fit the words and how often each word sequence occurred in training.
- D) The language model supplies both: its n-grams score the word sequence and also the feature vectors expected for each word.

> [!success]- Answer
> **B.** The acoustic model (HMM) gives $p(X\mid W)$: how well the features fit the words. The language model (n-gram) gives $P(W)$: how likely the word sequence is before any sound is heard. The word lexicon says how each word is built from the units of the acoustic model. The numbers (0.9) and (0.8) are confidence scores from the confidence scoring block.
> - A: The two models are swapped. The language model never sees the features.
> - C: The acoustic model does not count word sequences. The prior $P(W)$ comes from the n-gram language model.
> - D: An n-gram model works on words only. It knows nothing about feature vectors.

## 2. Pattern matching and features (slides 7-11)

### Q5 (slide 7)

Template matching assumes that the same person repeats the same isolated word. Each word of a vocabulary of $V$ words has $K$ stored templates, and an incoming word is compared with the templates to find the closest one. Which statement is correct?

- A) If another person says the word, only the pitch changes. The filter-bank features remove pitch, so the stored templates still fit.
- B) If the word is spoken inside a sentence, the whole-word template still lines up, because DTW finds the word boundaries itself.
- C) If another person says the word, the vocal tract and the accent change the pattern. The stored templates fit only their speaker.
- D) Storage grows as $V\cdot K$, but the work does not grow with $V$, because the search stops at the first template that is close.

> [!success]- Answer
> **C.** Another speaker has a different vocal tract, pitch and accent, so the same word gives a different pattern. The system is speaker dependent: each new user records templates, or many templates per word are stored.
> - A: Pitch is removed, but the vocal tract (formants) and the accent still differ between speakers.
> - B: In connected speech coarticulation changes the sounds, and DTW needs the end points: its path must start at $(1,1)$ and end at $(n,N)$. It does not find boundaries.
> - D: The classifier is nearest neighbour. It must compare the word with all $V\cdot K$ templates, so the work also grows with $V$.

### Q6 (slide 8)

The linear distance between two words with the same number $n$ of frames is $D(X,Y)=\sum_{i=1}^{n}\lVert\mathbf x_i-\mathbf y_i\rVert$. Here $\mathbf x_i$ and $\mathbf y_i$ are the feature vectors of frame $i$ of the incoming word and of the template, and $\lVert\cdot\rVert$ is the Euclidean distance. The word has frames $\mathbf x_1=(1,2)$, $\mathbf x_2=(3,1)$; the template has $\mathbf y_1=(1,0)$, $\mathbf y_2=(0,5)$. What is $D$?

- A) $7$
- B) $9$
- C) $29$
- D) $5.39$

> [!success]- Answer
> **A.** Frame 1: $\lVert(1-1,\ 2-0)\rVert=\sqrt{0^2+2^2}=2$.
> Frame 2: $\lVert(3-0,\ 1-5)\rVert=\sqrt{3^2+(-4)^2}=\sqrt{25}=5$.
> $D=2+5=7$.
> - B: The absolute differences were added (city-block distance): $(0+2)+(3+4)=9$.
> - C: The square root was forgotten: $4+25=29$.
> - D: One square root was taken over both frames together: $\sqrt{4+25}=\sqrt{29}\approx5.39$.

### Q7 (slide 9)

A filter bank on an auditory scale gives one power value per channel, and the features are the log-powers. The same vowel is spoken at a pitch of 100 Hz and at 200 Hz. In another frame a strong formant has power 100 and a weak one has power 1, and both grow by 50 %. Which statement is correct?

- A) Each filter-bank channel is narrower than the harmonic spacing, so it picks one harmonic. That is why pitch hardly changes the features.
- B) A change of pitch moves the formants, but the mean log-power subtraction removes that shift, so the features stay the same.
- C) The natural log-powers grow by $\ln50\approx3.91$ and $\ln0.5\approx-0.69$, so the strong formant still counts more in the distance.
- D) Both natural log-powers grow by $\ln1.5\approx0.405$, so the weak formant counts as much as the strong one in the distance.

> [!success]- Answer
> **D.** Strong formant: $\ln150-\ln100=\ln1.5\approx0.405$. Weak formant: $\ln1.5-\ln1=\ln1.5\approx0.405$. On raw power the changes are 50 and 0.5, a factor 100 apart. The log makes them equal, so a weak formant takes part in the distance.
> - A: It is the other way round. A channel is wide and sums several harmonics, so the harmonic structure is smoothed away and only the envelope remains.
> - B: Pitch changes the spacing of the harmonics, not the formants. Mean subtraction removes a constant gain, not a shift along frequency.
> - C: The log was taken of the change in power ($\ln50$, $\ln0.5$) instead of the change of the log-power.

### Q8 (slide 10)

![[cs568_lecture_12_l12_p010.jpg]]

The figure is a 10-channel filter-bank analysis (0 to 5 kHz, about 0.5 s) of "three" and of two examples of "eight". Take the dot size as the log-power of that channel in that frame: a larger dot means more energy (the slide has no legend; use this reading). Both "eight" patterns have an empty stretch between "I" and "GHT". Which statement is correct?

- A) The empty stretch is the closure of the stop [t], and the burst after it starts later in the middle "eight" than in the bottom one.
- B) The empty stretch is a breath pause between the two syllables, and it has about the same length in both "eight" examples.
- C) The empty stretch is the closure of the stop [t], and the burst after it starts later in the bottom "eight" than in the middle one.
- D) The empty stretch holds the [t] burst, which lies above 5 kHz and outside the channels, and both examples have the same timing.

> [!success]- Answer
> **A.** During the closure of [t] the mouth is closed and almost no sound comes out, so every channel is empty. The release gives a burst, mainly in the upper channels ("GHT"). Both examples have the same order of events (E, I, gap, GHT), but in the middle panel the burst starts at about three quarters of the time axis, clearly later than in the bottom panel.
> - B: "eight" is one syllable, and the gaps have clearly different lengths. The silence is part of the stop consonant.
> - C: The two panels were read the wrong way round. The burst is later in the middle panel.
> - D: The burst is the energy that appears after the gap in the upper channels, inside 0 to 5 kHz. And the timing differs.

### Q9 (slide 11)

![[cs568_lecture_12_l12_p011.jpg]]

The left panel compares two examples of "eight": each dot is one pair (a frame of one word, a frame of the other). Take a large dark dot as a pair with a small distance (the slide has no legend; use this reading). The blue curve runs through the large dots from the bottom-left to the top-right corner. It is steeper in the first part and flatter near the end. For two words of equal length $n$ the linear distance is $\sum_{i=1}^{n}\lVert\mathbf x_i-\mathbf y_i\rVert$, where $\mathbf x_i$ and $\mathbf y_i$ are the feature vectors of frame $i$ of each word. Which statement is correct?

- A) The speed relation of the two words changes inside the word. The linear distance also follows the curve, since it pairs frames that have the same content.
- B) The speed relation of the two words changes inside the word. The linear distance uses the straight diagonal and adds large distances where the curve leaves it.
- C) The words differ by one constant speed factor, so one stretch would fix it. The linear distance uses the straight diagonal and misses the curve.
- D) The words differ by one constant speed factor, so one stretch would fix it. The linear distance also follows the curve, since it pairs frames of equal content.

> [!success]- Answer
> **B.** A constant speed factor would give a straight line. The bend means the speed relation changes inside the word, so one stretch factor cannot describe it. The linear distance pairs frame $i$ with frame $i$: the straight diagonal. Where the curve leaves the diagonal, it adds large distances although the words are the same.
> - A: The sum pairs frames with the same index $i$, not frames with the same content. Finding the frames with the same content is the job of DTW.
> - C: One stretch factor gives a straight line from corner to corner. The curve is not straight.
> - D: Both slips: the bend rules out one constant factor, and the linear distance pairs frames by index, not by content.

## 3. End points and timescale variation (slides 12-13)

### Q10 (slide 12)

An end-point detector marks a frame as speech when its level is above a fixed threshold. Which statement is correct?

- A) A weak [f] at the start of a word stays below the threshold, so it is cut off and the word comes out too short.
- B) A lip click or a breath stays below the threshold, so the detector removes it together with the weak sounds.
- C) The fix the slide suggests is a higher threshold, so that breath noise and lip clicks are never marked as speech.
- D) Using the spectrum of the background noise cannot help, since a weak [f] and room noise look the same at equal level.

> [!success]- Answer
> **A.** A weak fricative has a low level, so it stays below the threshold and is cut off. The word comes out too short.
> - B: Lip clicks and breaths rise above the threshold although they are not speech. They add extra frames at the start or end.
> - C: A higher threshold cuts off even more weak sounds. The slide suggests using the spectral properties of the background noise.
> - D: A weak [f] has a different spectrum from room noise even at a similar level. That is why the slide suggests the spectrum.

### Q11 (slide 13)

![[cs568_lecture_12_l12_p013.jpg]]

Each thin line joins one sample of the upper (solid) curve with the sample of the lower (dotted) curve that DTW matched to it. Both curves have about 64 samples. Which statement is correct?

- A) In the middle the lines lean because the lower curve is longer there. A frame-by-frame comparison would draw the same lines, since the lengths agree.
- B) Where several lines meet in one point, DTW skipped samples of the other curve. A frame-by-frame comparison would draw only vertical lines.
- C) Near both ends the lines lean, because the curves are flat there and any match is equally good. A frame-by-frame comparison draws vertical lines.
- D) Near both ends the lines are vertical, so the timing agrees. Frame by frame, the upper peak near 20 would meet a rising part of the lower curve.

> [!success]- Answer
> **D.** At both flat ends sample $k$ is joined with sample $k$: vertical lines, the timing agrees. In the middle the same shape comes later in the lower curve; the upper peak near 20 is joined with the lower peak near 26. A frame-by-frame comparison draws only vertical lines. It compares the upper peak near 20 with the lower curve at 20, which is still rising, and reports a large distance for two equal shapes.
> - A: The total lengths are about equal. Equal length does not mean equal timing inside the word, so frame by frame gives other (vertical) lines.
> - B: DTW never skips a sample. Several lines in one point mean that one sample is matched with several samples of the other curve (a local stretch).
> - C: This misreads the figure: the lines at the ends are vertical.

## 4. Dynamic time warping (slides 14-18)

### Q12 (slide 14)

Two words of 3 frames each give a $3\times3$ grid of cells $(i,j)$. A path goes from $(1,1)$ to $(3,3)$. Each step moves one cell right $(i+1,j)$, one cell up $(i,j+1)$, or one cell diagonally $(i+1,j+1)$. How many different paths are there?

- A) $6$
- B) $13$
- C) $27$
- D) $63$

> [!success]- Answer
> **B.** Let $P(i,j)$ be the number of paths to $(i,j)$. A path arrives from the left, from below or diagonally, so $P(i,j)=P(i-1,j)+P(i,j-1)+P(i-1,j-1)$, with $P=1$ on the bottom row and the left column (Delannoy numbers).
> $P(2,2)=1+1+1=3$, $P(3,2)=P(2,3)=3+1+1=5$, $P(3,3)=5+5+3=13$.
> DP evaluates only $3\times3=9$ cells. $P(2,2)$ is used three times: these are the overlapping subproblems.
> - A: The diagonal step was forgotten: right/up paths only, $\binom{4}{2}=6$.
> - C: Three choices at each of three steps: $3^3=27$. But paths have different numbers of steps, and many choices leave the grid.
> - D: The count was done for a $4\times4$ grid (frames taken as steps): $P(4,4)=63$.

### Q13 (slide 15)

![[cs568_lecture_12_l12_p015.jpg]]

The incoming word "pPaTerRn" ($n=8$ frames, index $i$) runs along the bottom, the template "PATTERN" ($N=7$ frames, index $j$) along the left side. A horizontal step increases only $i$, a vertical step only $j$, a diagonal step both. Read the path cell by cell. How many steps of each kind does it have, and what does the vertical step in column 4 ("T") mean?

- A) 1 horizontal, 2 vertical, 5 diagonal. One incoming frame "T" is matched to two template frames; the incoming word is faster there.
- B) 2 horizontal, 1 vertical, 5 diagonal. One incoming frame "T" is matched to two template frames; the incoming word is faster there.
- C) 2 horizontal, 1 vertical, 5 diagonal. Two incoming frames are matched to one template frame "T"; the incoming word is slower there.
- D) 1 horizontal, 2 vertical, 5 diagonal. Two incoming frames are matched to one template frame "T"; the incoming word is slower there.

> [!success]- Answer
> **B.** Path: $(1,1)\to(2,1)\to(3,2)\to(4,3)\to(4,4)\to(5,5)\to(6,6)\to(7,6)\to(8,7)$. Steps: H, D, D, V, D, D, H, D, so 2 horizontal, 1 vertical, 5 diagonal.
> Check: horizontal + diagonal $=7=n-1$, vertical + diagonal $=6=N-1$.
> The vertical step $(4,3)\to(4,4)$ keeps $i=4$: the single incoming frame "T" covers the two template frames "T", "T". The incoming word is faster there.
> - A: Horizontal and vertical were swapped in the count. Then horizontal + diagonal $=6\neq n-1$.
> - C: The count is right, but the vertical step was read as a horizontal one: two incoming frames on one template frame is the step $(1,1)\to(2,1)$ ("p", "P").
> - D: The two axes were swapped everywhere, as if the incoming word ran along the left side.

### Q14 (slide 16)

The plain DTW recursion is $D(i,j)=\min[D(i,j-1),D(i-1,j),D(i-1,j-1)]+d(i,j)$. Here $d(i,j)$ is the local distance between incoming frame $i$ and template frame $j$, and $D(i,j)$ is the cumulative distance of the best path from $(1,1)$ to $(i,j)$. The slide assumes that the path always goes forward in time and that no frame is skipped. Which statement is correct?

- A) The cell $(i+1,j-1)$ is not a predecessor of $(i,j)$ because the step from it would skip a frame. The "no frames skipped" rule excludes it.
- B) The cell $(i-2,j-1)$ is not a predecessor of $(i,j)$ because the step from it would go back in time. The "forward in time" rule excludes it.
- C) Storing only the best cost $D$ in each cell is safe: the best path to $(i,j)$ reaches each cell on it by the cheapest path to that cell.
- D) Storing only the best cost $D$ in each cell is an approximation: a path that is worse at $(i-1,j)$ may still become the best one at $(n,N)$.

> [!success]- Answer
> **C.** This is the optimal substructure (Bellman's principle). Suppose the best path to $(i,j)$ reached a predecessor by a path that is not the cheapest one to it. Replace that part by the cheapest one: the rest stays the same and the total gets smaller, a contradiction. So each cell keeps one number.
> - A: The rules are swapped. $(i+1,j-1)$ would need a step back in incoming time, which "forward in time" forbids.
> - B: The rules are swapped. $(i-2,j-1)$ goes forward but skips incoming frame $i-1$, which "no frames skipped" forbids.
> - D: From $(i,j)$ on, both partial paths can continue in exactly the same way and pay the same later costs, so the worse one can never overtake. Keeping the minimum loses nothing.

### Q15 (slide 17)

Local distances $d(i,j)$ for $n=4$ incoming frames ($i$) and $N=4$ template frames ($j$):

| $d(i,j)$ | $i=1$ | $i=2$ | $i=3$ | $i=4$ |
|---|---|---|---|---|
| $j=4$ | 2 | 1 | 5 | 3 |
| $j=3$ | 2 | 6 | 0 | 2 |
| $j=2$ | 3 | 4 | 1 | 5 |
| $j=1$ | 3 | 0 | 5 | 1 |

Use $D(1,1)=d(1,1)$, on the edges $D(1,j)=D(1,j-1)+d(1,j)$ and $D(i,1)=D(i-1,1)+d(i,1)$, and inside $D(i,j)=\min[D(i,j-1),D(i-1,j),D(i-1,j-1)]+d(i,j)$. What are the score $D(4,4)$ and the backtracked path?

- A) $D(4,4)=10$, path $(1,1)\to(2,2)\to(3,3)\to(4,4)$
- B) $D(4,4)=13$, path $(1,1)\to(2,1)\to(2,2)\to(3,2)\to(3,3)\to(4,3)\to(4,4)$
- C) $D(4,4)=7$, path $(1,1)\to(2,1)\to(3,2)\to(3,3)\to(4,4)$
- D) $D(4,4)=10$, path $(1,1)\to(2,1)\to(3,2)\to(3,3)\to(4,3)\to(4,4)$

> [!success]- Answer
> **C.** Edges: $D(1,1)=3$; left column $6, 8, 10$; bottom row $D(2,1)=3$, $D(3,1)=8$, $D(4,1)=9$.
> Column 2: $D(2,2)=\min[3,6,3]+4=7$, $D(2,3)=\min[7,8,6]+6=12$, $D(2,4)=\min[12,10,8]+1=9$.
> Column 3: $D(3,2)=\min[8,7,3]+1=4$, $D(3,3)=\min[4,12,7]+0=4$, $D(3,4)=\min[4,9,12]+5=9$.
> Column 4: $D(4,2)=\min[9,4,8]+5=9$, $D(4,3)=\min[9,4,4]+2=6$, $D(4,4)=\min[6,9,4]+3=7$.
> Backtrack: $(4,4)$ from $(3,3)$, $(3,3)$ from $(3,2)$, $(3,2)$ from $(2,1)$, $(2,1)$ from $(1,1)$. Check: $3+0+1+0+3=7$. The ties at $(2,2)$ and $(4,3)$ are not on this path, so the path is unique.
> - A: Only diagonal steps, the frame-by-frame comparison: $3+4+0+3=10$.
> - B: The minimum was taken over two predecessors only (the diagonal $D(i-1,j-1)$ left out), so every diagonal move costs two steps: $3+0+4+1+0+2+3=13$.
> - D: The weight $2\,d(i,j)$ of slide 19 was put on the diagonal, although the plain recursion is asked: $3+0+2\cdot1+0+2+3=10$.

## 5. Refinements to DTW (slides 19-21)

### Q16 (slide 19, penalties)

Local distances $d(i,j)$ for an incoming word of $n=4$ frames ($i$) and a template of $N=3$ frames ($j$):

| $d(i,j)$ | $i=1$ | $i=2$ | $i=3$ | $i=4$ |
|---|---|---|---|---|
| $j=3$ | 3 | 1 | 4 | 1 |
| $j=2$ | 2 | 0 | 4 | 1 |
| $j=1$ | 3 | 1 | 1 | 1 |

Use $D(1,1)=d(1,1)$ and
$$D(i,j)=\min\begin{bmatrix}D(i-1,j)+d(i,j)+\mathit{hdp}\\ D(i-1,j-1)+2\,d(i,j)\\ D(i,j-1)+d(i,j)+\mathit{vdp}\end{bmatrix}$$
with $\mathit{hdp}=\mathit{vdp}=1$. On the edges only the one possible line is used. Then divide $D(4,3)$ by the length of the template. What is the normalised score?

- A) $2.33$
- B) $3.33$
- C) $2.50$
- D) $4.00$

> [!success]- Answer
> **B.** Edges: $D(1,1)=3$, $D(1,2)=3+2+1=6$, $D(1,3)=6+3+1=10$; $D(2,1)=5$, $D(3,1)=7$, $D(4,1)=9$.
> $D(2,2)=\min[7,\,3,\,6]=3$, $D(2,3)=\min[12,\,8,\,5]=5$, $D(3,2)=\min[8,\,13,\,12]=8$, $D(3,3)=\min[10,\,11,\,13]=10$, $D(4,2)=\min[10,\,9,\,11]=9$, $D(4,3)=\min[12,\,10,\,11]=10$ (order H, D, V).
> Path $(1,1)\to(2,2)\to(3,2)\to(4,3)$: $3+2\cdot0+(4+1)+2\cdot1=10$. Template length $N=3$: $10/3\approx3.33$.
> - A: The plain recursion was used although weights are given: score 7, and $7/3\approx2.33$.
> - C: Divided by the incoming length $n=4$ instead of the template length: $10/4=2.50$.
> - D: The penalty was also added to the diagonal line ($2d+1$): score 12, and $12/3=4.00$.

### Q17 (slide 19, choice of penalties)

DTW with distortion penalties uses $D(i,j)=\min[D(i-1,j)+d(i,j)+\mathit{hdp},\ D(i-1,j-1)+2\,d(i,j),\ D(i,j-1)+d(i,j)+\mathit{vdp}]$, where $\mathit{hdp}$ and $\mathit{vdp}$ are the horizontal and vertical penalties. Which statement is correct?

- A) Very large penalties push the path onto the diagonal, so DTW acts like the frame-by-frame distance and cannot follow real timing differences.
- B) Zero penalties are best: each horizontal or vertical step already adds a local distance, so any extra penalty only biases the score.
- C) The factor 2 on the diagonal makes diagonal steps more attractive, so the path stays close to the straight line between the corners.
- D) The penalties must equal the mean local distance, a value that the slide derives from the step weights of the recursion.

> [!success]- Answer
> **A.** With very large penalties every horizontal or vertical step is too expensive, so the path takes the diagonal wherever it can. If $n\neq N$, at least $\lvert n-N\rvert$ off-diagonal steps are still needed, and each pays the large penalty.
> - B: With zero penalties long horizontal or vertical runs cost nothing extra. One frame may be matched to many frames, which is implausible, and wrong templates can score too well.
> - C: It is the other way round. A horizontal plus a vertical step add two local distances; the factor 2 gives the diagonal the same weight, so it is not favoured.
> - D: The slide says the penalties are found empirically, by trying values on data.

### Q18 (slide 20)

One column of the cumulative matrix holds $D=(7,\,5,\,12,\,21,\,6)$. Score pruning removes every cell whose $D$ is more than 6 above the column minimum, and paths are extended only from the kept cells. Which cells are kept, and can pruning make a template's final score smaller than the $D(n,N)$ of full DTW?

- A) The cells 7, 5 and 6 are kept. Yes: pruning removes expensive paths, so the final score can get smaller.
- B) The cells 5 and 6 are kept. No: pruning only removes paths, so the final score stays equal or gets larger.
- C) The cells 7, 5 and 6 are kept. No: pruning only removes paths, so the final score stays equal or gets larger.
- D) The cells 5 and 6 are kept. Yes: pruning removes expensive paths, so the final score can get smaller.

> [!success]- Answer
> **C.** Minimum $5$, threshold $5+6=11$. Kept: $7$, $5$, $6$. Pruned: $12$ and $21$.
> Pruning only removes candidate paths. The minimum over fewer paths is equal to the true minimum or larger. The result is no longer guaranteed optimal; the gain is a speed-up of about 5 to 10.
> - A: Removing paths cannot lower a minimum. The best remaining path is at best the true best path.
> - B: The value 6 was used as an absolute threshold ($D\le6$), not as a distance above the minimum.
> - D: Both slips: the absolute threshold, and the idea that removing paths can lower the minimum.

### Q19 (slide 21)

Slide 21 shows three DTW runs. In each panel the hatched area marks the cells that were really evaluated (the slide has no legend; use this reading). Left: two examples of "eight", score pruning on, no distortion penalty. Middle: the same two words, score pruning on, a small distortion penalty. Right: "three" against "eight". In the left and middle panels the hatched area is a narrow band; in the right panel it covers almost the whole matrix. Which statement is correct?

- A) For "three" against "eight" the two words have different lengths, and DTW has to evaluate every cell of the matrix whenever $n\neq N$.
- B) With the small penalty the path in the middle panel contains longer horizontal and vertical runs than the path in the left panel.
- C) Score pruning keeps the most cells when the words are different, because for different words it can never lose the best path.
- D) For "three" against "eight" pruning was switched off, because no path stays clearly below the rest and pruning would lose the best one.

> [!success]- Answer
> **D.** For different words no path is clearly better than the others, so the best one may drift far from the column minimum and be pruned. The slide says pruning was removed because the path "would have been seriously suboptimal". Then nearly every cell is computed: the full cost of DTW.
> - A: DTW handles $n\neq N$ inside the normal grid, and pruning works for any $n$ and $N$. The two "eight" examples need not have equal lengths either. The cause is that pruning was off.
> - B: It is the other way round. The penalty makes long runs cost extra, so the path follows the two timescales more evenly.
> - C: It is the other way round. Pruning is most likely to lose the best path when the words are different.
