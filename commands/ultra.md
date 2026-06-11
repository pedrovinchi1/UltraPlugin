---
name: ultra
description: Show UltraPlugin routing table and session status. Use when unsure which skill handles a task, or to verify all component plugins are active.
---

Show UltraPlugin session status. Do the following:

1. Read the domain ownership matrix from the `ultraplugin` skill and present it as a compact table.

2. For each component plugin, indicate if it appears to be installed (check if its skills are available in the current session):
   - caveman → skills: caveman, cavecrew, caveman-compress, caveman-review, caveman-commit
   - context-mode → skills: context-mode, ctx-index, ctx-search, ctx-stats, ctx-doctor
   - token-optimizer → skills: token-optimizer, token-coach, token-dashboard, fleet-auditor
   - obsidian-skills → skills: obsidian-cli, obsidian-markdown, obsidian-bases, json-canvas, defuddle

3. If user asks "what skill handles X" — look up in domain table and answer in one line.

4. Format everything in caveman mode (ultra): fragments OK, drop articles, no filler.
