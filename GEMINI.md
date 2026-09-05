# HonkIO

HonkIO is a Canadian SMS API. The `honkio` MCP server in this extension acts on the account whose API key was entered at install time.

- Keys starting with `mk_test_` simulate everything for free: sends come back DELIVERED but nothing leaves the building and nothing is charged. Keys starting with `mk_live_` send real SMS and spend real money.
- Three tools spend money on a live key: `send_sms` and `start_verification` charge per SMS part, and `provision_phone_number` charges a first month plus a one-time activation fee and starts monthly rent. Call `get_pricing` for current figures. Confirm with the user before buying a number or sending to many recipients.
- Commercial messages need CASL consent on file. Use `check_consent` before `send_sms`; `record_consent` stores express or implied consent. A refused send returns an error code such as `NO_CONSENT`, `OPT_OUT_BLOCKED` or `DAILY_LIMIT_REACHED` that says what to fix.
- Phone numbers are E.164 (`+1` and ten digits). 555-XXXX exchanges are reserved and refused. Omit `account_id` on account tools; it is resolved from the key.

Full tool reference: https://www.npmjs.com/package/@honkio/mcp
