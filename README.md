# Woodstitch plugin for Claude Code

Blinded human listening tests on your text-to-speech audio, from inside Claude Code.
Paid US listeners judge clips in pairs (which do they prefer?) or one at a time (human or AI?),
and you get a win rate with a 95% confidence interval back.

The plugin connects Claude to the hosted Woodstitch MCP server at `https://api.woodstitch.com/mcp`
(13 tools over the [Woodstitch API](https://woodstitch.com/docs); source on
[GitHub](https://github.com/woodstitch-llc/woodstitch-mcp))
and a `listening-test` skill that knows when a listening test is the right tool, how to design one that is
not underpowered, and the one rule that matters with real money: show the quote before starting.

## Install

```
/plugin marketplace add woodstitch-llc/claude-plugin
/plugin install tts-human-eval@woodstitch
```

The first time Claude uses a Woodstitch tool, it asks you to sign in with Woodstitch: paste an API key
(from <https://woodstitch.com/app/api-keys>) or create a trial account with $25 of credit in one click.
Nothing is installed or run on your machine; the hosted server at `https://api.woodstitch.com/mcp` does
the work, and your audio goes only to your own study.

The same server works as a custom connector in the Claude apps: add `https://api.woodstitch.com/mcp`
and sign in.

## What it will not do

Score audio on a 1–5 MOS scale. Woodstitch runs two tasks only: pairwise preference and
human-vs-AI detection. Start a paid study without you seeing the price: `woodstitch_start_eval`
sends the amount you confirmed as a hard ceiling and the API refuses anything above it.

## Links

Docs <https://woodstitch.com/docs> · Methodology <https://woodstitch.com/research/methodology> ·
Research <https://woodstitch.com/research/> · Contact <https://woodstitch.com/contact>

MIT © Woodstitch LLC
