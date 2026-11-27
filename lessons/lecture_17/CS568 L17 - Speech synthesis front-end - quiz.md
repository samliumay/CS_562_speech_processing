---
course: CS 568 Speech Processing
lesson: CS568 L17 - Speech synthesis front-end
type: quiz
questions: 20
---

# CS568 L17 - Speech synthesis front-end - quiz

Lesson note: [[CS568 L17 - Speech synthesis front-end]]. One question per check question of the lesson. Pick an option, then open the answer.

## 1. TTS, front-end and back-end (slides 2-5)

### Q1 (slide 2)

A text-to-speech (TTS) system must turn any input text into intelligible and natural speech. Why is TTS not a "cut-and-paste" system that strings together recorded words?

- A) Recorded words are less intelligible than synthetic sounds, so the back-end must build every sound from formant rules.
- B) A large enough set of recordings covers any text, but the joins between words make clicks that DSP cannot remove.
- C) Each word keeps its own pitch and length in any sentence, so the only problem is finding the recordings fast enough.
- D) The text is arbitrary, so no set of recordings has every word, and words recorded alone carry their own pitch and length.

> [!success]- Answer
> **D.** Two reasons. The input is arbitrary text, so no fixed set of recordings contains every word. And a word recorded alone has its own pitch and duration, so a string of such words does not sound natural in a new sentence.
> This is why TTS uses linguistic analysis (NLP, the front-end) and acoustic models of speech (DSP, the back-end).
> - A: Natural recordings are not less intelligible. Concatenative synthesis even uses recorded units. The problem is coverage and prosody.
> - B: No finite set of recordings covers arbitrary text, for example new names or numbers.
> - C: Pitch and length of a word change with its place in the sentence. A word recorded alone does not fit a new context.

### Q2 (slide 3)

Find the mistakes in this claim: "The back-end expands '25 km' into words and then makes the sound. The front-end gives it a rough waveform to start from." Which statement is correct?

- A) The front-end expands "25 km" into words, and it also outputs a rough waveform that the back-end refines.
- B) The back-end expands "25 km" while it makes the sound, and it starts from a rough waveform from the front-end.
- C) The front-end expands "25 km" into words, and its output is only phones and prosody, with no sound yet.
- D) The back-end expands "25 km" into words, as the front-end outputs only phones and prosody, with no sound yet.

> [!success]- Answer
> **C.** Expanding numbers and abbreviations is text normalisation, a job of the front-end (the NLP part).
> The front-end produces no waveform. Its output is a symbolic linguistic representation: a phonetic transcription plus prosody information. Only the back-end (DSP, the "synthesizer") makes sound.
> - A: This fixes the first mistake only. The front-end makes no waveform at all.
> - B: This repeats the claim, so both mistakes remain. The back-end works on symbols that are already phones, and the front-end makes no sound.
> - D: This fixes the second mistake only. The expansion into words is still a front-end task.

### Q3 (slide 4)

The front-end must decide three things: (a) "2026" is read "twenty twenty-six", (b) the sounds of the word "cough", (c) the voice goes up at the end of "Are you coming?". In which order are they decided, and why?

- A) (b), (a), (c): letters become sounds first, then numbers are expanded among the words, then pitch is set.
- B) (a), (c), (b): words first, then the melody of the sentence, as pitch belongs to whole words, and phonemes last.
- C) (c), (a), (b): the "?" shows the sentence type, so the intonation is set first, before any work on words.
- D) (a), (b), (c): a number must become words before its sounds are found, and pitch is set per phoneme.

> [!success]- Answer
> **D.** (a) is text processing, (b) is phonetic analysis, (c) is prosodic analysis. The front-end runs them in this fixed order.
> You cannot find the phonemes of "2026" before it is written as words. You cannot give each phoneme a duration and a pitch before you have the phonemes.
> - A: "2026" has no letters to convert. It must first become words, so text processing comes before phonetic analysis.
> - B: Prosody is assigned per phoneme (pitch, duration, amplitude), so the phonemes must exist first.
> - C: The prosody stage needs phonemes to attach pitch to. Seeing "?" does not let it skip the earlier stages.

