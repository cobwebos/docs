---
title: "使用 Microsoft Flow自动执行 Azure Application Insights 过程"
description: "了解如何通过 Microsoft Flow 使用 Flow 的 Application Insights 连接器快速自动执行可重复的过程。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/25/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 7e17fde55097a8e852be33bac66940a05fdd9df6
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-the-connector-for-microsoft-flow"></a>使用 Microsoft Flow 的连接器自动执行 Application Insights 过程

你是否发现自己曾对遥测数据重复运行相同的查询，用于检查你的服务是否正常运行？ 想要自动执行这些查询以查找趋势和异常，并针对这些信息构建你自己的工作流？ 嗯，适用于 Microsoft Flow 的Application Insights Connector（预览版）正是你所需要的！
通过这种集成，现在无需编写任何代码就可以自动执行许多过程。 使用 Application Insights 操作创建流后，它会自动运行 Application Insights Analytics 查询。 你也可以添加其他操作。 Flow 可以实现数百个操作。 例如，你可以使用 Flow 自动发送电子邮件通知，或在 Visual Studio Team Services 中创建一个 bug。 你还可以使用适用于 Microsoft Flow 连接器的众多[模板](https://ms.flow.microsoft.com/en-us/connectors/shared_applicationinsights/?slug=azure-application-insights)之一。 这些模板可加快创建流的过程。 

<!--The Application Insights connector also works with [Azure Power Apps](https://powerapps.microsoft.com/en-us/) and [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/?v=17.23h). --> 

## <a name="tutorial-for-creating-a-flow-for-application-insights"></a>为 Application Insights 创建流的教程

在本教程中，你将了解如何创建流以使用 Analytics 自动群集算法，针对 Web 应用程序对数据属性进行分组。 流会自动通过电子邮件发送结果。 这只是如何结合使用 Microsoft Flow 和 Application Insights Analytics 的一个示例。 

### <a name="step-1-create-a-flow"></a>步骤 1：创建流
1. 在 http://flow.microsoft.com 上登录，然后选择“我的流”。
2. 单击“从头开始创建流”。

### <a name="step-2-create-a-trigger-for-your-flow"></a>步骤 2：为流创建触发器
1. 选择“计划”，然后选择“计划 - 循环”。
2. 将“频率”设置为“天”，“间隔”设置为 1。

![流触发对话框](./media/app-insights-automate-with-flow/flow1.png)


### <a name="step-3-add-an-azure-application-insights-action"></a>步骤 3：添加 Azure Application Insights 操作
1. 单击“新建步骤”，然后单击“添加操作”。
2. 搜索“Azure Application Insights”。
3. 单击“Azure Application Insights”– 可视化 Analytics 查询预览。

![运行 Analytics 查询屏幕](./media/app-insights-automate-with-flow/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>步骤 4：连接到 Application Insights 资源

**先决条件**

需要提供一个应用程序 ID 和一个 API 密钥，资源才能完成此步骤。 你可以从 Azure 门户检索它们，如下图所示：

![Azure 门户中的应用程序 ID](./media/app-insights-automate-with-flow/appid.png) 

- 为你的连接提供一个名称，并提供应用程序 ID 和 API 密钥。

![流连接屏幕](./media/app-insights-automate-with-flow/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>步骤 5：指定 Analytics 查询和图表类型
此示例在最后一天选择失败的请求，并将它们与操作过程中发生的异常相关联。 Analytics 根据 operation_Id 标识符进行关联。 然后，该查询使用 autocluster 算法细分结果。 创建你自己的查询时，请务必确保在将其添加到你的流之前，它们可在 Analytics 中正常运行。

- 添加以下 Analytics 查询并选择 HTML 表图表类型。 

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
![Analytics 查询配置屏幕](./media/app-insights-automate-with-flow/flow4.png)

### <a name="step-6-configure-the-flow-to-send-email"></a>步骤 6：配置流以发送电子邮件

1. 单击“新建步骤”，然后选择“添加操作”。
2. 搜索“Office 365 Outlook”。
3. 单击“Office 365 Outlook”– 发送一封电子邮件。
![Office 365 Outlook 选择](./media/app-insights-automate-with-flow/flow2b.png)

4. 在电子邮件操作中，添加以下数据：
 - 指定收件人的电子邮件地址
 - 提供电子邮件的主题
 - 将光标置于“正文”字段，然后从右侧打开的动态内容菜单中选择“正文”。
 - 单击“显示高级选项”。

 ![Office 365 Outlook 配置](./media/app-insights-automate-with-flow/flow5.png)

5. 从动态内容菜单中执行以下操作：
- 选择“附件名称”
- 选择“附件内容”
- 在“是否为 HTML”字段中选择“是”

![Office 365 电子邮件配置屏幕](./media/app-insights-automate-with-flow/flow7.png)
### <a name="step-7-save-and-test-your-flow"></a>步骤 7：保存并测试流
- 向流添加一个名称并单击“创建流”。

![Microsoft Flow 创建屏幕](./media/app-insights-automate-with-flow/flow8.png)

你可以等待触发器运行此操作，也可以[按需运行触发器](https://flow.microsoft.com/blog/run-now-and-six-more-services/)以立即运行流。

运行流时，在电子邮件列表中指定的收件人将收到如下所示的电子邮件：

![示例电子邮件](./media/app-insights-automate-with-flow/flow9.png)


## <a name="next-steps"></a>后续步骤

- 详细了解如何创建 [Analytics 查询](app-insights-analytics-using.md)。
- 详细了解 [Microsoft Flow](https://ms.flow.microsoft.com)。



<!--Link references-->






