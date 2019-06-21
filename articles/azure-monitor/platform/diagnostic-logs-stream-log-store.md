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
ms.openlocfilehash: 13eb1a8fcea2f74cda5921a51b8c2e8816be975f
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303717"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics-workspace-in-azure-monitor"></a>将 Azure 诊断日志流式传输到 Azure Monitor 中的 Log Analytics 工作区

**[Azure 诊断日志](diagnostic-logs-overview.md)** 可以以到 Log Analytics 工作区，使用门户、 PowerShell cmdlet 或 Azure CLI 的 Azure Monitor 中的近实时方式流式传输。

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

几分钟后，新设置会显示在此资源的设置列表中，只要生成新的事件数据，就会立即将诊断日志流式传输到该工作区。 可能最多 15 分钟之间时，将发出事件和 Log Analytics 中的出现时。

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

## <a name="azure-diagnostics-vs-resource-specific"></a>Azure 诊断与特定于资源的  
Azure 诊断配置中启用 Log Analytics 目标后，有数据会显示在工作区中的两种不同方法：  
- **Azure 诊断**-这是大多数 Azure 服务目前使用的旧方法。 在此模式下，所有指向给定工作区的任何诊断设置中的数据将会出现_AzureDiagnostics_表。 
<br><br>因为许多资源将数据发送到同一个表 (_AzureDiagnostics_)，此表的架构是超级一组的收集的所有不同的数据类型的架构。 例如，如果找不到以下数据类型创建了诊断设置，所有正在发送到同一个工作区：
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

- **特定于资源的**-在此模式下，在所选工作区中的各个表创建每个诊断设置配置中选择每个类别。 此较新的方法可以更轻松地找到完全你想要通过显式分离关注点查找： 每个类别的表。 此外，它为动态类型提供其支持的好处。 已可以看到此模式下进行，请选择 Azure 资源类型，例如[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)或[Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor)日志。 从根本上讲，我们预计每种数据类型迁移到特定于资源的模式。 

    在上面的示例中，这将导致正在创建的三个表： 
    - 表_AuditLogs_ ，如下所示：

        | ResourceProvider | 类别 | A | B | C |
        | -- | -- | -- | -- | -- |
        | Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
        | Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
        | ... |

    - 表_ErrorLogs_ ，如下所示：  

        | ResourceProvider | 类别 | D | E | F |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource2 | ErrorLogs | q1 | w1 | e1 |
        | Microsoft.Resource2 | ErrorLogs | q2 | w2 | e2 |
        | ... |

    - 表_DataFlowLogs_ ，如下所示：  

        | ResourceProvider | 类别 | G | H | I |
        | -- | -- | -- | -- | -- | 
        | Microsoft.Resource3 | DataFlowLogs | j1 | k1 | l1|
        | Microsoft.Resource3 | DataFlowLogs | j3 | k3 | l3|
        | ... |

    使用特定于资源的模式的其他优势包括跨引入延迟和查询时间，更好的架构和其结构，其授予针对特定表和的详细信息的 RBAC 权限的能力的可发现性改进的性能。

### <a name="selecting-azure-diagnostic-vs-resource-specific-mode"></a>选择 Azure 诊断与特定于资源的模式
对于大多数 Azure 资源，你将不能选择是否要使用 Azure 诊断或特定于资源的模式;数据会自动将流经该资源已选择要使用的方法。 请参阅已启用的应用模式的详细信息将数据发送到 Log Analytics 的资源提供的文档。 

如前一部分中所述，最终的目标是 Azure monitor 中使用 Azure 的特定于资源的模式将所有服务。 若要简化这一转换，并确保数据不会丢失其加入到 Log Analytics 将为您提供一系列模式时某些 Azure 服务的一部分：  
   ![诊断设置模式选择器](media/diagnostic-logs-stream-log-store/diagnostic-settings-mode-selector.png)

我们**强**建议，若要避免可能很难迁移下一步，任何新创建的诊断设置使用的资源为中心的模式。  

对于现有诊断设置，一次启用由特定 Azure 资源，你将能够以追溯方式进行从 Azure 诊断切换到特定于资源的模式。 你以前引入的数据将继续以供_AzureDiagnostics_之前就会按工作区中，在保留期设置中配置，但任何新数据将发送到专用的表。 这意味着，对于任何查询，必须跨越旧数据和新 （直到旧数据完全过期）、 一个[union](https://docs.microsoft.com/azure/kusto/query/unionoperator)需要在查询中的运算符来组合两个数据集。

请查看有关新的 Azure 消息在服务中的特定于资源的模式的支持日志[的 Azure 更新](https://azure.microsoft.com/updates/)博客 ！

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>已知的限制：AzureDiagnostics 中的列限制
包含的列数不超过 500 个的任意给定 Azure 日志表具有明确的限制。 一旦达到该限制，在引入时，包含不属于前 500 个列的数据的行将被删除。 AzureDiagnostics 表特别容易受到此限制的影响。 这通常发生大量不同的数据源发送到同一个工作区，因为或多个发送到同一个工作区的详细数据源。 

#### <a name="azure-data-factory"></a>Azure 数据工厂  
Azure 数据工厂中，由于一组非常详细的日志，而是已知会特别受此限制的资源。 具体而言，任何特定于资源的之前配置的诊断设置模式为启用或显式选择使用特定于资源的模式进行反向兼容性的原因：  
- *针对管道中的任一活动定义的用户参数*：将会针对任一活动，为每个唯一命名的用户参数创建一个新列。 
- *活动输入和输出*： 这些不同活动活动并基于它们的详细特性生成大量的列。 
 
作为与更广泛的解决方法建议，建议将迁移应用日志，尽可能快地使用特定于资源的模式。 如果无法立即执行此操作，临时替代方法是将 ADF 日志隔离到其自己的工作区以影响其他日志类型，在您的工作区中收集这些日志的可能性降到最低。 
 
#### <a name="workarounds"></a>解决方法
缩写词，直到在特定于资源的模式下的的任何服务，不启用所有 Azure 服务还支持特定于资源的模式下，建议将详细的数据类型到单独的工作区发布的这些服务以减少达到限制的可能性。  
 
更长的时间，Azure 诊断将转移到支持的特定于资源的模式的所有 Azure 服务。 我们建议迁移到此模式下，越早越好为了尽量避免可能的不受此 500 列限制的影响。  


## <a name="next-steps"></a>后续步骤

* [详细了解 Azure 诊断日志](diagnostic-logs-overview.md)

