---
name: honkio-sms
description: Send SMS, verify phones, and buy Canadian numbers through HonkIO without breaking Canadian anti-spam law (CASL). Use whenever the user asks to text someone, verify a phone number, get a Canadian phone number, or check whether they are allowed to message a number.
---

# Sending SMS through HonkIO

The `honkio` MCP server acts on the account whose API key is in `HONKIO_API_KEY`. Before the first call, run `whoami` and tell the user which account and mode the key is for.

## Test versus live

- `mk_test_` keys simulate everything for free. Sends come back `DELIVERED`, but the `mode` field says `TEST` and no phone receives anything. Use them to explore.
- `mk_live_` keys send real SMS and spend real money. Say so before the first live send in a session.

## Before you send a commercial message

1. `check_consent` for the recipient. Commercial messages need CASL consent on record: express consent never expires, implied consent expires two years after the last transaction.
2. If consent is missing, do not pass `skip_consent_check`. Ask the user whether they hold consent outside HonkIO and, if so, `record_consent` with the source they name.
3. If the number has opted out, stop. The recipient must text START or UNSTOP to come back; nothing you can call overrides that.
4. Then `send_sms`. Pass an `idempotency_key` on any retry so a retry can never become a second send or a second charge.

Transactional messages the recipient asked for (a verification code, an appointment reminder they requested) do not need commercial consent, but the opt-out rule still applies.

## Reading a refusal

A refused send returns an error code that says what to fix. `NO_CONSENT` and `CONSENT_EXPIRED` mean record consent first. `OPT_OUT_BLOCKED` means stop. `DAILY_LIMIT_REACHED`, `FANOUT_LIMIT_REACHED`, `NUMBER_RATE_LIMITED`, `RECIPIENT_RATE_LIMITED` and `SENDING_PAUSED` are sending limits: call `get_send_limit` and explain the cap to the user rather than retrying in a loop. `RESERVED_DESTINATION`, `UNDELIVERABLE_NUMBER` and `NOT_A_MOBILE_NUMBER` mean the number itself cannot receive SMS; do not retry it.

## Money

Three tools spend money on a live key. `send_sms` and `start_verification` charge per SMS part, and `provision_phone_number` charges the first month plus a one-time activation fee, then monthly rent until `release_phone_number`. Call `get_pricing` for current figures and confirm with the user before buying a number or sending to more than a handful of recipients. A body over 10 parts is refused before anything is charged; long messages cost more, so keep them short.

## Numbers

Phone numbers are E.164: `+1` and ten digits. Never invent a 555-XXXX number as a placeholder; those exchanges are reserved and a send to one is refused. To get a sending number, `search_phone_numbers` by area code, show the user the options and the price, and only then `provision_phone_number`.

## Verification codes

`start_verification` texts a one-time code; `check_verification` submits what the user received. On a test key the code is always `000000` padded to the requested length.
