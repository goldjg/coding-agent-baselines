You are Codex implementing a focused AADLCv2 field-test follow-up PR in:

https://github.com/goldjg/coding-agent-baselines

Branch name:
feat/aadlcv2-prompt-as-code

Goal:
Integrate AADLCv2 field-test findings from the goldjg/m365-graph-schema-monitor PR1/PR2 exercise into the baseline instruction architecture.

This is a documentation/instruction-only change. Keep scope tight.

The field test showed:

* AADLCv2 worked well for small bounded PRs.
* Prompt-as-code solved long GitHub agent UI prompt truncation/misparse issues.
* Agents can over-anchor on previous PR contracts.
* AADLC needs clearer lifecycle semantics for completed PR contracts vs durable invariants.
* Model availability and model quality vary; fallback must not mutate the PR contract.
* Repeated corrective prompting is a failure signal; add a prompt ping-pong stop rule.
* For boundary-sensitive work, committed plan/spec files should be preferred over giant UI prompts.

Required changes

1. Add prompt-as-code plan artefact directory

Create:

.github/aadlc/plans/README.md
.github/aadlc/plans/plan-template.md

.github/aadlc/plans/README.md

Start with:

<!-- version: 1.0.0 -->
# AADLC Plans
This directory stores version-controlled planning artefacts for substantial, long, or boundary-sensitive agent tasks.

Required content:

* Explain that prompt-as-code should be used when a task is long, nested, security-sensitive, trust-boundary-changing, or likely to suffer UI prompt truncation.
* State that plan files are contracts, not scratchpads.
* State that the coding agent should read the relevant plan file and produce plan-only output before implementation.
* State that temporary root-level PLAN.md files are acceptable during feature-branch work, but should be removed or moved into .github/aadlc/plans/ before merge.
* State that plan files should not contain secrets, tenant data, private customer data, or live credentials.
* Include recommended naming:

.github/aadlc/plans/prN-short-description.md

* Include a “When to use prompt-as-code” list:
    * long or nested prompt
    * boundary-sensitive work
    * trust-boundary amendment
    * model comparison
    * repeated prompt misunderstanding
    * work requiring line-addressable instructions
    * mobile/UI prompt fragility

.github/aadlc/plans/plan-template.md

Start with:

<!-- version: 1.0.0 -->
# AADLC Plan Template

Include these sections:

## Plan metadata
- PR / branch:
- Status:
- Author:
- Created:
- Last updated:
## Task summary
## Current repository context
## Previous contract status
## Contract lifecycle note
## Intentional contract amendments
## Goal
## Non-goals
## Approved scope
## Forbidden scope
## Trust boundaries
## Invariants to preserve
## Expected files / directories
## Implementation phases
## Test strategy
## Acceptance criteria
## Prompt ping-pong budget
## Model fallback strategy
## Stop conditions
## Context reset requirements

Required wording under ## Contract lifecycle note:

Completed PR contracts are historical evidence, not binding scope. Only constraints explicitly promoted to durable invariants carry forward. This plan may intentionally amend previous constraints when the amendment is explicit, scoped, and recorded here.

Required wording under ## Prompt ping-pong budget:

One corrective prompt is acceptable. Two corrective prompts means reset the session. Three means abandon the session/model and restart with a fresh model or clearer plan.

Required wording under ## Model fallback strategy:

Model availability and capability are not stable invariants. If the preferred model is unavailable or repeatedly misinterprets the contract, switch model/session without changing the PR contract, non-goals, or acceptance criteria.

2. Update .github/aadlc/current-pr-contract.md

Bump version:

* <!-- version: 1.0.0 --> → <!-- version: 1.1.0 -->

Add or refine sections so it clearly distinguishes:

* active PR constraints
* completed PR constraints
* durable invariants
* intentional contract amendments

Add a new section after ## Goal:

## Contract status
<!-- draft | active | closed | superseded -->

Add a new section after ## Non-goals:

## Carry-forward rules
<!-- State which constraints are durable invariants and which are only scoped to this PR. Completed PR constraints are historical evidence unless explicitly promoted to invariants. -->

