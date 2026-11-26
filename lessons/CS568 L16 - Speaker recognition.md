---
course: CS 568 Speech Processing
lecture: 16
title: Speaker recognition
tags: [cs568, lecture]
slides: lectures/lecture_16/l16.pdf
full_notes: lecture_notes/lecture_16/CS568_Lecture_16_Speaker_Recognition.pdf
---
# CS 568 · Lecture 16 · Speaker recognition

> [!summary] In short
> - Identification picks one speaker out of many and gets harder with more speakers. Verification tests one claimed identity and does not.
> - Features: MFCCs with $\Delta$, $\Delta^2$ and cepstral mean subtraction (CMS). High-level cues (words, prosody) need long recordings.
> - Models: DTW templates and nearest neighbour (non-parametric), VQ, GMM, HMM (parametric). Speaker GMMs are adapted from a universal background model (UBM).
> - Verification thresholds a likelihood ratio against a background model.
> - Performance: false acceptance against false rejection on ROC and DET curves, summarised by the EER or the DCF.

## 1. Identification, verification, text dependence (slides 2-6)
![[cs568_lecture_16_l16_p002.jpg]]
![[cs568_lecture_16_l16_p003.jpg]]
![[cs568_lecture_16_l16_p004.jpg]]
![[cs568_lecture_16_l16_p005.jpg]]
![[cs568_lecture_16_l16_p006.jpg]]

**Notes.**
- **Speaker identification (SID)**: match an unknown voice to one of several speaker models. No identity is claimed. Closed-set: the speaker is one of the enrolled ones, so the answer is always a name. Open-set: the answer may be "no match". Accuracy drops as the number of speakers grows.
- **Speaker verification**: the user claims an identity. The sample is compared only with the claimed model (and with a general impostor model, slide 4). One comparison, so performance does not depend on the population size.
- Two phases (slide 5): **enrollment** trains one model ("voiceprint") per speaker. **Verification** runs the same feature extraction on a new utterance, fetches the claimed model, scores, decides.
- **Text-dependent**: the system knows the text (fixed password or prompted phrase). Random prompts stop replay attacks, so it suits security. **Text-independent**: the text is unknown. More flexible, harder, suits uncooperative speakers.

> [!info] Reading the block diagram on slide 4 (beyond the slides)
> - The identity claim ("My name is Bob") only selects which speaker model is fetched. It is not evidence.
> - The same feature vectors go to two models. The speaker model gives $\log p(Y\mid\lambda_{Bob})$. The impostor model gives $\log p(Y\mid\lambda_{imp})$. It stands for "anybody else".
> - The $\Sigma$ node combines the two scores. Slide 4 draws no signs. The same diagram on slide 19 marks the speaker model with $+$ and the background model with $-$, so the node subtracts the two log scores. A difference of logs is the log of a ratio, so its output is the log likelihood ratio (olabilirlik oranı):
>
> $$\Lambda=\log p(Y\mid\lambda_{Bob})-\log p(Y\mid\lambda_{imp})=\log\frac{p(Y\mid\lambda_{Bob})}{p(Y\mid\lambda_{imp})}$$
>
> - The decision block compares $\Lambda$ with a threshold (eşik): accept if it is above, reject if it is below.
> - Speaker recognition (the whole field) is "konuşmacı tanıma" in Turkish. Speaker verification is "konuşmacı doğrulama". Speaker identification is "konuşmacı tanılama" (also "konuşmacı belirleme").

> [!question]- Check (slide 2): 50 speakers are enrolled. A person who is not enrolled speaks. What does a closed-set system answer, and what does an open-set system answer? What extra decision does the open-set system need?
> The closed-set system must answer with one of the 50 names, so here it is certainly wrong. It assumes the speaker is always one of the known speakers.
> The open-set system may answer "no match". For this it needs one more decision: is the best match good enough? That is a comparison of the best score with a threshold.

> [!question]- Check (slide 3): Count the arrows with a question mark in the two drawings at the bottom of the slide. What does the count say about the two tasks? Why does the slide call verification a special case of open-set identification?
> Identification has four arrows, one to each enrolled speaker. Verification has one arrow, to Bob only.
> So identification makes one comparison per enrolled speaker, and each extra speaker is one more chance for a wrong model to win. Verification makes one comparison, so its performance does not depend on the number of speakers.
> Open-set identification answers "one of the models, or no match". With only one model in the set this becomes "Bob, or not Bob" (one against all others). That is verification.

> [!question]- Check (slide 4): Name the two inputs of the $\Sigma$ node on the slide and say what its output means. What goes wrong if the impostor model is removed and the speaker-model score alone is compared with a threshold?
> The inputs are the score of the features under Bob's model (speaker model) and under the impostor model. The output is their difference, the log likelihood ratio: how much better Bob's model explains the speech than "anybody else" does.
> The second part is not on the slide (beyond the slides). Without the impostor model the raw score also changes with things that have nothing to do with the speaker: the words, the noise, the channel. A noisy utterance of the real Bob gets a low score and is rejected. The same things lower both scores, so they mostly cancel in the difference. One threshold then works for many conditions.

> [!question]- Check (slide 5): Find the mistake: "In the verification phase the test utterance is scored against Bob's and Sally's voiceprints, and the claim is accepted if Sally's is the better one." Also name the block that both phases share.
> The mistake: verification does not compare the enrolled speakers with each other. The claimed identity (Sally) selects one voiceprint, the arrow from Sally's model to the decision block. Only this model (and a general impostor model, slide 4) is scored, and the score is compared with a threshold. Comparing all models and picking the best is identification.
> The shared block is feature extraction. It must be the same in both phases, or the test features do not match the model.

