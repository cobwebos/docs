---
title: 使用 Azure Monitor 监视数据工厂 | Microsoft Docs
description: 了解如何使用 Azure Monitor 通过使用数据工厂中的信息启用诊断日志来监视/Azure 数据工厂管道。
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
ms.openlocfilehash: 2a707eda6a7e32a95666dd70e196c8da3c3b7834
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71815950"
---
# <a name="alert-and-monitor-data-factories-by-using-azure-monitor"></a>使用 Azure Monitor 警报和监视数据工厂

云应用程序很复杂，包含许多移动部件。 监视器提供的数据可帮助确保应用程序在正常运行时保持运行状态。 监视器还有助于避免出现潜在问题并排除过去的问题。

你可以使用监视数据来深入了解你的应用程序。 此知识有助于提高应用程序的性能和可维护性。 它还可帮助您自动执行操作，否则需要手动干预。

Azure Monitor 提供了大多数 Azure 服务的基本基础结构指标和日志。 Azure 诊断日志由资源发出，并提供有关该资源的操作的丰富、频繁的数据。 Azure 数据工厂在监视器中写入诊断日志。

有关详细信息，请参阅[Azure Monitor 概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)。

## <a name="keeping-azure-data-factory-data"></a>保留 Azure 数据工厂数据

数据工厂仅将管道运行的数据存储在45天内。 如果要将数据保留更长时间，请使用监视器。 借助 Monitor，可以路由诊断日志进行分析。 你还可以将它们保留在存储帐户中，以便获取所选持续时间的工厂信息。

## <a name="diagnostic-logs"></a>诊断日志

* 将诊断日志保存到存储帐户进行审核或手动检查。 您可以使用诊断设置来指定保留时间（天）。
* 将日志流式传输到 Azure 事件中心。 日志将成为合作伙伴服务或自定义分析解决方案（例如 Power BI）的输入。
* 用 Log Analytics 分析日志。

你可以使用不在发出日志的资源的订阅中的存储帐户或事件中心命名空间。 配置设置的用户必须对两个订阅都具有适当的基于角色的访问控制（RBAC）访问权限。

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

### <a name="diagnostic-settings"></a>诊断设置

使用诊断设置配置 noncompute 资源的诊断日志。 资源控件的设置具有以下功能：

* 它们指定发送诊断日志的位置。 例如，Azure 存储帐户、Azure 事件中心或监视日志。
* 它们指定发送的日志类别。
* 它们指定每个日志类别应在存储帐户中保留多长时间。
* 保留期为 0 天表示永久保留日志。 否则，该值可以是介于1到2147483647之间的任意天数。
* 如果设置了保留策略，但禁止将日志存储在存储帐户中，则保留策略无效。 例如，如果仅选择事件中心或监视日志选项，则可能发生此情况。
* 保留策略按天应用。 天之间的边界在协调世界时（UTC）午夜发生。 在一天结束时，将删除保留策略之外的日期的日志。 例如，如果您有一天的保留策略，则在今天之前的日志中，将删除昨天之前的日志。

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>通过 Azure Monitor REST API 启用诊断日志

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>在监视器中创建或更新诊断设置 REST API

##### <a name="request"></a>请求

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>标头

* 将 `{api-version}` 替换为 `2016-09-01`。
* 替换`{resource-id}`为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure Active Directory 获取的 JSON web 令牌（Azure AD）。 有关详细信息，请参阅[对请求进行身份验证](../active-directory/develop/authentication-scenarios.md)。

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

| 属性 | 类型 | 描述 |
| --- | --- | --- |
| **storageAccountId** |字符串 | 要将诊断日志发送到的存储帐户的资源 ID。 |
| **serviceBusRuleId** |字符串 | 要在其中为流式诊断日志创建事件中心的服务总线命名空间的服务总线规则 ID。 规则 ID 的格式`{service bus resource ID}/authorizationrules/{key name}`为。|
| **workspaceId** | 复杂类型 | 指标时间粒度及其保留策略的数组。 此属性的值为空。 |
|**指标**| 要传递到被调用管道的管道运行的参数值| 将参数名称映射到参数值的 JSON 对象。 |
| **logs**| 复杂类型| 资源类型的诊断日志类别的名称。 若要获取资源的诊断日志类别列表，请执行 "获取诊断设置" 操作。 |
| **category**| 字符串| 日志类别及其保留策略的数组。 |
| **timeGrain** | 字符串 | 以 ISO 8601 持续时间格式捕获的指标粒度。 属性值必须为`PT1M`，该值指定一分钟。 |
| **enabled**| 布尔 | 指定是否为此资源启用指标或日志类别的集合。 |
| **retentionPolicy**| 复杂类型| 描述指标或日志类别的保留策略。 此属性仅用于存储帐户。 |
|**天数**| Int| 保留指标或日志的天数。 如果该属性值为0，则将永久保留日志。 此属性仅用于存储帐户。 |

