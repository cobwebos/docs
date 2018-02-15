---
title: "比较 IoT 中心的事件网格和路由|Microsoft Docs"
description: "IoT 中心提供其自己的消息路由服务，同时还与事件网格集成，以进行事件发布。 比较这两个功能。"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.service: iot-hub
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/30/2018
ms.author: kgremban
ms.openlocfilehash: 5a0a97ccf033b2981ba13be455482146ba212228
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>比较 IoT 中心的消息路由和事件网格

Azure IoT 中心可以从已连接的设备流式传输数据并将该数据集成到商业应用程序中。 IoT 中心提供了两种将 IoT 事件集成到其他 Azure 服务或商业应用程序的方法。 本文讨论提供此功能的两个功能，以便你可选择最适合你的方案的选项。

* **IoT 中心消息路由**：此 IoT 中心功能使用户可将[设备到云消息](iot-hub-devguide-messages-read-custom.md)路由到服务终结点，如 Azure 存储容器、事件中心、服务总线队列和服务总线主题。 路由规则可灵活地执行基于查询的路由。 它们还支持通过查询触发操作的[重要警报](iot-hub-devguide-messages-d2c.md)，并且可以基于消息标头和正文。 
* **IoT 中心与事件网格的集成**：Azure 事件网格是一种完全托管的事件路由服务，使用发布 - 订阅模型。 IoT 中心和事件网格共同协作，准实时地[将 IoT 中心事件集成到 Azure 和非 Azure 服务中](iot-hub-event-grid.md)。 

## <a name="similarities-and-differences"></a>相似性和差异性

尽管消息路由和事件网格都启用了警报配置，但两者之间存在一些重要差异。 有关详细信息，请参照下表：

| 功能 | IoT 中心消息路由 | IoT 中心与事件网格的集成 |
| ------- | --------------- | ---------- |
| **设备消息** | 是，消息路由可用于遥测数据。 | 否，事件网格仅可用于非遥测 IoT 中心事件。 |
| **事件类型** | 是，消息路由可报告双向更改和设备生命周期事件。 | 是，事件网格可报告设备注册到 IoT 中心的时间，以及删除设备的时间。 |
| **排序** | 是，事件顺序保持不变。  | 否，无法保证事件顺序。 | 
| **最大消息大小** | 256 KB，设备到云 | 64 KB |
| **筛选** | 通过类似 SQL 的语言进行充分筛选，支持对消息标头和正文进行筛选。 有关示例，请参阅 [IoT 中心查询语言](iot-hub-devguide-query-language.md)。 | 根据设备 ID 的后缀/前缀进行筛选，适用于存储等分层服务。 |
| **EndPoints** | <ul><li>事件中心</li> <li>存储 blob</li> <li>服务总线队列</li> <li>服务总线主题</li></ul><br>付费 IoT 中心 SKU（S1、S2 和 S3）限制为 10 个自定义终结点。 每个 IoT 中心都可创建 100 个路由。 | <ul><li>Azure Functions</li> <li>Azure 自动化</li> <li>事件中心</li> <li>逻辑应用</li> <li>Microsoft Flow</li> <li>通过 Webhook 的第三方服务</li></ul><br>有关最新的终结点列表，请参阅[事件网格事件处理程序](../event-grid/overview.md#event-handlers)。 |
| **成本** | 不会针对消息路由单独收费。 仅针对 IoT 中心的遥测数据入口收费。 例如，如果将一条消息路由到三个不同终结点，则只收取一条消息的费用。 | 不收取 IoT 中心费用。 事件网格每月免费提供前 100,000 次操作，之后每百万次操作收取 0.60 美元。 |

IoT 中心消息路由和事件网格也具有相似之处，其中一些详细信息如下表所示：

| 功能 | IoT 中心消息路由 | IoT 中心与事件网格的集成 |
| ------- | --------------- | ---------- |
| **可靠性** | 高：向终结点发送所有消息，每个路由至少一次。 所有未在一小时内发送的消息都将过期。 | 高：向 Webhook 发送所有消息，每个订阅至少一次。 所有未在 24 小时内发送的事件都将过期。 | 
| **伸缩性** | 高：已经过优化，可支持数百万个同时连接的设备发送数十亿条消息。 | 高：每个区域每秒可路由 10,000,000 个事件。 |
| **延迟** | 低：准实时。 | 低：准实时。 |
| **发送到多个终结点** | 是，将一条消息发送到多个终结点。 | 是，将一条消息发送到多个终结点。  | 
| **安全性** | Iot 中心提供每设备标识和可吊销的访问控制。 有关详细信息，请参阅 [IoT 中心访问控制](iot-hub-devguide-security.md)。 | 事件网格提供三点验证：事件订阅、事件发布和 Webhook 事件传送。 有关详细信息，请参阅[事件网格安全性和身份验证](../event-grid/security-authentication.md)。 |

## <a name="how-to-choose"></a>如何选择

IoT 中心消息路由和 IoT 中心与事件网格的集成通过执行不同的操作来实现类似结果。 它们均从 IoT 中心解决方案中获取信息并传递信息，以便其他服务可作出反应。 那么，如何决定使用哪一种？ 除上一节的数据外，可通过以下问题帮助你做出决定： 

* **要向终结点发送什么类型的数据？**

   必须将遥测数据发送到其他服务时，请使用 IoT 中心消息路由。 消息路由还可查询消息标头和消息正文。 

   IoT 中心与事件网格的集成可与 IoT 中心服务中发生的事件一起使用。 这些 IoT 中心事件包括设备的创建和删除。 

* **哪些终结点需要接收此信息？**

   IoT 中心消息路由支持有限的终结点，但可构建连接器以将数据和事件重新路由到其他终结点。 有关受支持终结点的完整列表，请参阅上节中的表。 

   IoT 中心与事件网格的集成支持更多终结点。 它也可与 Webhook 一起使用，将路由扩展到 Azure 服务生态系统外，并集成到第三方商业应用程序中。 

* **数据按顺序到达是否重要？**

   IoT 中心消息路由保持消息的发送顺序不变，以便其以相同顺序到达。

   事件网格不保证终结点按照事件的发生顺序接收事件。 但是，事件架构确实包含时间戳，可用于确定事件到达终结点后的顺序。 

## <a name="next-steps"></a>后续步骤

* 了解有关 [IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)和 [IoT 中心终结点](iot-hub-devguide-endpoints.md)的详细信息。

* 了解有关 [Azure 事件网格](../event-grid/overview.md)的详细信息

* 通过[使用逻辑应用发送有关 Azure IoT 中心事件的电子邮件通知](../event-grid/publish-iot-hub-events-to-logic-apps.md)，尝试事件网格集成