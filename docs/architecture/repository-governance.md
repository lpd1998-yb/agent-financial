# Repository and Documentation Governance Design

- Status: Accepted
- Owner: Project
- Last reviewed: 2026-09-23
- Scope: Defines the repository layout, documentation ownership, change rules, code organization, and migration of the initial monolithic design document.
- Related ADRs: ADR-0004

## 1. Purpose

At the start of the migration, the repository contained a short root README and one 1,300-line design document. That document was useful as an initial design snapshot but was too broad to remain the only source of truth during implementation.

This governance design establishes:

- where product, architecture, decision, plan, runbook, and reference documents live;
- which document is authoritative for each concern;
- how future Codex sessions discover and update project guidance;
- where backend, frontend, automation, infrastructure, and evaluation code live;
- when an architecture decision record is required;
- how the existing monolithic design is migrated without leaving competing copies.

## 2. Principles

1. Each concept has one authoritative document.
2. Other documents link to the authoritative source instead of copying content.
3. Stable design documents use stable filenames without dates.
4. Historical decisions and implementation plans use numbered or milestone-specific filenames.
5. Product intent, current architecture, historical decisions, implementation plans, and operational procedures remain separate.
6. Accepted documents contain no unresolved placeholder markers.
7. A document that exceeds roughly 400 lines or owns more than one independent concern should be split.
8. Product and system architecture documents use Chinese prose and English filenames, code identifiers, API names, and schema names. Agent instructions and implementation plans may use English when required by their execution templates.
9. A behavior-changing implementation updates its affected documentation in the same commit or an explicitly linked commit.
10. Temporary discussion artifacts, browser mockup caches, generated output, and debug files are not committed.

## 3. Repository Layout

```text
agent-financial/
├── AGENTS.md
├── README.md
│
├── docs/
│   ├── README.md
│   ├── product/
│   │   ├── vision-and-scope.md
│   │   ├── user-experience.md
│   │   ├── reports-and-investment-view.md
│   │   └── feedback-and-revision.md
│   ├── architecture/
│   │   ├── repository-governance.md
│   │   ├── system-overview.md
│   │   ├── domain-model.md
│   │   ├── agent-workflow.md
│   │   ├── data-and-rag.md
│   │   ├── conversation-and-context.md
│   │   ├── observability-and-evals.md
│   │   ├── security-and-reliability.md
│   │   └── deployment-and-cost.md
│   ├── decisions/
│   │   ├── README.md
│   │   └── ADR-NNNN-short-title.md
│   ├── plans/
│   │   ├── README.md
│   │   └── MN-short-title.md
│   ├── runbooks/
│   │   ├── local-development.md
│   │   ├── deployment.md
│   │   ├── backup-and-restore.md
│   │   └── incident-response.md
│   └── reference/
│       ├── glossary.md
│       └── external-services.md
│
├── apps/
│   ├── research-service/
│   └── control-plane-web/
├── automation/
│   └── n8n/
│       └── workflows/
├── infra/
│   ├── docker/
│   ├── terraform/
│   └── scripts/
├── evals/
│   ├── cases/
│   ├── datasets/
│   └── baselines/
└── .github/
    └── workflows/
```

Only directories with content are created. Empty implementation directories are introduced by the milestone that first needs them.

## 4. Documentation Ownership

### 4.1 Product documents

`docs/product/` owns the user problem, product boundary, interaction model, report experience, and feedback behavior. It does not define infrastructure or implementation steps.

### 4.2 Architecture documents

`docs/architecture/` owns the currently valid technical design and repository governance rules. These files are living documents and change when the accepted architecture or governance model changes.

### 4.3 Architecture decision records

`docs/decisions/` records why a durable choice was made. An accepted ADR is not rewritten to hide history. A later decision supersedes it with a new ADR.

An ADR is required when changing:

- the primary financial data provider;
- the database or vector storage technology;
- the controlled Agent execution model;
- the modular monolith boundary or service topology;
- the primary cloud deployment model;
- an infrastructure dependency that affects multiple modules.

Routine bug fixes, field changes, and local refactors do not require ADRs.

### 4.4 Plans

`docs/plans/` contains implementation plans for M0 through M7. A plan explains how an accepted design is implemented; it does not become a second architecture source of truth.

Plans are created one milestone at a time. M0 is planned, implemented, and accepted before creating the detailed M1 execution plan.

### 4.5 Runbooks

`docs/runbooks/` contains procedures that an operator can execute: local startup, deployment, backup restoration, incident response, and credential rotation. Runbooks are introduced alongside the capability they operate.

### 4.6 Reference documents

`docs/reference/` contains shared terminology and external service facts such as API ownership, account prerequisites, permissions, pricing links, and renewal requirements. Time-sensitive facts include their verification date and authoritative source.

## 5. Document Metadata

Every maintained product and architecture document begins with:

