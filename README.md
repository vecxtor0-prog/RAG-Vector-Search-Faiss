# RAG-Vector-Search-Faiss
 SDAIA Program for Developing AI Solution 
A simple Retrieval-Augmented Generation (RAG) and semantic search project built in Google Colab.

This project reads three .txt files from different fields, applies dynamic text chunking, converts the chunks into numerical embeddings using a local Sentence Transformers model, stores the vectors in a FAISS vector database, and retrieves the top 3 most relevant chunks using cosine similarity.

The project does not require an API key.

Project Features

Reads exactly three .txt files

Supports documents from different fields or topics

Uses Dynamic Text Chunking

Generates dense vector embeddings

Uses a local embedding model

Stores embeddings in FAISS

Saves the vector database for future use

Uses cosine similarity for semantic search

Returns the Top 3 most relevant chunks

Displays:

Question

Retrieved text

Source file

Chunk number

Cosine similarity score

Final answer

Phase 2 does not repeat Phase 1

No OpenAI or Gemini API key is required

Technologies Used

Python

Google Colab

Sentence Transformers

FAISS

NumPy

Pickle

Embedding Model

The project uses the following local embedding model:

sentence-transformers/all-MiniLM-L6-v2

The model converts text into 384-dimensional dense vectors.

The same embedding model is used for both:

Document chunks in Phase 1

User queries in Phase 2

Using the same embedding model is important because the document vectors and query vector must exist in the same vector space.

Project Workflow

The project is divided into two main phases.

Phase 1 — Build the Vector Database

Phase 1 is used to create the vector database.

It should only be executed when creating or rebuilding the database.

The workflow is:

Three TXT Files
      ↓
Load Documents
      ↓
Dynamic Text Chunking
      ↓
Sentence Transformer
      ↓
Generate Embeddings
      ↓
L2 Normalization
      ↓
FAISS Vector Database
      ↓
Save Database + Metadata

Phase 1 produces two files:

vector_database.faiss
chunk_metadata.pkl

These files are saved and reused during Phase 2.

Phase 2 — Vector Similarity Search

Phase 2 is used for every new search query.

It does not repeat document loading, chunking, or document embedding generation.

The workflow is:

User Question
      ↓
Same Embedding Model
      ↓
Query Vector
      ↓
L2 Normalization
      ↓
FAISS Search
      ↓
Cosine Similarity
      ↓
Rank Results
      ↓
Top 3 Relevant Chunks
      ↓
Question + Answer + References

Only the query is converted into an embedding during Phase 2.

The existing FAISS database is loaded from:

vector_database.faiss

The chunk information and file references are loaded from:

chunk_metadata.pkl

Dynamic Text Chunking

Instead of splitting documents into fixed-size pieces without considering text structure, the project uses dynamic chunking.

The chunking process:

Splits the document into paragraphs

Keeps paragraphs together when possible

Combines smaller paragraphs into larger chunks

Splits very large paragraphs by sentences

Keeps each chunk below a defined maximum size

Example:

Document
   ↓
Paragraph 1
Paragraph 2
Paragraph 3
   ↓
Dynamic Chunking
   ↓
Chunk 1
Chunk 2
Chunk 3

This approach helps preserve more meaningful context compared with basic fixed-character splitting.

Vector Embeddings

Each generated text chunk is passed into the Sentence Transformers model.

Example:

vector = model.encode(
    text,
    convert_to_numpy=True
)

The text is transformed into a dense numerical vector.

For example:

"Saudi Arabia has many traditional foods."

becomes a mathematical representation similar to:

[0.021, -0.114, 0.087, ..., 0.042]

These embeddings allow the system to compare text based on semantic meaning instead of only matching exact words.

FAISS Vector Database

FAISS is used as the vector database.

The project creates the index using:

index = faiss.IndexFlatIP(dimension)

The document vectors are then added to the index:

index.add(embedding_matrix)

The FAISS database is saved using:

faiss.write_index(
    index,
    "vector_database.faiss"
)

This allows Phase 2 to load the existing database without regenerating all document embeddings.

Cosine Similarity

The assignment requires cosine similarity between the user query and stored document chunks.

The project normalizes document vectors using:

faiss.normalize_L2(
    embedding_matrix
)

The query vector is also normalized:

