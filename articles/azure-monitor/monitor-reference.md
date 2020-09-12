---
title: Azure Monitor 监视哪些内容
description: 有关 Azure Monitor 监视的所有服务和其他资源的参考信息。
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 08/15/2020
ms.openlocfilehash: 1841c4eb8975c865c5f15a0e8fe3a6b5f0522820
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435399"
---
# <a name="what-is-monitored-by-azure-monitor"></a>Azure Monitor 监视哪些内容？
本文介绍了由 Azure Monitor 监视的不同应用程序和服务。 

## <a name="insights-and-core-solutions"></a>见解和核心解决方案
核心见解和解决方案被视为 Azure Monitor 的一部分，并遵循 Azure 的支持和服务级别协议。 它们在可使用 Azure Monitor 的所有 Azure 区域中均受支持。

### <a name="insights"></a>洞察力

见解为特定的应用程序和服务提供自定义监视体验。 它们收集和分析日志和指标。

| 见解 | 说明 |
|:---|:---|
| [Application Insights](app/app-insights-overview.md) | 可扩展应用程序性能管理 (APM) 服务，用于监视任何平台上的实时 web 应用程序。 |
| [用于容器的 Azure Monitor](insights/container-insights-overview.md) | 监视部署到 Azure 容器实例或 Azure Kubernetes 服务 (AKS) 上托管的托管 Kubernetes 群集的容器工作负荷的性能。 |
| [用于 Cosmos DB 的 Azure Monitor](insights/cosmosdb-insights-overview.md) | 提供了关于所有 Azure Cosmos DB 资源的整体性能、故障、容量和运行状况的统一交互式体验视图。 |
| [用于网络的 Azure Monitor（预览版）](insights/network-insights-overview.md) | 提供所有网络资源的运行状况和指标的综合视图。 高级搜索功能通过便捷搜索网站名称，帮助你识别资源依赖关系，从而能够支持多种方案，比如识别用于托管网站的资源等。 |
[用于资源组的 Azure Monitor（预览版）](insights/resource-group-insights.md) |  分类和诊断各资源出现的任何问题，同时提供有关资源组的运行状况和性能的整体上下文。 |
| [用于存储的 Azure Monitor](insights/storage-insights-overview.md) | 通过提供 Azure 存储服务性能、容量和可用性的统一视图，提供对 Azure 存储帐户的全面监视。 |
| [用于 VM 的 Azure Monitor](insights/vminsights-overview.md) | 可以大规模监视 Azure 虚拟机 (VM) 和虚拟机规模集。 它分析 Windows 和 Linux VM 的性能和运行状况，并监视它们的进程及其对其他资源和外部进程的依赖关系。 |
| [Key Vault 的 Azure Monitor（预览版）](./insights/key-vault-insights-overview.md) | 提供 Key Vault 请求、性能、失败和延迟的统一视图，在其中可以全面监视密钥保管库。 |
| [适用于 Azure Cache for Redis 的 Azure Monitor（预览版）](insights/redis-cache-insights-overview.md) |  提供有关整体性能、故障、容量和运行状况的统一交互式视图。 |


### <a name="core-solutions"></a>核心解决方案

解决方案基于为特定应用程序或服务自定义的日志查询和视图。 它们只收集和分析日志，并随着时间推移而不再被使用，取而代之的是见解。

| 解决方案 | 说明 |
|:---|:---|
| [代理运行状况](insights/solution-agenthealth.md) | 分析 Log Analytics 代理的运行状况和配置。 |
| [警报管理](platform/alert-management-solution.md) | 分析从 System Center Operations Manager、Nagios 或 Zabbix 收集的警报。 |
| [服务地图](insights/service-map.md) | 自动发现 Windows 和 Linux 系统上的应用程序组件，并映射服务之间的通信。 |



## <a name="azure-services"></a>Azure 服务
下表列出了 Azure 服务及其收集到 Azure Monitor 中的数据。 

