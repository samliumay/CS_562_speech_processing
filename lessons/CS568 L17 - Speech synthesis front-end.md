---
course: CS 568 Speech Processing
lecture: 17
title: Speech synthesis front-end
tags: [cs568, lecture]
slides: lectures/lecture_17/l17.pdf
full_notes: lecture_notes/lecture_17/CS568_Lecture_17_TTS_Front_End.pdf
---
# CS 568 · Lecture 17 · Speech synthesis: the front-end

> [!summary] In short
> - Text-to-speech (TTS) turns any text into intelligible, natural speech. The front-end is NLP (text to symbols), the back-end is DSP (symbols to waveform).
> - The front-end has three stages: text processing, phonetic analysis, prosodic analysis.
> - Phonetic analysis: morphs, homographs, and grapheme-to-phoneme conversion with a lexicon plus letter-to-sound rules.
> - Prosody per phone: pitch ($F_0$, most important), duration, loudness (often ignored).
> - Prosodic models: Klatt (duration), sums of products, Pierrehumbert and ToBI, tilt, Fujisaki.

## 1. TTS, front-end and back-end (slides 2-5)
![[cs568_lecture_17_l17_p002.jpg]]
![[cs568_lecture_17_l17_p003.jpg]]
![[cs568_lecture_17_l17_p004.jpg]]
![[cs568_lecture_17_l17_p005.jpg]]

**Notes.** TTS must be **intelligible** (the listener understands) and **natural** (it sounds human). It is not cut-and-paste of recorded words.

- **Front-end** (NLP): turns raw text (numbers, abbreviations) into written-out words, gives each word a phonetic transcription, and marks prosodic units. Its output is a symbolic linguistic representation: phones plus prosody, no sound yet.
- **Back-end** (DSP, the "synthesiser"): turns the symbols into sound. Formant, articulatory, concatenative or HMM-based synthesis (Lecture 18).

Front-end stages, in order: text processing, then phonetic analysis, then prosodic analysis. Each stage needs the output of the one before.

## 2. Text processing (slides 6-7)
![[cs568_lecture_17_l17_p006.jpg]]
![[cs568_lecture_17_l17_p007.jpg]]

**Notes.** Text processing finds everything about the text that is not phonetic or prosodic. Four tasks:

1. **Document structure detection**: filter headers, find paragraphs and lists. Easier with SGML markup.
2. **Text normalisation**: expand abbreviations, acronyms, dates and numbers the way an educated speaker reads them. "St." is "street" or "saint", "Dr." is "drive" or "doctor". "IBM" is spelled out, "NATO" is read as a word.
3. **Text markup interpretation**: markup such as SSML controls the rendering (address mode, emotion, pauses).
4. **Linguistic analysis**: part-of-speech tags, word sense, emphasis, speech acts. In "They can can cans" the three "can" are a function word, a verb and a noun.

> [!warning] Watch out
> Normalisation is not a lookup table: the reading depends on context. Most TTS systems do not fully parse the input, because parsing is costly and the input is often fragments.

## 3. Phonetic analysis (slides 8-9)
![[cs568_lecture_17_l17_p008.jpg]]
![[cs568_lecture_17_l17_p009.jpg]]

**Notes.**
- **Morphological analysis**: split a word into morphs (prefixes, stem, suffixes). Example: anti-dis-establish-ment-arian-ism has 6 morphs. Useful because derived words are pronounced like their roots. Rules handle about 95% of words.
- **Homograph disambiguation**: same spelling, different sense and pronunciation ("object", "resume", "read"). Find the sense to pick the pronunciation.
- **Grapheme-to-phoneme (G2P)**: spelling to phonemes. Letter-to-sound (LTS) rules suit languages with regular spelling (Spanish, Finnish). English needs a **lexicon**, at least for the words that the rules get wrong. Words not in the lexicon go through LTS rules, which can be learned with classification and regression trees (CART).

G2P order: look up the lexicon first, fall back on LTS rules.

## 4. Prosodic analysis (slides 10-13)
![[cs568_lecture_17_l17_p010.jpg]]
![[cs568_lecture_17_l17_p011.jpg]]
![[cs568_lecture_17_l17_p012.jpg]]
![[cs568_lecture_17_l17_p013.jpg]]

**Notes.** Prosody is how intonation, speaking rate and loudness move across an utterance. Per phoneme it is three numbers: amplitude, duration, pitch ($F_0$, the fundamental frequency). Prosody separates words in tonal languages, structures phrases, and focuses attention.

