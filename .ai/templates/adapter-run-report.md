# Adapter Run Report Template

## Run Status
- status: `pass` | `fail` | `partial` | `rejected`

## Runtime Target
- runtime: `<runtime-name>`
- adapter schema contract: `<path>`
- output target pattern: `<path-pattern>`

## Canonical Routing Identifiers
- canonical adapter `name` is authoritative for routing and reporting.
- canonical role path is authoritative for role identity.
- runtime display nickname/alias (if any) is non-authoritative metadata.

## Generated Adapters
- `<adapter-name>`: `<output-path>`
  - canonical role path: `<.ai/agents/<role>.md>`
  - runtime display nickname observed (optional): `<nickname-or-n/a>`

## Skipped Adapters
- `<adapter-name>`: `<reason>`

## Rejected Adapters
- `<adapter-name>`: `<rejection-reason>`

## Source Files Read
- `<canonical-source-file-path>`

## Contracts Loaded
- `<contract-path>`

## Validation Checks
- canonical source existence: `pass|fail`
- adapter output existence: `pass|fail|n/a`
- required metadata: `pass|fail`
- required fields: `pass|fail`
- canonical source pointer: `pass|fail`
- minimal-content/no-full-duplication: `pass|fail`
- runtime-specific schema: `pass|fail`

## Drift Checks
- source fingerprint/checksum match: `pass|fail`
- generated timestamp validity: `pass|fail`
- drift state: `in-sync|stale|missing-metadata|missing-adapter`

## Collision Checks
- reserved/built-in collision: `pass|fail|unknown`
- custom name uniqueness in target scope: `pass|fail`
- override requested: `yes|no`

## Failures
- state: `<missing_source|missing_adapter|stale_adapter|invalid_schema|unsafe_duplicate_content|unknown_runtime_schema>`
  - affected adapter: `<adapter-name>`
  - details: `<details>`

## Remediations
- action: `<regenerate-adapter|reject-adapter|require-explicit-user-confirmation|fallback-to-sequential>`
  - target: `<adapter-name|run>`
  - owner: `<parent-runtime-agent|user>`

## Next Steps
1. `<next-step>`
2. `<next-step>`
3. `<next-step>`
