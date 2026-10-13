# ≡ƒæ╜ RAG PDFBot - V3 (FastAPI + Streamlit)

This is the **production-ready refactor** of [rag-bot-chroma](https://github.com/Zlash65/rag-bot-chroma), introducing a real separation between frontend (UI) and backend (logic) using **Streamlit** and **FastAPI** respectively. This modular architecture helps in scaling, extending, and deploying the bot in real-world environments.

---

<details>
  <summary> ≡ƒöù Helpful Links </summary>

- ≡ƒºæΓÇì≡ƒÆ╗ [Version 1 - Basic RAG PDFBot (FAISS)](https://github.com/Zlash65/rag-bot-basic)
- Γ£ì∩╕Å [V1 Blog Walkthrough](https://dev.to/zlash65/building-a-rag-powered-pdf-chatbot-with-langchain-streamlit-and-faiss-9i9)

- ≡ƒºæΓÇì≡ƒÆ╗ [Version 2 - Modular Streamlit + Chroma](https://github.com/Zlash65/rag-bot-chroma)
- Γ£ì∩╕Å [V2 Blog Walkthrough](https://dev.to/zlash65/refactoring-rag-pdfbot-modular-design-with-langchain-streamlit-and-chromadb-41fn)

- ≡ƒºæΓÇì≡ƒÆ╗ [Version 3 - Streamlit + FastAPI](https://github.com/Zlash65/rag-bot-fastapi)
- Γ£ì∩╕Å [V3 Blog Walkthrough](https://dev.to/zlash65/rag-pdfbot-v3-from-prototype-to-production-ready-ish-58h7)

</details>

---

## ≡ƒöä What Changed from `rag-bot-chroma`

| Feature | Version 2 | Version 3 |
|--------|-------------|--------------|
| Codebase | One Streamlit app | Split into `client/` + `server/` |
| PDF Upload | In Streamlit | Async FastAPI API |
| Chat | In Streamlit | Calls `/chat` API |
| Vectorstore | In UI | Controlled by backend |
| Model Options | Static | Dynamically fetched |
| Inspector | In sidebar | Main panel toggle |
| Splitting | `RecursiveTextSplitter` | `TokenTextSplitter` |
| UX | Crude | Responsive, clear, downloadable |
| Extendability | Hard | Easy to plug new LLMs, tools |

---

## ≡ƒº¬ How It Looks

### Demo
![demo-gif](/assets/rag-bot-fastapi.gif)

---

## ≡ƒÅù∩╕Å Architecture

![architecture](/assets/rag-bot-fastapi-architecture.png)

---

## ≡ƒÜÇ Features

- ≡ƒôü Upload multiple PDFs and chat with them
- ≡ƒöî Choose from Groq or Gemini as LLM providers
- ≡ƒöÄ Query inspector for vectorstore transparency
- ≡ƒºá RAG with LangChain + ChromaDB
- ≡ƒôª Streamlit frontend, FastAPI backend
- ≡ƒº¬ Token-based chunking for LLM precision
- ≡ƒÆ¼ Downloadable chat history
- ≡ƒº░ Tools for reset, undo, clear
- ≡ƒîÉ Fully API-driven interaction

---

<details>
  <summary>≡ƒ¢á∩╕Å Tech Stack</summary>

- **Frontend**: Streamlit
- **Backend**: FastAPI
- **LLMs**: Groq & Gemini via LangChain
- **Vector DB**: ChromaDB
- **Embeddings**: HuggingFace & Google GenAI
- **Chunking**: TokenTextSplitter (was RecursiveCharacterTextSplitter)
- **Parsing**: PyPDF
- **Orchestration**: LangChain Retrieval Chain

</details>

---

## ≡ƒôª Installation

```bash
git clone https://github.com/Zlash65/rag-bot-fastapi.git
cd rag-bot-fastapi
```

Setup Virtual Environment:
```bash
python3 -m venv venv
source venv/bin/activate
```

Install frontend:

```bash
cd client
pip3 install -r requirements.txt
```

Install backend:

```bash
cd ../server
pip3 install -r requirements.txt
```

---

## ≡ƒöÉ API Keys Required

- **Groq API key** from [console.groq.com](https://console.groq.com/)
- **Google Gemini API key** from [ai.google.dev](https://ai.google.dev/)

Create a `.env` file:

```env
GROQ_API_KEY=your-groq-key
GOOGLE_API_KEY=your-google-key
```

---

## Γû╢∩╕Å Run the Bot

Start FastAPI backend:

```bash
# Terminal 1
cd server
uvicorn main:app --reload
```

Start Streamlit frontend:

```bash
# Terminal 2
cd client
streamlit run app.py
```

---

<details>
  <summary>≡ƒôü Project Structure</summary>

```bash
rag-bot-v3/
Γö£ΓöÇΓöÇ client/                         # Streamlit Frontend
Γöé   Γö£ΓöÇΓöÇ app.py                      # Main Streamlit entrypoint
Γöé   Γö£ΓöÇΓöÇ components/                 # UI modules
Γöé   Γöé   Γö£ΓöÇΓöÇ chat.py
Γöé   Γöé   Γö£ΓöÇΓöÇ inspector.py
Γöé   Γöé   ΓööΓöÇΓöÇ sidebar.py
Γöé   Γö£ΓöÇΓöÇ state/
Γöé   Γöé   ΓööΓöÇΓöÇ session.py              # Session state manager
Γöé   Γö£ΓöÇΓöÇ utils/
Γöé   Γöé   Γö£ΓöÇΓöÇ api.py                  # Talks to backend
Γöé   Γöé   Γö£ΓöÇΓöÇ config.py               # API_URL and config values
Γöé   Γöé   ΓööΓöÇΓöÇ helpers.py              # API wrappers for frontend
Γöé   Γö£ΓöÇΓöÇ requirements.txt
Γöé   ΓööΓöÇΓöÇ README.md

Γö£ΓöÇΓöÇ server/                         # FastAPI Backend
Γöé   Γö£ΓöÇΓöÇ api/
Γöé   Γöé   Γö£ΓöÇΓöÇ routes.py               # API endpoints
Γöé   Γöé   ΓööΓöÇΓöÇ schemas.py              # Pydantic schemas for I/O
Γöé   Γö£ΓöÇΓöÇ core/
Γöé   Γöé   Γö£ΓöÇΓöÇ document_processor.py   # Handles PDF validation and chunking
Γöé   Γöé   Γö£ΓöÇΓöÇ llm_chain_factory.py    # Builds LLM chains and prompts
Γöé   Γöé   ΓööΓöÇΓöÇ vector_database.py      # Embeddings + ChromaDB ops
Γöé   Γö£ΓöÇΓöÇ config/
Γöé   Γöé   ΓööΓöÇΓöÇ settings.py             # App config, model provider setup
Γöé   Γö£ΓöÇΓöÇ utils/
Γöé   Γöé   ΓööΓöÇΓöÇ logger.py               # Logging setup
Γöé   Γö£ΓöÇΓöÇ main.py                     # FastAPI app entrypoint
Γöé   Γö£ΓöÇΓöÇ requirements.txt
Γöé   ΓööΓöÇΓöÇ README.md

Γö£ΓöÇΓöÇ README.md                       # Root README (overview + instructions)
Γö£ΓöÇΓöÇ .gitignore
```

</details>

---

<details>
  <summary> ≡ƒæô Different Views </summary>

| View | Description |
|------|-------------|
| ≡ƒÆ¼ Chat | Renders chat bubbles, input box, and chat history download |
| ≡ƒö¼ Inspector | Renders inspector to test vectorstore responses |

![views](/assets/rag-bot-fastapi-clean-ui-ux.gif)

</details>

---

<details>
  <summary>≡ƒº╝ Tools Panel</summary>

| Button | Function |
|----------|--------|
| ≡ƒöä Reset | Clears session state and reruns app |
| ≡ƒº╣ Clear Chat | Clears chat + PDF submission |
| Γå⌐∩╕Å Undo | Removes last question/response |

</details>

---

<details>
  <summary>≡ƒôª Download Chat History</summary>

Chat history is saved in the session state and can be exported as a CSV with the following columns:

| Question | Answer | Model Provider | Model Name | PDF File | Timestamp |
|----------|--------|----------------|------------|---------------------|-----------|
| What is this PDF about? | This PDF explains... | Groq | llama3-70b-8192 | file1.pdf, file2.pdf | 2025-07-03 21:00:00 |

</details>

---

<details>
  <summary>≡ƒÖÅ Acknowledgements</summary>

- [LangChain](https://www.langchain.com/)
- [Streamlit](https://streamlit.io/)
- [Groq](https://console.groq.com/)
- [Google Gemini](https://ai.google.dev/)
- [Chroma](https://docs.trychroma.com/)

</details>

---

## ≡ƒºá New to this Project?

Start from the basics:
≡ƒæë [Version 1 - rag-bot-basic](https://github.com/Zlash65/rag-bot-basic)

Understand modular design:
≡ƒæë [Version 2 - rag-bot-chroma](https://github.com/Zlash65/rag-bot-chroma)

Then return here for real-world patterns.

---

Happy building! ≡ƒ¢á∩╕Å