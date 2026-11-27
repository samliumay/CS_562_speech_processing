---
course: CS 568 Speech Processing
lesson: CS568 L01 - Introduction
type: quiz
questions: 9
---

# CS568 L01 - Introduction - quiz

Lesson note: [[CS568 L01 - Introduction]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. What speech processing is, and the overview figure (slides 1-2)

### Q1 (slide 1)

Two different people say the same sentence. Speech recognition finds what was said. Speaker recognition finds who said it. What role do the differences between the two voices (pitch, voice quality, accent) play in each task?

- A) Recognition: the voice differences help it find the words. Speaker recognition: they are the information it needs, and the words are what it ignores.
- B) Recognition: the voice differences are a disturbance it must ignore. Speaker recognition: they are the information it needs, and the words are what it ignores.
- C) Recognition: the voice differences help it find the words. Speaker recognition: only the pitch is information, and voice quality and accent are ignored.
- D) Recognition: the voice differences are a disturbance it must ignore. Speaker recognition: only the pitch is information, and voice quality and accent are ignored.

> [!success]- Answer
> **B.** The words are the same in both signals, so every voice difference is useless for finding the words: recognition must ignore it.
> For speaker recognition the voice differences are exactly the identity. The words are the part it ignores. The two tasks keep opposite parts of the same signal.
> - A: The words are the same, so the voice differences carry no word information. They only disturb recognition.
> - C: Two slips. The words are the same, so the voice differences carry no word information. And the identity is not only in the pitch: voice quality and accent also tell the two people apart.
> - D: Recognition is right, but pitch alone is not the identity. Voice quality and accent also tell the two people apart, so speaker recognition uses them too.

### Q2 (slide 2, left panel)

![[cs568_lecture_01_l1_introduction_p002.jpg]]

In the left panel, $P(s)=U(s)\,T(s)\,R(s)$. $U$ is the spectrum of the source (airflow pulses from the vocal folds), $T$ is the vocal-tract transfer function, $R$ is the lip radiation, $P$ is the spectrum of the sound that leaves the mouth. $U$ and $P$ are drawn as many vertical lines, $T$ and $R$ as smooth curves. $U$ only falls with frequency, but $P$ has peaks. Which explanation is correct?

- A) $U$ is lines because a computer finds a spectrum only at a few sampled frequencies and draws one line at each. $P$ has peaks because each line of $U$ is scaled by the gain of $T$, which peaks at the formants.
- B) $U$ is lines because the source is periodic, so its energy sits only at multiples of its repetition rate. $P$ has peaks because the vocal tract adds new lines of its own at the formant frequencies.
- C) $U$ is lines because a computer finds a spectrum only at a few sampled frequencies and draws one line at each. $P$ has peaks because the vocal tract adds new lines of its own at the formant frequencies.
- D) $U$ is lines because the source is periodic, so its energy sits only at multiples of its repetition rate. $P$ has peaks because each line of $U$ is scaled by the gain of $T$, which peaks at the formants.

> [!success]- Answer
> **D.** The vocal folds open and close at a regular rate. A periodic signal has energy only at multiples of its repetition rate, so $U$ is a set of lines. $T$ and $R$ are filters: a filter has a gain at every frequency, so they are smooth curves.
> $P=U\,T\,R$ multiplies each source line by the filter gain at its frequency. The lines stay where they are, and their heights now follow the peaks of $T$ (the formants).
> - A: If sampling made the lines, $T$ and $R$ would be drawn as lines too. The lines come from the periodic source.
> - B: A filter only scales what the source gives. It cannot create energy at a new frequency, so the lines of $P$ are the lines of $U$.
> - C: Two slips: the wrong reason for the lines (sampling) and the wrong reason for the peaks (new lines from the filter).

### Q3 (slide 2, middle panel)

![[cs568_lecture_01_l1_introduction_p002.jpg]]

The middle panel is a left-to-right HMM with states 1 to 6. States 1 and 6 are entry and exit and take no frame. States 2 to 5 each take one frame per visit. From a state the model can take a self-loop (stay one more frame), go to the next state, or skip one state. A segment of 6 frames enters at state 2 and leaves from state 5 without any skip. How many self-loops are used, and how does the model fit slow and fast speech?

