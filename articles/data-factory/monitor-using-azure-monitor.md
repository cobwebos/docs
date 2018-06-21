---
title: 使用 Azure Monitor 监视数据工厂 | Microsoft Docs
description: 了解如何在 Azure Monitor 中，使用 Azure 数据工厂中的信息通过启用诊断日志来监视数据工厂管道。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: shlo
ms.openlocfilehash: 234dacca152dca6e8e212a86f3921c9355f640e4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620334"
---
# <a name="monitor-data-factories-using-azure-monitor"></a>使用 Azure Monitor 监视数据工厂  
云应用程序很复杂，包含很多移动部件。 监视可以为用户提供数据，确保应用程序始终处于健康运行状态。 监视还有助于避免潜在问题，或者解决过去的问题。 此外，还可以利用监视数据深入了解应用程序的情况。 了解这些情况有助于改进应用程序的性能或可维护性，或者实现本来需要手动干预的操作的自动化。

Azure Monitor 针对 Microsoft Azure 中的大多数服务提供基本级别的基础结构指标和日志。 有关详细信息，请参阅[监视概述](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor)。 Azure 诊断日志是资源发出的日志，记录与该资源的操作相关的各种频繁生成的数据。 数据工厂在 Azure Monitor 中输出诊断日志。 

> [!NOTE]
> 本文适用于目前处于预览版的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[监视和管理数据工厂版本 1 中的管道](v1/data-factory-monitor-manage-pipelines.md)。

## <a name="diagnostic-logs"></a>诊断日志

* 将诊断日志保存到**存储帐户**进行审核或手动检查。 可以使用诊断设置指定保留时间（天）。
* 将诊断日志流式传输到**事件中心**，方便第三方服务或自定义分析解决方案（例如 PowerBI）引入。
* 使用 **Log Analytics** 对诊断日志进行分析

可以使用与发出日志的资源不同的订阅中的存储帐户或事件中心命名空间。 配置此设置的用户必须对两个订阅都具有适当的基于角色的访问控制 (RBAC) 访问权限。

## <a name="set-up-diagnostic-logs"></a>设置诊断日志

### <a name="diagnostic-settings"></a>诊断设置
可以使用诊断设置配置非计算资源的诊断日志。 用于资源控制的诊断设置：

* 将诊断日志发送到何处：存储帐户、事件中心和/或 Log Analytics。
* 发送哪些日志类别。
* 应该将每个日志类别在存储帐户中保留多长时间
* 保留期为零天表示日志将永久保留。 如果不需永久保留，则可将该值设置为 1 到 2147483647 之间的任意天数。
* 如果设置了保留策略，但禁止将日志存储在存储帐户中（例如，仅选择了“事件中心”或“Log Analytics”选项），则保留策略无效。
* 保留策略按天应用，因此在一天结束时 (UTC)，会删除当天已超过保留策略期限的日志。 例如，假设保留策略的期限为一天，则在今天开始时，会删除前天的日志。

### <a name="enable-diagnostic-logs-via-rest-apis"></a>通过 REST API 启用诊断日志

在 Azure Monitor REST API 中创建或更新诊断设置

**请求**
```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**标头**
* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的资源 ID。 有关详细信息，请参阅[使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-portal.md)。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure Active Directory 获取的 JSON Web 令牌。 有关详细信息，请参阅[对请求进行身份验证](../active-directory/develop/active-directory-authentication-scenarios.md)。

**正文**
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

| 属性 | Type | 说明 |
| --- | --- | --- |
| storageAccountId |String | 要将诊断日志发送到的存储帐户的资源 ID |
| serviceBusRuleId |String | 要在其中创建事件中心，以便流式传输诊断日志的服务总线命名空间的服务总线规则 ID。 规则 ID 的格式为：“{服务总线资源 ID}/authorizationrules/{密钥名称}”。|
| workspaceId | 复杂类型 | 指标时间粒度及其保留策略的数组。 目前，此属性为空。 |
|指标| 要传递到被调用管道的管道运行的参数值| 用于将参数名称映射到参数值的 JSON 对象 | 
| 日志| 复杂类型| 某个资源类型的诊断日志类别的名称。 若要获取资源的诊断日志类别列表，请先执行 GET 诊断设置操作。 |
| category| String| 日志类别及其保留策略的数组 |
| timeGrain | String | 以 ISO 8601 持续时间格式捕获的指标的粒度。 必须为 PT1M（1 分钟）|
| 已启用| 布尔 | 指定是否为此资源启用了该指标或日志类别的收集|
| retentionPolicy| 复杂类型| 描述指标或日志类别的保留策略。 仅用于存储帐户选项。|
| days| int| 指标或日志的保留天数。 如果值为 0，则无限期保留日志。 仅用于存储帐户选项。 |

**响应**

200 正常


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

在 Azure Monitor REST API 中获取有关诊断设置的信息

**请求**
```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

