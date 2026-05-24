# Hebrew Video Transcription and Transcript Evaluation

This project focuses on Hebrew video transcription and transcript evaluation.

The goal is to build a complete workflow that can:

1. Take Hebrew video or audio as input.
2. Extract the audio.
3. Detect speakers when needed.
4. Split the audio into smaller parts.
5. Transcribe the speech using different transcription models.
6. Save clean transcript outputs.
7. Compare different model transcripts against a reference transcript.
8. Evaluate the quality using WER and CER.

This project was mainly developed and tested in Google Colab.

---

## Project Overview

Hebrew transcription is not always easy because Hebrew audio can be very different depending on the source.

Some audio is clean, short, and recorded in a studio.  
Other audio is noisy, spontaneous, spoken by many speakers, or taken from real videos.

Because of this, the project tests transcription models on different types of Hebrew data:

- a larger Hebrew dataset loaded from Arrow files,
- the SASpeech Hebrew dataset,
- a Hebrew YouTube video with subtitles,
- and a complete video transcription pipeline using Caspi and speaker diarization.

The project does not only create transcripts.  
It also measures how accurate the transcripts are.

---

## Main Idea

A transcription model receives audio as input and produces text as output.

```text
Input  → Hebrew audio
Output → Hebrew transcript
```

To know if the model is good, we compare its output with a reference transcript.

The reference transcript is the correct or expected text.

Then we calculate:

- WER: Word Error Rate
- CER: Character Error Rate

Lower WER and CER mean better transcription quality.

---

## Project Structure

```text
Video-Transcription-Hebrew-ASR-Pipeline/

├── README.md
├── requirements.txt
├── .gitignore
└── notebooks/
    ├── video_transcription_pipeline.ipynb
    ├── dataset1_transcription_model_evaluation.ipynb
    ├── dataset2_transcription_model_evaluation.ipynb
    └── youtube_hebrew_transcription_model_evaluation.ipynb
```

---

# Notebooks

## 1. Video Transcription Pipeline

```text
notebooks/video_transcription_pipeline.ipynb
```

This is the main transcription pipeline.

It takes a video file, extracts the audio, detects speakers, splits the audio into speaker-based chunks, transcribes each chunk, and saves the final transcript.

This notebook is useful when we want to transcribe a full video and produce a clean readable transcript.

### Main steps

1. Load the video file.
2. Extract audio from the video.
3. Convert the audio to a format suitable for transcription.
4. Run speaker diarization.
5. Split the audio according to speaker turns.
6. Transcribe each audio chunk.
7. Save the transcript as JSON.
8. Save the transcript as PDF.

### Main outputs

```text
video_name_caspi_transcript.json
video_name_caspi_transcript.pdf
```

### JSON output contains

Each transcript segment contains:

```text
speaker
start time
end time
duration
transcription
```

### PDF output

The PDF contains a clean readable transcript.  
The transcript is grouped by speaker so it is easier to read.

---

## 2. Dataset 1 Transcription Model Evaluation

```text
notebooks/dataset1_transcription_model_evaluation.ipynb
```

This notebook evaluates transcription models on the first Hebrew dataset.

The dataset is loaded from Arrow shards.  
Each audio clip is transcribed individually.  
The model transcript is then compared with the reference transcript.

The notebook calculates:

- WER
- CER
- average RTF

---

## 3. Dataset 2 Transcription Model Evaluation

```text
notebooks/dataset2_transcription_model_evaluation.ipynb
```

This notebook evaluates transcription models on the SASpeech Hebrew dataset.

SASpeech is cleaner than the first dataset.  
It is a studio-quality Hebrew dataset with one speaker.

This notebook helps us compare how models behave on easier and cleaner Hebrew audio.

---

## 4. YouTube Hebrew Transcript Evaluation

```text
notebooks/youtube_hebrew_transcription_model_evaluation.ipynb
```

This notebook evaluates transcription models on a Hebrew YouTube video.

The YouTube video used was:

```text
Advanced HEBREW Conversation With Subtitles // Learn Hebrew vocabulary about health & environment
```

