# Woodstitch plugin for Claude Code and Codex

Blinded human listening tests on your text-to-speech audio, from inside Claude Code, Codex or ChatGPT.
Paid US listeners compare clips in pairs (which do they prefer? how much better? — preference and CMOS)
or score them one at a time (MOS naturalness, or human or AI?), and you get results with 95% confidence intervals back.

The plugin connects Claude to the hosted Woodstitch MCP server at `https://api.woodstitch.com/mcp`
(12 tools over the [Woodstitch API](https://woodstitch.com/docs); source on
[GitHub](https://github.com/woodstitch-llc/woodstitch-mcp))
and a `listening-test` skill that knows when a listening test is the right tool, how to design one that is
not underpowered, and the one rule that matters with real money: show the quote before starting.

## Install

```
/plugin marketplace add woodstitch-llc/claude-plugin
/plugin install tts-human-eval@woodstitch
```

Pricing and templates work right away. The first time Claude needs your account (to quote or run a study), it
asks you to connect: sign in with Google or an emailed link and click Allow. Existing API-key users can choose "Connect with a key" on the same page.
Nothing is installed or run on your machine; the hosted server at `https://api.woodstitch.com/mcp` does
the work, and your audio goes only to your own study.

The same server works as a custom connector in the Claude apps: add `https://api.woodstitch.com/mcp`
and sign in.

## Codex and ChatGPT (OpenAI)

Codex CLI:

```
codex plugin marketplace add woodstitch-llc/claude-plugin
codex plugin add woodstitch@woodstitch
codex mcp login woodstitch
```

`codex mcp login` opens the same Woodstitch sign-in (Google, an emailed link, or "Connect with a key").
In the ChatGPT desktop app this repo can also be added as a plugin marketplace.

ChatGPT developer mode (paid plans): Settings → Apps → Advanced → Developer mode, then create an app with
the server URL `https://api.woodstitch.com/mcp` and OAuth, and sign in.

OpenAI API (Responses API / Agents SDK): use it as a remote MCP tool with your Woodstitch API key:

```python
tools=[{"type": "mcp", "server_label": "woodstitch", "server_url": "https://api.woodstitch.com/mcp",
        "authorization": "<WOODSTITCH_API_KEY>",
        "require_approval": {"always": {"tool_names": ["woodstitch_start_eval", "woodstitch_cancel_eval", "woodstitch_delete_file"]},
                             "never": {"tool_names": ["woodstitch_templates", "woodstitch_pricing", "woodstitch_account",
                                                      "woodstitch_list_evals", "woodstitch_get_eval", "woodstitch_get_results"]}}}]
```

Starting a study spends account credit, so keep approval on `woodstitch_start_eval` and show the quote to a person first.

The Codex package lives in `codex/woodstitch/` (manifest `.codex-plugin/plugin.json`, server config `.mcp.json`).

## What the server's tools can do

Read-only (change nothing, cost nothing): `woodstitch_templates`, `woodstitch_pricing`, `woodstitch_account`,
`woodstitch_list_files`, `woodstitch_list_evals`, `woodstitch_get_eval`, `woodstitch_get_results`, `woodstitch_export`.

Tools that change your account, spend money or delete:

| Tool | What it does | Money |
|---|---|---|
| `woodstitch_create_eval` | Creates a study in your account and prices it (a quote). Your clips are fetched and stored with the study. | Charges nothing |
| `woodstitch_start_eval` | **Pays for a study:** starts it, which spends your account credit as listeners work. Cannot be undone once listeners begin. Requires the quote total you approved; that amount is a hard spending ceiling. | Spends credit, up to the amount you approved |
| `woodstitch_cancel_eval` | Stops a running study early. Requires `confirm=true`. | Charges only for work already handed to listeners |
| `woodstitch_delete_file` | **Deletes** one uploaded audio file from your account. Studies that already used it keep their copy. | None |

No tool sends email or messages, publishes anything, moves money out of your account, or changes who can
access it. Your audio is played only to the listeners in your own study. Claude asks before starting,
cancelling or deleting.

## What it will not do

Start a paid study without you seeing the price: `woodstitch_start_eval`
sends the amount you confirmed as a hard ceiling and the API refuses anything above it.

## Links

Docs <https://woodstitch.com/docs> · Methodology <https://woodstitch.com/research/methodology> ·
Research <https://woodstitch.com/research/> · Contact <https://woodstitch.com/contact>

MIT © Woodstitch LLC
