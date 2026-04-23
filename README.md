# Open Problems META-DB (Rei-AIOS) — Public Snapshot

**Canonical citation**: Fujimoto, N. (2026). *Open Problems META-DB (Rei-AIOS): D-FUMT₈ META-Classification of 2615 Open Problems*. Paper 130. Zenodo. DOI: [10.5281/zenodo.19700758](https://doi.org/10.5281/zenodo.19700758)

**License**: [CC-BY 4.0](LICENSE) — free to reuse with attribution.

**Version**: 2.0-public (snapshot generated 2026-04-23)

---

## What this is

A structured meta-database of **2633 mathematical unsolved problems + Rei-AIOS research contributions**, classified by:

- **Rei typology** (7 types: I_INFINITE_SEARCH, VI_BRIDGING, ...) — structural reason for the problem's difficulty
- **D-FUMT₈ value** (8-valued logic: TRUE/FALSE/BOTH/NEITHER/INFINITY/ZERO/FLOWING/SELF) — Rei-AIOS's logical classification
- **solveProbability** — Rei's assessment of tractability
- **Lean 4 formalization status** — scaffold/partial/complete
- **Cross-references** — Wikipedia / OEIS / Mathlib / SEED_KERNEL links

## Three tiers

| Tier | Name | Count | Content |
|:-:|------|----:|---------|
| **1** | World-public open | 2615 | Wikipedia + Kourovka + Erdős + Millennium + Smale + Hilbert + Green + OEIS + MathOverflow + arXiv papers |
| **2** | Rei Inventions | 15 | Rei-AIOS Invention Engine approved conjectures (framework: I(x) = Ψ × Φ × Ω, see Paper 132) |
| **3** | Claude Discoveries | 3 | Research observations from Rei-AIOS Claude Code sessions (subset; 2 entries withheld pending Paper 135/136 publication) |

Total: **2633 entries**.

## Structure

```
data/open-problems/
├── wikipedia/       # Wikipedia List of Unsolved Problems (116 entries)
├── kourovka/        # Kourovka Notebook (group theory)
├── erdos/           # erdosproblems.com + formal-conjectures (406 entries)
├── greens/          # Ben Green's open problems
├── millennium/      # Clay Millennium Prize Problems
├── smale/           # Smale's 18 problems
├── hilbert/         # Hilbert's 23 problems (residual)
├── mathoverflow/    # MathOverflow collection
├── oeis/            # OEIS integer sequences
├── wowii/           # Miscellaneous
├── paper/           # Individual arXiv papers
├── rei-inventions/  # Tier 2: Rei Invention Engine
└── claude-discoveries/  # Tier 3: Claude Code research findings
INDEX.json           # Canonical metadata and counters
```

## JSON schema (brief)

Each entry is a JSON file with fields including:
- `id`, `source`, `sourceRef` — provenance
- `statement` (en / ja / latex) — problem text
- `field`, `subfield`, `proposer`, `year`, `status` — classification
- `reiTyping` — Rei's structural analysis (primaryType, dfumt8, confidence)
- `cross_refs` — Wikipedia / OEIS / Mathlib / seedKernel links
- `formalization` — Lean 4 / Coq / Agda / Isabelle status, sorryCount
- `reiAssessment` — solveProbability, priority, paperCandidate

## Usage examples

### Find all Erdős problems with Rei "high priority"

```bash
for f in data/open-problems/erdos/*.json; do
  jq -r 'select(.reiAssessment.priority == "high") | .id + " " + .statement.en[0:80]' "$f" 2>/dev/null
done
```

### Count Tier 1 problems by field

```bash
jq -r '.field' data/open-problems/**/*.json 2>/dev/null | sort | uniq -c | sort -rn
```

## Paper citations

The following Rei-AIOS papers use this META-DB:

- **Paper 130** (DOI 10.5281/zenodo.19700758): Original META-DB construction
- **Paper 131**: Bipartite Ramsey b(2,2)=5 Lean 4 formalization
- **Paper 132**: Five Rei candidates (Sunflower / Hadwiger-Nelson / Happy Ending / Herzog-Schönheim / Wolstenholme)
- **Paper 133** (in-progress): Paper 132 Tier-1 closures
- **Paper 134**: AI math tooling survey (2024-2026)

## Held-back items

2 Tier 3 entries are withheld from this public snapshot pending scheduled paper publication:

- `chang-rei-tier2-convergence.json` — held for Paper 135/136
- `happy-ending-q33-formula-coincidence.json` — held for Paper 135/136

Will be added to public snapshot upon paper publication.

## Updates

This is a **snapshot**. Canonical development happens in private repo `fc0web/rei-aios`.
Updates to this public snapshot are periodic (via `scripts/build-public-metadb-snapshot.ts`).

## License & attribution

All content: CC-BY 4.0.

When citing, please use:

```bibtex
@misc{fujimoto2026meta,
  author = {Fujimoto, Nobuki},
  title = {Open Problems META-DB (Rei-AIOS): D-FUMT₈ META-Classification of 2572 Open Problems},
  year = {2026},
  publisher = {Zenodo},
  doi = {10.5281/zenodo.19700758},
  url = {https://doi.org/10.5281/zenodo.19700758}
}
```

## Contact

- **Author**: Nobuki Fujimoto (藤本 伸樹)
- **Email**: fc2webb@gmail.com
- **Website**: [note.com/nifty_godwit2635](https://note.com/nifty_godwit2635)
- **Facebook**: Nobuki Fujimoto

## Peace Axiom #196

This work follows Rei-AIOS's Peace Axiom (#196, immutable): contribute to understanding, do not contribute to harm.
