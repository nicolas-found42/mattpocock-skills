---
name: handoff
description: Compact the current conversation into a handoff document for another agent to pick up.
argument-hint: "What will the next session be used for?"
disable-model-invocation: true
---

Write a handoff document summarising the current conversation so a fresh agent can continue the work. Save to the temporary directory of the user's OS - not the current workspace.

Include a "suggested skills" section in the document, naming which skills the next agent should call the Skill tool for.

Do not duplicate content already captured in other artifacts (specs, plans, ADRs, issues, commits, diffs). Reference them by path or URL instead.

Redact any sensitive information, such as API keys, passwords, or personally identifiable information.

If the user passed arguments, treat them as a description of what the next session will focus on and tailor the doc accordingly.

For work in a managed worktree, inspect the app's artifact metadata and include:

- The exact attachment identity and checkout path, owning chat ID/name, and current branch/head. Mark unavailable ownership as unknown.
- The external evidence destination and preservation manifest, plus the primary checkout's WIP inventory or proof path.
- The archive route: use the app's archive tool from an authorized owning chat after preserving required ignored files. Record whether messaging that chat has been authorized; obtain explicit authorization before sending a cleanup request when needed.

Put this metadata beside remaining delivery work so the next agent can identify the cleanup dependency before beginning. Reference the repository's cleanup procedure when one exists.
