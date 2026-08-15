<div align="center">

<br>

<p>
  <img src="https://readme-typing-svg.herokuapp.com?font=Fira+Code&weight=600&size=32&pause=1000&color=5865F2&center=true&vCenter=true&width=400&lines=%F0%9F%A7%B9+Watermarks+Remover" alt="Watermarks Remover">
</p>

<h3>
  <img src="https://media.giphy.com/media/mGcNjg1biX4ofqYTwg/giphy.gif" width="20" height="20">
  <em>The privacy toolkit for AI-generated content</em>
  <img src="https://media.giphy.com/media/mGcNjg1biX4ofqYTwg/giphy.gif" width="20" height="20">
</h3>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://img.shields.io/badge/Python-3.10+-blue?style=for-the-badge&logo=python&logoColor=white&labelColor=1a1a2e">
  <source media="(prefers-color-scheme: light)" srcset="https://img.shields.io/badge/Python-3.10+-blue?style=for-the-badge&logo=python&logoColor=white">
  <img alt="Python" src="https://img.shields.io/badge/Python-3.10+-blue?style=for-the-badge&logo=python&logoColor=white">
</picture>
<img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge&logo=opensourceinitiative&logoColor=white" alt="License">
<img src="https://img.shields.io/badge/Dependencies-0-red?style=for-the-badge&logo=ghost&logoColor=white" alt="No Dependencies">
<img src="https://img.shields.io/badge/Runs_Locally-✓-purple?style=for-the-badge&logo=serverless&logoColor=white" alt="Local">

<br>

