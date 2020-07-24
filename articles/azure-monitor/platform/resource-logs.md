---
title: Azure 资源日志
description: 了解如何将 Azure 资源日志流式传输到 Azure Monitor 中的 Log Analytics 工作区。
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 6a7b24de860b543778d7e6ceabc95d10bf7c44c2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87077087"
---
# <a name="azure-resource-logs"></a>Azure 资源日志
Azure 资源日志是[平台日志](platform-logs-overview.md)，可以通过它深入了解已在 Azure 资源中执行的操作。 资源日志的内容因 Azure 服务和资源类型而异。 默认不会收集资源日志。 必须为每个 Azure 资源创建诊断设置，以便将其资源日志发送到 Log Analytics 工作区与 [Azure Monitor 日志](data-platform-logs.md)一起使用，发送到 Azure 事件中心以转发到 Azure 外部，或者发送到 Azure 存储进行存档。

有关创建诊断设置的详细信息，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](diagnostic-settings.md)。若要详细了解如何使用 Azure Policy 为你创建的每个 Azure 资源自动创建诊断设置，请参阅[使用 Azure Policy 大规模部署 Azure Monitor](deploy-scale.md)。

## <a name="send-to-log-analytics-workspace"></a>发送到 Log Analytics 工作区
 将资源日志发送到 Log Analytics 工作区，以启用 [Azure Monitor 日志](data-platform-logs.md)的功能，其中包括以下内容：

- 将资源日志数据与 Azure Monitor 收集的其他监视数据关联。
- 将来自多个 Azure 资源、订阅和租户的日志项合并到一个位置一起进行分析。
- 使用日志查询执行复杂分析，并深入了解日志数据。
- 使用带有复杂警报逻辑的日志警报。

[创建诊断设置](diagnostic-settings.md)，以便将资源日志发送到 Log Analytics 工作区。 如 [Azure Monitor 日志的结构](../log-query/logs-structure.md)中所述，此数据将存储在表中。 资源日志使用的表取决于资源使用的收集类型：

- Azure 诊断 - 所有数据将写入到 _AzureDiagnostics_ 表中。
- 特定于资源 - 每个类别的资源的数据将写入到单独的表中。

### <a name="azure-diagnostics-mode"></a>Azure 诊断模式 
在此模式下，任何诊断设置中的所有数据都将收集到 AzureDiagnostics 表中。 这是当今大多数 Azure 服务使用的传统方法。 由于多个资源类型会将数据发送到同一个表，因此其架构是所收集的所有不同数据类型的架构的超集。

在以下示例中，以下数据类型的诊断设置将收集到同一个工作区中：

- 服务 1 的审核日志（架构由列 A、B 和 C 组成）  
- 服务 1 的错误日志（架构由列 D、E 和 F 组成）  
- 服务 2 的审核日志（架构由列 G、H 和 I 组成）  

AzureDiagnostics 表的外观如下所示：  

| ResourceProvider    | Category     | A  | B  | C  | D  | E  | F  | G  | H  | I  |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Service1 | AuditLogs    | x1 | y1 | z1 |    |    |    |    |    |    |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q1 | w1 | e1 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j1 | k1 | l1 |
| Microsoft.Service1 | ErrorLogs    |    |    |    | q2 | w2 | e2 |    |    |    |
| Microsoft.Service2 | AuditLogs    |    |    |    |    |    |    | j3 | k3 | l3 |
| Microsoft.Service1 | AuditLogs    | x5 | y5 | z5 |    |    |    |    |    |    |
| ... |

### <a name="resource-specific"></a>特定于资源
在此模式下，将会根据在诊断设置中选择的每个类别，在所选工作区中创建各个表。 建议使用此方法，因为它可以大幅简化日志查询中数据的处理、更好地发现架构及其结构、改善引入延迟和查询时间方面的性能、可以授予针对特定表的 RBAC 权限，等等。 所有 Azure 服务最终都会迁移到特定于资源的模式。 

以上示例会创建三个表：
 
- 如下所示的表 *Service1AuditLogs*：

    | 资源提供程序 | Category | A | B | C |
    | -- | -- | -- | -- | -- |
    | Service1 | AuditLogs | x1 | y1 | z1 |
    | Service1 | AuditLogs | x5 | y5 | z5 |
    | ... |

- 如下所示的表 *Service1ErrorLogs*：  

    | 资源提供程序 | Category | D | E | F |
    | -- | -- | -- | -- | -- | 
    | Service1 | ErrorLogs |  q1 | w1 | e1 |
    | Service1 | ErrorLogs |  q2 | w2 | e2 |
    | ... |

- 如下所示的表 *Service2AuditLogs*：  

    | 资源提供程序 | Category | G | H | I |
    | -- | -- | -- | -- | -- |
    | Service2 | AuditLogs | j1 | k1 | l1|
    | Service2 | AuditLogs | j3 | k3 | l3|
    | ... |



