# Codex thread coordination

Use this procedure after the user requests a team or a multi-agent system. The `Agent work/` workspace and verification loop are defaults; do not require the user to restate them.

1. Ensure the user supplied a project directory. Create `Agent work/` there and the four role folders plus `shared/`.
2. Create Product, Design, Engineering, and QA tasks in the same local project directory. Their prompts must name their assigned folder, files, boundaries, and the upstream files to read.
3. Start Product immediately. Start Design, Engineering, and QA in waiting state: they must not invent missing inputs.
4. When Product finishes, record a coordinator trace entry and request user approval if requirements affect scope, budget, dates, or material commitments. Then message Design with exact product-file paths.
5. When Design finishes, record the handoff. Request user approval for material design direction if required. Then message Engineering with exact product and design paths.
6. When Engineering finishes, ask it to record verification. Message QA with exact paths to product acceptance criteria, design handoff, and engineering notes.
7. When QA reports failures, append them to the trace and send only those failures to Engineering. After Engineering records a fix, send QA a retest request. Limit automatic fix/retest to two rounds, then escalate to the user.
8. When QA passes, record the result and return a final coordinator summary. Do not claim deployment, testing, or external publication without evidence.

Suggested one-sentence invocation:

`Use $agent-team to create Product, Design, Engineering, and QA agent tasks in the current project. My goal: <goal>.`
