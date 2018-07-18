---
title: Azure 事件网格事件源
description: 介绍 Azure 事件网格支持的事件源
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: tomfitz
ms.openlocfilehash: f9c3bcb6b92b43fe5b5bad72c99e6ce199c17448
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34302120"
---
# <a name="event-sources-in-azure-event-grid"></a>Azure 事件网格中的事件源

事件源即事件发生的位置。 会自动将多个 Azure 服务配置为发送事件。 也可创建用于发送事件的自定义应用程序。 不需将自定义应用程序托管在 Azure 中即可使用事件网格来分发事件。

本文提供每个事件源的内容的链接。

## <a name="azure-subscriptions"></a>Azure 订阅

订阅 Azure 订阅事件即可对整个 Azure 订阅中的资源更改进行响应。

|标题 |说明  |
|---------|---------|
| [将 Azure 自动化与事件网格和 Microsoft Teams 相集成](ensure-tags-exists-on-new-virtual-machines.md) |创建用于发送事件的虚拟机。 此事件触发一个用于标记虚拟机的自动化 Runbook，并触发一条发送到 Microsoft Teams 频道的消息。 |
| [事件架构](event-schema-subscriptions.md) | 显示 Azure 订阅事件中的字段。 |

## <a name="custom-topics"></a>自定义主题

订阅自定义主题即可响应应用程序事件。

|标题  |说明  |
|---------|---------|
| [使用 Azure CLI 创建和路由自定义事件](custom-event-quickstart.md) | 说明如何使用 Azure CLI 发送自定义事件。 |
| [使用 Azure PowerShell 创建和路由自定义事件](custom-event-quickstart-powershell.md) | 说明如何使用 Azure PowerShell 发送自定义事件。 |
| [使用 Azure 门户创建和路由自定义事件](custom-event-quickstart-portal.md) | 说明如何使用门户发送自定义事件。 |
| [发布到自定义主题](post-to-custom-topic.md) | 说明如何将事件发布到自定义主题。 |
| [将自定义事件路由到 Azure 队列存储](custom-event-to-queue-storage.md) | 说明如何将自定义事件发送到队列存储。 |
| [事件架构](event-schema.md) | 说明自定义事件中的字段。 |

## <a name="event-hubs"></a>事件中心

订阅事件中心事件即可响应捕获文件事件。

|标题  |说明  |
|---------|---------|
| [将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md) | 当事件中心创建捕获文件时，事件网格会将一个事件发送到函数应用。 应用会检索捕获文件并将数据迁移到数据仓库。 |
| [事件架构](event-schema-event-hubs.md) | 说明事件中心事件中的字段。 |

## <a name="iot-hub"></a>IoT 中心

订阅 IoT 中心事件即可响应创建的设备和删除的事件。

|标题  |说明  |
|---------|---------|
| [使用逻辑应用发送有关 Azure IoT 中心事件的电子邮件](publish-iot-hub-events-to-logic-apps.md) | 每次将设备添加到 IoT 中心时，逻辑应用就会发送一封通知电子邮件。 |
| [使用事件网格来触发操作，对 IoT 中心事件进行响应](../iot-hub/iot-hub-event-grid.md) | 概述 IoT 中心与事件网格的集成。 |
| [事件架构](event-schema-iot-hub.md) | 说明 IoT 中心事件中的字段。 |

## <a name="media-services"></a>媒体服务

订阅媒体服务事件即可响应作业状态事件。

|标题  |说明  |
|---------|---------|
| [响应媒体服务事件](../media-services/latest/reacting-to-media-services-events.md) | 概述媒体服务与事件网格的集成。 |
| [使用 CLI 将 Azure 媒体服务事件路由到自定义 Web 终结点](../media-services/latest/job-state-events-cli-how-to.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 说明如何从媒体服务发送事件。 |
| [事件架构](../media-services/latest/media-services-event-schemas.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 说明媒体服务事件中的字段。 |

## <a name="resource-groups"></a>资源组

订阅资源组事件即可对整个资源组中的资源更改进行响应。

|标题  |说明  |
|---------|---------|
| [通过 Azure 事件网格和逻辑应用监视虚拟机的更改](monitor-virtual-machine-changes-event-grid-logic-app.md) | 逻辑应用可监视对虚拟机的更改并就这些更改发送电子邮件。 |
| [事件架构](event-schema-resource-groups.md) | 说明资源组事件中的字段。 |

## <a name="service-bus"></a>服务总线

订阅服务总线事件即可在没有活动侦听器的情况下响应消息。

|标题  |说明  |
|---------|---------|
| [Azure 服务总线到 Azure 事件网格集成示例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件网格将消息从服务总线主题发送到函数应用和逻辑应用。 |
| [Azure 服务总线到事件网格的集成概述](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md) | 概述服务总线与事件网格的集成。 |
| [事件架构](event-schema-service-bus.md) | 说明服务总线事件中的字段。 |

## <a name="storage"></a>存储

订阅 Blob 存储事件即可响应创建的 Blob 和删除的事件。

|标题  |说明  |
|---------|---------|
| [使用 Azure CLI 将 Blob 存储事件路由到自定义 Web 终结点](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 说明如何使用 Azure CLI 发送 Blob 存储事件。 |
| [使用 PowerShell 将 Blob 存储事件路由到自定义 Web 终结点](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 说明如何使用 Azure PowerShell 发送 Blob 存储事件。 |
| [对 Blob 存储事件作出反应](../storage/blobs/storage-blob-event-overview.md) | 概述 Blob 存储与事件网格的集成。 |
| [事件架构](event-schema-blob-storage.md) | 说明 Blob 存储事件中的字段。 |

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
