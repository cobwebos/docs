---
title: 开发 Azure IoT Edge 模块 | Microsoft Docs
description: 为 Azure IoT Edge 开发可与运行时和 IoT 中心通信的自定义模块
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/25/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 5285490ca1a27494cbcd3ea3d6527b78c7d38c8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833439"
---
# <a name="develop-your-own-iot-edge-modules"></a>开发你自己的 IoT Edge 模块

Azure IoT Edge 模块可以与其他 Azure 服务连接，帮助你实现更大的云数据管道。 本文介绍如何开发模块以与 IoT Edge 运行时和 IoT 中心通信，并进而与 Azure 云的其他服务通信。 

## <a name="iot-edge-runtime-environment"></a>IoT Edge 运行时环境
IoT Edge 运行时提供基础结构可集成多个 IoT Edge 模块的功能并将其部署到 IoT Edge 设备上。 概括而言，任意程序都可打包为 IoT Edge 模块。 但是，若要充分利用 IoT Edge 通信和管理功能，在模块中运行的程序需要连接到本地 IoT Edge 中心，集成在 IoT Edge 运行时中。

## <a name="using-the-iot-edge-hub"></a>使用 IoT Edge 中心
IoT Edge 中心提供两种主要功能：连接到 IoT 中心的代理和本地通信。

### <a name="iot-hub-primitives"></a>IoT 中心基元
IoT 中心在以下意义上将模块实例视为与设备类似：

* 它拥有模块孪生，这与[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)和该设备的其他模块孪生不同且相隔离；
* 它可以发送[设备到云消息](../iot-hub/iot-hub-devguide-messaging.md)；
* 它可以接收专门针对其标识的[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)。

当前，模块不能接收云到设备消息，也不能使用文件上传功能。

编写模块时，可使用 [Azure IoT 设备 SDK](../iot-hub/iot-hub-devguide-sdks.md) 连接到 IoT Edge 中心，使用上述将 IoT 中心用于设备应用程序时的功能，唯一的区别在于，需得从应用程序后端引用模块标识而非设备标识。

### <a name="device-to-cloud-messages"></a>设备到云的消息
若要启用设备到云消息的复杂处理，IoT Edge 中心需要提供模块之间以及模块与 IoT 中心之间消息的声明性路由。 声明性路由允许模块拦截并处理其他模块发送的消息，并将它们传播到复杂管道。 有关详细信息，请参阅[在 IoT Edge 中部署模块和建立路由](module-composition.md)。

与常规 IoT 中心设备应用程序不同，IoT Edge 模块可以接收其本地 IoT Edge 中心所代理的设备到云消息，以便进行处理。

IoT Edge 中心基于[部署清单](module-composition.md)中介绍的声明性路由将消息传播到模块。 开发 IoT Edge 模块时，可以通过设置消息处理程序来接收这些消息。

若要简化路由的创建，IoT Edge 要添加模块*输入*和*输出*终结点的概念。 无需指定任意输入，模块即可接收所有路由给它的设备到云消息，并且无需指定任意输出，模块即可发送设备到云消息。 然而使用显式输入和输出使得路由规则更容易理解。 

最后，Edge 中心处理的设备到云消息标有以下系统属性：

| 属性 | 描述 |
| -------- | ----------- |
| $connectionDeviceId | 发送消息的客户端的设备 ID |
| $connectionModuleId | 发送消息的模块的模块 ID |
| $inputName | 接收此消息的输入。 可以为空。 |
| $outputName | 用来发送消息的输出。 可以为空。 |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>从模块连接到 IoT Edge 中心
从模块连接到本地 IoT Edge 中心涉及两个步骤： 
1. 在应用程序中创建 ModuleClient 实例。
2. 确保应用程序接受该设备上 IoT Edge 中心提供的证书。

创建一个 ModuleClient 实例，将模块连接到设备上运行的 IoT Edge 中心，其方式与 DeviceClient 实例将 IoT 设备连接到 IoT 中心类似。 有关 ModuleClient 类及其通信方法的更多信息，请参阅首选 SDK 语言的 API 参考：[C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)、[C 和 Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable) 或 [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)。


## <a name="next-steps"></a>后续步骤

[为 IoT Edge 准备开发和测试环境](development-environment.md)

[使用 Visual Studio 开发适用于 IoT Edge 的 C# 模块](how-to-visual-studio-develop-module.md)

[使用 Visual Studio Code 开发适用于 IoT Edge 的模块](how-to-vs-code-develop-module.md)