- 指标 - 服务自动将指标收集到 Azure Monitor 指标中。 
- 日志 - 该服务支持诊断设置，可将平台日志和指标收集到 Azure Monitor 日志。
- 见解 - 有一个可用于服务的见解，它为服务提供自定义的监视体验。

| 服务 | 指标 | 日志 | 见解 | 说明 |
|:---|:---|:---|:---|:---|
|Active Directory | 否 | 是 | [是](../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md) |  |
|Active Directory B2C | 否 | 否 | 否 |  |
|Active Directory 域服务 | 否 | 是 | 否 |  |
|活动日志 | 否 | 是 | 否 | |
|高级威胁防护 | 否 | 否 | 否 |  |
|顾问 | 否 | 否 | 否 |  |
|AI Builder | 否 | 否 | 否 |  |
|Analysis Services | 是 | 是 | 否 |  |
|API for FHIR | 否 | 否 | 否 |  |
|API 管理 | 是 | 是 | 否 |  |
|应用服务 | 是 | 是 | 否 |  |
|AppConfig | 否 | 否 | 否 |  |
|应用程序网关 | 是 | 是 | 否 |  |
|证明服务 | 否 | 否 | 否 |  |
|自动化 | 是 | 是 | 否 |  |
|Azure 服务管理器 (RDFE) | 否 | 否 | 否 |  |
|备份 | 否 | 是 | 否 |  |
|Bastion | 否 | 否 | 否 |  |
|Batch | 是 | 是 | 否 |  |
|Batch AI | 否 | 否 | 否 |  |
|区块链服务 | 否 | 是 | 否 |  |
|蓝图 | 否 | 否 | 否 |  |
|Bot 服务 | 否 | 否 | 否 |  |
|云服务 | 是 | 是 | 否 | 监视来宾操作系统和工作流时所需的代理。  |
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
|Dynamics 365 Customer Engagement | 否 | 否 | 否 |  |
|Dynamics 365 Finance and Operations | 否 | 否 | 否 |  |
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
|Key Vault | 是 | 是 | [是](./insights/key-vault-insights-overview.md) |  |
|Kubernetes 服务 (AKS) | 否 | 否 | [是](insights/container-insights-overview.md)  |  |
|负载均衡器 | 是 | 否 | 否 |  |
|逻辑应用 | 是 | 是 | 否 |  |
|机器学习服务 | 否 | 否 | 否 |  |
|托管应用程序  | 否 | 否 | 否 |  |
|地图  | 否 | 否 | 否 |  |
|媒体服务 | 是 | 是 | 否 |  |
|Microsoft Flow | 否 | 否 | 否 |  |
|Microsoft 托管桌面 | 否 | 否 | 否 |  |
|Microsoft PowerApps | 否 | 否 | 否 |  |
|Microsoft 社交参与 | 否 | 否 | 否 |  |
|Microsoft Stream | 是 | 是 | 否 |  |
|迁移 | 否 | 否 | 否 |  |
|多重身份验证 | 否 | 是 | 否 |  |
|网络观察程序 | 是 | 是 | 否 |  |
|通知中心 | 是 | 否 | 否 |  |
|开放数据集 | 否 | 否 | 否 |  |
|策略 | 否 | 否 | 否 |  |
|Power BI Embedded | 是 | 是 | 否 |  |
|专用链接 | 否 | 否 | 否 |  |
|项目假脱机通信平台 | 否 | 否 | 否 |  |
|Red Hat OpenShift | 否 | 否 | 否 |  |
|Redis 缓存 | 是 | 是 | [是](insights/redis-cache-insights-overview.md) | |
|Resource Graph | 否 | 否 | 否 |  |
|资源管理器 | 否 | 否 | 否 |  |
|零售搜索 - 通过必应 | 否 | 否 | 否 |  |
|搜索 | 是 | 是 | 否 |  |
|服务总线 | 是 | 是 | 否 |  |
|Service Fabric | 否 | 是 | 否 | 监视来宾操作系统和工作流时所需的代理。  |
|注册门户 | 否 | 否 | 否 |  |
|Site Recovery | 否 | 是 | 否 |  |
|Spring Cloud 服务 | 否 | 否 | 否 |  |
|Azure Synapse Analytics | 是 | 是 | 否 |  |
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
|虚拟机规模集 | 否 | 是 | [是](insights/vminsights-overview.md) | 监视来宾操作系统和工作流时所需的代理。 |
|虚拟机 | 是 | 是 | [是](insights/vminsights-overview.md) | 监视来宾操作系统和工作流时所需的代理。 |
|虚拟网络 | 是 | 是 | [是](insights/network-insights-overview.md) |  |
|虚拟网络 - NSG 流日志 | 否 | 是 | 否 |  |
|VPN 网关 | 是 | 是 | 否 |  |
|Windows 虚拟桌面 | 否 | 否 | 否 |  |


