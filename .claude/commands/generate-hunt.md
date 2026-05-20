Generate a new HEARTH threat hunt hypothesis from a CTI source.

**Usage:** `/generate-hunt <URL-or-description>`

Arguments: $ARGUMENTS

## Steps

1. **Fetch the CTI source**
   - If `$ARGUMENTS` is a URL, use WebFetch to retrieve the page content
   - If it's a description or pasted text, use it directly

2. **Select the single most actionable MITRE ATT&CK technique**
   - Choose based on: actionability (observable in logs), impact, uniqueness, detection gap
   - Prioritize one technique even if the CTI covers multiple

3. **Write the hypothesis** following these rules:
   - Exactly four parts: Actor type + precise behavior + tactical goal + specific target
   - Single specific technique — no broad statements
   - Firm language: "are", not "may be"
   - No MITRE technique IDs in the hypothesis text
   - No CVE names — use the technique name
   - No actor names in the hypothesis (put them in `## Why`)

   **Bad:** "Threat actors are using PowerShell to run scripts"
   **Good:** "Threat actors are invoking `mshta.exe` with remote HTA URLs from attacker-controlled infrastructure to execute VBScript payloads and bypass AppLocker application controls"

4. **Determine the next hunt ID**
   - Run: `ls Flames/H*.md | sort | tail -1` to find the highest existing ID
   - Increment by 1 (e.g., H042 → H043)

5. **Create the hunt file** at `Flames/<id>.md`:

```
---
id: <id>
category: Flames
hypothesis: "<your hypothesis>"
tactics:
  - <MITRE tactic name>
techniques:
  - <Txxxx>
tags:
  - <lowercase-tag1>
  - <lowercase-tag2>
severity: <critical|high|medium|low|informational>
status: current
created: <YYYY-MM-DD>
submitter:
  name: <your name>
---

## Why

- <Why this behavior is important to detect>
- <Tactical impact if adversary succeeds>
- <Connection to real campaigns if known>

## References

- https://attack.mitre.org/techniques/<Txxxx>/
- <CTI source URL>
```

6. **Report** the created file path and the final hypothesis to confirm.
