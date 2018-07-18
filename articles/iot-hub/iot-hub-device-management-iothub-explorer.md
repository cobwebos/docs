---
title: 使用 iothub-explorer 进行 Azure IoT 设备管理 | Microsoft Docs
description: 使用 iothub-explorer CLI 工具进行 Azure IoT 中心设备管理，该特点是使用直接方法并提供孪生的所需属性管理选项。
author: rangv
manager: nasing
keywords: azure iot 设备管理, azure iot 中心设备管理, 设备管理 iot, iot 中心设备管理
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 4/11/2018
ms.author: rangv
ms.openlocfilehash: 0603976312ecab4f6974f51f0e181d2438681255
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634193"
---
# <a name="use-iothub-explorer-for-azure-iot-hub-device-management"></a>使用 iothub-explorer 进行 Azure IoT 中心设备管理

![端到端关系图](media/iot-hub-get-started-e2e-diagram/2.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[iothub-explorer](https://github.com/azure/iothub-explorer) 是一种在主机上运行的 CLI 工具，用于管理 IoT 中心注册表中的设备标识。 它附带了可用于执行各种任务的管理选项。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

| 管理选项          | 任务                                                                                                                            |
|----------------------------|------------------------------------------------------------------------------------------------------------------------------|
| 直接方法             | 让设备执行操作，如开始或停止发送消息或重新启动设备。                                        |
| 孪生所需属性    | 让设备进入特定状态，例如将 LED 设置为绿色，或将遥测发送间隔设置为 30 分钟。         |
| 孪生报告属性   | 获取报告的设备状态。 例如，设备报告 LED 现在正在闪烁。                                    |
| 孪生标记                  | 将设备特定的元数据存储在云中。 例如，存储在自动售货机的部署位置。                         |
| 云到设备的消息   | 向设备发送通知。 例如，“今天很可能会下雨。 不要忘记带雨伞。”              |
| 设备孪生查询        | 查询所有设备孪生以检索符合任意条件的设备孪生，例如确定可供使用的设备。 |

有关这些选项的差异和使用指导的更详细说明，请参阅[设备到云通信指南](iot-hub-devguide-d2c-guidance.md)和[云到设备通信指南](iot-hub-devguide-c2d-guidance.md)。

设备孪生是存储设备状态信息（元数据、配置和条件）的 JSON 文档。 IoT 中心为连接到它的每台设备保留一个设备孪生。 有关设备孪生的详细信息，请参阅[设备孪生入门](iot-hub-node-node-twin-getstarted.md)。

## <a name="what-you-learn"></a>学习内容

学习在开发计算机上如何使用 iothub-explorer 和各种管理选项。

## <a name="what-you-do"></a>准备工作

使用各种管理选项运行 iothub-explorer。

## <a name="what-you-need"></a>所需条件

- 已完成教程[设置设备](iot-hub-raspberry-pi-kit-node-get-started.md)，其中涵盖以下要求：
- 一个有效的 Azure 订阅。
- 已在订阅中创建一个 Azure IoT 中心。
- 一个可向 Azure IoT 中心发送消息的客户端应用程序。
- 在学习本教程期间，确保设备与客户端应用程序均处于运行状态。
- iothub-explorer, 在开发计算机上[安装 iothub-explorer](https://github.com/azure/iothub-explorer)。

## <a name="connect-to-your-iot-hub"></a>连接到 IoT 中心

通过运行以下命令连接到 IoT 中心：

```bash
iothub-explorer login <your IoT hub connection string>
```

## <a name="use-iothub-explorer-with-direct-methods"></a>结合使用 iothub-explorer 和直接方法

通过运行以下命令在设备应用中调用 `start` 方法以向 IoT 中心发送消息：

```bash
iothub-explorer device-method <your device Id> start
```

通过运行以下命令在设备应用中调用 `stop` 方法以停止向 IoT 中心发送消息：

```bash
iothub-explorer device-method <your device Id> stop
```

## <a name="use-iothub-explorer-with-twins-desired-properties"></a>结合使用 iothub-explorer 和孪生的所需属性

通过运行以下命令将所需属性间隔设置为 3000：

```bash
iothub-explorer update-twin <your device id> {\"properties\":{\"desired\":{\"interval\":3000}}}
```

可通过设备读取此属性。

## <a name="use-iothub-explorer-with-twins-reported-properties"></a>结合使用 iothub-explorer 和孪生的报告属性

通过运行以下命令获取报告的设备属性：

```bash
iothub-explorer get-twin <your device id>
```

其中一个属性是 $metadata.$lastUpdated，它显示该设备上次发送或接收消息的时间。

## <a name="use-iothub-explorer-with-twins-tags"></a>结合使用 iothub-explorer 和孪生的标记

通过运行以下命令显示设备的标记和属性：

```bash
iothub-explorer get-twin <your device id>
```

通过运行以下命令向设备添加字段角色 = 温度和湿度：

```bash
iothub-explorer update-twin <your device id> "{\"tags\":{\"role\":\"temperature&humidity\"}}"
```

## <a name="use-iothub-explorer-with-cloud-to-device-messages"></a>使用 iothub-explorer 发送云到设备的消息

运行以下命令，将“Hello World”消息发送到设备：

```bash
iothub-explorer send <device-id> "Hello World"
```

有关使用此命令的真实应用场景，请参阅[使用 iothub-explorer 在设备与 IoT 中心之间发送和接收消息](iot-hub-explorer-cloud-device-messaging.md)。

## <a name="use-iothub-explorer-with-device-twins-queries"></a>将 iothub-explorer 用于设备孪生查询

通过运行以下命令查询角色标记 =“温度和湿度”的设备：

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role = 'temperature&humidity'"
```

通过运行以下命令查询除角色标记 =“温度和湿度”的设备以外的所有设备：

```bash
iothub-explorer query-twin "SELECT * FROM devices WHERE tags.role != 'temperature&humidity'"
```

## <a name="next-steps"></a>后续步骤

现已了解如何结合使用 iothub-explorer 和各种管理选项。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
