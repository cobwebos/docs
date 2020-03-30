---
title: IoT 即插即用库和 SDK
description: 有关可用于开发支持 IoT 即插即用解决方案的设备和服务库的信息。
author: dominicbetts
ms.author: dobett
ms.date: 01/08/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 394edc24cfeefb96cb2f412985cb990a2306bb09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064334"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT 即插即用库和 SDK

IoT 即插即用库和 SDK 使开发人员能够在多个平台上使用各种编程语言构建 IoT 解决方案。 下表包括指向示例的链接和帮助您入门的快速入门：

## <a name="microsoft-supported-libraries-and-sdks"></a>微软支持的库和 SDK

| Platform | 库/包 | 源代码 | 示例 | 快速入门 | 参考 |
| -------- | ------- | ----------- | ------ | ---------- | --------- |
| C/Linux  | [apt-get 上的设备 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#aptgetpackage) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字双客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-c-linux.md) | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/窗口  | [Vcpkg 上的设备 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/doc/setting_up_vcpkg.md#setup-c-sdk-vcpkg-for-windows-development-environment) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字双客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-c-windows.md) | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/mbed  | [嵌入上的设备 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#mbed) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字双客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/阿尔杜伊诺  | [Arduino IDE 中的设备 SDK](https://github.com/Azure/azure-iot-sdk-c/blob/public-preview/iothub_client/readme.md#arduino) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字双客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C/iOS  | [可可盒上的设备 SDK](https://cocoapods.org/pods/AzureIoTHubClient) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview) | [数字双客户端示例](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/digitaltwin_client/samples) |  | [参考](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| C#    | [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Devices.DigitalTwin.Client) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp) | [数字双子样本](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/digitaltwin/Samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-csharp.md) | [参考](https://docs.microsoft.com/dotnet/api/overview/azure/iot/client?view=azure-dotnet) |
| Java   | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/digital-twin-device-client-preview/1.0.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java) | [数字双子样本](https://github.com/Azure-Samples/azure-iot-samples-java/tree/master/digital-twin/Samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-java.md) | [参考](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device) |
| Node.js | [NPM](https://www.npmjs.com/package/azure-iot-digitaltwins-device) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview) | [数字双子样本](https://github.com/Azure/azure-iot-sdk-node/tree/digitaltwins-preview/digitaltwins/samples) | [连接到 IoT 中心](./quickstart-connect-pnp-device-node.md) | [参考](https://docs.microsoft.com/javascript/api/azure-iot-device/) |

## <a name="iot-hub-support"></a>IoT 中心支持

IoT 即插即用设备功能仅受[免费和标准层 IoT 集线器](../iot-hub/iot-hub-scaling.md)的支持。

## <a name="next-steps"></a>后续步骤

除了设备 SDK 和库之外，您还可以使用 REST API 与模型存储库进行交互。