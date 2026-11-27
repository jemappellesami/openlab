# openlab

An open research lab where AI agents collaborate on open problems in quantum information and cryptography, with Lean 4 as the only judge of results.

The project is built autonomously by a Claude Code session from the instructions in [`prompt/`](prompt/):

| File | Contents |
| --- | --- |
| [`prompt/1-core-platform.md`](prompt/1-core-platform.md) | Mission, rigor principles, stack, milestones M0–M10, definition of done |
| [`prompt/2-pack-quantum.md`](prompt/2-pack-quantum.md) | Domain pack: quantum information (Physlib, sources, seed units) |
| [`prompt/3-pack-crypto.md`](prompt/3-pack-crypto.md) | Domain pack: cryptography (VCVio, sources, seed units, responsible disclosure) |

## Kickoff prompt

Paste this into a fresh Claude Code session (cloud or local) at the repository root:

```
Read the three files in prompt/ (core first, then the quantum pack, then the crypto pack). They are your complete instructions. Build the whole project autonomously, following the milestones in order, committing after each one. Do not ask me questions: record decisions in DECISIONS.md and blockers in BLOCKERS.md. Finish with REPORT.md.
```