- **Loudness**: set mainly by the phone (voiceless fricatives weak, vowels strong), slightly higher on stressed syllables. Least important, most systems ignore it.
- **Duration** (second most important): phones differ ("bit" vs "beet"). Steady sounds (vowels, fricatives) change more with speaking rate than stops. A vowel is longer before a voiced consonant ("feed") than before an unvoiced one ("feet"). Emphasised syllables and phrase-final syllables are lengthened.
- **Pitch** (most important): $F_0$ peaks near stressed syllables. $F_0$ drifts down over a phrase (**declination**). The final syllable rises in yes/no questions and non-terminal phrases, and falls faster in terminal phrases.

Schools of intonation: British, autosegmental-metrical, Fujisaki (production based), tilt (engineering).

## 5. History and Klatt's duration model (slides 14-15)
![[cs568_lecture_17_l17_p014.jpg]]
![[cs568_lecture_17_l17_p015.jpg]]

**Notes.** Three generations: rule-based (formant synthesisers: Klatt, Pierrehumbert), statistical (diphone synthesisers: sums of products, tilt), use as-is (unit selection: take prosody from a large natural corpus).

**Klatt**: each segment has an inherent duration $InhDur$ and a minimum $MinDur$. Rules stretch or shrink only the part in between:

$$Dur=MinDur+\frac{(InhDur-MinDur)\cdot Perc}{100}$$

Each rule $k$ that fires gives a percentage $Perc_k$. They combine by multiplication: $Perc=100\cdot\prod_k (Perc_k/100)$. The model ignores interactions between rules.

> [!example] Two rules
> $InhDur=140$ ms, $MinDur=60$ ms, rules $+40\%$ and $-30\%$. $Perc=100\cdot1.4\cdot0.7=98$. $Dur=60+80\cdot0.98=138.4$ ms. Adding the percentages instead ($Perc=110$) would give the wrong 148 ms.

**Sums of products** (van Santen): $Dur(f_1,\dots,f_N)=\sum_i\prod_{j\in I_i}S_{i,j}(f_j)$. The $f_j$ are context factors (phone, stress, position). The $S_{i,j}$ are learned from data.

> [!warning] Slide error
> The formula on slide 15 is missing the "+" between $MinDur$ and the fraction.

## 6. Pierrehumbert and ToBI (slides 16-17)
![[cs568_lecture_17_l17_p016.jpg]]
![[cs568_lecture_17_l17_p017.jpg]]

**Notes.** Intonation is a sequence of high (H) and low (L) tones, grouped into three units:

| Unit | Symbols | Role |
|---|---|---|
| Pitch accent | H\*, L\*, L+H\*, L\*+H, H\*+L, H+L\* | marks prominence. The \* tone sits on the stressed syllable |
| Phrase accent | L-, H- | links the last pitch accent to the phrase boundary |
| Boundary tone | %H, %L, H%, L% | marks the edge of an intonational phrase. % first means onset, % last means offset |

An intonational phrase is: optional initial boundary tone, one or more pitch accents, a phrase accent, a final boundary tone.

**ToBI** (tones and break indices) is the annotation standard built on this theory. It is only a labelling system. A separate method must turn the labels into an $F_0$ contour.

## 7. Tilt model (slide 18)
![[cs568_lecture_17_l17_p018.jpg]]

**Notes.** An engineering model. Intonation is a sequence of events: pitch accents, boundary tones, connections (straight lines), silences. Each accent is a rise followed by a fall, with amplitudes $A_{rise}$, $A_{fall}$ (Hz) and durations $D_{rise}$, $D_{fall}$:

$$tilt_{amp}=\frac{|A_{rise}|-|A_{fall}|}{|A_{rise}|+|A_{fall}|},\qquad tilt_{dur}=\frac{D_{rise}-D_{fall}}{D_{rise}+D_{fall}},\qquad tilt=\frac{tilt_{amp}+tilt_{dur}}{2}$$

$tilt=+1$ is a pure rise, $0$ a symmetric rise-fall, $-1$ a pure fall. An event is described by total amplitude $A$, total duration $D$ and $tilt$. Going back (assuming $tilt_{amp}=tilt_{dur}$): $A_{rise}=A(1+tilt)/2$, $A_{fall}=A(1-tilt)/2$, and the same for $D$.

> [!warning] Slide detail
> The slide leaves out the absolute-value bars. A fall is often given a negative amplitude, and the bars make the formula work either way.

## 8. Fujisaki model (slide 19)
![[cs568_lecture_17_l17_p019.jpg]]

