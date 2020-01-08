---
title: 用于 VM 的 Azure Monitor 工作簿创建交互式报表 |Microsoft Docs
description: 通过用于 VM 的 Azure Monitor 的预定义和自定义参数化工作簿，简化复杂报表。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: b89e61f0c268df0ed6dd69ae1c6d97f00678fc32
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75365914"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>用于 VM 的 Azure Monitor 工作簿创建交互式报表

工作簿将文本、 [日志查询](../log-query/query-language.md)、指标和参数合并到丰富的交互式报表中。 有权访问相同 Azure 资源的其他团队成员都可编辑工作簿。

工作簿适用于如下方案：

* 如果事先不知道感兴趣的指标，请浏览虚拟机的使用情况： CPU 使用率、磁盘空间、内存、网络依赖项等。与其他使用情况分析工具不同，工作簿允许您组合多种类型的可视化和分析，使其适合这种自由格式浏览。
* 通过显示密钥计数器和其他日志事件的指标，向团队说明最近预配的 VM 的执行方式。
* 与团队的其他成员共享 VM 调整大小试验的结果。 您可以用文本解释实验的目标，然后显示用于评估实验的每个使用情况指标和 analytics 查询，以及每个指标是否高于或低于目标的明确调用。
* 报告使用 VM 的中断的影响，组合数据、文本说明，并讨论后续步骤以防止将来出现故障。

用于 VM 的 Azure Monitor 包括多个工作簿以使你入门，下表对它们进行了总结。

| 工作簿 | Description | 范围 |
|----------|-------------|-------|
| 性能 | 在单个工作簿中提供了一个可自定义的版本，其中包含已启用的所有 Log Analytics 性能计数器。| 大规模 |
| 性能计数器 | 跨各种性能计数器的前 N 个图表视图。 | 大规模 |
| 连接 | 连接提供来自受监视 Vm 的入站和出站连接的深入视图。 | 大规模 |
| 活动端口 | 提供已绑定到所选时间范围内受监视 Vm 上的端口的进程的列表。 | 大规模 |
| 打开端口 | 提供受监视的 Vm 上打开端口的数量，以及这些打开端口的详细信息。 | 大规模 |
| 失败的连接数 | 显示受监视 Vm 上的失败连接数、失败趋势以及失败百分比是否随着时间推移而增加。 | 大规模 |
| 安全和审核 | 对在总体连接上报告的 TCP/IP 流量进行的分析，恶意连接，其中的 IP 终结点在全局位置。  若要启用所有功能，你将需要启用安全检测。 | 大规模 |
| TCP 流量 | 已监视的 Vm 的排名报表，并在网格中发送、接收和总网络流量，并显示为趋势线。 | 大规模 |
| 流量比较 | 使用此工作簿可以比较单个计算机或一组计算机的网络流量趋势。 | 大规模 |
| 性能 | 提供可自定义的性能视图版本，利用已启用的所有 Log Analytics 性能计数器。 | 单个 VM | 
| 连接 | 连接提供来自 VM 的入站和出站连接的深入视图。 | 单个 VM |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>从模板或已保存的工作簿开始

工作簿由可单独编辑的图表、表格、文本和输入控件构成的各部分组成。 为了更好地理解工作簿，我们首先打开一个模板并演练如何创建自定义工作簿。 

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 选择“虚拟机”。

3. 从列表中选择一个虚拟机。

4. 在“VM”页上的“监视”部分，选择“见解(预览版)”。

5. 在 "VM insights" 页上，选择 "**性能**" 或 "**映射**" 选项卡，然后从页面上的链接中选择 "**查看工作簿**"。 

    ![导航到工作簿的屏幕截图](media/vminsights-workbooks/workbook-option-01.png)