The audio was split into chunks of 30 seconds or less.  
Each model transcribed the same chunks.  
The output was compared with the YouTube subtitle/reference transcript.

---

# Transcription Pipeline

The transcription pipeline is used to create a transcript from a video.

## Input

The input is an MP4 video file.

Example:

```python
MP4_PATH = "/content/drive/MyDrive/hebrew/video.mp4"
```

## Audio extraction

The video audio is extracted and converted to a suitable format.

Usually the audio is converted to:

```text
16,000 Hz
mono
float32
```

This format is commonly used by transcription models.

## Speaker diarization

Speaker diarization means detecting who spoke and when.

Instead of producing one long transcript, diarization helps us split the audio by speaker.

Example:

```text
Speaker 1: 00:00 → 00:12
Speaker 2: 00:12 → 00:25
Speaker 1: 00:25 → 00:40
```

This is useful for videos with more than one person.

## Diarization models used

Two pyannote diarization models were tested:

```text
pyannote/speaker-diarization-3.1
pyannote/speaker-diarization-community-1
```

## Transcription

After diarization, each speaker segment is transcribed.

The transcription model receives an audio chunk and returns text.

```text
Audio chunk → transcription model → Hebrew text
```

## Output formats

The transcript is saved in two main formats:

### JSON

The JSON output is useful for code, evaluation, and later processing.

It contains structured information such as speaker, start time, end time, duration, and transcription.

### PDF

The PDF output is useful for reading.

It gives a clean transcript that can be opened, shared, or reviewed easily.

---

# Evaluation Metrics

The project uses two main metrics:

1. WER
2. CER

Both are used to compare the model transcript with the reference transcript.

---

## WER: Word Error Rate

WER means Word Error Rate.

It measures mistakes at the word level.

WER checks how many word mistakes the model made compared to the reference transcript.

### WER counts three types of errors

#### 1. Substitution

A substitution happens when the model writes the wrong word.

Example:

```text
Reference: אני אוהב עברית
Prediction: אני לומד עברית
```

The word `אוהב` was replaced by `לומד`.

This is a substitution.

#### 2. Deletion

A deletion happens when the model misses a word.

Example:

```text
Reference: אני אוהב ללמוד עברית
Prediction: אני אוהב עברית
```

The word `ללמוד` is missing.

This is a deletion.

#### 3. Insertion

An insertion happens when the model adds an extra word.

Example:

```text
Reference: אני אוהב עברית
Prediction: אני מאוד אוהב עברית
```

The word `מאוד` was added.

This is an insertion.

## WER formula

```text
WER = (Substitutions + Deletions + Insertions) / Number of words in the reference
```

A lower WER means the transcript is better.

Example:

```text
WER = 0.10
WER = 10%
```

This means around 10% word error rate.

---

## CER: Character Error Rate

CER means Character Error Rate.

It measures mistakes at the character level.

CER is similar to WER, but instead of checking full words, it checks characters.

This is useful for Hebrew because small spelling differences can affect the quality of the transcript.

## CER formula

```text
CER = (Character substitutions + Character deletions + Character insertions) / Number of characters in the reference
```

A lower CER means the predicted transcript is closer to the reference transcript.

---

## Difference Between WER and CER

WER checks errors word by word.

CER checks errors character by character.

| Metric | Level | What it measures | Useful for |
|---|---|---|---|
| WER | Word level | Wrong, missing, or extra words | Overall transcript quality |
| CER | Character level | Spelling and character mistakes | Hebrew spelling accuracy |

Both are important.

WER tells us if the model understood the words correctly.  
CER tells us if the model wrote the words correctly at the spelling level.

---

# Hebrew Text Normalization

Before calculating WER and CER, Hebrew text was normalized.

This is important because small writing differences can make the score worse, even if the transcript is mostly correct.

Normalization included:

- removing nikud,
- normalizing final Hebrew letters,
- removing punctuation,
- cleaning extra spaces.

## Why normalization is important

Hebrew can have different written forms.

For example, a transcript may contain punctuation, nikud, or final letter differences.

If these are not normalized, the evaluation may count them as errors even when the transcription is acceptable.

Normalization makes the comparison fairer.

