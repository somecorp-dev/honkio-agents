# HonkIO for AI agents

Install HonkIO, the Canadian SMS API, into your agent of choice. Everything here talks to the same API with your API key, which you get at https://honkio.ca/dashboard/keys. Start with a test key (`mk_test_...`): it works before your first top-up, and sends and verifications are simulated for free. Tools that buy or release numbers, change webhooks or run erasure need a live key.

## Gemini CLI

```sh
gemini extensions install https://github.com/somecorp-dev/honkio-agents
```

Gemini CLI prompts for your API key once and keeps it in the system keychain. The extension runs the `@honkio/mcp` package with `npx`, so Node.js 20 or newer must be on your machine (Gemini CLI itself needs it too).

## Claude Code

```sh
/plugin marketplace add somecorp-dev/honkio-agents
/plugin install honkio@honkio
```

Then run `/mcp` and pick `honkio` to sign in: a browser window opens on honkio.ca where you approve the connection and choose live or test mode. A live connection also asks for your account password. No key to paste. The plugin connects to the hosted endpoint at `https://mcp.honkio.ca/mcp` and adds skills that walk Claude through CASL-compliant SMS and email.

## Everything else

Cursor, VS Code, the Claude Messages API, the OpenAI Responses API and any other MCP client: see https://honkio.ca/docs#mcp for one-click links and config snippets, or the package README at https://www.npmjs.com/package/@honkio/mcp.

## Layout

- `gemini-extension.json` and `GEMINI.md`: the Gemini CLI extension (this repo's root, as the gallery requires).
- `.claude-plugin/marketplace.json` and `claude-code-plugin/`: a one-plugin Claude Code marketplace.

Source of truth lives in the `integrations/agents` directory of the HonkIO repository and is pushed here on each release.