> [!question]- Check (slide 6): Choose text-dependent or text-independent and give the reason from the slide: (a) telephone banking login, (b) finding one known person in recorded phone calls. Why does a random prompted phrase help in (a)?
> (a) Text-dependent. The user is cooperative and the application is security. The system knows the text, so it can also check that the right words were said.
> (b) Text-independent. The speaker is uncooperative and says anything, so the system cannot know the text.
> A fixed password can be recorded once and played back by an impostor. A random prompt changes at every login, so an old recording does not contain the requested phrase.

## 2. Speaker features (slides 7-9)
![[cs568_lecture_16_l16_p007.jpg]]
![[cs568_lecture_16_l16_p008.jpg]]
![[cs568_lecture_16_l16_p009.jpg]]

**Notes.** Low-level features: short-time spectra (MFCCs, the same as in speech recognition), their derivatives $\Delta$ and $\Delta^2$, pitch and energy averages. High-level features: word usage, phone sequences, pronunciation, pitch and energy contours. These need long recordings and are hard to extract.

**Cepstral mean subtraction (CMS)**: subtract the average cepstral vector over the recording, with $\mathbf c(t)$ the cepstrum of frame $t$ and $T$ frames:

$$\tilde{\mathbf c}(t)=\mathbf c(t)-\bar{\mathbf c},\qquad \bar{\mathbf c}=\frac1T\sum_{t=1}^{T}\mathbf c(t)$$

A fixed channel $h[n]$ convolves the signal, so it adds a constant in the cepstrum: $\mathbf c_y(t)=\mathbf c_x(t)+\mathbf c_h$. Subtracting the mean removes it: $\mathbf c_y(t)-\bar{\mathbf c}_y=\mathbf c_x(t)-\bar{\mathbf c}_x$.

> [!warning] Watch out
> CMS removes everything constant over the recording, including the speaker's own long-term average spectrum. It does not cancel a channel that changes during the recording.

> [!info] What carries the speaker information (beyond the slides)
> - **Segmental** features describe one short frame (about 20-30 ms), so they describe the spectral envelope. The envelope is set by the vocal-tract shape and size, which differ between people. LPC and filter-bank (MFCC) analysis measure this envelope without having to find formant peaks one by one.
> - **Supra-segmental** features extend over several segments: pitch, energy, rhythm. Pitch comes from the vocal folds, not from the vocal tract. It exists only in voiced frames, so a voiced/unvoiced detector must run first.
> - **High-level** features are learned habits: word choice, dialect, idiolect, prosody. They are not properties of the body.

> [!question]- Check (slide 7): The slide names a segmental and a supra-segmental low-level feature. For each one say what is measured in practice and which difficulty the slide mentions. Why do the high-level features not replace them?
> Segmental: the spectral shape of a short frame. Formants would describe it, but they are hard to track reliably, so short-term spectral measurements (LPC, filter bank) are used.
> Supra-segmental: pitch periodicity. It is easy to extract, but only voiced frames have a pitch, so a voiced/unvoiced detector is needed first.
> High-level features (words, syntax, prosody, dialect, idiolect) are harder to extract than the low-level ones.

> [!question]- Check (slide 8): Why does the slide call the use of MFCCs for speaker recognition counterintuitive, and how does it resolve this? Which of the four feature groups on the slide does it call unreliable, and why?
> Speech recognition should ignore who speaks. Speaker recognition should ignore what is said. So one expects two different optimal feature sets. In practice the best speech representation is also a good speaker representation. The slide's guess: perhaps the optimal representation contains both speech and speaker information.
> Pitch (and energy) averages: robust pitch extraction is hard, and the pitch of one speaker changes a lot between recordings (large intra-speaker variation). So pitch separates speakers less well than one may expect.

> [!question]- Check (slide 8, CMS): The slide says CMS needs a "sufficiently long" recording and a "slowly varying" channel. What goes wrong if (a) the recording is one short word, (b) the channel changes in the middle of the recording?
> (a) The mean of a few frames is mostly the mean of the few sounds that were said, not the channel. Subtracting it removes speech and speaker information, and the result depends on the word.
> (b) CMS subtracts one constant vector. A channel that changes adds a different constant in each part, so one average cannot cancel both. A residual offset stays in every frame.

> [!question]- Check (slide 9): Why can linguistic measurements be used for indexing broadcast news but not in a fixed-password system? Name one weakness of the word-usage features and one of the phone-sequence features from the slide.
> Word frequencies, fillers and pronunciation habits are statistics. They need many words before they are stable, so they need long recordings. A password is a few seconds long and its words are fixed by the system, so the speaker has no choice of words to show.
> Word usage: it needs a large-vocabulary recogniser (LVCSR), and the recognition errors of that system corrupt the word statistics.
> Phone sequences: the lexical constraints that help ASR push the output towards dictionary pronunciations, so the unusual phone sequences that are typical of one speaker may be removed.

## 3. Non-parametric models (slides 10-12)
![[cs568_lecture_16_l16_p010.jpg]]
![[cs568_lecture_16_l16_p011.jpg]]
![[cs568_lecture_16_l16_p012.jpg]]

**Notes.** Non-parametric models keep the enrollment data. Parametric models summarise it (VQ, GMM, HMM, SVM).

**Template matching**: for fixed passwords. Align the test utterance with each enrollment utterance by DTW, accept if the distance is below a threshold. Rarely used today.

**Nearest neighbour**: with $X=\{\mathbf x_j\}$ the enrollment vectors, $\mathbf y$ a test vector, $d_{nn}(\mathbf y,X)=\min_{\mathbf x_j\in X}\lVert\mathbf y-\mathbf x_j\rVert$ and $V[r]\propto r^D$ the volume of a $D$-dimensional sphere:

$$p_{nn}(\mathbf y;X)=\frac{1}{V\big[d_{nn}(\mathbf y,X)\big]},\qquad s_{nn}(Y;X)=-\sum_{\mathbf y_j\in Y}\ln d_{nn}(\mathbf y_j,X)$$

