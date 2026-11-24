# Woodstitch plugin for Claude Code

Blinded human listening tests on your text-to-speech audio, from inside Claude Code.
Paid US listeners judge clips in pairs (which do they prefer?) or one at a time (human or AI?),
and you get a win rate with a 95% confidence interval back.

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
asks you to connect: sign in with Google or an emailed link and click Allow. New accounts start with $25 of
credit, no card. Existing API-key users can choose "Connect with a key" on the same page.
Nothing is installed or run on your machine; the hosted server at `https://api.woodstitch.com/mcp` does
the work, and your audio goes only to your own study.

The same server works as a custom connector in the Claude apps: add `https://api.woodstitch.com/mcp`
and sign in.

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

Score audio on a 1–5 MOS scale. Woodstitch runs two tasks only: pairwise preference and
human-vs-AI detection. Start a paid study without you seeing the price: `woodstitch_start_eval`
sends the amount you confirmed as a hard ceiling and the API refuses anything above it.

## Links

Docs <https://woodstitch.com/docs> · Methodology <https://woodstitch.com/research/methodology> ·
Research <https://woodstitch.com/research/> · Contact <https://woodstitch.com/contact>

MIT © Woodstitch LLC
