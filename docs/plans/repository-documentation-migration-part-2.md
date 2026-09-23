# Repository Documentation Migration Part 2 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Complete the architecture, decision, reference, and index migration, prove content parity, and retire the initial monolithic design.

**Architecture:** Continue from Part 1's accepted governance and product/core-architecture documents. Migrate the remaining concerns into single-owner documents, record durable choices as ADRs, validate every source heading and relative link, then remove the transitional source.

**Tech Stack:** Markdown, Git, POSIX shell, `rg`, Ruby, and standard Unix text utilities.

**Spec:** `docs/architecture/repository-governance.md`.

**Dependency:** Complete `docs/plans/repository-documentation-migration-part-1.md` through Task 4 before starting this plan.

## Global Constraints

- Each concept has one authoritative document; other files link to it instead of copying it.
- Maintained product and architecture documents include complete governance metadata.
- Migrated product and system architecture prose is Chinese; filenames, identifiers, API names, and schema names are English. Agent instructions and these execution plans may remain in English.
- Each document owns one coherent concern and stays below roughly 400 lines.
- This migration changes documentation and governance only.
- Delete the source only after all 134 headings have verified destinations.
- Never commit credentials, private keys, tokens, production data, personal data, generated state, or debug output.
- End each task with a focused commit and a clean whitespace check.

## Review Focus

1. **Missing AI or operational requirements:** Tasks 5 and 6 preserve workflow, RAG, context, evaluation, security, reliability, deployment, and cost constraints.
2. **Lost decision rationale:** Task 7 records the four accepted durable choices and their alternatives and consequences.
3. **Time-sensitive external facts:** Task 7 attaches authoritative links and verification dates without storing credential values.
4. **Requirement loss during deletion:** Task 8 maps and checks all 134 source headings before removal.
5. **Broken navigation or stale sources:** Task 8 checks all relative links and active-document references after removal.

---

### Task 5: Create Agent, RAG, and Conversation Architecture

**Files:**

- Create: `docs/architecture/agent-workflow.md`
- Create: `docs/architecture/data-and-rag.md`
- Create: `docs/architecture/conversation-and-context.md`

**Interfaces:**

- Consumes: Initial design §§8, 11, 12, 17, and technical parts of §21.
- Produces: Execution state machines, tool boundaries, evidence rules, prompt/model routing, and context policy.

- [ ] **Step 1: Write agent-workflow.md**

Use the required metadata followed by: 执行原则; 任务类型; 结构化分析计划; 研究状态机; 工具分层与契约; Agent 节点输入输出; 审批、重试与恢复边界; 质量门禁; 幂等性与审计事件.

Preserve the controlled workflow choice, original transitions, tool groups, task types, and explicit schema/budget constraints.

- [ ] **Step 2: Write data-and-rag.md**

Use the required metadata followed by: 数据分类与权威来源; Tushare 结构化数据; 公告和财报; PDF 保存与解析; 复杂表格; 分块、嵌入与索引; 混合检索与重排; 历史时点一致性; 引用与证据链; 防幻觉; 数据质量与回补.

Keep financial facts out of vector-only retrieval, enforce publication-time availability, require page/table citation locations, and define the low-confidence table-extraction fallback.

- [ ] **Step 3: Write conversation-and-context.md**

Use the required metadata followed by: Prompt 分层; 版本管理; 模型路由; 会话记忆与研究事实; 上下文组装; 分章节生成; 飞书意图; 普通/研究对话路由; 引用约束; Token 与成本预算.

State that conversation memory is not evidence and ordinary chat does not create a PDF unless requested or required by research intent.

- [ ] **Step 4: Verify and commit**

Run:

```bash
rg -q '受控工作流' docs/architecture/agent-workflow.md
rg -q '幂等' docs/architecture/agent-workflow.md
for term in 历史时点 复杂表格 引用 防幻觉; do rg -q "$term" docs/architecture/data-and-rag.md; done
rg -q '会话记忆.*不是.*证据\|会话记忆.*不.*证据' docs/architecture/conversation-and-context.md
rg -q '普通对话' docs/architecture/conversation-and-context.md
git diff --check
git add docs/architecture/agent-workflow.md docs/architecture/data-and-rag.md docs/architecture/conversation-and-context.md
git commit -m "docs: define agent data and conversation architecture"
```

Expected: all constraints are found and the commit succeeds.

### Task 6: Create Evaluation, Reliability, and Deployment Architecture

**Files:**

- Create: `docs/architecture/observability-and-evals.md`
- Create: `docs/architecture/security-and-reliability.md`
- Create: `docs/architecture/deployment-and-cost.md`

**Interfaces:**

- Consumes: Initial design §§18.1–18.5, 18.9, 19, and 20.
- Produces: Evaluation gates, telemetry, failure handling, security, Alibaba Cloud topology, backup, and cost envelope.

- [ ] **Step 1: Write observability-and-evals.md**

Use the required metadata followed by: 可观测目标; ResearchRun Trace; 系统、RAG、报告、Agent、产品和成本指标; 固定评测集; 发布门禁; 反馈驱动调优.

