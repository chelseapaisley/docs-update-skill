---
name: docs-update-skill
description: Use this skill when the user wants to set up a skill for keeping documentation up to date, build a doc updater for their team, or create a tool that takes a change (Jira, PR, commit, diff, or description) and produces a doc update. Trigger when the user says things like "set up my docs skill", "build me a doc updater", "create a skill for updating docs", or "I want Claude to keep my docs in sync". This skill analyses existing docs, infers conventions, fills gaps through a minimal interview, tests against real changes with a visible feedback loop, and outputs a personalised doc-updater.md skill ready to install.
---

# Docs Update Skill — Setup

This skill does one thing: learns how your docs are structured and written, understands what kinds of changes make it into docs and what don't, then produces a personalised `doc-updater.md` skill the user can point at future changes.

The output skill takes a change in any format — Jira, PR, commit, diff, or plain description — and produces a ready-to-apply doc update, or explains clearly why nothing needs documenting.

---

## Step 1 — Ingest the docs

Ask the user:

> "First I need to read your existing docs. How would you like to provide them?"
>
> **Option 1 — MCP**: If you have a docs MCP or Google Drive MCP connected, I can pull the pages directly. Give me the doc set, folder, or domain to start from.
>
> **Option 2 — URLs**: Paste one or more URLs — a single page, a section index, or a support domain (e.g. `help.yourproduct.com`). I'll read what's accessible.
>
> **Option 3 — Local files**: Upload or paste the doc files directly.
>
> **Combinations are fine** — e.g. a style guide URL plus a local docs folder.

Also ask:

> "Do you have a style guide? If so, include it the same way — URL, MCP, or file. If not, no problem, I'll infer style from the docs themselves."

Once you have the docs, confirm what you received: how many pages/files, rough topic areas, and whether a style guide was found.

---

## Step 2 — Analyse the docs

Read all provided docs and extract the following automatically. Do not ask the user for anything that can be determined from the docs themselves.

Read `references/doc-analysis.md` for the full extraction guide.

At the end of this step, produce an internal analysis summary (not shown to the user yet) covering:

- **Structure map**: the doc set's information architecture — top-level sections, page types, how pages relate to each other
- **Style rules**: voice, formatting, terminology, heading patterns, step conventions — extracted as rules not observations
- **Content patterns**: what kinds of changes appear to have generated past doc updates (inferred from content, not commit history)
- **Gaps and ambiguities**: things that couldn't be determined from the docs alone and will need to be asked

---

## Step 3 — Minimal interview

Only ask about what genuinely couldn't be determined from the docs. Present findings first, then ask about gaps.

Tell the user:

> "Here's what I learned from your docs: [brief summary of structure and style]. I have [N] questions I couldn't answer from the docs alone."

Then ask only the questions from this list that remain genuinely unanswered after analysis. Do not ask questions the docs already answered.

**Questions to ask only if unanswered:**

1. **The user-facing line**: When a change doesn't directly touch the UI or user workflow — for example, a backend change that alters an error message, or an API change that affects an integration — does it get documented? Or does the rule follow user experience impact rather than code location?

2. **Bug fixes**: Do bug fixes get documented (e.g. "Fixed an issue where X")? Or only new features and behaviour changes?

3. **Audience**: Who reads these docs — end users, admins, developers, or a mix? If a mix, are there separate doc sets per audience or one unified set?

4. **Archiving changed features**: When a feature changes significantly, do the old docs get updated in place, archived separately, or is there a "deprecated" convention? (If nothing in the docs suggests this pattern, ask.)

Stop here — do not ask about style, voice, formatting, or structure. Those come from the docs.

---

## Step 4 — Test with real changes

Tell the user:

> "Now I'll test my understanding against some real changes. Give me 2–3 recent changes to your product — anything works: Jira tickets (IDs if you have MCP, or paste the body), PR descriptions, commit messages, diffs, or just describe what changed. A mix is fine."

### Handling each change format

**Jira via MCP**: Fetch the full ticket — summary, description, AC, labels, type.

**Jira ID without MCP or pasted ticket**: Use whatever fields are present. If the body is minimal, work with it — don't ask for more unless the change is completely ambiguous.

**PR description**: Extract: what changed, what it affects, scope signals (breaking/non-breaking, user-facing/internal).

