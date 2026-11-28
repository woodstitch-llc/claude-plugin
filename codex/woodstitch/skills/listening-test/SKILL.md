---
name: listening-test
description: Run a blinded human listening test on text-to-speech audio through Woodstitch. Use when the user wants real people to judge TTS output - which of two voices, models, checkpoints or settings listeners prefer, a MOS or CMOS score, or whether synthetic speech passes as human - or asks for "human eval", "listening test", "A/B test my TTS", "MOS", "mean opinion score", "CMOS" or "does my TTS sound human". Not for automated metrics (WER, speaker similarity, UTMOS), which need no listeners.
---

# Human listening tests on Woodstitch

Woodstitch pays US listeners to judge audio blind. Four templates; pick by the question being asked:

- **Pairwise preference** (`tts_preference.v1`, `pairs`): a listener hears two clips of the same text and picks one. Forced choice by default. The most sensitive way to tell two close systems apart; the default for "which is better".
- **CMOS** (`tts_cmos.v1`, `pairs`): a listener scores B against A from -3 (much worse) to +3 (much better). Returns a comparative MOS per system pair: how much better, not just which.
- **MOS** (`tts_mos.v1`, `clips`): a listener scores one clip's naturalness 1–5 (ITU-T P.800 ACR). An absolute score per system, e.g. for a paper or a benchmark table. Give every clip a `system` label.
- **Detection** (`tts_detect.v1`, `clips`): a listener hears one clip and says human or AI, with confidence 1–4. Use it to learn whether output passes as real. Needs genuine human recordings of the same passages in the study as the baseline.

If the user asks for "a MOS score" to decide between systems, MOS works, but say that preference (or CMOS) separates close systems with fewer judgements. Pass `template` to `woodstitch_create_eval`; `question` overrides the default listener question. Play order in pairs is randomised per judgement and mapped back, so results are per system, never per screen position.

## Flow

1. **Design before spending.** Same text on both sides of every pair, same audio format and bitrate on both sides, one `passage` id per text, `system_a`/`system_b` labels. For a comparison of several systems, one pair per matchup and `per_participant: {"unique_by": ["passage"], "max_per": {"system": 7}}` so no listener hears one system often enough to recognise it. The create response warns when a design is underpowered; relay those warnings.
2. **Audio:** pass public or signed URLs straight to `woodstitch_create_eval` (each clip is fetched once). For local files, upload them first through the API's `POST /v1/files` (multipart `file[]` parts, same Bearer key) and use the returned file ids. The hosted server can't read your disk.
3. **Create and price** with `woodstitch_create_eval`. This charges nothing. Use `dry_run: true` to price without storing.
4. **Show the user the quoted total and ask whether to start.** Never skip this.
5. **Start** with `woodstitch_start_eval(eval_id, user_confirmed_amount_usd=<the quoted total>)`. The confirmed amount is sent as a hard spending ceiling; the API refuses if the quote is higher.
6. **Poll** `woodstitch_get_eval` occasionally. First judgements usually land within minutes; a few hundred judgements take hours to days. Interim numbers are provisional: do not stop early because a number crossed a threshold.
7. **Report** `woodstitch_get_results`. Preference: `human_pref/b_win_rate` with its 95% interval (0.5 = indistinguishable; an interval that spans 0.5 does not establish a preference). MOS: mean score per system; CMOS: mean B-vs-A score per system pair; both with 95% intervals bootstrapped over listeners and passages. `woodstitch_export` gives every judgement for the user's own analysis.

## Sizing

Cost is per judgement, based on how much audio a listener hears (MOS and detection play one clip, preference and CMOS two). Call `woodstitch_pricing` for the current rate card and `woodstitch_create_eval` with `dry_run: true` for an exact quote. A large preference gap (50 Elo) needs about 400 judgements for 80% power; a moderate gap (20 Elo) about 2,400. Few pairs means a wide interval regardless of judgements per pair, because the interval is bootstrapped over pairs.

## Where things are

- Docs: https://woodstitch.com/docs (markdown at /docs.md, OpenAPI at /openapi.json)
- Method, listener pay and controls: https://woodstitch.com/research/methodology
- Published studies run on the same platform: https://woodstitch.com/research/
- Keys: https://woodstitch.com/app/api-keys
