---
title: 使用 Azure Monitor 工作簿创建交互式报表 | Microsoft docs
description: 使用预先构建的自定义参数化工作簿简化复杂的报表
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.openlocfilehash: bbbf511286c63acf4a939e0a0e7d9c3dc9efa75b
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899378"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>使用 Azure Monitor 工作簿创建交互式报表

工作簿可将 [分析查询](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)、Azure 指标和参数合并到丰富的交互式报表中。 有权访问相同 Azure 资源的其他团队成员都可编辑工作簿。

工作簿对以下情况非常有用，例如：

* 如果事先不知道感兴趣的指标，请浏览应用的使用情况：用户数、保留率、转换率等。与其他使用情况分析工具不同，工作簿允许您组合多种类型的可视化和分析，使其适合这种自由格式浏览。
* 通过显示主要交互的用户计数和其他指标，向团队成员说明新发布的功能的作用。
* 在应用中与团队的其他成员共享 A/B 实验的结果。 可以用文本解释实验的目标，然后展示用于评估实验的每个使用情况指标和 Analytics 查询，以及说明每个指标是否高于或低于目标的标注。
* 结合数据、文本说明和后续步骤讨论，报告故障对应用使用的影响，从而防止未来发生故障。

## <a name="starting-with-a-template-or-saved-workbook"></a>从模板或已保存的工作簿开始

工作簿由可单独编辑的图表、表格、文本和输入控件构成的各部分组成。 若要更好地了解工作簿，最好是打开一个。 

在应用的 Application Insights 体验中，选择左侧菜单中的“工作簿”。

![导航到工作簿的屏幕截图](./media/usage-workbooks/001-workbooks.png)

这将启动包含大量预先构建工作簿的工作簿库，以帮助你入门。

![工作簿库的屏幕截图](./media/usage-workbooks/002-workbook-gallery.png)

我们将从“默认模板”开始，位于“快速入门”标题下方。

