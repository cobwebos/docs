---
title: 快速入门 - 处理短信事件
titleSuffix: An Azure Communication Services quickstart
description: 了解如何使用 Azure 通信服务处理短信事件。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 02a0a5dd5cfe3a1ecaa264f1e81c848a8da6c58d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943513"
---
# <a name="quickstart-handle-sms-events"></a>快速入门：处理短信事件

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

通过 Azure 事件网格处理通信服务短信事件，开始使用 Azure 通信服务。 

## <a name="about-azure-event-grid"></a>关于 Azure 事件网格

[Azure 事件网格](https://docs.microsoft.com/azure/event-grid/overview)是一种基于云的事件处理服务。 在本文中，你将了解如何针对[通信服务事件](../../concepts/event-handling.md)订阅事件，以及触发事件以查看结果。 通常，你会将事件发送到处理事件数据并执行操作的终结点。 在本文中，我们会将事件发送到收集并显示消息的 Web 应用。

## <a name="prerequisites"></a>先决条件
- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 
- Azure 通信服务资源。 可在[创建 Azure 通信资源](../create-communication-resource.md)快速入门中找到更多详细信息。
- 启用短信的电话号码。 [获取电话号码](./get-phone-number.md)。

## <a name="setting-up"></a>设置

### <a name="enable-event-grid-resource-provider"></a>启用事件网格资源提供程序

如果以前未在 Azure 订阅中使用过事件网格，则可能需要按照以下步骤注册事件网格资源提供程序：

在 Azure 门户中：

1. 在左侧菜单中，选择“订阅”。 
2. 选择要用于事件网格的订阅。
3. 在左侧菜单中的“设置”下，选择“资源提供程序”。  
4. 找到 **Microsoft.EventGrid**。
5. 如果尚未注册，请选择“注册”。  

完成注册可能需要一些时间。 选择“刷新”可更新状态。  当“状态”为“已注册”后，即可继续。  

### <a name="event-grid-viewer-deployment"></a>事件网格查看器部署

对于本快速入门，我们将使用 [Azure 事件网格查看器示例](https://docs.microsoft.com/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)，以便几乎实时地查看事件。 这会为用户提供实时源体验。 此外，每个事件的有效负载还应该可进行检查。  

## <a name="subscribe-to-the-sms-events-using-web-hooks"></a>使用 Webhook 订阅短信事件

在门户中，导航到创建的 Azure 通信服务资源。 在通信服务资源中，从“通信服务”页的左侧菜单中选择“事件” 。

:::image type="content" source="./media/handle-sms-events/select-events.png" alt-text="显示在资源事件页中选择事件订阅按钮的屏幕截图。":::

按“添加事件订阅”以进入创建向导。

在“创建事件订阅”页上，为事件订阅输入名称 。

可以订阅特定事件，以告知事件网格要跟踪哪些短信事件，以及要将事件发送到何处。 从下拉菜单中选择要订阅的事件。 对于短信，可以选择 `SMS Received` 和 `SMS Delivery Report Received`。 

查看 [Azure 通信服务支持的事件](../../concepts/event-handling.md)的完整列表。

:::image type="content" source="./media/handle-sms-events/select-events-create-eventsub.png" alt-text="显示选择了“短信已收到”和“短信传递报告已收到”事件类型的屏幕截图。":::

对于“终结点类型”，请选择“Web Hook”。  

:::image type="content" source="./media/handle-sms-events/select-events-create-linkwebhook.png" alt-text="显示设置为 Webhook 的“终结点类型”字段的屏幕截图。":::

对于“终结点”，请单击“选择终结点”，然后输入 Web 应用的 URL 。

在此例中，我们将使用来自我们之前在快速入门中设置的 [Azure 事件网格查看器示例](https://docs.microsoft.com/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)的 URL。 示例的 URL 将采用以下格式：`https://{{site-name}}.azurewebsites.net/api/updates`

然后选择“确认选择”。

:::image type="content" source="./media/handle-sms-events/select-events-create-selectwebhook-epadd.png" alt-text="显示确认 Webhook 终结点的屏幕截图。":::

## <a name="viewing-sms-events"></a>查看短信事件

### <a name="triggering-sms-events"></a>触发短信事件

若要查看事件触发器，必须首先生成事件。

- 当通信服务电话号码收到短信时，会生成 `SMS Received` 事件。 若要触发事件，只需将消息从你的电话发送到附加到通信服务资源的电话号码。
- 当使用通信服务电话号码向用户发送短信时，会生成 `SMS Delivery Report Received` 事件。 若要触发和事件，需要在[已发送短信](../telephony-sms/send.md)的选项中启用 `Delivery Report`。 尝试使用 `Delivery Report` 向你的电话发送消息。 完成此操作会从你的 Azure 帐户中扣取最多几美分的费用。

查看 [Azure 通信服务支持的事件](../../concepts/event-handling.md)的完整列表。

### <a name="receiving-sms-events"></a>接收短信事件

完成以上任一操作后，你会注意到 `SMS Received` 和 `SMS Delivery Report Received` 事件发送到你的终结点。 这些事件会显示在我们在开始时设置的 [Azure 事件网格查看器示例](https://docs.microsoft.com/samples/azure-samples/azure-event-grid-viewer/azure-event-grid-viewer/)中。 可以按事件旁的眼睛图标来查看整个有效负载。 事件将如下所示：

:::image type="content" source="./media/handle-sms-events/sms-received.png" alt-text="显示“短信已收到”事件的事件网格架构的屏幕截图。":::

:::image type="content" source="./media/handle-sms-events/SMS-Delivery-Report-Received.png" alt-text="显示“短信传递报告”事件的事件网格架构的屏幕截图。":::

详细了解[事件架构和其他事件处理概念](../../concepts/event-handling.md)

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除通信服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。 了解[清理资源](../create-communication-resource.md#clean-up-resources)的详细信息。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你学习了如何使用短信事件。 可以通过创建事件网格订阅来接收短信。

> [!div class="nextstepaction"] 
> [发送短信](../telephony-sms/send.md)

你可能还想要：

 - [了解事件处理概念](../../concepts/event-handling.md)
 - [了解事件网格](https://docs.microsoft.com/azure/event-grid/overview)
