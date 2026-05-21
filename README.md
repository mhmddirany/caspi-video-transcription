# Hebrew Video Transcription and Transcript Evaluation

This project contains two Google Colab notebooks for Hebrew video transcription and transcript evaluation.

The first notebook transcribes Hebrew videos using Caspi ASR and pyannote speaker diarization.  
The second notebook evaluates and compares transcript quality between different transcription models using WER and CER.

## Project Goal

The goal of this project is to build a complete Hebrew transcription workflow:

1. Transcribe Hebrew video from youtube/audio files.
2. Detect speakers and split the audio into speaker-based chunks.
3. Save clean transcript outputs.
4. Compare transcripts produced by different models using youtube transcript.
6. Evaluate transcript quality using WER and CER.

## Notebooks

### 1. Caspi Video Transcription

```text
notebooks/transcribe_video_using_caspi.ipynb
```

This notebook takes an MP4 video, extracts the audio, detects speakers, splits the audio into speaker-based chunks, transcribes each chunk using Caspi ASR, and saves the final transcript.

Main outputs:

```text
video_name_caspi_transcript.json
video_name_caspi_transcript.pdf
```

The JSON output contains:

- speaker
- start time
- end time
- duration
- transcription

The PDF output contains a clean readable transcript grouped by speaker.

### 2. Hebrew Transcript Model Evaluation

```text
notebooks/hebrew_transcript_model_evaluation.ipynb
```

This notebook compares transcript outputs from different models against a reference transcript.

It calculates:

- WER: Word Error Rate
- CER: Character Error Rate

Lower WER and CER mean better transcript quality.

Main outputs:

```text
SUMMARY_WER_CER.csv
SUMMARY_WER_CER.xlsx
ALL_MODEL_RESULTS.csv
ALL_MODEL_RESULTS.xlsx
```

## Features

- Extract audio from MP4 video
- Detect speakers using pyannote diarization
- Transcribe speech using Caspi ASR
- Split audio into speaker-based chunks
- Export transcript as JSON
- Export transcript as PDF
- Compare transcript results from different models
- Calculate WER and CER
- Save evaluation results as CSV and Excel files
- Designed to run in Google Colab

## Models Used

### Speaker Diarization

```text
pyannote/speaker-diarization-3.1
```

### Speech Recognition

```text
OzLabs/Caspi-1.7B
```

### Models Used for Evaluation

The evaluation notebook can compare models such as:

- Caspi
- Whisper large-v3
- SeamlessM4T
- Ivrit / Hebrew transcription models
- Other transcript models

## Evaluation Metrics

### WER

WER means Word Error Rate.  
It measures transcription mistakes at the word level.

It counts:

- deleted words
- inserted words
- substituted words

### CER

CER means Character Error Rate.  
It measures transcription mistakes at the character level.

CER is useful for Hebrew because small spelling changes can affect the transcript quality.

## Project Structure

```text
Video-Transcription-Hebrew-ASR-Pipeline/

├── README.md
├── requirements.txt
├── .gitignore
└── notebooks/
    ├── transcribe_video_using_caspi.ipynb
    └── hebrew_transcript_model_evaluation.ipynb
```

## How to Use

### Caspi Video Transcription Notebook

1. Open the notebook in Google Colab.
2. Add your Hugging Face token in Colab Secrets as:

```text
HF_TOKEN
```

3. Set your video path:

```python
MP4_PATH = "/content/drive/MyDrive/hebrew/piece_of_hebrew.mp4"
```

4. Set your output folder:

```python
OUTPUT_DIR = "/content/drive/MyDrive/output"
```

5. Run the notebook cells from top to bottom.

### Transcript Evaluation Notebook

1. Open the evaluation notebook in Google Colab.
2. Load the model transcript results.
3. Load the reference transcript.
4. Run the evaluation cells.
5. Check the WER and CER summary tables.

## Requirements

Install dependencies with:

```bash
pip install -r requirements.txt
```

In Colab, the notebooks install the required libraries automatically.

## Notes

- Video files are not uploaded to GitHub.
- Hugging Face tokens are not saved in the notebooks.
- Output PDF, JSON, CSV, and Excel files should not be uploaded unless needed.
- The transcription notebook uses Caspi ASR.
- The evaluation notebook compares transcript quality between models.
- No private data or tokens should be committed to GitHub.

