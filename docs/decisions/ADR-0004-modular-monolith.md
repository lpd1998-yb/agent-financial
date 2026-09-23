# ADR-0004：Spring Boot 模块化单体

- Status: Accepted
- Date: 2026-09-23
- Deciders: Project
- Supersedes: None

## Context

系统同时包含公司、市场数据、披露、证据、研究、报告、对话、关注列表和执行等业务能力。第一阶段由个人开发和运维，业务边界仍会随着 M0–M4 实施调整。

提前拆分微服务会引入网络调用、分布式事务、消息一致性、独立部署和可观测成本，但完全无模块边界的单体又会导致领域与供应商耦合。

## Decision

使用 Spring Boot 模块化单体：

- 代码按业务能力划分顶级模块，不按全局 controller/service/repository 分层；
- 每个模块内部可使用 domain/application/infrastructure/interfaces；
- 模块只能通过显式应用服务、领域事件或端口协作；
- 领域代码不依赖 Tushare、飞书、n8n、Spring AI、模型 SDK 或云厂商；
- API 与 Research Worker 使用同一代码库、不同运行配置；
- 数据库可共享实例，但表、迁移和所有权按模块明确。

## Alternatives

### 微服务

独立扩缩和隔离更强，但当前团队、规模和边界成熟度不支持其复杂度，未采用。

### 无边界分层单体

初始文件少，但业务规则、供应商适配和跨模块数据访问会快速纠缠，不利于测试和后续拆分，未采用。

### Serverless 函数集合

适合零散事件任务，但长研究流程、状态恢复、本地调试和统一领域模型更复杂，未采用为核心拓扑。

## Consequences

正面结果：

- 本地开发、事务、测试、部署和恢复简单；
- 模块边界可在单进程内验证；
- 避免不必要的网络和消息基础设施；
- 后续可按已验证边界拆分。

代价：

- 需要自动化或评审阻止跨模块内部访问；
- 批处理与 API 共享数据库资源，需要并发和容量控制；
- 单个部署故障影响多个模块。

仅在以下证据出现时评估拆分：

- 某模块需要独立扩缩且资源竞争持续影响 SLO；
- 故障隔离要求无法在进程内满足；
- 不同安全或数据边界要求独立部署；
- 团队所有权需要独立发布节奏；
- 单体构建、部署或数据库变更成为可测量瓶颈。

拆分必须创建新 ADR，并包含数据所有权、通信契约、迁移、可观测和回滚方案。

## References

- [系统架构总览](../architecture/system-overview.md)
- [领域模型](../architecture/domain-model.md)
- [仓库治理](../architecture/repository-governance.md)
