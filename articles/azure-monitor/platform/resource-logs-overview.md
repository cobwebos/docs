---
title: Azure 资源日志概述 |Microsoft Docs
description: 了解 Azure 资源日志支持的服务和事件架构。
ms.service: azure-monitor
ms.subservice: logs
ms.topic: reference
author: rboucher
ms.author: robb
ms.date: 09/20/2019
ms.openlocfilehash: a418e3d1a59379284422d1d24c1457ab61d84a4c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551754"
---
# <a name="azure-resource-logs-overview"></a>Azure 资源日志概述
Azure 资源日志是由 Azure 资源发出的[平台日志](platform-logs-overview.md)，描述了其内部操作。 所有资源日志共享一个通用顶级架构，并为每个服务提供灵活性，以便为自己的事件发出唯一属性。

> [!NOTE]
> 资源日志以前称为诊断日志。

## <a name="collecting-resource-logs"></a>收集资源日志
资源日志由受支持的 Azure 资源自动生成，但不收集这些资源，除非使用[诊断设置](diagnostic-settings.md)对其进行配置。 为每个 Azure 资源创建诊断设置，以便将日志转发到以下目标：

| 目标 | 场景 |
|:---|:---|:---|
| [Log Analytics 工作区](resource-logs-collect-storage.md) | 使用其他监视数据分析日志，并利用日志查询和日志警报等 Azure Monitor 功能。 |
| [Azure 存储](archive-diagnostic-logs.md) | 存档日志以进行审核或备份。 |
| [事件中心](resource-logs-stream-event-hubs.md) | 将日志流式传输到第三方日志记录和遥测系统。  |

