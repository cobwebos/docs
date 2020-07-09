---
title: Azure 事件网格中的系统主题
description: 介绍 Azure 事件网格中的系统主题。
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 655ec5f0ad23b3902c1c99ba75eef2ef428911eb
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119916"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure 事件网格中的系统主题
事件网格中的 "系统" 主题表示由 azure 服务（如 Azure 存储和 Azure 事件中心）发布的一个或多个事件。 例如，系统主题可能表示**所有 blob 事件**，或仅为**特定存储帐户**发布的 blob 已**创建**和**blob 删除**事件。 在此示例中，当某个 blob 上传到存储帐户时，Azure 存储服务会将一个**blob 创建**事件发布到事件网格中的系统主题，然后将该事件转发给接收并处理该事件的主题[订户](event-handlers.md)。 

> [!NOTE] 
> 只有 Azure 服务可以将事件发布到系统主题。 因此，你不会获得一个终结点或访问密钥，你可以使用它来发布与自定义主题或域相同的事件。

## <a name="azure-services-that-support-system-topics"></a>支持系统主题的 Azure 服务
下面是支持在其上创建系统主题的 Azure 服务的当前列表。

- [Azure 应用配置](event-schema-app-configuration.md)
- [Azure 应用服务](event-schema-app-service.md)
- [Azure Blob 存储](event-schema-blob-storage.md)
- [Azure 容器注册表](event-schema-container-registry.md)
- [Azure 事件中心](event-schema-event-hubs.md)
- [Azure IoT 中心](event-schema-iot-hub.md)
- [Azure 密钥保管库](event-schema-key-vault.md)
- [Azure 机器学习](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure 媒体服务](event-schema-media-services.md)
- [Azure 资源组](event-schema-resource-groups.md)
- [Azure 服务总线](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure 订阅](event-schema-subscriptions.md)

## <a name="system-topics-as-azure-resources"></a>作为 Azure 资源的系统主题
在过去，系统主题是隐式的，出于简单起见，不会公开。 系统主题现在作为 Azure 资源显示，并提供以下功能：

- [查看 Azure 门户中的系统主题](create-view-manage-system-topics.md#view-all-system-topics)
- 导出 Azure 门户中的系统主题和事件订阅资源管理器模板
- [为系统主题设置诊断日志](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- 设置有关发布和传递失败的警报 

## <a name="lifecycle-of-system-topics"></a>系统主题的生命周期
可以通过两种方式创建系统主题： 

- [在 Azure 资源上创建作为扩展资源的事件订阅](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)，此操作会自动创建名称采用以下格式的系统主题： `<Azure resource name>-<GUID>` 。 删除主题的最后一个事件订阅后，将自动删除以这种方式创建的系统主题。 
- 为 Azure 资源创建系统主题，然后为该系统主题创建事件订阅。 使用此方法时，可以为系统主题指定一个名称。 删除最后一个事件订阅后，系统主题不会自动删除。 需要手动将其删除。 

    使用 Azure 门户时，始终使用此方法。 使用 Azure 资源的 " [**事件**" 页](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)创建事件订阅时，将首先创建系统主题，然后创建该主题的订阅。 您可以首先使用[**事件网格系统主题**页](create-view-manage-system-topics.md#create-a-system-topic)显式创建系统主题，然后创建该主题的订阅。 

使用[CLI](create-view-manage-system-topics-cli.md)、 [REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)或[Azure 资源管理器模板](create-view-manage-system-topics-arm.md)时，可以选择上述两种方法之一。 建议先创建一个系统主题，然后创建该主题的订阅，因为这是创建系统主题的最新方法。

如果设置了 Azure 策略，导致事件网格服务无法创建 Azure 策略，则系统主题创建将失败。 例如，你可能有一个策略，只允许在订阅中创建特定类型的资源（例如： Azure 存储、Azure 事件中心等）。 

## <a name="location-and-resource-group-for-a-system-topic"></a>系统主题的位置和资源组
对于位于特定区域/位置的 Azure 事件源，将在与 Azure 事件源相同的位置创建系统主题。 例如，如果在美国东部为 Azure blob 存储创建事件订阅，则系统主题将在美国东部创建。 对于 Azure 订阅、资源组或 Azure Maps 等全局 Azure 事件，事件网格在**全局**位置创建系统主题。 

通常，系统主题在 Azure 事件源所在的资源组中创建。 对于在 Azure 订阅范围内创建的事件订阅，系统主题在资源组“Default-EventGrid”  下创建。 如果该资源组不存在，则 Azure 事件网格会在创建系统主题之前创建该资源组。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [使用 Azure 门户创建、查看和管理系统主题](create-view-manage-system-topics.md)。
- [使用 Azure CLI 创建、查看和管理事件网格系统主题](create-view-manage-system-topics-cli.md)
- [使用 Azure 资源管理器模板创建事件网格系统主题](create-view-manage-system-topics-arm.md)
