# Fork ledger

This fork of [HazAT/pi-interactive-subagents](https://github.com/HazAT/pi-interactive-subagents) runs non-blocking Pi subagents next to the [pstack-pi](https://github.com/aaalexliu/pstack-pi) package, in panes Alex can watch and steer. Base: upstream v3.7.2 (c100577). Each change below is one commit, so a rebase on upstream can drop or redo it on its own.

| Change | Why | Files |
| ------ | --- | ----- |
| Force-close finished child panes in cmux | cmux 0.65 refuses `close-surface` while the pane still owns a process. The close failed and the parent lost the child's report. Cleanup now runs in a `finally` and can never replace the result. | `pi-extension/subagents/cmux.ts`, `pi-extension/subagents/index.ts`, `test/test.ts` |
| Rename the tools: `subagent_start`, `subagent_send`, `subagent_stop`; `subagents_list` becomes the `/subagents-list` command | pstack-pi owns the blocking `subagent` tool and the `/subagents` command. | `pi-extension/subagents/index.ts`, `pi-extension/subagents/plan-skill.md`, `agents/planner.md`, `README.md`, `test/` |
| Children get no spawning tools unless their agent says `spawning: true`, and run with `PSTACK_SUBAGENT_DEPTH=1` | Only the parent delegates. The depth hides pstack-pi's `subagent` tool in children. | `pi-extension/subagents/index.ts`, `README.md`, `test/test.ts` |
| `interactive-subagents.json` with `maxConcurrent`, `cmuxLayout`, `agentDirs`; list frontmatter accepts `[a, b]` | One user file for the fork's settings. `agentDirs` lets pstack-pi's agents load; they write `tools` as a YAML flow list. | `pi-extension/subagents/config.ts`, `pi-extension/subagents/index.ts`, `README.md`, `test/test.ts` |
| Cap running children at `maxConcurrent` (default 4) and queue the rest | Bounds load. A queued call returns at once and starts when a slot frees. | `pi-extension/subagents/index.ts`, `test/test.ts` |
| cmux: one `agents: <session>` workspace per parent, 2x2 grid or tabs | Keeps children out of Alex's own panes. | `pi-extension/subagents/cmux.ts`, `pi-extension/subagents/cmux-layout.ts`, `pi-extension/subagents/index.ts`, `README.md`, `test/test.ts` |
| tmux: each child is a window in the parent's session | Full-size panes instead of ever narrower splits. | `pi-extension/subagents/cmux.ts`, `README.md` |
