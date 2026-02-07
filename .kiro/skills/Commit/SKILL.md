---
name: git-commit-workflow
description: "Guides git commits with medical device traceability, Linear integration, and compliance formatting. Activate with #Commit or commit-related requests."
keywords: ["commit"]
tools: ["git", "mcp_linear_official_get_issue", "mcp_linear_official_create_comment"]
inclusion: auto
---

# Git Commit Workflow

## Purpose

This skill guides the creation of well-formed git commits with proper traceability for medical device software development. It ensures commits follow regulatory compliance requirements and maintain clear links between code changes, requirements, specifications, and test cases.

## Activation

Reference this skill with `#Commit` in chat when you're ready to commit changes.

## Overview

When the user asks you to commit changes, follow this process to create well-formed commits with proper traceability.

## Commit Message Format

```
[Linear: MIU-XXX] <type>: <subject>

<body explaining what and why>

Linear: MIU-XXX
Use case: uc-[name]
Requirements: RQ-[ID], RQ-[ID]
Specifications: SW-[ID], HW-[ID]
Implements CR: CR-[ID] (if applicable)
Mitigates risk: RISK-[ID] (if applicable)
Resolves anomaly: AN-[ID] (if applicable)
Tested by: TC-[ID], TC-[ID]
Change type: [Preventive|Corrective|Adaptive]
Risk impact: [Low|Medium|High]
```

### Commit Types

- `feat`: New feature or capability
- `fix`: Bug fix
- `docs`: Documentation changes
- `refactor`: Code restructuring without behavior change
- `test`: Adding or updating tests
- `chore`: Build process, dependencies, tooling

### Subject Line Rules

- Start with `[Linear: MIU-XXX]` if applicable
- Use imperative mood ("add" not "added")
- Keep under 72 characters total
- No period at the end
- Lowercase after type prefix

### Body Content

- Explain WHAT changed and WHY
- Describe implementation approach
- Note impact on other components
- Reference related work items

### Traceability Footer

Include applicable references for medical device compliance:
- **Linear Issue**: Work item tracking (MIU-XXX)
- **Use Case**: Feature context (uc-[name])
- **Requirements**: User/system requirements (RQ-[ID])
- **Specifications**: Design specs (SW-[ID], HW-[ID])
- **Change Requests**: Formal changes (CR-[ID])
- **Risks**: Risk mitigation/introduction (RISK-[ID])
- **Anomalies**: Bug fixes (AN-[ID])
- **Test Cases**: Verification tests (TC-[ID])
- **Change Type**: Preventive, Corrective, or Adaptive
- **Risk Impact**: Low, Medium, or High

## Workflow Steps

### 1. Analyze Changes

```bash
git status
git diff
```

Review conversation history:
- What was accomplished
- Why changes were made
- Which use cases/requirements affected
- Related Linear issues

### 2. Query Linear for Context

Use `mcp_linear_official_get_issue` to gather:
- Issue title and description
- Current status
- Related requirements/specs
- Team context
- Labels and project associations

### 3. Group Changes Logically

Create atomic commits:
- One logical change per commit
- Group related files together
- Separate concerns (feature code vs docs)

### 4. Draft Commit Messages

For each commit:
- Subject line with Linear ID (if applicable)
- Body explaining what and why
- Complete traceability footer

**Examples:**

**Feature with full traceability:**
```
[Linear: MIU-123] feat: add CGM data validation

Implement CSV validation for CGM uploads to ensure data integrity
before processing. Added format validation, range checks, and 
pseudonymization verification.

Validation includes:
- Required columns (timestamp, glucose value)
- Physiological range checks (20-600 mg/dL, 1.1-33.3 mmol/L)
- Minimum record count (100 readings)
- File size limits (<50MB)

Linear: MIU-123
Use case: uc-004-manage-cgm-upload-processing
Requirements: RQ-DATA-001, RQ-DATA-002, RQ-PSEUDO-001
Specifications: SW-UPLOAD-001, SW-VALIDATE-001
Mitigates risk: RISK-DATA-CORRUPT, RISK-PRIVACY-001
Tested by: TC-UPLOAD-001, TC-UPLOAD-002, TC-VALIDATE-001
Change type: Preventive
Risk impact: Low (improves data quality)
```

**Bug fix with anomaly reference:**
```
[Linear: MIU-456] fix: correct glucose unit conversion in AGP plot

Fixed incorrect conversion factor causing AGP plots to display
values 1.8% higher than actual. Updated GLUCOSE_CONVERSION_FACTOR
from 18.0 to 18.0182 per ISO 15197:2013 standard.

Root cause: Rounded conversion factor in unitUtils.ts
Impact: All AGP plots generated before this fix

Linear: MIU-456
Use case: uc-003-glucose-visualization
Requirements: RQ-UNIT-001
Specifications: SW-CONVERT-001
Resolves anomaly: AN-2024-015
Tested by: TC-UNIT-001, TC-AGP-003
Change type: Corrective
Risk impact: Medium (affects clinical interpretation)
```

**Documentation update:**
```
[Linear: MIU-789] docs: update CGM upload requirements

Clarify timezone handling requirements based on clinical feedback
and Freestyle Libre data model constraints. Added section on
DST transition detection and multi-timezone limitations.

Changes:
- Added Freestyle Libre data model constraints section
- Clarified single-timezone-per-upload assumption
- Updated DST handling requirements
- Added validation criteria for timestamp anomalies

Linear: MIU-789
Use case: uc-004-manage-cgm-upload-processing
Requirements: RQ-TIME-001, RQ-TIME-002
Specifications: SW-TIMEZONE-001
Change type: Adaptive
Risk impact: Low (documentation only)
```

