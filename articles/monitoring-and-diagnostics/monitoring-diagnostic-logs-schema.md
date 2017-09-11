---
title: "Azure 诊断日志支持的服务和架构 | Microsoft Docs"
description: "了解 Azure 诊断日志支持的服务和事件架构。"
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: aa4fa6e0310b2725005dfa34e3225c89fb4282d6
ms.contentlocale: zh-cn
ms.lasthandoff: 08/24/2017

---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Azure 诊断日志支持的服务、架构和类别

[Azure 资源诊断日志](monitoring-overview-of-diagnostic-logs.md)是 Azure 资源发出的日志，用于描述该资源的操作。 这些日志特定于资源类型。 在本文中，我们概要列出每个服务发出的事件支持的一组服务和事件架构。 本文还提供每种资源类型可用的日志类别的完整列表。

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>资源诊断日志支持的服务和架构
资源诊断日志的架构因资源和日志类别而异。   

| 服务 | 架构和文档 |
| --- | --- |
| API 管理 | 架构不可用。 |
| 应用程序网关 |[应用程序网关的诊断日志记录](../application-gateway/application-gateway-diagnostics.md) |
| Azure 自动化 |[Azure 自动化的 Log Analytics](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 诊断日志记录](../batch/batch-diagnostics.md) |
| Customer Insights | 架构不可用。 |
| 内容传送网络 | 架构不可用。 |
| CosmosDB | 架构不可用。 |
| 数据湖分析 |[访问 Azure Data Lake Analytics 的诊断日志](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[访问 Azure Data Lake Store 的诊断日志](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 事件中心 |[Azure 事件中心诊断日志](../event-hubs/event-hubs-diagnostic-logs.md) |
| 密钥保管库 |[Azure 密钥保管库日志记录](../key-vault/key-vault-logging.md) |
| 负载均衡器 |[Azure 负载均衡器的 Log Analytics](../load-balancer/load-balancer-monitor-log.md) |
| 逻辑应用 |[逻辑应用 B2B 自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 网络安全组 |[网络安全组 (NSG) 的 Log Analytics](../virtual-network/virtual-network-nsg-manage-log.md) |
| 恢复服务 | 架构不可用。|
| 搜索 |[允许并使用搜索流量分析](../search/search-traffic-analytics.md) |
| 服务器管理 | 架构不可用。 |
| 服务总线 |[Azure 服务总线诊断日志](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| 流分析 |[作业诊断日志](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |

## <a name="supported-log-categories-per-resource-type"></a>每种资源类型支持的日志类别
|资源类型|类别|类别显示名称|
|---|---|---|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 网关的相关日志|
|Microsoft.Automation/automationAccounts|JobLogs|作业日志|
|Microsoft.Automation/automationAccounts|JobStreams|作业流|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Dsc 节点状态|
|Microsoft.Batch/batchAccounts|ServiceLog|服务日志|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|获取终结点的指标，例如带宽、流出量等。|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataLakeAnalytics/accounts|审核|审核日志|
|Microsoft.DataLakeAnalytics/accounts|请求|请求日志|
|Microsoft.DataLakeStore/accounts|审核|审核日志|
|Microsoft.DataLakeStore/accounts|请求|请求日志|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.EventHub/namespaces|ArchiveLogs|存档日志|
|Microsoft.EventHub/namespaces|OperationalLogs|运行日志|
|Microsoft.EventHub/namespaces|AutoScaleLogs|自动缩放日志|
|Microsoft.KeyVault/vaults|AuditEvent|审核日志|
|Microsoft.Logic/workflows|WorkflowRuntime|工作流运行时诊断事件|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|集成帐户跟踪事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|网络安全组事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|网络安全组规则计数器|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|负载均衡器警报事件|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|负载均衡器探测运行状况|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|应用程序网关访问日志|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|应用程序网关性能日志|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|应用程序网关防火墙日志|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure 备份报告数据|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery 作业|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery 事件|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 复制项|
|Microsoft.Search/searchServices|OperationLogs|操作日志|
|Microsoft.ServiceBus/namespaces|OperationalLogs|运行日志|
|Microsoft.StreamAnalytics/streamingjobs|执行|执行|
|Microsoft.StreamAnalytics/streamingjobs|创作|创作|

## <a name="next-steps"></a>后续步骤

* [详细了解诊断日志](monitoring-overview-of-diagnostic-logs.md)
* [将资源诊断日志流式传输到事件中心](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [使用 Azure Monitor REST API 更改资源诊断设置](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [使用 Log Analytics 分析 Azure 存储中的日志](../log-analytics/log-analytics-azure-storage.md)

