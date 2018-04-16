---
title: 将 Azure 诊断日志流式传输到 Log Analytics | Microsoft Docs
description: 了解如何将 Azure 诊断日志流式传输到 Log Analytics 工作区。
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem
ms.openlocfilehash: 82011126375a3c5016e110aac9ce6bc1b2d59cdf
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>将 Azure 诊断日志流式传输到 Log Analytics

可以使用门户、PowerShell cmdlet 或 Azure CLI 2.0 将 [Azure 诊断日志](monitoring-overview-of-diagnostic-logs.md)近实时地流式传输到 Azure Log Analytics。

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>可以对 Log Analytics 中的诊断日志执行的操作

Azure Log Analytics 是一种灵活的日志搜索和分析工具，利用该工具，可以深入了解从 Azure 资源生成的原始日志数据。 其中一些功能包括：

* **日志搜索** - 对日志数据编写高级查询、关联来自各种源的日志，甚至生成可以固定到 Azure 仪表板的图表。
* **警报** - 当检测到一个或多个事件与特定查询匹配时，通过电子邮件或 webhook 调用进行通知。
* **解决方案** - 使用预生成的视图和仪表板，立即了解日志数据。
* **高级分析** - 应用机器学习和模式匹配算法，确定日志揭示的可能问题。

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>启用诊断日志到 Log Analytics 的流式传输

可以通过门户或使用 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/servicediagnosticsettings) 以编程方式启用诊断日志的流式传输。 无论采用哪种方式，都可以创建一个诊断设置并在其中指定 Log Analytics 工作区，以及要发送到该工作区的日志类别和指标。 诊断日志类别是一类可由资源提供的日志。

只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，Log Analytics 工作区就不必与发出日志的资源位于同一订阅中。

> [!NOTE]
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。
>
> *例如*：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>使用门户流式传输诊断日志
1. 在门户中，导航到 Azure Monitor 并单击“诊断设置”

    ![Azure Monitor 的“监视”部分](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-blade.png)

2. （可选）按资源组或资源类型筛选列表，并单击要为其设置诊断设置的资源。

3. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“启用诊断”。

   ![添加诊断设置 - 没有现有的设置](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-none.png)

   如果资源上有现有的设置，则会看到已在此资源上配置的设置列表。 单击“添加诊断设置”。

   ![添加诊断设置 - 现有的设置](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-multiple.png)

3. 为设置提供名称，并选中“发送到 Log Analytics”框，然后选择 Log Analytics 工作区。

   ![添加诊断设置 - 现有的设置](media/monitoring-stream-diagnostic-logs-to-log-analytics/diagnostic-settings-configure.png)

4. 单击“ **保存**”。

几分钟后，新设置会显示在此资源的设置列表中，只要生成新的事件数据，就会立即将诊断日志流式传输到该工作区。 请注意，发出事件后可能需要最多 15 分钟的时间该事件才会出现在 Log Analytics 中。

### <a name="via-powershell-cmdlets"></a>通过 PowerShell Cmdlet
若要通过 [Azure PowerShell Cmdlet](insights-powershell-samples.md) 启用流式传输，可以使用 `Set-AzureRmDiagnosticSetting` cmdlet 并设置以下参数：

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

请注意，workspaceID 属性采用工作区的完整 Azure 资源 ID，而不是 Log Analytics 门户中显示的工作区 ID/密钥。

### <a name="via-azure-cli-20"></a>通过 Azure CLI 2.0

若要通过 [Azure CLI 2.0](insights-cli-samples.md) 启用流式传输，可以使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) 命令。

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

可以通过将字典添加到以 `--logs` 参数传递的 JSON 数组来将其他类别添加到诊断日志。

仅当 `--workspace` 不是对象 ID 时，才需要 `--resource-group` 参数。

## <a name="how-do-i-query-the-data-in-log-analytics"></a>如何在 Log Analytics 中查询数据？

在门户的“日志搜索”边栏选项卡中或作为 Log Analytics 的一部分的高级分析体验中，可以查询诊断日志作为 AzureDiagnostics 表下日志管理解决方案的一部分。 此外，还可以安装 [Azure 资源的多个解决方案](../log-analytics/log-analytics-add-solutions.md)，以立即深入了解发送到 Log Analytics 的日志数据。

## <a name="next-steps"></a>后续步骤

* [详细了解 Azure 诊断日志](monitoring-overview-of-diagnostic-logs.md)
