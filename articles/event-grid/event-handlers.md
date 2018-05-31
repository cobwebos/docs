---
title: Azure 事件网格事件处理程序
description: 介绍 Azure 事件网格支持的事件处理程序
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: 996bd4b3497861a3bfcbfecebe18a6936f487028
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/18/2018
ms.locfileid: "34301761"
---
# <a name="event-handlers-in-azure-event-grid"></a>Azure 事件网格中的事件处理程序

事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 会自动将多个 Azure 服务配置为处理事件。 也可以使用任意 Webhook 来处理事件。 不需将 Webhook 托管在 Azure 中来处理事件。

本文提供每个事件处理程序的内容的链接。

## <a name="azure-automation"></a>Azure 自动化

使用 Azure 自动化通过自动化的 Runbook 来处理事件。

|标题  |说明  |
|---------|---------|
|[将 Azure 自动化与事件网格和 Microsoft Teams 相集成](ensure-tags-exists-on-new-virtual-machines.md) |创建用于发送事件的虚拟机。 此事件触发一个用于标记虚拟机的自动化 Runbook，并触发一条发送到 Microsoft Teams 频道的消息。 |

## <a name="azure-functions"></a>Azure Functions

使用 Azure Functions 对事件进行无服务器响应。

|标题  |说明  |
|---------|---------|
| [Azure Functions 的事件网格触发器](../azure-functions/functions-bindings-event-grid.md) | 在 Functions 中使用事件网格触发器概述。 |
| [使用事件网格自动调整已上传图像的大小](resize-images-on-storage-blob-upload-event.md) | 用户通过 Web 应用将映像上传到存储帐户。 创建存储 Blob 后，事件网格会向用于重设已上传映像的大小的函数应用发送一个事件。 |
| [将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md) | 当事件中心创建捕获文件时，事件网格会将一个事件发送到函数应用。 应用会检索捕获文件并将数据迁移到数据仓库。 |
| [Azure 服务总线到 Azure 事件网格集成示例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件网格将消息从服务总线主题发送到函数应用和逻辑应用。 |

## <a name="hybrid-connections"></a>混合连接

使用 Azure 中继混合连接将事件发送到企业网络内没有可公开访问的终结点的应用程序。

|标题  |说明  |
|---------|---------|
| [发送事件混合连接](custom-event-to-hybrid-connection.md) | 将自定义事件发送到现有混合连接以供侦听器应用程序处理。 |

## <a name="logic-apps"></a>逻辑应用

使用逻辑应用自动执行事件响应业务流程。

|标题  |说明  |
|---------|---------|
| [通过 Azure 事件网格和逻辑应用监视虚拟机的更改](monitor-virtual-machine-changes-event-grid-logic-app.md) | 逻辑应用可监视对虚拟机的更改并就这些更改发送电子邮件。 |
| [使用逻辑应用发送有关 Azure IoT 中心事件的电子邮件](publish-iot-hub-events-to-logic-apps.md) | 每次将设备添加到 IoT 中心时，逻辑应用就会发送一封通知电子邮件。 |
| [Azure 服务总线到 Azure 事件网格集成示例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件网格将消息从服务总线主题发送到函数应用和逻辑应用。 |

## <a name="queue-storage"></a>队列存储

使用队列存储接收需拉取的事件。

|标题  |说明  |
|---------|---------|
| [使用 Azure CLI 和事件网格将自定义事件路由到 Azure 队列存储](custom-event-to-queue-storage.md) | 说明如何将自定义事件发送到队列存储。 |

## <a name="webhooks"></a>Webhook

将 Webhook 用于可响应事件的可自定义终结点。

|标题  |说明  |
|---------|---------|
| [将事件接收到 HTTP 终结点](receive-events.md) | 介绍如何验证 HTTP 终结点以接收来自事件订阅的事件，然后接收和反序列化事件。 |

## <a name="next-steps"></a>后续步骤

* 有关事件网格的介绍，请参阅[关于事件网格](overview.md)。
* 若要快速开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。
