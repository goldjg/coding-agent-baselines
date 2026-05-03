<!-- version: 1.0.0 -->
# coding-agent-baselines

A curated set of GitHub Copilot coding-agent instruction packs for consistent, secure, and maintainable AI-assisted development.

---

## Overview

This repository provides modular instruction files (packs) that tell GitHub Copilot's coding agent how to behave across different languages, platforms, and cloud providers. Each pack encodes opinionated engineering conventions so that every agent session starts from a known, high-quality baseline without the need to re-specify those conventions on a per-task basis.

The packs are composed together in [`.github/copilot-instructions.md`](.github/copilot-instructions.md), which GitHub Copilot reads automatically for every task run inside this repository.

---

## Repository Structure

```
.github/
├── copilot-instructions.md          # Root composition file – loaded by Copilot automatically
└── instructions/
    ├── core/
    │   ├── baseline.instructions.md     # Engineering operating model (plan-first, small changes, no skipped tests)
    │   ├── security.instructions.md     # Security baseline (no hard-coded secrets, least privilege, SSRF prevention)
    │   ├── dependency.instructions.md   # Dependency discipline (no CVEs, prefer native, justify every addition)
    │   └── identity.instructions.md    # Identity & trust baseline (validate tokens, avoid confused deputy)
    ├── languages/
    │   ├── python.instructions.md       # Python conventions (stdlib-first, type hints, safe subprocess)
    │   ├── typescript.instructions.md   # TypeScript conventions (strict types, runtime validation, no `any`)
    │   ├── javascript.instructions.md   # JavaScript conventions (no eval, safe DOM, no client-side secrets)
    │   ├── terraform.instructions.md    # Terraform conventions (plan-before-apply, least privilege IAM, no secrets in state)
    │   ├── powershell.instructions.md   # PowerShell conventions (ShouldProcess, no secrets in logs, Graph least-privilege)
    │   └── html.instructions.md         # HTML conventions (semantic markup, accessibility, CSP-aware)
    ├── platform/
    │   ├── cicd.instructions.md         # CI/CD conventions (pin actions, OIDC over stored creds, no secret leaks)
    │   ├── docker.instructions.md       # Docker conventions (non-root images, no secrets baked in, minimal layers)
    │   └── kubernetes.instructions.md   # Kubernetes conventions (least-privilege RBAC, pod security, no hostPath)
    └── cloud/
        ├── azure.instructions.md        # Azure conventions (managed identity, Key Vault, private endpoints)
        ├── entra.instructions.md        # Microsoft Entra ID conventions (CA policy safety, least-privilege app permissions)
        ├── microsoft-graph.instructions.md # Microsoft Graph conventions (v1.0 preferred, no broad permissions, pagination)
        ├── gcp.instructions.md          # Google Cloud Platform conventions (Workload Identity, narrow IAM roles)
        └── netlify.instructions.md      # Netlify conventions (no secrets in bundles, CORS, secure Functions)

README.md                                # This file
```

---

## Intent

The goal of this repository is to act as a **reusable, version-controlled baseline** for teams and individuals who use GitHub Copilot's coding agent. Instead of relying on ad-hoc prompts or hoping the agent applies good defaults, the packs enforce:

| Concern | What the packs enforce |
|---|---|
| **Engineering discipline** | Plan-first, small reversible changes, tests are not optional |
| **Security** | No hard-coded secrets, input validation, least privilege everywhere |
| **Dependency hygiene** | No new packages for <300 LOC tasks, no unresolved Critical/High CVEs |
| **Identity safety** | Token audience/issuer validation, no confused deputy, no ROPC |
| **Cloud security** | Managed/workload identity over static keys, private endpoints, narrow RBAC |
| **Container security** | Non-root images, no privileged containers, no Docker socket mounts |
| **CI/CD safety** | Pinned action SHAs, OIDC federation, no secret leaks in logs |
| **Code quality** | Language-specific conventions for Python, TypeScript, JavaScript, Terraform, PowerShell, and HTML |

---

## How It Works

1. GitHub Copilot automatically reads `.github/copilot-instructions.md` at the start of every agent session in this repository.
2. That file contains the full text of the **Copilot Project Operating Model** — an overarching set of principles (plan-first, spec before code, security as a constraint, etc.).
3. The individual pack files under `.github/instructions/` are referenced by Copilot's context system and are surfaced by the agent's instruction packs capability. Each file covers a single concern (language, platform, or cloud provider) and is self-contained so it can be reused or referenced independently.

---

## Pack Categories

### Core Packs
Foundational rules that apply to every task, regardless of language or platform:
- **Baseline** – operating mode, change discipline, test requirements
- **Security** – authentication/authorisation, injection prevention, secret hygiene
- **Dependency Discipline** – CVE management, native-first preference, justification requirement
- **Identity & Trust** – token validation, trust boundary discipline

### Language Packs
Language-specific conventions and guardrails:
- **Python** – stdlib-first, type hints, safe subprocess usage, no shell=True
- **TypeScript** – strict typing, `unknown` over `any`, runtime validation at boundaries
- **JavaScript** – safe DOM, no eval, no client-side secrets, CommonJS/ESM hygiene
- **Terraform** – plan-before-apply, no secrets in state, least-privilege IAM, pinned providers
- **PowerShell** – `ShouldProcess` for destructive actions, no secrets in logs, Graph least-privilege
- **HTML** – semantic markup, accessibility, safe template rendering, no raw HTML injection

### Platform Packs
Deployment and infrastructure conventions:
- **CI/CD** – OIDC over stored credentials, pinned action SHAs, no secret leaks, deployment gates
- **Docker** – non-root images, no baked-in secrets, minimal layers, `.dockerignore`
- **Kubernetes** – least-privilege RBAC, pod security contexts, no hostPath, NetworkPolicy

### Cloud Packs
Provider-specific security and operational guidance:
- **Azure** – managed identity, Key Vault, private endpoints, no stored credentials
- **Microsoft Entra ID** – CA policy safety, app permission least-privilege, break-glass accounts
- **Microsoft Graph** – v1.0 preferred, narrow permission scopes, pagination handling
- **Google Cloud Platform** – Workload Identity Federation, narrow IAM roles, no service account keys
- **Netlify** – no secrets in frontend bundles, CORS discipline, secure Netlify Functions

---

## Usage

### Using this repository directly

Fork or copy this repository into your own GitHub account or organisation. GitHub Copilot will automatically pick up the instruction file and packs when running coding-agent tasks inside the repository.

### Copying packs into another repository

Copy any individual pack file from `.github/instructions/` into the corresponding path in your target repository. Then reference the pack from your own `.github/copilot-instructions.md` or let Copilot discover it via the instructions directory.

### Versioning

Each markdown file carries a `<!-- version: X.Y.Z -->` comment at the top. Increment the version when making substantive changes to a pack's guidance, following [Semantic Versioning](https://semver.org/):
- **MAJOR** – breaking change to conventions (removes previously required behaviour)
- **MINOR** – new guidance added in a backwards-compatible way
- **PATCH** – clarifications, corrections, or minor wording improvements

---

## Contributing

1. Open an issue describing the convention gap or new pack you want to add.
2. Follow the existing file naming pattern: `<name>.instructions.md` in the appropriate subdirectory.
3. Keep each pack focused on a single concern.
4. Add a `<!-- version: 1.0.0 -->` comment at the top of any new file.
5. Update this README to reflect the new pack.

---

## License

See repository root for licence information.
