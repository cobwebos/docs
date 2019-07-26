---
title: Azure Monitor Log Analytics 入门 |Microsoft Docs
description: 本文提供有关在 Azure 门户中使用 Log Analytics 编写查询的教程。
services: log-analytics
author: bwren
manager: carmonm
ms.service: log-analytics
ms.topic: conceptual
ms.date: 07/19/2019
ms.author: bwren
ms.openlocfilehash: cf2aee475f5d3933421de45fa5b2ade687bed62f
ms.sourcegitcommit: e72073911f7635cdae6b75066b0a88ce00b9053b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68348080"
---
# <a name="get-started-with-log-analytics-in-azure-monitor"></a>Azure Monitor 中的 Log Analytics 入门

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

本教程介绍如何在 Azure 门户中使用 Log Analytics 来编写 Azure Monitor 日志查询。 具体内容包括：

- 使用 Log Analytics 编写一个简单查询
- 了解数据的架构
- 筛选、排序和分组结果
- 应用时间范围
- 创建图表
- 保存和加载查询
- 导出和共享查询

有关编写日志查询的教程，请参阅 [Azure Monitor 中的日志查询入门](get-started-queries.md)。<br>
有关日志查询的详细信息，请参阅 [Azure Monitor 中的日志查询概述](log-query-overview.md)。

## <a name="meet-log-analytics"></a>满足 Log Analytics
Log Analytics 是用于写入和执行 Azure Monitor 日志查询的 web 工具。 可以通过在 Azure Monitor 菜单中选择“日志”来将其打开。 它将启动并显示一个新的空白查询。

![主页](media/get-started-portal/homepage.png)

## <a name="firewall-requirements"></a>防火墙要求
若要使用 Log Analytics，浏览器需要访问以下地址。 如果浏览器通过防火墙访问 Azure 门户，则必须允许访问这些地址。

| Uri | IP | 端口 |
|:---|:---|:---|
| portal.loganalytics.io | 动态 | 80,443 |
| api.loganalytics.io | 动态 | 80,443 |
| docs.loganalytics.io | 动态 | 80,443 |

## <a name="basic-queries"></a>基本查询
查询可用于搜索字词、识别趋势、分析模式，以及基于数据提供其他许多见解。 从基本查询着手：

```Kusto
Event | search "error"
```

此查询在 _Event_ 表中搜索任何属性中包含词语“error”的记录。

查询可以从表名或 [search](/azure/kusto/query/searchoperator) 命令开始。 上面的示例以表名 _Event_ 开头，它检索 Event 表中的所有记录。 竖线 (|) 字符分隔命令，因此第一个命令的输出用作后一个命令的输入。 可在单个查询中添加任意数目的命令。

编写同一查询的另一种方法是：

```Kusto
search in (Event) "error"
```

在此示例中，**search** 的范围限定为 _Event_ 表，将在该表中搜索包含词语“error”的所有记录。

## <a name="running-a-query"></a>运行查询
通过单击“运行”按钮或按 **Shift+Enter** 来运行查询。 请注意以下详细信息，其中确定了要运行的代码以及返回的数据：

- 换行符：使用单个换行符可让查询更易于阅读。 使用多个换行符会将查询拆分为多个独立的查询。
- 游标：将游标置于查询中的某个位置以执行它。 当前查询被视为空行之前的代码。
- 时间范围 - 默认设置的时间范围为过去 24 小时。 若要使用不同的范围，请使用时间选取器，或者在查询中添加明确的时间范围筛选器。


## <a name="understand-the-schema"></a>了解架构
架构是直观分组到某个逻辑类别下的表集合。 有多个类别来自监视解决方案。 _LogManagement_ 类别包含公用数据，例如 Windows 和 Syslog 事件、性能数据和代理检测信号。

![架构](media/get-started-portal/schema.png)

在每个表中，数据组织成具有不同数据类型的列。数据类型由列名旁边的图标指示。 例如，屏幕截图中显示的 _Event_ 表包含文本类型的 _Computer_、数字类型的 _EventCategory_ 和日期/时间类型的 _TimeGenerated_ 等列。

## <a name="filter-the-results"></a>筛选结果
首先获取 _Event_ 表中的所有内容。

```Kusto
Event
```

Log Analytics 会按以下方式自动限定结果:

- 时间范围:默认情况下，查询限制为过去 24 小时。
- 结果数：结果数限制为最多 10,000 条记录。

此查询的范围太泛，它返回了过多的结果，因此不太实用。 可以通过表元素或者在查询中显式添加筛选器来筛选结果。 通过表元素筛选结果的方法适用于现有结果集，而在查询本身中添加的筛选器会返回新的筛选结果集，因此可以生成更准确的结果。

### <a name="add-a-filter-to-the-query"></a>将筛选器添加到查询
每条记录的左侧有一个箭头。 单击此箭头可以打开特定记录的详细信息。

将鼠标悬停在列名上会显示“+”和“-”图标。 若要添加筛选器以便仅返回具有相同值的记录，请单击“+”号。 单击“-”排除具有此值的记录，然后单击“运行”以再次运行查询。

![向查询添加筛选器](media/get-started-portal/add-filter.png)

