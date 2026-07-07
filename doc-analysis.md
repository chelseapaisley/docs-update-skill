# Doc Analysis Reference

This file is read by the setup skill during Step 2 (doc analysis) and referenced in the generated skill's "doesn't fit anywhere" rule.

---

## Full extraction guide

When analysing a doc set, extract the following as **rules**, not observations. Write "Steps are always numbered" not "Steps tend to be numbered."

### Structure map

Extract the information architecture:
- What are the top-level sections? (e.g. Getting Started, Features, Troubleshooting, API Reference)
- What page types exist? (e.g. overview pages, step-by-step guides, reference tables, release notes)
- How are pages related — flat list, nested hierarchy, hub-and-spoke?
- Is there a changelog or release notes section? What's its format?
- Are there any pages that seem to function as indexes or tables of contents?

Output this as a navigable map, not a prose description — it needs to be usable by the generated skill when deciding where a doc update belongs.

### Style rules

Extract each of the following as a concrete rule:

**Voice**
- Second person ("you can enable...") or third person ("users can enable...")?
- Active or passive voice dominant?
- How is the product referred to — "the app", "the platform", by name?
- How are UI elements referred to — bold (`**Save**`), code (`` `Save` ``), quoted ("Save"), or plain?

**Tone**
- Formal or conversational?
- Are contractions used?
- How are warnings or cautions handled — callout blocks, bold, inline?

**Headings**
- Sentence case or title case?
- Imperative verb ("Configure your settings") or noun phrase ("Settings configuration") or question ("How do I configure settings?")?
- Are H2s always present? H3s used for sub-steps or sub-features?

**Steps and lists**
- Are sequential steps always numbered?
- Are non-sequential items always bulleted?
- Maximum nesting depth for lists?
- Do steps include the expected outcome ("Click Save. The settings panel closes.")?

**Terminology**
- Are there product-specific terms used consistently? (e.g. "workspace" not "project", "member" not "user")
- Are feature names capitalised?
- Are there terms conspicuously avoided?

**Length and density**
- Typical paragraph length
- Do pages have intros before getting into steps?
- Are there summary sections or TLDRs?

### Content patterns

Infer from the content what kinds of changes appear to have generated doc updates. Look for:
- Pages that describe specific features in detail (suggests features get documented)
- Release notes or changelog entries (suggests what level of change is noted)
- Known patterns like "Added", "Updated", "Fixed" in any changelog (suggests bug fix policy)
- Pages that are thin or placeholder-like (suggests some areas are under-documented)
- Any explicit "this doc covers X" or "out of scope" statements

Do not invent signals that aren't there. If the docs don't reveal the content policy, flag it for the interview.

### Gaps and ambiguities

Flag anything that couldn't be determined:
- User-facing line (if no clear signal from content)
- Bug fix policy (if no changelog or release notes)
- Audience (if not stated and not obvious from tone/terminology)
- Archiving/deprecation convention (if no examples exist)

---

## The "doesn't fit anywhere" test

Used during setup (Step 4) and embedded into the generated skill.

When a change doesn't map cleanly to an existing section, run this test in order:

**1. Is it user-facing?**
If not — or if genuinely unclear — default to "not documenting" and flag to the user. Internal changes, refactors, test changes, config changes, and dependency updates are almost never documented unless they produce user-visible effects.

**2. Does it affect an existing documented feature?**
If yes, the update belongs in that feature's section even if the change is indirect (e.g. a performance improvement to a feature the user interacts with can be noted there, briefly).

**3. Is it a new concept or capability the user will encounter?**
If yes, look harder for a home — it may belong under an existing parent section (e.g. a new filter option belongs under the search/filter section, not in a new "Filters" section).

**4. Still no home?**
Flag it: "This change doesn't map to any existing section. I've left it out — it may not need documenting here, or it may signal a gap in the doc structure. Your call."

Only suggest a new section if:
- The change introduces a substantial new area of the product
- It's clearly user-facing
- There is genuinely no existing section it belongs under
- And even then: propose it as a question, not a decision

The bar for a new section is high. Most "doesn't fit" cases are "doesn't need documenting" cases.

---

## Diff reading guide

When a diff is provided as the change source:

**Focus on:**
- Changed or added strings visible to users (UI labels, button text, error messages, notifications, email content)
- Changes to API contracts (new endpoints, changed parameters, deprecated fields)
- Changes to user-facing component logic (new states, changed behaviour on interaction)
- New feature flags that enable user-visible functionality

**Ignore:**
- Test file changes
- Code formatting / linting changes
- Dependency version bumps (unless they change a user-visible behaviour)
- Internal logging or monitoring changes
- Config file changes (unless they expose new user-configurable options)
- Comment changes

If the diff is very large (100+ files or 500+ lines), focus on the signal files — components, templates, API route definitions, string/copy files — and note to the user that you've focused your analysis there.
