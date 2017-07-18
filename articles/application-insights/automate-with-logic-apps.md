---
title: "使用 Azure 逻辑应用自动执行 Azure Application Insights 过程。"
description: "了解如何通过将 Application Insights 连接器添加到 Azure 逻辑应用来快速自动执行可重复的过程。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 571a76253fa62a89f325e0c295e9a7e98e201079
ms.contentlocale: zh-cn
ms.lasthandoff: 07/01/2017

---

# <a name="automate-application-insights-processes-with-an-azure-logic-app"></a>使用 Azure 逻辑应用自动执行 Application Insights 过程

你是否发现自己对遥测数据重复运行相同的查询以检查服务是否正常运行？ 想要自动执行这些查询以查找趋势和异常并围绕它们构建你自己的工作流？ 嗯，适用于 Azure 逻辑应用的 Application Insights 连接器（预览版）正是你所需要的！
通过这种集成，现在可以自动执行众多过程，而无需编写任何代码。 可以使用 Application Insights 连接器创建逻辑应用，以快速自动执行任何 Application Insights 过程。 也可以添加其他操作。 逻辑应用可实现数百个操作。 例如，可以在 Visual Studio Team Services 中作为逻辑应用的一部分自动发送电子邮件通知或创建 bug。 还可以使用适用于逻辑应用的多个[模板](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates)之一。 这些模板可加快创建逻辑应用的过程。 

## <a name="tutorial-for-creating-an-azure-logic-app-for-application-insights"></a>为 Application Insights 创建 Azure 逻辑应用的教程

本教程介绍如何创建 Azure 逻辑应用以使用 Analytics 自动群集算法对 Web 应用程序的数据中的属性进行分组。 流会自动通过电子邮件发送结果。 这只是举例说明如何结合使用 Application Insights Analytics 和 Azure 逻辑应用。 

### <a name="step-1-create-a-logic-app"></a>步骤 1：创建逻辑应用
1. 登录到 https://portal.azure.com。
2. 从“新建/Web + 移动”菜单创建新的逻辑应用。

![“新建逻辑应用”屏幕](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>步骤 2：为逻辑应用创建触发器
1.  在逻辑应用设计器中的“开始使用常见触发器”下，选择“重复周期”。
2.  将“频率”设置为“天”，“间隔”设置为 1。

![“逻辑应用触发器”对话框](./media/automate-with-logic-apps/logicapp2.png)

![“逻辑应用频率”对话框](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-azure-application-insights-action"></a>步骤 3：添加 Azure Application Insights 操作
1. 单击“新建步骤”，然后单击“添加操作”。
2. 搜索“Azure Application Insights”。
3. 单击“Azure Application Insights”- 将 Analytics 查询预览可视化。

![运行 Analytics 查询屏幕](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>步骤 4：连接到 Application Insights 资源

**先决条件**

需要应用程序 ID 和 API 密钥，资源才能完成此步骤。 可以从 Azure 门户检索它们，如下图所示：

![Azure 门户中的应用程序 ID](./media/automate-with-logic-apps/appid.png) 

- 为连接提供名称以及应用程序 ID 和 API 密钥。

![“流连接”屏幕](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>步骤 5：指定 Analytics 查询和图表类型
此示例在最后一天选择失败的请求，并将它们与操作过程中发生的异常相关联。 Analytics 根据 operation_Id 标识符进行关联。 然后，该查询使用自动群集算法细分结果。 创建你自己的查询时，请务必确保在将其添加到自己的流之前它们可在 Analytics 中正常运行。

- 添加以下 Analytics 查询并选择 Html 表图表类型。 

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
![“Analytics 查询配置”屏幕](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-app-to-send-email"></a>步骤 6：配置应用以发送电子邮件

1. 单击“新建步骤”，然后选择“添加操作”。
2. 搜索“Office 365 Outlook”。
3. 单击“Office 365 Outlook”- 发送一封电子邮件。
![Office 365 Outlook 选择](./media/automate-with-logic-apps/flow2b.png)

4. 在电子邮件操作中，添加以下数据：
 - 指定收件人的电子邮件地址
 - 提供电子邮件的主题
 - 将光标置于“正文”字段中，然后从右侧打开的动态内容菜单中选择“正文”。
 - 单击“显示高级选项”。

 ![Office 365 Outlook 配置](./media/automate-with-logic-apps/flow5.png)

5. 从动态内容菜单执行以下操作：
- 选择“附件名称”
- 选择“附件内容”
- 在“是 HTML”字段中选择“是”

![“Office 365 电子邮件配置”屏幕](./media/automate-with-logic-apps/flow7.png)
### <a name="step-7-save-and-test-your-logic-app"></a>步骤 7：保存并测试逻辑应用
1. 单击“保存”以保存更改。
1. 可以等待触发器运行逻辑应用，也可以通过选择“运行”立即运行它。

![“创建逻辑应用”屏幕](./media/automate-with-logic-apps/step7.png)

逻辑应用运行时，在电子邮件列表中指定的收件人将收到如下所示的电子邮件：

![“逻辑应用”电子邮件](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>后续步骤

- 了解有关创建 [Analytics 查询](app-insights-analytics-using.md)的详细信息。
- 了解有关 [Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)的详细信息。



<!--Link references-->






