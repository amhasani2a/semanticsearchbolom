# semanticsearchbolom

# RAG-Based Document Search Engine

This project implements a semantic search engine utilizing artificial intelligence and vector databases. It enables users to upload various document types and perform context-aware semantic searches, moving beyond standard lexical keyword matching. Designed primarily for execution within Google Colab, the system includes an integrated graphical user interface (GUI) for file ingestion, query processing, and database management.

## Key Features

* **Hybrid Search:** Combines exact full-text search (lexical) with AI-driven semantic search (vector-based) to maximize retrieval accuracy.
* **Multi-Format Support:** Ingests and processes PDF, DOCX, TXT, and EPUB files.
* **Multilingual Capabilities:** Utilizes the `multilingual-e5-base` model, providing robust support for multiple languages including English and Persian.
* **Integrated OCR Fallback:** Employs Tesseract OCR to automatically extract text from scanned PDFs and image-based documents.
* **Reranking Pipeline:** Integrates a `CrossEncoder` model to re-evaluate, score, and sort initial retrieval candidates for enhanced precision.
* **Interactive GUI:** Built with `ipywidgets` to provide dedicated interface tabs for searching, uploading, and managing files.
* **Contextual Highlighting:** Automatically applies HTML-based highlighting to search query terms within the returned text snippets.
* **Automated Persistence:** Interfaces with Google Drive to automatically back up the PostgreSQL database state and prevent data loss across sessions.

## Technology Stack

| Component | Technology |
| --- | --- |
| **Database** | PostgreSQL + `pgvector` extension |
| **Embedding Model** | `intfloat/multilingual-e5-base` (Sentence-Transformers) |
| **Reranker Model** | `cross-encoder/ms-marco-MiniLM-L-6-v2` |
| **User Interface** | `ipywidgets` |
| **Document Parsing** | `PyMuPDF` (fitz), `python-docx`, `EbookLib`, `pdf2image` |
| **Optical Character Recognition** | `Tesseract OCR` |

## Deployment and Execution

Due to system-level dependencies (e.g., PostgreSQL, Tesseract), this application is optimized for the Google Colab environment.

### 1. Colab Initialization

1. Create a new notebook in Google Colab.
2. Paste the entire source code into a single cell.
3. Execute the cell.

### 2. Storage Authorization

During execution, the script will request permission to mount your Google Drive. Granting this access is required for the system to read and write the database backup file (`search_engine_dump.sql`).

### 3. Interface Usage

Once the environment setup and model downloads are complete, the GUI will render below the cell. It consists of three primary tabs:

* **Upload:** Select files via the `Select Files` widget and initialize ingestion using `Process Uploads`. The system will extract text, generate chunks, compute embeddings, and store the data.
* **Search:** Input query terms into the `Query` field. Adjust the `Semantic Wt` slider to balance vector similarity versus lexical matching. Click `Search` to retrieve reranked and highlighted results.
* **Manage:** View the list of ingested documents via the dropdown menu and remove specific entries from the database using the `Delete File` button.

## System Architecture

1. **Document Processing:** Ingested files are parsed based on their MIME type/extension. If standard PDF text extraction yields insufficient data, the OCR pipeline is triggered.
2. **Chunking:** Extracted text is segmented into discrete chunks using a sliding window approach (default: 300 words with a 50-word overlap) to preserve contextual boundaries.
3. **Embedding Generation:** Text chunks are passed through the Sentence-Transformer model to generate normalized 768-dimensional vector embeddings.
4. **Storage:** The raw text, file metadata, and corresponding vectors are inserted into PostgreSQL relational tables.
5. **Retrieval:** The system executes a hybrid SQL query leveraging HNSW indexing for cosine similarity (vectors) and GIN indexing for `tsvector` matching (full-text).
6. **Reranking:** Top-K candidate chunks are paired with the search query and evaluated by the CrossEncoder model to determine the final output order.

## Implementation Notes

* **Local Execution:** If deploying outside of Google Colab, the `DB_DUMP_PATH` variable must be modified to point to a valid local directory path.
* **Hardware Acceleration:** To significantly reduce model inference and document processing times in Colab, navigate to `Runtime > Change runtime type` and select a hardware accelerator (e.g., T4 GPU) prior to execution.

* This project was built with the help of artificial intelligence.