Separate offline evaluation, pre-release regression, production monitoring, and user feedback signals.

- [ ] **Step 2: Write security-and-reliability.md**

Use the required metadata followed by: 威胁与故障模型; 凭据和权限; 网络安全; 数据隐私; 幂等、重试和熔断; 降级; 任务恢复; 告警; 审计; PDF 长期可访问性.

Preserve the original failure categories, retry/degradation rules, signed private-asset access, and distinction between durable PDFs and service health.

- [ ] **Step 3: Write deployment-and-cost.md**

Use the required metadata followed by: 部署原则; 阿里云拓扑; 组件清单; 环境; 域名/HTTPS/公网; 云盘/OSS; 数据库/备份; 规格演进; 月度成本; 人工前置事项; 部署验收.

Keep the 2-core/8-GB trial and 4-core/8-GB estimates distinct. Mark prices as estimates and identify Alibaba Cloud account, real-name verification, domain, ICP, Feishu, Tushare, and model-provider actions that require the user.

- [ ] **Step 4: Verify and commit**

Run:

```bash
for term in RAG 报告 Agent 成本 评测; do rg -q "$term" docs/architecture/observability-and-evals.md; done
for term in 凭据 幂等 重试 降级 审计 PDF; do rg -q "$term" docs/architecture/security-and-reliability.md; done
for term in 阿里云 HTTPS 云盘 备份 月度 用户; do rg -q "$term" docs/architecture/deployment-and-cost.md; done
git diff --check
git add docs/architecture/observability-and-evals.md docs/architecture/security-and-reliability.md docs/architecture/deployment-and-cost.md
git commit -m "docs: define operations and deployment architecture"
```

Expected: all operational concerns are found and the commit succeeds.

### Task 7: Record Decisions and External References

**Files:**

- Create: `docs/decisions/README.md`
- Create: `docs/decisions/ADR-0001-controlled-workflow-with-agent-nodes.md`
- Create: `docs/decisions/ADR-0002-tushare-primary-market-data-provider.md`
- Create: `docs/decisions/ADR-0003-postgresql-pgvector-storage.md`
- Create: `docs/decisions/ADR-0004-modular-monolith.md`
- Create: `docs/reference/glossary.md`
- Create: `docs/reference/external-services.md`

**Interfaces:**

- Consumes: Initial design §§3, 4, 11.1, 12, 20, and 24 plus governance ADR rules.
- Produces: Decision rationale, terminology, and dated external-service facts.

- [ ] **Step 1: Create the ADR set**

In `docs/decisions/README.md`, define Proposed, Accepted, Superseded, and Rejected states, numbering, required Context/Decision/Alternatives/Consequences/References sections, and supersession rules.

Record these Accepted decisions:

- ADR-0001: controlled workflow with Agent nodes; compare fully autonomous and fully hard-coded execution.
- ADR-0002: Tushare as first-version structured A-share provider; official disclosures remain filing authority and adapters prevent domain coupling.
- ADR-0003: PostgreSQL plus pgvector; state when a separate vector database would become justified.
- ADR-0004: Spring Boot modular monolith; state measurable triggers for service extraction.

- [ ] **Step 2: Create glossary.md and external-services.md**

Define Agent, Agent node, controlled workflow, ResearchRun, Evidence, Citation, InvestmentView, cold start, point-in-time consistency, RAG, reranking, revision, and quality gate.

For Tushare, Feishu, the selected model provider, Alibaba Cloud ECS/cloud disk/OSS/domain/HTTPS/ICP, PostgreSQL, n8n, and Spring AI, record purpose, owner, account prerequisite, credential type by name only, authoritative documentation URL, pricing URL where applicable, verification date, and quota/renewal risk. Never record credential values.

- [ ] **Step 3: Verify and commit**

Run:

```bash
for file in docs/decisions/ADR-*.md; do
  for heading in Context Decision Alternatives Consequences References; do rg -q "^## ${heading}$" "$file"; done
  rg -q '^- Status: Accepted$' "$file"
done
for term in ResearchRun Evidence Citation InvestmentView RAG; do rg -q "$term" docs/reference/glossary.md; done
for term in Tushare Feishu Alibaba PostgreSQL n8n 'Spring AI'; do rg -q "$term" docs/reference/external-services.md; done
rg -q 'Last verified\|最后核验' docs/reference/external-services.md
! rg -n '(BEGIN (RSA|OPENSSH|EC) PRIVATE KEY|password\s*[:=]|token\s*[:=]\s*[^<])' docs AGENTS.md
git diff --check
git add docs/decisions docs/reference
git commit -m "docs: record decisions and external references"
```

Expected: ADR shape and references pass, no credential-like value is found, and the commit succeeds.

### Task 8: Build the Index, Prove Parity, and Retire the Monolith

**Files:**

- Create: `docs/README.md`
- Create: `docs/plans/README.md`
- Modify: `README.md`
- Modify: migrated documents when parity or link defects are found
- Delete: `docs/design/2026-09-22-a-share-research-agent-design.md`
- Delete directory when empty: `docs/design/`

**Interfaces:**

