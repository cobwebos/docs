---
title: 部署 Azure IoT Edge 模块（门户）| Microsoft Docs
description: 使用 Azure 门户将模块部署到 IoT Edge 设备
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4082189d451f670c1ae3f76b8ec785d8bd0518b3
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034370"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>通过 Azure 门户部署 Azure IoT Edge 模块

使用业务逻辑创建 IoT Edge 模块后，需要将其部署到设备后才能在边缘操作。 如果多个模块共同协作来收集和处理数据，可同时部署它们并声明用于连接它们的路由规则。 

本文介绍了 Azure 门户如何引导创建部署清单并将部署推送给 IoT Edge 设备。 要了解如何创建基于设备的共享标记而面向多台设备的部署，请参阅[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。 
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device-portal.md)。 

## <a name="select-your-device"></a>选择设备

1. 登录 [Azure 门户](https://portal.azure.com)，导航到 IoT 中心。
2. 从菜单中选择 IoT Edge。
3. 在设备列表中单击目标设备的 ID。 
4. 选择“设置模块”。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的必需属性。 要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

Azure 门户提供部署清单的创建向导，无需你手动构建 JSON 文档。 创建分为三步：添加模块、指定路由和评审部署。 

### <a name="add-modules"></a>添加模块

1. 在页面的“注册表设置”部分，提供用于任何访问包含模块映像的专用容器注册表的凭据。 
2. 在页面的“部署模块”部分中，选择“添加”。 
3. 在下拉列表中选择模块类型： 
   * **IoT Edge 模块** - 默认选项。
   * **Azure 流分析模块** - 仅限通过 Azure 流分析工作负荷生成的模块。 

4. 提供模块名称，然后指定容器映像。 例如： 
   * **名称** - tempSensor
   * **映像 URI** - mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
5. 必要时请填写可选字段。 要详细了解容器创建选项、重启策略和所需状态，请参阅 [EdgeAgent 必需属性](module-edgeagent-edgehub.md#edgeagent-desired-properties)。 要详细了解模块孪生，请参阅[定义或更新所需属性](module-composition.md#define-or-update-desired-properties)。
6. 选择“保存”。
7. 重复步骤 2-6，将其他模块添加到部署。 
8. 选择“下一步”转到路由部分。

### <a name="specify-routes"></a>指定路由

向导默认提供名为“route”且定义为 FROM /\* INTO $upstream** 的路由，这表示任何模块输出的任何消息都将发送到 IoT 中心。  

添加路由或使用[声明路由](module-composition.md#declare-routes)中的信息进行更新，然后选择“下一步”转到评审部分。

### <a name="review-deployment"></a>评审部署

评审部分介绍了根据上述两部分中的选择所创建的 JSON 部署清单。 请注意，$edgeAgent 和 $edgeHub 模块已声明但却未添加。 这两个模块构成 [IoT Edge 运行时](iot-edge-runtime.md)，且是每个部署中所需的默认设置。 

审阅部署信息，然后选择“提交”。 

## <a name="view-modules-on-your-device"></a>查看设备上的模块

将模块部署到设备之后，即可在门户的“设备详细信息”页中查看所有模块。 此页面显示每个已部署模块的名称，以及部署状态和退出代码等有用信息。 

## <a name="next-steps"></a>后续步骤

了解如何[大规模地部署和监视 IoT Edge 模块](how-to-deploy-monitor.md)
