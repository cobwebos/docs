---
title: 使用 Microsoft Flow自动执行 Azure Application Insights 过程
description: 了解如何通过 Microsoft Flow 使用 Application Insights 连接器快速自动执行可重复的过程。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/29/2019
ms.openlocfilehash: 7d9c966051c2756d7936fa079a0aac68e694425d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407547"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>使用 Microsoft Flow 的连接器自动执行 Azure Application Insights 过程

你是否发现自己曾对遥测数据重复运行相同的查询，用于检查你的服务是否正常运行？ 是否希望自动执行这些查询以查找趋势和异常，然后针对这些信息构建你自己的工作流？ 适用于 Microsoft Flow 的 Azure 应用程序 Insights 连接器是用于实现这些目的的合适工具。

通过这种集成，现在无需编写任何代码就可自动执行许多过程。 使用 Application Insights 操作创建流后，它自动运行 Application Insights Analytics 查询。

也可以添加其他操作。 Microsoft Flow 可以实现数百个操作。 例如，可以使用 Microsoft Flow 自动发送电子邮件通知，或在 Azure DevOps 中创建 bug。 还可使用适用于 Microsoft Flow 连接器的众多[模板](https://ms.flow.microsoft.com/connectors/shared_applicationinsights/?slug=azure-application-insights)之一。 这些模板可加快创建流的过程。

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). -->

## <a name="create-a-flow-for-application-insights"></a>创建 Application Insights 流

在本教程中，您将学习如何创建一个流，该流使用分析 autocluster 算法对 web 应用程序的数据中的属性进行分组。 此流通过电子邮件自动发送结果，这只是如何结合使用 Microsoft Flow 和 Application Insights Analytics 的一个示例。

### <a name="step-1-create-a-flow"></a>步骤 1：创建流

1. 登录到 [Microsoft Flow](https://flow.microsoft.com)，然后选择“我的流”。
2. 单击 "**新建**"，然后单击 "**计划-从空白**"。

    ![从计划的空白创建新流](./media/automate-with-flow/1-create.png)

### <a name="step-2-create-a-trigger-for-your-flow"></a>步骤 2：为流创建触发器

1. 在弹出项 "**生成计划的流**" 中，填写流的名称和希望流运行的频率。

    ![通过输入频率和间隔设置计划重复周期](./media/automate-with-flow/2-schedule.png)

1. 单击“创建”。

### <a name="step-3-add-an-application-insights-action"></a>步骤 3：添加 Application Insights 操作

1. 搜索**Application Insights**。
2. 单击 " **Azure 应用程序 Insights-可视化分析查询**"。

    ![选择操作： Azure 应用程序 Insights 可视化分析查询](./media/automate-with-flow/3-visualize.png)

3. 选择“新建步骤”。

### <a name="step-4-connect-to-an-application-insights-resource"></a>步骤 4：连接到 Application Insights 资源

若要完成此步骤，需要为资源提供应用程序 ID 和 API 密钥。 可从 Azure 门户检索它们，如下图所示：

![Azure 门户中的应用程序 ID](./media/automate-with-flow/5apiaccess.png)

![Azure 门户中的 API 密钥](./media/automate-with-flow/6apikey.png)

为你的连接提供一个名称，并提供应用程序 ID 和 API 密钥。

   ![Microsoft Flow 连接窗口](./media/automate-with-flow/4-connection.png)

如果 "连接" 框不会立即显示，而是直接进入查询，请单击框右上角的省略号。 然后选择 "我的连接" 或使用现有的连接。

单击“创建”。

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>步骤 5：指定 Analytics 查询和图表类型
此查询示例选择最后一天内失败的请求，并将它们与操作过程中发生的异常相关联。 Analytics 根据 operation_Id 标识符关联异常。 然后，该查询使用 autocluster 算法细分结果。

创建自己的查询时，请在将其添加到流之前，验证它们是否可在 Analytics 中正常运行。

- 添加以下 Analytics 查询，并选择 HTML 表图表类型。 然后选择 "**新建步骤**"。

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```
    
    ![Analytics 查询配置窗口](./media/automate-with-flow/5-query.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>步骤 6：配置流以发送电子邮件

1. 搜索“Office 365 Outlook”。
2. 单击“Office 365 Outlook - 发送电子邮件”。

    ![Office 365 Outlook 选择窗口](./media/automate-with-flow/6-outlook.png)

1. 在 "**发送电子邮件**" 窗口中：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 键入收件人的电子邮件地址。

   b.保留“数据库类型”设置，即设置为“共享”。 键入电子邮件的主题。

   c. 单击“正文”框中的任意位置，然后在右侧打开的动态内容菜单中选择“正文”。

   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 选择 "**显示高级选项**"

1. 在 "动态内容" 菜单上：

    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“附件名称”。

    b.保留“数据库类型”设置，即设置为“共享”。 选择“附件内容”。
    
    c. 在“是否为 HTML”框中选择“是”。

    ![Office 365 Outlook 配置](./media/automate-with-flow/7-email.png)

### <a name="step-7-save-and-test-your-flow"></a>步骤 7：保存并测试流

单击“ **保存**”。

可以等待触发器运行此操作，或单击顶部的 "![烧杯测试" 图标](./media/automate-with-flow/testicon.png)**测试**。

选择**测试**后：

1. 选择**我将执行触发器操作**。
2. 选择 "**运行流**"。

当流运行时，在电子邮件列表中指定的收件人将收到如下所示的电子邮件。

![示例电子邮件](./media/automate-with-flow/flow9.png)

## <a name="next-steps"></a>后续步骤

- 详细了解如何创建 [Analytics 查询](../../azure-monitor/log-query/get-started-queries.md)。
- 详细了解 [Microsoft Flow](https://ms.flow.microsoft.com)。

<!--Link references-->