---

# RTF: Real-Time Factor

RTF means Real-Time Factor.

It measures how fast the model transcribes audio.

```text
RTF = transcription time / audio duration
```

Example:

```text
RTF = 0.5x
```

This means the model takes 0.5 seconds to transcribe 1 second of audio.

Lower RTF means faster transcription.

Example:

```text
0.185x is faster than 0.562x
```

---

# Experiments and Results

---

## Experiment 1: First Hebrew Dataset

### Dataset source

```text
/content/drive/MyDrive/hebrewasr/dataset
```

The dataset was loaded from 3 Arrow shards.

### Full dataset size

```text
9,360 clips
451.4 minutes
7.52 hours
```

### Used for scoring

```text
610 clips
30.0 minutes
```

Only part of the full dataset was used for scoring.

---

## How Dataset 1 Transcription Was Done

The transcription was done clip by clip.

Each audio clip was transcribed individually.

### Audio format

```text
float32 numpy arrays
16,000 Hz
mono
```

### Whisper and Ivrit models

Whisper and Ivrit models were run using Hugging Face:

```python
pipeline("automatic-speech-recognition")
```

Main settings:

```text
chunk_length_s = 30
stride_length_s = 5
forced language = he
```

### Caspi and Qwen3-ASR

Caspi and Qwen3-ASR were run using:

```python
Qwen3ASRModel.transcribe()
```

from the `qwen_asr` package.

### Scoring

WER and CER were computed using:

```text
jiwer
```

The text was normalized before scoring.

### Checkpointing

Results were saved to Google Drive every 50 clips.

This prevents losing progress if Colab disconnects.

---

## Dataset 1 Results

| Model | Hugging Face ID | WER % | CER % | Avg RTF |
|---|---|---:|---:|---:|
| Whisper | openai/whisper-large-v3 | 25.11% | 14.45% | 0.562x |
| Ivrit | ivrit-ai/whisper-large-v3-turbo | 22.93% | 14.15% | 0.185x |
| Caspi | OzLabs/Caspi-1.7B | 25.02% | 12.21% | 0.419x |
| Qwen3-ASR | Qwen/Qwen3-ASR-1.7B | ❌ | ❌ | ❌ |

---

## Dataset 1 Conclusion

On the first dataset, Ivrit had the best WER.

```text
Ivrit WER = 22.93%
```

This means Ivrit made the fewest word-level mistakes.

Caspi had the best CER.

```text
Caspi CER = 12.21%
```

This means Caspi made fewer character-level mistakes.

Whisper large-v3 performed well, but it was weaker than Ivrit in WER and weaker than Caspi in CER.

Qwen3-ASR was dropped because Hebrew was not in the supported language list.

---

# Experiment 2: SASpeech Dataset

## Dataset information

Dataset name:

```text
SASpeech / saspeech_gold_standard
```

Links:

```text
https://huggingface.co/datasets/upai-inc/saspeech
https://www.openslr.org/134
```

SASpeech is a Hebrew speech dataset.

It contains around 30 hours of single-speaker Hebrew audio.

The gold-standard subset contains about 4 hours with manually tagged transcriptions.

The audio is sampled at:

```text
44,100 Hz
```

The audio is divided into utterances of up to 14 seconds.

The transcriptions were created by manual correction of Whisper large-v2 output.

---

## Used for scoring

```text
100 samples
8.1 minutes of audio
```

---

## How SASpeech Transcription Was Done

The transcription was done clip by clip.

The clips were loaded from WAV files using `metadata.csv`.

The transcription models were forced to use Hebrew:

```text
forced language = he
```

Before WER and CER scoring, Hebrew normalization was applied.

This included nikud stripping and other cleanup steps.

---

## SASpeech Results

| Model | WER % | CER % |
|---|---:|---:|
| Whisper large-v3 | 15.12 | 7.06 |
| SeamlessM4T v2 large | 17.83 | 8.67 |
| ivrit-ai/whisper-large-v3 | 11.24 | 5.73 |
| Caspi / OzLabs/Caspi-1.7B | 14.50 | 6.90 |

---

## SASpeech Conclusion

