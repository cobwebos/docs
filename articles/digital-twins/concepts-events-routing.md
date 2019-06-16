---
title: 使用 Azure 数字孪生路由事件和消息 | Microsoft Docs
description: 使用 Azure 数字孪生将事件和消息路由到服务终结点的概述
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 7dfda00aca403f7f95f0c56d1db28c3c609bebd2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67080664"
---
# <a name="routing-events-and-messages"></a>路由事件和消息

IoT 解决方案通常将多种强大的服务（包括存储和分析等）结合在一起。 本文介绍如何将 Azure 数字孪生应用连接到 Azure分析、AI 和存储服务，以便为它们提供更深入的见解和功能。

## <a name="route-types"></a>路由类型  

Azure 的数字孪生提供两种方法来将 IoT 事件连接与其他 Azure 服务或业务应用程序：

* **路由 Azure 数字孪生事件**：当空间图中的对象发生更改、收到遥测数据或用户定义的函数根据预定义的条件创建通知时，可以触发 Azure 数字孪生事件。 用户可以将这些事件发送到 [Azure 事件中心](https://azure.microsoft.com/services/event-hubs/)、[Azure 服务总线主题](https://azure.microsoft.com/services/service-bus/)或 [Azure 事件网格](https://azure.microsoft.com/services/event-grid/)以进一步进行处理。

* **路由设备遥测**：除了路由事件，Azure 数字孪生还可以将原始设备遥测消息路由到事件中心，以获得进一步的见解和分析。 Azure 数字孪生不处理这些类型的消息。 这些消息只会转发到事件中心。

用户可以指定一个或多个出口终结点来发送事件或转发消息。 事件和消息将根据这些预定义的路由首选项发送到终结点。 换句话说，用户可以指定特定的终结点来接收图形操作事件，指定另一个终结点来接收设备遥测事件等。

![Azure 数字孪生事件路由][1]

到事件中心的路由保持遥测消息的发送顺序。 因此，消息将按照最初接收的相同顺序抵达终结点。 事件网格和服务总线不保证终结点按照事件的发生顺序接收事件。 但是，事件架构包含时间戳，可用于确定事件到达终结点后的顺序。

## <a name="route-implementation"></a>路由实现

Azure 数字孪生服务当前支持以下 EndpointTypes  ：

* **EventHub** 是事件中心连接字符串终结点。
* **ServiceBus** 是服务总线连接字符串终结点。
* **EventGrid** 是事件网格连接字符串终结点。

Azure 数字孪生当前支持以下将发送到所选终结点的 EventTypes  ：

* **DeviceMessages** 是从用户的设备发送并由系统转发的遥测消息。
* **TopologyOperation** 是更改图或图的元数据的操作。 例如，添加或删除实体（如空间）。
* **SpaceChange** 是设备遥测消息生成的空间计算值的更改。
* **SensorChange** 是设备遥测消息生成的传感器计算值的更改。
* **UdfCustom** 是来自用户定义的函数的自定义通知。

> [!IMPORTANT]  
> 不是所有 EndpointTypes 都支持所有 EventTypes   。
> 请参阅下表了解每个 **EndpointType** 允许的 **EventTypes**。

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>有关如何创建终结点的详细信息和事件架构的示例，请参阅[出口和终结点](how-to-egress-endpoints.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解 Azure 数字孪生预览版的限制，请参阅[公共预览版服务的限制](concepts-service-limits.md)。

- 若要尝试 Azure 数字孪生示例，请参阅[查找可用房间的快速入门](quickstart-view-occupancy-dotnet.md)。

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
