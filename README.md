# 🤖 Advanced AI Telegram Assistant — n8n Workflow

> A private, owner-only AI assistant for Telegram that handles **text**, **voice**, and **image** messages — powered by Google Gemini and built on n8n.

---

## ✨ Features

- 🔒 **Owner-only access** — only your Telegram account can use the bot; everyone else is denied automatically
- 💬 **Text messages** — full AI conversation with memory across the session
- 🎙️ **Voice messages** — transcribed and processed by Gemini AI
- 🖼️ **Image messages** — analysed and described by Gemini Vision
- 🧠 **Conversation memory** — remembers context within a session (Window Buffer Memory)
- 📄 **Long message support** — automatically splits responses that exceed Telegram's character limit
- 🎨 **Telegram HTML formatting** — responses use bold, italic, code blocks, and links natively
- ⚡ **Typing indicator** — sends a "typing…" action while the AI processes
- 🛡️ **Graceful error handling** — sends a clean error message if something fails

---

## 🗂️ Workflow Architecture

```
Telegram Message
      │
      ▼
 Owner Check (IF)
  ├── ❌ Not owner → Send Deny Message → Stop
  └── ✅ Owner match
           │
           ▼
     Send Typing Action
           │
           ▼
   Determine Content Type (Switch)
    ├── text  → Set Text Message
    ├── voice → Download Voice → Transcribe (Gemini) → Set Transcription
    └── image → Download Image → Analyze (Gemini Vision) → Set Analysis
           │
           ▼
        AI Agent (Gemini + Memory + Portfolio Knowledge)
           │
           ▼
     Split Long Message
           │
      ┌────┴─────┐
      ▼           ▼
 Send Chunks   Strip HTML Fallback → Send Plain Text
```

---

## 🔧 Nodes Used

| Node | Purpose |
|---|---|
| `Telegram Trigger` | Listens for incoming messages |
| `IF` | Owner-only access check |
| `Telegram` (×7) | Send typing, download files, send replies, send deny |
| `Switch` | Routes by message type (text / voice / image) |
| `Set` (×3) | Normalises message content |
| `Google Gemini` (×2) | Voice transcription + image analysis |
| `AI Agent` | Core LLM reasoning with portfolio knowledge |
| `Google Gemini Chat Model` | LLM backend for the agent |
| `Window Buffer Memory` | Session-level conversation memory |
| `Code` (×2) | Split long messages + strip HTML fallback |

---

## 🚀 Setup Guide

### Prerequisites

- [n8n](https://n8n.io) instance (self-hosted or cloud)
- Telegram Bot Token — create one via [@BotFather](https://t.me/BotFather)
- Google Gemini API Key — get one at [Google AI Studio](https://aistudio.google.com)
- Your Telegram numeric user ID — message [@userinfobot](https://t.me/userinfobot) to find it

---

### Step 1 — Import the Workflow

1. Open your n8n instance
2. Go to **Workflows → Import from file**
3. Upload `Advance_Ai_Assistant_Updated.json`

---

### Step 2 — Set the Environment Variable

Go to **Settings → Environment Variables** and add:

| Variable | Value |
|---|---|
| `OWNER_TELEGRAM_ID` | Your Telegram numeric user ID (e.g. `123456789`) |

> ⚠️ This is the most important step. Without it, the owner check will not work correctly.

**How to find your Telegram ID:**
Open Telegram → search for **@userinfobot** → send any message → it replies with your numeric ID.

---

### Step 3 — Add Credentials

In n8n go to **Credentials** and create:

**Telegram Bot:**
- Type: `Telegram API`
- Bot Token: your token from @BotFather

**Google Gemini:**
- Type: `Google Gemini(PaLM) Api`
- API Key: your key from Google AI Studio

Then attach both credentials to their respective nodes inside the workflow.

---

### Step 4 — Activate

Toggle the workflow to **Active**. Send a message to your bot on Telegram to test.

---

## 🔒 How Owner-Only Protection Works

Every incoming Telegram message carries the sender's numeric user ID.

The **Owner Check** node compares that ID against your `OWNER_TELEGRAM_ID` environment variable:

```
sender ID == OWNER_TELEGRAM_ID  →  ✅ continue to AI
sender ID != OWNER_TELEGRAM_ID  →  ⛔ send denial + stop
```

The AI agent's system prompt also contains the same check as a secondary layer — the model is instructed to refuse any request if the IDs do not match.

---

## 🛠️ Customisation

### Update the AI's Knowledge Base

Open the **AI Agent** node → click **Options** → edit the **System Message**.

The system message contains your personal portfolio data (name, skills, services, projects, social links). Update any section as needed.

### Change the AI Model

Open the **Google Gemini Chat Model** node and select a different model (e.g. `gemini-2.5-pro`, `gemini-2.0-flash`).

### Adjust Memory Window

Open the **Window Buffer Memory** node and change the **Context Window Length** to control how many messages back the bot remembers.

---

## 📁 Repository Structure

```
├── Advance_Ai_Assistant_Updated.json   # n8n workflow (import this)
└── README.md                           # This file
```

---

## 🧑‍💻 Author

**Sahin Enam**
Full Stack Web Developer & AI Automation Specialist — Dhaka, Bangladesh

[![GitHub](https://img.shields.io/badge/GitHub-SahinShazi-181717?style=flat&logo=github)](https://github.com/SahinShazi)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-sahinenam-0A66C2?style=flat&logo=linkedin)](https://www.linkedin.com/in/sahinenam)
[![Telegram](https://img.shields.io/badge/Telegram-SahinShazi-26A5E4?style=flat&logo=telegram)](https://t.me/SahinShazi)

---

## 📄 License

MIT — free to use, modify, and distribute. Attribution appreciated.
