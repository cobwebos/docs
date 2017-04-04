---
title: "使用第一个 Azure 逻辑应用创建工作流 | Microsoft 文档"
description: "开始使用第一个逻辑应用连接应用和 SaaS 服务"
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: ce3582b5-9c58-4637-9379-75ff99878dcd
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/25/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 221f1b9f0985bbaf0553f6ca01f0f048b9976315
ms.lasthandoff: 03/28/2017


---
# <a name="create-a-new-logic-app-connecting-saas-services"></a>创建连接 SaaS 服务的新逻辑应用
本主题演示如何操作，只需几分钟时间，你便可以开始使用 [Azure 逻辑应用](logic-apps-what-are-logic-apps.md)。 我们将演练一个允许你向你的电子邮件发送有趣推文的简单工作流。

若要使用此方案，你需要︰

* Azure 订阅
* Twitter 帐户
* Outlook.com 或 Office 365 Outlook 帐户

## <a name="create-a-new-logic-app-to-email-you-tweets"></a>创建新的逻辑应用以通过电子邮件向你发送推文

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 从左侧菜单中选择“新建” > “Enterprise Integration” > “逻辑应用”。

    也可选择“新建”，然后在搜索框中键入 `logic app`，再按 Enter。 选择“逻辑应用” > “创建”。

3. 输入逻辑应用的名称，选择 Azure 订阅，创建或选择 Azure 资源组，选择位置，然后选择“创建”。

    如果选择“固定到仪表板”，则逻辑应用会在部署后自动打开。

4. 第一次打开逻辑应用后，可以选择从模板开始。
现在，请单击“ **空逻辑应用** ”从头开始构建。 

5. 首先需要创建的项目是触发器。 这是启动逻辑应用的事件。 在搜索框中搜索“twitter”，然后选择“发布新推文时”。 使用 Twitter 帐户的用户名和密码登录。

6. 现在请键入触发逻辑应用的搜索词。

   ![Twitter 搜索](media/logic-apps-create-a-logic-app/twittersearch.png)

    “频率”和“间隔”用于确定逻辑应用检查新推文的频率，并返回相应时间跨度内的所有推文。

7. 选择“新建步骤”，然后选择“添加操作”或“添加条件”。

    选择“添加操作”时，可以通过搜索“可用连接器”来选择操作。[](../connectors/apis-list.md) 

8. 在搜索框中搜索“outlook”，然后选择“发送电子邮件”，以便从 Outlook 帐户向任何指定的电子邮件地址发送电子邮件。

   ![操作](media/logic-apps-create-a-logic-app/actions.png)

9. 接着，需要填写电子邮件所需的参数︰

   ![Parameters](media/logic-apps-create-a-logic-app/parameters.png)

10. 最后，可以选择“ **保存** ”以使逻辑应用生效。

## <a name="manage-your-logic-app-after-creation"></a>在创建后管理逻辑应用

现在，你的逻辑应用已启动并运行。 它将使用输入的搜索词定期检查推文。 当它找到匹配的推文时，它会向你发送一封电子邮件。 最后，将学习如何禁用此应用，或了解它是如何运行的。

1. 转到 [Azure 门户](https://portal.azure.com)。

2. 在左侧菜单上单击“更多服务”。 在“Enterprise Integration”下选择“逻辑应用”。 选择逻辑应用。

    *    若要查看应用的状态、执行历史记录和常规信息，请在逻辑应用菜单上选择“概览”。 如果找不到预期的数据，请在命令栏中选择“刷新”。

    *    若要编辑应用，请在逻辑应用菜单上选择“逻辑应用设计器”。

    *    若要暂时关闭应用，请在逻辑应用菜单上选择“概览”。 在命令栏中选择“禁用”。

    *    若要删除应用，请在逻辑应用菜单上选择“概览”。 
    在命令栏中选择“删除”。 输入逻辑应用的名称，然后选择“删除”。

在 5 分钟内，你就能够设置一个在云中运行的简单逻辑应用。 若要了解有关使用逻辑应用功能的详细信息，请参阅 [使用逻辑应用功能]。 若要了解有关逻辑应用定义本身的信息，请参阅 [创作逻辑应用定义](../logic-apps/logic-apps-author-definitions.md)。

<!-- Shared links -->
[Azure portal]: https://portal.azure.com
[使用逻辑应用功能]: logic-apps-create-a-logic-app.md
