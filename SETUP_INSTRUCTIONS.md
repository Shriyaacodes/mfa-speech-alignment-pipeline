# Setup Instructions for Montreal Forced Aligner

These are the exact steps I followed to install and run MFA on Google Colab.

## Prerequisites

- Ubuntu Linux (or Google Colab)
- Internet connection
- Sudo access

## Installation Steps

### 1. Install Python 3.10

MFA does not work with Python 3.12, so we need 3.10.
```bash
sudo apt-get update -qq
sudo apt-get install -y python3.10 python3.10-venv python3.10-dev
```

### 2. Install Miniconda
```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh
bash miniconda.sh -b -p /content/miniconda
export PATH="/content/miniconda/bin:$PATH"
```

### 3. Configure Conda
```bash
conda config --remove channels defaults
conda config --add channels conda-forge
conda config --set channel_priority strict
```

### 4. Create Environment
```bash
conda create -n mfa_conda python=3.10 -y
```

### 5. Install MFA
```bash
conda install -n mfa_conda -c conda-forge montreal-forced-aligner -y
```

Takes 2-3 minutes. Installs MFA and Kaldi dependencies.

### 6. Verify Installation
```bash
conda run -n mfa_conda mfa version
```

Should output: 3.3.9

### 7. Download Models
```bash
conda run -n mfa_conda mfa model download dictionary english_us_arpa
conda run -n mfa_conda mfa model download acoustic english_us_arpa
```

## Data Preparation

Create corpus folder with matching wav and txt files:
```
corpus/
├── file1.wav
├── file1.txt
├── file2.wav
├── file2.txt
```

File names must match exactly.

## Running Alignment
```bash
conda run -n mfa_conda mfa align \
  /path/to/corpus \
  english_us_arpa \
  english_us_arpa \
  /path/to/output
```

## Handling OOV Words

1. Run validation to find OOV words
2. Create oov_pronunciations.txt with ARPA phonemes
3. Merge with base dictionary
4. Re-run alignment with custom dictionary

## Common Issues

**kalpy module not found** - Use conda installation, not pip

**Terms of Service error** - Switch to conda-forge channel

**Python version error** - MFA requires Python 3.10

**OOV words** - Create custom pronunciations using ARPA phoneme set

## Time Estimates

- Installation: 5-10 minutes
- Model downloads: 2-3 minutes
- Alignment (6 files): 2-3 minutes



## Installing and Using Praat (for Analysis)

### Download and Install

1. Go to https://www.fon.hum.uva.nl/praat/
2. Download the version for your operating system (Windows/Mac/Linux)
3. Extract the downloaded file to a folder
4. Run Praat.exe (or Praat application)

**Note:** Windows may show a security warning. Click "More info" then "Run anyway" - Praat is safe software from University of Amsterdam.

### Opening Files in Praat

Once Praat opens, you'll see the "Praat Objects" window.

To view alignment results:

1. Click **Open** → **Read from file...**
2. Navigate to your audio file (e.g., F2BJRLP1.wav) and open it
3. Click **Open** → **Read from file...**  
4. Navigate to the TextGrid file (e.g., F2BJRLP1.TextGrid) and open it
5. Select both files (click first one, hold Ctrl, click second one)
6. Click **View & Edit** button

### Understanding the Visualization

You'll see three main areas:

- **Waveform (top)**: Shows the audio signal amplitude over time
- **Spectrogram (middle)**: Shows frequency content (darker = more energy at that frequency)
- **Annotation tiers (bottom)**:
  - Words tier: Shows word boundaries and labels
  - Phones tier: Shows individual phoneme boundaries

### Zoom to See Details

The full recording is too long to see phoneme details. Zoom in:

1. Click and drag on the waveform to select 5-10 seconds
2. Click the **sel** button at bottom left
3. Now you can see individual phonemes clearly

### What to Look For

**Good alignment:**
- Phoneme boundaries align with visible changes in the spectrogram
- Each phoneme has a clear label (like K, AA, T, etc.)

**Poor alignment (OOV words before handling):**
- Shows "spn" (spoken noise) instead of actual phonemes
- Missing detailed timing information

### Comparing Before and After

Open both BEFORE and AFTER TextGrids with the same audio file to compare:
- Look for proper names (Hennessy, Dukakis, etc.)
- BEFORE: These show as "spn" 
- AFTER: These show detailed phoneme sequences

This visual comparison clearly demonstrates the improvement from OOV handling.
