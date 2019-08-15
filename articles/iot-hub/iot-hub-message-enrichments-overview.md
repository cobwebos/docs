---
title: Azure IoT 中心消息扩充概述
description: Azure IoT 中心消息的消息扩充概述
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 5ef1749217dd8980892d7bf933cbe1412929fbbd
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976979"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>设备到云 IoT 中心消息的消息扩充（预览版）

“消息扩充”是 IoT 中心在将消息发送到指定的终结点之前，使用附加的信息为消息添加戳记的功能。 使用消息扩充的原因之一是包含可用于简化下游处理的数据。 例如，使用设备孪生标记扩充设备遥测消息可以减少客户对此信息发出设备孪生 API 调用所造成的负载。

![消息扩充流](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

消息扩充具有三个关键元素：

* 扩充名称或键

* 值

* 要对其应用扩充的一个或多个[终结点](iot-hub-devguide-endpoints.md)。

**该键**是一个字符串。 密钥只能包含字母数字字符或以下特殊字符: 连字符 (`-`)、下划线 (`_`) 和句点 (`.`)。

**该值**可以是以下任一示例:

* 任意静态字符串。 不允许条件、逻辑、操作和函数等动态值。 例如，如果你开发由多个客户使用的 SaaS 应用程序，可为每个客户分配一个标识符，并使该标识符在应用程序中可用。 当该应用程序运行时，IoT 中心会使用客户的标识符来戳记设备遥测消息，从而可为每个客户以不同的方式处理消息。

* 发送消息的 IoT 中心的名称。 此值为 *$iothubname*。

* 设备孪生中的信息，例如其路径。 示例包括 *$twin.tags.field* 和 *$twin.tags.latitude*。

   > [!NOTE]
   > 目前, 仅 $iothubname、$twin、$twin 和 $twin 都是消息扩充的支持的变量和属性。

消息根据作为应用程序属性添加到发送到所选终结点的消息。  

## <a name="applying-enrichments"></a>应用扩充

消息可能来自 [IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)支持的任何数据源，包括：

* 设备遥测数据，例如温度或压力
* 设备孪生更改通知 - 设备孪生中发生的更改
* 设备生命周期事件，例如创建或删除了设备时

可将扩充添加到正在发往 IoT 中心内置终结点的消息，或添加到正在路由到自定义终结点（例如 Azure Blob 存储、服务总线队列或服务总线主题）的消息。

还可以通过选择“事件网格”作为终结点，将扩充添加到正在发布到事件网格的消息。 有关详细信息，请参阅 [IoT 中心和事件网格](iot-hub-event-grid.md)。

扩充是按终结点应用的。 如果指定要为特定终结点戳记五个扩充，则发往该终结点的所有消息都将使用五个相同的扩充进行戳记。

若要了解如何体验消息扩充，请参阅[消息扩充教程](tutorial-message-enrichments.md)

## <a name="limitations"></a>限制

* 对于标准或基本层中的每个 IoT 中心，最多可以添加 10 个扩充。 对于免费层中的 IoT 中心，最多可以添加 2 个扩充。

* 在某些情况下，如果你要使用设置为设备孪生中的标记或属性的值应用扩充，该值将戳记为字符串值。 例如，如果某个扩充值设置为 $twin.tags.field，则会使用字符串“$twin.tags.field”而不是孪生中该字段的值来戳记消息。 这适用于以下情况：

   * IoT 中心位于基本层中。 基本层 IoT 中心不支持设备孪生。

   * IoT 中心位于标准层中，但发送消息的设备没有设备孪生。

   * IoT 中心位于标准层中，扩充值使用的设备孪生路径不存在。 例如，如果扩充值设置为 $twin.tags.location，并且设备孪生在标记下没有位置属性，该会使用字符串“$twin.tags.location”戳记消息。 

* 对设备孪生所做的更新最长可能需要在五分钟后才反映在相应的扩充值中。

* 总消息大小（包括扩充）不能超过 256 KB。 如果消息大小超过 256 KB，IoT 中心会丢弃消息。 如果丢弃了消息，可以使用 [IoT 中心指标](iot-hub-metrics.md)来识别和调试错误。 例如，可以监视 d2c.telemetry.egress.invalid。

## <a name="pricing"></a>定价

使用消息扩充不会产生额外的费用。 目前，将消息发送到 IoT 中心需要付费。 即使消息要发往多个终结点，也只需支付该消息的费用一次。

## <a name="availability"></a>可用性

此功能目前以预览版提供，可在除美国东部、美国西部、西欧、[Azure 政府](/azure/azure-government/documentation-government-welcome)、[Azure 中国世纪互联](/azure/china)和 [Azure 德国](https://azure.microsoft.com/global-infrastructure/germany/)以外的其他所有区域中使用。

## <a name="next-steps"></a>后续步骤

请查看以下文章来详细了解如何将消息路由到 IoT 中心：

* [使用 IoT 中心消息路由将设备到云的消息发送到不同的终结点](iot-hub-devguide-messages-d2c.md)

* [教程：IoT 中心路由](tutorial-routing.md)
