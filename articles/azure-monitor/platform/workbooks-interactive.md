---
title: Azure 监视具有自定义参数的工作簿
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658262"
---
# <a name="interactive-workbooks"></a>交互式工作簿

工作簿允许作者为其消费者创建交互式报告和体验。 交互性在许多方面得到支持。

## <a name="parameter-changes"></a>参数更改
当工作簿用户更新参数时，使用 该参数的任何控件都会自动刷新和重绘以反映新状态。 这是大多数 Azure 门户报告都支持交互性的方式。 工作簿以非常直接的方式提供这一点，用户工作量最小。

在[工作簿中了解有关参数的更多详细信息](workbooks-parameters.md)

## <a name="grid-row-clicks"></a>网格行单击
工作簿允许作者构造方案，其中单击网格中的行会根据行的内容更新后续图表。 

例如，用户可以有一个网格，显示请求列表和某些统计信息（如失败计数）。 他们可以设置它，以便单击与请求对应的行，将导致下面的详细图表更新以筛选到仅该请求。

### <a name="setting-up-interactivity-on-grid-row-click"></a>设置网格行上的交互性单击
1. 通过单击 _"编辑_"工具栏项，将工作簿切换到编辑模式。
2. 使用 _"添加"查询_链接将日志查询控件添加到工作簿。 
3. 选择查询类型作为_日志_、资源类型（例如，应用程序见解）和目标资源。
4. 使用查询编辑器输入 KQL 进行分析
    ```kusto
    requests
    | summarize AllRequests = count(), FailedRequests = countif(success == false) by Request = name
    | order by AllRequests desc    
    ```
5. `Run query`查看结果
6. 单击查询页脚上的 _"高级设置"_ 图标（该图标看起来像齿轮）。 这将打开高级设置窗格 
7. 检查设置：`When an item is selected, export a parameter`
    1. 要导出的字段：`Request`
    2. 参数名称：`SelectedRequest`
    3. 默认值：30`All requests`
    
    ![显示高级编辑器的图像，其中设置将字段导出为参数](./media/workbooks-interactive/advanced-settings.png)

8. 单击 `Done Editing`。
9. 使用步骤 2 和步骤 3 添加另一个查询控件。
10. 使用查询编辑器输入 KQL 进行分析
    ```kusto
    requests
    | where name == '{SelectedRequest}' or 'All Requests' == '{SelectedRequest}'
    | summarize ['{SelectedRequest}'] = count() by bin(timestamp, 1h)
    ```
11. `Run query`以查看结果。
12. 将_可视化更改为_`Area chart`
12. 单击第一个网格中的行。 请注意下面的区域图如何筛选到所选请求。

生成的报表在编辑模式下如下所示：

