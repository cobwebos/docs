---
title: "在系统与云服务之间自动执行工作流 - Azure 逻辑应用 | Microsoft Docs"
description: "创建逻辑应用，将系统集成和企业应用程序集成 (EAI) 方案的工作流自动化"
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
ms.date: 10/20/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 5906605192f9b03f612e6ca3a445434a23713d7f
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/21/2017
---
# <a name="automate-your-first-workflow-to-process-data-with-a-logic-app"></a>自动执行第一个通过逻辑应用处理数据的工作流

若要加快为组织集成系统和服务的速度，可以通过 [Azure 逻辑应用](../logic-apps/logic-apps-what-are-logic-apps.md)自动执行工作流和业务流程。 本快速入门介绍如何通过创建逻辑应用来快速生成和运行自动化工作流。 示例应用演示了如何自动执行一个工作流，以便检查网站 RSS 源中是否存在新项，并针对每个新项发送电子邮件。

该示例逻辑应用发送电子邮件时，如以下示例所示：

![针对新的 RSS 源项发送的电子邮件](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

下面是生成的概略性逻辑应用工作流：

![概述 - 逻辑应用示例](./media/logic-apps-create-a-logic-app/logic-app-simple-overview.png)

此快速入门介绍如何：

> [!div class="checklist"]
> * 创建空白逻辑应用。
> * 添加一个触发器，在新项出现在 RSS 源中时启动工作流。
> * 添加一个操作，用于发送包含 RSS 源项详细信息的电子邮件。
> * 运行逻辑应用工作流。

如果还没有 Azure 订阅，可以在开始前[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

* 来自 Azure 逻辑应用所支持的任何电子邮件提供商的电子邮件帐户，用于发送通知。 例如，可以使用 Office 365 Outlook、Outlook.com、Gmail。 如需其他受支持的电子邮件连接器，请[查看连接器列表](https://docs.microsoft.com/connectors/)。 本快速入门使用 Office 365 Outlook。

  > [!TIP]
  > 如果已注册个人 [Microsoft 帐户](https://account.microsoft.com/account)，则已经有了一个 Outlook.com 帐户。 如果已注册 Azure 工作或学校帐户，则已有了一个 Office 365 Outlook 帐户。

* 网站 RSS 源的链接。 此示例使用 [Reuters 网站发布的头条新闻的 RSS 源](http://feeds.reuters.com/reuters/topNews)：`http://feeds.reuters.com/reuters/topNews`

本快速入门不需编写代码，但逻辑应用支持其他使用代码的方案，例如，通过 [Azure Functions](../azure-functions/functions-overview.md) 从逻辑应用运行你自己的代码。

## <a name="create-a-blank-logic-app"></a>创建空白逻辑应用 

1. 登录 [Azure 门户](https://portal.azure.com "Azure portal")。 

2. 从主 Azure 菜单中选择“新建” > “企业集成” > “逻辑应用”。

   ![Azure 门户 >“新建”>“Enterprise Integration”>“逻辑应用”](./media/logic-apps-create-a-logic-app/azure-portal-create-logic-app.png)

3. 使用表中的设置创建逻辑应用，见下图：

   ![提供逻辑应用的详细信息](./media/logic-apps-create-a-logic-app/logic-app-settings.png)

   | 设置 | 建议的值 | 说明 | 
   | ------- | --------------- | ----------- | 
   | **Name** | your-logic-app-name | 提供一个唯一的逻辑应用名称。 | 
   | **订阅** | your-Azure-subscription-name | 选择要使用的 Azure 订阅。 | 
   | **资源组** | your-Azure-resource-group-name | 创建一个用于此逻辑应用的 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)，以便组织与此应用相关联的所有资源。 | 
   | **位置** | your-Azure-datacenter-region | 选择用于部署逻辑应用的数据中心区域，例如“美国西部”。 | 
   | **Log Analytics** | 关闭 | 为逻辑应用启用诊断日志记录功能，但对于本快速入门，请保留“关闭”设置。 | 
   |||| 

4. 准备就绪后，请选择“固定到仪表板”。 这样一来，逻辑应用就会自动显示在 Azure 仪表板上，并在部署后打开。 选择“创建”。

   > [!NOTE]
   > 如果不希望固定逻辑应用，则必须在部署后手动查找并打开逻辑应用，然后才能继续。

   在 Azure 部署逻辑应用以后，逻辑应用设计器会打开并显示一个包含简介视频的页面。 
   在视频下可以找到常用逻辑应用模式的模板。 
   本快速入门将从头开始生成逻辑应用。 

5. 滚动浏览简介视频和常见的触发器。 在“模板”下选择“空白逻辑应用”。

   ![选择空白逻辑应用模板](./media/logic-apps-create-a-logic-app/choose-logic-app-template.png)

   逻辑应用设计器会显示可用的连接器及其触发器，用于启动逻辑应用工作流。

   ![逻辑应用触发器](./media/logic-apps-create-a-logic-app/logic-app-triggers.png)

## <a name="add-a-trigger-to-detect-new-items"></a>添加用于检测新项的触发器

每个逻辑应用工作流均以[触发器](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)开始。 如果发生特定的事件，或者新数据符合设置的条件，触发器就会触发。 每当触发器触发时，逻辑应用引擎就会创建一个逻辑应用实例来启动并运行工作流。

1. 在搜索框中，输入“rss”作为筛选器。 选择触发器“RSS - 发布源项时” 

   ![选择触发器“RSS - 发布源项时”](./media/logic-apps-create-a-logic-app/rss-trigger.png)

2. 提供要监视的 RSS 源的链接，例如 `http://feeds.reuters.com/reuters/topNews`。 设置定期计划的间隔和频率。 此示例每隔五分钟检查一次源。

   ![使用 RSS 源、频率和间隔设置触发器](./media/logic-apps-create-a-logic-app/rss-trigger-setup.png)

   逻辑应用创建到 RSS 源的连接。

   > [!TIP]
   > 若要简化设计器中的视图，可以折叠和隐藏某个形状的详细信息 - 直接在该形状的标题栏中单击。

3. 保存工作。 在设计器工具栏上，选择“保存”。 

   ![保存逻辑应用](./media/logic-apps-create-a-logic-app/save-logic-app.png)

   逻辑应用现已生成，但除了检查 RSS 源，不能执行任何操作。 因此，让我们添加一项在触发器触发时进行响应的操作。

## <a name="add-an-action-to-send-email"></a>添加一项用于发送电子邮件的操作

有了触发器以后，请添加一项[操作](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts)，在 RSS 源中出现新项时发送电子邮件。 工作流在触发器触发后执行此操作。

1. 在逻辑应用设计器的触发器下，选择“+ 新步骤” > “添加操作”。

   ![添加操作](./media/logic-apps-create-a-logic-app/add-new-action.png)

   设计器显示逻辑应用在触发器触发时能够执行的操作。

   ![从操作列表中选择](./media/logic-apps-create-a-logic-app/logic-app-actions.png)

2. 在搜索框中，输入“发送电子邮件”作为筛选器。 找到并选择要使用的电子邮件连接器。 然后选择该连接器的“发送电子邮件”操作。 例如： 

   * 对于 Azure 工作或学校帐户，请选择“Office 365 Outlook”。 
   * 对于个人 Microsoft 帐户，请选择“Outlook.com”。 
   * 对于 Gmail 帐户，请选择“Gmail”。 

   本快速入门使用 Office 365 Outlook。 
   如果使用其他电子邮件提供商，则这些步骤保持不变，但 UI 显示可能会有所不同。 

   ![选择操作“Office 365 Outlook - 发送电子邮件”](./media/logic-apps-create-a-logic-app/actions.png)

3. 系统提示输入凭据时，请使用电子邮件帐户的用户名和密码登录。 

   逻辑应用创建到电子邮件帐户的连接。

4. 现在指定要包括在电子邮件中的数据。 

   1. 在“收件人”框中，输入收件人的电子邮件地址。 
   为进行测试，可以使用自己的电子邮件地址。

   2. 在“主题”框中，输入电子邮件主题。 
   对于此示例，请输入“新的 RSS 项：”，如下所示：

      ![输入电子邮件主题](./media/logic-apps-create-a-logic-app/logic-app-add-subject.png)

      单击编辑框时，“添加动态内容”列表就会打开，方便你选择可以包括在操作中的数据字段。 
      如果动态内容列表没有打开，请在相应的编辑框下，选择“添加动态内容”。

   3. 从“添加动态内容”列表中选择“源标题”，其中包括项在电子邮件中的标题。

      ![输入电子邮件主题](./media/logic-apps-create-a-logic-app/logic-app-select-field.png)

      完成后，电子邮件主题如以下示例所示：

      ![添加的源标题](./media/logic-apps-create-a-logic-app/added-feed-title.png)

      > [!NOTE] 
      > 如果你碰巧选择了一个包含数组的字段，例如“categories-item”，设计器会围绕引用该字段的操作自动添加“For each”循环。 这样一来，逻辑应用就可以对每个数组项执行该操作。 
      > 
      > 若要删除循环，请选择循环的标题栏上的省略号 (...)，然后选择“删除”。

   4. 在“正文”框中，输入电子邮件正文内容。 
   对于此示例，请输入以下文本并选择以下字段：

      ![添加电子邮件正文内容](./media/logic-apps-create-a-logic-app/logic-app-complete.png)

      | 字段 | 说明 | 
      | ----- | ----------- | 
      | 源标题 | 显示项的标题。 | 
      | 源发布日期 | 显示项的发布日期和时间。 | 
      | 源主链接 | 显示项的 URL。 | 
      ||| 

      > [!TIP]
      > 若要在编辑框中添加空行，请按 Shift + Enter。 
      
5. 保存工作。 在设计器工具栏上，选择“保存”。

   ![完成的逻辑应用](./media/logic-apps-create-a-logic-app/save-complete-logic-app.png)

## <a name="run-your-logic-app-workflow"></a>运行逻辑应用工作流

若要手动启动逻辑应用，可在设计器工具栏中选择“运行”。 否则，可等待逻辑应用按设置的计划运行。

![运行逻辑应用](./media/logic-apps-create-a-logic-app/run-complete-logic-app.png)

如果 RSS 源有新项，逻辑应用会为每个新项发送一封电子邮件。 例如，下面是此逻辑应用发送的一封示例 Outlook 电子邮件：

![针对新的 RSS 源项发送的电子邮件](./media/logic-apps-create-a-logic-app/rss-feed-email.png)

如果源没有新项，逻辑应用会跳过发送电子邮件的步骤，等待下次再进行检查。 

> [!TIP]
> 如果没有收到任何电子邮件，请检查垃圾邮件文件夹。 否则，如果不确定逻辑应用是否正常运行，请参阅[逻辑应用故障排除](../logic-apps/logic-apps-diagnosing-failures.md)。

祝贺！你现已创建并运行第一个逻辑应用。 本快速入门介绍了如何才能轻松快捷地创建自动化工作流，以便集成系统和服务。

## <a name="clean-up-resources"></a>清理资源

在关闭或删除逻辑应用之前，该应用会持续运行，可能会产生 Azure 订阅费用。 另外，在为逻辑应用创建连接后，这些连接会始终存在，即使删除逻辑应用也是如此。 

在完成本快速入门后，请确保禁用或删除不希望其产生费用或不希望其保留的任何资源。 若要删除为本快速入门创建的所有资源，请删除为此逻辑应用创建的 Azure 资源组。 

### <a name="delete-resource-group"></a>删除资源组

如果不希望保留与逻辑应用相关的任何内容，请删除为本快速入门创建的资源组以及所有相关的资源。 详细了解[如何管理 Azure 资源组](../azure-resource-manager/resource-group-portal.md#manage-resources)。

1. 在 Azure 菜单上选择“资源组”。

2. 选择要删除的资源组。 在资源组菜单中选择“概览”（如果尚未选择）。 

3. 查看资源组中需删除的所有资源。 准备就绪后，选择资源组工具栏中的“删除资源组”。

### <a name="turn-off-logic-app"></a>关闭逻辑应用

若要在不删除所做工作的情况下停止运行逻辑应用，请禁用应用。 

在逻辑应用菜单上，选择“概述”。 在工具栏上，选择“禁用”。

  ![关闭逻辑应用](./media/logic-apps-create-a-logic-app/turn-off-disable-logic-app.png)

  > [!TIP]
  > 如果看不到逻辑应用菜单，请尝试返回到 Azure 仪表板，然后重新打开逻辑应用。

### <a name="delete-logic-app"></a>删除逻辑应用

可以只删除逻辑应用，但保留所有其他相关的资源，例如所创建的连接。

1. 在逻辑应用菜单上选择“概览”。 在工具栏上选择“删除”。 

   ![删除逻辑应用](./media/logic-apps-create-a-logic-app/delete-logic-app.png)

   > [!TIP]
   > 如果看不到逻辑应用菜单，请尝试返回到 Azure 仪表板，然后重新打开逻辑应用。

2. 确认要删除逻辑应用，然后选择“删除”。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能方面的想法或进行相关投票，请访问[逻辑应用用户反馈站点](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [从预生成的模板创建逻辑应用工作流](../logic-apps/logic-apps-create-logic-apps-from-templates.md)