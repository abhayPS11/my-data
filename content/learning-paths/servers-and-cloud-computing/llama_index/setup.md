---
title: Build RAG applications with LlamaIndex on Google Cloud Axion
weight: 1

### FIXED, DO NOT MODIFY
layout: learningpathall
---

## Build RAG applications with LlamaIndex on Google Cloud Axion

In this guide, you will deploy a simple Retrieval-Augmented Generation (RAG) application using LlamaIndex on a Google Cloud C4A Axion Arm64 VM.

You will:

- Install Python 3.11
- Install Docker
- Install Ollama for local LLM inference
- Install LlamaIndex and ChromaDB
- Build a RAG pipeline
- Connect documents to an LLM
- Store embeddings in a vector database
- Expose the application using FastAPI
- Troubleshoot common Docker and Ollama issues

## What is LlamaIndex?

LlamaIndex is a framework for building context-aware LLM applications.

It helps developers:

- Connect custom data to LLMs
- Build indexing pipelines
- Create retrieval workflows
- Build RAG applications
- Integrate vector databases
- Serve AI applications


## Target environment

Use a Google Cloud Axion Arm64 VM:

```text
OS: SUSE Linux Enterprise Server 15 SP6
Architecture: ARM64 (aarch64)
Recommended VM: c4a-standard-4 or higher
RAM: 16 GB or higher
```

## Step 1: Verify VM architecture

Run:

```bash
uname -m
```

Expected output:

```output
aarch64
```

Check OS:

```bash
cat /etc/os-release
```

## Step 2: Update the system

```bash
sudo zypper refresh
sudo zypper update -y
```

Install required tools:

```bash
sudo zypper install -y \
git \
curl \
wget \
tar \
gzip \
gcc \
gcc-c++ \
make \
cmake \
sqlite3 \
python311 \
python311-pip \
python311-devel \
python311-setuptools \
python311-wheel
```


Step 3: Install Docker

Install Docker:

```bash
sudo zypper install -y docker
```

Enable Docker:

```bash
sudo systemctl enable docker
sudo systemctl start docker
```

Check Docker status:

```bash
sudo systemctl status docker
```

Add current user to Docker group:

```bash
sudo usermod -aG docker $USER
```

Apply group changes:

```bash
newgrp docker
```

Test Docker:

```bash
docker run hello-world
```

## Step 4: Fix common Docker errors

Error: permission denied

Fix:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Error: Cannot connect to the Docker daemon

Fix:

```bash
sudo systemctl start docker
sudo systemctl enable docker
```

Check status:

```bash
sudo systemctl status docker
```


Error: Docker service failed

Check logs:

```bash
sudo journalctl -u docker --no-pager -n 50
```

Restart Docker:

```bash
sudo systemctl restart docker
```


## Step 5: Create project directory

```bash
mkdir -p ~/llamaindex-rag/data
cd ~/llamaindex-rag
```

Create virtual environment:

```bash
python3.11 -m venv rag-env
```

Activate environment:

```bash
source rag-env/bin/activate
```

Upgrade pip:

```bash
pip install --upgrade pip setuptools wheel
```

## Step 6: Install Ollama

Install Ollama:

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Verify:

```bash
ollama -v
```

Start Ollama server:

```bash
ollama serve
```

Keep this terminal running.

## Step 7: Open a new terminal

Open a new SSH terminal.

Activate environment again:

```bash
cd ~/llamaindex-rag
source rag-env/bin/activate
```

## Step 8: Pull a lightweight model

Pull Llama 3.2 model:

```bash
ollama pull llama3.2:1b
```

Test model:

```bash
ollama run llama3.2:1b "Explain Retrieval-Augmented Generation."
```


## Step 9: Install LlamaIndex packages

```bash
pip install llama-index
pip install llama-index-llms-ollama
pip install llama-index-embeddings-huggingface
pip install llama-index-vector-stores-chroma
pip install chromadb
pip install sentence-transformers
pip install fastapi
pip install uvicorn
```


## Step 10: Create sample documents

Create first document:

```bash
cat > data/arm_cloud.txt <<'EOF'
Google Cloud Axion is a family of Arm-based processors optimized for cloud-native workloads and AI applications.
EOF
```

Create second document:

```bash
cat > data/rag.txt <<'EOF'
Retrieval-Augmented Generation combines vector search and large language models to generate grounded responses from custom data.
EOF
```

Create third document:

