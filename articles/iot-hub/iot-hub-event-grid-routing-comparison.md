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
ms.openlocfilehash: e7d5ab3ebdf29fbf699220a3b214176ec4862739
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60398718"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>比较 IoT 中心的消息路由和事件网格

Azure IoT 中心提供了流式处理来自连接的设备数据并将该数据集成到您的业务应用程序的能力。 IoT 中心提供了两种将 IoT 事件集成到其他 Azure 服务或商业应用程序的方法。 本文讨论提供此功能的两个功能，以便你可选择最适合你的方案的选项。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)**:此 IoT 中心功能使用户能够将设备到云消息路由到 Azure 存储容器、 事件中心、 服务总线队列和服务总线主题等的服务终结点。 路由还提供了一种查询功能，让你先筛选数据，再将其路由到终结点。 除了设备遥测数据之外，还可以发送可用于触发操作的[非遥测事件](iot-hub-devguide-messages-d2c.md#non-telemetry-events)。 

**IoT 中心与事件网格集成**:Azure 事件网格是一种完全托管的事件路由服务，使用发布-订阅模型。 IoT 中心和事件网格共同协作，准实时地[将 IoT 中心事件集成到 Azure 和非 Azure 服务中](iot-hub-event-grid.md)。 

## <a name="differences"></a>差异

尽管消息路由和事件网格都启用了警报配置，但两者之间存在一些重要差异。 有关详细信息，请参照下表：

| Feature | IoT 中心消息路由 | IoT 中心与事件网格的集成 |
| ------- | --------------- | ---------- |
| **设备消息** | 是，消息路由可用于遥测数据。 | 否，事件网格仅可用于非遥测 IoT 中心事件。 |
| **设备事件** | 是，消息路由可报告双向更改和设备生命周期事件。 | 是，事件网格可以报告设备何时创建、删除、连接以及何时与 IoT 中心断开连接 |
| **排序** | 是，事件顺序保持不变。  | 否，无法保证事件顺序。 | 
| **最大消息大小** | 256 KB，设备到云 | 64 KB |
| **筛选** | 对消息应用程序属性、消息系统属性、消息正文、设备孪生标记和设备孪生属性执行的丰富筛选。 有关示例，请参阅[消息路由查询语法](iot-hub-devguide-routing-query-syntax.md)。 | 根据设备 ID 的后缀/前缀进行筛选，适用于存储等分层服务。 |
| **EndPoints** | <ul><li>事件中心</li> <li>Azure Blob 存储</li> <li>服务总线队列</li> <li>服务总线主题</li></ul><br>付费 IoT 中心 SKU（S1、S2 和 S3）限制为 10 个自定义终结点。 每个 IoT 中心都可创建 100 个路由。 | <ul><li>Azure Functions</li> <li>Azure 自动化</li> <li>事件中心</li> <li>逻辑应用</li> <li>存储 Blob</li> <li>自定义主题</li> <li>通过 Webhook 的第三方服务</li></ul><br>有关最新的终结点列表，请参阅[事件网格事件处理程序](../event-grid/overview.md#event-handlers)。 |
| **成本** | 不会针对消息路由单独收费。 仅针对 IoT 中心的遥测数据入口收费。 例如，如果将一条消息路由到三个不同终结点，则只收取一条消息的费用。 | 不收取 IoT 中心费用。 事件网格提供每月前 100,000 个操作免费，0.60 美元每百万个操作之后。 |

## <a name="similarities"></a>相似之处

IoT 中心消息路由和事件网格也具有相似之处，其中一些详细信息如下表所示：

| Feature | IoT 中心消息路由 | IoT 中心与事件网格的集成 |
| ------- | --------------- | ---------- |
| **可靠性** | 高：将每个消息传送到至少一次为每个路由的终结点。 所有未在一小时内发送的消息都将过期。 | 高：将每个消息传送到至少一次为每个订阅的 webhook。 所有未在 24 小时内发送的事件都将过期。 | 
| **伸缩性** | 高：优化以支持数百万台同步连接的设备发送数十亿条消息。 | 高：能够每秒每个区域的路由 10,000,000 事件。 |
| **延迟** | 低：近乎实时。 | 低：近乎实时。 |
| **发送到多个终结点** | 是，将一条消息发送到多个终结点。 | 是，将一条消息发送到多个终结点。  | 
| **安全性** | Iot 中心提供每设备标识和可吊销的访问控制。 有关详细信息，请参阅 [IoT 中心访问控制](iot-hub-devguide-security.md)。 | 事件网格提供三点验证：事件订阅、事件发布和 Webhook 事件传送。 有关详细信息，请参阅[事件网格安全性和身份验证](../event-grid/security-authentication.md)。 |

## <a name="how-to-choose"></a>如何选择

IoT 中心消息路由和 IoT 中心与事件网格的集成通过执行不同的操作来实现类似结果。 它们均从 IoT 中心解决方案中获取信息并传递信息，以便其他服务可作出反应。 那么，如何决定使用哪一种？ 请考虑以下问题来帮助指导您的决定： 

* **要向终结点发送什么类型的数据？**

   必须将遥测数据发送到其他服务时，请使用 IoT 中心消息路由。 借助消息路由，还可以查询消息应用程序和系统属性、消息正文、设备孪生标记和设备孪生属性。

   IoT 中心与事件网格的集成可与 IoT 中心服务中发生的事件一起使用。 这些 IoT 中心事件包括设备已创建、已删除、已连接和已断开连接。 

* **哪些终结点需要接收此信息？**

   IoT 中心消息路由支持有限的终结点，但可构建连接器以将数据和事件重新路由到其他终结点。 有关受支持终结点的完整列表，请参阅上节中的表。 

   IoT 中心与事件网格的集成支持更多终结点。 它也可与 Webhook 一起使用，将路由扩展到 Azure 服务生态系统外，并集成到第三方商业应用程序中。 

* **数据按顺序到达是否重要？**

   IoT 中心消息路由保持消息的发送顺序不变，以便其以相同顺序到达。

   事件网格不保证终结点按照事件的发生顺序接收事件。 但是，事件架构确实包含时间戳，可用于确定事件到达终结点后的顺序。 

## <a name="next-steps"></a>后续步骤

* 详细了解 [IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)和 [IoT 中心终结点](iot-hub-devguide-endpoints.md)。
* 详细了解 [Azure 事件网格](../event-grid/overview.md)。
* 若要了解如何创建消息路由，请参阅[使用路由处理 IoT 中心的设备到云消息](../iot-hub/tutorial-routing.md)教程。
* 尝试通过事件网格集成[有关使用逻辑应用的 Azure IoT 中心事件发送电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)。