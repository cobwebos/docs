---
title: Azure Monitor 包含自定义参数的工作簿
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
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77658262"
---
# <a name="interactive-workbooks"></a>交互式工作簿

工作簿允许作者为其使用者创建交互式报表和体验。 通过多种方式支持交互。

## <a name="parameter-changes"></a>参数更改
当工作簿用户更新参数时，使用该参数的任何控件都会自动刷新并重新绘制，以反映新状态。 这就是大多数 Azure 门户报表支持交互的方式。 工作簿以非常简单的方式提供此项，并可实现最少的用户工作。

了解有关[工作簿中参数的](workbooks-parameters.md)详细信息

## <a name="grid-row-clicks"></a>网格行单击
工作簿允许作者构造在网格中单击某一行时，将根据该行的内容更新后续图表的情况。 

例如，用户可以有一个网格，其中显示请求列表和一些统计信息，如失败计数。 他们可以对其进行设置，以便单击与请求相对应的行，并在更新后生成详细的图表，以筛选出该请求。

### <a name="setting-up-interactivity-on-grid-row-click"></a>设置网格行单击上的交互
1. 单击 "_编辑_" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "_添加查询_" 链接向工作簿添加日志查询控件。 
3. 选择 "_日志_"、"资源类型" （例如 Application Insights）和 "要作为目标的资源" 的查询类型。
4. 使用查询编辑器输入分析的 KQL
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query` 查看结果
6. 单击 "查询" 页脚上的 "_高级设置_" 图标（图标看起来像一个齿轮）。 这将打开 "高级设置" 窗格 
7. 检查设置： "`When an item is selected, export a parameter`
    1. 要导出的字段： `Request`
    2. 参数名称： `SelectedRequest`
    3. 默认值：30`All requests`
    
    ![显示高级编辑器，其中包含用于将字段导出为参数的设置的图像](./media/workbooks-interactive/advanced-settings.png)

8. 单击 `Done Editing`。
9. 使用步骤2和3添加另一个查询控件。
10. 使用查询编辑器输入分析的 KQL
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query` 查看结果。
12. 将_可视化效果_更改为 `Area chart`
12. 单击第一个网格中的行。 请注意下面的面积图如何筛选选定的请求。

在编辑模式下，生成的报表如下所示：

![图像显示使用网格行单击创建交互式体验](./media/workbooks-interactive//grid-click-create.png)

下图显示了基于相同原则的只读模式下更精细的交互式报表。 报表使用网格单击来导出参数，而参数则在两个图表和一个文本块中使用。

![图像显示使用网格行单击创建交互式体验](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>导出整行的内容
有时需要导出所选行的全部内容，而不只是导出特定的列。 在这种情况下，请在上面的步骤7.1 中不设置 `Field to export` 属性。 工作簿会将整行内容导出为作为参数的 json。 

在引用 KQL 控件上，使用 `todynamic` 函数分析 json 并访问各个列。

 ## <a name="grid-cell-clicks"></a>网格单元格单击
工作簿允许作者通过一种称为 `link renderer`的特殊类型的网格列呈现器来添加交互性。 链接呈现器根据单元格的内容将网格单元转换为超链接。 工作簿支持多种类型的链接呈现器，其中包括允许打开资源概述边栏选项卡、属性包查看器、App Insights 搜索、使用情况、事务跟踪等。

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>使用网格单元单击设置交互
1. 单击 "_编辑_" 工具栏项，将工作簿切换到编辑模式。
2. 使用 "_添加查询_" 链接向工作簿添加日志查询控件。 
3. 选择 "_日志_"、"资源类型" （例如 Application Insights）和 "要作为目标的资源" 的查询类型。
4. 使用查询编辑器输入分析的 KQL
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query` 查看结果
6. 单击 "_列设置_" 打开 "设置" 窗格。
7. 在 "_列_" 部分中，设置：
    1. _示例_-列呈现器： `Link`，要打开的视图： `Cell Details`，链接标签： `Sample`
    2. _计数_-列呈现器： `Bar`，调色板： `Blue`，最小值： `0`
    3. _请求_列呈现器： `Automatic`
    4. 单击 "_保存并关闭_" 以应用更改
8. 在网格中单击其中一个 `Sample` 链接。 这将打开一个属性窗格，其中包含抽样请求的详细信息。

    ![图像显示使用网格单元单击创建交互式体验](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>链接呈现器操作
| 链接操作 | 单击时的操作 |
|:------------- |:-------------|
| `Generic Details` | 显示属性网格上下文边栏选项卡中的行值 |
| `Cell Details` | 显示属性网格上下文边栏选项卡中的单元值。 当单元包含包含信息的动态类型（例如，具有位置、角色实例等的请求属性）时，此方法非常有用。 |
| `Cell Details` | 显示属性网格上下文边栏选项卡中的单元值。 当单元包含包含信息的动态类型（例如，具有位置、角色实例等的请求属性）时，此方法非常有用。 |
| `Custom Event Details` | 用单元格中的自定义事件 ID （itemId）打开 Application Insights 搜索详细信息 |
| `* Details` | 类似于自定义事件详细信息，依赖项、异常、页面视图、请求和跟踪除外。 |
| `Custom Event User Flows` | 打开对单元中自定义事件名称透视的 Application Insights 用户流体验 |
| `* User Flows` | 类似于自定义事件用户流除了异常、页面视图和请求之外 |
| `User Timeline` | 用单元格中的用户 ID （user_Id）打开用户时间线 |
| `Session Timeline` | 打开单元中的值 Application Insights 搜索体验（例如，搜索文本 "abc"，其中 abc 是单元中的值） |
| `Resource overview` | 根据单元中的资源 ID 值，在门户中打开资源的概述 |

## <a name="conditional-visibility"></a>条件可见性
工作簿允许用户根据参数的值显示或隐藏某些控件。 这使作者可以根据用户输入或遥测状态显示不同的报表。 例如，当事情良好但出现问题时显示完整详细信息时，使用者只显示一个摘要。

### <a name="setting-up-interactivity-using-conditional-visibility"></a>使用条件可见性设置交互
1. 按照 `Setting up interactivity on grid row click` 部分中的步骤设置两个交互控件。
2. 在顶部添加一个新参数：
    1. 名称：`ShowDetails`
    2. 参数类型： `Drop down`
    3. 必需： `checked`
    4. 从以下来源获取数据： `JSON`
    5. JSON 输入： `["Yes", "No"]`
    6. 保存以提交更改。
3. 将参数值设置为 `Yes`
4. 在带有面积图的查询控件中，单击 "_高级设置_" 图标（齿轮图标）
5. 检查设置 `Make this item conditionally visible`
    1. 如果 `ShowDetails` 参数值 `equals`，则此项可见 `Yes`
6. 单击 "_完成编辑_" 以提交更改。
7. 单击 "工作簿" 工具栏上的 "_完成编辑_" 以进入读取模式。
8. 将参数 `ShowDetails` 的值切换为 `No`。 请注意，下图将消失。

下图显示了 `ShowDetails` `Yes` 的可见情况

![显示图表可见位置的条件可见性的图像](./media/workbooks-interactive/conditional-visibility.png)

下图显示了 `ShowDetails` `No` 的隐藏大小写

![显示隐藏图表的条件可见性的图像](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>后续步骤


* [开始](workbooks-visualizations.md)了解有关工作簿许多丰富可视化效果选项的详细信息。
* [控制](workbooks-access-control.md)和共享对工作簿资源的访问权限。