![工作簿库的屏幕截图](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>编辑、重新排列、克隆和删除工作簿部分

工作簿具有两种模式：编辑模式和阅读模式。 首次启动默认工作簿时，它将在编辑模式下打开。 这将显示工作簿的所有内容，其中包括以任何方式隐藏的任何步骤和参数。 **阅读模式**提供了简化的报表样式视图。 这使你能够免去创建报表时所遇到的复杂性，同时仍保留相应的基础机制，即只需几次单击即可进行修改。

![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/editing-controls-new.png)

1. 完成编辑某个部分后，点击该部分左下角的“完成编辑”。

2. 创建某个部分的副本，请单击“克隆此部分”图标。 创建副本是迭代查询的绝佳方式，不会丢失以前的迭代。

3. 要上移工作簿中的某个部分，请单击“上移”或“下移”图标。

4. 若要永久删除某个部分，请单击“删除”图标。

## <a name="adding-text-and-markdown-sections"></a>添加文本和 Markdown 部分

为工作簿添加标题、说明和注释有助于叙述一组表和图表。 工作簿中的文本部分支持用于设置文本格式的 [Markdown 语法](https://daringfireball.net/projects/markdown/)，如标题、粗体、斜体和点符列表。

若要将文本部分添加到工作簿，使用工作簿底部或任何部分底部的“添加文本”按钮。

## <a name="adding-query-sections"></a>添加查询部分

![工作簿中的查询部分](./media/usage-workbooks/analytics-section-new.png)

若要将查询部分添加到工作簿，使用工作簿底部或任何部分底部的“添加查询”按钮。

查询部分非常灵活，可用于回答以下问题：

* 使用率下降的同一时段内，网站引发了多少异常？
* 用户查看某个页面的页面加载时间分布是什么样的？
* 有多少用户在网站上查看了某组页面，而没有查看其它组页面？ 这有助于了解你是否有使用不同网站功能子集的用户群集（在 [Kusto 查询语言](/azure/kusto/query/)中使用具有 `kind=leftanti` 修饰符的 `join` 运算符）。

此外，你还不仅限于通过工作簿启动的应用程序的上下文中进行查询。 只要你有权访问这些资源，就可跨多个受 Application Insights 监视的应用，以及 Log Analytics 工作区进行查询。

若要从其他外部 Application Insights 资源中查询，请使用 app 标识符。

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

此查询结合来自三个不同应用程序的请求。 名为 app01 的应用，名为 app02 的应用，以及本地 Application Insights 资源的请求。

若要从外部 Log Analytics 工作区提取数据，请使用 workspace 标识符。

若要了解有关跨资源查询的详细信息，请参阅[官方指南](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search)。

### <a name="advanced-analytic-query-settings"></a>高级分析查询设置

每个部分都有自己的高级设置，可通过位于“添加参数”按钮右侧的设置图标 ![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/005-settings.png)访问。

![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **自定义宽度**    | 此设置可将某项设置为任意大小，以便可将许多项容纳在单行中，使你更好地将图表和表格整理到丰富的交互式报表中。  |
   | **有条件可见** | 使用此选项在阅读模式下根据参数隐藏步骤。 |
   | **导出参数**| 这允许网格或图表中的所选行能够导致后续步骤更改值或变得可见。  |
   | **未进行编辑时显示查询** | 这将在图表或表格上方显示查询，即便处于阅读模式也是如此。
   | **未进行编辑时显示“在分析中打开”按钮** | 这将向图表的右侧添加蓝色的“分析”图标，以允许单击访问。|

大多数这些设置都相当直观，但若要了解导出参数，最好查看使用此功能的工作簿。

其中一个预先构建的工作簿可提供有关活动用户的信息。

工作簿的第一部分基于分析查询数据：

![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/003-active-users.png)

第个部分也基于分析查询数据，但在第一个表中选择某一行将交互更新图表的内容：

![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/004-active-users-trend.png)

 这可以通过使用在表格的分析查询中启用的高级设置“选择项目后，导出参数”实现。

![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/007-settings-export.png)

然后，第二个分析查询将在选择某行时利用导出的值。 如果未选择行，则默认为表示总体值的行。 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>添加指标部分

指标部分提供完全访问权限，以将 Azure Monitor 指标数据纳入交互式报表。 许多预构建的工作簿将同时包含分析查询数据和指标数据，使你能够在一处充分利用这两种功能。 此外，还能够从任何有权访问的订阅中的资源中提取指标数据。

下例是关于被拉取到工作簿中以提供 CPU 性能的网格可视化效果的虚拟机数据：

![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>添加参数部分

工作簿参数使你能够更改工作簿中的值，而无需手动编辑查询或文本部分。  这免除了对了解基础分析查询语言的需求，并极大地扩大了基于工作簿的报表的潜在受众范围。

通过将参数名称放在大括号中（如 ``{parameterName}``），以查询、文本或其他参数部分替换参数值。  参数名称限于类似 JavaScript 标识符的规则，基本上是字母字符或下划线，后接字母数字字符或下划线。 例如，允许使用 a1，但不允许使用 1a。

参数是线性的，从工作簿的顶部开始并向下转到后续步骤。  工作簿中稍后声明的参数可以替代前面声明的参数。  此外，这还允许使用查询的参数访问前面定义的参数值。  在参数步骤本身中，参数也是线性的，从左到右，右侧的参数可以依赖以前在该步骤中声明的参数。
 
当前支持四种不同类型的参数：

  |         |          |
   | ---------------- |:-----|
   | **Text**    | 用户将编辑文本框，并可以选择提供一个查询用于填充默认值。 |
   | **下拉列表** | 用户将从一组值中选择。 |
   | **时间范围选取器**| 用户将从一组预定义的时间范围值中选择，或者从自定义时间范围中选择。|
   | **资源选取器** | 用户将从为工作簿所选资源中选择。|

### <a name="using-a-text-parameter"></a>使用文本参数

用户在文本框中输入的值将直接在查询中替换，不带任何转义或引号。 如果所需值为字符串，则查询应该在参数周围采用引号（如 '{parameter}'）。

这样，文本框中的值可在任意位置使用。 它可以是表名、列名、函数名、运算符等等。

文本参数类型包含“从分析查询获取默认值”设置，使工作簿作者能够使用查询填充该文本框的默认值。

使用分析查询中的默认值时，仅第一行的第一个值（行 0，列 0）用作默认值。 因此建议限制查询，以仅返回一行和一列。 忽略由查询返回的其他任何数据。 

查询返回的任何值都将被直接替换，不带任何转义或引号。 如果查询不返回行，该参数的结果为空字符串（如果参数不是必需的）或未定义（如果参数是必需的）。

### <a name="using-a-dropdown"></a>使用下拉列表

下拉列表参数类型可让你创建一个下拉列表控件，支持选择一个或多个值。

下拉列表由分析查询进行填充。 如果查询返回一列，则该列中的值同时为下拉列表控件中的值和标签。 如果查询返回两列，第一列为值，第二列为下拉列表中显示的标签。  如果查询返回三列，则第三列用于指示该下拉列表中的默认选择。  此列可以是任何类型，但最简单的是使用布尔值或数值类型，其中 0 表示 false，1 表示 true。

 如果列为字符串类型，null/空字符串被视为 false，而任何其他值被视为 true。 对于单项选择下拉列表，值为 true 的第一个值用作默认选择。  对于多项选择下拉列表，值为 true 的所有值用作默认选择集。 下拉列表中的项以查询返回行的任何顺序显示。 

让我们看看活动用户报表中提供的参数。 单击“时间范围”旁边的编辑符号。

![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/009-time-range.png)

这将启动“编辑参数”菜单项：

![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/010-time-range-edit.png)

查询使用称为数据表的分析查询语言功能，使用户能够生成任意表，表中全是无中生有的内容。 例如，以下分析查询：

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

生成结果：

![Application Insights 工作簿部分编辑控件](./media/usage-workbooks/011-data-table.png)

更适用的示例是使用下拉列表按名称从一组国家/地区中进行选择：

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

该查询将显示如下所示的结果：

![国家/地区下拉列表](./media/usage-workbooks/012-country-dropdown.png)

下拉列表是极其强大的工具，可用于自定义和创建交互式报表。

### <a name="time-range-parameters"></a>时间范围参数

尽管可以通过下拉列表参数类型制作自己的自定义时间范围参数，但如果不需要相同程度的灵活性，还可使用现成的时间范围参数类型。 

时间范围参数类型包含 15 个默认范围，从 5 分钟到 90 天不等。 此外，还可以通过一个选项，允许自定义时间范围选择，使报表操作员能够选择明确的开始和停止时间范围值。

### <a name="resource-picker"></a>资源选取器

资源选取器参数类型使你能够将报表类型范围限定到特定资源类型。 Failure Insights 工作簿就是利用资源选取器类型的一个预先构建工作簿示例。

![国家/地区下拉列表](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>保存并与团队共享工作簿

无论是在私有的“我的报表”部分中，还是在有权访问 Application Insights 资源的每个人均可访问的“共享报表”部分中，工作簿都保存在 Application Insights 内。 若要查看资源中的所有工作簿，请单击操作栏中的“打开”按钮。

共享目前在“我的报表”中的工作簿：

1. 在操作栏中，单击“打开”
2. 单击要共享的工作簿旁的“...”按钮
3. 单击“移到共享报表”。

若要通过链接或电子邮件共享工作簿，请单击操作栏中的“共享”。 请记住，链接的收件人需要在 Azure 门户中访问此资源才能查看该工作簿。 若要进行编辑，收件人至少需要资源的参与者权限。

将工作簿的链接固定到 Azure 仪表板：

1. 在操作栏中，单击“打开”
2. 单击需固定的工作簿旁的“...”按钮
3. 单击“固定到仪表板”。

## <a name="contributing-workbook-templates"></a>参与创建工作簿模板

你是否创建了令人惊叹的工作簿模板，并且想要与社区共享？ 若要了解详细信息，请访问 [GitHub 存储库](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md)。

## <a name="next-steps"></a>后续步骤
- 若要启用使用体验，请首先发送[自定义事件](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent)或[页面视图](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views)。
- 如果已经发送自定义事件或页面视图，请浏览使用情况工具，了解用户如何使用服务。
    - [用户、会话、事件](../../azure-monitor/app/usage-segmentation.md)
    - [漏斗图](../../azure-monitor/app/usage-funnels.md)
    - [保留](../../azure-monitor/app/usage-retention.md)
    - [用户流](../../azure-monitor/app/usage-flows.md)
    - [添加用户上下文](../../azure-monitor/app/usage-send-user-context.md)