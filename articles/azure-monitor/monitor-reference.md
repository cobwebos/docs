---
title: Azure 监视器监视的内容
description: Azure 监视器监视的所有服务和其他资源的引用。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 0ef7e5d869ab2d7e085cbf861bfc32e57b1fad4b
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408494"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure Monitor 监视哪些内容？
本文介绍了 Azure 监视器监视的不同应用程序和服务。 

## <a name="insights-and-core-solutions"></a>洞察和核心解决方案
核心见解和解决方案被视为 Azure 监视器的一部分，并遵循 Azure 的支持和服务级别协议。 它们在 Azure 监视器可用的所有 Azure 区域都支持。

### <a name="insights"></a>洞察力

Insights 在 Azure Monitor 中为特定的应用程序和服务提供了自定义的监视体验。 它们收集和分析日志和指标。

| 见解 | 说明 |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 可扩展应用程序性能管理 （APM） 服务，用于在任何平台上监视实时 Web 应用程序。 |
| [容器的 Azure 监视器](insights/container-insights-overview.md) | 监视部署到 Azure 容器实例或托管在 Azure 库伯奈斯服务 （AKS） 上托管的托管库伯奈斯群集的容器工作负载的性能。 |
| [用于宇宙数据库的 Azure 监视器（预览）](insights/cosmosdb-insights-overview.md) | 在统一的交互式体验中提供所有 Azure Cosmos DB 资源的总体性能、故障、容量和操作运行状况视图。 |
| [网络 Azure 监视器（预览版）](insights/network-insights-overview.md) | 为所有网络资源提供运行状况和指标的全面视图。 高级搜索功能可帮助您识别资源依赖项，通过搜索网站名称，启用标识托管网站的资源等方案。 |
[用于资源组的 Azure Monitor（预览版）](insights/resource-group-insights.md) |  对单个资源遇到的任何问题进行会审和诊断，同时提供资源组整体运行状况和性能的上下文。 |
| [用于存储的 Azure 监视器（预览）](insights/storage-insights-overview.md) | 通过提供 Azure 存储服务性能、容量和可用性的统一视图，全面监视 Azure 存储帐户。 |
| [虚拟机的 Azure 监视器（预览版）](insights/container-insights-overview.md) | 大规模监视 Azure 虚拟机 （VM） 和虚拟机缩放集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。 |

### <a name="core-solutions"></a>核心解决方案

解决方案基于为特定应用程序或服务自定义的日志查询和视图。 他们只收集和分析日志，并且随着时间的推移被弃用，以获得见解。

| 解决方案 | 说明 |
|:---|:---|
| [代理运行状况](insights/solution-agenthealth.md) | 分析日志分析代理的运行状况和配置。 |
| [警报管理](platform/alert-management-solution.md) | 分析从系统中心运营经理、纳吉奥斯或扎比克斯收集的警报。 |
| [服务地图](insights/service-map.md) | 自动发现 Windows 和 Linux 系统上的应用程序组件，并映射服务之间的通信。 |



## <a name="azure-services"></a>Azure 服务
下表列出了 Azure 服务及其收集到 Azure 监视器中的数据。 

- 指标 - 服务会自动将指标收集到 Azure 监视器指标中。 
- 日志 - 该服务支持诊断设置，这些设置可以将平台日志和指标收集到 Azure 监视器日志。
- Insight - 该服务有可供洞察，为服务提供自定义的监视体验。