**Commit message**: Use as context. If a diff is also available, prefer the diff for understanding scope — focus on changed strings, UI components, API contracts, and error messages rather than reading every line. Ignore test files, config, and formatting changes unless they surface user-visible behaviour.

**Diff only**: Same as above — extract user-visible impact, ignore noise.

**Plain description**: Take it at face value. If scope is unclear, make a reasonable assumption and flag it.

### For each test change

1. Decide: does this need a doc update, a partial update, or nothing?
2. If nothing: explain why — which rule it fell under (internal change, bug fix policy, no user-facing impact, etc.)
3. If something: identify which section(s) of the existing docs are affected
4. Apply the "doesn't fit anywhere" test — see `references/doc-analysis.md` — before assuming a new section is needed
5. Draft the update: new or revised content, placed in the correct section, written in the doc's voice and style

Show the user: the drafted update (or the "not documenting this because..." explanation), and which section it goes in.

---

## Step 5 — Feedback loop with visible rule updates

After each test draft, ask the user:

> "Does this look right? Tell me anything that's off — wrong section, wrong tone, shouldn't be documented, missing something, etc."

When the user gives feedback, do two things:

**1. Apply the fix** to the draft.

**2. Propose the rule it implies**, explicitly:

> "I'll update my understanding: [proposed rule in plain language]. Does that sound right as a general rule, or was this a one-off?"

Wait for confirmation before treating it as a rule. If the user refines it, use their version.

Keep a running **Rule Log** — updated after each confirmed rule change. Show it to the user as it grows so they can see what the skill is learning.

Example rule log entry:
```
✓ Backend changes that alter user-visible error messages → document under 
  the relevant feature section, not a separate "errors" section
✓ Bug fixes get a one-line note only if the bug was widely reported or 
  caused significant user confusion — otherwise skip
```

After all test changes are done, show the user the complete rule log:

> "Here are all the rules I've derived — from your docs and from our feedback loop. Review and edit anything before I generate your skill."

Let the user edit the rule log directly if they want to. Proceed only when they confirm it's right.

---

## Step 6 — Generate the personalised skill

Output a complete, copy-pasteable `doc-updater.md`. Tell the user:

> "Here's your personalised doc updater. Install it as a Claude skill — then point it at any change and it'll produce a doc update or tell you why nothing needs documenting."

The generated skill must follow this structure, fully populated:

```markdown
---
name: doc-updater
description: Update documentation based on a product change. Trigger when 
given a Jira ticket, PR, commit, diff, or description of a change and asked 
to update, check, or draft docs. Also trigger when asked "does this need 
documenting?" or "what docs need updating for this?"
---

# Doc Updater

Takes a change in any format and either produces a ready-to-apply doc update 
or explains clearly why nothing needs documenting.

## Accepted change formats
- Jira ticket (MCP fetch by ID, or pasted body)
- PR description or commit message
- Git diff (focus on user-visible changes — strings, UI, API contracts, 
  error messages — ignore test files, config, formatting)
- Plain description of what changed

## Doc structure
[map of the doc set — sections, page types, how to navigate it]

## Decision rules
[when to document vs. not, derived from analysis and feedback loop]

## The "doesn't fit anywhere" rule
If a change doesn't map to any existing section:
- Default assumption: it doesn't need documenting here
- Flag it to the user: "This doesn't fit any existing section. I've left 
  it out because [reason]. Let me know if you think it belongs somewhere."
- Only suggest a new section if the change is substantial, clearly 
  user-facing, and genuinely has no home — and frame it as a question, 
  not a decision

## Style rules
[voice, formatting, terminology, heading patterns, step conventions — 
extracted from their docs]

## Output format
For each change, output:
1. Decision: document / partial update / skip (with one-line reason if skip)
2. If documenting: which section(s) are affected
3. The draft content, written in the doc's voice, ready to paste in
4. A "---" followed by anything ambiguous that needs a human decision 
   before applying

## Keeping the skill current
If a change reveals a gap in these rules, note it at the end of your output:
"Note: this change doesn't fit cleanly into existing rules. Consider 
re-running the docs-update-skill setup to update your conventions."
```

Write the generated skill in first person ("my docs", "my team's convention"). It should read like the user wrote it themselves.

Omit any section that doesn't apply — if there's no multi-audience split, don't mention it.