### Q4 (slide 5)

In the slide's flow chart of the front-end, the stages text processing and phonetic analysis get help from a side box "Dictionary, rules". Which knowledge source feeds the prosodic analysis stage?

- A) The same dictionary, since the prosody of each word is stored in its lexicon entry.
- B) Hand-written rules or a model learned from data (machine learning).
- C) No extra source: prosody follows from the tagged phones by one fixed formula.
- D) Feedback from the back-end waveform, so the pitch can be corrected after synthesis.

> [!success]- Answer
> **B.** The side box of prosodic analysis is "Rules or machine learning". The first two stages rest on stored knowledge about words. Prosody is set by hand-written rules or by a model trained on data.
> The arrows: tagged text after text processing, tagged phones after phonetic analysis, control information (sounds, durations, pitch) after prosodic analysis.
> - A: Prosody depends on the whole sentence (phrasing, focus, question or statement), so it cannot be stored per word.
> - C: The stage has its own knowledge source on the chart. Pitch, duration and stress depend on context, not on one formula.
> - D: The chart has no arrow back from the back-end. The front-end hands over control information and is finished.

## 2. Text processing (slides 6-7)

### Q5 (slide 6)

A TTS system reads: "Meet me on 3/4 at St. Mary's, 12 Park St., near the MIT and NATO offices." Which statement about text normalisation of this sentence is correct?

- A) "3/4" has one reading, "three quarters", because a slash between two numbers always marks a fraction.
- B) The two "St." get one reading, because one abbreviation has exactly one expansion in the table.
- C) The two "St." get different readings: "saint" before a name and "street" after a street name.
- D) "MIT" and "NATO" are both read as words, because all acronyms in capital letters follow one rule.

> [!success]- Answer
> **C.** Normalisation depends on context. Before a name "St." is "saint", after a street name it is "street".
> The same holds in Turkish: "5 kat" is "beş kat", but "5. kat" is "beşinci kat", because the dot after a number marks an ordinal and not the end of a sentence.
> - A: "3/4" can be a date (March 4 or 3 April) or a fraction. Only the context decides.
> - B: A lookup table with one entry per abbreviation fails here. Normalisation is not a plain table lookup.
> - D: "MIT" is spelled letter by letter, "NATO" is read as a word. In Turkish, "TBMM" is spelled and "ASELSAN" is read as a word. The system needs a list or a rule per acronym.

### Q6 (slide 7)

In "Please book the book" both words "book" are pronounced with the same phonemes. Why does the TTS front-end still need their part-of-speech tags?

- A) The tags feed the prosody stage: the accentuation of the sentence depends on the role of each word.
- B) The tags are not needed here: when the sounds are the same, the front-end can skip linguistic analysis.
- C) The tags let the system build a full parse tree, which every TTS front-end makes for each sentence.
- D) The tags go on to the back-end, which picks a different stored sound unit for a verb and a noun.

> [!success]- Answer
> **A.** The first "book" is a verb, the second a noun. The phonemes are the same, but the accent pattern of the sentence depends on the role of each word, as in "They can can cans". The tags are input for prosodic analysis.
> - B: Prosody still needs the roles, so linguistic analysis is still needed.
> - C: Most TTS systems do not fully parse: it costs too much, and the input is often fragments that a parser handles badly.
> - D: The back-end receives only phones with their prosody, not word classes. The tags act earlier, in the prosody stage.

## 3. Phonetic analysis (slides 8-9)

### Q7 (slide 8)

A letter rule says "th is one sound, as in 'the'". Applied to "hothouse", it joins the "t" of "hot" and the "h" of "house" into one sound. Which task of phonetic analysis prevents this, and how?

- A) Morphological analysis: it splits the word into hot + house, and each morph is pronounced like its root word.
- B) Homograph disambiguation: it uses the sentence to choose between the "th" reading and the "t-h" reading.
- C) Text normalisation: it rewrites every compound as two separate words before phonetic analysis starts.
- D) A fixed letter rule: "th" inside a word is always two sounds, and only "th" at the start is one sound.

