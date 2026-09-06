# Optional: automating the mailbox check

This skill is reactive by design - it reacts to a notice, whether you paste one in or it's already sitting in the project's status log. It cannot, by itself, sit and wait for new email; that needs an external scheduler. This is entirely optional. Without it, you just paste in notices as they arrive, or as your normal hosting/GitHub email notifications come in.

If you do want hands-off polling, the important design principle is: **the scheduled job only reads and logs, it never fixes anything or emails the client.** Fixing code and talking to a client are actions with real consequences that deserve a human in the loop at least at review time; a scheduled background job shouldn't take them unattended. Keep that separation even if you're tempted to have the scheduled job "just handle it" - that's exactly the failure mode this skill's Step 4 default (ask before sending) is designed to prevent, and a scheduler that fixes+emails on its own bypasses it entirely.

## Option 1: Claude Code cloud routine (claude.ai/code/routines)

If you have access to Claude Code cloud routines and a Gmail (or similar) MCP connector, create a recurring routine (daily or a few times a week is usually enough) with a prompt along these lines:

```
You're checking the mailbox [mailbox address] for security/hosting notices about
[project name]. Access is via a connected Gmail MCP connector (read-only) - check
your available MCP tools at the start of the run; if the connector isn't available,
stop and say so clearly rather than assuming anything about the inbox.

Search for emails from the last ~24-48 hours from senders associated with
[hosting provider domain(s), GitHub, etc.].

For each one, judge whether it's genuinely security- or action-relevant (real
vulnerability/CVE reports, dependency security alerts, login/2FA security
warnings, suspicious login attempts, expiring certificates, payment/subscription
issues that could affect uptime, critical deploy failures) versus not
(marketing emails, generic activity notifications with no security angle,
routine success confirmations).

If you find anything relevant that isn't already noted in [status_log_path]:
add a short, dated entry there (format: "- **[date] sender/topic**: what was
reported, in 1-2 sentences. (Source: email from X, date)"). Check first that
it isn't already logged - don't duplicate.

Commit and push ONLY changes to [status_log_path], and only if you actually
added something. Do not attempt to fix anything, do not send any email - this
is a read-only monitoring pass. If you found nothing relevant, don't change
anything; a quiet confirmation in your summary is enough.

You have read-only access to the mailbox, access to this repository, and
nothing else. This is a monitoring tool, not an action tool.
```

Fill in the bracketed parts from the project's config. Keep the routine's write access scoped to just the status log file if your tooling allows restricting it - there's no reason for a monitoring job to touch anything else.

## Option 2: generic cron / script (no Claude cloud access needed)

Same idea, implemented as a small script on any scheduler (cron, GitHub Actions on a schedule, etc.):

1. Use the Gmail API, an IMAP client, or whatever the mailbox provider supports to pull messages since the last run from the relevant senders.
2. Apply the same relevant/not-relevant filter (a short keyword/sender allowlist is often enough; an LLM call works too if you want the same judgment quality as Option 1).
3. Append a matching entry to the project's status log file and commit/push it (or open a small PR, if the repo prefers review before anything lands on the default branch).
4. Do nothing else - no fixing, no emailing. Leave those steps to a human running this skill interactively once they see the new log entry.

Either way, the output is the same: a new, dated entry in the project's status log, ready for Step 1 of this skill's workflow.