### <a name="select-the-collection-mode"></a>选择收集模式
大多数 Azure 资源在“Azure 诊断”或“特定于资源”模式下将数据写入工作区，而不允许用户选择模式。  有关使用哪种模式的详细信息，请参阅[每个服务的文档](./resource-logs-schema.md)。 所有 Azure 服务最终都会使用特定于资源的模式。 在进行这种过渡期间，某些资源允许在诊断设置中选择模式。 为任何新的诊断设置指定特定于资源的模式，因为这可以更轻松地管理数据，并可帮助你在今后避免复杂的迁移。
  
   ![诊断设置模式选择器](media/resource-logs-collect-workspace/diagnostic-settings-mode-selector.png)

> [!NOTE]
> 有关使用 resource manager 模板设置收集模式的示例，请参阅[Azure Monitor 中的诊断设置资源管理器模板示例](../samples/resource-manager-diagnostic-settings.md#diagnostic-setting-for-recovery-services-vault)。


可将现有的诊断设置修改为特定于资源的模式。 在这种情况下，已收集的数据将保留在 _AzureDiagnostics_ 表中，直到根据工作区的保留设置删除了这些数据。 新数据将收集到专用表中。 可以使用 [union](/azure/kusto/query/unionoperator) 运算符跨两个表查询数据。

有关支持特定于资源模式的 Azure 服务的公告，请继续阅读 [Azure 更新](https://azure.microsoft.com/updates/)博客。

### <a name="column-limit-in-azurediagnostics"></a>AzureDiagnostics 中的列限制
Azure Monitor 日志中的任何一个表限制为 500 个属性。 一旦达到该限制，在引入时，包含不属于前 500 个属性的数据的所有行将被删除。 *AzureDiagnostics* 表特别容易超过此限制，因为它包含写入到其中的所有 Azure 服务的属性。

如果从多个服务收集资源日志，AzureDiagnostics 可能会超过此限制，因此数据将会丢失。 在所有 Azure 服务都支持特定于资源的模式之前，应将资源配置为写入到多个工作区，以减少达到 500 列限制的可能性。

### <a name="azure-data-factory"></a>Azure 数据工厂
由于包含一组详细的日志，因此 Azure 数据工厂是一种已知会写入大量列并可能导致 AzureDiagnostics 超过其限制的服务。 对于在启用特定于资源的模式之前配置的任何诊断设置，将会针对任一活动，为每个唯一命名的用户参数创建一个新列。 由于活动输入和输出的详细特性，将会创建更多列。
 
应尽快迁移日志，以使用特定于资源的模式。 如果无法立即做到这一点，一种临时的替代方案是将 Azure 数据工厂日志隔离到其自身的工作区，以尽量减少这些日志影响工作区中收集的其他日志类型的可能性。


## <a name="send-to-azure-event-hubs"></a>发送到 Azure 事件中心
将资源日志发送到事件中心可将其发送到 Azure 之外，例如，发送到第三方 SIEM 或其他日志分析解决方案。 来自事件中心的资源日志以 JSON 格式使用，其中的 `records` 元素包含每个有效负载中的记录。 架构取决于资源类型，如 [Azure 资源日志的通用架构和特定于服务的架构](resource-logs-schema.md)中所述。

下面是事件中心的资源日志输出数据示例：

```json
{
    "records": [
        {
            "time": "2019-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2019-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "00000000-0000-0000-0000-000000000000",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

## <a name="send-to-azure-storage"></a>发送到 Azure 存储
将资源日志发送到 Azure 存储，将其保留以供存档。 创建诊断设置以后，一旦在已启用的日志类别之一中出现事件，就会在存储帐户中创建存储容器。 容器中的 blob 使用以下命名约定：

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

例如，网络安全组的 blob 的名称可能如下所示：

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

每个 PT1H.json blob 都包含一个 JSON blob，其中的事件为在 blob URL 中指定的小时（例如 h=12）内发生的。 在当前的小时内发生的事件将附加到 PT1H.json 文件。 分钟值始终为 00 (m=00)，因为资源日志事件按小时细分成单个 blob。

在 PT1H.json 文件中，每个事件都按以下格式存储。 这将使用通用顶级架构，但该架构对于每个 Azure 服务都是独一无二的，如[资源日志架构](./resource-logs-schema.md)中所述。

``` JSON
{"time": "2016-07-01T00:00:37.2040000Z","systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1","category": "NetworkSecurityGroupRuleCounter","resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG","operationName": "NetworkSecurityGroupCounters","properties": {"vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}","subnetPrefix": "10.3.0.0/24","macAddress": "000123456789","ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp","direction": "In","type": "allow","matchedConnections": 1988}}
```

> [!NOTE]
> 平台日志使用 [JSON 行](http://jsonlines.org/)写入到 blob 存储，其中每个事件都是一行，换行符表示新事件。 此格式已在 2018 年 11 月实现。 在此日期之前，日志以记录的 json 数组形式写入到 blob 存储，详见[为存档到存储帐户的 Azure Monitor 平台日志的格式更改做准备](resource-logs-blob-format.md)。


## <a name="next-steps"></a>后续步骤

* [详细阅读资源日志](platform-logs-overview.md)。
* [创建诊断设置以将平台日志和指标发送到不同的目标](diagnostic-settings.md)。