## <a name="product-integrations"></a>产品集成
下表中的服务和解决方案将其数据存储在 Log Analytics 工作区中，以便可以将这些数据与使用 Azure Monitor 收集的其他日志数据一起进行分析。

| 产品/服务 | 说明 |
|:---|:---|
| [Azure 自动化](../automation/index.yml) | 管理操作系统更新并跟踪 Windows 和 Linux 计算机上的更改。 请参阅[更改跟踪](../automation/change-tracking.md)和[更新管理](../automation/update-management/update-mgmt-overview.md)。 |
| [Azure 信息保护](/azure/information-protection/) | 对文档和电子邮件进行分类和选择性保护。 请参阅 [Azure 信息保护的中央报告](/azure/information-protection/reports-aip#configure-a-log-analytics-workspace-for-the-reports)。 |
| [Azure 安全中心](../security-center/index.yml) | 收集和分析安全事件并执行威胁分析。 请参阅 [Azure 安全中心中的数据收集](../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](../sentinel/index.yml) | 连接到不同的源，包括 Office 365 和 Amazon Web Services 云轨迹。 请参阅[连接数据源](../sentinel/connect-data-sources.md)。 |
| [Microsoft Intune](/intune/) | 创建诊断设置以将日志发送到 Azure Monitor。 请参阅[将日志数据发送到 Intune 中的存储、事件中心或日志分析（预览版）](/intune/fundamentals/review-logs-using-azure-monitor)。  |
| 网络  | [网络性能监视器](insights/network-performance-monitor.md) - 监视与服务和应用程序终结点的网络连接和其性能。<br>[Azure 应用程序网关](insights/azure-networking-analytics.md#azure-application-gateway-analytics-solution-in-azure-monitor) - 分析来自 Azure 应用程序网关的日志和指标。<br>[流量分析](../network-watcher/traffic-analytics.md) - 可以分析网络观察程序网络安全组 (NSG) 流日志，帮助洞察 Azure 云中的流量流。 |
| [Office 365](insights/solution-office-365.md) | 监视 Office 365 环境。 使用通过 Azure Sentinel 提供改进的载入来更新版本。 |
| [SQL Analytics](insights/azure-sql.md) | 大规模或跨多个订阅监视 Azure SQL 数据库和 SQL 托管实例的性能。 |
| [Surface Hub](insights/surface-hubs.md) | 跟踪 Surface Hub 设备的运行状况和使用情况。 |
| [System Center Operations Manager](/system-center/scom) | 通过将 Operations Manager 代理的管理组连接到 Azure Monitor，从代理收集数据。 请参阅[将 Operations Manager 连接到 Azure Monitor](platform/om-agents.md)<br> 使用 [Operations Manager 评估](insights/scom-assessment.md)解决方案评估 System Center Operations Manager 管理组的风险和运行状况。 |
| [Microsoft 团队聊天室](/microsoftteams/room-systems/azure-monitor-deploy) | Microsoft 团队聊天室设备的集成、端到端管理。 |
| [Visual Studio App Center](/appcenter/) | 构建、测试和分发应用程序，然后监视它们的状态和使用情况。 请参阅[开始使用 App Center 和 Application Insights 分析移动应用](learn/mobile-center-quickstart.md)。 |
| Windows | [Windows 更新符合性](/windows/deployment/update/update-compliance-get-started) - 评估 Windows 桌面升级。<br>[桌面分析](/configmgr/desktop-analytics/overview) - 与配置管理器集成，提供见解和情报，帮助你制定关于 Windows 客户端更新就绪性的更明智决策。 |



## <a name="other-solutions"></a>其他解决方案
有可用于监视其他应用程序和服务的其他解决方案，但当前没有相关开发处于活动状态，并且不一定能所有区域中使用。 Azure Log Analytics 数据引入服务级别协议中涵盖了这些解决方案。

| 解决方案 | 说明 |
|:---|:---|
| [Active Directory 运行状况检查](insights/ad-assessment.md) | 评估 Active Directory 环境的风险和运行状况。 |
| [Active Directory 复制状态](insights/ad-replication-status.md) | 定期监视 Active Directory 环境中是否有任何复制失败。 |
| [活动日志分析](platform/activity-log.md#activity-log-analytics-monitoring-solution) | 查看活动日志条目。 |
| [DNS Analytics（预览版）](insights/dns-analytics.md) | 收集、分析和关联 Windows DNS 分析和审核日志，以及 DNS 服务器的其他相关数据。 |
| [Cloud Foundry](../cloudfoundry/cloudfoundry-oms-nozzle.md) | 跨多个部署收集、查看和分析 Cloud Foundry 系统的运行状况和性能指标。 |
| [容器](insights/containers.md) | 查看和管理 Docker 和 Windows 容器主机。 |
| [按需评估](/services-hub/health/getting_started_with_on_demand_assessments) | 评估和优化本地、混合和云 Microsoft 技术环境的可用性、安全性和性能。 |
| [SQL 运行状况检查](insights/sql-assessment.md) | 评估 SQL Server 环境的风险和运行状况。  |
| [传输数据](insights/wire-data.md) | 通过 Log Analytics 代理从与 Windows 和 Linux 相连的计算机中收集的网络与性能整合数据。 |

## <a name="third-party-integration"></a>第三方集成

| 解决方案 | 说明 |
|:---|:---|
| [ITSM](platform/itsmc-overview.md) | 使用 IT 服务管理连接器 (ITSMC) 可以连接 Azure 和支持的 IT 服务管理 (ITSM) 产品/服务。  |


## <a name="resources-outside-of-azure"></a>Azure 以外的资源
Azure Monitor 可以使用下表中列出的方法从 Azure 以外的资源收集数据。

| 资源 | 方法 |
|:---|:---|
| 应用程序 | 使用 Application Insights 监视 Azure 以外的 web 应用程序。 请参阅[什么是 Application Insights？](./app/app-insights-overview.md)。 |
| 虚拟机 | 使用代理从其他云环境中的或本地的虚拟机的来宾操作系统收集数据。 请参阅 [Azure Monitor 代理概述](platform/agents-overview.md)。 |
| REST API 客户端 | 可使用单独的 API 将数据从任何 REST API 客户端写入 Azure Monitor 日志和指标。 请参阅[使用 HTTP 数据收集器 API 将日志数据发送到 Azure Monitor](platform/data-collector-api.md)，以及[使用 REST API 将 Azure 资源的自定义指标发送到 Azure Monitor 指标存储](platform/metrics-store-custom-rest-api.md)。 |



## <a name="next-steps"></a>后续步骤

- 阅读和深入了解[存储由见解和解决方案收集的日志和指标的 Azure Monitor数据平台](platform/data-platform.md)。
- 完成[有关监视 Azure 资源的教程](learn/tutorial-resource-logs.md)。
- 完成[编写日志查询以分析 Azure Monitor 日志中的数据的相关教程](learn/tutorial-resource-logs.md)。
- 完成[创建指标图表以分析 Azure Monitor 指标中的数据的相关教程](learn/tutorial-metrics-explorer.md)。

 
