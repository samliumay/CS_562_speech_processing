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

> [!question]- Check (slide 2): Name the two blocks of the diagram and say what the arrow between them carries. Why is TTS not "cut-and-paste" of recorded words?
> The blocks are the TTS front-end (left, next to the input text) and the TTS back-end (right, next to the output speech).
> The arrow between them carries control information: phoneme-level tags that specify pronunciation and prosody. These are symbols, there is no sound yet.
> Cut-and-paste fails for two reasons. The input text is arbitrary, so no set of recordings has every word. And a word recorded alone has its own pitch and length, so a string of such words does not sound natural.

> [!question]- Check (slide 3): Find the mistakes: "The back-end expands '25 km' into words and then makes the sound. The front-end gives it a rough waveform to start from."
> Mistake 1: expanding numbers and abbreviations into written-out words is a job of the front-end (the NLP component).
> Mistake 2: the front-end gives no waveform at all. Its output is a symbolic linguistic representation: a phonetic transcription plus prosody information.
> Only the back-end (the DSP component, the "synthesizer") makes sound, with formant, articulatory, concatenative or HMM-based synthesis.

> [!question]- Check (slide 4): Which front-end stage decides each of these? (a) "2026" is read "twenty twenty-six". (b) The sounds of the word "cough". (c) The voice goes up at the end of "Are you coming?".
> (a) Text processing. It is knowledge about the text that is not phonetic or prosodic: a non-word item becomes words.
> (b) Phonetic analysis. It turns the spelling of a word into phonemes, and possibly stress (vurgu) placement.
> (c) Prosodic analysis. It sets intonation (tonlama), speaking rate and amplitude for each phoneme.
> The order is fixed: you cannot find phonemes before the text is words, and you cannot set a duration and a pitch per phoneme before you have the phonemes.

> [!question]- Check (slide 5): In the flow chart, what is written on the arrow after each of the three boxes? Which side box feeds which stage?
> After text processing: tagged text. After phonetic analysis: tagged phones. After prosodic analysis: control information (sequence of sounds, durations, pitch). This last output goes to the back-end.
> The side box "Dictionary, rules" feeds text processing and phonetic analysis. The side box "Rules or machine learning" feeds prosodic analysis.
> So the first two stages rest on stored knowledge about words, and the prosody stage is either hand-written rules or a model learned from data.

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

> [!question]- Check (slide 6): "Meet me on 3/4 at St. Mary's, 12 Park St., near the MIT and NATO offices." Which items must text normalisation handle, and why is each one ambiguous?
> "3/4": a date or the fraction "three quarters". As a date it can even be March 4 or 3 April. Only the context decides.
> "St." twice: before a name it is "saint", after a street name it is "street". Same letters, two readings.
> "12": "twelve" here, but digits can also be read one by one (a phone number) or as a year.
> "MIT" is spelled letter by letter, "NATO" is read as a word. The spelling does not tell which, so the system needs a list or a rule.
> All of these are text normalisation: the goal is to read the text as an educated human speaker would.
> Added example (Turkish): the abbreviation (kısaltma) "Dr." becomes "doktor". "5 kat" is "beş kat" (five floors), but "5. kat" is "beşinci kat" (fifth floor): in Turkish the dot after a number marks an ordinal, so the normaliser must not read it as the end of a sentence. "TBMM" is spelled letter by letter (te-be-me-me) and "ASELSAN" is read as a word, the same contrast as "MIT" against "NATO".

> [!question]- Check (slide 7): In "Please book the book" both words "book" sound the same. Why does the TTS still need their part-of-speech tags? Give the two reasons why most TTS systems do not fully parse the text.
> The first "book" is a verb, the second is a noun. The phonemes are the same, but the accentuation of the sentence depends on the role of each word, in the same way as in "They can can cans". The tags are input for the prosody stage.
> No full parsing because: (1) it costs too much computation, (2) the input is often isolated sentences or fragments, which a full parser cannot handle well.
> Compare with markup: with SSML the author of the text tells the engine how to render it (address mode, emotion). With linguistic analysis the system must find the information itself.

## 3. Phonetic analysis (slides 8-9)
![[cs568_lecture_17_l17_p008.jpg]]
![[cs568_lecture_17_l17_p009.jpg]]

**Notes.**
- **Morphological analysis**: split a word into morphs (prefixes, stem, suffixes). Example: anti-dis-establish-ment-arian-ism has 6 morphs. Useful because derived words are pronounced like their roots. Rules handle about 95% of words.
- **Homograph disambiguation**: same spelling, different sense and pronunciation ("object", "resume", "read"). Find the sense to pick the pronunciation.
- **Grapheme-to-phoneme (G2P)**: spelling to phonemes. Letter-to-sound (LTS) rules suit languages with regular spelling (Spanish, Finnish). English needs a **lexicon**, at least for the words that the rules get wrong. Words not in the lexicon go through LTS rules, which can be learned with classification and regression trees (CART).

