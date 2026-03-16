---
title: Architecture
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Chatbot Architecture Using Qdrant

This example demonstrates how a chatbot retrieves knowledge using vector similarity search.

## System Architecture

```text
User Question
      │
      ▼
Embedding Model
(Sentence Transformer)
      │
      ▼
Vector Representation
      │
      ▼
Qdrant Vector Database
(Vector Similarity Search)
      │
      ▼
Top Matching Knowledge
      │
      ▼
Chatbot Response
```

## Components

**Embedding Model**

The embedding model converts text into numerical vectors representing semantic meaning.

****Example model used:**

```text
sentence-transformers/all-MiniLM-L6-v2
```

## Vector Database (Qdrant)
Qdrant stores and indexes vector embeddings.

It enables fast **nearest-neighbor similarity search**.

Key capabilities:

- high performance vector indexing
- semantic similarity search
- scalable vector storage

## Knowledge Base

The system stores knowledge documents such as:

- technical documentation
- support articles
- FAQs
- internal company knowledge

These documents are converted into embeddings during ingestion.

## Chatbot Query Engine

When the user asks a question:

1. The query is converted into an embedding
2. Qdrant searches for the closest vectors
3. The chatbot returns relevant information

## Benefits of This Architecture

This design provides several advantages:

- semantic search instead of keyword matching
- scalable knowledge retrieval
- faster query responses
- efficient AI workloads on Arm infrastructure

## Running on Axion

This example demonstrates that Axion Arm infrastructure can efficiently run vector search workloads.

- Benefits include:
- energy-efficient compute
- scalable cloud infrastructure
- optimized performance for AI workloads
