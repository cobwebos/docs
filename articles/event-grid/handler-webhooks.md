---
title: Webhook 作为 Azure 事件网格事件的事件处理程序
description: 介绍如何将 Webhook 用作 Azure 事件网格事件的事件处理程序。 支持通过 Webhook 将 Azure 自动化 runbook 和逻辑应用用作事件处理程序。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: ba67b1cd93bc1c713648f799090e0b2cd77cff1b
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596376"
---
# <a name="webhooks-automation-runbooks-logic-apps-as-event-handlers-for-azure-event-grid-events"></a>Webhook、自动化 runbook、逻辑应用作为 Azure 事件网格事件的事件处理程序
事件处理程序是发送事件的位置。 处理程序将执行一些进一步的操作来处理事件。 会自动将多个 Azure 服务配置为处理事件。 也可以使用任意 Webhook 来处理事件。 Webhook 不需要托管在 Azure 中便可处理事件。 事件网格仅支持 HTTPS Webhook 终结点。

> [!NOTE]
> 支持通过 Webhook 将 Azure 自动化 runbook 和逻辑应用用作事件处理程序。 

## <a name="webhooks"></a>Webhook
有关使用 Webhook 作为事件处理程序的概述和示例，请参阅以下文章。 

|标题  |说明  |
|---------|---------|
| 快速入门：使用 [Azure CLI](custom-event-quickstart.md)、[PowerShell](custom-event-quickstart-powershell.md) 和[门户](custom-event-quickstart-portal.md)创建和路由自定义事件。 | 展示了如何将自定义事件发送到 WebHook。 |
| 快速入门：使用 [Azure CLI](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)、[PowerShell](../storage/blobs/storage-blob-event-quickstart-powershell.md?toc=%2fazure%2fevent-grid%2ftoc.json) 和[门户](blob-event-quickstart-portal.md)将 Blob 存储事件路由到自定义 Web 终结点。 | 展示了如何将 Blob 存储事件发送到 WebHook。 |
| [快速入门：发送容器注册表事件](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 介绍如何使用 Azure CLI 发送容器注册表事件。 |
| [概述：将事件接收到 HTTP 终结点](receive-events.md) | 介绍如何验证 HTTP 终结点以接收来自事件订阅的事件，然后接收和反序列化事件。 |


## <a name="azure-automation"></a>Azure 自动化
可以使用 Azure 自动化 runbook 处理事件。 支持通过 Webhook 使用自动化 runbook 来处理事件。 为 runbook 创建 Webhook，然后使用 Webhook 处理程序。 有关示例，请参阅以下教程： 

|标题  |说明  |
|---------|---------|
|[教程：Azure 自动化与事件网格和 Microsoft Teams](ensure-tags-exists-on-new-virtual-machines.md) |创建用于发送事件的虚拟机。 此活动触发一个用于标记虚拟机的自动化 Runbook，并触发一条发送到 Microsoft Teams 频道的消息。 |


## <a name="logic-apps"></a>逻辑应用
使用逻辑应用实现处理事件网格事件所需的业务流程。 在这种情况下，不显式创建 Webhook。 当你将逻辑应用配置为处理事件网格中的事件时，系统会自动为你创建 Webhook。 有关示例，请参阅以下教程： 

|标题  |说明  |
|---------|---------|
| [教程：通过 Azure 事件网格和逻辑应用监视虚拟机更改](monitor-virtual-machine-changes-event-grid-logic-app.md) | 逻辑应用可监视对虚拟机的更改并就这些更改发送电子邮件。 |
| [教程：使用逻辑应用发送有关 Azure IoT 中心事件的电子邮件通知](publish-iot-hub-events-to-logic-apps.md) | 每次将设备添加到 IoT 中心时，逻辑应用就会发送一封通知电子邮件。 |
| [教程：使用 Azure Functions 和 Azure 逻辑应用响应通过 Azure 事件网格收到的 Azure 服务总线事件](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | 事件网格将消息从服务总线主题发送到函数应用和逻辑应用。 |

## <a name="next-steps"></a>后续步骤
如需支持的事件处理程序的列表，请参阅[事件处理程序](event-handlers.md)一文。 
