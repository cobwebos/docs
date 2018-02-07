---
title: "了解 Azure IoT 中心终结点 | Microsoft Docs"
description: "开发人员指南 - 有关 IoT 中心面向设备和面向服务的终结点的参考信息。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2017
ms.author: dobett
ms.openlocfilehash: dc983549aea53ed29859205102d6308a3367bec7
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="reference---iot-hub-endpoints"></a>参考 - IoT 中心终结点

## <a name="iot-hub-names"></a>IoT 中心名称

可以在“概述”边栏选项卡上找到在门户中承载你的终结点的 IoT 中心的名称。 默认情况下，IoT 中心的 DNS 名称如下所示：`{your iot hub name}.azure-devices.net`。

可以使用 Azure DNS 为 IoT 中心创建自定义 DNS 名称。 有关详细信息，请参阅[使用 Azure DNS 为 Azure 服务提供自定义域设置](../dns/dns-custom-domain.md)。

## <a name="list-of-built-in-iot-hub-endpoints"></a>内置 IoT 中心终结点列表

Azure IoT 中心属于多租户服务，向各种执行组件公开功能。 下图显示了 IoT 中心公开的各种终结点。

![IoT 中心终结点][img-endpoints]

以下列表对这些终结点进行了说明：

* **资源提供程序**。 IoT 中心资源提供程序公开一个 [Azure 资源管理器][lnk-arm]接口。 此接口使 Azure 订阅所有者可以创建和删除 IoT 中心以及更新 IoT 中心属性。 IoT 中心属性可管理[中心级别的安全策略][lnk-accesscontrol]，相对于设备级别的访问控制和云到设备及设备到云消息传送的功能选项。 IoT 中心资源提供程序还可让你[导出设备标识][lnk-importexport]。
* **设备标识管理**。 每个 IoT 中心公开一组用于管理设备标识的 HTTPS REST 终结点（创建、检索、更新和删除）。 [设备标识][lnk-device-identities]用于设备身份验证和访问控制。
* **设备孪生管理**。 每个 IoT 中心都会公开一组面向服务的 HTTPS REST 终结点，用于查询和更新[设备孪生][lnk-twins]（更新标记和属性）。
* **作业管理**。 每个 IoT 中心都会公开一组面向服务的 HTTPS REST 终结点，用于查询和管理[作业][lnk-jobs]。
* **设备终结点**。 对于标识注册表中的每个设备，IoT 中心都会公开一组终结点：

  * *发送设备到云的消息*。 设备使用此终结点[发送设备到云的消息][lnk-d2c]。
  * *接收云到设备的消息*。 设备使用此终结点接收面向[云到设备的消息][lnk-c2d]。
  * *启动文件上传*。 设备使用此终结点接收来自 IoT 中心的 Azure 存储 SAS URI，以便[上传文件][lnk-upload]。
  * *检索和更新设备孪生属性*。 设备使用此终结点访问其[设备孪生][lnk-twins]的属性。
  * *接收直接方法请求*。 设备使用此终结点侦听[直接方法][lnk-methods]的请求。

    这些终结点使用 [MQTT v3.1.1][lnk-mqtt]、HTTPS 1.1 和 [AMQP 1.0][lnk-amqp] 协议进行公开。 还可通过端口 443 上的 [WebSocket][lnk-websockets] 来实现 AMQP。

