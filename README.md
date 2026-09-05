# HonkIO for AI agents

Install HonkIO, the Canadian SMS API, into your agent of choice. Everything here talks to the same API with your API key, which you get at https://honkio.ca/dashboard/keys. Start with a test key (`mk_test_...`): every tool works and nothing is sent or charged.

## Gemini CLI

```sh
gemini extensions install https://github.com/jeffcaldwellca/honkio-agents
```

Gemini CLI prompts for your API key once and keeps it in the system keychain. The extension runs the `@honkio/mcp` package with `npx`, so Node.js 20 or newer must be on your machine (Gemini CLI itself needs it too).

## Claude Code

```sh
/plugin marketplace add jeffcaldwellca/honkio-agents
/plugin install honkio@honkio
```

Then export `HONKIO_API_KEY` in your shell. The plugin connects to the hosted endpoint at `https://mcp.honkio.ca/mcp` and adds a skill that walks Claude through a CASL-compliant send.

## Everything else

Cursor, VS Code, the Claude Messages API, the OpenAI Responses API and any other MCP client: see https://honkio.ca/docs#mcp for one-click links and config snippets, or the package README at https://www.npmjs.com/package/@honkio/mcp.

## Layout

- `gemini-extension.json` and `GEMINI.md`: the Gemini CLI extension (this repo's root, as the gallery requires).
- `.claude-plugin/marketplace.json` and `claude-code-plugin/`: a one-plugin Claude Code marketplace.

Source of truth lives in the `integrations/agents` directory of the HonkIO repository and is pushed here on each release.
