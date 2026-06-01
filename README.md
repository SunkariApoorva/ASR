# 🎙️ Automatic Speech Recognition (ASR) Pipeline using OpenAI Whisper

## Overview

This repository provides a complete, modular, and production-ready Automatic Speech Recognition (ASR) pipeline built using OpenAI Whisper. The pipeline is designed to convert raw audio recordings into clean, normalized, and tokenized text suitable for downstream Natural Language Processing (NLP), Large Language Models (LLMs), analytics, search indexing, and conversational AI applications.

The system follows a multi-stage architecture:

1. Audio Ingestion
2. Audio Preprocessing
3. Audio Cleaning & Enhancement
4. Speech Recognition using Whisper
5. Text Normalization
6. Text Tokenization
7. Structured Output Generation

Each stage is implemented as an independent module, enabling flexibility, scalability, and maintainability.

---

# Table of Contents

- Introduction
- System Architecture
- Project Structure
- Pipeline Workflow
- Module Details
  - audio.py
  - audio_clean.py
  - normaliser.py
  - tokeniser.py
- Whisper Integration
- Installation Guide
- Environment Setup
- Input Audio Requirements
- End-to-End Processing Flow
- Usage Examples
- Configuration Options
- Performance Optimization
- Error Handling
- Logging and Monitoring
- Output Formats
- Testing
- Deployment Recommendations
- Future Enhancements
- License

---

# Introduction

Speech is one of the most natural forms of human communication. Automatic Speech Recognition (ASR) systems convert spoken language into machine-readable text, enabling applications such as:

- Voice Assistants
- Meeting Transcription
- Call Center Analytics
- Voice Search
- Accessibility Tools
- Subtitle Generation
- Speech-to-Text APIs
- Conversational AI Systems

This project leverages OpenAI Whisper, a state-of-the-art multilingual speech recognition model trained on hundreds of thousands of hours of audio data.

The repository extends Whisper's capabilities by introducing:

- Audio quality enhancement
- Noise reduction
- Standardized text normalization
- Flexible tokenization
- Modular architecture

---

# System Architecture

```text
                     ┌─────────────────┐
                     │   Audio Input   │
                     └────────┬────────┘
                              │
                              ▼
                   ┌────────────────────┐
                   │      audio.py      │
                   │ Audio Processing   │
                   └────────┬───────────┘
                            │
                            ▼
                ┌──────────────────────────┐
                │     audio_clean.py       │
                │ Noise Reduction Module   │
                └──────────┬───────────────┘
                           │
                           ▼
                ┌──────────────────────────┐
                │      Whisper ASR         │
                │ Speech Recognition       │
                └──────────┬───────────────┘
                           │
                           ▼
                ┌──────────────────────────┐
                │      normaliser.py       │
                │ Text Normalization       │
                └──────────┬───────────────┘
                           │
                           ▼
                ┌──────────────────────────┐
                │      tokeniser.py        │
                │ Text Tokenization        │
                └──────────┬───────────────┘
                           │
                           ▼
                   ┌─────────────────┐
                   │ Final Output    │
                   └─────────────────┘
```

---

# Project Structure

```text
asr-whisper/
│
├── audio.py
├── audio_clean.py
├── normaliser.py
├── tokeniser.py
├── transcribe.py
│
├── configs/
│   ├── model_config.yaml
│   └── audio_config.yaml
│
├── data/
│   ├── raw/
│   ├── processed/
│   └── outputs/
│
├── tests/
│   ├── test_audio.py
│   ├── test_cleaner.py
│   ├── test_normaliser.py
│   └── test_tokeniser.py
│
├── logs/
│
├── requirements.txt
├── README.md
└── LICENSE
```

---

# Pipeline Workflow

The ASR pipeline consists of five major stages.

## Stage 1: Audio Loading

The audio file is loaded into memory.

Supported formats:

- WAV
- MP3
- FLAC
- OGG
- M4A
- AAC

Tasks performed:

- File validation
- Audio decoding
- Sample rate detection
- Channel detection
- Format conversion

Output:

