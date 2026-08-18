# Multi-Utility Agentic Chatbot System

A production-oriented agentic chatbot built with **LangGraph** and **Streamlit**, supporting dynamic tool usage, real-time external operations, persistent conversation state, and PDF-based retrieval.

## Features

* Agentic chatbot workflow using **LangGraph**
* Interactive **Streamlit** interface
* Persistent conversation state using **async SQLite**
* **MCP** integration for dynamic tool discovery
* Support for MCP servers over **stdio and HTTP**
* Real-time **DuckDuckGo web search**
* Real-time **stock price retrieval**
* Multi-step tool-based reasoning
* PDF **RAG pipeline** using FAISS
* **OpenAI embeddings** for document retrieval
* Thread-specific document chunking and retrieval

## Tech Stack

**Core**

* Python
* LangGraph
* LangChain
* Streamlit

**State & Storage**

* SQLite
* Async SQLite

**Tools & Agents**

* MCP
* MultiServerMCPClient
* DuckDuckGo Search
* Stock APIs

**RAG**

* FAISS
* OpenAI Embeddings
* PDF document processing

## Architecture

```text
                    ┌─────────────────┐
                    │    Streamlit    │
                    │       UI        │
                    └────────┬────────┘
                             │
                             ▼
                    ┌─────────────────┐
                    │    LangGraph    │
                    │     Agent       │
                    └────────┬────────┘
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
        ┌──────────┐   ┌──────────┐   ┌──────────┐
        │   MCP    │   │ Web/Stock│   │ PDF RAG  │
        │  Tools   │   │   Tools  │   │  FAISS   │
        └──────────┘   └──────────┘   └──────────┘
              │              │              │
              └──────────────┼──────────────┘
                             ▼
                    ┌─────────────────┐
                    │  Async SQLite   │
                    │  State Storage  │
                    └─────────────────┘
```

## MCP Tool Integration

The system uses `MultiServerMCPClient` to connect to multiple MCP servers and dynamically discover available tools.

```python
from langchain_mcp_adapters.client import MultiServerMCPClient

client = MultiServerMCPClient({
    "search": {
        "transport": "stdio",
        "command": "python",
        "args": ["search_server.py"]
    },
    "remote_tools": {
        "transport": "streamable_http",
        "url": "http://localhost:8000/mcp"
    }
})

tools = await client.get_tools()
```

This allows tools to be added without hard-coding every tool directly into the chatbot.

## RAG Pipeline

PDF documents are processed into chunks and converted into vector embeddings.

```text
PDF
 ↓
Text Extraction
 ↓
Document Chunking
 ↓
OpenAI Embeddings
 ↓
FAISS Vector Store
 ↓
Similarity Search
 ↓
Relevant Context
 ↓
LLM Response
```

Thread-specific document storage allows retrieved context to remain associated with the relevant conversation.

## Example Queries

```text
Search the web for the latest NVIDIA news

What is the current price of AAPL?

Summarize the uploaded PDF

What does the document say about catalyst performance?

Compare the information in the PDF with current web results
```

## Installation

Clone the repository:

```bash
git clone <repository-url>
cd multi-utility-agent
```

Install dependencies:

```bash
pip install -r requirements.txt
```

Create a `.env` file:

```env
OPENAI_API_KEY=your_openai_api_key
```

## Run the Application

```bash
streamlit run app.py
```

## Project Structure

```text
multi-utility-agent/
│
├── app.py
├── agent/
│   ├── graph.py
│   ├── state.py
│   └── tools.py
│
├── mcp_servers/
│   ├── search_server.py
│   └── stock_server.py
│
├── rag/
│   ├── pdf_loader.py
│   ├── embeddings.py
│   └── vector_store.py
│
├── database/
│   └── state.py
│
├── requirements.txt
├── .env
└── README.md
```

## Core Workflow

```text
User Query
    ↓
LangGraph Agent
    ↓
Determine Required Tool
    ↓
MCP / Search / Stock / RAG
    ↓
Retrieve Result
    ↓
Reason Over Results
    ↓
Generate Response
    ↓
Store Conversation State
```

## Key Highlights

* Built an **agentic workflow** capable of selecting and orchestrating tools dynamically.
* Integrated **MCP servers** for extensible tool discovery across local and remote services.
* Added **real-time web and financial data retrieval**.
* Implemented **PDF RAG with FAISS and OpenAI embeddings**.
* Added persistent conversation state using **async SQLite**.
* Built an interactive **Streamlit** interface for multi-utility chatbot interactions.
