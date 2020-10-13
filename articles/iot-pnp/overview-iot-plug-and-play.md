---
title: IoT 即插即用简介 | Microsoft Docs
description: 了解 IoT 即插即用。 IoT 即插即用基于开放式建模语言，使智能 IoT 设备能够声明其功能。 IoT 设备连接到云解决方案时会提供称为设备模型的声明。 然后，云解决方案可以自动了解该设备，并开始与其交互，无需编写任何代码。
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 3f74c593cd44470efd231578fddcf53715a3979a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91575089"
---
# <a name="what-is-iot-plug-and-play"></a>什么是 IoT 即插即用？

通过 IoT 即插即用，解决方案构建者无需进行任何手动配置即可将智能设备集成到解决方案中。 IoT 即插即用的核心是设备模型，设备通过设备模型将其功能告知支持 IoT 即插即用的应用程序。 该模型被构造为一组元素，这些元素定义以下内容：

- _属性_：表示设备或其他实体的只读或可写状态。 例如，设备序列号可以是只读属性，而调温器上的目标温度可以是可写属性。
- _遥测_：设备发出的数据，包括常规的传感器读数流、偶发性错误或信息消息。
- _命令_：描述可在设备上执行的功能或操作。 例如，命令可以重新启动网关，或使用远程相机拍照。

你可以在接口中对这些元素进行分组，以在模型之间重复使用，从而使协作更加轻松并加快开发速度。

要使 IoT 即插即用与 [Azure 数字孪生](../digital-twins/about-digital-twins.md)协同工作，你可以使用[数字孪生定义语言 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) 定义模型和接口。 IoT 即插即用和 DTDL 在社区中开放，Microsoft 欢迎与各客户、合作伙伴与行业合作。 两者都基于 JSON-LD 和 RDF 等开放 W3C 标准，因此在不同的服务和工具中更易于采用。

使用 IoT 即插即用和 DTDL 不会产生额外的费用。 [Azure IoT 中心](../iot-hub/about-iot-hub.md)和其他 Azure 服务的标准费率保持不变。

本文概述：

- 与使用 IoT 即插即用的项目关联的典型角色。
- 如何在应用程序中使用 IoT 即插即用设备。
- 如何开发支持 IoT 即插即用的 IoT 设备应用程序。

## <a name="user-roles"></a>用户角色

IoT 即插即用适用于两种类型的开发人员：

- 解决方案构建者负责使用 Azure IoT 和其他 Azure 资源开发 IoT 解决方案，并识别要集成的 IoT 设备。
- 设备构建者创建在已连接到解决方案的设备上运行的代码。

## <a name="use-iot-plug-and-play-devices"></a>使用 IoT 即插即用设备

解决方案构建者可以开发使用 IoT 即插即用设备的云托管式 IoT 解决方案。 [IoT 中心](../iot-hub/about-iot-hub.md) - 一项充当消息中心的托管式云服务，用于在 IoT 应用程序与设备之间进行双向通信。

将 IoT 即插即用设备连接到 IoT 中心时，你可以使用 [Azure IoT 资源管理器](./howto-use-iot-explorer.md)工具查看构成模型的接口中定义的遥测、属性和命令。

如果已将现有传感器附加到 Windows 或 Linux 网关，则可以使用 [IoT 即插即用桥接](./concepts-iot-pnp-bridge.md)连接这些传感器并创建 IoT 即插即用设备，而无需编写设备软件/固件（针对[支持的协议](./concepts-iot-pnp-bridge.md#supported-protocols-and-sensors)）。

## <a name="develop-an-iot-device-application"></a>开发 IoT 设备应用程序

设备构建者可以开发支持 IoT 即插即用的 IoT 硬件产品。 此过程包括三个关键步骤：

1. 定义设备模型。 使用 [DTDL](https://github.com/Azure/opendigitaltwins-dtdl) 创作一组定义设备功能的 JSON 文件。 模型描述完整的实体（例如物理产品），并定义该实体实现的接口集。 接口是唯一标识设备所支持的遥测、属性和命令的共享协定。 可跨不同的模型重复使用接口。

1. 按照遥测、属性和命令遵循 IoT 即插即用约定的方式编写设备软件或固件。 如果要连接已附加到 Windows 或 Linux 网关的现有传感器，[IoT 即插即用桥接](./concepts-iot-pnp-bridge.md)可以简化此步骤。

1. 设备将模型 ID 作为 MQTT 连接的一部分进行公告。 Azure IoT SDK 包括用于在连接时提供模型 ID 的新构造。

> [!Important]
> IoT 即插即用设备必须通过 WebSocket 使用 MQTT 或 MQTT。 其他协议（例如 AMQP 或 HTTP）对于实现 IoT 即插即用设备是无效的。

## <a name="device-certification"></a>设备认证

[IoT 即插即用认证计划](howto-certify-device.md)验证设备是否符合 IoT 即插即用认证要求。 可将已验证的设备添加到公共 [Azure IoT 认证设备目录](https://aka.ms/devicecatalog)。

## <a name="regional-availability"></a>区域可用性

IoT 即插即用刷新在美国中部、欧洲北部和日本东部区域创建的 IoT 中心提供。

## <a name="next-steps"></a>后续步骤

大致了解 IoT 即插即用后，我们建议接下来尝试学习以下快速入门之一：

- [将设备连接到 IoT 中心 (C)](./quickstart-connect-device-c.md)
- [与解决方案中的设备交互 (Node.js)](./quickstart-service-node.md)

