---
title: 使用网关转换 modbus 协议 - Azure IoT Edge | Microsoft Docs
description: 通过创建 IoT Edge 网关设备，允许设备使用 Modbus TCP 与 Azure IoT 中心通信
author: kgremban
manager: philmea
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: kgremban
ms.openlocfilehash: 649c7f620b83464d1bb56cf4b8191b0747105f01
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457218"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway"></a>通过 IoT Edge 设备网关连接 Modbus TCP 设备

若要将使用 Modbus TCP 或 RTU 协议的 IoT 设备连接到 Azure IoT 中心，请使用 IoT Edge 设备作为网关。 此网关设备从 Modbus 设备读取数据，然后使用支持的协议将该数据传送到云。

![Modbus devices connect to IoT Hub through IoT Edge gateway](./media/deploy-modbus-gateway/diagram.png)

本文介绍如何针对 Modbus 模块创建自己的容器映像（也可使用预构建的示例），然后将其部署到会充当网关的 IoT Edge 设备。

本文假定你使用的是 Modbus TCP 协议。 若要详细了解如何配置支持 Modbus RTU 的模块，请参阅 GitHub 上的 [Azure IoT Edge Modbus 模块](https://github.com/Azure/iot-edge-modbus)项目。

## <a name="prerequisites"></a>必备组件
* Azure IoT Edge 设备。 For a walkthrough on how to set up one, see [Deploy Azure IoT Edge on Windows](quickstart.md) or [Linux](quickstart-linux.md).
* IoT Edge 设备的主键连接字符串。
* 支持 Modbus TCP 的物理或模拟 Modbus 设备。

## <a name="prepare-a-modbus-container"></a>准备 Modbus 容器

若要测试 Modbus 网关功能，可以使用 Microsoft 提供的示例模块。 You can access the module from the Azure Marketplace, [Modbus](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot.edge-modbus?tab=Overview), or with the image URI, **mcr.microsoft.com/azureiotedge/modbus:1.0**.

如果需要创建自己的模块并根据环境对其自定义，可以使用 GitHub 上的开源 [Azure IoT Edge Modbus 模块](https://github.com/Azure/iot-edge-modbus)项目。 按照该项目中的指南创建自己的容器映像。 To create a container image, refer to [Develop C# modules in Visual Studio](how-to-visual-studio-develop-csharp-module.md) or [Develop modules in Visual Studio Code](how-to-vs-code-develop-module.md). Those articles provide instructions on creating new modules and publishing container images to a registry.

## <a name="try-the-solution"></a>Try the solution

This section walks through deploying Microsoft's sample Modbus module to your IoT Edge device.

1. 在 [Azure 门户](https://portal.azure.com/)中转到 IoT 中心。

2. 转到“IoT Edge”，然后单击 IoT Edge 设备。

3. 选择“设置模块”。

4. 添加 Modbus 模块：

   1. 单击“添加”，然后选择“IoT Edge 模块”。

   2. 在“名称”字段中，输入“modbus”。

   3. 在“映像”字段中，输入示例容器的映像URI：`mcr.microsoft.com/azureiotedge/modbus:1.0`。

   4. 勾选“启用”框，更新模块孪生的所需属性。

   5. 将以下 JSON 复制到文本框中。 将 **SlaveConnection** 的值更改为 Modbus 设备的 IPv4 地址。

      ```JSON
      {
        "properties.desired":{
          "PublishInterval":"2000",
          "SlaveConfigs":{
            "Slave01":{
              "SlaveConnection":"<IPV4 address>","HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{
                "Op01":{
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"40001",
                  "Count":"2",
                  "DisplayName":"Voltage"
                }
              }
            }
          }
        }
      }
      ```

   6. 选择“保存”。

5. 返回到“添加模块”步骤，选择“下一步”。

7. 在“指定路由”步骤中，将以下 JSON 复制到文本框中。 此路由将 Modbus 模块收集的所有消息发送到 IoT 中心。 In this route, **modbusOutput** is the endpoint that Modbus module uses to output data and **$upstream** is a special destination that tells IoT Edge hub to send messages to IoT Hub.

   ```JSON
   {
     "routes": {
       "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
     }
   }
   ```

8. 选择“**下一步**”。

9. 在“复查部署”步骤中，选择“提交”。

10. 返回到“设备详细信息”页，并选择“刷新”。 此时会看到新的 **modbus** 模块与 IoT Edge 运行时一起运行。

## <a name="view-data"></a>查看数据
查看通过 modbus 模块发送过来的数据：
```cmd/sh
iotedge logs modbus
```

也可使用[适用于 Visual Studio Code 的 Azure IoT 中心工具包扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)（以前称为 Azure IoT 工具包扩展）查看设备正发送的遥测数据。

## <a name="next-steps"></a>后续步骤

- To learn more about how IoT Edge devices can act as gateways, see [Create an IoT Edge device that acts as a transparent gateway](./how-to-create-transparent-gateway.md).
- For more information about how IoT Edge modules work, see [Understand Azure IoT Edge modules](iot-edge-modules.md).
