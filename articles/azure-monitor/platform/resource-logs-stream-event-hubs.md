---
title: 将 Azure 资源日志流式传输到事件中心
description: 了解如何将 Azure 资源日志流式传输到事件中心，以便将数据发送到外部系统，例如第三方 Siem 和其他 log analytics 解决方案。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 680570c5102f656b2b2d2e05f9e08f51fe892f44
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74304950"
---
# <a name="stream-azure-resource-logs-to-azure-event-hubs"></a>将 Azure 资源日志流式传输到 Azure 事件中心
Azure 中的[资源日志](resource-logs-overview.md)提供有关 Azure 资源内部操作的丰富、频繁的数据。 本文介绍如何将资源日志流式传输到事件中心，以便将数据发送到外部系统，例如第三方 SIEM 和其他日志分析解决方案。


## <a name="what-you-can-do-with-resource-logs-sent-to-an-event-hub"></a>如何处理发送到事件中心的资源日志
将 Azure 中的资源日志流式传输到事件中心可提供以下功能：

* **将日志流式传输到第三方日志记录和遥测系统** - 将所有资源日志流式传输到单个事件中心，以便将日志数据通过管道传送到第三方 SIEM 或日志分析工具。
* **生成自定义遥测和日志记录平台** - 可利用事件中心高度可缩放的发布-订阅功能，灵活地将资源日志引入自定义 teletry 平台。 有关详细信息，请参阅 [Designing and Sizing a Global Scale Telemetry Platform on Azure Event Hubs](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)（在 Azure 事件中心设计全球规模的遥测平台并设置其大小）。

* **通过将数据流式传输到 Power BI 查看服务运行状况** - 通过事件中心、流分析和 Power BI 在 Azure 服务中将诊断数据转化成准实时见解。 有关此解决方案的详细信息，请参阅[流分析和 Power BI：用于流式处理数据的实时分析仪表板](../../stream-analytics/stream-analytics-power-bi-dashboard.md)。

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
需[创建事件中心](../../event-hubs/event-hubs-create.md)（如果还没有）。 如果先前已将资源日志流式传输到此事件中心命名空间，则会重用该事件中心。

命名空间的共享访问策略定义流式处理机制具有的权限。 流式传输到事件中心需要“管理”、“发送”和“侦听”权限。 在 Azure 门户中事件中心命名空间的“配置”选项卡下，可以创建或修改共享访问策略。

若要更新诊断设置，使之包括流式传输，则必须在事件中心授权规则中拥有 ListKey 权限。 只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限并且这两个订阅都在同一个 AAD 租户中，事件中心命名空间就不必与发出日志的订阅位于同一订阅中。

## <a name="create-a-diagnostic-setting"></a>创建诊断设置
默认不会收集资源日志。 需要通过创建 Azure 资源的诊断设置，将它们发送到事件中心和其他目标。 有关详细信息，请参阅[创建诊断设置以收集 Azure 中的日志和指标](diagnostic-settings.md)。

## <a name="stream-data-from-compute-resources"></a>对来自计算资源的数据进行流式处理
本文中的过程适用于非计算资源，详见 [Azure 资源日志概述](diagnostic-settings.md)。
使用 Windows Azure 诊断代理对来自 Azure 计算资源的资源日志进行流式处理。 有关详细信息，请参阅[使用事件中心流式处理热路径中的 Azure 诊断数据](diagnostics-extension-stream-event-hubs.md)。


## <a name="consuming-log-data-from-event-hubs"></a>使用事件中心的日志数据
使用事件中心的资源日志时，这些日志为 JSON 格式，其中包含下表中的元素。

| 元素名称 | 说明 |
| --- | --- |
| records |此有效负载中所有日志事件的数组。 |
| time |发生事件的时间。 |
| category |此事件的日志类别。 |
| resourceId |生成此事件的资源的资源 ID。 |
| operationName |操作的名称。 |
| 级别 |可选。 指示日志事件级别。 |
| properties |事件的属性。 这些属性因 Azure 服务而异，详见 []()。 |


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
* [详细了解 Azure 资源日志](resource-logs-overview.md)。
* [事件中心入门](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)。

