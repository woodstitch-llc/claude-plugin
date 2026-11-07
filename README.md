# Woodstitch plugin for Claude Code

Blinded human listening tests on your text-to-speech audio, from inside Claude Code.
Paid US listeners judge clips in pairs (which do they prefer?) or one at a time (human or AI?),
and you get a win rate with a 95% confidence interval back.

The plugin adds the [Woodstitch MCP server](https://pypi.org/project/woodstitch-mcp/) (13 tools over
the [Woodstitch API](https://woodstitch.com/docs)) and a skill that knows when a listening test is the
right tool, how to design one that is not underpowered, and the one rule that matters with real money:
show the quote before starting.

## Install

```
/plugin marketplace add TaylorHawkes/woodstitch-plugin
/plugin install woodstitch@woodstitch
```

Claude Code will ask for your API key (from <https://woodstitch.com/app/api-keys>). Leave it empty to
browse templates and pricing only. Requires `uv` (the server runs with `uvx woodstitch-mcp`).

Prefer a bare MCP server without the skill? `claude mcp add woodstitch -e WOODSTITCH_API_KEY=ws_live_... -- uvx woodstitch-mcp`

## What it will not do

Score audio on a 1–5 MOS scale. Woodstitch runs two tasks only: pairwise preference and
human-vs-AI detection. Start a paid study without you seeing the price: `woodstitch_start_eval`
sends the amount you confirmed as a hard ceiling and the API refuses anything above it.

## Links

Docs <https://woodstitch.com/docs> · Methodology <https://woodstitch.com/research/methodology> ·
Research <https://woodstitch.com/research/> · Contact <https://woodstitch.com/contact>

MIT © Woodstitch LLC
