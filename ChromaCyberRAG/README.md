# Uploading Cyber-Security Data to **ChromaDB**

This notebook (`DataUploadChromaDB.ipynb`) showcases how to ingest, embed, and store cybersecurity data—such as MITRE ATT&CK techniques and CISA advisories—into **ChromaDB**, enabling similarity search and integration with LLM-powered retrieval workflows.

---

## 📋 Table of Contents

1. [Project Overview](#project-overview)
2. [Prerequisites](#prerequisites)
3. [Notebook Walkthrough](#notebook-walkthrough)
4. [Customization Tips](#customization-tips)
5. [Next Steps](#next-steps)

---

## Project Overview

Pipeline steps:

1. **Load CSV Data** → Import MITRE ATT&CK & CISA advisories into Pandas DataFrames.
2. **Generate Embeddings** → Use `all-MiniLM-L12-v2` from Sentence Transformers.
3. **Persist to ChromaDB** → Save vector representations and metadata locally.
4. **Enable Search & Retrieval** → Ready the database for similarity search or LLM RAG integration.

---

## Prerequisites

| Requirement      | Minimum Version                               |
| ---------------- | --------------------------------------------- |
| Python           | 3.9+                                          |
| pip              | 22+                                           |
| Jupyter Notebook | Any                                           |
| Hardware         | CPU or GPU (≥8 GB VRAM recommended for speed) |

### Python Packages

```bash
pip install -U chromadb pandas transformers sentence-transformers langchain-community
```

> Note: `langchain-community` enables integration with ChromaDB and Hugging Face models.

---

## Notebook Walkthrough

| Step                  | Purpose                                                     |
| --------------------- | ----------------------------------------------------------- |
| **Setup Environment** | Install required packages (optional if preinstalled)        |
| **Load Data**         | Read and prepare MITRE/CISA CSV files                       |
| **Load Embeddings**   | Initialize local embedding model using SentenceTransformers |
| **Build ChromaDB**    | Create vector store with texts and metadata                 |
| **Test Database**     | Run similarity search queries and check stored vectors      |

---

## Customization Tips

- **Switch Embedding Model** → Try alternatives from Hugging Face (e.g., `paraphrase-MiniLM-L6-v2`).
- **Adjust Storage Path** → Change `persist_directory` to customize where vectors are saved.
- **Expand Data Sources** → Ingest more cybersecurity datasets or unstructured text.

---

## Next Steps

- Connect ChromaDB to an LLM with LangChain `RetrievalQA` for advanced Q&A.
- Build a RESTful API (e.g., with FastAPI) to expose search to applications.
- Automate updates with pipelines pulling the latest threat intel feeds.

---
