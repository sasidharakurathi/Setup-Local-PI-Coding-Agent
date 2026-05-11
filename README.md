# PI Coding Agent + Gemma 4 - Free Local AI Coding Agent

> **Run a local AI coding agent on any laptop. No GPU, no API keys.**

---

## What Is This?

Run **PI coding agent** connected to **Gemma 4** locally via **LM Studio**. Everything runs on your machine.

| Tool | Role |
|------|------|
| **PI Coding Agent** | Termial based coding agent - 4 tools optimized system prompt |
| **LM Studio** | Local model server - OpenAI-compatible API |
| **Gemma 4 E4B** | Google's new open source model - 6.33GB, tool calling native + Multimodel |

---

## Requirements

| Requirement | Detail |
|------------|--------|
| OS | Windows (Tested on windows, it might work on MacOS or Linux too)|
| RAM | 16GB  |
| Node.js | v20 or higher |

---

## Installation

### Step 1 - Install LM Studio

```powershell
winget install ElementLabs.LMStudio -e
cmd /c %USERPROFILE%\.lmstudio\bin\lms.exe bootstrap
```
Close and reopen terminal, then verify:
```powershell
lms --version
```
---

### Step 2 - Download & Load Gemma 4

```bash
lms server start
lms status
```

**Download model:**
```bash
lms get "google"
```
- Select: google/gemma-4-e4b
- Confirm download (~6.3GB)

**Check estimate before loading:**
```bash
lms load google/gemma-4-e4b --estimate-only
```

**Load with 8K context:**
```bash
lms load google/gemma-4-e4b --context-length 8000 --identifier "gemma4"
lms ps
```

**Quick test:**
```bash
lms chat gemma4 -p "Hey buddy, what model are you?"
```

---

### Step 3 - Install PI Coding Agent

**Install Node.js (skip if already exists):**

```powershell
winget install OpenJS.NodeJS.LTS -e
node --version
```

**Install PI Coding Agent:**
```bash
npm install -g @mariozechner/pi-coding-agent
pi --version
```

---

### Step 4 - Configure PI Coding Agent to use LM Studio:

**Create config file:**

```powershell
code $HOME\.pi\agent\models.json
```

**Paste this config:**
```json
{
  "providers": {
    "lmstudio": {
      "baseUrl": "http://localhost:1234/v1",
      "api": "openai-completions",
      "apiKey": "lm-studio",
      "compat": {
        "supportsDeveloperRole": false,
        "supportsReasoningEffort": false
      },
      "models": [
        {
          "id": "gemma4",
          "reasoning": true
        }
      ]
    }
  }
}
```

---

### Step 5 - Launch PI

**Terminal 1 (Watch live logs):**
```bash
lms log stream
```

**Terminal 2 (Launch PI Coding Agent):**
- Create a test directory and open terminal there:
- Then run:
```bash
pi
```

At PI terminal, select the LM Studio model:
```
/model → select lmstudio → gemma4
```

Test prompt:
```
list all files in this directory
```

Check Terminal 1 - you'll see the tool call trigger live.

---

## Note:
- If `n_keep >= n_ctx` then Increase context: `lms load gemma4 --context-length 8000` |
- First message is slow on CPU because LM Studio caches system prompt at first request. Subsequent requests are faster.

---

## Key Commands

```bash
lms server start              # Starts LM Studio server
lms get "google"              # To browse and download models
lms ls                        # Lists all downloaded models
lms load google/gemma-4-e4b   # Loads the model into Memory (CPU - RAM or GPU + VRAM)
lms ps                        # Lists all loaded models
lms log stream                # Logs all the live requests
lms chat gemma4 -p "hello"    # Send a quick prompt to the model
lms unload --all              # Stops all models and frees up RAM/VRAM
pi                            # Launches PI Coding agent
```