[![CI](https://github.com/DesusLove/Watermarks-Remover/actions/workflows/ci.yml/badge.svg)](https://github.com/DesusLove/Watermarks-Remover/actions/workflows/ci.yml)
[![GitHub stars](https://img.shields.io/github/stars/DesusLove/Watermarks-Remover?style=social)](https://github.com/DesusLove/Watermarks-Remover/stargazers)
[![GitHub forks](https://img.shields.io/github/forks/DesusLove/Watermarks-Remover?style=social)](https://github.com/DesusLove/Watermarks-Remover/network/members)

<br>

### ❯ <strong>Clean watermarks from your content — text, images, and documents.</strong>

<br>

<a href="#-installation">
  <img src="https://img.shields.io/badge/Get_Started-5865F2?style=for-the-badge" alt="Get Started">
</a>
<a href="#-how-it-works">
  <img src="https://img.shields.io/badge/How_It_Works-2ea043?style=for-the-badge" alt="How It Works">
</a>
<a href="#-http-api">
  <img src="https://img.shields.io/badge/API_Reference-f0883e?style=for-the-badge" alt="API Reference">
</a>

</div>

<img src="https://user-images.githubusercontent.com/73097560/115834477-dbab4500-a447-11eb-908a-139a6edaec5c.gif" width="100%">

---

## Why This Exists

AI systems leave traces. Hidden characters in text. Metadata in images. C2PA manifests in documents. These markers identify content as AI-generated, sometimes without you knowing.

Watermarks Remover strips these traces from content **you own**. Run it locally. No cloud. No dependencies. Your data stays on your machine.

---

## 📦 Installation

**Clone and run:**

```bash
git clone https://github.com/DesusLove/Watermarks-Remover.git
cd Watermarks-Remover
make serve
```

**Docker:**

```bash
docker pull ghcr.io/desuslove/watermarks-remover:latest
docker run --rm -p 127.0.0.1:8765:8765 watermarks-remover
```

That's it. Python 3.10+ stdlib only — no `pip install` required.

---

## 🎯 What Gets Removed

| Layer | Target | Method |
|:-----:|--------|--------|
| **A** | Invisible Unicode, zero-width chars, bidi overrides, tag characters | Deterministic scrubbing |
| **B** | Statistical text watermarks (token-sampling patterns) | AI paraphrase rewrite |
| **Files** | C2PA, EXIF, XMP, document properties | Format-specific stripping |

**Formats supported:** PNG · JPEG · WebP · SVG · PDF · DOCX · ODT · HTML · Markdown

**Vendors covered:** Claude · Gemini/SynthID · OpenAI · Open-LLM (Kirchenbauer-style)

---

## 🔧 Usage

### Command Line

```bash
# Inspect before cleaning
python3 service/scripts/inspect_file.py document.md

# Clean text
python3 service/scripts/clean_file.py document.md -o clean.md

# Clean images
python3 service/scripts/clean_file.py photo.png -o clean.png

# Clean documents
python3 service/scripts/clean_file.py report.pdf -o clean.pdf
```

### HTTP API

```bash
# Start server
python3 service/scripts/server.py --host 127.0.0.1 --port 8765

# Inspect
curl -X POST http://127.0.0.1:8765/inspect \
  -H "Content-Type: application/json" \
  -d '{"file": "'$(base64 -w0 file.md)'", "name": "file.md"}'

# Clean
curl -X POST http://127.0.0.1:8765/clean \
  -H "Content-Type: application/json" \
  -d '{"file": "'$(base64 -w0 file.md)'", "name": "file.md"}'
```

---

## 🛠 HTTP Endpoints

| Endpoint | Method | Purpose |
|:---------|:------:|:--------|
| `/health` | GET | Server status |
| `/capabilities` | GET | Available backends |
| `/openapi.json` | GET | OpenAPI 3.0.3 spec |
| `/inspect` | POST | Detect watermarks in a file |
| `/clean` | POST | Remove watermarks from a file |

All file payloads are base64-encoded. The server auto-detects format by extension and magic bytes.

---

## ⚙️ How It Works

### Text Cleaning

```
Input Text
    │
    ▼
┌─────────────────────────────────────┐
│  Layer A: Unicode Scrubbing         │
│  Remove hidden characters,          │
│  bidi overrides, tag chars          │
│  (Lossless, verifiable)             │
└─────────────────────────────────────┘
    │
    ▼
┌─────────────────────────────────────┐
│  Layer B: Rewrite (optional)        │
│  Paraphrase text to break           │
│  statistical watermarks              │
│  (Changes style)                    │
└─────────────────────────────────────┘
    │
    ▼
Clean Text
```

### File Metadata Removal

| Format | What's stripped |
|--------|-----------------|
| **Images** (PNG, JPEG, WebP) | C2PA chunks, XMP, EXIF AI tags |
| **SVG** | Metadata blocks, XMP payloads |
| **PDF** | XMP, Info dictionary (needs `exiftool` + `qpdf`) |
| **DOCX** | docProps, customXml sections |
| **ODT** | meta.xml generator tags |
| **HTML** | Meta tags, JSON-LD, `data-ai*` attrs |
| **Markdown** | YAML frontmatter AI keys |

---

## 🔌 Optional Backends

<details>
<summary><b>SynthID Detection</b> — Score pixel watermarks in images</summary>

Uses [reverse-SynthID](https://github.com/aloshdenny/reverse-SynthID) to detect Google's SynthID watermarks:

```bash
./service/scripts/setup_synthid.sh
REVERSE_SYNTHID_DIR=~/reverse-SynthID \
  python3 service/scripts/score_synthid.py image.png
```

</details>

<details>
<summary><b>CtrlRegen Removal</b> — Remove pixel watermarks</summary>

Removes SynthID, StegaStamp, Tree-Ring watermarks using [CtrlRegen](https://arxiv.org/abs/2410.05470):

```bash
./service/scripts/setup_ctrlregen.sh
NOAI_WATERMARK_DIR=~/noai-watermark \
  python3 service/scripts/clean_ctrlregen.py image.png -o clean.png
```

</details>

<details>
<summary><b>MarkLLM Verification</b> — Test text watermark removal</summary>

Verify watermarks before/after cleaning with [MarkLLM](https://github.com/THU-BPM/MarkLLM):

```bash
./service/scripts/setup_markllm.sh
MARKLLM_DIR=~/MarkLLM \
  python3 service/scripts/detect_text_watermark.py detect text.txt --scheme kgw
```

</details>

---

## ⚠️ What This Cannot Do

| Not Supported | Why |
|---------------|-----|
| Soft-binding C2PA | Requires in-content watermarks, out of scope |
| Audio/video watermarks | Different domain, not implemented |
| Training backdoors | Hidden in model weights, unreachable |
| Guaranteed detector failure | No public vendor detectors exist to test against |

**Honesty note:** This removes *verifiable* markers (Unicode, metadata) and provides *best-effort* statistical rewriting. We cannot certify vendor-specific detectors will fail.

---

## 🎓 Configuration

Set environment variables in `.env`:

```bash
cp .env.example .env
```

| Variable | Purpose |
|----------|---------|
| `WATERMARKS_SERVER_API_KEY` | Require Bearer auth on API |
| `WATERMARKS_SERVICE_URL` | Service URL (default: `http://127.0.0.1:8765`) |
| `WATERMARKS_REWRITE_BACKEND` | Backend for Layer B: `ollama`, `openai-compatible` |
| `WATERMARKS_REWRITE_MODEL` | Model name for rewrites |
| `WATERMARKS_REWRITE_API_KEY` | API key (env only, never CLI args) |
| `HF_TOKEN` | Hugging Face token for gated models |

---

## 🧪 Development

```bash
make test           # Run test suite
make smoke          # Quick CLI smoke test
make compose-check  # Validate Docker stack
```

---

## 📋 Ethics & Responsible Use

**This is for content you own.**

- ✅ Privacy protection on your own content
- ✅ Research and testing
- ✅ Content hygiene on authorized materials

- ❌ Academic dishonesty
- ❌ Plagiarism
- ❌ Fraudulent "human-written" claims

See [`ethics.md`](skills/remove-ai-marks/references/ethics.md) for full guidelines.

---

## 📚 References

This project builds on:

- [C2PA Specification](https://c2pa.org/) — Content provenance standard
- [SynthID](https://deepmind.google/science/synthid/) — Google's watermarking approach
- [Kirchenbauer et al.](https://arxiv.org/abs/2301.10226) — LLM watermarking research
- [CtrlRegen](https://arxiv.org/abs/2410.05470) — Image watermark removal (ICLR 2025)
- [MarkLLM](https://github.com/THU-BPM/MarkLLM) — LLM watermark toolkit
- [MarkDiffusion](https://arxiv.org/abs/2509.10569) — Image watermark toolkit

---

## 📄 License

MIT License — see [LICENSE](LICENSE).

---

<div align="center">

**[⬆ Back to Top](#-watermarks-remover)**

*Built for privacy. Use responsibly.*

</div>
