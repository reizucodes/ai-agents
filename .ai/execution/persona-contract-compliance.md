# Persona and Contract Compliance

## Purpose

Make stack/persona conventions executable obligations rather than implicit context. A
persona is loaded on demand, but loading alone is not evidence of compliance.

## Harness-agnostic persona resolution

Applicable personas are every readable Markdown file under `.ai/agents/personas/`.
This protocol is canonical and applies equally to OpenCode, Claude, Codex, and any
other harness. Each persona declares its own `persona`, `stack_keys`, and
`runtime_roles` metadata; no central registry edit is required when adding a persona.
Malformed metadata or an ambiguous match fails closed as `PERSONA_UNRESOLVED`.

Before spawning a role:

1. Inspect the task prompt and repository for a stack signal.
2. Scan persona metadata and match the signal against `stack_keys`; require exactly
   one compatible persona and runtime role.
3. Read the matched persona before spawning the compatible runtime role.
4. Build the persona packet below and include it in the child handoff. The runtime
   adapter transports the packet; it does not decide persona semantics.

```text
Persona packet
Stack: <stack>
Persona: .ai/agents/personas/<stack>.md
Runtime role: <canonical runtime role>
Mandatory conventions: <rules that apply to this task>
Required acknowledgement: state the persona path and conventions before editing
```

If no matching persona exists, record `Persona: none` and continue with the canonical
runtime role contract.

## Mandatory resolution gate

Apply this Markdown gate at the two enforcement points:

1. `PERSONA_UNRESOLVED`: stack detection or persona lookup has not completed. No
   implementation role may be spawned.
2. `PERSONA_RESOLVED`: before spawning, the parent read the matching persona and
   included a packet in the child handoff, or recorded `Persona: none`.
3. `PERSONA_ACKNOWLEDGED`: before editing, the child repeated the matched persona
   path and applicable rules. Only this state permits implementation when a persona
   matched.

A matching persona without acknowledgement blocks implementation and escalates to
the parent.

Do not proceed on generic minimalism or silently fall back to another convention.

## Downstream propagation

The resolved persona packet travels through every phase that can influence the
implementation:

- Planning agents use it to constrain the technical plan and dependency decisions.
- Design agents use it to constrain styling and accessibility decisions.
- Implementation agents acknowledge it before editing.
- QA/review agents use it to compare the result against the persona rules.
- The parent carries the same packet and gate state into every downstream handoff.

A planning or design recommendation that contradicts a mandatory persona convention
is a compliance deviation and requires approval before implementation proceeds.

Persona rules marked as defaults for a new project are hard gates when their
preconditions match. Existing project conventions and explicit user requirements
take precedence. Missing setup is not the same as unavailable installation: the
worker must verify whether setup can be performed before requesting a deviation.
For Vue and React projects without an established styling system, the matching
persona’s Tailwind default is therefore `REQUIRED` unless an approved exception
exists.

Example: a task’s stack signal matches one persona’s `stack_keys`; the parent reads
that persona, includes its declared runtime role and mandatory conventions, and only
then spawns the worker. Broad minimalism cannot replace a matched hard convention;
unavailable installation must be reported as an approved deviation.

## Required worker sequence

When a runtime role matches a stack persona:

1. Receive the persona packet with the runtime-role handoff.
2. Read the canonical runtime role contract and the matching persona before planning
   implementation.
3. Acknowledge the exact persona path and list the mandatory conventions that apply to
   the task.
4. Resolve each convention against the user request, existing project state, and
   dependency/tool availability.
5. Implement the approved choice and include the compliance summary in the parent
   handoff.

“Loaded on demand” is an instruction to perform this sequence, not a completion
condition.

## Rule precedence

Resolve conflicts from most specific to least specific:

1. System/developer safety constraints.
2. Explicit user requirements and approved deviations.
3. Trust-boundary, security, accessibility, and data-safety requirements.
4. Matching stack/persona mandatory conventions.
5. Canonical runtime-role requirements.
6. Repository/workflow conventions.
7. Broad minimalism and dependency-avoidance guidance.

Broad guidance must not weaken a more specific rule. For example, “avoid new
dependencies” does not override a Vue persona’s “use Tailwind CSS by default.” If
installation is unavailable, report the deviation and its impact; do not silently
substitute another approach.

## Compliance checklist

Planning and implementation handoffs must answer each applicable item:

- [ ] Required framework/library choices are identified and satisfied.
- [ ] Styling-system requirement is identified, including existing-system preservation
      or the stack default for a new project.
- [ ] Dependency constraints and installation/tool availability are recorded.
- [ ] Accessibility requirements and validation are identified.
- [ ] Security requirements and validation are identified.
- [ ] Testing requirements, regression coverage, and execution status are identified.
- [ ] Any deviation names the exact rule, reason, impact, fallback, and owner.
- [ ] Any deviation from a mandatory persona/runtime rule has explicit parent/user
      approval before implementation or merge.

## Required implementation-agent handoff

Every implementation agent working under a matching persona returns:

```text
Persona loaded: <path or none>
Mandatory rules applied:
- <rule and implementation consequence>
Compliance checklist: framework/library=<pass|fail|n/a>; styling=<pass|fail|n/a>;
  dependencies=<pass|fail|n/a>; accessibility=<pass|fail|n/a>;
  security=<pass|fail|n/a>; testing=<pass|fail|n/a>
Deviations: <none or exact rule + reason + impact + fallback>
Approval required: <yes/no>; approver/status: <parent or user + status>
```

Missing persona acknowledgement, checklist status, or deviation approval is an
implementation-gate failure.

## Final review rule

QA/review must compare the implementation and handoff against the loaded persona and
runtime contract. A contradiction is a contract defect even when tests pass. The
review records the exact rule, affected files, severity, and whether an approved
deviation exists. Unapproved contradictions block the Quality Gate and merge.

## Enforcement boundary

This Markdown contract is the harness-agnostic source of truth. It defines what the
parent, child, and downstream roles must do; it does not intercept runtime spawns by
itself. Each harness must honor the parent handoff and child-edit gate using its
native orchestration mechanism. A consumer test is successful only when the runtime
shows the resolved persona packet before spawn and refuses implementation when the
child acknowledgement is missing.
