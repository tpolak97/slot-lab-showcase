# Slot Lab

Slot Lab is a Windows desktop tool I built with Codex to test the math and replay data for Ashes of Edo, my Stake Engine slot prototype. I wanted a repeatable way to find a problem, get back to the exact outcome that caused it, and check the fix against the same inputs.

## How it works

1. Pick a game, bet mode, round count, and seed. Slot Lab samples the game's published lookup weights and reports hit rate, bonus frequency, and observed return to player (RTP).
2. In **Events**, Slot Lab also rebuilds each sampled outcome with the game's model. It compares the payout with the lookup table, runs the game's event checks, and records the seed, round number, and outcome ID if a check fails.
3. **Deep Math** separately reads every exported outcome in the selected mode. It checks ID coverage, exact payout agreement, and the event sequence. Slot Lab calculates expected RTP from the lookup weights and payouts divided by the mode cost, using the [Stake Engine math file format](https://stake-engine.com/docs/math/math-file-format).

## A bug I tracked down

An Ashes of Edo event run stopped on round 63 with seed `42`. The export and lookup table both paid **4× the base bet** for outcome `12841`, while Slot Lab reconstructed a zero payout. The tool was using an older reconstruction method. I updated it to read the export's authoring metadata and use the current mechanic version, authored seeds, and cut overrides. The corrected run reproduced the exported events and payout for that outcome.

| Local check | Result |
| --- | --- |
| Seed `42`, Ashes of Edo Base mode | 10,000 reconstructed rounds passed with zero payout mismatches |
| Complete Base-mode export | 100,000 outcomes passed the payout and event audit |
| Exact expected RTP for this export | 96.7% from the mode's own weighted payouts and cost |

[Verification summary](verification.json)

Slot Lab currently supports Ashes of Edo and Blood Moon through their Python models. This case study covers a local prototype and its exported math. Live wallets and platform approval were outside the test.
