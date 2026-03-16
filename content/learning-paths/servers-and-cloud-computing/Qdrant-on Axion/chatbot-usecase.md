---
title: Chatbot Use Case with Qdrant
weight: 7

### FIXED, DO NOT MODIFY
layout: learningpathall
---

# Chatbot Use Case with Qdrant on Axion (Arm64)

## Overview

This section demonstrates how a **chatbot can retrieve knowledge using vector similarity search** powered by Qdrant running on Arm-based Axion infrastructure.

The chatbot uses:

- **Sentence Transformers** to generate embeddings
- **Qdrant** to store and retrieve vectors
- **Python** to create a simple chatbot interface

This is the **retrieval component used in Retrieval-Augmented Generation (RAG) systems**.


## Navigate to Project Directory

You can move to the project directory created earlier.

```bash
cd ~/qdrant-rag-demo
```

**Verify files:**

```bash
ls
```

The output is similar to: 
```output
ingest.py
search.py
```

## Load Knowledge into Qdrant

The ingestion script converts documents into embeddings and stores them in Qdrant.

**Run the script:**

```bash
python3.11 ingest.py
```

The output is similar to: 
```output
Documents indexed successfully in Qdrant!
```

**Verify the collection:**

curl http://localhost:6333/collections

The output is similar to: 
```output

 "result": {
  "collections":[{"name":"axion_demo"}]
 }
}
```

## Create Chatbot Script

Create a new file:

```bash
vi chatbot.py
```

Paste the following code.

```python
from qdrant_client import QdrantClient
from sentence_transformers import SentenceTransformer

client = QdrantClient(url="http://localhost:6333")

model = SentenceTransformer("sentence-transformers/all-MiniLM-L6-v2")

print("Chatbot ready! Type a question or 'exit' to quit.")

while True:

    query = input("\nUser: ")

    if query.lower() == "exit":
        break

    query_vector = model.encode(query).tolist()

    results = client.query_points(
        collection_name="axion_demo",
        query=query_vector,
        limit=2
    )

    print("\nChatbot Response:\n")

    for point in results.points:
        print("-", point.payload["text"])
```

## Run the Chatbot

Run the chatbot application.

```bash
python3.11 chatbot.py
```

The output is similar to: 
```output
Loading weights: 100%|██████████████████████████████████████████████████████████████████████████| 103/103 [00:00<00:00, 9100.57it/s]
BertModel LOAD REPORT from: sentence-transformers/all-MiniLM-L6-v2
Key                     | Status     |  |
------------------------+------------+--+-
embeddings.position_ids | UNEXPECTED |  |

Notes:
- UNEXPECTED    :can be ignored when loading from different task/architecture; not ok if you expect identical arch.
Chatbot ready! Ask a question (type 'exit' to quit)

User:
```

## Test the Chatbot

Example interaction:

```bash
User: What is Qdrant?
```

The output is similar to: 
```output
Chatbot Response:

- Qdrant is optimized for vector similarity search.
- Vector databases enable semantic search.
```


**Another example:**

```bash
User: what are rag pipelines?
```

The output is similar to: 
```output
Chatbot Response:

- RAG pipelines combine retrieval with LLMs.
- Axion processors provide Arm based cloud compute.
```

**Exit the chatbot:**

```bash
exit
```

## How the Chatbot Works

1. The user asks a question.
2. The system converts the question into a vector embedding.
3. Qdrant performs a similarity search.
4. The chatbot returns the most relevant knowledge documents.
5. This enables semantic search instead of simple keyword matching.

## Real-World Applications

This architecture is widely used in:

- Customer support chatbots
- Enterprise knowledge assistants
- AI documentation search tools
- Technical support bots
- RAG-based AI assistants