G2P order: look up the lexicon first, fall back on LTS rules.

> [!question]- Check (slide 8): A letter rule says "th is one sound, as in 'the'". What goes wrong for "hothouse", and which task of phonetic analysis prevents it? Give a second benefit of this task.
> The rule would join the "t" of "hot" and the "h" of "house" into one "th" sound, which is wrong (the word "hothouse" is an example beyond the slides).
> Morphological analysis first splits the compound into its morphs hot + house. Each part is then pronounced like the root word, because derived and compound words are pronounced close to their roots.
> Second benefit: the lexicon can store roots and affixes and does not need every word form. Rules decompose about 95% of the words of a typical text.
> Added example (Turkish): "evlerimizden" (from our houses) is ev-ler-imiz-den: the stem "ev" (house) and three suffixes (plural, "our", "from"), 4 morphs. It shows the decomposition of an inflected word. Turkish builds very many forms from one root, so a list of full word forms is not practical.

> [!question]- Check (slide 9, homographs): "I will present the present." Which task must act here, what information does it use, and what is the result?
> Homograph disambiguation. The two words have the same spelling but a different sense and pronunciation.
> It uses the context, mainly the part-of-speech tag from linguistic analysis. After "will" the word is a verb: preSENT. After "the" it is a noun: PREsent.
> A lexicon alone cannot solve this, because the lexicon has both entries. The choice needs the sentence.
> Added example (Turkish): with the circumflex written, "kar" (snow) and "kâr" (profit) have different spellings, so they are not homographs. But many texts leave the circumflex out. Then both are written "kar", the two readings sound different, and the system must choose from the context. The same happens with "hala" (aunt) and "hâlâ" (still) when both are written "hala".
> A contrast: "gül" (rose / laugh!) is a true homograph, but both senses have the same pronunciation, so it needs no disambiguation for pronunciation.

> [!question]- Check (slide 9, G2P): Compare the lexicon and letter-to-sound rules. What happens to a new name such as "Zorblat"? Why does English need a lexicon more than Spanish or Finnish?
> Lexicon: a lookup dictionary. It is exact for the words it has, also the irregular ones, but it is finite and has nothing for a new word.
> LTS rules: they give a pronunciation for any letter string, but they are wrong for irregular words.
> "Zorblat" is out of vocabulary, so it goes through the LTS rules. This is why the order is lexicon first, rules second. The rules can be learned from data with classification and regression trees.
> In English one spelling has many sounds: "ough" is different in "though", "through", "cough" and "bough". In Spanish or Finnish the relation between letters and sounds is simple, so rules alone are almost enough.
> Added example: Turkish spelling is also close to regular, so rules do most of the work there.

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

> [!question]- Check (slide 10): Match each case to a role of prosody on the slide. (a) In Mandarin the syllable "ma" means different things with different pitch movements. (b) "I ordered TEA, not coffee." (c) A pause and a pitch change after "When he arrived," tell you that more is coming.
> (a) Tonal language: pitch distinguishes lexical items (different words).
> (b) Focus of attention on a word, here contrastive stress.
> (c) Structure: prosody groups the utterance into phrases and shows how the phrases relate.
> In the end the prosody stage writes all of this as three values per phoneme: amplitude, duration and pitch ($F_0$).

> [!question]- Check (slide 11): Find the mistake: "Loudness is mainly set by stress, so a TTS system must model it with care."
> Two things are wrong. Loudness is mainly set by the phone identity: voiceless fricatives such as /s/ are weak, most vowels are strong. Stress adds only a little.
> And loudness is not critical for the quality of synthetic speech, compared to pitch and duration. Rules for it are easy to write, but most TTS systems ignore it.

> [!question]- Check (slide 12): (a) Is "mat" longer in "The cat sat on the mat." or in "The mat is on the floor."? (b) The speaker talks twice as fast. Which shrinks more, the vowel of "mat" or its /t/?
> (a) In the first sentence. There "mat" is at the end of the phrase, and syllables before a phrase break are lengthened.
> (b) The vowel. Steady sounds (vowels, fricatives) change more with speaking rate than transient sounds (stops).
> Duration therefore carries three things: stress, phrasing and rhythm. This is why it is the second most important prosodic element.