> [!success]- Answer
> **A.** Morphological analysis splits a word into morphs (prefixes, stem, suffixes). The parts of "hothouse" are then pronounced like the words "hot" and "house".
> A second benefit: the lexicon can store roots and affixes and not every word form. For Turkish this is essential: "evlerimizden" is ev-ler-imiz-den, 4 morphs.
> - B: "hothouse" is not a homograph. There is one word with one sense; the problem is the morph boundary inside it.
> - C: Normalisation expands non-standard items such as numbers and abbreviations. It does not split ordinary words into morphs.
> - D: The rule fails for words such as "mother" or "father", where "th" inside the word is one sound.

### Q8 (slide 9, homographs)

The TTS system must read "I will present the present." Which task acts here, and with what result?

- A) Lexicon lookup alone: the lexicon has one entry for "present", so both words get the same sounds.
- B) Homograph disambiguation with the part-of-speech tags: preSENT after "will", PREsent after "the".
- C) Homograph disambiguation with the part-of-speech tags: PREsent after "will", preSENT after "the".
- D) Letter-to-sound rules: the letters around each "present" decide the stress, without the context.

> [!success]- Answer
> **B.** The two words have the same spelling, a different sense and a different pronunciation: a homograph. The context decides, mainly the part-of-speech tag. After "will" it is a verb, preSENT. After "the" it is a noun, PREsent.
> - A: The lexicon has both pronunciations. It cannot choose between them without the sentence.
> - C: This swaps the stress. English verbs of this pair stress the second syllable, nouns the first.
> - D: Both words have exactly the same letters, so letter rules give them the same pronunciation.

### Q9 (slide 9, G2P)

A grapheme-to-phoneme module has a lexicon and letter-to-sound (LTS) rules. It meets the new name "Zorblat". What happens?

- A) LTS rules are tried first for every word; the lexicon is used only for a word on which the rules fail.
- B) The system cannot find a pronunciation, so it spells the name letter by letter, like an acronym.
- C) Homograph disambiguation picks its pronunciation from the part-of-speech tags in the sentence.
- D) It is not in the lexicon, so the LTS rules give its pronunciation; the lexicon is looked up first.

> [!success]- Answer
> **D.** The order is lexicon first, LTS rules second. The lexicon is exact for its words, also irregular ones, but it is finite. "Zorblat" is out of vocabulary, so the rules (which can be learned with classification and regression trees) produce its phonemes.
> - A: This reverses the order. Rules are wrong for irregular words, so the lexicon must come first. The rules cannot tell when they fail.
> - B: LTS rules give a pronunciation for any letter string. Spelling out is for acronyms such as "MIT".
> - C: A homograph has several known pronunciations. A new name has none in the system.

## 4. Prosodic analysis (slides 10-13)

### Q10 (slide 10)

In "When he arrived, we left." a pause and a pitch change after "arrived" tell the listener that more is coming. Which role of prosody is this?

- A) Focus: prosody makes "arrived" the most important word, against a contrast.
- B) Tone: pitch tells two words apart, as in a tonal language.
- C) Structure: prosody groups the utterance into phrases and links them.
- D) Rate: prosody slows down the speech for a listener who is far away.

> [!success]- Answer
> **C.** Prosody divides the utterance into phrases and shows how the phrases relate. The pause and pitch movement mark the end of a non-final phrase.
> - A: Focus puts attention on one word, as in "I ordered TEA, not coffee". No word is contrasted here.
> - B: In a tonal language such as Mandarin, the pitch movement of "ma" changes the word. Here it does not change any word.
> - D: The pause marks a phrase boundary inside the sentence. It is not a global change of speaking rate.

### Q11 (slide 11)

Find the mistake: "Loudness is mainly set by stress, so a TTS system must model it with care." Which correction is right?