Add a new section after ## Approved scope:

## Intentional amendments
<!-- Explicitly record any intentional changes to previous PR constraints, trust boundaries, or assumptions. -->

Keep the existing sections:

* Goal
* Non-goals
* Approved scope
* Forbidden scope
* Architectural constraints
* Security constraints
* Files expected to change
* Tests / validation
* Stop conditions
* Escalation triggers
* Context reset notes

Do not remove existing guidance.

3. Update .github/instructions/core/pr-contract.instructions.md

Bump version:

* <!-- version: 1.0.0 --> → <!-- version: 1.1.0 -->

Add guidance for:

* completed PR contracts are historical evidence, not binding scope
* only promoted invariants carry forward
* new PRs may intentionally amend prior constraints when the amendment is explicit
* agents must not over-anchor on previous PR contracts
* if the current PR intentionally introduces a new trust boundary, the agent should treat that as an approved contract amendment only when recorded in the current PR contract or plan file

Include the named failure mode:

**Avoid stale contract anchoring.** Do not treat a completed PR constraint as durable law unless it has been promoted to an invariant.

Also add:

**Contract amendments must be explicit.** If a PR intentionally changes a previous constraint or introduces a new trust boundary, record the amendment in the current PR contract or plan file before implementation.

4. Update .github/instructions/core/aadlc.instructions.md

Bump version:

* <!-- version: 1.0.0 --> → <!-- version: 1.1.0 -->

Add prompt-as-code guidance:

* Use committed plan files for long, nested, boundary-sensitive, or model-comparison tasks.
* Prefer .github/aadlc/plans/prN-short-description.md.
* Temporary PLAN.md is acceptable on a feature branch but should be removed or archived before merge.
* For substantial work, the agent should read the plan file and respond in Plan-only mode before implementation.

Add a bullet:

**Use prompt-as-code for substantial tasks.** Store long or boundary-sensitive task contracts in `.github/aadlc/plans/` so prompts are version-controlled, diffable, and line-addressable.

Add a bullet:

**Stop prompt ping-pong early.** If more than one corrective prompt is required to understand the PR contract, reset the session or switch models instead of continuing to patch a failing mental frame.

5. Update .github/instructions/core/cognition-governance.instructions.md

Bump version:

* <!-- version: 1.0.0 --> → <!-- version: 1.1.0 -->

Add model fallback and correction-budget guidance:

* model availability is not stable
* model capability varies by task
* fallback must preserve the PR contract
* repeated correction is a session failure signal

Add bullets:

**Treat model availability as unstable.** Do not assume a named model will remain available or equally capable across sessions.
**Preserve the contract across model fallback.** If switching models, keep the same goal, non-goals, scope, invariants, and acceptance criteria unless the user explicitly amends them.
**Use a correction budget.** One corrective prompt is acceptable. Two means reset the session. Three means abandon the session/model and restart from a clearer plan.

6. Update .github/instructions/core/memory-cache.instructions.md

Bump version:

* <!-- version: 1.0.0 --> → <!-- version: 1.1.0 -->

Add guidance:

* field-test findings may be stored as durable project truth when they affect future workflow
* do not store all session chatter
* record prompt transport issues, model availability issues, and repeated failure modes when they are stable enough to guide future work

Add bullet:

**Record durable field-test lessons.** Persist recurring workflow hazards such as prompt transport failures, stale contract anchoring, model availability gaps, and repeated correction loops when they should influence future planning.

7. Update .github/aadlc/memory.md

Bump version:

* <!-- version: 1.0.0 --> → <!-- version: 1.1.0 -->

Lightly dogfood durable memory with stable project truths.

Populate only durable content. Do not create a session diary.

Under ## Project purpose, add:

This repository provides reusable GitHub Copilot coding-agent instruction packs and AADLCv2 governance artefacts for consistent, secure, maintainable, and governed AI-assisted development.

Under ## Architecture summary, add:

