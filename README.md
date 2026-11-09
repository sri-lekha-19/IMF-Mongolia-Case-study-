# LLM Pipeline Case Study — IMF Country Report No. 25/265 (Mongolia, 2025 Article IV)

PDF-to-dashboard pipeline: PDF parsing and cleaning, token-aware chunking, LLM summarisation with an LLM-as-a-Judge evaluator, structured indicator extraction, RAG retrieval and a visual dashboard.

## Run it (marker instructions)

1. Open `IMF_Mongolia_LLM_Pipeline.ipynb` in Google Colab (badge below, or *File → Open notebook → GitHub* and paste this repository's URL).
2. Select **Runtime → Run all**.

That's all. Cell 0 clones this repository into the Colab runtime, so the report PDF, cached LLM responses and pinned requirements are available without Google Drive.

**No API keys are needed to reproduce the submitted results.** Every LLM response is cached in `outputs/cache/llm/`, keyed by a hash of the prompt, model and output schema, so a re-run replays them without network calls.

To regenerate responses with your own keys instead, add them under Colab **Secrets** (key icon in the left sidebar) and delete `outputs/cache/llm/`:

| Secret | Purpose |
|---|---|
| `GEMINI_API_KEY` | Free-tier key from Google AI Studio; used by both generator and judge |
| `GROQ_API_KEY` (optional) | Only to re-run the cross-family judge (Cell 25b); cached Groq audits replay without it |

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/<your-github-user>/<your-repo>/blob/main/IMF_Mongolia_LLM_Pipeline.ipynb)

## Repository layout

```
.
├── IMF_Mongolia_LLM_Pipeline.ipynb        # pipeline notebook (Tasks 1-4)
├── Data/
│   └── <IMF report>.pdf           # IMF Country Report No. 25/265
├── outputs/
│   ├── cache/llm/*.json           # cached LLM responses (enables key-free replay)
│   ├── cache/judge_resolution.json
│   ├── extracted/                 # parsed text, section map, chunks, tables
│   ├── task1/                     # summaries, executive summary, judge audits
│   ├── task2/                     # core_indicators.json, extended_indicators.json, supplementary_indicators.json, fallbacks, themes, risk insights, trends
│   ├── task3/                     # FAISS index, embeddings, RAG answers, RAG Triad scores and monitor
│   ├── cross_family_judge/        # optional Groq judge: scores, justifications, agreement (Cell 25b)
│   ├── evaluation/                # constraint adherence, hallucination evidence, failure log (Cell 25c)
│   ├── task4/                     # tidy (pd.melt) long-format CSVs behind every plot
│   └── figures/                   # 4-plot dashboard.png, panels (dpi 300), dashboard_commentary.md
├── requirements.txt               # pinned versions written by the notebook
├── LICENSE                        # MIT
└── README.md
```

## Configuration

All settings are environment variables read in Cell 2; none are required.

| Variable | Default | Meaning |
|---|---|---|
| `REPO_URL` | set in Cell 0 | Repository cloned by the bootstrap cell |
| `PROJECT_DIR` | cloned repo | Project root override |
| `USE_DRIVE` | `0` | `1` = use Google Drive folder `PROJECT_FOLDER` (author's development only) |
| `GEMINI_MODEL` / `GEMINI_FALLBACK_MODEL` | `gemini-3.1-flash-lite` / none | Generator (free tier) |
| `GEMINI_JUDGE_MODELS` | `gemini-3.5-flash-lite` | Judge (free tier, distinct from generator) |
| `GEMINI_RPM_LIMIT` / `GEMINI_TPM_LIMIT` | `14` / `225000` | Per-model rate limiter (free tier: 15 RPM, 250K TPM) |
| `JUDGE_MAX_SOURCE_TOKENS` | `24000` | Max source text a judge reads |
| `LLM_MODE` | `live` | `mock` = development run with placeholder answers, no API calls (`outputs_mock/`) |
| `RUN_JUDGE` | `1` | `0` = judge audits from cache only (development) |
| `DEV_MAX_SECTIONS` | `0` (all) | Summarise only the first N sections (development) |
| `CHUNK_MAX_TOKENS` / `CHUNK_OVERLAP_TOKENS` | `400` / `50` | Chunking |
| `EXTRACTION_MODELS` | generator chain | Models compared for indicator extraction |
| `EMBEDDING_MODEL` | `sentence-transformers/all-MiniLM-L6-v2` | RAG embeddings |
| `RAG_TOP_K` | `5` | Chunks retrieved per question |

## Source document

International Monetary Fund (2025). *Mongolia: 2025 Article IV Consultation — Press Release; Staff Report; and Statement by the Alternate Executive Director for Mongolia*. IMF Country Report No. 25/265. Washington, DC: IMF. The report is © International Monetary Fund and is included only to make the pipeline reproducible.

## Licence

Code released under the MIT License (see `LICENSE`).