**Refactoring without functional changes:**
```
[Linear: MIU-321] refactor: extract duplicate detection to service

Moved duplicate detection logic from routes.ts to dedicated
DuplicateDetector service for better testability and separation
of concerns. No functional changes to detection algorithm.

Benefits:
- Improved unit test coverage
- Clearer separation of concerns
- Easier to maintain and extend

Linear: MIU-321
Use case: uc-004-manage-cgm-upload-processing
Specifications: SW-UPLOAD-001
Tested by: TC-UPLOAD-005, TC-DUPLICATE-001
Change type: Preventive
Risk impact: Low (no functional changes)
```

**Spec/Use Case Updates:**
```
docs: refresh design for UC-004 CGM upload processing

Updated design document to reflect single-unit storage approach
(mmol/L only) per updated requirements. Removed dual-unit storage
from database schema and updated all data flow diagrams.

Key changes:
- Database schema: removed glucose_mg_dl field
- Updated unit conversion model
- Revised correctness properties
- Added benefits of single-unit storage

Use case: uc-004-manage-cgm-upload-processing
Requirements: RQ-UNIT-001, RQ-UNIT-002
Specifications: SW-STORAGE-001
Change type: Adaptive
Risk impact: Low (design documentation)
```

### 5. Present Plan to User

Show commit plan:
```
I'll create 2 commits:

Commit 1: [Linear: MIU-123] feat: add CGM data validation
Files: server/routes.ts, server/csvValidator.ts, shared/schema.ts

Commit 2: docs: update CGM upload requirements
Files: .kiro/specs/use-cases/uc-004-manage-cgm-upload-processing/requirements.md

Proceed?
```

### 6. Execute Commits

Upon confirmation:
```bash
git add <specific-files>
git commit -m "type: subject" -m "body text"
git log --oneline -n <number>
```

### 7. Update Linear Issues

After successful commits, add a comment to related Linear issues using `mcp_linear_official_create_comment`:

```markdown
✅ Commit created: [hash]

**Commit:** [type]: [subject]

**Files changed:**
- file1.ts
- file2.tsx

**Traceability:**
- Requirements: RQ-[ID]
- Specifications: SW-[ID]
- Tested by: TC-[ID]
```

This keeps Linear issues synchronized with git history and provides audit trail visibility.

## Critical Rules

### Attribution
- NEVER add co-author information
- NEVER include AI attribution
- NEVER add "Co-Authored-By" lines
- Write commits as if the user wrote them directly

### File Selection
- NEVER use `git add -A` or `git add .`
- ALWAYS specify individual files explicitly
- Exclude unrelated changes from commits

### Message Quality
- Be specific and descriptive in commit messages
- Include context for non-obvious changes
- Reference applicable traceability items
- Explain impact on other components
- Always query Linear for context before drafting messages

### Traceability
Include when applicable:
1. Linear issue ID
2. Use case reference (for feature work)
3. Requirements (RQ-[ID])
4. Specifications (SW-[ID])
5. Change Requests (CR-[ID])
6. Risk references (RISK-[ID])
7. Anomaly references (AN-[ID]) for bugs
8. Test cases (TC-[ID])
9. Change type classification
10. Risk impact assessment

Omit references when not applicable (e.g., docs may not have test cases).

## Project-Specific Context

### Linear Integration
- Query `mcp_linear_official_get_issue` before drafting
- Include issue ID in subject: `[Linear: MIU-XXX]`
- Reference in footer: `Linear: MIU-XXX`

### Use Case References
Always include for feature work:
```
Use case: uc-[name]
```

### Change Classification
Always include:
```
Change type: [Preventive|Corrective|Adaptive]
Risk impact: [Low|Medium|High]
```

- **Preventive**: New features, proactive improvements
- **Corrective**: Bug fixes, anomaly resolutions
- **Adaptive**: Documentation, refactoring, maintenance

### Medical Device Context
For clinical functionality changes, note:
- Data integrity implications
- Validation requirements
- glucose360 package integration
- Pseudonymization considerations
- Impact on clinical interpretation

## Quick Reference Templates

### Feature
```
[Linear: MIU-XXX] feat: [feature name]

[What was added and why]
[Implementation details]

Linear: MIU-XXX
Use case: uc-[name]
Requirements: RQ-[ID]
Specifications: SW-[ID]
Tested by: TC-[ID]
Change type: Preventive
Risk impact: [Low|Medium|High]
```

### Bug Fix
```
[Linear: MIU-XXX] fix: [issue fixed]

[Root cause]
[Solution]
[Impact]

Linear: MIU-XXX
Use case: uc-[name]
Resolves anomaly: AN-[ID]
Tested by: TC-[ID]
Change type: Corrective
Risk impact: [Low|Medium|High]
```

### Refactor
```
[Linear: MIU-XXX] refactor: [what changed]

[Why refactored]
[Benefits]
[No functional changes]

Linear: MIU-XXX
Use case: uc-[name]
Specifications: SW-[ID]
Tested by: TC-[ID]
Change type: Preventive
Risk impact: Low
```

### Documentation
```
[Linear: MIU-XXX] docs: [document updated]

[What changed]
[Why needed]

Linear: MIU-XXX
Use case: uc-[name]
Change type: Adaptive
Risk impact: Low
```

### Tests
```
[Linear: MIU-XXX] test: [what tested]

[Test coverage added]
[Test approach]

Linear: MIU-XXX
Use case: uc-[name]
Test cases: TC-[ID]
Change type: Preventive
Risk impact: Low
```