# 外部服务参考

- Last verified: 2026-09-23
- Scope: 记录外部服务用途、账号前置、凭据类型、官方资料、价格入口和持续风险；不保存任何凭据值。
- Owner: Project

时间敏感信息以本文件的核验日期为准。购买和升级前必须重新打开官方页面核价、确认地域和配额。

## 1. Tushare

- 用途：A 股公司、证券、行业、财务、行情、估值、公告索引和新闻。
- 责任边界：第一版结构化市场数据提供方；正式财报和公告原文仍以官方披露文件为证据。
- 账号前置：用户注册个人账号，购买所需积分和独立权限。
- 凭据类型：Tushare API Token，仅在 Secret 中配置。
- 官方文档：[积分与独立权限](https://tushare.pro/document/1?doc_id=290)。
- 价格入口：同一权限页面；2026-09-23 页面显示 5000 积分约 500 元/年、新闻资讯约 1000 元/年、公告信息约 1000 元/年。
- 风险：积分、独立权限、频率和字段可能调整；购买前验证实际接口覆盖，个人权限不能假设适用于机构用途。

## 2. Feishu Open Platform

- 用途：机器人消息、事件订阅、交互卡片、进度通知和用户命令。
- 责任边界：渠道接入，不保存核心研究状态。
- 账号前置：用户拥有可创建企业自建应用的飞书组织权限，并完成应用发布与授权。
- 凭据类型：App ID、App Secret、Verification Token、Encrypt Key。
- 官方文档：[飞书开放平台文档](https://open.feishu.cn/document/home/index)。
- 价格入口：飞书工作台和开放平台当期版本；第一版不假设任何企业版付费权益。
- 风险：应用权限需管理员审批；事件订阅、回调公网可达性、频率和卡片版本受平台规则影响。

## 3. Alibaba Cloud Model Studio

- 用途：Qwen Chat、结构化输出、报告生成和 Embedding 模型调用。
- 责任边界：模型服务；业务规则、工具执行和正式状态由 Spring Boot 控制。
- 账号前置：用户开通阿里云百炼/Model Studio，完成实名、充值和模型授权。
- 凭据类型：Model Studio API Key。
- 官方文档：[产品说明](https://help.aliyun.com/en/model-studio/what-is-model-studio)、[模型价格](https://help.aliyun.com/en/model-studio/model-pricing)。
- 价格入口：模型价格页；按模型、地域、输入/输出 Token 和上下文区间计费。
- 风险：模型 ID、版本、价格、免费额度、上下文和区域可用性会变化；路由配置必须版本化并设置预算。

## 4. Alibaba Cloud ECS 与云盘

- 用途：运行 Nginx、前端、Spring Boot、Worker、n8n、PostgreSQL 和备份任务。
- 账号前置：用户完成阿里云实名认证、充值，确认地域、网络和购买周期。
- 凭据类型：RAM Role 或最小权限 AccessKey（部署时使用），服务器 SSH 公钥。
- 官方文档：[ECS 产品文档](https://help.aliyun.com/zh/ecs/)、[ECS 价格](https://ecs-buy.aliyun.com/price)。
- 价格入口：目标地域实时询价；2026-09-23 公开价格页可见 2 vCPU/8 GB 示例约 255～300 元/月，4 vCPU/8 GB 示例约 452～532 元/月，实际实例代际和优惠不同。
- 风险：实例库存、代际、系统盘、ESSD、快照、带宽、续费价和促销会改变总价；不要只看首购折扣。

## 5. Alibaba Cloud OSS

- 用途：永久保存原始 PDF、正式报告、数据库备份和 n8n 工作流导出。
- 账号前置：用户开通 OSS，创建私有 Bucket，确认地域和版本控制。
- 凭据类型：ECS RAM Role 或最小权限 AccessKey。
- 官方文档：[OSS 产品文档](https://help.aliyun.com/zh/oss/)、[OSS 价格](https://cn.aliyun.com/price/detail/oss)。
- 价格入口：存储、请求、外网流量和资源包分别计费；2026-09-23 中国内地标准 LRS 按量存储公开价为 0.12 元/GB/月。
- 风险：外网流量和请求可能高于存储费；生命周期规则不得误删永久 PDF 或备份。

## 6. 域名、HTTPS 与 ICP

- 用途：飞书 Webhook、研究控制台和永久 PDF 的稳定 HTTPS 地址。
- 账号前置：用户购买并实名认证域名；中国内地服务器需按主体和省份要求完成 ICP 备案。
- 凭据类型：DNS API 凭据、证书私钥或托管证书授权。
- 官方文档：[阿里云域名](https://help.aliyun.com/zh/dws/)、[个人网站 ICP 备案](https://help.aliyun.com/zh/icp-filing/basic-icp-service/getting-started/quick-start-for-icp-filing-for-personal-websites)。
- 价格入口：域名注册与续费页、证书服务当期价格页。
- 风险：备案是外部等待项；域名和证书需要续期；证书私钥不得进入仓库。

## 7. PostgreSQL 与 pgvector

- 用途：事务业务数据、版本、事件、全文检索和向量检索。
- 责任边界：第一版自建于 ECS；原始 PDF 存 OSS。
- 账号前置：无外部 SaaS 账号，使用受控容器镜像和数据库管理员流程。
- 凭据类型：数据库用户名、密码和应用连接串，均只在 Secret 中配置。
- 官方文档：[PostgreSQL 文档](https://www.postgresql.org/docs/current/)、[pgvector 官方项目](https://github.com/pgvector/pgvector)。
- 价格入口：软件本身开源；成本包含 ECS、云盘、快照、备份和运维。
- 风险：版本兼容、扩展升级、索引重建、容量和恢复必须测试；数据库端口不公开。

## 8. n8n

- 用途：飞书 Webhook、定时触发、调用 Spring Boot、通知、简单重试和人工补偿入口。
- 责任边界：不保存核心业务状态，不承担复杂 Agent 推理。
- 账号前置：第一版自托管，无 n8n Cloud 账号要求；需要设置本地 owner 和加密配置。
- 凭据类型：n8n 加密密钥、owner 登录信息和工作流内 Secret 引用。
- 官方文档：[n8n Docker 安装](https://docs.n8n.io/hosting/installation/docker/)、[自托管文档](https://docs.n8n.io/hosting/)。
- 价格入口：自托管软件成本计入 ECS；若改用 n8n Cloud，按官方当期套餐核价。
- 风险：工作流和凭据数据库需要备份；升级前导出工作流并测试兼容；n8n 管理界面不公开。

## 9. Spring AI

- 用途：模型抽象、ChatClient、结构化输出、Tool Calling、Embedding、向量存储、RAG、记忆、评测和 MCP 集成。
- 责任边界：位于 Spring Boot 内部的 AI 集成层，不承载财经领域规则。
- 账号前置：无单独 SaaS 账号；依赖 Maven Central 和具体模型供应商账号。
- 凭据类型：仅使用下游模型供应商 API Key，Spring AI 本身无独立密钥。
- 官方文档：[Spring AI Reference](https://docs.spring.io/spring-ai/reference/)、[Spring AI API](https://docs.spring.io/spring-ai/reference/api/)。
- 价格入口：开源库无调用费；模型、向量存储和云资源分别计费。
- 风险：Spring AI 与 Spring Boot 的兼容矩阵会变化；实施 M0 时锁定 BOM 和 Java/Spring Boot 版本并记录升级评测。

## 10. GitHub

- 用途：源代码、文档、版本历史和后续 CI。
- 账号前置：仓库所有者账号和明确的远端操作授权。
- 凭据类型：本机 SSH agent 中已有的 SSH key 或受限 GitHub App 凭据；不读取私钥内容。
- 官方文档：[GitHub SSH 文档](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)。
- 价格入口：当前私有/公开仓库套餐页面。
- 风险：远端推送、分支改写和 Pull Request 必须由用户明确授权；本地 Git 提交身份需在推送前确认。
