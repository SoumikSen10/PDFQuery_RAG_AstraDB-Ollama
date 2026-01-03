````markdown
# PDF Query RAG with AstraDB & Ollama

A Retrieval-Augmented Generation (RAG) system for querying PDF documents. Extracts text from PDFs, stores embeddings in **AstraDB**, and answers questions using **Ollama LLM**. No paid APIs required.

---

## Features

- Extracts and splits PDF text into chunks.
- Converts text into embeddings using **OllamaEmbeddings**.
- Stores embeddings in **AstraDB** for fast similarity search.
- Provides an interactive Q&A loop for querying PDFs.
- Compatible with:
  - Python 3.12
  - LangChain 1.2.0
  - Ollama LLM
  - AstraDB

---

## Installation

1. Clone the repository:

```bash
git clone https://github.com/your-username/pdf-query-rag.git
cd pdf-query-rag
````

2. Create and activate a virtual environment:

```bash
python -m venv venv
# Windows
venv\Scripts\activate
# Linux/macOS
source venv/bin/activate
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

---

## Environment Variables

Create a `.env` file in the project root:

```env
ASTRA_DB_API_ENDPOINT=<your_astra_db_api_endpoint>
ASTRA_DB_APPLICATION_TOKEN=<your_astra_db_token>
ASTRA_DB_NAMESPACE=<your_astra_db_keyspace>
```

> **Note:** Namespace corresponds to your AstraDB keyspace.

---

## Usage

### 1. Load PDF content into the vector store

```python
from astra_vector_store_setup import astra_vector_store, texts

astra_vector_store.add_texts(texts[:50])
print(f"Inserted {len(texts[:50])} chunks.")
```

### 2. Interactive Q&A

```python
from interactive_query import astra_vector_store, llm

first_question = True
while True:
    query_text = input("Enter your question (or type 'quit' to exit): ").strip() \
                 if first_question else input("What's your next question? ").strip()

    if query_text.lower() == "quit":
        break
    if not query_text:
        continue

    first_question = False

    # Retrieve top 3 relevant chunks
    results = astra_vector_store.similarity_search(query_text, k=3)
    context = " ".join([doc.page_content for doc in results])

    # Generate answer using Ollama
    prompt = f"Answer the question based on the following context:\n{context}\n\nQuestion: {query_text}"
    output = llm.generate([prompt])
    answer = output.generations[0][0].text

    print("\nANSWER:", answer)
    print("\nTOP RELEVANT CHUNKS:")
    for i, doc in enumerate(results, 1):
        print(f"\t- {doc.page_content[:100].replace(chr(10), ' ')}...")
```

---

## Notes

* Works offline with Ollama LLM.
* Ideal for PDF-based RAG tasks: research papers, manuals, reports.
* Fully compatible with **Python 3.12** and the specified package versions.

---

## License

MIT License


