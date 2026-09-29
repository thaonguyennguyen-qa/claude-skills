---
name: qa-write-test-cases
description: Write test cases and checklists for a feature from a ticket, spec, design, code, or a short description. Use for "write test cases for X", "create a test checklist", "test scenarios from these requirements", "test plan for this feature", or any request for manual QA documentation. Works best with docs or requirements, but can start from a short prompt.
author: Sharon Mathew
version: 1.1.0
---

# Write Test Cases & Checklists

Goal: go from "here's a feature" to a clean set of test cases I can run or upload to Testomat.io — with my approval at each step, so nothing surprising gets written.

Helper files:
- `references/writing-rules.md` — how to write titles, preconditions, steps, and expected results
- `references/testomat-format.md` — the Testomat.io upload format, tags, labels, and MCP tools

## My test case format (default)

Use this for every test case unless I ask for something else:

| # | Field | Notes |
|---|---|---|
| 1 | **Test ID** | Local ref: `TC-1`, `TC-2`… Never a Testomat ID (`@T…`, `@S…`) — Testomat creates those when cases are uploaded |
| 2 | **Module / Feature** (also *Applies to*) | e.g. "Banking App / Transactions" |
| 3 | **Title** | One behaviour only |
| 4 | **Priority** | High / Medium / Low |
| 5 | **Type** | Functional / API / Negative / Edge / UI / Accessibility / i18n / Performance / Security |
| 6 | **Preconditions** | What must be true before starting |
| 7 | **Steps** | Numbered actions, plain neutral verbs (click, type, open) |
| 8 | **Expected result** | What I should see — specific and checkable |
| 9 | **Actual result** | Blank until run |
| 10 | **Status** | Pass / Fail / Blocked / Not Executed (default: Not Executed) |
| 11 | **Attachments** | Screenshots, logs |
| 12 | **Test data** | Optional — exact values used |
| 13 | **Platform notes** | Optional — where a gesture or result differs by web / iOS / Android |

Layout in the `.md` file:

```markdown
## TC-1: User can search transactions by payee name

- **Module / Feature:** Banking App / Transactions
- **Applies to:** Web, iOS, Android
- **Priority:** High
- **Type:** Functional
- **Preconditions:** User is logged in and has a past payment to "Rent"
- **Steps:**
  1. Open *Transactions*
  2. Click the **Search** field
  3. Type `Rent`
- **Expected result:** List shows only transactions with "Rent" as the payee
- **Test data:** search term `Rent`
- **Actual result:**
- **Status:** Not Executed
```

At the top of each file, add an **Execution summary** table: `Test ID | Title | Priority | Status`.

Don't add a tag line like `P2 · E2E · Reg · manual` — the Priority field covers it.

**About IDs:** local `TC-1` refs are fine in my own files. Never make up Testomat IDs, and never put `TC-…` refs into a Testomat `id:` field.

**For Testomat.io upload**, use the Testomat format in `references/testomat-format.md` instead:
- Priority maps: High → `high`, Medium → `normal`, Low → `low`
- Actual result, Status, and Attachments are run-time fields in Testomat. They stay in my local files, but on upload they go in the description text — they don't map to Testomat fields.

## Before starting

If available:
- Scan the project first — app code, e2e tests, existing test cases (a `scan-automation-project` skill, if installed, does this)
- Pull the existing Testomat.io tests (a `sync-test-cases-with-tms` skill, if installed, or `check-tests pull`)
- Use plan mode for the question steps

## How to ask me questions

- **Always make a checklist first**, even if I ask straight for test cases. It's quicker to fix a list than 30 written cases.
- **Don't skip steps. Wait for my OK at each gate** before moving on.
- Use plan mode for the question steps; only switch to editing on the last step, when writing the files.
- Use the Ask tool for choices if available, and fill in suggestions where I need to type.
- Mark questions with ❓ and numbered options:

```
❓ What next?

1. ➡️ Go to next step
2. ✏️ Type changes
```

## The steps

1. Gather context → ✅ my OK
2. Pick coverage size → ✅ my OK
3. Pick a testing angle → ✅ my OK (skip for smoke)
4. Make the checklist → ✅ my OK
5. Write the test cases
6. Show a summary

### Step 1 — Gather context

Work out: what the feature does and why it matters, the main user flows, and anything I'm worried about.

Look in:
- My message
- Ticket trackers — Linear, Jira
- Docs — specs, Confluence
- Designs — Figma, Miro
- Existing test cases — Testomat.io, TestRail
- The code:
  - Manual test files: `.test.md` or `.md` in folders like `manual-tests`, `tests`, `manual`, `qa`, `spec`
  - Automated test files: `*.spec.ts`, `*.test.ts`, `*.cy.ts` and similar
  - The overall project structure

Sources can come as pasted text, links, or MCP tools. If a tool isn't connected, ask me to set it up.

**What kind of project is it?** Check first:
- **Test-only project** (no app code): scan quickly, focus on the test files
- **App + tests**: read the app code properly; skip unit-test detail unless I ask

**Is it a Testomat.io project?** Two ways to check:

1. **Testomat MCP tools** (if connected) — look up:
   - Suites (`suites_list`) — existing structure
   - Tests (`tests_list`) — existing tests, to avoid duplicates
   - Steps (`steps_list`) — shared steps to reuse
   - Tags and labels (`tags_list`, `tags_search`, `labels_list`) — project conventions
