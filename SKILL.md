---
name: agent-team
description: Design platform-independent multi-agent systems from a user's goal. Use when asked to plan, create, organize, review, or evolve a team of AI agents; define agent roles, a registry, capability mapping, collaboration workflows, traceability, or evaluation criteria. Produces reusable declarative blueprints for Codex, Claude Code, Cursor, and similar hosts without owning tool integrations or runtime execution.
---

# Multi-Agent Architect

Design the smallest reliable agent system that can achieve the user's goal. The host AI owns tools and execution. When the user calls this Skill in a project task, the current/originating task becomes the mandatory Coordinator. Do not create a separate Coordinator task. When the user explicitly asks to **create a team**, **open separate agent tasks/windows**, or **set up an Agent work workspace**, create only the needed Specialist and Verifier tasks.

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

Create roles from required capabilities, not from fixed job titles. Every non-trivial team always needs a **Coordinator**. The Coordinator owns the goal, constraints, acceptance criteria, state, scheduling, and trace. Add Specialists only for required domain work and add one Independent Verifier only when the deliverable needs verification.

The Coordinator is the only role that dispatches or redirects work. Specialists and Verifiers do not directly assign work to one another. They return a structured handoff to the Coordinator, which records it and sends the next minimal task.

Do not use free-form Agent-to-Agent chat as the source of truth. The Coordinator communicates through versioned task packets and recorded results. The priority for resolving a conflict is: explicit user confirmation, latest confirmed version, accepted acceptance criteria, current task packet, then no assumption. If none resolves the conflict, stop with `Status: needs-decision`.

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

Create these two project-wide views in every `Agent work/` workspace:

- `dashboard.md`: the Coordinator's single status view. Update it after every dispatch, result, decision, verifier outcome, and blocker. Include each role, current task, status, latest artifact path, verifier result, blocker, and next action.
- `deliverables/`: the user's single output exit. Include `index.md` with links to every approved final artifact and `final-summary.md` when the workflow completes. Put only user-facing, approved outputs here; keep working notes and drafts in role folders.

Each Agent result must name its exact artifact path. The Coordinator adds that path to `dashboard.md`. Only after the Verifier passes an artifact, the Coordinator adds it to `deliverables/index.md` or creates the needed final user-facing copy there. Never list a draft as a deliverable.

## Thin main-thread reporting

Keep the Coordinator's chat context small. Child Agents write full artifacts to their assigned folders and return only a compact handoff to the Coordinator. Do not paste a child's full response or full artifact into the Coordinator chat by default.

For each completed or approved output, show the user one short delivery card in the Coordinator chat:

```text
Delivered: <artifact name>
Status: <draft | approved | blocked>
Summary: <one or two factual sentences>
Open: <relative path under Agent work/deliverables/ or role folder>
```

The delivery card is the main-chat view; the linked file is the full source of truth. Read or display the full artifact in the Coordinator chat only when the user explicitly asks to open it, revise it, or make a decision that requires its contents. When a child output is not yet approved, link its role-folder path; when approved, link the `deliverables/` path.

## Minimum-context access

Do not automatically read, summarize, or enumerate the full project directory. The current task is the Coordinator, but it also reads project files only when needed for routing or a user decision.

Create `Agent work/shared/context/` with:

- `project-map.md`: a short, Coordinator-maintained map of only the files and modules discovered as relevant.
- `task-packets/<role>.md`: one compact packet per dispatched task containing the task, exact allowed read paths, exact required outputs, and acceptance criteria.

By default, a Specialist or Verifier reads only the paths named in its task packet. It must not scan the repository, read unrelated files, or load whole documents “just in case.” If essential context is missing, return `Status: needs-context` with the exact path or fact needed and the reason; the Coordinator decides whether to provide it. Do not include large file contents in task messages when a path is sufficient.

This is an instruction-level access boundary, not a security boundary: hosts may still technically grant every task access to the same project directory. Use separate worktrees or host-level sandboxing when true filesystem isolation is required.

## Confirmed versions and retention

Treat a version as a user-confirmed project state, never as an automatic checkpoint. Create `Agent work/shared/versions/` with `registry.yaml`, `CHANGELOG.md`, and `cleanup-candidates.md` for every project that produces lasting artifacts.

When the user explicitly says to confirm, save, or release a version:

