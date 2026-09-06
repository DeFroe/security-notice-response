# Project config schema

Each client project using this skill needs one small config file, conventionally named `security-response.config.md`, living in that project's repo (root, or wherever the user tells you). Plain Markdown, not YAML - it's meant to be readable in any editor or Obsidian-style vault, and easy for a human to hand-edit without worrying about indentation or quoting.

Use this template when creating a new one. Every field is a short heading followed by its value; omit a field entirely if it doesn't apply (don't write "none" unless the field explicitly allows it).

```markdown
---
type: security-response-config
---

# Security Response Config: [Project Name]

## Client contact
[Name and/or company the client-facing email should address]

## Monitored mailbox(es)
[Address(es) that receive hosting/security/error notices for this project - informational unless automated polling is set up, see references/automation-setup.md]

## Internal status log
[Path to the file where technical status entries go, e.g. `docs/status.md`. State the section/heading within it to use, if any.]

## Changelog
[Path to a more detailed technical changelog, if this project keeps one separately from the status log. Omit this field if there isn't one - status log entries are enough.]

## Client-facing log
[Path to the non-technical, client-readable history file, e.g. `docs/client-updates.md`. Omit if the project doesn't keep one - client emails are still sent, just not logged separately.]

## Email template
[Path to a project-specific email template, if the user wants to override the bundled references/email-template.md. Omit to use the default.]

## Email from / to
From: [sender address]
To: [recipient address(es)]

## Fix delegate
[Name of a subagent, team member, or process that should handle the actual code/config fix, or "self" if there's no delegation - just do it directly]

## Protected remote/branch
[The git remote and/or branch that actually deploys to the live production site, if pushing there needs its own explicit confirmation separate from a general "go ahead". Omit if there's no such distinction for this project.]

## Auto-send client email
[true or false - default false/unset. Only set to true if the user has explicitly said they want client emails sent without a confirmation step for this project.]

## Relevance scope
[Free text: what counts as "security- or function-relevant enough to notify the client" for this project vs. what's cosmetic/dev-only and stays internal-only. A sentence or two is enough - this is what the skill checks against in Step 4.]
```

## Notes on filling it in

- **Client contact** and **relevance scope** are worth spending a moment on during setup - they're the two fields that most affect tone and judgment calls later, and are easy to get wrong if rushed.
- If a project has no separate changelog or client-facing log, that's fine - just omit those sections. Not every project needs three separate files; a single status log with two kinds of entries works too, in which case say so in **Internal status log** (e.g. "single file, tag client-facing entries with `[client]`").
- Leave **Auto-send client email** unset (or explicitly `false`) unless the user has clearly decided they're comfortable with unattended client communication for this specific project. This is a per-project decision, not a one-time global preference - a user might want auto-send for a low-stakes internal tool and manual approval for a paying client's storefront.
