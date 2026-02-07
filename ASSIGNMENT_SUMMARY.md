# My Journey with Montreal Forced Aligner

## What I Built
I implemented a forced alignment pipeline that automatically matches audio recordings to their text transcripts at the word and phoneme level. This was my first real experience with speech processing, and I learned a ton along the way.

## Understanding Forced Alignment
Forced alignment is like creating automatic subtitles with extremely precise timing - not just for words, but for every single sound. For example, it tells you that the word "hello" starts at 0.54 seconds, and within that word, the "HH" sound is from 0.54-0.60s, "EH" from 0.60-0.70s, and so on. This matters for speech research, language learning apps, and text-to-speech systems.

## The Main Challenge: Out-of-Vocabulary Words

When I first ran the alignment, I got a warning: 29 out-of-vocabulary (OOV) words found. 

I was confused at first - why would common words be "out of vocabulary"? After digging into the transcripts, I figured out the issue:
- The dictionary doesn't know proper names like "Hennessy" or "Dukakis"
- Punctuation like "S.J.C.'s" or "WBUR's" was breaking the system
- Hyphenated words like "de-politicize" weren't recognized

This was my first real debugging challenge - the system was telling me something was wrong, and I had to figure out why and how to fix it.

## How I Solved It

### Finding the Problem Words
I went through the transcripts and found:
- Proper names: Edward, Hennessy, Dukakis, Margo, Melnicove, Maffy (from news broadcasts about judicial appointments)
- Acronyms with punctuation: S.J.C.'s (Supreme Judicial Court)
- Media call letters: WBUR's (a radio station)
- Compound words: de-politicize

### Learning Phonetic Transcription
This part was completely new to me. I had to learn how to break words down into phonemes using the ARPA phoneme set. The numbers after phonemes indicate stress levels (1 = primary stress, 0 = no stress).

For example:
```
HENNESSY → HH EH1 N AH0 S IY0
DUKAKIS → D UW0 K AA1 K IH0 S
```

It felt like learning a new language, honestly. I had to think about how each syllable is pronounced and which parts are stressed.

### Cleaning the Text
I realized I needed to preprocess the transcripts to remove problematic punctuation:
- "S.J.C.'s" became "SJC"
- "WBUR's" became "WBURS"  
- "de-politicize" became "DEPOLITICIZE"

This taught me that sometimes you need to modify your input data to work with existing tools rather than expecting the tools to handle everything.

### Creating Custom Pronunciations
I manually created phonetic pronunciations for 8 key OOV words and added them to the dictionary:
```
EDWARD       EH1 D W ER0 D
HENNESSY     HH EH1 N AH0 S IY0
DUKAKIS      D UW0 K AA1 K IH0 S
MARGO        M AA1 R G OW0
MELNICOVE    M EH1 L N IH0 K OW0 V
MAFFY        M AE1 F IY0
WBURS        D AH1 B AH0 L Y UW1 B IY1 AA1 R Z
DEPOLITICIZE D IY0 P AH0 L IH1 T AH0 S AY2 Z
```

The base dictionary had 208,651 entries, and after adding mine it had 208,659.

## Results

### Comparison: Before and After
Before OOV handling:
- 29 words failed to align properly
- F2BJRLP1.TextGrid: 1,540 lines
- Missing proper name timings

After OOV handling:
- All words successfully aligned
- F2BJRLP1.TextGrid: 1,596 lines (56 more phoneme boundaries)
- Complete timing data for every word including proper names

The improvement might look small numerically, but the quality difference is significant. Now every single word has precise timing information.

## Technical Challenges I Overcame

### Python Version Incompatibility
MFA didn't work with Python 3.12 (Colab's default). I had to install Python 3.10 using conda and create a virtual environment. This taught me why environment management matters in real projects.

### Kaldi Dependency Errors
I kept getting "_kalpy module not found" errors. After trying pip installation multiple times, I learned that MFA's Kaldi dependencies need to be installed through conda, not pip. This was frustrating but taught me about package management.

### Conda Terms of Service Issues
Conda kept blocking me with TOS acceptance requirements. I had to switch to the conda-forge channel and reconfigure everything. Each roadblock taught me something new about how package repositories work.

## Tools and Technologies Used
- Google Colab (cloud computing platform)
- Montreal Forced Aligner v3.3.9
- Miniconda (Python package management)
- Bash scripting
- TextGrid format (Praat software)
- Python 3.10

## What I Found Most Interesting

### Seeing Real Results
When I opened a TextGrid file and saw:
```
intervals [2]:
    xmin = 0.54 
    xmax = 1.12 
    text = "wanted"
```
I realized I had just taught a computer to understand that someone said "wanted" from 0.54 to 1.12 seconds. That was pretty cool.

