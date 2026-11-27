---
course: CS 568 Speech Processing
lesson: CS568 L16 - Speaker recognition
type: quiz
questions: 28
---

# CS568 L16 - Speaker recognition - quiz

Lesson note: [[CS568 L16 - Speaker recognition]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. Identification, verification, text dependence (slides 2-6)

### Q1 (slide 2)

A speaker identification system has 50 enrolled speakers. A person who is not enrolled speaks to it. Which statement about the closed-set and the open-set version of the system is correct?

- A) The closed-set system answers "no match", because no model fits well. The open-set system must answer one of the 50 names, because it accepts any voice.
- B) The closed-set system must answer one of the 50 names, so it is certainly wrong here. The open-set system may answer "no match" if its best score is below a threshold.
- C) The closed-set system must answer one of the 50 names, so it is certainly wrong here. The open-set system enrolls the new voice and answers with its new name.
- D) Both systems may answer "no match". The open-set system must also ask the person for a claimed identity, and then it verifies that one claim.

> [!success]- Answer
> **B.** A closed-set system assumes that the speaker is always one of the enrolled speakers, so its answer is always a name. Here that name is wrong.
> An open-set system may answer "no match". For this it needs one more decision: is the best match good enough? It compares the best score with a threshold.
> Speaker identification is "konuşmacı tanılama" in Turkish, speaker verification is "konuşmacı doğrulama".
> - A: This swaps the two definitions. "Closed" means the set of possible answers is closed: only the enrolled names.
> - C: Enrollment is a separate phase that needs training speech. In the test the open-set system can only add the answer "no match".
> - D: A closed-set system can never answer "no match". A claimed identity belongs to verification; in identification nobody claims an identity.

### Q2 (slide 3)

Slide 3 draws speaker identification as one test voice with an arrow to each of four enrolled speakers. It draws speaker verification as one voice with a single arrow to Bob, for the question "Is this Bob's voice?". The slide calls verification a special case of open-set identification. Why?

- A) With only the claimed model in the set, open-set identification decides "Bob" or "not Bob": one model against all other voices.
- B) Verification is closed-set identification with Bob as the only model, so every voice that it is given is labelled as Bob.
- C) Verification also scores all four enrolled models, as open-set identification does, and it accepts if Bob's model wins.
- D) Verification, like open-set identification, gets less accurate as more speakers are enrolled, because each speaker adds an arrow.

> [!success]- Answer
> **A.** Open-set identification picks one of the models in the set or rejects the voice. If the set holds only Bob's model, the two answers are "Bob" and "not Bob".
> That is exactly the verification decision, "one vs. all" on the slide. It makes one comparison, so its performance does not depend on the number of speakers.
> - B: A closed set with one model always answers "Bob" and can never reject an impostor. Verification must be able to reject, which is the "open" part.
> - C: In verification the voice is compared only with the model of the claimed identity: the single arrow on the slide. Scoring all models and picking the best is identification.
> - D: Verification has one arrow whatever the number of enrolled speakers. The slide says its performance is independent of the size of the population.

### Q3 (slide 4)

![[cs568_lecture_16_l16_p004.jpg]]

In the figure the same features go to Bob's speaker model and to an impostor model, and the $\Sigma$ node combines the two log scores before the decision. Suppose the impostor model is removed. Bob's log score $\log p(Y\mid\lambda_{Bob})$ alone is compared with a fixed threshold. What goes wrong?

- A) The raw score also changes with the words, the noise and the channel. A noisy utterance of the real Bob then scores low and is rejected.
- B) Nothing changes. The impostor score is the same for every utterance, so removing it only shifts the threshold by a constant.
- C) The system turns into identification, because Bob's score must now be compared with the scores of all enrolled speakers.
- D) Only impostors are affected. Bob's model always fits Bob's voice well, so his score does not depend on the noise or the channel.

> [!success]- Answer
> **A.** The log score depends on things that have nothing to do with the speaker: what is said, the noise, the channel.
> With the impostor model these things lower both scores, so they mostly cancel in the difference $\log p(Y\mid\lambda_{Bob})-\log p(Y\mid\lambda_{imp})$. One threshold then works for many conditions.
> Without it, a fixed threshold is too strict for bad conditions and too loose for good ones.
> - B: The impostor score is not constant. It falls with the same noise and channel that lower Bob's score, and this is why the difference is useful.
> - C: No other speaker model is scored. The system still makes one comparison with a threshold; it is only a worse verification system.
> - D: Bob's model was trained on his enrollment speech. Noise or another channel in the test lowers his score too, so the real Bob can be rejected.

### Q4 (slide 5)

Slide 5 shows the two phases of a verification system. In the enrollment phase Bob and Sally each get a voiceprint (a speaker model). Later a new utterance arrives with the claim "I am Sally". Which description of the verification phase is correct?