The speaker with the largest $s_{nn}$ is identified. The measure $s'_{nn}$ on slide 12 works better: average squared nearest-neighbour distance from $Y$ to $X$ and from $X$ to $Y$, minus the same averages inside $Y$ and inside $X$.

> [!warning] Watch out
> $s_{nn}$ is a similarity (pick the maximum). $s'_{nn}$ is a distance (pick the minimum, it can be negative). A test vector equal to an enrollment vector gives $d_{nn}=0$ and an infinite $s_{nn}$, so real systems floor $d_{nn}$.

> [!info] Where $p_{nn}$ and $s_{nn}$ come from (beyond the slides)
> Slide 11 gives $p_{nn}$ without a derivation ("see CSCE 666 lecture notes").
> - The $k$-nearest-neighbour density estimate is $p(\mathbf y)\approx k/(N\,V)$. $N$ is the number of enrollment vectors. $V$ is the volume of the smallest sphere around $\mathbf y$ that contains $k$ of them.
> - With $k=1$ the radius of that sphere is $d_{nn}$. $k/N$ is a constant for one speaker, so $p\propto 1/V[d_{nn}]$. A close neighbour means a small sphere and a high density.
> - Slide 12, "taking logs and removing constant terms": frames are treated as independent, so the log-likelihood of $Y$ is a sum over frames. With $V[r]\propto r^D$:
>
> $$\ln p_{nn}(\mathbf y;X)=-\ln V[d_{nn}]=-D\ln d_{nn}-\text{const}$$
>
> - Drop the constant and the factor $D>0$. Neither changes which speaker has the largest value. What is left is $s_{nn}(Y;X)=-\sum_j\ln d_{nn}(\mathbf y_j,X)$.
> - The four terms of $s'_{nn}$ in slide order: (1) average squared distance from each test vector to its nearest enrollment vector, (2) the same from each enrollment vector to its nearest test vector, (3) minus the average squared nearest-neighbour distance inside $Y$, (4) minus the same inside $X$. $N_y$ and $N_x$ are the numbers of test and enrollment vectors.

> [!question]- Check (slide 10): Compare the two model classes: what does each one store after enrollment, and when does the slide say each one is effective? Put DTW templates, GMM, nearest neighbour, VQ and HMM in the right class.
> Non-parametric: makes few structural assumptions and keeps the enrollment data itself. Effective when there is sufficient enrollment data to match the test data. DTW templates and nearest neighbour.
> Parametric: assumes a structure and stores only its parameters (centroids, means, covariances, weights, transition probabilities). This is the "parsimonious" representation. It uses the enrollment data well if the constraints are chosen properly, which means if the assumed structure fits speech. VQ, GMM, HMM (and SVM).

> [!question]- Check (slide 11, template matching): Why does template matching need DTW before a distance can be computed, and why does it only suit fixed-password systems?
> Two repetitions of the same password have different lengths and different timing. DTW aligns the frames of the test utterance with the frames of an enrollment utterance, so that the same sounds are compared.
> The template is a sequence of frames of one specific text. If the test text is different there is nothing to align, so the distance is meaningless.

> [!question]- Check (slide 11, nearest neighbour): In $p_{nn}(y;X)=1/V[d_{nn}(y,X)]$ with $V[r]\sim r^D$, the nearest-neighbour distance of a test vector doubles. By what factor does the likelihood change for $D=2$ and for $D=12$?
> $V$ grows with $r^D$, so doubling $d_{nn}$ multiplies the volume by $2^D$ and divides the likelihood by $2^D$.
> $D=2$: $2^2=4$, the likelihood falls to $1/4$.
> $D=12$: $2^{12}=4096$, the likelihood falls to $1/4096$.
> In a high-dimensional feature space a small change of distance is a very large change of likelihood.

> [!question]- Check (slide 12): A test utterance has two vectors. Their nearest-neighbour distances to speaker A's data are $0.5$ and $1$, and to speaker B's data $2$ and $2$. Compute $s_{nn}$ for both and decide. Then say why $s'_{nn}$ subtracts the last two sums.
> $s_{nn}(Y;A)=-(\ln 0.5+\ln 1)=-(-0.693+0)=0.693$.
> $s_{nn}(Y;B)=-(\ln 2+\ln 2)=-(0.693+0.693)=-1.386$.
> $0.693>-1.386$, so speaker A is identified (greatest $s_{nn}$).
> The slide gives no reason, it only says that $s'_{nn}$ works better. The following is an interpretation (beyond the slides). The last two sums are nearest-neighbour distances inside $Y$ and inside $X$. They measure how spread out each set is by itself. A set that is spread out gives large cross distances even to data of the same speaker. Subtracting the inside distances removes this effect, so the measure is small when the points of $Y$ lie about as close to $X$ as the points of each set lie to each other.
> The third sum (inside $Y$) is the same for every speaker, so it cannot change which speaker wins. It only shifts the value, which matters when $s'_{nn}$ is compared with a threshold. The fourth sum (inside $X$) differs from speaker to speaker.

## 4. Parametric models: VQ, GMM, HMM (slides 13-15)
![[cs568_lecture_16_l16_p013.jpg]]
![[cs568_lecture_16_l16_p014.jpg]]
![[cs568_lecture_16_l16_p015.jpg]]

**Notes.**
- **VQ**: a codebook of centroids $\boldsymbol\mu_j$ built with LBG. Start with $k=1$, split the cluster with the largest variance into $\boldsymbol\mu\pm\boldsymbol\epsilon$, re-run k-means, repeat. Score: the sum-squared error of the test vectors against the claimed speaker's codebook (small is good),

$$D=\sum_{j=1}^{J}\ \sum_{\mathbf x_i\to\boldsymbol\mu_j}\lVert\mathbf x_i-\boldsymbol\mu_j\rVert^2$$