##### <a name="response"></a>响应

200正常。


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

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>获取有关监视器中诊断设置的信息 REST API

##### <a name="request"></a>请求

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>标头

* 将 `{api-version}` 替换为 `2016-09-01`。
* 替换`{resource-id}`为要编辑其诊断设置的资源的 ID。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/manage-resource-groups-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure AD 获取的 JSON web 令牌。 有关详细信息，请参阅[对请求进行身份验证](../active-directory/develop/authentication-scenarios.md)。

##### <a name="response"></a>响应

200正常。

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

#### <a name="activity-run-log-attributes"></a>活动-运行日志属性

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

| 属性 | 类型 | 描述 | 示例 |
| --- | --- | --- | --- |
| **级别** |字符串 | 诊断日志的级别。 对于 "活动-运行日志"，将属性值设置为4。 | `4` |
| **correlationId** |字符串 | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字符串 | 时间跨度 UTC 格式`YYYY-MM-DDTHH:MM:SS.00000Z`的事件时间。 | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| 字符串| 活动运行的 ID。 | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| 字符串| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| 字符串 | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| 字符串 | 诊断日志的类别。 将属性值设置为`ActivityRuns`。 | `ActivityRuns` |
|**level**| 字符串 | 诊断日志的级别。 将属性值设置为`Informational`。 | `Informational` |
|**operationName**| 字符串 | 活动的名称及其状态。 如果活动为启动检测信号，则属性值为`MyActivity -`。 如果活动是最终检测信号，则属性值为`MyActivity - Succeeded`。 | `MyActivity - Succeeded` |
|**pipelineName**| 字符串 | 管道的名称。 | `MyPipeline` |
|**activityName**| 字符串 | 活动的名称。 | `MyActivity` |
|**start**| 字符串 | 活动的开始时间以 timespan UTC 格式运行。 | `2017-06-26T20:55:29.5007959Z`|
|**端面**| 字符串 | 活动的结束时间以 timespan UTC 格式运行。 如果诊断日志显示某个活动已经开始但尚未结束，则属性值为`1601-01-01T00:00:00Z`。 | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>管道-运行日志属性

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

| 属性 | 类型 | 描述 | 示例 |
| --- | --- | --- | --- |
| **级别** |字符串 | 诊断日志的级别。 对于 "活动-运行日志"，将属性值设置为4。 | `4` |
| **correlationId** |字符串 | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字符串 | 时间跨度 UTC 格式`YYYY-MM-DDTHH:MM:SS.00000Z`的事件时间。 | `2017-06-28T21:00:27.3534352Z` |
|**runId**| 字符串| 管道运行的 ID。 | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| 字符串 | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| 字符串 | 诊断日志的类别。 将属性值设置为`PipelineRuns`。 | `PipelineRuns` |
|**level**| 字符串 | 诊断日志的级别。 将属性值设置为`Informational`。 | `Informational` |
|**operationName**| 字符串 | 管道的名称及其状态。 管道运行完成后，属性值为`Pipeline - Succeeded`。 | `MyPipeline - Succeeded`。 |
|**pipelineName**| 字符串 | 管道的名称。 | `MyPipeline` |
|**start**| 字符串 | 活动的开始时间以 timespan UTC 格式运行。 | `2017-06-26T20:55:29.5007959Z`。 |
|**端面**| 字符串 | 活动的结束时间以 timespan UTC 格式运行。 如果诊断日志显示某个活动已经开始但尚未结束，则属性值为`1601-01-01T00:00:00Z`。  | `2017-06-26T20:55:29.5007959Z` |
|**status**| 字符串 | 管道运行的最终状态。 可能的属性值`Succeeded`为`Failed`和。 | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>触发器-运行日志属性

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

