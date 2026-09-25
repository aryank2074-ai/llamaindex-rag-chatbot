# llamaindex-rag-chatbot
# LlamaIndex RAG Chatbot

## 📌 Overview
This project builds a complete **Retrieval-Augmented Generation (RAG)** pipeline using **LlamaIndex** and **OpenAI**. It loads local documents, splits them into chunks, generates embeddings, stores them in a searchable vector index, and answers natural-language questions by retrieving the most relevant chunks and passing them to an LLM as context — rather than relying on the LLM's raw memory alone.

```
Documents → Chunking → Embeddings → Vector Index → Retrieval → LLM → Answer
```

## 📊 What It Does
- Ingests local `.txt` documents (easily extendable to PDFs, `.docx`, etc.)
- Splits documents into overlapping chunks ("nodes") for better retrieval granularity
- Generates vector embeddings for each chunk using OpenAI's embedding model
- Builds and **persists** a vector index so documents don't need to be re-embedded on every run
- Exposes a **query engine** that retrieves the top-k most relevant chunks for any question
- Answers questions while showing exactly **which chunks were retrieved** (with similarity scores) — making the RAG process transparent instead of a black box
- Wraps everything in a reusable `ask()` function plus an optional interactive `chat()` loop

## 🛠️ Project Workflow

### 1. Setup
- Installs LlamaIndex core + OpenAI LLM/embedding integrations, Chroma vector store, PDF support, and `python-dotenv`
- Configures the OpenAI API key securely via `getpass` (never hardcoded or printed)
- Sets the global LLM (`gpt-4o-mini`, temperature=0 for deterministic answers) and embedding model (`text-embedding-3-small`)

### 2. Data Ingestion
- Creates a small set of sample documents (Python, Machine Learning, RAG topics)
- Loads them using `SimpleDirectoryReader`

### 3. Chunking
- Splits documents into ~100-token chunks with 20-token overlap using `SentenceSplitter`, so retrieval works on focused, meaningful text segments

### 4. Embedding & Indexing
- Embeds every chunk and stores the vectors in a `VectorStoreIndex`
- Persists the index to disk (`storage/`) to avoid re-embedding on subsequent runs

### 5. Retrieval & Querying
- Builds a query engine (`similarity_top_k=3`) that retrieves the most relevant chunks for a given question
- Tests the pipeline on both a single question and a batch of questions spanning all sample documents
- Prints the retrieved source chunks and similarity scores alongside each answer

### 6. Reusable Interface
- `ask(question)` — call the RAG system programmatically
- `chat()` — an interactive Q&A loop for live use inside the notebook

## 🧰 Tech Stack
- **Python**
- **LlamaIndex** – document loading, chunking, indexing, retrieval, query engine
- **OpenAI API** – LLM (`gpt-4o-mini`) and embeddings (`text-embedding-3-small`)
- **ChromaDB** *(optional)* – persistent vector store backend
- **python-dotenv** – environment variable / API key management

## 🚀 How to Run
1. Clone the repository
2. Install dependencies:
   ```
   pip install llama-index llama-index-llms-openai llama-index-embeddings-openai llama-index-vector-stores-chroma chromadb pypdf python-dotenv
   ```
3. Run the notebook `Llama_index_and_rag.ipynb`
4. Enter your OpenAI API key when prompted (or set it in a `.env` file as `OPENAI_API_KEY=...`)
5. Run all cells to build the index and see sample Q&A in action
6. Use `ask("your question")` or uncomment `chat()` to ask your own questions

## 📈 Key Takeaways
- RAG grounds LLM answers in your own data, reducing hallucination on domain-specific questions
- Persisting the vector index avoids the cost of re-embedding documents on every run
- Showing retrieved source chunks alongside answers makes the system's reasoning auditable
- The pipeline is easily extended to larger document sets, PDFs, or a persistent Chroma-backed vector store for production use

## 🔒 Security Note
API keys are entered securely via `getpass()` or loaded from a `.env` file — never hardcoded in the notebook. If you fork or share this project, double-check that no real API key is committed to version control.