- **GMM**: $p(\mathbf y\mid\lambda)=\sum_{m=1}^{M}w_m\,\mathcal N(\mathbf y;\boldsymbol\mu_m,\Sigma_m)$, trained with EM. Score: the average log-likelihood, so that utterances of different lengths compare,

$$s(Y\mid\lambda)=\frac1T\sum_{t=1}^{T}\log p(\mathbf y_t\mid\lambda)$$

- **Likelihood-ratio test** for verification: accept if $\dfrac{p(Y\mid\lambda)}{p(Y\mid\lambda_{BG})}>\theta$, where $\lambda_{BG}$ is a background model trained on a large independent database.
- **HMM**: best for text-dependent systems (phone, word or sentence models, Baum-Welch). For text-independent systems ergodic HMMs (all transitions allowed) add little over GMMs.

> [!warning] Slide error
> Slide 13 writes the summand of $D$ as $(x_i-\mu_j)$. A sum-squared error needs the squared norm, as above.

> [!warning] Slide wording
> Slide 13 calls $\mu_j$ "the sample mean of test vectors assigned to the $j$-th cluster". That is how k-means defines a centroid while the codebook is trained (there the vectors are the training vectors). For testing a claim, the same slide says the VQ models of the target speaker are already available. So in this note, as in the full lecture notes, $\boldsymbol\mu_j$ is a centroid of the claimed speaker's codebook from enrollment, and $\mathbf x_i\to\boldsymbol\mu_j$ means that test vector $\mathbf x_i$ is assigned to its nearest centroid. This reading is our interpretation of the slide.

> [!info] GMMs ignore frame order
> Each term of $s(Y\mid\lambda)$ uses only its own frame, so shuffling the frames does not change the score.

> [!warning] Slide typo
> Slide 14 writes the frame inside the log as $y_y$. The sum runs over $t$, so it is $\mathbf y_t$, as in the formula above.

> [!info] The likelihood ratio in the log domain (beyond the slides)
> Slide 14 shows the ratio $p(Y\mid\lambda)/p(Y\mid\lambda_{BG})$ but the score it defines is an average log-likelihood. Systems work with logs, so the ratio becomes a difference:
>
> $$\Lambda(Y)=s(Y\mid\lambda)-s(Y\mid\lambda_{BG})=\frac1T\sum_{t=1}^{T}\Big[\log p(\mathbf y_t\mid\lambda)-\log p(\mathbf y_t\mid\lambda_{BG})\Big]$$
>
> - $\lambda$: the model of the claimed speaker. $\lambda_{BG}$: the background model, "speech of anybody".
> - $\Lambda>0$: the claimed speaker's model explains the utterance better than the background model.
> - Decision: accept if $\Lambda$ is at or above a threshold. Without the $1/T$, "ratio $\ge\theta$" and "log ratio $\ge\log\theta$" are the same test (frames treated as independent, so $\log p(Y\mid\lambda)$ is the sum over frames). $\Lambda$ is that log ratio divided by $T$, so its threshold is $(\log\theta)/T$, a value per frame.
> - $1/T$ makes the score an average per frame, so a long and a short utterance can use the same threshold on $\Lambda$.

> [!question]- Check (slide 13): Find the two mistakes: "LBG needs the codebook size $k$ before it starts. After training, the claim is accepted if the distortion $D$ of the test vectors is above a threshold, because a large $D$ means a good match."
> Mistake 1: LBG is used because $k$ is unknown. It starts with $k=1$ (one centroid, the mean of all data), splits the cluster with the largest variance into two centroids $\mu\pm\epsilon$, re-runs k-means, and repeats until the codebook is large enough.
> Mistake 2: $D$ is a sum-squared error, the summed squared distance of each test vector to the centre $\mu_j$ of the cluster it is assigned to. An error is small for a good match, so the claim is accepted if $D$ is below a threshold.
> In this note $\mu_j$ is a centroid of the claimed speaker's codebook, so a small $D$ means the test vectors lie close to that speaker's centroids. The slide words $\mu_j$ differently (see the warning "Slide wording" above).

> [!question]- Check (slide 14): A test utterance has average log-likelihood $s(Y\mid\lambda)=-41.2$ under the claimed speaker's GMM and $s(Y\mid\lambda_{BG})=-42.0$ under the background model. The threshold on the average log ratio $\Lambda$ is $0.5$. Decide. What would the decision be for a threshold of $1.0$? Why is $-41.2$ alone not enough to decide?
> $\Lambda=-41.2-(-42.0)=0.8$ per frame.
> $0.8\ge0.5$: accept. With the threshold $1.0$: $0.8<1.0$, reject. A higher threshold is stricter.
> $-41.2$ alone says little. The value depends on the words, the noise and the channel, not only on the speaker. The background score shows what any speaker would get on the same utterance. Only the difference tells whether the claimed model fits better than "anybody".

> [!question]- Check (slide 14, GMM): Name the three kinds of parameters of a GMM and the algorithm that learns them. In which sense is a GMM a generalisation of k-means, and what does the last bullet of the slide say a GMM cannot model?
> Parameters: the means, the covariance matrices and the mixing coefficients (weights) of the components. They are learned with the EM algorithm.
> k-means describes each cluster only by its centre. A GMM gives each cluster its own covariance matrix (its own shape and size) and a weight, and it assigns each vector softly to all clusters.
> It does not model the temporal aspects of speech: the order of the frames. This is why it suits text-independent recognition, where the order of the sounds is not known.

