# Claude Code tooling — rename this folder to `.claude/`

This folder contains the Claude Code configuration for the project: project settings, custom slash
commands, and specialized subagents.

**One step before you use it:** rename `claude-config/` to `.claude/` at the repo root.

```bash
# from the repo root
mv claude-config .claude
```

(It's staged under a normal name because the environment that generated this kit couldn't write to a
`.claude/` path directly. The contents are final — only the folder name needs changing.)

## What's inside

```
.claude/
├── settings.json          # permissions + env defaults for Claude Code
├── commands/              # slash commands: the phase loop
│   ├── plan-phase.md      # /plan-phase N
│   ├── build-phase.md     # /build-phase N
│   ├── verify-phase.md    # /verify-phase N
│   ├── ship-phase.md      # /ship-phase N
│   └── design-review.md   # /design-review
└── agents/                # specialized subagents
    ├── architect.md
    ├── fullstack-builder.md
    ├── test-engineer.md
    ├── security-reviewer.md
    └── design-reviewer.md
```

After renaming, open the repo in Claude Code and run `/plan-phase 0`.