`.github/copilot-instructions.md` is the root operating model. `.github/instructions/` contains modular single-concern instruction packs. `.github/aadlc/` contains AADLCv2 governance artefacts and templates, not instruction-pack logic.

Under ## Core invariants, add:

- Instruction packs should remain modular and focused on a single concern.
- The root Copilot instructions act as the repository constitution.
- Existing language, platform, cloud, and core packs should not be modified as side effects of unrelated changes.
- AADLCv2 artefacts should reduce semantic rediscovery without becoming a per-turn session diary.
- Prompt-as-code should be used for substantial, long, or boundary-sensitive agent tasks.

Under ## Known sharp edges, add:

- Long nested prompts in agent UIs may truncate or misparse; prefer committed plan files for boundary-sensitive work.
- Agents may over-anchor on completed PR contracts; distinguish durable invariants from historical PR constraints.
- Model availability and capability can vary; fallback models must preserve the active PR contract.
- Repeated corrective prompting is a failure signal; reset the session or switch model instead of continuing prompt ping-pong.

Under ## Current operating assumptions, add:

Model availability is not a stable invariant. The PR contract remains the source of truth across model fallback.

Under ## Last updated, add:

2026-05-30 by Codex

8. Update .github/aadlc/repo-map.example.json

Keep valid JSON.

Add .github/aadlc/plans to directories:

".github/aadlc/plans": "Version-controlled prompt-as-code planning artefacts"

Add the two new plan files to key_files:

{
  "path": ".github/aadlc/plans/README.md",
  "purpose": "Prompt-as-code usage guidance"
},
{
  "path": ".github/aadlc/plans/plan-template.md",
  "purpose": "Reusable AADLC planning contract template"
}

Do not otherwise rewrite the file.

9. Update README.md

Bump version:

* <!-- version: 1.1.1 --> → <!-- version: 1.2.0 -->

Update Repository Structure tree:

* Under .github/aadlc/, add:

│   ├── plans/
│   │   ├── README.md                    # Prompt-as-code guidance for substantial tasks
│   │   └── plan-template.md             # Reusable AADLC planning contract template

Adjust tree glyphs correctly so repo-map.example.json remains the last file or move it after plans with valid tree formatting.

Update ## Cognition Governance Artefacts table:

* Add rows for:
    * .github/aadlc/plans/README.md
    * .github/aadlc/plans/plan-template.md

Add a new section after ## Cognition Governance Artefacts:

## Prompt-as-Code
For substantial, long, nested, or boundary-sensitive tasks, prefer a committed plan file over a large UI prompt.
Use `.github/aadlc/plans/prN-short-description.md` for durable planning artefacts. A temporary root-level `PLAN.md` may be used on a feature branch, but it should be removed or moved into `.github/aadlc/plans/` before merge.
Prompt-as-code makes task contracts version-controlled, diffable, auditable, and line-addressable. It also reduces risk from UI prompt truncation, mobile prompt fragility, and repeated prompt ping-pong.

In ### Copying packs into another repository, add a short paragraph:

For AADLCv2 usage, also copy `.github/aadlc/` and populate the artefacts for the target repository, especially `memory.md`, `current-pr-contract.md`, `invariants.yml`, `trust-boundaries.md`, `tool-policy.yml`, `repo-map.json`, and any relevant plan files under `.github/aadlc/plans/`.

10. Update .github/copilot-instructions.md

Bump version:

* <!-- version: 1.1.0 --> → <!-- version: 1.2.0 -->

In # Cognition Governance (AADLCv2), add after the paragraph ending with .github/aadlc/current-pr-contract.md:

Before any repository write, classify the action against `.github/aadlc/tool-policy.yml` and confirm it is allowed by the active PR contract.

Also add:

For substantial, long, nested, or boundary-sensitive tasks, prefer prompt-as-code in `.github/aadlc/plans/` over large UI prompts. The plan file is the task contract; read it before planning or implementation.

Also add:

Model fallback must preserve the active PR contract. If a model is unavailable or repeatedly misinterprets scope, switch model or reset the session without changing the goal, non-goals, invariants, or acceptance criteria.

Also add:

