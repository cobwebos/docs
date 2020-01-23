---
title: 从 Azure 门户部署模块 Azure IoT Edge
description: 使用 Azure 门户中的 IoT 中心将 IoT Edge 模块从 IoT 中心推送到 IoT Edge 设备，由部署清单进行配置。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 12/30/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5c44561895bc1905328ec0eb357bee1c68a8eb55
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76510526"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>通过 Azure 门户部署 Azure IoT Edge 模块

使用业务逻辑创建 IoT Edge 模块后，需要将其部署到设备后才能在边缘操作。 如果多个模块共同协作来收集和处理数据，可同时部署它们并声明用于连接它们的路由规则。

本文介绍了 Azure 门户如何引导创建部署清单并将部署推送给 IoT Edge 设备。 有关创建基于共享标记的多个设备的部署的详细信息，请参阅[大规模部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)。

## <a name="prerequisites"></a>必备组件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device.md#register-in-the-azure-portal)。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 若要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

Azure 门户提供部署清单的创建向导，无需你手动构建 JSON 文档。 创建分为三步：添加模块、指定路由和评审部署。

### <a name="select-device-and-add-modules"></a>选择设备并添加模块

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 在左侧窗格中，从菜单中选择 " **IoT Edge** "。
1. 在设备列表中单击目标设备的 ID。
1. 在上方栏中，选择“设置模块”。
1. 在页面的 "**容器注册表设置**" 部分中，提供用于访问任何包含模块映像的私有容器注册表的凭据。
1. 在该页的 " **IoT Edge 模块**" 部分中，选择 "**添加**"。
1. 从下拉菜单中查看模块类型：

   * **IoT Edge 模块**-提供模块名称和容器映像 URI。 例如，`mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`示例 SimulatedTemperatureSensor 模块的图像 URI。 如果模块映像存储在专用容器注册表中，请在此页上添加凭据来访问映像。
   * **Marketplace 模块**-托管在 Azure marketplace 中的模块。 某些 marketplace 模块需要其他配置，因此请查看[Azure marketplace IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)列表中的模块详细信息。
   * **Azure 流分析模块**-从 Azure 流分析工作负荷生成的模块。

1. 添加模块后，从列表中选择模块名称以打开模块设置。 必要时请填写可选字段。 要详细了解容器创建选项、重启策略和所需状态，请参阅 [EdgeAgent 必需属性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。 要详细了解模块孪生，请参阅[定义或更新所需属性](module-composition.md#define-or-update-desired-properties)。
1. 如果需要，请重复步骤5到步骤8，将其他模块添加到部署。
1. 选择 "**下一步：** 继续路由" 部分。

### <a name="specify-routes"></a>指定路由

在“路由”选项卡中，定义消息在模块和 IoT 中心之间传递的方式。 消息由名称/值对构造。 默认情况下，路由称为**route** ，并定义为**从/messages/\* 到 $upstream**中，这意味着任何模块输出的所有消息都将发送到 IoT 中心。  

添加或更新包含[声明路由](module-composition.md#declare-routes)中的信息的路由，然后选择 "**下一步：查看 + 创建**" 以继续执行向导的下一步骤。

### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 请注意，$edgeAgent 和 $edgeHub 模块已声明但却未添加。 这两个模块构成 [IoT Edge 运行时](iot-edge-runtime.md)，且是每个部署中所需的默认设置。

查看部署信息，然后选择 "**创建**"。

## <a name="view-modules-on-your-device"></a>查看设备上的模块

将模块部署到设备后，可以在 IoT 中心的 "设备详细信息" 页中查看这些模块。 此页面显示每个已部署模块的名称，以及部署状态和退出代码等有用信息。

## <a name="deploy-modules-from-azure-marketplace"></a>从 Azure 市场部署模块

[Azure marketplace](https://azuremarketplace.microsoft.com/)是一个在线应用程序和服务市场，你可以在其中浏览各种企业应用程序和解决方案，这些应用程序和解决方案经过认证和优化，可在 Azure 上运行，包括[IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。

你可以从 Azure Marketplace 和 IoT 中心部署 IoT Edge 模块。

### <a name="deploy-from-azure-marketplace"></a>从 Azure 市场部署

细读应用商店中的 IoT Edge 模块，找到所需的模块后，可以通过选择 "**创建**" 或 "**立即获取**" 来部署它。 继续执行部署向导步骤，具体取决于所选的 IoT Edge 模块：

1. 通过选择“继续”，承认提供商的使用条款和隐私政策。 您可能首先需要提供联系信息。
1. 选择你的订阅和要将目标设备附加到的 IoT 中心。
1. 选择“部署到设备”。
1. 输入设备的名称，或选择“查找设备”以浏览注册到中心的设备。
1. 选择“创建”以继续配置部署清单的标准过程，包括根据需要添加其他模块。 新模块的详细信息（例如映像 URI、创建选项以及所需的属性）已预定义，但可以更改。

验证是否已在 Azure 门户的 IoT 中心部署该模块。 选择设备，选择 "**设置模块**"，模块应在**IoT Edge 模块**"部分中列出。

### <a name="deploy-from-azure-iot-hub"></a>从 Azure IoT 中心部署

可以在 Azure 门户的 IoT 中心内将模块从 Azure Marketplace 快速部署到设备。

1. 在 Azure 门户中，导航到 IoT 中心。
1. 在左窗格中的 "**自动设备管理**" 下，选择**IoT Edge**。
1. 选择要接收部署的 IoT Edge 设备。
1. 在上方栏中，选择“设置模块”。
1. 在 " **IoT Edge 模块**" 部分中，单击 "**添加**"，然后从下拉菜单中选择 " **Marketplace 模块**"。

![在 IoT 中心添加模块](./media/how-to-deploy-modules-portal/iothub-add-module.png)

从 " **IoT Edge 模块市场**" 页中选择模块。 系统会自动为您的订阅、资源组和设备配置所选的模块。 然后，它将显示在 IoT Edge 模块列表中。 某些模块可能需要其他配置。

> [!TIP]
> Azure IoT 中心 IoT Edge 模块的相关信息受到限制。 你可以先详细了解 Azure Marketplace 中的[IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。

选择 "**下一步"：** 按本文前面[指定路由](#specify-routes)和[评审部署](#review-deployment)中所述，按路由和继续部署。

## <a name="next-steps"></a>后续步骤

了解如何[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)
