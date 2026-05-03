---
title: Stabilizing Gemini TTS voice with RVC
description: Routing Gemini TTS through RVC collapsed speaker-embedding std by 83%.
tags: [tts, rvc, audio, ml]
draft: false
date: 2026-05-03
---

## TL;DR

Gemini TTS samples drift in timbre across generations. Routing them through RVC collapsed speaker-embedding std from **0.0116 → 0.0020 (−83.2%)** — indistinguishable by ear.

## Background

Gemini TTS is fluent but speaker voice subtly varies between generations — fine for one-shots, breaks consistency for long narration. RVC remaps any source voice to a fixed target, so it should normalize that variance.

## Approach

Generated 15 utterances of the same script via Gemini TTS, ran each through RVC v2 with VOICEPEAK Japanese Female 1, then measured speaker similarity with Resemblyzer's voice encoder over all pairs (cosine).

Params: `protect=0.5`, `index_rate=0.5`, `f0_method=rmvpe`, CPU inference.

## Results

| set     | std    | n pairs |
| ------- | ------ | ------- |
| TTS raw | 0.0116 | 105     |
| RVC out | 0.0020 | 105     |

Std change: **−83.2%**. Listening test: all 15 RVC outputs sound like the same speaker.

## Gotchas

| Issue                                                              | Fix                       |
| ------------------------------------------------------------------ | ------------------------- |
| rvc-python deps fail to build on Python 3.11+                      | pin Python 3.10           |
| PyTorch 2.6+ `torch.load(weights_only=True)` breaks hubert loading | pin torch 2.4.x           |
| rvc-python auto-selects MPS on Apple Silicon → SIGSEGV             | force CPU                 |
| faiss multi-threaded `search` SIGSEGVs on Apple Silicon            | `omp_set_num_threads(1)`  |

## References

- RVC: <https://github.com/RVC-Project/Retrieval-based-Voice-Conversion-WebUI>
- Speaker encoder: <https://github.com/resemble-ai/Resemblyzer>