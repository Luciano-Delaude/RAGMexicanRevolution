# 📚 Stori Gen‑AI Challenge – Mexican Revolution RAG Assistant

A lightweight **Retrieval‑Augmented Generation (RAG)** chatbot that answers questions about the Mexican Revolution using local, open‑source models – no external APIs required.

---

## 🚀 Key Features

| Module            | Tech                                     | Purpose                                |
| ----------------- | ---------------------------------------- | -------------------------------------- |
| **Vector Store**  | FAISS + `mpnet-base-dot-v1`              | Fast semantic retrieval of PDF chunks  |
| **Generator**     | `google/flan‑t5‑base` + **LoRA** adapter | Low‑cost local text generation         |
| **Semantic Gate** | Global‑vector cosine ≥ 0.40              | Blocks off‑topic questions             |
| **Summarizer**    | `facebook/bart-large-cnn`                | Compresses long answers / chat history |
| **API**           | FastAPI (`/ask`, `/summarize`)           | Simple REST interface                  |
| **GUI**           | Streamlit one‑pager                      | Chat + “Summarize” button              |
| **Container**     | Docker (Python 3.10‑slim)                | One‑shot build & run                   |

---

## 🗂️ Project Layout

```
stori-genai-challenge/
├── app/                  # FastAPI + RAG logic
├── scripts/
│   └── build_index.py    # Build FAISS index from PDF
├── data/                 # mexican_revolution.pdf
├── embeddings/           # faiss_index/ + global_embedding.pkl
├── requirements.txt
├── Dockerfile
└── README.md (this file)
```

---

## ⚙️ Local Setup (dev‑mode)

```bash
# 1 Clone repo
$ git clone <repo-url> && cd stori-genai-challenge

# 2 Create venv & install deps
$ python -m venv venv
$ source venv/bin/activate
$ pip install -r requirements.txt

# 3 Build FAISS index (once)
$ python scripts/build_index.py

# 4 Run API
$ uvicorn app.main:app --reload  # http://localhost:8000/

# 5 Run GUI (optional)
$ streamlit run app/gui.py       # http://localhost:8501
```

---

## 🐳 Docker Usage (recommended)

The Dockerfile already calls `scripts/build_index.py` during the image build.

```bash
# Build image
$ docker build -t stori-rag .

# Run container
$ docker run -p 8000:8000 stori-rag
# → Swagger UI at http://localhost:8000/docs
```

> **Tip** : Mount a host volume if you want the index to persist and skip the build step at container start‑time.

---

## 🧪 Endpoints

| Method | Path        | Body / Query                                             | Description                                 |
| ------ | ------------| -------------------------------------------------------- | --------------------------------------------|
| `POST` | `/ask`      | `{ "question": "¿Cuáles fueron las causas políticas?" }` | Returns answer or rejection if off‑topic    |
| `GET`  | `/summarize`| —                                                        | Summarizes conversation so far              |

---

## ⚖️ Design Notes (trade‑offs)

* **Local models** → free, offline, but slower than remote GPT‑3.5.
* **LoRA fine‑tune (1 000 synthetic rows)** → quick accuracy boost without full retrain.
* **Single container** → minimal ops; FAISS index baked at build time.
* **CPU‑only** → runs everywhere; heavy (>7 B) models not feasible.

---

## 🌱 Future Work

* Hybrid BM25 + dense retrieval
* Streamed token output (SSE)
* Automated RAG eval (LangChain QAGold)
* Persist embeddings on volume / DB to speed container startups

---

Made with ❤️ for Stori’s Generative AI Challenge
