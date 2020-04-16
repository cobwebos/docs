---
title: 使用 Azure Monitor 监视数据工厂
description: 了解如何在 Azure Monitor 中，使用数据工厂中的信息通过启用诊断日志来监视 Azure 数据工厂管道。
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/11/2018
ms.openlocfilehash: 5753336eeef115038de4eb0b5ade0651b1fa293e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419453"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>使用 Azure Monitor 发出警报和监视数据工厂

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

云应用程序比较复杂，包含很多移动部件。 监视功能可以提供数据来确保应用程序始终处于正常运行状态。 监视功能还有助于避免潜在问题，或者排查以往的问题。

可以使用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性。 此外，还有助于实现本来需要手动干预的操作的自动化。

Azure Monitor 针对大多数 Azure 服务提供基本级别的基础结构指标和日志。 Azure 诊断日志由资源发出，提供与该资源的操作相关的各种频繁生成的数据。 Azure 数据工厂在 Monitor 中写入诊断日志。

有关详细信息，请参阅 [Azure Monitor 概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)。

## <a name="keeping-azure-data-factory-data"></a>保留 Azure 数据工厂数据

数据工厂仅将管道运行数据存储 45 天。 若要将这些数据保留更长时间，请使用 Monitor。 使用 Monitor 可以路由诊断日志以进行分析。 此外，还可以将诊断日志保存到存储帐户中，以获得所选持续时间内的工厂信息。

## <a name="diagnostic-logs"></a>诊断日志

* 将诊断日志保存到存储帐户进行审核或手动检查。 可以使用诊断设置指定保留时间（天）。
* 将日志流式传输到 Azure 事件中心。 日志可用作合作伙伴服务或自定义分析解决方案（例如 Power BI）的输入。
* 使用 Log Analytics 分析日志。

可以使用与发出日志的资源不同的订阅中的存储帐户或事件中心命名空间。 配置此设置的用户必须对两个订阅都具有适当的基于角色的访问控制 (RBAC) 访问权限。

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

### <a name="diagnostic-settings"></a>诊断设置

可以使用诊断设置来配置非计算资源的诊断日志。 用于资源控制的诊断设置具有以下功能：

* 指定要将诊断日志发送到何处。 例如，发送到 Azure 存储帐户、Azure 事件中心或 Monitor 日志。
* 指定要发送的日志类别。
* 指定要将每个日志类别保留在存储帐户中多长时间。
* 保留期为 0 天表示永久保留日志。 如果不需要永久保留，可将该值设置为 1 到 2,147,483,647 的任意天数。
* 如果设置了保留策略，但禁止将日志存储在存储帐户中，则保留策略无效。 例如，如果仅选择了“事件中心”或“Monitor 日志”选项，此可能会发生这种情况。
* 保留策略按天应用。 一天的结束时间可能出现在协调世界时 (UTC) 的午夜。 在一天结束时，会删除当天已超过保留策略期限的日志。 例如，如果保留策略的期限为一天，则在今天开始时，会删除前天的日志。

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>通过 Azure Monitor REST API 启用诊断日志

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>在 Monitor REST API 中创建或更新诊断设置

##### <a name="request"></a>请求

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>头文件

* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组来管理 Azure 资源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure Active Directory (Azure AD) 获取的 JSON Web 令牌。 有关详细信息，请参阅[身份验证请求](../active-directory/develop/authentication-scenarios.md)。

##### <a name="body"></a>Body

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| properties | 类型 | 说明 |
| --- | --- | --- |
| **存储帐户 Id** |字符串 | 要将诊断日志发送到的存储帐户的资源 ID。 |
| **serviceBusRuleId** |字符串 | 服务总线命名空间的服务总线规则 ID。你要在该服务总线命名空间中创建事件中心，以便流式传输诊断日志。 规则 ID 的格式为 `{service bus resource ID}/authorizationrules/{key name}`。|
| **工作区 Id** | 复杂类型 | 指标时间粒度及其保留策略的数组。 此属性的值为空。 |
|**指标**| 要传递到被调用管道的管道运行的参数值| 将参数名映射为自变量值的 JSON 对象。 |
| **日志**| 复杂类型| 某个资源类型的诊断日志类别的名称。 若要获取资源的诊断日志类别列表，请先执行 GET 诊断设置操作。 |
| **类别**| 字符串| 日志类别及其保留策略的数组。 |
| **时间格雷** | 字符串 | 以 ISO 8601 持续时间格式捕获的指标的粒度。 该属性值必须为 `PT1M`（1 分钟）。 |
| **启用**| Boolean | 指定是否为此资源启用了该指标或日志类别的收集。 |
| **retentionPolicy**| 复杂类型| 描述指标或日志类别的保留策略。 此属性仅用于存储帐户。 |
|**天**| Int| 指标或日志的保留天数。 如果该属性值为 0，则永久保留日志。 此属性仅用于存储帐户。 |