**标头**
* 将 `{api-version}` 替换为 `2016-09-01`。
* 将 `{resource-id}` 替换为要编辑其诊断设置的资源的资源 ID。 有关详细信息，请参阅“使用资源组管理 Azure 资源”。
* 将 `Content-Type` 标头设置为 `application/json`。
* 将授权标头设置为从 Azure Active Directory 获取的 JSON Web 令牌。 有关详细信息，请参阅“对请求进行身份验证”。

**响应**

200 正常

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
[在此处了解详细信息](https://msdn.microsoft.com/library/azure/dn931932.aspx)

## <a name="schema-of-logs--events"></a>日志和事件的架构

### <a name="activity-run-logs-attributes"></a>活动运行日志属性

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
   "properties:" 
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

| 属性 | Type | 说明 | 示例 |
| --- | --- | --- | --- |
| 级别 |String | 诊断日志的级别。 活动运行日志始终为级别 4。 | `4`  |
| correlationId |String | 用于跟踪特定端到端请求的唯一 ID | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | 事件的时间，以时间跨度表示，采用 UTC 格式 | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|activityRunId| String| 活动运行的 ID | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|pipelineRunId| String| 管道运行的 ID | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | 数据工厂资源的关联资源 ID | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | 诊断日志的类别。 请将此属性设置为“ActivityRuns”。 | `ActivityRuns` |
|级别| String | 诊断日志的级别。 请将此属性设置为“Informational” | `Informational` |
|operationName| String |活动的名称及其状态。 如果状态为启动检测信号，则返回 `MyActivity -`。 如果状态为结束检测信号，则返回 `MyActivity - Succeeded` 和最终状态 | `MyActivity - Succeeded` |
|pipelineName| String | 管道的名称 | `MyPipeline` |
|activityName| String | 活动名称 | `MyActivity` |
|start| String | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式 | `2017-06-26T20:55:29.5007959Z`|
|end| String | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果活动尚未结束（正在启动的活动的诊断日志），则设置默认值 `1601-01-01T00:00:00Z`。  | `2017-06-26T20:55:29.5007959Z` |


### <a name="pipeline-run-logs-attributes"></a>管道运行日志属性

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

| 属性 | Type | 说明 | 示例 |
| --- | --- | --- | --- |
| 级别 |String | 诊断日志的级别。 活动运行日志为级别 4。 | `4`  |
| correlationId |String | 用于跟踪特定端到端请求的唯一 ID | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | 事件的时间，以时间跨度表示，采用 UTC 格式 | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|runId| String| 管道运行的 ID | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|resourceId| String | 数据工厂资源的关联资源 ID | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | 诊断日志的类别。 请将此属性设置为“PipelineRuns” | `PipelineRuns` |
|级别| String | 诊断日志的级别。 请将此属性设置为“Informational” | `Informational` |
|operationName| String |管道的名称及其状态。 如果管道运行已完成，则返回“Pipeline - Succeeded”和最终状态| `MyPipeline - Succeeded` |
|pipelineName| String | 管道的名称 | `MyPipeline` |
|start| String | 活动运行的开始时间，以时间跨度表示，采用 UTC 格式 | `2017-06-26T20:55:29.5007959Z`|
|end| String | 活动运行的结束时间，以时间跨度表示，采用 UTC 格式。 如果活动尚未结束（正在启动的活动的诊断日志），则设置默认值 `1601-01-01T00:00:00Z`。  | `2017-06-26T20:55:29.5007959Z` |
|status| String | 管道运行的最终状态（Succeeded 或 Failed） | `Succeeded`|


### <a name="trigger-run-logs-attributes"></a>触发器运行日志属性

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

| 属性 | Type | 说明 | 示例 |
| --- | --- | --- | --- |
| 级别 |String | 诊断日志的级别。 对于活动运行日志，请设置为级别 4。 | `4`  |
| correlationId |String | 用于跟踪特定端到端请求的唯一 ID | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| time | String | 事件的时间，以时间跨度表示，采用 UTC 格式 | `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
|triggerId| String| 触发器运行的 ID | `08587023010602533858661257311` |
|resourceId| String | 数据工厂资源的关联资源 ID | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|category| String | 诊断日志的类别。 请将此属性设置为“PipelineRuns” | `PipelineRuns` |
|级别| String | 诊断日志的级别。 请将此属性设置为“Informational” | `Informational` |
|operationName| String |触发器的名称，以及指示是否已成功激发的最终状态。 如果检测信号成功，则返回“MyTrigger - Succeeded”| `MyTrigger - Succeeded` |
|triggerName| String | 触发器的名称 | `MyTrigger` |
|triggerType| String | 触发器的类型（手动触发器或计划的触发器） | `ScheduleTrigger` |
|triggerEvent| String | 触发器的事件 | `ScheduleTime - 2017-07-06T01:50:25Z` |
|start| String | 激发触发器的开始时间，以时间跨度表示，采用 UTC 格式 | `2017-06-26T20:55:29.5007959Z`|
|status| String | 指示是否已成功激发触发器的最终状态（Succeeded 或 Failed） | `Succeeded`|

## <a name="metrics"></a>度量值

在 Azure 监视器中可以使用遥测来查看 Azure 上的工作负荷的性能与运行状况。 最重要的 Azure 遥测数据类型是大多数 Azure 资源发出的指标（也称为性能计数器）。 Azure 监视器提供多种方式来配置和使用这些指标，以便进行监视与故障排除。

ADFV2 发出以下指标

| **指标**           | **指标显示名称**         | **单位** | **聚合类型** | **说明**                                       |
|----------------------|---------------------------------|----------|----------------------|-------------------------------------------------------|
| PipelineSucceededRun | 成功的管道运行数指标 | Count    | 总计                | 在一分钟时段内成功的管道运行总数 |
| PipelineFailedRuns   | 失败的管道运行数指标    | Count    | 总计                | 在一分钟时段内失败的管道运行总数    |
| ActiviySucceededRuns | 成功的活动运行数指标 | Count    | 总计                | 在一分钟时段内成功的活动运行总数  |
| ActivityFailedRuns   | 失败的活动运行数指标    | Count    | 总计                | 在一分钟时段内失败的活动运行总数     |
| TriggerSucceededRuns | 成功的触发器运行数指标  | Count    | 总计                | 在一分钟时段内成功的触发器运行总数   |
| TriggerFailedRuns    | 失败的触发器运行数指标     | Count    | 总计                | 在一分钟时段内失败的触发器运行总数      |

若要访问指标，请按照 https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics 一文中的说明进行操作 

## <a name="alerts"></a>警报

可在数据工厂中发出有关受支持指标的警报。 单击数据工厂“监视器”页上的“警报”按钮。

![“警报”选项](media/monitor-using-azure-monitor/alerts_image1.png)

这将进入“警报”页。

![“警报”页](media/monitor-using-azure-monitor/alerts_image2.png)

还可以登录到 Azure 门户并单击“监视”-&gt;“警报”，直接访问“警报”页。

![门户菜单中的警报](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>创建警报

1.  单击“+ 新建警报规则”，创建新的警报。

    ![新建警报规则](media/monitor-using-azure-monitor/alerts_image4.png)

2.  定义警报条件。

    > [!NOTE]
    > 请务必在“按资源类型筛选”中选择“所有”。

    ![警报条件，第 1 屏，共 3 屏](media/monitor-using-azure-monitor/alerts_image5.png)

    ![警报条件，第 2 屏，共 3 屏](media/monitor-using-azure-monitor/alerts_image6.png)

    ![警报条件，第 3 屏，共 3 屏](media/monitor-using-azure-monitor/alerts_image7.png)

3.  定义警报详细信息。

    ![警报详细信息](media/monitor-using-azure-monitor/alerts_image8.png)

4.  定义操作组。

    ![操作组，第 1 屏，共 4 屏](media/monitor-using-azure-monitor/alerts_image9.png)

    ![操作组，第 2 屏，共 4 屏](media/monitor-using-azure-monitor/alerts_image10.png)

    ![操作组，第 3 屏，共 4 屏](media/monitor-using-azure-monitor/alerts_image11.png)

    ![操作组，第 4 屏，共 4 屏](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="next-steps"></a>后续步骤
参阅[以编程方式监视和管理管道](monitor-programmatically.md)一文，了解如何通过运行  来监视和管理管道。 
