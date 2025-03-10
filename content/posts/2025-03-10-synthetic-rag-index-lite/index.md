---
categories:
  - rag
  - open-source
  - ai
  - llm
  - code
  - azure
comments: true
Params:
  ShowReadingTime: true
ShowToc: true
TocOpen: false
cover:
  image: "cover_synthetic_rag_lite.webp"
  relative: true
date: "2025-03-10T12:00:00Z"
description: >
  Discover Synthetic RAG Index Lite - a lightweight adaptation of Microsoft's Synthetic RAG Index. Learn how to extract, synthesize, and index content from markdown files and images for efficient retrieval-augmented generation (RAG).
tags:
  - Synthetic RAG
  - LLM
  - LiteLLM
  - OCR
  - best-practices
  - local deployment
title: "Synthetic RAG Index Lite: Extract and Synthesize"
---

## Why Synthetic RAG Index Lite?

In the fast-moving landscape of large language models (LLMs) and retrieval-augmented generation (RAG), it’s essential to have a straightforward yet powerful tool. Microsoft’s [Synthetic RAG Index](https://github.com/microsoft/synthetic-rag-index) is a robust solution for indexing and compressing large amounts of data, but sometimes you just need core functionalities without a full-stack deployment. That’s where **Synthetic RAG Index Lite** steps in.

**Key Goals**:

1. **Lightweight Implementation**: Keep the essential steps - extract, synthesize, and index - without the overhead of more advanced serverless architecture.
2. **Multi-Provider Support**: Integrate easily with multiple LLM providers using [LiteLLM](https://pypi.org/project/litellm) to choose the best model for your use case.
3. **User-Friendliness**: Provide clear commands, environment configurations, and minimal friction for setup.

This Lite version preserves the spirit and core ideas from Microsoft’s original Synthetic RAG Index, while introducing simpler structures for smaller-scale or quick-turnaround projects. It **respects** the seminal work that inspired it, yet provides a tailored alternative for those seeking a direct, minimal solution.

---

## Core Features

1. **Markdown and Text Processing**  
   - Reads `.md` and `.txt` files out-of-the-box.
   - Quickly extracts the text and splits it into manageable chunks.

2. **Optional OCR for Images**  
   - If needed, you can enable image processing for `.jpg`, `.jpeg`, `.png`, `.gif`, and `.bmp` files.
   - Requires [Pillow](https://pypi.org/project/Pillow/) and [pytesseract](https://pypi.org/project/pytesseract/) (plus a Tesseract OCR installation).
   - Perfect if you have diagrams or screenshots containing critical text.

3. **Multi-Stage Pipeline**  
   1. **Extract**: Get the raw text from files or images.  
   2. **Chunk**: Split content into chunks for easier handling.  
   3. **Synthesize**: Summarize or condense these chunks to reduce redundancy.  
   4. **Generate Facts (Q&A Pairs)**: Turn each chunk into Q&A pairs for direct integration into RAG scenarios.  
   5. **Critique**: Score each fact for quality, and remove low-relevance data.  
   6. **Index**: Output final facts in chosen formats, ready for retrieval.

4. **Flexible Output Formats**  
   - **JSON** (pretty-printed, default)  
   - **JSON Lines (JSONL)**  
   - **Markdown**  
   - **All** (simultaneously)  

   This makes it straightforward to consume outputs in a variety of pipelines or viewer tools.

5. **Advanced LiteLLM Capabilities**  
   - **Model Fallbacks**: If one model fails, automatically switch to another without breaking your workflow.  
   - **Load Balancing**: Through a LiteLLM proxy, distribute queries across multiple models or providers.  
   - **Caching**: Reuse previous completions to save time and cost.  

6. **Straightforward Setup**  
   - A single Python script: `synthetic_rag_lite.py`  
   - Minimal dependencies.  
   - Symbolic link for backward compatibility (`local_rag.py` → `synthetic_rag_lite.py`).  

---

## Differences from Microsoft’s Synthetic RAG Index

1. **Scope**:  
   - *Original*: Handles a broad range of file types (PDF, Office, HTML) with a full serverless Azure architecture.  
   - *Lite Version*: Focuses on `.md`, `.txt`, and optionally images. Perfect for local or smaller-scale use.

2. **LLM Integration**:  
   - *Original*: Primarily uses Azure OpenAI GPT-based models.  
   - *Lite Version*: Provides **expanded** provider support (OpenAI, Azure, Anthropic, Cohere, Ollama, Together.ai, Google VertexAI, AWS Bedrock) via LiteLLM.

3. **Infrastructure**:  
   - *Original*: Built for cloud deployment, with Azure Functions, Blob Storage, AI Search, etc.  
   - *Lite Version*: Designed for quick local runs. No forced cloud dependencies unless you configure them.

4. **Output Approach**:  
   - *Original*: Stores facts in AI Search.  
   - *Lite Version*: Produces local JSON/JSONL/Markdown index files ready to be loaded into your own retrieval system.

5. **Complexity**:  
   - *Original*: Full suite (OCR, advanced text extraction, PDF compression, etc.).  
   - *Lite Version*: Minimal code footprint but maintainable for your personal or small-team projects.

The **Lite** approach is ideal if you need a quick, local solution, or if your data mainly comes from markdown, text, or images (with optional OCR). It respects the advanced architecture from Microsoft’s version but distills it to the essentials.

---

## Installation and Quick Start

```bash
git clone https://github.com/forcewake/synthetic-rag-index-lite.git
cd synthetic-rag-index-lite

# Create a virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# Install base requirements
pip install -r requirements.txt

# (Optional) For image OCR support
pip install pillow pytesseract
# Install Tesseract on your system (brew, apt-get, Windows .exe)
```

**Example Usage**:

```bash
# Prepare input and output directories
mkdir -p input output

# Place your .md, .txt, or image files in the input/ directory
# Then run:
python synthetic_rag_lite.py \
    --input input \
    --output output \
    --llm openai \
    --model-fast gpt-4o-mini \
    --model-quality gpt-4
```

When finished, check the `output/` folder:

- `0-sanitize/` → Optional stage (if using OCR or text cleanups)  
- `1-extract/`, `2-chunck/`, `3-synthesis/`, `4-page/`, `5-fact/`, `6-critic/`, `7-index/` → The pipeline stages, each containing the intermediate and final artifacts.

---

## Advanced Configuration

### LiteLLM

- **Fallbacks**  
  Specify multiple models in order of preference, so if one times out, it falls back automatically:

  ```bash
  python synthetic_rag_lite.py \
      --input input \
      --output output \
      --llm openai \
      --model-fast gpt-4o-mini \
      --model-quality gpt-4 \
      --use-fallbacks \
      --fallback-models anthropic/claude-v1 cohere/command-xlarge
  ```

- **Proxy**  
  If you have a local or remote LiteLLM proxy for load balancing and caching:

  ```bash
  python synthetic_rag_lite.py --litellm-proxy "http://localhost:8080"
  ```

### OCR and Repetition Filtering

- **OCR**: Requires `pillow` + `pytesseract`. Image text is extracted before chunking.  
- **Repetition Threshold**: You can adjust an environment variable, e.g., `export REPETITION_THRESHOLD=1.5` to tighten or loosen the repetition filter.

### Output Formats

- `--fact-format`
- `--critic-format`
- `--index-format`
  
All accept `json`, `jsonl`, `markdown`, or `all`. Example:

```bash
python synthetic_rag_lite.py \
  --fact-format all \
  --critic-format json \
  --index-format markdown
```

---

## Conclusion

**Synthetic RAG Index Lite** delivers a simplified yet powerful approach to content ingestion and fact generation for RAG workflows - perfect for projects focused on markdown, text, and images. It stands as a respectful homage to the original [Synthetic RAG Index](https://github.com/microsoft/synthetic-rag-index), preserving the core methodology while offering a leaner codebase and broader LLM support.

If your use case doesn’t require a full serverless Azure-based platform or advanced PDF ingestion, this **Lite** version likely meets your needs in a more **straightforward** and **resource-friendly** manner. Check out the [GitHub repository](https://github.com/forcewake/synthetic-rag-index-lite) for installation instructions, examples, and ongoing updates.
