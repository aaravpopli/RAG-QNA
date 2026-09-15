# Conversational RAG with PDFs

A Streamlit app for asking questions about uploaded PDF documents. It uses retrieval-augmented generation (RAG) to retrieve relevant document passages and provide answers with a Groq-hosted language model. Chat history helps the app understand follow-up questions.

## Features

- Upload one or more PDFs.
- Ask questions about their content.
- Ask follow-up questions using conversation history.
- Keep separate chat histories by entering different session IDs within the current Streamlit session.

## Tech stack

- **Interface:** Streamlit
- **RAG pipeline:** LangChain
- **Language model:** Groq, configured as `openai/gpt-oss-120b`
- **Embeddings:** Hugging Face `all-MiniLM-L6-v2`
- **Vector store:** Chroma
- **PDF loading:** PyPDFLoader

## Run locally

Run these commands from the project folder.

### 1. Create and activate a virtual environment

```bash
python3 -m venv venv
source venv/bin/activate
```

On Windows, activate it with:

```powershell
venv\Scripts\Activate.ps1
```

### 2. Install dependencies

```bash
python -m pip install -r requirements.txt langchain-classic
```

`langchain-classic` is included explicitly because `app.py` imports it, but it is not currently listed in `requirements.txt`.

### 3. Configure your Hugging Face token

Create a `.env` file in the project folder:

```dotenv
HF_TOKEN=your_hugging_face_token
```

The current code expects `HF_TOKEN` to be set before initializing the embedding model. Keep your real token in `.env`; this file is excluded from Git.

### 4. Start the app

```bash
python -m streamlit run app.py
```

Open the local URL shown in the terminal, usually `http://localhost:8501`.

## How to use

1. Enter your Groq API key in the app's password field.
2. Keep the default session ID or enter a different one for a separate chat history.
3. Upload one or more text-based PDFs.
4. Enter a question and press Enter.
5. Ask follow-up questions using the same session ID.

The Groq API key is entered in the interface; the app does not currently read it from `.env`.

## How it works

1. Load text from the uploaded PDFs.
2. Split the text into chunks of up to 5,000 characters with a 500-character overlap.
3. Generate embeddings and store the chunks in Chroma.
4. Use chat history to reformulate follow-up questions for retrieval.
5. Retrieve relevant passages and ask the language model to answer using that context.
6. Save the question and answer in the current session's chat history.

## Project files

```text
.
├── app.py               # Streamlit interface and RAG pipeline
├── requirements.txt     # Python dependencies
├── README.md
└── .gitignore
```

## Current limitations

- Chat history is stored in Streamlit session state and is not saved permanently. Session IDs are not user accounts.
- PDF processing and indexing run again on Streamlit reruns; larger documents may be slow.
- Uploaded files are written to a shared `temp.pdf` file, so this implementation is intended for local learning rather than simultaneous users.
- Scanned PDFs need OCR before use; this app does not perform OCR.
- The first run may download the embedding model. Groq calls require internet access and are subject to your account's limits.
- Questions, retrieved passages, and conversation context are sent to Groq to generate responses.
