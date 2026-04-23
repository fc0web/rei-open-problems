# Open Problems META-DB (Rei-AIOS) v2 — Auto-Ingest Workflow

## Architecture

3 tiers separated by origin:

| Tier | Source | Ingest mode | Who writes |
|:-:|------|-------------|------------|
| **1** | World-public open (Wikipedia / Kourovka / Erdős / Millennium / Smale / Hilbert / ...) | Manual / Phase-A ingest scripts | Rei-AIOS ingest pipelines |
| **2** | Rei Invention Engine approved conjectures | **Auto via `metadb-auto-sync`** | Reads `data/invention/approvals/approved-*.json` |
| **3** | Claude Code research discoveries | **Semi-auto via `record-claude-discovery`** | Claude invokes per discovery |

## Commands

### Daily / on-demand sync
```
npm run metadb:sync
# = npx tsx scripts/metadb-auto-sync.ts
```
Idempotent. Scans all tiers, writes new Tier 2 files as needed, updates INDEX.json counters.

### Preview only (no writes)
```
npm run metadb:sync:dry
# = npx tsx scripts/metadb-auto-sync.ts --dry
```

### Record a new Claude discovery
```
npm run metadb:discovery -- \
  --id my-discovery-slug \
  --statement-en "Full English statement" \
  --statement-ja "日本語説明" \
  --field collatz-meta \
  --priority high \
  --session STEP-XXX \
  --documents "path/to/doc.md"
```

Outputs `data/open-problems/claude-discoveries/<id>.json` and auto-runs sync.

## Tier 2: Rei Inventions auto-ingest

Flow:
```
1. Rei Invention Engine produces daily report
   → data/invention/invention-YYYY-MM-DD.json
2. User approves via direct verbal (Nobuki)
   → Claude creates data/invention/approvals/approved-YYYY-MM-DD.json
   → Claude creates src/axiom-os/seed-kernel-approved-inventions-YYYY-MM-DD.ts
3. npm run metadb:sync
   → data/open-problems/rei-inventions/{theoryId}.json  (auto-generated)
   → INDEX.json totalReiInventions updated
```

**Idempotent**: re-running `metadb:sync` skips already-ingested inventions.

## Tier 3: Claude Code discoveries

Flow:
```
1. Claude Code identifies a research finding during a session
   (e.g., cross-paper convergence, numerical coincidence, structural link)
2. Claude runs: npm run metadb:discovery -- --id <slug> --statement-en <text> ...
3. → data/open-problems/claude-discoveries/<id>.json
4. → INDEX.json totalClaudeDiscoveries auto-updated
```

**Idempotent check**: `record-claude-discovery` refuses to overwrite existing files.

## INDEX.json invariants

| Field | Meaning | Paper 130 link |
|-------|---------|:-:|
| `totalProblems` | Tier 1 disk count | Historically 2,572 (Paper 130 publication) |
| `totalReiInventions` | Tier 2 disk count | new in v2 |
| `totalClaudeDiscoveries` | Tier 3 disk count | new in v2 |
| `grandTotalEntries` | Sum of all 3 tiers | new in v2 |
| `paperReference.paper130Published.totalProblems` | Historical 2,572 citation | immutable reference |

**If Tier 1 disk count drifts from Paper 130's 2572, both values preserved**:
- `totalProblems` = current disk truth
- `paperReference.paper130Published.totalProblems` = historical 2572

## Hook integration (optional, user's choice)

Recommended Claude Code hooks to add in `.claude/settings.local.json`:

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write",
        "pattern": "data/invention/approvals/approved-.*\\.json",
        "command": "npm run metadb:sync"
      },
      {
        "matcher": "Write",
        "pattern": "data/open-problems/claude-discoveries/.*\\.json",
        "command": "npm run metadb:sync"
      }
    ]
  }
}
```

This would auto-run `metadb:sync` whenever a new approval or discovery file is written.
Not enabled by default; user can add via `/settings`.

## Learning cycle integration

Once daily at 14:10 JST via `scheduled-tasks.ts`:
```
Phase 6 (Daily Invention) → approved invention → metadb:sync
```
Approved inventions in Tier 2 are automatically ingested every day.

## Discovery guidelines (for Claude)

Claude should call `record-claude-discovery` when finding:
1. **Cross-paper convergence**: two independent papers arrive at same structure
2. **Numerical coincidence**: exact or near-exact match across unrelated domains
3. **Structural anomaly**: statistical over/under-representation beyond random
4. **Negative search result**: absence of expected pattern (informative for direction)
5. **Formula coincidence**: same closed-form appears in separate open problems

Avoid recording:
- Routine calculation outputs
- Already-known textbook facts
- Session-specific debugging notes

## Related files

- `scripts/metadb-auto-sync.ts` — main sync (idempotent)
- `scripts/record-claude-discovery.ts` — semi-auto Tier 3 recorder
- `scripts/ingest-rei-inventions.ts` — initial ingest (legacy, now subsumed by metadb-auto-sync)
- `data/open-problems/INDEX.json` — canonical counter state
- `data/open-problems/rei-inventions/` — Tier 2 files
- `data/open-problems/claude-discoveries/` — Tier 3 files
