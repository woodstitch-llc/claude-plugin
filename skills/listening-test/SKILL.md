---
name: listening-test
description: Run a blinded human listening test on text-to-speech audio through Woodstitch. Use when the user wants real people to judge TTS output - which of two voices, models, checkpoints or settings listeners prefer, or whether synthetic speech passes as human - or asks for "human eval", "listening test", "A/B test my TTS", "MOS" or "does my TTS sound human". Not for automated metrics (WER, speaker similarity, UTMOS), which need no listeners.
---

# Human listening tests on Woodstitch

Woodstitch pays US listeners to judge audio blind. Two tasks only:

- **Pairwise preference** (`tts_preference.v1`): a listener hears two clips of the same text and picks one. Forced choice, no tie. Use it to choose between two systems, voices, checkpoints or settings.
- **Detection** (`tts_detect.v1`): a listener hears one clip and says human or AI, with confidence 1–4. Use it to learn whether output passes as real. Needs genuine human recordings of the same passages in the study as the baseline.

Not offered: MOS, CMOS, MUSHRA or any 1–5 scale. If the user asks for a MOS score, say so and offer preference instead: "which do people prefer" is usually the question behind "what is the MOS".

## Flow

1. **Design before spending.** Same text on both sides of every pair, same audio format and bitrate on both sides, one `passage` id per text, `system_a`/`system_b` labels. For a comparison of several systems, one pair per matchup and `per_participant: {"unique_by": ["passage"], "max_per": {"system": 7}}` so no listener hears one system often enough to recognise it. The create response warns when a design is underpowered; relay those warnings.
2. **Upload** local files with `woodstitch_upload_files`, or use public URLs.
3. **Create and price** with `woodstitch_create_eval`. This charges nothing. Use `dry_run: true` to price without storing.
4. **Show the user the quoted total and ask whether to start.** Never skip this.
5. **Start** with `woodstitch_start_eval(eval_id, user_confirmed_amount_usd=<the quoted total>)`. The confirmed amount is sent as a hard spending ceiling; the API refuses if the quote is higher.
6. **Poll** `woodstitch_get_eval` occasionally. First judgements usually land within minutes; a few hundred judgements take hours to days. Interim numbers are provisional: do not stop early because a number crossed a threshold.
7. **Report** `woodstitch_get_results`: `human_pref/b_win_rate` with its 95% interval (0.5 = indistinguishable; an interval that spans 0.5 does not establish a preference). `woodstitch_export` gives every judgement for the user's own analysis.

## Sizing

Rate card: $18 per hour of audio a listener hears, per judgement. 100 pairs of 30-second clips at 5 judgements each is 500 judgements, $150. A large preference gap (50 Elo) needs about 400 judgements for 80% power; a moderate gap (20 Elo) about 2,400. Few pairs means a wide interval regardless of judgements per pair, because the interval is bootstrapped over pairs.

## Where things are

- Docs: https://woodstitch.com/docs (markdown at /docs.md, OpenAPI at /openapi.json)
- Method, listener pay and controls: https://woodstitch.com/research/methodology
- Published studies run on the same platform: https://woodstitch.com/research/
- Keys: https://woodstitch.com/app/api-keys
