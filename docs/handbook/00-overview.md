# Slice Methodology

A **Slice** is a self-contained, reproducible unit of work.

If a task cannot be explained as a slice, it is not ready for execution.

---

## What a Slice is
- Executable by another team member
- Produces concrete artifacts  
- Has clear validation and rollback steps
- Lives under `docs/slices/phase-X/`

## What a Slice is NOT
- A design discussion
- A checklist without steps
- A copy of a portal screenshot

---

## Required structure
Every slice must include:
1. Purpose
2. Architectural Context
3. Prerequisites
4. What You Will Build
5. Step-by-step Procedure
6. Validation Checklist
7. Artifacts Produced
8. Rollback / Undo

Slices that do not follow this structure will be rejected.

---

## Slice lifecycle
Draft → Ready → Complete

- **Draft**: being written
- **Ready**: executable, reviewed
- **Complete**: executed and validated
