# Security Notice Response

A [Claude Code skill](https://docs.claude.com/en/docs/claude-code/skills) for people who maintain live websites or apps for clients. Hosting providers, npm/GitHub, and registrars all send a steady stream of security scans, dependency advisories, and account notifications - and it's easy for them to either get ignored, or get fixed quietly with the client never finding out anything happened. This skill turns "notice arrived" into a repeatable path: triage it, fix it (or delegate the fix), record it internally, and tell the client in plain language - without reinventing the process, or the wording, every time.

It works across any number of client projects. Each project gets one small config file describing its own paths, contacts, and rules; the skill itself carries no client-specific assumptions.

## Install

Clone this repo directly into your Claude Code skills folder:

```bash
git clone https://github.com/DeFroe/security-notice-response ~/.claude/skills/security-notice-response
```

Or download it and copy the folder contents into `~/.claude/skills/security-notice-response/` yourself. Either way, restart/reopen Claude Code so it picks up the new skill.

## Quick start

1. Open a session in the client project you want to set this up for.
2. Paste in (or describe) the first notice you want handled - e.g. "hosting flagged 6 vulnerabilities on the Acme Retail site." If there's no project config yet, the skill will notice and walk you through creating one - a single Markdown file, `security-response.config.md`, in that project's repo (see `references/project-config.md` for the full schema, `examples/example-project-config.md` for a filled-in sample).
3. From there it runs the workflow: triage → fix (or hand off, if you configured a delegate) → internal documentation → a client-facing email draft → a log entry.

By default, client emails are **drafted for your approval**, never sent automatically. If you want a specific project to send without asking, set **Auto-send client email** to true in that project's config - that's a deliberate per-project choice, not something to turn on lightly.

## Optional: automate the mailbox check itself

This skill reacts to notices - it doesn't sit and watch an inbox on its own. If you want that too, `references/automation-setup.md` covers two ways to set up hands-off polling (a Claude Code cloud routine, or a generic cron job/script), both built around the same rule: the automated part only reads and logs, it never fixes anything or emails a client unattended. Those actions stay with you, in an interactive session, at least at review time.

## Background

This started as a routine, not as an idea. Maintaining a live website for a client means a steady trickle of hosting scans, dependency advisories and the occasional "something on the site is broken" report - and every one of them ran through the same four steps: work out how serious it actually is, fix it, write it down internally, then tell the client in language they'd actually understand. Same steps, same order, every time. The client email was reliably the part that got put off the longest, even though it's the part the client sees.

Once you've done that often enough, turning it into a skill is the obvious next move - not to automate the judgment calls, but to stop re-deciding the process and re-inventing the wording every time. Before release it was run end to end against two simulated client projects. Those runs surfaced two gaps worth fixing: a project config that let placeholder email addresses slip through into a client-facing draft, and a production push that could be waved through on an earlier, generic "go ahead" instead of its own confirmation. Both are fixed.

## No warranty

The MIT license below covers this in legal terms; in plain ones: this skill edits files in your repositories, can push to your remotes, and - if you configure it that way - can send email to your clients under your name. None of that removes the need to read what it produces before it goes out. Review the diffs, review the drafts. Anything that reaches a client is yours, not the skill's.

## What's in this repo

- `SKILL.md` - the workflow itself, loaded by Claude Code when this skill is relevant
- `references/project-config.md` - the schema for a project's config file
- `references/email-template.md` - the default client email template
- `references/automation-setup.md` - optional inbox-polling setup
- `examples/example-project-config.md` - a filled-in sample config

## License

MIT - see [LICENSE](LICENSE).
