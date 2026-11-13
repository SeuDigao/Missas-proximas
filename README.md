# OGG Audio Translator

Batch-translates OGG audio files into other languages while preserving the original speaker's voice, emotion, pacing, and intonation.

---

## Two approaches

| | ElevenLabs Dubbing | Meta SeamlessExpressive |
|---|---|---|
| **Script** | `dub_elevenlabs.py` | `dub_seamless.py` |
| **Cost** | ~$0.30–0.50 / min of audio | Free |
| **Setup** | API key only | GPU + ~10 GB model download |
| **Voice preserved** | Yes (cloned) | Approximate |
| **Expression preserved** | Yes | Best available (research-grade) |
| **Output format** | MP3 | WAV |

Start with **ElevenLabs** unless you have a GPU and need to run at scale without API costs.

---

## Quick start (ElevenLabs)

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Set your API key

Open `.env` and replace the placeholder with your actual key:

```
ELEVENLABS_API_KEY=your_key_here
```

Get your key at: https://elevenlabs.io/app/settings/api-keys

### 3. Run

```bash
python scripts/dub_elevenlabs.py \
  --input  ./test \
  --output ./out \
  --target-lang es
```

Output files are saved as `<original_name>_<lang>.mp3` in the output directory.
Re-running skips files that were already dubbed.

### Language codes (BCP-47)

| Language | Code |
|---|---|
| Spanish | `es` |
| French | `fr` |
| German | `de` |
| Portuguese (Brazil) | `pt` |
| Italian | `it` |
| Japanese | `ja` |
| Chinese | `zh` |
| Korean | `ko` |
| Arabic | `ar` |
| Hindi | `hi` |

Full list: https://elevenlabs.io/docs/dubbing/overview

---

## Quick start (SeamlessExpressive)

### 1. Install dependencies

```bash
pip install torch torchaudio soundfile tqdm
pip install git+https://github.com/facebookresearch/seamless_communication.git
```

> Requires Python 3.9+, a CUDA-capable GPU (8 GB+ VRAM recommended), and ~10 GB disk space for the model (downloaded automatically on first run).

### 2. Run

```bash
python scripts/dub_seamless.py \
  --input  ./test \
  --output ./out \
  --target-lang spa
```

Use `--device cpu` if you don't have a GPU (expect significantly slower processing).

### Language codes (ISO 639-3)

| Language | Code |
|---|---|
| Spanish | `spa` |
| French | `fra` |
| German | `deu` |
| Portuguese | `por` |
| Italian | `ita` |
| Japanese | `jpn` |
| Chinese (Mandarin) | `cmn` |
| Korean | `kor` |
| Arabic | `arb` |
| Hindi | `hin` |

Full list: https://github.com/facebookresearch/seamless_communication#supported-languages

---

## Folder structure

```
ogg-translator/
├── scripts/
│   ├── dub_elevenlabs.py   # ElevenLabs Dubbing API (primary)
│   └── dub_seamless.py     # Meta SeamlessExpressive (local)
├── test/                   # drop your OGG files here to test
├── requirements.txt
├── .env               ← add your API key here
├── .env.example       ← template
└── README.md
```

---

## Limitations

Full expression preservation is still an open research problem. Both approaches will faithfully carry over voice identity and general emotional tone, but subtle nuances (e.g. a specific laugh, a sharp breath) may not transfer perfectly. This is the current state of the art across all tools in this space.
