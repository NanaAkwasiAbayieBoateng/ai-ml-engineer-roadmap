# P3 — RAG Application over Real Documents

**Stage:** Intermediate · **Builds on:** P1's serving habits · **Goal:** a deployed retrieval app with measured quality — the eval report is the deliverable hiring managers actually read.

1. **Pick the corpus and scope it.** A few hundred real documents you can legally use — e.g. public fraud/AML guidance, product documentation, or research papers. Write down the 5–10 question types the app must answer.
2. **Ingestion pipeline:** parse PDFs/HTML to clean text, strip boilerplate, dedupe, and store each document with metadata (title, source, date) you will later use for citations and filtering.
3. **Chunking experiment:** implement three strategies — fixed-size with overlap, recursive splitting, and structure-aware (by heading/section). Keep chunking configurable; you will compare them, not guess.
4. **Embeddings and index:** embed the chunks with one embedding model, index in pgvector or Qdrant, and store the metadata alongside each vector.
5. **Build the evaluation set before tuning anything:** 30–50 questions with the source passage that answers each. Measure retrieval with recall@k and MRR.
6. **Retrieval baseline:** vector-only search behind a FastAPI endpoint. Log every query and what it retrieved.
7. **Improve retrieval:** add hybrid search (BM25 + vector) and a reranker. Re-run the eval set after each change and keep a results table — chunking strategy × retrieval method × score.
8. **Generation layer:** a prompt template that answers only from retrieved passages, cites them, and refuses when the evidence is not there. Test the refusal behavior explicitly.
9. **Serve it:** FastAPI + a minimal UI (Streamlit or one HTML page), deployed the same way as P1, with the model/index versions visible.
10. **Light monitoring:** log low-confidence and refused queries; sample them weekly and score a few with an LLM judge.

**Done when:** a live URL plus a written eval report comparing chunking and retrieval variants with numbers.
