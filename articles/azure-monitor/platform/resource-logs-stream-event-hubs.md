---
title: 将 Azure 资源日志流式传输到事件中心
description: 了解如何将 Azure 资源日志流式传输到事件中心。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 1d7a533658b6c72caae9649d7e5a9c4fad117245
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262409"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>将 Azure 资源日志流式传输到 Azure 事件中心
Azure 中的[资源日志](resource-logs-overview.md)提供有关 Azure 资源内部操作的丰富、频繁的数据。 本文介绍如何将资源日志流式传输到事件中心，以便将数据发送到外部系统，例如第三方 Siem 和其他 log analytics 解决方案。


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>可对发送到事件中心的资源日志执行的操作
将 Azure 中的资源日志流式传输到事件中心，以提供以下功能：

* **将日志流式传输到第三方日志记录和遥测系统**-将所有资源日志流式传输到单个事件中心，以便将日志数据传递给第三方 SIEM 或 log analytics 工具。
* **构建自定义遥测和日志记录平台**–事件中心高度可缩放的发布-订阅特性使你可以灵活地将资源日志引入自定义 teletry 平台。 有关详细信息，请参阅[在 Azure 事件中心设计和调整全局缩放遥测平台](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

* **通过将数据流式传输到 Power BI 来查看服务运行状况**–使用事件中心、流分析和 Power BI 将诊断数据转换为 Azure 服务的近乎实时见解。 请[参阅流分析和 Power BI：用于流式处理数据](../../stream-analytics/stream-analytics-power-bi-dashboard.md)的实时分析仪表板，详细了解此解决方案。

    以下 SQL 代码是一个流分析查询示例，可用于将所有日志数据解析成 Power BI 表：
    
    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

## <a name="prerequisites"></a>先决条件
如果还没有[事件中心](../../event-hubs/event-hubs-create.md)，则需要创建一个。 如果以前将资源日志流式传输到此事件中心命名空间，则会重复使用该事件中心。

命名空间的共享访问策略定义流式传输机制所具有的权限。 流式传输到事件中心需要“管理”、“发送”和“侦听”权限。 可以在事件中心命名空间的 "配置" 选项卡下的 Azure 门户中创建或修改共享访问策略。

若要将诊断设置更新为包含流式处理，必须对该事件中心授权规则具有 ListKey 权限。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限并且这两个订阅都在同一个 AAD 租户中，事件中心命名空间就不必与发出日志的订阅位于同一订阅中。

## <a name="create-a-diagnostic-setting"></a>创建诊断设置
默认情况下，不收集资源日志。 通过创建 Azure 资源的诊断设置，将它们发送到事件中心和其他目标。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的日志和指标](diagnostic-settings.md)。

## <a name="stream-data-from-compute-resources"></a>从计算资源流式传输数据
本文中的过程适用于非计算资源，如[Azure 资源日志概述](diagnostic-settings.md)中所述。
使用 Windows Azure 诊断代理从 Azure 计算资源流式传输资源日志。 有关详细信息，请参阅[使用事件中心流式传输 Azure 诊断热路径中的数据](diagnostics-extension-stream-event-hubs.md)。


## <a name="consuming-log-data-from-event-hubs"></a>使用事件中心的日志数据
使用事件中心的资源日志时，它将为 JSON 格式，其中包含下表中的元素。

| 元素名称 | 描述 |
| --- | --- |
| records |此有效负载中所有日志事件的数组。 |
| time |发生事件的时间。 |
| category |此事件的日志类别。 |
| resourceId |生成此事件的资源的资源 ID。 |
| operationName |操作的名称。 |
| 级别 |可选。 指示日志事件级别。 |
| properties |事件的属性。 这对于每个 Azure 服务将有所不同，如[]()中所述。 |


下面是事件中心的输出数据示例：

```json
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
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
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
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
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
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



## <a name="next-steps"></a>后续步骤

* [用 Azure Monitor 流式传输 Azure Active Directory 日志](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)。
* [阅读有关 Azure 资源日志的详细信息](resource-logs-overview.md)。
* [事件中心入门](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。