- A) Bob's and Sally's voiceprints are scored. The claim is accepted if Sally's voiceprint gives the better score of the two.
- B) Only Sally's voiceprint is fetched and scored. The score is compared with a threshold, and the claim is accepted or rejected.
- C) Sally's voiceprint is trained again with the new utterance. The claim is accepted if the model changes only a little.
- D) The name "Sally" is looked up in the list of enrolled speakers. If the name is found, the claim is accepted.

> [!success]- Answer
> **B.** The claimed identity only selects which voiceprint is used: the arrow from Sally's model to the decision block. In practice the score is a ratio against a general impostor model (slide 4).
> The same feature extraction as in enrollment runs on the new utterance first, so that the test features match the model.
> - A: Comparing all enrolled models and picking the best is identification. Verification never compares the enrolled speakers with each other.
> - C: Model training belongs to the enrollment phase. The verification phase only scores the stored model and makes a decision.
> - D: The claim is not evidence. It only selects which model is scored; the speech must still match that model.

### Q5 (slide 6)

A telephone-banking login uses text-dependent speaker verification. The designers can use a fixed password, or a random phrase that the system prompts at each login. Why is the random prompt more secure?

- A) A random prompt makes the system text-independent, and a text-independent system is more accurate than a text-dependent one.
- B) A random phrase is longer than a password, so the average over its frames removes the channel better.
- C) A fixed password can be recorded once and played back. A recording of an old login does not contain a newly prompted phrase.
- D) With a random prompt the system needs no speaker model: it only checks that the right words were said.

> [!success]- Answer
> **C.** An impostor who recorded the real user saying the fixed password can replay it, and it matches the model.
> A random prompt changes at every login. The system knows the prompted text, so it stays text-dependent, and an old recording does not contain the requested words.
> - A: The system knows the text it prompted, so it is still text-dependent. Also, text-independent recognition is harder, not more accurate.
> - B: This mixes in cepstral mean subtraction. A random phrase need not be longer, and the security problem here is replay, not the channel.
> - D: Anybody can read a prompt aloud. A word check alone says nothing about who speaks; the speaker model is still needed.

## 2. Speaker features (slides 7-9)

### Q6 (slide 7)

Slide 7 names two low-level speaker features: a segmental one (the spectrum of one short frame) and a supra-segmental one (pitch). Which statement about how each one is measured in practice is correct?

- A) Segmental: formant frequencies, tracked directly in each frame. Supra-segmental: pitch periodicity, after a voiced/unvoiced detector.
- B) Segmental: short-term spectral measures (LPC, filter bank), as formants are hard to track. Supra-segmental: pitch periodicity, in every frame.
- C) Segmental: formant frequencies, tracked directly in each frame. Supra-segmental: pitch periodicity, measured in every frame.
- D) Segmental: short-term spectral measures (LPC, filter bank), as formants are hard to track. Supra-segmental: pitch periodicity, after a voiced/unvoiced detector.

> [!success]- Answer
> **D.** Formants would describe the spectral shape, but they are hard to track reliably. So LPC or filter-bank measurements of the short-term spectrum are used.
> Pitch exists only in voiced frames, where the vocal folds vibrate. So a voiced/unvoiced detector must run before the pitch period is measured.
> - A: The pitch part is right. The slide says formants are hard to track reliably, which is why spectral measurements are used instead.
> - B: The segmental part is right. Unvoiced frames (for example /s/) have no periodicity, so they have no pitch to measure.
> - C: Both parts are wrong: formants are hard to track, and only voiced frames have a pitch.

### Q7 (slide 8)

Slide 8 lists four groups of low-level features: short-time spectra (MFCCs), cepstral mean subtraction (CMS), dynamic features ($\Delta$, $\Delta^2$) and pitch and energy averages. For which group does the slide name a weakness, and what is it?

- A) MFCCs: they are designed for speech recognition, which should ignore the speaker, so they carry little speaker information.
- B) Dynamic features: $\Delta$ and $\Delta^2$ describe how the words change, so they help speech recognition but not speaker recognition.
- C) Pitch and energy averages: robust pitch extraction is hard, and the pitch of one speaker varies a lot between recordings.
- D) CMS: it works only when the channel changes quickly, and a telephone channel changes slowly during a call.

> [!success]- Answer
> **C.** The slide: "robust pitch extraction is hard and pitch has large intra-speaker variation".
> A feature that changes a lot within one speaker separates speakers less well than one may expect.
> - A: The slide calls this expectation counterintuitive and then rejects it: the best speech representation is also a good speaker representation.
> - B: The slide says the derivatives are useful both for speech and for speaker recognition.
> - D: This reverses the condition. CMS removes convolutional distortion in slowly varying channels, as the slide says.

### Q8 (slide 8, CMS)

Cepstral mean subtraction (CMS) subtracts the average cepstral vector of the recording from every frame. A fixed channel adds the same vector $\mathbf c_h$ to the cepstrum of every frame. The slide says CMS needs a sufficiently long recording and a slowly varying channel. What goes wrong if (a) the recording is one short word, (b) the channel changes in the middle of the recording?

