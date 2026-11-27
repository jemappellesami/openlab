# Domain pack: Quantum information

This pack plugs into the core platform. Build it after the core milestones M0 to M3 work.

## Q1. Foundations (state of Lean as of November 2026)

Build on Physlib's QuantumInfo target; treat the other libraries as sources, not dependencies, in version one.

| Library | What it gives | Use in v1 |
| --- | --- | --- |
| [Physlib, QuantumInfo target](https://github.com/leanprover-community/physlib) | Lean-QuantumInfo (Meiburg et al.) merged into Physlib in March 2026, under leanprover-community. Over 1000 theorems as of October 2025: states, channels, entropies, the generalized quantum Stein's lemma formalization. Its own conventions, separate build target. | **The workspace dependency.** Pin one commit; build only `lake build QuantumInfo`. |
| [Lean-Quantum](https://arxiv.org/abs/2607.05492) (Kasaura, Yamasaki et al., July 2026) | Basis-independent framework: systems, states, channels, partial traces, Choi, Kraus, Stinespring; the data processing inequality for sandwiched Rényi divergence; strong subadditivity. | Not a dependency (different conventions). Note in `DECISIONS.md` whether it builds against the same Mathlib; if so, propose it as a later second workspace for entropy questions. |
| [Formal Conjectures](https://github.com/google-deepmind/formal-conjectures) (Google DeepMind, Apache-2.0) | About 2,600 Lean statements, over 1,000 of them open; about 125 drawn from the Open Quantum Problems list. Mathlib only, not Physlib. | **Import as a source** (next bullet). |
| [LeanQuantum](https://github.com/inQWIRE/LeanQuantum) (inQWIRE) | Gates, Dirac notation, circuit identities. | Out of scope for v1; mention in `FOUNDATIONS.md`. |

- **Pinning:** read Physlib's `lean-toolchain` and `lake-manifest.json` at the chosen commit and use exactly its Lean and Mathlib versions. Never upgrade Mathlib independently of Physlib. Fallbacks: an older Physlib commit that builds; then Mathlib alone with minimal definitions in `Frozen/`.
- **Formal Conjectures import:** pin a commit, parse the quantum-related files (Open Quantum Problems and any other quantum statements), and store each as a question with its Lean text, source, category and the license attribution. Status: Draft in our workspace until a `formalize` task ports it to QuantumInfo's definitions and a `review_formalization` task confirms the port. Never assume their definitions match QuantumInfo's.
- **Upstream later:** results that would strengthen Physlib (new lemmas about the foundations) are listed in `REPORT.md` as candidates to contribute back; do not open pull requests to external repositories.

## Q2. Sub-domains and sources

**Taxonomy** (`packs/quantum/taxonomy.yaml`, multi-label, one primary sub-domain per question):

- Quantum error correction and fault tolerance
- Quantum cryptography and QKD (shared with the cryptography pack: one sub-domain id used by both, so its questions appear in both maps)
- Quantum Shannon theory and channel capacities
- Entanglement theory
- Quantum complexity theory
- Quantum algorithms
- Hamiltonian complexity and many-body
- Quantum learning, tomography and property testing
- Nonlocality and foundations
- Quantum metrology and hypothesis testing
- Resource theories
- Quantum thermodynamics

**Sources** (ingest adapters in `packs/quantum/ingest/`), in priority order:

1. **QIP 2026 accepted papers:** the official list at [qipconference.org/2026/accepted-papers](https://qipconference.org/2026/accepted-papers/). Parse titles, authors and abstracts, match each to its arXiv id, save as `seed/qip2026.json`. Process these first.
2. **QIP 2027 (Singapore, 20 to 26 February 2027):** per [the conference site](https://qipconference.org/2027/), talk decisions are due 30 November 2026, so the accepted list may appear days after this run. Build an adapter that checks the 2027 accepted-papers page once a day and ingests the list when it appears, and is harmlessly empty until then.
3. **arXiv quant-ph:** recent papers through the arXiv API (one request every 3 seconds at most); use OAI-PMH for any bulk backfill. Prioritize papers whose abstracts or text contain phrases such as "open problem", "open question", "we conjecture", "remains open" or "we leave".
4. **Formal Conjectures quantum statements** (Q1), already formalized, as a separate source type.

Every adapter is configurable in `pack.yaml` (enabled, window of dates, maximum papers per run) so the owner can widen or narrow the flood.

## Q3. Seed units, numerics and definitions

**Rule for every seed:** use QuantumInfo's definitions wherever they exist, and check that the statement is not already a theorem in the library (search it, and try `exact?`); a unit that is already proved upstream is useless. Any new definition (for example MUB, SIC, PPT-squared, entanglement-breaking, if missing) goes in `Frozen/` with an English docstring and the status "machine-written, awaiting human review".

- **About 15 warmups,** for example: a mixture of density matrices is a density matrix; partial trace preserves trace; partial trace of a product state returns the factor; Kronecker products of positive semidefinite matrices are positive semidefinite; product states are PPT; the two-qubit maximally entangled state is not PPT (exact computation); a pure state has purity one.
- **About 8 solid units,** for example: finite convex combinations of product states are PPT; the three Pauli eigenbases form a complete set of mutually unbiased bases in dimension 2; explicit SICs exist in dimensions 2 and 3 (exact coordinates); the Choi matrix of a completely positive map is positive semidefinite.
- **Two or three open units** with clean statements, for example: no four pairwise mutually unbiased orthonormal bases exist in C^6; a SIC exists in every dimension d at least 2; the PPT-squared conjecture in a dimension where it is still open. Check each one's current status on the web before seeding, and cite it.
- **From Formal Conjectures:** port up to five of the cleanest Open Quantum Problems statements as `formalize` tasks, not as units, so the review ladder handles them.

**Numerics scouts** (`packs/quantum/scout/`): helpers with numpy and cvxpy for SDPs, seesaw optimization and random search over states and channels, plus a converter that rounds a numerical witness to exact rationals (or exact algebraic numbers where needed) and emits a Lean skeleton the agent then proves. A witness that fails to verify exactly is reported as a lead, never a result.
