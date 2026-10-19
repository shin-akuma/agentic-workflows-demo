---
title: Agentic Workflows Demo
description: Facilitator runbook for a 90-minute GitHub Agentic Workflows workshop.
---

## Start Here

Completed examples for [Building GitHub Agentic Workflows](https://github.com/shin-akuma/agentic-workflows-lab).
The fictional support handbook has an intentional mismatch with its service policy.
Use that small, verifiable change to demonstrate an agent proposing a draft PR.

| Workflow | Trigger | Expected result |
| --- | --- | --- |
| [weekly-summary](.github/workflows/weekly-summary.md) | Manual dispatch | One summary issue |
| [issue-helper](.github/workflows/issue-helper.md) | `/issue-helper` | One triage comment; optional follow-up issue |
| [repo-assist](.github/workflows/repo-assist.md) | `/repo-assist` or manual dispatch | Labels, investigation, and a draft PR as requested |

No workflow has a schedule. Runs consume GitHub Actions and Copilot usage.

## Authentication

For this personal repository, create a fine-grained PAT with Account permissions >
Copilot Requests > Read. Add it under Settings > Secrets and variables > Actions
as `COPILOT_GITHUB_TOKEN`. Never paste a token into chat, source files, or issues.
The account that owns the token must have Copilot access.

CLI alternative, with a hidden interactive prompt:

```bash
gh secret set COPILOT_GITHUB_TOKEN --repo shin-akuma/agentic-workflows-demo
```

The regular Actions `GITHUB_TOKEN` handles repository reads and safe-output writes.
It is separate from the inference credential. Organisation token-based inference
requires eligible centralised Copilot billing; it is not configured here.

## Rehearsal

1. Verify the secret exists and Actions permits PR creation.
2. Open the seeded support-hours issue. Add this comment:

   ```text
   /repo-assist Label this issue appropriately, then fix only the support-hours mismatch in docs/support-handbook.md using docs/service-policy.md as the source of truth. Read AGENTS.md, validate the diff, and open one draft PR linked to this issue. Do not merge it or edit other files.
   ```

3. Follow its Actions run. Verify the issue receives a label and the PR is a draft.
4. Check that only the handbook changed and its hours now match the policy.
5. Keep the successful run and PR open as the fallback for tomorrow's live demo.

To demonstrate again, leave the PR unmerged and show the existing result.
Do not promise a second identical PR: Repo Assist checks for duplicate work.
For a fresh change, use the escalation-contact issue after the first rehearsal.

## Ten-Minute Opening

1. Show the issue and the conflicting policy and handbook text.
2. Post a scoped `/repo-assist` request, or open the completed rehearsal run.
3. Show the Markdown trigger, tools, and capped safe outputs while it runs.
4. Open the run graph: agent reads; separate jobs perform approved writes.
5. Review the label, draft PR, and exact diff. Humans decide whether to merge.

Start a live run a few minutes before presenting. If it is slow or fails, use the
real rehearsal artifacts and explain their status; do not present them as live.

## Lab Reference Commands

From this repository's local checkout:

```bash
gh aw compile
gh workflow run weekly-summary.lock.yml
gh run list --limit 5
```

On the escalation-contact issue, post `/issue-helper`. To request a follow-up,
post `/issue-helper create-follow-up` instead. Inspect both the comment and new issue.

For gallery labelling, post `/repo-assist Run Task 1 only. Do not change files or open pull requests.`
Command mode deliberately skips the Monthly Activity report. A manual dispatch
with an empty command runs autonomous task selection and can create that report,
but can also propose other changes. It is optional, not needed for this workshop.

## Version and Safety Notes

* Compiled with `gh-aw` v0.92.0; commit Markdown and generated lock files together
* Gallery source: `githubnext/agentics` at `b4e175bab6674279e0fe158f7dc877b77bc9d92f`
* Workshop changes: no schedule, 15-minute timeout, no optional web-fetch tool, and lower safe-output caps
* Repo Assist retains upstream read tools, sandbox shell access, and a schema-validated JSON memory branch
* Imported memory validation was reviewed: it validates local JSON shape and limits without network calls or secret access
* Repo Assist can create one draft PR, one issue, three comments, and five label operations per run
* `AGENTS.md` provides behavioural instructions, not an enforceable security boundary; review every diff
* No auto-merge is configured; do not grant extra PAT repository permissions to make CI trigger

PRs created using `GITHUB_TOKEN` do not generally trigger ordinary push/PR CI runs.
This documentation-only exercise uses diff review; discuss GitHub Apps separately
if a production workflow must trigger downstream CI.

## Troubleshooting and Shutdown

* Authentication failure: check the secret name, PAT expiry, Copilot permission, and account entitlement
* No command run: commit the workflow and lock file to the default branch, and put the slash command first in the comment
* PR failure: check Settings > Actions > General > Allow GitHub Actions to create and approve pull requests
* Slow run: show the rehearsal artifact rather than repeatedly retriggering
* Version errors: use the workshop compiler version and recompile

After the workshop, disable the workflows in Actions and revoke the workshop PAT
if it is no longer needed. Do not merge rehearsal PRs until you no longer need the
original issue for demonstrations.
