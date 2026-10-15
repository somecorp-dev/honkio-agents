# HonkIO for AI agents

Install HonkIO, the Canadian SMS API, into your agent of choice. Everything here talks to the same API with your API key, which you create at https://honkio.ca/dashboard/keys (API Keys → Create key; the full key is shown once, right after you create it). Start with a test key (`mk_test_...`): it works before your first top-up, and sends and verifications are simulated for free. Tools that buy or release numbers, change webhooks or run erasure need a live key.

## Google Antigravity

```sh
agy plugin install https://github.com/somecorp-dev/honkio-agents
```

Then open Agent settings, go to Customizations and press Authenticate next to `honkio`: a browser window opens on honkio.ca where you approve the connection and choose live or test mode. No key to paste. The plugin connects to the hosted endpoint at `https://mcp.honkio.ca/mcp` and adds the same SMS and email skills as the Claude Code plugin.

## Gemini CLI

Google stopped serving Gemini CLI for personal accounts on 2026-06-18 and moved them to Antigravity (above). Gemini Code Assist Standard and Enterprise users, and anyone using a paid Gemini API key, can still use the extension:

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

## ChatGPT and Codex

`openai-plugin/` is the package submitted to OpenAI's plugin directory. It signs in with OAuth against the same hosted endpoint and carries the same skills.

## Everything else

Cursor, VS Code, the Claude Messages API, the OpenAI Responses API and any other MCP client: see https://honkio.ca/docs/mcp for one-click links and config snippets, or the package README at https://www.npmjs.com/package/@honkio/mcp.

## Layout

- `plugin.json`, `mcp_config.json` and `skills/`: the Google Antigravity plugin (this repo's root, which `agy plugin install` reads).
- `gemini-extension.json` and `GEMINI.md`: the Gemini CLI extension (also the root, as its gallery requires).
- `.claude-plugin/marketplace.json` and `claude-code-plugin/`: a one-plugin Claude Code marketplace.
- `openai-plugin/`: the ChatGPT and Codex plugin.

The skills are copied into each plugin, because each package must carry its own. The copies in `claude-code-plugin/skills` are the ones to edit; a test in the HonkIO repository fails until the others match.

Source of truth lives in the `integrations/agents` directory of the HonkIO repository and is pushed here on each release.
