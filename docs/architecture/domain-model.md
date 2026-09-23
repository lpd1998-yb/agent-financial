# 领域模型

- Status: Accepted
- Owner: Project
- Last reviewed: 2026-09-23
- Scope: 定义业务模块、聚合、实体、值对象、状态和不变量；不定义数据库表、API DTO 或供应商适配器。
- Related ADRs: ADR-0001, ADR-0002, ADR-0003, ADR-0004

## 1. 建模原则

1. 时间是一等公民：区分报告期、正式披露时间、数据可用时间、系统获取时间和分析截止时间。
2. 原始事实尽量不可变：数据或文档修订创建新版本，不覆盖支撑历史报告的版本。
3. 结论必须有依据：重要结论引用结构化事实、`MetricResult` 或 `Citation`。
4. 原始数据、程序计算和模型解释分层保存，不能混入一个不可审计 JSON。
5. 领域表达研究状态、估值吸引力、风险和证据强度，不直接建模买卖或仓位指令。
6. 聚合通过 ID 引用其他聚合；跨模块由应用服务、领域事件或端口协作。
7. 外部供应商类型不能进入领域对象。

通用值对象包括 `CompanyId`、`SecurityId`、`DocumentId`、`ReportId`、`ResearchRunId`、`TradingDate`、`ReportingPeriod`、`AvailabilityTime`、`AsOfTime`、`Money`、`Ratio` 和 `SourceRef`。

## 2. Company 与 MarketData

### 2.1 Company 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `companyId` |
| 持有状态 | 公司法定身份、名称历史、行业分类引用、证券 ID 集合 |
| 实体/值对象 | `Security`、`IndustryClassificationRef` |
| 不变量 | 公司与证券分离；证券代码带交易所；名称和行业变化保留生效时间 |
| 事件 | `CompanyResolved`、`SecurityListed`、`IndustryClassificationChanged` |
| 外部引用 | 仅保存 `securityId`、`industryClassificationId` |

一家公司可以对应多只或多地证券，研究对象必须明确到 `securityId`。

### 2.2 MarketData 聚合

`FinancialSnapshot` 与 `MarketSnapshot` 是不可变版本聚合。

| 聚合 | ID | 持有状态 | 核心不变量 | 事件 |
| --- | --- | --- | --- | --- |
| `FinancialSnapshot` | `financialSnapshotId` | 报告期、披露时间、获取时间、来源版本、标准化科目 | 修订新建版本；分析时点只能选择当时已公开版本 | `FinancialSnapshotImported`、`FinancialSnapshotRevised` |
| `MarketSnapshot` | `marketSnapshotId` | 交易日、价格、估值、复权和来源版本 | 同一证券/交易日/口径幂等；缺失值不能由模型补齐 | `MarketSnapshotImported` |
| `PeerGroup` | `peerGroupId` | 行业规则、成员 securityId、有效期 | 成员选择可解释；报告绑定具体版本 | `PeerGroupCreated` |

`MetricResult`、`ValuationAssessment` 和 `PeerComparison` 是由版本化输入计算的研究产物，必须保存算法版本和输入 ID。

## 3. Disclosure 与 Evidence

### 3.1 SourceDocument 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `documentId` |
| 持有状态 | 文档类型、发布方、公告时间、获取时间、原始哈希、OSS 对象引用、版本列表 |
| 实体 | `DocumentVersion` |
| 不变量 | 原始文件不可覆盖；相同哈希幂等；发布时间晚于分析截止时间的版本不可进入证据包 |
| 事件 | `SourceDocumentDiscovered`、`DocumentVersionStored`、`DocumentParseRequested` |
| 外部引用 | `companyId`、`securityId` |

### 3.2 EvidenceBundle 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `evidenceBundleId` |
| 持有状态 | `EvidenceChunk` 引用、`Citation`、查询、过滤条件、检索与重排版本 |
| 不变量 | 每个 Citation 可回到 documentId/version/page/table；精确核心数字不能只来自向量片段 |
| 事件 | `EvidenceBundleBuilt`、`CitationValidated`、`EvidenceInsufficient` |
| 外部引用 | documentId、researchRunId、metricResultId |

`EvidenceChunk` 是检索单元，不是权威事实。`Citation` 把研究结论连接到原文位置和文档版本。原设计中的 `EvidencePackage` 统一命名为 `EvidenceBundle`。

## 4. Research 与 InvestmentView

### 4.1 ResearchTask 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `researchTaskId` |
| 持有状态 | 目标 securityId、任务类型、AsOfTime、用户意图、当前生命周期 |
| 不变量 | 分析截止时间创建后不可静默改变；重试不覆盖原执行 |
| 事件 | `ResearchTaskCreated`、`ResearchRequested`、`ResearchTaskClosed` |
| 外部引用 | securityId、conversationSessionId |

