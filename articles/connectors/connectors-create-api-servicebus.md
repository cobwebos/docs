---
title: 使用 Azure 服务总线发送和接收消息 - Azure 逻辑应用 | Microsoft Docs
description: 在 Azure 逻辑应用中使用 Azure 服务总线设置企业云消息传送
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 68378c87e18df874059579445352b8fd1b2b6c13
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105574"
---
# <a name="exchange-messages-in-the-cloud-with-azure-service-bus-and-azure-logic-apps"></a>使用 Azure 服务总线和 Azure 逻辑应用在云中交换消息

使用 Azure 逻辑应用和 Azure 服务总线连接器，可以创建自动化的任务和工作流用于传输数据，例如，销售和采购订单、日记帐，以及组织应用之间的库存转移信息。 连接器不仅会监视、发送和管理消息，而且还能对队列、会话、主题、订阅等执行操作，例如：

* 监视消息何时抵达（自动完成），或者在队列、主题和主题订阅中接收（扫视-锁定）。 
* 发送消息。
* 创建和删除主题订阅。
* 管理队列和主题订阅中的消息，例如，获取、获取延期消息、完成、延期、丢弃和加入死信。
* 续订队列和主题订阅中的消息和会话上的锁。
* 关闭队列和主题中的会话。

可以使用触发器从服务总线获取响应，并使输出可供逻辑应用中的其他操作使用。 还可以让其他操作使用服务总线操作的输出。 如果你不太熟悉服务总线和逻辑应用，请查看[什么是 Azure 服务总线？](../service-bus-messaging/service-bus-messaging-overview.md) 和[什么是 Azure 逻辑应用？](../logic-apps/logic-apps-overview.md)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请<a href="https://azure.microsoft.com/free/" target="_blank">注册一个免费 Azure 帐户</a>。 

* 服务总线命名空间和消息传送实体，例如队列。 如果没有这些项，请了解如何[创建服务总线命名空间和队列](../service-bus-messaging/service-bus-create-namespace-portal.md)。 

  这些项必须位于使用它们的逻辑应用所在的同一 Azure 订阅中。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 要在其中使用服务总线的逻辑应用。 逻辑应用必须位于服务总线所在的同一 Azure 订阅中。 若要从服务总线触发器开始，请[创建空白的逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要使用服务总线操作，请使用另一个触发器（例如“重复”触发器）启动逻辑应用。

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>检查权限

确认逻辑应用有权访问服务总线命名空间。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 

2. 转到服务总线的命名空间。 在命名空间页上的“设置”下，选择“共享访问策略”。 在“声明”下，检查你是否对该命名空间拥有“管理”权限。

   ![管理服务总线命名空间的权限](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

3. 获取服务总线命名空间的连接字符串。 在逻辑应用中输入连接信息时需要此字符串。

   1. 选择“RootManageSharedAccessKey”。 
   
   1. 在主连接字符串的旁边，选择“复制”按钮。 保存连接字符串供以后使用。

      ![复制服务总线命名空间连接字符串](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 若要确认连接字符串是与服务总线命名空间关联还是与消息传送实体（例如队列）关联，请在该连接字符串中搜索 `EntityPath`  参数。 如果找到了该参数，则表示连接字符串适用于特定的实体，不是适用于逻辑应用的正确字符串。

## <a name="add-trigger-or-action"></a>添加触发器或操作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到 [Azure 门户](https://portal.azure.com)，在逻辑应用设计器中打开逻辑应用（如果尚未打开）。

1. 若要将触发器添加到空白逻辑应用，请在搜索框中输入“Azure 服务总线”作为筛选器。 在触发器列表下，选择所需的触发器。 

   例如，若要在新项发送到服务总线队列时触发逻辑应用，选择以下触发器：**（自动完成） 的队列中收到一条消息时**

   ![选择服务总线触发器](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   > [!NOTE]
   > 某些触发器会返回一条或多条消息，例如“一条或多条消息抵达队列时(自动完成)”触发器。 当这些触发器触发时，它们会返回一定数目的消息，消息数介于 1 和由触发器的**最大消息计数**属性指定的数目之间。

   所有服务总线触发器都是长轮询触发器，这意味着，当触发器激发时，触发器将处理所有消息，然后等待 30 秒，让更多的消息出现在队列或主题订阅中。 
   如果在 30 秒内未显示任何消息，则会跳过触发器运行。 
   否则，该触发器将继续读取消息，直到队列或主题订阅为空。 下一次触发器轮询将基于在触发器的属性中指定的重复周期间隔。

1. 若要将操作添加到现有逻辑应用，请执行以下步骤： 

   1. 在要添加操作的最后一个步骤下，选择“新建步骤”。 

      若要在步骤之间添加操作，请将鼠标指针移到步骤之间的箭头上。 
      选择出现的加号 (**+**)，然后选择“添加操作”。

   1. 在搜索框中，输入“Azure 服务总线”作为筛选器。 
   在操作列表下，选择所需的操作。 
 
      例如，选择此操作：**发送消息**

      ![选择服务总线操作](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. 如果你是首次将逻辑应用连接到服务总线命名空间，则逻辑应用设计器现在会提示你输入连接信息。 

   1. 请提供连接名称，并选择服务总线命名空间。

      ![创建服务总线连接 - 第 1 部分](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-1.png)

      若要手动输入连接字符串，请选择“手动输入连接信息”。 
      如果没有连接字符串，请了解[如何查找连接字符串](#permissions-connection-string)。

   1. 现在选择服务总线策略，然后选择“创建”。

      ![创建服务总线连接第 2 部分](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-2.png)

1. 对于本示例，请选择所需的消息传送实体，例如某个队列或主题。 在本示例中，请选择服务总线队列。 
   
   ![选择服务总线队列](./media/connectors-create-api-azure-service-bus/service-bus-select-queue.png)

1. 提供触发器或操作的所需详细信息。 对于本示例，请遵循触发器或操作的相关步骤： 

   * **示例触发器**:设置轮询间隔和检查队列的频率。

     ![设置轮询间隔](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

     完成后，通过添加所需的操作继续生成逻辑应用的工作流。 例如，可以添加一个可在收到新消息时发送电子邮件的操作。
     当触发器检查队列并找到新消息时，逻辑应用会针对找到的消息运行选定的操作。

   * **示例操作**:输入消息内容和任何其他详细信息。 

     ![提供消息内容和详细信息](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

     完成后，通过添加所需的其他任何操作继续生成逻辑应用的工作流。 例如，可以添加一个操作来发送电子邮件，确认消息已发送。

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

## <a name="connector-reference"></a>连接器参考

有关触发器、操作和限制（请参阅连接器的 OpenAPI（以前称为 Swagger）说明）的技术详细信息，请查看连接器的[参考页](/connectors/servicebus/)。

## <a name="get-support"></a>获取支持

* 有关问题，请访问 [Azure 逻辑应用论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)。
* 若要提交功能建议或对功能建议进行投票，请访问[逻辑应用用户反馈网站](https://aka.ms/logicapps-wish)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)