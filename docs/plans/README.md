# 实施计划说明

本目录保存已经评审的执行计划。计划描述如何落地设计，是执行记录，不取代 `docs/product/`、`docs/architecture/` 或 ADR。

## 命名规则

- 产品里程碑：`M0-short-title.md`、`M1-short-title.md`，依次推进；
- 一次性治理或迁移：使用稳定的描述性文件名；
- 一个计划超过约 400 行时按连续 Part 拆分，并显式声明依赖和顺序；
- 文件名使用英文小写和连字符，不包含随意日期。

## 计划状态

- `Planned`：已经编写，等待设计或执行确认；
- `In progress`：正在执行，使用任务复选框和 SDD ledger 跟踪；
- `Complete`：所有任务、验证和最终评审完成；
- `Superseded`：后续计划替代，保留历史并链接新计划。

计划状态记录在本索引；任务执行状态以计划复选框和 Git 提交为依据。

## 执行规则

1. 先评审产品/架构设计，再编写计划；
2. M0 完成并验收后才详细编写 M1，避免远期计划假装精确；
3. 每个任务定义文件、输入输出、验证命令和聚焦提交；
4. 实施发现设计冲突时先记录裁决并更新权威设计；
5. 完成计划后保留为历史执行记录，不在计划中继续维护当前架构；
6. 生产发布、远端推送和破坏性操作仍需要用户明确授权。

## 计划索引

| 顺序 | 计划 | 状态 | 结果 |
| ---: | --- | --- | --- |
| 1 | [仓库文档迁移 Part 1](repository-documentation-migration-part-1.md) | Complete | 治理、产品、系统和领域文档 |
| 2 | [仓库文档迁移 Part 2](repository-documentation-migration-part-2.md) | Complete | AI/数据/运维架构、ADR、参考和索引 |

## 下一计划

下一步编写并评审 `M0-technical-chain.md`，覆盖飞书 → n8n → Spring Boot/Spring AI → Tushare/百炼 → PostgreSQL 的最小闭环、测试、部署和验收。M0 尚未开始实施。
