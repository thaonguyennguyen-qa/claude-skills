# How I Write Test Cases

If I've given example test cases, match their style first. Then use these rules.

## The big rule

**Why goes in the description. What goes in the steps.**

- Description: the purpose, business rules, formulas, anything that explains the test
- Steps: only actions and what to check

## Suites (groups of tests)

- Put setup that every test in the group needs in the suite description, as bullet points
- Put shared rules or formulas there too, not in each test — a code block is fine for formulas or diagrams

## Test cases

- Test from the outside, like a user: through the UI, or a public API if there's no UI
- Prefer the UI over the API when both work
- Only check the database or server directly if there's no other way to see the result
- If I don't know how a page works, look at the frontend code, a stage environment, or screenshots — ask me for access if needed

## Titles

Say who does what, from the user's point of view:

`<who> <can / cannot> <action> <object> <detail>`

Good:
- Patient can cancel an appointment from My Appointments
- Guest cannot open the admin dashboard
- User sees an error when the email format is wrong
- Admin can invite up to 10 team members from Settings

Avoid:
- Starting with "Verify that…", "Test that…", "Check…", "Should…"
- Two things in one title ("…and then…") — split it
- Repeating the suite name in every title
- Vague endings like "works" or "successful"

| Instead of | Write |
|---|---|
| Verify successful login | User can log in with valid email and password |
| Wrong password test | User cannot log in with a wrong password |
| Login and update profile | Two separate tests |
| Email validation | User sees an error when the email format is wrong |

## Preconditions

- What must be set up before the test, that isn't part of the test itself
- Bullet points
- Include the role if it matters
- Skip obvious ones like "the site is running", unless that's what's being tested

## Steps

Each step = one action + what should happen.

```markdown
## Steps

- Go to `/login`
  *Expected*: Login form shows email and password fields
- Enter `qa.user@example.com` in **Email** and a valid password in **Password**
  *Expected*: No error messages show
- Click **Sign in**
  *Expected*: Dashboard opens
```

Rules:
- One short, simple action per step
- Use real values the tester can type straight in
- Use paths, not full URLs: `/auth/login`
- Replace vague words ("small", "some", "around", "e.g.", "like") with exact values
- Checks go in *Expected*, not in the action
- If an expected result has two conditions, split them onto two lines:
  - *Expected*: Comment appears in the list
  - *Expected*: Current user is shown as the author
- A code block after a step is fine for API requests, SQL, or commands
- Don't turn values into `${placeholders}` on the first pass. Only do it when the same value is reused across steps or the test is data-driven
- Never turn preconditions or unrelated data into placeholders
- Use exact values when they matter: boundaries, formats, locale-specific input
- General statements belong in the description, not the steps
- Every action must be doable through the UI or a public API

## Bold and italic

- **Bold** = things you click or type into: buttons, fields, tabs, links (`Click **Save**`)
- *Italic* = names of pages or screens (`Open the *Privacy Policy* page`)
- Don't bold whole sentences, and don't mix bold and italic

## Plain writing

Write like a person, not a brochure. Say exactly what happens.

Avoid:
- Hype words: crucial, seamless, robust, cutting-edge, pivotal, vital
- AI-sounding words: delve, leverage, foster, streamline, landscape, notably
- Filler: "it's important to note", "in summary", "overall", "plays a key role", "serves as", "is a testament to"
- Empty "-ing" add-ons: ensuring, showcasing, highlighting, enhancing
- "Not only… but also…" and "It's not just X, it's Y" sentences
- "Despite these challenges…" / "Future outlook" style framing
- Lots of emojis, bullets, or bold for decoration
