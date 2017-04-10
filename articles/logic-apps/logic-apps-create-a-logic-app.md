---
title: "在云应用与云服务之间创建第一个工作流 - Azure 逻辑应用 | Microsoft Docs"
description: "通过在 Azure 逻辑应用中创建并运行工作流，将系统集成和企业应用程序集成 (EAI) 方案的业务流程自动化"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
keywords: "工作流, 云应用, 云服务, 业务流程, 系统集成, 企业应用程序集成, EAI"
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/31/2017
ms.author: jehollan; estfan; LADocs
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 6634f0ee88e68f2fcb09fd7534a88677e8efa029
ms.lasthandoff: 04/03/2017

---

# <a name="create-your-first-logic-app-workflow-to-automate-processes-between-cloud-apps-and-cloud-services"></a>创建第一个逻辑应用工作流以自动化云应用与云服务之间的流程

使用 [Azure 逻辑应用](logic-apps-what-are-logic-apps.md)创建并运行工作流后，无需编写任何代码，即可更轻松、更快速地将业务流程自动化。 第一个示例演示如何创建一个基本的逻辑应用工作流用于在网站上的 RSS 源中检查新内容。 当网站的源中出现新项时，该逻辑应用会通过 Outlook 或 Gmail 帐户发送电子邮件。

若要创建并运行逻辑应用，需要准备好以下各项：

