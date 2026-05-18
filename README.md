# Caspi Video Transcription

This project transcribes video files using Caspi ASR and pyannote speaker diarization.

The pipeline takes an MP4 video, extracts the audio, detects speakers, splits the audio into speaker-based chunks, transcribes each chunk, and saves the final transcript as both JSON and PDF.

## Features

- Extract audio from MP4 video
- Detect speakers using pyannote diarization
- Transcribe speech using Caspi ASR
- Split audio into speaker-based chunks
- Export transcript as JSON
- Export transcript as PDF
- Designed to run in Google Colab
- No Whisper is used

## Project Structure

caspi-video-transcription/

- notebooks/transcribe_video_using_caspi.ipynb
- requirements.txt
- .gitignore
- README.md

## Models Used

Speaker diarization:

pyannote/speaker-diarization-3.1

Speech recognition:

OzLabs/Caspi-1.7B

## Outputs

The notebook creates two files:

- video_name_caspi_transcript.json
- video_name_caspi_transcript.pdf

The JSON output contains speaker, start time, end time, duration, and transcription.

The PDF output contains a clean readable transcript grouped by speaker.

## How to Use

1. Open the notebook in Google Colab.
2. Add your Hugging Face token in Colab Secrets as HF_TOKEN.
3. Set your video path:

MP4_PATH = "/content/drive/MyDrive/hebrew/piece_of_hebrew.mp4"

4. Set your output folder:

OUTPUT_DIR = "/content/drive/MyDrive/output"

5. Run the notebook cells from top to bottom.

## Requirements

Install dependencies with:

pip install -r requirements.txt

In Colab, the notebook installs the required libraries automatically.

## Notes

- Video files are not uploaded to GitHub.
- Hugging Face tokens are not saved in the notebook.
- Output PDF and JSON files are ignored by Git.
- This project uses Caspi, not Whisper.
