---
name: ai-bug-triage
description: Sort incoming bugs and test failures — find duplicates, set severity and priority, and draft clean tickets. Use when asked to "triage these bugs", "is this a duplicate", "what severity is this", "go through these failures", or "turn this into a ticket".
author: Sharon Mathew
version: 1.0.0
---

# Bug Triage

Goal: turn a messy pile of bug reports, CI failures, or Slack messages into a short, clean list of real issues — each with the right severity, priority, and owner.

## When to use

- A batch of new bugs or test failures needs sorting
- I want to know if a bug is already reported
- A bug report needs a severity/priority and a proper ticket

## Step 1 — Gather the input

Collect everything first: ticket text, error messages, stack traces, screenshots, CI logs, Slack threads. Note where each one came from.

## Step 2 — Clean up the noise

Before comparing bugs, strip out the bits that change every time so similar errors look the same:

- Timestamps, dates, request IDs, user IDs
- Random numbers, hashes, temp file paths
- Line numbers inside library code

What's left (error type, message, the first file from our own code, page or endpoint) is the "fingerprint".

## Step 3 — Group duplicates

- **Same fingerprint** → same bug. Group them.
- **Very similar** (same error, slightly different wording) → likely the same. Flag for me to confirm.
- **Search the ticket tracker** for open tickets with the same error text or page before calling anything new.

Don't rely on "it sounds similar" alone — compare the actual error details.

## Step 4 — Decide the kind of failure

| Kind | Signs |
|---|---|
| **Product bug** | Real wrong behaviour a user would see |
| **Test bug** | Test is wrong, outdated, or has a bad selector |
| **Flaky** | Passes on retry with no code change |
| **Environment** | Timeouts, service down, bad test data, CI machine problem |

Only product bugs become product tickets. Test bugs and flaky tests get their own tickets. Environment problems go to whoever owns that environment.

## Step 5 — Set severity and priority

**Severity = how bad is the damage.** **Priority = how soon we fix it.** They're different.

| Severity | Meaning |
|---|---|
| Critical | Data loss, security hole, main flow completely broken, no workaround |
| High | Major feature broken, workaround is painful |
| Medium | Feature partly broken, easy workaround |
| Low | Cosmetic, typo, small layout issue |

| Priority | Meaning |
|---|---|
| Urgent | Fix now / hotfix |
| High | This cycle |
| Medium | Next cycle or so |
| Low | When there's time |

Quick guide:
- Critical + many users → Urgent
- High + common flow → High
- Low severity on a highly visible page (like the home page) can still be High priority
- Don't mark everything Critical — it makes the label meaningless

## Step 6 — Draft the ticket

```
**Title:** [Area] Short description of what's wrong
**Severity / Priority:** High / High
**Kind:** Product bug
**Environment:** stage, Chrome 128
**Steps to reproduce:**
1. ...
**Expected:** ...
**Actual:** ...
**Error (short):** the key line only, not the whole log
**How often:** 3/3
**Duplicates / related:** FIN-123
**Suggested owner:** team or area
```

Keep the log short — just the important lines. Attach the full log as a file if needed.

## Step 7 — I review before anything is sent

Never create, close, or merge tickets without my OK. Show me the grouped list and drafts first.

## Mistakes to avoid

- Calling two bugs the same because they "sound alike"
- Closing tickets automatically
- Marking everything Critical
- Filing environment problems as product bugs
- Pasting huge raw logs into tickets
- Not saying who should own it

## Done when

- Every item is grouped, labelled (product / test / flaky / environment), and has severity + priority
- Duplicates point to the existing ticket
- New tickets are drafted and waiting for my approval
