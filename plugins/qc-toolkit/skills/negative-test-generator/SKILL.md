---
name: negative-test-generator
description: Find the ways a form, API, or feature can be broken and write negative test cases for them — bad input, missing fields, limits, wrong permissions, bad requests. Use for "negative tests for X", "what could break this", "edge cases for this form/API", or "test invalid input".
author: Sharon Mathew
version: 1.0.0
---

# Negative Test Generator

Happy-path tests prove something works. Negative tests prove it **fails safely** — with a clear error, no crash, and no bad data saved.

## When to use

- A new form, API endpoint, or feature needs "what if the user does it wrong?" coverage
- A bug came from bad input and I want to find similar ones
- Before a release, to check error handling

## Step 1 — Learn the rules from the code

Before writing anything, find:
- Required fields and their types
- Min/max lengths and number ranges
- Allowed formats (email, URL, date, slug)
- Who is allowed to do what (roles, ownership)
- What error messages and status codes the code returns

Only write tests for rules that exist. If a rule is missing (for example, no max length), note it as a possible bug, don't invent the expected result.

## Step 2 — Go through each category

For every field or action, ask:

| Category | Try this |
|---|---|
| **Missing** | Leave it empty, remove it, send `null` |
| **Wrong type** | Text where a number goes, a list where text goes, `true` instead of a string |
| **Boundaries** | Min − 1, min, max, max + 1, zero, negative, very large |
| **Format** | Bad email, bad URL, wrong date format, spaces only, emoji, very long text |
| **Special input** | `<script>` tags, quotes, SQL-looking text like `' OR 1=1`, `../` in paths |
| **Permissions** | Logged out, wrong role, someone else's item, deleted user, expired session |
| **Bad request** (APIs) | Broken JSON, wrong content type, extra unknown fields, huge body |
| **Timing** | Double-click submit, two people editing at once, slow network, going offline mid-save |
| **State** | Act on something already deleted, archived, or published |

## Step 3 — Write the expected result exactly

Each case must say what **should** happen:
- Which error message appears (exact text if the code has it)
- For APIs, the exact status code: 400 bad input, 401 not logged in, 403 not allowed, 404 not found, 409 conflict, 422 invalid, 429 too many requests
- That nothing was saved or changed
- That the page didn't crash and the user can recover

"Shows an error" is not enough. "Shows 'Title is required' under the title field and doesn't save" is.

## Step 4 — Output

Give me:
1. A table of cases: ID, field/action, category, input, expected result, priority
2. If I ask for automated tests, Playwright or Jest code — and explain each assertion so I understand what it checks
3. A short list of **possible bugs** spotted while reading the code (missing validation, missing permission check)

## Mistakes to avoid

- Treating all 4xx errors as the same — test the specific code
- Only checking the UI; the API must reject bad input too
- Tests that pass as long as *any* error appears
- Forgetting to check that nothing was saved
- Using real user data or real production accounts