**Notes.** $\ln F_0$ is a base value plus two components:

- **Phrase command**: the overall trend. Impulses at phrase boundaries, passed through the filter $G_p$.
- **Accent command**: local excursions on stressed syllables. Steps (on at $T_1$, off at $T_2$), passed through the filter $G_a$.

$$\ln F_0(t)=\ln F_b+\sum_{i}A_{p_i}\,G_p(t-T_{0i})+\sum_{j}A_{a_j}\big[G_a(t-T_{1j})-G_a(t-T_{2j})\big]$$

$$G_p(t)=\alpha^2t\,e^{-\alpha t},\qquad G_a(t)=\min\big[1-(1+\beta t)e^{-\beta t},\ \gamma\big]\qquad(t\ge0)$$

Both are zero for $t<0$. Typical values: $\alpha\approx3\ \mathrm{s^{-1}}$, $\beta\approx20\ \mathrm{s^{-1}}$, ceiling $\gamma\approx0.9$. The equations are from our notes, the slide only shows the block diagram.

> [!warning] Watch out
> The sum is in the log domain. On the linear scale the components multiply. Phrase commands are impulses, accent commands are steps: do not swap them.

## Key formulas and terms
| Term | Meaning / formula |
|---|---|
| Front-end / back-end | NLP: text to phones plus prosody / DSP: symbols to waveform |
| Text normalisation | expand abbreviations, numbers, dates into words |
| Morph | elementary unit of a word: prefix, stem, suffix |
| Homograph | same spelling, different sense and pronunciation |
| G2P | lexicon first, letter-to-sound rules for the rest |
| Declination | downward trend of $F_0$ over a phrase |
| Klatt | $Dur=MinDur+(InhDur-MinDur)\,Perc/100$, rule percentages multiply |
| Sums of products | $Dur=\sum_i\prod_{j\in I_i}S_{i,j}(f_j)$ |
| ToBI | pitch accents (\*), phrase accents (-), boundary tones (%); labels only |
| Tilt | $\tfrac12(tilt_{amp}+tilt_{dur})\in[-1,1]$ |
| Fujisaki | $\ln F_0=\ln F_b+$ phrase (impulses, $G_p$) $+$ accent (steps, $G_a$) |

## Check yourself
> [!question]- 1. How does a normaliser read "Dr. Who lives on Oak Dr."?
> First "Dr.": a name follows, so "doctor". Second "Dr.": it follows a street name, so "drive". Result: "Doctor Who lives on Oak drive."

> [!question]- 2. Split "unhelpfulness" into morphs.
> un-help-ful-ness: 1 prefix, 1 stem, 2 suffixes, 4 morphs.

> [!question]- 3. Which vowel is longer, the one in "bead" or in "beat"? Why?
> "bead". Same vowel, but /d/ is voiced and /t/ is voiceless, and vowels are longer before voiced consonants.

> [!question]- 4. Klatt: $InhDur=120$ ms, $MinDur=50$ ms, rules give $Perc_1=80$ and $Perc_2=125$. Find $Dur$.
> $Perc=100\cdot0.80\cdot1.25=100$. $Dur=50+70\cdot100/100=120$ ms. The two rules cancel.

> [!question]- 5. What is the tilt of a pure fall?
> $A_{rise}=0$ and $D_{rise}=0$, so $tilt_{amp}=-1$ and $tilt_{dur}=-1$. $tilt=-1$.

> [!question]- 6. Fujisaki, $F_b=120$ Hz, $\alpha=3$. Which $A_p$ makes the phrase peak reach 180 Hz?
> Needed rise in the log domain: $\ln(180/120)=\ln1.5\approx0.4055$. The peak of $G_p$ is $\alpha/e\approx1.1036$ (at $t=1/\alpha$). $A_p=0.4055/1.1036\approx0.367$.

## Links
- Course index: [[CS568 Home]]
- Next lecture: [[CS568 L18 - Speech synthesis back-end]]
- Quizzes and labs that use this lecture: none (the midterm booklet covers Lectures 1 to 6).
- Full step-by-step notes (PDF): [CS568_Lecture_17_TTS_Front_End.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_17/CS568_Lecture_17_TTS_Front_End.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lecture_notes/lecture_17/CS568_Lecture_17_TTS_Front_End.pdf`
- Original slides (PDF): [l17.pdf](file:///home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_17/l17.pdf) `/home/umaysamli/Desktop/university/ms/semester_2/cs_568/lectures/lecture_17/l17.pdf`

Slides not embedded: 1
