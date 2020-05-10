---
title: 了解 Azure IoT 中心终结点 | Microsoft Docs
description: 开发人员指南 - 有关 IoT 中心面向设备和面向服务的终结点的参考信息。
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 9554713e50e7a2ead2e25f274428ad0ecba4934d
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82996960"
---
# <a name="reference---iot-hub-endpoints"></a>参考 - IoT 中心终结点

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>IoT 中心名称

可在中心的“概述”页上找到在门户中托管终结点的 IoT 中心的主机名  。 默认情况下，IoT 中心的 DNS 名称如下所示：`{your iot hub name}.azure-devices.net`。

## <a name="list-of-built-in-iot-hub-endpoints"></a>内置 IoT 中心终结点列表

Azure IoT 中心属于多租户服务，向各种执行组件公开功能。 下图显示了 IoT 中心公开的各种终结点。

![IoT 中心终结点](./media/iot-hub-devguide-endpoints/endpoints.png)

以下列表对这些终结点进行了说明：

* **资源提供程序**。 IoT 中心资源提供程序公开一个 [Azure 资源管理器](../azure-resource-manager/management/overview.md)接口。 此接口使 Azure 订阅所有者可以创建和删除 IoT 中心以及更新 IoT 中心属性。 IoT 中心属性可管理[中心级别安全策略](iot-hub-devguide-security.md#access-control-and-permissions)，而不是设备级别的访问控制以及云到设备和设备到云消息传送的功能选项。 IoT 中心资源提供程序还可让你[导出设备标识](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)。

* **设备标识管理**。 每个 IoT 中心公开一组用于管理设备标识的 HTTPS REST 终结点（创建、检索、更新和删除）。 [设备标识](iot-hub-devguide-identity-registry.md)用于设备身份验证和访问控制。

* **设备孪生管理**。 每个 IoT 中心都会公开一组面向服务的 HTTPS REST 终结点，用于查询和更新[设备孪生](iot-hub-devguide-device-twins.md)（更新标记和属性）。

* **作业管理**。 每个 IoT 中心都会公开一组面向服务的 HTTPS REST 终结点，用于查询和管理[作业](iot-hub-devguide-jobs.md)。

* **设备终结点**。 对于标识注册表中的每个设备，IoT 中心都会公开一组终结点：

  * *发送设备到云的消息*。 设备使用此终结点[发送设备到云的消息](iot-hub-devguide-messages-d2c.md)。

  * *接收云到设备的消息*。 设备使用此终结点接收目标[云到设备的消息](iot-hub-devguide-messages-c2d.md)。

  * *启动文件上传*。 设备使用此终结点接收来自 IoT 中心的 Azure 存储 SAS URI，以便[上传文件](iot-hub-devguide-file-upload.md)。

  * *检索和更新设备孪生属性*。 设备使用此终结点访问其[设备孪生](iot-hub-devguide-device-twins.md)的属性。

  * *接收直接方法请求*。 设备使用此终结点侦听[直接方法](iot-hub-devguide-direct-methods.md)的请求。

    这些终结点使用 [MQTT v3.1.1](https://mqtt.org/)、HTTPS 1.1 和 [AMQP 1.0](https://www.amqp.org/) 协议进行公开。 AMQP 和 MQTT 在端口443上也可通过[websocket](https://tools.ietf.org/html/rfc6455)获得。

* **服务终结点**。 每个 IoT 中心公开一组终结点，供解决方案后端用于与设备通信。 除了一个例外，这些终结点仅使用[AMQP](https://www.amqp.org/)和 AMQP over websocket 协议公开。 直接方法调用终结点通过 HTTPS 协议公开。
  
  * *接收设备到云的消息*。 此终结点与 [Azure 事件中心](https://azure.microsoft.com/documentation/services/event-hubs/)兼容。 后端服务可用它来读取由设备发送的[设备到云的消息](iot-hub-devguide-messages-d2c.md)。 可以在此内置终结点之外的 IoT 中心创建自定义终结点。
  
  * *发送云到设备的消息和接收送达确认*。 这些终结点可让解决方案后端发送可靠的[云到设备的消息](iot-hub-devguide-messages-c2d.md)，以及接收对应的传送或过期确认。
  
  * *接收文件通知*。 此消息传递终结点允许在设备成功上传文件时接收通知。 
  
  * *直接方法调用*。 此终结点允许后端服务在设备上调用[直接方法](iot-hub-devguide-direct-methods.md)。
  
  * *接收操作监视事件*。 此终结点可以用于接收操作监视事件，前提是已将 IoT 中心配置为发出这些事件。 有关详细信息，请参阅 [IoT 中心操作监视](iot-hub-operations-monitoring.md)。

[Azure IoT SDK](iot-hub-devguide-sdks.md) 一文介绍了访问这些终结点的各种方法。

所有 IoT 中心终结点都使用 [TLS](https://tools.ietf.org/html/rfc5246) 协议，且绝不会在未加密/不安全的通道上公开任何终结点。

## <a name="custom-endpoints"></a>自定义终结点

可将订阅中的现有 Azure 服务链接到充当消息路由终结点的 IoT 中心。 这些终结点充当服务终结点，用作消息路由的接收器。 设备无法直接写入附加终结点。 详细了解[消息路由](../iot-hub/iot-hub-devguide-messages-d2c.md)。

IoT 中心当前支持将以下 Azure 服务作为附加终结点：

* Azure 存储容器
* 事件中心
* 服务总线队列
* 服务总线主题

有关可添加的终结点的数量限制，请参阅[配额和限制](iot-hub-devguide-quotas-throttling.md)。

可以使用 REST API [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) 获取终结点的运行状况状态。 我们建议使用与路由消息延迟相关的 [IoT 中心指标](iot-hub-metrics.md)来识别和调试终结点运行状况不佳或运行不正常时的错误，因为我们预计当终结点处于其中一种状态时，延迟会更高。

|运行状况|说明|
|---|---|
|healthy|终结点按预期方式接受消息。|
|不正常|终结点未按预期方式接受消息，IoT 中心正重试将数据发送到此终结点。 当 IoT 中心建立了最终一致的运行状况状态以后，系统会将不正常终结点的状态更新为正常。|
|未知|IoT 中心尚未建立与该终结点的连接。 尚未向该终结点传送任何消息，也未拒绝该终结点发送的任何消息。|
|不活动|重试期间，在 IoT 中心重试发送消息以后，终结点不接受消息。|

## <a name="field-gateways"></a>现场网关

在 IoT 解决方案中，*现场网关*位于设备和 IoT 中心终结点之间。 它通常位于靠近设备的位置。 设备使用设备支持的协议，直接与现场网关通信。 现场网关使用 IoT 中心支持的协议连接到 IoT 中心终结点。 现场网关可能是专用硬件设备或运行自定义网关软件的低功率计算机。

可使用 [Azure IoT Edge](/azure/iot-edge/) 实现现场网关。 IoT Edge 提供一些功能，例如从多台设备向同一 IoT 中心连接多路复用通信。

## <a name="next-steps"></a>后续步骤

此 IoT 中心开发人员指南中的其他参考主题包括：

* [用于设备孪生、作业和消息路由的 IoT 中心查询语言](iot-hub-devguide-query-language.md)
* [配额和限制](iot-hub-devguide-quotas-throttling.md)
* [IoT 中心 MQTT 支持](iot-hub-mqtt-support.md)
* [了解 IoT 中心 IP 地址](iot-hub-understand-ip-address.md)