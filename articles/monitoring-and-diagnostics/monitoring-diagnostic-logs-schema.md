---
title: Azure 诊断日志支持的服务和架构 | Microsoft Docs
description: 了解 Azure 诊断日志支持的服务和事件架构。
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/12/2018
ms.author: johnkem
ms.openlocfilehash: 91c3f1507bb4fb64d5395917e8e431951f77e72b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Azure 诊断日志支持的服务、架构和类别

[Azure 资源诊断日志](monitoring-overview-of-diagnostic-logs.md)是 Azure 资源发出的日志，用于描述该资源的操作。 这些日志特定于资源类型。 在本文中，我们概要列出每个服务发出的事件支持的一组服务和事件架构。 本文还提供每种资源类型可用的日志类别的完整列表。

## <a name="supported-services-and-schemas-for-resource-diagnostic-logs"></a>资源诊断日志支持的服务和架构
资源诊断日志的架构因资源和日志类别而异。   

| 服务 | 架构和文档 |
| --- | --- |
| Analysis Services | 架构不可用。 |
| API 管理 | [API 管理诊断日志](../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| 应用程序网关 |[应用程序网关的诊断日志记录](../application-gateway/application-gateway-diagnostics.md) |
| Azure 自动化 |[Azure 自动化的 Log Analytics](../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure 批处理 |[Azure Batch 诊断日志记录](../batch/batch-diagnostics.md) |
| Customer Insights | 架构不可用。 |
| 内容传送网络 | 架构不可用。 |
| CosmosDB | [Azure Cosmos DB 日志记录](../cosmos-db/logging.md) |
| 数据湖分析 |[访问 Azure Data Lake Analytics 的诊断日志](../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[访问 Azure Data Lake Store 的诊断日志](../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 事件中心 |[Azure 事件中心诊断日志](../event-hubs/event-hubs-diagnostic-logs.md) |
| IoT 中心 | [IoT 中心操作](../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure 密钥保管库日志记录](../key-vault/key-vault-logging.md) |
| 负载均衡器 |[Azure 负载均衡器的 Log Analytics](../load-balancer/load-balancer-monitor-log.md) |
| 逻辑应用 |[逻辑应用 B2B 自定义跟踪架构](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 网络安全组 |[网络安全组 (NSG) 的 Log Analytics](../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS 保护 | 架构不可用。 |
| 恢复服务 | [Azure 备份的数据模型](../backup/backup-azure-reports-data-model.md)|
| 搜索 |[允许并使用搜索流量分析](../search/search-traffic-analytics.md) |
| 服务器管理 | 架构不可用。 |
| 服务总线 |[Azure 服务总线诊断日志](../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL 数据库 | [Azure SQL 数据库诊断日志记录](../sql-database/sql-database-metrics-diag-logging.md) |
| 流分析 |[作业诊断日志](../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 虚拟网络 | 架构不可用。 |

## <a name="supported-log-categories-per-resource-type"></a>每种资源类型支持的日志类别
|资源类型|类别|类别显示名称|
|---|---|---|
|Microsoft.AnalysisServices/servers|引擎|引擎|
|Microsoft.AnalysisServices/servers|服务|服务|
|Microsoft.ApiManagement/service|GatewayLogs|ApiManagement 网关的相关日志|
|Microsoft.Automation/automationAccounts|JobLogs|作业日志|
|Microsoft.Automation/automationAccounts|JobStreams|作业流|
|Microsoft.Automation/automationAccounts|DscNodeStatus|Dsc 节点状态|
|Microsoft.Batch/batchAccounts|ServiceLog|服务日志|
|Microsoft.Cdn/profiles/endpoints|CoreAnalytics|获取终结点的指标，例如带宽、流出量等。|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|管道活动运行日志|
|Microsoft.DataFactory/factories|PipelineRuns|管道运行日志|
|Microsoft.DataFactory/factories|TriggerRuns|触发器运行日志|
|Microsoft.DataLakeAnalytics/accounts|审核|审核日志|
|Microsoft.DataLakeAnalytics/accounts|请求|请求日志|
|Microsoft.DataLakeStore/accounts|审核|审核日志|
|Microsoft.DataLakeStore/accounts|Requests|请求日志|
|Microsoft.DBforPostgreSQL/servers|PostgreSQLLogs|PostgreSQL 服务器日志|
|Microsoft.Devices/IotHubs|连接|连接|
|Microsoft.Devices/IotHubs|DeviceTelemetry|设备遥测|
|Microsoft.Devices/IotHubs|C2DCommands|C2D 命令|
|Microsoft.Devices/IotHubs|DeviceIdentityOperations|设备标识操作|
|Microsoft.Devices/IotHubs|FileUploadOperations|文件上传操作|
|Microsoft.Devices/IotHubs|路由|路由|
|Microsoft.Devices/IotHubs|D2C 孪生操作|D2C 孪生操作|
|Microsoft.Devices/IotHubs|C2DTwinOperations|C2D 孪生操作|
|Microsoft.Devices/IotHubs|TwinQueries|孪生查询|
|Microsoft.Devices/IotHubs|JobsOperations|作业操作|
|Microsoft.Devices/IotHubs|DirectMethods|直接方法|
|Microsoft.Devices/IotHubs|E2EDiagnostics|E2E 诊断（预览版）|
|Microsoft.Devices/provisioningServices|DeviceOperations|设备操作|
|Microsoft.Devices/provisioningServices|ServiceOperations|服务操作|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
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
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS 保护通知|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM 保护警报|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|应用程序网关访问日志|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|应用程序网关性能日志|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|应用程序网关防火墙日志|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|网关诊断日志|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|隧道诊断日志|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|路由诊断日志|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE 诊断日志|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S 诊断日志|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|流量管理器探测运行状况结果事件|
|Microsoft.Network/expressRouteCircuits|GWMCountersTable|GWM 计数器表|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure 备份报告数据|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery 作业|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery 事件|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 复制项|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery 复制统计信息|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 恢复点|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 复制数据上传速度|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 受保护的磁盘数据改动|
|Microsoft.Search/searchServices|OperationLogs|操作日志|
|Microsoft.ServiceBus/namespaces|OperationalLogs|运行日志|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|查询存储运行时统计信息|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|查询存储等待统计信息|
|Microsoft.Sql/servers/databases|Errors|Errors|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|数据库等待统计信息|
|Microsoft.Sql/servers/databases|超时|超时|
|Microsoft.Sql/servers/databases|块|块|
|Microsoft.Sql/servers/databases|SQLInsights|SQL Insights|
|Microsoft.Sql/servers/databases|审核|审核日志|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL 安全审核事件|
|Microsoft.StreamAnalytics/streamingjobs|执行|执行|
|Microsoft.StreamAnalytics/streamingjobs|创作|创作|

## <a name="next-steps"></a>后续步骤

* [详细了解诊断日志](monitoring-overview-of-diagnostic-logs.md)
* [将资源诊断日志流式传输到事件中心](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [使用 Azure Monitor REST API 更改资源诊断设置](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [使用 Log Analytics 分析 Azure 存储中的日志](../log-analytics/log-analytics-azure-storage.md)