> [!question]- Check (slide 15): Compare the left-right HMM of ASR with the ergodic HMM. What do the emission and the transition probabilities of an ergodic HMM represent? Why is a GMM usually chosen for text-independent systems?
> Left-right HMM: states are passed in a fixed order, which fits a known text (a password, a digit). Ergodic HMM: all transitions between states are allowed, so any order of sounds is possible. This fits unknown text.
> Emissions: the different spectral characteristics, associated with different phones. Transitions: some temporal information, which sound class tends to follow which.
> Experiments show that adding the transition probabilities has little effect on performance. A GMM is the same idea without transitions, so it is simpler and about as good.

## 5. Adapting from a UBM (slides 16-17)
![[cs568_lecture_16_l16_p016.jpg]]
![[cs568_lecture_16_l16_p017.jpg]]

**Notes.** Enrollment data is too little to train a model from scratch, and conditions differ between enrollment and test (handset, network, noise). So train a large GMM on many speakers, the **universal background model (UBM)**, and adapt it to each speaker.

MAP adaptation of the means (from our notes, after Reynolds et al., 2000). $\gamma_m(t)$ is the UBM posterior of component $m$ for speaker frame $\mathbf x_t$, $r$ is the relevance factor (about 16):

$$n_m=\sum_t\gamma_m(t),\qquad E_m(\mathbf x)=\frac{1}{n_m}\sum_t\gamma_m(t)\,\mathbf x_t$$

$$\hat{\boldsymbol\mu}_m=\alpha_m E_m(\mathbf x)+(1-\alpha_m)\,\boldsymbol\mu_m^{UBM},\qquad \alpha_m=\frac{n_m}{n_m+r}$$

Components that saw much speaker data move to the data. The others stay at the UBM. The UBM itself serves as $\lambda_{BG}$ in the likelihood ratio.

> [!info] Reading the figure on slide 17 (beyond the slides)
> - Panel a: the four grey ellipses are four Gaussian components of the UBM. The crosses are the enrollment frames of one speaker. They lie near the two upper components only.
> - Panel b: the filled brown ellipses are the adapted speaker model. The dashed outlines show where the UBM components were. The two upper components have moved towards the crosses. The two lower ones lie exactly on their dashed outlines: they saw no speaker data, so they are copies of the UBM.
> - In the drawing the two moved ellipses also have a slightly different shape. The formula in this note adapts only the means. The weights and covariances are then copied from the UBM (the full notes: "often only the means are adapted").
> - In the MAP formula this is $n_m$: many frames near component $m$ give a large $n_m$, so $\alpha_m$ is close to 1 and the mean moves to the data. $n_m=0$ gives $\alpha_m=0$ and $\hat{\boldsymbol\mu}_m=\boldsymbol\mu_m^{UBM}$.
> - The speaker model and the UBM keep the same components in the same order. In the likelihood ratio the unadapted components give almost the same value in numerator and denominator, so sounds that the speaker never said in enrollment do not count against the speaker.

> [!question]- Check (slide 16): The slide gives two kinds of mismatch between enrollment and test. Name them. Why is training a speaker GMM from the enrollment data alone a bad idea, and what does adaptation do instead?
> Mismatch in conditions: enrollment may be recorded in a single call, and the test call uses a different handset, network (landline or cellular) or background noise. Mismatch in linguistic content: in text-independent systems the test speech contains sounds and words that the enrollment speech did not.
> The enrollment data is too limited. A model with many parameters trained on it is unreliable, and it has no information about sounds or conditions that did not occur in enrollment.
> Adaptation starts from a model trained on much data from many speakers and changes it only where the speaker's data gives evidence. The rest is taken over from the large model.

> [!question]- Check (slide 17): In panel b, which components differ from their dashed outlines and which do not? Explain with panel a. Then use the MAP formula for the means from the Notes above (it is not on the slide): with relevance factor $r=16$, UBM mean $2$ and data mean $E_m(x)=5$, compute the adapted mean for $n_m=4$ and for $n_m=64$.
> The two upper components have moved, because the speaker's training data (the crosses in panel a) lies near them. The two lower components are unchanged, because no cross lies near them.
> $n_m=4$: $\alpha_m=4/(4+16)=0.2$. $\hat\mu_m=0.2\cdot5+0.8\cdot2=1.0+1.6=2.6$.
> $n_m=64$: $\alpha_m=64/(64+16)=0.8$. $\hat\mu_m=0.8\cdot5+0.2\cdot2=4.0+0.4=4.4$.
> With little data the mean stays near the UBM value 2. With much data it moves near the data mean 5. A larger $r$ makes the model trust the UBM longer.

## 6. Decision rules, threshold, normalisation (slides 18-19)
![[cs568_lecture_16_l16_p018.jpg]]
![[cs568_lecture_16_l16_p019.jpg]]

**Notes.**
- Identification: $\hat S=\arg\max_j s(Y\mid\lambda_j)$.
- Verification: accept if $s(Y\mid\lambda_i)\ge\theta$ for the claimed speaker $i$.
- Open-set identification: closed-set identification first, then verification of the winner.

Bayes threshold on the likelihood ratio, with $c_{fa}$ the cost of a false acceptance, $c_{fr}$ the cost of a false rejection, $p_{imp}$ the prior probability of an impostor:

$$\theta^*=\frac{c_{fa}}{c_{fr}}\cdot\frac{p_{imp}}{1-p_{imp}}$$

Real scores do not behave as the theory says, so they are normalised: $\Lambda_{norm}=(\Lambda-\mu)/\sigma$. **Z-norm**: $\mu,\sigma$ from scoring the target model offline against impostor utterances. **T-norm**: from scoring the test utterance against a cohort of impostor models. **H-norm**: Z-norm per handset type.

> [!warning] Slide wording
> Slide 19 calls $c_{fa}$ and $c_{fr}$ "rates". They are costs, not error rates.

