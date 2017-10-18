---
title: "在系统与云服务之间创建第一个自动化工作流 - Azure 逻辑应用 | Microsoft Docs"
description: "通过创建并运行逻辑应用，将系统集成和企业应用程序集成 (EAI) 方案的业务流程和工作流自动化"
author: ecfan
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
ms.date: 09/18/2017
ms.author: LADocs; estfan
ms.openlocfilehash: d62255ba6e3d5bdfbd792a47f3a92d4c88876742
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="create-your-first-logic-app-for-automating-workflows-and-processes-through-the-azure-portal"></a>通过 Azure 门户创建第一个用于工作流和流程自动化的逻辑应用

无需编写代码即可集成系统和服务，方法是通过 [Azure 逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)生成并运行自动化工作流。 你可以轻松地通过工作流来自动完成任务。为了说明这一点，本教程创建了一个基本的逻辑应用，用于检查 RSS 源中是否存在网站的新内容，并针对源中的每个新项目发送一封电子邮件。 

![概述 - 第一个逻辑应用示例](./media/logic-apps-create-a-logic-app/logic-app-overview.png)

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 创建空白逻辑应用。
> * 添加一个触发器，以便在 RSS 源项发布时启动逻辑应用。
> * 添加一个操作，用于发送包含 RSS 源项详细信息的电子邮件。
> * 运行并测试逻辑应用。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果没有订阅，可以[从免费的 Azure 帐户着手](https://azure.microsoft.com/free/)。 也可以[注册即用即付订阅](https://azure.microsoft.com/pricing/purchase-options/)。

* 来自 [Azure 逻辑应用所支持的任何电子邮件提供商](../connectors/apis-list.md)的电子邮件帐户，用于发送通知。 例如，可以使用 Office 365 Outlook、Outlook.com、Gmail 或其他受支持的提供商。 本教程使用 Office 365 Outlook。

  > [!TIP]
  > 如果已注册个人 [Microsoft 帐户](https://account.microsoft.com/account)，则已经有了一个 Outlook.com 帐户。 如果已注册 Azure 工作或学校帐户，则已有了一个 Office 365 Outlook 帐户。

* 网站 RSS 源的链接。 此示例使用 [CNN.com 网站发布的头条新闻的 RSS 源](http://rss.cnn.com/rss/cnn_topstories.rss)：`http://rss.cnn.com/rss/cnn_topstories.rss`

## <a name="1-create-a-blank-logic-app"></a>1.创建空白逻辑应用 

1. 登录 [Azure 门户](https://portal.azure.com "Azure portal")。

2. 从主 Azure 菜单中选择“新建” > “企业集成” > “逻辑应用”。

   ![Azure 门户 >“新建”>“Enterprise Integration”>“逻辑应用”](media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. 使用表中指定的设置创建逻辑应用。

   ![提供逻辑应用的详细信息](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **Name** | your-logic-app-name | 提供一个唯一的逻辑应用名称。 | 
   | **订阅** | your-Azure-subscription | 选择要使用的 Azure 订阅。 | 
   | **资源组** | your-Azure-resource-group | 创建或选择 Azure 资源组，用于组织和管理相关的 Azure 资源。 | 
   | **位置** | your-Azure-region | 选择用于部署逻辑应用的数据中心区域。 | 
   |||| 

4. 准备就绪后，请选择“固定到仪表板”，然后选择“创建”。

   你现在已为逻辑应用程序创建 Azure 资源。 
   在 Azure 部署逻辑应用后，逻辑应用设计器会显示针对常用模式的模板，以便你可以更快地入门。

   > [!NOTE] 
   > 如果选择了“固定到仪表板”，则部署后逻辑应用会显示在 Azure 仪表板上并在逻辑应用设计器中自动打开。 否则，你可以手动查找和打开逻辑应用。

5. 现在，请在“模板”下选择“空白逻辑应用”，以便从头开始构建逻辑应用。

   ![选择逻辑应用模板](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   此时，逻辑应用设计器会显示可用的[连接器](../connectors/apis-list.md)及其[触发器](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)，用于启动逻辑应用工作流。

   ![逻辑应用触发器](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="2-add-a-trigger-for-starting-the-workflow"></a>2.添加用于启动工作流的触发器

每个逻辑应用必须以[触发器](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)开头。 如果发生特定的事件，或者新数据符合已设置的条件，触发器就会触发。 然后，逻辑应用引擎会创建用于运行工作流的逻辑应用实例。 每当触发器触发时，该引擎就会创建另一个单独的实例，用于运行逻辑应用工作流。

1. 在搜索框中键入“rss”作为筛选器。 选择触发器“RSS - 发布源项时” 

   ![选择触发器“RSS - 发布源项时”](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. 提供要跟踪的网站 RSS 源的链接，例如 `http://rss.cnn.com/rss/cnn_topstories.rss`。 设置重复间隔和频率。 在此示例中，请将这些属性设置为每天都检查源。 

   ![使用 RSS 源、频率和间隔设置触发器](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

3. 保存到目前为止完成的工作。 在设计器工具栏上，选择“保存”。
若要折叠和隐藏触发器的详细信息，请选择触发器的标题栏。

   ![保存逻辑应用](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   逻辑应用现已发布，但在你将操作添加到工作流之前，它不会执行任何操作（查看 RSS 源中是否存在新项除外）。 

## <a name="3-add-an-action-that-responds-to-the-trigger"></a>3.添加一项响应触发器的操作

现在请添加一项[操作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)，即逻辑应用工作流执行的任务。 在此示例中，请添加一项操作，在 RSS 源中出现新项时发送电子邮件。

1. 在逻辑应用设计器的触发器下，选择“+ 新步骤” > “添加操作”。

   ![添加操作](./media/logic-apps-create-a-logic-app/add-new-action.png)

   设计器会显示[可用的连接器](../connectors/apis-list.md)，让你选择激发触发器时要执行的操作。

   ![从操作列表中选择](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. 在搜索框中，输入“发送电子邮件”作为筛选器。 根据你的电子邮件提供程序，找到并选择匹配的连接器。 然后选择连接器的“发送电子邮件”操作。 例如： 

   * 对于 Azure 工作或学校帐户，请选择 Office 365 Outlook 连接器。 
   * 对于个人 Microsoft 帐户，请选择 Outlook.com 连接器。 
   * 对于 Gmail 帐户，则选择 Gmail 连接器。 

   我们将继续使用 Office 365 Outlook 连接器。 
   如果你使用不同的提供程序，步骤保持不变，但你的 UI 显示可能会有所不同。 

   ![选择操作“Office 365 Outlook - 发送电子邮件”](./media/logic-apps-create-a-logic-app/actions.png)

3. 系统提示输入凭据时，请使用电子邮件帐户的用户名和密码登录。 

4. 提供表中指定的详细信息，选择要包括在邮件中的字段。

   | 目标 | 步骤 | 
   | -- | ----- | 
   | 选择适用于工作流的字段。 | 单击“编辑”框以打开“动态内容”列表，或选择“添加动态内容”。 | 
   | 查看其他可用字段。 | 对于每个部分，请在“动态内容”列表中选择“查看详情”。  | 
   | 在编辑框中添加空白行。 | 按 Shift + Enter。 | 
   | 关闭“动态内容”列表。 | 再次选择“添加动态内容”。 | 
   ||| 

   ![选择要包含在电子邮件中的数据](./media/logic-apps-create-a-logic-app/rss-action-setup.png)

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **To** | recipient-email-address | 输入收件人的电子邮件地址。 为进行测试，可以使用自己的电子邮件地址。 | 
   | **主题** | 新的 CNN 帖子：源标题 | 输入电子邮件的主题内容。 <p>就本教程来说，请输入建议的文本，然后选择触发器的“源标题”字段，以便显示源项的标题。 | 
   | **正文** | 标题：源标题 <p>发布日期：源主链接 <p>链接：源主链接 | 输入电子邮件的正文内容。 <p>就本教程来说，请输入建议的文本，然后选择以下触发器字段： <p>- 源标题，再次显示源项的标题。 </br>- 源发布日期：显示项的已发布日期和时间 </br>- 源主链接，显示源项的 URL | 
   |||| 

   > [!NOTE] 
   > 如果选择一个字段来存储数组，设计器会围绕引用该数组的操作自动添加“For each”循环。 这样一来，逻辑应用会对每个数组项执行该操作。

5. 完成后，保存所做的更改。 在设计器工具栏上，选择“保存”。

   ![完成的逻辑应用](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

   若要现在测试逻辑应用，请继续学习下一节。

## <a name="4-run-and-test-your-workflow"></a>4.运行并测试工作流

1. 若要手动运行需测试的逻辑应用，请在设计器工具栏中选择“运行”。 也可以让逻辑应用根据设置的计划检查指定的 RSS 源。

   ![运行逻辑应用](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

   逻辑应用在找到新项时会发送包含所选数据的电子邮件，例如：

   ![针对新的 RSS 源项发送的电子邮件](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

   逻辑应用在找不到任何新项时，会跳过发送电子邮件的操作，等待下次再进行检查。 

2. 若要查看逻辑应用的运行与触发历史记录，请在逻辑应用菜单中选择“概览”。
若要查看有关运行的更多详细信息，请选择运行所在的行。

   ![监视和查看逻辑应用的运行与触发历史记录](./media/logic-apps-create-a-logic-app/logic-app-run-trigger-history.png)

   > [!TIP]
   > 如果找不到预期的数据，请尝试在工具栏中选择“刷新”。

   不管运行是成功还是失败，“运行详细信息”视图都会显示成功或失败的步骤。 

   ![查看逻辑应用运行的详细信息](./media/logic-apps-create-a-logic-app/logic-app-run-details.png)

   若要详细了解逻辑应用的状态、运行历史记录和触发器历史记录，或者要对逻辑应用进行诊断，请参阅[排查逻辑应用问题](../logic-apps/logic-apps-diagnosing-failures.md)。

3. 若要查看每个步骤的输入和输出，请展开要查看的步骤。 此信息可以帮助你诊断和调试逻辑应用中的问题。 例如：

   ![查看步骤详细信息](./media/logic-apps-create-a-logic-app/logic-app-run-details-expanded.png)

   有关详细信息，请参阅[监视逻辑应用](../logic-apps/logic-apps-monitor-your-logic-apps.md)。

祝贺！你已创建并运行第一个基本的逻辑应用。 此示例介绍了如何轻松创建工作流，以便 自动执行进行系统和服务集成的流程 - 不需代码。

> [!NOTE]
> 在关闭逻辑应用之前，逻辑应用会一直运行。 若要暂时关闭应用，请转到下一部分。

## <a name="clean-up-resources"></a>清理资源

本教程使用的资源和执行的操作可能会在 Azure 订阅上产生费用。 完成本教程和测试后，请务必禁用或删除不希望其产生费用的任何资源。

你可以停止逻辑应用的运行和发送电子邮件，而不删除此应用。 在逻辑应用菜单上，选择“概述”。 在工具栏上，选择“禁用”。

![关闭逻辑应用](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

## <a name="faq"></a>常见问题

问：我还可以使用逻辑应用做些什么？ </br>
答：可以执行其他任务，例如编辑和查看 JSON 定义、查看活动日志，或者删除逻辑应用。

若要查找其他逻辑应用管理任务，请在逻辑应用菜单中查看以下命令：

| 任务 | 步骤 | 
| ---- | ----- | 
| 查看应用的状态、运行和触发器历史记录，以及常规信息 | 选择“概述”。 | 
| 编辑应用 | 选择“逻辑应用设计器”。 | 
| 查看应用的工作流 JSON 定义 | 选择“逻辑应用代码视图”。 | 
| 查看针对逻辑应用执行的操作 | 选择“活动日志”。 | 
| 查看逻辑应用的先前版本 | 选择“版本”。 | 
| 暂时关闭应用 | 选择“概览”，然后在工具栏中选择“禁用”。 | 
| 删除应用 | 选择“概览”，然后在工具栏中选择“删除”。 输入逻辑应用的名称，并选择“删除”。 | 
||| 

## <a name="get-support"></a>获取支持

* 有关 Azure 逻辑应用的问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。

* 想要帮助我们改进 Azure 逻辑应用和连接器？ 请在 [Azure 逻辑应用用户之声站点](http://aka.ms/logicapps-wish)上提交你自己的想法，或者进行相关投票。

## <a name="next-steps"></a>后续步骤

* [使用 Visual Studio 创建逻辑应用](../logic-apps/logic-apps-deploy-from-vs.md)
* [添加条件和运行工作流](../logic-apps/logic-apps-use-logic-app-features.md)
*   [逻辑应用模板](../logic-apps/logic-apps-use-logic-app-templates.md)
* [通过 Azure Resource Manager 模板创建逻辑应用](../logic-apps/logic-apps-arm-provision.md)
* [逻辑应用用量计算](../logic-apps/logic-apps-pricing.md) 
* [逻辑应用定价](https://azure.microsoft.com/pricing/details/logic-apps)
