# LangChain Chatbot

A conversational AI chatbot built with [LangChain](https://www.langchain.com/) and [Ollama](https://ollama.com/), running large language models locally on your machine. No API keys needed — fully private and offline.

## Features

- **Local LLM Inference** — Powered by Ollama, keeping all data on your machine
- **Chat History** — Maintains multi-turn conversation context with configurable turn limits
- **Prompt Templates** — Uses LangChain's `ChatPromptTemplate` with system prompts and message history
- **Context Management** — Auto-warns when approaching the context limit and prevents overflow
- **Environment Config** — Model, temperature, and max turns configurable via `.env`

## Prerequisites

- **Python** 3.11+
- **Ollama** — [Install Ollama](https://ollama.com/download)
- **uv** (recommended) — [Install uv](https://docs.astral.sh/uv/getting-started/installation/)

## Quick Start

### 1. Clone the repository

```bash
git clone -b main https://github.com/the-schoolofai/langchain-chatbot.git
cd langchain-chatbot
```

### 2. Install dependencies

```bash
uv sync
```

### 3. Pull an Ollama model

```bash
ollama pull qwen2.5-coder:3b
```

### 4. Configure environment

```bash
cp .env.example .env
```

Edit `.env` to customize:

```env
MODEL_NAME=qwen2.5-coder:3b
TEMPERATURE=0.7
MAX_TURNS=5
```

### 5. Run the chatbot

```bash
uv run python main.py
```

## Usage

```
Chatbot Ready! (type 'quit' to exit, 'clear' to reset memory)

You: What is Python?
AI: Python is a high-level, interpreted programming language...

You: clear
Memory cleared. Starting fresh!

You: quit
```

| Command | Description                        |
| ------- | ---------------------------------- |
| `quit`  | Exit the chatbot                   |
| `clear` | Reset conversation history         |

## Configuration

| Variable      | Default            | Description                          |
| ------------- | ------------------ | ------------------------------------ |
| `MODEL_NAME`  | `qwen2.5-coder:3b` | Ollama model to use ([browse models](https://ollama.com/library)) |
| `TEMPERATURE` | `0.7`              | Creativity of responses (0.0 – 1.0) |
| `MAX_TURNS`   | `5`                | Max conversation turns before reset  |

## Project Structure

```
langchain-chatbot/
├── main.py            # Chatbot logic and interactive loop
├── .env               # Local config (git-ignored)
├── .env.example       # Example config template
├── pyproject.toml     # Project metadata and dependencies
├── uv.lock            # Locked dependency versions
└── .gitignore
```

## Tech Stack

- [LangChain](https://www.langchain.com/) — LLM application framework
- [Ollama](https://ollama.com/) — Local LLM runtime
- [python-dotenv](https://pypi.org/project/python-dotenv/) — Environment variable management
- [uv](https://docs.astral.sh/uv/) — Fast Python package manager

## License

This project is open source and available under the [MIT License](LICENSE).
