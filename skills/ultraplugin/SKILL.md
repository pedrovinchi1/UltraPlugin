---
name: ultraplugin
description: >
  Master session orchestrator for all installed component plugins. Shows routing table,
  resolves skill conflicts, and guides domain delegation.
  Trigger: /ultraplugin, "what skill handles X", "show routing", "which plugin for Y",
  "session setup", or when two skills seem to overlap for a task.
version: 1.0.0
---

# UltraPlugin — Session Orchestrator

**Requires installed:** `caveman` · `context-mode` · `token-optimizer` · `obsidian-skills`

---

## Domain Ownership Matrix

One skill per domain. No overlap.

| Task | Owner Skill | Never Route To |
|---|---|---|
| Claude output style (terse) | `caveman` | — (protocol layer, always active) |
| Locate symbol / file / function | `cavecrew-investigator` | Explore, grep, context-mode |
| Edit 1–2 files (surgical) | `cavecrew-builder` | main thread inline edits |
| PR/diff findings (→ main thread) | `cavecrew-reviewer` | caveman-review |
| PR review comments (→ humans) | `caveman-review` | cavecrew-reviewer |
| Files >500L / API / logs / tests | `context-mode` | cavecrew, `Bash cat/curl` |
| Index files for FTS5 search | `ctx-index` | raw ctx_execute reads |
| Compress .md files on disk | `caveman-compress` | caveman (speech only) |
| Full token audit + implement fixes | `token-optimizer` | token-coach |
| Token coaching / arch planning | `token-coach` | token-optimizer |
| Quick token health check | `token-optimizer:quick` | — |
| Obsidian vault file operations | `obsidian-cli` | generic fs tools |
| Write/edit Obsidian .md notes | `obsidian-markdown` | generic markdown |
| Create/edit .base database views | `obsidian-bases` | — |
| Create/edit .canvas files | `json-canvas` | — |
| Extract clean content from web URL | `defuddle` | WebFetch (web pages) |
| Git commit message authoring | `caveman-commit` | manual write |

---

## Anti-Collision Rules

### caveman vs caveman-compress
- `caveman` = controls Claude OUTPUT style. Active every response.
- `caveman-compress` = rewrites `.md` FILES on disk to save input tokens.
- **Rule:** terse speech → caveman. Compress a file → caveman-compress.

### cavecrew-reviewer vs caveman-review
- `cavecrew-reviewer` = subagent, returns `path:line: 🔴 severity: problem. fix.` table to main thread.
- `caveman-review` = outputs PR comment text ready to paste into GitHub/GitLab.
- **Rule:** "findings for me to process" → cavecrew-reviewer. "comments for the PR" → caveman-review.

### context-mode vs cavecrew-investigator
- `cavecrew-investigator` → files <500 lines, code navigation, symbol/grep search.
- `context-mode` → files >500 lines, runtime data, APIs, logs, browser snapshots, DB dumps.
- **Rule:** never use context-mode for routine variable/function lookups.

### token-optimizer vs token-coach
- `token-optimizer` = runs audit scripts, measures waste, implements fixes. Action-oriented.
- `token-coach` = coaching conversation, architecture advice, pattern review. Advice-oriented.
- **Rule:** planning phase → token-coach. Fixing phase → token-optimizer.

---

## Delegation Chain Patterns

**Locate → Edit → Verify** (most common code task):
```
cavecrew-investigator → cavecrew-builder → cavecrew-reviewer
```

**Parallel scout** (broad investigation):
```
3× cavecrew-investigator in parallel (defs / callers / tests) → aggregate main thread
```

**Token emergency** (context filling up):
```
token-optimizer:quick → token-optimizer (full) → /compact if needed
```

**Obsidian workflow**:
```
obsidian-cli (read/create) → obsidian-markdown (content) → obsidian-bases (views)
```

**Large file analysis** (not editing):
```
context-mode ctx_execute_file → ctx_search (repeated queries) → report findings
```

**Browser / Playwright**:
```
browser_snapshot(filename) → ctx_index(path) → ctx_search(queries)
```
Never: `browser_snapshot()` without filename → floods context.

---

## Routing by Intent

| User says... | Route to |
|---|---|
| "find where X is defined" | cavecrew-investigator |
| "fix this function" (1 file) | cavecrew-builder |
| "review the PR" | cavecrew-reviewer or caveman-review |
| "analyze this log file" | context-mode ctx_execute_file |
| "context is getting full" | token-optimizer |
| "plan my agent architecture" | token-coach |
| "open my vault / create a note" | obsidian-cli |
| "compress CLAUDE.md" | caveman-compress |
| "write commit message" | caveman-commit |
| "read this article" (URL) | defuddle |

---

## Session Status

On session start, UltraPlugin activates via hook:

- Caveman mode (ultra) — terse output, ~75% token savings on prose
- Cavecrew routing — investigator/builder/reviewer delegation rules
- Context-mode boundaries — large data routed to sandbox (not context)
- Token health monitoring — optimizer and coach available
- Obsidian integration — all vault skills ready

All component skills remain directly invocable via their own `/skillname` commands.
