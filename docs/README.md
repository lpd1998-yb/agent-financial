# 文档中心

本文件是项目文档的唯一入口。根 README 只介绍项目并链接到这里；具体产品、架构、决策和实施内容由下列单一权威文档维护。

## 1. 推荐阅读顺序

1. [产品愿景与范围](product/vision-and-scope.md)：理解用户、产品边界和 M0–M7。
2. [系统架构总览](architecture/system-overview.md)：理解飞书、n8n、Spring Boot、Spring AI 和数据系统的职责。
3. [领域模型](architecture/domain-model.md)：理解模块、聚合、状态和不变量。
4. [Agent 工作流](architecture/agent-workflow.md) 与 [数据和 RAG](architecture/data-and-rag.md)：理解 AI 任务如何受控执行。
5. [报告体系与最新研究观点](product/reports-and-investment-view.md)：理解用户最终获得的研究产物。
6. 当前里程碑的实施计划和相关 ADR。

## 2. 产品文档

| 文档 | 状态 | 权威范围 |
| --- | --- | --- |
| [产品愿景与范围](product/vision-and-scope.md) | Accepted | 用户、投资周期、产品边界、里程碑和最终验收 |
| [用户体验与交互](product/user-experience.md) | Accepted | 飞书对话、任务体验和 AI 研究控制台 |
| [报告体系与最新研究观点](product/reports-and-investment-view.md) | Accepted | 公司研究、日周月报告、冷启动、最新观点和 PDF |
| [反馈与报告修订](product/feedback-and-revision.md) | Accepted | 认同度、调整建议、修订、版本和调优信号 |

## 3. 架构文档

| 文档 | 状态 | 权威范围 |
| --- | --- | --- |
| [仓库与文档治理](architecture/repository-governance.md) | Accepted | 仓库结构、文档所有权、代码组织和变更规则 |
| [系统架构总览](architecture/system-overview.md) | Accepted | 系统上下文、组件职责、模块边界和依赖方向 |
| [领域模型](architecture/domain-model.md) | Accepted | 聚合、实体、值对象、状态机和不变量 |
| [Agent 工作流](architecture/agent-workflow.md) | Accepted | 计划、工具、执行节点、重试和发布门禁 |
| [数据与 RAG](architecture/data-and-rag.md) | Accepted | 结构化数据、PDF、检索、历史时点和 Citation |
| [对话与上下文](architecture/conversation-and-context.md) | Accepted | Prompt、模型路由、会话记忆和意图路由 |
| [可观测性与评测](architecture/observability-and-evals.md) | Accepted | Trace、指标、固定评测集和调优门禁 |
| [安全与可靠性](architecture/security-and-reliability.md) | Accepted | 凭据、网络、故障、降级、恢复、审计和 PDF 可用性 |
| [部署与成本](architecture/deployment-and-cost.md) | Accepted | 阿里云拓扑、存储、备份、规格、费用和人工前置事项 |

## 4. 架构决策

ADR 是历史决策记录，当前有效设计仍以架构文档为准。编号、状态和新增规则见 [ADR 索引](decisions/README.md)。

| ADR | 状态 | 决策 |
| --- | --- | --- |
| [ADR-0001](decisions/ADR-0001-controlled-workflow-with-agent-nodes.md) | Accepted | 受控工作流 + Agent 节点 |
| [ADR-0002](decisions/ADR-0002-tushare-primary-market-data-provider.md) | Accepted | Tushare 为第一版主要结构化数据源 |
| [ADR-0003](decisions/ADR-0003-postgresql-pgvector-storage.md) | Accepted | PostgreSQL + pgvector |
| [ADR-0004](decisions/ADR-0004-modular-monolith.md) | Accepted | Spring Boot 模块化单体 |

## 5. 实施计划

计划说明如何实施已批准的设计，不是第二份架构来源。计划索引和状态规则见 [实施计划说明](plans/README.md)。

当前已完成仓库文档迁移：

1. [迁移计划 Part 1](plans/repository-documentation-migration-part-1.md)
2. [迁移计划 Part 2](plans/repository-documentation-migration-part-2.md)

下一份计划是 M0 技术链路验证。M0 完成并验收后再详细规划 M1。

## 6. 运维手册

当前尚未进入部署实施，因此不创建空白 runbook 或无效链接。能力落地时同步创建：

- 本地开发与启动；
- 部署、升级和回滚；
- 备份与恢复；
- 故障响应；
- 凭据轮换。

运维步骤只能存在于 `docs/runbooks/`，架构文档只定义目标和约束。

## 7. 参考资料

| 文档 | 用途 |
| --- | --- |
| [术语表](reference/glossary.md) | 统一 Agent、ResearchRun、Evidence、RAG 等术语 |
| [外部服务参考](reference/external-services.md) | 账号前置、凭据类型、官方资料、价格入口和时效风险 |

时间敏感信息必须记录核验日期并链接官方来源。密钥和凭据值不得写入任何文档。

## 8. 当前阶段

- 文档治理与初始设计拆分：Complete
- 当前代码阶段：尚未创建应用代码
- 下一实施里程碑：M0 技术链路验证
- 首轮产品最低目标：M4 持续研究与飞书产品体验
- 远端状态：以 Git 状态和用户明确授权为准，本页不宣称已经推送或部署

## 9. 文档维护规则

- 一个概念只有一个权威文档，其他位置使用链接；
- 产品意图、当前架构、历史决策、实施计划、运维步骤和外部事实分开；
- 产品和架构文档修改时更新状态、复核日期和 Related ADRs；
- 跨模块耐久决策创建新 ADR，Accepted ADR 不重写历史；
- 行为变更与受影响文档在同一提交或明确关联的提交中更新；
- 单一文档超过约 400 行或包含多个独立关注点时拆分；
- M0–M7 每次只详细规划一个里程碑；
- 临时输出、浏览器状态、调试文件、生产数据和凭据不进入 Git；
- 提交前检查相对链接、占位项、元数据和空白错误。
