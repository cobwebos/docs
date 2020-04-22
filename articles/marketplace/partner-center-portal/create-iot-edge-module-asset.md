---
title: 准备 IoT 边缘模块技术资产 - Azure 应用商店
description: 了解物联网 （IoT） 边缘模块技术资产必须满足的技术和配置要求，然后才能将它们发布到 Azure 应用商店。
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 2c0cd47acbd4639ff5eff2af78dcebdfc26270a7
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730704"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>准备 IoT Edge 模块技术资产

> [!IMPORTANT]
> 我们将 IoT Edge 模块产品的管理从云合作伙伴门户迁移到合作伙伴中心。 在迁移产品/服务之前，请按照云合作伙伴门户[准备 IoT Edge 模块技术资产](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/iot-edge-module/cpp-create-technical-assets)以管理产品/服务的说明进行操作。

本文介绍了物联网 （IoT） 边缘模块技术资产在 Azure 应用商店中发布之前必须满足的要求。

## <a name="get-started"></a>入门

IoT Edge 模块是在 IoT 边缘设备上运行的与 Docker 兼容的容器。

- 若要了解有关 IoT Edge 模块的详细信息，请参阅[了解 Azure IoT Edge 模块](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules)。
- 要开始开发 IoT 边缘模块，请参阅[开发您自己的 IoT 边缘模块](https://docs.microsoft.com/azure/iot-edge/module-development)。

## <a name="technical-requirements"></a>技术要求

IoT Edge 模块必须满足以下技术要求才能在 Azure 应用商店中进行认证和发布。

### <a name="platform-support"></a>平台支持

IoT Edge 模块必须支持以下平台选项之一：

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支持的第 1 层平台

您的模块必须支持 IoT 边缘支持的所有第 1 层平台（如[Azure IoT 边缘支持](https://docs.microsoft.com/azure/iot-edge/support)中记录）。 我们之所以推荐此选项，是因为它提供更好的客户体验。 将展示符合此标准的模块。 使用此平台选项的模块必须：

- 提供使用[GitHub 清单工具](https://github.com/estesp/manifest-tool)构建的清单标记的最新标记和版本标记（例如 1.0.1）。

- 使用[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中的产品/服务列表选项卡在 **"有用链接**"部分下向[Azure IoT 边缘认证设备目录](https://catalog.azureiotsolutions.com/alldevices?filters={%2218%22:[%221%22]}/)添加链接。

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>IoT Edge 支持的一部分第 1 层平台

您的模块必须支持 IoT Edge 支持的第 1 层平台的子集（至少一个）（如[Azure IoT Edge 支持](https://docs.microsoft.com/azure/iot-edge/support)中所记录）。 使用此平台选项的模块必须：

- 提供最新的标记和版本标记（例如，1.0.1），这些标记是使用 GitHub[清单工具](https://github.com/estesp/manifest-tool)构建的清单标记（如果支持多个平台）。 仅当只支持一个平台时，清单标记才是可选的。
- 使用[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中的产品/服务列表选项卡将 **"有用链接**"部分下的链接添加到[Azure IoT 边缘认证设备目录中](https://catalog.azureiotsolutions.com/)的至少一个 IoT Edge 设备。

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="这是合作伙伴中心内优惠列表部分的图像":::

### <a name="device-dimensions"></a>设备规格

目标 IoT 边缘设备上的 IoT 边缘模块尺寸（如 CPU、RAM、存储和 GPU）必须满足以下要求：

- 该模块必须与[Azure IoT 边缘认证设备目录中](https://catalog.azureiotsolutions.com/)的至少一个 IoT 边缘设备配合使用。

- 最低硬件要求必须记录为产品/服务描述中的最后一段（在[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)的产品/服务列表选项卡下）。 （可选）如果模块有明显的差异，则还可以列出建议的硬件要求。 例如，在套餐说明的末尾添加以下部分：

复制此 HTML 文本或在编辑窗口中使用相应的富文本函数。

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>配置

您的模块必须包含默认配置设置，以使部署到 IoT Edge 设备尽可能简单。 此信息可在[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中的"**计划技术配置**"页中提供。 容器还可以包括 IoT 边缘模块 SDK，以便与边缘集线器和 IoT 中心进行通信。

#### <a name="default-configuration"></a>默认配置

IoT Edge 模块必须能够从[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)中计划**的技术配置**页中提供的默认设置开始。 可使用以下默认设置：

- 默认**路由**
- 默认**模块双所需属性**
- 默认**环境变量**
- 默认**容器创建选项**

在默认值所需的参数没有意义（例如，客户服务器的 IP 地址）的情况下，添加参数作为默认值。 此值为大写，并包含在括号中。 此示例设置以下默认环境变量：

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>配置文档

必须清楚地记录 IoT Edge 模块的所有配置设置。 例如，必须记录如何使用其路由、双所需属性、环境变量、创建选项等。 您必须提供指向文档的链接，或将其作为产品/服务或计划说明的一部分。 您可以在[合作伙伴中心的](https://partner.microsoft.com/dashboard/commercial-marketplace)**"产品/服务"列表**和**计划列表**页中提供此信息。

#### <a name="tags-and-versioning"></a>标记和版本控制

客户必须能够轻松部署模块并自动从市场获取更新（在开发人员方案中）。 他们还必须能够使用和冻结他们测试的精确版本（在生产方案中）。

为了满足这些客户的期望并在市场上发布，IoT Edge 模块必须满足以下要求

- 包括指向所有受支持平台上最新版本的清单最新标记。
- 在 X.Y.Z 窗体中制作版本标记，其中 X、Y 和 Z 是整数。
- 包含一个"版本"标记，如 1.0.1，用于指向所有受支持平台上的特定版本。
- 不要更新"版本"标记（如 1.0.1），因为它们不能更改。

> [!NOTE]
> 可选，版本控制可以包括"滚动版本"标记，如 2.0 和 1.0。 这样就可以支持同时维护多个主要版本。

### <a name="telemetry"></a>遥测

出于遥测目的，使用 IoT 模块 SDK 的模块必须将唯一模块标识符设置为 PublisherId.OfferId.SkuId。 唯一标识符可帮助 Azure 应用商店标识正在运行的模块实例数。

使用 IoT 模块 SDK 中的以下方法之一将 ProductInfo 设置为此标识符：

- [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo?view=azure-dotnet#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.productinfo?view=azure-java-stable)

对于不使用 IoT 模块 SDK 的模块，合作伙伴中心提供的准确见解（如下载次数）不太精确。

### <a name="security"></a>安全性

IoT 边缘模块必须避免[特权模块](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)。 而是要求尽可能少特权地访问主机。

### <a name="module-iot-sdk"></a>模块 IoT SDK

认证过程并不要求包含 IoT 模块 SDK。 但是，包含 IoT 模块 SDK 可以提供更好的用户体验。 例如，可支持将消息路由或发送到云中。

需要 IoT 模块 SDK 来获取有关正在运行的模块实例数的遥测数据。

## <a name="recertification-process"></a>重新认证过程

每当有影响其模块的重大变化时，都会通知合作伙伴，例如：

- IoT 边缘支持的第 1 层操作系统/拱形支持矩阵
- IoT 模块 SDK
- IoT Edge 运行时
- IoT 边缘模块认证指南

合作伙伴必须通过在[合作伙伴中心](https://partner.microsoft.com/dashboard/commercial-marketplace)重新发布产品/服务来更新和重新认证其优惠。

如果您更新产品/服务（如添加新图像标记），您的产品/服务也将重新认证。

## <a name="host-module-in-azure-container-registry"></a>Azure 容器注册表中的主机模块

要将 IoT 边缘模块上载到 Azure 应用商店，首先需要将其托管在[Azure 容器注册表](https://azure.microsoft.com/services/container-registry/)（ACR） 中。 该模块必须包含要发布的所有标记，包括清单标记引用的图像标记。 有关详细信息，请参阅教程["创建 Azure 容器注册表"并推送容器映像](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-acr)。

## <a name="next-steps"></a>后续步骤

- [创建 IoT Edge 模块产品/服务](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation)