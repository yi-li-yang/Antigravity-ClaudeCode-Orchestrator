# Antigravity–Claude Code Orchestrator: revised conceptualisation

> **Status:** reviewed on 2026-10-28. This version replaces the conceptualisation of 2026-10-26 (commit `b97a64e`). No code is planned.

## 1. Summary

The question was whether Claude Code and Google's Antigravity CLI (`agy`) can work together in one workspace, and whether pairing them pays off.

- **It can work.** `agy` has a documented headless mode with JSON output. On the test machine, run from Claude Code's Bash tool, it used its saved sign-in and listed its models in 2 seconds. One risk is still open: `agy -p` has been reported to hang on native Windows.
- **It does not pay off as designed.** The design rested on two premises that are out of date. One was that Claude has a much smaller context window than Gemini. The other was that a Gemini Pro model would audit Claude's work. The design also claimed advantages over an earlier project that the earlier project already has.
- **Pairing pays off in three narrow cases, with Claude Code as the lead:**
  - a second opinion on high-stakes diffs;
  - large, self-contained tasks that have tests, when Claude's usage limits get in the way;
  - audio, video or Google Cloud work.

  An existing plugin already covers all three on macOS, Linux and WSL.

Section 6 describes four checks that would show whether these cases pay off in your own work.

## 2. What the first version proposed

The 2026-10-26 version proposed an MCP server, `ag-claude`, loaded from `.mcp.json`. Claude Code would call `agy` through these tools:

- `agy_explore` would send large reading jobs to Gemini's "1M–2M token" context and its "Jetski language-server AST graph".
- `agy_audit` would ask "Gemini Pro" for an independent review.
- `agy_delegate` would hand subtasks to `agy`.
- `ag_read_board` and `ag_update_board` would share notes and task state through a blackboard file in `.ag-claude/`.

The two agents would work as peers in the same workspace and "see each other's actions in real time". Claude would decide by itself when to call `agy`, using thresholds such as "files over 500 lines" or "logs over 500 kB". The design claimed five advantages over [yuting0624/antigravity-for-claude-code][yuting]: MCP instead of slash commands, a peer relationship, Windows support, shared state and autonomous decisions.

## 3. How the claims were checked

- **Official documentation:** Claude Code's pages on model configuration, plugins and MCP; the Antigravity CLI pages on headless mode and its command reference; Google's developer guide for Gemini 3.8 Flash; Anthropic's pricing page.
- **Earlier projects:** the README of the yuting0624 plugin and its issue #6; OpenAI's Codex plugin for Claude Code; PAL MCP.
- **The test machine on 2026-10-28:** Windows 11 with `agy` 1.4.9 (at `~/.gemini/bin/agy.exe`, not on `PATH`), Claude Code 2.1.310 and Node 24.14.1. The commands `agy --version`, `agy --help`, `agy models` and `agy agents` were run from Git Bash through Claude Code's Bash tool. No prompt was sent to any model.

## 4. Claim audit

### 4.1 Claims about the two engines

