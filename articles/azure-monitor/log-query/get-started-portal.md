---
title: 教程：Log Analytics 查询入门
description: 本教程介绍如何在 Azure 门户中使用 Log Analytics 编写和管理 Azure Monitor 日志查询。
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 03/17/2020
ms.openlocfilehash: bfed93a4ed13878448d21b95d265e49bf0260742
ms.sourcegitcommit: a989fb89cc5172ddd825556e45359bac15893ab7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/01/2020
ms.locfileid: "85798249"
---
# <a name="tutorial-get-started-with-log-analytics-queries"></a>教程：Log Analytics 查询入门

本教程介绍如何在 Azure 门户中使用 Log Analytics 编写、执行和管理 Azure Monitor 日志查询。 可以使用 Log Analytics 查询来搜索字词、识别趋势、分析模式，以及基于数据提供其他许多见解。 

本教程将介绍如何使用 Log Analytics 实现以下目的：

> [!div class="checklist"]
> * 了解日志数据的架构
> * 编写和运行简单的查询，以及修改查询时间范围
> * 对查询结果进行筛选、排序和分组
> * 查看、修改和共享查询结果的视觉对象
> * 保存、加载、导出和复制查询与结果

有关日志查询的详细信息，请参阅 [Azure Monitor 中的日志查询概述](log-query-overview.md)。<br/>
有关编写日志查询的详细教程，请参阅 [Azure Monitor 中的日志查询入门](get-started-queries.md)。