| 属性 | 类型 | 描述 | 示例 |
| --- | --- | --- | --- |
| **级别** |字符串 | 诊断日志的级别。 对于 "活动-运行日志"，将属性值设置为4。 | `4` |
| **correlationId** |字符串 | 用于跟踪特定请求的唯一 ID。 | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | 字符串 | 时间跨度 UTC 格式`YYYY-MM-DDTHH:MM:SS.00000Z`的事件时间。 | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| 字符串| 触发器运行的 ID。 | `08587023010602533858661257311` |
|**resourceId**| 字符串 | 与数据工厂资源关联的 ID。 | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| 字符串 | 诊断日志的类别。 将属性值设置为`PipelineRuns`。 | `PipelineRuns` |
|**level**| 字符串 | 诊断日志的级别。 将属性值设置为`Informational`。 | `Informational` |
|**operationName**| 字符串 | 触发器的名称，其最终状态指示是否已成功激发触发器。 如果检测信号成功，则属性值为`MyTrigger - Succeeded`。 | `MyTrigger - Succeeded` |
|**triggerName**| 字符串 | 触发器的名称。 | `MyTrigger` |
|**triggerType**| 字符串 | 触发器的类型。 可能的属性值`Manual Trigger`为`Schedule Trigger`和。 | `ScheduleTrigger` |
|**triggerEvent**| 字符串 | 触发器的事件。 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| 字符串 | 以 timespan UTC 格式触发触发器的开始时间。 | `2017-06-26T20:55:29.5007959Z`|
|**status**| 字符串 | 显示触发器是否成功激发的最终状态。 可能的属性值`Succeeded`为`Failed`和。 | `Succeeded`|

### <a name="log-analytics-schema"></a>Log Analytics 架构

Log Analytics 从监视器中继承架构，但有以下例外：

* 每个列名中的第一个字母都大写。 例如，监视器中的列名 "correlationId" 为 Log Analytics 中的 "CorrelationId"。
* 没有 "级别" 列。
* 动态 "properties" 列保留为以下动态 JSON blob 类型。

    | Azure Monitor 列 | Log Analytics 列 | type |
    | --- | --- | --- |
    | $. 属性。UserProperties | UserProperties | 动态 |
    | $. 属性。批注 | 批注 | 动态 |
    | $. 属性。送 | 输入 | 动态 |
    | $. 属性。输出 | Output | 动态 |
    | $. 属性。错误。错误代码 | ErrorCode | int |
    | $. 属性。错误。消息 | ErrorMessage | string |
    | $. 属性。条 | Error | 动态 |
    | $. 属性。早期 | 早期 | 动态 |
    | $. 属性。Parameters | Parameters | 动态 |
    | $. 属性。SystemParameters | SystemParameters | 动态 |
    | $. 属性。Tags | Tags | 动态 |
    
## <a name="metrics"></a>指标

利用监视器，可以查看 Azure 工作负荷的性能和运行状况。 最重要的监视器数据类型是指标，也称为性能计数器。 指标由大多数 Azure 资源发出。 监视器提供多种方式来配置和使用这些指标，以便进行监视和故障排除。

Azure 数据工厂版本2发出以下度量值。

| **指标**           | **指标显示名称**         | **单位** | **聚合类型** | **说明**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRuns | 成功的管道运行数指标 | Count    | 总计                | 在一分钟时间范围内成功的管道运行总数。 |
| PipelineFailedRuns   | 失败的管道运行数指标    | Count    | 总计                | 分钟窗口中失败的管道运行总数。    |
| ActivitySucceededRuns | 成功的活动运行数指标 | Count    | 总计                | 在一分钟时间范围内成功的活动运行总数。  |
| ActivityFailedRuns   | 失败的活动运行数指标    | Count    | 总计                | 分钟窗口中失败的活动运行总数。     |
| TriggerSucceededRuns | 成功的触发器运行数指标  | Count    | 总计                | 在一分钟时间范围内成功的触发器运行总数。   |
| TriggerFailedRuns    | 失败的触发器运行数指标     | Count    | 总计                | 分钟窗口内失败的触发器运行总数。      |

若要访问指标, 请完成[Azure Monitor 数据平台](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics)中的说明。

## <a name="monitor-data-factory-metrics-with-azure-monitor"></a>利用 Azure Monitor 监视数据工厂指标

可以使用数据工厂与 Monitor 的集成将数据路由到监视器。 此集成在以下情况下非常有用：

* 你想要对由数据工厂发布的一组丰富的指标编写复杂的查询以进行监视。 可以通过监视器为这些查询创建自定义警报。

* 你希望跨数据工厂进行监视。 可以将数据从多个数据工厂路由到单个 "监视器" 工作区。

有关此功能的 7 分钟介绍和演示，请观看以下视频：

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

### <a name="configure-diagnostic-settings-and-workspace"></a>配置诊断设置和工作区

为数据工厂创建或添加诊断设置。

1. 在门户中，切换到 "监视"。 选择 "**设置** > " "**诊断设置**"。

1. 选择要为其设置诊断设置的数据工厂。

