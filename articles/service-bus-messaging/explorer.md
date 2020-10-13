---
title: 使用 Azure 服务总线资源管理器在服务总线上执行数据操作（预览版）
description: 本文介绍如何使用基于门户的 Azure 服务总线资源管理器访问 Azure 服务总线数据。
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: e5e97c6860c2cc01048f4f7caf9f40f9e07592d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295594"
---
# <a name="use-service-bus-explorer-to-perform-data-operations-on-service-bus-preview"></a>使用服务总线资源管理器在服务总线上执行数据操作（预览版）

## <a name="overview"></a>概述

借助 Azure 服务总线，发送方和接收方客户端应用程序可以使用熟悉的点到点（队列）和发布-订阅（主题-订阅）语义来解耦其业务逻辑。

在 Azure 服务总线命名空间上执行的操作分为两种类型 
   * 管理操作 - 创建、更新和删除服务总线命名空间、队列、主题与订阅。
   * 数据操作 - 向/从队列、主题和订阅发送/接收消息。

Azure 服务总线资源管理器扩展了门户功能，使其不仅支持管理操作，而且还支持对队列、主题和订阅（及其死信子实体）执行数据操作（发送、接收、速览）- 所有这些操作可以直接在 Azure 门户中执行。

> [!NOTE]
> 本文重点介绍 Azure 门户上的 Azure 服务总线资源管理器功能。
>
> Azure 服务总线资源管理器工具不是社区拥有的 OSS 工具 [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer)。
>

## <a name="prerequisites"></a>先决条件

若要使用服务总线资源管理器工具，需要预配一个 Azure 服务总线命名空间。 

预配服务总线命名空间后，需要创建一个队列用于发送和接收消息，或者创建一个包含订阅的主题用于测试功能。

若要详细了解如何创建队列、主题和订阅，请参阅以下链接
   * [快速入门 - 创建队列](service-bus-quickstart-portal.md)
   * [快速入门 - 创建主题](service-bus-quickstart-topics-subscriptions-portal.md)


## <a name="using-the-service-bus-explorer"></a>使用服务总线资源管理器

若要使用 Azure 服务总线资源管理器，需要导航到要在其中执行发送、速览和接收操作的服务总线命名空间。

如果想要对队列执行操作，请从导航菜单中选择“队列”。 如果想要对主题（及其相关订阅）执行操作，请选择“主题”。 

:::image type="content" source="./media/service-bus-explorer/queue-topics-left-navigation.png"alt-text="实体选择":::

选择“队列”或“主题”后，选择特定的队列或主题。 

从左侧导航菜单中选择“服务总线资源管理器(预览版)”

:::image type="content" source="./media/service-bus-explorer/left-navigation-menu-selected.png" alt-text="实体选择":::

### <a name="sending-a-message-to-a-queue-or-topic"></a>向队列或主题发送消息

若要向**队列**或**主题**发送消息，请单击服务总线资源管理器中的“发送”选项卡。

若要在此处撰写消息 - 

1. 选取“Text/Plain”、“Application/Xml”或“Application/Json”作为“内容类型”。
2. 添加消息**内容**。 确保此内容与前面设置的“内容类型”相匹配。
3. 设置“高级属性”（可选）- 包括“相关 ID”、“消息 ID”、“标签”、“ReplyTo”、“生存时间(TTL)”和“计划排队时间”（适用于计划的消息）。
4. 设置“自定义属性”- 可以是针对字典键设置的任何用户属性。

撰写消息后，点击“发送”。

:::image type="content" source="./media/service-bus-explorer/send-experience.png" alt-text="实体选择":::

发送操作成功完成后， 

* 如果是发送到队列，则“活动消息”指标计数器将会递增。

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="实体选择":::

* 如果是发送到主题，则“活动消息”指标计数器将在消息路由到的订阅上递增。

    :::image type="content" source="./media/service-bus-explorer/topic-after-send-metrics.png" alt-text="实体选择":::

### <a name="receiving-a-message-from-a-queue"></a>接收来自队列的消息

服务总线资源管理器中的 receive 函数允许一次接收一条消息。 接收操作是使用 **ReceiveAndDelete** 模式执行的。

> [!IMPORTANT]
> 请注意，服务总线资源管理器执行的“接收”操作是一种破坏性接收，即，当消息显示在服务总线资源管理器工具中时，会将其从队列中删除。
>
> 若要浏览消息而不将其从队列中删除，请考虑使用“速览”功能。
>