- A) (a) The mean is mostly the mean of the few sounds said, so CMS removes speech and speaker information. (b) One subtracted vector cannot cancel two channel offsets.
- B) (a) Nothing: the channel is constant over the word, so the mean equals $\mathbf c_h$ exactly. (b) One subtracted vector cannot cancel two channel offsets.
- C) (a) The mean is mostly the mean of the few sounds said, so CMS removes speech and speaker information. (b) Nothing: the two offsets average out.
- D) (a) Nothing: the channel is constant over the word, so the mean equals $\mathbf c_h$ exactly. (b) Nothing: the two offsets average out.

> [!success]- Answer
> **A.** (a) The mean is $\mathbf c_h$ plus the mean of the clean cepstra. Only over long, varied speech is this second mean a stable long-term value. For one word it depends on the word.
> (b) The first part has offset $\mathbf c_{h1}$, the second $\mathbf c_{h2}$. CMS subtracts one average of the two, so in each part a residual offset of about half the difference stays.
> - B: Part (b) is right. In (a) the mean also contains the mean of the clean cepstra of the few sounds, not only $\mathbf c_h$.
> - C: Part (a) is right. In (b) the average of the two offsets is subtracted from both parts, so each part keeps a residual offset.
> - D: Both parts assume that CMS always cancels the channel. Its two conditions on the slide exist because it does not.

### Q9 (slide 9)

Slide 9 lists linguistic (high-level) speaker features: word usage (frequent words, fillers) and phone sequences (pronunciation habits). They are useful for indexing broadcast news. Why are they of no use in a fixed-password verification system?

- A) They are segmental features of single frames, and a short password has too few voiced frames to measure them.
- B) They need a known text to align with. Broadcast news has transcripts, but the text of a password is not known to the system.
- C) They are statistics that need many words, so long recordings. A password is a few seconds long, and its words are fixed by the system.
- D) They need cooperative speakers who read a fixed script, as news readers do. Password users are not cooperative.

> [!success]- Answer
> **C.** Word frequencies and pronunciation habits become stable only after many words. A news broadcast gives minutes of speech per speaker.
> A password gives a few seconds, and the speaker has no choice of words that could show his habits.
> - A: Linguistic features are high-level: they extend over words and sentences, not single frames. The voiced-frame problem belongs to pitch.
> - B: A fixed-password system knows its text: it is text-dependent. And these features come from a recogniser's output, not from alignment with a given text.
> - D: Password users are cooperative (slides 3 and 6). Word usage shows a speaker's own choice of words, so a fixed text removes it.

## 3. Non-parametric models (slides 10-12)

### Q10 (slide 10)

Slide 10 divides speaker models into non-parametric models (few structural assumptions, they keep the enrollment data) and parametric models (they assume a structure and keep only its parameters). Where do nearest neighbour and vector quantisation (VQ) belong?

- A) Nearest neighbour: parametric, its likelihood $p_{nn}$ is given by a formula. VQ: non-parametric, its centroids are vectors like the data.
- B) Nearest neighbour: non-parametric, it keeps all enrollment vectors. VQ: non-parametric, its centroids are vectors like the data.
- C) Nearest neighbour: parametric, its likelihood $p_{nn}$ is given by a formula. VQ: parametric, it keeps only a codebook of centroids.
- D) Nearest neighbour: non-parametric, it keeps all enrollment vectors. VQ: parametric, it keeps only a codebook of centroids.

> [!success]- Answer
> **D.** Nearest neighbour searches the stored enrollment vectors at every test, so it keeps the data: non-parametric, like DTW templates.
> VQ summarises the data by a few centroids and throws the data away: parametric, like GMM and HMM.
> - A: This swaps the two classes. A formula for the score does not make a model parametric: $p_{nn}$ is computed from the stored data. Centroids are averages, not the data.
> - B: The nearest-neighbour part is right. A centroid is an average of many vectors, a parameter of the codebook; the data itself is not kept.
> - C: The VQ part is right. $p_{nn}$ needs the distance to the nearest stored enrollment vector, so all vectors must be kept.

### Q11 (slide 11, template matching)

Template matching aligns a test utterance with a stored enrollment utterance by dynamic time warping (DTW) and accepts if the aligned distance is below a threshold. Why does it suit only fixed-password systems?

- A) DTW needs the test and the template to have exactly the same number of frames, and only a fixed password gives that.
- B) The template is a frame sequence of one specific text. A test with other words has no matching frames, so the distance means nothing.
- C) DTW compares the average spectra of the two utterances, and these averages are only stable when the words are the same ones.
- D) A fixed password is known to the system in advance, so an impostor cannot replay an old recording of it to the system.

