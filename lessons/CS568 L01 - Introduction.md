---
course: CS 568 Speech Processing
lecture: 1
title: Introduction
tags: [cs568, lecture]
slides: lectures/lecture_01/l1_Introduction.pdf
full_notes: lecture_notes/lecture_01/CS568_Lecture_01_Introduction.pdf
---
# CS 568 · Lecture 1 · Introduction

> [!summary] In short
> - Speech processing has five sub-areas: speech recognition (what), speaker recognition (who), coding (fewer bits), synthesis (make speech), enhancement (cleaner speech).
> - Two models return in every lecture: the source-filter model $P(s)=U(s)\,T(s)\,R(s)$ and the left-to-right HMM.
> - The field borrows from DSP, NLP, machine learning, phonetics, HCI, perceptual psychology and pattern recognition.
> - Grade: term project 40%, midterm 25%, final 35%. The project is individual.
> - The course has five blocks; the midterm is in the week of 9 to 13 Nov.

## 1. What speech processing is, and the overview figure (slides 1-2)
![[cs568_lecture_01_l1_introduction_p001.jpg]]
![[cs568_lecture_01_l1_introduction_p002.jpg]]

**Notes.** Speech processing is the study of speech signals and of the methods used to process them.

| Sub-area | Question | Direction |
|---|---|---|
| Speech recognition | What was said? | waveform to symbols (analysis) |
| Speaker recognition | Who said it? | waveform to symbols (analysis) |
| Speech coding | How to use fewer bits? | waveform to waveform |
| Speech synthesis | How to make speech from text? | symbols to waveform |
| Speech enhancement | How to make it cleaner? | waveform to waveform |

The figure on slide 2 has three panels.

- **Left, source-filter model.** $P(s)=U(s)\,T(s)\,R(s)$. $s$ is the frequency variable. $U$ is the source spectrum (airflow pulses from the vocal folds), $T$ is the vocal-tract transfer function (its peaks are the formants), $R$ is the lip radiation, $P$ is the spectrum of the sound that leaves the mouth. In dB the three curves add.
- **Middle, left-to-right HMM** with six states. States 1 and 6 are entry and exit and take no frame. States 2 to 5 each take one frame (about 10 ms of signal) per visit. A self-loop spends one more frame in the same state. A skip ($2\to4$, $3\to5$) jumps over a state. The model never goes back.
- **Right, a sentence** in ordinary spelling (orthographic) and in phonetic symbols. Square brackets mark variants.

Applications on slide 2: human-computer interfaces, telecommunication, assistive technologies, audio mining (diarization means "who spoke when"), security.

> [!example] One frequency, in numbers
> $|U|=0.5$, $|T|=4$, $|R|=2$. Then $|P|=0.5\cdot4\cdot2=4$. In dB ($20\log_{10}$): $-6.02+12.04+6.02=12.04$ dB, which equals $20\log_{10}4$.

> [!warning] Watch out
> Spectra multiply, waveforms do not. In time the source waveform is convolved with the vocal-tract impulse response ([[CS568 L04 - Signals and transforms]]).

## 2. Related disciplines (slide 3)
![[cs568_lecture_01_l1_introduction_p003.jpg]]

**Notes.** Seven neighbouring fields feed the course: digital signal processing (lectures 4 to 6), natural language processing (15, 17), machine learning (10, 13), phonetics (3), human-computer interaction, perceptual psychology (2), pattern recognition (11, 12). Phonetics describes the sounds a speaker makes. Perceptual psychology describes what the listener hears.

## 3. Objectives, logistics, references, grading (slides 4-7)
![[cs568_lecture_01_l1_introduction_p004.jpg]]
![[cs568_lecture_01_l1_introduction_p005.jpg]]
![[cs568_lecture_01_l1_introduction_p006.jpg]]
![[cs568_lecture_01_l1_introduction_p007.jpg]]

**Notes.** Outcomes: analyse and visualise speech signals, decompose, code and modify them, and build a complete recogniser with HTK (lecture 21). Class: Tuesday 18:00 to 21:00, room F217. No official textbook, the slides are the course.