- A) 3 self-loops. Slow speech uses the self-loops more, and fast speech uses the forward arcs and the skips more.
- B) 2 self-loops. Slow speech uses the forward arcs and the skips more, and fast speech uses the self-loops more.
- C) 3 self-loops. Slow speech uses the forward arcs and the skips more, and fast speech uses the self-loops more.
- D) 2 self-loops. Slow speech uses the self-loops more, and fast speech uses the forward arcs and the skips more.

> [!success]- Answer
> **D.** 6 frames need $6-1=5$ moves between frames. From state 2 to state 5 without skips needs 3 forward moves. The other $5-3=2$ moves are self-loops.
> Slow speech has more frames per sound, so the model stays longer in each state: more self-loops. Fast speech has fewer frames, so it moves forward or skips more often.
> - A: $6-3=3$ subtracts the forward moves from the frames, not from the moves. 6 frames give only 5 moves.
> - B: The count is right, but the direction is swapped. More frames per sound means more self-loops, not more skips.
> - C: Two slips: the count $6-3$ and the swapped direction.

## 2. Related disciplines (slide 3)

### Q4 (slide 3)

Slide 3 lists seven related disciplines: digital signal processing, natural language processing, machine learning, phonetics, human-computer interaction, perceptual psychology, pattern recognition. Match each job to one of them: (a) design a filter that removes a 50 Hz hum, (b) explain why a step of 100 Hz sounds smaller at high frequencies than at low ones, (c) describe how the tongue and lips form the vowel [i], (d) decide which of ten stored word models fits a measured feature sequence best.

- A) (a) signal processing, (b) phonetics, (c) perceptual psychology, (d) natural language processing
- B) (a) signal processing, (b) phonetics, (c) perceptual psychology, (d) pattern recognition
- C) (a) signal processing, (b) perceptual psychology, (c) phonetics, (d) pattern recognition
- D) (a) signal processing, (b) perceptual psychology, (c) phonetics, (d) natural language processing

> [!success]- Answer
> **C.** (a) A filter works on the waveform and its spectrum: signal processing. (b) "Sounds smaller" is about what the listener hears: perceptual psychology. (c) How sounds are made by the tongue and lips: phonetics. (d) Putting a measurement into one of several classes: pattern recognition.
> - A: Two slips: (b) and (c) are swapped, and (d) is not language processing. Natural language processing works with text and word sequences, not with acoustic feature vectors.
> - B: (d) is right, but (b) and (c) are swapped. Phonetics describes the speaker, perceptual psychology the listener.
> - D: (b) and (c) are right, but (d) compares acoustic measurements with models. That is pattern recognition, even if the models are of words.

## 3. Objectives, logistics, references, grading (slides 4-7)

### Q5 (slide 4)

The second outcome of the course names three operations on speech signals: decompositions, codifications (coding) and modifications. Which statement uses one of these terms correctly, in the sense of this course?

- A) Coding changes one property of the speech, such as the pitch, and keeps the rest of the speech as it was.
- B) Modification describes the signal with fewer bits and rebuilds it at the receiver. Sending a few parameters per frame is an example.
- C) Decomposition splits the signal into parts, mainly a source and a filter. Linear prediction is one such method.
- D) Decomposition cuts a recording into its separate words. This is the first step of the text-to-speech front-end.

> [!success]- Answer
> **C.** Decomposition splits speech into parts, mainly the source (vocal folds) and the filter (vocal tract). Linear prediction, source estimation and cepstral analysis do this.
> - A: This is the definition of modification, not of coding. Coding keeps the speech as it is and uses fewer bits.
> - B: This is the definition of coding, not of modification. Modification changes a property such as pitch, duration or speaker.
> - D: Decomposition splits the signal into source and filter, not into words. And the front-end starts from text, not from a recording.

### Q6 (slides 5-7)

Slides 5 to 7 give the references and the grading of the course. Which statement about them is correct?

