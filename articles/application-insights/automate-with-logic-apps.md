---
title: 使用逻辑应用自动执行 Azure Application Insights 过程。
description: 了解如何通过将 Application Insights 连接器添加到逻辑应用来快速自动执行可重复的过程。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/29/2017
ms.author: mbullwin
ms.openlocfilehash: 4ae5adaf37f49e9909fa80fd31f088ed6ee1b081
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39450437"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>使用逻辑应用自动执行 Application Insights 过程

是否发现自己曾对遥测数据重复运行相同的查询以检查服务是否正常运行？ 是否希望自动执行这些查询以查找趋势和异常，然后针对这些信息构建你自己的工作流？ 用于逻辑应用的 Azure Application Insights 连接器（预览版）是适用于此用途的合适工具。

通过这种集成，无需编写任何代码就可以自动执行许多过程。 可以使用 Application Insights 连接器创建逻辑应用，快速自动执行任何 Application Insights 过程。 

也可以添加其他操作。 Azure 应用服务的逻辑应用功能提供数百个操作。 例如，可使用逻辑应用在 Visual Studio Team Services 中自动发送电子邮件通知或创建 bug。 还可使用多个可用[模板](https://docs.microsoft.com/azure/logic-apps/logic-apps-use-logic-app-templates)帮助加快逻辑应用的创建过程。 

## <a name="create-a-logic-app-for-application-insights"></a>为 Application Insights 创建逻辑应用

本教程介绍如何创建使用 Analytics autocluster 算法对 Web 应用程序的数据中的属性进行分组的逻辑应用。 此流自动通过电子邮件发送结果，这只是如何结合使用 Application Insights Analytics 和逻辑应用的一个示例。 

### <a name="step-1-create-a-logic-app"></a>步骤 1：创建逻辑应用
1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 单击“创建资源”，选择“Web + 移动”，然后选择“逻辑应用”。

    ![新建逻辑应用窗口](./media/automate-with-logic-apps/logicapp1.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>步骤 2：为逻辑应用创建触发器
1. 在“逻辑应用设计器”窗口中的“首先使用常用触发器”下，选择“重复周期”。

    ![“逻辑应用设计器”窗口](./media/automate-with-logic-apps/logicapp2.png)

1. 在“频率”框中选择“日期”，然后在“时间间隔”框中键入 1。

    ![逻辑应用设计器“重复周期”窗口](./media/automate-with-logic-apps/step2b.png)

### <a name="step-3-add-an-application-insights-action"></a>步骤 3：添加 Application Insights 操作
1. 单击“新建步骤”，并单击“添加操作”。

1. 在“选择操作”搜索框中，键入“Azure Application Insights”。

1. 在“操作”下，单击“Azure Application Insights - 将 Analytics 查询可视化(预览)”。

    ![逻辑应用设计器“选择操作”窗口](./media/automate-with-logic-apps/flow2.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>步骤 4：连接到 Application Insights 资源

若要完成此步骤，需要为资源提供应用程序 ID 和 API 密钥。 可从 Azure 门户检索它们，如下图所示：

![Azure 门户中的应用程序 ID](./media/automate-with-logic-apps/appid.png) 

为连接提供名称，并提供应用程序 ID 和 API 密钥。

![逻辑应用设计器流连接窗口](./media/automate-with-logic-apps/flow3.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>步骤 5：指定 Analytics 查询和图表类型
在以下示例中，查询在最后一天选择失败的请求，并将它们与操作过程中发生的异常相关联。 Analytics 根据 operation_Id 标识符关联失败的请求。 然后，该查询使用 autocluster 算法细分结果。 

创建自己的查询时，请在将其添加到流之前，验证它们是否可在 Analytics 中正常运行。

1. 在“查询”框中，添加以下 Analytics 查询： 

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

1. 在“图表类型”框中，选择“Html 表”。

    ![Analytics 查询配置窗口](./media/automate-with-logic-apps/flow4.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>步骤 6：配置逻辑应用以发送电子邮件

1. 单击“新建步骤”，然后选择“添加操作”。

1. 在搜索框中键入 Office 365 Outlook。

1. 单击“Office 365 Outlook - 发送电子邮件”。

    ![Office 365 Outlook 选择](./media/automate-with-logic-apps/flow2b.png)

1. 在“发送电子邮件”窗口中，执行以下操作：

   a. 键入收件人的电子邮件地址。

   b. 键入电子邮件的主题。

   c. 单击“正文”框中的任意位置，然后在右侧打开的动态内容菜单中选择“正文”。

   d. 单击“显示高级选项”。

      ![Office 365 Outlook 配置](./media/automate-with-logic-apps/flow5.png)

1. 在动态内容菜单上执行以下操作：

    a. 选择“附件名称”。

    b. 选择“附件内容”。
    
    c. 在“是否为 HTML”框中选择“是”。

      ![“Office 365 电子邮件配置”屏幕](./media/automate-with-logic-apps/flow7.png)

### <a name="step-7-save-and-test-your-logic-app"></a>步骤 7：保存并测试逻辑应用
* 单击“保存”以保存更改。

可以等待触发器运行逻辑应用，也可以通过选择“运行”立即运行逻辑应用。

![逻辑应用创建屏幕](./media/automate-with-logic-apps/step7.png)

逻辑应用运行时，在电子邮件列表中指定的收件人将收到如下所示的电子邮件：

![逻辑应用电子邮件](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>后续步骤

- 详细了解如何创建 [Analytics 查询](app-insights-analytics-using.md)。
- 了解有关[逻辑应用](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps)的详细信息。



<!--Link references-->