The best model on SASpeech was:

```text
ivrit-ai/whisper-large-v3
```

It achieved:

```text
WER = 11.24%
CER = 5.73%
```

This means it had the lowest word-level and character-level error rates.

Caspi also performed well.

Caspi beat Whisper large-v3 on both WER and CER even though Caspi is a smaller model.

Whisper large-v3 was solid, but it was outperformed by Hebrew-specific models.

SeamlessM4T v2 large had the weakest score in this experiment.

---

# Why Whisper Performed Better on SASpeech Than Dataset 1

Whisper performed much better on SASpeech than on the first dataset.

This happened because SASpeech is easier audio.

## SASpeech is easier audio

SASpeech is:

- recorded in a professional studio,
- single speaker,
- clean audio,
- no background noise,
- no interruptions,
- consistent microphone,
- consistent volume,
- short clips,
- up to 14 seconds per utterance.

This makes transcription easier.

## Dataset 1 is harder audio

The first dataset is harder because it is crowd-sourced.

It contains:

- many different speakers,
- different microphones,
- different environments,
- spontaneous speech,
- hesitation,
- interruptions,
- slang,
- informal Hebrew,
- different accents,
- different speaking styles.

This makes transcription harder.

## Whisper comparison

| Dataset | Type | Whisper WER |
|---|---|---:|
| Dataset 1 / hebrewasr | Crowd, spontaneous | 25.11% |
| Dataset 2 / SASpeech | Studio, single speaker | 15.12% |

Whisper improved by about 10% WER on SASpeech.

This difference is explained by the audio quality and speech style.

SASpeech is closer to a best-case scenario.

Real-world Hebrew transcription is usually closer to the first dataset because real audio is often noisy, spontaneous, and spoken by different people.

---

# Experiment 3: YouTube Video Transcript Evaluation

## Video

The evaluated video was:

```text
Advanced HEBREW Conversation With Subtitles // Learn Hebrew vocabulary about health & environment
```

This video contains Hebrew conversation with subtitles.

The subtitles were used as the reference transcript.

---

## How the YouTube Evaluation Was Done

The audio was split into chunks.

The chunk size was:

```text
30 seconds or less
```

Each model transcribed the same chunks.

Then the transcript of each model was compared with the reference subtitle transcript.

The final score was calculated using WER and CER.

---

## YouTube Evaluation Final Ranking

| Rank | Model | WER | CER |
|---:|---|---:|---:|
| 1 | ivrit_ai_whisper_large_v3 | 0.1196 | 0.0852 |
| 2 | ivrit_ai_whisper_large_v3_ct2 | 0.1225 | 0.0877 |
| 3 | ivrit_ai_whisper_large_v3_turbo_ct2 | 0.1250 | 0.0878 |
| 4 | ivrit_ai_whisper_large_v3_turbo | 0.1260 | 0.0887 |
| 5 | caspi_1_7b | 0.1427 | 0.0928 |
| 6 | openai_whisper_large_v3_turbo | 0.1450 | 0.0914 |
| 7 | openai_whisper_large_v3 | 0.1459 | 0.0926 |

---

## YouTube Evaluation Conclusion

The best model was:

```text
ivrit_ai_whisper_large_v3
```

It achieved the lowest WER and CER.

Caspi-1.7B worked successfully.

Caspi performed better than the OpenAI Whisper models in WER.

However, Caspi was still weaker than all ivrit.ai models.

The ivrit.ai models were the strongest models in this YouTube evaluation.

---

# Models Used

## Transcription models

The tested transcription models include:

```text
openai/whisper-large-v3
openai/whisper-large-v3-turbo
ivrit-ai/whisper-large-v3
ivrit-ai/whisper-large-v3-turbo
OzLabs/Caspi-1.7B
facebook/seamless-m4t-v2-large
Qwen/Qwen3-ASR-1.7B
```

## Diarization models

The tested diarization models include:

```text
pyannote/speaker-diarization-3.1
pyannote/speaker-diarization-community-1
```

---

# General Conclusions

The strongest model overall was usually the ivrit.ai Hebrew transcription model.

It performed best on:

