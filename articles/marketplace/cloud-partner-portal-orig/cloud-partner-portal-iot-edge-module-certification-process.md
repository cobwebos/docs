---
title: IoT Edge 模块认证 | Microsoft Docs
description: 认证适用于市场的 IoT Edge 模块。
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
ms.date: 09/19/2018
ms.author: pbutlerm
ms.openlocfilehash: c37ed908b61ca54957affed3f81526353bc3f53b
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389558"
---
# <a name="the-iot-edge-module-certification-process"></a>IoT Edge 模块认证流程

本文介绍对要在 Azure 市场上发布的 IoT Edge 模块进行认证的步骤和要求。 

>[!Note]
>这是一个临时文档。 IoT Edge 模块市场正在并行构建中，本文日后将被公共文档取代。

## <a name="understanding-an-iot-edge-module"></a>了解 IoT Edge 模块

模块术语：

-   模块映像是包含定义模块的程序包。

-   模块实例是在 IoT Edge 设备上运行模块映像的特定计算单位。 该模块实例由 IoT Edge 运行时启动。

模块也可能包含使用以下术语的 IoT 模块 SDK：

-   **模块标识**是存储在 IoT 中心的一段信息（包括安全凭据），与每个模块实例相关联。

-   模块孪生是存储在 IoT 中心的 JSON 文档，包含模块实例的状态信息，其中包括元数据、配置和条件。

-   **SDK** 用于以多种语言开发自定义模块，例如：C\#、C、Python、Java 和 Node.JS。

## <a name="the-onboarding-process-for-an-iot-edge-module"></a>IoT Edge 模块的上架流程

以下屏幕截图展示了 IoT Edge 模块在 Azure 市场公开发行的流程。

![认证流程](./media/cloud-partner-portal-iot-edge-module-certification-process/onboarding-process.png)

### <a name="onboarding-step-details"></a>上架步骤详细信息

-   **步骤 1**：合作伙伴在云合作伙伴门户工具中将产品/服务类型为 **IoT Edge 模块**的产品/服务提交给 Azure 市场团队。 必须是官方的 MS 合作伙伴才能访问云合作伙伴门户工具。 有关详细信息，请参阅[发布者指南](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide)。

-   **步骤 2**：市场自动运行防病毒和反恶意软件检查。 IoT 团队可在此阶段运行其自定义的自动化引入测试。

-   **步骤 3**：公开发行模块。 模块列于市场中，并且可以从 URL 匿名拉取容器。 例如，*marketplace.azurecr.io/module-identifier*。

## <a name="iot-edge-module-certification-criteria"></a>IoT Edge 模块认证标准

以下是 IoT Edge 模块要通过认证并且在 Azure 市场中发布所需满足的主要要求。

>[!Note]
>这些要求可能会发生变化。 你将事先收到通知，并有时间更新容器，使其满足更新后的要求。

### <a name="technical-requirements"></a>技术要求

**成为 IoT Edge 模块**

-   目前只有与 Docker 兼容的容器才能作为 IoT Edge 模块。 模块必须在 [Moby](https://mobyproject.org/) 上运行。 

    >[!Note]
    >Docker 容器可能适用于 Moby，因为 Moby 是 Docker 的基础开放源代码项目。

-   合作伙伴必须提供以下默认设置： 

    -   默认 **tag**（不能为空。）

    -   默认 **createOptions**（可以为空。）

    -   如果使用 IoT 模块 SDK，则需要默认 **twin**（可以为空。）

    -   如果使用 IoT 模块 SDK，则需要默认 **routes**（可以为空。）

**平台支持**

-   仅需支持已在 IoT Edge 第 1 层**公开发行**的平台（如 [Azure IoT Edge 支持](https://docs.microsoft.com/azure/iot-edge/support)中所记录）。 例如，这当前意味着支持以下 OS 和体系结构组合：

    -   适用于 x64 的 Ubuntu Server 18.04

    -   适用于 x64 的 Ubuntu Server 16.04

    -   Raspbian-stretch for arm32 (armhf)

**设备尺寸标注**

-   任何与 Raspberry Pi 或更高版本尺寸相当的设备（CPU/RAM/存储/GPU 等等）都可以作为 IoT Edge 设备。 如果模块只能在特定的尺寸约束下工作，则必须在模块说明中指定这些约束。

**默认设置**

-   对于每个支持的平台（OS + 体系结构），模块一开始应使用现成的默认参数。

-   应明确记录配置设置（标记、环境变量、孪生、路由。）在列表中，合作伙伴可以为其模块定义支持基本 HTML 标记或指向外部网页的说明。

**版本控制**

-   客户必须能够选择他们是要自动更新来自市场的模块，还是要使用他们已测试的版本。 市场模块必须遵循与 IoT Edge 运行时相同的版本控制模式。 例如：

    -   可更新累积标记（例如“1.0”）。

    -   不能更新次要版本标记（例如“1.3.24”）。

**遥测**

-   使用 IoT 模块 SDK 的模块必须设置由市场分配的唯一模块标识符，以供遥测之用。 这可让 Azure 市场识别正在运行的模块实例数。 这个唯一标识符是市场在引入时分配的容器名称。 请使用以下 SDK 的方法设置此标识符：
    - [C\#](https://hub.docker.com/_/mysql/)
    - [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
    - [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device._product_info?view=azure-java-stable)

-   对于不使用 IoT 模块 SDK 的模块，将通过云合作伙伴门户提供不太精确的深入信息。 例如下载次数。

**安全性**

-   容器对主机的访问权限应尽可能具有最低特权。 “特权”容器应仅占极少数。

-   合作伙伴必须在其提供的支持中确保其模块的安全性。

**更新**

-   合作伙伴必须将其模块保持在最新状态且正常运行。 如果为 IoT Edge 运行时规划了任何中断性变更，他们将事先获得通知。

**模块 IoT SDK**

-   认证过程并不要求包含 IoT 模块 SDK。
    但是，包含 IoT 模块 SDK 可以提供更好的用户体验。 例如，可支持将消息路由或发送到云端等等。 需要使用 IoT 模块 SDK 来获取有关正在运行的模块实例数的遥测数据。

**主观要求**

-   必须满足至少一个实际 IoT Edge 用例。 例如，除非客户愿意从 IoT Edge 使用 WordPress 容器，否则就不应将该容器上架。

**法律要求（来自 AMP 政策）**

-   模块必须遵从 Microsoft Azure 市场协议和政策。 有关详细信息，请参阅随附的发布者协议和[参与政策](https://azure.microsoft.com/support/legal/marketplace/participation-policies/)。

-   除了 Azure 市场协议和政策外，可能还会有 IoT Edge 模块产品/服务类型特有的其他法律要求。 此要求当前正在审查中。

-   模块必须具有*使用条款*和*隐私策略*。

-   如果模块使用任何第三方产品，则模块还应有第三方声明。

**支持要求（来自 AMP 政策）**

-   合作伙伴须负责支持自己的 IoT Edge 模块。 他们应确保在 IoT Edge 模块说明中提供的支持选项可供使用，且至少有一个支持选项始终可用。 （有关更多详细信息，请参阅 AMP 发布者协议第 4 条。）

**分类**

-   所有 IoT Edge 模块都会出现在“物联网”\>“IoT Edge 模块”类别下。 合作伙伴须自行为其产品选择最合适的子类别。