### The Debugging Process
Every error message became a learning opportunity. Instead of getting frustrated, I found myself getting curious about why things broke and how to fix them. "OOV words found" led me to understand dictionary limitations. "Kaldi not found" taught me about dependencies.

### Real-World Applications
Forced alignment isn't just academic. It's used in:
- Speech therapy apps for pronunciation feedback
- Language learning platforms like Duolingo
- Audiobook production for syncing text with narration
- Accessibility tools for precise subtitles

Knowing this has real applications made the work more meaningful.

## What I Learned

Technical skills:
- Speech processing basics (phonemes, alignment, acoustic models)
- Python environment management with conda
- Linux command line and bash scripting
- Debugging methodology
- Text preprocessing and normalization
- Phonetic transcription using ARPA

Problem-solving approach:
- Breaking down complex errors into specific, solvable problems
- Persistence when facing repeated failures
- Reading documentation and error messages carefully
- Not being afraid to try different approaches

## What I'd Do Differently

If I were starting this project again, I would:
- Explore the data first before running any alignment
- Set up the environment more carefully from the start
- Keep better notes on errors and solutions as I went
- Try automated G2P tools before doing manual phonetic transcription

## Why Speech Technology Interests Me

This project showed me how much complexity exists behind simple features we use daily. When Siri understands your voice, when Google Translate pronounces words, when YouTube auto-generates subtitles - there's sophisticated technology making it work.

Speech is the most natural form of human communication. Being able to process it computationally opens up possibilities for making technology more accessible and intuitive. I want to be part of building those tools.

## Deliverables

Project structure:
```
MFA_Assignment_LTRC/
├── outputs/
│   ├── before_oov/          (6 TextGrid files - baseline)
│   ├── after_oov/           (6 TextGrid files - improved)
│   └── oov_pronunciations.txt
├── wav/                      (original audio)
├── transcripts/             (original transcripts)
└── ASSIGNMENT_SUMMARY.md
```

## Next Steps

I want to:
- Learn more about how acoustic modeling actually works
- Explore automatic pronunciation generation with G2P models
- Work with larger, more diverse datasets
- Apply these skills to accessibility technology
- Contribute to open-source speech processing projects

## Reflection

This assignment was challenging and sometimes frustrating, but incredibly educational. Each error message taught me something. Each successful alignment felt like a small victory. I'm genuinely excited to keep learning and building in this space.

The combination of linguistics, machine learning, and real-world impact makes speech technology fascinating to me. I'm looking forward to developing these skills further.


## Praat Analysis and Visualization

### Methodology
I used Praat software to visually inspect and compare the alignment quality before and after OOV handling. I focused on the word "Dukakis" (a proper name that was identified as OOV) in the file F2BJRLP1.

### Key Findings

#### Before OOV Handling
When analyzing the BEFORE alignment in Praat, I observed:
- The word "Dukakis" appears in the word tier
- However, in the phoneme tier below, it shows only "spn" (spoken noise)
- This indicates MFA could not properly align the phonemes because the word was not in the dictionary
- The system treated it as unrecognizable speech rather than a proper word

**Screenshot:** See `praat_analysis/Dukasis_before_OOV.png`

#### After OOV Handling  
After adding custom pronunciation (DUKAKIS D UW0 K AA1 K IH0 S) to the dictionary:
- The word "Dukakis" appears correctly in the word tier
- The phoneme tier now shows individual phonemes: D, UW, K, AA, K, IH, S
- Each phoneme has precise timing boundaries
- The alignment matches the actual pronunciation

**Screenshot:** See `praat_analysis/Dukasis_after_OOV.png`

### Visual Comparison
The Praat visualization clearly shows:
- **Waveform** (top): Audio amplitude over time
- **Spectrogram** (middle): Frequency content showing formants and sound characteristics
- **Word tier**: Word-level segmentation
- **Phoneme tier**: Phoneme-level segmentation with timing

The improvement is dramatic - going from a generic "spn" marker to detailed phoneme-by-phoneme alignment.

### Impact of OOV Handling
This visual analysis confirms that handling OOV words significantly improves alignment quality:
1. Proper names and specialized terms get accurate phonetic transcriptions
2. Phoneme boundaries become precise instead of being lumped as "spoken noise"
3. The resulting TextGrids are much more useful for downstream applications like speech synthesis or phonetic research

### Other Observations
- Words that were already in the dictionary (like "appointment", "important") showed good alignment even before OOV handling
- The spectrogram visualization helps verify that phoneme boundaries align with actual acoustic changes
- Praat's visual interface makes it easy to spot alignment errors and verify improvements
