---
title: 开发 Azure IoT Edge 模块 | Microsoft Docs
description: 了解如何创建 Azure IoT Edge 的自定义模块
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 10/05/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: dbbd07e93602855afb0c9755e8872e0b46557611
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030013"
---
# <a name="understand-the-requirements-and-tools-for-developing-iot-edge-modules"></a>了解开发 IoT Edge 模块的要求和工具

本文介绍编写作为 IoT Edge 模块运行的应用程序时可用的功能，以及如何充分利用它们。

## <a name="iot-edge-runtime-environment"></a>IoT Edge 运行时环境
IoT Edge 运行时提供基础结构可集成多个 IoT Edge 模块的功能并将其部署到 IoT Edge 设备上。 概括而言，任意程序都可打包为 IoT Edge 模块。 但是，若要充分利用 IoT Edge 通信和管理功能，在模块中运行的程序需要连接到本地 IoT Edge 中心，集成在 IoT Edge 运行时中。

## <a name="using-the-iot-edge-hub"></a>使用 IoT Edge 中心
IoT Edge 中心提供两种主要功能：连接到 IoT 中心的代理和本地通信。

### <a name="iot-hub-primitives"></a>IoT 中心基元
IoT 中心将看到类似设备的模块实例，从某种意义上：

* 它拥有模块孪生，这与[设备孪生][lnk-devicetwin]和该设备的其他模块孪生不同且与相隔离；
* 它可以发送[设备到云消息][lnk-iothub-messaging]；
* 它可以接收专门针对其标识的[直接方法][lnk-methods]。

当前，模块不能接收云到设备消息，也不能使用文件上传功能。

编写模块时，使用 [Azure IoT 设备 SDK][lnk-devicesdk] 即可连接到 IoT Edge 中心，使用上述将 IoT 中心用于设备应用程序时的功能，唯一的区别在于，需得从应用程序后端引用模块标识而非设备标识。

若要了解发送设备到云消息和使用模块孪生的模块应用程序的示例，请参阅[开发 IoT Edge 模块并将其部署到模拟设备][lnk-tutorial2]。

### <a name="device-to-cloud-messages"></a>设备到云的消息
若要启用设备到云消息的复杂处理，IoT Edge 中心需要提供模块之间和模块与 IoT 中心之间消息的声明性路由。
这允许模块拦截并处理其他模块发送的消息，并将它们传播到复杂管道。
[模块组合][lnk-module-comp]一文介绍如何使用路由将模块组合到复杂管道。

不同于常规 IoT 中心设备应用程序，IoT Edge 模块可以接收其本地 IoT Edge 中心所代理的设备到云消息，以便进行处理。

IoT Edge 中心基于[模块组合][lnk-module-comp]一文中介绍的声明性路由将消息传播到模块。 开发 IoT Edge 模块时，可以通过设置消息处理程序来接收这些消息，如教程[开发 IoT Edge 模块并将其部署到模拟设备][lnk-tutorial2]中所示。

若要简化路由的创建，IoT Edge 要添加模块输入和输出终结点的概念。 无需指定任意输入，模块即可接收所有路由给它的设备到云消息，并且无需指定任意输出，模块即可发送设备到云消息。
然而使用显式输入和输出使得路由规则更容易理解。 若要详细了解路由规则和模块输入和输出终结点，请参阅[模块组合][lnk-module-comp]。

最后，Edge 中心处理的设备到云消息标有以下系统属性：

| 属性 | 说明 |
| -------- | ----------- |
| $connectionDeviceId | 发送消息的客户端的设备 ID |
| $connectionModuleId | 发送消息的模块的模块 ID |
| $inputName | 接收此消息的输入。 可以为空。 |
| $outputName | 用来发送消息的输出。 可以为空。 |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>从模块连接到 IoT Edge 中心
从模块连接到本地 IoT Edge 中心涉及两个步骤：模块启动时，使用 IoT Edge 运行时提供的连接字符串，并确保应用程序接受该设备 IoT Edge 中心提供的证书。

要使用的连接字符串由 IoT Edge 运行时注入环境变量 `EdgeHubConnectionString`。 这使得它对任意需要使用它的程序可用。

类似地，用以验证 IoT Edge 中心连接的证书由 IoT Edge 运行时注入其路径提供在环境变量 `EdgeModuleCACertificateFile` 中的文件。

[开发 IoT Edge 模块并将其部署到模拟设备][lnk-tutorial2]教程介绍如何确保证书位于模块应用程序的计算机存储中。 显然，任何其他信任使用该证书的连接的方法均有效。

## <a name="packaging-as-an-image"></a>打包为映像
IoT Edge 模块打包为 Docker 映像。
可以直接使用 Docker 工具链，或者[开发 IoT Edge 模块并将其部署到模拟设备][lnk-tutorial2] 教程中的 Visual Studio Code。

## <a name="next-steps"></a>后续步骤

开发模块后，了解如何[大规模部署和监视 IoT Edge 模块][lnk-howto-deploy]。

[lnk-devicesdk]: ../iot-hub/iot-hub-devguide-sdks.md
[lnk-devicetwin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-iothub-messaging]: ../iot-hub/iot-hub-devguide-messaging.md
[lnk-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-tutorial2]: tutorial-csharp-module.md
[lnk-module-comp]: module-composition.md
[lnk-howto-deploy]: how-to-deploy-monitor.md