```markdown
# Title

- Status: Draft | Proposed | Accepted | Superseded
- Owner: Project
- Last reviewed: YYYY-MM-DD
- Scope: What this document owns and explicitly does not own.
- Related ADRs: ADR-NNNN, or None.
```

`docs/README.md` is the only documentation entry point. It lists:

- the recommended reading order;
- every authoritative document and its scope;
- current document status;
- accepted ADRs;
- the current implementation milestone;
- links to active plans and runbooks.

## 6. Project-Level AGENTS.md

The repository root contains `AGENTS.md`. It supplements the user's global Codex guidance and applies only to this project.

It requires future agents to:

### 6.1 Before work

- read `docs/README.md` first;
- load only the documents relevant to the current task;
- read the active milestone plan before implementation;
- report conflicts between code, plans, ADRs, and architecture instead of silently choosing one;
- preserve unrelated user changes.

### 6.2 Documentation changes

- update the single authoritative document;
- link instead of duplicating sections;
- create an ADR for durable cross-module decisions;
- keep implementation mechanics in plans and runbooks;
- exclude temporary mockups, generated output, and local debug state.

### 6.3 Code changes

- preserve the modular monolith and explicit module boundaries;
- keep domain code independent of Tushare, Feishu, n8n, Spring AI, and model SDKs;
- integrate external systems through ports and adapters;
- keep controllers and Agent tools free of core business calculations;
- version database changes with migrations;
- test deterministic financial calculations;
- use contract tests or stable fixtures for external services;
- cover critical user flows with integration tests;
- never commit credentials, `.env` files, private keys, production data, or personal data.

### 6.4 Git operations

- keep each commit focused on one intent;
- run relevant tests and documentation checks before committing;
- avoid unrelated formatting or refactors;
- use the `codex/` prefix for new branches unless the user specifies otherwise;
- do not push, force-push, rewrite published history, or create a pull request without explicit user authorization.

## 7. Code Organization

### 7.1 Backend

The backend is a modular monolith in `apps/research-service`. Top-level packages are organized by business capability rather than global technical layers:

```text
company/
marketdata/
disclosure/
evidence/
research/
report/
conversation/
watchlist/
execution/
shared/
```

Each business module may contain:

```text
domain/
application/
infrastructure/
interfaces/
```

Module internals are not accessed directly across boundaries. Cross-module interaction uses explicit application services, domain events, or declared ports.

### 7.2 Frontend

The Vue control plane in `apps/control-plane-web` is organized by product feature:

```text
src/
├── app/
├── features/
│   ├── research-runs/
│   ├── reports/
│   ├── evidence/
│   ├── watchlist/
│   └── feedback/
└── shared/
```

### 7.3 Automation, infrastructure, and evaluation

- Versioned n8n workflow exports live in `automation/n8n/workflows/`.
- Docker, Terraform, and deployment scripts live under `infra/`.
- Flyway migrations live with the backend application.
- Evaluation cases, frozen datasets, and approved baselines live under `evals/`.
- Production-derived fixtures must be anonymized and minimized before entering Git.

## 8. Change Workflow

### 8.1 Product change

1. Update the owning product document.
2. Identify affected architecture documents.
3. Add an ADR when the change alters a durable cross-module decision.
4. Update or create the relevant milestone plan.
5. Implement and test.
6. Update runbooks when operation changes.

### 8.2 Architecture change

1. Explain the problem and alternatives.
2. Create or supersede an ADR.
3. Update the living architecture document.
4. Update affected milestone plans.
5. Implement only after design review.

### 8.3 Implementation change

Implementation work follows the active milestone plan. If implementation discovers a design contradiction, work stops at that boundary and the relevant design is reviewed before continuing.

## 9. Migration of the Initial Design

The initial `docs/design/2026-09-22-a-share-research-agent-design.md` is split into authoritative product and architecture documents. This document is canonical at `docs/architecture/repository-governance.md`; the completed repository contains no transitional `docs/design/` directory.

Migration rules:

1. Preserve all accepted requirements and decisions.
2. Remove duplicated explanations during the split.
3. Add relative links between related documents.
4. Create `docs/README.md` and update the root `README.md`.
5. Create the repository-level `AGENTS.md`.
6. Create initial ADRs for the controlled workflow, Tushare provider, PostgreSQL with pgvector, and modular monolith.
7. Remove the monolithic design document from the active tree after content parity is verified.
8. Rely on Git history rather than keeping a duplicate archive document.
9. Perform the migration as documentation-only commits without product code or dependency changes.

## 10. Verification

The migration is complete when:

- every original heading maps to one authoritative destination;
- no accepted requirement is lost;
- no active document duplicates another document's owned content;
- all relative links resolve;
- `docs/README.md` identifies the current source of truth;
- the root `AGENTS.md` matches the documented governance rules;
- the root README points to the documentation index;
- repository search finds no stale link to the removed monolithic file;
- documentation files pass Markdown and whitespace checks;
- the Git diff contains only documentation and governance changes.
