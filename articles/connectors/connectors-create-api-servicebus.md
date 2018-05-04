---
title: 使用 Azure 服务总线为 Azure 逻辑应用设置消息传送 | Microsoft Docs
description: 通过逻辑应用使用 Azure 服务总线发送和接收消息
services: logic-apps
documentationcenter: ''
author: ecfan
manager: anneta
editor: ''
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 02/06/2018
ms.author: ladocs
ms.openlocfilehash: d5a4760e1e0f38fd81fd779786985f5753d77eab
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="send-and-receive-messages-with-the-azure-service-bus-connector"></a>使用 Azure 服务总线连接器发送和接收消息

要通过逻辑应用发送和接收消息，请连接到 [Azure 服务总线](https://azure.microsoft.com/services/service-bus/)。 你可以执行发送到队列、发送到主题、从队列接收和从订阅接收等操作。 详细了解 [Azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)以及[逻辑应用触发器的定价机制](../logic-apps/logic-apps-pricing.md)。

## <a name="prerequisites"></a>先决条件

必须具有下列项，然后才能使用服务总线连接器，这些项必须存在于同一 Azure 订阅中以便它们可以发现彼此：

* [服务总线命名空间和消息传送实体，例如队列](../service-bus-messaging/service-bus-create-namespace-portal.md)
* [逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)

<a name="permissions-connection-string"></a>

## <a name="connect-to-azure-service-bus"></a>连接到 Azure 服务总线

要使逻辑应用能够访问某个服务，必须在逻辑应用与该服务之间创建[连接](./connectors-overview.md)（如果尚未创建）。 该连接授权逻辑应用访问数据。 要使逻辑应用能够访问服务总线帐户，请检查你的权限。

1. 登录 [Azure 门户](https://portal.azure.com "Azure portal")。 

2. 转到你的服务总线“命名空间”，而非特定的“消息传送实体”。 在命名空间页上，在“设置”下，选择“共享访问策略”。 在“声明”下，检查你是否有该命名空间的“管理”权限。

   ![管理服务总线命名空间的权限](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. 如果希望在以后手动输入连接信息，请获取你的服务总线命名空间的连接字符串。 选择“RootManageSharedAccessKey”。 在主密钥连接字符串旁边选择复制按钮。 保存连接字符串供以后使用。

   ![复制服务总线命名空间连接字符串](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 若要确认连接字符串是与服务总线命名空间关联，还是与特定的实体关联，请检查该连接字符串中是否存在 `EntityPath` 参数。 如果找到了该参数，则说明连接字符串适用于特定实体，不是适用于逻辑应用的正确字符串。

## <a name="trigger-workflow-when-your-service-bus-gets-new-messages"></a>在服务总线收到新消息时触发工作流

[触发器](../logic-apps/logic-apps-overview.md#logic-app-concepts)是一个事件，在逻辑应用中启动工作流。 若要在有新消息发送到服务总线时启动工作流，请执行以下步骤来添加用于检测这些消息的触发器。

1. 在 [Azure 门户](https://portal.azure.com "Azure 门户")中转到现有的逻辑应用，或者创建一个空白逻辑应用。

2. 在逻辑应用设计器中，在搜索框中输入“服务总线”作为筛选器。 选择“服务总线”连接器。 

   ![选择“服务总线”连接器](./media/connectors-create-api-azure-service-bus/select-service-bus-connector.png) 

3. 选择要使用的触发器。 例如，若要在有新项发送到服务总线队列时运行某个逻辑应用，请选择以下触发器：“服务总线 - 队列中收到邮件时(自动完成)”。

   ![选择服务总线触发器](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > 某些触发器会返回一条或多条消息，例如 *Service Bus - When one or more messages arrive in a queue (auto-complete)* 触发器。
   > 当这些触发器触发时，它们会返回一定数目的消息，消息数介于 1 和由触发器的**最大消息计数**属性指定的数目之间。

   1. 如果还没有到服务总线命名空间的连接，则会提示你立即创建该连接。 为连接提供一个名称，然后选择要使用的服务总线命名空间。

      ![创建服务总线连接](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      或者，若要手动输入连接字符串，请选择“手动输入连接信息”。 
      了解[如何查找连接字符串](#permissions-connection-string)。
      

   2. 现在，选择要使用的服务总线策略，然后选择“创建”。

      ![创建服务总线连接第 2 部分](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

4. 选择要使用的服务总线队列，并针对何时检查队列设置间隔和频率。

   ![选择服务总线队列，设置轮询间隔](./media/connectors-create-api-azure-service-bus/select-service-bus-queue.png)

   > [!NOTE]
   > 所有服务总线触发器都是**长轮询**触发器，这意味着当触发器触发时，触发器将处理所有消息，然后等待 30 秒，以等待更多消息出现在队列或主题订阅中。
   > 如果在 30 秒内未收到消息，则会跳过触发器运行。 否则，该触发器将继续读取消息，直到队列或主题订阅为空。
   > 下一次触发器轮询将基于在触发器的属性中指定的重复周期间隔。

5. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

现在，当逻辑应用检索所选队列并找到新消息时，触发器将针对找到的消息在逻辑应用中运行操作。

## <a name="send-messages-from-your-logic-app-to-your-service-bus"></a>从逻辑应用向服务总线发送消息

[*操作*](../logic-apps/logic-apps-overview.md#logic-app-concepts)是逻辑应用工作流执行的任务。 将触发器添加到逻辑应用后，可以添加一个操作，以便使用该触发器生成的数据执行操作。 若要从逻辑应用向服务总线消息传送实体发送消息，请执行以下步骤。

1. 在逻辑应用设计器中，在你的触发器下，选择“+ 新建步骤” > “添加操作”。

2. 在搜索框中，输入“服务总线”作为筛选器。 选择以下连接器：“服务总线”

   ![选择“服务总线”连接器](./media/connectors-create-api-azure-service-bus/select-service-bus-connector-for-action.png) 

3. 选择以下操作：“服务总线 - 发送消息”

   ![选择“服务总线 - 发送消息”](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png)

4. 选择消息传送实体，这是要为其发送消息的队列或主题名称。 然后，输入消息内容和任何其他详细信息。

   ![选择消息传送实体并提供消息详细信息](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)    

5. 保存逻辑应用。 

现在，你已设置了一个用于从逻辑应用发送消息的操作。 

## <a name="connector-specific-details"></a>特定于连接器的详细信息

若要详细了解由 Swagger 文件和任何限制定义的触发器和操作，请查看[连接器详细信息](/connectors/servicebus/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](http://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 详细了解[适用于 Azure 逻辑应用的其他连接器](../connectors/apis-list.md)