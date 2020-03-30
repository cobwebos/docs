---
title: 微软 Azure 数据资源管理器流连接器（预览）使用示例
description: 了解一些常见的 Microsoft 流连接器使用示例。
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/15/2020
ms.openlocfilehash: 759ea9800a0ed051fed887adfb10b06f7a53b72f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79529197"
---
# <a name="microsoft-flow-connector-preview-usage-examples"></a>微软流连接器（预览）使用示例

Azure 数据资源管理器流连接器允许 Azure 数据资源管理器使用[Microsoft Power 自动的流功能](https://flow.microsoft.com/)作为计划或触发任务的一部分自动运行 Kusto 查询和命令。 本文档包含几个常见的 Microsoft Flow 连接器使用示例。

有关详细信息，请参阅[Microsoft 流连接器（预览）。](flow.md)

* [微软流连接器和 SQL](#microsoft-flow-connector-and-sql)
* [将数据推送到 Power BI 数据集](#push-data-to-power-bi-dataset)
* [条件查询](#conditional-queries)
* [通过电子邮件发送多个 Azure 数据资源管理器流程图表](#email-multiple-azure-data-explorer-flow-charts)
* [向不同的联系人发送不同的电子邮件](#send-a-different-email-to-different-contacts)
* [创建自定义 HTML 表](#create-a-custom-html-table)

## <a name="microsoft-flow-connector-and-sql"></a>微软流连接器和 SQL

使用 Microsoft Flow 连接器查询数据并将其聚合到 SQL 数据库中。

> [!Note]
> SQL 插入针对每行单独完成。 仅对少量输出数据使用 Microsoft 流连接器。 

![](./media/flow-usage/flow-sqlexample.png)

> [!IMPORTANT]
> 在 *"群集名称"* 字段中，输入群集 URL。

## <a name="push-data-to-power-bi-dataset"></a>将数据推送到 Power BI 数据集

Microsoft Flow 连接器可与 Power BI 连接器一起使用，将数据从 Kusto 查询推送到 Power BI 流数据集。

1. 创建新的"运行"查询并列出结果操作。
1. 选择“新建步骤”。****
1. 选择 **"添加操作**"并搜索 Power BI。
1. 选择“Power BI”。****
1. 选择**将行添加到数据集**。 

    ![流量功率 BI 连接器](./media/flow-usage/flow-powerbiconnector.png)
1. 输入要推送数据的**工作区**、**数据集**和**表**。
1. 在动态内容对话框中，添加包含数据集架构和相关 Kusto 查询结果的有效负载。

    ![流功率 BI 字段](./media/flow-usage/flow-powerbifields.png)

流会自动为 Kusto 查询结果表的每一行应用 Power BI 操作。 

![每行的流功率 BI 操作](./media/flow-usage/flow-powerbiforeach.png)

## <a name="conditional-queries"></a>条件查询

Kusto 查询的结果可用作下一个流操作的输入或条件。

在下面的示例中，我们查询 Kusto 中最后一天发生的事件。 对于每个已解决的事件，将发布一条松弛消息，并创建推送通知。
对于仍处于活动状态的每个事件，将查询 Kusto 以了解有关类似事件的详细信息。 它将该信息作为电子邮件发送，并打开相关的 TFS 任务。

按照以下说明创建类似的流：

1. 创建新的"运行"查询并列出结果操作。
1. 选择“新建步骤”。****
1. 选择**条件控件**。
1. 从动态内容窗口中选择要用作下一个操作的条件的参数。
1. 选择*关系*和*值*的类型以在给定参数上设置特定条件。

    [![](./media/flow-usage/flow-condition.png "Flow conditions")](./media/flow-usage/flow-condition.png#lightbox)

    Flow 在查询结果表的每一行上应用此条件。
1. 添加条件为真假时的操作。

    [![](./media/flow-usage/flow-conditionactions.png "Flow condition actions")](./media/flow-usage/flow-conditionactions.png#lightbox)

您可以使用 Kusto 查询中的结果值作为下一个操作的输入。 从动态内容窗口中选择结果值。
在下面的示例中，添加了"松弛 - 消息后"操作和可视化工作室 - 创建新的工作项操作，其中包含来自 Kusto 查询的数据。

![松弛 - 消息后操作](./media/flow-usage/flow-slack.png)

![视觉工作室操作](./media/flow-usage/flow-visualstudio.png)

在此示例中，如果事件仍然处于活动状态，请再次查询 Kusto 以获取有关过去如何解决来自同一源的事件的信息。

![流条件查询](./media/flow-usage/flow-conditionquery.png)

> [!IMPORTANT]
> 在 *"群集名称"* 字段中，输入群集 URL。

将此信息可视化为饼图，并将其通过电子邮件发送给团队。

![流条件电子邮件](./media/flow-usage/flow-conditionemail.png)

## <a name="email-multiple-azure-data-explorer-flow-charts"></a>通过电子邮件发送多个 Azure 数据资源管理器流程图表

1. 使用"重复"触发器创建新的 Flow，并定义流的间隔和频率。 
1. 添加新步骤，包含一个或多个 Kusto - 运行查询并可视化结果操作。 

    ![在流中运行多个查询](./media/flow-usage/flow-severalqueries.png)
1. 对于每个 Kusto - 运行查询并可视化结果，请定义以下字段：
    * 群集 URL（在*群集名称*字段中）
    * 数据库名称
    * 查询和图表类型（HTML 表格/饼图/时间图表/条形图/输入自定义值）。

    ![使用多个附件可视化结果](./media/flow-usage/flow-visualizeresultsmultipleattachments.png)

    > [!IMPORTANT]
    > 在 *"群集名称"* 字段中，输入群集 URL。

1. 添加"发送电子邮件"操作。 
    * 在 *"正文"* 字段中，插入所需的正文，以便查询的可视化结果包含在电子邮件正文中。
    * 要向电子邮件添加附件，请添加附件名称和附件内容。
    
    ![通过电子邮件发送多个附件](./media/flow-usage/flow-emailmultipleattachments.png)

结果：

[![](./media/flow-usage/flow-resultsmultipleattachments.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments.png#lightbox)

[![](./media/flow-usage/flow-resultsmultipleattachments2.png "Results of multiple attachments")](./media/flow-usage/flow-resultsmultipleattachments2.png#lightbox)

## <a name="send-a-different-email-to-different-contacts"></a>向不同的联系人发送不同的电子邮件

您可以使用 Microsoft Flow 向不同的联系人发送不同的自定义电子邮件。 电子邮件地址和电子邮件内容是 Kusto 查询的结果。

示例：

![使用 Kusto 查询的动态电子邮件](./media/flow-usage/flow-dynamicemailkusto.png)

> [!IMPORTANT]
> 在 *"群集名称"* 字段中，输入群集 URL。

![流操作中的动态电子邮件](./media/flow-usage/flow-dynamicemail.png)

## <a name="create-a-custom-html-table"></a>创建自定义 HTML 表

您可以使用 Microsoft Flow 创建和使用自定义 HTML 元素，如自定义 HTML 表。

下面的示例演示如何创建自定义 HTML 表。 HTML 表的行将按日志级别着色（与 Azure 数据资源管理器中相同）。

按照以下说明创建类似的流：

1. 创建新的 Kusto - 运行查询和列表结果操作。

    ![列出 HTML 表的结果](./media/flow-usage/flow-listresultforhtmltable.png)

> [!IMPORTANT]
> 在 *"群集名称"* 字段中，输入群集 URL。

1. 循环查询结果并创建 HTML 表正文： 
    1. 要创建变量以保存 HTML 字符串，请选择 **"新建步骤"**
    1. 选择 **"添加操作**"并搜索变量。 
    1. 选择**变量 - 初始化变量**。 
    1. 初始化字符串变量，如下所示：

    ![初始化变量](./media/flow-usage/flow-initializevariable.png)

1. 循环对结果：
    1. 选择“新建步骤”。****
    1. 选择 **"添加操作**"。
    1. 搜索变量。 
    1. 选择**变量 - 追加到字符串变量**。 
    1. 选择之前初始化的变量名称，并使用查询结果创建 HTML 表行。 
    选择查询结果时，将自动添加"应用于每个查询结果"。

    在下面的示例中，`if`表达式用于定义每行的样式：

    ```if(equals(items('Apply_to_each')?['Level'], 'Warning'), 'Yellow', if(equals(items('Apply_to_each')?['Level'], 'Error'), 'red', 'white'))```

    [![](./media/flow-usage/flow-createhtmltableloopcontent.png "Create HTML table loop content")](./media/flow-usage/flow-createhtmltableloopcontent.png#lightbox)

1. 创建完整的 HTML 内容： 
    1. 在"应用于每个操作"之外添加新操作。 
    在下面的示例中，使用的操作是发送电子邮件。
    1. 使用上述步骤中的变量定义 HTML 表。 
    1. 如果要发送电子邮件，请选择 **"显示高级选项**"，并在"是 HTML"下选择"**是**"。

    ![自定义 HTML 表电子邮件](./media/flow-usage/flow-customhtmltablemail.png)

结果：

![自定义 HTML 表电子邮件结果](./media/flow-usage/flow-customhtmltableresult.png)

## <a name="next-steps"></a>后续步骤

了解[Microsoft Azure 资源管理器逻辑应用连接器](https://docs.microsoft.com/azure/kusto/tools/logicapps)，这是作为计划或触发任务的一部分自动运行 Kusto 查询和命令的另一种方法。
