---
title: 从 Azure 门户部署模块 - Azure IoT Edge
description: 根据部署清单的配置，在 Azure 门户中使用 IoT 中心将 IoT Edge 模块从 IoT 中心推送到 IoT Edge 设备。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271442"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>通过 Azure 门户部署 Azure IoT Edge 模块

使用业务逻辑创建 IoT Edge 模块后，需要将其部署到设备后才能在边缘操作。 如果多个模块共同协作来收集和处理数据，可同时部署它们并声明用于连接它们的路由规则。

本文介绍了 Azure 门户如何引导创建部署清单并将部署推送给 IoT Edge 设备。 有关基于多个设备的共享标记创建针对多个设备的部署的信息，请参阅[大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)。

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device.md#register-in-the-azure-portal)。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 若要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

Azure 门户提供部署清单的创建向导，无需你手动构建 JSON 文档。 创建分为三步：添加模块、指定路由和评审部署************。

### <a name="select-device-and-add-modules"></a>选择设备并添加模块

1. 登录到 Azure[门户](https://portal.azure.com)并导航到 IoT 中心。
1. 在左窗格的菜单中，选择“IoT Edge”****。
1. 在设备列表中单击目标设备的 ID。
1. 在上方栏中，选择“设置模块”****。
1. 在此页的“容器注册表设置”部分，提供用于访问包含模块映像的任何专用容器注册表的凭据****。
1. 在此页的“IoT Edge 模块”部分，选择“添加”********。
1. 从下拉菜单中查看模块类型：

   * **IoT Edge 模块** - 提供模块名称和容器映像 URI。 例如，示例 SimulatedTemperatureSensor 模块的映像 URI 为 `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`。 如果模块映像存储在专用容器注册表中，则在此页面上添加凭据来访问该映像。
   * **市场模块** - Azure 市场中托管的模块。 某些市场模块需要其他配置，因此请查看 [Azure 市场 IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)列表中的模块详细信息。
   * **Azure 流分析模块** - 通过 Azure 流分析工作负载生成的模块。

1. 添加模块后，从列表中选择模块名称以打开模块设置。 必要时请填写可选字段。 要详细了解容器创建选项、重启策略和所需状态，请参阅 [EdgeAgent 必需属性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。 要详细了解模块孪生，请参阅[定义或更新所需属性](module-composition.md#define-or-update-desired-properties)。
1. 如果需要，请重复步骤 5 到 8，向部署中添加其他模块。
1. 选择 **"下一步"：要继续**到路线部分的路由。

### <a name="specify-routes"></a>指定路由

在“路由”选项卡中，定义消息在模块和 IoT 中心之间传递的方式****。 使用名称/值对构造消息。 默认情况下，路由称为**路由**，定义为**从 /消息/into\* $upstream**，这意味着任何模块输出的任何消息都发送到 IoT 中心。  

使用["声明"路由](module-composition.md#declare-routes)中的信息添加或更新路由，然后选择 **"下一步：审阅 + 创建"** 以继续向导的下一步。

### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 请注意，$edgeAgent 和 $edgeHub 模块已声明但却未添加********。 这两个模块构成 [IoT Edge 运行时](iot-edge-runtime.md)，且是每个部署中所需的默认设置。

查看部署信息，然后选择“创建”****。

## <a name="view-modules-on-your-device"></a>查看设备上的模块

将模块部署到设备之后，即可在 IoT 中心的设备详细信息页中查看所有模块。 此页面显示每个已部署模块的名称，以及部署状态和退出代码等有用信息。

## <a name="deploy-modules-from-azure-marketplace"></a>从 Azure 市场部署模块

[Azure 应用商店](https://azuremarketplace.microsoft.com/)是一个在线应用程序和服务市场，您可以在其中浏览各种经过认证和优化以在 Azure 上运行的企业应用程序和解决方案，包括[IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。

可以从 Azure 市场和 IoT 中心部署 IoT Edge 模块。

### <a name="deploy-from-azure-marketplace"></a>从 Azure 市场部署

详细考察市场中的 IoT Edge 模块，找到所需的模块后，可以通过选择“创建”或“立即获取”来部署它********。 继续执行部署向导步骤，这些步骤可能会因所选的 IoT Edge 模块而异：

1. 通过选择“继续”，承认提供商的使用条款和隐私政策****。 你可能首先需要提供联系信息。
1. 选择你的订阅和要将目标设备附加到的 IoT 中心。
1. 选择“部署到设备”****。
1. 输入设备的名称，或选择“查找设备”以浏览注册到中心的设备****。
1. 选择“创建”以继续配置部署清单的标准过程，包括根据需要添加其他模块****。 新模块的详细信息（例如映像 URI、创建选项以及所需的属性）已预定义，但可以更改。

验证该模块是否已部署在 Azure 门户的 IoT 中心。 选择设备，选择”设置模块“，该模块应列在“IoT Edge 模块”部分中********。

### <a name="deploy-from-azure-iot-hub"></a>从 Azure IoT 中心部署

可以将 Azure 市场中的模块快速部署到 Azure 门户的 IoT 中心的设备上。

1. 在 Azure 门户中，导航到 IoT 中心。
1. 在左窗格的“自动设备管理”下，选择“IoT Edge”********。
1. 选择要接收部署的 IoT Edge 设备。
1. 在上方栏中，选择“设置模块”****。
1. 在“IoT Edge 模块”**** 部分中，单击“添加”****，然后从下拉菜单中选择“市场模块”****。

![在 IoT 中心添加模块](./media/how-to-deploy-modules-portal/iothub-add-module.png)

从“IoT Edge 模块市场”页面选择一个模块****。 系统会自动为你的订阅、资源组和设备配置所选的模块。 然后，所选模块将显示在 IoT Edge 模块列表中。 某些模块可能需要其他配置。

> [!TIP]
> Azure IoT 中心提供的有关 IoT Edge 模块的信息是有限的。 你可以先在 Azure 市场中了解有关 [IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)的详细信息。

选择 **"下一步：路由**"，然后按照本文前面文中[指定路由](#specify-routes)和[查看部署](#review-deployment)所述继续部署。

## <a name="next-steps"></a>后续步骤

了解如何[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)
