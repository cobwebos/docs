---
title: 包含自定义参数的 Azure Monitor 工作簿
description: 使用预先构建的自定义参数化工作簿简化复杂的报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: 4d9f6e48722f01970a90a3a1d8d8b58b5d939774
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "77658262"
---
# <a name="interactive-workbooks"></a>交互式工作簿

作者可以通过工作簿为其使用者创建交互式报表和体验。 通过多种方式支持交互。

## <a name="parameter-changes"></a>参数更改
当工作簿用户更新某个参数时，使用该参数的任何控件将自动刷新并重新绘制，以反映新状态。 这是大多数 Azure 门户报告支持交互的方式。 工作簿以非常直接的方式提供此功能，可最大程度地减少用户的工作量。

详细了解[工作簿中的参数](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>网格行单击
工作簿允许作者构造这种方案：单击网格中的某行可根据该行的内容更新后续图表。 

例如，用户有一个网格，其中显示请求列表以及一些统计信息（例如失败计数）。 他们可以对网格进行设置，使得在单击对应于某个请求的行时会在下面生成详细图表，这些图表仅根据该请求进行筛选以更新其内容。

### <a name="setting-up-interactivity-on-grid-row-click"></a>设置在单击网格行时的交互
1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。 
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. 选择 `Run query` 查看结果
6. 单击查询页脚中的“高级设置”图标（齿轮图标）。 此时会打开“高级设置”窗格 
7. 选中设置：`When an item is selected, export a parameter`
    1. 要导出的字段：`Request`
    2. 参数名称：`SelectedRequest`
    3. 默认值：30`All requests`
    
    ![显示高级编辑器的插图，其中包含用于将字段导出为参数的设置](./media/workbooks-interactive/advanced-settings.png)

8. 单击 `Done Editing`。
9. 使用步骤 2 和 3 添加另一个查询控件。
10. 使用查询编辑器输入用于分析的 KQL
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. 选择 `Run query` 查看结果。
12. 将“可视化效果”更改为 `Area chart`
12. 单击第一个网格中的某行。 请注意，下面的面积图已根据所选请求筛选了内容。

在编辑模式下，生成的报表如下所示：

![显示使用网格行单击创建交互式体验的插图](./media/workbooks-interactive//grid-click-create.png)

下图显示了基于相同原理的、处于只读模式的更精致交互式报表。 报表使用网格单击来导出参数，而这些参数又在两个图表和一个文本块中使用。

![显示使用网格行单击创建交互式体验的插图](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>导出整行的内容
有时需要导出所选行的全部内容，而不只是导出特定的列。 在这种情况下，请不要在上述步骤 7.1 中设置 `Field to export` 属性。 工作簿会将整行内容作为 JSON 导出到参数。 

在引用 KQL 控件时，请使用 `todynamic` 函数来分析该 JSON 并访问各个列。

 ## <a name="grid-cell-clicks"></a>网格单元格单击
工作簿允许作者通过一种特殊类型的称为 `link renderer` 的网格列呈现器来添加交互。 链接呈现器根据单元格的内容将网格单元格转换为超链接。 工作簿支持多种类型的链接呈现器 - 包括允许打开资源概述边栏选项卡、属性包查看器、App Insights 搜索、使用情况、事务跟踪等的呈现器。

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>使用网格单元格单击设置交互
1. 单击“编辑”工具栏项，将工作簿切换到编辑模式。
2. 使用“添加查询”链接将日志查询控件添加到工作簿。 
3. 选择“日志”作为查询类型，并选择资源类型（例如 Application Insights）以及目标资源。
4. 使用查询编辑器输入用于分析的 KQL
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. 选择 `Run query` 查看结果
6. 单击“列设置”打开“设置”窗格。
7. 在“列”部分，设置：
    1. _样本_ - 列呈现器：`Link`，要打开的视图：`Cell Details`，链接标签：`Sample`
    2. _计数_ - 列呈现器：`Bar`，调色板：`Blue`，最小值：`0`
    3. _请求_ - 列呈现器：`Automatic`
    4. 单击“保存并关闭”以应用更改
8. 单击网格中的 `Sample` 链接之一。 此时会打开一个属性窗格，其中包含采样请求的详细信息。

    ![显示使用网格单元格单击创建交互式体验的插图](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>链接呈现器操作
| 链接操作 | 单击时的操作 |
|:------------- |:-------------|
| `Generic Details` | 在属性网格上下文边栏选项卡中显示行值 |
| `Cell Details` | 在属性网格上下文边栏选项卡中显示单元格值 当单元包含带有信息的动态类型（例如，包含位置、角色实例等请求属性的 JSON）时，此操作非常有用。 |
| `Cell Details` | 在属性网格上下文边栏选项卡中显示单元格值 当单元包含带有信息的动态类型（例如，包含位置、角色实例等请求属性的 JSON）时，此操作非常有用。 |
| `Custom Event Details` | 使用单元格中的自定义事件 ID (itemId) 打开 Application Insights 搜索详细信息 |
| `* Details` | 类似于自定义事件详细信息，只是在依赖项、异常、页面视图、请求和跟踪方面不同。 |
| `Custom Event User Flows` | 打开围绕单元格中自定义事件名称构建的 Application Insights 用户流体验 |
| `* User Flows` | 类似于自定义事件用户流，只是在异常、页面视图和请求方面不同 |
| `User Timeline` | 使用单元格中的用户 ID (user_Id) 打开用户时间线 |
| `Session Timeline` | 针对单元中的值打开 Application Insights 搜索体验（例如，当单元格中的值为 abc 时，搜索文本“abc”） |
| `Resource overview` | 根据单元格中的资源 ID 值在门户中打开资源的概述 |

## <a name="conditional-visibility"></a>条件可见性
工作簿允许用户根据参数的值显示或隐藏某些控件。 这样，作者便可以根据用户输入或遥测状态生成不同外观的报告。 例如，当一切顺利时，只向使用者显示摘要；遇到问题时显示完整详细信息。

### <a name="setting-up-interactivity-using-conditional-visibility"></a>使用条件可见性设置交互
1. 遵循 `Setting up interactivity on grid row click` 部分中的步骤设置两个交互式控件。
2. 在顶部添加一个新参数：
    1. 名称：`ShowDetails`
    2. 参数类型：`Drop down`
    3. 必需：`checked`
    4. 从以下位置获取数据：`JSON`
    5. JSON 输入：`["Yes", "No"]`
    6. 选择“保存”以提交更改。
3. 将参数值设置为 `Yes`
4. 在带有面积图的查询控件中，单击“高级设置”图标（齿轮图标）
5. 选中设置 `Make this item conditionally visible`
    1. 如果 `ShowDetails` 参数值 `equals` `Yes`，则显示此项
6. 单击“完成编辑”以提交更改。
7. 在工作簿工具栏上单击“完成编辑”以进入读取模式。
8. 将参数 `ShowDetails` 的值切换为 `No`。 请注意下面的图表已消失。

下图显示了当 `ShowDetails` 为 `Yes` 时的内容可视情况

![显示当图表可见时的条件可见性的插图](./media/workbooks-interactive/conditional-visibility.png)

下图显示了当 `ShowDetails` 为 `No` 时的内容隐藏情况

![显示当图表隐藏时的条件可见性的插图](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>后续步骤


* [开始](workbooks-visualizations.md)详细了解工作簿丰富的可视化效果选项。
* [控制](workbooks-access-control.md)并共享对工作簿资源的访问权限。
