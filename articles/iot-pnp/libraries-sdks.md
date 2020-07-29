---
title: IoT 即插即用库和 Sdk
description: 有关可用于开发启用 IoT 即插即用解决方案的设备和服务库的信息。
author: rido-min
ms.author: rmpablos
ms.date: 07/22/2020
ms.topic: reference
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 3331a0a9a8d3fb5d028d801d334daf2dbfa25235
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337324"
---
# <a name="iot-plug-and-play-libraries-and-sdks"></a>IoT 即插即用库和 Sdk

IoT 即插即用库和 Sdk 使开发人员能够在多个平台上使用各种编程语言来构建 IoT 解决方案。 下表提供了一些链接，这些链接指向可帮助你入门的示例和快速入门：

## <a name="microsoft-sdks-for-iot-plug-and-play"></a>用于 IoT 的 Microsoft Sdk 即插即用

**设备 SDK**

| 语言 | 程序包 | 代码存储库 | 示例 | 快速入门 | 参考 |
|---|---|---|---|---|---|
| C-设备 | [vcpkg 1.3。9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [示例](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [连接到 IoT 中心](quickstart-connect-device-c.md) | [引用](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-设备 | [NuGet 1.27。0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [连接到 IoT 中心](quickstart-connect-device-csharp.md) | [引用](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java-设备 | [Maven 1.24。0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [连接到 IoT 中心](quickstart-connect-device-java.md) | [引用](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python-设备 | [pip 2.1。4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [连接到 IoT 中心](quickstart-connect-device-python.md) | [引用](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| 节点-设备 | [npm 1.17。0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [连接到 IoT 中心](quickstart-connect-device-node.md) | [引用](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

**服务 Sdk （预览版）**

| 语言 | 程序包 | 代码存储库 | 示例 | 快速入门 | 参考 |
|---|---|---|---|---|---|
| .NET-IoT 中心服务预览版 | [NuGet 1.27.1-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [示例](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | na | [引用](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices?view=azure-dotnet) |
| Java-IoT 中心服务预览版 | [Maven 1.1。0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | na | [引用](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service?view=azure-java-stable) |
| Python-IoT 中心/数字孪生服务预览版 | [pip 2.2.1 rc1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [与 IoT 中心数字孪生 API 交互](quickstart-service-python.md) | [引用](https://docs.microsoft.com/python/api/azure-iot-hub/?view=azure-python) |
| 节点-IoT 中心/数字孪生服务预览版 | [npm 1.0.0-pnp-id](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [与 IoT 中心数字孪生 API 交互](quickstart-service-node.md) | [引用](https://docs.microsoft.com/javascript/api/azure-iothub/?view=azure-node-latest) |

## <a name="next-steps"></a>后续步骤

若要尝试 Sdk 和库，请参阅[开发人员指南](concepts-developer-guide.md)和[设备快速入门](quickstart-connect-device-c.md)和[服务快速入门](quickstart-service-node.md)。
