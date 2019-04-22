---
title: 使用 Azure Monitor 工作簿创建交互式报表 |Microsoft Docs
description: 简化复杂的报告使用的预定义和自定义参数化的工作簿的 Azure Monitor 的 Vm。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: magoedte
ms.openlocfilehash: 90c236347380bb5d5e51db56d0f431d2659a7258
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59288706"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>使用 Azure Monitor 工作簿创建交互式报表

工作簿将文本 [记录查询](../log-query/query-language.md)，指标，以及到丰富的交互式报表的参数。 有权访问相同 Azure 资源的其他团队成员都可编辑工作簿。

工作簿是很有帮助的方案例如：

* 当你在事先不知道感兴趣的指标，请浏览你的虚拟机的使用情况：CPU 使用率、 磁盘空间、 内存、 网络依赖关系，等等。与其他使用情况分析工具不同，工作簿可以结合多个类型的可视化效果和分析，非常适合这种自由探索。
* 向你的团队解释最近预配的 VM 的性能如何，通过显示关键计数器和其他日志事件的指标。
* 与团队其他成员共享你的 VM 大小调整试验的结果。 可以解释为文本，使用的目标，然后显示每个使用情况指标和分析查询来评估试验，以及明确标注为每个指标是否上面或下面的目标。
* 报告的 VM，合并数据、 文本说明和后续步骤讨论以在将来防止服务中断的使用情况的发生服务中断的影响。

适用于 Vm 的 azure 监视器包含多个工作簿以帮助你入门，和下表总结了它们。

| 工作簿 | 描述 | 范围 |
|----------|-------------|-------|
| 性能 | 提供了我们的前 N 个列表和利用所有已启用的 Log Analytics 性能计数器的单个工作簿中的图表视图的可自定义版本。| 大规模 |
| 性能计数器 | 前 n 个图表视图跨范围组的性能计数器。 | 大规模 |
| 连接 | 连接提供了对受监视的虚拟机的入站和出站连接的深入视图。 | 大规模 |
| 活动端口 | 提供有关受监视的 Vm 和所选时间范围中的其活动绑定到端口的进程的列表。 | 大规模 |
| 打开端口 | 提供在受监视的 Vm 上打开的端口号和的详细信息的那些打开的端口。 | 大规模 |
| 失败的连接数 | 在受监视的 Vm，失败趋势上显示的失败的连接计数，如果逐渐增加失败的百分比。 | 大规模 |
| 安全和审核 | 你报告总体连接，恶意 IP 终结点所在全局的连接的 TCP/IP 流量分析。  若要启用所有功能，您将需要都启用安全检测。 | 大规模 |
| TCP 流量 | 排名的报表需要在受监视的虚拟机和其发送、 接收，和总网络流量在网格中，显示为趋势线。 | 大规模 |
| 流量比较 | 此工作簿可用于比较的一台计算机或一组计算机的网络流量趋势。 | 大规模 |
| 性能 | 提供我们利用了所有已启用的 Log Analytics 性能计数器的性能视图的可自定义版本。 | 单个 VM | 
| 连接 | 连接提供了从 VM 的入站和出站连接的深入视图。 | 单个 VM |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>从模板或已保存的工作簿开始

工作簿由可单独编辑的图表、表格、文本和输入控件构成的各部分组成。 若要更好地了解工作簿，让我们首先打开一个模板，并引导完成创建自定义工作簿。 

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择“虚拟机”。

3. 从列表中选择一个虚拟机。

4. 在“VM”页上的“监视”部分，选择“见解(预览版)”。

5. 在 VM insights 页面上，选择**性能**或**地图**选项卡，然后选择**View 工作簿**从页面上的链接。 

    ![导航到工作簿的屏幕截图](media/vminsights-workbooks/workbook-option-01.png)

