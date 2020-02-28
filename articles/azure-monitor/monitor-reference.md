---
title: Azure Monitor 监视的内容
description: Azure Monitor 监视的所有服务和其他资源的参考。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/17/2020
ms.openlocfilehash: 3cd330e9c4ceba2feeb7a74cafe9f094fd03d690
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669108"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure Monitor 监视哪些内容？
本文介绍 Azure Monitor 监视的各种应用程序和服务。 

## <a name="insights-and-core-solutions"></a>见解和核心解决方案
核心见解和解决方案被视为 Azure Monitor 的一部分，并遵循 Azure 的支持和服务级别协议。 所有可用 Azure Monitor 的 Azure 区域都支持这些支持。

### <a name="insights"></a>洞察力

Insights 提供针对特定应用程序和服务的自定义监视体验。 它们收集和分析日志和指标。

| 见解 | 说明 |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 可扩展的应用程序性能管理（APM）服务，用于在任何平台上监视实时 web 应用程序。 |
| [容器 Azure Monitor](insights/container-insights-overview.md) | 监视部署到 azure 容器实例或托管在 Azure Kubernetes 服务（AKS）上的托管 Kubernetes 群集的容器工作负荷的性能。 |
| [Cosmos DB 的 Azure Monitor （预览）](insights/cosmosdb-insights-overview.md) | 提供统一交互式体验中所有 Azure Cosmos DB 资源的总体性能、故障、容量和操作运行状况的视图。 |
| [网络 Azure Monitor （预览）](insights/network-insights-overview.md) | 提供所有网络资源的运行状况和指标的综合视图。 "高级搜索" 功能可帮助你识别资源依赖关系，通过只需搜索你的网站名称来启用标识托管你的网站的资源等方案。 |
[资源组的 Azure Monitor （预览）](insights/resource-group-insights.md) |  会审和诊断您的个别资源遇到的任何问题，同时提供作为整体资源组的运行状况和性能的上下文。 |
| [用于存储的 Azure Monitor （预览）](insights/storage-insights-overview.md) | 提供 Azure 存储服务性能、容量和可用性的统一视图，全面监视 Azure 存储帐户。 |
| [用于 VM 的 Azure Monitor （预览）](insights/container-insights-overview.md) | 大规模监视 Azure 虚拟机（VM）和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。 |

### <a name="core-solutions"></a>核心解决方案

解决方案基于针对特定应用程序或服务自定义的日志查询和视图。 它们只收集和分析日志，并在一段时间内弃用，以支持见解。

| 解决方案 | 说明 |
|:---|:---|
| [代理运行状况](insights/solution-agenthealth.md) | 分析 Log Analytics 代理的运行状况和配置。 |
| [警报管理](platform/alert-management-solution.md) | 分析从 System Center Operations Manager、Nagios 或 Zabbix 收集的警报。 |
| [服务地图](insights/service-map.md) | 自动发现 Windows 和 Linux 系统上的应用程序组件，并映射服务之间的通信。 |



## <a name="azure-services"></a>Azure 服务
下表列出了 Azure 服务及其收集到 Azure Monitor 的数据。 

- 指标-服务会自动将度量值收集到 Azure Monitor 指标中。 
- 日志-服务支持诊断设置，可收集平台日志和指标来 Azure Monitor 日志。
- 见解-提供服务的自定义监视体验的服务可提供见解。

