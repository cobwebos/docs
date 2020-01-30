---
title: Azure 事件网格事件源
description: 本文介绍 Azure 事件网格支持的事件源。 事件源即事件发生的位置。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: spelluru
ms.openlocfilehash: 3a52d906d958fdfd1d7e09c6229b12af3204bc52
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846382"
---
# <a name="event-sources-in-azure-event-grid"></a>Azure 事件网格中的事件源

事件源即事件发生的位置。 会自动将多个 Azure 服务配置为发送事件。 也可创建用于发送事件的自定义应用程序。 不需要将自定义应用程序托管在 Azure 中即可使用事件网格来分发事件。

本文提供每个事件源的内容的链接。

## <a name="azure-subscriptions"></a>Azure 订阅

订阅 Azure 订阅事件即可对整个 Azure 订阅中的资源更改进行响应。

|标题 |Description  |
|---------|---------|
| [教程：Azure 自动化与事件网格和 Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |创建用于发送事件的虚拟机。 此活动触发一个用于标记虚拟机的自动化 Runbook，并触发一条发送到 Microsoft Teams 频道的消息。 |
| [如何：通过门户订阅事件](subscribe-through-portal.md) | 使用门户订阅 Azure 订阅的事件。 |
| [Azure CLI：订阅 Azure 订阅的事件](./scripts/event-grid-cli-azure-subscription.md) |用于在 Azure 订阅中创建事件网格订阅并将事件发送到 WebHook 的示例脚本。 |
| [PowerShell：订阅 Azure 订阅的事件](./scripts/event-grid-powershell-azure-subscription.md)| 用于在 Azure 订阅中创建事件网格订阅并将事件发送到 WebHook 的示例脚本。 |
| [事件架构](event-schema-subscriptions.md) | 显示 Azure 订阅事件中的字段。 |

## <a name="container-registry"></a>容器注册表

订阅容器注册表事件以响应映像中的更改。

|标题 |Description  |
|---------|---------|
| [快速入门：发送容器注册表事件](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 介绍如何使用 Azure CLI 发送容器注册表事件。 |
| [事件架构](event-schema-container-registry.md) | 显示容器注册表事件中的字段。 |

## <a name="custom-topics"></a>自定义主题

订阅自定义主题即可响应应用程序事件。

|标题  |Description  |
|---------|---------|
| [快速入门：使用 Azure CLI 创建和路由自定义事件](custom-event-quickstart.md) | 说明如何使用 Azure CLI 发送自定义事件。 |
| [快速入门：使用 Azure PowerShell 创建和路由自定义事件](custom-event-quickstart-powershell.md) | 说明如何使用 Azure PowerShell 发送自定义事件。 |
| [快速入门：使用 Azure 门户创建和路由自定义事件](custom-event-quickstart-portal.md) | 说明如何使用门户发送自定义事件。 |
| [快速入门：将自定义事件路由到 Azure 队列存储](custom-event-to-queue-storage.md) | 说明如何将自定义事件发送到队列存储。 |
| [如何：发布到自定义主题](post-to-custom-topic.md) | 说明如何将事件发布到自定义主题。 |
| [Azure CLI：创建事件网格自定义主题](./scripts/event-grid-cli-create-custom-topic.md)|用于创建自定义主题的示例脚本。 该脚本检索终结点和密钥。|
| [Azure CLI：订阅自定义主题的事件](./scripts/event-grid-cli-subscribe-custom-topic.md)|用于创建自定义主题的订阅的示例脚本。 它将事件发送到 WebHook。|
| [PowerShell：创建事件网格自定义主题](./scripts/event-grid-powershell-create-custom-topic.md)|用于创建自定义主题的示例脚本。 该脚本检索终结点和密钥。|
| [PowerShell：订阅自定义主题的事件](./scripts/event-grid-powershell-subscribe-custom-topic.md)|用于创建自定义主题的订阅的示例脚本。 它将事件发送到 WebHook。|
| [资源管理器模板：自定义主题和 WebHook 终结点](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) | 用于创建自定义主题及其订阅的资源管理器模板。 它将事件发送到 WebHook。 |
|
| [资源管理器模板：自定义主题和事件中心终结点](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| 用于创建自定义主题的订阅的资源管理器模板。 它将事件发送到 Azure 事件中心。 |
| [事件架构](event-schema.md) | 说明自定义事件中的字段。 |

## <a name="event-hubs"></a>事件中心

订阅事件中心事件即可响应捕获文件事件。 事件中心可以充当事件源或事件处理程序。 以下文章介绍了如何将事件中心用作源。

|标题  |Description  |
|---------|---------|
| [教程：将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md) | 当事件中心创建捕获文件时，事件网格会将一个事件发送到函数应用。 应用会检索捕获文件并将数据迁移到数据仓库。 |
| [事件架构](event-schema-event-hubs.md) | 说明事件中心事件中的字段。 |

有关事件中心用作源的示例，请参阅[事件中心处理程序](event-handlers.md#event-hubs)。

## <a name="iot-hub"></a>IoT 中心

订阅 IoT 中心事件以响应创建、删除、连接、断开连接和遥测事件的设备。

|标题  |Description  |
|---------|---------|
| [使用逻辑应用发送有关 Azure IoT 中心事件的电子邮件](publish-iot-hub-events-to-logic-apps.md) | 每次将设备添加到 IoT 中心时，逻辑应用就会发送一封通知电子邮件。 |
| [使用事件网格来触发操作，对 IoT 中心事件进行响应](../iot-hub/iot-hub-event-grid.md) | 概述 IoT 中心与事件网格的集成。 |
| [事件架构](event-schema-iot-hub.md) | 说明 IoT 中心事件中的字段。 |
| [订阅设备已连接和设备已断开连接事件](../iot-hub/iot-hub-how-to-order-connection-state-events.md) | 显示如何订阅设备连接状态事件。 |

## <a name="key-vault-preview"></a>Key Vault （预览）

与事件网格的 Key Vault 集成目前处于预览阶段。 

订阅要在机密即将过期、机密过期或机密具有可用的新版本时通知的 Key Vault 事件。 

|标题  |Description  |
|---------|---------|
| [通过 Azure 事件网格监视 Key Vault 事件](../key-vault/event-grid-overview.md) | 将 Key Vault 与事件网格集成的概述。 |
| [教程：用事件网格创建和监视 Key Vault 事件](../key-vault/event-grid-tutorial.md) | 了解如何为 Key Vault 设置事件网格通知。 |
| [事件架构](event-schema-key-vault.md) | 显示 Key Vault 事件中的字段。 |

## <a name="media-services"></a>媒体服务

订阅媒体服务事件即可响应作业状态事件。

|标题  |Description  |
|---------|---------|
| [概述：响应媒体服务事件](../media-services/latest/reacting-to-media-services-events.md) | 概述媒体服务与事件网格的集成。 |
| [教程：使用 CLI 将 Azure 媒体服务事件路由到自定义 Web 终结点](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 说明如何从媒体服务发送事件。 |
| [事件架构](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 说明媒体服务事件中的字段。 |

## <a name="resource-groups"></a>资源组

订阅资源组事件即可对整个资源组中的资源更改进行响应。

|标题  |Description  |
|---------|---------|
| [教程：通过 Azure 事件网格和逻辑应用监视虚拟机更改](monitor-virtual-machine-changes-event-grid-logic-app.md) | 逻辑应用可监视对虚拟机的更改并就这些更改发送电子邮件。 |
| [Azure CLI：订阅资源组的事件](./scripts/event-grid-cli-resource-group.md)| 用于订阅资源组的事件的示例脚本。 它将事件发送到 WebHook。 |
| [Azure CLI：订阅资源组的事件并筛选资源](./scripts/event-grid-cli-resource-group-filter.md) | 用于订阅资源组的事件并筛选一个资源的事件的示例脚本。 |
| [PowerShell：订阅资源组的事件](./scripts/event-grid-powershell-resource-group.md) | 用于订阅资源组的事件的示例脚本。 它将事件发送到 WebHook。 |
| [PowerShell：订阅资源组的事件并筛选资源](./scripts/event-grid-powershell-resource-group-filter.md) | 用于订阅资源组的事件并筛选一个资源的事件的示例脚本。 |
| [资源管理器模板：资源订阅](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) | 订阅 Azure 订阅的事件或资源组。 它将事件发送到 WebHook。 |
| [事件架构](event-schema-resource-groups.md) | 说明资源组事件中的字段。 |

## <a name="service-bus"></a>服务总线

订阅服务总线事件即可在没有活动侦听器的情况下响应消息。

|标题  |Description  |
|---------|---------|
| [教程：Azure 服务总线与 Azure 事件网格的集成示例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件网格将消息从服务总线主题发送到函数应用和逻辑应用。 |
| [概述：Azure 服务总线与事件网格的集成](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | 概述服务总线与事件网格的集成。 |
| [事件架构](event-schema-service-bus.md) | 说明服务总线事件中的字段。 |

## <a name="storage"></a>存储空间

订阅 Blob 存储事件即可响应创建的 Blob 和删除的事件。

>[!NOTE]
> 只有类型**StorageV2 （常规用途 v2）** 和**BlobStorage**的存储帐户支持事件集成。 **Storage （genral）** *不支持与*事件网格集成。

|标题  |Description  |
|---------|---------|
| [快速入门：使用 Azure CLI 将 Blob 存储事件路由到自定义 Web 终结点](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 介绍如何使用 Azure CLI 将 Blob 存储事件发送到 WebHook。 |
| [快速入门：使用 PowerShell 将 Blob 存储事件路由到自定义 Web 终结点](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 介绍如何使用 Azure PowerShell 将 Blob 存储事件发送到 WebHook。 |
| [快速入门：使用 Azure 门户创建和路由 Blob 存储事件](blob-event-quickstart-portal.md) | 介绍如何使用门户将 Blob 存储事件发送到 WebHook。 |
| [Azure CLI：订阅 Blob 存储帐户的事件](./scripts/event-grid-cli-blob.md) | 用于订阅 Blob 存储帐户的事件的示例脚本。 它将事件发送到 WebHook。 |
| [PowerShell：订阅 Blob 存储帐户的事件](./scripts/event-grid-powershell-blob.md) | 用于订阅 Blob 存储帐户的事件的示例脚本。 它将事件发送到 WebHook。 |
| [资源管理器模板：创建 Blob 存储和订阅](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) | 部署 Azure Blob 存储帐户并订阅该存储帐户的事件。 它将事件发送到 WebHook。 |
| [概述：响应 Blob 存储事件](../storage/blobs/storage-blob-event-overview.md) | 概述 Blob 存储与事件网格的集成。 |
| [事件架构](event-schema-blob-storage.md) | 说明 Blob 存储事件中的字段。 |

## <a name="maps"></a>地图
订阅 Azure Maps 事件以响应地理围栏事件。 例如，当设备每次进入或退出地理围栏时，应用程序都可以提供电子邮件通知。

|标题  |Description  |
|---------|---------|
| [使用事件网格对 Azure Maps 事件做出响应](../azure-maps/azure-maps-event-grid-integration.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 概述了 Azure Maps 与事件网格的集成。 |
| [教程：设置地域隔离区内](../azure-maps/tutorial-geofence.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 本教程将引导你完成使用 Azure Maps 设置地理围栏的基本步骤。 你将使用 Azure 事件网格来流式传输地理围栏结果，并根据地理围栏结果设置通知。 |
| [事件架构](event-schema-azure-maps.md) | 显示了 Azure Maps 事件中的字段。 |

## <a name="app-configuration"></a>应用程序配置
订阅 Azure 应用配置事件以响应键值修改事件。

|标题 | Description |
|---------|---------|
| [使用事件网格响应 Azure 应用配置事件](../azure-app-configuration/concept-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 将 Azure 应用配置与事件网格集成的概述。 |
| [快速入门：使用 Azure CLI 将 Azure 应用配置事件路由到自定义 web 终结点](../azure-app-configuration/howto-app-configuration-event.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 演示如何使用 Azure CLI 向 WebHook 发送 Azure 应用配置事件。 |
| [事件架构](event-schema-app-configuration.md) | 显示 Azure 应用配置事件中的字段。 |

## <a name="azure-signalr"></a>Azure SignalR
订阅 Azure SignalR 服务事件，以响应客户端连接事件。

|标题 | Description |
|---------|---------|
| [使用事件网格响应 Azure SignalR 服务事件](../azure-signalr/signalr-concept-event-grid-integration.md) | 将 Azure SignalR 服务与事件网格集成概述。 |
| [如何将 Azure SignalR 服务事件发送到事件网格](../azure-signalr/signalr-howto-event-grid-integration.md) | 演示如何通过事件网格将 Azure SignalR 服务事件发送到应用程序。 |
| [事件架构](event-schema-azure-signalr.md) | 显示 Azure SignalR 服务事件中的字段。 |

## <a name="azure-machine-learning"></a>Azure 机器学习

订阅 Azure 机器学习工作区事件以响应模型注册。

| 标题 | Description |
| ----- | ----- |
| [使用 Azure 机器学习事件](../machine-learning/concept-event-grid-integration.md) | 将 Azure 机器学习与事件网格集成的概述。 |
| [Azure 机器学习的 Azure 事件网格事件架构](event-schema-machine-learning.md) | 显示 Azure 机器学习事件中的字段。 |

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
