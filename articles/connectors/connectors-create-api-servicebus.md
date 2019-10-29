---
title: 与 Azure 服务总线交换消息-Azure 逻辑应用
description: 在 Azure 逻辑应用中使用 Azure 服务总线发送和接收消息
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 09/19/2019
tags: connectors
ms.openlocfilehash: 477fdc2291d875c57bfb0ae3cb87b6df9bd41398
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024298"
---
# <a name="exchange-messages-in-the-cloud-by-using-azure-logic-apps-and-azure-service-bus"></a>使用 Azure 逻辑应用和 Azure 服务总线在云中交换消息

通过[Azure 逻辑应用](../logic-apps/logic-apps-overview.md)和[azure 服务总线](../service-bus-messaging/service-bus-messaging-overview.md)连接器，你可以创建自动任务和工作流，以便在组织的应用程序之间传输数据（例如销售和采购订单、日志和清单移动）。 连接器不仅会监视、发送和管理消息，而且还能对队列、会话、主题、订阅等执行操作，例如：

* 监视消息何时抵达（自动完成），或者在队列、主题和主题订阅中接收（扫视-锁定）。
* 发送消息。
* 创建和删除主题订阅。
* 管理队列和主题订阅中的消息，例如，获取、获取延期消息、完成、延期、丢弃和加入死信。
* 续订队列和主题订阅中的消息和会话上的锁。
* 关闭队列和主题中的会话。

可以使用触发器从服务总线获取响应，并使输出可供逻辑应用中的其他操作使用。 还可以让其他操作使用服务总线操作的输出。 如果你不熟悉服务总线和逻辑应用，请查看[什么是 Azure 服务总线？](../service-bus-messaging/service-bus-messaging-overview.md)什么是[azure 逻辑应用](../logic-apps/logic-apps-overview.md)？

[!INCLUDE [Warning about creating infinite loops](../../includes/connectors-infinite-loops.md)]

## <a name="prerequisites"></a>必备组件

