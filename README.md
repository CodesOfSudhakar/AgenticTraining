# Cease & Desist Document Processing Agent

Capstone project built as part of the Agentic AI Training program (Day 5).

---

## Problem Statement

Enterprises receive Cease and Desist documents from customers requesting
to stop all direct communication. Currently, human agents manually read
each scanned PDF to determine if the request is legitimate — a process
that is slow, inconsistent and difficult to scale.

---

## Solution

An intelligent multi-agent system that automatically reads, classifies
and processes these documents with minimal human intervention.

---

## How It Works
```
PDF Document
    │
    ▼
Load and extract text
(PyPDFLoader for digital PDFs, Groq Vision OCR for scanned docs)
    │
    ▼
Classify document using Groq LLM
    │
    ├── Cease      → Save to SQLite database
    ├── Irrelevant → Write to archive flat file
    └── Uncertain  → Pause for human review
                         │
                         ├── Approved → Save to database
                         └── Rejected → Write to archive
    │
    ▼
Audit log updated for every decision
```

---

## Classification Categories

| Label | Meaning | Action |
|---|---|---|
| Cease | Valid Cease and Desist request | Saved to SQLite database |
| Irrelevant | Not a Cease and Desist document | Written to archive file |
| Uncertain | Cannot determine with confidence | Escalated to human reviewer |

---

## Tech Stack

| Component | Technology |
|---|---|
| Framework | LangChain and LangGraph |
| LLM | Groq (llama-3.1-8b-instant) |
| OCR Fallback | Groq Vision (llama-4-scout-17b) |
| Workflow | LangGraph State Machine |
| Database | SQLite |
| Monitoring | LangSmith |
| Audit Logging | Python logging library |
| Environment | Google Colab |

---

## Agent Architecture

The system is built as a LangGraph state machine with the following nodes:

- load_document_node — reads the PDF and extracts text
- classify_node — sends text to Groq LLM and returns classification
- database_node — saves valid Cease requests to SQLite
- archive_node — writes Irrelevant documents to a flat file
- hitl_node — pauses workflow and collects human decision
- audit_node — logs every decision with timestamp

---

## Key Concepts Applied

- LangGraph state machine for multi-node workflow orchestration
- Structured output using Pydantic schemas for reliable LLM responses
- Human-in-the-Loop using LangGraph interrupt and resume pattern
- Smart PDF loading with automatic OCR fallback for scanned documents
- Error handling with try/except blocks in all storage operations
- Audit trail using Python logging with file and screen handlers

---

## Project Structure
```
capstone-project/
│
├── U838925_Capstone_GenAi_LangChain.ipynb    ← main notebook
├── README.md                                  ← this file
│
├── guides/
│   ├── Capstone_Guide_LangChain_LangGraph.md
│   └── Capstone_Guide_Google_ADK.md
│
└── Sample Docs/
    ├── LoA1.pdf to LOA9.pdf                   ← Letters of Authorization
    ├── notice_1.pdf to notice_5.pdf           ← Legal Notices
    └── bw_doc_1.pdf to bw_doc_5.pdf           ← Business Documents
```

---

## Setup and Usage

1. Open the notebook in Google Colab
2. Add the following keys to Colab Secrets:
   - GROQ_API_KEY
   - LANGCHAIN_API_KEY
3. Upload your PDF documents to /content/CapstoneInputData
4. Run all cells from top to bottom
5. When prompted for human review, type cease or irrelevant

---

## Results

The system processed 29 documents across three categories with
high confidence on clearly identifiable documents and appropriate
escalation to human review for ambiguous cases.

| Category | Count | Outcome |
|---|---|---|
| Legal Cease and Desist | 10 | Saved to database |
| Letters of Authorization | 9 | Mostly saved to database |
| Business Documents | 5 | Archived |
| Legal Notices | 5 | Saved to database or human review |

---

## Notes

- Groq free tier has a daily token limit of 100,000
- If the limit is reached, switch to llama-3.1-8b-instant
  which has a separate pool of 500,000 tokens per day
- Colab resets the file system on runtime restart
  so PDFs need to be re-uploaded each session

---

## Author

-Sudhakar Sukumar