- A) Loudness is set mainly by stress, but rules for it are hard to write, so most systems ignore it.
- B) Loudness is set mainly by the phone, and it is not critical for quality, so most systems ignore it.
- C) Loudness is set mainly by the phone, and as vowels are loud it is the most important prosodic cue.
- D) Loudness is set mainly by stress, but pitch already marks every stress, so loudness has no effect.

> [!success]- Answer
> **B.** The phone identity sets loudness: voiceless fricatives such as /s/ are weak, most vowels are strong. Stress adds only a little.
> Loudness is not critical for the quality of synthetic speech, compared to pitch and duration. Rules for it are easy to write, but most TTS systems ignore it.
> - A: Stress is not the main factor, and the slide says the rules are easy to write.
> - C: Pitch is the most important prosodic element, then duration. Loudness is the least important.
> - D: Stress is not the main factor. Loudness does change, it is simply not critical.

### Q12 (slide 12)

(a) Is the word "mat" longer in "The cat sat on the mat." or in "The mat is on the floor."? (b) The speaker talks twice as fast. Which part of "mat" shrinks more, the vowel or the final /t/?

- A) In the first sentence, as phrase-final syllables are lengthened; the vowel, as steady sounds change more with rate.
- B) In the first sentence, as phrase-final syllables are lengthened; the /t/, as short stops are squeezed first.
- C) In the second sentence, as the subject of a sentence is stressed; the vowel, as steady sounds change more with rate.
- D) In the second sentence, as the subject of a sentence is stressed; the /t/, as short stops are squeezed first.

> [!success]- Answer
> **A.** (a) In the first sentence "mat" ends the phrase, and syllables before a phrase break are lengthened.
> (b) Steady sounds (vowels, fricatives) change more with speaking rate than transient sounds such as stops.
> - B: Part (b) is reversed. A stop is a short transient, it has little room to shrink.
> - C: Part (a) is wrong. Being the subject does not lengthen a word; the phrase-final position does.
> - D: Both parts are wrong, for the two reasons above.

### Q13 (slide 13)

Compare the $F_0$ (pitch) contours of the statement "It is raining." and the yes/no question "Is it raining?". Which description is correct?

- A) Both have a peak near "rain" and a downward trend; at the last syllable the statement rises and the question falls faster.
- B) Both stay flat until the last syllable, as $F_0$ moves only at phrase ends; there the statement falls and the question rises.
- C) The statement has a peak near "rain" and declines; the question has no peak and rises steadily over the whole phrase.
- D) Both have a peak near "rain" and a downward trend; at the last syllable the statement falls faster and the question rises.

> [!success]- Answer
> **D.** Both contours have an $F_0$ maximum close to the stressed syllable "rain" and a general fall over the phrase (declination).
> At the final syllable a terminal phrase (statement) falls even faster. A yes/no question reverses the trend: $F_0$ rises. A non-terminal phrase also rises, for example before a comma.
> - A: This swaps the two endings.
> - B: $F_0$ peaks near stressed syllables inside the phrase, not only at its end, and it drifts down across the phrase.
> - C: The question also has its peak near the stressed syllable and the declination; only the end is reversed.

## 5. History and Klatt's duration model (slides 14-15)

### Q14 (slide 14)

A unit-selection synthesiser stores a large corpus of natural speech. Why does it keep several tokens of the same diphone?

- A) To average the tokens into one clean diphone per pair of phones, as a diphone synthesiser stores it.
- B) To estimate the rule percentages of Klatt's duration model from the lengths of the tokens.
- C) Tokens differ in context and prosody, so one that already fits the wanted prosody can be picked.
- D) So that signal processing can shift the pitch of any one token to the target, whatever its context.

> [!success]- Answer
> **C.** This is the "use as-is" generation. Each token was spoken in a different context, with its own pitch and duration. The system picks the token that already has the wanted prosody, so natural prosody is used as it is.
> - A: Averaging is not done. Unit selection uses the tokens themselves; the variety between them is the point.
> - B: Klatt's model belongs to the rule-based generation (formant synthesisers). Its rules come from experiments and the literature.
> - D: Large pitch changes by signal processing reduce naturalness. Unit selection avoids them by choosing a token that fits.

