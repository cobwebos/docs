---
title: Azure 资源日志支持的服务和架构
description: 了解 Azure 资源日志支持的服务和事件架构。
ms.subservice: logs
ms.topic: reference
ms.date: 06/15/2020
ms.openlocfilehash: 7a97afa3f960393637b8af63c56fba419f853465
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077056"
---
# <a name="common-and-service-specific-schema-for-azure-resource-logs"></a>Azure 资源日志的通用架构和特定于服务的架构

> [!NOTE]
> 资源日志以前称为诊断日志。 此名称在 2019 年 10 月发生了更改，因为 Azure Monitor 收集的日志类型已转变，不仅仅包括 Azure 资源。 此外，你可以收集的资源日志类别列表过去曾在本文中列出。 它们已移到[资源日志类别](resource-logs-categories.md)。 

[Azure Monitor 资源日志](../../azure-monitor/platform/platform-logs-overview.md)是 Azure 服务发出的日志，用于描述这些服务或资源的操作。 通过 Azure Monitor 提供的所有资源日志共享公共顶级架构，且每个服务都能灵活地为其事件发出唯一属性。

资源类型（为 `resourceId` 属性时可用）和 `category` 的组合唯一标识架构。 本文介绍了资源日志的顶级架构以及每个服务的架构链接。


## <a name="top-level-common-schema"></a>顶级通用架构

