# 📄 Research Paper Question Answering using RAG

This project implements a **Retrieval-Augmented Generation (RAG)** system over research PDFs related to Transformer architectures and attention mechanisms.  
The system retrieves relevant document chunks using vector similarity search and generates **grounded, context-aware answers** using a Large Language Model, with a strong emphasis on **evaluation and failure analysis**.

Unlike demo-style RAG projects, this work focuses on understanding **why the system works, when it fails, and how it can be improved**.





---

## 🔧 System Architecture

PDF Documents
↓
Document Loading
↓
Chunking (500 tokens, 50 overlap)
↓
Embeddings (Sentence-Transformers: MiniLM)
↓
FAISS Vector Store
↓
Top-k Retriever
↓
Grounded Prompt
↓
LLM (Local via Ollama / OpenAI-compatible)
↓
Final Answer




---

## 🧠 Key Design Decisions

### Chunking Strategy
- **500 token chunk size with 50 token overlap**
- Evaluated against smaller chunks (300/30)
- Larger chunks preserved contextual completeness for research-style PDFs while reducing noise from boilerplate sections

### Embeddings
- `all-MiniLM-L6-v2`
- Lightweight, fast, and effective for semantic similarity search

### Vector Store
- **FAISS**
- Local, efficient, and easy to inspect for debugging retrieval behavior

### Prompting
- Strict grounding instruction:
  > *Answer only using the retrieved context. If the answer is not present, say "I don't know".*

This was intentionally designed to study hallucination behavior.

---

## 📊 Evaluation Methodology

The system was **manually evaluated** to assess real-world behavior instead of relying on synthetic metrics.

### Evaluation Dimensions
1. **Retrieval Relevance**  
   Whether the retrieved chunks actually contained the answer.
2. **Answer Faithfulness**  
   Whether the generated answer stayed within the retrieved context.

### Evaluation Artifacts
- `evaluation/questions.json` – curated evaluation questions  
- `evaluation/results.csv` – structured evaluation results  
- `evaluation/analysis.md` – detailed failure analysis and insights  

Manual evaluation was chosen to ensure **interpretability and honest assessment**.

---

## ⚠️ Observed Failure Cases

Several realistic failure patterns were identified:

- **Ambiguous queries** resulted in mixed retrieval and hallucinated synthesis  
- **High-level “why” questions** often led to partial grounding  
- **Background knowledge leakage**, where answers were correct but not explicitly retrieved  

These behaviors are documented in detail in `evaluation/analysis.md`.

---

## 📈 Evaluation Summary

- Definition-based questions showed **high retrieval relevance**
- Faithfulness degraded under ambiguity or incomplete context
- Failures were systematic, not random — indicating clear improvement paths

This evaluation reflects the **real limitations of RAG systems**, not idealized performance.

---

## 🚀 Future Improvements

The following enhancements were identified but intentionally left unimplemented to preserve evaluation integrity:

- Query rewriting for ambiguous inputs  
- Reranking retrieved chunks  
- Citation-based answer enforcement  
- Post-generation faithfulness verification  
- Fine-tuning via LoRA for domain adaptation  

---

## 🛠️ Tech Stack

- Python  
- Sentence-Transformers  
- FAISS  
- Ollama (local LLM inference)  
- OpenAI-compatible architecture  

---

## 📌 Key Takeaway

This project demonstrates not just how to build a RAG system, but how to **evaluate, analyze, and reason about its behavior and failure modes** — a critical skill for production AI systems.

