# flux-image-pipeline

> Bulk AI image generation pipeline using Flux on RunPod GPU — optimised for high-volume video production with Ken Burns motion and cinematic style output.

[![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white)](https://python.org)
[![RunPod](https://img.shields.io/badge/RunPod-GPU_Cloud-673AB7?style=flat-square&logo=nvidia&logoColor=white)](https://runpod.io)
[![Flux AI](https://img.shields.io/badge/Flux_AI-Image_Gen-FF6B35?style=flat-square)](https://blackforestlabs.ai)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow?style=flat-square)](LICENSE)

---

## Overview

`flux-image-pipeline` is a high-throughput image generation system built around **Flux** (Black Forest Labs) running on **RunPod GPU** instances. Designed specifically for video production workflows, it generates batches of cinematic, scene-matched images from structured prompts — ready for Ken Burns pan/zoom motion and direct injection into video assembly pipelines.

---

## Features

- **Batch generation** — submit a list of scene prompts and retrieve all images in one run
- **RunPod GPU orchestration** — spins up serverless GPU endpoints on demand, no idle cost
- **Cinematic style tuning** — built-in prompt templates for documentary-style, high-detail, film-grain aesthetic
- **Ken Burns ready** — outputs at high resolution (landscape) for smooth pan-and-zoom without quality loss
- **Parallel requests** — sends concurrent generation jobs to RunPod for maximum throughput
- **Automatic retry** — retries failed generation jobs with backoff
- **Structured output** — images saved with scene index filenames, ready for FFmpeg assembly

---

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Image Model | Flux (Black Forest Labs) |
| GPU Cloud | RunPod Serverless |
| Language | Python 3.11+ |
| HTTP Client | httpx (async) |
| Image Handling | Pillow |
| Orchestration | Async batch processor |

---

## Project Structure

```
flux-image-pipeline/
├── pipeline/
│   ├── generator.py         # RunPod API client & Flux job submission
│   ├── batch.py             # Async batch processor with concurrency control
│   ├── prompts.py           # Cinematic prompt templates & scene prompt builder
│   └── downloader.py        # Result polling & image download
├── config/
│   └── pipeline_config.yaml # RunPod endpoint, resolution, style settings
├── output/                  # Generated images (scene_001.png, scene_002.png ...)
├── requirements.txt
└── README.md
```

---

## Getting Started

### Prerequisites

- Python 3.11+
- RunPod account with a Flux model deployed as a Serverless endpoint
- RunPod API key

### Installation

```bash
git clone https://github.com/InFrontWebs/flux-image-pipeline.git
cd flux-image-pipeline
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

### Configuration

```bash
cp config/pipeline_config.yaml.example config/pipeline_config.yaml
```

Set your RunPod API key:

```bash
export RUNPOD_API_KEY=your_runpod_api_key
```

Edit `pipeline_config.yaml` to point at your RunPod endpoint ID and set resolution / style preferences.

### Usage

**Generate images from a JSON scene list:**

```bash
python pipeline/batch.py --scenes scenes.json --output output/
```

`scenes.json` format:

```json
[
  {"index": 1, "prompt": "Ancient Roman forum at golden hour, cinematic, photorealistic, 8K"},
  {"index": 2, "prompt": "Silk Road merchants crossing desert dunes, wide angle, film grain"},
  {"index": 3, "prompt": "Medieval illuminated manuscript detail, macro, dramatic lighting"}
]
```

**Single image:**

```bash
python pipeline/generator.py --prompt "Your prompt here" --output output/scene_001.png
```

---

## Output Specifications

| Setting | Default |
|---------|---------|
| Resolution | 1792 × 1024 (16:9) |
| Format | PNG |
| Style | Cinematic, photorealistic, film grain |
| Filename | `scene_NNN.png` (zero-padded index) |

Images are sized for Ken Burns pan-and-zoom — the extra resolution headroom allows cropping and animation without visible quality degradation at 1080p output.

---

## Integration

Used as the image generation stage in [video-automation-pipeline](https://github.com/InFrontWebs/video-automation-pipeline):

```python
from pipeline.batch import generate_batch

images = await generate_batch(scenes=scene_list, output_dir="output/")
```

---

## Cost Notes

RunPod Serverless charges per GPU-second of actual inference time. With Flux, generation cost per image is typically a fraction of a cent. Batch parallelism is configurable to balance throughput against RunPod spend.

---

## License

MIT — see [LICENSE](LICENSE)

---

Built by [InFrontWebs](https://infrontwebs.com) · Bangladesh
