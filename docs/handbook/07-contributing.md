# Team Operating Procedure (TOP)

This document defines **how we work** in the Documentation Factory project.  
It is mandatory reading for all contributors.

If something is unclear, improve the documentation — do not work around it.

---

## 1. Core Principles

1. **Documentation is a product**
   - If it’s not documented, it doesn’t exist.
   - If it’s documented but not reproducible, it’s broken.

2. **Decisions before implementation**
   - Architectural choices are documented as ADRs *before* large implementation work.

3. **Reproducibility over speed**
   - Another team member must be able to follow your slice without asking you.

4. **Single source of truth**
   - MkDocs site is authoritative.
   - Slack, email, meetings are not sources of truth.

---

## 2. What We Produce

We produce four types of artifacts:

| Artifact | Purpose |
|--------|--------|
| **Handbook** | Rules, standards, and how we work |
| **Slices** | Step-by-step implementation guides |
| **ADRs** | Architecture decisions and rationale |
| **Runbooks / Playbooks** | Operations and incident response |

Each artifact has a **fixed location and template**.  
Do not invent new formats.

---

## 3. When to Create What

### Create an ADR when:
- Choosing a cloud service, region, or security model
- Making a decision that is hard or expensive to reverse
- Introducing a new architectural pattern

### Create or update a Slice when:
- Work is executable and produces concrete artifacts
- The steps can be followed by someone else
- Validation and rollback can be described

### Create a Runbook when:
- The task is planned and repeatable (deploy, rotate, backup)

### Create a Playbook when:
- The task is reactive or incident-based (outage, breach, corruption)

---

## 4. Standard Workflow

### Step 1 — Create an Issue
Every change starts with an issue describing:
- Goal
- Scope (what’s in / out)
- Acceptance criteria
- Related slices or ADRs

### Step 2 — Decide (ADR if needed)
If the work involves a decision:
- Write ADR
- Status = Proposed
- Get review
- Status = Accepted

### Step 3 — Implement (Slice)
- Use the canonical slice template
- Fill **acceptance criteria first**
- Write steps with commands and expected outcomes
- Include validation and rollback

### Step 4 — Open a Pull Request
- Use the PR checklist
- Attach validation evidence (logs, outputs, screenshots)
- Ensure Docs CI passes

### Step 5 — Review and Merge
- Reviewer checks reproducibility, not just correctness
- Slice status updated to **Complete**
- MkDocs becomes the new source of truth

---

## 5. Definition of Done

A change is **done** only if:

- [ ] Correct file location and structure
- [ ] Uses the appropriate template
- [ ] Acceptance criteria are met and validated
- [ ] Docs CI passes (`mkdocs build --strict`)
- [ ] Another person can follow it without clarification

If any box is unchecked, the work is not done.

---

## 6. Review Expectations

### Reviewers:
- Validate against acceptance criteria
- Challenge unclear steps
- Reject “tribal knowledge”

### Authors:
- Fix the documentation, not just the implementation
- Treat review comments as documentation bugs

---

## 7. Rules You Should Memorize

- Handbook defines standards; slices do not redefine them
- ADRs explain **why**; slices explain **how**
- No secrets in Git — ever
- No undocumented decisions
- No silent shortcuts

---

## 8. Improving This Process

If this process slows you down:
1. Identify friction
2. Propose an improvement
3. Update the handbook
4. Get it reviewed
5. Move on

Process evolves — chaos does not.

---

**If you’re unsure what to do next:**
- Read the Handbook
- Check existing slices
- Ask for review early
