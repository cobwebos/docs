---
title: Azure 通信服务中的通知
titleSuffix: An Azure Communication Services concept document
description: 向在 Azure 通信服务上构建的应用的用户发送通知。
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: abc2367c309f46ee1b29a51145c67e8d71919774
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91665389"
---
# <a name="communication-services-notifications"></a>通信服务通知

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

Azure 通信服务聊天和呼叫客户端库会创建一个实时消息通道，允许以高效且可靠的方式将信号消息推送到连接的客户端。 这使你可以在应用程序中构建丰富的实时通信功能，而无需实现复杂的 HTTP 轮询逻辑。 但是，在移动应用程序中，此信号通道仅当应用程序在前台处于活动状态时才保持连接状态。 如果希望用户在应用程序处于后台时接收传入呼叫或聊天消息，则应使用推送通知。

推送通知使你可以将信息从你的应用程序发送到用户的移动设备。 可以使用推送通知显示对话、播放声音或显示传入呼叫 UI。 Azure 通信服务提供与 [Azure 事件网格](https://docs.microsoft.com/azure/event-grid/overview)和 [Azure 通知中心](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-overview)的集成，使你可以向应用添加推送通知。

## <a name="trigger-push-notifications-via-azure-event-grid"></a>通过 Azure 事件网格触发推送通知

Azure 通信服务与 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)集成，从而以可靠、可缩放且安全的方式提供实时事件通知。 你可以利用此集成来创建将移动推送通知传递给用户的通知服务，具体方法是创建触发 [Azure 函数](https://docs.microsoft.com/azure/azure-functions/functions-overview)或 Webhook 的事件网格订阅。

:::image type="content" source="./media/notifications/acs-events-int.png" alt-text="显示通信服务如何与事件网格集成的关系图。":::

详细了解 [Azure 通信服务中的事件处理](./event-handling.md)。

## <a name="deliver-push-notifications-via-azure-notification-hubs"></a>通过 Azure 通知中心传递推送通知

可以将 Azure 通知中心连接到通信服务资源，以便在收到传入呼叫时自动向用户的移动设备发送推送通知。 你应使用这些推送通知从后台唤醒你的应用程序，并显示让用户接受或拒绝呼叫的 UI。 

:::image type="content" source="./media/notifications/acs-anh-int.png" alt-text="显示通信服务如何与事件网格集成的关系图。":::

通信服务使用 Azure 通知中心作为直通服务，以便使用[直接发送](https://docs.microsoft.com/rest/api/notificationhubs/direct-send) API 与各种特定于平台的推送通知服务进行通信。 这使你可以重复使用现有 Azure 通知中心资源和配置，向你的应用程序传递低延迟、可靠的呼叫通知。

### <a name="notification-hub-provisioning"></a>通知中心预配 

若要使用通知中心将推送通知传递到客户端设备，请在与通信服务资源相同的订阅中[创建通知中心](https://docs.microsoft.com/azure/notification-hubs/create-notification-hub-portal)。 必须为想要使用的平台通知服务配置 Azure 通知中心。 若要了解如何从通知中心获取客户端应用程序中的推送通知，请参阅[通知中心入门](https://docs.microsoft.com/azure/notification-hubs/ios-sdk-get-started)，并从页面顶部附近的下拉列表中选择目标客户端平台。

通知中心配置完成后，便可以使用 Azure 资源管理器客户端或通过 Azure 门户为中心提供连接字符串，从而将它关联到通信服务资源。 连接字符串应包含“发送”权限。 建议专门为中心创建另一个仅具有“发送”权限的访问策略。 详细了解[通知中心安全和访问策略](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-security)

> 注意：若要启用 Apple Push Notification 服务 VOIP 通知，必须使用 `.voip` 后缀将通知中心的名称设置为应用程序捆绑 ID。 请参阅[通过通知中心使用 APNS VOIP](https://docs.microsoft.com/azure/notification-hubs/voip-apns)。

#### <a name="using-the-azure-resource-manager-client-to-configure-the-notification-hub"></a>使用 Azure 资源管理器客户端配置通知中心

若要登录 Azure 资源管理器，请执行以下操作并使用你的凭据登录。

```console
armclient login
```

 成功登录后，请执行以下操作来预配通知中心：

```console
armclient POST /subscriptions/<sub_id>/resourceGroups/<resource_group>/providers/Microsoft.Communication/CommunicationServices/<resource_id>/linkNotificationHub?api-version=2020-08-20-preview "{'connectionString': '<connection_string>','resourceId': '<resource_id>'}"
```

#### <a name="using-the-azure-portal-to-configure-the-notification-hub"></a>使用 Azure 门户配置通知中心

在门户中，导航到 Azure 通信服务资源。 在通信服务资源中，从“通信服务”页的左侧菜单中选择“推送通知”，并连接之前预配的通知中心。 你需要在此处提供连接字符串和资源 ID：

:::image type="content" source="./media/notifications/acs-anh-portal-int.png" alt-text="显示通信服务如何与事件网格集成的关系图。":::

#### <a name="device-registration"></a>设备注册 

请参阅[语音呼叫快速入门](../quickstarts/voice-video-calling/getting-started-with-calling.md)，以了解如何向通信服务注册设备句柄。

## <a name="next-steps"></a>后续步骤

* 有关 Azure 事件网格的简介，请参阅[什么是事件网格？](https://docs.microsoft.com/azure/event-grid/overview)
* 若要详细了解 Azure 通知中心概念，请参阅 [Azure 通知中心文档](https://docs.microsoft.com/azure/notification-hubs/)