> [!success]- Answer
> **B.** DTW aligns the frames of two repetitions of the same text, which differ in length and timing, so that the same sounds are compared.
> If the test text is different, there is no correct alignment, and a small or large distance says nothing about the speaker.
> - A: DTW exists to align utterances of different lengths. Two repetitions of one password rarely have the same number of frames.
> - C: DTW compares frame with frame along an alignment path. An average spectrum would need no time alignment at all.
> - D: It is the opposite: a fixed password can be recorded and replayed (slide 6). Security is also not why templates need a fixed text.

### Q12 (slide 11, nearest neighbour)

The nearest-neighbour likelihood is $p_{nn}(\mathbf y;X)=1/V[d_{nn}(\mathbf y,X)]$. Here $d_{nn}$ is the distance from the test vector $\mathbf y$ to the nearest enrollment vector in $X$, and $V[r]\propto r^D$ is the volume of a $D$-dimensional sphere of radius $r$. In a feature space with $D=12$ the nearest-neighbour distance of a test vector doubles. What happens to $p_{nn}$?

- A) It grows to $4096$ times its old value.
- B) It falls to $1/24$ of its old value.
- C) It falls to $1/4096$ of its old value.
- D) It falls to $1/2$ of its old value.

> [!success]- Answer
> **C.** Doubling the radius multiplies the volume by $2^{D}=2^{12}=4096$.
> $p_{nn}$ is $1/V$, so it is divided by 4096. In a high-dimensional space a small change of distance is a large change of likelihood.
> - A: This takes $p_{nn}$ proportional to $V$ instead of $1/V$. A far neighbour must give a lower likelihood.
> - B: This computes $2\cdot D=24$ instead of $2^{D}$.
> - D: This drops the exponent: it uses $p_{nn}\propto 1/d_{nn}$ instead of $1/d_{nn}^{D}$.

### Q13 (slide 12)

The nearest-neighbour score is $s_{nn}(Y;X)=-\sum_{j}\ln d_{nn}(\mathbf y_j,X)$, where $d_{nn}(\mathbf y_j,X)$ is the distance from test vector $\mathbf y_j$ to its nearest enrollment vector of one speaker (natural log). A test utterance has two vectors. Their nearest-neighbour distances are $0.5$ and $1$ to speaker A's data, and $2$ and $2$ to speaker B's data. Which line gives both scores and the identified speaker?

- A) $s_{nn}(Y;A)=-0.693$, $s_{nn}(Y;B)=1.386$, speaker B
- B) $s_{nn}(Y;A)=0.693$, $s_{nn}(Y;B)=-1.386$, speaker A
- C) $s_{nn}(Y;A)=0.693$, $s_{nn}(Y;B)=-1.386$, speaker B
- D) $s_{nn}(Y;A)=-0.693$, $s_{nn}(Y;B)=1.386$, speaker A

> [!success]- Answer
> **B.** $s_{nn}(Y;A)=-(\ln0.5+\ln1)=-(-0.693+0)=0.693$.
> $s_{nn}(Y;B)=-(\ln2+\ln2)=-1.386$.
> $s_{nn}$ is the log of a likelihood, a similarity, so the largest wins: $0.693>-1.386$, speaker A.
> - A: This drops the minus sign in front of the sum and then takes the largest value.
> - C: The scores are right, but this takes the smallest score, as if $s_{nn}$ were a distance.
> - D: This drops the minus sign, so it computes $\sum\ln d_{nn}$, a log distance, and then correctly picks the smallest distance. The decision is right, the scores are not $s_{nn}$.

## 4. Parametric models: VQ, GMM, HMM (slides 13-15)

### Q14 (slide 13)

A VQ codebook is trained with the LBG algorithm. To test a claim, each test vector $\mathbf x_i$ is assigned to its nearest centroid $\boldsymbol\mu_j$ of the claimed speaker's codebook (built at enrollment). The distortion is $D=\sum_{j}\sum_{\mathbf x_i\to\boldsymbol\mu_j}\lVert\mathbf x_i-\boldsymbol\mu_j\rVert^2$. Which statement is correct?

- A) LBG starts with one centroid and splits clusters until the codebook is large enough. The claim is accepted if $D$ is below a threshold.
- B) LBG needs the codebook size $k$ before it starts, as k-means does. The claim is accepted if $D$ is below a threshold.
- C) LBG starts with one centroid and splits clusters until the codebook is large enough. The claim is accepted if $D$ is above a threshold.
- D) LBG needs the codebook size $k$ before it starts, as k-means does. The claim is accepted if $D$ is above a threshold.

> [!success]- Answer
> **A.** LBG is used because the codebook size $k$ is unknown. It starts with $k=1$, splits the cluster with the largest variance into $\boldsymbol\mu\pm\boldsymbol\epsilon$, re-runs k-means and repeats.
> $D$ is a sum-squared error. Test vectors close to the claimed speaker's centroids give a small $D$, so a good match is a small $D$.
> - B: The decision part is right. LBG grows the codebook by splitting, starting from $k=1$, so $k$ need not be known.
> - C: The LBG part is right. $D$ is an error, so a large $D$ means a bad match.
> - D: Both parts are wrong: LBG finds $k$ by splitting, and a large error is a bad match.

