## What it does

`pr` writes or updates a pull request description that explains why the change is needed, shows the important before/after difference, and supports its claims with **evidence**. The [agent](https://www.aihero.dev/ai-coding-dictionary/agent) inspects the current diff and gathers missing evidence within the existing project setup. Every important outcome is backed by an observed result or an explicit statement of what remains unverified.

The body gives a reviewer enough context to judge the change without following the implementation conversation. It preserves the repository's PR template and scales the explanation to the work: a quoting fix may need one small diff and parser output; a visual redesign needs comparable screenshots of the affected states.

## When to reach for it

Type `/pr`, or the agent reaches for it automatically when writing or updating a PR body.

| Your situation | Reach for |
| --- | --- |
| A branch needs a description a reviewer can assess | `pr` |
| Later commits have made the PR description or evidence stale | `pr` to refresh it |
| The code needs a review against its requirements and standards | [code-review](https://aihero.dev/skills-code-review) |
| Review comments need triage or fixes | Handle those as a separate task; `pr` prepares the description |

## Prerequisites

The agent needs access to the changes and their intended purpose. Gathering evidence also needs the relevant project tools: runnable checks, a local app for visual comparisons, and an attachment mechanism when images will go into a hosted PR. Missing access becomes a named verification gap in the description.

## A difference you can inspect

The **smallest view** shows the important change beside a short explanation of its purpose. It can take several forms:

| What the reviewer needs to understand | Useful representation |
| --- | --- |
| Exact syntax or a small implementation change | A representative code diff |
| Changed behaviour, flow, or structure | A clearly labeled conceptual diff |
| A new feature | A concrete usage example and its result, with the previous limitation explained |

Changes to existing code, behaviour, or structure include a diff block. Tables and diagrams can supplement that comparison. A file tree can show where a new component lives, but the description must also explain what that component enables. Tradeoffs, limitations, and migration steps belong where they affect the reader's decision. A large change can need several comparisons; a small one should remain small.

## Evidence that matches the claim

The description distinguishes what was edited from what was observed. A search confirming that an instruction was added proves the text changed. An actual run showing the resulting behaviour supports the stronger claim that the instruction works.

| Change being judged | Evidence to expect |
| --- | --- |
| Layout, styling, or rendered formatting | Before/after screenshots in comparable states and viewports |
| Wording with no visual effect | A text diff |
| CLI, API, logic, or instruction behaviour | Relevant commands or scenarios with captured results |
| New functionality | An observed usage example, with the previous limitation stated |
| A refactor preserving behaviour | Relevant checks on both versions showing the same outcome |

The agent can use temporary checkouts, documented dependency setup, local services, and targeted checks. It reuses evidence that still applies and refreshes results affected by later commits. Substantial infrastructure work and defects discovered during verification go back to the implementation task.

Without a repository template, the body uses **Summary**, **Evidence**, and **Merge Danger**. The risk statement names rollback cost and the affected users or systems. A **two-way door** is cheap to undo; a **one-way door** produces effects that are difficult or impossible to reverse. Reverting a commit cannot unsend an email.

## Common questions

**Does it open the PR for me?**

Invoking `pr` prepares the body and its evidence. Opening or publishing a PR follows the surrounding task's instructions. You can ask the agent to open a PR and have it use this skill for the description; the skill itself adds no publication step.

**My repo already has a PR template. Which one wins?**

The repository's template. The agent fills it and fits the explanation, comparison, evidence, and risk information into its sections. It adds sections only where necessary, preserving required issue links, deployment notes, or checklists.

**Are screenshots required for every PR?**

Screenshots are required when visual comparison matters, including layout and rendered formatting. Text diffs and captured output are more useful for nonvisual changes. A screenshot of terminal output is rarely clearer than the output itself. Images in a hosted description must be accessible to reviewers; a path on the author's machine is insufficient.

**What if the app will not start or there is no before screenshot?**

The agent tries the available setup and reasonable troubleshooting, then finishes the description with the evidence it has. It names the missing verification, the reason, and the check still needed. An after-only capture stays labeled as such. A complete description can still expose a change that is not ready to merge.

**Does it keep the body up to date as the PR changes?**

When asked to write or update the body, it rechecks the current diff, removes stale claims, and refreshes affected evidence. It does not monitor the PR automatically. Ask for a refresh after a substantial change during review.

**How much information is enough?**

Enough to understand the problem, the resulting behaviour, the verification, and the relevant risks. A parser check supports a parsing claim; it does not prove a package can be installed. A compact representative diff is often more useful than another paragraph or the entire patch repeated in the body.

**Can I trust the agent's own risk assessment?**

Treat it as a claim to assess during [human review](https://www.aihero.dev/ai-coding-dictionary/human-review). The description should explain rollback cost and concrete consequences, including effects on downstream consumers outside the repository, so you can challenge its reasoning. External effects and data changes often matter more than how easy the code is to revert.

## It's working if

- You can explain why the change matters and what it does without reading the implementation conversation.
- A small diff or example makes the important difference visible before you open the full patch.
- Visual changes have comparable screenshots you can actually open, or a specific explanation of what could not be captured.
- Verification claims point to observed results; failed or missing checks remain visible.
- An updated body describes the current implementation and uses evidence that still applies.
- The body fits your repository's template, and the risk statement names who or what could be affected.

## Where it fits

`pr` is a chain step after [code-review](https://aihero.dev/skills-code-review) when work goes up as a pull request: review examines the implementation, and `pr` makes the result assessable in the description. [implement](https://aihero.dev/skills-implement) produces the changes and takes back implementation work discovered during evidence gathering. The agent can also use `pr` whenever a description needs writing or refreshing outside that chain.

[ask-matt](https://aihero.dev/skills-ask-matt) routes across the whole set when you are unsure which skill fits.