6. 从下拉列表中，选择**转到库**从列表的底部。

    ![工作簿下拉列表的屏幕截图](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    它将启动带有大量预建的工作簿，以帮助您入门的工作簿库。

7. 我们将从“默认模板”开始，位于“快速入门”标题下方。

    ![工作簿库的屏幕截图](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>编辑工作簿部分

工作簿具有两种模式：编辑模式和阅读模式。 在首次启动默认模板工作簿，它将在中打开**编辑模式**。 它显示工作簿，其中包括任何步骤和参数，否则隐藏的所有的内容。 **阅读模式**提供了简化的报表样式视图。 读取模式，可抽象出进入了创建报表，同时仍然只需单击几下即可有的基础结构，需要进行修改时的复杂性。

![Azure 监视的 Vm 的工作簿部分编辑控件](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. 完成后编辑某个部分，单击**完成编辑**左下角的部分。

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

* 我作为增加的网络流量在同一时间段内的 CPU 使用率的如何？
* 上个月的趋势中的可用磁盘空间是多少？
* 我的 VM 过去两周内未遇到网络连接故障数？ 

您还不仅限于查询虚拟机的上下文从启动中的工作簿。 只要您具有对这些资源的访问权限，可以跨多个虚拟机，以及 Log Analytics 工作区中进行查询。

若要将数据从其他 Log Analytics 工作区或从特定的 Application Insights 应用使用包含**工作区**标识符。 若要了解有关跨资源的查询的详细信息，请参阅[官方指南](../log-query/cross-workspace-query.md)。

### <a name="advanced-analytic-query-settings"></a>高级分析查询设置

每个部分都有其自己高级的设置，可通过设置访问![工作簿部分编辑控件](media/vminsights-workbooks/006-settings.png)右侧的图标位于**将参数添加**按钮。

![Azure 监视的 Vm 的工作簿部分编辑控件](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **自定义宽度**    | 使项目任意大小，因此可容纳在单个行，从而可以更好地将图表和表组织成丰富的交互式报表的多个项。  |
| **有条件可见** | 指定隐藏时在读取模式下基于参数的步骤。 |
| **导出参数**| 允许所选的行中的网格或图表以导致更高版本的步骤更改值或变得可见。  |
| **未进行编辑时显示查询** | 当处于阅读模式下显示上面图表或偶数的表的查询。
| **未进行编辑时显示“在分析中打开”按钮** | 将蓝色分析图标添加到图表，以允许一键访问的右上角。|

大多数这些设置都相当直观，但若要了解导出参数，最好查看使用此功能的工作簿。

预生成的工作簿-之一**TCP 流量**，从 VM 连接指标上提供的信息。

该工作簿的第一个部分基于日志查询数据。 第二个部分也基于查询的日志数据，但第一个表中选择某一行以交互方式更新图表的内容：

![Azure 监视的 Vm 的工作簿部分编辑控件](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

行为是可以通过使用**时选择的项，导出参数**高级设置，启用表的日志查询中。

![Azure 监视的 Vm 的工作簿部分编辑控件](media/vminsights-workbooks/009-settings-export.png)

选择行来创建一组值，然后由节标题和图表时，第二个日志查询然后利用导出的值。 如果未选择行，它会隐藏部分标题和图表。 

例如，第二个部分中的隐藏的参数使用网格中选定的行中的以下引用：

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>添加指标部分

指标部分提供完全访问权限，以将 Azure Monitor 指标数据纳入交互式报表。 在 Azure 监视器中的 Vm，预生成的工作簿通常包含分析查询的数据而不是指标数据。  您可以选择创建的工作簿与指标数据，让您充分利用这两项功能的最佳都集中在一个位置。 此外，还能够从任何有权访问的订阅中的资源中提取指标数据。

下面是虚拟机数据拉取到工作簿提供的 CPU 性能的网格可视化效果的示例：

![Azure 监视的 Vm 的工作簿部分编辑控件](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>添加参数部分

工作簿参数使你能够更改工作簿中的值，而无需手动编辑查询或文本部分。 这免除了对了解基础分析查询语言的需求，并极大地扩大了基于工作簿的报表的潜在受众范围。

通过将参数名称放在大括号中（如 ``{parameterName}``），以查询、文本或其他参数部分替换参数值。 参数名称仅限于为 JavaScript 标识符、 字母字符或下划线后, 接字母数字字符或下划线类似的规则。 例如，允许使用 a1，但不允许使用 1a。

参数是线性的，从工作簿的顶部开始并向下转到后续步骤。  更高版本的工作簿中声明的参数可以覆盖之前已声明的参数。 这还允许使用查询来访问中之前定义的参数的值的参数。 在参数步骤本身中，参数也是线性的，从左到右，右侧的参数可以依赖以前在该步骤中声明的参数。
 
有四种不同类型的当前支持的参数：

|                  |      |
| ---------------- |:-----|
| **Text**    | 用户可以编辑文本框中，并可以根据需要提供一个查询来填充默认值。 |
| **下拉列表** | 允许用户从一组值中进行选择。 |
| **时间范围选取器**| 允许用户从一组预定义时间范围值中进行选择，或者从自定义时间范围选取。|
| **资源选取器** | 允许用户从工作簿所选的资源中进行选择。|

### <a name="using-a-text-parameter"></a>使用文本参数

用户在文本框中键入替换直接在查询中，不带任何转义或用引号括起来的值。 如果所需值为字符串，则查询应该在参数周围采用引号（如 '{parameter}'）。

文本参数允许在文本框中，以便任何位置使用的值。 它可以是表名、列名、函数名、运算符等等。文本参数类型已设置**从分析查询获取默认值**，其中，工作簿作者可以使用查询来填充该文本框的默认值。

在使用来自一个日志查询的默认值时，仅第一行 （第 0 行、 第 0 列） 的第一个值用作默认值。 因此建议限制查询，以仅返回一行和一列。 忽略由查询返回的其他任何数据。 

查询返回的任何值都将被直接替换，不带任何转义或引号。 如果查询不返回行，该参数的结果为空字符串（如果参数不是必需的）或未定义（如果参数是必需的）。

### <a name="using-a-drop-down"></a>使用下拉列表

下拉列表中参数类型可让你创建一个下拉列表控件，可以选择一个或多个值。

下拉列表中填充的日志查询或 JSON。 如果查询返回一列，该列中的值将是值和下拉控件中的标签。 如果查询返回两个列，第一列是值和第二列是在下拉列表中显示的标签。 如果查询返回三列，第三列用于指示该下拉列表中的默认选择。 此列可以是任何类型，但最简单的是使用布尔值或数值类型，其中 0 表示 false，1 表示 true。

如果列为字符串类型，null/空字符串被视为 false，而任何其他值被视为 true。 对于单项选择下拉列表中，使用值为 true 的第一个值用作默认选择。  对于多个选择下拉列表中，使用值为 true 的所有值都用作默认选定组。 在下拉列表中的项显示的顺序查询返回的行。 

让我们看看在连接概述报告中提供的参数。 单击编辑符号旁边**方向**。

![Azure 监视的 Vm 的工作簿部分编辑控件](media/vminsights-workbooks/011-workbook-using-dropdown.png)

这将启动**编辑参数**菜单项。

![Azure 监视的 Vm 的工作簿部分编辑控件](media/vminsights-workbooks/012-workbook-edit-parameter.png)

JSON 允许您生成的任意表填充其内容。 例如，以下 JSON 下拉列表中，生成两个值：

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

更适用的示例使用下拉列表来按名称选择从一组性能计数器：

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

该查询将显示如下所示的结果：

![性能计数器下拉列表中](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

下拉列表是用于自定义和创建交互式报表功能非常强大的工具。

### <a name="time-range-parameters"></a>时间范围参数

尽管可以通过下拉列表参数类型制作自己的自定义时间范围参数，但如果不需要相同程度的灵活性，还可使用现成的时间范围参数类型。 

时间范围参数类型包含 15 个默认范围，从 5 分钟到 90 天不等。 此外，还可以通过一个选项，允许自定义时间范围选择，使报表操作员能够选择明确的开始和停止时间范围值。

### <a name="resource-picker"></a>资源选取器

资源选取器参数类型使你能够将报表类型范围限定到特定资源类型。 利用资源选取器类型的预生成工作簿的一个示例是**性能**工作簿。

![工作区下拉列表中](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>保存并与团队共享工作簿

Log Analytics 工作区或虚拟机资源，具体取决于如何访问工作簿库中保存工作簿。 可以将该工作簿保存到**我的报表**部分中给您，或者在专用**共享报表**可供每个人都有权访问该资源访问的部分。 若要查看资源中的所有工作簿，请单击操作栏中的“打开”按钮。

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
若要了解如何使用运行状况的功能，请参阅[查看 Azure VM 运行状况](vminsights-health.md)，或若要查看发现的应用程序依赖关系，请参阅[查看 Azure 监视的 Vm 映射](vminsights-maps.md)。 