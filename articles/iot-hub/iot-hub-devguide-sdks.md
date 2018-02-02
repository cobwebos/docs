---
title: "了解 Azure IoT SDK | Microsoft Docs"
description: "开发人员指南 - 介绍了相关链接，其指向可用于构建设备应用和后端应用的各种 Azure IoT 设备和服务 SDK。"
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: c5c9a497-bb03-4301-be2d-00edfb7d308f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 96dad8917f4483c3d7c74ea0462b96d89286f549
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/01/2018
---
# <a name="understand-and-use-azure-iot-sdks"></a>了解和使用 Azure IoT SDK

有三种类别的软件开发工具包 (SDK) 适用于 IoT 中心：

* **设备 SDK** 用于构建可在 IoT 设备上运行的应用。 这些应用将遥测发送到 IoT 中心，并可以选择从 IoT 中心接收消息。

* **服务 SDK** 用于管理 IoT 中心，并可以选择向 IoT 设备发送消息。

* **Azure IoT Edge** 用于为不使用支持的协议之一的设备构建网关。 网关还可以处理边缘上的消息。

提供 SDK 以支持多种编程语言。

## <a name="azure-iot-device-sdks"></a>Azure IoT 设备 SDK

Microsoft Azure IoT 设备 SDK 包含的代码可帮助构建连接到 Azure IoT 中心服务并由这些服务管理的设备和应用程序。

以下 Azure IoT 设备 SDK 可以从 GitHub 进行下载：

* [适用于 .NET 的 Azure IoT 设备 SDK][lnk-dotnet-device-sdk]
* [适用于 Java 的 Azure IoT 设备 SDK][lnk-java-device-sdk]
* [适用于 Node.js 的 Azure IoT 设备 SDK][lnk-node-device-sdk]
* [适用于 Python 的 Azure IoT 设备 SDK][lnk-python-device-sdk]
* [适用于 C 的 Azure IoT 设备 SDK][lnk-c-device-sdk]，采用 ANSI C (C99) 编写，具有可移植性和广泛的平台兼容性。 有两个适用于 C 的设备客户端库，即低级别 iothub_client 和序列化程序。

> [!NOTE]
> 有关使用语言和平台特定的程序包管理器在开发计算机上安装二进制文件和依赖项的信息，请参阅 GitHub 存储库中的自述文件。
> 
> 

### <a name="os-platform-and-hardware-compatibility"></a>操作系统平台和硬件兼容性

有关与特定硬件设备的 SDK 兼容性的详细信息，请参阅 [Azure IoT 认证设备目录][lnk-certified]。

## <a name="azure-iot-service-sdks"></a>Azure IoT 服务 SDK

Azure IoT 服务 SDK 包含的代码可帮助生成直接与 IoT 中心进行交互以管理设备和安全性的应用程序。

以下 Azure IoT 服务 SDK 可以从 GitHub 进行下载：

* [适用于 .NET 的 Azure IoT 服务 SDK][lnk-dotnet-service-sdk]
* [适用于 Java 的 Azure IoT 服务 SDK][lnk-java-service-sdk]
* [适用于 Node.js 的 Azure IoT 服务 SDK][lnk-node-service-sdk]
* [适用于 Python 的Azure IoT 服务 SDK][lnk-python-service-sdk]
* [适用于 C 的 Azure IoT 服务 SDK][lnk-c-service-sdk]

> [!NOTE]
> 有关使用语言和平台特定的程序包管理器在开发计算机上安装二进制文件和依赖项的信息，请参阅 GitHub 存储库中的自述文件。

## <a name="azure-iot-edge"></a>Azure IoT Edge

Azure IoT Edge 包含用于创建 IoT 网关解决方案的基础结构和模块。 可以扩展 IoT Edge，创建适用于任何端到端方案的网关。

可以从 GitHub 下载 [Azure IoT Edge][lnk-iot-edge]。

## <a name="online-api-reference-documentation"></a>联机 API 参考文档

以下列表包含 Azure IoT 设备、服务和网关库的联机 API 参考文档链接：

* [物联网 (IoT) .NET][lnk-dotnet-ref]
* [适用于 Java 的 Azure IoT 设备 SDK][lnk-java-ref]
* [适用于 Java 的 Azure IoT 服务 SDK][lnk-java-service-ref]
* [适用于 Node.js 的 Azure IoT 设备 SDK][lnk-node-ref]
* [适用于 Node.js 的 Azure IoT 服务 SDK][lnk-node-service-ref]
* [适用于 C 的 Azure IoT 设备 SDK][lnk-c-ref]
* [IoT 中心 REST][lnk-rest-ref]
* [Azure IoT Edge][lnk-gateway-ref]

## <a name="next-steps"></a>后续步骤

此 IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点][lnk-devguide-endpoints]
* [设备孪生、作业和消息路由的 IoT 中心查询语言][lnk-devguide-query]
* [配额和限制][lnk-devguide-quotas]
* [IoT 中心 MQTT 支持][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdk-c
[lnk-c-service-sdk]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_service_client
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/device
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/device
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdk-csharp/tree/master/service
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdk-java/tree/master/service
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/device
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdk-node/tree/master/service
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/device
[lnk-python-service-sdk]: https://github.com/Azure/azure-iot-sdk-python/tree/master/service
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-iot-edge]: https://github.com/Azure/iot-edge

[lnk-dotnet-ref]: https://docs.microsoft.com/dotnet/api/microsoft.azure.devices
[lnk-c-ref]: https://azure.github.io/azure-iot-sdk-c/index.html
[lnk-java-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device
[lnk-node-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-rest-ref]: https://docs.microsoft.com/rest/api/iothub/
[lnk-java-service-ref]: https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.auth
[lnk-node-service-ref]: https://azure.github.io/azure-iot-sdk-node/
[lnk-gateway-ref]: http://azure.github.io/iot-edge/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