| 服务 | 度量值 | 日志 | 见解 | 说明 |
|:---|:---|:---|:---|:---|
|Active Directory | 否 | 是 | [是](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | 否 | 否 | 否 |  |
|Active Directory 域服务 | 否 | 是 | 否 |  |
|活动日志 | 否 | 是 | 否 | |
|高级威胁防护 | 否 | 否 | 否 |  |
|顾问 | 否 | 否 | 否 |  |
|AI 生成器 | 否 | 否 | 否 |  |
|Analysis Services | 是 | 是 | 否 |  |
|适用于 FHIR 的 API | 否 | 否 | 否 |  |
|API 管理 | 是 | 是 | 否 |  |
|应用服务 | 是 | 是 | 否 |  |
|AppConfig | 否 | 否 | 否 |  |
|应用程序网关 | 是 | 是 | 否 |  |
|证明服务 | 否 | 否 | 否 |  |
|自动化 | 是 | 是 | 否 |  |
|Azure Service Manager （RDFE） | 否 | 否 | 否 |  |
|备份 | 否 | 是 | 否 |  |
|Bastion | 否 | 否 | 否 |  |
|Batch | 是 | 是 | 否 |  |
|Batch AI | 否 | 否 | 否 |  |
|区块链服务 | 否 | 是 | 否 |  |
|蓝图 | 否 | 否 | 否 |  |
|Bot 服务 | 否 | 否 | 否 |  |
|云服务 | 是 | 是 | 否 | 监视来宾操作系统和工作流所需的代理。  |
|Cloud Shell | 否 | 否 | 否 |  |
|认知服务 | 是 | 是 | 否 |  |
|容器实例 | 是 | 否 | 否 |  |
|容器注册表 | 是 | 是 | 否 |  |
|内容分发网络 (CDN) | 否 | 是 | 否 |  |
|Cosmos DB | 是 | 是 | [是](insights/cosmosdb-insights-overview.md) |  |
|成本管理 | 否 | 否 | 否 |  |
|Data Box | 否 | 否 | 否 |  |
|数据目录 Gen2 | 否 | 否 | 否 |  |
|数据资源管理器 | 是 | 是 | 否 |  |
|数据工厂 | 是 | 是 | 否 |  |
|数据工厂 v2 | 否 | 是 | 否 |  |
|数据共享 | 否 | 否 | 否 |  |
|MariaDB 的数据库 | 是 | 是 | 否 |  |
|MySQL 数据库 | 是 | 是 | 否 |  |
|PostgreSQL 的数据库 | 是 | 是 | 否 |  |
|数据库迁移服务 | 否 | 否 | 否 |  |
|Databricks | 否 | 是 | 否 |  |
|DDoS 保护 | 是 | 是 | 否 |  |
|DevOps | 否 | 否 | 否 |  |
|DNS | 是 | 否 | 否 |  |
|域名 | 否 | 否 | 否 |  |
|分发 | 否 | 否 | 否 |  |
|Dynamics 365 客户参与 | 否 | 否 | 否 |  |
|Dynamics 365 财务和运营 | 否 | 否 | 否 |  |
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
|Microsoft 社交合作 | 否 | 否 | 否 |  |
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
|项目假脱机通信平台 | 否 | 否 | 否 |  |
|Red Hat OpenShift | 否 | 否 | 否 |  |
|Redis 缓存 | 是 | 是 | 否 |  |
|Resource Graph | 否 | 否 | 否 |  |
|资源管理器 | 否 | 否 | 否 |  |
|零售搜索-按必应 | 否 | 否 | 否 |  |
|搜索 | 是 | 是 | 否 |  |
|服务总线 | 是 | 是 | 否 |  |
|Service Fabric | 否 | 是 | 否 | 监视来宾操作系统和工作流所需的代理。  |
|注册门户 | 否 | 否 | 否 |  |
|Site Recovery | 否 | 是 | 否 |  |
|春季云服务 | 否 | 否 | 否 |  |
|SQL 数据仓库 | 是 | 是 | 否 |  |
|SQL 数据库 | 是 | 是 | 否 |  |
|SQL Server Stretch Database | 是 | 是 | 否 |  |
|Stack | 否 | 否 | 否 |  |
|存储 | 是 | 否 | [是](insights/storage-insights-overview.md) |  |
|存储缓存 | 否 | 否 | 否 |  |
|存储同步服务 | 否 | 否 | 否 |  |
|流分析 | 是 | 是 | 否 |  |
|时序见解 | 是 | 是 | 否 |  |
|TINA | 否 | 否 | 否 |  |
|流量管理器 | 是 | 是 | 否 |  |
|通用打印 | 否 | 否 | 否 |  |
|虚拟机规模集 | 否 | 是 | [是](insights/vminsights-overview.md) | 监视来宾操作系统和工作流所需的代理。 |
|虚拟机 | 是 | 是 | [是](insights/vminsights-overview.md) | 监视来宾操作系统和工作流所需的代理。 |
|虚拟网络 | 是 | 是 | [是](insights/network-insights-overview.md) |  |
|虚拟网络-NSG 流日志 | 否 | 是 | 否 |  |
|VPN 网关 | 是 | 是 | 否 |  |
|Windows 虚拟桌面 | 否 | 否 | 否 |  |


## <a name="product-integrations"></a>产品集成
下表中的服务和解决方案将其数据存储在 Log Analytics 工作区中，以便可以使用 Azure Monitor 收集的其他日志数据对其进行分析。

| 产品/服务 | 说明 |
|:---|:---|
| [Azure 自动化](/azure/automation/) | 管理操作系统更新并跟踪 Windows 和 Linux 计算机上的更改。 请参阅[更改跟踪](../automation/change-tracking.md)和[更新管理](../automation/automation-update-management.md)。 |
| [Azure 信息保护](https://docs.microsoft.com/azure/information-protection/) | 对文档和电子邮件进行分类和（可选）保护。 请参阅[Azure 信息保护的集中报告](https://docs.microsoft.com/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports)。 |
| [Azure 安全中心](/azure/security-center/) | 收集和分析安全事件并执行威胁分析。 请参阅[Azure 安全中心中的数据收集](/azure/security-center/security-center-enable-data-collection) |
| [Azure Sentinel](/azure/sentinel/) | 连接到不同的源，包括 Office 365 和 Amazon Web Services 云跟踪。 请参阅[连接数据源](/azure/sentinel/connect-data-sources)。 |
| [密钥保管库分析](insights/azure-key-vault.md) | 分析 Azure Key Vault AuditEvent 日志。 |
| [Microsoft Intune](https://docs.microsoft.com/intune/) | 创建诊断设置以将日志发送到 Azure Monitor。 请参阅[在 Intune 中将日志数据发送到存储、事件中心或 log analytics （预览版）](https://docs.microsoft.com/intune/fundamentals/review-logs-using-azure-monitor)。  |
| 网络  | [网络性能监视器](insights/network-performance-monitor.md)-监视服务和应用程序终结点的网络连接和性能。<br>[Azure 应用程序网关](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor)-分析 Azure 应用程序网关上的日志和指标。<br>[流量分析](/azure/network-watcher/traffic-analytics)分析网络观察程序网络安全组（NSG）流日志，以便深入了解 Azure 云中的流量流。 |
| [Office 365](insights/solution-office-365.md) | 监视 Office 365 环境。 更新的版本，通过 Azure Sentinel 提供改进的加入。 |
| [SQL Analytics](insights/azure-sql.md) | 跨多个订阅大规模监视 Azure SQL 数据库、弹性池和托管实例的性能。 |
| [Surface Hub](insights/surface-hubs.md) | 跟踪 Surface Hub 设备的运行状况和使用情况。 |
| [System Center Operations Manager](https://docs.microsoft.com/system-center/scom) | 通过将数据的管理组连接到 Azure Monitor，从 Operations Manager 代理收集数据。 请参阅[连接 Operations Manager 到 Azure Monitor](platform/om-agents.md)<br> [Operations Manager 评估](insights/scom-assessment.md)解决方案，评估 System Center Operations Manager 管理组的风险和运行状况。 |
| [Microsoft 团队聊天室](https://docs.microsoft.com/microsoftteams/room-systems/azure-monitor-deploy) | Microsoft 团队聊天室设备的端到端集成。 |
| [Visual Studio App Center](https://docs.microsoft.com/appcenter/) | 生成、测试和分发应用程序，然后监视其状态和使用情况。 请参阅[开始分析你的移动应用 App Center 和 Application Insights](learn/mobile-center-quickstart.md)。 |
| Windows | [Windows 更新符合性](https://docs.microsoft.com/windows/deployment/update/update-compliance-get-started)-评估 Windows 桌面升级。<br>[桌面分析](https://docs.microsoft.com/configmgr/desktop-analytics/overview)-与 Configuration Manager 集成，提供见解和智能，为你的 Windows 客户端的更新准备情况做出更明智的决策。 |



## <a name="other-solutions"></a>其他解决方案
其他解决方案可用于监视不同的应用程序和服务，但活动开发已停止，并且可能无法在所有区域中使用。 它们由 Azure Log Analytics 数据引入服务级别协议涵盖。

| 解决方案 | 说明 |
|:---|:---|
| [Active Directory 运行状况检查](insights/ad-assessment.md) | 评估 Active Directory 环境的风险和运行状况。 |
| [Active Directory 复制状态](insights/ad-replication-status.md) | 定期监视 Active Directory 环境中的任何复制失败。 |
| [活动 log analytics](platform/activity-log-view.md#activity-logs-analytics-monitoring-solution) | 使用预定义的日志查询和视图分析活动日志项。 |
| [DNS Analytics （预览）](insights/dns-analytics.md) | 收集、分析和关联 Windows DNS 分析和审核日志以及来自 DNS 服务器的其他相关数据。 |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | 跨多个部署收集、查看和分析你的 Cloud Foundry 系统运行状况和性能指标。 |
| [容器](insights/containers.md) | 查看和管理 Docker 和 Windows 容器主机。 |
| [按需评估](https://docs.microsoft.com/services-hub/health/getting_started_with_on_demand_assessments) | 评估和优化本地、混合和云 Microsoft 技术环境的可用性、安全性和性能。 |
| [SQL 运行状况检查](insights/sql-assessment.md) | 评估 SQL Server 环境的风险和运行状况。  |
| [传输数据](insights/wire-data.md) | 与 Log Analytics agent 一起从 Windows 连接的计算机和连接到 Linux 的计算机收集的合并网络和性能数据。 |


## <a name="third-party-integration"></a>第三方集成

| 解决方案 | 说明 |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | 使用 IT 服务管理连接器 (ITSMC) 可以连接 Azure 和支持的 IT 服务管理 (ITSM) 产品/服务。  |


## <a name="resources-outside-of-azure"></a>Azure 之外的资源
Azure Monitor 可以使用下表中列出的方法从 Azure 外部的资源收集数据。

| 资源 | 方法 |
|:---|:---|
| 应用程序 | 使用 Application Insights 监视 Azure 外部的 web 应用程序。 请参阅[什么是 Application Insights？](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)。 |
| 虚拟机 | 使用 Log Analytics 代理从其他云环境中的虚拟机的来宾操作系统或本地收集数据。 请参阅[利用 Log Analytics Agent 收集日志数据](platform/log-analytics-agent.md)。 |
| REST API 客户端 | 可以使用单独的 Api 将数据写入任何 REST API 客户端 Azure Monitor 日志和指标。 请参阅使用用于日志的[HTTP 数据收集器 API 将日志数据发送到 Azure Monitor](platform/data-collector-api.md) ，并使用度量值[REST API 向 Azure Monitor 指标存储发送 Azure 资源的自定义指标](platform/metrics-store-custom-rest-api.md)。 |



## <a name="next-steps"></a>后续步骤

- 阅读有关[存储见解和解决方案收集的日志和指标的 Azure Monitor 数据平台](platform/data-platform.md)的详细信息。
- 完成有关[监视 Azure 资源的教程](learn/tutorial-resource-logs.md)。
- 完成[编写日志查询以分析 Azure Monitor 日志中的数据的教程](learn/tutorial-resource-logs.md)。
- 完成[创建指标图表以分析 Azure Monitor 度量数据中的数据的教程](learn/tutorial-metrics-explorer.md)。

 
