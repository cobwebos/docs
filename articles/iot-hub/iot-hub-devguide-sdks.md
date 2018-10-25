---
title: 了解 Azure IoT SDK | Microsoft Docs
description: 开发人员指南 - 介绍了相关链接，其指向可用于构建设备应用和后端应用的各种 Azure IoT 设备和服务 SDK。
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dobett
ms.openlocfilehash: 1eeb0afdd5ffcbe00357914d6a98c8d0b3d452ec
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017953"
---
# <a name="understand-and-use-azure-iot-hub-sdks"></a>了解和使用 Azure IoT 中心 SDK

有三种类别的软件开发工具包 (SDK) 适用于 IoT 中心：

* **设备 SDK**：可便于生成在使用设备客户端或模块客户端的 IoT 设备上运行的应用。 这些应用将遥测发送到 IoT 中心，并可以选择从 IoT 中心接收消息、作业、方法或孪生更新。  还可以使用模块客户端，为 [Azure IoT Edge 运行时](../iot-edge/about-iot-edge.md)创建[模块](../iot-edge/iot-edge-modules.md)。

* **服务 SDK**：可便于管理 IoT 中心，并视需要发送消息、计划作业、调用直接方法或向 IoT 设备或模块发送相应属性更新。

* **设备预配 SDK**：可便于使用[设备预配服务](../iot-dps/about-iot-dps.md)将设备预配到 IoT 中心。