> [!question]- Check (slide 13): Describe the $F_0$ contour of "It is raining." and of "Is it raining?" with the three rules of the slide. Which of the four schools starts from speech production, and which from engineering?
> Both: an $F_0$ maximum close to the stressed syllable "rain", and a general downward trend over the phrase (declination).
> Statement (terminal phrase): at the final syllable the fall gets even faster.
> Yes/no question: the trend is reversed at the final syllable, $F_0$ rises. The same rise happens in a non-terminal phrase, for example before a comma.
> Production: the Fujisaki model follows known biological mechanisms. Engineering: the tilt model. The British school comes from teaching "correct" intonation, and the autosegmental-metrical school wants one theory for all languages.

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

> [!question]- Check (slide 14): For each of the three generations, name the synthesiser type of its period and say where the prosody knowledge comes from. Why does a unit-selection corpus hold several tokens of the same diphone?
> Rule-based: formant synthesisers. The knowledge is a set of rules from experiments or the literature (Klatt for duration, Pierrehumbert for intonation).
> Statistical: diphone synthesisers. The model parameters are estimated from data (sums of products for duration, tilt for intonation).
> Use as-is: unit-selection systems. A large corpus of natural speech trains the prosodic models and is also the source of the units.
> Several tokens per diphone: each token has a different phonetic and prosodic context. The system can pick the one that already fits the wanted prosody, so natural prosody is used as it is.

> [!question]- Check (slide 15): In Klatt's formula, what is $Dur$ for $Perc=0$ and for $Perc=100$? Then take $InhDur=100$ ms, $MinDur=40$ ms, $Perc=50$. What is the weak point of the model?
> $Perc=0$: $Dur=MinDur$. The segment cannot be compressed below its minimum, whatever the rules say.
> $Perc=100$: $Dur=MinDur+(InhDur-MinDur)=InhDur$, the inherent duration.
> So the rules only scale the part between the minimum and the inherent duration.
> $Perc=50$: $Dur=40+\dfrac{(100-40)\cdot 50}{100}=40+30=70$ ms.
> Weak point: each rule gives its own $Perc$ and they are multiplied, so the model does not account for interactions between rules. Other duration models on the slide: CART-based (Festival), neural-network-based (Campbell), sums of products (van Santen).

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

> [!info] How to read the two figures of slide 17 (beyond the slides)
> **Upper figure.** It is a grammar drawn as a graph. Read it from left to right. The small circles are states and each arc is one tone you may choose. The four columns are: boundary tone, pitch accents, phrase accent, boundary tone. Any path from the left circle to the arrow on the right is a legal intonational phrase.
> - At the start there are three arcs: H%, L%, and one arc without a label. The arc without a label means "no initial boundary tone", so this tone is optional.
> - The curved arrow above the pitch accents goes back to the left. It lets you take one more pitch accent. So a phrase has one or more pitch accents.
> - After the pitch accents you must take exactly one phrase accent (H or L with a raised minus) and then exactly one final boundary tone (H% or L%).
>
> **Notation.** The figure writes some things differently from slide 16. The initial boundary tones are written H%, L% (slide 16: %H, %L). In a two-tone accent the unstarred tone has a raised minus, for example L\*+H⁻ (slide 16: L\*+H). The figure also has a seventh accent, H\*+H⁻, that slide 16 does not list.
>
> **Lower figure.** Eight schematic $F_0$ shapes of boundary tones, from the guidelines for Korean ToBI (K-ToBI), where one boundary tone can be a chain of several H and L targets. The vertical line marks the start of the final syllable (hece) of the phrase (this comes from our lecture notes, the slide does not say it). Read each label from left to right as the targets that $F_0$ visits: LHL% is low, then high, then low.
>
> **Break indices.** The "BI" of ToBI: a number between every two words for the strength of the boundary, from 0 (words run together) to 4 (full intonational phrase break).

> [!question]- Check (slide 16): For each label say which unit it is and what it does: L+H\*, H-, L%. Then find the mistake: "In L+H\* the low tone sits on the stressed syllable, and a ToBI transcription gives the $F_0$ curve directly."
> L+H\*: a pitch accent made of two tones. It marks prominence on a word.
> H-: a phrase accent. It links the last pitch accent to the phrase boundary.
> L%: a boundary tone at the offset (end) of the intonational phrase.
> Mistake 1: the asterisk marks the tone that is aligned with the stressed syllable. In L+H\* this is the H. The L comes just before it.
> Mistake 2: ToBI is only a labelling system. A separate method must convert the labels into an $F_0$ contour.