- SASpeech,
- YouTube Hebrew video evaluation,
- and Dataset 1 WER.

Caspi also performed well.

Caspi was especially strong in character-level accuracy on Dataset 1.

Whisper large-v3 was a good general model, but it was not the best model for Hebrew in these experiments.

SeamlessM4T was tested, but it was weaker than the Hebrew-specific models in the SASpeech experiment.

Qwen3-ASR was not included in the final Hebrew evaluation because Hebrew was not supported.

---

# Important Observations

## 1. Dataset quality changes the results

Clean studio audio gives better scores.

Noisy or spontaneous real-world audio gives worse scores.

This is why the same model can perform very differently on two datasets.

## 2. Hebrew-specific models performed better

Models trained or adapted specifically for Hebrew performed better than general multilingual models.

This was clear with the ivrit.ai models.

## 3. WER and CER give different information

WER tells us about word-level correctness.

CER tells us about spelling and character-level correctness.

A model can have a better WER but a worse CER, or the opposite.

That is why both metrics are useful.

## 4. Real-world transcription is harder

Real-world videos usually include:

- noise,
- interruptions,
- overlapping speech,
- different speakers,
- informal speech,
- slang,
- bad microphones.

So real-world results are usually worse than clean dataset results.

---

# How to Use

## 1. Clone the repository

```bash
git clone https://github.com/mhmddirany/Video-Transcription-Hebrew-ASR-Pipeline.git
cd Video-Transcription-Hebrew-ASR-Pipeline
```

## 2. Install requirements

```bash
pip install -r requirements.txt
```

In Google Colab, the notebooks install most required libraries automatically.

## 3. Add Hugging Face token

Some models require a Hugging Face token.

In Colab, add the token in:

```text
Colab Secrets
```

Use the name:

```text
HF_TOKEN
```

Do not write the token directly inside the notebook.

## 4. Run the video transcription notebook

Open:

```text
notebooks/video_transcription_pipeline.ipynb
```

Set the video path:

```python
MP4_PATH = "/content/drive/MyDrive/hebrew/video.mp4"
```

Set the output folder:

```python
OUTPUT_DIR = "/content/drive/MyDrive/output"
```

Then run the notebook cells from top to bottom.

## 5. Run the evaluation notebooks

Open one of the evaluation notebooks:

```text
notebooks/dataset1_transcription_model_evaluation.ipynb
notebooks/dataset2_transcription_model_evaluation.ipynb
notebooks/youtube_hebrew_transcription_model_evaluation.ipynb
```

Then:

1. Load the audio or transcript data.
2. Load the model results.
3. Load the reference transcript.
4. Normalize the Hebrew text.
5. Calculate WER and CER.
6. Save the results.

---

# Output Files

The project can generate several output files.

## Transcript outputs

```text
.json
.pdf
```

JSON is useful for structured data.  
PDF is useful for reading the transcript.

## Evaluation outputs

```text
.csv
.xlsx
```

CSV and Excel files are useful for comparing model results in tables.

Example outputs:

```text
SUMMARY_WER_CER.csv
SUMMARY_WER_CER.xlsx
ALL_MODEL_RESULTS.csv
ALL_MODEL_RESULTS.xlsx
```

---

# Notes

- Video files are not uploaded to GitHub.
- Hugging Face tokens are not saved in the notebooks.
- Large output files should not be committed unless needed.
- The notebooks are designed mainly for Google Colab.
- The results may change depending on GPU, package versions, and preprocessing.
- WER and CER depend strongly on text normalization.
- Clean audio usually gives better results than real-world spontaneous audio.

---

# Final Summary

This project builds and evaluates a Hebrew transcription workflow.

It includes:

- video transcription,
- audio extraction,
- speaker diarization,
- Hebrew transcription,
- transcript export,
- model comparison,
- WER and CER evaluation,
- and result saving.

The experiments showed that Hebrew-specific models, especially ivrit.ai models, gave the best overall transcription quality.

Caspi also performed well, especially compared with general Whisper models.

The results also showed that dataset type matters a lot.  
Clean studio audio gives much better results than spontaneous real-world audio.