> [!info] Where the Bayes threshold comes from (beyond the slides)
> Slide 19 gives $\theta^*$ without a derivation. "tar" means the speaker is the claimed target, "imp" means an impostor.
> - Accepting is wrong only for an impostor. Expected cost of accepting: $c_{fa}\,P(\text{imp}\mid Y)$.
> - Rejecting is wrong only for the target. Expected cost of rejecting: $c_{fr}\,P(\text{tar}\mid Y)$.
> - Accept when accepting is cheaper: $c_{fr}\,P(\text{tar}\mid Y)\ge c_{fa}\,P(\text{imp}\mid Y)$.
> - Bayes' rule: $P(\text{tar}\mid Y)\propto p(Y\mid\lambda)(1-p_{imp})$ and $P(\text{imp}\mid Y)\propto p(Y\mid\lambda_{BG})\,p_{imp}$, with the same factor $1/p(Y)$. Insert and rearrange:
>
> $$\frac{p(Y\mid\lambda)}{p(Y\mid\lambda_{BG})}\ \ge\ \frac{c_{fa}}{c_{fr}}\cdot\frac{p_{imp}}{1-p_{imp}}=\theta^*$$
>
> - Reading: a costly false acceptance (large $c_{fa}$) or many impostors (large $p_{imp}$) raise $\theta^*$, so the system is stricter. A costly false rejection (large $c_{fr}$) lowers it.
> - The block diagram on slide 19 is the diagram of slide 4 with one more block. The $\Sigma$ node gives $\Lambda$ = speaker-model score ($+$) minus background-model score ($-$). Score normalisation turns $\Lambda$ into $\Lambda_{norm}$, and the threshold is applied to $\Lambda_{norm}$.

> [!question]- Check (slide 18): Three enrolled speakers give the scores $s(Y\mid\lambda_A)=-38.5$, $s(Y\mid\lambda_B)=-36.9$, $s(Y\mid\lambda_C)=-37.4$. Give (a) the closed-set identification decision, (b) the open-set decision with $\theta=-36.0$, (c) the verification decision for the claim "I am C" with $\theta=-37.5$.
> (a) The maximum score is $-36.9$, so $\hat S=B$.
> (b) Step 1, closed-set identification: B. Step 2, verification of the winner: $-36.9\ge-36.0$ is false, so the answer is "no match".
> (c) Verification uses only the score of the claimed speaker: $-37.4\ge-37.5$ is true, so the claim is accepted. It does not matter that B has a higher score, because B is never scored in verification.

> [!question]- Check (slide 19, threshold): Name each term of $\theta^*=\dfrac{c_{fa}}{c_{fr}}\cdot\dfrac{p_{imp}}{1-p_{imp}}$. Compute $\theta^*$ and $\ln\theta^*$ for $c_{fa}=10$, $c_{fr}=1$, $p_{imp}=0.05$. In which direction does $\theta^*$ move if impostors become more frequent?
> $c_{fa}$: cost of a false acceptance. $c_{fr}$: cost of a false rejection. $p_{imp}$: prior probability that the speaker is an impostor. $1-p_{imp}$: prior probability of the true speaker. $\theta^*$ is compared with the likelihood ratio.
> $\theta^*=\dfrac{10}{1}\cdot\dfrac{0.05}{0.95}=10\cdot0.0526=0.526$. $\ln0.526=-0.642$.
> Accept if the log likelihood ratio is above $-0.642$. Impostors are rare here, so the claim is accepted even when the background model fits a little better.
> A larger $p_{imp}$ makes $p_{imp}/(1-p_{imp})$ larger, so $\theta^*$ rises and the system is stricter.

> [!question]- Check (slide 19, normalisation): In the block diagram, what are the two inputs of the $\Sigma$ node and what is $\Lambda$? A raw score $\Lambda=2.4$ belongs to a target model whose impostor scores have mean $0.6$ and standard deviation $1.2$. Compute $\Lambda_{norm}$. Why is this step needed if the theory already gives $\theta^*$?
> The inputs are the speaker-model score (sign $+$) and the background-model score (sign $-$). $\Lambda$ is their difference, the log likelihood ratio.
> $\Lambda_{norm}=(2.4-0.6)/1.2=1.8/1.2=1.5$. This is the Z-norm: mean and standard deviation come from scoring the target model against impostor utterances. The slide only names Z-norm, H-norm and T-norm. The formula is in the Notes above. The score is 1.5 standard deviations above the typical impostor score for this model.
> The models are not exact, so the scores are not true likelihood ratios. The impostor scores of different speaker models (or handsets) have different means and spreads, and one global threshold would be too strict for some and too loose for others. After normalisation the impostor scores of every model have mean 0 and standard deviation 1, so one threshold fits all.

## 7. Errors, ROC, DET, EER, DCF (slides 20-22)
![[cs568_lecture_16_l16_p020.jpg]]
![[cs568_lecture_16_l16_p021.jpg]]
![[cs568_lecture_16_l16_p022.jpg]]

**Notes.** Verification has two errors: false acceptance (impostor accepted, probability $p_{fa}$) and false rejection (genuine speaker rejected, $p_{fr}$). Moving $\theta$ lowers one and raises the other.

