---
title: 创建 Azure IoT Edge 模块技术资产 | Microsoft Docs
description: 创建 IoT Edge 模块的技术资产。
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pbutlerm
ms.openlocfilehash: 2ed4826eb47ab2fb13d312860475f9ec9b323bf7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/18/2019
ms.locfileid: "57884148"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>准备 IoT Edge 模块技术资产

本文介绍在 Azure 市场中发布 IoT Edge 模块技术资产之前，这些资源需要满足的要求。

## <a name="understanding-iot-edge-modules-and-getting-started"></a>了解 IoT Edge 模块和入门

IoT Edge 模块是可在 IoT Edge 设备上运行的与 Docker 兼容的容器。

- 若要了解有关 IoT Edge 模块的详细信息，请参阅[了解 Azure IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)。
- 若要开始进行 IoT Edge 模块开发，请参阅[开发 IoT Edge 模块的要求和可用工具](https://docs.microsoft.com/azure/iot-edge/module-development)。

## <a name="technical-requirements"></a>技术要求

要使 IoT Edge 模块通过认证并在 Azure 市场中发布，必须满足以下技术要求。

### <a name="platform-support"></a>平台支持

IoT Edge 模块必须支持以下平台选项之一。

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支持的第 1 层平台

支持 IoT Edge 所支持的全部第 1 层平台（已在 [Azure IoT Edge 支持](https://docs.microsoft.com/azure/iot-edge/support)中记录）。 我们之所以推荐此选项，是因为它提供更好的客户体验。 后面将会展示满足此条件的模块。 使用此平台选项的模块必须：

- 提供一个 `latest` 标记和一个版本标记（例如 `1.0.1`），这些标记是使用 GitHub [manifest-tool](https://github.com/estesp/manifest-tool) 生成的清单标记。
- 使用[“市场”选项卡](./cpp-marketplace-tab.md)添加[已认证的兼容 IoT Edge 设备](https://aka.ms/iot-edge-certified)的链接。 此链接将解析为 `https://aka.ms/iot-edge-certified`，客户可在此网站中浏览或搜索已认证的设备。 此网站也称为 [Azure IoT Edge 认证](https://catalog.azureiotsolutions.com/)设备目录。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支持的一部分第 1 层平台
  
支持 IoT Edge 所支持的一部分（至少一个）第 1 层平台（已在 [Azure IoT Edge 支持](https://docs.microsoft.com/azure/iot-edge/support)中记录）。 使用此平台选项的模块必须：

- 如果支持多个平台，请提供一个 `latest` 标记和一个版本标记（例如 `1.0.1`），这些标记是使用 GitHub [manifest-tool](https://github.com/estesp/manifest-tool) 生成的清单标记。 仅当只支持一个平台时，清单标记才是可选的。
- 使用[“市场”选项卡](./cpp-marketplace-tab.md)提供 [Azure IoT Edge 认证](https://catalog.azureiotsolutions.com/)设备目录中至少一个 IoT Edge 设备的链接。

### <a name="device-dimensions"></a>设备规格

目标 IoT Edge 设备上的 IoT Edge 模块规格（CPU/RAM/存储/GPU 等）必须满足以下要求：

- 该模块必须适用于 [Azure IoT Edge 认证](https://catalog.azureiotsolutions.com/)设备目录中的**至少一个 IoT Edge 认证**设备。
- 必须在套餐说明中的最后一个段落（在[“市场”选项卡](./cpp-marketplace-tab.md)下）阐述**最低硬件要求**。 （可选）如果模块有明显的差异，则还可以列出建议的硬件要求。 例如，在套餐说明的末尾添加以下部分：

  ```html
    <p><u>Minimum hardware requirements:</u> Linux x64 and arm32  OS, 1GB of RAM, 500 Mb of storage</p>
  ```

### <a name="configuration"></a>配置

该模块还包括默认配置设置，尽量使部署到 IoT Edge 设备的过程变得简单直接。 容器还可以包含 IoT Edge 模块 SDK，以便与 edgeHub 和 IoT 中心通信。

#### <a name="default-configuration"></a>默认配置

IoT Edge 模块必须能够使用[云合作伙伴门户的“SKU”选项卡](./cpp-skus-tab.md)中提供的默认设置启动。 可使用以下默认设置：

- 默认**路由**
- 默认**孪生所需属性**
- 默认**环境变量**
- 默认 **createOptions**

如果默认值所需的参数（例如客户服务器的 IP 地址）不起作用，请添加一个参数作为默认值。 将此值括在方括号中并采用大写。 此示例设置以下默认环境变量：

```
    ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>配置文档

必须明确阐述 IoT Edge 模块的所有配置设置（如何使用其路由、孪生所需属性、环境变量、createOptions，等等。）提供该文档的链接，或者在套餐/SKU 的说明中包含该文档。

### <a name="tags-and-versioning"></a>标记和版本控制

客户必须能够轻松地部署模块和从市场自动获取更新（在开发人员方案中）。他们还必须能够使用和冻结已测试的确切版本（在生产方案中）。

为了达到这些客户的期望并在市场中发布，IoT Edge 模块必须满足以下要求：

- 包含一个指向所有受支持平台上最新版本的 `latest` 清单标记。
- 版本标记必须采用 X.Y.Z 格式，其中 X、Y 和 Z 是整数。
- 包含一个指向所有受支持平台上特定版本的“版本”标记，例如 `1.0.1`。
- 不要更新类似于 `1.0.1` 的“版本”标记，因为它们必须是不可变的。

>[!Note]
>（可选）版本控制可以包含“滚动更新版本”标记，例如 `2.0` 和 `1.0`。 这样就可以支持同时维护多个主要版本。

### <a name="telemetry"></a>遥测

出于遥测目的，使用 IoT 模块 SDK 的模块必须将唯一模块标识符设置为 `PublisherId.OfferId.SkuId`。 使用唯一的标识符可让 Azure 市场识别正在运行的模块实例数。

 在 IoT 模块 SDK 中使用以下方法将 `ProductInfo` 设置到此标识符：

- [C\#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo) 
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

对于不使用 IoT 模块 SDK 的模块，将通过云合作伙伴门户提供不太精确的深入信息，例如下载次数。

### <a name="security"></a>安全

IoT Edge 模块必须尽量请求提供对主机的最低特权访问权限。 应避免使用[特权模块](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)。

### <a name="module-iot-sdk"></a>模块 IoT SDK

认证过程并不要求包含 IoT 模块 SDK。 但是，包含 IoT 模块 SDK 可以提供更好的用户体验。 例如，可支持将消息路由或发送到云中。

需要使用 IoT 模块 SDK 来获取有关正在运行的模块实例数的遥测数据。


## <a name="recertification-process"></a>重新认证过程

<!-- Add legal time windows-->
会影响他们的模块，如的重大更改时，合作伙伴将收到通知：

- IoT Edge 支持的第 1 层 OS/体系结构支持矩阵
- IoT 模块 SDK
- IoT Edge 运行时
- IoT Edge 模块认证准则

合作伙伴必须使用云合作伙伴门户工具更新并重新认证其模块。

## <a name="host-your-iot-edge-module-in-an-azure-container-registry"></a>在 Azure 容器注册表中托管 IoT Edge 模块

若要将 IoT Edge 模块上传到云合作伙伴门户，首先需要将其托管在 [Azure 容器注册表](https://azure.microsoft.com/services/container-registry/) (ACR) 中。 该模块必须包含所要发布的所有标记，包括清单标记引用的映像标记。


## <a name="next-steps"></a>后续步骤

- [创建 IoT Edge 模块套餐](./cpp-create-offer.md)
