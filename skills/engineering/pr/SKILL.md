---
name: pr
description: "Use when writing or updating a PR body. Inspect the changes and gather evidence so the description explains why, shows the difference, and supports its claims."
metadata:
  credits:
    skill: show-me
    author: Dex Horthy
    organisation: Humanlayer
    url: "https://github.com/humanlayer/skills/blob/main/plugins/show-me/skills/show-me/SKILL.md"
---

Write a PR body a reviewer can assess: why the change is needed, what changes for the user or caller, and evidence that supports the result. Scale the detail to the change.

## Prepare the body

1. **Establish the scope.** Read the current diff against the PR's actual base, the existing body when updating, and the relevant issue, spec, or conversation for intent. Ground implementation claims in the diff and observed behaviour. Use the domain vocabulary from `GLOSSARY.md` (follow `GLOSSARY-MAP.md` when present). Inspect the repository's PR template and authoring instructions.
2. **Gather evidence.** Match each important behaviour claim to the evidence below. Reuse collected results that still apply to the current changes; gather what is missing. Use temporary checkouts, documented dependency setup, local services, and targeted checks as needed. Preserve the user's working state and clean up temporary resources you created.
3. **Compose the body.** Fill the repository's template, fitting these requirements into its sections and adding sections only where needed. With no repository template, use the default below. Describe the final implementation, retaining design history only where it explains a relevant tradeoff.
4. **Check the result.** Every important change has a before/after representation, each outcome claim has supporting evidence or an explicit verification gap, and the body matches the current diff. When updating, remove stale claims and refresh evidence invalidated by later commits. Verify that included images and artifact links are accessible to the intended reviewers.

This skill prepares the description and its evidence. Publishing or opening a PR follows the surrounding task's instructions; invoking this skill alone does not request publication. Refresh the body when asked to write or update it; monitoring and handling review comments are separate work.

## Default template

```markdown
## Summary

<problem and why it matters; resulting behaviour; relevant issue/spec link>

<compact before/after representation of the important change>

<relevant tradeoffs, limitations, or migration steps>

## Evidence

- **<behaviour checked>**: <command/test or scenario and conditions>
  - **Before:** <observed result>
  - **After:** <observed result>
  - <screenshots, short output excerpts, or links to supporting artifacts>

<if incomplete: what remains unverified, why, and the check still needed>

## Merge Danger

**Door:** <one-way or two-way, with the reason>

**Blast Radius:** <affected users, callers, or systems and what could go wrong>
```

## Summary: show the difference

Pair a short explanation of the problem and result with the **smallest view** that makes the important change clear. Include enough context for someone who has not followed the implementation. A tree or diagram alone does not explain why the work was needed.

Require a compact before/after representation for each important change. For changes to existing code, behaviour, or structure, include a fenced `diff` block; a table or diagram can supplement it. Choose the representation by what the reviewer needs to judge:

| What matters | Show |
| --- | --- |
| Exact syntax, an API contract, or a small implementation change | A representative excerpt of the actual code diff |
| Behaviour, control flow, component structure, or file responsibilities | A simplified diff, explicitly labeled **Conceptual diff** |
| A wholly new feature | A concrete usage example and its result; describe the previous absence or limitation |

For example, an actual code excerpt can make a quoting fix clear:

```diff
-description: Writing, explore: mine raw fragments
+description: "Writing, explore: mine raw fragments"
```

A **Conceptual diff** can explain a change in control flow:

```diff
 on(save)
-  write content
+  if content is unchanged
+    return cached result
+  write content
+  invalidate cache
```

Choose the shape to match the change: pseudocode for an algorithm, a call tree for runtime flow, a component tree for UI ownership, a shallow file tree for responsibilities, or Mermaid for interactions. Use these within the comparison or alongside it when they clarify something the diff cannot. Show a whole block for new code when omitted context would hide ownership or order.

Place each visual next to the short text it supports. Keep only the calls, files, props, states, and boundaries needed to understand the change; link to the full diff for detail. Explain material tradeoffs, limitations, and migration steps where they affect the reviewer or consumer.

## Evidence: show what happened

Evidence supports the claimed outcome. A source diff shows what was edited; an execution or rendered result shows what that edit does. For a skill instruction change, observing an actual run is stronger evidence of behaviour than searching for the new wording.

| Change | Required evidence |
| --- | --- |
| Visual appearance matters: layout, styling, or rendered formatting | Before/after screenshots of the relevant states under comparable conditions |
| Wording or instructions where rendering is not the point | Text diffs and, for behaviour claims, captured output from a relevant run |
| CLI, API, logic, or background behaviour | Relevant commands or tests with observed before/after results and concise output |
| New functionality with no runnable prior equivalent | A concrete invocation or scenario and its observed result; state the previous limitation |
| Refactor intended to preserve behaviour | Relevant checks on both versions showing the outcome stays the same |

For screenshots, identify the version, scenario, state, and relevant viewport so the comparison is interpretable. Cover the affected visual states and responsive sizes needed to judge the change. Embed the captures or link to reviewer-accessible artifacts through the available, authorized attachment mechanism. A local path is not a usable image in a hosted PR; if attachments cannot be made accessible, report that gap. Mockups and illustrative examples are not execution evidence.

For execution evidence, name the command or test and record what actually happened. Link to longer logs when useful; keep the decisive excerpt in the body. Clearly distinguish observed output, illustrative pseudocode, and checks not run. Scope claims to the check: parsing YAML successfully does not establish that installation works. Before/after test claims require observations from both versions.

### When verification is incomplete

Try the existing setup and reasonable troubleshooting first. Stop evidence gathering when the next step requires unavailable credentials, substantial new infrastructure, or implementation work such as fixing a defect or writing a new test suite. Report discovered defects and return that work to the implementation workflow.

Finish the description with the evidence available and an explicit gap: **what remains unverified, why evidence could not be obtained, and what check is still needed**. A missing baseline can leave an after-only result; label it accordingly. Keep failed checks visible. Completion of the description does not imply the change is ready to merge.

## Merge Danger

State whether the change is a **one-way door** or a **two-way door**, and explain the rollback cost. Destructive actions and hard-to-reverse decisions are one-way; a change cheap to undo is two-way. Consider effects already produced: reverting code cannot undo emails sent or restore deleted data by itself.

Name the **blast radius** concretely: affected users, callers, or systems, and the plausible consequences of a bad merge. Follow shipped changes to their downstream consumers, including users outside this repository. Include rollout or recovery details where they affect that judgement. Keep a small, reversible change's risk statement brief.