| Claim in the first version | What is true on 2026-10-28 | Consequence |
| --- | --- | --- |
| Claude Code runs Claude 3.7 Sonnet with a 200k-token context. | Claude Code defaults to Opus 5.5 on Pro, Max, Team, Enterprise and API accounts. Every current Claude model runs with a 1M-token window by default ([model configuration][cc-models]). | There is no context gap to fill. |
| `agy` runs Gemini 2.5/3.0 Pro with a 1M–2M-token context. | On the test account, `agy models` lists Gemini 3.8 Flash (high, medium and low), Claude Sonnet 4.6, Claude Opus 4.6 and GPT-OSS 120B. Gemini 3.8 Flash has a 1,048,576-token window ([developer guide][g38-guide]). No source supports 2M. | No Pro model is available to audit Claude, and both windows are the same size. |
| Gemini's "Jetski language-server AST graph" gives repo-wide symbol tracing. | This review found no public documentation of it. A caller receives only `agy`'s text or JSON answer, not its index. Claude Code can load language servers itself through plugins ([plugin reference][cc-plugins]). | Not an advantage that a bridge could pass on. |
| Gemini brings live Google Search and Vertex AI Search; Claude has only local tools. | Claude Code 2.1.310 has built-in WebSearch and WebFetch tools. | Small gain at most. |
| Gemini takes video, audio and mock-ups as input. | True. Gemini 3.8 Flash accepts text, images, audio and video ([developer guide][g38-guide]). Claude Code reads text, images and PDFs, but not audio or video. | A real gain, but a niche one for coding. |
| Delegating heavy work to `agy` saves cost and quota. | It saves only on large tasks (section 5.3). | Depends on task size. |
| Cross-model review "eliminates single-model confirmation bias". | A reviewer from another model family is less inclined to favour Claude's work, but errors stay correlated. On one leaderboard, when two models were both wrong, they gave the same wrong answer 60% of the time. More accurate models had more correlated errors, even across providers ([Kim et al.][kim]). | A real but modest gain, which must be measured. |

### 4.2 Claims about the earlier project and the design

