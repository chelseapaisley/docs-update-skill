# docs-update-skill

A Claude skill that learns your documentation structure and conventions, then generates a personalised doc updater you can point at any future change.

## What it does

This is a setup skill. Run it once to produce a `doc-updater.md` skill tailored to your docs. From then on, give the doc updater any change — Jira, PR, commit, diff, or description — and it either drafts the right doc update or tells you clearly why nothing needs documenting.

**Setup flow:**
1. Provide your existing docs (MCP, URLs, files, or a mix)
2. The skill analyses structure, style, and conventions automatically
3. It asks only the questions it couldn't answer from the docs
4. You provide 2–3 real recent changes to test against
5. You give feedback; the skill proposes rule updates visibly for your approval
6. You review and confirm the full ruleset
7. It outputs your personalised `doc-updater.md`

**Then day-to-day:** point `doc-updater.md` at any change → get a doc update or a clear explanation of why nothing needs changing.

## Files

```
docs-update-skill/
├── README.md
├── SKILL.md                      ← the setup skill — install this
└── references/
    └── doc-analysis.md           ← extraction guide and "doesn't fit" logic
```

## Install

1. Clone or download this repo
2. Copy the `docs-update-skill/` folder into your Claude skills directory
3. Reload skills
4. Say *"set up my docs update skill"* to start

## Accepted change formats

The generated doc updater handles any of:
- Jira ticket (MCP fetch by ID, or paste the body)
- PR description
- Commit message
- Git diff (Claude focuses on user-visible changes and ignores noise)
- Plain description of what changed
- Any combination of the above

## The feedback loop

During setup, when you correct a test draft, the skill doesn't just fix the draft — it proposes a general rule that your correction implies, asks you to confirm or refine it, and adds confirmed rules to a visible log. At the end you review the full ruleset before the skill is generated. Nothing gets baked in without your approval.

## The "doesn't fit" rule

If a change doesn't map to any existing doc section, the generated skill defaults to flagging it rather than inventing structure. Most "doesn't fit" cases are "doesn't need documenting" cases. New sections are proposed as questions, not decisions.

## Re-running setup

Re-run this skill when your doc structure changes significantly, you move to a new doc set, or the generated skill starts making wrong calls consistently. It'll produce a fresh `doc-updater.md`.