### Q15 (slide 14)

A GMM score is the average log-likelihood per frame, $s(Y\mid\lambda)=\frac1T\sum_{t=1}^{T}\log p(\mathbf y_t\mid\lambda)$. A test utterance gives $s(Y\mid\lambda)=-41.2$ under the claimed speaker's model and $s(Y\mid\lambda_{BG})=-42.0$ under the background model. The system computes $\Lambda=s(Y\mid\lambda)-s(Y\mid\lambda_{BG})$ and accepts if $\Lambda\ge\theta$, with $\theta$ given on the same per-frame log scale. Which line is correct?

- A) $\Lambda=-0.8$; reject for $\theta=0.5$, reject for $\theta=1.0$
- B) $\Lambda=-83.2$; reject for $\theta=0.5$, reject for $\theta=1.0$
- C) $\Lambda=0.8$; accept for $\theta=0.5$, reject for $\theta=1.0$
- D) $\Lambda=2.23$; accept for $\theta=0.5$, accept for $\theta=1.0$

> [!success]- Answer
> **C.** $\Lambda=-41.2-(-42.0)=0.8$ per frame.
> $0.8\ge0.5$: accept. $0.8<1.0$: reject. A higher threshold is stricter.
> - A: This subtracts the wrong way, background minus speaker.
> - B: This loses the minus sign of $-42.0$: it computes $-41.2-42.0$.
> - D: This takes $e^{0.8}=2.23$, the ratio itself, and compares it with a threshold that is on the log scale.

### Q16 (slide 14, GMM)

A GMM $p(\mathbf y\mid\lambda)=\sum_{m}w_m\,\mathcal N(\mathbf y;\boldsymbol\mu_m,\Sigma_m)$ has means, covariance matrices and weights, learned with the EM algorithm. Slide 14 calls it a generalisation of k-means. In which sense?

- A) Each cluster gets its own covariance matrix and weight, and each vector belongs softly to all clusters.
- B) Each vector is assigned to its nearest mean, as in k-means, but many more clusters are used.
- C) It also models the order of the frames, which k-means ignores, so it suits passwords.
- D) Its centres are found by LBG splitting from one cluster, instead of from random starting centres.

> [!success]- Answer
> **A.** k-means describes a cluster only by its centre and puts each vector in exactly one cluster.
> A GMM adds a shape and size (covariance) and a weight per cluster, and EM gives each vector a posterior for every component.
> - B: A hard assignment to the nearest mean is k-means itself. The number of clusters is not what makes a GMM more general.
> - C: The slide says a GMM does not model the temporal aspects of speech. Each frame is scored on its own.
> - D: LBG builds a VQ codebook (slide 13). It is not what distinguishes a GMM from k-means.

### Q17 (slide 15)

For text-independent recognition slide 15 considers an ergodic HMM, in which every state can follow every other state. Which statement about it is correct?

- A) Each state models one word of the password, and the transitions fix the word order. This makes it the usual text-independent model.
- B) Its emissions model the spectra of different sound classes. A GMM is still chosen, as it models the order of the frames better.
- C) It cannot be trained with Baum-Welch, as its states have no fixed order to follow. This is why a GMM is chosen instead.
- D) Its emissions model the spectra of sound classes, its transitions which class follows which. The transitions add little, so a GMM does as well.

> [!success]- Answer
> **D.** Emissions: the different spectral characteristics, associated with different phones. Transitions: some temporal information.
> Experiments show that adding the transition probabilities has little effect. A GMM is the same idea without transitions, so it is simpler and about as good.
> - A: Fixed states in a fixed order describe a left-right HMM for a known text. Text-independent speech has no fixed words.
> - B: A GMM ignores the order of the frames completely. It is chosen because the order adds little, not because it models it.
> - C: Baum-Welch trains any HMM, ergodic or left-right. The reason for the GMM is the small gain from the transitions.

## 5. Adapting from a UBM (slides 16-17)

### Q18 (slide 16)

Slide 16 names two mismatches between enrollment and test: conditions (handset, network, noise) and linguistic content (the test contains sounds that enrollment did not). Why is it a bad idea to train a speaker's GMM from his enrollment data alone?

- A) The enrollment data has too many frames, so EM would take too long. A UBM is trained only once and saves this time.
- B) The enrollment data is too little for a model with many parameters, and it says nothing about sounds or conditions it did not contain.
- C) A model trained on one speaker is too general: it has seen no other voices, so it would also fit the impostors well.
- D) EM cannot start on one speaker's data alone, because its starting means must come from the data of several speakers.

