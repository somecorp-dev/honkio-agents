# HonkIO

HonkIO is a Canadian SMS API. The `honkio` MCP server in this extension acts on the account whose API key was entered at install time.

- Keys starting with `mk_test_` simulate everything for free: sends come back DELIVERED but nothing leaves the building and nothing is charged. Keys starting with `mk_live_` send real SMS and spend real money.
- Three tools spend money on a live key: `send_sms` and `start_verification` charge per SMS part, and `provision_phone_number` charges a first month plus a one-time activation fee and starts monthly rent. Call `get_pricing` for current figures. Confirm with the user before buying a number or sending to many recipients.
- Commercial messages need CASL consent on file. Use `check_consent` before `send_sms`; `record_consent` stores express or implied consent. A refused send returns an error code such as `NO_CONSENT`, `OPT_OUT_BLOCKED` or `DAILY_LIMIT_REACHED` that says what to fix.
- Phone numbers are E.164 (`+1` and ten digits). 555-XXXX exchanges are reserved and refused. Omit `account_id` on account tools; it is resolved from the key.

## Email

The email tools (`send_email`, `send_email_batch`, `list_email_domains` and the rest) appear only when the account has email.

- Email is transactional by default. Set `is_commercial: true` only for promotional mail: it needs CASL consent on file (`record_consent` with `email_address`), goes to one recipient, and carries an unsubscribe footer.
- On a test key, send from `onboarding@test.honkio.ca` to `delivered@`, `bounced@`, `complained@` or `delayed@test.honkio.ca` to see each outcome for free.
- A live key needs a verified sending domain. Use one from `list_email_domains`; never guess a `from` domain. To add one, `add_email_domain`, show the user the DNS records, then `verify_email_domain`.
- `EMAIL_SUPPRESSED` and `EMAIL_OPT_OUT_BLOCKED` mean stop; `EMAIL_NO_CONSENT` means record consent first; `EMAIL_DOMAIN_NOT_VERIFIED` means the `from` domain is not set up.

Full tool reference: https://www.npmjs.com/package/@honkio/mcp
