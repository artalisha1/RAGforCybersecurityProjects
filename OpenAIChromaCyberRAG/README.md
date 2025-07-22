# Integrating OpenAI LLM + ChromaDB for Retrieval‑Augmented Generation

This notebook (OpenAILLMwithChroma.ipynb) builds a complete **Retrieval‑Augmented Generation (RAG)** pipeline.  
The notebook embeds cybersecurity data, stores vectors in **ChromaDB**, and answers questions with an OpenAI chat model—seamlessly falling back to a local Hugging Face model when the API is unavailable, rate‑limited, or too costly.


---

## 📋 Table of Contents

1. [Project Overview](#project-overview)  
2. [Prerequisites](#prerequisites)  
3. [Script Walkthrough](#script-walkthrough)  
4. [Customization Tips](#customization-tips)  
5. [Next Steps](#next-steps)  

---

## Project Overview

Pipeline steps:

1. **Load CSV data** – Read `CISA_combo_features_new.csv` with `langchain`’s `CSVLoader`.  
2. **Generate embeddings** – Encode each chunk with **MiniLM** (`all‑MiniLM‑L6‑v2`) using `HuggingFaceEmbeddings`.  
3. **Persist to ChromaDB** – Add the vectors to a local Chroma collection and call `persist()` so they survive restarts.  
4. **Create a retriever** – Expose the collection as a retriever with `k=1` nearest‑neighbor search.  
5. **Dual Retrieval‑QA chains** –  
   * **Primary:** OpenAI GPT‑3.5‑Turbo via `ChatOpenAI`  
   * **Secondary:** Local `gpt2` via `HuggingFacePipeline` for offline / fallback generation  
6. **Query & fail‑over logic** – Attempts OpenAI first; on `RateLimitError` or `OpenAIError`, gracefully switches to the local model.

---

## Prerequisites

| Requirement | Minimum Version |
|-------------|-----------------|
| **Python**  | 3.9+ |
| **pip**     | 22+ |
| **Hardware**| CPU is fine; GPU (≥ 8 GB VRAM) accelerates embedding & generation |

### Python packages

```bash
pip install langchain chromadb sentence-transformers pandas tqdm langchain_openai langchain_community transformers
pip install --upgrade openai
```

Set the environment variable for OpenAI:

```bash
export OPENAI_API_KEY="sk-…" #SET YOUR OWN API KEY HERE
```

---

## Script Walkthrough

| Step | Purpose |
|------|---------|
| **1 — Instantiate LLMs** | Reads `OPENAI_API_KEY` and sets up OpenAI GPT‑3.5 plus a local `gpt2` fallback. |
| **2 — Build vector store** | Creates a Chroma instance backed by MiniLM embeddings, adds documents, and persists to disk. |
| **3 — Create retriever** | Wraps the vector store for similarity search (`k‑NN`). |
| **4 — Construct Retrieval‑QA chains** | Two chains share the same retriever but use different LLMs—OpenAI for quality, `gpt2` for resilience. |
| **5 — Run a query** | Executes a sample MITRE ATT&CK question with automatic fallback handling. |
| **6 — Scale considerations** | Inline comments highlight storage, GPU, and cost factors for production deployments. |

---

## Customization Tips

* **Swap embedding model** – e.g., `paraphrase-MiniLM-L6-v2` for better paraphrase recall; just change `model_name` when instantiating `HuggingFaceEmbeddings`.  
* **Tune retrieval depth (`k`)** – raise `k` in `as_retriever(search_kwargs={"k": …})` for broader context windows.  
* **Persist directory** – point `chromadb_path` to a mounted volume for container deployments.  
* **Replace fallback model** – select a stronger open‑source model (e.g., *Mistral‑7B‑Instruct*) if you have GPU memory; update the `pipeline()` call accordingly.  

---

## Next Steps

* **Wrap the pipeline in a FastAPI micro‑service** to expose a `/query` endpoint that streams answers.  
* **Add vector‑store sharding** or switch to managed databases (e.g., Chroma Cloud) for multi‑tenant scale.  
* **Incorporate prompt‑engineering guards** (system messages, tool‑calling) to enrich answers with provenance.  
* **Automate continuous ingestion** of fresh CISA/MITRE feeds via scheduled jobs, keeping embeddings current.  