* Azure 订阅。 如果没有 Azure 订阅，请[注册一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

* 服务总线命名空间和消息传送实体，例如队列。 这些项和逻辑应用需要使用同一 Azure 订阅。 如果没有这些项，请了解如何[创建服务总线命名空间和队列](../service-bus-messaging/service-bus-create-namespace-portal.md)。

* 有关[如何创建逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)的基本知识

* 使用服务总线命名空间和消息传送实体的逻辑应用。 逻辑应用和服务总线需要使用同一 Azure 订阅。 若要使用服务总线触发器启动工作流，请[创建一个空白逻辑应用](../logic-apps/quickstart-create-first-logic-app-workflow.md)。 若要在工作流中使用服务总线操作，请使用另一个触发器（例如，[重复触发器](../connectors/connectors-native-recurrence.md)）启动逻辑应用。

<a name="permissions-connection-string"></a>

## <a name="check-permissions"></a>检查权限

确认逻辑应用有权访问服务总线命名空间。

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 转到服务总线的命名空间。 在命名空间页上的“设置”下，选择“共享访问策略”。 在“声明”下，检查你是否有该命名空间的“管理”权限。

   ![管理服务总线命名空间的权限](./media/connectors-create-api-azure-service-bus/azure-service-bus-namespace.png)

1. 获取服务总线命名空间的连接字符串。 在逻辑应用中提供连接信息时，需要此字符串。

   1. 在 "**共享访问策略**" 窗格中，选择 " **RootManageSharedAccessKey**"。
   
   1. 在主连接字符串旁边，选择 "复制" 按钮。 保存连接字符串供以后使用。

      ![复制服务总线命名空间连接字符串](./media/connectors-create-api-azure-service-bus/find-service-bus-connection-string.png)

   > [!TIP]
   > 若要确认连接字符串是与服务总线命名空间关联还是与消息传送实体（例如队列）关联，请在该连接字符串中搜索 `EntityPath`  参数。 如果找到了该参数，则表示连接字符串适用于特定的实体，不是适用于逻辑应用的正确字符串。

## <a name="add-service-bus-trigger"></a>添加 Service Bus 触发器

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到[Azure 门户](https://portal.azure.com)，然后在逻辑应用设计器中打开空白逻辑应用。

1. 在搜索框中，输入 "azure 服务总线" 作为筛选器。 从 "触发器" 列表中，选择所需的触发器。

   例如，若要在将新项发送到服务总线队列时触发逻辑应用，请选择 "在**队列中收到消息（自动完成）** " 触发器。

   ![选择服务总线触发器](./media/connectors-create-api-azure-service-bus/select-service-bus-trigger.png)

   所有服务总线触发器都是*长轮询*触发器。 此说明表示当触发器触发时，触发器将处理所有消息，然后等待30秒，以等待更多消息出现在队列或主题订阅中。 如果在 30 秒内未显示任何消息，则会跳过触发器运行。 否则，该触发器将继续读取消息，直到队列或主题订阅为空。 下一次触发器轮询将基于在触发器的属性中指定的重复周期间隔。

   某些触发器（如一条**或多条消息到达队列（自动完成）** 触发器）可以返回一条或多条消息。 当这些触发器触发时，它们会返回一个，以及触发器的**最大消息计数**属性指定的消息数。

1. 如果触发器首次连接到服务总线命名空间，请在逻辑应用设计器提示你提供连接信息时执行以下步骤。

   1. 请提供连接名称，并选择服务总线命名空间。

      ![创建服务总线连接 - 第 1 部分](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-1.png)

      若要改为手动输入连接字符串，请选择 "**手动输入连接信息**"。 如果没有连接字符串，请了解[如何查找连接字符串](#permissions-connection-string)。

   1. 选择服务总线策略，并选择 "**创建**"。

      ![创建服务总线连接第 2 部分](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-trigger-2.png)

   1. 选择所需的消息传递实体，例如队列或主题。 对于本示例，请选择服务总线队列。
   
      ![选择服务总线队列](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-trigger.png)

1. 提供选定触发器所需的信息。 若要向操作添加其他可用属性，请打开 "**添加新参数**" 列表，然后选择所需的属性。

   对于此示例的触发器，请选择用于检查队列的轮询间隔和频率。

   ![设置轮询间隔](./media/connectors-create-api-azure-service-bus/service-bus-trigger-details.png)

   有关可用触发器和属性的详细信息，请参阅连接器的[参考页](/connectors/servicebus/)。

1. 通过添加所需的操作，继续生成逻辑应用。

   例如，可以添加一个可在收到新消息时发送电子邮件的操作。 当触发器检查队列并找到新消息时，逻辑应用会针对找到的消息运行选定的操作。

## <a name="add-service-bus-action"></a>添加 Service Bus 操作

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. 登录到[Azure 门户](https://portal.azure.com)，然后在逻辑应用设计器中打开逻辑应用。

1. 在要添加操作的步骤下，选择 "**新建步骤**"。

   或者，若要在步骤之间添加操作，请将指针移到这些步骤之间的箭头上。 选择出现的加号（ **+** ），然后选择 "**添加操作**"。

1. 在 "**选择操作**" 下的 "搜索" 框中，输入 "azure 服务总线" 作为筛选器。 从 "操作" 列表中，选择所需的操作。 

   对于本示例，请选择 "**发送消息**" 操作。

   ![选择服务总线操作](./media/connectors-create-api-azure-service-bus/select-service-bus-send-message-action.png) 

1. 如果你的操作是第一次连接到服务总线命名空间，请在逻辑应用设计器提示你提供连接信息时执行以下步骤。

   1. 请提供连接名称，并选择服务总线命名空间。

      ![创建服务总线连接 - 第 1 部分](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-1.png)

      若要改为手动输入连接字符串，请选择 "**手动输入连接信息**"。 如果没有连接字符串，请了解[如何查找连接字符串](#permissions-connection-string)。

   1. 选择服务总线策略，并选择 "**创建**"。

      ![创建服务总线连接第 2 部分](./media/connectors-create-api-azure-service-bus/create-service-bus-connection-action-2.png)

   1. 选择所需的消息传递实体，例如队列或主题。 对于本示例，请选择服务总线队列。

      ![选择服务总线队列](./media/connectors-create-api-azure-service-bus/service-bus-select-queue-action.png)

1. 为所选操作提供必要的详细信息。 若要向操作添加其他可用属性，请打开 "**添加新参数**" 列表，然后选择所需的属性。

   例如，选择 "**内容**" 和 "**内容类型**" 属性，以便将它们添加到操作中。 然后，指定要发送的邮件的内容。

   ![提供消息内容和详细信息](./media/connectors-create-api-azure-service-bus/service-bus-send-message-details.png)

   有关可用操作及其属性的详细信息，请参阅连接器的[参考页](/connectors/servicebus/)。

1. 继续生成逻辑应用，方法是添加所需的任何其他操作。

   例如，你可以添加一个发送电子邮件的操作，以确认你的邮件已发送。

1. 保存逻辑应用。 在设计器工具栏上，选择“保存”。

## <a name="connector-reference"></a>连接器参考

服务总线连接器一次最多可以从服务总线向连接器缓存保存1500个唯一会话。 如果会话计数超过此限制，则将从缓存中删除旧会话。 有关由连接器的 OpenAPI （以前的 Swagger）说明描述的触发器、操作和限制的其他技术详细信息，请查看连接器的[参考页](/connectors/servicebus/)。

## <a name="next-steps"></a>后续步骤

* 了解其他[逻辑应用连接器](../connectors/apis-list.md)
