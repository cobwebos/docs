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
ms.openlocfilehash: 5638cd9973c6a4df809e0b200efe85b067aae026
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407789"
---
# <a name="microsoft-sdks-for-iot-plug-and-play"></a>用于 IoT 的 Microsoft Sdk 即插即用

IoT 即插即用库和 Sdk 使开发人员能够在多个平台上使用各种编程语言来构建 IoT 解决方案。 下表提供了一些链接，这些链接指向可帮助你入门的示例和快速入门：

## <a name="device-sdks-ga"></a>设备 Sdk （GA）

| 语言 | 程序包 | 代码存储库 | 示例 | 快速入门 | 参考 |
|---|---|---|---|---|---|
| C-设备 | [vcpkg 1.3。9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md) | [GitHub](https://github.com/Azure/azure-iot-sdk-c/releases/tag/2020-07-19) | [示例](https://github.com/Azure/azure-iot-sdk-c/tree/2020-07-19/iothub_client/samples/pnp) | [连接到 IoT 中心](quickstart-connect-device-c.md) | [引用](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/) |
| .NET-设备 | [NuGet 1.27。0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/1.27.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/device/samples/PnpDeviceSamples) | [连接到 IoT 中心](quickstart-connect-device-csharp.md) | [引用](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client?view=azure-dotnet) |
| Java-设备 | [Maven 1.24。0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client/1.24.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples/pnp-device-sample) | [连接到 IoT 中心](quickstart-connect-device-java.md) | [引用](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device?view=azure-java-stable) |
| Python-设备 | [pip 2.1。4](https://pypi.org/project/azure-iot-device/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples/pnp) | [连接到 IoT 中心](quickstart-connect-device-python.md) | [引用](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device?view=azure-python) |
| 节点-设备 | [npm 1.17。0](https://www.npmjs.com/package/azure-iot-device)  | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples/pnp) | [连接到 IoT 中心](quickstart-connect-device-node.md) | [引用](https://docs.microsoft.com/javascript/api/azure-iot-device/?view=azure-node-latest) |

## <a name="device-sdks-preview"></a>设备 Sdk （预览版）

| 语言 | 代码存储库/示例 |
|---|---|
|用于嵌入的 Azure SDK| [GitHub](https://github.com/Azure/azure-sdk-for-c/#) |
|Azure RTO IoT 中间件| [GitHub](https://github.com/azure-rtos/azure-iot-preview#) |
|Azure RTO 入门指南 | [GitHub](https://github.com/azure-rtos/getting-started) |

## <a name="service-sdks-preview"></a>服务 Sdk （预览版）

| 语言 | 程序包 | 代码存储库 | 示例 | 快速入门 | 参考 |
|---|---|---|---|---|---|
| .NET-IoT 中心服务预览版 | [NuGet 1.27.1-002](https://www.nuget.org/packages/Microsoft.Azure.Devices/1.27.1-preview-002 ) | [GitHub](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh) | [示例](https://github.com/Azure/azure-iot-sdk-csharp/tree/pnp-preview-refresh/iothub/service/samples/PnpServiceSamples) | N/A | 空值 |
| Java-IoT 中心服务预览版 | [Maven 1.1。0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client-preview/1.1.0) | [GitHub](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh) | [示例](https://github.com/Azure/azure-iot-sdk-java/tree/pnp-preview-refresh/service/iot-service-samples/pnp-service-sample) | N/A | 空值 |
| 节点-IoT 中心服务预览 | [npm 1.12.4-pnp-id](https://www.npmjs.com/package/azure-iothub/v/1.12.4-pnp-refresh.4) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/service/samples) | N/A | 空值 |
| Python-IoT 中心/数字孪生服务预览版 | [pip 2.2.1 rc1](https://pypi.org/project/azure-iot-hub/2.2.1rc1/) | [GitHub](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh) | [示例](https://github.com/Azure/azure-iot-sdk-python/tree/pnp-preview-refresh/azure-iot-hub/samples) | [与 IoT 中心数字孪生 API 交互](quickstart-service-python.md) | 空值 |
| Node-数字孪生服务预览版 | [npm 1.0.0-pnp-id](https://www.npmjs.com/package/azure-iot-digitaltwins-service/v/1.0.0-pnp-refresh.3) | [GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/) | [示例](https://github.com/Azure/azure-iot-sdk-node/tree/pnp-preview-refresh/digitaltwins/samples/service/javascript) | [与 IoT 中心数字孪生 API 交互](quickstart-service-node.md) | 空值 |

## <a name="next-steps"></a>后续步骤

若要尝试 Sdk 和库，请参阅[开发人员指南](concepts-developer-guide.md)和[设备快速入门](quickstart-connect-device-c.md)和[服务快速入门](quickstart-service-node.md)。
