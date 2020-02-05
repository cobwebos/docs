---
title: Azure IoT Edge 和 Azure IoT Central |Microsoft Docs
description: 了解如何在 IoT Central 应用程序中使用 Azure IoT Edge。
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 69660152458de26e9dbcbf1f50db6ce6824351d0
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027064"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>将 Azure IoT Edge 设备连接到 Azure IoT Central 应用程序

IoT Edge 包含三个组件：

* IoT Edge 模块是容器，可以运行 Azure 服务、合作伙伴服务或者你自己的代码。 这些模块部署到 IoT Edge 设备，在设备上以本地方式运行。
* IoT Edge 运行时在每个 IoT Edge 设备上运行，并管理部署到每个设备的模块。
* 可以通过基于云的界面远程监视和管理 IoT Edge 设备。 IoT Central 是云接口。

Azure IoT Edge 设备可以是具有连接到 IoT Edge 设备的下游设备的网关设备。 本文共享有关下游设备连接模式的详细信息。

设备模板定义设备和 IoT Edge 模块的功能。 功能包括模块发送的遥测数据、模块属性以及模块响应的命令。

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>下游设备与网关和模块的关系

下游设备可通过 `$edgeHub` 模块连接到 IoT Edge 网关设备。 在此方案中，此 IoT Edge 设备将成为透明网关。

![透明网关关系图](./media/concepts-iot-edge/gateway-transparent.png)

下游设备也可通过自定义模块连接到 IoT Edge 网关设备。 在以下方案中，下游设备通过 Modbus 自定义模块进行连接。

![自定义模块连接关系图](./media/concepts-iot-edge/gateway-module.png)

下图显示了通过两种类型的模块（自定义和 `$edgeHub`）与 IoT Edge 网关设备的连接。  

![通过两种连接模块连接的关系图](./media/concepts-iot-edge/gateway-module-transparent.png)

最后，下游设备可通过多个自定义模块连接到 IoT Edge 网关设备。 下图显示了通过 Modbus 自定义模块、BLE 自定义模块和 `$edgeHub` 模块连接的下游设备。 

![通过多个自定义模块连接的关系图](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>部署清单和设备模板

在 IoT Edge 中，你能以模块的形式来部署和管理业务逻辑。 IoT Edge 模块是由 IoT Edge 托管的最小计算单位，可以包含 Azure 服务（如 Azure 流分析）或你自己特定于解决方案的代码。 若要了解如何开发、部署和维护模块，请参阅 [IoT Edge 模块](../../iot-edge/iot-edge-modules.md)。

从较高层面讲，部署清单是配置了所需属性的模块孪生的列表。 部署清单告知某个 IoT Edge 设备（或一组设备）要安装哪些模块，以及如何配置这些模块。 部署清单包含每个模块孪生的所需属性。 IoT Edge 设备将报告每个模块的报告属性。

使用 Visual Studio Code 创建部署清单。 若要了解详细信息，请参阅[适用于 Visual Studio Code 的 Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)。

在 Azure IoT Central 中，可以导入部署清单来创建设备模板。 以下流程图显示了 IoT Central 中的部署清单生命周期。

![部署清单生命周期的流程图](./media/concepts-iot-edge/dmflow.png)

IoT 即插即用（预览版）对 IoT Edge 设备建模，如下所示：

* 每个 IoT Edge 设备模板都具有设备功能模型。
* 对于部署清单中列出的每个自定义模块，都会生成模块功能模型。
* 在每个模块功能模型和设备功能模型之间建立关系。
* 模块功能模型可实现模块接口。
* 每个模块接口都包含遥测、属性和命令。

![IoT Edge 建模关系图](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>IoT Edge 网关设备

如果选择 IoT Edge 设备作为网关设备，则可以将下游关系添加到将连接到网关设备的设备的设备功能模型。

## <a name="next-steps"></a>后续步骤

了解 IoT Central 应用程序模板之后，可以通过[创建 IoT Central 应用程序](quick-deploy-iot-central.md)开始操作。