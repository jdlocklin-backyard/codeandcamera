---
title: "Building a Local AI Chatbot with Ollama"
description: "Run powerful AI models locally on your own hardware"
tags:
  - ai
  - llm
  - project
  - intermediate
status: new
---

# Building a Local AI Chatbot with Ollama

Run AI models on your own hardware—private, free, and surprisingly capable.

## Overview

Ollama makes it easy to run large language models (LLMs) locally. No API keys, no cloud costs, no data leaving your machine. This guide walks you through setting up Ollama and creating a simple chatbot interface.

---

## Project Details

| Detail | Information |
|--------|-------------|
| **Difficulty** | Intermediate |
| **Time Required** | 1 hour |
| **Category** | AI & Machine Learning |
| **Last Updated** | December 2025 |

---

## What You'll Learn

- Installing and configuring Ollama
- Downloading and running various AI models
- Creating a Python chatbot interface
- Understanding model sizes and hardware requirements
- Customizing model behavior with system prompts

---

## Prerequisites

- Windows 11, macOS, or Linux
- 8GB+ RAM (16GB+ recommended)
- 10GB+ free disk space per model
- Basic command line familiarity
- Python 3.8+ (for the chatbot interface)

---

## Hardware Recommendations

| RAM | GPU VRAM | Recommended Models |
|-----|----------|-------------------|
| 8GB | None | Phi-3, Gemma 2B, TinyLlama |
| 16GB | None | Llama 3.2 3B, Mistral 7B |
| 16GB | 8GB+ | Llama 3.1 8B, CodeLlama |
| 32GB+ | 12GB+ | Llama 3.1 70B (quantized), Mixtral |

---

## Step-by-Step Guide

### Step 1: Install Ollama

