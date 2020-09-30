---
title: IoT 即插即用体系结构 |Microsoft Docs
description: 作为解决方案生成器，了解 IoT 即插即用的关键体系结构元素。
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 32e67bd7f30fecee3449935a35235844a047957b
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574309"
---
# <a name="iot-plug-and-play-architecture"></a>IoT 即插即用体系结构

IoT 即插即用使解决方案构建者能够将智能设备与其解决方案集成，无需任何手动配置。 IoT 即插即用的核心是一种设备 _型号_ ，用于描述设备对于启用 IoT 即插即用的应用程序的功能。 此模型的结构化为一组定义的接口：

- _属性_：表示设备或其他实体的只读或可写状态。 例如，设备序列号可以是只读属性，而调温器上的目标温度可以是可写属性。
- _遥测_：设备发出的数据，包括常规的传感器读数流、偶发性错误或信息消息。
- _命令_：描述可在设备上执行的功能或操作。 例如，命令可以重新启动网关，或使用远程相机拍照。

每个模型和接口都具有唯一的 ID。

下图显示 IoT 即插即用解决方案的关键元素：

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT 即插即用体系结构":::

## <a name="model-repository"></a>模型存储库

[模型存储库](./concepts-model-repository.md)是模型和接口定义的存储区。 使用 [数字孪生定义语言 (DTDL) ](https://github.com/Azure/opendigitaltwins-dtdl)定义模型和接口。

利用 web UI，你可以管理模型和接口。

模型存储库使用 RBAC 来限制对接口定义的访问。

## <a name="devices"></a>设备

设备生成器使用 [Azure IoT 设备 sdk](./libraries-sdks.md)之一来实现在 IoT 智能设备上运行的代码。 设备 Sdk 可帮助设备生成器执行以下操作：

- 安全地连接到 IoT 中心。
- 向 IoT 中心注册设备，并公告标识设备实现的 DTDL 接口集合的模型 ID。
- 同步在设备与 IoT 中心之间的 DTDL 接口中定义的属性。
- 添加 DTDL 接口中定义的命令的命令处理程序。
- 将遥测发送到 IoT 中心。

## <a name="iot-edge-gateway"></a>IoT Edge 网关

IoT Edge 网关充当中介，用于连接无法直接连接到 IoT 中心的 IoT 即插即用设备。 若要了解详细信息，请参阅 [如何将 IoT Edge 设备用作网关](../iot-edge/iot-edge-as-gateway.md)。

## <a name="iot-edge-modules"></a>IoT Edge 模块

利用 _IoT Edge 模块_ ，你可以部署和管理边缘上的业务逻辑。 Azure IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Azure 服务（如 Azure 流分析）或你自己特定于解决方案的代码。

_IoT Edge 中心_是组成 Azure IoT Edge 运行时的模块之一。 它通过公开与 IoT 中心相同的协议终结点，充当 IoT 中心的本地代理。 这种一致性意味着客户端（无论是设备还是模块）可以连接到 IoT Edge 运行时，就像连接到 IoT 中心一样。

设备 Sdk 有助于模块生成器：

- 使用 IoT Edge 集线器安全地连接到 IoT 中心。
- 向 IoT 中心注册模块，并公告标识设备实现的 DTDL 接口集合的模型 ID。
- 同步在设备与 IoT 中心之间的 DTDL 接口中定义的属性。
- 添加 DTDL 接口中定义的命令的命令处理程序。
- 将遥测发送到 IoT 中心。

## <a name="iot-hub"></a>IoT 中心

[Iot 中心](../iot-hub/about-iot-hub.md) 是一项云托管的服务，可充当 IoT 解决方案与它所管理的设备之间的双向通信。

IoT 中心：

- 使设备实现的模型 ID 可供后端解决方案使用。
- 维护与连接到集线器的每个即插即用设备关联的数字输出。
- 将遥测流转发到其他服务进行处理或存储。
- 将数字克隆更改事件路由到其他服务以启用设备监视。

## <a name="backend-solution"></a>后端解决方案

后端解决方案通过与 IoT 中心中的数字孪生交互来监视和控制连接的设备。 使用服务 Sdk 之一来实现后端解决方案。 为了了解连接的设备的功能，解决方案后端：

1. 检索在 IoT 中心注册的设备的模型 ID。
1. 使用模型 ID 从任何模型存储库检索接口定义。
1. 使用模型分析器从接口定义中提取信息。

后端解决方案可以使用接口定义中的信息来执行以下操作：

- 读取设备报告的属性值。
- 更新设备上的可写属性。
- 调用设备实现的命令。
- 了解设备发送的遥测格式。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解 IoT 即插即用解决方案的体系结构，接下来的步骤是详细了解以下内容：

- [模型存储库](./concepts-model-repository.md)
- [数字克隆模型集成](./concepts-model-discovery.md)
- [为 IoT 即插即用进行开发](./concepts-developer-guide-device-csharp.md)
