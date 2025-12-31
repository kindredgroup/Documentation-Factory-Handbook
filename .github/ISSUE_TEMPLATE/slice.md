---
name: New Slice
about: Create a new implementation slice
title: "Slice X.Y: <short, clear title>"
labels: ["slice"]
---

## 1. Goal (What are we trying to achieve?)
Describe the outcome in one or two sentences.

Example:
> Deploy Azure AI Search with private endpoints and semantic ranker enabled.

---

## 2. Scope
### In scope
- What this slice WILL do

### Out of scope
- What this slice will NOT do (important to avoid scope creep)

---

## 3. Architectural Impact
Answer **YES / NO**:

- [ ] Does this require a new architectural decision?
- [ ] Does this change security, networking, identity, or data flow?

If **YES** to any:
- Link or create an ADR **before** implementing the slice.

ADR link (if applicable):  
`ADR-XXX: <title>`

---

## 4. Prerequisites
### Required completed slices
List slice numbers that must already be complete.

Example:
- [ ] Slice 1.2: Private Networking
- [ ] Slice 1.4: Core Services

### Required access / roles
Example:
- Azure Contributor
- Entra ID Admin

---

## 5. Acceptance Criteria (MOST IMPORTANT)
Write this **before** implementation.

The slice is complete only when ALL are true:

- [ ] Resource(s) exist and provisioningState = Succeeded
- [ ] No public endpoints exposed
- [ ] Validation steps can be followed by another team member
- [ ] Rollback is documented

(Adjust criteria to your slice.)

---

## 6. What Will Be Built
List concrete outputs.

| Component | Description |
|---------|-------------|
| Resource / File | Purpose |

---

## 7. Validation Plan
How will we prove this worked?

Examples:
- Azure Portal checks
- `az` CLI commands
- DNS resolution tests
- Logs / metrics visible

---

## 8. Rollback Plan (high level)
How do we safely undo this if needed?

Examples:
- Delete resource group (dev)
- Remove private endpoint
- Revert config

---

## 9. Notes / Risks
Anything unclear, risky, or worth calling out early.
