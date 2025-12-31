# Slice 0.2: GitHub Organization & Repository Setup

**Phase:** 0 – Foundation  
**Status:** Ready  
**Estimated Time:** 30–45 minutes  
**Primary Owner:** Platform / DevOps Lead

---

## 1. Purpose
Establish the GitHub repository structure and organization settings to support the Documentation Factory:
- Create repository with proper naming and visibility
- Configure branch protection rules
- Set up team access and permissions
- Initialize issue templates for slices, ADRs, and incidents
- Configure repository settings for security and collaboration

This ensures:
- Consistent repository structure
- Protected main branch with required reviews
- Clear contribution guidelines
- Security scanning enabled
- Team collaboration patterns established

---

## 2. Architectural Context
- **Depends on:** None (foundational slice)
- **Related ADRs:** ADR-001 (Repository structure and governance)
- **Enables:** Slice 0.3 (Documentation Framework), Slice 0.4 (Secrets & Environment Management), all subsequent slices

---

## 3. Prerequisites

### Completed Slices
- None (this is a foundational slice)

### Access Required
- [ ] GitHub Organization Admin or Owner access
- [ ] Ability to create repositories
- [ ] Ability to configure organization settings
- [ ] Ability to manage teams and permissions

---

## 4. What You Will Build
| Component | Description |
|---|---|
| GitHub Repository | Main repository for Documentation Factory with proper settings |
| Branch Protection | Rules for main branch requiring reviews and status checks |
| Issue Templates | Templates for slices, ADRs, bugs, and feature requests |
| `.github/` configuration | Repository automation and settings |
| Team Access | Proper team assignments with appropriate permissions |
| Security Settings | Dependabot, code scanning, and secret scanning enabled |

---

## 5. Step-by-Step Procedure
> No unexplained jumps. Every command must be runnable.

### Step 5.1 – Create Repository
**Objective:** Create the GitHub repository with proper initial structure.

**Commands / Actions:**
```bash
# Set variables for your organization and repository
ORG_NAME="kindredgroup"
REPO_NAME="Documentation-Factory-Handbook"

# Using GitHub CLI (if available)
gh repo create ${ORG_NAME}/${REPO_NAME} \
  --public \
  --description "Single source of truth for how we design, decide, build, document, and extend the Documentation Factory" \
  --enable-issues \
  --enable-wiki=false

# Clone repository locally
gh repo clone ${ORG_NAME}/${REPO_NAME}
cd ${REPO_NAME}

# Initialize basic structure
mkdir -p .github/ISSUE_TEMPLATE .github/workflows
mkdir -p docs/{handbook,slices,adr,runbooks,playbooks,architecture}
mkdir -p docs/slices/{phase-0,phase-1,phase-2}
```

**Expected Outcome:**
- Repository exists in GitHub organization
- Local clone is ready for configuration
- Basic directory structure is in place

---

### Step 5.2 – Configure Repository Settings
**Objective:** Set repository settings for security, collaboration, and automation.

**Commands / Actions:**
Navigate to repository Settings in GitHub UI:

**General:**
- ✅ Issues enabled
- ❌ Wiki disabled (using docs/ instead)
- ❌ Projects disabled (using Issues/Milestones)
- ✅ Allow merge commits
- ✅ Allow squash merging
- ❌ Allow rebase merging
- ✅ Automatically delete head branches

**Security & Analysis:**
- ✅ Dependency graph enabled
- ✅ Dependabot alerts enabled
- ✅ Dependabot security updates enabled
- ✅ Code scanning enabled (GitHub Actions)
- ✅ Secret scanning enabled
- ✅ Push protection enabled

**Branches:**
See Step 5.3 for branch protection configuration.

**Expected Outcome:**
- Repository configured for security and best practices
- Automated security scanning active
- Merge strategy standardized

---

### Step 5.3 – Configure Branch Protection
**Objective:** Protect main branch with required reviews and status checks.

**Commands / Actions:**
Navigate to Settings → Branches → Add branch protection rule:

**Branch name pattern:** `main`

**Protection settings:**
- ✅ Require a pull request before merging
  - ✅ Require approvals (minimum: 1)
  - ✅ Dismiss stale pull request approvals when new commits are pushed
  - ✅ Require review from Code Owners (if CODEOWNERS file exists)
