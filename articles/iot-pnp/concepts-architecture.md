---
title: IoT 即插即用体系结构 |Microsoft Docs
description: 作为解决方案生成器，了解 IoT 即插即用的关键体系结构元素。
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f656de0bb2e5244e137ae21a6d7af88f3430b12c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475679"
---
# <a name="iot-plug-and-play-preview-architecture"></a>IoT 即插即用预览版体系结构

IoT 即插即用预览版使解决方案生成器能够将智能设备与其解决方案集成，无需任何手动配置。 IoT 即插即用的核心是一种设备_型号_，用于描述设备对于启用 IoT 即插即用的应用程序的功能。 此模型的结构化为一组定义的接口：

- 表示设备或其他实体的只读或可写状态的_属性_。 例如，设备序列号可以是只读属性，恒温器上的目标温度可能是可写的属性。
- _遥测_数据是指设备发出的数据，数据是传感器读数的常规流、偶尔出现的错误或信息消息。
- _命令_：描述可在设备上执行的功能或操作。 例如，命令可以重新启动网关，或使用远程相机拍照。

每个模型和接口都具有唯一的 ID。

下图显示 IoT 即插即用解决方案的关键元素：

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="IoT 即插即用体系结构":::

## <a name="model-repository"></a>模型存储库

[模型存储库](./concepts-model-repository.md)是模型和接口定义的存储区。 使用[数字孪生定义语言（DTDL）](https://github.com/Azure/opendigitaltwins-dtdl)定义模型和接口。

利用 web UI，你可以管理模型和接口。

模型存储库使用 RBAC 来限制对接口定义的访问。

## <a name="devices"></a>设备

设备生成器使用[Azure IoT 设备 sdk](./libraries-sdks.md)之一来实现在 IoT 智能设备上运行的代码。 设备 Sdk 可帮助设备生成器执行以下操作：

- 安全地连接到 IoT 中心。
- 向 IoT 中心注册设备，并公告标识设备实现的接口集合的模型 ID。
- 更新设备实现的 DTDL 接口中定义的属性。 这些属性是使用数字孪生实现的，这些属性用于管理与 IoT 中心的同步。
- 添加设备实现的 DTDL 接口中定义的命令的命令处理程序。
- 将遥测发送到 IoT 中心。

## <a name="iot-hub"></a>IoT 中心

[Iot 中心](../iot-hub/about-iot-hub.md)是一项云托管的服务，可充当 IoT 解决方案与它所管理的设备之间的双向通信。

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
- [为 IoT 即插即用进行开发](./concepts-developer-guide.md)
