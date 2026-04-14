# 🤖 Run Claude Code for Free with Ollama

> Use Anthropic's Claude Code — the powerful agentic coding CLI — with open-source and cloud models via Ollama. **No Anthropic API billing required.**

---

## 📋 Table of Contents

- [What is This?](#what-is-this)
- [System Requirements](#system-requirements)
- [Step 1 — Install Ollama](#step-1--install-ollama)
- [Step 2 — Pull a Model](#step-2--pull-a-model)
- [Step 3 — Install Claude Code](#step-3--install-claude-code)
- [Step 4 — Launch Claude Code via Ollama](#step-4--launch-claude-code-via-ollama)
- [Step 5 — Manual Setup (Alternative)](#step-5--manual-setup-alternative)
- [Recommended Models](#recommended-models)
- [Headless / CI Mode](#headless--ci-mode)
- [Tips & Troubleshooting](#tips--troubleshooting)
- [Resources](#resources)

---

## What is This?

**Claude Code** is Anthropic's agentic coding tool that lives in your terminal. It reads, edits, and executes code in your working directory through a conversational interface.

Normally it requires a paid Anthropic API plan. But thanks to **Ollama's Anthropic-compatible API** (available since Ollama v0.14.0), you can now point Claude Code at local or cloud open-source models — completely free.

---

## System Requirements

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| OS | Windows 10+, macOS, Linux | Windows 11, macOS (Apple Silicon), Ubuntu 22.04+ |
| RAM | 8 GB | 16–32 GB |
| Storage | 10 GB free | 20–50 GB free (for model files) |
| GPU | Optional (CPU works) | NVIDIA GPU with CUDA, or Apple Silicon (M-series) |
| Node.js | v18+ | Latest LTS |
| Ollama | v0.14.0+ | Latest |

> ⚠️ **RAM Guide:** 8 GB RAM → 7B models max · 16 GB → up to 13B models · 32 GB → 30B+ models
> GPU is optional but strongly recommended — it makes responses **5–20× faster**.

---

## Step 1 — Install Ollama

### 🪟 Windows (PowerShell)

```powershell
# Download and run the official Ollama Windows installer
irm https://ollama.com/install.ps1 | iex
```

> Alternatively, visit [ollama.com/download](https://ollama.com/download), download `OllamaSetup.exe`, and run it.

After installation, **open a new PowerShell window** and verify:

```powershell
ollama --version
```

You should see something like `ollama version 0.15.x`. If you get "command not found", restart PowerShell or reboot your PC — Ollama adds itself to PATH on install.

---

### 🍎 macOS

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Or download the macOS app from [ollama.com/download](https://ollama.com/download). Apple Silicon (M1/M2/M3) is automatically optimized — no extra configuration needed.

---

### 🐧 Linux

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Ollama runs as a background service after install.

---

## Step 2 — Pull a Model

Once Ollama is installed, download a model. Choose based on your RAM (see the [Recommended Models](#recommended-models) table below).

```powershell
# Example: pull a lightweight local model
ollama pull qwen3.5

# Or pull a cloud model (no local storage needed)
ollama pull kimi-k2.5:cloud
```

> ☁️ **Cloud models** (tagged `:cloud`) run on remote infrastructure through Ollama. Same commands, no local GPU needed, and they always run at full context length.

Verify the model works:

```powershell
ollama run qwen3.5
# Type a quick prompt to test, then Ctrl+D or /bye to exit
```

---

## Step 3 — Install Claude Code

Claude Code requires **Node.js v18+**. Install it from [nodejs.org](https://nodejs.org) if you don't have it.

### 🪟 Windows (PowerShell)

```powershell
irm https://claude.ai/install.ps1 | iex
```

### 🍎 macOS / 🐧 Linux

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

Verify installation:

```powershell
claude --version
```

---

## Step 4 — Launch Claude Code via Ollama

This is the easiest method. Ollama handles the API bridge automatically.

### Quick launch (model selector menu)

```powershell
ollama launch claude
```

This opens an interactive menu — use arrow keys to pick a model, then Claude Code starts.

### Launch with a specific model

```powershell
# With a cloud model (recommended for best tool use support)
ollama launch claude --model kimi-k2.5:cloud

# With a local model
ollama launch claude --model qwen3.5
```

> ✅ Navigate to your project directory first, then run the command — Claude Code will operate on files in that folder.

---

## Step 5 — Manual Setup (Alternative)

If you prefer to configure the environment variables yourself:

### 🪟 Windows (PowerShell)

```powershell
$env:ANTHROPIC_AUTH_TOKEN = "ollama"
$env:ANTHROPIC_API_KEY = ""
$env:ANTHROPIC_BASE_URL = "http://localhost:11434"

claude --model qwen3.5
```

To make these variables persist across sessions, add them to your PowerShell profile (`$PROFILE`).

### 🍎 macOS / 🐧 Linux

```bash
export ANTHROPIC_AUTH_TOKEN=ollama
export ANTHROPIC_API_KEY=""
export ANTHROPIC_BASE_URL=http://localhost:11434

claude --model kimi-k2.5:cloud
```

Or run inline in one command:

```bash
ANTHROPIC_AUTH_TOKEN=ollama ANTHROPIC_BASE_URL=http://localhost:11434 ANTHROPIC_API_KEY="" claude --model glm-5:cloud
```

---

## Recommended Models

| Model | Type | Best For | Min RAM |
|-------|------|----------|---------|
| `kimi-k2.5:cloud` | ☁️ Cloud | Best overall coding quality, full tool use | No local GPU needed |
| `glm-5:cloud` | ☁️ Cloud | Strong reasoning + code generation | No local GPU needed |
| `minimax-m2.7:cloud` | ☁️ Cloud | Balanced performance | No local GPU needed |
| `qwen3.5:cloud` | ☁️ Cloud | Fast, capable, great for most tasks | No local GPU needed |
| `glm-4.7-flash` | 💻 Local | Fast local model, good for small tasks | 8 GB |
| `qwen3.5` | 💻 Local | Solid local coding model | 8 GB |

> 💡 Cloud models are available at [ollama.com/search?c=cloud](https://ollama.com/search?c=cloud).
> 
> ⚠️ **Known limitation:** Full agentic capabilities (file read/write, project scanning) work most reliably with cloud models. Local models may behave as a plain LLM without tool access depending on their context window and capability.

---

## Headless / CI Mode

Run Claude Code non-interactively in Docker, CI/CD pipelines, or scripts:

```powershell
ollama launch claude --model kimi-k2.5:cloud --yes -- -p "Explain how this repository works"
```

- `--yes` — auto-pulls the model, skips interactive selectors (requires `--model` to be set)
- `--` — separates Ollama flags from Claude Code flags
- `-p "..."` — passes a prompt directly to Claude Code

---

## Tips & Troubleshooting

**`ollama: command not found` after install**
→ Close and reopen PowerShell/Terminal. If it persists, restart your computer.

**`unknown command "launch"` error**
→ Your Ollama version is outdated. Restart Ollama — it will auto-update. Then try again.

**Claude Code starts but can't read/write files (local models)**
→ This is a known limitation. Switch to a cloud model like `kimi-k2.5:cloud` for full agentic file operations.

**Model fails to load — "not enough memory" error**
→ Choose a smaller model. See the RAM guide in [System Requirements](#system-requirements).

**Slow responses on local models**
→ CPU-only mode is significantly slower. A GPU with at least 6 GB VRAM will dramatically improve speed.

**Context window too small**
→ Claude Code needs at least 32K–64K tokens. Cloud models always run at full context. For local models, check [Ollama's context length docs](https://docs.ollama.com/context-length).

---

## Resources

- 📖 [Ollama Claude Code Docs](https://docs.ollama.com/integrations/claude-code)
- 📖 [Claude Code Docs](https://docs.claude.ai/en/overview)
- 🔍 [Ollama Cloud Model Search](https://ollama.com/search?c=cloud)
- 💬 [Ollama Discord](https://discord.gg/74FzdcnZ)

---

> Made with ❤️ for developers who want powerful AI coding tools without the cloud bill.
> Feel free to share, fork, or contribute improvements!
