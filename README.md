---
title: Agentic Workflows Demo
description: Facilitator runbook for a 90-minute GitHub Agentic Workflows workshop.
---

## Start Here

Completed examples for [Building GitHub Agentic Workflows](https://github.com/shin-akuma/agentic-workflows-lab).
The fictional support service includes a small JavaScript module with an intentional
opening-time bug. Use its failing test to demonstrate an agent proposing a code fix
in a draft PR. Handbook mismatches remain available for the issue-triage labs.

| Workflow | Trigger | Expected result |
| --- | --- | --- |
| [weekly-summary](.github/workflows/weekly-summary.md) | Manual dispatch | One summary issue |
| [issue-helper](.github/workflows/issue-helper.md) | `/issue-helper` | One triage comment; optional follow-up issue |
| [repo-assist](.github/workflows/repo-assist.md) | `/repo-assist` or manual dispatch | Labels, investigation, and a draft PR as requested |

No workflow has a schedule. Runs consume GitHub Actions and Copilot usage.

## Programming Exercise

[isSupportOpen](src/support-hours.mjs) accepts a valid JavaScript `Date` and an
optional `isPublicHoliday` boolean, supplied by the caller. It reports standard
support availability, not the critical-incident on-call service. The contract is
Monday to Friday, from 08:00 UTC inclusive to 18:00 UTC exclusive, excluding public
holidays. It does not discover holidays or depend on the machine's local time zone.

Use Node.js 22 or later. No packages, installation, or build are required:

```bash
node --test test/support-hours.test.mjs
```

The starter deliberately has 11 passing tests and one failing test:
`open exactly at 08:00 UTC`. Keep that failure for the agent to fix. A completed
fix must pass all 12 existing tests, including closing time, weekends, holidays,
and time-zone offsets. The starter's CI is also expected to be red.

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
2. Run the tests above and confirm the single expected failure. After publishing
   the exercise files, create the code issue once if it does not already exist:

   ```bash
   gh issue create --repo shin-akuma/agentic-workflows-demo --title "Support availability is false at opening time" --body "isSupportOpen(new Date('2026-10-19T08:00:00.000Z')) returns false, but standard support opens at 08:00 UTC. Run node --test test/support-hours.test.mjs to reproduce. Fix src/support-hours.mjs without weakening tests or changing the policy. All 12 existing tests must pass, preserving UTC, weekdays, holidays, and the exclusive 18:00 closing time. Open one draft PR with this issue linked and before/after test results. Do not merge."
   ```

3. Open that code issue and add this comment:

   ```text
   /repo-assist Label this issue appropriately, then fix only the opening-time bug in src/support-hours.mjs using docs/service-policy.md. Read AGENTS.md, run node --test test/support-hours.test.mjs before and after the fix, preserve existing assertions, and open one draft PR linked to this issue with the actual test results. Do not merge it.
   ```

4. Follow its Actions run. Verify the issue receives a label and the PR is a draft.
5. Review the code diff and reported before/after tests. Existing assertions must
   remain intact; the fix must not change the policy, handbook, or workflows.
6. Run [manual CI](#verify-the-draft-pr) for the reviewed PR head and confirm all tests pass.
7. Keep the successful agent run, test run, and PR open as fallback artifacts.

To demonstrate again, leave the PR unmerged and show the existing result.
Do not promise a second identical PR: Repo Assist checks for duplicate work.
For a fresh task, use an unresolved handbook issue after the first rehearsal;
identify that as a documentation fix, not the code demo.

## Ten-Minute Opening

1. Show the code issue, the opening-time contract, and the failing test.
2. Post a scoped `/repo-assist` request, or open the completed rehearsal run.
3. Show the Markdown trigger, tools, and capped safe outputs while it runs.
4. Open the run graph: agent reads; separate jobs perform approved writes.
5. Review the label, draft PR, exact code diff, and test evidence. Humans decide whether to merge.

Start a live run a few minutes before presenting. If it is slow or fails, use the
real rehearsal artifacts and explain their status; do not present them as live.

## Verify the Draft PR

Agent-created PRs using `GITHUB_TOKEN` generally do not trigger ordinary push/PR
CI. Review the proposed code and tests before manually executing them. Replace
`123` with the PR number, then run from this repository:

```bash
PR_NUMBER=123
PR_SHA=$(gh pr view "$PR_NUMBER" --json headRefOid --jq .headRefOid)
gh workflow run test.yml --ref main -f ref="$PR_SHA"
gh run list --workflow test.yml --limit 5
```

The [test workflow](.github/workflows/test.yml) runs its definition from `main`
and checks out that exact PR commit. It uses Node.js 22, read-only repository
permissions, no supplied secrets, and no persisted checkout credentials. This is
a normal Actions workflow, not an agentic Markdown workflow; do not compile it.

Open the selected run in Actions and inspect the checkout SHA and test output.
Record the run URL and tested SHA in the review. This manual run is separate
evidence, not a substitute for required PR checks. If the PR changes, test its new
head before approving anything. The workshop does not merge or approve PRs.

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
Use the manual test run above as well as diff review; discuss GitHub Apps separately
if a production workflow must trigger downstream CI automatically.

## Troubleshooting and Shutdown

* Authentication failure: check the secret name, PAT expiry, Copilot permission, and account entitlement
* No command run: commit the workflow and lock file to the default branch, and put the slash command first in the comment
* PR failure: check Settings > Actions > General > Allow GitHub Actions to create and approve pull requests
* Slow run: show the rehearsal artifact rather than repeatedly retriggering
* Version errors: use the workshop compiler version and recompile

After the workshop, disable the workflows in Actions and revoke the workshop PAT
if it is no longer needed. Do not merge rehearsal PRs until you no longer need the
original issue for demonstrations.
