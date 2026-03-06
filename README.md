#  Agentic Bug Hunter

An agentic AI pipeline that automatically detects and explains bugs in RDI API code snippets using a RAG-powered MCP server and a Large Language Model.

---

##  Project Structure

```
Group_A_16/
├── code/
│   └── solution.py        ← Main pipeline (run this)
├── server/
│   ├── mcp_server.py      ← MCP search server (keep running)
│   ├── embedding_model/   ← Local embedding model
│   └── storage/           ← Indexed RDI API documentation
├── samples.csv            ← Input dataset (20 buggy code samples)
├── output.csv             ← Generated output (auto-created)
├── requirements.txt       ← Server dependencies
├── .env                   ← API key (never share this)
└── venv/                  ← Python virtual environment
```

---

##  How It Works

The pipeline runs 3 stages for each code sample:

```
samples.csv
    │
    ▼
Stage 0 — RAG RETRIEVAL
    MCP server searches RDI API docs for relevant context
    (2 searches per sample: context hint + code snippet)
    │
    ▼
Stage 1 — BUG DETECTION
    LLM + retrieved docs → finds exact buggy line numbers
    Output: "1" or "1, 3" for multiple bugs
    │
    ▼
Stage 2 — BUG DESCRIPTION
    LLM + retrieved docs + bug lines → explains each bug
    Output: "Line 1: wrong mode used, Line 3: execute() missing"
    │
    ▼
output.csv
```

---

##  Setup & Run

### Step 1 — Activate virtual environment
```bash
cd ~/Desktop/Group_A_16
source venv/bin/activate
```

### Step 2 — Install dependencies
```bash
pip install -r requirements.txt
pip install huggingface_hub python-dotenv httpx
```

### Step 3 — Add your API key
Create a `.env` file in the project root:
```
HF_API_KEY=hf_xxxxxxxxxxxxxxxxxxxxxxxx
```
Get your free key at: https://huggingface.co → Settings → Access Tokens

### Step 4 — Start the MCP server (Terminal 1)
```bash
cd ~/Desktop/Group_A_16/server
python mcp_server.py
```
Leave this running. You should see:
```
Uvicorn running on http://127.0.0.1:8003
```

### Step 5 — Run the pipeline (Terminal 2)
```bash
cd ~/Desktop/Group_A_16
source venv/bin/activate
python code/solution.py
```

---

## Output Format

The generated `output.csv` has exactly 3 columns:

| Column | Description | Example |
|--------|-------------|---------|
| ID | Unique code snippet ID | `16` |
| Bug Line | Line number(s) containing the bug | `1` or `1, 3` |
| Explanation | Short description of each bug | `TA::VECD used instead of TA::VTT` |

---

##  Tech Stack

| Component | Technology |
|-----------|------------|
| LLM | Hugging Face Inference API (Qwen2.5-72B) |
| RAG Retrieval | MCP Server + LlamaIndex + BGE Embeddings |
| MCP Transport | FastMCP SSE (Server-Sent Events) |
| HTTP Client | httpx (async) |
| Environment | python-dotenv |

---

##  Troubleshooting

| Error | Fix |
|-------|-----|
| `HF_API_KEY not set` | Check your `.env` file exists and has no spaces around `=` |
| `MCP search error` | Make sure `python mcp_server.py` is running in Terminal 1 |
| `model_not_supported` | Check `HF_MODEL` in `solution.py` is a valid chat model |
| `Connection refused 8003` | Restart the MCP server in Terminal 1 |
| `load_dotenv not found` | Run `pip install python-dotenv` |

---

##  Submission Structure

```
TeamName_Submission.zip
├── output.csv
├── code/
│   └── solution.py
├── requirements.txt
└── README.md
```