##### <a name="response"></a>响应

200 正常。


```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>在 Monitor REST API 中获取有关诊断设置的信息

##### <a name="request"></a>请求

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>头文件

* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组来管理 Azure 资源](../azure-resource-manager/management/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure AD 获取的 JSON Web 令牌。 有关详细信息，请参阅[身份验证请求](../active-directory/develop/authentication-scenarios.md)。

##### <a name="response"></a>响应

200 正常。

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}

```
有关详细信息，请参阅[诊断设置](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings)。

## <a name="schema-of-logs-and-events"></a>日志和事件的架构

### <a name="monitor-schema"></a>监视架构

#### <a name="activity-run-log-attributes"></a>活动运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| properties | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **级别** |字符串 | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **相关性Id** |字符串 | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字符串 | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| 字符串| 活动运行的 ID。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| 字符串| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**资源 Id**| 字符串 | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**类别**| 字符串 | 诊断日志的类别。 请将该属性值设置为 `ActivityRuns`。 | `ActivityRuns` |
|**水平**| 字符串 | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**操作名称**| 字符串 | 活动的名称及其状态。 如果活动是启动检测信号，则属性值为 `MyActivity -`。 如果活动是结束检测信号，则属性值为 `MyActivity - Succeeded`。 | `MyActivity - Succeeded` |
|**pipelineName**| 字符串 | 管道的名称。 | `MyPipeline` |
|**activityName**| 字符串 | 活动的名称。 | `MyActivity` |
|**start**| 字符串 | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`|
|**结束**| 字符串 | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果诊断日志显示活动已启动但尚未结束，则该属性值为 `1601-01-01T00:00:00Z`。 | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>管道运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| properties | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **级别** |字符串 | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **相关性Id** |字符串 | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字符串 | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| 字符串| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**资源 Id**| 字符串 | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**类别**| 字符串 | 诊断日志的类别。 请将该属性值设置为 `PipelineRuns`。 | `PipelineRuns` |
|**水平**| 字符串 | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**操作名称**| 字符串 | 管道的名称及其状态。 管道运行完成后，该属性值为 `Pipeline - Succeeded`。 | `MyPipeline - Succeeded`. |
|**pipelineName**| 字符串 | 管道的名称。 | `MyPipeline` |
|**start**| 字符串 | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`. |
|**结束**| 字符串 | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果诊断日志显示活动已启动但尚未结束，则该属性值为 `1601-01-01T00:00:00Z`。  | `2017-06-26T20:55:29.5007959Z` |
|**状态**| 字符串 | 管道运行的最终状态。 可能的属性值为 `Succeeded` 和 `Failed`。 | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>触发器运行日志属性

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}