从队列（或其死信子队列）接收消息 

1. 单击服务总线资源管理器中的“接收”选项卡。
2. 检查指标，确定是否存在可接收的“活动消息”或“死信消息”。 

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="实体选择":::

3. 在“队列”与“死信”子队列之间进行选择。

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="实体选择":::

4. 单击“接收”按钮，然后单击“是”以确认执行“接收并删除”操作。


接收操作成功后，消息详细信息会显示在网格中，如下所示。 可以在网格中选择该消息以显示其详细信息。

:::image type="content" source="./media/service-bus-explorer/receive-message-from-queue-2.png" alt-text="实体选择":::


### <a name="peeking-a-message-from-a-queue"></a>速览来自队列的消息

可以使用服务总线资源管理器中的速览功能来查看队列或死信队列中的前 32 条消息。

1. 若要速览队列中的消息，请单击服务总线资源管理器中的“速览”选项卡。

    :::image type="content" source="./media/service-bus-explorer/peek-tab-selected.png" alt-text="实体选择":::

2. 检查指标，确定是否存在可速览的“活动消息”或“死信消息”。 

    :::image type="content" source="./media/service-bus-explorer/queue-after-send-metrics.png" alt-text="实体选择":::

3. 然后在“队列”与“死信”子队列之间进行选择。

    :::image type="content" source="./media/service-bus-explorer/queue-or-deadletter.png" alt-text="实体选择":::

4. 单击“速览”按钮。 

速览操作完成后，网格中最多会显示 32 条消息，如下所示。 若要查看特定消息的详细信息，请在网格中选择该消息。 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-queue-2.png" alt-text="实体选择":::

> [!NOTE]
>
> 由于速览不是破坏性操作，因此**不会**从队列中删除该消息。
>

### <a name="receiving-a-message-from-a-subscription"></a>从订阅接收消息

与队列一样，可以对订阅（或其死信实体）执行“接收”操作。 但是，由于订阅存在于主题的上下文中，因此可以通过导航到给定主题的服务总线资源管理器来执行接收操作。

> [!IMPORTANT]
> 请注意，服务总线资源管理器执行的“接收”操作是一种破坏性接收，即，当消息显示在服务总线资源管理器工具中时，会将其从队列中删除。
>
> 若要浏览消息而不将其从队列中删除，请考虑使用“速览”功能。
>

1. 单击“接收”选项卡，然后从下拉选择器中选择特定的***订阅***。

    :::image type="content" source="./media/service-bus-explorer/receive-subscription-tab-selected.png" alt-text="实体选择":::

2. 在“订阅”与“死信”子实体之间进行选择。

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="实体选择":::

3. 单击“接收”按钮，然后单击“是”以确认执行“接收并删除”操作。

接收操作成功后，收到的消息会显示在网格中，如下所示。 若要查看消息详细信息，请单击该消息。

:::image type="content" source="./media/service-bus-explorer/receive-message-from-subscription.png" alt-text="实体选择":::

### <a name="peeking-a-message-from-a-subscription"></a>速览来自订阅的消息

如果只是想浏览订阅或其死信子实体中的消息，也可以对订阅使用“速览”功能。

1. 单击“速览”选项卡，然后从下拉选择器中选择特定的***订阅***。

    :::image type="content" source="./media/service-bus-explorer/peek-subscription-tab-selected.png" alt-text="实体选择":::

2. 在“订阅”与“死信”子实体之间进行选择。

    :::image type="content" source="./media/service-bus-explorer/subscription-or-deadletter.png" alt-text="实体选择":::

3. 单击“速览”按钮。

速览操作完成后，网格中最多会显示 32 条消息，如下所示。 若要查看特定消息的详细信息，请在网格中选择该消息。 

:::image type="content" source="./media/service-bus-explorer/peek-message-from-subscription.png" alt-text="实体选择":::

> [!NOTE]
>
> 由于速览不是破坏性操作，因此**不会**从队列中删除该消息。
>

## <a name="next-steps"></a>后续步骤

   * 详细了解服务总线[队列](service-bus-queues-topics-subscriptions.md#queues)和[主题](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)
   * 详细了解如何[通过 Azure 门户创建服务总线队列](service-bus-quickstart-portal.md)
   * 详细了解如何[通过 Azure 门户创建服务总线主题和订阅](service-bus-quickstart-topics-subscriptions-portal.md)