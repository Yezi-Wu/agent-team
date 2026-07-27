# Codex thread coordination

Use this procedure after the user requests a team or a multi-agent system. The `Agent work/` workspace and verification loop are defaults; do not require the user to restate them.

1. Treat the current project task that invoked `$agent-team` as the mandatory Coordinator. Do not create a separate Coordinator task. Ensure the user supplied a project directory. Create `Agent work/` there with `dashboard.md`, `deliverables/index.md`, `deliverables/final-summary.md`, the role folders, and `shared/`.
2. Create only Product, Design, Engineering, and QA tasks in the same local project directory when those roles are needed. Their prompts must use the concise handoff structure and name only their assigned folder, files, boundaries, and upstream files to read.
3. Start Product immediately. Start Design, Engineering, and QA in waiting state: they must not invent missing inputs.
4. When Product finishes, record a coordinator trace entry and request user approval if requirements affect scope, budget, dates, or material commitments. Then message Design with exact product-file paths.
5. When Design finishes, record the handoff. Request user approval for material design direction if required. Then message Engineering with exact product and design paths.
6. When Engineering finishes, ask it to record implementation evidence, not a quality self-review. Message QA with exact paths to product acceptance criteria, design handoff, and engineering evidence.
7. When QA reports failures, append them to the trace and send only the failed criteria and reproducible steps to Engineering. After Engineering records a fix, send QA one retest request. Escalate any remaining failure to the user.
8. After every dispatch, result, decision, or blocker, update `dashboard.md`. When QA passes, add the approved user-facing artifact to `deliverables/index.md`, update `deliverables/final-summary.md`, record the result, and return a final coordinator summary. Do not claim deployment, testing, or external publication without evidence.

Suggested one-sentence invocation:

`Use $agent-team to create Product, Design, Engineering, and QA agent tasks in the current project. My goal: <goal>.`
