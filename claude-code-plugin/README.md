# HonkIO

Send SMS and email in Canada from Claude: text customers, send receipts and notices by email, verify phone numbers with one-time codes, buy Canadian numbers, and keep the CASL consent records Canadian anti-spam law requires.

## What it adds

- **The HonkIO MCP server**, connected to HonkIO's hosted endpoint at `https://mcp.honkio.ca/mcp`. It has tools for SMS, email, phone verification, phone numbers, CASL consent and opt-outs, webhooks, and account usage and pricing.
- **Two skills**, `honkio-sms` and `honkio-email`, that guide Claude through sending compliantly: when consent is needed, how test mode differs from live, and what each refusal means.

The plugin runs nothing on your machine: no hooks, no scripts and no local server.

## Signing in

After installing, run `/mcp` and pick `honkio`. A browser window opens on honkio.ca, where you sign in to your HonkIO account, approve the connection and choose test or live mode. A live connection also asks for your account password. Each connection appears on the API keys page of the HonkIO dashboard and can be revoked there.

Test mode is free: SMS, email and verification sends are simulated with the same compliance checks as live sends, and nothing is delivered or charged. A live connection sends real messages and spends your account's prepaid credit.

## Data

The plugin talks only to `https://mcp.honkio.ca` and, for sign-in, `https://api.honkio.ca` and `https://honkio.ca`. It sends the tool calls Claude makes, such as a message body and its recipient, to HonkIO, which stores account data in Canada. Text other people send you, such as an SMS reply, comes back marked as untrusted content.

- Privacy policy: https://honkio.ca/legal/privacy
- Documentation: https://honkio.ca/docs/mcp
- Support: support@honkio.ca

## License

MIT
