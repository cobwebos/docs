---
title: 使用 Microsoft Flow自动执行 Azure Application Insights 过程
description: 了解如何通过 Microsoft Flow 使用 Application Insights 连接器快速自动执行可重复的过程。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2017
ms.author: mbullwin
ms.openlocfilehash: c92b50ee78c100a531a2973181251444b3810967
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293763"
---
# <a name="automate-azure-application-insights-processes-with-the-connector-for-microsoft-flow"></a>使用 Microsoft Flow 的连接器自动执行 Azure Application Insights 过程

你是否发现自己曾对遥测数据重复运行相同的查询，用于检查你的服务是否正常运行？ 是否希望自动执行这些查询以查找趋势和异常，然后针对这些信息构建你自己的工作流？ Microsoft Flow Application Insights 连接器（预览版）是适用于这些用途的工具。

通过这种集成，现在无需编写任何代码就可自动执行许多过程。 使用 Application Insights 操作创建流后，它自动运行 Application Insights Analytics 查询。 

也可以添加其他操作。 Microsoft Flow 可以实现数百个操作。 例如，可以使用 Microsoft Flow 自动发送电子邮件通知，或在 Visual Studio Team Services 中创建 bug。 还可使用适用于 Microsoft Flow 连接器的众多[模板](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights)之一。 这些模板可加快创建流的过程。 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="create-a-flow-for-application-insights"></a>创建 Application Insights 流

在本教程中，你将了解如何创建流以使用 Analytics 自动群集算法，针对 Web 应用程序对数据属性进行分组。 此流通过电子邮件自动发送结果，这只是如何结合使用 Microsoft Flow 和 Application Insights Analytics 的一个示例。 

### <a name="step-1-create-a-flow"></a>步骤 1：创建流
1. 登录到 [Microsoft Flow](http://flow.microsoft.com)，然后选择“我的流”。
2. 单击“从头开始创建流”。

### <a name="step-2-create-a-trigger-for-your-flow"></a>步骤 2：为流创建触发器
1. 选择“计划”，然后选择“计划 - 循环”。
2. 在“频率”框中选择“天”，然后在“时间间隔”框中输入 1。

    ![Microsoft Flow 触发器对话框](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-application-insights-action"></a>步骤 3：添加 Application Insights 操作
1. 单击“新建步骤”，并单击“添加操作”。
2. 搜索“Azure Application Insights”。
3. 单击“Azure Application Insights - 将 Analytics 查询可视化(预览)”。

    ![运行 Analytics 查询窗口](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>步骤 4：连接到 Application Insights 资源

若要完成此步骤，需要为资源提供应用程序 ID 和 API 密钥。 可从 Azure 门户检索它们，如下图所示：

![Azure 门户中的应用程序 ID](./media/app-insights-automate-with-flow/appid.png) 

- 为你的连接提供一个名称，并提供应用程序 ID 和 API 密钥。

    ![Microsoft Flow 连接窗口](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>步骤 5：指定 Analytics 查询和图表类型
此查询示例选择最后一天内失败的请求，并将它们与操作过程中发生的异常相关联。 Analytics 根据 operation_Id 标识符关联异常。 然后，该查询使用 autocluster 算法细分结果。 

创建自己的查询时，请在将其添加到流之前，验证它们是否可在 Analytics 中正常运行。

- 添加以下 Analytics 查询，然后选择 HTML 表图表类型。 

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
    
    ![Analytics 查询配置窗口](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>步骤 6：配置流以发送电子邮件

1. 单击“新建步骤”，并单击“添加操作”。
2. 搜索“Office 365 Outlook”。
3. 单击“Office 365 Outlook - 发送电子邮件”。

    ![Office 365 Outlook 选择窗口](./media/app-insights-automate-with-flow/flow2b.png)

4. 在“发送电子邮件”窗口中，执行以下操作：

   a. 键入收件人的电子邮件地址。

   b. 键入电子邮件的主题。

   c. 单击“正文”框中的任意位置，然后在右侧打开的动态内容菜单中选择“正文”。

   d. 单击“显示高级选项”。

    ![Office 365 Outlook 配置](./media/app-insights-automate-with-flow/flow5.png)

5. 在动态内容菜单上执行以下操作：

    a. 选择“附件名称”。

    b. 选择“附件内容”。
    
    c. 在“是否为 HTML”框中选择“是”。

    ![Office 365 电子邮件配置窗口](./media/app-insights-automate-with-flow/flow7.png)

### <a name="step-7-save-and-test-your-flow"></a>步骤 7：保存并测试流
- 在“流名称”框中为流添加名称，然后单击“创建流”。

    ![流创建窗口](./media/app-insights-automate-with-flow/flow8.png)

可以等待触发器运行此操作，也可[根据需要运行触发器](https://flow.microsoft.com/blog/run-now-and-six-more-services/)来立即运行流。

运行流时，在电子邮件列表中指定的收件人将收到如下所示的电子邮件：

![示例电子邮件](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>后续步骤

- 详细了解如何创建 [Analytics 查询](app-insights-analytics-using.md)。
- 详细了解 [Microsoft Flow](https://ms.flow.microsoft.com)。



<!--Link references-->





