# Agentic RAG with Vector Database

An AI chat assistant that answers questions from a private knowledge base using **Retrieval-Augmented Generation (RAG)**. Company information (stored as content entries with categories) is converted into vector embeddings and held in a **Supabase** vector database. When a user asks a question, the agent searches that database by meaning and answers based on the most relevant stored information.

Built on n8n Cloud with OpenAI (embeddings and chat) and Supabase (vector store).

---

## The Problem

A general AI model does not know anything about a specific company's products, prices, or policies, and if you ask it, it will often make things up. Businesses need an assistant that answers only from their own trusted information.

RAG solves this. Instead of relying on the model's general training, the assistant searches a private knowledge base for the relevant facts and answers from those. This keeps responses accurate, grounded in real company data, and easy to update (you change the data, not the model).

---

## How It Works

This project has two parts: an ingestion pipeline that loads the knowledge base, and a retrieval agent that answers questions from it.

### Part 1: Ingestion Pipeline

```
Trigger
   │
   ▼
Get rows from Google Sheets   (the company knowledge base)
   │
   ▼
Convert each record to embeddings   (OpenAI)
   │
   ▼
Store in Supabase vector database
```

Each record from the knowledge base sheet is turned into a vector embedding, which is a numeric representation of its meaning, and stored in Supabase. This is what lets the data later be searched by meaning rather than by exact keywords.

### Part 2: Retrieval Agent (the agentic part)

```
User question (chat)
   │
   ▼
AI Agent  (with conversation memory)
   │  decides to search the knowledge base
   ▼
Supabase Vector Store  (used as a tool)
   │  embeds the question, finds the closest stored records
   ▼
Answer, based on the retrieved information
```

The agent embeds the user's question, searches the Supabase vector store for the most similar stored records, and answers using what it finds. It is "agentic" because the agent itself decides when to search the knowledge base, rather than the search being a fixed step.

---

## Why It Is Built This Way

- **Embeddings on both sides.** The data is embedded when stored, and the question is embedded when asked, so the system can compare meaning to meaning rather than matching exact words. This is what makes the search understand intent.
- **Vector store used as a tool, not a fixed step.** The agent chooses to call the knowledge base when it needs to, which is the "agentic" RAG pattern. This lets it handle follow-up questions and conversation naturally.
- **Ingestion and retrieval are separate workflows.** Loading the data and answering questions are different jobs, so they are kept as separate, independently runnable workflows.

---

## Tech Used

- **n8n Cloud:** workflow orchestration and the agent framework
- **OpenAI:** text embeddings (for both data and questions) and chat responses
- **Supabase:** vector database storing the embedded knowledge base
- **RAG pattern:** retrieval-augmented generation with semantic (meaning-based) search

---

## Demo

The screenshots in this folder show:

- **Ingestion pipeline:** a successful run loading the knowledge base records, embedding them, and storing them in Supabase (the Data Loader shows the records processed).
- **Retrieval agent:** the architecture, with the Supabase vector store connected to the agent as a search tool.
- **Successful execution:** the retrieval agent answering a question end to end, using the knowledge base.

---

## Files

- `AgenticRag.json`: the retrieval agent workflow (answers questions from the knowledge base)
- `RAG2DataIngest.json`: the ingestion pipeline workflow (loads and embeds the data)
- Screenshots: workflow canvases and successful executions

*Note: API keys and credentials have been removed from the exported workflows. To run this yourself, you would connect your own OpenAI and Supabase credentials in n8n, and set up a Supabase table with vector support.*
