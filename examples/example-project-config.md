---
type: security-response-config
---

# Security Response Config: Acme Retail Website

## Client contact
Sam Rivera, owner of Acme Retail (acmeretail.example.com)

## Monitored mailbox(es)
security-notices@acmeretail-hosting.example.com (dedicated inbox, used only for hosting/GitHub account notifications, not customer/business mail)

## Internal status log
`docs/status.md`, section "Security & Hosting Notices"

## Changelog
`docs/changelog.md`

## Client-facing log
`docs/client-updates.md`

## Email template
(using the default from references/email-template.md)

## Email from / to
From: agency@example.com
To: sam@acmeretail.example.com

## Fix delegate
self

## Protected remote/branch
`production` remote, `main` branch - this is the one that actually deploys to the live store. Always confirm separately before pushing here.

## Auto-send client email
false

## Relevance scope
Anything affecting the live storefront or checkout: dependency vulnerabilities in runtime packages, broken checkout/contact forms, expiring SSL certificates, suspicious account logins. Dev-tooling-only findings (e.g. a lint config vulnerability with no runtime impact) and pure design/copy changes stay internal-only, no client email.