| 服务 | 指标 | 日志 | 见解 | 说明 |
|:---|:---|:---|:---|:---|
|Active Directory | 否 | 是 | [是](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | 否 | 否 | 否 |  |
|Active Directory 域服务 | 否 | 是 | 否 |  |
|活动日志 | 否 | 是 | 否 | |
|高级威胁防护 | 否 | 否 | 否 |  |
|顾问 | 否 | 否 | 否 |  |
|AI 生成器 | 否 | 否 | 否 |  |
|Analysis Services | 是 | 是 | 否 |  |
|API for FHIR | 否 | 否 | 否 |  |
|API 管理 | 是 | 是 | 否 |  |
|应用服务 | 是 | 是 | 否 |  |
|AppConfig | 否 | 否 | 否 |  |
|应用程序网关 | 是 | 是 | 否 |  |
|证明服务 | 否 | 否 | 否 |  |
|自动化 | 是 | 是 | 否 |  |
|Azure 服务管理器 （RDFE） | 否 | 否 | 否 |  |
|备份 | 否 | 是 | 否 |  |
|Bastion | 否 | 否 | 否 |  |
|Batch | 是 | 是 | 否 |  |
|Batch AI | 否 | 否 | 否 |  |
|区块链服务 | 否 | 是 | 否 |  |
|蓝图 | 否 | 否 | 否 |  |
|Bot 服务 | 否 | 否 | 否 |  |
|云服务 | 是 | 是 | 否 | 监控来宾操作系统和工作流所需的代理。  |
|Cloud Shell | 否 | 否 | 否 |  |
|认知服务 | 是 | 是 | 否 |  |
|容器实例 | 是 | 否 | 否 |  |
|容器注册表 | 是 | 是 | 否 |  |
|内容分发网络 (CDN) | 否 | 是 | 否 |  |
|Cosmos DB | 是 | 是 | [是](insights/cosmosdb-insights-overview.md) |  |
|成本管理 | 否 | 否 | 否 |  |
|Data Box | 否 | 否 | 否 |  |
|数据目录第 2 代 | 否 | 否 | 否 |  |
|数据资源管理器 | 是 | 是 | 否 |  |
|数据工厂 | 是 | 是 | 否 |  |
|数据工厂 v2 | 否 | 是 | 否 |  |
|Data Share | 否 | 否 | 否 |  |
|Database for MariaDB | 是 | 是 | 否 |  |
|Database for MySQL | 是 | 是 | 否 |  |
|Database for PostgreSQL | 是 | 是 | 否 |  |
|数据库迁移服务 | 否 | 否 | 否 |  |
|Databricks | 否 | 是 | 否 |  |
|DDoS 保护 | 是 | 是 | 否 |  |
|DevOps | 否 | 否 | 否 |  |
|DNS | 是 | 否 | 否 |  |
|域名 | 否 | 否 | 否 |  |
|DPS | 否 | 否 | 否 |  |
|动态 365 客户互动 | 否 | 否 | 否 |  |
|动态 365 财务和运营 | 否 | 否 | 否 |  |
|事件网格 | 是 | 否 | 否 |  |
|事件中心 | 是 | 是 | 否 |  |
|ExpressRoute | 是 | 是 | 否 |  |
|防火墙 | 是 | 是 | 否 |  |
|Front Door | 是 | 是 | 否 |  |
|函数 | 是 | 是 | 否 |  |
|HDInsight | 否 | 是 | 否 |  |
|HPC 缓存 | 否 | 否 | 否 |  |
|信息保护 | 否 | 是 | 否 |  |
|Intune | 否 | 是 | 否 |  |
|IoT Central | 否 | 否 | 否 |  |
|IoT 中心 | 是 | 是 | 否 |  |
|Key Vault | 是 | 是 | 否 |  |
|Kubernetes 服务 (AKS) | 否 | 否 | [是](insights/container-insights-overview.md)  |  |
|负载均衡器 | 是 | 是 | 否 |  |
|逻辑应用 | 是 | 是 | 否 |  |
|机器学习服务 | 否 | 否 | 否 |  |
|托管应用程序  | 否 | 否 | 否 |  |
|地图  | 否 | 否 | 否 |  |
|媒体服务 | 是 | 是 | 否 |  |
|Microsoft Flow | 否 | 否 | 否 |  |
|Microsoft 托管桌面 | 否 | 否 | 否 |  |
|Microsoft PowerApps | 否 | 否 | 否 |  |
|Microsoft Social Engagement | 否 | 否 | 否 |  |
|Microsoft Stream | 是 | 是 | 否 |  |
|迁移 | 否 | 否 | 否 |  |
|多重身份验证 | 否 | 是 | 否 |  |
|网络观察程序 | 是 | 是 | 否 |  |
|通知中心 | 是 | 否 | 否 |  |
|开放数据集 | 否 | 否 | 否 |  |
|策略 | 否 | 否 | 否 |  |
|Power BI | 是 | 是 | 否 |  |
|Power BI Embedded | 否 | 否 | 否 |  |
|专用链接 | 否 | 否 | 否 |  |
|项目后台通信平台 | 否 | 否 | 否 |  |
|Red Hat OpenShift | 否 | 否 | 否 |  |
|Redis 缓存 | 是 | 是 | 否 |  |
|Resource Graph | 否 | 否 | 否 |  |
|资源管理器 | 否 | 否 | 否 |  |
|零售搜索 – 由必应 | 否 | 否 | 否 |  |
|搜索 | 是 | 是 | 否 |  |
|服务总线 | 是 | 是 | 否 |  |
|Service Fabric | 否 | 是 | 否 | 监控来宾操作系统和工作流所需的代理。  |
|注册门户 | 否 | 否 | 否 |  |
|Site Recovery | 否 | 是 | 否 |  |
|春云服务 | 否 | 否 | 否 |  |
|SQL 数据仓库 | 是 | 是 | 否 |  |
|SQL 数据库 | 是 | 是 | 否 |  |
|SQL Server Stretch Database | 是 | 是 | 否 |  |
|堆栈 | 否 | 否 | 否 |  |
|存储 | 是 | 否 | [是](insights/storage-insights-overview.md) |  |
|存储缓存 | 否 | 否 | 否 |  |
|存储同步服务 | 否 | 否 | 否 |  |
|流分析 | 是 | 是 | 否 |  |
|时序见解 | 是 | 是 | 否 |  |
|TINA | 否 | 否 | 否 |  |
|流量管理器 | 是 | 是 | 否 |  |
|通用打印 | 否 | 否 | 否 |  |
|虚拟机规模集 | 否 | 是 | [是](insights/vminsights-overview.md) | 监控来宾操作系统和工作流所需的代理。 |
|虚拟机 | 是 | 是 | [是](insights/vminsights-overview.md) | 监控来宾操作系统和工作流所需的代理。 |
|虚拟网络 | 是 | 是 | [是](insights/network-insights-overview.md) |  |
|虚拟网络 - NSG 流日志 | 否 | 是 | 否 |  |
|VPN 网关 | 是 | 是 | 否 |  |
|Windows 虚拟桌面 | 否 | 否 | 否 |  |


## <a name="product-integrations"></a>产品集成
下表中的服务和解决方案将其数据存储在日志分析工作区中，以便可以使用 Azure 监视器收集的其他日志数据对其进行分析。

| 产品/服务 | 说明 |
|:---|:---|
| [Azure 自动化](/azure/automation/) | 管理操作系统更新并跟踪 Windows 和 Linux 计算机上的更改。 请参阅[更改跟踪](../automation/change-tracking.md)和[更新管理](../automation/automation-update-management.md)。 |
| [Azure 信息保护](https://docs.microsoft.com/azure/information-protection/) | 对文档和电子邮件进行分类并选择性地保护文档和电子邮件。 请参阅[Azure 信息保护的中央报告](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports)。 |
| [Azure 安全中心](/azure/security-center/) | 收集和分析安全事件并执行威胁分析。 请参阅[Azure 安全中心中的数据收集](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | 连接到不同的来源，包括 Office 365 和 Amazon Web 服务云跟踪。 请参阅[连接数据源](/azure/sentinel/connect-data-sources)。 |
| [密钥保管库分析](insights/azure-key-vault.md) | 分析 Azure 密钥保管库审核事件日志。 |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | 创建诊断设置以将日志发送到 Azure 监视器。 请参阅[在 Intune（预览）中将日志数据发送到存储、事件中心或日志分析](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)。  |
| 网络  | [网络性能监视器](insights/network-performance-monitor.md)- 监控与服务和应用程序终结点的网络连接和性能。<br>[Azure 应用程序网关](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor)- 分析 Azure 应用程序网关的日志和指标。<br>[流量分析](/azure/network-watcher/traffic-analytics)- 分析网络观察程序网络安全组 （NSG） 流日志，以便深入了解 Azure 云中的流量流。 |
| [办公室 365](insights/solution-office-365.md) | 监视 Office 365 环境。 更新的版本，可通过 Azure Sentinel 提供改进的载入服务。 |
| [SQL 分析](insights/azure-sql.md) | 大规模监视 Azure SQL 数据库、弹性池和托管实例的性能，并跨多个订阅。 |
| [Surface Hub](insights/surface-hubs.md) | 跟踪曲面集线器设备的运行状况和使用。 |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | 通过将管理组连接到 Azure 监视器，从操作管理器代理收集数据。 请参阅[将操作管理器连接到 Azure 监视器](platform/om-agents.md)<br> 使用[运营经理评估](insights/scom-assessment.md)解决方案评估系统中心运营经理管理团队的风险和运行状况。 |
| [微软团队会议室](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | 微软团队会议室设备的集成端到端管理。 |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | 生成、测试和分发应用程序，然后监视其状态和使用情况。 请参阅[开始分析你的移动应用程序与应用程序中心和应用程序见解](learn/mobile-center-quickstart.md)。 |
| Windows | [Windows 更新合规性](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)- 评估 Windows 桌面升级。<br>[桌面分析](https://docs.microsoft.com/configmgr/desktop-analytics/overview)- 与配置管理器集成，提供见解和智能，以便对 Windows 客户端的更新就绪情况做出更明智的决策。 |



## <a name="other-solutions"></a>其他解决方案
其他解决方案可用于监视不同的应用程序和服务，但主动开发已经停止，并且可能无法在所有区域可用。 它们由 Azure 日志分析数据引入服务级别协议涵盖。

| 解决方案 | 说明 |
|:---|:---|
| [活动目录运行状况检查](insights/ad-assessment.md) | 评估活动目录环境的风险和运行状况。 |
| [活动目录复制状态](insights/ad-replication-status.md) | 定期监视活动目录环境，以监视任何复制失败。 |
| [活动日志分析](platform/activity-log-view.md#azure-portal) | 查看活动日志条目。 |
| [DNS 分析（预览）](insights/dns-analytics.md) | 从 DNS 服务器收集、分析和关联 Windows DNS 分析和审核日志以及其他相关数据。 |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | 跨多个部署收集、查看和分析云铸造系统运行状况和性能指标。 |
| [容器](insights/containers.md) | 查看和管理 Docker 和 Windows 容器主机。 |
| [按需评估](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | 评估和优化本地、混合和云 Microsoft 技术环境的可用性、安全性和性能。 |
| [SQL 运行状况检查](insights/sql-assessment.md) | 评估 SQL Server 环境的风险和运行状况。  |
| [传输数据](insights/wire-data.md) | 使用日志分析代理从 Windows 连接和 Linux 连接的计算机收集的整合网络和性能数据。 |


## <a name="third-party-integration"></a>第三方集成

| 解决方案 | 说明 |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | 使用 IT 服务管理连接器 (ITSMC) 可以连接 Azure 和支持的 IT 服务管理 (ITSM) 产品/服务。  |


## <a name="resources-outside-of-azure"></a>Azure 以外的资源
Azure 监视器可以使用下表中列出的方法从 Azure 外部的资源收集数据。

| 资源 | 方法 |
|:---|:---|
| 应用程序 | 使用应用程序见解监视 Azure 外部的 Web 应用程序。 请参阅[什么是 Application Insights？](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 |
| 虚拟机 | 使用日志分析代理从其他云环境或本地虚拟机的来宾操作系统收集数据。 请参阅[使用日志分析代理收集日志数据](platform/log-analytics-agent.md)。 |
| REST API 客户端 | 单独的 API 可用于从任何 REST API 客户端将数据写入 Azure 监视器日志和指标。 请参阅[使用用于日志的 HTTP 数据收集器 API 向 Azure 监视器发送日志数据](platform/data-collector-api.md)，以及使用 REST API 将[Azure 资源的自定义指标发送到 Azure 监视器指标存储](platform/metrics-store-custom-rest-api.md)。 |



## <a name="next-steps"></a>后续步骤

- 阅读有关[Azure 监视器数据平台的更多内容，该平台存储了见解和解决方案收集的日志和指标](platform/data-platform.md)。
- 完成[有关监视 Azure 资源的教程](learn/tutorial-resource-logs.md)。
- 完成[有关编写日志查询以分析 Azure 监视器日志中的数据的教程](learn/tutorial-resource-logs.md)。
- 完成[有关创建指标图表以分析 Azure 监视器指标中的数据的教程](learn/tutorial-metrics-explorer.md)。

 
