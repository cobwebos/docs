---
title: 什么是 Azure 事件网格？
description: 使用 Azure 事件网格将事件数据从源发送到处理程序。 生成基于事件的应用程序，并与 Azure 服务集成。
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: overview
ms.date: 02/20/2020
ms.author: babanisa
ms.custom: seodec18
ms.openlocfilehash: 75ee828c659aba895f65d0cf14070966428a1d19
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682510"
---
# <a name="what-is-azure-event-grid"></a>什么是 Azure 事件网格？

通过 Azure 事件网格，可使用基于事件的体系结构轻松生成应用程序。 首先，选择要订阅的 Azure 资源，然后提供要向其发送事件的事件处理程序或 WebHook 终结点。 事件网格包含来自 Azure 服务对事件的内置支持，如存储 blob 和资源组。 事件网格还使用自定义主题支持自己的事件。 

可以使用筛选器将特定事件路由到不同的终结点，多播到多个终结点，并确保事件可靠传送。

部署 Azure 事件网格是为了通过本机分布在每个区域中的多个容错域和可用性区域（在支持它们的区域中）来最大限度地提高可用性。 有关事件网格支持的区域列表，请参阅[可用产品(按区域)](https://azure.microsoft.com/global-infrastructure/services/?products=event-grid&regions=all)。

本文将对 Azure 事件网格进行简要概述。 若要开始使用事件网格，请参阅[使用 Azure 事件网格创建和路由自定义事件](custom-event-quickstart.md)。 

![源和处理程序的事件网格模型](./media/overview/functional-model.png)

此图展示事件网格连接源和处理程序的方式，而不是支持的集成的完整列表。

## <a name="event-sources"></a>事件源

当前，以下 Azure 服务支持将事件发送到事件网格：

- [Azure 应用配置](event-schema-app-configuration.md)
- [Azure Blob 存储](event-schema-blob-storage.md)
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

## <a name="event-handlers"></a>事件处理程序

有关每个处理程序的功能的完整详细信息及相关文章，请参阅[事件处理程序](event-handlers.md)。 当前，以下 Azure 服务支持从事件网格处理事件： 

* [Azure 自动化](handler-webhooks.md#azure-automation)
* [Azure Functions](handler-functions.md)
* [事件中心](handler-event-hubs.md)
* [中继混合连接](handler-relay-hybrid-connections.md)
* [逻辑应用](handler-webhooks.md#logic-apps)
* [Power Automate（之前称为 Microsoft Flow）](https://preview.flow.microsoft.com/connectors/shared_azureeventgrid/azure-event-grid/)
* [服务总线](handler-service-bus.md)
* [队列存储](handler-storage-queues.md)
* [WebHook](handler-webhooks.md)

## <a name="concepts"></a>概念

在可以开始进行操作的 Azure 事件网格中有五个概念：

* 事件 - 发生了什么。
* **事件源** - 事件发生的地点。
* 主题 - 其中发布者发送事件的终结点。
* **事件订阅** - 用于路由事件，有时用于多个处理程序的终结点或内置机制。 订阅还用于处理程序，以便智能地筛选传入事件。
* 事件处理程序 - 对事件作出反应的应用或服务。

有关这些概念的详细信息，请参阅 [Azure 事件网格中的概念](concepts.md)。

## <a name="capabilities"></a>功能

下面是 Azure 事件网格中的一些主要功能：

* 简洁性 - 指向并单击从 Azure 资源到任何事件处理程序或终结点的目标事件。
* **高级筛选** - 筛选事件类型或事件发布路径，以确保事件处理程序仅接收相关的事件。
* **扇出** - 订阅到相同事件的多个终结点，以将该事件的副本发送到所需的所有位置。
* **可靠性** - 使用指数退避算法在 24 小时内重试，以确保事件成功传送。
* 按事件支付 - 仅支付事件网格的使用量。
* 高吞吐量 - 通过对每秒数以百万计事件的支持，在事件网格上生成大量工作负荷。
* 内置事件 - 使用资源定义的内置事件快速启动和运行。
* 自定义事件 - 在应用中使用事件网格路由、筛选并可靠地传送自定义事件。

有关事件网格、事件中心和服务总线之间的比较，请参阅[在传送消息的 Azure 服务之间进行选择](compare-messaging-services.md)。

## <a name="what-can-i-do-with-event-grid"></a>使用事件网格可以做什么？

Azure 事件网格提供多项功能，极大程度地改进了无服务器体系结构、运维自动化和[集成](https://azure.com/integration)工作： 

### <a name="serverless-application-architectures"></a>无服务器应用程序体系结构

![无服务器应用程序体系结构](./media/overview/serverless_web_app.png)

事件网格将数据源与事件处理程序连接。 例如，使用事件网格触发无服务器功能，该功能在添加到 blob 存储容器时分析图像。 

### <a name="ops-automation"></a>运维自动化

![运维自动化](./media/overview/Ops_automation.png)

使用事件网格，可以加快自动化，简化策略执行。 例如，使用事件网格在创建虚拟机或 SQL 数据库时通知 Azure 自动化。 使用事件自动检查服务配置是否符合要求、将元数据放入操作工具、标记虚拟机或提交工作项。

### <a name="application-integration"></a>应用程序集成

![将应用程序与 Azure 集成](./media/overview/app_integration.png)

事件网格将应用与其他服务连接。 例如，创建一个自定义主题以将应用的事件数据发送到事件网格，并利用其可靠交付、高级路由和与 Azure 的直接集成。 或者，可将事件网格与逻辑应用搭配使用，处理任何位置的数据，而无需编写代码。 

## <a name="how-much-does-event-grid-cost"></a>事件网格的费用是多少？

Azure 事件网格使用按事件支付的定价模型，因此，你只需为你所使用的事件付费。 每月前 100,000 个操作是免费的。 操作定义为事件引入、订阅交付尝试、管理调用和按使用者后缀筛选。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/event-grid/)。

## <a name="next-steps"></a>后续步骤

* [路由存储 Blob 事件](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json)  
  使用事件网格响应存储 blob 事件。
* [创建并订阅自定义事件](custom-event-quickstart.md)  
  立即开始使用 Azure 事件网格快速入门，将自己的自定义事件发送到任何终结点。
* [将逻辑应用用作事件处理程序](monitor-virtual-machine-changes-event-grid-logic-app.md)  
  本教程介绍如何使用逻辑应用生成应用，并对事件网格推送的事件作出响应。
* [将大数据流式传输到数据仓库](event-grid-event-hubs-integration.md)  
  本教程介绍如何使用 Azure Functions 将数据从事件中心流式传输到 SQL 数据仓库。
* [事件网格 REST API 参考](/rest/api/eventgrid)  
  提供用于管理事件订阅、路由和筛选的参考内容。