了解[使用 Azure IoT SDK 执行开发的益处](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="azure-iot-device-sdks"></a>Azure IoT 设备 SDK

Microsoft Azure IoT 设备 SDK 包含的代码可帮助构建连接到 Azure IoT 中心服务并由这些服务管理的设备和应用程序。

适用于 .NET 的 Azure IoT 中心设备 SDK： 

* 通过 [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) 安装
* [源代码](https://github.com/Azure/azure-iot-sdk-csharp)
* [API 参考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet)
* [模块参考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)

适用于 C 的 Azure IoT 中心设备 SDK，采用 ANSI C (C99) 编写，具有可移植性和广泛的平台兼容性：

* 通过 [apt-get、MBED、Arduino IDE 或 Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md) 安装
* [源代码](https://github.com/Azure/azure-iot-sdk-c)
* [API 参考](https://azure.github.io/azure-iot-sdk-c/index.html)
* [模块参考](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_module_client.h)

适用于 Java 的 Azure IoT 中心设备 SDK： 

* 添加到 [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk) 项目
* [源代码](https://github.com/Azure/azure-iot-sdk-java)
* [API 参考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device)
* [模块参考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._module_client?view=azure-java-stable)

适用于 Node.js 的 Azure IoT 中心设备 SDK： 

* 通过 [npm](https://www.npmjs.com/package/azure-iot-device) 安装
* [源代码](https://github.com/Azure/azure-iot-sdk-node)
* [API 参考](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-iot-typescript-latest)
* [模块参考](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)

适用于 Python 的 Azure IoT 中心设备 SDK： 

* 通过 [pip](https://pypi.python.org/pypi/azure-iothub-device-client/) 安装
* [源代码](https://github.com/Azure/azure-iot-sdk-python)
* API 参考：请参阅 [C API 参考](https://azure.github.io/azure-iot-sdk-c/index.html)

适用于 iOS 的 Azure IoT 中心设备 SDK： 

* 通过 [CocoaPod](https://cocoapods.org/pods/AzureIoTHubClient) 安装
* [示例](https://github.com/Azure-Samples/azure-iot-samples-ios)
* API 参考：请参阅 [C API 参考](https://azure.github.io/azure-iot-sdk-c/index.html)

> [!NOTE]
> 有关使用语言和平台特定的程序包管理器在开发计算机上安装二进制文件和依赖项的信息，请参阅 GitHub 存储库中的自述文件。
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>操作系统平台和硬件兼容性

有关受支持的 SDK 平台，可访问 [Azure IoT SDK 平台支持](iot-hub-device-sdk-platform-support.md)。

若要详细了解 SDK 与特定硬件设备的兼容性，请参阅 [Azure IoT 认证设备目录](https://catalog.azureiotsuite.com/)或各个存储库。

## <a name="azure-iot-service-sdks"></a>Azure IoT 服务 SDK

Azure IoT 服务 SDK 包含的代码可帮助生成直接与 IoT 中心进行交互以管理设备和安全性的应用程序。

适用于 .NET 的 Azure IoT 中心服务 SDK：

* 通过 [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Devices/) 下载
* [源代码](https://github.com/Azure/azure-iot-sdk-csharp)
* [API 参考](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices)

适用于 Java 的 Azure IoT 中心服务 SDK： 

* 添加到 [Maven](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk
) 项目
* [源代码](https://github.com/Azure/azure-iot-sdk-java)
* [API 参考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service)

适用于 Node.js 的 Azure IoT 中心服务 SDK： 

* 通过 [npm](https://www.npmjs.com/package/azure-iothub) 下载
* [源代码](https://github.com/Azure/azure-iot-sdk-node)
* [API 参考](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-iot-typescript-latest)

适用于 Python 的 Azure IoT 中心服务 SDK： 

* 通过 [pip](https://pypi.python.org/pypi/azure-iothub-service-client/) 下载
* [源代码](https://github.com/Azure/azure-iot-sdk-python)

适用于 C 的 Azure IoT 中心服务 SDK： 

* 通过 [apt-get、MBED、Arduino IDE 或 Nuget](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md) 下载
* [源代码](https://github.com/Azure/azure-iot-sdk-c)

适用于 iOS 的 Azure IoT 中心服务 SDK： 

* 通过 [CocoaPod](https://cocoapods.org/pods/AzureIoTHubServiceClient) 安装
* [示例](https://github.com/Azure-Samples/azure-iot-samples-ios)

> [!NOTE]
> 有关使用语言和平台特定的程序包管理器在开发计算机上安装二进制文件和依赖项的信息，请参阅 GitHub 存储库中的自述文件。

## <a name="device-provisioning-sdks"></a>设备预配 SDK

借助 **Microsoft Azure 预配 SDK**，可使用[设备预配服务](../iot-dps/about-iot-dps.md)将设备预配到 IoT 中心。

适用于 C# 的 Azure 预配设备和服务 SDK：

* [预配设备客户端 SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/device)
* [预配服务客户端 SDK](https://github.com/Azure/azure-iot-sdk-csharp/blob/master/provisioning/service)

适用于 Java 的 Azure 预配设备和服务 SDK：

* [预配设备客户端 SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-device-client)
* [预配服务客户端 SDK](https://github.com/Azure/azure-iot-sdk-java/blob/master/provisioning/provisioning-service-client)

适用于 Node.js 的 Azure 预配设备和服务 SDK：

* [预配设备客户端 SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device)
* [预配服务客户端 SDK](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)

适用于 Python 的 Azure 预配设备和服务 SDK：

* [预配设备客户端 SDK](https://github.com/Azure/azure-iot-sdk-python/blob/master/provisioning_device_client)
* [预配服务客户端 SDK](https://github.com/Azure/azure-iot-sdk-python/tree/master/provisioning_service_client)

适用于 C 的 Azure 预配设备和服务 SDK：

* [预配设备客户端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client)
* [预配服务客户端 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_service_client)

## <a name="next-steps"></a>后续步骤

Azure IoT SDK 还提供了一组工具来帮助开发：
* [iothub-diagnostics](https://github.com/Azure/iothub-diagnostics)：一个跨平台的命令行工具，用于帮助诊断与 IoT Hub 连接相关的问题。
* [设备资源管理器](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)：用于连接到 IoT 中心的 Windows 桌面应用程序。

此 IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点](iot-hub-devguide-endpoints.md)
* [用于设备孪生、作业和消息路由的 IoT 中心查询语言](iot-hub-devguide-query-language.md)
* [配额和限制](iot-hub-devguide-quotas-throttling.md)
* [IoT 中心 MQTT 支持](iot-hub-mqtt-support.md)
* [IoT 中心 REST API 参考](/rest/api/iothub/)
* [Azure IoT SDK 平台支持](iot-hub-device-sdk-platform-support.md)