# Ghost Data Still Exists — evidence reconciliation solution

This package solves the active deletion-request dataset with a deterministic, local Python program. It reads `dataset/` from the challenge repository and writes `output.csv` at the repository root.

## Run

From the challenge repository root, using Python 3.10 or newer:

```bash
python code/main.py
python code/evaluation/main.py
```

No third-party Python packages are required. No external services, language models, or API calls are used.

## Files

- `code/main.py` — CSV loading, identity/alias matching, SQL/JSON/log/handover parsing, evidence cutoff enforcement, policy mapping, output generation, and structural validation.
- `code/evaluation/main.py` — repeatable output validation plus four custom checks covering non-erasure requests, active data flows, tombstones, and retained audit records.
- `evaluation/evaluation_report.md` — test coverage and limitations.
- `evaluation/usage_report.md` — local-only execution and cost disclosure.
- `output.csv` — generated answer for the 518 active request rows (provided separately from `code.zip`).

## Decision approach

1. Map each request email to a current or historical `user_id` and collect known names, emails, phones, and email hashes as identity aliases.
2. Parse indexed evidence records, using only evidence whose indexed capture timestamp and event timestamp do not exceed the request's `audit_at` cutoff.
3. Treat system activity/catalog links as association clues, not as proof that a record currently exists. Directly observed conflicting customer identifiers take precedence over an uncorroborated link.
4. Resolve each subject's resources by latest admissible source evidence; map remaining profiles, exports, search records, caches, marketing records, and attachments to deletion; shared support content to redaction; attributable analytics to anonymization; active integrations to disablement; restorable backups to restore blocking; and clean tombstones/security/legal records to retention-aware verification.
5. Treat a contradicted identity-catalog link separately from its downstream resource. For `case_63`, the plan removes only the inconsistent `data_catalog` association; it does not delete `marketing_001` from `marketing_platform`, because the indexed screenshot identifies a different profile.
6. Emit `COMPLETE` with verification-only steps, `INCOMPLETE` with evidence-backed findings and future remediation/verification steps, or `NOT_APPLICABLE` with an empty step list for requests that do not authorize account erasure.

## Important limitation

This is an evidence-reconciliation heuristic for a synthetic dataset, not a cryptographic proof that a deletion operation occurred. The 32 `sample_output.csv` cases are separately supplied examples, not a row-for-row answer key for the active cases. Therefore the evaluation checks schema, allowed operation/state pairs, ordering, time cutoffs, and selected evidence-derived scenarios; it does not claim hidden-case status accuracy against an unavailable answer key. Review the evaluation report's unresolved/no-attribution note before submission.
