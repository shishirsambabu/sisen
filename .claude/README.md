# Claude Code tooling

This folder contains the Claude Code configuration for the project: project settings, custom slash
commands, and specialized subagents.

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

Open the repo in Claude Code and run `/plan-phase 0`.