| 名称 | 必需/可选 | 说明 |
|---|---|---|
| time | 必须 | 事件时间戳 (UTC)。 |
| ResourceId | 必须 | 发出事件的资源的资源 ID。 对于租户服务，其形式为 /tenants/tenant-id/providers/provider-name。 |
| tenantId | 对于租户日志而言是必需的 | 此事件关联到的 Active Directory 租户的租户 ID。 此属性仅用于租户级日志，它不会出现在资源级日志中。 |
| operationName | 必须 | 此事件表示的操作的名称。 如果事件表示 RBAC 操作，则这是 RBAC 操作名称（例如，Microsoft.Storage/storageAccounts/blobServices/blobs/Read）。 通常以资源管理器操作的形式建模，即使它们不是实际记录的资源管理器操作 (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | 可选 | 如果 operationName 是使用 API（例如 `http://myservice.windowsazure.net/object?api-version=2016-06-01`）执行的，则为与该操作关联的 api-version。 如果没有与此操作相对应的 API，则该版本表示该操作的版本，以防与操作相关联的属性在将来发生更改。 |
| category | 必须 | 事件的日志类别。 类别是可以在特定资源上启用或禁用日志的粒度。 在事件的属性 blob 内显示的属性在特定日志类别和资源类型中相同。 典型的日志类别是“Audit”、“Operational”、“Execution”和“Request”。 |
| resultType | 可选 | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| resultSignature | 可选 | 事件的子状态。 如果该操作对应于 REST API 调用，则此字段为相应 REST 调用的 HTTP 状态代码。 |
| resultDescription | 可选 | 此操作的静态文本说明，例如“获取存储文件”。 |
| durationMs | 可选 | 操作持续时间，以毫秒为单位。 |
| callerIpAddress | 可选 | 调用方 IP 地址，前提是该操作对应于来自某个具有公开可用 IP 地址的实体的 API 调用。 |
| correlationId | 可选 | 用于将一组相关事件组合在一起的 GUID。 通常，如果两个事件的 operationName 相同但状态不同（例如，状态分别为“Started”和“Succeeded”），则它们共享相同的相关 ID。 这也可以代表事件之间的其他关系。 |
| identity | 可选 | 描述执行操作的用户或应用程序的标识的 JSON Blob。 通常，此字段包括 Active Directory 中的授权和声明/JWT 令牌。 |
| Level | 可选 | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| location | 可选 | 发出事件的资源的区域，例如 "美国东部" 或 "法国南部" |
| properties | 可选 | 与此特定类别的事件相关的任何扩展属性。 所有自定义/唯一属性都必须放入此架构的“B 部分”。 |

## <a name="service-specific-schemas"></a>特定于服务的架构

资源日志的架构因资源和日志类别而异。 此列表显示可提供资源日志的服务，并链接到该服务和特定于类别的架构（如果可用）。 随着新服务的添加，此列表会不断变化。因此，如果你在下面看不到所需的内容，请使用搜索引擎来发现其他文档。 请随时在 GitHub 上提交与本文相关的问题，以便我们进行更新。

| 服务 | 架构和文档 |
| --- | --- |
| Azure Active Directory | [概述](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)、[审核日志架构](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)和[登录架构](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://docs.microsoft.com/azure/analysis-services/analysis-services-logging |
| API 管理 | [API 管理资源日志](../../api-management/api-management-howto-use-azure-monitor.md#resource-logs) |
| 应用程序网关 |[应用程序网关的日志记录](../../application-gateway/application-gateway-diagnostics.md) |
| Azure 自动化 |[适用于 Azure 自动化的 Log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure Batch |[Azure Batch 日志记录](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL 诊断日志](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL 日志](../../postgresql/concepts-server-logs.md#resource-logs) |
| Azure 数据资源管理器 | [Azure 数据资源管理器日志](/azure/data-explorer/using-diagnostic-logs) |
| 认知服务 | [Azure 认知服务的日志记录](../../cognitive-services/diagnostic-logging.md) |
| 容器注册表 | [Azure 容器注册表的日志记录](../../container-registry/container-registry-diagnostics-audit-logs.md) |
| 内容分发网络 | [用于 CDN 的 Azure 日志](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 日志记录](../../cosmos-db/monitor-cosmos-db.md) |
| Data Factory | [使用 Azure Monitor 监视数据工厂](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake Analytics |[访问 Azure Data Lake Analytics 的日志](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[访问 Azure Data Lake Store 的日志](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 事件中心 |[Azure 事件中心日志](../../event-hubs/event-hubs-diagnostic-logs.md) |
| Express Route | 架构不可用。 |
| Azure 防火墙 | 架构不可用。 |
| IoT 中心 | [IoT 中心操作](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| 密钥保管库 |[Azure 密钥保管库日志记录](../../key-vault/general/logging.md) |
| Kubernetes 服务 |[Azure Kubernetes 日志记录](../../aks/view-master-logs.md#log-event-schema) |
| 负载均衡器 |[Azure 负载均衡器的 Log Analytics](../../load-balancer/load-balancer-monitor-log.md) |
| 逻辑应用 |[逻辑应用 B2B 自定义跟踪架构](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 网络安全组 |[网络安全组 (NSG) 的 Log Analytics](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS 保护 | [管理 Azure DDoS 防护标准](../../virtual-network/manage-ddos-protection.md) |
| Power BI 专用 | [Azure 中 Power BI Embedded 的日志记录](/power-bi/developer/azure-pbie-diag-logs) |
| 恢复服务 | [Azure 备份的数据模型](../../backup/backup-azure-reports-data-model.md)|
| 搜索 |[允许并使用搜索流量分析](../../search/search-traffic-analytics.md) |
| 服务总线 |[Azure 服务总线日志](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL 数据库 | [Azure SQL 数据库日志记录](../../azure-sql/database/metrics-diagnostic-telemetry-logging-streaming-export-configure.md) |
| 流分析 |[作业日志](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 流量管理器 | [流量管理器日志架构](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 虚拟网络 | 架构不可用。 |
| 虚拟网络网关 | 架构不可用。 |


## <a name="next-steps"></a>后续步骤

* [查看可以收集的资源日志类别](resource-logs-categories.md)
* [详细了解资源日志](../../azure-monitor/platform/platform-logs-overview.md)
* [将资源日志流式传输到**事件中心**](./resource-logs.md#send-to-azure-event-hubs)
* [使用 Azure Monitor REST API 更改资源日志诊断设置](/rest/api/monitor/diagnosticsettings)
* [使用 Log Analytics 分析 Azure 存储中的日志](./resource-logs.md#send-to-log-analytics-workspace)
