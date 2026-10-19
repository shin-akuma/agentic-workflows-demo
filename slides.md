---
title: Building GitHub Agentic Workflows
description: Three introductory slides for the hands-on workshop.
marp: true
theme: default
size: 16:9
paginate: true
footer: Agentic Engineering Day
style: |
  section {
    background: #f7f9fc;
    color: #24292f;
    font-family: "Segoe UI", sans-serif;
    font-size: 30px;
    padding: 60px 72px;
    border-top: 10px solid #0969da;
    letter-spacing: 0;
  }
  h2 { color: #0969da; font-size: 46px; }
  strong { color: #116329; }
  a { color: #0969da; }
  table { font-size: 27px; width: 100%; }
  th { background: #e8eef5; }
  footer { font-size: 18px; color: #57606a; }
---

## Building GitHub Agentic Workflows

From a repository event to a reviewable result

* Describe a task in Markdown.
* Run an AI agent through GitHub Actions.
* Constrain its tools, permissions, and outputs.

Today: summarise activity, respond to issues, and propose fixes.

---

## How It Works

Event > Agent > Validated Safe Outputs > Human Review

| Stage | Responsibility |
| --- | --- |
| Trigger | Manual run, schedule, issue, or slash command |
| Agent | Task instructions, scoped tools, read-only repository access |
| Safe outputs | Separate jobs publish permitted changes within limits |
| Human | Reviews evidence and decides whether to merge a proposed PR |

Markdown defines the workflow. Compiled YAML runs it.

---

## Your Hands-On Labs

| Lab | Task | Time |
| --- | --- | --- |
| 1 | Create and run a weekly summary | 15 min |
| 2 | Add slash commands and bounded outputs | 20 min |
| 3 | Install and constrain Repo Assist | 15 min |

**Create your own repository from the template:**

[github.com/shin-akuma/agentic-workflows-lab](https://github.com/shin-akuma/agentic-workflows-lab)

Follow the README checklist. Keep tokens private and review proposed changes.