## <a name="open-log-analytics"></a>打开 Log Analytics
若要使用 Log Analytics，需要登录到 Azure 帐户。 如果没有 Azure 帐户，可[免费创建一个帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

若要完成本教程中的大部分步骤，可以使用[此演示环境](https://portal.loganalytics.io/demo)，其中包含了大量示例数据。 使用演示环境无法保存查询，或将结果固定到仪表板。

如果使用 Azure Monitor 来收集至少一个 Azure 资源中的日志数据，则你也可以使用自己的环境。 若要打开 Log Analytics 工作区，请在 Azure Monitor 的左侧导航栏中选择“日志”。  

## <a name="understand-the-schema"></a>了解架构
 
架构是分组到逻辑类别下的表集合。  “演示”架构包含监视解决方案中的多个类别。 例如，**LogManagement** 类别包含 Windows 和 Syslog 事件、性能数据与代理检测信号。

架构表显示在 Log Analytics 工作区的“表”选项卡上。  该表包含列，每个列的数据类型按照列名旁边的图标显示。 例如，**Event** 表包含 **Computer** 之类的文本列，以及 **EventCategory** 之类的数字列。

![架构](media/get-started-portal/schema.png)

## <a name="write-and-run-basic-queries"></a>编写和运行基本查询

Log Analytics 在打开时会在**查询编辑器**中显示一个新的空白查询。

![Log Analytics](media/get-started-portal/homepage.png)

### <a name="write-a-query"></a>编写查询

Azure Monitor 日志查询使用某种版本的 Kusto 查询语言。 查询可以从表名或 [search](/azure/kusto/query/searchoperator) 命令开始。 

以下查询检索 **Event** 表中的所有记录：

```Kusto
Event
```

竖线 (|) 字符分隔命令，第一个命令的输出是下一个命令的输入。 可在单个查询中添加任意数目的命令。 以下查询检索 **Event** 表中的记录，然后在这些记录中搜索任何属性中的 **error** 一词：

```Kusto
Event 
| search "error"
```

使用单个换行符可使查询更易于阅读。 使用多个换行符可将查询拆分为多个独立的查询。

编写同一查询的另一种方法是：

```Kusto
search in (Event) "error"
```

在第二个示例中，**search** 命令仅在 **Events** 表的记录中搜索 **error** 一词。

默认情况下，Log Analytics 会将查询时间范围限制为过去 24 小时。 若要设置不同的时间范围，可将显式的 **TimeGenerated** 筛选器添加到查询，或使用“时间范围”控件。 

### <a name="use-the-time-range-control"></a>使用“时间范围”控件
若要使用“时间范围”控件，请在顶部栏中选择该控件，然后从下拉列表中选择一个值，或选择“自定义”以创建自定义时间范围。  

![时间选取器](media/get-started-portal/time-picker.png)

- 时间范围值采用 UTC 格式，这可能与你的本地时区不同。
- 如果查询针对 **TimeGenerated** 显式设置了筛选器，则时间选取器控件将显示“在查询中设置”，并会禁用以防止冲突。 

### <a name="run-a-query"></a>运行查询
若要运行查询，请将光标放在查询中的某个位置，然后在顶部栏中选择“运行”，或按 **Shift**+**Enter**。  该查询将运行到它找到了空白行为止。

## <a name="filter-results"></a>筛选结果
Log Analytics 将结果数限制为最多 10,000 条记录。 类似于 `Event` 的常规查询会返回过多的结果，其中一些结果没有作用。 可以通过限制查询中的表元素，或者显式针对结果添加筛选器，来筛选查询结果。 通过表元素进行筛选会返回新的结果集，而显式筛选器将应用于现有的结果集。

### <a name="filter-by-restricting-table-elements"></a>通过限制表元素进行筛选
若要通过限制查询中的表元素将 `Event` 查询结果筛选为“Error”事件： 

1. 在查询结果中，选择“EventLevelName”列中包含“Error”的任何记录旁边的下拉箭头。   
   
1. 在展开的详细信息中，悬停鼠标并选择“EventLevelName”旁边的“...”，然后选择“包含‘Error’”。    
   
   ![向查询添加筛选器](media/get-started-portal/add-filter.png)
   
1. 请注意，**查询编辑器**中的查询现已更改为：
   
   ```Kusto
   Event
   | where EventLevelName == "Error"
   ```
   
1. 选择“运行”以运行新查询。 

### <a name="filter-by-explicitly-filtering-results"></a>通过显式筛选结果进行筛选
若要通过筛选查询结果将 `Event` 查询结果筛选为“Error”事件： 

1. 在查询结果中，选择列标题“EventLevelName”旁边的“筛选器”图标。   
   
1. 在弹出窗口的第一个字段中选择“等于”，然后在下一个字段中输入 *error*。  
   
1. 选择“筛选”。 
   
   ![筛选器](media/get-started-portal/filter.png)

## <a name="sort-group-and-select-columns"></a>排序、分组和选择列
若要按特定的列（例如“TimeGenerated [UTC]”）对查询结果进行排序，请选择列标题。  再次选择该标题可切换为按升序或降序排序。

![将列排序](media/get-started-portal/sort-column.png)

还可以按组来组织结果。 若要按特定的列对结果进行分组，请将列标题拖放到结果表上方的、带有“将列标题拖放到此处，以按该列分组”标签的栏中。  若要创建子组，请将其他列拖放到上部栏中。 可以在该栏中重新排列组和子组的层次结构与排序方式。

![组](media/get-started-portal/groups.png)

若要在结果中隐藏或显示列，请选择表上方的“列”，然后从下拉列表中选择或取消选择所需的列。 

![选择列](media/get-started-portal/select-columns.png)

## <a name="view-and-modify-charts"></a>查看和修改图表
还能够以可视格式查看查询结果。 输入以下查询作为示例：

```Kusto
Event 
| where EventLevelName == "Error" 
| where TimeGenerated > ago(1d) 
| summarize count() by Source 
```

默认情况下，结果将显示在表中。 选择表上方的“图表”可在图形视图中查看结果。 

![条形图](media/get-started-portal/bar-chart.png)

结果将显示在堆积条形图中。 选择其他选项（例如“堆积柱形图”或“饼图”）可显示结果的其他视图。  

![饼图](media/get-started-portal/pie-chart.png)

可以通过控件条手动更改视图的不同属性（例如 X 和 Y 轴）或者分组和拆分首选项。

还可以使用 [render](/azure/kusto/query/renderoperator) 运算符在查询本身中设置首选视图。

## <a name="pin-results-to-a-dashboard"></a>将结果固定到仪表板

若要将结果表或图表从 Log Analytics 固定到共享的 Azure 仪表板，请在顶部栏上选择“固定到仪表板”。  

![固定到仪表板](media/get-started-portal/pin-dashboard.png)

在“固定到另一个仪表板”窗格中，选择或创建要固定到的共享仪表板，然后选择“应用”。   表或图表将显示在所选的 Azure 仪表板上。

![已固定到仪表板的图表](media/get-started-portal/pin-dashboard2.png)

固定到共享仪表板的表或图表在以下方面已得到简化： 

- 数据限制为过去 14 天的数据。
- 表最多只显示 4 列和前 7 行。
- 包含许多离散类别的图表会自动将填充内容不多的类别分组到单个“其他”箱中。 

## <a name="save-load-or-export-queries"></a>保存、加载或导出查询

创建查询后，可以保存查询或结果，或者将其与他人共享。 

### <a name="save-queries"></a>保存查询

若要保存查询：

1. 在顶部栏上选择“保存”。 
   
1. 在“保存”对话框中，使用字符 a-z、A-Z、0-9、空格、连字符、下划线、句点、括号或竖线为查询指定**名称**。  
   
1. 选择是要将查询保存为“查询”还是“函数”。   函数是其他查询可以引用的查询。 
   
   若要将查询保存为函数，请提供**函数别名** - 供其他查询用来调用此查询的短名称。
   
1. 提供“查询资源管理器”的**类别**以用于查询。 
   
1. 选择“保存”。 
   
   ![保存函数](media/get-started-portal/save-function.png)

### <a name="load-queries"></a>加载查询
若要加载已保存的查询，请选择右上角的“查询资源管理器”。  此时会打开“查询资源管理器”窗格，其中按类别列出了所有查询。  展开类别或者在搜索栏中输入查询名称，然后选择某个查询以将其载入“查询编辑器”。  可以通过选择查询名称旁边的星形图标，将该查询标记为**收藏项目**。

![查询资源管理器](media/get-started-portal/query-explorer.png)

### <a name="export-and-share-queries"></a>导出和共享查询
若要导出查询，请在顶部栏上选择“导出”，然后从下拉列表中选择“导出到 CSV - 所有列”、“导出到 CSV - 显示的列”或“导出到 Power BI (M 查询)”。    

若要共享查询的链接，请在顶部栏上选择“复制链接”，然后选择“复制查询链接”、“复制查询文本”或“复制查询结果”以复制到剪贴板。     可将查询链接发送给有权访问同一工作区的其他人。

## <a name="next-steps"></a>后续步骤

请继续学习下一篇教程，详细了解如何编写 Azure Monitor 日志查询。
> [!div class="nextstepaction"]
> [编写 Azure Monitor 日志查询](get-started-queries.md)
