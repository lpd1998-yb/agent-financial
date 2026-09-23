# Repository Documentation Migration Part 1 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Establish repository governance and migrate the product, system, and domain portions of the initial monolithic design into focused authoritative documents without losing any accepted requirement.

**Architecture:** Migrate content by concern, keep exactly one owner for each concept, and use `docs/README.md` as the documentation entry point. Preserve decision history in ADRs, retain the original only until a heading-by-heading parity check passes, then remove it and rely on Git history.

**Tech Stack:** Markdown, Git, POSIX shell, `rg`, and standard Unix text utilities.

**Spec:** Current source: `docs/design/repository-and-documentation-governance-design.md`; canonical location after Task 1: `docs/architecture/repository-governance.md`.

## Global Constraints

- Each concept has one authoritative document; other files link to it instead of copying it.
- Stable design documents use stable English filenames without dates.
- Product intent, current architecture, historical decisions, implementation plans, and operational procedures remain separate.
- Maintained product and architecture documents contain the required Status, Owner, Last reviewed, Scope, and Related ADRs metadata.
- Migrated product and system architecture prose is Chinese; filenames, code identifiers, API names, and schema names are English. Agent instructions and these execution plans may remain in English.
- Documents should stay below roughly 400 lines and own one coherent concern.
- The migration changes documentation and governance only; it introduces no product code or dependencies.
- The monolithic design is deleted only after content parity and link verification pass.
- No credentials, private keys, tokens, production data, personal data, generated browser state, or debug output may enter Git.
- Every task ends with a focused commit and a clean whitespace check.

## Review Focus

1. **Requirement loss during splitting:** Part 2 Task 8 compares all 134 original headings with the destination map before deleting the source document.
2. **Competing sources of truth:** Tasks in both plan parts assign an explicit owner to every migrated subject; Part 2 Task 8 scans for stale sources and duplicated ownership.
3. **Broken navigation after deletion:** Part 2 Task 8 validates every repository-relative Markdown link and confirms no active link targets `docs/design/`.
4. **Time-sensitive vendor and pricing facts:** Task 7 records an authoritative URL and verification date for Tushare, model, Feishu, and Alibaba Cloud facts.
5. **Project guidance conflicting with credential or Git safety:** Task 1 verifies that project `AGENTS.md` forbids credential access and remote mutation without explicit authorization.

---

### Task 1: Accept and Install Repository Governance

**Files:**

- Move: `docs/design/repository-and-documentation-governance-design.md` → `docs/architecture/repository-governance.md`
- Create: `AGENTS.md`
- Modify: `docs/plans/repository-documentation-migration-part-1.md`

**Interfaces:**

- Consumes: The approved repository governance design and the user's global Codex guidance.
- Produces: The canonical governance document and project instructions used by every later task.

- [ ] **Step 1: Confirm migration preconditions**

Run:

```bash
git status --short
rg -n '^- Status: Accepted$' docs/design/repository-and-documentation-governance-design.md
test ! -e AGENTS.md
test ! -e docs/architecture/repository-governance.md
```

Expected: only the approved-spec and plan preparation changes are present, the accepted status is found, and both target files are absent.

- [ ] **Step 2: Move the governance document**

Run:

```bash
mkdir -p docs/architecture
git mv docs/design/repository-and-documentation-governance-design.md docs/architecture/repository-governance.md
```

Change this plan's `Spec` line to `**Spec:** docs/architecture/repository-governance.md.` and remove transitional references to the governance document's former path. Keep the rule that the initial monolith is removed only after parity verification.

- [ ] **Step 3: Create project AGENTS.md**

Create `AGENTS.md` with `Start Here`, `Documentation`, `Engineering Boundaries`, and `Security and Git` sections. Copy the accepted rules from governance §§6.1–6.4, including the exact prohibitions on reading or committing credentials and changing remote Git state without explicit user authorization.

- [ ] **Step 4: Verify and commit**

Run:

```bash
test -f AGENTS.md
test -f docs/architecture/repository-governance.md
test ! -f docs/design/repository-and-documentation-governance-design.md
for heading in 'Start Here' Documentation 'Engineering Boundaries' 'Security and Git'; do
  rg -q "^## ${heading}$" AGENTS.md
done
rg -q 'Never read, print, copy, upload, or commit credentials' AGENTS.md
rg -q 'Do not push, force-push' AGENTS.md
git diff --check
git add AGENTS.md docs/architecture/repository-governance.md docs/plans/repository-documentation-migration.md
git commit -m "docs: install repository governance"
```

Expected: all assertions pass, the whitespace check is silent, and the focused commit succeeds.

### Task 2: Create Product Vision and User Experience Documents

**Files:**

- Create: `docs/product/vision-and-scope.md`
- Create: `docs/product/user-experience.md`

**Interfaces:**

- Consumes: Initial design §§1, 2, 16, 21, 22, and 23.
- Produces: Authoritative product scope, milestone boundary, acceptance criteria, Feishu interaction model, and control-plane experience.

- [ ] **Step 1: Write vision-and-scope.md**

Use the required metadata followed by: 产品愿景; 目标用户与投资周期; 核心使用场景; MVP 范围; M0–M7 能力边界; 明确不做的内容; 产品成功标准; 最终验收标准.