### Q15 (slide 15)

Klatt's duration model gives a segment the duration $Dur=MinDur+\dfrac{(InhDur-MinDur)\cdot Perc}{100}$, where $InhDur$ is its inherent duration, $MinDur$ its minimum duration and $Perc$ the percentage from the rules. Take $InhDur=100$ ms, $MinDur=40$ ms and $Perc=50$. What is $Dur$?

- A) 70 ms
- B) 50 ms
- C) 30 ms
- D) 90 ms

> [!success]- Answer
> **A.** $Dur=40+\dfrac{(100-40)\cdot50}{100}=40+30=70$ ms.
> The rules scale only the part between $MinDur$ and $InhDur$: $Perc=0$ gives $MinDur$, $Perc=100$ gives $InhDur$. (The formula on the slide is missing the "+" after $MinDur$.)
> - B: This applies $Perc$ to $InhDur$ alone: $100\cdot50/100=50$. Then the segment could shrink below its minimum.
> - C: This keeps only the scaled part, $(100-40)\cdot50/100=30$, and forgets to add $MinDur$.
> - D: This adds $MinDur$ but scales all of $InhDur$: $40+100\cdot50/100=90$. It forgets to subtract $MinDur$ inside the bracket.

## 6. Pierrehumbert and ToBI (slides 16-17)

### Q16 (slide 16)

In ToBI, L+H\* is a pitch accent made of two tones, H- is a phrase accent and L% is a boundary tone. Which statement is correct?

- A) In L+H\* the L sits on the stressed syllable, and a separate method must turn the ToBI labels into an $F_0$ contour.
- B) In L+H\* the H sits on the stressed syllable, and a ToBI transcription gives the $F_0$ contour directly.
- C) In L+H\* the L sits on the stressed syllable, and a ToBI transcription gives the $F_0$ contour directly.
- D) In L+H\* the H sits on the stressed syllable, and a separate method must turn the ToBI labels into an $F_0$ contour.

> [!success]- Answer
> **D.** The asterisk marks the tone aligned with the stressed syllable. In L+H\* this is the H; the L comes just before it.
> ToBI is only a labelling system. A separate method must convert the labels into an $F_0$ contour.
> - A: The L has no asterisk, so it is not the tone on the stressed syllable.
> - B: The labels say only which tones occur and where. They give no $F_0$ values in Hz or exact timing.
> - C: Both parts are wrong, for the two reasons above.

### Q17 (slide 17, upper figure)

In the Pierrehumbert tone grammar behind ToBI, an intonational phrase is built from pitch accents (marked \*), phrase accents (H-, L-) and boundary tones (H%, L%). Which tone sequence is a legal intonational phrase?

- A) L- H\* L%
- B) L\* H- H%
- C) H\* L\* L%
- D) H\* L- H- L%

> [!success]- Answer
> **B.** A phrase is: an optional initial boundary tone, one or more pitch accents, exactly one phrase accent, exactly one final boundary tone. L\* H- H% has no initial boundary tone (allowed), one pitch accent, one phrase accent and a final boundary tone.
> The figure writes the phrase accents with a raised minus and the initial boundary tones as H%, L% (slide 16: %H, %L). A loop over the pitch accents allows several of them.
> - A: A phrase accent comes only after the pitch accents, never before them. After H\* the phrase accent is also missing before L%.
> - C: Two pitch accents are allowed, but the phrase accent is missing before L%.
> - D: The grammar allows exactly one phrase accent before the final boundary tone.

### Q18 (slide 17, lower figure)

Korean ToBI (K-ToBI) writes some boundary tones as chains of tones: L%, HL%, LHL%, HLHL% and H%, LH%, HLH%, LHLH%. Which statement about these labels is correct?

