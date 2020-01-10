---
title: IoT 即插即用库和 Sdk
description: 有关可用于开发启用 IoT 即插即用解决方案的设备和服务库的信息。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b328d7fe4cf3a3487614ed93dcf130aa7a233efd
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830566"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT 即插即用库和 Sdk

IoT 即插即用库和 Sdk 使开发人员能够在多个平台上使用各种编程语言来构建 IoT 解决方案。 下表提供了一些链接，这些链接指向可帮助你入门的示例和快速入门：

## <a name="microsoft-supported-libraries-and-sdks"></a>Microsoft 支持的库和 Sdk

| 平台 | 库/包 | 源代码 | 示例 | 快速入门 | 参考 |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [Apt 上的设备 SDK-get](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字克隆客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-c-linux.md) | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Windows  | [Vcpkg 上的设备 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字克隆客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-c-windows.md) | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [嵌入设备 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字克隆客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/Arduino  | [Arduino IDE 中的设备 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字克隆客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [CocoaPod 上的设备 SDK](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字克隆客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [数字输出示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-csharp.md) | [参考](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [数字输出示例](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-java.md) | [参考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [数字输出示例](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-node.md) | [参考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |

## <a name="iot-hub-support"></a>IoT 中心支持

仅[免费和标准层 iot 中心](../iot-hub/iot-hub-scaling.md)支持 IoT 即插即用设备功能。

## <a name="next-steps"></a>后续步骤

除了设备 Sdk 和库，还可以使用 REST Api 与模型存储库进行交互。