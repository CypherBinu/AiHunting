# 🎯 LOW COST AI HUNTING SETUP

Hey guys, it's **cypherbinu** 👋

This repository is very helpful for bug bounty hunters who want to use AI for their hunting workflow without wasting money on expensive AI tokens.

I've shared some useful and secret methodologies here that can help you use AI at a much lower cost while improving your hunting efficiency.

**New hunting skills and methodologies are being added regularly**, so make sure to check back and explore the different directories and files in this repository to find useful techniques for your specific hunting goals.

The goal is simple: **spend less on AI and hunt smarter.**

> Use these methods only on programs and targets where you have permission to test.

---

## 🛠️ Prerequisites

Ensure you have **Node.js (v18 or higher)** installed on your system.

Install Claude Code globally via NPM:

```bash
npm install -g @anthropic-ai/claude-code
```

> **Note:** Use `sudo` on Linux/macOS if you encounter permission errors.

---
## 🔑 AI API Providers & Gateways

- **AgentRouter** — Multi-model API gateway  
  🔗 [Register](https://agentrouter.org/register?aff=JXHC)  
  🎁 GitHub login & available signup credits  
  🤖 Multiple AI models through one API

- **FatBunny Hub** — Cheap AI API access via Telegram  
  🤖 `@FatBunny_Hub_bot`  
  💰 Cheap API tokens & multiple AI models

- **KiraAI** — Low-cost AI API access  
  🔗 [Register](https://kiraai.vn/?ref=cypherbb)  
  🎁 Signup bonus  
  📅 Daily check-in rewards  
  🔑 API token available

- **HackWithClaude** — Affordable access to AI models  
  🔗 [Register](https://hackwithclaude.com/?ref=671b19d2)  
  🔑 API token access  
  🤖 Multiple AI models

  ---
  ## 🤖 AgentRouter + Claude Code Setup

  ### 🐧 Linux (Bash / Zsh)

```bash
# For Zsh (Default on Kali/Ubuntu):
echo 'export ANTHROPIC_BASE_URL="https://agentrouter.org"' >> ~/.zshrc
echo 'export ANTHROPIC_AUTH_TOKEN="YOUR_AGENTROUTER_API_KEY"' >> ~/.zshrc
echo 'export ANTHROPIC_MODEL="claude-opus-4-6"' >> ~/.zshrc
echo 'export CLAUDE_CODE_USE_AUTH_TOKEN="true"' >> ~/.zshrc
source ~/.zshrc

# For Bash:
# Replace ~/.zshrc with ~/.bashrc in the commands above
# and run: source ~/.bashrc

# Added by AgentRouter CLI installer
export ANTHROPIC_BASE_URL="https://agentrouter.org"
export ANTHROPIC_AUTH_TOKEN="YOUR_AGENTROUTER_API_KEY"
export ANTHROPIC_MODEL="deepseek-v4-flash"
export CLAUDE_CODE_USE_AUTH_TOKEN="true"
export CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC="1"

```
> **Note:** Set `ANTHROPIC_MODEL` according to the model available in your AgentRouter account. Model IDs can change, so check the latest AgentRouter documentation/model list before configuring it. The configuration above follows the current AgentRouter Claude Code documentation.
---
### 🐰 FatBunny Hub

Cheap AI API access via Telegram.

🤖 `@FatBunny_Hub_bot`  
💰 Cheap API tokens & multiple AI models

#### ⚙️ Setup

1. Open `@FatBunny_Hub_bot` on Telegram.
2. Choose the required AI model/API and purchase the required token.
3. After purchase, the bot provides the **API documentation**.
4. Open the provided documentation to find the **API endpoint, API key/token, supported models, and usage examples**.
5. Copy the API key/token and configure it in your AI tool according to the provided documentation.
6. Test the API and start using the selected model.

![FatBunny Hub API Setup](https://raw.githubusercontent.com/CypherBinu/AiHunting/main/buytokenbot.png)