If more than one corrective prompt is required to understand the PR contract, reset the session or switch models instead of continuing prompt ping-pong.

Keep this section concise. Do not rewrite unrelated sections.

Hard constraints

* Do not add workflows.
* Do not add scripts.
* Do not add dependencies or package manifests.
* Do not modify any files under:
    * .github/instructions/languages/
    * .github/instructions/platform/
    * .github/instructions/cloud/
* Do not modify existing core packs except:
    * .github/instructions/core/aadlc.instructions.md
    * .github/instructions/core/cognition-governance.instructions.md
    * .github/instructions/core/memory-cache.instructions.md
    * .github/instructions/core/pr-contract.instructions.md
* Do not modify:
    * .github/instructions/core/baseline.instructions.md
    * .github/instructions/core/security.instructions.md
    * .github/instructions/core/dependency.instructions.md
    * .github/instructions/core/identity.instructions.md
    * .github/instructions/core/tool-permission-tiers.instructions.md
* Do not rename or move existing files.
* Do not alter Plan-only / Assisted implementation / Automatic mode semantics.
* Do not introduce the phrase Default to shallow pattern-matching.
* Do not put secrets, tenant data, customer data, or credentials in any plan/memory artefact.
* Do not make broad README rewrites.

Expected changed files

Exactly these files should change or be created:

.github/copilot-instructions.md
.github/aadlc/memory.md
.github/aadlc/current-pr-contract.md
.github/aadlc/repo-map.example.json
.github/aadlc/plans/README.md
.github/aadlc/plans/plan-template.md
.github/instructions/core/aadlc.instructions.md
.github/instructions/core/cognition-governance.instructions.md
.github/instructions/core/memory-cache.instructions.md
.github/instructions/core/pr-contract.instructions.md
README.md

No other files should change.

Validation steps

Run after implementation:

