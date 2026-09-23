# 架构决策记录

本目录保存影响多个模块或长期演进的架构决策。当前有效架构由 `docs/architecture/` 维护，ADR 解释决策当时的背景、替代方案和代价。

## 状态

- `Proposed`：等待评审，不能作为实施依据。
- `Accepted`：已经批准，实施和架构文档必须遵循。
- `Superseded`：被后续 ADR 替代，历史内容保持不变。
- `Rejected`：经过评审但未采用。

## 编号和文件名

使用连续四位编号和稳定英文标题：

```text
ADR-NNNN-short-title.md
```

编号一旦分配不复用。新的反向决策创建新 ADR，并在旧 ADR 的 `Superseded by` 与新 ADR 的 `Supersedes` 中互相链接，不改写旧决策理由。

## 必需结构

每份 ADR 包含：

```text
Status / Date / Deciders / Supersedes
Context
Decision
Alternatives
Consequences
References
```

涉及主数据源、数据库或向量存储、Agent 执行模型、模块化单体/服务拓扑、主要云部署模型或跨模块基础设施时必须创建 ADR。局部字段、普通缺陷修复和不改变边界的重构不需要 ADR。

## 当前决策

| ADR | 状态 | 决策 |
| --- | --- | --- |
| [ADR-0001](ADR-0001-controlled-workflow-with-agent-nodes.md) | Accepted | 受控工作流 + Agent 节点 |
| [ADR-0002](ADR-0002-tushare-primary-market-data-provider.md) | Accepted | Tushare 为第一版主要结构化数据源 |
| [ADR-0003](ADR-0003-postgresql-pgvector-storage.md) | Accepted | PostgreSQL + pgvector |
| [ADR-0004](ADR-0004-modular-monolith.md) | Accepted | Spring Boot 模块化单体 |
