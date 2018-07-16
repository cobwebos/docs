---
title: Azure IoT 中心简介 | Microsoft Docs
description: 了解 Azure IoT 中心。 此 IoT 服务针对可缩放数据引入、设备管理和安全性而构建。
author: nberdy
ms.author: nberdy
ms.date: 07/04/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-hub
services: iot-hub
manager: briz
ms.openlocfilehash: 0e54c48eb63c5b70626e72f83de6d3676710d4c4
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860150"
---
# <a name="what-is-azure-iot-hub"></a>Azure IoT 中心是什么？

IoT 中心托管服务在云中进行托管，充当中央消息中心，用于 IoT 应用程序与其管理的设备之间的双向通信。 可以使用 Azure IoT 中心，通过数百万 IoT 设备和云托管解决方案后端之间可靠又安全的通信，生成 IoT 解决方案。 几乎可以将任何设备连接到 IoT 中心。

IoT 中心支持设备与云之间的双向通信。 IoT 中心支持多种消息传递模式，例如设备到云的遥测、从设备上传文件以及从云控制设备的请求-回复方式。 IoT 中心监视有助于通过跟踪事件（例如设备创建、设备故障和设备连接），维持解决方案的运行状况。

IoT 中心的功能有助于生成可缩放且功能完整的 IoT 解决方案，例如管理制造业中使用的工业设备、跟踪医疗保健中宝贵的资产以及监视办公大楼使用情况。

## <a name="scale-your-solution"></a>缩放解决方案

IoT 中心可扩展为数百万个同时连接的设备，以及每秒数百万个事件以支持 IoT 工作负载。 IoT 中心提供多种服务级别，最大限度地满足你的缩放需求。 [了解详细信息](https://azure.microsoft.com/pricing/details/iot-hub/)。

## <a name="secure-your-communications"></a>保护通信

IoT 中心为发送数据的设备提供保护信道。

* 通过对每个设备进行身份验证，可以安全地将每个设备连接到 IoT 中心，并且安全地管理这些设备。
* 可以完全控制设备访问，并可以根据每个设备的级别控制连接。
* [IoT 中心设备预配服务](https://docs.microsoft.com/azure/iot-dps/)在设备首次启动时，自动将设备提供给正确的 IoT 中心。
* 多种身份验证类型支持各种设备功能：
  * 基于 SAS 令牌的身份验证可快速启动 IoT 解决方案。
  * 单个 X.509 证书身份验证，用于安全、以标准为基础的身份验证。
  * X.509 CA 身份验证，用于简单、以标准为基础的注册。

## <a name="route-device-data"></a>路由设备数据

内置的消息路由功能可以灵活地设置基于规则的自动消息扇出：

* 使用消息路由控制中心发送设备遥测的位置。
* 无需支付额外费用，即可将消息路由到多个终结点。
* 无代码路由规则替代了自定义消息调度程序代码。

## <a name="integrate-with-other-services"></a>与其他服务集成

可以将 IoT 中心与其他 Azure 服务集成，生成完整的端对端解决方案。 例如，使用：

* [Azure 事件网格](https://docs.microsoft.com/azure/event-grid/)，可以采用可靠、可缩放且安全的方式快速地让你的业务响应关键事件。

* [Azure 逻辑应用](https://docs.microsoft.com/azure/logic-apps/)，可以自动执行业务流程。

* [Azure 机器学习](https://docs.microsoft.com/azure/machine-learning/)，可为解决方案添加机器学习和 AI 模型。

* [Azure 流分析](https://docs.microsoft.com/azure/stream-analytics/)，可从设备对数据流执行实时分析计算。

## <a name="configure-and-control-your-devices"></a>配置和控制设备

可以通过内置功能的数组，管理连接到 IoT 中心的设备。

* 存储、同步和查询设备元数据和所有设备的状态信息。
* 根据每个设备或设备的共同功能设置设备状态。
* 通过信息路由集成自动响应报告设备的状态更改。

## <a name="make-your-solution-highly-available"></a>使解决方案具有高可用性

[IoT 中心的服务级别协议](https://azure.microsoft.com/support/legal/sla/iot-hub/)提供 99.9% 的可用性。 完整 [Azure SLA](https://azure.microsoft.com/support/legal/sla/) 说明了 Azure 作为整体的保证可用性。

## <a name="connect-your-devices"></a>连接数据

使用 [Azure IoT 设备 SDK](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks) 库，生成在设备上运行且与 IoT 中心集成的应用程序。 支持的平台包括多个 Linux 分发版、Windows 和实时操作系统。 支持的语言包括：

* C
* C#
* Java
* Python
* Node.js。

IoT 中心和设备 SDK 支持用于连接设备的以下协议：

* HTTPS
* AMQP
* 基于 WebSockets 的 AMQP
* MQTT
* 基于 WebSocket 的 MQTT

如果解决方案无法使用设备库，则设备可以使用 MQTT v3.1.1、HTTPS 1.1 或 AMQP 1.0 协议，本地连接到中心。

如果解决方案无法使用其中一种支持的协议，则可以将 IoT 中心扩展到支持自定义协议：

* 使用 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)，创建现场网关，执行边缘的协议转换。
* 自定义 [Azure IoT 协议网关](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md)，在云中执行协议转换。

## <a name="quotas-and-limits"></a>配额和限制

每个 Azure 订阅都有默认的配额限制以防止服务滥用，并且这些限制可能会影响 IoT 解决方案的作用域。 每个订阅的当前限制是每个订阅 10 个 IoT 中心。 可以通过联系支持人员，请求增加配额。 有关配额限制的更多详细信息，请参阅：

* [Azure 订阅服务限制](../azure-subscription-service-limits.md)
* [IoT Hub throttling and you](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)（IoT 中心限制和你）

## <a name="next-steps"></a>后续步骤

若要尝试端对端的 IoT 解决方案，请参阅 IoT 中心快速入门：

* [快速入门：将遥测数据从设备发送到 IoT 中心](quickstart-send-telemetry-node.md)