```

| properties | 类型 | 说明 | 示例 |
| --- | --- | --- | --- |
| **级别** |字符串 | 诊断日志的级别。 对于活动运行日志，请将该属性值设置为 4。 | `4` |
| **相关性Id** |字符串 | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字符串 | 事件的时间，以时间跨度表示，采用 UTC 格式 `YYYY-MM-DDTHH:MM:SS.00000Z`。 | `2017-06-28T21:00:27.3534352Z` |
|**触发器 Id**| 字符串| 触发器运行的 ID。 | `08587023010602533858661257311` |
|**资源 Id**| 字符串 | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**类别**| 字符串 | 诊断日志的类别。 请将该属性值设置为 `PipelineRuns`。 | `PipelineRuns` |
|**水平**| 字符串 | 诊断日志的级别。 请将该属性值设置为 `Informational`。 | `Informational` |
|**操作名称**| 字符串 | 触发器的名称及其最终状态（指示是否已成功激发该触发器）。 如果检测信号成功，则该属性值为 `MyTrigger - Succeeded`。 | `MyTrigger - Succeeded` |
|**触发器名称**| 字符串 | 触发器的名称。 | `MyTrigger` |
|**触发器类型**| 字符串 | 触发器的类型。 可能的属性值为 `Manual Trigger` 和 `Schedule Trigger`。 | `ScheduleTrigger` |
|**triggerEvent**| 字符串 | 触发器的事件。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| 字符串 | 激发触发器的开始时间，以时间跨度表示，采用 UTC 格式。 | `2017-06-26T20:55:29.5007959Z`|
|**状态**| 字符串 | 最终状态（指示是否已成功激发该触发器）。 可能的属性值为 `Succeeded` 和 `Failed`。 | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics 架构

Log Analytics 从 Monitor 继承架构，但存在以下例外情况：

* 每个列名中的第一个字母会大写。 例如，Monitor 中的列名“correlationId”在 Log Analytics 中为“CorrelationId”。
* 没有“Level”列。
* 动态“properties”列保留为以下动态 JSON Blob 类型。

    | Azure Monitor 列 | Log Analytics 列 | 类型 |
    | --- | --- | --- |
    | $.properties.UserProperties | UserProperties | 动态 |
    | $.properties.Annotations | 批注 | 动态 |
    | $.properties.Input | 输入 | 动态 |
    | $.properties.Output | 输出 | 动态 |
    | $.properties.Error.errorCode | ErrorCode | int |
    | $.properties.Error.message | ErrorMessage | 字符串 |
    | $.properties.Error | 错误 | 动态 |
    | $.properties.Predecessors | Predecessors | 动态 |
    | $.properties.Parameters | 参数 | 动态 |
    | $.properties.SystemParameters | SystemParameters | 动态 |
    | $.properties.Tags | Tags | 动态 |
    
## <a name="metrics"></a>指标

使用 Monitor 可以洞察 Azure 工作负荷的性能与运行状况。 最重要的 Monitor 数据类型是指标（也称为性能计数器）。 大多数 Azure 资源都会发出指标。 Monitor 提供多种方式来配置和使用这些指标，以便进行监视与故障排除。

Azure 数据工厂版本 2 发出以下指标。

| **指标**           | **指标显示名称**         | **单位** | **聚合类型** | **说明**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | 成功的管道运行数指标 | Count    | 总计                | 在一分钟时段内成功的管道运行总数。 |
| PipelineFailedRuns   | 失败的管道运行数指标    | Count    | 总计                | 在一分钟时段内失败的管道运行总数。    |
| ActivitySucceededRuns | 成功的活动运行数指标 | Count    | 总计                | 在一分钟时段内成功的活动运行总数。  |
| ActivityFailedRuns   | 失败的活动运行数指标    | Count    | 总计                | 在一分钟时段内失败的活动运行总数。     |
| TriggerSucceededRuns | 成功的触发器运行数指标  | Count    | 总计                | 在一分钟时段内成功的触发器运行总数。   |
| TriggerFailedRuns    | 失败的触发器运行数指标     | Count    | 总计                | 在一分钟时段内失败的触发器运行总数。      |

若要访问指标，请参阅 [Azure Monitor 数据平台](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)中的说明。

> [!NOTE]
> 仅发出已完成、触发的活动和管道运行事件。 正在进行，**不会**发出沙盒/调试运行。 

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>使用 Azure Monitor 监视数据工厂指标

可以使用 Azure 数据工厂与 Monitor 的集成将数据路由到 Monitor。 此集成在以下情况下非常有用：

* 可以针对由数据工厂发布到 Monitor 的丰富指标集编写复杂查询。 可以通过 Monitor 创建针对这些查询的自定义警报。

* 你希望跨数据工厂进行监视。 可将来自多个数据工厂的数据路由到单个 Monitor 工作区。

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>配置诊断设置和工作区

为数据工厂创建或添加诊断设置。

1. 在门户中，转到“监视”。 选择 **"设置** > **诊断设置**"。

1. 选择要为其设置诊断设置的数据工厂。

1. 如果所选数据工厂不存在任何设置，则系统会提示你创建设置。 选择“启用诊断”****。

   ![如果不存在任何设置，请创建一个诊断设置](media/data-factory-monitor-oms/monitor-oms-image1.png)

   如果数据工厂中存在现有设置，你将看到数据工厂中已配置的设置列表。 选择“添加诊断设置”。****

   ![如果存在设置，则添加诊断设置](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 为设置指定名称，选择“发送到 Log Analytics”****，然后从 **Log Analytics 工作区**中选择一个工作区。

    ![命名设置并选择 log-analytics 工作区](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. 选择“保存”。 

几分钟后，新设置将出现在此数据工厂的设置列表中。 生成新的事件数据后，诊断日志将立即流式传输到该工作区。 发出事件后可能需要最多 15 分钟的时间该事件才会出现在 Log Analytics 中。

* 在_特定于资源的_模式下，Azure 数据工厂的诊断日志将流入_ADFPipelineRun、ADF__触发器运行_和_ADFActivityRun_表
* 在“Azure 诊断”模式下，__ 诊断日志流入 _AzureDiagnostics_ 表

> [!NOTE]
> 由于 Azure 日志表的列数不能超过 500，因此强烈建议选择“特定于资源”模式。 有关详细信息，请参阅 [Log Analytics 已知限制](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics)。

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>从 Azure 市场安装 Azure 数据工厂分析

![转到"Azure 应用商店"，输入"分析筛选器"，然后选择"Azure 数据工厂分析（预览）"）](media/data-factory-monitor-oms/monitor-oms-image3.png)

![有关"Azure 数据工厂分析（预览）"的详细信息](media/data-factory-monitor-oms/monitor-oms-image4.png)

选择 **"创建**"，然后选择**OMS 工作区**和**OMS 工作区设置**。

![创建新解决方案](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>监视数据工厂指标

安装 Azure 数据工厂分析将创建一组默认的视图，以便启用以下指标：

- ADF 运行 - 1） 按数据工厂运行管道
 
- ADF 运行- 2) 数据工厂执行的活动运行

- ADF 运行 - 3） 按数据工厂触发运行

- ADF 错误 - 1） 数据工厂前 10 个管道错误

- ADF 错误 - 2） 数据工厂运行的前 10 个活动

- ADF 错误 - 3） 数据工厂前 10 个触发错误

- ADF 统计 - 1） 按类型运行活动

- ADF 统计 - 2） 按类型运行触发器

- ADF 统计 - 3） 最大管道运行持续时间

![突出显示了"工作簿（预览）"和"Azure 数据工厂分析"的窗口](media/data-factory-monitor-oms/monitor-oms-image6.png)

您可以可视化上述指标、查看这些指标后面的查询、编辑查询、创建警报以及执行其他操作。

![数据工厂运行的管道的图形表示"](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure 数据工厂分析（预览）将诊断日志发送到_特定于资源_的目标表。 您可以针对下表编写查询 _：ADFPipelineRun、ADF__触发器运行_和_ADFActivityRun_。

## <a name="alerts"></a>警报

登录到 Azure 门户并选择 **"监视** > **警报"** 以创建警报。

![门户菜单中的警报](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>创建警报

1. 选择“+ 创建新的预警规则”，创建新的警报****。

    ![新建警报规则](media/monitor-using-azure-monitor/alerts_image4.png)

1. 定义警报条件。

    > [!NOTE]
    > 请务必在“按资源类型筛选”下拉列表中选择“所有”。********

    ![“定义警报条件”>“选择目标”，此时会打开“选择资源”窗格 ](media/monitor-using-azure-monitor/alerts_image5.png)

    ![“定义警报条件”>“添加条件”，此时会打开“配置信号逻辑”窗格](media/monitor-using-azure-monitor/alerts_image6.png)

    ![“配置信号类型”窗格](media/monitor-using-azure-monitor/alerts_image7.png)

1. 定义警报详细信息。

    ![警报详细信息](media/monitor-using-azure-monitor/alerts_image8.png)

1. 定义操作组。

    ![创建规则的屏幕截图，其中突出显示了“新建操作组”](media/monitor-using-azure-monitor/alerts_image9.png)

    ![创建新的操作组](media/monitor-using-azure-monitor/alerts_image10.png)

    ![配置电子邮件、短信、推送和语音](media/monitor-using-azure-monitor/alerts_image11.png)

    ![删除操作组](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>后续步骤
[以编程方式监视和管理管道](monitor-programmatically.md)