* **服务终结点**。 每个 IoT 中心公开一组终结点，供解决方案后端用于与设备通信。 除了一个例外，这些终结点只使用 [AMQP][lnk-amqp] 协议进行公开。 方法调用终结点通过 HTTPS 协议进行公开。
  
  * *接收设备到云的消息*。 此终结点与 [Azure 事件中心][lnk-event-hubs]兼容。 后端服务可用它来读取由设备发送的[设备到云的消息][lnk-d2c]。 可以在此内置终结点之外的 IoT 中心创建自定义终结点。
  * *发送云到设备的消息和接收送达确认*。 这些终结点可让解决方案后端发送可靠的[云到设备的消息][lnk-c2d]，以及接收对应的传送或过期确认。
  * *接收文件通知*。 此消息传递终结点允许在设备成功上传文件时接收通知。 
  * *直接方法调用*。 此终结点允许后端服务调用设备上的[直接方法][lnk-methods]。
  * *接收操作监视事件*。 此终结点可以用于接收操作监视事件，前提是已将 IoT 中心配置为发出这些事件。 有关详细信息，请参阅 [IoT 中心操作监视][lnk-operations-mon]。

[Azure IoT SDK][lnk-sdks] 一文介绍了访问这些终结点的各种方法。

所有 IoT 中心终结点都使用 [TLS][lnk-tls] 协议，且绝不会在未加密/不安全的通道上公开任何终结点。

## <a name="custom-endpoints"></a>自定义终结点

可将订阅中的现有 Azure 服务链接到充当消息路由终结点的 IoT 中心。 这些终结点充当服务终结点，用作消息路由的接收器。 设备无法直接写入附加终结点。 若要深入了解消息路由，请参阅[通过 IoT 中心发送和接收消息][lnk-devguide-messaging]中的开发人员指南条目。

IoT 中心当前支持将以下 Azure 服务作为附加终结点：

* Azure 存储容器
* 事件中心
* 服务总线队列
* 服务总线主题

IoT 中心需要这些服务终结点的写入权限，以便使用消息路由。 如果通过 Azure 门户配置终结点，则将添加必要权限。 请确保将服务配置为支持预期吞吐量。 在首次配置 IoT 解决方案时，可能需要监视附加终结点，并针对实际负载进行任意的必要调整。

如果消息与多个路由匹配，而这些路由全部指向同一终结点，则 IoT 中心仅向该终结点传递一次消息。 因此不必在服务总线队列或主题中配置重复数据删除。 在分区队列中，分区相关性可保障消息排序。

有关可添加的终结点的数量限制，请参阅[配额和限制][lnk-devguide-quotas]。

### <a name="when-using-azure-storage-containers"></a>如果使用 Azure 存储容器

IoT 中心仅支持将数据以 [Apache Avro](http://avro.apache.org/) 格式作为 blob 写入 Azure 存储容器。 IoT 中心将在其达到特定大小或经过一定的时间之后（以先发生者为准），会对消息进行批处理，并将数据写入 blob。 如果没有要写入的数据，IoT 中心不会写入一个空 blob。

IoT 中心默认为以下文件命名约定：

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

可以使用任何你希望的文件命名约定，但必须使用所有列出的令牌。

### <a name="when-using-service-bus-queues-and-topics"></a>如果使用服务总线队列和主题

用作 IoT 中心终结点的服务总线队列和主题不得启用**会话**或**重复检测**选项。 如果启用了其中任一选项，该终结点会在 Azure 门户中显示为**无法访问**。

## <a name="field-gateways"></a>现场网关

在 IoT 解决方案中，*现场网关*位于设备和 IoT 中心终结点之间。 它通常位于靠近设备的位置。 设备使用设备支持的协议，直接与现场网关通信。 现场网关使用 IoT 中心支持的协议连接到 IoT 中心终结点。 现场网关可能是专用硬件设备或运行自定义网关软件的低功率计算机。

可使用 [Azure IoT Edge][lnk-iot-edge]实现现场网关。 IoT Edge 提供一些功能，例如从多台设备向同一 IoT 中心连接多路复用通信。

## <a name="next-steps"></a>后续步骤

此 IoT 中心开发人员指南中的其他参考主题包括：

* [设备孪生、作业和消息路由的 IoT 中心查询语言][lnk-devguide-query]
* [配额和限制][lnk-devguide-quotas]
* [IoT 中心 MQTT 支持][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md