* Azure 订阅。 如果你没有订阅，可以[从免费的 Azure 帐户着手](https://azure.microsoft.com/free/)。 也可以[注册即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

  将在你的 Azure 订阅中计收逻辑应用的使用费。 了解 Azure 逻辑应用的[用量计量](../logic-apps/logic-apps-pricing.md)和[定价](https://azure.microsoft.com/pricing/details/logic-apps)方式。

此外，本示例需要以下各项：

* Outlook.com、Office 365 Outlook 或 Gmail 帐户

    > [!TIP]
    > 如果你已注册个人 [Microsoft 帐户](https://account.microsoft.com/account)，则已经有了一个 Outlook.com 帐户。 如果你已注册 Azure 工作或学校帐户，则已有了一个 **Office 365 Outlook** 帐户。

* 网站 RSS 源的链接。 本示例使用 [MSDN 第 9 频道网站](https://channel9.msdn.com/)的 RSS 源：`https://s.ch9.ms/Feeds/RSS`

## <a name="add-a-trigger-that-starts-your-workflow"></a>添加用于启动工作流的触发器

[*触发器*](./logic-apps-what-are-logic-apps.md#logic-app-concepts)是启动逻辑应用工作流的事件，并且是逻辑应用所需的第一个项。

1. 登录 [Azure 门户](https://portal.azure.com "Azure portal")。

2. 在左侧菜单中选择“新建” > “Enterprise Integration” > “逻辑应用”，如下所示：

     ![Azure 门户 >“新建”>“Enterprise Integration”>“逻辑应用”](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

   > [!TIP]
   > 也可选择“新建”，然后在搜索框中键入 `logic app`，再按 Enter。 然后选择“逻辑应用” > “创建”。

3. 为逻辑应用命名，然后选择你的 Azure 订阅。 现在，创建或选择 Azure 资源组，用于帮助组织和管理相关的 Azure 资源。 最后，选择托管逻辑应用的数据中心位置。 准备就绪后，请选择“固定到仪表板”，然后选择“创建”。

     ![逻辑应用详细信息](media/logic-apps-create-a-logic-app/logic-app-settings.png)

   > [!NOTE]
   > 如果选择了“固定到仪表板”，则部署后逻辑应用将显示在 Azure 仪表板上并自动打开。 如果逻辑应用程序未显示在仪表板上，请在“所有资源”磁贴中选择“查看更多”，然后选择该逻辑应用。 或者在左侧菜单中选择“更多服务”。 在“Enterprise Integration”下选择“逻辑应用”，然后选择你的逻辑应用。

4. 首次打开逻辑应用时，逻辑应用程序设计器将显示可帮助你入门的模板。 暂时请选择“空白逻辑应用”，以便可以从头开始构建逻辑应用。

    逻辑应用程序设计器将会打开，并显示可在逻辑应用中使用的服务和*触发器*。

5. 在搜索框中键入 `RSS`，然后选择此触发器：“RSS - 发布源项时” 

    ![RSS 触发器](media/logic-apps-create-a-logic-app/rss-trigger.png)

6. 输入想要跟踪的网站 RSS 源的链接。 

     还可以更改“频率”和“间隔”。 
     这些设置确定逻辑应用检查新项的频率，并返回在该时间范围内找到的所有项。

     在本示例中，我们将每天检查 MSDN 第 9 频道网站上发布的新项。

     ![使用 RSS 源、频率和间隔设置触发器](media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

7. 保存到目前为止完成的工作。 （在设计器命令栏上选择“保存”。）

   ![保存逻辑应用](media/logic-apps-create-a-logic-app/save-logic-app.png)

   保存后，逻辑应用将会运行，但目前它只会检查指定 RSS 源中的新项。 
   为了使本示例发挥更大的作用，我们添加了激发触发器后逻辑应用所要执行的操作。

## <a name="add-an-action-that-responds-to-your-trigger"></a>添加一个响应触发器的操作

[*操作*](./logic-apps-what-are-logic-apps.md#logic-app-concepts)是逻辑应用工作流执行的任务。 将触发器添加到逻辑应用后，可以添加一个操作，以便使用该触发器生成的数据执行操作。 现在，我们将在本示例中添加一个操作，以便网站的 RSS 源中出现新项时发送电子邮件。

1. 在设计器中的触发器下面，选择“新建步骤” > “添加操作”，如下所示：

   ![添加操作](media/logic-apps-create-a-logic-app/add-new-action.png)

   设计器将显示[可用的连接器](../connectors/apis-list.md)，让你选择激发触发器时要执行的操作。

2. 根据所用的电子邮件帐户，遵循适用于 Outlook 或 Gmail 的步骤。

   * 若要从 Outlook 帐户发送电子邮件，请在搜索框中输入 `outlook`。 在“服务”下面，选择“Outlook.com”（如果使用个人 Microsoft 帐户）或“Office 365 Outlook”（如果使用 Azure 工作或学校帐户）。 
   在“操作”下面，选择“发送电子邮件”。

       ![在 Outlook 中选择“发送电子邮件”操作](media/logic-apps-create-a-logic-app/actions.png)

   * 若要从 Gmail 帐户发送电子邮件，请在搜索框中输入 `gmail`。 
   在“操作”下面，选择“发送电子邮件”。

       ![选择“Gmail - 发送电子邮件”](media/logic-apps-create-a-logic-app/actions-gmail.png)

3. 系统提示输入凭据时，请使用电子邮件帐户的用户名和密码登录。 

4. 提供此操作的详细信息（例如目标电子邮件地址），然后选择要包含在电子邮件中的数据的参数，例如：

   ![选择要包含在电子邮件中的数据](media/logic-apps-create-a-logic-app/rss-action-setup.png)

    因此，如果选择了“Outlook”，逻辑应用可能如以下示例所示：

    ![完成的逻辑应用](media/logic-apps-create-a-logic-app/save-run-complete-logic-app.png)

5.    保存所做更改。 （在设计器命令栏上选择“保存”。）

6. 现在，可以手动运行逻辑应用进行测试。 在设计器命令栏上选择“运行”。 也可以让逻辑应用根据设置的计划检查指定的 RSS 源。

   如果逻辑应用找到新项，将会发送包含所选数据的电子邮件。 
   如果未找到新项，逻辑应用将跳过发送电子邮件的操作。

7. 若要监视和检查逻辑应用的运行与触发历史记录，请在逻辑应用菜单中选择“概述”。

   ![监视和查看逻辑应用的运行与触发历史记录](media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > 如果未看到所需的数据，请在命令栏上选择“刷新”。

   若要详细了解逻辑应用的状态或者运行与触发历史记录，或者要诊断逻辑应用，请参阅 [Troubleshoot your logic app](logic-apps-diagnosing-failures.md)（排查逻辑应用问题）。

      > [!NOTE]
      > 在关闭逻辑应用之前，逻辑应用会一直运行。 若要暂时关闭该应用，请在逻辑应用菜单中选择“概述”。 在命令栏中选择“禁用”。

祝贺你设置并运行了第一个基本的逻辑应用。 此外，你还学习了如何轻松创建工作流以便将流程自动化以及将云应用和云服务相集成 - 所有这些操作都无需编写代码。

## <a name="manage-your-logic-app"></a>管理逻辑应用

若要管理应用，可以执行多种任务，例如，检查状态、编辑和查看历史记录、关闭或删除逻辑应用。

1. 登录 [Azure 门户](https://portal.azure.com "Azure portal")。

2. 在左侧菜单中选择“更多服务”。 在“Enterprise Integration”下选择“逻辑应用”。 选择逻辑应用。 

   逻辑应用菜单中提供了以下逻辑应用管理任务：

   |任务|步骤| 
   |:---|:---| 
   | 查看应用的状态、执行历史记录和常规信息| 选择“概述”。| 
   | 编辑应用 | 选择“逻辑应用设计器”。 | 
   | 查看应用的工作流 JSON 定义 | 选择“逻辑应用代码视图”。 | 
   | 查看针对逻辑应用执行的操作 | 选择“活动日志”。 | 
   | 查看逻辑应用的先前版本 | 选择“版本”。 | 
   | 暂时关闭应用 | 选择“概述”，然后在命令栏上选择“禁用”。 | 
   | 删除应用 | 选择“概述”，然后在命令栏上选择“删除”。 输入逻辑应用的名称，然后选择“删除”。 | 

## <a name="get-help"></a>获取帮助

若要提问、解答问题和了解其他 Azure 逻辑应用用户的活动，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

为了帮助我们改进 Azure 逻辑应用和连接器，敬请在 [Azure 逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)上投票或发表看法。

## <a name="next-steps"></a>后续步骤

*  [添加条件和运行工作流](../logic-apps/logic-apps-use-logic-app-features.md)
*     [逻辑应用模板](../logic-apps/logic-apps-use-logic-app-templates.md)
*  [通过 Azure Resource Manager 模板创建逻辑应用](../logic-apps/logic-apps-arm-provision.md)