> [!success]- Answer
> **B.** A GMM has many means, covariances and weights. Trained on a few seconds or minutes of speech it is unreliable.
> Components for sounds or conditions that never occurred in enrollment get no useful values. Adaptation from a UBM fills these in.
> - A: This reverses the problem: enrollment data is scarce. Training the UBM on many speakers costs far more than one speaker model.
> - C: This is the wrong direction. A model fitted to little data is too specific (it overfits). Impostors are handled by the background model.
> - D: EM can start from the speaker's own data, for example from k-means centres. The problem is the amount of data, not the start.

### Q19 (slide 17)

![[cs568_lecture_16_l16_p017.jpg]]

Panel b of the figure shows a speaker model adapted from the UBM in panel a. The means are adapted by MAP: $\hat\mu_m=\alpha_mE_m(x)+(1-\alpha_m)\,\mu_m^{UBM}$ with $\alpha_m=n_m/(n_m+r)$. Here $n_m=\sum_t\gamma_m(t)$ is the soft count of speaker frames for component $m$ ($\gamma_m(t)$ is the UBM posterior of component $m$ for frame $t$), $E_m(x)$ is the posterior-weighted mean of these frames and $r$ is the relevance factor. A 1-D component has $\mu_m^{UBM}=2$, $E_m(x)=5$, $n_m=4$ and $r=16$. What is $\hat\mu_m$?

- A) $4.4$
- B) $3.5$
- C) $1.0$
- D) $2.6$

> [!success]- Answer
> **D.** $\alpha_m=4/(4+16)=0.2$.
> $\hat\mu_m=0.2\cdot5+0.8\cdot2=1.0+1.6=2.6$.
> With little data the mean stays near the UBM value 2. With $n_m=64$ it would be $0.8\cdot5+0.2\cdot2=4.4$.
> - A: This uses $\alpha_m=r/(n_m+r)=0.8$ instead of $n_m/(n_m+r)$: $0.8\cdot5+0.2\cdot2=4.4$.
> - B: This ignores $n_m$ and $r$ and takes the plain average of the data mean and the UBM mean, $(5+2)/2$.
> - C: This keeps only $\alpha_mE_m(x)=0.2\cdot5$ and drops the term $(1-\alpha_m)\mu_m^{UBM}$.

## 6. Decision rules, threshold, normalisation (slides 18-19)

### Q20 (slide 18)

Three enrolled speakers give the average log-likelihood scores $s(Y\mid\lambda_A)=-38.5$, $s(Y\mid\lambda_B)=-36.9$ and $s(Y\mid\lambda_C)=-37.4$ for one test utterance. A higher score is a better fit. A verification decision accepts if the claimed speaker's score is at or above the threshold $\theta$. Which statement is correct?

- A) Open-set identification with $\theta=-36.0$ answers B, as B has the highest score.
- B) Verification of the claim "I am C" with $\theta=-37.5$ rejects it, as B scores higher.
- C) Closed-set identification with $\theta=-36.0$ answers "no match", as no score reaches it.
- D) Verification of the claim "I am C" with $\theta=-37.5$ accepts it, as C's score reaches $\theta$.

> [!success]- Answer
> **D.** Verification uses only the claimed speaker's score: $-37.4\ge-37.5$, so the claim is accepted.
> B is never scored in verification, so its higher score does not matter.
> - A: Open-set identification first picks B and then verifies the winner: $-36.9<-36.0$, so the answer is "no match".
> - B: This compares C with the other speakers, which is identification. Verification compares C's score only with $\theta$.
> - C: Closed-set identification uses no threshold and always answers a name: the maximum, $-36.9$, so B.

### Q21 (slide 19, threshold)

The Bayes threshold on the likelihood ratio $p(Y\mid\lambda)/p(Y\mid\lambda_{BG})$ is $\theta^*=\dfrac{c_{fa}}{c_{fr}}\cdot\dfrac{p_{imp}}{1-p_{imp}}$. Here $c_{fa}$ is the cost of a false acceptance, $c_{fr}$ the cost of a false rejection and $p_{imp}$ the prior probability of an impostor. Take $c_{fa}=10$, $c_{fr}=1$, $p_{imp}=0.05$. The system computes the log likelihood ratio of the whole utterance, $\ln p(Y\mid\lambda)-\ln p(Y\mid\lambda_{BG})$ (natural log, summed over the frames, not averaged), and accepts if it is at or above $\eta$. Which $\eta$ is the Bayes rule?

- A) $\eta=-0.642$
- B) $\eta=0.526$
- C) $\eta=-5.247$
- D) $\eta=5.247$

> [!success]- Answer
> **A.** $\theta^*=\dfrac{10}{1}\cdot\dfrac{0.05}{0.95}=0.526$.
> The test "ratio $\ge\theta^*$" is the same as "log ratio $\ge\ln\theta^*$", so $\eta=\ln0.526=-0.642$.
> Impostors are rare, so a claim is accepted even when the background model fits a little better.
> - B: This is $\theta^*$ itself, a threshold for the ratio, used on the log scale without taking its log.
> - C: This swaps the costs: $\ln\big(\frac{1}{10}\cdot\frac{0.05}{0.95}\big)=-5.247$.
> - D: This inverts the prior odds: $\ln\big(10\cdot\frac{0.95}{0.05}\big)=\ln190=5.247$.