### 4.2 ResearchBaseline 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `researchBaselineId` |
| 持有状态 | 基线版本、研究发现引用、证据包、指标、报告、有效时间和新鲜度 |
| 实体/值对象 | `ResearchFinding`、`RiskFinding`、`ResearchChangeSet` |
| 不变量 | 正式基线只由通过门禁的执行产生；旧版本不可覆盖 |
| 事件 | `ResearchBaselinePublished`、`ResearchBaselineMarkedStale` |
| 外部引用 | researchRunId、reportId、evidenceBundleId、metricResultId |

`DailyResearchSnapshot` 和 `DailyChangeBrief` 记录相对基线的增量，不修改基线本身。

### 4.3 CurrentInvestmentView 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `investmentViewId` |
| 持有状态 | 基线、日周月产物、即时数据引用、维度判断、证据强度、新鲜度和观察条件 |
| 不变量 | `VERIFIED` 必须满足数据与证据门槛；过期基线只能生成 `STALE` 视图；不包含个性化持仓或确定性买卖指令 |
| 事件 | `InvestmentViewCreated`、`InvestmentViewChanged`、`InvestmentViewMarkedStale` |
| 外部引用 | researchBaselineId、reportId 列表、marketSnapshotId、evidenceBundleId |

`ResearchTimeline` 是按 ID 连接基线、快照、报告、事件和观点版本的只读时间线投影，不是新的事实聚合。

## 5. Report

### 5.1 ResearchReport 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `reportId`，版本由 `reportVersion` 标识 |
| 持有状态 | 报告类型、章节、口径、状态、质量结果、网页和 PDF 资产引用 |
| 实体 | `ReportSection` |
| 不变量 | 已发布版本不可覆盖；章节结论必须绑定数据、计算或 Citation；门禁失败不得发布 |
| 事件 | `ReportDrafted`、`ReportSectionGenerated`、`ReportVerified`、`ReportPublished`、`ReportSuperseded` |
| 外部引用 | researchRunId、researchBaselineId、evidenceBundleId、modelUsageRecordId |

报告类型包括首次完整研究、日报、周报、月报和修订版。`WeeklyDigest` 是关注列表级周报投影，不是新的事实聚合。PDF 是报告版本的不可变资产，不是独立业务事实。

## 6. Watchlist 与 Alert

### 6.1 Watchlist 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `watchlistId` |
| 持有状态 | `WatchlistItem`、`AlertRule`、启停状态、报告频率和用户可见设置 |
| 不变量 | 同一 securityId 不能重复激活；加入后必须请求预热，移除不删除历史研究 |
| 事件 | `SecurityAddedToWatchlist`、`SecurityRemovedFromWatchlist`、`AlertRuleChanged`、`ResearchWarmupRequested` |
| 外部引用 | securityId、researchTaskId |

### 6.2 MarketEvent 与 AlertNotification

`MarketEvent` 以 `marketEventId` 标识，保存事件指纹、类别、等级、发生/披露时间和证据引用。相同指纹幂等，高等级事件必须有已验证原始证据。

`AlertNotification` 以 `notificationId` 标识，保存 channel、接收者引用、发送状态和去重键。通知失败不改变 `MarketEvent` 的事实状态。

## 7. Conversation

### 7.1 ConversationSession 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `conversationSessionId` |
| 持有状态 | 当前 securityId、比较对象、reportId/version、章节、AsOfTime、最近任务、已确认偏好引用和摘要 |
| 不变量 | 歧义对象未确认前不能执行状态修改；会话记忆不能成为报告证据 |
| 事件 | `ConversationContextChanged`、`UserConfirmationRequested`、`IntentResolved` |
| 外部引用 | securityId、reportId、researchTaskId、preferenceProfileId |

原始消息作为审计记录单独保存；模型上下文是按当前任务组装的 `ConversationState` 投影，不是新的聚合。

## 8. Execution 与 ResearchRun

### 8.1 ResearchRun 聚合

`ResearchRun` 是一次研究执行的权威聚合；原设计中的 `AnalysisRun` 统一归入该名称。

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `researchRunId` |
| 持有状态 | `AnalysisPlan`、`ExecutionNode`、状态、尝试次数、输入版本、产物引用、质量结果和使用量引用 |
| 实体 | `ExecutionNode`、`HumanIntervention` |
| 值对象 | `QualityGateResult`、`ModelUsageRecord`、`LineageEdge` |
| 不变量 | 节点只在依赖满足后运行；状态按允许迁移变化；发布必须通过硬门禁；重试保留历史尝试 |
| 事件 | `ResearchRunStarted`、`ExecutionNodeChanged`、`ArtifactProduced`、`QualityGateEvaluated`、`ResearchRunCompleted`、`ResearchRunFailed` |
| 外部引用 | researchTaskId、documentId、evidenceBundleId、reportId |

