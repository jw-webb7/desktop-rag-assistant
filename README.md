# Enterprise Desktop RAG Assistant

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![LangChain](https://img.shields.io/badge/LangChain-Enabled-green.svg)
![Gemini](https://img.shields.io/badge/Google_Gemini-2.5_Pro-orange.svg)
![Pinecone](https://img.shields.io/badge/Pinecone-Vector_DB-lightgrey.svg)

This app is a standalone desktop application designed to act as an internal corporate brain. It allows users to securely upload proprietary documents, process them into a cloud vector database, and query them in real-time using a conversational AI interface. 

By utilizing a strict Retrieval-Augmented Generation (RAG) pipeline, this model eliminates LLM hallucinations by forcing the AI to cite directly from the uploaded company data.

---

## ✨ Key Features

* **Desktop-Native UI:** Built with `CustomTkinter` for a modern, responsive, and lightweight dark-mode interface that runs directly on Windows without a web browser.
* **Intelligent Document Parsing:** Utilizes `PyMuPDF` to chunk and tokenize complex corporate PDFs, financial reports, and handbooks before vectorization.
* **Real-Time Vectorization:** Seamlessly connects to **Pinecone** to store document embeddings, allowing the AI to search millions of tokens in milliseconds.
* **Contextual AI Memory:** Engineered with a custom LangChain agent that maintains short-term conversational history, allowing users to ask natural follow-up questions.
* **Enterprise Security (BYOK):** Designed with a dynamic Global Environment Injector. The application relies on localized `credentials` folders, ensuring proprietary API keys and Google OAuth tokens never touch public version control.

---

## 🧠 System Architecture

The application operates on a dual-highway architecture to separate document ingestion from AI querying:

1. **The Ingestion Pipeline:** `PDF Upload` ➔ `PyMuPDF Shredder` ➔ `Google Gemini Embeddings (768d)` ➔ `Pinecone Index`
2. **The Query Pipeline:** `User Question` ➔ `Context Retrieval` ➔ `LangChain Prompt Injection` ➔ `Gemini 2.5 Pro` ➔ `UI Render`

---

## ⚙️ Local Setup & Installation

*Note: For security reasons, the compiled binaries and API keys are strictly excluded from this repository via `.gitignore`. To run the raw Python architecture locally, you must provide your own API and Cloud credentials.*

### Prerequisites
1. A free [Pinecone](https://www.pinecone.io/) account (Create an index with exactly **768 dimensions**).
2. A Google Gemini API Key.
3. A Google Cloud Console project with the **Google Drive API** enabled.

### 1. Clone & Install

```bash
git clone [https://github.com/jw-webb7/desktop-rag-assistant.git](https://github.com/jw-webb7/desktop-rag-assistant.git)
cd desktop-rag-assistant
pip install -r requirements.txt
```

### 2. Configure API Keys
Create a `credentials` folder in the root directory. Inside, create a text file named `api_key.txt` and provide your keys:

```text
GEMINI_API_KEY=your_gemini_api_key_here
PINECONE_API_KEY=your_pinecone_api_key_here
PINECONE_INDEX_NAME=your_index_name_here
```

### 3. Configure Google OAuth (Upload Feature)
To enable the document upload pipeline, you must authorize the application to stage files via Google Drive:
1. Go to your Google Cloud Console.
2. Under APIs & Services > Credentials, create a new **OAuth 2.0 Client ID** (Application type: *Desktop App*).
3. Download the JSON file, rename it to `client_secret.json`.
4. Place `client_secret.json` inside your `credentials/` folder alongside `api_key.txt`.

### 4. Pathing Configuration (Crucial Step)
*Due to a specific pathing requirement between local Python execution and the internal PyInstaller compilation logic, the credentials folder must be mirrored.*

Copy your entire `credentials/` folder and paste a duplicate of it directly inside the `_internal/` directory (create the `_internal` folder in the root directory if it does not already exist). Your folder structure should look like this:

```text
desktop-rag-assistant/
├── _internal/
│   └── credentials/ 
│       ├── api_key.txt
│       └── client_secret.json
├── credentials/
│   ├── api_key.txt
│   └── client_secret.json
├── desktop-rag-assistant.exe
└── ...
```

### 5. Launch

```bash
python main.py
```

---

## 🛠️ Tech Stack
* **Frontend:** Python, CustomTkinter
* **AI/LLM:** LangChain, Google Gemini 2.5 Pro (`models/embedding-001`)
* **Database:** Pinecone Vector DB
* **Document Processing:** PyMuPDF (fitz)
* **Cloud Auth:** Google Drive API (OAuth 2.0)
