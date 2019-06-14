---
title: 比较 IoT 中心的事件网格和路由|Microsoft Docs
description: IoT 中心提供其自己的消息路由服务，同时还与事件网格集成，以进行事件发布。 比较这两个功能。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 0b17a87fa02c382ae19cca6e4abcfff2ec475450
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66252679"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>比较 IoT 中心的消息路由和事件网格

Azure IoT 中心可以从已连接的设备流式传输数据并将该数据集成到商业应用程序中。 IoT 中心提供了两种将 IoT 事件集成到其他 Azure 服务或商业应用程序的方法。 本文讨论提供此功能的两个功能，以便你可选择最适合你的方案的选项。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)** ：借助此 IoT 中心功能，用户可将设备到云消息路由到服务终结点，如 Azure 存储容器、事件中心、服务总线队列和服务总线主题。 路由还提供了一种查询功能，让你先筛选数据，再将其路由到终结点。 除了设备遥测数据之外，还可以发送可用于触发操作的[非遥测事件](iot-hub-devguide-messages-d2c.md#non-telemetry-events)。 

**IoT 中心与事件网格的集成**：Azure 事件网格是一种完全托管的事件路由服务，使用发布-订阅模型。 IoT 中心和事件网格共同协作，准实时地[将 IoT 中心事件集成到 Azure 和非 Azure 服务中](iot-hub-event-grid.md)。 IoT 中心会发布[设备事件](iot-hub-event-grid.md#event-types)（正式版），现在还会发布遥测事件（目前为公共预览版）。

## <a name="differences"></a>差异

尽管消息路由和事件网格都启用了警报配置，但两者之间存在一些重要差异。 有关详细信息，请参照下表：

| Feature | IoT 中心消息路由 | IoT 中心与事件网格的集成 |
| ------- | --------------- | ---------- |
| **设备消息和事件** | 是，消息路由可以用于遥测数据，报告孪生更改和设备生命周期事件。 | 是，事件网格可以用于遥测数据，还可以用于报告设备何时创建、删除、连接以及何时与 IoT 中心断开连接 |
| **排序** | 是，事件顺序保持不变。  | 否，无法保证事件顺序。 | 
| **筛选** | 对消息应用程序属性、消息系统属性、消息正文、设备孪生标记和设备孪生属性执行的丰富筛选。 有关示例，请参阅[消息路由查询语法](iot-hub-devguide-routing-query-syntax.md)。 | 基于事件类型、使用者类型和每个事件中的属性进行筛选。 有关示例，请参阅[了解在事件网格订阅中筛选事件](../event-grid/event-filtering.md)。 订阅遥测事件时，在发布到事件网格之前，你可以对数据应用其他筛选器来基于你的 IoT 中心内的消息属性、消息正文和设备孪生进行筛选。 请参阅[如何筛选事件](../iot-hub/iot-hub-event-grid.md#filter-events)。 |
| **EndPoints** | <ul><li>事件中心</li> <li>Azure Blob 存储</li> <li>服务总线队列</li> <li>服务总线主题</li></ul><br>付费 IoT 中心 SKU（S1、S2 和 S3）限制为 10 个自定义终结点。 每个 IoT 中心都可创建 100 个路由。 | <ul><li>Azure Functions</li> <li>Azure 自动化</li> <li>事件中心</li> <li>逻辑应用</li> <li>存储 Blob</li> <li>自定义主题</li> <li>队列存储</li> <li>Microsoft Flow</li> <li>通过 Webhook 的第三方服务</li></ul><br>每个 IoT 中心支持 500 个终结点。 有关最新的终结点列表，请参阅[事件网格事件处理程序](../event-grid/overview.md#event-handlers)。 |
| **成本** | 不会针对消息路由单独收费。 仅针对 IoT 中心的遥测数据入口收费。 例如，如果将一条消息路由到三个不同终结点，则只收取一条消息的费用。 | 不收取 IoT 中心费用。 事件网格每月免费提供前 100,000 次操作，之后每百万次操作收取 0.60 美元。 |

## <a name="similarities"></a>相似性

IoT 中心消息路由和事件网格也具有相似之处，其中一些详细信息如下表所示：

| Feature | IoT 中心消息路由 | IoT 中心与事件网格的集成 |
| ------- | --------------- | ---------- |
| **最大消息大小** | 256 KB，设备到云 | 256 KB，设备到云 |
| **可靠性** | 高：向终结点发送所有消息，每个路由至少一次。 所有未在一小时内发送的消息都将过期。 | 高：向 Webhook 发送所有消息，每个订阅至少一次。 所有未在 24 小时内发送的事件都将过期。 | 
| **伸缩性** | 高：已经过优化，可支持数百万个同时连接的设备发送数十亿条消息。 | 高：每个区域每秒可路由 10,000,000 个事件。 |
| **延迟** | 低：准实时。 | 低：准实时。 |
| **发送到多个终结点** | 是，将一条消息发送到多个终结点。 | 是，将一条消息发送到多个终结点。  
| **安全性** | Iot 中心提供每设备标识和可吊销的访问控制。 有关详细信息，请参阅 [IoT 中心访问控制](iot-hub-devguide-security.md)。 | 事件网格提供三点验证：事件订阅、事件发布和 Webhook 事件传送。 有关详细信息，请参阅[事件网格安全性和身份验证](../event-grid/security-authentication.md)。 |

## <a name="how-to-choose"></a>如何选择

IoT 中心消息路由和 IoT 中心与事件网格的集成通过执行不同的操作来实现类似结果。 它们均从 IoT 中心解决方案中获取信息并传递信息，以便其他服务可作出反应。 那么，如何决定使用哪一种？ 制定决策时，请思考以下问题： 

* **要向终结点发送什么类型的数据？**

   必须将遥测数据发送到其他服务时，请使用 IoT 中心消息路由。 借助消息路由，还可以查询消息应用程序和系统属性、消息正文、设备孪生标记和设备孪生属性。

   IoT 中心与事件网格的集成可与 IoT 中心服务中发生的事件一起使用。 这些 IoT 中心事件包括遥测数据，设备已创建、已删除、已连接和已断开连接。 订阅遥测事件时，在发布到事件网格之前，你可以对数据应用其他筛选器来基于你的 IoT 中心内的消息属性、消息正文和设备孪生进行筛选。 请参阅[如何筛选事件](../iot-hub/iot-hub-event-grid.md#filter-events)。

* **哪些终结点需要接收此信息？**

   IoT 中心消息路由支持有限数量的终结点和终结点类型，但可构建连接器以将数据和事件重新路由到其他终结点。 有关受支持终结点的完整列表，请参阅上节中的表。 

   与事件网格的 IoT 中心集成支持每个 IoT 中心 500 个终结点和大量的终结点类型。 它本身集成了 Azure Functions、逻辑应用和服务总线队列，并且还与 Webhook 一起工作以在 Azure 服务生态系统外部发送数据以及发送到第三方业务应用程序中。

* **数据按顺序到达是否重要？**

   IoT 中心消息路由保持消息的发送顺序不变，以便其以相同顺序到达。

   事件网格不保证终结点按照事件的发生顺序接收事件。 如果消息的绝对顺序至关重要，并/或使用者需要消息的可信唯一标识符，建议使用消息路由。 

## <a name="next-steps"></a>后续步骤

* 详细了解 [IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)和 [IoT 中心终结点](iot-hub-devguide-endpoints.md)。
* 详细了解 [Azure 事件网格](../event-grid/overview.md)。
* 若要了解如何创建消息路由，请参阅[使用路由处理 IoT 中心的设备到云消息](../iot-hub/tutorial-routing.md)教程。
* 通过[使用逻辑应用发送有关 Azure IoT 中心事件的电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)，尝试事件网格集成。