- Consumes: All Task 1–7 outputs and all 134 headings in the initial design.
- Produces: The only documentation entry point, complete source map, valid navigation, and no transitional monolith.

- [ ] **Step 1: Create plan and documentation indexes**

`docs/plans/README.md` defines plan naming, Planned/In progress/Complete states, checkbox tracking, one-milestone-at-a-time planning, and lists both migration-plan parts in execution order. Completed plans are historical execution records, not architecture sources.

`docs/README.md` contains: 推荐阅读顺序; 产品文档; 架构文档; 架构决策; 实施计划; 运维手册; 参考资料; 当前阶段; 维护规则. List every authoritative file, its status, and its ownership boundary. Do not create dead runbook links; state that runbooks arrive with operated capabilities.

- [ ] **Step 2: Update root README.md**

Keep the concise product summary. Replace the monolith link with one primary link to `docs/README.md`, identify M0 as next, and do not duplicate the index.

- [ ] **Step 3: Audit all source headings**

Use this map while reviewing all 134 source headings:

```text
§§1-2     -> product/vision-and-scope.md
§§3-5     -> architecture/system-overview.md
§§6-7     -> architecture/domain-model.md
§8        -> architecture/agent-workflow.md
§§9-10    -> product/reports-and-investment-view.md
§11       -> architecture/agent-workflow.md
§12       -> architecture/data-and-rag.md
§§13-15   -> product/reports-and-investment-view.md
§16       -> product/user-experience.md + architecture/domain-model.md
§17       -> architecture/conversation-and-context.md
§18.1-5   -> architecture/observability-and-evals.md
§18.6-8   -> product/feedback-and-revision.md + architecture/domain-model.md
§18.9     -> architecture/observability-and-evals.md
§19       -> architecture/security-and-reliability.md
§20       -> architecture/deployment-and-cost.md
§21       -> product/user-experience.md + architecture/conversation-and-context.md
§§22-23   -> product/vision-and-scope.md
§24       -> decisions/ADR-0001..0004 + owning architecture documents
```

Run before deletion:

```bash
test "$(rg '^#{1,6} ' docs/design/2026-09-22-a-share-research-agent-design.md | wc -l | tr -d ' ')" = 134
rg '^#{1,6} ' docs/design/2026-09-22-a-share-research-agent-design.md
find docs/product docs/architecture docs/decisions docs/reference -name '*.md' -print0 | xargs -0 rg '^#{1,6} '
```

Expected: every source heading has an explicit destination and every accepted requirement appears there. Repair the owning destination before continuing.

- [ ] **Step 4: Remove the migrated source**

Run:

```bash
git rm docs/design/2026-09-22-a-share-research-agent-design.md
rmdir docs/design
```

Expected: the source and empty transitional directory disappear; history remains in Git.

- [ ] **Step 5: Validate relative Markdown links**

Run from the repository root:

```bash
ruby -e '
files = Dir.glob("{README.md,AGENTS.md}") + Dir.glob("docs" + "/**" + "/*.md")
missing = []
files.each do |file|
  File.read(file).scan(/\[[^\]]+\]\((?!https?:|mailto:|#)([^)#]+)(?:#[^)]+)?\)/).flatten.each do |target|
    path = File.expand_path(target, File.dirname(file))
    missing << "#{file} -> #{target}" unless File.exist?(path)
  end
end
abort("Broken links:\n#{missing.join("\n")}") unless missing.empty?
puts "Relative Markdown links: PASS"
'
```

Expected: `Relative Markdown links: PASS`.

- [ ] **Step 6: Run final governance checks**

Run:

```bash
test -f AGENTS.md
test -f docs/README.md
test -f docs/architecture/repository-governance.md
test ! -d docs/design
test "$(find docs/product -name '*.md' | wc -l | tr -d ' ')" = 4
test "$(find docs/architecture -name '*.md' | wc -l | tr -d ' ')" = 9
test "$(find docs/decisions -name 'ADR-*.md' | wc -l | tr -d ' ')" = 4
for file in docs/product/*.md docs/architecture/*.md; do
  for field in Status Owner 'Last reviewed' Scope 'Related ADRs'; do rg -q "^- ${field}: ." "$file"; done
done
! rg -n 'docs/design/|2026-09-22-a-share-research-agent-design' README.md AGENTS.md docs/README.md docs/product docs/architecture docs/decisions docs/reference --glob '!repository-governance.md'
! rg -n '^- (Status|Owner|Last reviewed|Scope|Related ADRs):\s*$' docs/product docs/architecture
! rg -n 'TO[D]O|TB[D]|FIXM[E]|待[补]充|待[定]' README.md AGENTS.md docs
git diff --check
```

Expected: every command exits 0 with no stale path, empty metadata, placeholder marker, or whitespace error.

- [ ] **Step 7: Review and commit**

Run:

```bash
git status --short
git diff --stat
git diff -- README.md docs/README.md docs/plans/README.md
```

Confirm that only documentation and governance changed, then run:

```bash
git add README.md docs
git commit -m "docs: complete documentation migration"
git status --short
git log --oneline -8
```

Expected: the working tree is clean and the log shows focused governance, product, architecture, decision/reference, and final-migration commits.