=== "Windows"
    1. Download from [ollama.com/download](https://ollama.com/download)
    2. Run the installer
    3. Ollama runs as a background service automatically

=== "macOS"
    ```bash
    # Using Homebrew
    brew install ollama
    
    # Or download from ollama.com/download
    ```

=== "Linux"
    ```bash
    curl -fsSL https://ollama.com/install.sh | sh
    ```

### Step 2: Verify Installation

```bash
# Check Ollama is running
ollama --version

# Should show something like: ollama version 0.1.x
```

### Step 3: Download Your First Model

```bash
# Download Llama 3.2 (3B parameters - good balance of speed/quality)
ollama pull llama3.2

# Other popular options:
# ollama pull mistral        # Great general purpose 7B model
# ollama pull codellama      # Optimized for code
# ollama pull phi3           # Microsoft's small but capable model
# ollama pull gemma2:2b      # Google's efficient 2B model
```

!!! info "Download Sizes"
    - `phi3`: ~2.5GB
    - `llama3.2`: ~2GB  
    - `mistral`: ~4GB
    - `llama3.1:8b`: ~4.7GB
    - `codellama`: ~4GB

### Step 4: Test in Terminal

```bash
# Start an interactive chat
ollama run llama3.2

# You'll see a prompt:
>>> Hello! What can you help me with?

# Type /bye to exit
```

### Step 5: Create a Python Chatbot

First, install the required package:

```bash
pip install ollama
```

Now create your chatbot:

```python title="chatbot.py"
"""
Local AI Chatbot using Ollama
A simple but powerful conversational interface.
"""

import ollama
from datetime import datetime

# Configuration
MODEL = "llama3.2"  # Change to your preferred model
SYSTEM_PROMPT = """You are a helpful, friendly AI assistant. You are:
- Concise but thorough in your responses
- Honest about limitations and uncertainties
- Focused on being genuinely helpful

Current date: {date}
"""

def create_chat():
    """Initialize a new chat session with conversation history."""
    return {
        "messages": [],
        "system": SYSTEM_PROMPT.format(date=datetime.now().strftime("%B %d, %Y"))
    }

def chat(session: dict, user_message: str) -> str:
    """
    Send a message and get a response while maintaining conversation history.
    
    Args:
        session: Chat session dictionary with message history
        user_message: The user's input message
        
    Returns:
        The AI's response text
    """
    # Add user message to history
    session["messages"].append({
        "role": "user",
        "content": user_message
    })
    
    # Prepare messages with system prompt
    messages = [
        {"role": "system", "content": session["system"]},
        *session["messages"]
    ]
    
    # Get response from Ollama
    response = ollama.chat(
        model=MODEL,
        messages=messages,
        options={
            "temperature": 0.7,     # Creativity (0.0-1.0)
            "top_p": 0.9,           # Nucleus sampling
            "num_predict": 1024,    # Max tokens in response
        }
    )
    
    # Extract and store assistant response
    assistant_message = response["message"]["content"]
    session["messages"].append({
        "role": "assistant", 
        "content": assistant_message
    })
    
    return assistant_message

def stream_chat(session: dict, user_message: str):
    """
    Stream a response token by token for a more interactive feel.
    
    Args:
        session: Chat session dictionary
        user_message: The user's input message
        
    Yields:
        Individual tokens as they're generated
    """
    session["messages"].append({
        "role": "user",
        "content": user_message
    })
    
    messages = [
        {"role": "system", "content": session["system"]},
        *session["messages"]
    ]
    
    # Stream the response
    full_response = ""
    for chunk in ollama.chat(
        model=MODEL,
        messages=messages,
        stream=True,
        options={"temperature": 0.7}
    ):
        token = chunk["message"]["content"]
        full_response += token
        yield token
    
    # Store complete response in history
    session["messages"].append({
        "role": "assistant",
        "content": full_response
    })

def main():
    """Main chat loop with streaming responses."""
    print("=" * 50)
    print(f"  Local AI Chatbot (Model: {MODEL})")
    print("=" * 50)
    print("Type 'quit' to exit, 'clear' to reset conversation\n")
    
    session = create_chat()
    
    while True:
        try:
            user_input = input("\n🧑 You: ").strip()
            
            if not user_input:
                continue
            
            if user_input.lower() == "quit":
                print("\nGoodbye! 👋")
                break
            
            if user_input.lower() == "clear":
                session = create_chat()
                print("\n🔄 Conversation cleared!")
                continue
            
            # Stream the response
            print("\n🤖 AI: ", end="", flush=True)
            for token in stream_chat(session, user_input):
                print(token, end="", flush=True)
            print()  # New line after response
            
        except KeyboardInterrupt:
            print("\n\nGoodbye! 👋")
            break
        except Exception as e:
            print(f"\n❌ Error: {e}")
            print("Make sure Ollama is running: `ollama serve`")

if __name__ == "__main__":
    main()
```

### Step 6: Run Your Chatbot

```bash
python chatbot.py
```

---

## Customizing Behavior

### Create a Custom Model with System Prompt

Create a `Modelfile`:

```dockerfile title="Modelfile"
FROM llama3.2

# Set the system prompt
SYSTEM """
You are CodeHelper, an expert programming assistant. You:
- Write clean, well-documented code
- Explain concepts clearly with examples
- Suggest best practices and optimizations
- Ask clarifying questions when requirements are unclear
"""

# Adjust parameters
PARAMETER temperature 0.3
PARAMETER top_p 0.9
```

Build and use your custom model:

```bash
# Create the custom model
ollama create codehelper -f Modelfile

# Run it
ollama run codehelper
```

### Useful API Examples

```python
# List available models
models = ollama.list()
print([m["name"] for m in models["models"]])

# Get model info
info = ollama.show("llama3.2")
print(f"Parameters: {info['details']['parameter_size']}")

# Generate text (no conversation)
response = ollama.generate(
    model="llama3.2",
    prompt="Write a haiku about programming:"
)
print(response["response"])

# Embeddings for semantic search
embedding = ollama.embeddings(
    model="llama3.2",
    prompt="What is machine learning?"
)
print(f"Embedding dimensions: {len(embedding['embedding'])}")
```

---

## Model Comparison

| Model | Size | Speed | Best For |
|-------|------|-------|----------|
| `phi3` | 2.5GB | ⚡⚡⚡ | Quick tasks, low resources |
| `gemma2:2b` | 1.6GB | ⚡⚡⚡ | Efficient, Google quality |
| `llama3.2` | 2GB | ⚡⚡⚡ | General purpose, balanced |
| `mistral` | 4GB | ⚡⚡ | Excellent reasoning |
| `llama3.1:8b` | 4.7GB | ⚡⚡ | High quality, versatile |
| `codellama` | 4GB | ⚡⚡ | Code generation |
| `mixtral` | 26GB | ⚡ | Best open-source quality |

---

## Common Issues & Troubleshooting

### Ollama not responding
```bash
# Check if service is running
ollama serve

# Or restart the service
# Windows: Restart from system tray
# macOS/Linux: `sudo systemctl restart ollama`
```

### Out of memory
- Try a smaller model (`phi3`, `gemma2:2b`)
- Close other applications
- Use quantized versions (e.g., `llama3.1:8b-q4_0`)

### Slow responses
- Ensure GPU is being used (check Task Manager/Activity Monitor)
- Try a smaller model
- Reduce `num_predict` in options

---

## Next Steps

- [ ] Build a web interface with Gradio or Streamlit
- [ ] Add RAG (Retrieval Augmented Generation) with your own documents
- [ ] Create specialized assistants for different tasks
- [ ] Experiment with vision models (LLaVA)
- [ ] Set up Ollama on your home server for network access

---

## Resources

- [Ollama Official Documentation](https://ollama.com/)
- [Ollama Model Library](https://ollama.com/library)
- [Ollama Python SDK](https://github.com/ollama/ollama-python)
- [r/LocalLLaMA](https://reddit.com/r/LocalLLaMA) - Community discussions