```python
audio_array
sample_rate
```

---

## Stage 2: Audio Cleaning

Raw recordings frequently contain:

- Background conversations
- Fan noise
- Road traffic
- Keyboard sounds
- Echo
- Silence

The cleaning stage improves signal quality.

Tasks:

- Noise reduction
- Silence trimming
- Volume normalization
- Dynamic range correction

Benefits:

- Improved transcription accuracy
- Reduced hallucinations
- Better language detection

---

## Stage 3: Speech Recognition

The cleaned audio is passed to Whisper.

Whisper performs:

- Speech-to-text conversion
- Language identification
- Context modeling
- Timestamp generation

Output:

```json
{
  "text": "Hello world",
  "language": "en"
}
```

---

## Stage 4: Text Normalization

Whisper output often requires cleanup.

Normalization performs:

### Lowercasing

Before:

```text
HELLO WORLD
```

After:

```text
hello world
```

### Punctuation Standardization

Before:

```text
Hello,,, world!!!
```

After:

```text
hello world
```

### Whitespace Cleanup

Before:

```text
hello     world
```

After:

```text
hello world
```

---

## Stage 5: Tokenization

Normalized text is converted into structured tokens.

Example:

Input:

```text
hello world
```

Output:

```python
["hello", "world"]
```

Applications:

- NLP
- Search
- Classification
- LLM Fine-Tuning
- RAG Systems

---

# Module Documentation

# audio.py

## Purpose

Responsible for audio ingestion and preprocessing.

---

## Responsibilities

### Audio Loading

```python
audio, sr = load_audio("speech.wav")
```

### Format Conversion

Converts:

```text
MP3 → WAV
FLAC → WAV
```

internally.

---

### Channel Normalization

Stereo:

```text
Left Channel
Right Channel
```

Converted to:

```text
Mono
```

---

### Sample Rate Resampling

Input:

```text
44100 Hz
```

Output:

```text
16000 Hz
```

Whisper's preferred sample rate.

---

## Example

```python
from audio import load_audio

audio, sr = load_audio("sample.wav")
```

---

# audio_clean.py

## Purpose

Improves audio quality before transcription.

---

## Features

### Noise Reduction

Removes:

- White noise
- Background hum
- Air conditioning noise
- Traffic noise

Example:

```python
clean_audio(audio)
```

---

### Silence Removal

Removes:

```text
[5 seconds silence]
```

at start/end.

---

### Volume Normalization

Ensures consistent loudness.

Before:

```text
Very quiet recording
```

After:

```text
Normalized volume
```

---

## Example

```python
from audio_clean import clean_audio

cleaned = clean_audio(audio, sr)
```

---

# normaliser.py

## Purpose

Standardizes ASR output.

---

## Features

### Case Normalization

```python
HELLO
```

↓

```python
hello
```

---

### Space Normalization

```python
hello     world
```

↓

```python
hello world
```

---

### Symbol Cleanup

```python
hello!!!???
```

↓

```python
hello
```

---

## Example

```python
from normaliser import normalize_text

text = normalize_text(raw_text)
```

---

# tokeniser.py

## Purpose

Converts normalized text into tokens.

---

## Token Types

### Word Tokens

```text
hello world
```

↓

```python
['hello', 'world']
```

---

### Sentence Tokens

```text
Hello world. Welcome.
```

↓

```python
[
  'Hello world.',
  'Welcome.'
]
```

---

## Example

```python
from tokeniser import tokenize

tokens = tokenize(text)
```

---

# Whisper Integration

## Why Whisper?

Whisper offers:

- High accuracy
- Multilingual support
- Robust noise handling
- Open-source availability
- Timestamp support

---

## Supported Models

| Model | Parameters | VRAM | Speed | Accuracy |
|---------|------------|---------|----------|----------|
| tiny | 39M | 1 GB | Very Fast | Low |
| base | 74M | 1 GB | Fast | Good |
| small | 244M | 2 GB | Medium | Better |
| medium | 769M | 5 GB | Slow | High |
| large-v3 | 1550M | 10+ GB | Slowest | Best |

