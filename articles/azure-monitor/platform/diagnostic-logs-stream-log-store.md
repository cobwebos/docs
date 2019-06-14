---
title: 将 Azure 诊断日志流式传输到 Azure Monitor 中的 Log Analytics 工作区
description: 了解如何将 Azure 诊断日志流式传输到 Azure Monitor 中的 Log Analytics 工作区。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: b17978da3195b364f868d33ab7ad9faa1544e9ec
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60238022"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>将 Azure 诊断日志流式传输到 Azure Monitor 中的 Log Analytics 工作区

可以使用门户、PowerShell cmdlet 或 Azure CLI 将 **[Azure 诊断日志](diagnostic-logs-overview.md)** 近实时地流式传输到 Azure Monitor 中的 Log Analytics 工作区。

## <a name="what-you-can-do-with-diagnostics-logs-in-a-log-analytics-workspace"></a>可对 Log Analytics 工作区中的诊断日志执行的操作

Azure Monitor 提供灵活的日志查询和分析工具让你深入了解从 Azure 资源生成的原始日志数据。 其中一些功能包括：

* **日志查询** - 对日志数据编写高级查询、关联来自各种源的日志，并生成可以固定到 Azure 仪表板的图表。
* **警报** - 当检测到一个或多个事件与特定查询匹配时，使用 Azure Monitor 警报通过电子邮件或 Webhook 调用接收通知。
* **高级分析** - 应用机器学习和模式匹配算法，确定日志揭示的可能问题。

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics-workspace"></a>启用向 Log Analytics 工作区流式传输诊断日志

可以通过门户或使用 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) 以编程方式启用诊断日志的流式处理。 无论采用哪种方式，都可以创建一个诊断设置并在其中指定 Log Analytics 工作区，以及要发送到该工作区的日志类别和指标。 诊断日志类别  是一类可由资源提供的日志。

只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，Log Analytics 工作区就不必位于发出日志的资源所在的订阅中。

> [!NOTE]
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。
>
>  例如：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>使用门户流式传输诊断日志
1. 在门户中，导航到“Azure Monitor”并单击“设置”菜单中的“诊断设置”   。


2. （可选）按资源组或资源类型筛选列表，并单击要为其设置诊断设置的资源。

3. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“启用诊断”。

   ![添加诊断设置 - 没有现有的设置](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   如果资源上有现有的设置，则会看到已在此资源上配置的设置列表。 单击“添加诊断设置”。

   ![添加诊断设置 - 现有的设置](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. 为设置提供名称，并选中“发送到 Log Analytics”  框，然后选择 Log Analytics 工作区。

   ![添加诊断设置 - 现有的设置](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. 单击“ **保存**”。

几分钟后，新设置会显示在此资源的设置列表中，只要生成新的事件数据，就会立即将诊断日志流式传输到该工作区。 请注意，发出事件后可能需要最多 15 分钟的时间该事件才会出现在 Log Analytics 中。

### <a name="via-powershell-cmdlets"></a>通过 PowerShell Cmdlet

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

若要通过 [Azure PowerShell Cmdlet](../../azure-monitor/platform/powershell-quickstart-samples.md) 启用流式传输，可以使用 `Set-AzDiagnosticSetting` cmdlet 并设置以下参数：

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

请注意，workspaceID 属性采用工作区的完整 Azure 资源 ID，而不是 Log Analytics 门户中显示的工作区 ID/密钥。

### <a name="via-azure-cli"></a>通过 Azure CLI

若要通过 [Azure CLI](../../azure-monitor/platform/cli-samples.md) 启用流式传输，可以使用 [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) 命令。

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

## <a name="how-do-i-query-the-data-from-a-log-analytics-workspace"></a>如何查询 Log Analytics 工作区中的数据？

在 Azure Monitor 门户上的“日志”边栏选项卡中，可以在“AzureDiagnostics”表下面查询作为日志管理解决方案的一部分提供的诊断日志。 此外，还可以安装[适用于 Azure 资源的多个监视解决方案](../../azure-monitor/insights/solutions.md)，以立即深入了解发送到 Azure Monitor 的日志数据。

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>已知的限制：AzureDiagnostics 中的列限制
由于许多资源将所有数据类型发送到同一个表 (_AzureDiagnostics_)，因此，此表的架构是所要收集的所有不同数据类型的架构的超集。 例如，如果你创建了以下诊断设置用于收集以下数据类型，而所有这些数据类型都会发送到同一个工作区：
- 资源 1 的审核日志（架构由列 A、B 和 C 组成）  
- 资源 2 的错误日志（架构由列 D、E 和 F 组成）  
- 资源 3 的数据流日志（架构由列 G、H 和 I 组成）  
 
则包含示例数据的 AzureDiagnostics 表的外观如下所示：  
 
| ResourceProvider | 类别 | A | B | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
| Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
| ... |
 
包含的列数不超过 500 个的任意给定 Azure 日志表具有明确的限制。 一旦达到该限制，在引入时，包含不属于前 500 个列的数据的行将被删除。 AzureDiagnostics 表特别容易受到此限制的影响。 原因往往是需要将多种不同的数据源发送到同一个工作区，或者要将非常详细的数据源发送到同一个工作区。 
 
#### <a name="azure-data-factory"></a>Azure 数据工厂  
众所周知，Azure 数据工厂就是一个特别容易受此限制影响的资源，因为它是一个非常详细的日志集。 具体而言：  
- *针对管道中的任一活动定义的用户参数*：将会针对任一活动，为每个唯一命名的用户参数创建一个新列。 
- *活动输入和输出*：这些活动各不相同且非常详细，因此会生成大量的列。 
 
结合下面所述的概括性解决方法建议，我们建议将 ADF 日志隔离到其自身的工作区，以尽量减少这些日志影响工作区中收集的其他日志类型的可能性。 我们预期 Azure Data Factory 的管理日志不久就可以使用。
 
#### <a name="workarounds"></a>解决方法
从短期来看，在重新定义 500 个列的限制之前，我们建议将详细数据类型隔离到独立的工作区，以减少达到该限制的可能性。
 
从长远来看，Azure 诊断将从统一的稀疏架构过渡到为每种数据类型提供单独的表的方案；配合对动态类型的支持，这可以大大提高通过 Azure 诊断机制传入 Azure 日志的数据的可用性。 传输某些 Azure 资源类型时已经体现了这种优势，例如 [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) 或 [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) 日志。 请在 [Azure 更新](https://azure.microsoft.com/updates/)博客中查看有关 Azure 中支持这些重新编排的日志的新资源类型的消息！


## <a name="next-steps"></a>后续步骤

* [详细了解 Azure 诊断日志](diagnostic-logs-overview.md)

