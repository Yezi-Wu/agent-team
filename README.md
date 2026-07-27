# Agent Team

[中文说明](README.zh-CN.md) | [English](README.md)

**Agent Team** is a portable Skill for designing and coordinating reliable multi-agent workflows. Give it a goal, and it creates the smallest useful team, explicit handoffs, a shared workspace, traceability, and an independent review-and-revision loop.

It is an architecture and coordination layer—not an MCP server, a model provider, or a proprietary runtime. Codex, Claude Code, Cursor, or another capable host supplies the model, tools, file access, and task execution.

## What it does

- Turns a goal into a capability map and a purpose-built agent team.
- Creates roles from the scenario instead of forcing fixed job titles.
- Uses a default workflow: **plan → specialist work → independent verification → targeted revision → re-verification → delivery**.
- Keeps one mandatory Coordinator responsible for all scheduling, state, and Agent-to-Agent handoffs.
- Places lasting team artifacts in `Agent work/`, with one folder per role and a shared folder for workflow, registry, and trace records.
- Creates visible role tasks when the host supports separate tasks, then coordinates documented handoffs between them.
- Keeps risky external actions—publishing, spending, deployments, or real account operations—behind human approval.

## When to use it

Use Agent Team when a request has several dependent pieces of work, requires independent review, or benefits from clear ownership. It is useful for software delivery, self-media operations, market research, recruiting, learning plans, and many other multi-step scenarios.

Do not use a multi-agent system for a simple, one-step task. A single focused agent is usually faster and clearer.

## Core model

```text
User goal
  ↓
Coordinator: clarify outcome and acceptance criteria
  ↓
Planner + specialists: perform domain work
  ↓
Independent verifier: evaluate against the criteria
  ↓
Fail → targeted revision → one re-verification
Pass → deliver and record the result
```

The verifier is independent from the specialist that produced the work. Specialists provide requested artifacts and execution evidence; they do not perform a duplicate acceptance review. In software the verifier is usually a QA Agent; in other domains it might be an editorial reviewer, fact checker, compliance reviewer, or learning reviewer.

## Example teams

| Scenario | Planner | Specialists | Independent verifier |
| --- | --- | --- | --- |
| Software delivery | Product Agent | Design, Engineering | QA Agent |
| Self-media account | Content Strategist | Trend Research, Writing, Visual Creation | Editorial / Analytics Reviewer |
| Market research | Research Lead | Source Research, Data Analysis | Fact Checker |
| Recruiting | Hiring Manager | Sourcing, Screening, Interview Support | Compliance Reviewer |
| Learning plan | Learning Planner | Research, Curriculum Builder | Learning Reviewer |

## Quick start in Codex

1. Clone or download this repository.
2. Put the `agent-team` folder in a Codex-discoverable Skill directory. A common location is:

   ```text
   C:\Users\<your-user>\.codex\skills\agent-team
   ```

   You can also keep the source elsewhere and create a directory link to it.
3. Start a new Codex task in the project directory where you want the work to happen.
4. Invoke the Skill. This same task becomes the Coordinator; Agent Team does not create an extra Coordinator window:

   ```text
   Use $agent-team to create a multi-agent system for a self-media account.
   ```

5. State the goal. The Skill chooses appropriate roles, creates the shared `Agent work/` workspace for lasting artifacts, and applies its review-and-revision loop by default.

### Software delivery example

```text
Use $agent-team to build a task-management web app in the current project.
```

The Skill can create a Product, Design, Engineering, and QA workflow:

```text
Product requirements + acceptance criteria
  → Design specification
  → Engineering implementation + verification
  → QA testing against acceptance criteria
  → Failed: Engineering fix → QA retest
  → Passed: coordinator summary
```

### Self-media example

```text
Use $agent-team to create a multi-agent system for a career-advice Xiaohongshu account.
```

A likely team includes a Content Strategist, Trend Researcher, Content Creator, and Editorial Reviewer. External publication always requires your approval.

## Generated workspace

For requests that create a team or lasting artifacts, Agent Team uses this layout at the selected project root:

```text
Agent work/
├── README.md
├── shared/
│   ├── registry.yaml       # Active roles and capabilities
│   ├── workflow.yaml       # Handoffs, approvals, and failure paths
│   └── trace.md            # Decisions and completed handoffs
├── <role-1>/
│   └── ...
├── <role-2>/
│   └── ...
└── <verifier>/
    └── ...
```

For a software team, roles are normally `product/`, `designer/`, `engineer/`, and `qa/`. Other scenarios use names that fit the work.

## Collaboration rules

- Every capability has one accountable owner.
- The Coordinator is the only role that assigns or redirects work.
- Agents hand off concise documented outputs: task, exact inputs, output, criteria, constraints, status, and evidence.
- Specialists do not redefine upstream requirements.
- The verifier tests against the Coordinator's acceptance criteria, not the implementer's interpretation.
- Conflicts, changed requirements, risky actions, and repeated failures are escalated to the user.
- Agents should not edit the same file concurrently.

## Included resources

| Path | Purpose |
| --- | --- |
| `SKILL.md` | Core instructions for an AI host. |
| `agents/openai.yaml` | Codex-facing display metadata. |
| `references/` | Agent, workflow, registry, trace, evaluation, and coordination schemas. |
| `assets/templates/` | Reusable Supervisor, Specialist, Reviewer, and QA role templates. |
| `assets/examples/` | Example workflows for software, product, research, and self-media work. |

## Boundaries

Agent Team does **not** bundle MCP servers, external service connectors, model access, or a background automation platform. Those are capabilities of the host environment.

The current project task becomes the Coordinator. The Skill can create and coordinate only the needed Specialist and Verifier tasks when the host provides those features. If it does not, the same roles and workflow can run sequentially in the current task.

## Contributing

Suggestions and improvements are welcome. When changing the Skill, keep it platform-independent, keep `SKILL.md` concise, and add examples or reference files only when they support repeatable use.

## License

[MIT](LICENSE)
