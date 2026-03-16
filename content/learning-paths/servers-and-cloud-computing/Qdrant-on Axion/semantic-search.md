---
title: Perform Semantic Search
weight: 6

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Perform Semantic Search

This section demonstrates how to query the vector database using semantic similarity search.

## Create the Search Script

Create:


```python
from qdrant_client import QdrantClient
from sentence_transformers import SentenceTransformer

client = QdrantClient(url="http://localhost:6333")

model = SentenceTransformer("sentence-transformers/all-MiniLM-L6-v2")

query = "What is vector search?"

query_vector = model.encode(query).tolist()

results = client.query_points(
    collection_name="axion_demo",
    query=query_vector,
    limit=2
)

print("\nTop results:\n")

for point in results.points:
    print(point.payload["text"])
```

## Run Search

```bash
python3.11 search.py
```

The output is similar to:
```output
Vector databases enable semantic search.
Qdrant is optimized for vector similarity search.
```


This demonstrates semantic similarity search using vector embeddings.