- ✅ Require status checks to pass before merging
  - ✅ Require branches to be up to date before merging
  - Status checks to require: (add as workflows are created)
    - `docs-validation`
    - `link-check`
- ✅ Require conversation resolution before merging
- ✅ Require linear history
- ❌ Do not allow bypassing the above settings (even for administrators)
- ✅ Restrict who can push to matching branches
  - Add: Administrators, specific teams if needed
- ✅ Allow force pushes: Everyone (for PR branch management)
- ❌ Allow deletions

**Expected Outcome:**
- Main branch cannot be directly pushed to
- All changes require PR with review
- Status checks must pass before merge

---

### Step 5.4 – Create Issue Templates
**Objective:** Standardize issue creation with templates for slices, ADRs, and bugs.

**Commands / Actions:**
```bash
# Create slice issue template
cat > .github/ISSUE_TEMPLATE/slice.md << 'EOF'
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
EOF

# Create ADR issue template
cat > .github/ISSUE_TEMPLATE/adr.md << 'EOF'
---
name: Architecture Decision Record
about: Document an architectural decision
title: "ADR-XXX: <decision title>"
labels: ["adr", "architecture"]
---

## Context
What is the issue that we're seeing that is motivating this decision or change?

## Decision
What is the change that we're proposing and/or doing?

## Consequences
What becomes easier or more difficult to do because of this change?

### Positive
- 

### Negative
- 

### Neutral
- 
EOF

# Create bug issue template
cat > .github/ISSUE_TEMPLATE/bug.md << 'EOF'
---
name: Bug Report
about: Report a bug or issue
title: "[BUG] "
labels: ["bug"]
---

## Description
A clear description of the bug.

## Steps to Reproduce
1. 
2. 
3. 

## Expected Behavior
What should happen?

## Actual Behavior
What actually happens?

## Environment
- Slice/Component: 
- Environment: (dev/test/stage/prod)
- Date/Time: 

## Additional Context
Any other relevant information.
EOF

# Create feature request template
cat > .github/ISSUE_TEMPLATE/feature.md << 'EOF'
---
name: Feature Request
about: Suggest a new feature or enhancement
title: "[FEATURE] "
labels: ["enhancement"]
---

## Problem Statement
What problem does this solve?

## Proposed Solution
How should this work?

## Alternatives Considered
What other approaches were considered?

## Additional Context
Any other relevant information.
EOF

# Create template config
cat > .github/ISSUE_TEMPLATE/config.yml << 'EOF'
blank_issues_enabled: false
contact_links:
  - name: Documentation
    url: https://{ORG_NAME}.github.io/{REPO_NAME}/
    about: Read the handbook and slice documentation
EOF

# Note: Replace {ORG_NAME} and {REPO_NAME} with actual values after creating the file

# Commit templates
git add .github/ISSUE_TEMPLATE/
git commit -m "Add issue templates for slices, ADRs, bugs, and features"
```

**Expected Outcome:**
- Issue templates available when creating new issues
- Consistent structure for different issue types
- Clear guidance for contributors

---

### Step 5.5 – Configure Team Access
**Objective:** Assign teams with appropriate permissions.

**Commands / Actions:**
Navigate to Settings → Collaborators and teams:

**Recommended structure:**
| Team | Permission | Purpose |
|------|-----------|---------|
| Platform Engineers | Admin | Full access for infrastructure and tooling |
| Documentation Team | Write | Create PRs, manage issues, review docs |
| Developers | Write | Contribute to slices and documentation |
| Stakeholders | Read | View-only access for oversight |

```bash
# Using GitHub CLI (example)
# Note: Replace team slugs with your organization's actual team names
ORG_NAME="kindredgroup"
REPO_NAME="Documentation-Factory-Handbook"

# Add team to repository with admin permission
gh api -X PUT /orgs/${ORG_NAME}/teams/platform-engineers/repos/${ORG_NAME}/${REPO_NAME} \
  -f permission=admin

# Add team to repository with write permission
gh api -X PUT /orgs/${ORG_NAME}/teams/documentation-team/repos/${ORG_NAME}/${REPO_NAME} \
  -f permission=push

# Or configure via GitHub UI (Settings → Collaborators and teams)
```

**Expected Outcome:**
- Teams have appropriate access levels
- Clear ownership and contribution paths
- Separation of concerns by team role

---

