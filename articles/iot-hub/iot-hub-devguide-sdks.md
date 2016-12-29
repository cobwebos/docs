---
title: "开发人员指南 - Azure IoT SDK | Microsoft Docs"
description: "Azure IoT 中心开发人员指南 - 有关各种 Azure IoT 设备和服务 SDK 的信息以及指向这些 SDK 的链接。"
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
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: 58dc546ee2db9a0925ecf0fc074795b531323e03


---
# <a name="azure-iot-sdks"></a>Azure IoT SDK
## <a name="azure-iot-device-sdk"></a>Azure IoT 设备 SDK
Microsoft Azure IoT 设备 SDK 包含的代码可帮助构建连接到 Azure IoT 中心服务并由这些服务管理的设备和应用程序。

以下 Azure IoT 设备 SDK 可以从 GitHub 进行下载：

* [适用于 C 的 Azure IoT 设备 SDK][lnk-c-device-sdk]，采用 ANSI C (C99) 编写，具有可移植性和广泛的平台兼容性。
* [适用于 .NET 的 Azure IoT 设备 SDK][lnk-dotnet-device-sdk]
* [适用于 Java 的 Azure IoT 设备 SDK][lnk-java-device-sdk]
* [适用于 Node.js 的 Azure IoT 设备 SDK][lnk-node-device-sdk]
* [适用于 Python 2.7 的 Microsoft Azure IoT 设备 SDK][lnk-python-device-sdk]

> [!NOTE]
> 有关使用语言和平台特定的程序包管理器在开发计算机上安装二进制文件和依赖项的信息，请参阅 GitHub 存储库中的自述文件。
> 
> 

## <a name="os-platforms-and-hardware-compatibility"></a>操作系统平台和硬件兼容性
有关与特定硬件设备的 SDK 兼容性的详细信息，请参阅 [Azure IoT 认证设备目录][lnk-certified]。

## <a name="azure-iot-service-sdk"></a>Azure IoT 服务 SDK
Azure IoT 服务 SDK 包含的代码可帮助构建直接与 IoT 中心进行交互以管理设备和安全性的应用程序。

以下 Azure IoT 服务 SDK 可以从 GitHub 进行下载：

* [适用于 .NET 的 Azure IoT 服务 SDK][lnk-dotnet-service-sdk]
* [适用于 Node.js 的 Azure IoT 服务 SDK][lnk-node-service-sdk]
* [适用于 Java 的 Azure IoT 服务 SDK][lnk-java-service-sdk]

> [!NOTE]
> 有关使用语言和平台特定的程序包管理器在开发计算机上安装二进制文件和依赖项的信息，请参阅 GitHub 存储库中的自述文件。
> 
> 

## <a name="azure-iot-gateway-sdk"></a>Azure IoT 网关 SDK
此 Azure IoT 网关 SDK 包含创建 IoT 网关解决方案的基础结构和模块。 你可以扩展此 SDK 来创建适用于任何端到端场景的网关。

可以从 GitHub 下载 [Azure IoT 网关 SDK][lnk-gateway-sdk]。

## <a name="online-api-reference-documentation"></a>联机 API 参考文档
下面是 Azure IoT 设备、服务和网关库的联机 API 参考文档的链接列表：

* [物联网 (IoT) .NET][lnk-dotnet-ref]
* [IoT 中心 REST][lnk-rest-ref]
* [适用于 C 的 Azure IoT 设备 SDK][lnk-c-ref]
* [适用于 Java 的 Azure IoT 设备 SDK][lnk-java-ref]
* [适用于 Java 的 Azure IoT 服务 SDK][lnk-java-service-ref]
* [适用于 Node.js 的 Azure IoT 设备 SDK][lnk-node-ref]
* [适用于 Node.js 的 Azure IoT 服务 SDK][lnk-node-service-ref]
* [Azure IoT 网关 SDK][lnk-gateway-ref]

## <a name="next-steps"></a>后续步骤
此 IoT 中心开发人员指南中的其他参考主题包括：

* [IoT 中心终结点][lnk-devguide-endpoints]
* [设备克隆和作业的 IoT 中心查询语言][lnk-devguide-query]
* [配额和限制][lnk-devguide-quotas]
* [IoT 中心 MQTT 支持][lnk-devguide-mqtt]

<!-- Links and images -->

[lnk-c-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/c/readme.md
[lnk-dotnet-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/device/readme.md
[lnk-java-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/device/readme.md
[lnk-dotnet-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/csharp/service/README.md
[lnk-java-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/java/service/readme.md
[lnk-node-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/device/readme.md
[lnk-node-service-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/node/service/README.md
[lnk-python-device-sdk]: https://github.com/Azure/azure-iot-sdks/blob/master/python/device/readme.md
[lnk-certified]: https://catalog.azureiotsuite.com/
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/README.md

[lnk-dotnet-ref]: https://msdn.microsoft.com/library/mt488521.aspx
[lnk-c-ref]: http://azure.github.io/azure-iot-sdks/c/api_reference/index.html
[lnk-java-ref]: http://azure.github.io/azure-iot-sdks/java/device/api_reference/index.html
[lnk-node-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iot-device/1.0.15/index.html
[lnk-rest-ref]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-java-service-ref]: http://azure.github.io/azure-iot-sdks/java/service/api_reference/index.html
[lnk-node-service-ref]: http://azure.github.io/azure-iot-sdks/node/api_reference/azure-iothub/1.0.17/index.html
[lnk-gateway-ref]: http://azure.github.io/azure-iot-gateway-sdk/api_reference/c/html/

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


