---
name: ponytail-debt
description: "Harvest ponytail: comment markers into a debt ledger. Trigger when the user wants to collect/report simplification debt markers left across the codebase."
---

Scan the entire repository tree for `ponytail:` comment markers (grep pattern: `ponytail:`), excluding `node_modules`, `.git`, `vendor`, `dist`.

For each marker report:
- file path
- line number
- simplified section description
- ceiling (named limit constraint if present)
- upgrade path (revisit trigger if present)

Flag entries with no defined upgrade path as `no-trigger`.

End with the total marker count and the count of `no-trigger` entries. If no markers: `No ponytail: debt. Clean ledger.`
