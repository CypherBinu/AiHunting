# Claude Code (Custom Setup)

![Node.js Version](https://img.shields.io/badge/Node.js-18%2B-brightgreen?style=flat-square)
[![npm version](https://img.shields.io/npm/v/@anthropic-ai/claude-code.svg?style=flat-square)](https://www.npmjs.com/package/@anthropic-ai/claude-code)

Claude Code is an agentic coding tool that runs directly in your terminal. It understands your codebase and can handle routine tasks, explain complex code, and manage git workflows via natural language commands.

---

## 🛠️ Prerequisites

Ensure you have **Node.js (v18 or higher)** installed on your system.

Install Claude Code globally via NPM:

```bash
npm install -g @anthropic-ai/claude-code
```

> **Note:** Use `sudo` on Linux/macOS if you encounter permission errors.

---

# ⚡ Configuration & Setup

You can configure Claude Code using either **AgentRouter** or **Nghimmo API**.

Choose the method you want to use and follow the corresponding setup instructions.

---

# 🔹 Method 1 — AgentRouter

AgentRouter allows Claude Code to connect through a custom API endpoint.

## 🪟 Windows — Command Prompt / PowerShell

Run:

```powershell
setx ANTHROPIC_BASE_URL "https://agentrouter.org"
setx ANTHROPIC_AUTH_TOKEN "YOUR_AGENTROUTER_API_KEY"
setx ANTHROPIC_MODEL "claude-opus-4-6"
setx CLAUDE_CODE_USE_AUTH_TOKEN "true"
```

After running the commands, **restart your terminal**.

---

## 🐧 Linux — Bash / Zsh

### Zsh

```bash
echo 'export ANTHROPIC_BASE_URL="https://agentrouter.org"' >> ~/.zshrc
echo 'export ANTHROPIC_AUTH_TOKEN="YOUR_AGENTROUTER_API_KEY"' >> ~/.zshrc
echo 'export ANTHROPIC_MODEL="claude-opus-4-6"' >> ~/.zshrc
echo 'export CLAUDE_CODE_USE_AUTH_TOKEN="true"' >> ~/.zshrc

source ~/.zshrc
```

### Bash

```bash
echo 'export ANTHROPIC_BASE_URL="https://agentrouter.org"' >> ~/.bashrc
echo 'export ANTHROPIC_AUTH_TOKEN="YOUR_AGENTROUTER_API_KEY"' >> ~/.bashrc
echo 'export ANTHROPIC_MODEL="claude-opus-4-6"' >> ~/.bashrc
echo 'export CLAUDE_CODE_USE_AUTH_TOKEN="true"' >> ~/.bashrc

source ~/.bashrc
```

---

# 🔹 Method 2 — Nghimmo API

Nghimmo provides an Anthropic-compatible API endpoint that can be used with Claude Code.

### Base URL

```text
https://api.nghimmo.com
```

## 🪟 Windows — Command Prompt / PowerShell

Run:

```powershell
setx ANTHROPIC_BASE_URL "https://api.nghimmo.com"
setx ANTHROPIC_AUTH_TOKEN "YOUR_NGHIMMO_API_KEY"
setx ANTHROPIC_MODEL "claude-opus-4-6"
setx CLAUDE_CODE_USE_AUTH_TOKEN "true"
```

After running the commands, **restart your terminal**.

---

## 🐧 Linux — Bash / Zsh

### Zsh

```bash
echo 'export ANTHROPIC_BASE_URL="https://api.nghimmo.com"' >> ~/.zshrc
echo 'export ANTHROPIC_AUTH_TOKEN="YOUR_NGHIMMO_API_KEY"' >> ~/.zshrc
echo 'export ANTHROPIC_MODEL="claude-opus-4-6"' >> ~/.zshrc
echo 'export CLAUDE_CODE_USE_AUTH_TOKEN="true"' >> ~/.zshrc

source ~/.zshrc
```

### Bash

```bash
echo 'export ANTHROPIC_BASE_URL="https://api.nghimmo.com"' >> ~/.bashrc
echo 'export ANTHROPIC_AUTH_TOKEN="YOUR_NGHIMMO_API_KEY"' >> ~/.bashrc
echo 'export ANTHROPIC_MODEL="claude-opus-4-6"' >> ~/.bashrc
echo 'export CLAUDE_CODE_USE_AUTH_TOKEN="true"' >> ~/.bashrc

source ~/.bashrc
```

> **Important:** Replace `YOUR_NGHIMMO_API_KEY` with your actual Nghimmo API key.

---

# 🔄 Switching Between AgentRouter and Nghimmo

Claude Code uses the following environment variables:

```text
ANTHROPIC_BASE_URL
ANTHROPIC_AUTH_TOKEN
ANTHROPIC_MODEL
CLAUDE_CODE_USE_AUTH_TOKEN
```

To switch from **AgentRouter** to **Nghimmo**, change the Base URL and API key.

### AgentRouter

```bash
export ANTHROPIC_BASE_URL="https://agentrouter.org"
export ANTHROPIC_AUTH_TOKEN="YOUR_AGENTROUTER_API_KEY"
```

### Nghimmo

```bash
export ANTHROPIC_BASE_URL="https://api.nghimmo.com"
export ANTHROPIC_AUTH_TOKEN="YOUR_NGHIMMO_API_KEY"
```

Then reload your shell:

```bash
source ~/.bashrc
```

or:

```bash
source ~/.zshrc
```

---

# 🤖 Model Configuration

The model can be changed using:

```bash
export ANTHROPIC_MODEL="claude-opus-4-6"
```

Make sure the selected model is supported by the API provider you are using.

---

# 🚀 Launching Claude Code

Once the configuration is complete, navigate to your project directory:

```bash
cd /path/to/your/project
```

Then start Claude Code:

```bash
claude
```

Claude Code will use the API endpoint, authentication token, and model configured in your environment variables.

---

# 🔍 Check Your Configuration

You can verify that the variables are set:

```bash
echo $ANTHROPIC_BASE_URL
echo $ANTHROPIC_MODEL
echo $CLAUDE_CODE_USE_AUTH_TOKEN
```

For security reasons, **do not print or share your API token**.

---

# 🔐 Security

Never publish your API keys in:

- GitHub repositories
- Screenshots
- Public posts
- README files
- Shared configuration files
- Discord/Telegram messages

Use placeholders such as:

```text
YOUR_AGENTROUTER_API_KEY
YOUR_NGHIMMO_API_KEY
```

instead of exposing your real API key.

If an API key is accidentally exposed, revoke or rotate it immediately.

---

# 📌 Quick Setup

### AgentRouter

```bash
export ANTHROPIC_BASE_URL="https://agentrouter.org"
export ANTHROPIC_AUTH_TOKEN="YOUR_AGENTROUTER_API_KEY"
export ANTHROPIC_MODEL="claude-opus-4-6"
export CLAUDE_CODE_USE_AUTH_TOKEN="true"
```

### Nghimmo

```bash
export ANTHROPIC_BASE_URL="https://api.nghimmo.com"
export ANTHROPIC_AUTH_TOKEN="YOUR_NGHIMMO_API_KEY"
export ANTHROPIC_MODEL="claude-opus-4-6"
export CLAUDE_CODE_USE_AUTH_TOKEN="true"
```

Then simply run:

```bash
claude
```

---

## 📖 Summary

| Provider | Base URL |
|---|---|
| AgentRouter | `https://agentrouter.org` |
| Nghimmo | `https://api.nghimmo.com` |

Both methods use the same Claude Code environment variables. Simply select the provider you want and configure its **Base URL**, **API key**, and **model**.
