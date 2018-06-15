---
title: 在 Azure IoT Edge 上部署 Modbus | Microsoft Docs
description: 通过创建 IoT Edge 网关设备，允许设备使用 Modbus TCP 与 Azure IoT 中心通信
author: kgremban
manager: timlt
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: kgremban
ms.openlocfilehash: d6e831cdd7cd18305beb617693ee1026bb6429e9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34631456"
---
# <a name="connect-modbus-tcp-devices-through-an-iot-edge-device-gateway---preview"></a>通过 IoT Edge 设备网关连接 Modbus TCP 设备 - 预览

若要将使用 Modbus TCP 或 RTU 协议的 IoT 设备连接到 Azure IoT 中心，请使用 IoT Edge 设备作为网关。 此网关设备从 Modbus 设备读取数据，然后使用支持的协议将该数据传送到云。 

![Modbus 设备通过 Edge 网关连接到 IoT 中心](./media/deploy-modbus-gateway/diagram.png)

本文介绍如何针对 Modbus 模块创建自己的容器映像（也可使用预构建的示例），然后将其部署到会充当网关的 IoT Edge 设备。 

本文假定你使用的是 Modbus TCP 协议。 若要详细了解如何配置支持 Modbus RTU 的模块，请参阅 Github 上的 [Azure IoT Edge Modbus 模块](https://github.com/Azure/iot-edge-modbus)项目。 

## <a name="prerequisites"></a>先决条件
* Azure IoT Edge 设备。 若要详细了解如何设置一个，请参阅[在 Windows 中将 Azure IoT Edge 部署到模拟的设备](tutorial-simulate-device-windows.md)或[在 Linux 中将 Azure IoT Edge 部署到模拟的设备](tutorial-simulate-device-linux.md)。 
* IoT Edge 设备的主键连接字符串。
* 支持 Modbus TCP 的物理或模拟 Modbus 设备。

## <a name="prepare-a-modbus-container"></a>准备 Modbus 容器

若要测试 Modbus 网关功能，可以使用 Microsoft 提供的示例模块。 若要使用示例模块，请转到[运行解决方案](#run-the-solution)部分，输入以下内容作为“映像 URI”： 

```URL
microsoft/azureiotedge-modbus-tcp:1.0-preview
```

如果需要创建自己的模块并根据环境对其自定义，可以使用 Github 上的开源 [Azure IoT Edge Modbus 模块](https://github.com/Azure/iot-edge-modbus)项目。 按照该项目中的指南创建自己的容器映像。 如果创建自己的容器映像，请参阅[开发和部署 C# IoT Edge 模块](tutorial-csharp-module.md)，了解如何将容器映像发布到注册表，以及如何将自定义模块部署到设备。 


## <a name="run-the-solution"></a>运行解决方案
1. 在 [Azure 门户](https://portal.azure.com/)中转到 IoT 中心。
2. 转到“IoT Edge (预览版)”，然后选择 IoT Edge 设备。
3. 选择“设置模块”。
4. 添加 Modbus 模块：
   1. 选择“添加 IoT Edge 模块”。
   2. 在“名称”字段中，输入“modbus”。
   3. 在“映像”字段中，输入示例容器的映像 URI：`microsoft/azureiotedge-modbus-tcp:1.0-preview`。
   4. 勾选“启用”框，更新模块孪生的所需属性。
   5. 将以下 JSON 复制到文本框中。 将 **SlaveConnection** 的值更改为 Modbus 设备的 IPv4 地址。

      ```JSON
      {  
        "properties.desired":{  
          "PublishInterval":"2000",
          "SlaveConfigs":{  
            "Slave01":{  
              "SlaveConnection":"<IPV4 address>",
              "HwId":"PowerMeter-0a:01:01:01:01:01",
              "Operations":{  
                "Op01":{  
                  "PollingInterval": "1000",
                  "UnitId":"1",
                  "StartAddress":"400001",
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
7. 在“指定路由”步骤中，将以下 JSON 复制到文本框中。 此路由将 Modbus 模块收集的所有消息发送到 IoT 中心。 在此路由中，“modbusOutput”是 Modbus 模块用于输出数据的终结点，“upstream”则是一个特殊目标，告知 Edge 中心将消息发送到 IoT 中心。 
   ```JSON
   {
    "routes": {
      "modbusToIoTHub":"FROM /messages/modules/modbus/outputs/modbusOutput INTO $upstream"
    }
   }
   ```

8. 选择“**下一步**”。 
9. 在“审阅模板”步骤中，选择“提交”。 
10. 返回到“设备详细信息”页，并选择“刷新”。 此时会看到新的 **modbus** 与 IoT Edge 运行时一起运行。

## <a name="view-data"></a>查看数据
查看通过 modbus 模块发送过来的数据：
```cmd/sh
docker logs -f modbus
```

还可使用 [IoT 中心资源管理器工具](https://github.com/azure/iothub-explorer)查看设备正在发送的遥测。 

## <a name="next-steps"></a>后续步骤

- 若要详细了解如何让 IoT Edge 设备充当网关，请参阅[创建充当透明网关的 IoT Edge 设备](how-to-create-transparent-gateway.md)
- 有关 IoT Edge 模块工作原理的详细信息，请参阅[了解 Azure IoT Edge 模块](iot-edge-modules.md)