---
name: security-notice-response
description: Handles the full workflow when a security or malfunction notice arrives about a client website/project you maintain - a hosting provider's vulnerability scan, an npm/GitHub security advisory, an SSL/domain warning, or a report of a broken feature (contact form, checkout, login). Covers triage through finished client communication: assess severity, fix or delegate, update the project's status/changelog docs, draft (or, if configured, send) a client update email, and log it. Use it as soon as the user pastes, forwards, or summarizes such a notice, or says things like "got a security alert for client X" or "hosting flagged some vulnerabilities on the site", even if they don't name the skill. Works across any number of client projects, each with its own small config file - if none exists yet for the project at hand, this skill's first job is to help create one.
---

# Security Notice Response

A repeatable workflow for people who maintain live websites/apps for clients: something (a hosting scan, a dependency advisory, a broken-feature report) shows up, and you need to figure out what it means, fix it if needed, keep an internal record, and tell the client in plain language - without forgetting any of those steps or reinventing the wording every time.

This skill is deliberately config-driven. It has no built-in knowledge of any specific client, mailbox, subagent, or file layout - all of that lives in a small **project config** file you create once per client project (see `references/project-config.md`). The workflow below always starts by loading that config.

## Step 0: Find or create the project config

Look for a project config file for the project currently being discussed. Convention: a file named `security-response.config.md` in the project's root, or wherever the user says they keep it (some users prefer a subfolder, e.g. `docs/security-response.config.md`). If the user has already told you a path in an earlier session, reuse it.

If no config exists yet: read `references/project-config.md` for the schema, interview the user briefly (project name, client contact, which mailbox/notices this covers, where their status/changelog/client-log files live, the actual **from and to email addresses** for client updates, whether a subagent should handle fixes, whether any git remote/branch needs an extra confirmation before pushing, and - important - whether they want client emails sent automatically or drafted for approval), then write the config file. Don't guess at defaults for anything client-communication-related, and don't leave the email addresses as placeholders - a config with a bracketed `[fill in]` where an address should be isn't finished; ask until you have real values.

Everything after this step assumes the config exists and has been read.

## Step 1: Detect and triage the notice

A notice reaches you one of two ways:

- **Manual**: the user pastes, forwards, or describes an email or alert.
- **Automated** (only if the user has set up the optional polling described in `references/automation-setup.md`): a scheduled job already found the notice and appended a short entry to the project's status log. In that case, treat a new, not-yet-resolved entry in that log as the trigger - you don't need the original email.

Either way, work out and state plainly:

- What's actually being reported (vulnerable package, CVE/GHSA IDs if any, suspicious login, expiring certificate, broken form, etc.)
- How severe it really is - a runtime dependency in a security advisory is not the same urgency as a dev-tooling-only one; a scan flagging six packages is not automatically six separate incidents
- Whether it affects the live/production site at all, or only a dev/staging environment
- Whether the notice itself looks legitimate - unsolicited "verify your account" or "security alert" emails claiming to be from a host or registrar are a common phishing vector; if something feels off, say so and stop here rather than acting on it

If the project's status log doesn't have an entry for this yet, add a short, dated one now, even before the fix is done - so there's a record even if the fix has to wait.

## Step 2: Fix

- If the config names a **Fix delegate** (a subagent or a specific person/process), hand it off there with the full context from Step 1 - not just "fix this". Otherwise, implement the fix yourself.
- If the config names a **Protected remote/branch** (the thing that actually pushes to the live/production site), stop **before** running that push and ask for it specifically - a generic "ok, commit and push" earlier in the conversation does not count, and neither does noting the push in a changelog entry after the fact. Get the explicit go-ahead first, then push, then document what happened in Step 3. This mirrors ordinary safe-push discipline but is worth restating because it's easy to bundle a security push into a broader "yes go ahead," or to treat "I'll mention it in the changelog" as if it were the confirmation itself.
- Verify before calling it done: run whatever the project's normal checks are (build, lint, audit, tests, a security review pass) - don't rely on "the fix compiled."

## Step 3: Document internally

Add an entry to the file named under **Internal status log** and, if the config has one, **Changelog** - what was reported, what was done, how it was verified, where it ended up (branch, deploy). Keep the internal record technical; the client-facing version comes next and is written differently.

## Step 4: Notify the client

Only for notices that actually reached the live site and were security- or function-relevant - not for pure dev-tooling findings, cosmetic issues, or anything still sitting on a non-production branch. If it's a genuine borderline case, say so and ask rather than guessing.

Draft the email using `references/email-template.md` (or a template the config points to instead). Write it in plain, non-technical language aimed at the client, not a copy-paste of the internal changelog entry - the point is reassurance and clarity, not a vulnerability report.

**Default behavior: show the draft and ask before sending.** Only send without asking if the project config explicitly sets **Auto-send client email** to true. That flag should only ever be turned on because the user made a deliberate call for that specific project - it is not something to enable on someone's behalf, and it is not the default for a new project config.

## Step 5: Log the client-facing summary

Add a short, non-technical entry to the file named under **Client-facing log** (if the config has one) - what happened, in the client's own terms, plus a line noting the email was sent (or drafted and is awaiting approval) with date and recipients. This becomes the searchable history of what the client was told, separate from the technical changelog.

## Edge case: informational notices with nothing to do

Not everything needs the full workflow. A "confirm this login was you" email, a routine renewal notice, or a scan result that turns out to be a false positive doesn't need a fix, a client email, or a changelog entry - just a short status-log note (and, if something looks like it needs the user's own judgment, like confirming a login was really them, flag that clearly).
