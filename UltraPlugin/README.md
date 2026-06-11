# UltraPlugin

Master orchestrator for Claude Code. Activates domain routing at session start — each plugin handles its own area, no overlaps.

---

## What It Does

On every session start, UltraPlugin injects routing rules that tell Claude which skill to use for each type of task:

- **Caveman** — terse communication style (~75% token savings)
- **Cavecrew** — code search (`cavecrew-investigator`), surgical edits (`cavecrew-builder`), diff review (`cavecrew-reviewer`)
- **Context-mode** — large files (>500 lines), API calls, logs, browser output
- **Token Optimizer** — full context audit and fixes; coaching for architecture
- **Obsidian** — vault operations, markdown, bases, canvas, URL extraction

---

## Requirements

UltraPlugin is an orchestrator — it requires these plugins to be installed first:

| Plugin | Marketplace Source |
|---|---|
| `caveman` | `https://github.com/JuliusBrussee/caveman.git` |
| `context-mode` | `mksglu/context-mode` (GitHub) |
| `token-optimizer` | `https://github.com/alexgreensh/token-optimizer.git` |
| `obsidian-skills` | `https://github.com/kepano/obsidian-skills.git` |

Install each via **Claude Code → Settings → Plugins → Add Marketplace**, then install and enable the plugin from each marketplace.

---

## Installation

### Option A: GitHub (recommended for sharing)

1. Push this repository to GitHub (e.g. `github.com/yourname/ultraplugin`)
2. In Claude Code: **Settings → Plugins → Add Marketplace**
3. Enter: `https://github.com/pedrovinchi1/ultraplugin.git`
4. Install and enable `ultraplugin` from the new marketplace
5. Restart Claude Code

### Option B: Manual (local install)

1. Copy the UltraPlugin folder anywhere on your machine
2. Edit `C:\Users\<you>\.claude\plugins\known_marketplaces.json` — add:
   ```json
   "ultraplugin": {
     "source": {
       "source": "local",
       "path": "C:\\path\\to\\UltraPlugin"
     },
     "installLocation": "C:\\Users\\<you>\\.claude\\plugins\\marketplaces\\ultraplugin",
     "lastUpdated": "2026-01-01T00:00:00.000Z"
   }
   ```
3. Copy the UltraPlugin folder to `C:\Users\<you>\.claude\plugins\marketplaces\ultraplugin\`
4. Copy the UltraPlugin folder to `C:\Users\<you>\.claude\plugins\cache\ultraplugin\ultraplugin\1.0.0\`
5. Edit `C:\Users\<you>\.claude\plugins\installed_plugins.json` — add inside `"plugins"`:
   ```json
   "ultraplugin@ultraplugin": [
     {
       "scope": "user",
       "installPath": "C:\\Users\\<you>\\.claude\\plugins\\cache\\ultraplugin\\ultraplugin\\1.0.0",
       "version": "1.0.0",
       "installedAt": "2026-01-01T00:00:00.000Z",
       "lastUpdated": "2026-01-01T00:00:00.000Z"
     }
   ]
   ```
   And inside `"enabledPlugins"`:
   ```json
   "ultraplugin@ultraplugin": true
   ```
6. Restart Claude Code

---

## Usage

After installation, UltraPlugin activates automatically on every session start. You will see in the session context:

```
ULTRAPLUGIN ACTIVE — Domain routing enforced: ...
```

### Slash Commands

| Command | Description |
|---|---|
| `/ultraplugin` | Show full domain routing table and anti-collision rules |
| `/ultra` | Quick status — which skills are active, what handles what |

---

## Domain Routing Reference

| Task | Skill to use |
|---|---|
| Find where a function/symbol is defined | `cavecrew-investigator` |
| Edit 1–2 files surgically | `cavecrew-builder` |
| Review a diff (findings for Claude) | `cavecrew-reviewer` |
| Write PR review comments (for humans) | `caveman-review` |
| Analyze file >500 lines / call API / read logs | `context-mode` |
| Index project files for repeated search | `ctx-index` |
| Compress a `.md` file on disk | `caveman-compress` |
| Full token audit + implement fixes | `token-optimizer` |
| Coaching on token-efficient architecture | `token-coach` |
| Quick session health check | `token-optimizer:quick` |
| Obsidian vault operations | `obsidian-cli` |
| Write/edit Obsidian markdown notes | `obsidian-markdown` |
| Create Obsidian `.base` database views | `obsidian-bases` |
| Create Obsidian `.canvas` files | `json-canvas` |
| Extract clean content from a web URL | `defuddle` |
| Git commit message | `caveman-commit` |

---

## Plugin Structure

```
UltraPlugin/
├── .claude-plugin/
│   ├── plugin.json          # Plugin manifest
│   └── marketplace.json     # Marketplace registration
├── skills/
│   └── ultraplugin/
│       └── SKILL.md         # Full routing table (loaded as /ultraplugin skill)
├── hooks/
│   ├── hooks.json           # Registers SessionStart hook
│   └── session-start.js     # Injects routing rules at session start
└── commands/
    └── ultra.md             # /ultra slash command
```

---

## Updating

To update UltraPlugin after editing files:

1. Edit files in the UltraPlugin source folder
2. Copy updated files to `cache\ultraplugin\ultraplugin\1.0.0\` and `marketplaces\ultraplugin\`
3. Restart Claude Code
