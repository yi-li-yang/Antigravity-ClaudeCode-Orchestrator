# Walkthrough: concept review of 2026-10-28

## Timeline

- **2026-10-26:** the first conceptualisation and README proposed an MCP bridge that would let Claude Code use Antigravity (`agy`) as a peer (commit `b97a64e`).
- **2026-10-28:** the review checked every claim against official documentation, earlier projects and a local installation (Windows 11, `agy` 1.4.9, Claude Code 2.1.310).

## Findings

- **Feasible.** `agy` has a documented headless mode with JSON output, and `agy models` worked when run from Claude Code's Bash tool on the test machine. Whether `agy -p` hangs on native Windows is still untested.
- **Not beneficial as designed.**
  - Claude Code now has a 1M-token context by default, the same size as Gemini 3.8 Flash, so there is no context gap.
  - The test account offers no Gemini Pro model.
  - Measured delegation saved money only on large tasks.
  - The design's claimed advantages over the yuting0624 plugin were mistaken.
- **Worth pairing in three cases, with Claude Code as the lead:**
  - a second opinion on high-stakes diffs;
  - large, self-contained tasks with tests when Claude's limits get in the way;
  - audio, video and Google Cloud work.

  The yuting0624 plugin already covers these on macOS, Linux and WSL.

## Decision

Revise the documents only. No code will be written. Section 6 of [conceptualisation.md](conceptualisation.md) lists four checks that would test the benefits in practice: a Windows smoke test, a review test, a limit diary and a hand-off trial.

## Changes

- [conceptualisation.md](conceptualisation.md) was rewritten as the review.
- [README.md](README.md) was rewritten to match the review. Its install steps were removed, because the `npx ag-claude init` command never existed.
- This file replaced the earlier walkthrough.
