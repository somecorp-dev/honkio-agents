---
name: honkio-email
description: Send transactional or commercial email through HonkIO without breaking Canadian anti-spam law (CASL), and set up a sending domain. Use whenever the user asks to email someone, send a receipt, reset or notification by email, set up an email domain, or check why an email bounced or was refused.
---

# Sending email through HonkIO

The `honkio` MCP server shows the email tools (`send_email`, `list_email_domains` and the rest) only when the account has email. If they are missing, email is not on for this account: say so, and don't try to send email another way. Before the first call, run `whoami` and tell the user which account and mode it is for.

## Transactional or commercial

- **Transactional** (the default): receipts, password resets, sign-in links, alerts, account and shipping notices. No consent record needed; leave `is_commercial` out.
- **Commercial**: anything that promotes a product, service, sale or event, including a newsletter. Set `is_commercial: true`. It must go to exactly one recipient, needs CASL consent on file for that address, and gets an unsubscribe footer automatically.
- When unsure, ask the user. Never mark a promotional message transactional to get around consent.

## Record consent first

For a commercial email, check and record consent before sending:

1. `check_consent` with `email_address`.
2. If there is none, ask the user whether the person opted in and how. Only then `record_consent` with `email_address`, `consent_type: "express"` and a `source_description` naming where they opted in (for example "Newsletter checkbox on acme.ca/signup, 2026-09-02"). Implied consent needs `relationship_type` and lasts two years from `last_transaction_date`.
3. Never invent a consent source.

## Test before live

- On a test key (`mk_test_...`), send from `onboarding@test.honkio.ca` with no domain set up. Nothing is delivered or charged.
- `delivered@test.honkio.ca` ends delivered; `bounced@` bounces and suppresses the address; `complained@` records a spam complaint; `delayed@` is delayed, then delivered. Add a label (`bounced+2@test.honkio.ca`) for a fresh address after a suppression.
- A live key (`mk_live_...`) delivers real email and is billed per recipient (`get_pricing`). Say so before the first live send in a session, and confirm before sending to more than a handful of people.

## Setting up a domain

1. `list_email_domains`. Use an existing verified domain if there is one.
2. Otherwise ask the user which domain or subdomain they control (a subdomain such as `mail.example.com` is best), then `add_email_domain`.
3. Show the user every record in the response: the DKIM TXT record, and the MX and SPF records on the `send.` subdomain. They publish them at their DNS host; you cannot.
4. `verify_email_domain`. `missing` lists records not found yet; DNS can take time, so check again later rather than looping.
5. `EMAIL_DOMAIN_IN_USE` means another account holds the domain; `details.retry_after` says when an unverified claim lapses.

## Never guess a from domain

The `from` address must be on a verified domain from `list_email_domains` (or `onboarding@test.honkio.ca` on a test key). Never make one up from the user's website or company name: the send is refused with `EMAIL_DOMAIN_NOT_VERIFIED`, and a guessed domain may belong to someone else.

## Reading a refusal

- `EMAIL_NO_CONSENT`: commercial with no consent on file; record it first, or send it as transactional if it truly is.
- `EMAIL_SUPPRESSED`: the address bounced, complained or unsubscribed. Stop. Only `remove_email_suppression` if the user confirms the person asked to hear from them again.
- `EMAIL_OPT_OUT_BLOCKED`: the recipient unsubscribed from this sender. Stop.
- `EMAIL_COMMERCIAL_MULTI_RECIPIENT`: a commercial email to several people; send one per recipient with `send_email_batch`.
- `EMAIL_DOMAIN_NOT_VERIFIED`: the `from` domain is not verified here; see "Setting up a domain".
- `EMAIL_TEST_ADDRESS_LIVE_KEY`: a `test.honkio.ca` address on a live key; use a real recipient.
- `EMAIL_TEMPLATE_VARIABLE_MISSING` or `EMAIL_TEMPLATE_NOT_PUBLISHED`: fix the variables, or ask the user to publish the template in the dashboard.
- `EMAIL_DOMAIN_NOT_ALLOWED`: this API key is restricted to certain sending domains and the `from` address isn't on one of them; use a domain the key is allowed for.
- `EMAIL_NOT_SCHEDULED`: the email already went out or was cancelled.
- `EMAIL_IDEMPOTENCY_IN_PROGRESS`: a request with that `idempotency_key` is still being processed; wait and retry the same key rather than switching to a new one.

Pass the same `idempotency_key` on any retry so a retry can never send or charge twice.

## Templates

`list_email_templates` and `get_email_template` show what exists; send one with `send_email` and `template: { id, variables }`. Creating, editing and publishing templates happens in the dashboard, not through these tools.