- A) The last letter gives the $F_0$ direction at the very end; the letters before it add turning points in the final syllable.
- B) The first letter gives the $F_0$ direction at the very end; the letters after it add turning points before that end.
- C) The number of letters gives the height of the last $F_0$ target; a longer label ends at a higher pitch than a short one.
- D) Only the last letter is a boundary tone; the letters before it are pitch accents on the earlier words of the phrase.

> [!success]- Answer
> **A.** The label is read from left to right as the targets that $F_0$ visits. Every label ending in L ends low or falling; every label ending in H ends with a rise.
> A longer label adds turning points: L% is a plain fall, HL% a rise then a fall, LHL% and HLHL% more ups and downs before the final fall. All of this happens in the final syllable.
> - B: This reads the label backwards. LHL% ends low, so the end is given by the last letter.
> - C: The length gives the number of turning points, not the height. LHL% and L% both end low.
> - D: The whole chain is one boundary tone on the final syllable. Pitch accents are marked with \*.

## 7. Tilt model (slide 18)

### Q19 (slide 18)

In the tilt model an accent is a rise followed by a fall, with
$$tilt_{amp}=\frac{\lvert A_{rise}\rvert-\lvert A_{fall}\rvert}{\lvert A_{rise}\rvert+\lvert A_{fall}\rvert},\qquad tilt_{dur}=\frac{D_{rise}-D_{fall}}{D_{rise}+D_{fall}},\qquad tilt=\frac{tilt_{amp}+tilt_{dur}}{2}.$$
Event X rises 45 Hz in 150 ms and then falls 15 Hz in 50 ms. The fall is stored as a negative amplitude, $A_{fall}=-15$ Hz. What is the tilt of X?

- A) 1.25
- B) −0.5
- C) 0.5
- D) 1.0

> [!success]- Answer
> **C.** $tilt_{amp}=\dfrac{45-15}{45+15}=\dfrac{30}{60}=0.5$ and $tilt_{dur}=\dfrac{150-50}{150+50}=\dfrac{100}{200}=0.5$, so $tilt=\dfrac{0.5+0.5}{2}=0.5$: a long rise with a short fall.
> Tilt gives only the shape. Its size needs the total amplitude and total duration. (The slide also puts bars on the durations; durations are never negative, so they change nothing.)
> - A: This drops the absolute values: $tilt_{amp}=\dfrac{45-(-15)}{45+(-15)}=\dfrac{60}{30}=2$, so $tilt=(2+0.5)/2=1.25$.
> - B: This swaps rise and fall in both fractions: $(15-45)/60=-0.5$ and $(50-150)/200=-0.5$. That is the mirror event, a short rise with a long fall.
> - D: This adds $tilt_{amp}+tilt_{dur}=1.0$ and forgets to divide by 2.

## 8. Fujisaki model (slide 19)

### Q20 (slide 19)

![[cs568_lecture_17_l17_p019.jpg]]

In the Fujisaki model, $\ln F_0(t)$ is a base value $\ln F_b$ plus a phrase component (impulses through a filter) plus an accent component (steps through a filter). The output plot on the right has a solid curve and a dashed curve. What does the dashed curve show?

- A) The accent component alone, after the accent filter, without the base value and the phrase part.
- B) The base value $\ln F_b$ plus the phrase component only: the slow trend under the accent humps.
- C) The measured $F_0$ contour of the utterance, which the solid model curve tries to fit.
- D) The sum of both components on a linear scale, before the logarithm of the output is taken.

> [!success]- Answer
> **B.** The dashed curve is $\ln F_b$ plus the filtered phrase commands. It falls slowly after each impulse. The solid curve is the full contour: it lies on the dashed curve and rises above it wherever an accent command is on.
> The last phrase arrow points down; our reading of the figure is a negative phrase command that resets the phrase component at the end (the slide does not explain it).
> - A: The accent component is a set of humps that start and end at zero. The dashed curve is a slow trend over the whole utterance.
> - C: Both curves are model outputs. The solid one is the sum of all parts, the dashed one only part of it.
> - D: The model adds the components in the log domain. On the linear scale they multiply.
