---
title: 将 Azure 诊断日志流式传输到 Log Analytics
description: 了解如何将 Azure 诊断日志流式传输到 Log Analytics 工作区。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: bd760fca20a602127e7d33913547dcb2c6bc95f6
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351537"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>将 Azure 诊断日志流式传输到 Log Analytics

可以使用门户、PowerShell cmdlet 或 Azure CLI 将 [Azure 诊断日志](diagnostic-logs-overview.md)近实时地流式传输到 Azure Log Analytics。

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>可以对 Log Analytics 中的诊断日志执行的操作

Azure Log Analytics 是一种灵活的日志搜索和分析工具，利用该工具，可以深入了解从 Azure 资源生成的原始日志数据。 其中一些功能包括：

* **日志搜索** - 对日志数据编写高级查询、关联来自各种源的日志，甚至生成可以固定到 Azure 仪表板的图表。
* **警报** - 当检测到一个或多个事件与特定查询匹配时，通过电子邮件或 webhook 调用进行通知。
* **解决方案** - 使用预生成的视图和仪表板，立即了解日志数据。
* **高级分析** - 应用机器学习和模式匹配算法，确定日志揭示的可能问题。

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>启用诊断日志到 Log Analytics 的流式传输

可以通过门户或使用 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings) 以编程方式启用诊断日志的流式传输。 无论采用哪种方式，都可以创建一个诊断设置并在其中指定 Log Analytics 工作区，以及要发送到该工作区的日志类别和指标。 诊断日志类别是一类可由资源提供的日志。

只要配置设置的用户同时拥有两个订阅的相应 RBAC 访问权限，Log Analytics 工作区就不必与发出日志的资源位于同一订阅中。

> [!NOTE]
> 当前不支持通过诊断设置发送多维指标。 多维指标将按平展后的单维指标导出，并跨维值聚合。
>
> 例如：可以基于每个队列级别浏览和绘制事件中心上的“传入消息”指标。 但是，当通过诊断设置导出时，该指标将表示为事件中心的所有队列中的所有传入消息。
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>使用门户流式传输诊断日志
1. 在门户中，导航到 Azure Monitor 并单击“诊断设置”

    ![Azure Monitor 的“监视”部分](media/diagnostic-logs-stream-log-store/diagnostic-settings-blade.png)

2. （可选）按资源组或资源类型筛选列表，并单击要为其设置诊断设置的资源。

3. 如果选定的资源上不存在任何设置，系统会提示创建设置。 单击“启用诊断”。

   ![添加诊断设置 - 没有现有的设置](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   如果资源上有现有的设置，则会看到已在此资源上配置的设置列表。 单击“添加诊断设置”。

   ![添加诊断设置 - 现有的设置](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. 为设置提供名称，并选中“发送到 Log Analytics”框，然后选择 Log Analytics 工作区。

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

## <a name="how-do-i-query-the-data-in-log-analytics"></a>如何在 Log Analytics 中查询数据？

在门户的“日志搜索”边栏选项卡中或作为 Log Analytics 的一部分的高级分析体验中，可以查询诊断日志作为 AzureDiagnostics 表下日志管理解决方案的一部分。 此外，还可以安装 [Azure 资源的多个解决方案](../../azure-monitor/insights/solutions.md)，以立即深入了解发送到 Log Analytics 的日志数据。

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>已知的限制： AzureDiagnostics 列限制
因为许多资源发送的数据类型发送到同一个表 (_AzureDiagnostics_)，此表的架构是超级一组的收集的所有不同的数据类型的架构。 例如，如果创建了以下数据类型的集合的诊断设置后，所有正在发送到同一个工作区：
- 审核日志的资源 1 （无架构，其中包括列 A、 B 和 C）  
- 错误日志的资源 2 （具有架构，其中包括的列 D、 E 和 F）  
- 将流日志数据的资源 3 （具有包含列 G，H，和我的架构）  
 
AzureDiagnostics 表将用一些示例数据，如下所示，查找：  
 
| ResourceProvider | 类别 | A | B | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
| Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
| ... |
 
没有任何给定的 Azure 日志表不具有 500 多个列显式限制。 一旦达到，会在引入时中删除任何包含数据与前 500 个之外的任何列的行。 AzureDiagnostics 表是特别容易为受影响的此限制。 这通常发生在发送到同一个工作区的大量不同的数据源发送到同一个工作区，因为或几个非常详细的数据源。 
 
#### <a name="azure-data-factory"></a>Azure 数据工厂  
Azure 数据工厂中，由于一组非常详细的日志，而是已知会特别受此限制的资源。 具体而言：  
- *针对你的管道中的任何活动定义的用户参数*： 将为每个唯一命名的用户参数与任何活动创建一个新列。 
- *活动输入和输出*： 这些不同活动活动并生成大量的由于其详细特性的列。 
 
作为与更广泛的解决方法建议，建议将 ADF 日志隔离到其自己的工作区以影响其他日志类型，在您的工作区中收集这些日志的可能性降到最低。 我们希望策划准备了日志用于 Azure 数据工厂可按年 4 月中旬 2019年。
 
#### <a name="workarounds"></a>解决方法
短期来看，直到重新定义的 500 列限制，建议将隔离到单独的工作区以减少可能达到的限制的详细数据类型。
 
更长的时间，Azure 诊断将会抛弃统一、 稀疏架构到每个数据类型，每个单独的表对动态类型的支持与配对，这样可以大大提高数据传入 Azure 日志，通过 Azure 诊断机制的可用性。 你可以已经看到此选择 Azure 资源类型，例如[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)或[Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor)日志。 请查找有关在 Azure 上支持这些组织有序的日志中的新资源类型的消息[的 Azure 更新](https://azure.microsoft.com/updates/)博客 ！


## <a name="next-steps"></a>后续步骤

* [详细了解 Azure 诊断日志](diagnostic-logs-overview.md)

