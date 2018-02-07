---
title: "将 Azure Log Analytics 数据导入 Power BI | Microsoft 文档"
description: "Power BI 是 Microsoft 的一种基于云的业务分析服务，它为不同数据集的分析提供了丰富的可视化功能和报告。  本文介绍如何配置将 Log Analytics 数据导入 Power BI 并将其配置为自动刷新。"
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
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: e687a1ee8ac4f565062e57b07cdfa9ac5e6bbf4f
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="import-azure-log-analytics-data-into-power-bi"></a>将 Azure Log Analytics 数据导入 Power BI


[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) 是 Microsoft 的一种基于云的业务分析服务，它为不同数据集的分析提供了丰富的可视化功能和报告。  你可以将 Log Analytics 日志搜索结果导入 Power BI 数据集，以便利用其各项功能，例如合并不同来源的数据以及在 Web 和移动设备上共享报表。

本文详细介绍了如何将 Log Analytics 数据导入 Power BI 并计划其自动刷新。  包含了适用于[已升级](#upgraded-workspace)和[旧](#legacy-workspace)工作区的不同过程。

## <a name="upgraded-workspace"></a>已升级工作区


若要将[已升级的 Log Analytics 工作区](log-analytics-log-search-upgrade.md)的数据导入到 Power BI，你可以基于 Log Analytics 中的日志搜索查询在 Power BI 中创建数据集。  每次刷新数据集时都会运行查询。  随后你即可生成使用数据集中的数据的 Power BI 报表。  若要在 Power BI 中创建数据集，你可以将查询从 Log Analytics 导出为 [Power Query (M) 语言](https://msdn.microsoft.com/library/mt807488.aspx)。  然后，你可以使用其在 Power BI Desktop 中创建查询，再将其作为数据集发布到 Power BI。  此过程的详细信息如下所述。

![Log Analytics 到 Power BI](media/log-analytics-powerbi/overview.png)

### <a name="export-query"></a>导出查询
首先创建一个[日志搜索](log-analytics-log-search-new.md)，其从 Log Analytics 返回要填充 Power BI 数据集的数据。  随后将该查询导出为 Power BI Desktop 可以使用的 [Power Query (M) 语言](https://msdn.microsoft.com/library/mt807488.aspx)。

1. 在 Log Analytics 中创建日志搜索以便为数据集提取数据。
2. 如果你正在使用日志搜索门户，请单击“Power BI”。  如果你正在使用 Analytics 门户，请选择“导出” > “Power BI Query (M)”。  这两种选项都会将查询导出到一个名为“PowerBIQuery.txt”的文本文件。 

    ![导出日志搜索](media/log-analytics-powerbi/export-logsearch.png) ![导出日志搜索](media/log-analytics-powerbi/export-analytics.png)

3. 打开该文本文件并复制其内容。

### <a name="import-query-into-power-bi-desktop"></a>将查询导入到 Power BI Desktop
Power BI Desktop 是一个桌面应用程序，用于创建可发布到 Power BI 的数据集和报表。  另外，你还可以使用它创建使用从 Log Analytics 导出的 Power Query 语言的查询。 

1. 请安装 [Power BI Desktop](https://powerbi.microsoft.com/desktop/)（如果你尚未安装），然后打开该应用程序。
2. 选择“获取数据” > “空查询”，以打开一个新查询。  然后选择“高级编辑器”并将导出文件的内容粘贴到查询中。 单击“Done”（完成） 。

    ![Power BI Desktop 查询](media/log-analytics-powerbi/desktop-new-query.png)

5. 查询将运行并显示其结果。  系统可能会提示你输入凭据以连接到 Azure。  
6. 为查询键入一个描述性的名称。  默认值为“Query1”。 单击“关闭并应用”，以向报表添加数据集。

    ![Power BI Desktop](media/log-analytics-powerbi/desktop-results.png)



### <a name="publish-to-power-bi"></a>发布到 Power BI
当你发布到 Power BI 时，将创建数据集和报表。  如果你是在 Power BI Desktop 中创建报表，则发布的报表将包含你的数据。  如果不是，则会创建一个空白报表。  你可以在 Power BI 中修改报表或基于数据集创建新报表。

8. 基于你的数据创建报表。  请使用 [Power BI Desktop 文档](https://docs.microsoft.com/power-bi/desktop-report-view)（如果你不熟悉此应用程序）。  如果你已准备好将其发送到 Power BI，请单击“发布”。  出现提示时，请在 Power BI 帐户中选择目标。  除非你有特定的目标，否则请使用“我的工作区”。

    ![Power BI Desktop 发布](media/log-analytics-powerbi/desktop-publish.png)

3. 发布完成后，单击“在 Power BI 中打开”，以在 Power BI 中打开新数据集。


### <a name="configure-scheduled-refresh"></a>配置计划的刷新
在 Power BI 中创建的数据集中包含的数据与你以前在 Power BI Desktop 中看到的数据一样。  你需要定期刷新数据集以再次运行查询，并使用 Log Analytics 中的最新数据进行填充。  

1. 单击要在其中上传报表的工作区并选择“数据集”菜单。 选择你的新数据集旁边的上下文菜单，然后选择“设置”。 在“数据源凭据”下，应显示一条消息，提示凭据无效。  这是因为你尚未提供在刷新数据时要使用的数据集的凭据。  单击“编辑凭据”，然后指定有权访问 Log Analytics 的凭据。

    ![Power BI 计划](media/log-analytics-powerbi/powerbi-schedule.png)

5. 在“计划的刷新”下，开启“使你的数据保持为最新”选项。  你可以视情况更改“刷新频率”和运行刷新的一个或多个特定时间。

    ![Power BI 刷新](media/log-analytics-powerbi/powerbi-schedule-refresh.png)

## <a name="legacy-workspace"></a>旧工作区
当你使用[旧 Log Analytics 工作区](log-analytics-powerbi.md)配置 Power BI 时，需要创建日志查询以将其结果导出到 Power BI 中的相应数据集。  查询和导出继续按照你定义的计划自动运行，使数据集包含 Log Analytics 收集的最新数据。

![Log Analytics 到 Power BI](media/log-analytics-powerbi/overview-legacy.png)

### <a name="power-bi-schedules"></a>Power BI 计划
*Power BI 计划*包含日志搜索，用于将一组数据从 Log Analytics 导出到 Power BI 中的相应数据集；还包含一个计划，用于定义为了数据集保持最新而运行此搜索的频率。

数据集内的字段与日志搜索返回的记录的属性相匹配。  如果搜索返回不同类型的记录，则数据集将包括每个包含的记录类型中的所有属性。  

### <a name="connecting-log-analytics-workspace-to-power-bi"></a>将 Log Analytics 工作区连接到 Power BI
从 Log Analytics 导出到 Power BI 之前，必须使用以下步骤将工作区连接到 Power BI 帐户。  

1. 在 OMS 控制台中，单击“设置”磁贴。
2. 选择“帐户”。
3. 在“工作区信息”部分中，单击“连接到 Power BI 帐户”。
4. 输入 Power BI 帐户的凭据。

### <a name="create-a-power-bi-schedule"></a>创建 Power BI 计划
使用以下步骤为每个数据集创建 Power BI 计划。

1. 在 OMS 控制台中，单击“日志搜索”磁贴。
2. 键入新查询或选择已保存的搜索，以返回要导出到 **Power BI** 的数据。  
3. 单击页面顶部的“Power BI”按钮打开“Power BI”对话框。
4. 提供下表中的信息并单击“保存”。

| 属性 | 说明 |
|:--- |:--- |
| 名称 |查看 Power BI 计划的列表时用于标识计划的名称。 |
| 已保存的搜索 |要运行的日志搜索。  可以选择当前查询，也可以从下拉框中选择现有已保存的搜索。 |
| 计划 |运行已保存的搜索并导出到 Power BI 数据集的频率。  该值必须介于 15 分钟到 24 小时之间。 |
| 数据集名称 |Power BI 中数据集的名称。  如果数据集不存在，则进行创建；如果数据集已存在，则进行更新。 |

### <a name="viewing-and-removing-power-bi-schedules"></a>查看和删除 Power BI 计划
使用以下步骤查看现有 Power BI 计划的列表。

1. 在 OMS 控制台中，单击“设置”磁贴。
2. 选择“Power BI”。

除了计划的详细信息，还会显示计划在上周运行的次数和上次同步的状态。  如果在同步时遇到错误，可以单击链接以运行日志搜索，查找包含该错误的详细信息的记录。

可以通过在“删除列”中单击“**X**”来删除计划。  可以通过选择“关闭”来禁用计划。  要修改计划，必须先将其删除，然后使用新设置重新创建。

![Power BI 计划](media/log-analytics-powerbi/schedules.png)

### <a name="sample-walkthrough"></a>示例演练
以下部分将演练一个示例，说明如何创建 Power BI 计划和使用其数据集创建简单的报告。  在此示例中，一组计算机的所有性能数据都导出到 Power BI，并创建折线图以显示处理器使用率。

#### <a name="create-log-search"></a>创建日志搜索
首先创建日志搜索以查找想要发送到数据集的数据。  在此示例中，将使用查询返回名称开头为 *srv* 的计算机的所有性能数据。  

![Power BI 计划](media/log-analytics-powerbi/walkthrough-query.png)

#### <a name="create-power-bi-search"></a>创建 Power BI 搜索
单击“Power BI”按钮以打开 Power BI 对话框，并提供所需的信息。  我们希望此搜索每小时运行一次并创建一个称为 *Contoso Perf* 的数据集。  由于我们已打开创建所需数据的搜索，因此保留“已保存的搜索”的默认值“使用当前搜索查询”。

![Power BI 搜索](media/log-analytics-powerbi/walkthrough-schedule.png)

#### <a name="verify-power-bi-search"></a>验证 Power BI 搜索
为了验证是否正确创建了计划，我们查看 OMS 仪表板中“设置”磁贴下的 Power BI 搜索列表。  请等待几分钟并刷新此视图，直到它报告已运行同步。  通常会计划数据集来自动刷新。

![Power BI 搜索](media/log-analytics-powerbi/walkthrough-schedules.png)

#### <a name="verify-the-dataset-in-power-bi"></a>在 Power BI 中验证数据集
在 [powerbi.microsoft.com](http://powerbi.microsoft.com/) 中登录到我们的帐户，并滚动到左窗格底部的“数据集”。  我们可以看到列出了“Contoso Perf”数据集，这表示导出已成功运行。

![Power BI 数据集](media/log-analytics-powerbi/walkthrough-datasets.png)

#### <a name="create-report-based-on-dataset"></a>基于数据集创建报告
选择“Contoso Perf”数据集，并单击右侧“字段”窗格中的“结果”以查看作为此数据集一部分的字段。  若要创建显示每台计算机处理器使用率的折线图，应执行以下操作。

1. 选择折线图可视化。
2. 将“ObjectName”拖动到“报告级别筛选器”，并选中“处理器”。
3. 将“**CounterName**”拖动到“**报告级别筛选器**”，并选中“**处理时间百分比**”。
4. 将“CounterValue”拖动到“值”。
5. 将“计算机”拖动到“图例”。
6. 将“**TimeGenerated**”拖动到“**轴**”。

我们可以看到，生成的折线图显示来自数据集中的数据。

![Power BI 折线图](media/log-analytics-powerbi/walkthrough-linegraph.png)

#### <a name="save-the-report"></a>保存报告
通过单击屏幕顶部的“保存”按钮保存报告，并验证它现在列于左窗格的“报告”部分中。

![Power BI 报表](media/log-analytics-powerbi/walkthrough-report.png)



## <a name="next-steps"></a>后续步骤
* 了解有关[日志搜索](log-analytics-log-searches.md)的信息以生成可导出到 Power BI 的查询。
* 了解有关 [Power BI](http://powerbi.microsoft.com) 的详细信息以基于 Log Analytics 导出内容生成可视化结果。
