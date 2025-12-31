## Summary
<!-- What changed and why? Keep it short and factual.   -->

## Type of Change
_Select at least one_
- [ ] Slice (implementation guide)
- [ ] ADR (architecture decision)
- [ ] Runbook (planned operational procedure)
- [ ] Playbook (incident response)
- [ ] Code / Infra only
- [ ] Docs / Handbook only

---

## Documentation Compliance Checklist (REQUIRED)

### General (applies to all PRs)
- [ ] No secrets committed (verified locally and by pre-commit)
- [ ] Files placed in correct directory structure
- [ ] Naming and tagging standards followed  
      (see `docs/handbook/06-naming-and-tags.md`)
- [ ] Links and references resolve correctly
- [ ] This PR does **not** redefine standards already covered by the handbook

---

### If this PR adds or modifies a **Slice**
- [ ] Slice is located under `docs/slices/phase-X/`
- [ ] Slice uses the **canonical slice structure**:
  - Purpose
  - Architectural Context
  - Prerequisites
  - What You Will Build
  - Step-by-Step Procedure
  - Validation Checklist
  - Artifacts Produced
  - Rollback / Undo
- [ ] Acceptance / validation checklist is explicit and testable
- [ ] All commands are runnable and include expected outcomes
- [ ] Produced artifacts are listed and committed (or explicitly deferred)
- [ ] Slice status updated correctly (Draft / Ready / Complete)

---

### If this PR adds or modifies an **ADR**
- [ ] ADR uses the ADR template from `docs/adr/templates/`
- [ ] Status is set correctly (Proposed / Accepted / Superseded / Deprecated)
- [ ] Decision outcome is clear and unambiguous
- [ ] Consequences (positive and negative) are documented
- [ ] Related slices reference this ADR (or a follow-up issue exists)

---

### If this PR adds or modifies a **Runbook**
- [ ] Runbook includes:
  - Purpose
  - Prerequisites
  - Step-by-step procedure
  - Verification steps
  - Rollback procedure
- [ ] Runbook clearly states **when to use** and **when not to use**
- [ ] Safe to follow under pressure (no ambiguous steps)

---

### If this PR adds or modifies a **Playbook**
- [ ] Playbook is scoped to an incident scenario
- [ ] Clear triggers and severity level defined
- [ ] Decision tree or escalation path included
- [ ] Communication steps included (who, when, how)

---

## Validation Evidence
<!--
Paste evidence that this change works:
- command output
- screenshots
- logs
- mkdocs build output
-->
