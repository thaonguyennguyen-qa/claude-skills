---
name: qa-thinking
description: Look at a feature (an idea, a spec, a branch, or a PR) the way a QA engineer would, and point out what could go wrong — edge cases, misuse, failures, clashes with other features. Use for "what could go wrong?", "what am I missing?", "review this as QA", or "poke holes in this feature".
author: Sharon Mathew
version: 1.0.0
---

# QA Thinking

Goal: help a developer (or me) spot the risky parts of a feature **before** it's built or shipped. Short, direct, and useful — not a long essay.

## What I can start from

- A description of the idea
- A spec or ticket (Linear, Jira)
- The current branch or a PR — read the changes and the PR description to understand what it's meant to do (pr-test-impact-analyzer helps here)

If I can't tell what the feature is supposed to do, ask me before going further.

## Questions to think through

1. **What is it for?** Who uses it and why?
2. **Does it fit?** Does it match how similar features in the app already work? Does the codebase already have something that does this?
3. **What if it's used wrong?** Wrong input, wrong order, wrong role, on purpose or by accident.
4. **What if something fails?** Network drops, server error, slow response, half-saved data.
5. **Edges and limits:** empty, one, many, maximum, very long text, special characters, time zones.
6. **Interrupted halfway:** user cancels, closes the tab, goes back, double-clicks, loses connection mid-save.
7. **Other features:** what else reads or changes the same data? Permissions, translations, mobile, analytics, emails.
8. **Security:** who can see or change what? Can someone reach another person's data? Is user input shown back without being cleaned?

## Output

Use exactly these three sections. Numbered lists, short sentences, plain words.

### 👷 Must be acknowledged
A quick summary: what the feature touches, which other areas it could affect, and the main risks.

### 👓 Must be clarified
Up to **5** questions about unclear or missing behaviour. Start each one with **"What if…"**.

### 🔬 Must be verified
Up to **5** of the most important risk scenarios to test. No more — pick the ones that matter most.

## Example

> 🔬 Must be verified
> 1. A patient can't see another patient's records by changing the ID in the URL.
> 2. Booking with no internet shows an error and doesn't create a half-booked appointment.

## After the review

Offer the next step:
- Turn the scenarios into test cases → **qa-write-test-cases**
- Go deeper on bad input and permissions → **negative-test-generator**
