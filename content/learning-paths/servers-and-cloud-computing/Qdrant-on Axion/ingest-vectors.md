---
title: Generate and Index Vectors
weight: 5

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Generate and Index Vectors

This step demonstrates how to generate embeddings and store them in Qdrant.


## Install Python Libraries

```bash
python3.11 -m pip install qdrant-client sentence-transformers
```

## Create Project Directory

```bash
mkdir qdrant-rag-demo
cd qdrant-rag-demo
```

## Create the Ingestion Script

Create the file:

```python
ingest.py
from qdrant_client import QdrantClient
from qdrant_client.models import VectorParams, Distance, PointStruct
from sentence_transformers import SentenceTransformer

client = QdrantClient(url="http://localhost:6333")

collection_name = "axion_demo"

client.recreate_collection(
    collection_name=collection_name,
    vectors_config=VectorParams(size=384, distance=Distance.COSINE),
)

model = SentenceTransformer("sentence-transformers/all-MiniLM-L6-v2")

documents = [
    "Axion processors provide Arm based cloud compute.",
    "Vector databases enable semantic search.",
    "Qdrant is optimized for vector similarity search.",
    "RAG pipelines combine retrieval with LLMs."
]

vectors = model.encode(documents)

points = [
    PointStruct(id=i, vector=vectors[i].tolist(), payload={"text": documents[i]})
    for i in range(len(documents))
]

client.upsert(collection_name=collection_name, points=points)

print("Documents indexed successfully in Qdrant")
```

## Run the Script

```bash
python3.11 ingest.py
```

```output
Documents indexed successfully in Qdrant!
```
## Verify Collection

```bash
curl http://localhost:6333/collections
```

```output
{"result":{"collections":[{"name":"axion_demo"}]},"status":"ok","time":4.01e-6}
```