### Q22 (slide 19, normalisation)

![[cs568_lecture_16_l16_p019.jpg]]

In the figure the $\Sigma$ node gives the log likelihood ratio $\Lambda$ (speaker-model score minus background-model score), and score normalisation turns it into $\Lambda_{norm}$. Z-norm uses $\Lambda_{norm}=(\Lambda-\mu)/\sigma$, where $\mu$ and $\sigma$ are the mean and the standard deviation of the scores of this target model against impostor utterances. For one target model these impostor scores have mean $0.6$ and variance $1.44$. A test gives $\Lambda=2.4$. What is $\Lambda_{norm}$?

- A) $1.25$
- B) $1.5$
- C) $2.0$
- D) $2.5$

> [!success]- Answer
> **B.** $\sigma=\sqrt{1.44}=1.2$.
> $\Lambda_{norm}=(2.4-0.6)/1.2=1.8/1.2=1.5$: the score lies 1.5 standard deviations above the typical impostor score of this model.
> - A: This divides by the variance instead of the standard deviation: $1.8/1.44=1.25$.
> - C: This forgets to subtract the mean: $2.4/1.2=2.0$.
> - D: This adds the mean instead of subtracting it: $(2.4+0.6)/1.2=2.5$.

## 7. Errors, ROC, DET, EER, DCF (slides 20-22)

### Q23 (slide 20)

A verification test has 400 impostor trials and 250 genuine trials. The system accepts 12 impostors and rejects 20 genuine speakers. What are the false acceptance rate $p_{fa}$ and the false rejection rate $p_{fr}$?

- A) $p_{fa}=8\%$, $p_{fr}=3\%$
- B) $p_{fa}=4.8\%$, $p_{fr}=5\%$
- C) $p_{fa}=1.85\%$, $p_{fr}=3.08\%$
- D) $p_{fa}=3\%$, $p_{fr}=8\%$

> [!success]- Answer
> **D.** $p_{fa}$ is counted on the impostor trials only: $12/400=0.03$.
> $p_{fr}$ is counted on the genuine trials only: $20/250=0.08$.
> A higher threshold accepts fewer trials of both kinds, so $p_{fa}$ falls and $p_{fr}$ rises.
> - A: This swaps the two rates: $20/250$ is the false rejection rate, $12/400$ the false acceptance rate.
> - B: This divides by the wrong group: $12/250$ and $20/400$. False acceptances can only happen on impostor trials.
> - C: This divides both counts by all 650 trials: $12/650$ and $20/650$.

### Q24 (slide 20, DET)

A DET curve plots the false rejection rate against the false acceptance rate, with both axes converted to normal deviates $z=\Phi^{-1}(p)$, where $\Phi$ is the standard normal distribution function. Under which condition is the DET curve a straight line with slope $-1$?

- A) The genuine and the impostor scores are both Gaussian with the same standard deviation.
- B) The genuine and the impostor scores are both Gaussian, the genuine ones with a larger standard deviation.
- C) Both error rates fall exponentially as the threshold moves, so they are straight on logarithmic axes.
- D) The genuine and the impostor scores do not overlap at all, so one threshold makes no errors.

> [!success]- Answer
> **A.** Impostor scores $\mathcal N(0,1)$, genuine scores $\mathcal N(d',1)$: then $\Phi^{-1}(p_{fr})=-\Phi^{-1}(p_{fa})-d'$, a line with slope $-1$.
> The normal-deviate axes undo the Gaussian shape. The slide says "plotted in log scale", but the axes are probit scaled, not logarithmic.
> - B: Then the DET is still straight, but its slope is $-\sigma_{imp}/\sigma_{gen}$, not $-1$.
> - C: The DET axes are normal deviates, not logarithms. This follows the slide's wording "log scale", which is not accurate.
> - D: Then both errors can be 0 at once, and $\Phi^{-1}(0)=-\infty$: the curve leaves the plot. Separation is not the condition for the slope.

### Q25 (slide 21, ROC)

![[cs568_lecture_16_l16_p021.jpg]]

In the top-left picture of the figure, the impostor scores (blue) lie to the left of the genuine scores (red), and a vertical threshold line separates them: scores to the right of the line are accepted. The top-right picture shows the ROC point $(P(FP),P(TP))$ for this threshold. The threshold line moves to the right. Where does the ROC point move?

- A) Up and to the right: a higher threshold makes the test stricter, so $P(TP)$ and $P(FP)$ both rise.
- B) Up and to the left: fewer impostors are accepted, so $P(FP)$ falls while $P(TP)$ rises.
- C) Down and to the left: fewer trials of both kinds are accepted, so $P(TP)$ and $P(FP)$ both fall.
- D) Down and to the right: fewer genuine trials are accepted, so $P(TP)$ falls while $P(FP)$ rises.

