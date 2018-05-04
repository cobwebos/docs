---
title: 设备模拟解决方案入门 - Azure | Microsoft Docs
description: IoT 套件模拟解决方案是一项工具，可以用来协助开发和测试 IoT 解决方案。 模拟服务是一项独立的服务，可以与其他预配置的解决方案或自己的自定义解决方案配合使用。
services: iot device simulation
suite: iot-suite
author: troyhopwood
manager: timlt
ms.author: troyhop
ms.service: iot-suite
ms.date: 12/15/2017
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 1452508822f08d4554f419a72f7e9e6018a52469
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="device-simulation-walkthrough"></a>设备模拟演练

Azure IoT 设备模拟是一项工具，可以用来协助开发和测试 IoT 解决方案。 设备模拟是一项独立的服务，可以与其他预配置的解决方案或自己的自定义解决方案配合使用。

本教程演示设备模拟的部分功能， 并介绍其工作原理，让你能够使用它来测试自己的 IoT 解决方案。

本教程介绍如何执行下列操作：

>[!div class="checklist"]
> * 配置模拟
> * 启动和停止模拟
> * 查看遥测指标

## <a name="prerequisites"></a>先决条件

若要完成本教程，需在 Azure 订阅中部署 Azure IoT 设备模拟的实例。

如果尚未部署设备模拟，则应完成[部署 Azure IoT 设备模拟](iot-suite-device-simulation-deploy.md)教程。

## <a name="configuring-device-simulation"></a>配置设备模拟

在仪表板中即可配置和运行设备模拟。 从 IoT 套件的[预配的解决方案](https://www.azureiotsuite.com/)页打开仪表板。 在新的设备模拟部署下单击“启动”。

### <a name="target-iot-hub"></a>以 IoT 中心为目标

可以将设备模拟与预配的 IoT 中心或任何其他 IoT 中心配合使用：

![目标 IoT 中心](media/iot-suite-device-simulation-explore/targethub.png)

> [!NOTE]
> 只有在部署设备模拟时选择创建新的 IoT 中心，系统才会提供使用预配的 IoT 中心的选项。 如果还没有 IoT 中心，则在 [Azure 门户](https://portal.azure.com)中新建一个即可。

若要以特定的 IoT 中心为目标，请提供 **iothubowner** 连接字符串。 可从 [Azure 门户](https://portal.azure.com)获取此连接字符串：

1. 在 Azure 门户的 IoT 中心配置页上，单击“共享访问策略”。
1. 单击“iothubowner”。
1. 复制主密钥或辅助密钥。
1. 将此值粘贴到“目标 IoT 中心”项下的文本框中。

![目标 IoT 中心](media/iot-suite-device-simulation-explore/connectionstring.png)

### <a name="device-model"></a>设备型号

可以通过设备型号来选择要模拟的设备类型。 可以选择一个预配置的设备型号，也可以针对自定义设备型号定义一个传感器列表：

![设备型号](media/iot-suite-device-simulation-explore/devicemodel.png)

#### <a name="pre-configured-device-models"></a>预配置的设备型号

设备模拟提供三个预配置的设备型号。 提供了冷却器、升降机和卡车的设备型号。

预配置的设备型号包括多个具有 JavaScript 文件中定义的高级行为的传感器。 Web UI 不支持这些自定义行为。 

下表显示了一系列配置，适用于各个预配置的设备型号：

| 设备型号 | 传感器 | 单位 | 
| -------------| ------ | -----| 
| 冷却器 | 湿度 | % |
| | 压强 | psig | 
| | 温度 | F | 
| 升降机 | 层 | 
| | 振动 | mm | 
| | 温度 | F | 
| 卡车 | 纬度 | |
| | 经度 | | 
| | 速度 | mph | 
| | cargotemperature | F | 

#### <a name="custom-device-model"></a>自定义设备型号

可以通过自定义设备型号来设置传感器的型号，使之更能代表自己的设备。 一个自定义设备可以有多达 10 个自定义传感器。

选择自定义设备型号时，可以通过单击“+ 添加传感器”添加新的传感器。

![添加传感器](media/iot-suite-device-simulation-explore/customsensors.png)

自定义传感器具有以下属性：

| 字段 | 说明 |
| ----- | ----------- |
| 传感器名称 | 传感器的易记名称，例如“温度”或“速度”。 |
| 行为 | 行为促使消息的遥测数据各不相同，这样是为了模拟真实世界的数据。 **递增**使发送的每条消息中的值加 1（从最小值开始）。 达到最大值以后，会从最小值重新开始。 **递减**的行为方式与**递增**相同，只是每次减 1。 **随机**行为生成一个介于最小值和最大值之间的随机值。 |
| 最小值 | 表示可接受范围的最小数字。 |
| 最大值 | 表示可接受范围的最大数字。 |
| 单位 | 传感器的度量单位，例如 °F 或 MPH。 |

### <a name="number-of-devices"></a>设备数

设备模拟目前允许模拟多达 20,000 个设备。

![设备数](media/iot-suite-device-simulation-explore/numberofdevices.png)

### <a name="telemetry-frequency"></a>遥测频率

遥测频率用于指定模拟设备将数据发送到 IoT 中心的频率。 可以频繁地发送数据，例如每 10 秒发送一次；也可以不这么频繁地发送数据，例如每 99 小时发送一次、每 59 分钟发送一次、每 59 秒发送一次。

![遥测频率](media/iot-suite-device-simulation-explore/frequency.png)

> [!NOTE]
> 如果使用的 IoT 中心不同于预配的 IoT 中心，则应考虑针对目标 IoT 中心的消息限制。 例如，如果有 1,000 个模拟设备每隔 10 秒向 S1 中心发送一次遥测数据，则一小时就会达到该中心的遥测数据限制。

### <a name="simulation-duration"></a>模拟持续时间

可以选择让模拟运行特定的时间长度，也可以让其一直运行到明确让其停止为止。 选择特定的时间长度时，可以选择从 10 分钟到 99 小时之间的任何时间长度，可以选择 59 分钟，也可以选择 59 秒。

![模拟持续时间](media/iot-suite-device-simulation-explore/duration.png)

### <a name="start-and-stop-the-simulation"></a>启动和停止模拟

向窗体添加所有必需的配置数据以后，“启动模拟”按钮就会启用。 若要启动模拟，请单击该按钮。

![启动模拟](media/iot-suite-device-simulation-explore/start.png)

如果为模拟指定了具体的持续时间，则该时间到了以后，模拟就会自动停止。 单击“停止模拟”就可以提前停止模拟。

如果选择无限期运行模拟，则除非单击“停止模拟”，否则该模拟会一直运行下去。 可以关闭浏览器，并且可以随时回到“设备模拟”页来停止模拟。

![停止模拟](media/iot-suite-device-simulation-explore/stop.png)

> [!NOTE]
> 一次只能运行一个模拟。 必须先停止当前正在运行的模拟，然后才能启动新的模拟。
