# Testomat.io Upload Format

Use this format only when preparing test cases to **upload to Testomat.io**. For my own local files, use my default format in `SKILL.md`.

## How a file is laid out

```markdown
<!-- suite
tags: search, regression
-->
# Transaction search

Tests for searching past transactions in the banking app.

<!-- test
type: manual
priority: high
tags: smoke
-->
# User can search transactions by payee name

User types a payee name and sees only matching transactions.

## Steps

- Open *Transactions* and click the **Search** field
  *Expected*: Search field is focused
- Type `Rent`
  *Expected*: Only transactions with "Rent" as the payee are shown
```

- One `<!-- suite -->` block at the top, then one `<!-- test -->` block per test
- A file can hold more than one suite — just start another `<!-- suite -->` block
- Inside a block, each line is `key: value`. Lines without a `:` are ignored
- The first `#` heading after a suite block is the suite title; the first `#` or `##` heading after a test block is the test title. Other headings are just part of the description
- Everything after the title (until the next `<!-- test` or `<!-- example`) is the description, in normal markdown
- Steps go under `## Steps`, as a bulleted (`-`) or numbered (`1.`) list, each with an `*Expected*` line underneath
- Attachments are links: `https://app.testomat.io/attachments/{uid}.{ext}`

## Suite fields

| Field | What it is |
|---|---|
| `id` | Testomat suite ID (`@S` + 8 characters). **Don't write this yourself** — Testomat adds it on upload |
| `emoji` | Optional icon for the suite |
| `tags` | Comma-separated, no `@` |
| `labels` | Comma-separated, `Name` or `Name: value` |
| `assignee` | Email of someone already in the Testomat project |

## Test fields

| Field | What it is |
|---|---|
| `id` | Testomat test ID (`@T` + 8 characters). **Don't write this yourself** — keep it only if the test already has one |
| `type` | `manual` or `automated` |
| `priority` | `low`, `normal`, `important`, `high`, or `critical` |
| `tags` | Comma-separated, no `@` |
| `labels` | Comma-separated, `Name` or `Name: value` |
| `assignee` | Email of someone already in the project |
| `creator` | Email of who made it (only if the project uses it) |
| `shared` | `true` if it's a shared test (only if the project uses it) |

If a test has no assignee, it uses the suite's assignee.

## Tag rules

- In a title: `@smoke @regression`
- In metadata: `tags: smoke, regression` (no `@`)
- Allowed characters: letters, numbers, and `= - _ ( ) . : &`, under 120 characters
- Never put suite or test IDs (`@S…`, `@T…`) in tags

## Tags vs labels

| | Tags | Labels |
|---|---|---|
| Use for | Grouping and filtering (`smoke`, `regression`, `api`, `auth`) | Details that change (owner, severity, status) |
| In the title | `@smoke` | — |
| In metadata | `tags: smoke, regression` | `labels: Component: Transactions` |

Only use labels that already exist in the project.

## Data-driven tests (examples table)

To run the same test with different data, add a table after `<!-- example -->`:

```markdown
<!-- example -->

| search term | expected results |
| --- | --- |
| Rent | 1 or more |
| zzzzzz | 0 |
```

The first row is the column names; the `---` row separates them from the data. Each row must start with `| ` and end with ` |`. If there's no `---` row, every row is treated as data (no column names).

## Testomat MCP tools

If the `testomatio` tools are connected:

| Use for | Tools |
|---|---|
| Structure | `suites_list`, `tests_list`, `steps_list` |
| Conventions | `tags_list`, `tags_search`, `labels_list` |

- **Always check existing tests before writing new ones**, to avoid duplicates
- Look for shared steps to reuse before writing new ones

**Duplicates:**
1. Find existing tests
2. Exact match → tell me and let me decide whether to skip it
3. Partial overlap → ask me
4. No match → write the new test

## Uploading

Upload with `check-tests push --update-ids` (or the `sync-test-cases-with-tms` skill). This creates the tests in Testomat and writes the new `@T…`/`@S…` IDs back into the files.

## Before uploading — quick check

- [ ] No IDs made up by me
- [ ] Priority set on each test
- [ ] Tags reuse existing ones
- [ ] Every step has an *Expected*
- [ ] No duplicates of tests already in Testomat (search first)