### <a name="filter-through-the-table-elements"></a>通过表元素筛选
现在，让我们关注严重性为 _Error_ 的事件。 名为 _EventLevelName_ 的列中指定了严重性。 需要向右滚动才能看到此列。

单击列标题旁边的“筛选”图标，然后在弹出窗口中选择以文本 _error_ 开头的值：

![筛选](media/get-started-portal/filter.png)


## <a name="sort-and-group-results"></a>排序和分组结果
现在，结果的范围已缩小，只包括过去 24 小时在 SQL Server 中创建的“错误”事件。 但是，结果未进行任何形式的排序。 若要按特定的列（例如 _timestamp_）将结果排序，请单击列标题。 单击一下会按升序排序，再单击一下会按降序排序。

![将列排序](media/get-started-portal/sort-column.png)

还可以按组来组织结果。 若要按特定的列来分组结果，只需将该列标题拖放到其他列的上方即可。 若要创建子组，请同时将其他列拖放到上部栏中。

![个组](media/get-started-portal/groups.png)

## <a name="select-columns-to-display"></a>选择要显示的列
结果表通常包含大量的列。 你可能发现，某些返回的列默认未显示，或者，你可能想要删除显示的某些列。 若要选择要显示的列，请单击“列”按钮：

![选择列](media/get-started-portal/select-columns.png)


## <a name="select-a-time-range"></a>选择时间跨度
默认情况下, Log Analytics 应用_过去24小时_的时间范围。 若要使用不同的范围，请通过时间选取器选择另一个值，然后单击“运行”。 除预设值以外，还可以使用“自定义时间范围”选项来选择查询的绝对范围。

![时间选取器](media/get-started-portal/time-picker.png)

选择自定义时间范围时，所选值采用 UTC 格式，这可能不同于你的本地时区。

如果查询显式包含 _TimeGenerated_ 的筛选器，则时间选取器标题中会显示“在查询中设置”。 将禁用手动选择，以防止冲突。


## <a name="charts"></a>图表
除了在表中返回结果以外，还会以可视格式显示查询结果。 使用以下查询作为示例：

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

默认会在表中显示结果。 单击“图表”可在图形视图中查看结果：

![条形图](media/get-started-portal/bar-chart.png)

在堆积条形图中显示结果。 单击“堆积柱形图”并选择“饼图”可以显示另一个结果视图：

![饼图](media/get-started-portal/pie-chart.png)

可以通过控件条手动更改视图的不同属性（例如 X 和 Y 轴）或者分组和拆分首选项。

还可以使用 render 运算符在查询本身中设置首选视图。

### <a name="smart-diagnostics"></a>智能诊断
时间图表中，如果数据出现突增或上了一个台阶，则可在图线中看到突出显示的点。 这表示智能诊断已识别到筛选出发生突然变化的属性组合。 单击相应的点可获取有关筛选器的详细信息以及查看筛选器版本。 这有助于识别导致发生变化的原因：

![智能诊断](media/get-started-portal/smart-diagnostics.png)

## <a name="pin-to-dashboard"></a>固定到仪表板
若要将图表或表固定到某个共享的 Azure 仪表板，请单击图钉图标。

![固定到仪表板](media/get-started-portal/pin-dashboard.png)

在某些情况下，将图表固定到仪表板时，图表会应用特定的简化功能：

- 表列和行：若要将某个表固定到仪表板，该表包含的列数不能超过四个。 只显示前七行。
- 时间限制：查询自动限制为过去 14 天。
- Bin 计数限制:如果显示的图表包含许多离散的 Bin，所占比例较少的 Bin 将自动分组到单个“其他”Bin。

## <a name="save-queries"></a>保存查询
创建有用的查询后，可以将其保存，或者与他人共享。 “保存”图标位于顶部栏上。

可以保存整个查询页，或者将单个查询保存为一个函数。 函数是也可以由其他查询引用的查询。 若要将查询保存为函数，必须提供函数别名，这是当此查询由其他查询引用时，用来调用此查询的名称。

![保存函数](media/get-started-portal/save-function.png)

>[!NOTE]
>在保存或编辑保存的`a–z, A–Z, 0-9, -, _, ., <space>, (, ), |`查询时, 在 "**名称**" 字段中支持以下字符。

Log Analytics 查询始终保存到选定的工作区中，并与该工作区的其他用户共享。

## <a name="load-queries"></a>加载查询
“查询资源管理器”图标位于右上区域。 其中按类别列出了所有已保存的查询。 在其中还可将特定的查询加入收藏夹，以便将来快速找到它们。 双击某个已保存的查询可将其添加到当前窗口中。

![查询资源管理器](media/get-started-portal/query-explorer.png)

## <a name="export-and-share-as-link"></a>作为链接导出和共享
Log Analytics 支持多种导出方法:

- Excel：将结果保存为 CSV 文件。
- Power BI：将结果导出到 Power BI。 有关详细信息，请参阅[将 Azure Monitor 日志数据导入到 Power BI 中](../../azure-monitor/platform/powerbi.md)。
- 共享链接：可将查询本身作为链接共享，然后，有权访问同一工作区的其他用户可以发送和执行该查询。

## <a name="next-steps"></a>后续步骤

- 详细了解[编写 Azure Monitor 日志查询](get-started-queries.md)。