1. 如果所选数据工厂上不存在任何设置，系统将提示您创建设置。 选择“启用诊断”。

   ![如果不存在任何设置，请创建一个诊断设置](media/data-factory-monitor-oms/monitor-oms-image1.png)

   如果数据工厂上存在设置，你将看到已在数据工厂上配置的设置列表。 选择 "**添加诊断设置**"。

   ![添加诊断设置（如果有设置）](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. 为设置指定名称，选择 "**发送到 Log Analytics**"，然后从**Log Analytics 工作**区中选择一个工作区。

    ![命名设置并选择日志分析工作区](media/data-factory-monitor-oms/monitor-oms-image2.png)

1. 选择“保存”。

几分钟后，新设置将出现在此数据工厂的设置列表中。 一旦生成新的事件数据，就会将诊断日志流式传输到该工作区。 发出事件时，可能需要长达15分钟的时间，在 Log Analytics 中显示事件。

* 在_特定于资源_的模式下，从 Azure 数据工厂流到_ADFPipelineRun_、 _ADFTriggerRun_和_ADFActivityRun_表的诊断日志
* 在_Azure 诊断_模式下，诊断日志流入_AzureDiagnostics_表

> [!NOTE]
> 由于 Azure 日志表的列数不能超过500，因此强烈建议选择 "特定于资源的模式"。 有关详细信息, 请参阅[Log Analytics 已知限制](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-stream-log-store#known-limitation-column-limit-in-azurediagnostics)。

### <a name="install-azure-data-factory-analytics-from-azure-marketplace"></a>从 Azure 市场安装 Azure 数据工厂分析

![进入 "Azure Marketplace"，输入 "Analytics filter"，并选择 "Azure 数据工厂分析（预览版）"](media/data-factory-monitor-oms/monitor-oms-image3.png)

![有关 "Azure 数据工厂分析（预览版）" 的详细信息](media/data-factory-monitor-oms/monitor-oms-image4.png)

选择 "**创建**"，然后选择 " **oms 工作区**和**oms 工作区设置**"。

![创建新解决方案](media/data-factory-monitor-oms/monitor-oms-image5.png)

### <a name="monitor-data-factory-metrics"></a>监视数据工厂指标

安装 Azure 数据工厂分析会创建一组默认视图，以便启用以下度量值：

- ADF 运行-1）管道按数据工厂运行
 
- ADF 运行- 2) 数据工厂执行的活动运行

- ADF 运行-3）触发器运行（按数据工厂）

- ADF 错误-1）数据工厂前10个管道错误

- ADF 错误-2）由数据工厂运行的前10个活动

- ADF 错误-3）按数据工厂列出的十大触发器错误

- ADF 统计信息-1）活动按类型运行

- ADF 统计信息-2）触发器按类型运行

- ADF 统计信息-3）最大管道运行持续时间

![突出显示 "工作簿（预览）" 和 "AzureDataFactoryAnalytics" 的窗口](media/data-factory-monitor-oms/monitor-oms-image6.png)

您可以可视化前面的指标，查看这些指标后面的查询，编辑查询，创建警报，以及执行其他操作。

![管道按数据工厂运行的图形表示形式](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure 数据工厂分析（预览版）将诊断日志发送到_特定于资源的_目标表。 可以针对以下表编写查询：_ADFPipelineRun_、 _ADFTriggerRun_和_ADFActivityRun_。

## <a name="alerts"></a>警报

登录到 Azure 门户，然后选择 "**监视** > **警报**" 以创建警报。

![门户菜单中的警报](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>创建警报

1. 选择“+ 创建新的预警规则”，创建新的警报。

    ![新建预警规则](media/monitor-using-azure-monitor/alerts_image4.png)

1. 定义警报条件。

    > [!NOTE]
    > 请确保在 "**按资源类型筛选**" 下拉列表中选择 "**全部**"。

    !["定义警报条件" > "选择目标"，这将打开 "选择资源" 窗格 ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["定义警报条件" > "添加条件"，这会打开 "配置信号逻辑" 窗格](media/monitor-using-azure-monitor/alerts_image6.png)

    !["配置信号类型" 窗格](media/monitor-using-azure-monitor/alerts_image7.png)

1. 定义警报详细信息。

    ![警报详细信息](media/monitor-using-azure-monitor/alerts_image8.png)

1. 定义操作组。

    ![创建一个规则，其中突出显示了 "新操作组"](media/monitor-using-azure-monitor/alerts_image9.png)

    ![创建新的操作组](media/monitor-using-azure-monitor/alerts_image10.png)

    ![配置电子邮件、短信、推送和语音](media/monitor-using-azure-monitor/alerts_image11.png)

    ![定义操作组](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>后续步骤
[以编程方式监视和管理管道](monitor-programmatically.md)
