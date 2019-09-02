---
title: IoT 即插即用预览版简介 | Microsoft Docs
description: 了解 IoT 即插即用预览版。 IoT 即插即用基于开放式建模语言，使 IoT 设备能够声明其功能。 IoT 设备在连接到 Azure IoT Central 等云解决方案或合作伙伴应用程序时，会出示该声明（称为“设备功能模型”）。 然后，云解决方案可以自动了解该设备，并开始与其交互 - 无需编写任何代码。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 06/21/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 56a73449c69010bc97122023f7dcbc9e3a9df154
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70048006"
---
# <a name="what-is-iot-plug-and-play-preview"></a>什么是 IoT 即插即用预览版？

IoT 即插即用预览版使解决方案开发人员能够将设备与其解决方案相集成，而无需编写任何嵌入式代码。 IoT 即插即用的核心是描述设备功能的设备功能模型架构。  此架构是一个 JSON 文档，结构化为一组包含以下对象的定义的接口：

- 属性：表示设备或其他实体的只读和读/写状态。  例如，设备序列号可以是只读属性，而恒温器上的目标温度可以是读/写属性。
- _遥测_：设备发出的数据，包括常规的传感器读数流、偶发性错误或信息消息。
- _命令_：描述可在设备上执行的功能或操作。 例如，命令可以重新启动网关，或使用远程相机拍照。

可在不同的设备功能模型中重复使用接口，以便更轻松地进行协作和加速开发。

为了使 IoT 即插即用与 [Azure 数字孪生](../digital-twins/about-digital-twins.md)无缝协同工作，已使用[数字孪生定义语言 (DTDL)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) 定义了 IoT 即插即用架构。 IoT 即插即用和 DTDL 在社区中开放，Microsoft 欢迎与各客户、合作伙伴与行业合作。 两者都基于 JSON-LD 和 RDF 等开放 W3C 标准，因此在不同的服务和工具中更易于采用。 此外，使用 IoT 即插即用和 DTDL 不会产生额外的费用。 [Azure IoT 中心](../iot-hub/about-iot-hub.md)、[Azure IoT Central](../iot-central/overview-iot-central.md) 和其他 Azure 服务的标准费率保持不变。

基于 IoT 中心或 IoT Central 构建的解决方案可从 IoT 即插即用受益。

本文概述：

- 与使用 IoT 即插即用的项目关联的典型角色。
- 如何在应用程序中使用 IoT 即插即用设备。
- 如何开发支持 IoT 即插即用的 IoT 设备应用程序。
- 如何认证 IoT 即插即用设备并发布到 [IoT 认证设备目录](https://catalog.azureiotsolutions.com/)。

## <a name="user-roles"></a>用户角色

IoT 即插即用适用于两种类型的开发人员：

- 解决方案开发人员负责使用 Azure IoT 和其他 Azure 资源开发 IoT 解决方案，并识别要集成的 IoT 设备。 
- 设备开发人员创建在已连接到解决方案的设备上运行的代码。 

## <a name="use-iot-plug-and-play-devices"></a>使用 IoT 即插即用设备

解决方案开发人员可以开发使用 IoT 即插即用设备的云托管式 IoT 解决方案。 可以使用以下 Azure 服务之一：

- [IoT Central](../iot-central/overview-iot-central.md) - 一个完全托管的 IoT 软件即服务解决方案，可用来轻松创建对物理和电子世界进行连接的产品。
- [IoT 中心](../iot-hub/about-iot-hub.md) - 一个充当消息中心的托管式云服务，用于在 IoT 应用程序与设备之间进行双向通信。

可以通过 Azure IoT 认证设备目录查找 IoT 即插即用设备。 该目录中的每个 IoT 即插即用设备已经过验证，具有设备功能模型。 查看设备功能模型可以了解该设备的功能，或者使用它来模拟 Azure IoT Central 中的设备。

连接 IoT 即插即用设备时，可以查看其设备功能模型、该模型中包含的接口，以及在这些接口中定义的遥测、属性和命令。

## <a name="develop-an-iot-device-application"></a>开发 IoT 设备应用程序

设备开发人员可以开发支持 IoT 即插即用的 IoT 硬件产品。 该过程涉及到两个关键步骤：

1. 定义设备功能模型和接口。 创作一组 JSON 文件，以使用 [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) 声明设备的功能。 设备功能模型描述完整的实体（例如物理产品），并定义该实体实现的接口集。 接口是唯一标识设备所支持的遥测、属性和命令的共享协定。 可跨不同的设备功能模型重复使用接口。

1. 创作设备软件或固件，用于实现设备功能模型和接口中声明的功能。 Azure IoT SDK 包含用于实现设备功能模型的 API。

[IoT Device Workbench for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) 扩展提供许多辅助功能。 例如，设备开发人员可以使用该扩展基于功能模型生成主干 C 项目。 但是，也可以使用任何 IDE 来创作和实现设备功能模型。

## <a name="certify-an-iot-plug-and-play-device"></a>认证 IoT 即插即用设备

设备开发人员可以提交 IoT 硬件产品进行认证。 可以在 IoT 认证设备目录中发布已认证的设备。 认证过程的步骤包括：

- 加入 [Microsoft 合作伙伴网络](https://partner.microsoft.com)。
- 在 Azure IoT 认证门户中登记。
- 提交 IoT 即插即用设备功能模型和营销信息以创建新的设备记录。
- 通过设备的自动化验证测试项目。
- 发布到 IoT 认证设备目录。

## <a name="regional-availability"></a>区域可用性
在公共预览期，IoT 即插即用可在北欧、美国中部和日本东部区域使用。 请确保在这其中的一个区域创建中心。

## <a name="message-quotas-in-iot-hub"></a>IoT 中心的消息配额
在公共预览期，IoT 即插即用设备按接口发送单独的消息，这可能会增加[消息配额](../iot-hub/iot-hub-devguide-quotas-throttling.md)涵盖的消息计数。

## <a name="next-steps"></a>后续步骤

大致了解 IoT 即插即用后，我们建议接下来尝试学习以下快速入门之一：

- [使用设备功能模型创建 IoT 即插即用设备](./quickstart-create-pnp-device.md)
- [将设备连接到 IoT 中心](./quickstart-connect-pnp-device.md)
- [连接到解决方案中的设备](./quickstart-connect-pnp-device-solution.md)