> [!question]- Check (slide 17, upper figure): Use the graph. Is L\* H- H% a legal phrase? Is L- H\* L%? How can one phrase have three pitch accents?
> L\* H- H%: legal. Take the arc without a label at the start (no initial boundary tone), then the pitch accent L\*, the phrase accent H-, the final boundary tone H%. The path reaches the end.
> L- H\* L%: not legal. A phrase accent comes only after the pitch accents, and after H\* the path must pass a phrase accent before it can reach L%.
> Three pitch accents: after each pitch accent the curved arrow on top leads back to the state before the pitch accents, so you can choose again. Go round twice.

> [!question]- Check (slide 17, lower figure): The eight small contours are in two rows: L%, HL%, LHL%, HLHL% and H%, LH%, HLH%, LHLH%. What do the contours of one row have in common, and what does a longer label change?
> Top row: every label ends in L, and every contour ends low or falling.
> Bottom row: every label ends in H, and every contour ends with a rise.
> A longer label adds turning points: L% is a plain fall, HL% is a rise then a fall, LHL% and HLHL% have more ups and downs before the final fall.
> So the last letter gives the direction at the very end of the phrase, and the letters before it give the movement inside the final syllable.

## 7. Tilt model (slide 18)
![[cs568_lecture_17_l17_p018.jpg]]

**Notes.** An engineering model. Intonation is a sequence of events: pitch accents, boundary tones, connections (straight lines), silences. Each accent is a rise followed by a fall, with amplitudes $A_{rise}$, $A_{fall}$ (Hz) and durations $D_{rise}$, $D_{fall}$:

$$tilt_{amp}=\frac{|A_{rise}|-|A_{fall}|}{|A_{rise}|+|A_{fall}|},\qquad tilt_{dur}=\frac{D_{rise}-D_{fall}}{D_{rise}+D_{fall}},\qquad tilt=\frac{tilt_{amp}+tilt_{dur}}{2}$$

$tilt=+1$ is a pure rise, $0$ a symmetric rise-fall, $-1$ a pure fall. An event is described by total amplitude $A$, total duration $D$ and $tilt$. Going back (assuming $tilt_{amp}=tilt_{dur}$): $A_{rise}=A(1+tilt)/2$, $A_{fall}=A(1-tilt)/2$, and the same for $D$.

> [!warning] Slide detail
> The slide writes absolute-value bars on the amplitudes and also on the durations. A fall is often given a negative amplitude, and the bars make the formula work either way. A duration is never negative, so the bars on $D_{rise}$, $D_{fall}$ change nothing and the formula above leaves them out.

> [!question]- Check (slide 18): Look at the five shapes under the formulas. Event X rises 45 Hz in 150 ms and then falls 15 Hz in 50 ms. Event Y is the mirror: it rises 15 Hz in 50 ms and falls 45 Hz in 150 ms. Which shape is each? What does tilt alone not tell you?
> X: $tilt_{amp}=\dfrac{45-15}{45+15}=\dfrac{30}{60}=0.5$ and $tilt_{dur}=\dfrac{150-50}{150+50}=\dfrac{100}{200}=0.5$. So $tilt=\dfrac{0.5+0.5}{2}=0.5$: the second shape, a long rise with a short fall.
> Y: $tilt_{amp}=\dfrac{15-45}{60}=-0.5$ and $tilt_{dur}=\dfrac{50-150}{200}=-0.5$. So $tilt=-0.5$: the fourth shape, a short rise with a long fall.
> Tilt gives only the shape, the balance between rise and fall. It does not give the size. For that the event also needs its total amplitude and total duration (and its position in time).
> These shapes describe pitch accents and boundary tones. The other two event types are connections (straight lines) and silences.

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

> [!question]- Check (slide 19): Name each part of the block diagram from left to right. What do the dashed curve and the solid curve on the right show? Why is there a filter after each command?
> Top left: the phrase commands, drawn as arrows (impulses) of size $A_p$ on a time axis. The first three arrows point up. The last arrow, at the end of the axis, points down: our reading of the figure is a negative phrase command that resets the phrase component at the end of the utterance (the slide does not explain it). Bottom left: the accent commands, drawn as rectangles (steps on, then off) of height $A_a$.
> Middle: each command signal goes through its own second-order linear filter, "phrase control" and "accent control".
> The circle with "+" adds the two filter outputs and the constant $\ln F_b$. The output is $\ln F_0(t)$, the fundamental frequency contour.
> Dashed curve: the base value plus the phrase component only. It is the slow overall trend, and it falls slowly after each impulse. Solid curve: the full contour. It lies on the dashed curve and has a hump above it wherever an accent command is on.
> Why filters: the commands are abrupt (an impulse, a rectangle), but $F_0$ cannot jump. The filters turn them into smooth rises and decays, as the speech organs would (beyond the slides).

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
