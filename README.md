# Agent Team Skill

A portable architecture Skill for designing governed multi-agent systems. It does not supply a runtime, tool connector, or MCP server: Codex, Claude Code, Cursor, or another host executes the resulting blueprint using its own capabilities. It creates roles from a scenario's required capabilities rather than imposing a fixed set of job titles.

## Use

Place this folder in the host's discoverable Skill directory, then invoke it with a request such as:

```text
Use $agent-team to create a reusable research-and-reporting team for competitor analysis.
```

The Skill creates a declarative blueprint: agent roles, capability mapping, workflow, registry changes, trace plan, and evaluation criteria. Its universal closed loop is Plan → specialist work → independent verification → revision/re-verification → delivery. For software implementation, the specialized form is Product → Design → Engineering → QA → fix/retest → final summary. Use the copyable templates under `assets/` when you want generated YAML files.

## Workspace layout

`skills/` holds reusable Skills, `shared-assets/` holds reusable non-Skill resources, and `projects/` is for future project-specific work. This repository contains no runtime or MCP integration by design.
