# Documentation Factory Handbook

**Purpose:**

> A single source of truth for *how we design, decide, build, document, and extend* the Documentation Factory.

---

## 1. Repository-Level Structure (Canonical)

This is the **only structure the team should use**.

```
docs/
├── handbook/                  # HOW we work (this document)
│   ├── 00-overview.md
│   ├── 01-slices.md
│   ├── 02-adrs.md
│   ├── 03-runbooks.md
│   ├── 04-playbooks.md
│   ├── 05-architecture.md
│   ├── 06-naming-and-tags.md
│   └── 07-contributing.md
│
├── slices/                    # WHAT we build (execution guides)
│   ├── phase-0/
│   │   ├── slice-0.1.md
│   │   ├── slice-0.2.md
│   │   └── slice-0.3.md
│   ├── phase-1/
│   │   ├── slice-1.1.md
│   │   ├── slice-1.2.md
│   │   └── slice-1.6.md
│   └── phase-2/
│       ├── slice-2.1.md
│       ├── slice-2.2.md
│       ├── slice-2.3.md
│       └── slice-2.5.md
│
├── adr/
│   ├── README.md
│   ├── ADR-001-*.md
│   ├── ADR-007-*.md
│   └── templates/
│       └── ADR-TEMPLATE.md
│
├── runbooks/
│   ├── README.md
│   ├── infrastructure/
│   ├── agents/
│   └── templates/
│       └── RUNBOOK-TEMPLATE.md
│
├── playbooks/
│   ├── README.md
│   └── templates/
│       └── PLAYBOOK-TEMPLATE.md
│
└── architecture/
    ├── diagrams/
    ├── decisions/
    └── system-context.md
```

**Golden rule:**

> *Slices explain execution. Handbook explains governance.*

---

## 2. Handbook Content (What Lives Where)

### 2.1 `docs/handbook/00-overview.md`

**Audience:** Anyone new

```md
# Documentation Factory – Handbook

This handbook defines:
- How we structure documentation
- How slices are written and reviewed
- How architectural decisions are recorded
- How new work is added safely

If you are building → read `01-slices.md`  
If you are deciding → read `02-adrs.md`
```

---

### 2.2 `docs/handbook/01-slices.md`

**This is critical. It formalizes slices as a contract.**

```md
# Slice Methodology

A Slice is:
- A bounded unit of work
- Independently executable
- Reviewable by another team member
- Produces explicit artifacts

## Slice Lifecycle
1. Slice created (Proposed)
2. Slice executed (In Progress)
3. Acceptance criteria met
4. Artifacts committed
5. Slice marked Complete
```

---

## 3. Canonical Slice Template (MANDATORY)

### `docs/slices/_templates/SLICE-TEMPLATE.md`

````md
# Slice X.Y: <Title>

**Phase:** <Phase Name>  
**Status:** Draft | Ready | Complete  
**Estimated Time:** X–Y hours  
**Primary Owner:** <Role, not name>  

---

## 1. Purpose
Why this slice exists and what capability it unlocks.

---

## 2. Architectural Context
- Related ADRs: ADR-00X
- Depends on: Slice A.B, Slice C.D
- Enables: Slice E.F

---

## 3. Prerequisites
### Completed Slices
- [ ] Slice X.Y

### Access Required
- [ ] Azure Role: Contributor
- [ ] Entra Role: Agent ID Admin

---

## 4. What You Will Build
| Component | Description |
|---------|------------|
| Resource | Purpose |

---

## 5. Step-by-Step Procedure
> No unexplained jumps. Every command must be runnable.

### Step 5.1 – <Title>
**Objective:**  
**Commands / Actions:**  
```bash
az ...
````

**Expected Outcome:**

* Resource exists
* Status = Succeeded

---

## 6. Validation Checklist

* [ ] Resource visible in Azure Portal
* [ ] No public endpoints
* [ ] Logs flowing to Log Analytics

---

## 7. Artifacts Produced

* `docs/adr/ADR-0XX-*.md`
* `infra/modules/...`
* Screenshots (if required)

---

## 8. Rollback / Undo

How to safely reverse this slice.

---

## 9. Notes & Gotchas

Things future you will forget.

````

This structure already matches how your best slices are written (e.g. 1.1, 2.2, 2.5) but **makes it enforceable**.

---

## 4. ADR Governance (Non-Negotiable)

### `docs/handbook/02-adrs.md`

```md
# Architecture Decision Records (ADR)

ADRs capture *why*, not *how*.

## Create an ADR when:
- Choosing a region
- Choosing a service
- Choosing a security model
- Introducing irreversible complexity

## Never:
- Mix ADRs with slices
- Modify an ADR without review
````

### ADR Numbering Rules

* Sequential, never reused
* Superseded ADRs stay in repo
* Slice must reference ADR if architectural

This aligns with your existing ADR-007 and ADR patterns .

---

## 5. Runbooks vs Playbooks (Clear Boundary)

### Runbooks = Planned

* Deploy
* Rotate
* Backup
* Scale

### Playbooks = Incidents

* Outage
* Security breach
* Data corruption

This is already reflected in Slice 0.3 and later operational docs .

---

## 6. Naming, Tags, and Consistency

Create **one authoritative page**:

### `docs/handbook/06-naming-and-tags.md`

```md
# Naming & Tagging Standards

Naming follows Azure CAF:
{resource}-{workload}-{env}-{region}

Required Tags:
- Environment
- Project
- CostCenter
- ManagedBy
- Owner

If a resource lacks tags → it is non-compliant.
```

No slice should redefine naming or tags. They **reference this page**.

---

## 7. Template for NEW Slices (Quick Start)

When adding a new slice:

1. Copy `SLICE-TEMPLATE.md`
2. Place under correct `phase-X/`
3. Assign next slice number
4. Define acceptance criteria first
5. Only then write steps

**Rule:**

> If acceptance criteria are unclear → the slice is not ready.

---

## 8. Why This Works (Key Design Principles)

* **Separation of concerns**

  * Handbook = rules
  * Slices = execution
  * ADRs = decisions

* **Audit-ready**

  * Every decision traceable
  * Every change reviewable

* **Scalable**

  * New phases don’t add chaos
  * New contributors onboard in hours, not weeks

* **Replicable**

  * You can clone this structure to any future platform or program

