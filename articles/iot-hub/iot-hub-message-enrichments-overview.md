---
title: Azure IoT 中心消息正的概述
description: Azure IoT 中心的消息的消息正的概述
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 2de945d2ef1d87b47ea81aac034916466d3c9bda
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/28/2019
ms.locfileid: "66258844"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>消息正对设备到云的 IoT 中心消息 （预览版）

*消息正*是一种向 IoT 中心能力*戳*之前将消息发送到指定终结点的其他信息的消息。 使用消息正的一个原因是包含可用于简化下游处理的数据。 例如，丰富设备的遥测消息数与设备孪生标记可以减少对客户进行 API 调用获取此信息的设备孪生的负载。

![消息正流](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

消息扩充具有三个关键要素：

* 扩充名称或键

* 一个值

* 一个或多个[终结点](iot-hub-devguide-endpoints.md)有关应用扩充。

该密钥可以是任何字符串。

值可以是任何下面的示例：

* 任何静态字符串。 不允许动态值，例如条件、 逻辑、 操作和函数。 例如，如果您开发的 SaaS 应用程序由多个客户，可将标识符分配给每个客户，并使该标识符在应用程序中可用。 运行程序时，IoT 中心将设备的遥测消息数使用标记客户的标识符，从而可以为每个客户以不同方式处理消息。

* 从设备孪生，例如其路径的信息。 将示例 *$twin.tags.field*并 *$twin.tags.latitude*。

* IoT 中心发送消息的名称。 此值是 *$iothubname*。

## <a name="applying-enrichments"></a>将应用到

消息可以来自支持的任何数据源[IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)，包括下面的示例：

* 设备遥测数据，例如温度或压力
* 设备孪生更改通知-设备孪生中的更改
* 设备生命周期事件，如创建或删除设备时

可以将正添加到要发送到 IoT 中心内置终结点的消息或被传送到自定义终结点，例如 Azure Blob 存储、 服务总线队列或服务总线主题的消息。

应用到每个终结点。 如果指定五个正以标记为特定终结点，请转到该终结点的所有消息都标记有相同的五个正。

若要了解如何试用消息到，请参阅[消息正教程](tutorial-message-enrichments.md)

## <a name="limitations"></a>限制

* 您可以添加这些中心的每个 IoT 中心最多 10 个到标准或基本层中。 对于在免费层中的 IoT 中心，可以添加最多 2 个到。

* 在某些情况下，如果应用扩充值设置为标记或在设备孪生中的属性值将标记作为字符串值。 例如，如果扩充值设置为 $twin.tags.field，消息将在而不是该字段的值从孪生与字符串"$twin.tags.field"标记。 在以下情况下发生这种情况：

   * IoT 中心是在基本层中。 基本层 IoT 中心不支持设备孪生。

   * IoT 中心是在标准层中，但发送消息的设备没有设备孪生。

   * IoT 中心是在标准层中，但扩充值所使用的设备孪生路径不存在。 例如，如果扩充值设置为 $twin.tags.location，并且设备孪生不具有标记下的位置属性，该消息均被标记为"$twin.tags.location"的字符串。 

* 设备孪生的更新可能需要五分钟才会反映在相应的扩充值。

* 总消息大小，包括到，不能超过 256 KB。 如果消息大小超出了 256 KB，IoT 中心将删除该消息。 可以使用[IoT 中心度量值](iot-hub-metrics.md)来识别和调试错误时将删除消息。 例如，你可以监视 d2c.telemetry.egress.invalid。

## <a name="pricing"></a>定价

消息正都可用于无需额外付费。 目前，向你收费时将消息发送到 IoT 中心。 您只需支付一次该消息，即使消息将转到多个终结点。

## <a name="availability"></a>可用性

此功能目前以预览版提供，并在除美国东部、 美国西部、 欧洲西部的所有区域均提供[Azure 政府版](/azure-government/documentation-government-welcome.md)， [Azure 中国 21Vianet](/azure/china/china-welcome.md)，和[Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/).

## <a name="next-steps"></a>后续步骤

请查看以下文章详细了解消息路由到 IoT 中心：

* [使用 IoT 中心消息路由到不同的终结点发送设备到云的消息](iot-hub-devguide-messages-d2c.md)

* [教程：路由的 IoT 中心](tutorial-routing.md)