2. **Pull the tests as files** (`sync-test-cases-with-tms` skill or `check-tests pull`), then read them for: suites, existing tests, and conventions (tags, labels, priorities).

If existing tests already cover this feature, tell me and ask whether to add to them or start a new suite (see also **detect-duplicate-test-cases**).

Then show me **only the names** of the sources used — not their content — and ask to continue:

```
I used:
- Ticket FIN-123
- Figma: Transaction search
- src/features/transactions/Search

❓ What next?
1. ➡️ Go to next step
2. ✏️ Type changes
```

### Step 2 — Pick coverage size

This sets the starting size of the checklist — I can still fine-tune it in Step 4.

- Estimate how many tests each option would give **for this feature specifically**, from what you found in Step 1. Don't use fixed or generic numbers.
- If it's too vague to estimate, say so — leave the numbers out or go back to Step 1.
- If I pick ✏️ Other, accept a number ("around 10") or a description ("just the API, no UI") and size the checklist from that.

```
❓ How much coverage do you want?

1. 🚀 Smoke ~<N> tests
   Critical path only
2. ⚖️ Balanced ~<N> tests
   Happy path, main negative cases, common edge cases
3. 🧨 Full ~<N> tests
   Everything, incl. errors, limits, security, performance, translations where relevant
4. ✏️ Other
   Pick a testing angle, give a number, or describe it in your own words
```

### Step 3 — Pick a testing angle

- For 🚀 Smoke: **skip this step** and use default. A smoke set is too small to need a special angle.
- Otherwise show the angles as a table, suggest one based on the feature, and let me pick.
- If I don't pick, use ⚙️ default.
- If I pick 🔧 other, ask me to describe my own angle.
- If you only show a few angles, add a "Show all" option:

```
❓ Choose an angle:

1. ⚙️ default (or the one you suggest)
2. ☰ Show all angles
3. ✏️ Type my own
```

| Angle | Focus |
|---|---|
| ⚙️ default | Balanced mix |
| 🌈 happy path | Things working as expected |
| 🤓 detail | Every rule and dependency |
| 🔪 edge cases | Limits, odd input, weird timing |
| 🔐 security | Permissions, access, bad input |
| 🎨 UI/UX | Layout, states, usability |
| ⚖️ wording | Text, error messages, typos |
| 🌐 languages | Translations, right-to-left, long text |
| 📈 performance | Speed, load, stress |
| 🔧 other | I describe my own |

### Step 4 — Make the checklist

A grouped list, sized to the option I picked:
- 🚀 Smoke — critical paths only
- ⚖️ Balanced — happy path, main negative cases, common edge cases
- 🧨 Full — everything
- ✏️ Other — whatever I described

```
- Search
  - Search by exact payee name
  - Search with no results
  - Search with special characters
- Filters
  - Filter transactions by date range
```

**Approval:**
- If a multi-select Ask tool is available, show each item as a checkbox with its group in front (`Search: no results`), with the suggested ones for my chosen size pre-ticked. The ticked items become the test cases.
- Otherwise show the list and ask me to add, remove, or change items.

Always ask about the amount of detail:

```
❓ How does this look?
1. 👍 Keep as is
2. ➖ Less detail
3. ➕ More detail
4. ✏️ Type changes
```

- More detail → switch to the 🤓 detail angle
- I change things → redo the checklist
- I'm not happy at all → go back to Step 1 for more context
- I approve → Step 5

### Step 5 — Write the test cases

If I only asked for a checklist, ask first: "Do you want test cases for this checklist?"

**Where to save:**
- Empty folder, or manual-tests-only folder → project root
- E2E test project → `manual-tests/`
- Anything else → `.testeiya/manual-tests/` (create it if needed and make sure it's in `.gitignore`)

**File rules:**
- File name: `feature-name.test.md` (always `.test.md`)
- Several features or a whole product → one file per feature
- **Only create `.test.md` files — never change the app code**
- Stay in scope: only test the feature asked for, not linked features, unless I ask

**Format rules:**
- **Local files → my 13-field format above.**
- **Testomat upload, or if I ask for it →** the Testomat format in `references/testomat-format.md`: wrap cases in `<!-- suite -->` and `<!-- test -->` blocks, and put `tags:` and `labels:` inside **each** test block, not only on the suite.
- If I give my own format in the prompt, use that instead.
- Follow `references/writing-rules.md` for descriptions, preconditions, steps, and expected results.
- Reuse existing tags and labels (from the MCP tools or other test files). Don't make up labels if you don't know any existing ones.
- Where it makes sense, fill in priority, preconditions, test data, tags, and labels from what you found.
- Use real, concrete values — **no `${placeholders}` on the first pass.** Only use `` `${name}` `` (in backticks) for values reused across many steps or in data-driven tables.
- **Never make up Testomat IDs** (`@T12345678`, `@S380c64db`). Testomat adds them when I upload with `check-tests push --update-ids` (or the `sync-test-cases-with-tms` skill).

**Copying an existing style:** if I give an example test case or ask for "similar" ones:
- **Do match:** writing style, level of detail, fields (Priority, Preconditions, Type, Test data…), extra fields like tags and labels, and the language
- **Don't copy:** any IDs from the example (Testomat `@T…`/`@S…` or prefixes like `TC-R001`), or what the example tests

### Step 6 — Summary

- Show a short table: number of test cases and suites, files created, and where they are
- Ask me to review the files and ask for changes
- Suggest uploading to Testomat.io (`sync-test-cases-with-tms` skill or `check-tests push`) if that's the next step
