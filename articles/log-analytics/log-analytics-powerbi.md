---
title: "将 Log Analytics 数据导出到 Power BI | Microsoft Docs"
description: "Power BI 是 Microsoft 的一种基于云的业务分析服务，它为不同数据集的分析提供了丰富的可视化功能和报告。  Log Analytics 可将数据从 OMS 存储库连续导出到 Power BI，以便可以利用其可视化和分析工具。  本文介绍如何在 Log Analytics 中配置自动定期导出到 Power BI 的查询。"
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: tysonn
ms.assetid: 83edc411-6886-4de1-aadd-33982147b9c3
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: bwren
ms.openlocfilehash: 271747e25f319c76195ec643025d24c6b7cdc9c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="export-log-analytics-data-to-power-bi"></a>将 Log Analytics 数据导出到 Power BI

>[!NOTE]
> 如果工作区已升级到[新的 Log Analytics 查询语言](log-analytics-log-search-upgrade.md)，则将 Log Analytics 数据导出到 Power BI 的此过程将不再工作。  将禁用升级前创建的任何现有计划。 还将不会再在“设置”的“预览功能”下看到启用 Power BI 导出的功能，因为该功能已在升级后的工作区中完全发布。 
>
> 升级后，Azure Log Analytics 会将同一平台用作 Application Insights，将 Log Analytics 查询导出到 Power BI 的过程与[将 Application Insights 查询导出到 Power BI 的过程](../application-insights/app-insights-export-power-bi.md#export-analytics-queries)相同。  可按该文章中所述，使用分析控制台导出查询，也可以在日志搜索门户的屏幕顶部选择“Power BI”按钮。
>
> 用户将需要访问 Azure 中的工作区资源，才能在升级后的工作区中使用 Power BI 导出功能。 如果没有访问权限，用户在将查询导入到他们没有访问权限的 Power BI Desktop 时将看到一条错误消息。



[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 是 Microsoft 的一种基于云的业务分析服务，它为不同数据集的分析提供了丰富的可视化功能和报告。  Log Analytics 可将数据从 OMS 存储库自动导出到 Power BI，以便可以利用其可视化和分析工具。

当使用 Log Analytics 配置 Power BI 时，需要创建日志查询以将其结果导出到 Power BI 中的相应数据集。  查询和导出继续按照你定义的计划自动运行，使数据集包含 Log Analytics 收集的最新数据。

![Log Analytics 到 Power BI](media/log-analytics-powerbi/overview.png)

## <a name="power-bi-schedules"></a>Power BI 计划
*Power BI 计划*包含日志搜索，用于将一组数据从 OMS 存储库导出到 Power BI 中的相应数据集；还包含一个计划，用于定义为了数据集保持最新而运行此搜索的频率。

数据集内的字段与日志搜索返回的记录的属性相匹配。  如果搜索返回不同类型的记录，则数据集将包括每个包含的记录类型中的所有属性。  

> [!NOTE]
> 最佳实践是使用返回原始数据的日志搜索查询，而不是使用命令（如 [Measure](log-analytics-search-reference.md#measure)）执行任何合并。  可以基于原始数据在 Power BI 中执行任何聚合和计算。
>
>

## <a name="connecting-oms-workspace-to-power-bi"></a>将 OMS 工作区连接到 Power BI
从 Log Analytics 导出到 Power BI 之前，必须使用以下步骤将 OMS 工作区连接到 Power BI 帐户。  

1. 在 OMS 控制台中，单击“设置”磁贴。
2. 选择“帐户”。
3. 在“工作区信息”部分中，单击“连接到 Power BI 帐户”。
4. 输入 Power BI 帐户的凭据。

## <a name="create-a-power-bi-schedule"></a>创建 Power BI 计划
使用以下步骤为每个数据集创建 Power BI 计划。

1. 在 OMS 控制台中，单击“日志搜索”磁贴。
2. 键入新查询或选择已保存的搜索，以返回要导出到 **Power BI** 的数据。  
3. 单击页面顶部的“Power BI”按钮打开“Power BI”对话框。
4. 提供下表中的信息并单击“保存”。

| 属性 | 说明 |
|:--- |:--- |
| Name |查看 Power BI 计划的列表时用于标识计划的名称。 |
| 已保存的搜索 |要运行的日志搜索。  可以选择当前查询，也可以从下拉框中选择现有已保存的搜索。 |
| 计划 |运行已保存的搜索并导出到 Power BI 数据集的频率。  该值必须介于 15 分钟到 24 小时之间。 |
| 数据集名称 |Power BI 中数据集的名称。  如果数据集不存在，则进行创建；如果数据集已存在，则进行更新。 |

## <a name="viewing-and-removing-power-bi-schedules"></a>查看和删除 Power BI 计划
使用以下步骤查看现有 Power BI 计划的列表。

1. 在 OMS 控制台中，单击“设置”磁贴。
2. 选择“Power BI”。

除了计划的详细信息，还会显示计划在上周运行的次数和上次同步的状态。  如果在同步时遇到错误，可以单击链接以运行日志搜索，查找包含该错误的详细信息的记录。

可以通过在“删除列”中单击“**X**”来删除计划。  可以通过选择“关闭”来禁用计划。  要修改计划，必须先将其删除，然后使用新设置重新创建。

![Power BI 计划](media/log-analytics-powerbi/schedules.png)

## <a name="sample-walkthrough"></a>示例演练
以下部分将演练一个示例，说明如何创建 Power BI 计划和使用其数据集创建简单的报告。  在此示例中，一组计算机的所有性能数据都导出到 Power BI，并创建折线图以显示处理器使用率。

### <a name="create-log-search"></a>创建日志搜索
首先创建日志搜索以查找想要发送到数据集的数据。  在此示例中，将使用查询返回名称开头为 *srv* 的计算机的所有性能数据。  

![Power BI 计划](media/log-analytics-powerbi/walkthrough-query.png)

### <a name="create-power-bi-search"></a>创建 Power BI 搜索
单击“Power BI”按钮以打开 Power BI 对话框，并提供所需的信息。  我们希望此搜索每小时运行一次并创建一个称为 *Contoso Perf* 的数据集。  由于我们已打开创建所需数据的搜索，因此保留“已保存的搜索”的默认值“使用当前搜索查询”。

![Power BI 搜索](media/log-analytics-powerbi/walkthrough-schedule.png)

### <a name="verify-power-bi-search"></a>验证 Power BI 搜索
为了验证是否正确创建了计划，我们查看 OMS 仪表板中“设置”磁贴下的 Power BI 搜索列表。  请等待几分钟并刷新此视图，直到它报告已运行同步。

![Power BI 搜索](media/log-analytics-powerbi/walkthrough-schedules.png)

### <a name="verify-the-dataset-in-power-bi"></a>在 Power BI 中验证数据集
在 [powerbi.microsoft.com](http://powerbi.microsoft.com/) 中登录到我们的帐户，并滚动到左窗格底部的“数据集”。  我们可以看到列出了“Contoso Perf”数据集，这表示导出已成功运行。

![Power BI 数据集](media/log-analytics-powerbi/walkthrough-datasets.png)

### <a name="create-report-based-on-dataset"></a>基于数据集创建报告
选择“Contoso Perf”数据集，并单击右侧“字段”窗格中的“结果”以查看作为此数据集一部分的字段。  若要创建显示每台计算机处理器使用率的折线图，应执行以下操作。

1. 选择折线图可视化。
2. 将“ObjectName”拖动到“报告级别筛选器”，并选中“处理器”。
3. 将“**CounterName**”拖动到“**报告级别筛选器**”，并选中“**处理时间百分比**”。
4. 将“CounterValue”拖动到“值”。
5. 将“计算机”拖动到“图例”。
6. 将“**TimeGenerated**”拖动到“**轴**”。

我们可以看到，生成的折线图显示来自数据集中的数据。

![Power BI 折线图](media/log-analytics-powerbi/walkthrough-linegraph.png)

### <a name="save-the-report"></a>保存报告
通过单击屏幕顶部的“保存”按钮保存报告，并验证它现在列于左窗格的“报告”部分中。

![Power BI 报表](media/log-analytics-powerbi/walkthrough-report.png)

## <a name="next-steps"></a>后续步骤
* 了解有关[日志搜索](log-analytics-log-searches.md)的信息以生成可导出到 Power BI 的查询。
* 了解有关 [Power BI](http://powerbi.microsoft.com) 的详细信息以基于 Log Analytics 导出内容生成可视化结果。