| Claim in the first version | What is true on 2026-10-28 | Consequence |
| --- | --- | --- |
| The yuting0624 plugin works only through manual slash commands. | It ships three automatic parts ([README][yuting]). A skill tells Claude when to use `agy`. A delegate subagent is picked up by Claude for bulk work. A SessionStart hook injects a cost-aware routing policy. | This advantage is false. |
| The yuting0624 plugin relies on Unix pseudo-terminals and hangs on Windows. | It uses no pseudo-terminal. `agy -p` itself hung when run without a real console, as reported on `agy` 1.0.10. The plugin added a timeout and recommends macOS, Linux or WSL. Its maintainer proposed a ConPTY wrapper as the proper fix ([issue #6][issue6]). | The first version has this backwards: its "native stdio" bridge is the setup that hung. Any bridge that starts `agy` carries this risk. |
| The yuting0624 plugin keeps no state between turns. | It runs background jobs with status, result and cancel commands. `agy` itself resumes conversations with `--continue` and `--conversation <id>` ([headless mode][agy-headless]). | This advantage is false. |
| An MCP server is more native than a plugin. | A Claude Code plugin can contain MCP servers, skills, subagents, hooks and language servers ([plugin reference][cc-plugins]). | Not a real distinction. A plugin is the better package. |
| An "autonomous heuristic engine" decides when to call `agy`. | The "engine" is only tool descriptions and instructions in `CLAUDE.md`, and thresholds such as "500 lines" are arbitrary. The yuting0624 author measured routing every task to `agy` as "a net loss below the break-even" ([README][yuting]). | Call `agy` only on conservative triggers, and measure the result. |
| The two agents work as peers in one workspace and share a blackboard. | In headless mode, `agy` reads and writes workspace files without asking ([headless mode][agy-headless]). If both agents edit at once, Claude Code refuses to edit a file that changed after Claude last read it. A blackboard costs tokens every time it is read, and it goes stale. The design was one-way in any case: Claude calls, and `agy` answers. | Drop both. Run `agy`'s edits in a separate git worktree. |
| yuting0624 is the only earlier project. | OpenAI publishes an official Codex plugin for Claude Code that follows the same pattern ([codex-plugin-cc][codex-cc]). PAL MCP's `clink` tool links Gemini CLI, Codex CLI and Claude Code ([PAL MCP][pal]). | A fair comparison must include them. |

## 5. What `agy` adds to Claude Code

### 5.1 The agent harness: about the same

`agy`'s agent harness closely mirrors Claude Code's. Both offer these features ([agy reference][agy-ref]):

- a headless print mode with JSON output;
- skills and plugins;
- MCP servers and subagents;
- a plan mode and a sandbox;
- rewind and scheduling;
- browser control and plans you can review.

Claude Code has close equivalents for most of these. Pairing the two therefore adds a second model, a second quota and some extra inputs, but not a better agent.

### 5.2 The model, the quota and the inputs

| What `agy` adds | Evidence | Pays off when |
| --- | --- | --- |
| Gemini 3.8 Flash: fast, cheap and strong on standard coding tasks | Google's table puts it at 73.7 on DeepSWE against Opus 5's 74.0, and 89.4 on Terminal-bench 2.1 against 89.1. It writes about 305 output tokens per second, but uses about 30% more output tokens per task ([Vellum][vellum]). | It reviews Claude's work, or does clearly specified work |
| The same model is weak on long, hard agent tasks | Terminal-bench 4.0: 19.1 against Opus 5's 51.8 ([Vellum][vellum]) | Claude stays the lead on hard, long tasks |
| A second model family | Fewer shared blind spots, though errors stay correlated (section 4.1) | A high-stakes diff needs review, and the review test in section 6.2 passes |
| A separate quota pool | Google AI Ultra gives 5× or 20× the quota of Google AI Pro ([Google][g-ultra]). Google publishes no per-plan request counts for the CLI, and users report lockouts they cannot predict ([forum][g-forum]). | You often hit Claude's limits, and the task is large, self-contained and has tests |
| Audio and video input, and Google Cloud tooling | Gemini 3.8 Flash accepts audio and video. The yuting0624 plugin uses `agy` to transcribe media and to debug Cloud Run services. | You have media to analyse, or you work on Google Cloud |

The Vellum figures come from Google's announcement table, and they compare Gemini 3.8 Flash with Opus 5, not Opus 5.5. Google's developer guide reports only Gemini's own scores. Its Terminal-bench 2.1 figure (90.8) differs from the announcement's (89.4).

Prices per million input and output tokens at API rates ([Vellum][vellum], [Anthropic][claude-pricing]):

| Model | Input | Output |
| --- | --- | --- |
| Gemini 3.8 Flash, until 2026-12-31 | $0.75 | $3.75 |
| Gemini 3.8 Flash, from 2027-01-01 | $1.50 | $7.50 |
| Claude Opus 5.5 | $4.00 | $20.00 |
| Claude Sonnet 5.5 | $2.00 | $10.00 |
| Claude Haiku 5.5, prompts up to 100,000 tokens | $0.10 | $0.50 |

On subscription plans, the quota each plan includes matters more than these prices.

### 5.3 Measurements from the earlier project

The yuting0624 author measured delegation on real work. The author ran the tests, the samples are small, and a Claude model judged quality. Treat the numbers as indications, not proof.

- **A large multi-agent build:** Claude plus `agy` used 27% less Claude-side cost than Claude alone at high effort, with equal quality. Gemini's own cost was not counted.
- **Eight real merged pull requests, replayed with their tests (two runs per setting):**
  - Delegating file by file cost 1.33–1.68× a solo Claude run.
  - Handing over the whole task cost 0.58× overall at Gemini's promotional price: 0.43× on large tasks and 1.20× on small ones.
  - At the price actually billed (twice the promotional rate), the overall saving disappeared. Large tasks still cost 0.77×.
  - Code from the hand-off scored 0.6 points lower out of 5.
  - Runs took 3–4× longer.
- **The same author's two-model review tool, on a test fixture with three known bugs:** it found two, with no false alarms. In another case, both models agreed on the same false alarm.

A fast model does not make the pairing fast. Claude writes a brief, `agy` runs its own loop, and then Claude checks the result.

### 5.4 Verdict on pairing

- **Worth it, with Claude Code as the lead:**
  - a second opinion on high-stakes diffs;
  - large, self-contained tasks with tests, when Claude's limits get in the way;
  - audio, video and Google Cloud work.
- **Not worth it:**
  - offloading context;
  - small tasks;
  - delegating file by file;
  - two agents editing one working tree at once.
- **No new orchestrator is needed for the worthwhile cases.** The yuting0624 plugin covers them on macOS, Linux and WSL. Whether it works on native Windows is unproven.

## 6. How to find out for your own work

These checks have not been run. The first takes minutes and the third takes two weeks. The flags are for `agy` 1.4.9, so check `agy --help` on your version first.

### 6.1 Windows smoke test (go or no-go)

Run one headless call in the two places where an integration would start `agy`:

- from Git Bash, which is how the yuting0624 plugin's scripts run;
- from Node, which is how an MCP server would run.

Put `-p` and the prompt last, and close stdin, because the yuting0624 plugin found that `agy` drops its output otherwise.

```bash
timeout 90 ~/.gemini/bin/agy.exe --output-format json --model gemini-3.8-flash-medium -p "Reply with exactly: OK" < /dev/null
echo "exit=$?"
```

```js
// smoke.mjs: run with `node smoke.mjs`
import { spawn } from "node:child_process";
import { homedir } from "node:os";
import { join } from "node:path";

const agy = join(homedir(), ".gemini", "bin", "agy.exe");
const args = ["--output-format", "json", "--model", "gemini-3.8-flash-medium", "-p", "Reply with exactly: OK"];
const child = spawn(agy, args, { stdio: ["ignore", "pipe", "pipe"], windowsHide: true });

let out = "";
child.stdout.on("data", (chunk) => (out += chunk));
const timer = setTimeout(() => {
  console.log("HANG: no exit after 90 s");
  spawn("taskkill", ["/pid", String(child.pid), "/T", "/F"]);
}, 90_000);
child.on("exit", (code) => {
  clearTimeout(timer);
  console.log(`exit=${code}`);
  console.log(out);
});
```

**Pass:** both print a JSON object with `"status": "SUCCESS"` within 90 seconds. **If the Node run hangs,** try again under a pseudo-console (the `node-pty` package uses Windows ConPTY). If that also hangs, use WSL or stop.

### 6.2 Review test

1. Take 10 recent diffs from your own repositories and plant one known bug in each, such as an off-by-one error, a missing null check or swapped arguments.
2. Give each diff and the same review prompt to three reviewers:
   - a Claude review subagent;
   - `agy` in plan mode, for example `agy --mode plan --output-format json -p "Review the uncommitted changes in this repository for bugs. Do not edit any files." < /dev/null`;
   - both together.
3. For each reviewer, count the planted bugs it found, the other real problems it found and its false alarms.
4. Run `git status` after each `agy` run, because it is not yet confirmed that `--mode plan` blocks writes.

**Pass:** Gemini finds planted bugs that Claude missed, with no more than about one false alarm per diff.

### 6.3 Limit diary

For two weeks, note each time a Claude usage limit stops or slows your work. Record the date, the task and how long you waited.

**Pass:** this happens more than once a week. If it happens less often, offloading work to `agy` has little to save.

### 6.4 Hand-off trial

Run this check only if the limit diary passes.

1. Pick 3 real tasks that are large, self-contained and covered by tests.
2. Do each task twice: once with Claude Code alone, and once as a hand-off. In the hand-off, Claude writes a brief, `agy` carries it out in a separate git worktree, and Claude then reviews and fixes the result.
3. Record Claude usage (tokens, or the change in your usage meter), tests passed, wall-clock time and your judgement of the code.

**Pass:** Claude usage drops by at least a third, the tests pass, and you would merge the code. The one-third threshold is a suggestion; set your own before you start.

## 7. If you build it later

First check whether the yuting0624 plugin works on your machine, and build only what it lacks. A new bridge should follow these rules:

- **Package it as a Claude Code plugin,** so that one install carries the skill, the tools and any hooks. An MCP tool suits long calls, because Claude Code moves a call that runs longer than two minutes to the background ([MCP][cc-mcp]).
- **Offer few tools:**
  - a read-only review;
  - a hand-off that runs in a separate git worktree and returns a branch and test results;
  - a tool for media and Google Cloud questions.
- **Start `agy` directly, without a shell.** Put `-p` last, close stdin, ask for JSON output and set a hard timeout that kills the whole process tree. `--print-timeout` defaults to no limit in `agy` 1.4.9.
- **Pass long briefs through a file,** because a Windows command line holds only about 32,000 characters.
- **Call `agy` only on conservative triggers:**
  - you ask for it;
  - a high-stakes diff is about to be committed;
  - a large task has tests.
- **Log each call to an append-only file** (tool, model, duration, tokens, outcome) instead of keeping a blackboard.
- **Never let `agy` and Claude edit the same working tree.**
- **Tell users that code sent to `agy` goes to Google** and is handled under their Google account's terms.

## 8. Open risks and unverified points

- Whether `agy -p` hangs on Windows with `agy` 1.4.9. The smoke test in section 6.1 would settle this.
- The Gemini scores come from Google's announcement table as reported by Vellum, and they compare with Opus 5, not Opus 5.5.
- The yuting0624 measurements were run by the plugin's author on small samples, and a Claude model judged quality. That judge may favour code written by Claude.
- Google publishes no per-plan request limits for the CLI.
- One third-party guide reports that Claude 4.6 and GPT-OSS will leave Antigravity on 2026-11-02 ([guide][layer3]).
- Whether `agy --mode plan` blocks file writes.
- Prices change. Gemini 3.8 Flash doubles in price on 2027-01-01.

## 9. Sources

- [Claude Code: model configuration][cc-models]
- [Claude Code: plugin reference][cc-plugins]
- [Claude Code: MCP][cc-mcp]
- [Anthropic: pricing][claude-pricing]
- [Antigravity CLI: headless mode][agy-headless]
- [Antigravity CLI: command reference][agy-ref]
- [Google Cloud: Gemini 3.8 Flash developer guide][g38-guide]
- [Vellum: Gemini 3.8 Flash benchmarks explained][vellum]
- [yuting0624/antigravity-for-claude-code][yuting]
- [yuting0624/antigravity-for-claude-code, issue #6][issue6]
- [openai/codex-plugin-cc][codex-cc]
- [PAL MCP (formerly Zen MCP)][pal]
- [Kim et al., "Correlated Errors in Large Language Models", ICML 2025][kim]
- [Google One: Google AI Ultra benefits][g-ultra]
- [Google AI developer forum: Antigravity quota report][g-forum]
- [Layer3 Labs: Google Antigravity CLI guide][layer3]

[cc-models]: https://code.claude.com/docs/en/model-config
[cc-plugins]: https://code.claude.com/docs/en/plugins-reference
[cc-mcp]: https://code.claude.com/docs/en/mcp
[claude-pricing]: https://platform.claude.com/docs/en/about-claude/pricing
[agy-headless]: https://antigravity.google/docs/cli/headless/
[agy-ref]: https://antigravity.google/docs/cli/reference/
[g38-guide]: https://docs.cloud.google.com/gemini-enterprise-agent-platform/models/guides/gemini-3-8-flash
[vellum]: https://www.vellum.ai/blog/gemini-3-8-flash-benchmarks-explained
[yuting]: https://github.com/yuting0624/antigravity-for-claude-code
[issue6]: https://github.com/yuting0624/antigravity-for-claude-code/issues/6
[codex-cc]: https://github.com/openai/codex-plugin-cc
[pal]: https://github.com/beehiveinnovations/zen-mcp-server
[kim]: https://arxiv.org/abs/2506.07962
[g-ultra]: https://support.google.com/googleone/answer/16286513?hl=en
[g-forum]: https://discuss.ai.google.dev/t/google-ai-pro-subscription-antigravity-quota-is-not-working-as-it-should-for-pro-users-7-days-lockdown-instead-of-5-hours/128982
[layer3]: https://www.layer3labs.io/guides/google-antigravity-cli
