# ADR-0003：PostgreSQL 与 pgvector 统一存储

- Status: Accepted
- Date: 2026-09-23
- Deciders: Project
- Supersedes: None

## Context

第一版需要事务业务状态、版本化财经数据、执行事件、全文检索和向量检索。单用户和十余只关注股票的数据规模有限，独立向量数据库会增加部署、备份、一致性和学习成本。

研究证据必须与公司、文档版本、历史时点、报告和任务通过结构化元数据关联。

## Decision

第一版使用 PostgreSQL 作为事务与文本数据存储，并启用 pgvector：

- 领域聚合、快照、事件、报告元数据和审计记录存入 PostgreSQL；
- 文本片段使用 PostgreSQL 全文检索；
- Embedding 使用 pgvector；
- 混合检索在应用层合并、重排和去重；
- 原始 PDF 与正式报告文件存入 OSS，不写入数据库大对象；
- 数据库、向量索引和 Schema 变更统一备份与迁移。

## Alternatives

### 独立向量数据库

可以提供更强的向量检索扩缩和专有功能，但带来额外服务、同步、备份和权限体系。第一版规模不足以证明必要。

### Elasticsearch/OpenSearch 加向量

全文检索能力强，但资源和运维成本更高，事务业务数据仍需 PostgreSQL，不符合第一版简化目标。

### 仅使用关键词检索

部署简单，但对财报解释和语义相近表达的召回不足，不能满足 RAG 需求。

## Consequences

正面结果：

- 一个事务边界管理业务状态、元数据和证据索引；
- 备份、恢复和本地开发简单；
- 元数据硬过滤与向量检索结合自然；
- Spring AI 支持 PostgreSQL/PGVector。

代价和演进条件：

- 数据库同时承载事务和检索负载，需要监控慢查询、索引大小与批处理影响；
- Embedding 重建需要版本化和容量计划；
- 当向量规模、并发、延迟、召回能力或故障隔离连续达不到 SLO，且优化 PostgreSQL 后仍不足时，通过新 ADR 评估独立搜索/向量服务；
- 拆分前必须定义双写、回填、切换和回滚方案。

## References

- [数据与 RAG 架构](../architecture/data-and-rag.md)
- [部署与成本](../architecture/deployment-and-cost.md)
- [pgvector 官方项目](https://github.com/pgvector/pgvector)
