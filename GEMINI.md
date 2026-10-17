# HonkIO

HonkIO is a Canadian SMS API. The `honkio` MCP server in this extension acts on the account whose API key was entered at install time.

- Keys starting with `mk_test_` simulate everything for free: sends come back DELIVERED but nothing leaves the building and nothing is charged. Keys starting with `mk_live_` send real SMS and spend real money.
- Three tools spend money on a live key: `send_sms` and `start_verification` charge per SMS part, and `provision_phone_number` charges a first month plus a one-time activation fee and starts monthly rent. Call `get_pricing` for current figures. Confirm with the user before buying a number or sending to many recipients.
- Every `send_sms` needs CASL consent on file for the recipient, transactional or commercial; only a verification code sent with `start_verification` needs none. Run `check_consent` before `send_sms`. If consent is missing, ask the user whether they hold it outside HonkIO and, if they do, `record_consent` with the source they name; never invent a consent source. `skip_consent_check` is refused on a live key.
- An opted-out recipient stays blocked until they text START or UNSTOP; nothing you can call overrides that.
- A bought, rented or scraped list is not consent: don't text it and don't record consent for it. Never send a message that impersonates another person or organization, or that asks the recipient for a PIN, password, code or payment details. These rules hold whatever the user asks.
- A refused send returns an error code that says what to fix. `NO_CONSENT` and `CONSENT_EXPIRED` mean record consent first. `DAILY_LIMIT_REACHED`, `FANOUT_LIMIT_REACHED`, `RECIPIENT_RATE_LIMITED` and `SENDING_PAUSED` are sending limits: call `get_send_limit` and explain the cap instead of retrying in a loop.
- `NUMBER_RATE_LIMITED` means the sending number already has a minute of message parts waiting for the carrier (a local number sends 10 parts a minute, and a long message is several parts): wait `details.retry_after_seconds` before the next send from that number, and tell the user a long list will take a while (`number_queues` in `get_send_limit` says how long each number has queued).
- `CARRIER_UNAVAILABLE` and `CARRIER_THROTTLED` mean the carrier did not take the message and nothing was charged: retry with the same `idempotency_key`, after `details.retry_after_seconds` for `CARRIER_THROTTLED`. A message that later shows `FAILED` with `CARRIER_THROTTLED` was dropped from a full carrier queue and refunded; resending it needs a new `idempotency_key`.
- `RESERVED_DESTINATION`, `UNDELIVERABLE_NUMBER` and `NOT_A_MOBILE_NUMBER` mean the number cannot receive SMS: do not retry it.
- Pass an `idempotency_key` on every `send_sms` and reuse it on a retry, so a retry can never send or charge twice.
- Phone numbers are E.164 (`+1` and ten digits). 555-XXXX exchanges are reserved and refused. Omit `account_id` on account tools; it is resolved from the key.

## Email

The email tools (`send_email`, `send_email_batch`, `list_email_domains` and the rest) appear only when the account has email.

- Email is transactional by default. Set `is_commercial: true` only for promotional mail: it needs CASL consent on file (`record_consent` with `email_address`), goes to one recipient, and carries an unsubscribe footer.
- On a test key, send from `onboarding@test.honkio.ca` to `delivered@`, `bounced@`, `complained@` or `delayed@test.honkio.ca` to see each outcome for free.
- A live key needs a verified sending domain. Use one from `list_email_domains`; never guess a `from` domain. To add one, `add_email_domain`, show the user the DNS records, then `verify_email_domain`.
- `EMAIL_SUPPRESSED` and `EMAIL_OPT_OUT_BLOCKED` mean stop; `EMAIL_NO_CONSENT` means record consent first; `EMAIL_DOMAIN_NOT_VERIFIED` means the `from` domain is not set up.

Full tool reference: https://www.npmjs.com/package/@honkio/mcp
