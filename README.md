# Antigravity-ClaudeCode Orchestrator

> **Autonomous multi-agent orchestration bridging Anthropic's Claude Code and Google's Antigravity (Gemini) inside VS Code.**

[![Node.js](https://img.shields.io/badge/Node.js-20%2B-brightgreen)](https://nodejs.org/)
[![Model Context Protocol](https://img.shields.io/badge/MCP-Standard-blue)](https://modelcontextprotocol.io/)
[![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20macOS%20%7C%20Linux-lightgrey)](https://github.com/)

---

## 🚀 Motivation: Why Pair Claude Code and Antigravity?

Modern software engineering demands both **deep, nuanced logical reasoning** and **vast, repo-wide contextual awareness**. Today, two frontier agentic coding ecosystems lead the industry:

1. **Claude Code (`claude`)**: Anthropic's terminal and VS Code agent powered by **Claude 3.7 Sonnet** with hybrid reasoning and extended thinking.
2. **Antigravity (`agy`)**: Google DeepMind's development engine powered by **Gemini 2.5 / 3.0 Pro & Flash** with massive context and deep language-server indexing.

Rather than choosing one over the other, **Antigravity-ClaudeCode Orchestrator** pairs them as an autonomous dual-agent team. They operate in the same workspace, see each other's actions in real-time, and leverage each other's distinct engine superpowers.

```
                    ┌────────────────────────────────────────────────────────┐
                    │                   Developer in VS Code                 │
                    └───────────────────────────┬────────────────────────────┘
                                                │
                                                ▼
┌─────────────────────────────────────────────────────────────────────────────────────────────────┐
│  VS Code Workspace                                                                              │
│                                                                                                 │
│   ┌───────────────────────────────┐                  ┌──────────────────────────────────────┐   │
│   │   Claude Code (Lead Agent)    │                  │       Antigravity (agy.exe)          │   │
│   │   • Engine: Claude 3.7 Sonnet │                  │   • Engine: Gemini 2.5 / 3.0 Pro     │   │
│   │   • Extended Thinking         │                  │   • 1M–2M Massive Context Window     │   │
│   │   • Surgical Diffs & Logic    │                  │   • AST Language Server Graph        │   │
│   │   • Interactive VS Code UI    │                  │   • Web & Vertex AI Search Grounding │   │
│   └───────────────┬───────────────┘                  └──────────────────▲───────────────────┘   │
│                   │                                                     │                       │
│                   │                Model Context Protocol               │                       │
│                   └─────────────────────────┐                           │                       │
│                                             ▼                           │                       │
│                               ┌───────────────────────────┐             │                       │
│                               │   ag-claude MCP Server    │─────────────┘                       │
│                               │   • agy_explore           │                                     │
│                               │   • agy_audit             │                                     │
│                               │   • agy_delegate          │                                     │
│                               │   • ag_read_board         │                                     │
│                               └─────────────┬─────────────┘                                     │
│                                             │                                                   │
│                                             ▼                                                   │
│                               ┌───────────────────────────┐                                     │
│                               │     Shared Blackboard     │                                     │
│                               │    • .ag-claude/state.json│                                     │
│                               │    • blackboard.md        │                                     │
│                               └───────────────────────────┘                                     │
└─────────────────────────────────────────────────────────────────────────────────────────────────┘
```

---

## ⚡ Complementary Engine Superpowers

Claude Code and Antigravity are fundamentally different in design:

| Capability | Claude Code (`claude`) | Antigravity (`agy`) | Orchestration Advantage |
| :--- | :--- | :--- | :--- |
| **Foundation Model** | **Claude 3.7 Sonnet** (Anthropic hybrid reasoning) | **Gemini 2.5 / 3.0 Pro & Flash** (Google DeepMind) | Combines top frontier reasoning models across independent AI research families. |
| **Context Capacity** | **200k tokens** (high-density precision) | **1M to 2M tokens** (massive capacity) | Claude delegates multi-megabyte log analysis or entire repository scans to Gemini without truncation. |
| **Code Intelligence** | Surgical diff patching, localized refactors, unit-level precision | **Jetski Language Server AST graph**, native symbol reference tracing, multi-file index | Deep architectural dependency maps fed directly into Claude's prompt. |
| **Research & Search** | Local project tools (`Grep`, `Glob`, `Read`) | Live Google Search grounding, Vertex AI enterprise search, multi-modal ingestion (video/audio/PDFs) | Live web verification and multi-modal asset understanding. |
| **Adversarial Audit** | Evaluates code with Anthropic reasoning priors | Evaluates code with DeepMind Gemini priors | **Cross-model verification**: catches edge cases and blind spots that a single model family misses. |

---

## 🧠 Model Architecture: Main Agents vs. Subagents

Both systems support hierarchical agent delegation:

### 1. Claude Code Models
- **Main Agent**: Defaults to **Claude 3.7 Sonnet** with adaptive extended thinking. (Alternative models like Claude 3 Opus can be configured via `ANTHROPIC_MODEL`).
- **Claude Subagents** (`.claude/agents/*.md`):
  - `model: haiku` — high-speed, cost-effective file scanning and log filtering.
  - `model: sonnet` — standard feature generation and refactoring.
  - `model: opus` — deep architectural verification.
  - `model: inherit` — mirrors the parent session.

### 2. Antigravity Models
- **Main Agent**: Defaults to **Gemini 3.0 Pro / 2.5 Pro** or **Gemini 3.8 Flash High**.
- **Antigravity Subagents** (`research`, `self`, etc.):
  - `flash_lite` / `flash` — ultra-fast indexing and wide-angle searches.
  - `pro` — high-complexity synthesis, AST reasoning, and code reviews.
  - `inherit` — mirrors parent setting.

### 3. Orchestrator Synergy
When Claude Code runs in VS Code, it uses **Claude 3.7 Sonnet**. When it determines that a task requires repo-wide indexing, massive log digestion, or an independent security audit, it calls the MCP bridge. The orchestrator invokes `agy.exe` running **Gemini Pro/Flash**, returning distilled findings directly into Claude's context.

---

## 🎯 How Claude Code Autonomously Uses Antigravity

There are no rigid manual scripts. Through semantic MCP tool schemas and workspace guidance (`CLAUDE.md`), Claude Code autonomously evaluates incoming tasks:

```
Task Received in VS Code
 │
 ├── Surgical edit, single-file logic, conversational prompt?
 │    └── 🟢 Claude executes directly.
 │
 ├── Repository-wide symbol tracing, 100+ files, or 500kb+ log file?
 │    └── 🔵 Claude calls `agy_explore` (Gemini 1M+ context + AST Language Server).
 │
 ├── Critical architecture decision, security audit, or stuck on a bug (2+ retries)?
 │    └── 🟣 Claude calls `agy_audit` (Gemini Pro independent cross-model review).
 │
 └── Heavy parallel boilerplate or exhaustive test suite?
      └── 🟡 Claude calls `agy_delegate` (Antigravity background generation).
```

---

## 🔬 Comparison with `yuting0624/antigravity-for-claude-code`

The open-source plugin [`yuting0624/antigravity-for-claude-code`](https://github.com/yuting0624/antigravity-for-claude-code) demonstrated early interest in combining these tools. Our orchestrator evolves the concept to a production-grade standard:

| Feature | `yuting0624/antigravity-for-claude-code` | Antigravity-ClaudeCode Orchestrator |
| :--- | :--- | :--- |
| **Integration Method** | Claude Code plugin relying on manual slash commands (`/antigravity:delegate`, `/antigravity:review`). | **Official Model Context Protocol (MCP)** server via `.mcp.json`. Loaded automatically by VS Code. |
| **Invocation** | Manual developer typing. | **Autonomous AI-driven**: Claude Code decides when to invoke Antigravity based on heuristics. |
| **Windows Support** | **Fragile**: Relies on Unix pseudo-terminals (`pty`); known to hang on native Windows without WSL. | **Native Windows & Cross-Platform**: Robust stdio IPC and process isolation built specifically for Windows PowerShell / CMD. |
| **Shared State** | None: Fire-and-forget subprocess outputs. | **Blackboard Architecture**: Shared `.ag-claude/state.json` and `blackboard.md` for continuous collaboration memory. |
| **Engine Philosophy** | Treats Antigravity merely as a "cheap worker" for volume scaffolding. | **True Peer Synergy**: Respects Antigravity's unique 1M+ context window, language server graph, and artifacts. |

---

## 🛠️ MCP Tool Suite

When installed, Claude Code has access to four core tools in VS Code:

### 1. `agy_explore`
- **Purpose**: Deep codebase reconnaissance and massive context ingestion.
- **When used**: Scanning large dependency trees, tracing symbol definitions across multiple projects, or analyzing massive stack traces.
- **Superpower**: Uses Antigravity's 1M–2M token context and AST Language Server.

### 2. `agy_audit`
- **Purpose**: Independent, cross-model architectural and security review.
- **When used**: Validating complex algorithms, verifying PR diffs, or breaking debugging deadlocks.
- **Superpower**: Eliminates single-model confirmation bias via Gemini 3.0 Pro.

### 3. `agy_delegate`
- **Purpose**: Delegating self-contained subtasks or test suite generation.
- **When used**: Offloading boilerplate scaffolding or test writing while Claude focuses on core architecture.

### 4. `ag_read_board` & `ag_update_board`
- **Purpose**: Synchronizing shared task progress, active subtasks, and architecture notes in `.ag-claude/blackboard.md`.

---

## 💻 Environment & Setup (Windows & VS Code)

### Prerequisites
- **Node.js** v20+ (Node 24 recommended)
- **Claude Code** (`claude` CLI and/or VS Code Claude Code extension)
- **Google Antigravity CLI** (`agy` in `~/.gemini/bin/agy.exe` or PATH)

### Quickstart
1. Clone or navigate to the repository:
   ```bash
   git clone https://github.com/your-username/Antigravity-ClaudeCode-Orchestrator.git
   cd Antigravity-ClaudeCode-Orchestrator
   ```
2. Install dependencies & build:
   ```bash
   npm install
   npm run build
   ```
3. Initialize the orchestrator in any target project:
   ```bash
   npx ag-claude init
   ```
   *This automatically generates `.mcp.json` (auto-loaded by VS Code), creates `.ag-claude/` for shared state, and injects collaboration heuristics into `CLAUDE.md`.*

4. **Open VS Code**:
   - Claude Code automatically connects to `ag-claude` via MCP.
   - Simply chat with Claude Code as normal—Claude will autonomously collaborate with Antigravity whenever needed!

---

## 📜 License
MIT License. Open-source and community-driven.