- **ROC**: true-positive rate $1-p_{fr}$ against $p_{fa}$ as $\theta$ sweeps.
- **DET**: $p_{fr}$ against $p_{fa}$ on normal-deviate axes, $z=\Phi^{-1}(p)$. If impostor scores are $\mathcal N(0,1)$ and genuine scores $\mathcal N(d',1)$, then $\Phi^{-1}(p_{fr})=-\Phi^{-1}(p_{fa})-d'$: a straight line of slope $-1$. Better systems lie closer to the bottom-left corner.
- **EER**: the error at the threshold where $p_{fa}=p_{fr}$. One number, no costs.
- **DCF**: choose the threshold that minimises $C=p_{imp}\,c_{fa}\,p_{fa}+(1-p_{imp})\,c_{fr}\,p_{fr}$.

> [!warning] Slide wording
> Slide 20 says "detection error threshold" and "plotted in log scale". The name is detection error trade-off, and the axes are normal-deviate (probit) scaled, not logarithmic.

> [!info] Reading the four pictures on slide 21 (beyond the slides)
> - **Top left**: two score distributions. The blue one (left) is the negative class, here the impostor scores. The red one (right) is the positive class, the genuine scores. The vertical line is the threshold. Everything to its right is accepted.
> - The four areas: TP = red area right of the line (genuine, accepted). FN = red area left of the line (genuine, rejected: false rejection (yanlış ret)). FP = blue area right of the line (impostor, accepted: false acceptance (yanlış kabul)). TN = blue area left of the line (impostor, rejected).
> - **Table**: each column sums to 1. TP + FN = 1 for the genuine trials, FP + TN = 1 for the impostor trials. So $P(TP)=1-p_{fr}$ and $P(FP)=p_{fa}$.
> - **Top right**: one threshold gives one point $(P(FP),P(TP))$. Moving the threshold (green arrow) moves the point along the curve. That curve is the ROC. The straight diagonal is a system that guesses.
> - **Bottom left (ROC)**: several systems in one plot. A curve closer to the top-left corner is better.
> - **Bottom right (DET)**: miss probability ($p_{fr}$) against false-alarm probability ($p_{fa}$), both in percent. The ticks 0.5, 2, 5, 10, 20, 30 are not equally spaced: this is the normal-deviate scale. A curve closer to the bottom-left corner is better. The equal error rate (eşit hata oranı) of a curve is where it crosses the diagonal "miss = false alarm".

> [!question]- Check (slide 20): A test has 400 impostor trials and 250 genuine trials. The system accepts 12 impostors and rejects 20 genuine speakers. Compute $p_{fa}$ and $p_{fr}$. Then the threshold $\theta$ is raised: what happens to each of the two?
> $p_{fa}=12/400=0.03=3\%$. It is counted on the impostor trials only.
> $p_{fr}=20/250=0.08=8\%$. It is counted on the genuine trials only.
> A higher threshold accepts fewer trials of both kinds. So $p_{fa}$ falls and $p_{fr}$ rises. The two errors move in opposite directions, as the slide says.

> [!question]- Check (slide 20, DET): The slide says identification and verification are evaluated differently. How? And under which condition is a DET curve a straight line with slope $-1$?
> Identification has one kind of error: the wrong speaker is chosen. So it is evaluated with the probability of misclassification. Verification has two kinds of error, false acceptance and false rejection, and their balance depends on the threshold. So it is evaluated with a curve of $p_{fr}$ against $p_{fa}$ over all thresholds.
> The DET is a straight line with slope $-1$ if the genuine scores and the impostor scores are both Gaussian with the same standard deviation ($\sigma=1$ on the slide). The reason: the normal-deviate axis $z=\Phi^{-1}(p)$ undoes the Gaussian curve shape, so $z_{fr}=-z_{fa}-d'$ (see the Notes above). Real scores are roughly Gaussian, so real DET curves are nearly straight. A real curve can then be judged by how close it is to this ideal line and to the bottom-left corner.

> [!question]- Check (slide 21, ROC): In the top-left picture the threshold line moves to the right. What happens to the areas TP, FP, FN and TN, and in which direction does the point on the top-right ROC move? In the bottom-left ROC plot, which curve is the worst system?
> Less of each distribution lies to the right of the line. TP and FP shrink. FN and TN grow.
> $P(TP)$ and $P(FP)$ both fall, so the point moves down and to the left along the curve, towards the corner $(0\%,0\%)$.
> The black curve is the worst. It lies below the coloured curves over most of the plot, closest to the diagonal of a guessing system: for the same false positive rate it has the lowest true positive rate.

> [!question]- Check (slide 21, DET): The DET plot compares four systems (baseline, feature warping, T-norm, feature warping + T-norm). Which curve is the lowest at the left end (false alarms near 0.5%) and which at the right end (near 30%)? What does it mean that curves cross?
> At the left end the "feature warping + T-norm" curve (magenta) is the lowest. It is the only curve inside the plot there, with a miss probability a little below 30%. The other three leave the plot through the top (miss above 30%) before they reach 0.5%. At the right end the "feature warping" curve (green) is the lowest and the "T-norm" curve (blue) is the highest, above the baseline (red).
> Lower means fewer misses at the same false-alarm rate, so that system is better at that operating point.
> Crossing curves mean that no system is the best at every threshold. The ranking depends on the operating point, so one must first say which errors matter (slide 22) before choosing a system.

> [!question]- Check (slide 22): Name each term of $C=p_{imp}c_{fa}p_{fa}+(1-p_{imp})c_{fr}p_{fr}$. With $p_{imp}=0.02$, $c_{fa}=10$, $c_{fr}=1$, compare threshold A ($p_{fa}=1\%$, $p_{fr}=12\%$) with threshold B ($p_{fa}=5\%$, $p_{fr}=4\%$). Why is the EER threshold not always the best choice?
> $p_{imp}$: prior probability of an impostor. $c_{fa}$, $c_{fr}$: costs of a false acceptance and of a false rejection. $p_{fa}$, $p_{fr}$: the two error probabilities at the chosen threshold. First term: expected cost of false acceptances. Second term: expected cost of false rejections.
> A: $C=0.02\cdot10\cdot0.01+0.98\cdot1\cdot0.12=0.002+0.1176=0.1196$.
> B: $C=0.02\cdot10\cdot0.05+0.98\cdot1\cdot0.04=0.010+0.0392=0.0492$.
> B is better. Impostors are rare, so the many false rejections of A cost more than its few false acceptances save.
> The EER uses no costs and no prior. It treats the two errors as equally important. When costs or priors are unequal the cost-minimising threshold lies elsewhere on the DET curve.

## 8. Applications (slide 23)
![[cs568_lecture_16_l16_p023.jpg]]

**Notes.** Transaction authentication and access control use verification. Monitoring, information retrieval (audio indexing, speaker diarisation) and forensics mostly use identification. A voice changes with health, age and channel, and it can be replayed or synthesised, so secure systems use random prompts and liveness checks.

> [!question]- Check (slide 23): For (a) a telephone credit card purchase, (b) speaker diarisation of a recorded meeting, (c) home parole verification: is it identification or verification, and would the system be text-dependent or text-independent? Reason with slides 2-6.
> The slide only lists the applications. The labels below are our reasoning from slides 2-6, not statements of the slide.
> (a) Verification. The caller claims to be the card holder and one claim is tested. The caller is cooperative, so a text-dependent system with a prompted phrase fits.
> (b) Closest to identification. Nobody claims an identity. The question is "who speaks when" among the speakers in the recording. Usually there are no enrolled models, so the system groups the speech by speaker and does not give names. The speech is free conversation, so it must be text-independent.
> (c) Verification, as the name says, although the slide lists it under Monitoring. The system checks that the person who answers the call is the one named person. The person is cooperative and can be asked to say a prompted phrase, so text-dependent fits, and a random prompt protects against a recording.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Closed-set / open-set | answer is always a name / may be "no match" |
| CMS | $\tilde{\mathbf c}(t)=\mathbf c(t)-\bar{\mathbf c}$, cancels a fixed channel |
| Nearest-neighbour score | $s_{nn}(Y;X)=-\sum_j\ln d_{nn}(\mathbf y_j,X)$, maximise |
| VQ distortion | $D=\sum_j\sum_{\mathbf x_i\to\boldsymbol\mu_j}\lVert\mathbf x_i-\boldsymbol\mu_j\rVert^2$, minimise |
| GMM score | $s(Y\mid\lambda)=\frac1T\sum_t\log p(\mathbf y_t\mid\lambda)$ |
| Likelihood-ratio test | $p(Y\mid\lambda)/p(Y\mid\lambda_{BG})$ compared with $\theta$ |
| UBM | large GMM trained on many speakers |
| MAP adaptation | $\hat{\boldsymbol\mu}_m=\alpha_mE_m(\mathbf x)+(1-\alpha_m)\boldsymbol\mu_m^{UBM}$, $\alpha_m=n_m/(n_m+r)$ |
| Bayes threshold | $\theta^*=(c_{fa}/c_{fr})\,p_{imp}/(1-p_{imp})$ |
| Z-norm, T-norm | $(\Lambda-\mu)/\sigma$ with impostor utterances / cohort models |
| EER | error where $p_{fa}=p_{fr}$ |
| DCF | $C=p_{imp}c_{fa}p_{fa}+(1-p_{imp})c_{fr}p_{fr}$ |

## Check yourself
> [!question]- 1. Why does identification accuracy drop with 1000 enrolled speakers instead of 10, while verification accuracy does not?
> In identification the correct model must beat every wrong model, and 999 wrong models give many more chances of an accidental win. Verification makes one comparison with the claimed model, so the other speakers never enter.

> [!question]- 2. Two frames have 1-D cepstra 4 and 6 after a channel with $c_h=3$. Apply CMS. Compare with CMS on the clean frames.
> Received: mean 5, so $-1$ and $+1$. Clean frames: $4-3=1$ and $6-3=3$, mean 2, so $-1$ and $+1$. The same: CMS output does not depend on the channel.

> [!question]- 3. Codebook $\mu_1=(0,0)$, $\mu_2=(2,2)$. Test vectors $(0,1)$, $(2,1)$, $(3,3)$. Compute the VQ distortion $D$.
> $(0,1)$ goes to $\mu_1$ with error 1. $(2,1)$ goes to $\mu_2$ with error 1. $(3,3)$ goes to $\mu_2$ with error 2. $D=1+1+2=4$.

> [!question]- 4. 1-D, $r=16$, $\mu^{UBM}_A=1.0$. Frames $x=1,2,3$ have posteriors $\gamma_A=0.9,\ 0.8,\ 0.3$. Find the adapted mean.
> $n_A=2.0$. $E_A(x)=(0.9+1.6+0.9)/2.0=1.7$. $\alpha_A=2/18\approx0.111$. $\hat\mu_A=0.111\cdot1.7+0.889\cdot1.0\approx1.078$. With little data the prior wins.

> [!question]- 5. Impostor scores $\mathcal N(0,1)$, genuine scores $\mathcal N(3,1)$. Find the EER.
> $p_{fa}=\Phi(-\theta)$, $p_{fr}=\Phi(\theta-3)$. Equal when $-\theta=\theta-3$, so $\theta=1.5$. $\mathrm{EER}=\Phi(-1.5)=0.0668\approx6.7\%$.

> [!question]- 6. $p_{imp}=0.1$, $c_{fa}=5$, $c_{fr}=1$, $p_{fa}=2\%$, $p_{fr}=10\%$. Compute the DCF.
> $0.1\cdot5\cdot0.02=0.01$ and $0.9\cdot1\cdot0.10=0.09$. $C=0.10$.

## Links
- Course index: [[CS568 Home]]
- Previous lecture: [[CS568 L15 - Large vocabulary speech recognition]]
- Quizzes and labs that use this lecture: none (the midterm booklet covers Lectures 1 to 6).
- Full step-by-step notes (PDF): [CS568_Lecture_16_Speaker_Recognition.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_16/CS568_Lecture_16_Speaker_Recognition.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_16/CS568_Lecture_16_Speaker_Recognition.pdf`
- Original slides (PDF): [l16.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_16/l16.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_16/l16.pdf`

Slides not embedded: 1
