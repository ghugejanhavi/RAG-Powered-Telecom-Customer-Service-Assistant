# RAG-Powered Telecom Customer Service Assistant

An AI-powered customer service assistant built with Retrieval-Augmented Generation (RAG) that answers complex, policy-specific questions by grounding every response in a 22-document internal knowledge base — eliminating hallucinations and keeping answers consistent across agents.

---

## What This Project Does

Standard LLMs cannot answer company-specific questions accurately — they have no knowledge of internal pricing, SLA terms, or cancellation policies, and they hallucinate when asked. This project solves that by building a RAG pipeline that retrieves the right documents first, then generates a grounded answer.

The assistant is built for a telecom company's customer service team and can handle questions like:
- *"A customer on Fiber 250 is getting 150 Mbps — is this an SLA violation?"*
- *"What compensation does a Business Premium customer get for a 3-hour outage?"*
- *"Walk me through the cancellation process for a 24-month Fiber 500 contract."*

---

## Pipeline Architecture

The system was built in four progressively improved versions:

| Version | Strategy | What It Solves |
|---------|----------|----------------|
| 2a | Similarity Search + **Reranking** (Cohere) | Vector distance ≠ true relevance |
| 2b | + **Query Expansion** | Vocabulary mismatch between user query and document |
| 2c | + **Parent-Child Chunking** | Small chunks for precise retrieval, large chunks for rich LLM context |
| 2d | + **Metadata Filtering** | Narrows search to relevant folders before retrieval begins |

---

## Tech Stack

| Tool | Role |
|------|------|
| `LangChain` | Orchestration — loaders, splitters, chains |
| `ChromaDB` | Vector store |
| `OpenAI` (`text-embedding-ada-002`) | Embeddings |
| `GPT-4 Turbo` | Answer generation |
| `Cohere Rerank` (`rerank-english-v3.0`) | Post-retrieval reranking |
| `Gradio` | Chat interface |
| `Google Colab` | Runtime environment |

---

## Knowledge Base

22 Markdown files across 4 folders, each tagged with `doc_type` metadata:

```
Customer knowledge base/
├── customer-service/    # Complaint handling, escalation paths, SLA compensation
├── policies/            # Refund rules, early termination fees, uptime guarantees
├── procedures/          # Cancellation, equipment return, provisioning steps
└── products/            # Plans, pricing, bundles, add-ons
```

---

## How to Run

1. Upload the `Customer knowledge base` folder to your Google Drive
2. Add your API keys to Colab Secrets:
   - `OPENAI_API_KEY`
   - `CO_API_KEY` (Cohere)
3. Open `RAG_Powered_Telecom_Customer_Service_Assistant.ipynb` in Google Colab
4. Run all cells in order
5. The Gradio interface will launch at the end — ask it anything about the telecom knowledge base

---

## Evaluation

Six test prompts were run against the final pipeline (Version 2d), ranging from medium to very hard difficulty. Results are documented directly in the notebook with Gradio screenshots and written analysis covering:
- Metadata filter accuracy
- Response quality and groundedness
- System limitations and failure cases

**Key findings:**
- The system performed excellently on medium-difficulty prompts where the question mapped cleanly to one folder. For example, comparing all postpaid mobile plans returned accurate prices, features, and recommendations pulled directly from the knowledge base.
- Parent-child chunking made a real difference on complex multi-part questions. By searching small 400-character chunks but passing 2000-character parent chunks to the LLM, the system could answer all four sub-questions in the Business Premium SLA prompt without losing context between them.
- The one consistent weak spot was metadata filtering for cancellation and billing queries. The LLM classifier sometimes only detected `procedures` and missed `policies`, which is where refund rules and early termination fees actually live, causing the system to fall back on generic estimates instead of real policy numbers.

---

## Author

**Janhavi Ghuge**  
Course: BNAN 570 — Generative AI  
