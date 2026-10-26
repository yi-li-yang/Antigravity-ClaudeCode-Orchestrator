# Walkthrough: Antigravity-ClaudeCode Orchestrator Conceptualisation

We have successfully established the foundational design, theoretical grounding, engine comparative analysis, and user-facing documentation for the **Antigravity-ClaudeCode Orchestrator**.

---

## Deliverables in Repository

| Deliverable | Location | Description |
| :--- | :--- | :--- |
| **Project Conceptualisation** | [conceptualisation.md](conceptualisation.md) | Comprehensive technical design document covering the theoretical foundation, engine architecture comparisons, main vs. subagent model routing, and autonomous collaboration heuristics. |
| **User-Facing Documentation** | [README.md](README.md) | High-level developer documentation outlining motivation, complementary superpowers, comparison with prior community plugins, MCP tool specifications, and setup instructions. |
| **Walkthrough** | [walkthrough.md](walkthrough.md) | Permanent walkthrough record summarizing key conceptual pillars and decisions. |

---

## Key Conceptual Pillars Established

### 1. Distinct Engine Superpowers
- **Claude Code (`claude`)**: Powered by **Claude 3.7 Sonnet** with hybrid reasoning and extended thinking. Excels at precision diff generation, surgical localized refactoring, and interactive dialogue in the VS Code UI.
- **Antigravity (`agy`)**: Powered by **Gemini 2.5 / 3.0 Pro & Flash**. Features an unprecedented **1M to 2M token context window**, native **Jetski Language Server AST graph / symbol indexing**, multi-modal ingestion (video, audio, mockups), and first-class artifacts.
- **The Synergy**: Claude handles high-density reasoning without blowing its 200k limit by delegating repo-wide exploration, 500kb+ log digestion, and independent cross-model security audits to Antigravity.

### 2. Model Routing Across the Ecosystem
- **Main Agents**: Claude 3.7 Sonnet (Claude Code) and Gemini 3.0/2.5 Pro (Antigravity).
- **Subagents**:
  - Claude subagents (`.claude/agents/*.md`) can select `haiku` (fast scan), `sonnet` (standard code), `opus` (high-stakes architecture), or `inherit`.
  - Antigravity subagents (`research`, `self`, etc.) select `flash_lite`, `flash`, `pro`, or `inherit`.
- **Cross-Tool Synergy**: Invocations via the orchestrator bridge query Gemini Pro/Flash from Claude 3.7 Sonnet, delivering genuine cross-model verification.

### 3. Comparison with Prior Work (`yuting0624/antigravity-for-claude-code`)
- Solves the critical **Windows compatibility limitation** (eliminates reliance on Unix PTYs by using native stdio JSON-RPC).
- Evolves from manual slash-commands to **standard Model Context Protocol (MCP)** via `.mcp.json` automatically loaded by VS Code.
- Adds a **shared blackboard architecture** (`.ag-claude/blackboard.md` + `state.json`) for collaborative task memory.
- Enables **autonomous heuristic decision-making** so Claude Code decides on its own when to leverage Antigravity.