`ExecutionEvent` 是聚合状态变化形成的不可变事件。`ResearchArtifact` 是数据快照、证据包、计算结果或章节等产物引用。控制台通过事件和投影观察执行，但不能直接改写节点状态。

## 9. Feedback 与 Revision

### 9.1 ReportFeedback 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `feedbackId` |
| 持有状态 | `FeedbackTarget`、认同度、用户原文、`FeedbackInterpretation`、状态和验证结果 |
| 不变量 | 必须绑定 reportId/version；客观事实纠错必须重新验证；偏好未确认不能长期保存 |
| 事件 | `ReportFeedbackReceived`、`FeedbackInterpreted`、`FeedbackVerificationRequested`、`RevisionProposed` |
| 外部引用 | reportId/version、conversationSessionId |

### 9.2 ReportRevision 聚合

| 属性 | 定义 |
| --- | --- |
| 聚合 ID | `revisionId` |
| 持有状态 | `RevisionProposal`、批准记录、新旧 reportId/version、差异和原因 |
| 不变量 | 修订生成新报告版本；原报告不可覆盖；未经用户批准的主观调整不能应用 |
| 事件 | `RevisionApproved`、`ReportRevisionApplied`、`FeedbackConvertedToEvalCase` |
| 外部引用 | feedbackId、sourceReportId、resultReportId、researchRunId |

`ResearchPreferenceProfile` 只保存用户明确确认、可见、可修改和可删除的研究偏好。

## 10. 聚合边界和跨域协作

典型协作使用 ID 和事件：

```text
ResearchTaskCreated
  → ResearchRunStarted
  → MarketData / Disclosure / Evidence 准备产物
  → ResearchBaselinePublished
  → ReportPublished
  → InvestmentViewCreated
  → AlertNotification 或飞书完成通知
```

- Company 提供身份，不持有财务、报告或会话。
- MarketData 和 Disclosure 保存原始版本，不依赖研究结论。
- Evidence 引用文档版本和研究执行，不拥有报告。
- Research 组合指标与证据引用，不改写原始事实。
- Report 发布版本化表达，不成为市场数据来源。
- Execution 协调状态，不承载财经计算规则。
- Conversation 和 Watchlist 通过应用服务发起动作，不直接操作其他聚合。

## 11. 状态机与不变量

### 11.1 ResearchRun

```text
CREATED → VALIDATING → COLLECTING_DATA → COMPUTING_METRICS
→ RETRIEVING_EVIDENCE → SYNTHESIZING → VERIFYING → PUBLISHED
```

允许分支：

```text
COLLECTING_DATA → PARTIALLY_AVAILABLE → COMPUTING_METRICS
VERIFYING → NEEDS_REGENERATION → SYNTHESIZING
任一可重试阶段 → FAILED → RETRYING → VALIDATING
```

主体识别错误、核心财报缺失或关键数字无法验证时必须失败。非关键数据缺失可以降级，但必须记录缺口。

### 11.2 InvestmentView

```text
UNINITIALIZED → BOOTSTRAPPING → PROVISIONAL → VERIFIED
BOOTSTRAPPING → INSUFFICIENT_EVIDENCE
BOOTSTRAPPING → FAILED
VERIFIED → STALE → REFRESHING → VERIFIED
```

### 11.3 Feedback

```text
RECEIVED → INTERPRETED → VERIFYING → REVISION_PROPOSED
REVISION_PROPOSED → USER_APPROVED → APPLIED
```

终止状态为 `REJECTED`、`INSUFFICIENT_EVIDENCE` 或 `CONVERTED_TO_EVAL_CASE`。

所有状态迁移必须记录时间、操作者、原因、输入版本和对应事件。跨聚合最终一致时，Outbox 事件与聚合修改在同一数据库事务中提交。

## 12. 统一命名

- `ResearchRun` 是执行聚合的正式名称，替代原设计中的 `AnalysisRun`；
- `EvidenceBundle` 是证据集合的正式名称，替代 `EvidencePackage`；
- `ConversationSession` 是会话聚合，`ConversationState` 仅表示一次模型调用的上下文投影；
- `asOfTime` 是统一分析截止时间，可表达日期与时间，替代只表达日期的 `asOfDate`；
- 兼容层可以接受旧名称，但领域事件、数据库和新 API 只使用正式名称。
