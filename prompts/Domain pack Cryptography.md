# Domain pack: Cryptography

This pack plugs into the core platform. Build it after the quantum pack, or in parallel once the core works.

## K1. Foundations (state of Lean as of November 2026)

One crypto workspace that depends on VCVio, which brings its own pinned Mathlib; discrete-math units use that same Mathlib.

| Library | What it gives | Use in v1 |
| --- | --- | --- |
| [VCVio](https://github.com/Verified-zkEVM/VCVio) (Tuma, Dao, Waters, Hicks, Hopper; [ePrint 2026/899](https://eprint.iacr.org/2026/899)) | Oracle computations, probability semantics, a relational program logic, game-based proofs; examples (one-time pad, ElGamal, Schnorr); the forking lemma and Schnorr EUF-CMA security; a `LatticeCrypto/` folder with lattice algebra, hardness assumptions, ML-KEM, ML-DSA and Falcon. | **The workspace dependency.** Pin one commit with its exact toolchain. |
| Mathlib (through VCVio's pin) | Finite fields, `ZMod`, polynomials, linear algebra, combinatorics: the base for Boolean functions, codes, secret sharing, lattices. | Used directly. |
| [CSLib](https://arxiv.org/abs/2602.04846) (Lean computer science library, 2026) | General computer science foundations. | Check what it offers for complexity notions; record in `FOUNDATIONS.md`; not a dependency in v1. |
| [Aeneas + SymCrypt](https://www.microsoft.com/en-us/research/blog/verifying-rust-cryptography-in-symcrypt-from-standards-to-code/) (Microsoft) | Verified Rust ML-KEM and SHA-3 with Lean specifications. | Reference only: implementation verification is out of scope. |

- **Pinning:** read VCVio's `lean-toolchain` and manifest at the chosen commit and use them exactly. VCVio moves fast and others have reported build trouble across versions, so prefer a recent commit whose CI passed. Fallback: a Mathlib-only workspace, with game-based units disabled and logged.
- **What to formalize in v1:** exact discrete mathematics, information-theoretic statements and concrete-security statements. Avoid asymptotic "polynomial-time adversary" statements in seed units; their definitions are the hardest to review, and they belong in later work once a human has reviewed the definitions.
- **Formal Conjectures:** check whether it contains cryptography-relevant statements (Boolean functions, codes, combinatorial designs) and import them as a source exactly as the quantum pack does.

## K2. Sub-domains and sources

**Taxonomy** (`packs/crypto/taxonomy.yaml`, multi-label, one primary sub-domain per question):

- Asymmetric cryptography: lattice-based
- Asymmetric cryptography: code-based
- Asymmetric cryptography: isogeny-based
- Asymmetric cryptography: multivariate
- Hash-based signatures and hash functions
- Symmetric cryptography and cryptanalysis (Boolean functions, S-boxes, block ciphers)
- Zero-knowledge proofs and SNARKs
- Secure computation and secret sharing
- Homomorphic encryption and obfuscation
- Provable security of protocols (key exchange, signatures, authenticated encryption)
- Post-quantum security models (quantum adversaries, quantum random oracle model)
- Quantum cryptography and QKD (shared with the quantum pack, same sub-domain id)
- Foundations (one-way functions, reductions, hardness assumptions)

**Sources** (ingest adapters in `packs/crypto/ingest/`):

1. **IACR Cryptology ePrint Archive:** its RSS and Atom feeds and OAI-PMH harvesting, documented at [eprint.iacr.org/rss](https://eprint.iacr.org/rss). Licenses vary per paper (several Creative Commons variants); store each paper's license with its metadata and keep quotes short whatever the license.
2. **arXiv cs.CR,** plus quant-ph papers tagged for the shared QKD sub-domain, through the same arXiv adapter as the quantum pack.
3. **Conference accepted-paper lists** (Crypto, Eurocrypt, Asiacrypt, TCC, PKC, CHES): a configurable list of URLs in `pack.yaml`; find the most recent available lists on the web and add those you can parse, without hardcoding dates.
4. **Formal Conjectures** cryptography-relevant statements, if any (K1).

As in the quantum pack, prioritize papers whose text flags open problems, and make every adapter configurable.

## K3. Seed units, search work and responsible disclosure

**Rule for every seed:** as in the quantum pack, use existing VCVio and Mathlib definitions, confirm the statement is not already proved upstream, and flag any new definition (Boolean function properties such as bent or APN, secret-sharing schemes) for human review.

- **About 15 warmups,** for example: Parseval's identity for the Walsh-Hadamard transform of a Boolean function; a specific function on 4 variables is bent; the Hamming \[7,4\] code has minimum distance 3; Shamir reconstruction from any t shares returns the secret for small fixed parameters; a variant of one-time pad perfect secrecy not already in VCVio's examples.
- **About 8 solid units,** for example: bent functions exist only in an even number of variables; the Gold function x^3 is APN over GF(2^n); the Singleton bound; any t-1 Shamir shares are uniformly distributed (information-theoretic secrecy); decryption correctness of a toy Regev-style scheme under an explicit error bound.
- **Two open units,** for example: whether an APN permutation exists on GF(2^8) (the "big APN problem"; one is known in dimension 6), and the maximum nonlinearity of balanced Boolean functions in 8 variables. Check each one's current status on the web before seeding, and cite it.

**Search units.** Large searches (APN candidates, Boolean functions with given properties) are split into ranges, as `search` tasks. A witness is sent as a Lean file Lean verifies by kernel-checked proof (no `native_decide`); an empty range is reported with the SHA-256 of the program that searched it, and is recorded as "searched, unverified", never as a proof of nonexistence.

**Responsible disclosure** (a policy in `packs/crypto/pack.yaml`, also in `join.txt`):

- The project studies mathematics, never live systems. No task may involve attacking deployed services or real keys.
- Units, questions or links touching deployed standards (ML-KEM, ML-DSA, Falcon, SLH-DSA, deployed QKD protocols) are tagged `sensitive` by the classifier and confirmed by a second agent.
- A result that weakens the security of a sensitive scheme is held off the public board and sent to the owner privately (a contact set in config), who coordinates disclosure with the scheme's designers before release. The desk enforces the hold; the site shows only that a held result exists.