faiss.normalize_L2(
    query_vector
)

After L2 normalization, inner product search using:

faiss.IndexFlatIP

produces cosine similarity scores.

Cosine similarity can be represented as:

cosine_similarity(A, B)
=
(A · B) / (||A|| × ||B||)

A higher score means that the query and document chunk are more semantically similar.

A score closer to 1.0 generally indicates greater similarity.

Top 3 Retrieval

For each user question, FAISS searches the database and returns the three most similar chunks.

Example:

Question:
What are some popular foods in Saudi Arabia?

Top 3 Results:

Rank #1
Reference File: saudi_food.txt
Chunk Number: 2
Cosine Similarity Score: 0.8124

Rank #2
Reference File: saudi_food.txt
Chunk Number: 4
Cosine Similarity Score: 0.7451

Rank #3
Reference File: saudi_food.txt
Chunk Number: 1
Cosine Similarity Score: 0.6928

The most relevant chunk is also displayed as the final answer.

Project Files

A possible repository structure is:

rag-vector-search-faiss/
│
├── README.md
│
├── rag_vector_search.ipynb
│
├── sample_data/
│   ├── football.txt
│   ├── saudi_food.txt
│   └── artificial_intelligence.txt
│
└── requirements.txt

The generated database files are:

vector_database.faiss
chunk_metadata.pkl

These files may also be stored locally and uploaded when running Phase 2.

Installation

In Google Colab, install the required libraries using:

!pip install -q sentence-transformers faiss-cpu numpy

Then import the required libraries:

from google.colab import files
from sentence_transformers import SentenceTransformer

import numpy as np
import faiss
import pickle
import re
import os

How to Run

First Run

Run:

Cell 1
↓
Cell 2

Then upload three .txt files.

Example:

football.txt
saudi_food.txt
artificial_intelligence.txt

Phase 1 creates:

vector_database.faiss
chunk_metadata.pkl

Download and keep both files.

Future Searches

For future searches, run:

Cell 1
↓
Cell 3

Upload only:

vector_database.faiss
chunk_metadata.pkl

Then enter your question.

You do not need to upload the original three documents again.

You also do not need to regenerate their embeddings.

Example Query

What is a popular traditional food in Saudi Arabia?

Example output:

QUESTION

What is a popular traditional food in Saudi Arabia?


TOP 3 MOST RELEVANT CHUNKS

Rank #1
Reference File: saudi_food.txt
Chunk Number: 1
Cosine Similarity Score: 0.8231

Retrieved Text:
Kabsa is one of the most famous traditional dishes in Saudi Arabia...


FINAL RESULT

Question:
What is a popular traditional food in Saudi Arabia?

Answer:
Kabsa is one of the most famous traditional dishes in Saudi Arabia...

Assignment Requirements

Requirement

Implementation

Read three .txt files

Yes

Files from different fields

Yes

Semantic or Dynamic Chunking

Dynamic Chunking

Generate embeddings

Sentence Transformers

Store embeddings in Vector DB

FAISS

Save Vector DB

Yes

Phase 2 does not repeat Phase 1

Yes

Accept natural-language query

Yes

Same embedding model for query

Yes

Cosine Similarity

Yes

Rank retrieved chunks

Yes

Return Top 3 chunks

Yes

Display filename references

Yes

Display similarity scores

Yes

Print Question and Answer

Yes

API key required

No

Important Note

This project does not use a generative AI model such as Gemini, ChatGPT, or another LLM.

The local Sentence Transformers model is used only to generate embeddings.

Because there is no generative language model, the final answer is taken directly from the highest-ranked retrieved chunk.

The main goal of the project is to demonstrate:

Dynamic chunking

Text vectorization

Vector database storage

Semantic retrieval

Cosine similarity

Top-K ranking

Document references

Advantages

This implementation has several advantages:

No API cost

No API key required

Works locally after the embedding model is available

Simple architecture

Fast vector search using FAISS

Reusable vector database

Phase 2 avoids unnecessary document processing

Supports documents from multiple fields

Repository

Suggested repository name:

rag-vector-search-faiss

Suggested GitHub description:

RAG and semantic search pipeline using dynamic chunking, local embeddings, FAISS vector database, and cosine similarity.

Author

Created as part of an Information Retrieval and Retrieval-Augmented Generation assignment.
