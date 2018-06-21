---
title: 了解 Azure IoT SDK | Microsoft Docs
description: 开发人员指南 - 介绍了相关链接，其指向可用于构建设备应用和后端应用的各种 Azure IoT 设备和服务 SDK。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: dobett
ms.openlocfilehash: 718bf82a921ec3b70939b7a70205a4c650702dd0
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823344"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>了解和使用 Azure IoT 中心 SDK

有两种类别的软件开发工具包 (SDK) 适用于 IoT 中心：

* **设备 SDK** 用于构建可在 IoT 设备上运行的应用。 这些应用将遥测发送到 IoT 中心，并可以选择从 IoT 中心接收消息、作业、方法或孪生更新。

* **服务 SDK** 用于管理 IoT 中心，并且还可以选择用来发送消息、计划作业、调用直接方法，或者向 IoT 设备发送所需的属性更新。

可以从[此处][lnk-benefits-blog]了解使用 Azure IoT SDK 进行开发的好处。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT 设备 SDK

Microsoft Azure IoT 设备 SDK 包含的代码可帮助构建连接到 Azure IoT 中心服务并由这些服务管理的设备和应用程序。

适用于 .NET 的 Azure IoT 中心设备 SDK： 
* 通过 [Nuget][lnk-nuget-csharp-device] 安装
* [源代码][lnk-dotnet-sdk]
* [API 参考][lnk-dotnet-ref]

适用于 C 的 Azure IoT 中心设备 SDK：采用 ANSI C (C99) 编写，具有可移植性和广泛的平台兼容性
* 通过 [apt-get、MBED、Arduino IDE 或 Nuget][lnk-c-package] 安装
* [源代码][lnk-c-sdk]
* [API 参考][lnk-c-ref]

适用于 Java 的 Azure IoT 中心设备 SDK： 
* 添加到 [Maven][lnk-maven-device] 项目
* [源代码][lnk-java-sdk]
* [API 参考][lnk-java-ref]

适用于 Node.js 的 Azure IoT 中心设备 SDK： 
* 通过 [npm][lnk-npm-device] 安装
* [源代码][lnk-node-sdk]
* [API 参考][lnk-node-ref]

适用于 Python 的 Azure IoT 中心设备 SDK： 
* 通过 [pip][lnk-pip-device] 安装
* [源代码][lnk-python-sdk]

适用于 iOS 的 Azure IoT 中心设备 SDK： 
* 从 [CocoaPod][lnk-cocoa-device] 安装
* [示例][lnk-ios-sample]

> [!NOTE]
> 有关使用语言和平台特定的程序包管理器在开发计算机上安装二进制文件和依赖项的信息，请参阅 GitHub 存储库中的自述文件。
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>操作系统平台和硬件兼容性

有关与特定硬件设备的 SDK 兼容性的详细信息，请参阅 [Azure IoT 认证设备目录][lnk-certified]或个体存储库。

## <a name="azure-iot-service-sdks"></a>Azure IoT 服务 SDK

Azure IoT 服务 SDK 包含的代码可帮助生成直接与 IoT 中心进行交互以管理设备和安全性的应用程序。

适用于 .NET 的 Azure IoT 中心服务 SDK：
* 通过 [Nuget][lnk-nuget-csharp-service] 下载
* [源代码][lnk-dotnet-sdk]
* [API 参考][lnk-dotnet-service-ref]

适用于 Java 的 Azure IoT 中心服务 SDK： 
* 添加到 [Maven][lnk-maven-service] 项目
* [源代码][lnk-java-sdk]
* [API 参考][lnk-java-service-ref]

适用于 Node.js 的 Azure IoT 中心服务 SDK： 
* 通过 [npm][lnk-npm-service] 下载
* [源代码][lnk-node-sdk]
* [API 参考][lnk-node-service-ref]

适用于 Python 的 Azure IoT 中心服务 SDK： 
* 通过 [pip][lnk-pip-service] 下载
* [源代码][lnk-python-sdk]

适用于 C 的 Azure IoT 中心服务 SDK： 
* 通过 [apt-get、MBED、Arduino IDE 或 Nuget][lnk-c-package] 下载
* [源代码][lnk-c-sdk]

适用于 iOS 的 Azure IoT 中心服务 SDK： 
* 从 [CocoaPod][lnk-cocoa-service] 安装
* [示例][lnk-ios-sample]

> [!NOTE]
> 有关使用语言和平台特定的程序包管理器在开发计算机上安装二进制文件和依赖项的信息，请参阅 GitHub 存储库中的自述文件。


## <a name="next-steps"></a>后续步骤

此 IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点][lnk-devguide-endpoints]
* [设备孪生、作业和消息路由的 IoT 中心查询语言][lnk-devguide-query]
* [配额和限制][lnk-devguide-quotas]
* [IoT 中心 MQTT 支持][lnk-devguide-mqtt]
* [IoT 中心 REST API 参考][lnk-rest-ref]

<!-- Links and images -->

[lnk-c-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-dotnet-sdk]: https://github.com/Azure/azure-iot-sdk-csharp
[lnk-java-sdk]: https://github.com/Azure/azure-iot-sdk-java
[lnk-node-sdk]: https://github.com/Azure/azure-iot-sdk-node
[lnk-python-sdk]: https://github.com/Azure/azure-iot-sdk-python
[lnk-certified]: https://catalog.azureiotsuite.com/

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet
[lnk-dotnet-service-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service
[lnk-node-service-ref]: https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest

[lnk-maven-device]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk
[lnk-maven-service]: https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
[lnk-npm-device]: https://www.npmjs.com/package/azure-iot-device
[lnk-npm-service]: https://www.npmjs.com/package/azure-iothub
[lnk-nuget-csharp-device]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-csharp-service]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-c-package]: https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md
[lnk-pip-device]: https://pypi.python.org/pypi/azure-iothub-device-client/
[lnk-pip-service]: https://pypi.python.org/pypi/azure-iothub-service-client/


[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-benefits-blog]: https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/
[lnk-cocoa-device]: https://cocoapods.org/pods/AzureIoTHubClient
[lnk-ios-sample]: https://github.com/Azure-Samples/azure-iot-samples-ios
[lnk-cocoa-service]: https://cocoapods.org/pods/AzureIoTHubServiceClient