---

## Loading Whisper

```python
import whisper

model = whisper.load_model("base")
```

---

## Transcription

```python
result = model.transcribe("audio.wav")
```

---

## Output Example

```python
{
    "text": "hello everyone",
    "language": "en"
}
```

---

# Installation

## Clone Repository

```bash
git clone https://github.com/your-org/asr-whisper.git

cd asr-whisper
```

---

## Create Virtual Environment

```bash
python -m venv venv
```

Activate:

Linux/macOS

```bash
source venv/bin/activate
```

Windows

```bash
venv\Scripts\activate
```

---

## Install Dependencies

```bash
pip install -r requirements.txt
```

---

## requirements.txt

```text
openai-whisper
torch
torchaudio
numpy
librosa
soundfile
scipy
nltk
regex
tqdm
pandas
noisereduce
```

---

# FFmpeg Installation

Whisper requires FFmpeg.

Ubuntu

```bash
sudo apt update
sudo apt install ffmpeg
```

macOS

```bash
brew install ffmpeg
```

Windows

Download FFmpeg and add it to PATH.

Verify:

```bash
ffmpeg -version
```

---

# End-to-End Example

```python
import whisper

from audio import load_audio
from audio_clean import clean_audio
from normaliser import normalize_text
from tokeniser import tokenize

audio, sr = load_audio("sample.wav")

audio = clean_audio(audio, sr)

model = whisper.load_model("base")

result = model.transcribe("sample.wav")

raw_text = result["text"]

normalized_text = normalize_text(raw_text)

tokens = tokenize(normalized_text)

print("Raw:", raw_text)
print("Normalized:", normalized_text)
print("Tokens:", tokens)
```

---

# Example Execution

Input Audio:

```text
HELLO everyone!!! Welcome to our ASR demo.
```

Whisper Output:

```text
HELLO everyone!!! Welcome to our ASR demo.
```

Normalized Output:

```text
hello everyone welcome to our asr demo
```

Tokenized Output:

```python
[
 'hello',
 'everyone',
 'welcome',
 'to',
 'our',
 'asr',
 'demo'
]
```

---

# Performance Optimization

## CPU Inference

Recommended:

```python
base
```

or

```python
small
```

---

## GPU Inference

Recommended:

```python
medium
```

or

```python
large-v3
```

---

## Batch Processing

Process multiple files:

```python
for file in audio_files:
    process(file)
```

---

## Memory Optimization

Use:

```python
tiny
base
```

for low-memory environments.

---

# Logging

Recommended log levels:

```text
INFO
WARNING
ERROR
DEBUG
```

Example:

```python
logging.info("Audio loaded successfully")
```

---

# Testing

Run all tests:

```bash
pytest
```

Coverage:

```bash
pytest --cov=.
```

---

# Deployment Recommendations

## Docker

```bash
docker build -t asr-whisper .
```

---

## FastAPI

Expose transcription service:

```bash
POST /transcribe
```

---

## Kubernetes

Recommended for:

- High-volume transcription
- Distributed inference
- Enterprise deployment

---

# Future Enhancements

- Streaming ASR
- Speaker Diarization
- Voice Activity Detection
- Word-Level Timestamps
- Forced Alignment
- Language Translation
- Real-Time Inference
- Distributed Processing
- Whisper Fine-Tuning
- Custom Vocabulary Support
- REST API
- Web Interface
- Mobile Integration

---

# License

MIT License

Copyright (c) 2026

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software.

---

# Acknowledgements

This project builds upon:

- OpenAI Whisper
- PyTorch
- Librosa
- FFmpeg
- NumPy
- SciPy
- NLTK

Their contributions to the open-source ecosystem make modern speech recognition systems possible.

---

# Summary

This repository provides a complete ASR pipeline that transforms raw audio into clean, normalized, and tokenized text using OpenAI Whisper. The modular architecture allows each component to be independently developed, tested, and deployed while maintaining a robust end-to-end speech recognition workflow suitable for research, production, and large-scale NLP applications.
