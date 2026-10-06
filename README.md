<div align="center">

# 🔎 RAG Vector Search with FAISS

### Local Semantic Search • Dynamic Chunking • FAISS • Cosine Similarity

A lightweight **Retrieval-Augmented Generation (RAG)** style information retrieval project built in **Google Colab** using local embeddings and a FAISS vector database.
Developed in Training on SDAIA Develop AI Solutions 

No API key required.

![Python](https://img.shields.io/badge/Python-3.x-blue?logo=python)
![Google Colab](https://img.shields.io/badge/Google%20Colab-Notebook-orange?logo=googlecolab)
![FAISS](https://img.shields.io/badge/Vector%20DB-FAISS-green)
![Sentence Transformers](https://img.shields.io/badge/Embeddings-SentenceTransformers-purple)
![API](https://img.shields.io/badge/API%20Key-Not%20Required-brightgreen)

</div>

---

## 📌 Overview

This project demonstrates a complete semantic retrieval pipeline using **three `.txt` files from different fields**.

It:

- loads three text documents
- applies **Dynamic Text Chunking**
- converts chunks into dense vector embeddings
- stores vectors inside a **FAISS vector database**
- saves the database for later use
- accepts user questions in natural language
- converts the query using the **same embedding model**
- calculates **Cosine Similarity**
- ranks the most relevant document chunks
- returns the **Top 3 results**
- displays the source file, chunk number, similarity score, and final answer

---

## ✨ Features

✅ Three text files from different domains

✅ Dynamic paragraph- and sentence-aware chunking

✅ Local embedding generation

✅ FAISS vector database

✅ Persistent database storage

✅ Cosine Similarity search

✅ Top-3 semantic retrieval

✅ Source file references

✅ Similarity scores

✅ No Gemini API

✅ No OpenAI API

✅ No API key required

✅ Phase 2 does not rebuild Phase 1

---

# 🧠 System Architecture

```text
┌─────────────────────────────┐
│       Three TXT Files       │
│                             │
│  football.txt               │
│  saudi_food.txt             │
│  artificial_intelligence.txt│
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│    Dynamic Text Chunking    │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│   Sentence Transformer      │
│   all-MiniLM-L6-v2          │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│    384-Dimensional Vectors  │
└──────────────┬──────────────┘
               │
               ▼
┌─────────────────────────────┐
│         FAISS Vector DB     │
└─────────────────────────────┘
```

---

# ⚙️ Project Workflow

The project is separated into two phases.

## Phase 1 — Build the Vector Database

Phase 1 is executed when the database is created.

```text
3 TXT Documents
       │
       ▼
Load Documents
       │
       ▼
Dynamic Chunking
       │
       ▼
Generate Embeddings
       │
       ▼
L2 Normalization
       │
       ▼
FAISS Vector Database
       │
       ▼
Save Database + Metadata
```

### Output files

Phase 1 produces:

```text
vector_database.faiss
chunk_metadata.pkl
```

These files are reused later.

> **Important:** Phase 1 does not need to run again for every new question.

---

## Phase 2 — Semantic Vector Search

Phase 2 loads the existing database and performs retrieval.

```text
User Question
      │
      ▼
Same Embedding Model
      │
      ▼
Query Vector
      │
      ▼
L2 Normalization
      │
      ▼
FAISS Search
      │
      ▼
Cosine Similarity
      │
      ▼
Rank Results
      │
      ▼
Top 3 Relevant Chunks
      │
      ▼
Answer + Sources + Scores
```

Only the **new user query** is embedded during Phase 2.

The original documents are not reprocessed.

---

# 🤖 Embedding Model

The project uses:

```text
sentence-transformers/all-MiniLM-L6-v2
```

This model converts text into **384-dimensional dense vectors**.

Example:

```text
"Saudi Arabia has many traditional foods."
```

becomes a vector similar to:

```text
[0.021, -0.114, 0.087, ..., 0.042]
```

The exact same embedding model is used for:

```text
Document Chunks
       +
User Query
```

This ensures that both are represented inside the same semantic vector space.

---

# ✂️ Dynamic Text Chunking

Instead of cutting documents blindly every fixed number of characters, this project uses a dynamic chunking strategy.

### The chunking process

```text
Document
   │
   ▼
Split into Paragraphs
   │
   ▼
Combine Small Paragraphs
   │
   ▼
Split Large Paragraphs
by Sentence
   │
   ▼
Final Semantic Chunks
```

This helps preserve context and keeps related information together.

---

# 🗄️ FAISS Vector Database

FAISS is used to store and search document embeddings.

The index is created using:

```python
index = faiss.IndexFlatIP(dimension)
```

Vectors are added using:

```python
index.add(embedding_matrix)
```

The database is then saved:

```python
faiss.write_index(
    index,
    "vector_database.faiss"
)
```

The metadata is saved separately:

```text
chunk_metadata.pkl
```

Metadata contains:

```text
Chunk Text
Source File
Chunk Number
Embedding Model
Vector Dimension
```

---

# 📐 Cosine Similarity

The project uses cosine similarity to measure semantic similarity between the user query and document chunks.

The mathematical formula is:

```text
                     A · B
Cosine Similarity = ─────────────
                    ||A|| × ||B||
```

Both document and query vectors are normalized:

```python
faiss.normalize_L2(embedding_matrix)
```

and:

```python
faiss.normalize_L2(query_vector)
```

Because normalized vectors are used with:

```python
faiss.IndexFlatIP
```

the returned inner-product value becomes equivalent to **Cosine Similarity**.

### Score interpretation

```text
Closer to 1.0  → More semantically similar
Closer to 0.0  → Less semantically similar
```

---

# 🔍 Top-3 Retrieval

For every question, FAISS returns the three most semantically similar chunks.

Example:

```text
QUESTION

What is a popular traditional food in Saudi Arabia?


TOP 3 MOST RELEVANT CHUNKS

Rank #1
Reference File: saudi_food.txt
Chunk Number: 2
Cosine Similarity Score: 0.8241

Rank #2
Reference File: saudi_food.txt
Chunk Number: 1
Cosine Similarity Score: 0.7618

Rank #3
Reference File: saudi_food.txt
Chunk Number: 4
Cosine Similarity Score: 0.6912
```

---

# 💬 Example Final Result

```text
============================================================
FINAL RESULT
============================================================

Question:
What is a popular traditional food in Saudi Arabia?

Answer:
Kabsa is one of the most famous traditional dishes in
Saudi Arabia. It is usually made with rice, chicken or lamb,
tomatoes, onions, and spices.

References:

- saudi_food.txt
  Chunk 2
  Cosine Similarity: 0.8241

- saudi_food.txt
  Chunk 1
  Cosine Similarity: 0.7618

- saudi_food.txt
  Chunk 4
  Cosine Similarity: 0.6912
```

---

# 📂 Repository Structure

```text
rag-vector-search-faiss/
│
├── README.md
│
├── rag_vector_search.ipynb
│
├── requirements.txt
│
├── sample_data/
│   ├── football.txt
│   ├── saudi_food.txt
│   └── artificial_intelligence.txt
│
└── generated/
    ├── vector_database.faiss
    └── chunk_metadata.pkl
```

---

# 🚀 Getting Started

## 1. Open the Notebook

Open:

```text
rag_vector_search.ipynb
```

inside Google Colab.

---

## 2. Install Dependencies

```python
!pip install -q sentence-transformers faiss-cpu numpy
```

---

## 3. Run Phase 1

Run:

```text
Cell 1
   ↓
Cell 2
```

Upload exactly three `.txt` files.

Example:

```text
football.txt
saudi_food.txt
artificial_intelligence.txt
```

Phase 1 creates:

```text
vector_database.faiss
chunk_metadata.pkl
```

Download and save both files.

---

## 4. Run Phase 2

For future searches:

```text
Cell 1
   ↓
Cell 3
```

Upload:

```text
vector_database.faiss
chunk_metadata.pkl
```

Then enter your question.

Example:

```text
What is a famous Saudi traditional dish?
```

---

# 🔄 Phase Comparison

| Phase | Purpose | Runs |
|---|---|---|
| **Phase 1** | Build vector database | Once |
| **Phase 2** | Search existing database | Every query |

### Phase 1 performs

```text
Load
Chunk
Embed
Normalize
Store
Save
```

### Phase 2 performs

```text
Load DB
Embed Query
Normalize Query
Search
Rank
Return Top 3
```

---

# ✅ Assignment Requirements

| Requirement | Status | Implementation |
|---|:---:|---|
| Read 3 `.txt` files | ✅ | Google Colab upload |
| Documents from different fields | ✅ | Any three domains |
| Dynamic / Semantic Chunking | ✅ | Paragraph + sentence-aware |
| Generate embeddings | ✅ | Sentence Transformers |
| Store vectors | ✅ | FAISS |
| Save Vector DB | ✅ | `.faiss` file |
| Phase 2 does not repeat Phase 1 | ✅ | Existing database is loaded |
| Accept natural-language query | ✅ | Python `input()` |
| Same embedding model | ✅ | MiniLM |
| Cosine Similarity | ✅ | L2 + Inner Product |
| Rank results | ✅ | FAISS ranking |
| Return Top 3 | ✅ | `top_k=3` |
| File references | ✅ | Metadata |
| Similarity scores | ✅ | Cosine scores |
| Question + Answer | ✅ | Console output |
| API key | ❌ Not required | Local model |

---

# 🛠️ Technologies

| Technology | Purpose |
|---|---|
| 🐍 Python | Main programming language |
| 📓 Google Colab | Notebook environment |
| 🤗 Sentence Transformers | Text embeddings |
| 🔎 FAISS | Vector database and similarity search |
| 🔢 NumPy | Numerical vector operations |
| 💾 Pickle | Metadata persistence |

---

# 🔐 No API Key Required

This implementation does not use:

```text
❌ OpenAI API
❌ Gemini API
❌ Claude API
❌ External generation API
```

It uses a local Sentence Transformers model for embeddings.

This makes the project:

```text
✅ Free to run
✅ Simple to reproduce
✅ Independent from API limits
✅ Suitable for academic demonstrations
```

---

# ⚠️ Important Note

This project does **not** use a generative LLM to rewrite or summarize the final answer.

The final answer is taken directly from the **highest-ranked retrieved text chunk**.

Therefore, this project primarily demonstrates the **retrieval component of a RAG system**, including:

- chunking
- embeddings
- vector storage
- semantic search
- cosine similarity
- ranking
- source attribution

---

# 🎯 Learning Objectives

By completing this project, you can understand how modern retrieval systems work before introducing a full generative language model.

Key concepts include:

```text
Document Processing
        ↓
Chunking
        ↓
Embeddings
        ↓
Vector Database
        ↓
Similarity Search
        ↓
Top-K Retrieval
        ↓
Relevant Answer
```

---

# 📌 Repository Information

**Repository Name**

```text
RAG-Vector-Search-Faiss
```

**Description**

```text
Semantic search pipeline using dynamic chunking, local embeddings,
FAISS vector database, cosine similarity, and Top-K retrieval.
```

---

<div align="center">

## ⭐ RAG Vector Search with FAISS

Built with **Python • Sentence Transformers • FAISS • Google Colab**

**No API key required.**

</div>