```bash
cat > data/llamaindex.txt <<'EOF'
LlamaIndex provides indexing, retrieval, query engines, chat engines, and vector database integrations for LLM applications.
EOF
```



## Step 11: Create the RAG application

```python
cat > rag_app.py <<'EOF'
import chromadb

from llama_index.core import (
    VectorStoreIndex,
    SimpleDirectoryReader,
    StorageContext,
    Settings
)

from llama_index.core.node_parser import SentenceSplitter

from llama_index.llms.ollama import Ollama

from llama_index.embeddings.huggingface import HuggingFaceEmbedding

from llama_index.vector_stores.chroma import ChromaVectorStore


DATA_DIR = "./data"
CHROMA_DIR = "./chroma_db"
COLLECTION_NAME = "llamaindex_demo"


def build_query_engine():

    llm = Ollama(
        model="llama3.2:1b",
        request_timeout=120.0
    )

    embed_model = HuggingFaceEmbedding(
        model_name="BAAI/bge-small-en-v1.5"
    )

    Settings.llm = llm
    Settings.embed_model = embed_model

    Settings.node_parser = SentenceSplitter(
        chunk_size=512,
        chunk_overlap=50
    )

    documents = SimpleDirectoryReader(
        DATA_DIR
    ).load_data()

    chroma_client = chromadb.PersistentClient(
        path=CHROMA_DIR
    )

    chroma_collection = chroma_client.get_or_create_collection(
        COLLECTION_NAME
    )

    vector_store = ChromaVectorStore(
        chroma_collection=chroma_collection
    )

    storage_context = StorageContext.from_defaults(
        vector_store=vector_store
    )

    index = VectorStoreIndex.from_documents(
        documents,
        storage_context=storage_context
    )

    query_engine = index.as_query_engine(
        similarity_top_k=3
    )

    return query_engine


if __name__ == "__main__":

    query_engine = build_query_engine()

    print("RAG application ready.")
    print("Type exit to quit.")

    while True:

        question = input("\nQuestion: ")

        if question.lower() == "exit":
            break

        response = query_engine.query(question)

        print("\nAnswer:")
        print(response)
EOF
```


## Step 12: Run the RAG application

Run:

```bash
python rag_app.py
```

Test questions:

```text
Question 1: What is LlamaIndex?

Question 2: What is RAG?

Quetion 3: What is Google Cloud Axion?
```

## Step 13: Verify Chroma vector database

Check files:

```bash
ls -lh
```

Check Chroma persistence:

```bash
ls -lh chroma_db
```


## Step 14: Create FastAPI application

```python
cat > api.py <<'EOF'
from fastapi import FastAPI
from pydantic import BaseModel

from rag_app import build_query_engine


app = FastAPI()

query_engine = build_query_engine()


class QueryRequest(BaseModel):
    question: str


@app.get("/")
def root():
    return {
        "message": "LlamaIndex RAG API running"
    }


@app.post("/query")
def query_rag(request: QueryRequest):

    response = query_engine.query(
        request.question
    )

    return {
        "question": request.question,
        "answer": str(response)
    }
EOF
```

## Step 15: Run FastAPI server

```text
uvicorn api:app --host 0.0.0.0 --port 8000
```


## Step 16: Test the API locally

Check API:

```text
curl http://127.0.0.1:8000/
```

Test query:

```bash
curl -X POST http://127.0.0.1:8000/query \
-H "Content-Type: application/json" \
-d '{"question":"What is RAG?"}'
```

## Step 17: Open GCP firewall

Create firewall rule:

```bash
gcloud compute firewall-rules create allow-llamaindex-8000 \
--allow tcp:8000 \
--source-ranges 0.0.0.0/0 \
--description "Allow LlamaIndex API"
```

## Get VM IP:

gcloud compute instances list

Access API:

```text
http://VM_EXTERNAL_IP:8000
```

Swagger UI:

```text
http://VM_EXTERNAL_IP:8000/docs
```

## What you have learned and what is next

You deployed a Retrieval-Augmented Generation (RAG) application using LlamaIndex on a Google Cloud Axion Arm64 VM. You installed Docker and Ollama, integrated ChromaDB as a vector database, indexed custom documents, and exposed the application using FastAPI.

Next, you can extend this setup by:

Adding PDF ingestion
Using larger LLM models
Connecting cloud object storage
Deploying with Kubernetes
Adding authentication
Using production vector databases
Building multi-user AI applications