### Step 5.6 – Add Repository Files
**Objective:** Create README, LICENSE, and contributing guidelines.

**Commands / Actions:**
```bash
# README already exists, ensure it's comprehensive
# (See repository for actual README content)

# Create CONTRIBUTING.md
cat > CONTRIBUTING.md << 'EOF'
# Contributing to Documentation Factory Handbook

## How to Contribute

### For Slices
1. Check if a slice issue exists for your work
2. If not, create one using the "New Slice" template
3. Follow the slice template structure
4. Submit a PR with your slice documentation
5. Request review from team members

### For ADRs
1. Create an issue using the "Architecture Decision Record" template
2. Discuss with team before writing
3. Follow ADR template in `docs/adr/templates/`
4. Submit PR with ADR document
5. Requires approval from architecture team

### For Bug Fixes
1. Create issue using "Bug Report" template
2. Reference issue in your PR
3. Include validation steps

## Style Guide
- Use clear, actionable language
- Include runnable commands
- Document expected outcomes
- Add validation steps
- Consider rollback scenarios

## Review Process
- At least 1 approval required
- All status checks must pass
- All conversations must be resolved
- Squash and merge preferred

EOF

# Create CODE_OF_CONDUCT.md (optional but recommended)
cat > CODE_OF_CONDUCT.md << 'EOF'
# Code of Conduct

## Our Pledge
We are committed to providing a welcoming and inclusive environment for all contributors.

## Our Standards
- Be respectful and constructive
- Focus on what is best for the project
- Accept feedback graciously
- Show empathy towards others

## Enforcement
Violations can be reported to project maintainers.
EOF

# Commit files
git add CONTRIBUTING.md CODE_OF_CONDUCT.md
git commit -m "Add contributing guidelines and code of conduct"
git push origin main
```

**Expected Outcome:**
- Clear contribution guidelines available
- Code of conduct established
- Professional and welcoming repository

---

## 6. Validation Checklist

- [ ] Repository exists in GitHub organization
- [ ] Branch protection active on `main` (verify cannot push directly)
- [ ] Issue templates appear when creating new issue
- [ ] Team members can access repository with correct permissions
- [ ] Dependabot alerts enabled and visible
- [ ] Secret scanning active
- [ ] README renders correctly on repository homepage
- [ ] Repository is public (or visibility as required)
- [ ] Can create PR successfully
- [ ] PR requires review before merge

---

## 7. Artifacts Produced

- GitHub repository: `kindredgroup/Documentation-Factory-Handbook`
- `.github/ISSUE_TEMPLATE/slice.md`
- `.github/ISSUE_TEMPLATE/adr.md`
- `.github/ISSUE_TEMPLATE/bug.md`
- `.github/ISSUE_TEMPLATE/feature.md`
- `.github/ISSUE_TEMPLATE/config.yml`
- `CONTRIBUTING.md`
- `CODE_OF_CONDUCT.md`
- Branch protection rules configured
- Team access configured
- Security features enabled

---

## 8. Rollback / Undo

If settings need to be reverted:

**Branch Protection:**
- Navigate to Settings → Branches → Edit rule
- Modify or delete protection rule

**Issue Templates:**
```bash
git rm -r .github/ISSUE_TEMPLATE/
git commit -m "Remove issue templates"
git push
```

**Repository Archive:**
- Settings → General → Danger Zone → Archive repository
- Can be unarchived later if needed

**Repository Deletion:**
- Settings → General → Danger Zone → Delete repository
- Requires typing repository name to confirm
- Cannot be undone after 90 days

---

## 9. Notes & Gotchas

**Branch Protection:**
- If you enable "Do not allow bypassing" for administrators, even admins cannot force-push to main. This is often desired but be aware.
- Status checks can only be added after the checks exist in workflows.

**Teams:**
- Teams must exist in the organization before they can be added to repositories.
- Outside collaborators are managed separately from teams.

**Issue Templates:**
- YAML frontmatter must be valid for templates to appear.
- blank_issues_enabled: false prevents creating issues without templates.

**Security:**
- Secret scanning may create alerts for historical commits if secrets were committed before enabling.
- Dependabot PRs will be created automatically for vulnerable dependencies.

**Best Practices:**
- Keep main branch protection strict from day one.
- Review Dependabot PRs promptly to stay secure.
- Regularly audit team access and permissions.
- Use labels consistently across issues and PRs.