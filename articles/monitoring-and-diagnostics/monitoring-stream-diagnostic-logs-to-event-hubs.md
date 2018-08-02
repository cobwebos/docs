---
title: 将 Azure 诊断日志流式传输到事件中心
description: 了解如何将 Azure 诊断日志流式传输到事件中心。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.component: ''
ms.openlocfilehash: 5b4a15204a934bf55810fcdccd48a7a15a48c5ed
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258178"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>将 Azure 诊断日志流式传输到事件中心
可将 **[Azure 诊断日志](monitoring-overview-of-diagnostic-logs.md)** 近实时地流式传输到任何应用程序，方法是使用门户中的内置“导出到事件中心”选项，或者通过 Azure PowerShell Cmdlet 或 Azure CLI 2.0 在诊断设置中启用事件中心授权规则 ID。

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>可以对诊断日志和事件中心执行的操作
可以通过下述几种方式将流式传输功能用于诊断日志：

* **将日志流式传输到第三方日志记录和遥测系统** - 可以将所有诊断日志流式传输到单个事件中心，以便将日志数据通过管道传送到第三方 SIEM 或日志分析工具。
* **通过将“热路径”数据流式传输到 Power BI 查看服务运行状况** – 可以通过事件中心、流分析和 Power BI 在 Azure 服务中轻松将诊断数据转化成准实时分析结果。 [本文档很好地概述了如何设置事件中心、如何使用流分析处理数据，以及如何将 Power BI 用作输出](../stream-analytics/stream-analytics-power-bi-dashboard.md)。 下面是有关如何设置诊断日志的一些提示：

  * 在门户中选中相关选项或通过 PowerShell 启用相关选项以后，即可自动创建针对某类诊断日志的事件中心，因此需在命名空间中选择名称以 **insights-** 开头的事件中心。
  * 以下 SQL 代码是一个流分析查询示例，可用于将所有日志数据解析成 Power BI 表：

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **生成自定义遥测和日志记录平台** – 如果已经有一个自定义生成的遥测平台，或者正想生成一个，则可利用事件中心高度可缩放的发布-订阅功能，灵活地引入诊断日志。 [请参阅此处提供的 Dan Rosanova 的指南，了解如何在全局规模的遥测平台中使用事件中心](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)。

## <a name="enable-streaming-of-diagnostic-logs"></a>启用诊断日志的流式传输

可以通过门户或使用 [Azure Monitor REST API](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings) 以编程方式启用诊断日志的流式传输。 无论采用哪种方式，都可以创建一个诊断设置并在其中指定事件中心命名空间，以及要发送到该命名空间的日志类别和指标。 在该命名空间中针对每个启用的日志类别创建一个事件中心。 诊断**日志类别**是一类可供资源收集的日志。

> [!WARNING]
> 从计算资源（例如，VM 或 Service Fabric）启用诊断日志并对其进行流式传输[需要另一组步骤](../event-hubs/event-hubs-streaming-azure-diags-data.md)。

只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限并且这两个订阅都是属于同一个 AAD 租户，事件中心命名空间就不必与资源发出日志位于同一订阅中。

> [!NOTE]
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。
>
> 例如：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>使用门户流式传输诊断日志

1. 在门户中，导航到 Azure Monitor 并单击“诊断设置”

    ![Azure Monitor 的“监视”部分](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-blade.png)

2. （可选）按资源组或资源类型筛选列表，并单击要为其设置诊断设置的资源。

3. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“启用诊断”。

   ![添加诊断设置 - 没有现有的设置](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-none.png)

   如果资源上有现有的设置，则会看到已在此资源上配置的设置列表。 单击“添加诊断设置”。

   ![添加诊断设置 - 现有的设置](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-multiple.png)

3. 为设置命名，选中“流式传输到事件中心”对应的复选框，并选择一个事件中心命名空间。

   ![添加诊断设置 - 现有的设置](media/monitoring-stream-diagnostic-logs-to-event-hubs/diagnostic-settings-configure.png)

   选择的命名空间将是创建事件中心的地方（如果这是用户第一次流式传输诊断日志），或者是将诊断日志流式传输到事件中心的地方（如果已有资源将日志类别流式传输到该命名空间），而策略则定义流式传输机制所具有的权限。 目前，流式传输到事件中心需要“管理”、“发送”和“侦听”权限。 在门户中针对命名空间的“配置”选项卡下，可以创建或修改事件中心命名空间的共享访问策略。 若要更新这些诊断设置的其中一个，必须在事件中心授权规则中让客户端拥有 ListKey 权限。 还可以选择指定事件中心名称。 如果指定事件中心名称，日志将被路由到该事件中心，而不是为每个日志类别新创建的事件中心。

4. 单击“ **保存**”。

几分钟后，新设置会显示在此资源的设置列表中，只要生成新的事件数据，就会立即将诊断日志流式传输到该事件中心。

### <a name="via-powershell-cmdlets"></a>通过 PowerShell Cmdlet

若要通过 [Azure PowerShell Cmdlet](insights-powershell-samples.md) 启用流式传输，可以使用 `Set-AzureRmDiagnosticSetting` cmdlet 并设置以下参数：

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

事件中心授权规则 ID 是以下格式的字符串：`{Event Hub namespace resource ID}/authorizationrules/{key name}`，例如 `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`。 目前无法使用 PowerShell 选择特定事件中心名称。

### <a name="via-azure-cli-20"></a>通过 Azure CLI 2.0

若要通过 [Azure CLI 2.0](https://docs.microsoft.com/en-us/cli/azure/monitor?view=azure-cli-latest) 启用流式传输，可以使用 [az monitor diagnostic-settings create](https://docs.microsoft.com/en-us/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create) 命令。

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

可以通过将字典添加到以 `--logs` 参数传递的 JSON 数组来将其他类别添加到诊断日志。

`--event-hub-rule` 参数使用与事件中心授权规则 ID 相同的格式，如 PowerShell Cmdlet 的说明所述。

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>如何使用事件中心的日志数据？

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

| 元素名称 | Description |
| --- | --- |
| records |此有效负载中所有日志事件的数组。 |
| time |发生事件的时间。 |
| category |此事件的日志类别。 |
| resourceId |生成此事件的资源的资源 ID。 |
| operationName |操作的名称。 |
| 级别 |可选。 指示日志事件级别。 |
| 属性 |事件的属性。 |

可以在[此处](monitoring-overview-of-diagnostic-logs.md)查看支持流式传输到事件中心的所有资源提供程序的列表。

## <a name="stream-data-from-compute-resources"></a>对来自计算资源的数据进行流式处理

还可以使用 Windows Azure 诊断代理对来自计算资源的诊断日志进行流式处理。 [请参阅本文](../event-hubs/event-hubs-streaming-azure-diags-data.md)了解如何进行设置。

## <a name="next-steps"></a>后续步骤

* [使用 Azure Monitor 流式传输 Azure Active Directory 日志](../active-directory/reporting-azure-monitor-diagnostics-azure-event-hub.md)
* [详细了解 Azure 诊断日志](monitoring-overview-of-diagnostic-logs.md)
* [事件中心入门](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
