# Hate Speech Analyser — Desktop App 🌍

> **Built on top of [cop28-hate-speech](https://github.com/YOUR_USERNAME/cop28-hate-speech)** — a university research project investigating hate speech on X (Twitter) during the 28th UN Climate Change Conference (COP28, Dubai, Nov 30 – Dec 13, 2023). This repository takes that research pipeline and wraps it into a standalone desktop application for easier use and demonstration.

---

## What This Is

The original project was a command-line pipeline: collect tweets via Apify, classify them with a BERT model, export results to Excel. It worked, but required running multiple scripts manually and understanding the JSON formats involved.

This app packages everything into a single native desktop window.

---

## Screenshot

```

```

---

## Features

- **Native desktop window** — opens directly, no browser or terminal needed during use
- **Drag-and-drop free file picker** — uses your OS's standard file dialog
- **Auto-detects Apify actor format** — supports both `apidojo/twitter-scraper-lite` (camelCase) and `altimis/scweet` (snake_case) JSON structures automatically
- **Mixed format warning** — if you accidentally upload files from different actors, the app warns you before you run anything
- **English-only filtering** — non-English tweets are excluded automatically using Twitter's own `lang` field
- **Deduplication** — duplicate tweets across multiple files are removed during merge
- **Real-time progress bar** — tracks classification tweet by tweet
- **Three interactive charts** — distribution pie, breakdown bar, and side-by-side comparison with the original study
- **Tabbed results table** — browse Hate, Offensive, and All tweets separately
- **One-click Excel export** — saves a colour-coded `.xlsx` via your OS save dialog

---

## Supported Apify Actor Formats

| Actor | Format | Auto-detected as |
|-------|--------|-----------------|
| `apidojo/twitter-scraper-lite` | camelCase (`createdAt`, `likeCount`, `isReply`) | `scraper-lite` |
| `altimis/scweet` | snake_case (`created_at`, `favorite_count`) | `scweet` |

You can also override the auto-detection manually using the format dropdown.

---

## Quick Start

### 1 — Install dependencies

```bash
pip install pywebview transformers torch pandas openpyxl
```

> **Note on PyTorch:** `torch` can be large (~2 GB). If you only need CPU inference (no GPU), you can install the lighter CPU-only version:
> ```bash
> pip install torch --index-url https://download.pytorch.org/whl/cpu
> ```

### 2 — Run the app

```bash
python app.py
```
or simply use the executable file from the releases for Windows use.

A desktop window opens immediately.

> **First run:** When you click *Run Analysis* for the first time, the classification model (`cardiffnlp/twitter-roberta-base-offensive`, ~500 MB) downloads automatically and is cached at `~/.cache/huggingface/`. Subsequent runs use the cached version.

### 3 — Test with sample data

Sample `.json` files are included for each format in this repository (real English tweets from COP28). You can load it immediately without needing an Apify account to verify the app works end-to-end.

---

## Getting Your Own Data

Tweets are collected via [Apify](https://apify.com) — a cloud scraping platform. The free tier gives 1,000 tweets per run.

1. Create a free account at [apify.com](https://apify.com)
2. Open the [Twitter Scraper Lite](https://apify.com/apidojo/twitter-scraper-lite) actor
3. Use this input:
   ```json
   {
     "searchTerms": [
       "#COP28 lang:en since:2023-11-30_00:00:00_UTC until:2023-12-13_23:59:59_UTC",
       "COP28 lang:en since:2023-11-30_00:00:00_UTC until:2023-12-13_23:59:59_UTC"
     ],
     "lang": "en",
     "filter:replies": false
   }
   ```
4. Export the result as JSON and load it in the app

---

## How Classification Works

The app uses a two-step classification approach:

1. **[`cardiffnlp/twitter-roberta-base-offensive`](https://huggingface.co/cardiffnlp/twitter-roberta-base-offensive)** — a RoBERTa model fine-tuned on Twitter data — reads each tweet and classifies it as offensive or not
2. If offensive, a **violence keyword check** (`kill`, `shoot`, `bomb`, `attack`, etc.) further escalates the tweet to *Hate*

This mirrors the logic of the original study's model (`ctoraman/hate-speech-bert`), which has since been removed from HuggingFace. See the [original project](https://github.com/YOUR_USERNAME/cop28-hate-speech) for full methodological context.

---

## Dependencies Explained

| Package | What it is | Why needed |
|---------|-----------|------------|
| `pywebview` | Native desktop window renderer | Opens the app as a standalone window without a browser |
| `transformers` | HuggingFace model library | Loads and runs the BERT classification model |
| `torch` | PyTorch — deep learning framework by Meta | Powers model computations |
| `pandas` | Data analysis library | Handles tweet data as a table |
| `openpyxl` | Excel file library | Writes the colour-coded `.xlsx` output |

---

## Project Structure

```
cop28-hate-speech-app/
│
├── app.py											# The entire application — run this
├── sample_scraper_lite.json			# Real COP28 tweets for testing (scraper lite format)
├── sample_scweet.json					# 206 real COP28 tweets for testing (scweet format)
├── requirements.txt						# Python dependencies
└── README.md									# This file
```

---

## Relation to the Original Project

This app is a development of the pipeline originally built for the *Language in the Media* course at TU Dortmund (WiSe 2023/2024). The core classification logic — merging, filtering, BERT model, Excel output — is identical. What this repo adds is the desktop interface layer built with PyWebView, making the pipeline accessible without any command-line interaction.

👉 See the original pipeline: [cop28-hate-speech](https://github.com/YOUR_USERNAME/cop28-hate-speech)

---

## Limitations

- The model applies a stricter definition of offensive language than the original study's model, resulting in lower offensive/hate rates. See the original project's README for a full discussion.
- Processing ~12,000 tweets takes 30–60 minutes on a standard laptop CPU. The progress bar tracks this in real time.
- The results table shows the first 300 rows in the app. The full dataset is always saved in the Excel export.

---

*Built on research from: Language in the Media · TU Dortmund · Faculty of Cultural Studies · WiSe 2023/2024*
