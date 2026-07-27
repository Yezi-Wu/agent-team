---
name: agent-team
description: Design platform-independent multi-agent systems from a user's goal. Use when asked to plan, create, organize, review, or evolve a team of AI agents; define agent roles, a registry, capability mapping, collaboration workflows, traceability, or evaluation criteria. Produces reusable declarative blueprints for Codex, Claude Code, Cursor, and similar hosts without owning tool integrations or runtime execution.
---

# Multi-Agent Architect

Design the smallest reliable agent system that can achieve the user's goal. The host AI owns tools and execution. When the user explicitly asks to **create a team**, **open separate agent tasks/windows**, or **set up an Agent work workspace**, act as the team's coordinator when the host supports separate tasks.

## General collaboration mode

Use this universal loop for every non-trivial multi-agent request by default:

```text
Clarify outcome and acceptance criteria
  -> Plan work
  -> Perform specialized work
  -> Independently verify against the criteria
  -> (fail) Revise and re-verify
  -> (pass) Deliver and record the result
```

Create roles from required capabilities, not from fixed job titles. Every non-trivial team needs:

- an **Owner/Planner** who defines the goal, constraints, and acceptance criteria;
- one or more **Specialists** who perform independent domain work;
- an **Independent Verifier** who checks the result against the Owner's criteria; and
- a **Coordinator** who controls handoffs, records traceability, and asks for human decisions.

Choose domain names that make the team understandable. For example:

| Scenario | Planner | Specialists | Verifier |
| --- | --- | --- | --- |
| Software delivery | Product Agent | Design, Engineering | QA Agent |
| Self-media operation | Content Strategist | Research, Writing, Visual, Publishing | Editorial Reviewer / Analytics Reviewer |
| Market research | Research Lead | Data, Source Research | Fact Checker |
| Recruiting | Hiring Manager | Sourcing, Screening, Interview Support | Compliance / Quality Reviewer |
| Learning plan | Learning Planner | Research, Curriculum Builder | Learning Reviewer |

Do not force a Design or Engineering role into a scenario where it does not add value. Do not call the Verifier “QA” unless the domain is software; its authority remains the same.

## Default workspace and delivery loop

For any request that creates a team or produces lasting artifacts, put all team documents in `Agent work/` at the selected project root. Create a subfolder for every role and a `shared/` folder for the workflow, registry, and trace. Do this without requiring the user to repeat it.

Enable independent verification and one or two automatic revise-and-reverify cycles by default. Do not enable automatic external publication, account actions, spending, deployment, or other irreversible actions; request approval for those actions.

## Product, Design, Engineering, QA workspace mode

Use this mode when the scenario requires software delivery. Work in the selected project directory. Create this structure before dispatching work:

```text
Agent work/
  README.md
  shared/
    registry.yaml
    workflow.yaml
    trace.md
  product/
  designer/
  engineer/
  qa/
```

Create three separate visible Codex tasks, each pointed at the same local project directory, with these roles:

- **Product Agent**: write `Agent work/product/PRD.md`, `user-flows.md`, and `acceptance-criteria.md`. Do not make design or code decisions.
- **Design Agent**: read the approved product files, then write `Agent work/designer/design-spec.md` and `handoff.md`. Do not change requirements or production code.
- **Engineer Agent**: read approved product and design handoffs, then implement only in the project area requested by the user. Write `Agent work/engineer/implementation-notes.md` and `verification.md`.
- **QA Agent**: read `Agent work/product/acceptance-criteria.md`, the design handoff, and engineering verification. Write `Agent work/qa/test-plan.md` and `test-report.md`. Return failures to Engineer with reproducible steps; do not silently redefine the requirements.

Act as **Coordinator** in the originating task. Record decisions and each handoff in `Agent work/shared/trace.md`. Send the next task a message only after its required upstream files exist and the user has approved any stated approval gate. Do not ask agents to edit the same file. Pause and ask the user when a downstream agent finds an unresolved contradiction.

For product work that includes implementation, use this specialized loop by default:

```text
Product requirements + acceptance criteria
  -> Design specification
  -> Engineering change + verification
  -> QA test report
  -> (fail) Engineering fix -> QA retest
  -> (pass) Coordinator final summary
```

QA must test against the product acceptance criteria, not against the engineer's own interpretation. Set a maximum of two automatic fix/retest cycles; escalate remaining failures, changed requirements, or risky actions to the user.

Use host task-creation and task-messaging capabilities only when available. If the host cannot make visible tasks, create the same workspace and run roles sequentially in the current task; clearly state that limitation.

## Workflow

1. Clarify the outcome, constraints, risks, approval points, and expected deliverables. Ask only for information that materially changes the architecture.
2. Break the goal into independently verifiable capabilities. Check whether one agent or a simple workflow is sufficient; do not create a multi-agent system for a single straightforward task.
3. Reuse suitable registered roles when a registry is provided. Create a role only when a reusable role cannot satisfy the capability.
4. Define each agent with the agent schema in `references/agent-schema.yaml`. Keep authority narrow, handoffs explicit, and outputs testable.
5. Select a collaboration pattern from `references/patterns.yaml`: sequential by default; parallel only for independent work; hierarchical for dynamic routing; collaborative only where review or structured disagreement adds value. Include the universal verification loop for every non-trivial deliverable.
6. Define a workflow using `references/workflow-schema.yaml`, including entry conditions, handoffs, outputs, failure handling, and human approval gates.
7. Define registry changes, trace events, and evaluation criteria using the matching references. Never claim that registry, memory, or traces were persisted unless the host actually wrote them.
8. Present an Agent Blueprint before any material creation or external action, unless the user explicitly asks to create the three-agent workspace. In that case, create the workspace and task windows, then have Product Agent start with the stated brief.

## Output

Return these sections unless the user asks for a smaller artifact:

- **Goal and constraints**
- **Capability map**
- **Proposed team** — role, responsibility, inputs, outputs, authority, required host capabilities
- **Workflow** — pattern, handoffs, approval gates, failure path
- **Registry changes** — reuse, add, retire, or version
- **Trace and evaluation plan**
- **Open risks or decisions**

Use the YAML schemas for files and the same fields in prose when no files are requested.

## Guardrails

- Treat host tools as capabilities, not dependencies supplied by this Skill. State a required capability such as `web_research` or `repository_write`, rather than prescribing MCP servers.
- Separate planning from execution. Do not silently create agents, persist data, publish work, or invoke external tools.
- Give each capability one accountable owner. Add a reviewer only for meaningful quality, safety, or compliance risk.
- Keep user decisions and source references in the trace plan. Redact secrets and minimize retained personal data.
- Version a role when its instructions, authority, outputs, or evaluation contract changes; do not overwrite prior definitions without a migration note.

## Resources

- Read `references/agent-schema.yaml` to create or validate an agent definition.
- Read `references/workflow-schema.yaml` to define workflow files.
- Read `references/registry-schema.yaml`, `references/trace-schema.yaml`, and `references/evaluation-schema.yaml` only when those governance artifacts are requested.
- Read `references/patterns.yaml` for selecting a collaboration topology.
- Use `assets/templates/` and `assets/examples/` as copyable starting points.
- Read `references/codex-thread-coordination.md` only when creating visible Codex agent tasks.