6. 从下拉列表中，选择 "从列表底部**中转到库**"。

    ![工作簿下拉列表的屏幕截图](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    它使用许多预生成的工作簿启动工作簿库，以帮助你入门。

7. 我们将从“默认模板”开始，位于“快速入门”标题下方。

    ![工作簿库的屏幕截图](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>编辑工作簿部分

工作簿具有两种模式：编辑模式和阅读模式。 首次启动默认模板工作簿时，它将在**编辑模式下**打开。 它显示工作簿的所有内容，包括其他隐藏的步骤和参数。 **阅读模式**提供了简化的报表样式视图。 使用 "读取" 模式，可以抽象地消除正在创建报表的复杂性，同时在需要修改时只需单击几下即可。

![用于 VM 的 Azure Monitor 工作簿部分编辑控件](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. 编辑完某个部分后，请单击部分左下角的 "**完成编辑**"。

2. 创建某个部分的副本，请单击“克隆此部分”图标。 创建副本是迭代查询的绝佳方式，不会丢失以前的迭代。

3. 要上移工作簿中的某个部分，请单击“上移”或“下移”图标。

4. 若要永久删除某个部分，请单击“删除”图标。

## <a name="adding-text-and-markdown-sections"></a>添加文本和 Markdown 部分

为工作簿添加标题、说明和注释有助于叙述一组表和图表。 工作簿中的文本部分支持用于设置文本格式的 [Markdown 语法](https://daringfireball.net/projects/markdown/)，如标题、粗体、斜体和点符列表。

若要将文本部分添加到工作簿，使用工作簿底部或任何部分底部的“添加文本”按钮。

## <a name="adding-query-sections"></a>添加查询部分

![工作簿中的查询部分](media/vminsights-workbooks/005-workbook-query-section.png)

若要将查询部分添加到工作簿，使用工作簿底部或任何部分底部的“添加查询”按钮。

查询部分非常灵活，可用于回答以下问题：

* 我的 CPU 利用率在同一时间段内作为网络流量增加的情况如何？
* 上个月的可用磁盘空间趋势如何？
* 过去两周内我的 VM 体验有多少网络连接失败？ 

你也不仅限于从从中启动工作簿的虚拟机的上下文进行查询。 你可以跨多个虚拟机和 Log Analytics 工作区进行查询，前提是你有权访问这些资源。

使用**工作区**标识符包含来自其他 Log Analytics 工作区或从特定 Application Insights 应用程序的数据。 若要了解有关跨资源查询的详细信息，请参阅[官方指导](../log-query/cross-workspace-query.md)。

### <a name="advanced-analytic-query-settings"></a>高级分析查询设置

每个部分都有其自己的高级设置，可通过 "**添加参数**" 按钮右侧](media/vminsights-workbooks/006-settings.png) 图标的 "设置" ![工作簿 "部分的" 编辑控件 "进行访问。

![用于 VM 的 Azure Monitor 工作簿部分编辑控件](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **自定义宽度**    | 使某一项成为任意大小，因此您可以将多个项放在单个行上，从而可以更好地将您的图表和表组织为丰富的交互式报表。  |
| **有条件可见** | 指定在读取模式下隐藏基于参数的步骤。 |
| **导出参数**| 允许网格或图表中选定的行导致后续步骤更改值或变为可见。  |
| **未进行编辑时显示查询** | 即使处于读取模式下，也会在图表或表上方显示查询。
| **未进行编辑时显示“在分析中打开”按钮** | 将蓝色分析图标添加到图表的右角，以允许一键式访问。|

大多数这些设置都相当直观，但若要了解导出参数，最好查看使用此功能的工作簿。

一个预构建的工作簿- **TCP 流量**，提供有关 VM 中的连接指标的信息。

工作簿的第一部分基于日志查询数据。 第二部分也基于日志查询数据，但在第一个表中选择一行将以交互方式更新图表内容：

![用于 VM 的 Azure Monitor 工作簿部分编辑控件](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

此行为可以通过使用 "**当项处于选定状态时"，导出参数**"高级设置"，该参数在表的日志查询中已启用。

![用于 VM 的 Azure Monitor 工作簿部分编辑控件](media/vminsights-workbooks/009-settings-export.png)

然后，第二个日志查询利用导出的值来创建一组值，然后由节标题和图表使用。 如果未选择任何行，则会隐藏节标题和图表。 

例如，第二部分中的 hidden 参数使用网格中选定的行的以下引用：

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>添加指标部分

指标部分提供完全访问权限，以将 Azure Monitor 指标数据纳入交互式报表。 在用于 VM 的 Azure Monitor 中，预先生成的工作簿通常包含分析查询数据，而不包含度量值数据。  您可以选择创建包含指标数据的工作簿，这样就可以在一个位置充分利用这两种功能的优势。 此外，还能够从任何有权访问的订阅中的资源中提取指标数据。

下面是将虚拟机数据拉取到工作簿中以提供 CPU 性能的网格可视化的示例：

![用于 VM 的 Azure Monitor 工作簿部分编辑控件](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>添加参数部分

工作簿参数使你能够更改工作簿中的值，而无需手动编辑查询或文本部分。 这免除了对了解基础分析查询语言的需求，并极大地扩大了基于工作簿的报表的潜在受众范围。

通过将参数名称放在大括号中（如 ``{parameterName}``），以查询、文本或其他参数部分替换参数值。 参数名称限制为类似的规则，如 JavaScript 标识符、字母字符或下划线，后跟字母数字字符或下划线。 例如，允许使用 a1，但不允许使用 1a。

参数是线性的，从工作簿的顶部开始并向下转到后续步骤。  稍后在工作簿中声明的参数可以重写之前声明的参数。 这还允许使用查询从前面定义的参数访问值的参数。 在参数步骤本身中，参数也是线性的，从左到右，右侧的参数可以依赖以前在该步骤中声明的参数。
 
目前支持四种不同类型的参数：

|                  |      |
| ---------------- |:-----|
| **Text**    | 允许用户编辑文本框，还可以选择提供查询来填充默认值。 |
| **下拉列表** | 允许用户从一组值中进行选择。 |
| **时间范围选取器**| 允许用户从一组预定义的时间范围值中进行选择，或从自定义时间范围中进行选择。|
| **资源选取器** | 允许用户从为工作簿选择的资源中进行选择。|

### <a name="using-a-text-parameter"></a>使用文本参数

用户在文本框中键入的值将在查询中直接替换，无转义或引用。 如果所需值为字符串，则查询应该在参数周围采用引号（如 '{parameter}'）。

Text 参数允许在任何位置使用文本框中的值。 它可以是表名称、列名称、函数名称、运算符等。 Text 参数类型有一个 "**通过 analytics 查询获取默认值**" 选项，该选项允许工作簿作者使用查询来填充该文本框的默认值。

当使用日志查询中的默认值时，只有第一行的第一个值（行0，列0）用作默认值。 因此建议限制查询，以仅返回一行和一列。 忽略由查询返回的其他任何数据。 

查询返回的任何值都将被直接替换，不带任何转义或引号。 如果查询不返回行，该参数的结果为空字符串（如果参数不是必需的）或未定义（如果参数是必需的）。

### <a name="using-a-drop-down"></a>使用下拉

利用 dropdown 参数类型，您可以创建一个下拉控件，允许选择一个或多个值。

下拉列表由日志查询或 JSON 填充。 如果查询返回一列，则该列中的值将是下拉控件中的值和标签。 如果查询返回两个列，则第一列是值，第二列是下拉列表中显示的标签。 如果查询返回三列，则第三列用于指示该下拉列表中的默认选择。 此列可以是任何类型，但最简单的是使用布尔值或数值类型，其中 0 表示 false，1 表示 true。

如果列为字符串类型，null/空字符串被视为 false，而任何其他值被视为 true。 对于单项选择下拉选项，将使用值为 true 的第一个值作为默认选项。  对于多重选择下拉选项，所有值为 true 的值将用作默认选定集。 下拉箭头中的项按查询返回的行的顺序显示。 

让我们看看 "连接概述" 报告中的参数。 单击 "**方向**" 旁的 "编辑符号"。

![用于 VM 的 Azure Monitor 工作簿部分编辑控件](media/vminsights-workbooks/011-workbook-using-dropdown.png)

这将启动**编辑参数**菜单项。

![用于 VM 的 Azure Monitor 工作簿部分编辑控件](media/vminsights-workbooks/012-workbook-edit-parameter.png)

JSON 允许生成使用内容填充的任意表。 例如，下面的 JSON 在下拉中生成两个值：

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

更适用的示例是使用下拉名称按名称从一组性能计数器中进行选择：

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

该查询将显示如下所示的结果：

![性能计数器下拉列表](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

下拉项是非常强大的工具，可用于自定义和创建交互式报表。

### <a name="time-range-parameters"></a>时间范围参数

尽管可以通过下拉列表参数类型制作自己的自定义时间范围参数，但如果不需要相同程度的灵活性，还可使用现成的时间范围参数类型。 

时间范围参数类型包含 15 个默认范围，从 5 分钟到 90 天不等。 此外，还可以通过一个选项，允许自定义时间范围选择，使报表操作员能够选择明确的开始和停止时间范围值。

### <a name="resource-picker"></a>资源选取器

资源选取器参数类型使你能够将报表类型范围限定到特定资源类型。 **性能**工作簿是利用资源选取器类型的预生成工作簿的一个示例。

![工作区下拉列表](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>保存并与团队共享工作簿

工作簿保存在 Log Analytics 工作区或虚拟机资源中，具体取决于您访问工作簿库的方式。 可以将工作簿保存到专用于你或 "**共享报表**" 部分的**我的报表**节中，每个人都可以访问该资源。 若要查看资源中的所有工作簿，请单击操作栏中的“打开”按钮。

共享目前在“我的报表”中的工作簿：

1. 在操作栏中，单击“打开”
2. 单击要共享的工作簿旁的“...”按钮
3. 单击“移到共享报表”。

若要通过链接或电子邮件共享工作簿，请单击操作栏中的“共享”。 请记住，链接的收件人需要在 Azure 门户中访问此资源才能查看该工作簿。 若要进行编辑，收件人至少需要资源的参与者权限。

将工作簿的链接固定到 Azure 仪表板：

1. 在操作栏中，单击“打开”
2. 单击需固定的工作簿旁的“...”按钮
3. 单击“固定到仪表板”。

## <a name="next-steps"></a>后续步骤

- 若要确定限制和总体 VM 性能，请参阅[查看 AZURE Vm 性能](vminsights-performance.md)。

- 若要了解已发现的应用程序依赖关系，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
