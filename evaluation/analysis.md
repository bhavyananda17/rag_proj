# RAG Evaluation Analysis

This document presents a qualitative analysis of the Retrieval-Augmented Generation (RAG) system based on manual evaluation of question–answer pairs.  
The goal of this analysis is to understand **why the system succeeds or fails**, not just whether it works.

---

## Evaluation Setup

- **Documents:** Research PDFs related to Transformer architectures and attention mechanisms  
- **Chunking Strategy:** 500 tokens with 50-token overlap  
- **Embedding Model:** all-MiniLM-L6-v2  
- **Vector Store:** FAISS  
- **LLM:** Local LLM (Ollama)  
- **Prompting:** Strictly grounded prompt (“answer only using the context”)

Evaluation focused on two dimensions:
1. **Retrieval Relevance** – whether retrieved chunks contained the answer  
2. **Answer Faithfulness** – whether the answer stayed within retrieved context  

---

## Summary of Results

Across the evaluated questions:

- Retrieval was generally **effective for definition-based questions**
- Faithfulness degraded for:
  - ambiguous queries
  - high-level “explain” or “why” questions
- Some answers included **true but unstated background knowledge**, which was marked as partial faithfulness

This behavior is consistent with real-world RAG systems.

---

## Detailed Failure Analysis

### Failure Case 1: Mild Extrapolation on Definition Questions

**Question:**  
What is attention mechanism?

**Observed Behavior:**  
- Retrieved chunks clearly defined attention as a query–key–value weighted sum  
- The generated answer additionally mentioned:
  > “used in deep learning, particularly in models like Transformers”

**Why This Failed:**  
- While factually correct, this information was **not explicitly present** in the retrieved chunks  
- The LLM filled gaps using prior knowledge despite grounding instructions

**Classification:**  
- Retrieval Relevance: Yes  
- Answer Faithfulness: Partial  

**Insight:**  
Even with strict prompting, LLMs may add general background context when the retrieved information is broad.

---

### Failure Case 2: Ambiguous Query Leading to Hallucination

**Question:**  
Explain attention in transformers

**Observed Behavior:**  
- Retrieved chunks covered attention concepts but from multiple sections  
- The answer included:
  - encoder–decoder interactions
  - multi-head attention details
  - downstream task examples (translation, summarization)

**Why This Failed:**  
- The query was **too broad**
- Retrieval returned heterogeneous chunks
- The LLM synthesized a coherent but **unsupported architectural explanation**

**Classification:**  
- Retrieval Relevance: Partial  
- Answer Faithfulness: No  

**Insight:**  
Ambiguous queries significantly increase hallucination risk in RAG systems.

---

### Failure Case 3: Partial Grounding for High-Level “Why” Questions

**Question:**  
What are the computational advantages of attention over recurrence?

**Observed Behavior:**  
- Retrieved chunks hinted at parallelization
- The answer discussed:
  - factorization tricks
  - conditional computation
  - reduced sequential constraints

**Why This Failed:**  
- Some efficiency claims were **not explicitly retrieved**
- The model inferred advanced concepts from general Transformer literature

**Classification:**  
- Retrieval Relevance: Partial  
- Answer Faithfulness: Partial  

**Insight:**  
High-level analytical questions require either:
- more targeted retrieval
- or explicit source coverage in documents

---

## Key Patterns Observed

1. **Definition-based questions**  
   → High retrieval relevance, mostly faithful answers  

2. **Ambiguous or broad questions**  
   → Mixed retrieval, hallucinated synthesis  

3. **Why / advantage questions**  
   → Partial grounding due to missing explicit explanations  

These patterns align with known limitations of RAG systems.

---

## Potential Improvements (Not Implemented)

The following techniques could improve performance:

- Query rewriting for ambiguous questions  
- Reranking retrieved chunks to reduce noise  
- Citation-based answer enforcement  
- Post-generation faithfulness verification  

These were intentionally left as future work to preserve evaluation integrity.

---

## Conclusion

This evaluation demonstrates that while the RAG pipeline reliably retrieves relevant information for concrete questions, answer faithfulness degrades under ambiguity or incomplete context.  
Understanding and documenting these failure modes is essential for building reliable RAG systems.

