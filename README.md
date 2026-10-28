# Antigravity–Claude Code Orchestrator

> **Status (2026-10-28):** concept reviewed; there is no code. Claude Code and Google's Antigravity CLI (`agy`) can work together, but most of the benefits this project first claimed do not hold. The full review is in [conceptualisation.md](conceptualisation.md).

## What this repository is

This repository began as the design of an MCP bridge that would let Claude Code call `agy` as a peer. A review on 2026-10-28 checked that design against current documentation, earlier projects and a real installation. The repository now holds that review and a guide to when pairing the two tools is worth it. It contains no software to install.

## The short answer

Pairing pays off in three cases, with Claude Code as the lead:

- **A second opinion on high-stakes diffs.** Gemini 3.8 Flash comes from a different model family, but frontier models share many errors, so test whether it finds bugs that Claude misses.
- **Large, self-contained tasks that have tests, when you keep hitting Claude's usage limits.** `agy` draws on your Google AI plan's quota instead of Claude's.
- **Audio, video and Google Cloud work.** Gemini accepts audio and video, which Claude Code does not.

Pairing does not pay off in these cases:

- **Offloading context.** Claude Code and Gemini 3.8 Flash both have a context window of about one million tokens.
- **Small tasks, or delegating file by file.** Writing the brief and checking the result cost more than the delegation saves.
- **Two agents editing the same working tree at once.** In headless mode, `agy` writes files without asking.

## Use these instead of building

| Project | What it does | Notes |
| --- | --- | --- |
| [yuting0624/antigravity-for-claude-code](https://github.com/yuting0624/antigravity-for-claude-code) | A Claude Code plugin that sends delegation, hand-off, review, research and media tasks to `agy`, with background jobs | Recommended on macOS, Linux and WSL. On native Windows, headless `agy` runs have been reported to hang. |
| [openai/codex-plugin-cc](https://github.com/openai/codex-plugin-cc) | OpenAI's official plugin that follows the same pattern for Codex | For Codex, not Gemini |
| [PAL MCP](https://github.com/beehiveinnovations/zen-mcp-server) | An MCP server whose `clink` tool links CLIs such as Gemini CLI, Codex CLI and Claude Code | Not specific to `agy` |

## Before you send code to agy

- Code and prompts you send to `agy` go to Google and are handled under your Google account's terms.
- In headless mode, `agy` reads and writes files in its workspace without asking. Run any task that edits code in a separate git worktree.

## Repository contents

- [conceptualisation.md](conceptualisation.md): the review, including the claim audit, what `agy` adds to Claude Code, and four checks to test the benefits in your own work.
- [walkthrough.md](walkthrough.md): a short record of the review and the decision.

## License

MIT.
