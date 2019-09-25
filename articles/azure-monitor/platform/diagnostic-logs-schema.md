---
title: Azure 诊断日志支持的服务和架构
description: 了解 Azure 诊断日志支持的服务和事件架构。
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/11/2018
ms.author: robb
ms.subservice: logs
ms.openlocfilehash: 362b696351e4faca02fa6ea8aed7e7447454cd34
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262028"
---
# <a name="supported-services-schemas-and-categories-for-azure-diagnostic-logs"></a>Azure 诊断日志支持的服务、架构和类别

[Azure Monitor 诊断日志](../../azure-monitor/platform/resource-logs-overview.md)是 Azure 服务发出的日志，用于描述这些服务或资源的操作。 通过 Azure Monitor 提供的所有诊断日志共享公共顶级架构，且每个服务都能灵活地为其事件发出唯一属性。

资源类型（为 `resourceId` 属性时可用）和 `category` 的组合唯一标识架构。 本文介绍了诊断日志的顶级架构以及每个服务的架构链接。

## <a name="top-level-diagnostic-logs-schema"></a>顶级诊断日志架构

| 姓名 | 必需/可选 | 描述 |
|---|---|---|
| time | 必填 | 事件时间戳 (UTC)。 |
| resourceId | 必填 | 发出事件的资源的资源 ID。 对于租户服务，其形式为 /tenants/tenant-id/providers/provider-name。 |
| tenantId | 对于租户日志而言是必需的 | 此事件关联到的 Active Directory 租户的租户 ID。 此属性仅用于租户级日志，它不会出现在资源级日志中。 |
| operationName | 必填 | 此事件表示的操作的名称。 如果该事件表示 RBAC 操作，则这是 RBAC 操作名称 （例如 Microsoft.Storage/storageAccounts/blobServices/blobs/Read）。 通常以资源管理器操作的形式建模，即使它们不是实际记录的资源管理器操作 (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | 可选 | 如果使用 API 执行 operationName，则 api-version 与该操作关联（例如 `http://myservice.windowsazure.net/object?api-version=2016-06-01`）。 如果没有与此操作相对应的 API，则该版本表示该操作的版本，以防与操作相关联的属性在将来发生更改。 |
| category | 必填 | 事件的日志类别。 类别是可以在特定资源上启用或禁用日志的粒度。 在事件的属性 blob 内显示的属性在特定日志类别和资源类型中相同。 典型的日志类别是“Audit”、“Operational”、“Execution”和“Request”。 |
| resultType | 可选 | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| resultSignature | 可选 | 事件的子状态。 如果此操作对应于 REST API 调用，则这是相应 REST 调用的 HTTP 状态代码。 |
| resultDescription | 可选 | 此操作的静态文本说明，例如 “获取存储文件”。 |
| durationMs | 可选 | 操作持续时间，以毫秒为单位。 |
| callerIpAddress | 可选 | 调用方 IP 地址，如果该操作对应于来自具有公开 IP 地址的实体的 API 调用。 |
| correlationId | 可选 | 用于将一组相关事件组合在一起的 GUID。 通常情况下，如果两个事件具有相同 operationName，但具有两个不同状态（例如 “Started”和“Succeeded”），则它们共享相同的关联 ID。 这也可以代表事件之间的其他关系。 |
| identity | 可选 | 描述执行操作的用户或应用程序的标识的 JSON Blob。 通常，这将包括 Active Directory 中的授权和声明/JWT 令牌。 |
| Level | 可选 | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| location | 可选 | 发出事件的资源区域，例如 “美国东部”或“法国南部” |
| properties | 可选 | 与此特定类别的事件相关的任何扩展属性。 所有自定义/唯一属性都必须放入此架构的“B 部分”。 |

## <a name="service-specific-schemas-for-resource-diagnostic-logs"></a>资源诊断日志的服务特定架构
资源诊断日志的架构因资源和日志类别而异。 此列表显示使诊断日志和链接对服务和类别特定的架构可用的所有服务（如果可用）。

| 服务 | 架构和文档 |
| --- | --- |
| Azure Active Directory | [概述](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)、[审核日志架构](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)和[登录架构](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API 管理 | [API 管理诊断日志](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| 应用程序网关 |[应用程序网关的诊断日志记录](../../application-gateway/application-gateway-diagnostics.md) |
| Azure 自动化 |[Azure 自动化的 Log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 诊断日志记录](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL 诊断日志](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL 诊断日志](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| 认知服务 | [Azure 认知服务的诊断日志记录](../../cognitive-services/diagnostic-logging.md) |
| 内容分发网络 | [CDN 的 Azure 诊断日志](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 日志记录](../../cosmos-db/logging.md) |
| 数据工厂 | [使用 Azure Monitor 监视数据工厂](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[访问 Azure Data Lake Analytics 的诊断日志](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[访问 Azure Data Lake Store 的诊断日志](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 事件中心 |[Azure 事件中心诊断日志](../../event-hubs/event-hubs-diagnostic-logs.md) |
| 快速路由 | 架构不可用。 |
| Azure 防火墙 | 架构不可用。 |
| IoT 中心 | [IoT 中心操作](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure 密钥保管库日志记录](../../key-vault/key-vault-logging.md) |
| 负载均衡器 |[Azure 负载均衡器的 Log Analytics](../../load-balancer/load-balancer-monitor-log.md) |
| 逻辑应用 |[逻辑应用 B2B 自定义跟踪架构](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 网络安全组 |[网络安全组 (NSG) 的 Log Analytics](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS 保护 | [管理 Azure DDoS 防护标准](../../virtual-network/manage-ddos-protection.md) |
| Power BI 专用 | [Azure 中的 Power BI Embedded 的诊断日志记录](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| Recovery Services | [Azure 备份的数据模型](../../backup/backup-azure-reports-data-model.md)|
| 搜索 |[允许并使用搜索流量分析](../../search/search-traffic-analytics.md) |
| 服务总线 |[Azure 服务总线诊断日志](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL 数据库 | [Azure SQL 数据库诊断日志记录](../../sql-database/sql-database-metrics-diag-logging.md) |
| 流分析 |[作业诊断日志](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 通信管理器 | [流量管理器日志架构](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 虚拟网络 | 架构不可用。 |
| 虚拟网络网关 | 架构不可用。 |

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
|Microsoft.ClassicNetwork/networksecuritygroups|网络安全组规则流事件|网络安全组规则流事件|
|Microsoft.CognitiveServices/accounts|审核|审核日志|
|Microsoft.CognitiveServices/accounts|RequestResponse|请求和响应日志|
|Microsoft.ContainerService/managedClusters|kube-apiserver|Kubernetes API 服务器|
|Microsoft.ContainerService/managedClusters|kube-controller-manager|Kubernetes 控制器管理器|
|Microsoft.ContainerService/managedClusters|cluster-autoscaler|Kubernetes 群集自动缩放程序|
|Microsoft.ContainerService/managedClusters|kube-scheduler|Kubernetes 计划程序|
|Microsoft.ContainerService/managedClusters|防护|身份验证 Webhook|
|Microsoft.CustomerInsights/hubs|AuditEvents|AuditEvents|
|Microsoft.DataFactory/factories|ActivityRuns|管道活动运行日志|
|Microsoft.DataFactory/factories|PipelineRuns|管道运行日志|
|Microsoft.DataFactory/factories|TriggerRuns|触发器运行日志|
|Microsoft.DataLakeAnalytics/accounts|审核|审核日志|
|Microsoft.DataLakeAnalytics/accounts|请求|请求日志|
|Microsoft.DataLakeStore/accounts|审核|审核日志|
|Microsoft.DataLakeStore/accounts|请求|请求日志|
|Microsoft.DBforMySQL/servers|MySqlSlowLogs|MySQL 服务器日志|
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
|Microsoft.Devices/IotHubs|配置|配置|
|Microsoft.Devices/provisioningServices|DeviceOperations|设备操作|
|Microsoft.Devices/provisioningServices|ServiceOperations|服务操作|
|Microsoft.DocumentDB/databaseAccounts|DataPlaneRequests|DataPlaneRequests|
|Microsoft.DocumentDB/databaseAccounts|MongoRequests|MongoRequests|
|Microsoft.DocumentDB/databaseAccounts|QueryRuntimeStatistics|QueryRuntimeStatistics|
|Microsoft.EventHub/namespaces|ArchiveLogs|存档日志|
|Microsoft.EventHub/namespaces|OperationalLogs|操作日志|
|Microsoft.EventHub/namespaces|AutoScaleLogs|自动缩放日志|
|Microsoft.Insights/AutoscaleSettings|AutoscaleEvaluations|自动缩放评估|
|Microsoft.Insights/AutoscaleSettings|AutoscaleScaleActions|自动缩放缩放操作|
|Microsoft.IoTSpaces/Graph|跟踪|跟踪|
|Microsoft.IoTSpaces/Graph|操作|操作|
|Microsoft.IoTSpaces/Graph|审核|审核|
|Microsoft.IoTSpaces/Graph|UserDefinedFunction|UserDefinedFunction|
|Microsoft.IoTSpaces/Graph|流入量|流入量|
|Microsoft.IoTSpaces/Graph|流出量|流出量|
|Microsoft.KeyVault/vaults|AuditEvent|审核日志|
|Microsoft.Logic/workflows|WorkflowRuntime|工作流运行时诊断事件|
|Microsoft.Logic/integrationAccounts|IntegrationAccountTrackingEvents|集成帐户跟踪事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupEvent|网络安全组事件|
|Microsoft.Network/networksecuritygroups|NetworkSecurityGroupRuleCounter|网络安全组规则计数器|
|Microsoft.Network/loadBalancers|LoadBalancerAlertEvent|负载均衡器警报事件|
|Microsoft.Network/loadBalancers|LoadBalancerProbeHealthStatus|负载均衡器探测运行状况|
|Microsoft.Network/publicIPAddresses|DDoSProtectionNotifications|DDoS 保护通知|
|Microsoft.Network/publicIPAddresses|DDoSMitigationFlowLogs|DDoS 缓解决策的流日志|
|Microsoft.Network/publicIPAddresses|DDoSMitigationReports|DDoS 缓解措施报告|
|Microsoft.Network/virtualNetworks|VMProtectionAlerts|VM 保护警报|
|Microsoft.Network/applicationGateways|ApplicationGatewayAccessLog|应用程序网关访问日志|
|Microsoft.Network/applicationGateways|ApplicationGatewayPerformanceLog|应用程序网关性能日志|
|Microsoft.Network/applicationGateways|ApplicationGatewayFirewallLog|应用程序网关防火墙日志|
|Microsoft.Network/securegateways|AzureFirewallApplicationRule|Azure 防火墙应用程序规则|
|Microsoft.Network/securegateways|AzureFirewallNetworkRule|Azure 防火墙网络规则|
|Microsoft.Network/azurefirewalls|AzureFirewallApplicationRule|Azure 防火墙应用程序规则|
|Microsoft.Network/azurefirewalls|AzureFirewallNetworkRule|Azure 防火墙网络规则|
|Microsoft.Network/virtualNetworkGateways|GatewayDiagnosticLog|网关诊断日志|
|Microsoft.Network/virtualNetworkGateways|TunnelDiagnosticLog|隧道诊断日志|
|Microsoft.Network/virtualNetworkGateways|RouteDiagnosticLog|路由诊断日志|
|Microsoft.Network/virtualNetworkGateways|IKEDiagnosticLog|IKE 诊断日志|
|Microsoft.Network/virtualNetworkGateways|P2SDiagnosticLog|P2S 诊断日志|
|Microsoft.Network/trafficManagerProfiles|ProbeHealthStatusEvents|流量管理器探测运行状况结果事件|
|Microsoft.Network/expressRouteCircuits|PeeringRouteLog|对等互连路由表日志|
|Microsoft.Network/frontdoors|FrontdoorAccessLog|Frontdoor 访问日志|
|Microsoft.Network/frontdoors|FrontdoorWebApplicationFirewallLog|Frontdoor Web 应用程序防火墙日志|
|Microsoft.PowerBIDedicated/capacities|引擎|引擎|
|Microsoft.RecoveryServices/Vaults|AzureBackupReport|Azure 备份报告数据|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryJobs|Azure Site Recovery 作业|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryEvents|Azure Site Recovery 事件|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicatedItems|Azure Site Recovery 复制项|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationStats|Azure Site Recovery 复制统计|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryRecoveryPoints|Azure Site Recovery 恢复点|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryReplicationDataUploadRate|Azure Site Recovery 复制数据上传速度|
|Microsoft.RecoveryServices/Vaults|AzureSiteRecoveryProtectedDiskDataChurn|Azure Site Recovery 受保护的磁盘数据改动|
|Microsoft.Search/searchServices|OperationLogs|操作日志|
|Microsoft.ServiceBus/namespaces|OperationalLogs|操作日志|
|Microsoft.Sql/servers/databases|SQLInsights|SQL 见解|
|Microsoft.Sql/servers/databases|AutomaticTuning|自动优化|
|Microsoft.Sql/servers/databases|QueryStoreRuntimeStatistics|查询存储运行时统计信息|
|Microsoft.Sql/servers/databases|QueryStoreWaitStatistics|查询存储等待统计信息|
|Microsoft.Sql/servers/databases|错误|错误|
|Microsoft.Sql/servers/databases|DatabaseWaitStatistics|数据库等待统计信息|
|Microsoft.Sql/servers/databases|超时|超时|
|Microsoft.Sql/servers/databases|块|块|
|Microsoft.Sql/servers/databases|死锁|死锁|
|Microsoft.Sql/servers/databases|审核|审核日志|
|Microsoft.Sql/servers/databases|SQLSecurityAuditEvents|SQL 安全审核事件|
|Microsoft.Sql/servers/databases|DmsWorkers|Dms 辅助角色|
|Microsoft.Sql/servers/databases|ExecRequests|Exec 请求|
|Microsoft.Sql/servers/databases|RequestSteps|请求步骤|
|Microsoft.Sql/servers/databases|SqlRequests|Sql 请求|
|Microsoft.Sql/servers/databases|Waits|等待|
|Microsoft.Sql/managedInstances|ResourceUsageStats|资源使用情况统计信息|
|Microsoft.Sql/managedInstances|SQLSecurityAuditEvents|SQL 安全审核事件|
|Microsoft.Sql/managedInstances/databases|SQLInsights|SQL 见解|
|Microsoft.Sql/managedInstances/databases|QueryStoreRuntimeStatistics|查询存储运行时统计信息|
|Microsoft.Sql/managedInstances/databases|QueryStoreWaitStatistics|查询存储等待统计信息|
|Microsoft.Sql/managedInstances/databases|错误|错误|
|Microsoft.StreamAnalytics/streamingjobs|执行|执行|
|Microsoft.StreamAnalytics/streamingjobs|创作|创作|
|microsoft.web/sites|FunctionExecutionLogs|函数执行日志|
|microsoft.web/sites/slots|FunctionExecutionLogs|函数执行日志|

## <a name="next-steps"></a>后续步骤

* [详细了解诊断日志](../../azure-monitor/platform/resource-logs-overview.md)
* [将资源诊断日志流式传输到事件中心](../../azure-monitor/platform/resource-logs-stream-event-hubs.md)
* [使用 Azure Monitor REST API 更改资源诊断设置](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)
* [使用 Log Analytics 分析 Azure 存储中的日志](../../azure-monitor/platform/collect-azure-metrics-logs.md)