> [!success]- Answer
> **C.** Less of each distribution lies to the right of the line. TP and FP shrink, FN and TN grow.
> So the point moves along the curve towards the corner $(0\%,0\%)$.
> - A: A stricter test accepts fewer trials, so both rates fall. This gets the direction wrong.
> - B: Moving the threshold cannot improve both rates. Fewer genuine trials are accepted too, and the point stays on the curve.
> - D: The impostor area to the right of the line shrinks too, so $P(FP)$ falls.

### Q26 (slide 21, DET)

A DET plot (miss probability against false-alarm probability, both in percent; lower is better) compares four systems. At false alarms of about 0.5%, system P (feature warping + T-norm) has the lowest miss probability. At about 30%, system Q (feature warping alone) has the lowest. So the curves cross. What follows?

- A) System P is better at every threshold, because a system that is best at low false-alarm rates is best everywhere.
- B) No system is best at every threshold. The choice depends on the operating point, that is on which errors matter.
- C) Choose the system with the lowest EER, because the EER sums up the whole curve over all operating points.
- D) The plot must be wrong, because DET curves are straight lines with slope $-1$ and so they cannot cross.

> [!success]- Answer
> **B.** Lower means fewer misses at the same false-alarm rate. P wins at a low false-alarm rate, Q at a high one.
> So the ranking depends on the operating point. The costs of the two errors (slide 22) decide which point matters.
> - A: At about 30% false alarms Q has fewer misses than P. Crossing curves mean the order changes.
> - C: The EER is one point, where $p_{fa}=p_{fr}$. It says nothing about the other operating points.
> - D: Slope $-1$ holds only for Gaussian scores with equal spreads. Real curves are only roughly straight, with different slopes, so they can cross.

### Q27 (slide 22)

The detection cost is $C=p_{imp}\,c_{fa}\,p_{fa}+(1-p_{imp})\,c_{fr}\,p_{fr}$. Here $p_{imp}$ is the prior probability of an impostor, $c_{fa}$ and $c_{fr}$ are the costs of a false acceptance and a false rejection, and $p_{fa}$, $p_{fr}$ are the two error rates. Take $p_{imp}=0.02$, $c_{fa}=10$, $c_{fr}=1$. Threshold A gives $p_{fa}=1\%$, $p_{fr}=12\%$. Threshold B gives $p_{fa}=5\%$, $p_{fr}=4\%$. Which threshold has the lower cost, and what is that cost?

- A) A, with $C=0.0338$
- B) A, with $C=0.22$
- C) B, with $C=0.393$
- D) B, with $C=0.0492$

> [!success]- Answer
> **D.** A: $C=0.02\cdot10\cdot0.01+0.98\cdot1\cdot0.12=0.002+0.1176=0.1196$.
> B: $C=0.02\cdot10\cdot0.05+0.98\cdot1\cdot0.04=0.010+0.0392=0.0492$.
> Impostors are rare, so the many false rejections of A cost more than its few false acceptances save.
> - A: This swaps $p_{fa}$ and $p_{fr}$: A gives $0.02\cdot10\cdot0.12+0.98\cdot0.01=0.0338$, B gives $0.057$.
> - B: This drops the prior weights $p_{imp}$ and $1-p_{imp}$: A gives $10\cdot0.01+0.12=0.22$, B gives $0.54$.
> - C: This swaps the costs: B gives $0.02\cdot1\cdot0.05+0.98\cdot10\cdot0.04=0.393$, A gives $1.176$.

## 8. Applications (slide 23)

### Q28 (slide 23)

Slide 23 lists applications of speaker recognition. Classify them with slides 2-6: identification or verification, text-dependent or text-independent. Which statement is correct?

- A) Telephone credit card purchase: identification, text-independent, as the system must find the caller among all card holders.
- B) Speaker diarisation of a meeting: verification, text-dependent, as each speaker claims a turn and the agenda fixes the words.
- C) Home parole verification: verification, text-dependent with a random prompt, as one named person is checked and he is cooperative.
- D) Home parole verification: identification, text-independent, as slide 23 lists it under monitoring of a population.

> [!success]- Answer
> **C.** The system checks that the person who answers the call is the one named person: one claim, so verification.
> The person is cooperative and can be asked to say a prompted phrase, and a random prompt protects against a recording. The slide only lists the application; this is reasoning from slides 2-6.
> - A: The caller claims to be the card holder, and this one claim is tested: verification. The caller is cooperative, so a prompted phrase fits.
> - B: Nobody claims an identity; the question is who speaks when, closest to identification. Free conversation is text-independent.
> - D: The slide lists it under Monitoring, but the system checks one named person, so it is verification.