![显示使用网格行单击创建交互式体验的图像](./media/workbooks-interactive//grid-click-create.png)

下图显示了基于相同原则的读取模式下更精细的交互式报表。 报表使用网格单击来导出参数 - 这些参数又用于两个图表和一个文本块。

![显示使用网格行单击创建交互式体验的图像](./media/workbooks-interactive/grid-click-read-mode.png)

### <a name="exporting-the-contents-of-an-entire-row"></a>导出整行的内容
有时需要导出所选行的全部内容，而不仅仅是特定列。 在这种情况下，保留上述`Field to export`步骤 7.1 中未设置的属性。 工作簿将整个行内容作为 json 导出到参数。 

在引用 KQL 控件上，使用`todynamic`函数解析 json 并访问各个列。

 ## <a name="grid-cell-clicks"></a>网格单元格单击
工作簿允许作者通过称为 的特殊类型的网格列呈现器添加交互性`link renderer`。 链接渲染器根据单元格的内容将网格单元格转换为超链接。 工作簿支持多种链接呈现器 - 包括允许打开资源概述刀片、属性包查看器、应用见解搜索、使用情况、事务跟踪等的功能。

### <a name="setting-up-interactivity-using-grid-cell-clicks"></a>使用网格单元格单击设置交互性
1. 通过单击 _"编辑_"工具栏项，将工作簿切换到编辑模式。
2. 使用 _"添加"查询_链接将日志查询控件添加到工作簿。 
3. 选择查询类型作为_日志_、资源类型（例如，应用程序见解）和目标资源。
4. 使用查询编辑器输入 KQL 进行分析
    ```kusto
    requests
    | summarize Count = count(), Sample = any(pack_all()) by Request = name
    | order by Count desc
    ```
5. `Run query`查看结果
6. 单击 _"列设置"_ 以打开设置窗格。
7. 在 _"列"_ 部分中，设置：
    1. _示例_- 列渲`Link`染器：、查看`Cell Details`打开： 、链接标签：`Sample`
    2. _计数_- 列渲`Bar`染器：、`Blue`调色板： 、最小值：`0`
    3. _请求_- 列渲染器：`Automatic`
    4. 单击"_保存"和"关闭_"以应用更改
8. 单击网格中的`Sample`一个链接。 这将打开一个包含采样请求详细信息的属性窗格。

    ![显示使用网格单元格单击创建交互式体验的图像](./media/workbooks-interactive/grid-cell-click-create.png)

### <a name="link-renderer-actions"></a>链接渲染器操作
| 链接操作 | 单击操作 |
|:------------- |:-------------|
| `Generic Details` | 在属性网格上下文边栏选项卡中显示行值 |
| `Cell Details` | 在属性网格上下文边栏选项卡中显示单元格值。 当单元格包含包含包含信息的动态类型（例如，具有请求属性（如位置、角色实例等）的 json 时非常有用。 |
| `Cell Details` | 在属性网格上下文边栏选项卡中显示单元格值。 当单元格包含包含包含信息的动态类型（例如，具有请求属性（如位置、角色实例等）的 json 时非常有用。 |
| `Custom Event Details` | 使用单元格中的自定义事件 ID （itemId） 打开应用程序见解搜索详细信息 |
| `* Details` | 与自定义事件详细信息类似，但依赖项、异常、页面视图、请求和跟踪除外。 |
| `Custom Event User Flows` | 打开在单元格中的自定义事件名称上旋转的应用程序见解用户流体验 |
| `* User Flows` | 与自定义事件用户流类似，例外、页面视图和请求除外 |
| `User Timeline` | 在单元格中使用用户 ID （user_Id） 打开用户时间线 |
| `Session Timeline` | 打开单元格中值的应用程序见解搜索体验（例如，搜索 abc 中的值的 abc 文本"abc" |
| `Resource overview` | 根据单元格中的资源 ID 值在门户中打开资源概览 |

## <a name="conditional-visibility"></a>条件可见性
工作簿允许用户根据参数的值显示或消失某些控件。 这允许作者根据用户输入或遥测状态使报表看起来不同。 例如，当情况良好时，向消费者显示摘要，但当出现问题时显示全部详细信息。

### <a name="setting-up-interactivity-using-conditional-visibility"></a>使用条件可见性设置交互性
1. 按照本节中`Setting up interactivity on grid row click`的步骤设置两个交互式控件。
2. 在顶部添加新参数：
    1. 名称：`ShowDetails`
    2. 参数类型：`Drop down`
    3. 必填：`checked`
    4. 从：`JSON`
    5. JSON 输入：`["Yes", "No"]`
    6. 保存以提交更改。
3. 将参数值设置为`Yes`
4. 在带有面积图的查询控件中，单击 _"高级设置"_ 图标（齿轮图标）
5. 检查设置`Make this item conditionally visible`
    1. 如果`ShowDetails`参数值`equals`可见此项`Yes`
6. 单击 _"完成编辑_"以提交更改。
7. 单击作业簿工具栏上的 _"完成编辑_"以进入读取模式。
8. 将参数`ShowDetails`的值切换到`No`。 请注意，下面的图表将消失。

下图显示了可见大小写`ShowDetails``Yes`

![显示图表可见条件可见性的图像](./media/workbooks-interactive/conditional-visibility.png)

下图显示了隐藏大小写`ShowDetails``No`

![显示图表隐藏位置的条件可见性的图像](./media/workbooks-interactive/conditional-invisible.png)

## <a name="next-steps"></a>后续步骤


* [开始](workbooks-visualizations.md)了解有关工作簿的许多丰富可视化选项的详细信息。
* [控制和](workbooks-access-control.md)共享对工作簿资源的访问权限。
