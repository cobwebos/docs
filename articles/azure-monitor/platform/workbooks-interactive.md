---
title: 包含自定义参数的 Azure Monitor 工作簿
description: 使用预先构建的自定义参数化工作簿简化复杂的报表
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: mbullwin
ms.openlocfilehash: 33da3cd8a72bb4d93011c348db65c5b4d9e687ed
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461397"
---
# <a name="interactive-workbooks"></a>交互式工作簿

作者可以通过工作簿为其使用者创建交互式报表和体验。 通过多种方式支持交互。

## <a name="parameter-changes"></a>参数更改

当工作簿用户更新某个参数时，使用该参数的任何控件将自动刷新并重新绘制，以反映新状态。 这是大多数 Azure 门户报告支持交互的方式。 工作簿以最少的用户工作方式提供此项。

详细了解[工作簿中的参数](workbooks-parameters.md)

## <a name="grid-tile-chart-selections"></a>网格，磁贴，图表选择

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
6. 选择 "查询" 页脚上的 "_高级设置_" 图标（图标看起来像齿轮）。 这将打开 "高级设置" 窗格。
7. 检查设置： `When an item is selected, export a parameter` 。
8. 在选中的设置下，选择 "*添加参数*"，并填写下面的信息。
    1. 要导出的字段：`Request`
    2. 参数名称：`SelectedRequest`
    3. 默认值：30`All requests`
9. 选择“保存”

    ![显示高级编辑器的屏幕截图，其中的设置用于将字段导出为参数。](./media/workbooks-interactive/export-parameters-add.png)

10. 选择 `Done Editing`。
11. 使用步骤 2 和 3 添加另一个查询控件。
12. 使用查询编辑器为分析输入 KQL。
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
13. 选择 `Run query` 查看结果。
14. 将_可视化效果_更改为 `Area chart` 。
15. 选择要在第一个网格中选择的行。 请注意，下面的面积图已根据所选请求筛选了内容。

在编辑模式下，生成的报表如下所示：

