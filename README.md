<div align="center">

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=200&section=header&text=Adaptive%20RAG&fontSize=60&fontColor=ffffff&animation=fadeIn&fontAlignY=35&desc=A%20RAG%20pipeline%20that%20decides%20how%20to%20answer%20before%20it%20answers&descAlignY=55&descSize=18" width="100%"/>

<img src="https://readme-typing-svg.demolab.com?font=Fira+Code&size=22&pause=1000&color=8A63D2&center=true&vCenter=true&width=650&lines=Routes+every+query+before+retrieving+anything;Indexed+docs+%E2%86%92+general+knowledge+%E2%86%92+live+web+search;Self-grades%2C+rewrites%2C+and+retries+bad+retrievals;Built+on+LangGraph+%2B+FastAPI+%2B+Streamlit" alt="Typing SVG" />

<br/>

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![LangGraph](https://img.shields.io/badge/LangGraph-Orchestration-1C3C3C?style=for-the-badge&logo=langchain&logoColor=white)](https://www.langchain.com/langgraph)
[![FastAPI](https://img.shields.io/badge/FastAPI-Backend-009688?style=for-the-badge&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![Streamlit](https://img.shields.io/badge/Streamlit-Frontend-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white)](https://streamlit.io/)
[![MongoDB](https://img.shields.io/badge/MongoDB-Chat%20History-47A248?style=for-the-badge&logo=mongodb&logoColor=white)](https://www.mongodb.com/)

</div>

<img src="https://capsule-render.vercel.app/api?type=rect&color=gradient&customColorList=6,11,20&height=3&section=header" width="100%"/>

## The idea

Most RAG demos run every question through the same vector search whether it needs it or not. Adaptive RAG skips that. A **query classifier** looks at each question first and decides which of three lanes it belongs in: your uploaded documents, the LLM's own general knowledge, or a live web search. Only then does it go retrieve anything.

If the indexed-document path comes back with something irrelevant, a **grading** step catches it and sends the query back through a **rewrite** node instead of just returning a bad answer.

<div align="center">
<img src="adaptive_RAG.png" width="480" alt="Adaptive RAG LangGraph flow"/>
</div>

<img src="https://capsule-render.vercel.app/api?type=rect&color=gradient&customColorList=6,11,20&height=3&section=header" width="100%"/>

## Stack

<div align="center">

| Layer | Tech |
|---|---|
| Orchestration | LangGraph state machine |
| LLM | OpenAI `gpt-4o` |
| Retrieval | FAISS (Qdrant integration already wired in, just switched off) |
| Web fallback | Tavily |
| Chat memory | MongoDB (async, via Motor) |
| API | FastAPI |
| UI | Streamlit |

</div>

<img src="https://capsule-render.vercel.app/api?type=rect&color=gradient&customColorList=6,11,20&height=3&section=header" width="100%"/>

## Run it

```bash
git clone https://github.com/khushaan18/adaptive-rag.git
cd adaptive-rag
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
```

Drop a `.env` in the root:

```
OPENAI_API_KEY=your_openai_key
TAVILY_API_KEY=your_tavily_key
```

MongoDB needs to be running locally at `mongodb://localhost:27017` for chat history.

```bash
uvicorn src.main:app --reload          # backend
streamlit run streamlit_app/home.py    # UI, separate terminal
```

Upload a PDF or TXT through the sidebar with a short description of what's in it. That description gets rewritten into a proper tool instruction so the graph knows exactly when to reach for it versus falling back to general knowledge or a web search.

<details>
<summary><b>Or query the API directly</b></summary>

```bash
curl -X POST http://127.0.0.1:8000/rag/query \
  -H "Content-Type: application/json" \
  -d '{"query": "What does the uploaded document say about X?", "session_id": "test-session"}'
```

</details>

<img src="https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=6,11,20&height=100&section=footer" width="100%"/>
