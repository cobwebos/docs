---
title: 使用网关转换 modbus 协议 - Azure IoT Edge | Microsoft Docs
description: 通过创建 IoT Edge 网关设备，允许设备使用 Modbus TCP 与 Azure IoT 中心通信
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: kgremban
ms.openlocfilehash: 23fbbd87230ea0a0147dc9d90c77729f4d531e98
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2020
ms.locfileid: "76511138"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>通过 IoT Edge 设备网关连接 Modbus TCP 设备

如果要将使用 Modbus TCP 或 RTU 协议的 IoT 设备连接到 Azure IoT 中心，可以使用 IoT Edge 设备作为网关。 此网关设备从 Modbus 设备读取数据，然后使用支持的协议将该数据传送到云。

![Modbus 设备通过 IoT Edge 网关连接到 IoT 中心](./media/deploy-modbus-gateway/diagram.png)

本文介绍如何针对 Modbus 模块创建自己的容器映像（也可使用预构建的示例），然后将其部署到会充当网关的 IoT Edge 设备。

本文假定你使用的是 Modbus TCP 协议。 若要详细了解如何配置支持 Modbus RTU 的模块，请参阅 GitHub 上的 [Azure IoT Edge Modbus 模块](https://github.com/Azure/iot-edge-modbus)项目。

## <a name="prerequisites"></a>必备组件

* Azure IoT Edge 设备。 有关如何设置一个的演练，请参阅在 Windows 或[Linux](quickstart-linux.md)[上部署 Azure IoT Edge](quickstart.md) 。
* IoT Edge 设备的主键连接字符串。
* 支持 Modbus TCP 的物理或模拟 Modbus 设备。 你将需要知道其 IPv4 地址。

## <a name="prepare-a-modbus-container"></a>准备 Modbus 容器

若要测试 Modbus 网关功能，可以使用 Microsoft 提供的示例模块。 可以通过 Azure Marketplace、 [Modbus](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview)或映像 URI `mcr.microsoft.com/azureiotedge/modbus:1.0`来访问模块。

如果需要创建自己的模块并根据环境对其自定义，可以使用 GitHub 上的开源 [Azure IoT Edge Modbus 模块](https://github.com/Azure/iot-edge-modbus)项目。 按照该项目中的指南创建自己的容器映像。 若要创建容器映像，请参阅[在C# Visual Studio 中开发模块](how-to-visual-studio-develop-csharp-module.md)或[开发 Visual Studio Code 中的模块](how-to-vs-code-develop-module.md)。 这些文章提供了有关在注册表中创建新模块和发布容器映像的说明。

## <a name="try-the-solution"></a>尝试解决方案

本部分逐步讲解如何将 Microsoft 的示例 Modbus 模块部署到 IoT Edge 设备。

1. 在 [Azure 门户](https://portal.azure.com/)中转到 IoT 中心。

2. 转到“IoT Edge”，然后单击 IoT Edge 设备。

3. 选择“设置模块”。

4. 在**IoT Edge 模块**"部分中，添加 Modbus 模块：

   1. 单击 "**添加**" 下拉模块并选择 " **Marketplace 模块**"。
   2. 搜索 "`Modbus`"，并选择 " **MODBUS TCP" 模块**"Microsoft"。
   3. 模块自动配置为 IoT 中心，并显示在 IoT Edge 模块列表中。 路由也会自动配置。 选择“查看 + 创建”。
   4. 查看部署清单，然后选择 "**创建**"。

5. 在列表中选择 "Modbus" 模块，`ModbusTCPModule`，然后选择 "**模块克隆设置**" 选项卡。自动填充模块所需的模块所需属性的 JSON。

6. 查找 JSON 中的**SlaveConnection**属性，并将其值设置为 Modbus 设备的 IPv4 地址。

7. 选择“更新”。

8. 依次选择 "查看" 和 "**创建**"，查看部署，然后选择 "**创建**"。

9. 返回到“设备详细信息”页，并选择“刷新”。 应该会看到与 IoT Edge 运行时一起运行的新 `ModbusTCPModule` 模块。

## <a name="view-data"></a>查看数据

查看通过 Modbus 模块的数据：

```cmd/sh
iotedge logs modbus
```

你还可以使用[适用于 Visual Studio Code 的 Azure Iot 中心扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)（以前称为 Azure iot 工具包扩展）查看设备正在发送的遥测。

## <a name="next-steps"></a>后续步骤

* 若要详细了解 IoT Edge 设备如何作为网关，请参阅[创建充当透明网关的 IoT Edge 设备](./how-to-create-transparent-gateway.md)。
* 有关 IoT Edge 模块的工作原理的详细信息，请参阅[了解 Azure IoT Edge 模块](iot-edge-modules.md)。
