# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A conversational AI chatbot using LangChain and Ollama for local LLM inference. Single-file application (`main.py`) with an interactive terminal loop. No API keys required — runs fully offline.

## Commands

```bash
uv sync              # Install dependencies
uv run python main.py # Run the chatbot
```

Ollama must be running locally with the configured model pulled:
```bash
ollama pull qwen2.5-coder:3b
```

## Architecture

All logic lives in `main.py`:

1. **Config** — Loaded from `.env` via `python-dotenv` with defaults (`MODEL_NAME`, `TEMPERATURE`, `MAX_TURNS`)
2. **Chain** — LangChain LCEL pipeline: `ChatPromptTemplate | ChatOllama | StrOutputParser`
3. **Chat history** — In-memory list of `HumanMessage`/`AIMessage` objects, passed via `MessagesPlaceholder`
4. **Turn management** — Caps conversation at `MAX_TURNS` exchanges, warns at 2 remaining

## Code Flow

```
┌─────────────────────────────────────────────────────────────────--┐
│                        STARTUP                                    │
│                                                                   │
│  .env ──► load_dotenv() ──► MODEL_NAME, TEMPERATURE, MAX_TURNS    │
│                                      │                            │
│                                      ▼                            │
│                              ChatOllama(model, temp)              │
│                                      │                            │
│                                      ▼                            │
│              ChatPromptTemplate ──► LCEL Chain ──► StrOutputParser│
│         (system + history + human)                                │
└─────────────────────────────────────────────────────────────────--┘

┌─────────────────────────────────────────────────────────────────┐
│                     CONVERSATION LOOP                           │
│                                                                 │
│  User Input                                                     │
│      │                                                          │
│      ▼                                                          │
│  ┌─ "quit" ──► Exit                                             │
│  ├─ "clear" ──► Reset chat_history[] ──► Loop back              │
│  └─ question ──► chat()                                         │
│                    │                                            │
│                    ▼                                            │
│          Check turns >= MAX_TURNS?                              │
│           ├─ Yes ──► "Context full" message                     │
│           └─ No                                                 │
│               │                                                 │
│               ▼                                                 │
│     ┌─────────────────────────────────────┐                     │
│     │         LCEL Chain Invoke           │                     │
│     │                                     │                     │
│     │  question + chat_history            │                     │
│     │         │                           │                     │
│     │         ▼                           │                     │
│     │  ChatPromptTemplate                 │                     │
│     │  (formats system prompt,            │                     │
│     │   injects history, adds question)   │                     │
│     │         │                           │                     │
│     │         ▼                           │                     │
│     │  ChatOllama                         │                     │
│     │  (sends to local Ollama server      │                     │
│     │   at localhost:11434)               │                     │
│     │         │                           │                     │
│     │         ▼                           │                     │
│     │  StrOutputParser                    │                     │
│     │  (extracts string from AIMessage)   │                     │
│     └─────────────────────────────────────┘                     │
│               │                                                 │
│               ▼                                                 │
│     Append HumanMessage + AIMessage to chat_history[]           │
│               │                                                 │
│               ▼                                                 │
│     Warn if ≤ 2 turns remaining                                 │
│               │                                                 │
│               ▼                                                 │
│     Print response ──► Loop back                                │
└─────────────────────────────────────────────────────────────────┘
```

## Key Dependencies

- `langchain-ollama` — LangChain integration for Ollama models
- `ollama` — Ollama Python client
- `python-dotenv` — Environment variable loading

## Configuration

Environment variables in `.env` (see `.env.example`):
- `MODEL_NAME` — Ollama model identifier (default: `qwen2.5-coder:3b`)
- `TEMPERATURE` — Float 0.0–1.0 (default: `0.7`)
- `MAX_TURNS` — Integer conversation turn limit (default: `5`)
