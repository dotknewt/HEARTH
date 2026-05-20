# HEARTH — Hunting Exchange and Research Threat Hub

Community library of threat hunting hypotheses organized by the PEAK framework. Maintained by THE THOR Collective.

## Hunt Categories (PEAK Framework)

| Directory | Category | Purpose |
|-----------|----------|---------|
| `Flames/` | Hypothesis-driven | Classic threat hunting starting from a specific adversary behavior |
| `Embers/` | Baselining & exploration | Understand what's normal before hunting for anomalies |
| `Alchemy/` | Model-assisted / ML | Algorithmic and statistical detection |

## Hunt File Format

Every hunt is a markdown file with YAML frontmatter:

```yaml
---
id: H001                          # Flames=H###, Embers=B###, Alchemy=M###
category: Flames                  # Flames | Embers | Alchemy
hypothesis: "Adversaries are..."  # Single-sentence, single-technique behavior
tactics:
  - Credential Access             # MITRE ATT&CK tactic name(s)
techniques:
  - T1110                         # MITRE ATT&CK technique ID(s)
tags:
  - bruteforce                    # Lowercase keywords
  - vpn
severity: high                    # critical | high | medium | low | informational
status: current                   # current | stale | retired
created: 2024-01-01
submitter:
  name: Your Name
  link: https://x.com/yourhandle  # Optional
---

## Why

- Reason this behavior matters to detect
- Tactical impact if the adversary succeeds
- Links to real-world campaigns or threat actors

## References

- https://attack.mitre.org/techniques/T1110/
- [Source CTI or blog post]
```

## Writing a Good Hypothesis

A strong hypothesis has exactly four parts:
1. **Actor type** — e.g., "Threat actors", "Adversaries", "Attackers"
2. **Precise behavior** — specific commands, tools, or methods (not vague)
3. **Immediate tactical goal** — what the technique directly achieves
4. **Specific target** — exact systems, services, or data affected

**Bad (too broad):**
- "Threat actors are using PowerShell to execute malicious code"

**Good (narrow and specific):**
- "Threat actors are using PowerShell's `Invoke-WebRequest` cmdlet to download encrypted payloads from Discord CDN to evade network detection"

Rules:
- One technique per hunt — no exceptions
- No actor names in the hypothesis (put them in `## Why`)
- No CVE numbers or vulnerability names — use the technique name
- Use firm language ("are", not "may be")

## Next Hunt ID

Scan the target directory for the highest existing ID and increment:

```bash
ls Flames/H*.md | sort | tail -1   # find highest Flames ID
ls Embers/B*.md | sort | tail -1   # find highest Embers ID
ls Alchemy/M*.md | sort | tail -1  # find highest Alchemy ID
```

## Key Scripts

| Script | Purpose |
|--------|---------|
| `scripts/generate_from_cti.py` | Generate a hunt from a CTI URL or file using Claude API |
| `scripts/hunt_schema.py` | Canonical JSON Schema definition for hunt validation |
| `scripts/duplicate_detection.py` | Detect near-duplicate hypotheses |
| `scripts/hunt_parser.py` | Parse YAML frontmatter from hunt files |
| `scripts/build_hunt_database.py` | Rebuild SQLite index |

## Environment Variables

| Variable | Default | Purpose |
|----------|---------|--------|
| `ANTHROPIC_API_KEY` | required | Claude API key for hunt generation |
| `CLAUDE_MODEL` | `claude-sonnet-4-6` | Claude model to use |
| `AI_PROVIDER` | `claude` | `claude` or `openai` |

## Validation

```bash
python scripts/hunt_schema.py Flames/H001.md   # validate one file
python -m pytest scripts/tests/               # run all schema tests
```