1. Freeze the approved deliverable list in `deliverables/index.md`.
2. Record version ID, timestamp, user confirmation, scope, approved artifact paths, and rollback reference in `shared/versions/registry.yaml` and `CHANGELOG.md`.
3. If the project is a Git repository, create a dedicated commit and tag for the confirmed state. If it is not, create a dated snapshot of the approved deliverables and the exact changed project files in `shared/versions/snapshots/`.
4. Update `dashboard.md` with the active confirmed version.

On a rollback request, first show the target version, affected files, and current version. Roll back only after explicit user confirmation. Before rollback, create a new safety checkpoint of the current state. Use Git restoration when a Git rollback reference exists; otherwise restore the recorded snapshot. Record the rollback as a new confirmed version event; never erase version history.

After a confirmed version, automatically delete only clearly disposable files created under `Agent work/.tmp/`. Do not automatically delete drafts, task packets, traces, deliverables, snapshots, or project files. List other obsolete documents in `shared/versions/cleanup-candidates.md` with path, reason, and proposed action. Archive or delete those candidates only after the user confirms the cleanup plan.

Do not require Specialists to self-review. A Specialist supplies the requested artifact and concrete execution evidence only. The Independent Verifier performs the quality and acceptance check. When it fails, the Coordinator sends the specific failed criteria to the responsible Specialist, then schedules one re-verification. Escalate repeated failures or changed criteria to the user. Do not enable automatic external publication, account actions, spending, deployment, or other irreversible actions; request approval for those actions.

## Concise handoffs

Every Coordinator-to-Agent task and every Agent-to-Coordinator result must use this compact structure:

```text
Handoff-ID: <unique ID>
Task: <one verifiable action>
Inputs: <exact artifact paths or supplied facts>
Input versions: <path@version or path@timestamp>
Output: <named artifact or result>
Criteria: <objective pass conditions>
Constraints: <scope, format, or authority limits>
Status: <done | blocked | needs-decision>
Evidence: <paths, test output, sources, or "none">
```

Use exact names, artifact paths, dates, and supplied facts. Do not use vague directives such as “make it good,” “handle the rest,” “use best practice,” or “complete everything.” Do not invent facts, results, sources, approvals, or completed actions. Mark missing information as `needs-decision` or `blocked`.

Before a dependent Agent starts work, it sends a compact receipt to the Coordinator:

```text
Received: <Handoff-ID>
Understood task: <one sentence>
Inputs available: <paths>
Expected output: <path>
Missing or conflicting information: <none | exact item>
Status: <accepted | needs-decision>
```

The Coordinator checks that referenced files exist, input versions match, outputs do not conflict with another Agent's ownership, and criteria are executable. Do not dispatch when the receipt reports missing or conflicting information. Record the receipt and any decision in `trace.md`; do not repeat full artifacts in messages.

## Product, Design, Engineering, QA workspace mode

Use this mode when the scenario requires software delivery. Work in the selected project directory. Create this structure before dispatching work:

```text
Agent work/
  README.md
  dashboard.md
  deliverables/
    index.md
    final-summary.md
  shared/
    versions/
      registry.yaml
      CHANGELOG.md
      cleanup-candidates.md
      snapshots/
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
- **Engineer Agent**: read approved product and design handoffs, then implement only in the project area requested by the user. Write `Agent work/engineer/implementation-notes.md` and `evidence.md`; do not perform a separate acceptance review.
- **QA Agent**: read `Agent work/product/acceptance-criteria.md`, the design handoff, and engineering evidence. Write `Agent work/qa/test-report.md`. Report only failed criteria with reproducible steps; do not silently redefine the requirements.

Act as **Coordinator** in the originating task. Do not create another Coordinator window. Record every task, result, decision, and handoff in `Agent work/shared/trace.md`; maintain `registry.yaml`, `workflow.yaml`, and the project `dashboard.md`; maintain `deliverables/index.md` as the only approved-output list; and send every Agent message. Send the next task only after its required upstream files exist and the user has approved any stated approval gate. Do not ask agents to edit the same file. Pause and ask the user when a downstream agent finds an unresolved contradiction.

For product work that includes implementation, use this specialized loop by default:

```text
Product requirements + acceptance criteria
  -> Design specification
  -> Engineering change + evidence
  -> QA test report
  -> (fail) Engineering fix -> QA retest
  -> (pass) Coordinator final summary
```

QA must test against the product acceptance criteria, not against the engineer's own interpretation. QA is the only acceptance-check role. After one failed QA report, the Coordinator sends the failed criteria to Engineering and schedules one QA retest; escalate remaining failures, changed requirements, or risky actions to the user.

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