- A) The lecture slides are the main study source. The books on slide 6 are only recommended references.
- B) The final exam has the largest weight in the grade. It counts more than the term project.
- C) The term project may be done in a team, as long as each member hands in a separate report.
- D) The midterm and the final together count for less of the grade than the term project.

> [!success]- Answer
> **A.** There is no official textbook. The slides are the course, and the books on slide 6 are only recommended.
> - B: The weights are project 40%, final 35%, midterm 25%. The project has the largest weight.
> - C: The project (proposal and final report) must be done individually. Team work is not allowed.
> - D: The two exams give $25\%+35\%=60\%$, which is more than the project's 40%. The project is only the largest single part.

## 4. Course contents and schedule (slides 8-10)

### Q7 (slide 8)

In the course plan the block "Speech analysis and coding" comes before the block "Speech and speaker recognition". Why does a recogniser need the analysis methods first?

- A) The recogniser compares raw samples. The analysis block is only needed so that these samples can be stored with fewer bits.
- B) The recogniser cuts the signal into short frames and describes each by a few spectral numbers. The analysis methods produce these numbers.
- C) The analysis methods remove all speaker information from the signal, so that the recogniser only sees the words.
- D) The states of the HMM are the formants of the sound, so the analysis must first find the formant frequencies of each frame.

> [!success]- Answer
> **B.** A recogniser does not work on raw samples. Each short frame is described by a few numbers (features) about its spectrum. Short-time Fourier analysis, linear prediction and cepstral analysis produce them, and the HMMs then model sequences of these feature vectors.
> - A: This mixes up features with coding. The recogniser compares feature vectors, not stored samples.
> - C: The features still carry speaker information. Speaker recognition uses the same kinds of features to find the identity.
> - D: HMM states are abstract parts of a sound that each absorb frames. They are not formants, and the features are not only formant frequencies.

### Q8 (slide 9)

The block "Speech synthesis and modification" has four topics: the text-to-speech front-end, the text-to-speech back-end, prosodic modification and voice conversion. Which statement is correct?

- A) The back-end works with symbols only. The front-end turns this description into a waveform.
- B) Voice conversion changes the words of a recording and keeps the voice of the speaker.
- C) Prosodic modification changes pitch, duration or loudness and keeps the words and the speaker.
- D) Prosodic modification changes the speaker identity and keeps the pitch and timing as they were.

> [!success]- Answer
> **C.** Prosody is the melody and rhythm of speech: pitch, duration and loudness. Prosodic modification changes these. The words and the speaker stay.
> - A: The roles are swapped. The front-end turns text into a symbolic description (sounds, stress, melody). The back-end makes the waveform.
> - B: Voice conversion changes the speaker identity. The words stay the same.
> - D: This mixes up the two methods. Changing the identity is voice conversion, and prosodic modification changes exactly the pitch and timing.

### Q9 (slide 10)

![[cs568_lecture_01_l1_introduction_p010.jpg]]

The midterm is in the week of 9 to 13 Nov. Which statement about this week and the weeks before it is correct?

- A) Cepstral analysis and probability are scheduled in the midterm week itself. The slide does not say if they belong to the midterm.
- B) Cepstral analysis is taught in the week before the midterm week, together with linear prediction and source estimation.
- C) Pattern recognition and template matching are scheduled in the midterm week, right after cepstral analysis.
- D) The slide includes cepstral analysis and probability in the midterm, because they are in the same row.

> [!success]- Answer
> **A.** The row 09 Nov - 13 Nov has the topics cepstral analysis and probability, statistics and estimation, and the word "Midterm". The last topics before that week are linear prediction and source estimation (02 Nov - 06 Nov). The slide does not say if the topics of the midterm week are examined.
> - B: The week 02 Nov - 06 Nov has only linear prediction and source estimation. Cepstral analysis is in the next row, the midterm week.
> - C: Pattern recognition and template matching are one row later, in the week 16 Nov - 20 Nov (with the project proposal).
> - D: Sharing a row only means the same week. The slide gives no list of exam topics, so this is an assumption.