## <a name="compute-resources"></a>计算资源
资源日志不同于 Azure 计算资源中的来宾 OS 级日志。 计算资源需要代理从其来宾操作系统收集日志和指标，包括诸如事件日志、syslog 和性能计数器这样的数据。 使用[诊断扩展](agents-overview.md#azure-diagnostic-extension)从 azure 虚拟机和[Log Analytics 代理](agents-overview.md#log-analytics-agent)路由日志数据，以便从 azure 中的虚拟机、其他云中以及本地到 Log Analytics 工作区收集日志和指标。 有关详细信息，请参阅[Azure Monitor 的监视数据源](data-sources.md)。

## <a name="resource-logs-schema"></a>资源日志架构
每个 Azure 服务都有其自己的架构，当资源日志写入其中一个目标时，它们都共享下表中的顶层架构。 有关每个服务的架构的链接，请参阅下面的[特定于服务的架构](#service-specific-schemas)。 

| 名称 | 必需/可选 | 描述 |
|---|---|---|
| time | 需要 | 事件时间戳 (UTC)。 |
| resourceId | 需要 | 发出事件的资源的资源 ID。 对于租户服务，其形式为 /tenants/tenant-id/providers/provider-name。 |
| tenantId | 对于租户日志而言是必需的 | 此事件关联到的 Active Directory 租户的租户 ID。 此属性仅用于租户级日志，它不会出现在资源级日志中。 |
| operationName | 需要 | 此事件表示的操作的名称。 如果该事件表示 RBAC 操作，则这是 RBAC 操作名称 （例如 Microsoft.Storage/storageAccounts/blobServices/blobs/Read）。 通常以资源管理器操作的形式建模，即使它们不是实际记录的资源管理器操作 (`Microsoft.<providerName>/<resourceType>/<subtype>/<Write/Read/Delete/Action>`) |
| operationVersion | 可选 | 如果使用 API 执行 operationName，则 api-version 与该操作关联（例如 `http://myservice.windowsazure.net/object?api-version=2016-06-01`）。 如果没有与此操作相对应的 API，则该版本表示该操作的版本，以防与操作相关联的属性在将来发生更改。 |
| category | 需要 | 事件的日志类别。 类别是可以在特定资源上启用或禁用日志的粒度。 在事件的属性 blob 内显示的属性在特定日志类别和资源类型中相同。 典型的日志类别是“Audit”、“Operational”、“Execution”和“Request”。 |
| resultType | 可选 | 事件的状态。 典型值包括“Started”、“In Progress”、“Succeeded”、“Failed”、“Active”和“Resolved”。 |
| resultSignature | 可选 | 事件的子状态。 如果此操作对应于 REST API 调用，则这是相应 REST 调用的 HTTP 状态代码。 |
| resultDescription | 可选 | 此操作的静态文本说明，例如 “获取存储文件”。 |
| durationMs | 可选 | 操作持续时间，以毫秒为单位。 |
| callerIpAddress | 可选 | 调用方 IP 地址，如果该操作对应于来自具有公开 IP 地址的实体的 API 调用。 |
| correlationId | 可选 | 用于将一组相关事件组合在一起的 GUID。 通常情况下，如果两个事件具有相同 operationName，但具有两个不同状态（例如 “Started”和“Succeeded”），则它们共享相同的关联 ID。 这也可以代表事件之间的其他关系。 |
| 标识 | 可选 | 描述执行操作的用户或应用程序的标识的 JSON Blob。 通常，这将包括 Active Directory 中的授权和声明/JWT 令牌。 |
| 级别 | 可选 | 事件的严重级别。 必须是信息性、警告、错误或严重。 |
| 位置 | 可选 | 发出事件的资源区域，例如 “美国东部”或“法国南部” |
| 属性 | 可选 | 与此特定类别的事件相关的任何扩展属性。 所有自定义/唯一属性都必须放入此架构的“B 部分”。 |

## <a name="service-specific-schemas"></a>服务特定的架构
资源诊断日志的架构因 `resourceId` 属性定义的资源类型和 `category` 属性而异。 以下列表显示了支持资源日志的所有 Azure 服务，其中包含指向服务的链接和特定于类别的架构的链接（如果有）。

| 服务 | 架构和文档 |
| --- | --- |
| Azure Active Directory | [概述](../../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)、[审核日志架构](../../active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema.md)和[登录架构](../../active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md) |
| Analysis Services | https://azure.microsoft.com/blog/azure-analysis-services-integration-with-azure-diagnostic-logs/ |
| API 管理 | [API 管理诊断日志](../../api-management/api-management-howto-use-azure-monitor.md#diagnostic-logs) |
| 应用程序网关 |[应用程序网关的诊断日志记录](../../application-gateway/application-gateway-diagnostics.md) |
| Azure 自动化 |[Azure 自动化的 Log Analytics](../../automation/automation-manage-send-joblogs-log-analytics.md) |
| Azure 批处理 |[Azure Batch 诊断日志记录](../../batch/batch-diagnostics.md) |
| Azure Database for MySQL | [Azure Database for MySQL 诊断日志](../../mysql/concepts-server-logs.md#diagnostic-logs) |
| Azure Database for PostgreSQL | [Azure Database for PostgreSQL 诊断日志](../../postgresql/concepts-server-logs.md#diagnostic-logs) |
| 认知服务 | [Azure 认知服务的诊断日志记录](../../cognitive-services/diagnostic-logging.md) |
| 内容分发网络 | [CDN 的 Azure 诊断日志](../../cdn/cdn-azure-diagnostic-logs.md) |
| CosmosDB | [Azure Cosmos DB 日志记录](../../cosmos-db/logging.md) |
| 数据工厂 | [使用 Azure Monitor 监视数据工厂](../../data-factory/monitor-using-azure-monitor.md) |
| Data Lake 分析 |[访问 Azure Data Lake Analytics 的诊断日志](../../data-lake-analytics/data-lake-analytics-diagnostic-logs.md) |
| Data Lake Store |[访问 Azure Data Lake Store 的诊断日志](../../data-lake-store/data-lake-store-diagnostic-logs.md) |
| 事件中心 |[Azure 事件中心诊断日志](../../event-hubs/event-hubs-diagnostic-logs.md) |
| 快速路由 | 架构不可用。 |
| Azure 防火墙 | 架构不可用。 |
| IoT 中心 | [IoT 中心操作](../../iot-hub/iot-hub-monitor-resource-health.md#use-azure-monitor) |
| Key Vault |[Azure 密钥保管库日志记录](../../key-vault/key-vault-logging.md) |
| 负载均衡器 |[Azure 负载均衡器的 Log Analytics](../../load-balancer/load-balancer-monitor-log.md) |
| Logic Apps |[逻辑应用 B2B 自定义跟踪架构](../../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md) |
| 网络安全组 |[网络安全组 (NSG) 的 Log Analytics](../../virtual-network/virtual-network-nsg-manage-log.md) |
| DDOS 保护 | [管理 Azure DDoS 防护标准](../../virtual-network/manage-ddos-protection.md) |
| Power BI 专用 | [Azure 中的 Power BI Embedded 的诊断日志记录](https://docs.microsoft.com/power-bi/developer/azure-pbie-diag-logs) |
| 恢复服务 | [Azure 备份的数据模型](../../backup/backup-azure-reports-data-model.md)|
| 搜索 |[允许并使用搜索流量分析](../../search/search-traffic-analytics.md) |
| 服务总线 |[Azure 服务总线诊断日志](../../service-bus-messaging/service-bus-diagnostic-logs.md) |
| SQL Database | [Azure SQL 数据库诊断日志记录](../../sql-database/sql-database-metrics-diag-logging.md) |
| Stream Analytics |[作业诊断日志](../../stream-analytics/stream-analytics-job-diagnostic-logs.md) |
| 流量管理器 | [流量管理器日志架构](../../traffic-manager/traffic-manager-diagnostic-logs.md) |
| 虚拟网络 | 架构不可用。 |
| 虚拟网络网关 | 架构不可用。 |

## <a name="next-steps"></a>后续步骤

* [了解更多有关](platform-logs-overview.md)可用于监视 Azure 的 azure 平台日志的信息。
