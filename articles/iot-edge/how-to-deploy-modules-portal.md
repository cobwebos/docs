---
title: 从 Azure 门户部署模块 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure 门户将模块部署到 IoT Edge 设备
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6d915145e64a5f1a097f38cf79b19426c3acbaf2
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457425"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>通过 Azure 门户部署 Azure IoT Edge 模块

使用业务逻辑创建 IoT Edge 模块后，需要将其部署到设备后才能在边缘操作。 如果多个模块共同协作来收集和处理数据，可同时部署它们并声明用于连接它们的路由规则。

本文介绍了 Azure 门户如何引导创建部署清单并将部署推送给 IoT Edge 设备。 要了解如何创建基于设备的共享标记而面向多台设备的部署，请参阅[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device.md#register-in-the-azure-portal)。

## <a name="select-your-device"></a>选择设备

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
1. 从菜单中选择“IoT Edge”。
1. 在设备列表中单击目标设备的 ID。
1. 选择“设置模块”。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

Azure 门户提供部署清单的创建向导，无需你手动构建 JSON 文档。 创建分为三步：添加模块、指定路由和评审部署。

### <a name="add-modules"></a>添加模块

1. 在此页的“容器注册表设置”部分，提供用于访问包含模块映像的任何专用容器注册表的凭据。

1. 在此页的“部署模块”部分中，选择“添加”。

1. 在下拉列表中找到模块类型：

   * **IoT Edge 模块** - 默认选项。
   * **Azure 流分析模块** - 仅限通过 Azure 流分析工作负荷生成的模块。
   * **Azure 机器学习模块** - 仅限从 Azure 机器学习工作区生成的模型映像。

1. 选择“IoT Edge 模块”。

1. 提供模块名称，然后指定容器映像。 例如：

   * **名称** - SimulatedTemperatureSensor
   * **映像 URI** - mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. 必要时请填写可选字段。 要详细了解容器创建选项、重启策略和所需状态，请参阅 [EdgeAgent 必需属性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。 要详细了解模块孪生，请参阅[定义或更新所需属性](module-composition.md#define-or-update-desired-properties)。

1. 选择“保存”。

1. 重复步骤 2-6，将其他模块添加到部署。

1. 选择“下一步”转到路由部分。

### <a name="specify-routes"></a>指定路由

默认情况下，向导提供名为**route**且定义为“FROM /*INTO $upstream** **的路由。也就是说，任意模块输出的任何消息都发送到 IoT 中心。  

在路由中添加或更新[声明路由](module-composition.md#declare-routes)中的信息，再选择“下一步”继续转到评审部分。

### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 请注意，$edgeAgent 和 $edgeHub 模块已声明但却未添加。 这两个模块构成 [IoT Edge 运行时](iot-edge-runtime.md)，且是每个部署中所需的默认设置。

审阅部署信息，然后选择“提交”。

## <a name="view-modules-on-your-device"></a>查看设备上的模块

将模块部署到设备之后，即可在门户的“设备详细信息”页中查看所有模块。 此页面显示每个已部署模块的名称，以及部署状态和退出代码等有用信息。

## <a name="deploy-modules-from-azure-marketplace"></a>从 Azure 市场部署模块

Azure 市场是一个应用程序和服务在线市场，可在其中浏览各种企业应用程序和解决方案，这些应用程序和解决方案针对在 Azure 上运行进行了认证和优化，包括 [IoT Edge 模块](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)。 还可以在“创建资源”下通过 Azure 门户访问 Azure 市场。

可以从 Azure 市场或 Azure 门户安装 IoT Edge 模块：

1. 查找模块并开始部署过程。

   * Azure 门户：查找模块并选择 "**创建**"。

   * Azure 市场：

     1. 查找模块，然后选择“立即获取”。
     1. 通过选择“继续”，承认提供商的使用条款和隐私政策。

1. 选择你的订阅和要将目标设备附加到的 IoT 中心。

1. 选择“部署到设备”。

1. 输入设备的名称，或选择“查找设备”以浏览注册到中心的设备。

1. 选择“创建”以继续配置部署清单的标准过程，包括根据需要添加其他模块。 新模块的详细信息（例如映像 URI、创建选项以及所需的属性）已预定义，但可以更改。

## <a name="next-steps"></a>后续步骤

了解如何[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)
