---
title: IoT 即插即用当前版本 | Microsoft Docs
description: 了解 IoT 即插即用当前版本中包含的内容。
author: dominicbetts
ms.author: dobett
ms.date: 10/01/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: c7ebcdac3174f7eb497ae3e976386ab92212e1ba
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715559"
---
# <a name="whats-in-the-current-iot-plug-and-play-release"></a>IoT 即插即用当前版本中包含的内容

本文总结了支持 IoT 即插即用当前版本的工具、SDK 和 API。 所示版本号反映了 IoT 即插即用产品正式发布时的版本号。 版本号可能会在发布之后递增。

## <a name="modeling-language"></a>建模语言

[数字孪生定义语言 (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl)。

若要详细了解 IoT 即插即用设备如何与 DTDL 配合使用，请参阅 [IoT 即插即用约定](concepts-convention.md)。

## <a name="tools-and-utilities"></a>工具和实用程序

- Azure IoT 资源管理器 0.12.0。

    若要了解详细信息，请参阅[安装并使用 Azure IoT 资源管理器](howto-use-iot-explorer.md)。

- VS Code 扩展 1.0.0。

    若要了解详细信息，请参阅[安装并使用 DTDL 创作工具](howto-use-dtdl-authoring-tools.md)。

- Visual Studio 2019 扩展 1.0.0。

    若要了解详细信息，请参阅[安装并使用 DTDL 创作工具](howto-use-dtdl-authoring-tools.md)。

- Azure CLI IoT 扩展 0.10.0。

    若要了解详细信息，请参阅[安装并使用适用于 Azure CLI 的 Azure IoT 扩展](howto-use-iot-pnp-cli.md)。

    > [!TIP]
    > Azure IoT 扩展包含有助于验证设备的命令。 请参阅 `az iot product -h`。

## <a name="libraries-and-sdks"></a>库和 SDK

若要了解有关库和 SDK 的详细信息，请参阅[用于 IoT 即插即用的 Microsoft SDK](libraries-sdks.md)。

- C 设备 SDK [vcpkg 1.3.9](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/setting_up_vcpkg.md)
- 嵌入的 C 设备 SDK [GitHub](https://github.com/Azure/azure-sdk-for-c/)
- .NET 设备 SDK [NuGet 1.31.0](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client)
- Java 设备 SDK [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-device-client)
- Python 设备 SDK [pip 2.3.0](https://pypi.org/project/azure-iot-device/)
- Node.js 设备 SDK [npm 1.17.2](https://www.npmjs.com/package/azure-iot-device)
- .NET - IoT 中心服务 [NuGet 1.27.1](https://www.nuget.org/packages/Microsoft.Azure.Devices )
- Java - IoT 中心服务 [Maven 1.26.0](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot/iot-service-client/1.26.0)
- Node.js - IoT 中心服务 [npm 1.13.0](https://www.npmjs.com/package/azure-iothub)
- Python - IoT 中心/数字孪生服务 [pip 2.2.3](https://pypi.org/project/azure-iot-hub)
- DTDL 模型分析程序 [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DigitalTwins.Parser)。

## <a name="rest-apis"></a>REST API

REST API [2020-09-30](https://docs.microsoft.com/rest/api/iothub)。

若要了解详细信息，请参阅 [IoT 即插即用开发人员指南](concepts-developer-guide-service.md)。

## <a name="iot-hub"></a>IoT 中心

所有区域的 IoT 中心均支持 IoT 即插即用。 仅标准或免费层 IoT 中心支持 IoT 即插即用。

## <a name="announcements"></a>公告

有关当前和以前的 IoT 即插即用公告，请参阅以下博客文章：

- [公共预览版刷新（发布于 2020 年 8 月 29 日）](https://techcommunity.microsoft.com/t5/internet-of-things/add-quot-plug-and-play-quot-to-your-iot-solutions/ba-p/1548531)
- [准备并验证设备以进行 IoT 即插即用（发布于 2020 年 8 月 26 日）](https://azure.microsoft.com/blog/prepare-and-certify-your-devices-for-iot-plug-and-play/)
- [IoT 即插即用现已提供预览版（发布于 2019 年 8 月 22 日）](https://azure.microsoft.com/blog/iot-plug-and-play-is-now-available-in-preview/)
- [使用 Azure IoT Central 和 IoT 即插即用进行生成（发布于 2019 年 5 月 7 日）](https://azure.microsoft.com/blog/build-with-azure-iot-central-and-iot-plug-and-play/)