Preserve these accepted constraints: personal use, A shares, monthly rather than short-term investing, no holdings integration, commercial model APIs allowed, M4 as the minimum expected product level, and research support rather than deterministic trading instructions.

- [ ] **Step 2: Write user-experience.md**

Use the required metadata followed by: 入口与用户旅程; 飞书普通对话; 研究任务对话; 回复形态与 PDF 访问; 连续对话与上下文; AI 研究控制台; 系统总览; 股票研究详情; 响应与可用性目标.

Migrate the product-facing content from §§16 and 21. Link to architecture for execution events, context storage, and telemetry rather than duplicating those mechanics.

- [ ] **Step 3: Verify and commit**

Run:

```bash
for file in docs/product/vision-and-scope.md docs/product/user-experience.md; do
  rg -q '^- Status: Accepted$' "$file"
  rg -q '^- Owner: Project$' "$file"
  rg -q '^- Last reviewed: 2026-09-23$' "$file"
  rg -q '^- Scope:' "$file"
  rg -q '^- Related ADRs:' "$file"
done
rg -q 'M4' docs/product/vision-and-scope.md
for term in 飞书 普通对话 PDF 控制台; do rg -q "$term" docs/product/user-experience.md; done
git diff --check
git add docs/product/vision-and-scope.md docs/product/user-experience.md
git commit -m "docs: define product scope and experience"
```

Expected: metadata and named capabilities are found and the commit succeeds.

### Task 3: Create Report, Investment View, and Feedback Documents

**Files:**

- Create: `docs/product/reports-and-investment-view.md`
- Create: `docs/product/feedback-and-revision.md`

**Interfaces:**

- Consumes: Initial design §§9, 10, 13, 14, 15, and 18.6–18.9.
- Produces: The report hierarchy, latest research view, cold-start behavior, feedback contract, and revision experience.

- [ ] **Step 1: Write reports-and-investment-view.md**

Use the required metadata followed by: 研究产物体系; 公司研究报告结构; 日报、周报和月报; 重大事件提醒; 最新投资分析观点; 观点变化解释; 首次研究与冷启动; 关注列表预热; PDF 研究资产; 内容质量与风险表达.

Preserve the report fields, evidence appendix, analysis expression rules, three frequencies, major-event behavior, falsification conditions, two-stage cold start, and permanent PDF availability. Use “研究观点” and “观察建议”; never promise returns.

- [ ] **Step 2: Write feedback-and-revision.md**

Use the required metadata followed by: 反馈目标; 认同度; 修改与调整建议; 反馈解释; 报告修订流程; 版本与审计; 反馈进入评测和调优.

Preserve the original feedback objects and states. Distinguish preference learning from correction of objective facts.

- [ ] **Step 3: Verify and commit**

Run:

```bash
for term in 日报 周报 月报 最新 冷启动 PDF; do rg -q "$term" docs/product/reports-and-investment-view.md; done
for term in 认同度 修改 修订 审计 评测; do rg -q "$term" docs/product/feedback-and-revision.md; done
git diff --check
git add docs/product/reports-and-investment-view.md docs/product/feedback-and-revision.md
git commit -m "docs: define reports and feedback"
```

Expected: every named behavior exists and the commit succeeds.

### Task 4: Create System and Domain Architecture

**Files:**

- Create: `docs/architecture/system-overview.md`
- Create: `docs/architecture/domain-model.md`

**Interfaces:**

- Consumes: Initial design §§3–7 plus domain objects in §§14.3, 15.3, 16.3, and 18.8.
- Produces: System boundaries, dependency direction, modules, aggregates, value objects, states, and invariants.

- [ ] **Step 1: Write system-overview.md**

Use the required metadata followed by: 架构目标; 系统上下文; 受控工作流与 Agent 节点; 飞书、n8n、Spring Boot 和 Spring AI 职责; 数据与大模型职责边界; 模块化单体边界; 同步与异步交互; 外部依赖; 演进原则.

Include Mermaid context and dependency diagrams. State that n8n owns orchestration, Spring Boot owns business rules and authoritative state, Spring AI integrates models and tools, and deterministic calculations do not use free-form model output.

- [ ] **Step 2: Write domain-model.md**

Use the required metadata followed by: 建模原则; Company 与 MarketData; Disclosure 与 Evidence; Research 与 InvestmentView; Report; Watchlist 与 Alert; Conversation; Execution 与 ResearchRun; Feedback 与 Revision; 聚合边界; 状态机与不变量.

For each aggregate record its ID, owned state, invariants, domain events, and cross-aggregate references by ID.

- [ ] **Step 3: Verify and commit**

Run:

```bash
for term in 飞书 n8n 'Spring Boot' 'Spring AI' 模块化单体; do rg -q "$term" docs/architecture/system-overview.md; done
for term in Company MarketData Disclosure Evidence Research InvestmentView Report Watchlist Conversation ResearchRun Feedback; do
  rg -q "$term" docs/architecture/domain-model.md
done
git diff --check
git add docs/architecture/system-overview.md docs/architecture/domain-model.md
git commit -m "docs: define system and domain architecture"
```

Expected: all components and domain concepts are found and the commit succeeds.