![在编辑模式下的前两个查询的屏幕截图。](./media/workbooks-interactive//interactivity-grid-create.png)

下图显示了基于相同原理的、处于只读模式的更精致交互式报表。 报表使用网格单击来导出参数，而这些参数又在两个图表和一个文本块中使用。

![在读取模式下使用网格单击屏幕快照报表。](./media/workbooks-interactive/interactivity-grid-read-mode.png)

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
6. 选择 "_列设置_" 打开 "设置" 窗格。
7. 在“列”部分，设置：
    1. _样本_ - 列呈现器：`Link`，要打开的视图：`Cell Details`，链接标签：`Sample`
    2. _计数_ - 列呈现器：`Bar`，调色板：`Blue`，最小值：`0`
    3. _请求_ - 列呈现器：`Automatic`
    4. 选择 "_保存并关闭_" 以应用更改

    ![列设置选项卡的屏幕截图。](./media/workbooks-interactive/column-settings.png)

8. 单击网格中的 `Sample` 链接之一。 这会打开一个窗格，其中包含抽样请求的详细信息。

    ![采样请求的详细信息窗格的屏幕截图。](./media/workbooks-interactive/details.png)

### <a name="link-renderer-actions"></a>链接呈现器操作

| 链接操作 | 单击时的操作 |
|:------------- |:-------------|
| `Generic Details` | 显示属性网格上下文选项卡中的行值 |
| `Cell Details` | 显示 "属性网格" 上下文选项卡中的单元值。当单元格包含包含信息的动态类型（例如，使用位置、角色实例等请求属性的 json）时，很有用。 |
| `Cell Details` | 显示 "属性网格" 上下文选项卡中的单元值。当单元格包含包含信息的动态类型（例如，使用位置、角色实例等请求属性的 json）时，很有用。 |
| `Custom Event Details` | 用单元格中的自定义事件 ID （）打开 Application Insights 搜索详细信息 `itemId` |
| `* Details` | 类似于自定义事件详细信息，只是在依赖项、异常、页面视图、请求和跟踪方面不同。 |
| `Custom Event User Flows` | 打开围绕单元格中自定义事件名称构建的 Application Insights 用户流体验 |
| `* User Flows` | 类似于自定义事件用户流，只是在异常、页面视图和请求方面不同 |
| `User Timeline` | 使用单元格中的用户 ID (user_Id) 打开用户时间线 |
| `Session Timeline` | 针对单元中的值打开 Application Insights 搜索体验（例如，当单元格中的值为 abc 时，搜索文本“abc”） |
| `Resource overview` | 根据单元格中的资源 ID 值在门户中打开资源的概述 |

## <a name="conditional-visibility"></a>条件可见性

工作簿允许用户根据参数的值显示或隐藏某些控件。 这样，作者便可以根据用户输入或遥测状态生成不同外观的报告。 例如，当一切顺利时，只向使用者显示摘要；遇到问题时显示完整详细信息。

### <a name="setting-up-interactivity-using-conditional-visibility"></a>使用条件可见性设置交互

1. 按照 "在[网格行上设置交互性](#setting-up-interactivity-on-grid-row-click)" 一节中的步骤设置两个交互控件。
2. 在顶部添加一个新参数：
    1. 名称：`ShowDetails`
    2. 参数类型：`Drop down`
    3. 必需：`checked`
    4. 从以下位置获取数据：`JSON`
    5. JSON 输入：`["Yes", "No"]`
    6. 选择“保存”以提交更改。

    ![选择 "添加参数" 按钮后，将显示 "编辑参数" 窗格。](./media/workbooks-interactive/edit-parameter.png)

3. 将参数值设置为 `Yes`

    ![在 "完成编辑" 按钮的上方，可以设置参数值](./media/workbooks-interactive/set-parameter.png)

4. 在带有面积图的查询控件中，选择 "_高级设置_" 图标（齿轮图标）
5. 选中设置 `Make this item conditionally visible`
    1. 如果 `ShowDetails` 参数值 `equals` `Yes`，则显示此项
6. 选择 "_完成编辑_" 以提交更改。
7. 选择 "工作簿" 工具栏上的 "_完成编辑_" 以进入读取模式。
8. 将参数 `ShowDetails` 的值切换为 `No`。 请注意下面的图表已消失。

下图显示了当 `ShowDetails` 为 `Yes` 时的内容可视情况

![显示图表可见的条件可见性的屏幕截图](./media/workbooks-interactive/interactivity-conditional-visibility-visible.png)

下图显示了当 `ShowDetails` 为 `No` 时的内容隐藏情况

![显示隐藏图表的条件可见性的屏幕截图](./media/workbooks-interactive/interactivity-conditional-visibility-invisible.png)

## <a name="interactivity-with-multiple-selections-in-grids-and-charts"></a>网格和图表中的多个选择的交互

选择行（或多行）时，查询和指标步骤还可以导出一个或多个参数。

![显示具有多个参数的导出参数设置的屏幕截图。 ](./media/workbooks-interactive/interactivity-export-parameters.png)

1. 在显示网格的查询步骤中，请参阅 "高级设置"。
2. 选中复选 `When items are selected, export parameters` 框。 将显示其他控件。
3. 选中复选 `allow selection of multiple values` 框。
    1. 显示的可视化效果将允许多选和导出参数的值为值的数组，如使用多选择下拉列表参数时。
    2. 如果未选中，则显示可视化将仅考虑最后一个选定的项。 一次仅导出单个值。
4. 对于要导出的每个参数，请使用 "*添加参数*" 按钮。 将显示一个弹出窗口，其中包含要导出的参数的设置。

启用 "单项选择" 后，作者可以指定要导出的原始数据字段。 如果未选择任何内容，则字段包括要使用的参数名称、参数类型和默认值（可选）。

启用多选后，作者指定要导出的原始数据字段。 字段包含参数名称、参数类型、带有 and 分隔符的引号。 当在查询中替换箭头值时，将使用引号和分隔符值将其转换为文本。 如果未选择任何值，则选择 "多个"，默认值为空数组。

> [!NOTE]
> 对于多重选择，将只导出唯一值，你将看不到输出数组值（如 "1，1，2，1"），你将获得 "1，2" 作为输出值。

可以在导出设置中保留 "要导出的字段" 设置为空。 如果执行此操作，数据中的所有可用字段都将导出为 key： value 对的字符串化 JSON 对象。 对于网格和标题，这将是网格中的所有字段。 对于图表，可用字段为 x、y、序列和标签（取决于图表的类型）。

尽管默认行为是将参数作为文本导出，但如果你知道该字段是订阅或资源 ID，请将其用作导出参数类型。 这将允许参数在需要这些类型的参数的地方使用。

## <a name="next-steps"></a>后续步骤

* [开始](workbooks-visualizations.md)详细了解工作簿丰富的可视化效果选项。
* [控制](workbooks-access-control.md)并共享对工作簿资源的访问权限。
