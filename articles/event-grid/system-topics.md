---
title: Azure 事件网格中的系统主题
description: 介绍 Azure 事件网格中的系统主题。
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: b3a6e7528da2a11c2f91007425ab8beecaf920c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91297277"
---
# <a name="system-topics-in-azure-event-grid"></a>Azure 事件网格中的系统主题
事件网格中的系统主题表示由 Azure 服务（如 Azure 存储和 Azure 事件中心）发布的一个或多个事件。 例如，一个系统主题可以表示已为特定存储帐户发布的所有 Blob 事件或仅“Blob 已创建”和“Blob 已删除”事件  。 在此示例中，当 Blob 上传到存储帐户时，Azure 存储服务会将“Blob 已创建”事件发布到事件网格中的系统主题，然后，事件网格会将该事件转发给接收和处理该事件的主题[订阅服务器](event-handlers.md)。 

> [!NOTE] 
> 只有 Azure 服务才可以将事件发布到系统主题。 因此，你不会获得一个可用来像你为自定义主题或域一样发布事件的终结点或访问密钥。

## <a name="azure-services-that-support-system-topics"></a>支持系统主题的 Azure 服务
下面是支持创建系统主题的 Azure 服务的当前列表。

- [Azure 应用配置](event-schema-app-configuration.md)
- [Azure 应用服务](event-schema-app-service.md)
- [Azure Blob 存储](event-schema-blob-storage.md)
- [Azure 通信服务](event-schema-communication-services.md) 
- [Azure 容器注册表](event-schema-container-registry.md)
- [Azure 事件中心](event-schema-event-hubs.md)
- [Azure IoT 中心](event-schema-iot-hub.md)
- [Azure Key Vault](event-schema-key-vault.md)
- [Azure 机器学习](event-schema-machine-learning.md)
- [Azure Maps](event-schema-azure-maps.md)
- [Azure 媒体服务](event-schema-media-services.md)
- [Azure 资源组](event-schema-resource-groups.md)
- [Azure 服务总线](event-schema-service-bus.md)
- [Azure SignalR](event-schema-azure-signalr.md)
- [Azure 订阅](event-schema-subscriptions.md)

## <a name="system-topics-as-azure-resources"></a>作为 Azure 资源的系统主题
在过去，系统主题是隐式的，并且为了简单起见而未公开。 系统主题现在作为 Azure 资源可见，并提供以下功能：

- [在 Azure 门户中查看系统主题](create-view-manage-system-topics.md#view-all-system-topics)
- 在 Azure 门户中导出系统主题和事件订阅的资源管理器模板
- [为系统主题设置诊断日志](enable-diagnostic-logs-topic.md#enable-diagnostic-logs-for-a-system-topic)
- 设置有关发布和交付失败的警报 

## <a name="lifecycle-of-system-topics"></a>系统主题的生命周期
可以通过两种方式创建系统主题： 

- 创建 [Azure 资源的事件订阅作为扩展资源](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate)，这会自动创建名称采用以下格式的系统主题：`<Azure resource name>-<GUID>`。 删除该主题的最后一个事件订阅时，以这种方式创建的系统主题将会自动删除。 
- 为 Azure 资源创建系统主题，然后为该系统主题创建事件订阅。 使用此方法时，可以为系统主题指定一个名称。 删除最后一个事件订阅时，系统主题不会自动删除。 需要手动将其删除。 

    使用 Azure 门户时，始终要使用此方法。 使用 Azure 资源的 " [**事件** " 页](blob-event-quickstart-portal.md#subscribe-to-the-blob-storage)创建事件订阅时，将首先创建系统主题，然后创建该主题的订阅。 可以先使用[“事件网格系统主题”页](create-view-manage-system-topics.md#create-a-system-topic)显式创建系统主题，然后创建该主题的订阅。 

使用 [CLI](create-view-manage-system-topics-cli.md)、[REST](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate) 或 [Azure 资源管理器模板](create-view-manage-system-topics-arm.md)时，可以选择上述两种方法之一。 建议先创建一个系统主题，然后创建该主题的订阅，因为这是创建系统主题的最新方法。

如果设置 Azure 策略的方式导致事件网格服务无法创建系统主题，则系统主题创建将失败。 例如，你的某个策略可能仅允许在订阅中创建特定类型的资源（例如：Azure 存储、Azure 事件中心等）。 

## <a name="location-and-resource-group-for-a-system-topic"></a>系统主题的位置和资源组
对于位于特定区域/位置的 Azure 事件源，将在与 Azure 事件源相同的位置创建系统主题。 例如，如果在美国东部为 Azure blob 存储创建事件订阅，则系统主题将在美国东部创建。 对于 Azure 订阅、资源组或 Azure Maps 等全局 Azure 事件源，事件网格会在全局位置创建系统主题。 

通常，系统主题在 Azure 事件源所在的资源组中创建。 对于在 Azure 订阅范围内创建的事件订阅，系统主题是在**美国西部 2**区域中的**EventGrid**资源组中创建的。 如果该资源组不存在，则 Azure 事件网格会在创建系统主题之前创建该资源组。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章： 

- [使用 Azure 门户创建、查看和管理系统主题](create-view-manage-system-topics.md)。
- [使用 Azure CLI 创建、查看和管理事件网格系统主题](create-view-manage-system-topics-cli.md)
- [使用 Azure 资源管理器模板创建事件网格系统主题](create-view-manage-system-topics-arm.md)