Set-Location C:\GitHub\coding-agent-baselines
Write-Output "== changed files =="
git --no-pager status --short
git --no-pager diff --name-only HEAD
Write-Output "`n== expected file set check =="
$expected = @(
'.github/copilot-instructions.md',
'.github/aadlc/memory.md',
'.github/aadlc/current-pr-contract.md',
'.github/aadlc/repo-map.example.json',
'.github/aadlc/plans/README.md',
'.github/aadlc/plans/plan-template.md',
'.github/instructions/core/aadlc.instructions.md',
'.github/instructions/core/cognition-governance.instructions.md',
'.github/instructions/core/memory-cache.instructions.md',
'.github/instructions/core/pr-contract.instructions.md',
'README.md'
) | Sort-Object
$actual = git --no-pager diff --name-only HEAD | Sort-Object
$unexpected = Compare-Object $expected $actual
if ($unexpected) { $unexpected; "FAIL: changed file set differs from expected" } else { "OK: changed file set exactly matches expected" }
Write-Output "`n== version checks =="
if ((Get-Content README.md -TotalCount 1) -match 'version:\s*1\.2\.0') { "OK: README version 1.2.0" } else { "FAIL: README version" }
if ((Get-Content .github/copilot-instructions.md -TotalCount 1) -match 'version:\s*1\.2\.0') { "OK: copilot-instructions version 1.2.0" } else { "FAIL: copilot-instructions version" }
if ((Get-Content .github/aadlc/memory.md -TotalCount 1) -match 'version:\s*1\.1\.0') { "OK: memory version 1.1.0" } else { "FAIL: memory version" }
if ((Get-Content .github/aadlc/current-pr-contract.md -TotalCount 1) -match 'version:\s*1\.1\.0') { "OK: PR contract version 1.1.0" } else { "FAIL: PR contract version" }
if ((Get-Content .github/instructions/core/aadlc.instructions.md -TotalCount 1) -match 'version:\s*1\.1\.0') { "OK: aadlc pack version 1.1.0" } else { "FAIL: aadlc pack version" }
if ((Get-Content .github/instructions/core/cognition-governance.instructions.md -TotalCount 1) -match 'version:\s*1\.1\.0') { "OK: cognition governance pack version 1.1.0" } else { "FAIL: cognition governance pack version" }
if ((Get-Content .github/instructions/core/memory-cache.instructions.md -TotalCount 1) -match 'version:\s*1\.1\.0') { "OK: memory-cache pack version 1.1.0" } else { "FAIL: memory-cache pack version" }
if ((Get-Content .github/instructions/core/pr-contract.instructions.md -TotalCount 1) -match 'version:\s*1\.1\.0') { "OK: pr-contract pack version 1.1.0" } else { "FAIL: pr-contract pack version" }
Write-Output "`n== new plan file header checks =="
if ((Get-Content .github/aadlc/plans/README.md -TotalCount 1) -match 'version:\s*1\.0\.0') { "OK: plans README version 1.0.0" } else { "FAIL: plans README version" }
if ((Get-Content .github/aadlc/plans/plan-template.md -TotalCount 1) -match 'version:\s*1\.0\.0') { "OK: plan template version 1.0.0" } else { "FAIL: plan template version" }
Write-Output "`n== prohibited scope checks =="
$files = git --no-pager diff --name-only HEAD
if ($files | Select-String '^\.github/instructions/(languages|platform|cloud)/') { "FAIL: language/platform/cloud pack changed" } else { "OK: language/platform/cloud packs untouched" }
if ($files | Select-String '^\.github/instructions/core/(baseline|security|dependency|identity|tool-permission-tiers)\.instructions\.md$') { "FAIL: protected core pack changed" } else { "OK: protected core packs untouched" }
if ($files | Select-String '^\.github/workflows/|package\.json|requirements\.txt|Makefile|\.sh$') { "FAIL: prohibited file type changed" } else { "OK: no workflows/scripts/dependencies added" }
Write-Output "`n== content checks =="
$c = Get-Content .github/copilot-instructions.md -Raw
if ($c -match 'Before any repository write, classify the action against `.github/aadlc/tool-policy.yml`') { "OK: write-control sentence present" } else { "FAIL: write-control sentence missing" }
if ($c -match 'prompt-as-code') { "OK: prompt-as-code root guidance present" } else { "FAIL: prompt-as-code root guidance missing" }
if ($c -match 'Model fallback must preserve the active PR contract') { "OK: model fallback root guidance present" } else { "FAIL: model fallback root guidance missing" }
if ($c -match 'prompt ping-pong') { "OK: prompt ping-pong root guidance present" } else { "FAIL: prompt ping-pong root guidance missing" }
if ($c -match 'Default to shallow pattern-matching') { "FAIL: prohibited phrase found" } else { "OK: prohibited phrase absent" }
$r = Get-Content README.md -Raw
if ($r -match '## Prompt-as-Code') { "OK: README prompt-as-code section present" } else { "FAIL: README prompt-as-code section missing" }
if ($r -match '\.github/aadlc/plans/plan-template.md') { "OK: README documents plan template" } else { "FAIL: README missing plan template" }
if ($r -match 'For AADLCv2 usage, also copy `.github/aadlc/`') { "OK: README AADLC copy guidance present" } else { "FAIL: README AADLC copy guidance missing" }
$p = Get-Content .github/aadlc/plans/plan-template.md -Raw
if ($p -match 'Completed PR contracts are historical evidence, not binding scope') { "OK: contract lifecycle wording present" } else { "FAIL: contract lifecycle wording missing" }
if ($p -match 'One corrective prompt is acceptable') { "OK: correction budget wording present" } else { "FAIL: correction budget wording missing" }
if ($p -match 'Model availability and capability are not stable invariants') { "OK: model fallback wording present" } else { "FAIL: model fallback wording missing" }
Write-Output "`n== JSON parse check =="
python -m json.tool .github/aadlc/repo-map.example.json > $null
if ($LASTEXITCODE -eq 0) { "OK: repo-map.example.json parses" } else { "FAIL: repo-map.example.json parse failed" }

Final response format

Use exactly these headings:

* summary
* changes
* tests run/not run
* risks

Keep the final response concise but include any validation failures honestly.
