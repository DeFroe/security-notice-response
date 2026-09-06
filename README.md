# Security Notice Response

A [Claude Code skill](https://docs.claude.com/en/docs/claude-code/skills) for people who maintain live websites or apps for clients. Hosting providers, npm/GitHub, and registrars all send a steady stream of security scans, dependency advisories, and account notifications - and it's easy for them to either get ignored, or get fixed quietly with the client never finding out anything happened. This skill turns "notice arrived" into a repeatable path: triage it, fix it (or delegate the fix), record it internally, and tell the client in plain language - without reinventing the process, or the wording, every time.

It works across any number of client projects. Each project gets one small config file describing its own paths, contacts, and rules; the skill itself carries no client-specific assumptions.

## Install

Clone this repo directly into your Claude Code skills folder:

```bash
git clone https://github.com/<your-username>/security-notice-response ~/.claude/skills/security-notice-response
```

Or download it and copy the folder contents into `~/.claude/skills/security-notice-response/` yourself. Either way, restart/reopen Claude Code so it picks up the new skill.

## Quick start

1. Open a session in the client project you want to set this up for.
2. Paste in (or describe) the first notice you want handled - e.g. "hosting flagged 6 vulnerabilities on the Acme Retail site." If there's no project config yet, the skill will notice and walk you through creating one (see `references/project-config.md` for the full schema, `examples/example-project-config.md` for a filled-in sample).
3. From there it runs the workflow: triage → fix (or hand off, if you configured a delegate) → internal documentation → a client-facing email draft → a log entry.

By default, client emails are **drafted for your approval**, never sent automatically. If you want a specific project to send without asking, set `auto_send_email: true` in that project's config - that's a deliberate per-project choice, not something to turn on lightly.

## Optional: automate the mailbox check itself

This skill reacts to notices - it doesn't sit and watch an inbox on its own. If you want that too, `references/automation-setup.md` covers two ways to set up hands-off polling (a Claude Code cloud routine, or a generic cron job/script), both built around the same rule: the automated part only reads and logs, it never fixes anything or emails a client unattended. Those actions stay with you, in an interactive session, at least at review time.

## What's in this repo

- `SKILL.md` - the workflow itself, loaded by Claude Code when this skill is relevant
- `references/project-config.md` - the schema for a project's config file
- `references/email-template.md` - the default client email template
- `references/automation-setup.md` - optional inbox-polling setup
- `examples/example-project-config.md` - a filled-in sample config

## License

MIT - see [LICENSE](LICENSE).
