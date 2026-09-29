---
name: improve-test-cases
description: Review and tidy up existing manual test cases written in markdown — clearer titles, specific steps, checkable expected results, consistent format — without changing their IDs or what they test. Use for "improve these test cases", "clean up my manual tests", "make these tests clearer", or "fix the format of these test cases".
author: Sharon Mathew
version: 1.0.0
---

# Improve Test Cases

Goal: make existing manual test cases easy for anyone on the team to run and get the same result — **without changing what they test.**

## What I never change

- Test IDs and suite IDs
- What the test is checking (its intent)
- The language it's written in — only fix clarity, grammar, and typos
- File names and folder structure

## Step 1 — Find the test files

Check the folder I name. If none, look in the usual places: `manual-tests/`, `tests/manual/`, `docs/tests/`, and files ending `.test.md`. If nothing is found, ask me where they are.

## Step 2 — Review each test

Check for these problems:

**Title**
- Vague ("Login test", "Check page")
- Doesn't say what's being done or checked

**Description**
- Missing, or doesn't say why the test exists

**Steps and expected results**
- A step with no expected result
- Steps out of order, or several actions squashed into one step
- Expected result doesn't match the step
- No clear pass/fail

**Vague words**
- "should work", "looks fine", "correct", "properly", "might"
- Missing values ("enter a long name" — how long?)
- Things that can't be checked objectively

**Format**
- Broken markdown, missing sections, headings at the wrong level

## Step 3 — Show me what's wrong first

List the problems per test with a suggested fix. **Ask me to confirm before changing anything.** If I say no, leave the files alone.

## Step 4 — Improve

**Titles** — say the action and the outcome:
- Before: "Login"
- After: "Log in with valid email and password opens the dashboard"

**Steps** — one action per step, each with its own expected result:

```markdown
## Steps

- Go to `/login`
  _Expected_: Login form shows email and password fields
- Enter `qa.user@example.com` and a valid password, click **Sign in**
  _Expected_: Dashboard opens within 3 seconds and shows "Welcome, QA User"
```

**Vague words** — replace with something checkable:
- "Error should show" → "Red message 'Email is required' shows under the email field"
- "Page loads correctly" → "Page shows the appointments list with at least one appointment"

**Metadata** — keep the metadata format the file already uses. Make sure:
- `type: manual`
- `priority` is one of: `low`, `normal`, `important`, `high`, `critical`
- Tags are consistent (same spelling and case across files)

## Step 5 — Save

Overwrite the original files in place. Same names, same folders, same IDs.

## Step 6 — Summary

Tell me:
- How many files and tests changed
- The main kinds of improvements
- Any tests I should look at myself (for example, where the intent was unclear and I left it as is)

## Mistakes to avoid

- Rewriting a test so it checks something different
- Changing or dropping IDs
- Adding steps or checks the original didn't have (suggest them instead)
- Translating the test into another language
