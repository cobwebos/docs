---
title: Azure IoT 中心消息根据概述
description: 本文介绍消息根据，它使 IoT 中心能够在消息发送到指定终结点之前，将消息标记为包含附加信息。
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: c3dbd01faf61c164c88f09b0da03c07be4abd187
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429125"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>用于设备到云的 IoT 中心消息的消息根据

*消息根据*是 IoT 中心在将消息发送到指定终结点之前，用其他信息来*标记*消息的能力。 使用 message 根据的一个原因是包含可用于简化下游处理的数据。 例如，使用设备克隆标记浓缩设备遥测消息可降低客户的负载，使设备对此信息进行 API 调用。

![消息根据流](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

消息扩充具有三个关键元素：

* 扩充名称或密钥

* 值

* 应应用扩充的一个或多个[终结点](iot-hub-devguide-endpoints.md)。

**该键**是一个字符串。 密钥只能包含字母数字字符或以下特殊字符：连字符（`-`）、下划线（`_`）和句点（`.`）。

**该值**可以是以下任一示例：

* 任何静态字符串。 不允许使用动态值，如条件、逻辑、操作和函数。 例如，如果你开发由多个客户使用的 SaaS 应用程序，则可以为每个客户指定一个标识符，并使该标识符在应用程序中可用。 当应用程序运行时，IoT 中心会用客户的标识符标记设备遥测消息，从而使每个客户都可以不同地处理消息。

* 发送消息的 IoT 中心的名称。 此值为 *$iothubname*。

* 设备克隆中的信息，例如其路径。 示例 *$twin. 标记. 字段*和 *$twin*。

   > [!NOTE]
   > 目前，仅 $iothubname、$twin、$twin 和 $twin 都是消息扩充的支持的变量和属性。

消息根据作为应用程序属性添加到发送到所选终结点的消息。  

## <a name="applying-enrichments"></a>应用根据

消息可来自[IoT 中心消息路由](iot-hub-devguide-messages-d2c.md)支持的任何数据源，包括以下示例：

* 设备遥测，如温度或压力
* 设备克隆更改通知--设备克隆中的更改
* 设备生命周期事件，例如设备创建或删除的时间

你可以将根据添加到发送到 IoT 中心内置终结点的消息，或添加路由到自定义终结点（例如 Azure Blob 存储、服务总线队列或服务总线主题）的消息。

通过选择终结点作为事件网格，可以将根据添加到要发布到事件网格中的消息。 我们基于事件网格订阅在 IoT 中心内创建到设备遥测的默认路由。 此单个路由可以处理所有的事件网格订阅。 您可以在创建设备遥测的事件网格订阅后，为事件网格终结点配置根据。 有关详细信息，请参阅[Iot 中心和事件网格](iot-hub-event-grid.md)。

每个终结点应用根据。 如果为特定终结点指定要标记的五个根据，则将使用相同的5个根据来标记发送到该终结点的所有消息。

可以使用以下方法配置根据：

| **方法** | **命令** |
| ----- | -----| 
| 门户 | [Azure 门户](https://portal.azure.com) | 请参阅[message 根据教程](tutorial-message-enrichments.md) | 
| Azure CLI   | [az iot 中心消息-扩充](https://docs.microsoft.com/cli/azure/iot/hub/message-enrichment?view=azure-cli-latest) |
| Azure PowerShell | [AzIotHubMessageEnrichment](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubmessageenrichment?view=azps-2.8.0) |

添加消息根据不会将延迟添加到消息路由。

若要尝试消息根据，请参阅[message 根据教程](tutorial-message-enrichments.md)

## <a name="limitations"></a>限制

* 对于 "标准" 层或 "基本" 层中的中心，每个 IoT 中心最多可添加10个根据。 对于免费层的 IoT 中心，最多可添加2个根据。

* 在某些情况下，如果要应用的扩充的值设置为设备克隆中的标记或属性，则会将该值标记为字符串值。 例如，如果将扩充值设置 $twin 为 ""，则将使用字符串 "$twin，而不是从整数开始的字段的值来标记消息。 在以下情况下会发生这种情况：

   * IoT 中心在 "基本" 层中。 基本层 IoT 中心不支持设备孪生。

   * IoT 中心在标准层中，但发送消息的设备没有设备克隆。

   * IoT 中心在标准层中，但用于扩充的值的设备克隆路径不存在。 例如，如果 "扩充" 值设置为 "$twin"，并且设备克隆在 "标记" 下没有 "位置" 属性，则会将该消息标记为 "$twin 位置" 字符串。 

* 设备克隆的更新可能需要长达五分钟才能反映在相应的扩充值中。

* 消息总大小（包括根据）不能超过 256 KB。 如果消息大小超过 256 KB，IoT 中心将删除该消息。 删除消息时，可以使用[IoT 中心指标](iot-hub-metrics.md)来识别和调试错误。 例如，你可以监视 d2c。

* 消息根据不适用于数字克隆更改事件（ [IoT 即插即用公共预览版](../iot-pnp/overview-iot-plug-and-play.md)的一部分）。

## <a name="pricing"></a>价格

消息根据可用，无额外费用。 目前，向 IoT 中心发送消息时需要支付费用。 你只需为该消息收费一次，即使该消息发送到多个终结点也是如此。

## <a name="next-steps"></a>后续步骤

有关将消息路由到 IoT 中心的详细信息，请参阅以下文章：

* [Message 根据教程](tutorial-message-enrichments.md)

* [使用 IoT 中心消息路由将设备到云消息发送到不同的终结点](iot-hub-devguide-messages-d2c.md)

* [教程： IoT 中心路由](tutorial-routing.md)