$$\text{grade}=0.40\cdot\text{project}+0.25\cdot\text{midterm}+0.35\cdot\text{final}$$

> [!example] Weighted grade
> Project 80, midterm 60, final 70: $32+15+24.5=71.5$.

## 4. Course contents and schedule (slides 8-10)
![[cs568_lecture_01_l1_introduction_p008.jpg]]
![[cs568_lecture_01_l1_introduction_p009.jpg]]
![[cs568_lecture_01_l1_introduction_p010.jpg]]

**Notes.** Five blocks, with the lecture numbers of the slide decks:

| Block | Lectures |
|---|---|
| Introduction | 1, 2, 3 |
| Mathematical foundations | 4, 5, 10, 11 |
| Speech analysis and coding | 6, 7, 8, 9 |
| Speech and speaker recognition | 12, 13, 14, 15, 16, 21 |
| Speech synthesis and modification | 17, 18, 19, 20 |

Dates from the schedule: project announced 5 to 9 Oct, midterm 9 to 13 Nov, project proposal 16 to 20 Nov, final report upload 21 to 25 Dec, project presentations 28 Dec to 1 Jan.

> [!warning] Watch out
> Lecture numbers follow the slide decks, not the blocks. Lectures 10 and 11 are mathematical foundations but come after lecture 9. The "Chapter" column of the schedule is the reading, not a lecture number.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Source-filter model | $P(s)=U(s)\,T(s)\,R(s)$: source times vocal tract times lip radiation, at every frequency |
| dB | $20\log_{10}\lvert\cdot\rvert$. Products of spectra become sums |
| Analysis | waveform to symbols (speech and speaker recognition) |
| Synthesis | symbols to waveform (text-to-speech) |
| Frame | a short slice of signal, about 10 ms |
| Left-to-right HMM | states only move forward: self-loop, next state, or skip one |
| Emitting state | takes one frame per visit (states 2 to 5). States 1 and 6 take none |
| Diarization | cutting a recording into speaker turns: who spoke when |
| Bit rate | samples per second times bits per sample. Telephone: $8000\times8=64$ kbit/s |

## Check yourself
> [!question]- 1. Classify: (a) a phone-call codec, (b) unlocking a bank account by voice, (c) a GPS voice reading street names.
> (a) Waveform to waveform, speech coding. (b) Analysis, speaker recognition (verification). (c) Symbols to waveform, synthesis.

> [!question]- 2. The source falls by 12 dB per octave, the radiation rises by 6 dB per octave. Ignoring the vocal tract, what is the level at 2000 Hz if it is 0 dB at 500 Hz?
> Slopes add in dB: $-12+6=-6$ dB per octave. 500 to 2000 Hz is 2 octaves, so $2\cdot(-6)=-12$ dB.

> [!question]- 3. What is the smallest number of frames the six-state HMM can absorb?
> From state 2 to state 5 is a climb of 3. The largest step is $+2$, so two moves are needed and three emitting states are visited: **3 frames**. The paths are $2,4,5$ and $2,3,5$. Two frames is impossible.

> [!question]- 4. With self-loops allowed, how many state sequences absorb exactly 4 frames?
> Three steps from $\{0,+1,+2\}$ must add to 3: $\{1,1,1\}$ gives 1 sequence, $\{0,1,2\}$ gives $3!=6$. Total **7**.

> [!question]- 5. Project 80 and midterm 60. Which final score gives an overall 70?
> $32+15+0.35F=70$, so $F=23/0.35\approx65.7$.

> [!question]- 6. Telephone speech is 8000 samples per second at 8 bits. How much smaller is an 8 kbit/s codec?
> $8000\times8=64$ kbit/s. $64/8=8$ times fewer bits.

## Links
- Course index: [[CS568 Home]]
- Next: [[CS568 L02 - Speech production and perception]]
- Full step-by-step notes (PDF): [CS568_Lecture_01_Introduction.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_01/CS568_Lecture_01_Introduction.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_01/CS568_Lecture_01_Introduction.pdf`
- Original slides (PDF): [l1_Introduction.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_01/l1_Introduction.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_01/l1_Introduction.pdf`
