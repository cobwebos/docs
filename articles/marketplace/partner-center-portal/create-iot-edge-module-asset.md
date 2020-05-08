---
title: 准备 IoT Edge 模块技术资产-Azure Marketplace
description: 了解物联网（IoT）边缘模块在将其发布到 Azure Marketplace 之前必须满足的技术和配置要求。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: fc35602b55e79f3351da0def800d2a2b2698e250
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82856727"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>准备 IoT Edge 模块技术资产

本文介绍了物联网（IoT）边缘模块在 Azure Marketplace 中发布之前必须满足的要求。

## <a name="get-started"></a>入门

IoT Edge 模块是在 IoT Edge 设备上运行的 Docker 兼容容器。

- 若要了解有关 IoT Edge 模块的详细信息，请参阅[了解 Azure IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)。
- 若要开始开发 IoT Edge 模块，请参阅[开发自己的 IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/module-development)。

## <a name="technical-requirements"></a>技术要求

IoT Edge 模块必须满足以下技术要求才能在 Azure Marketplace 中认证和发布。

### <a name="platform-support"></a>平台支持

IoT Edge 模块必须支持以下平台选项之一：

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支持的第 1 层平台

模块必须支持 IoT Edge 支持的所有第1层平台（如[Azure IoT Edge 支持](https://docs.microsoft.com/azure/iot-edge/support)中所述）。 我们之所以推荐此选项，是因为它提供更好的客户体验。 满足此条件的模块将为展示。 使用此平台选项的模块必须：

- 提供使用[GitHub 清单工具](https://github.com/estesp/manifest-tool)生成的清单标记的最新标记和版本标记（例如1.0.1）。

- 使用 "[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)" 中的 "产品列表" 选项卡，在 "**有用链接**" 部分下添加指向[Azure IoT Edge 认证设备目录](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/)的链接。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支持的一部分第 1 层平台

模块必须支持 IoT Edge 支持的第1层平台（如[Azure IoT Edge 支持](https://docs.microsoft.com/azure/iot-edge/support)中所述）的一个子集（至少一个）。 使用此平台选项的模块必须：

- 提供一个新标记和一个版本标记（例如1.0.1），该标记是使用 GitHub 清单生成的清单标记[-](https://github.com/estesp/manifest-tool)如果支持多个平台，则为。 仅当只支持一个平台时，清单标记才是可选的。
- 使用 "[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)" 中的 "产品列表" 选项卡，从[Azure IoT Edge 认证设备目录](https://catalog.azureiotsolutions.com/)中的至少一个 IoT Edge 设备添加到 "**有用链接**" 部分下的链接。

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="这是合作伙伴中心内产品/服务列表部分的图像":::

### <a name="device-dimensions"></a>设备规格

目标 IoT Edge 设备上 IoT Edge 模块维度（例如 CPU、RAM、存储和 GPU）必须满足以下要求：

- 该模块必须与[Azure IoT Edge 认证设备目录](https://catalog.azureiotsolutions.com/)中的至少一个 IoT Edge 设备配合使用。

- 最低硬件要求必须记录为产品/服务说明的最后一段（在[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的 "产品列表" 选项卡下）。 （可选）如果模块有明显的差异，则还可以列出建议的硬件要求。 例如，在套餐说明的末尾添加以下部分：

复制此 HTML 文本或在编辑窗口中使用相应的多格式文本函数。

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>配置

模块必须包含默认配置设置，才能使部署到 IoT Edge 设备尽可能简单。 此信息可在[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的计划的**技术配置**页中提供。 容器还可以包含 IoT Edge 模块 SDK，以实现与边缘集线器和 IoT 中心的通信。

#### <a name="default-configuration"></a>默认配置

IoT Edge 模块必须能够以[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)计划的 "**技术配置**" 页中提供的默认设置开始。 可使用以下默认设置：

- 默认**路由**
- 默认**模块克隆所需属性**
- 默认**环境变量**
- 默认**容器创建选项**

在默认值所需的参数没有意义的情况下（例如，客户服务器的 IP 地址），请将参数添加为默认值。 此值为大写，并用括号括起来。 此示例设置以下默认环境变量：

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>配置文档

必须清楚地记录 IoT Edge 模块的所有配置设置。 例如，你必须记录如何使用其路由、对所需的属性、环境变量、createOptions 等。 您必须提供文档的链接或使其成为您的产品/服务或计划说明的一部分。 你可以在[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的**产品/服务**列表和**计划列表**页提供此信息。

#### <a name="tags-and-versioning"></a>标记和版本控制

客户必须能够轻松部署模块并自动从 marketplace 获取更新（在开发人员方案中）。 它们还必须能够使用和冻结其测试的确切版本（在生产方案中）。

为了满足这些客户的期望并在 marketplace 中发布，IoT Edge 模块必须满足以下要求

- 包括一个清单最新标记，该标记指向所有受支持平台上的最新版本。
- 使版本标记采用格式 X.x.x.x，其中 X、Y 和 Z 是整数。
- 包含指向所有受支持平台上的特定版本的 "版本" 标记，如1.0.1。
- 请勿更新 "版本" 标记，如1.0.1，因为不得更改它们。

> [!NOTE]
> 版本控制可以选择包括 "滚动版本" 标记，例如2.0 和1.0。 这样就可以支持同时维护多个主要版本。

### <a name="telemetry"></a>遥测

使用 IoT 模块 SDK 的模块必须将唯一的模块标识符设置为 PublisherId，以用于遥测目的。 唯一标识符有助于 Azure Marketplace 标识正在运行的模块实例的数目。

使用 IoT 模块 Sdk 中的以下方法之一将 Data.productinfo 设置为此标识符：

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

对于不使用 IoT 模块 SDK 的模块，通过合作伙伴中心提供不太准确的见解，如下载数量。

### <a name="security"></a>安全性

IoT Edge 模块必须避免具有[特权的模块](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)。 而是要求尽可能最少的权限访问主机。

### <a name="module-iot-sdk"></a>模块 IoT SDK

认证过程并不要求包含 IoT 模块 SDK。 但是，包含 IoT 模块 SDK 可以提供更好的用户体验。 例如，可支持将消息路由或发送到云中。

需要 IoT 模块 SDK 才能获取有关正在运行的模块实例数的遥测数据。

## <a name="recertification-process"></a>重新认证过程

每当存在影响其模块的重大更改时，就会通知合作伙伴，例如：

- IoT Edge 支持第1层 OS/弯支持矩阵
- IoT 模块 SDK
- IoT Edge 运行时
- IoT Edge 模块认证准则

合作伙伴必须通过在[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)重新发布其产品/服务来更新和再次验证其产品/服务。

如果更新产品/服务（如添加新的图像标记），您的产品/服务也会 recertified。

## <a name="host-module-in-azure-container-registry"></a>Azure 容器注册表中的主机模块

若要将 IoT Edge 模块上传到 Azure Marketplace，首先需要将其托管在[Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)（ACR）中。 该模块必须包含你要发布的所有标记，包括清单标记所引用的图像标记。 有关详细信息，请参阅教程[创建 Azure 容器注册表和推送容器映像](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr)。

## <a name="next-steps"></a>后续步骤

- [创建 IoT Edge 模块产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)