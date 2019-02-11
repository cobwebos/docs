---
title: 将 Raspberry Pi 连接到 Azure IoT Central 应用程序 (Python) | Microsoft Docs
description: 如何以设备开发者的身份使用 Python 将 Raspberry Pi 连接到 Azure IoT Central 应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 9f39832b50ed983e7d8a0bfc0a06366870717fa3
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2019
ms.locfileid: "54051979"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>将 Raspberry Pi 连接到 Azure IoT Central 应用程序 (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

本文介绍如何以设备开发者的身份使用 Python 编程语言将 Raspberry Pi 连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下组件：

* 基于“示例 Devkit”应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
* 运行 Raspbian 操作系统的 Raspberry Pi 设备。 需要将监视器、键盘和鼠标连接到 Raspberry Pi 才能访问 GUI 环境。 Raspberry Pi 必须能够[连接到 Internet](https://www.raspberrypi.org/learning/software-guide/wifi/)。
* 适用于 Raspberry Pi 的 [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) 加载板（可选）。 此板从各种传感器收集遥测数据，然后将其发送到 Azure IoT Central 应用程序。 如果没有 Sense Hat 板，可以改用仿真器（作为 Raspberry Pi 映像的一部分提供）。

## <a name="sample-devkits-application"></a>**示例 Devkits** 应用程序

从“示例 Devkit”应用程序模板创建的应用程序包含一个具有以下特征的 Raspberry Pi 设备模板： 

- 遥测数据，包括设备将收集的以下度量值：
    - 湿度
    - 温度
    - 压力
    - 磁力计 (X, Y, Z)
    - 加速计 (X, Y, Z)
    - 陀螺仪 (X, Y, Z)
- 设置
    - 电压
    - 当前
    - 风扇速度
    - 红外开关。
- 属性
    - “模具编号”设备属性
    - “位置”云属性

有关设备模板配置的完整详细信息，请参阅 [Raspberry PI 设备模板详细信息](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)
    

## <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，从 Raspberry Pi 设备模板添加真实设备，并记录设备连接详细信息（作用域 ID、设备 ID 和主密钥）。 有关详细信息，请参阅[将真实设备添加到 Azure IoT Central 应用程序](tutorial-add-device.md)。


### <a name="configure-the-raspberry-pi"></a>配置 Raspberry Pi

以下步骤介绍如何从 GitHub 下载示例 Python 应用程序并对其进行配置。 此示例应用程序可以：

* 将遥测数据和属性值发送到 Azure IoT Central。
* 响应在 Azure IoT Central 中做的设置更改。

若要配置设备，请[按照 GitHub 上的分布说明进行操作](https://aka.ms/iotcentral-docs-Raspi-releases)。


> [!NOTE]
> 有关 Raspberry Pi Python 示例的详细信息，请参阅 GitHub 上的[自述](https://aka.ms/iotcentral-docs-Raspi-releases)文件。


1. 设备配置后，你的设备应该很快就会开始将数据发送到 Azure IoT Central。
1. 在 Azure IoT Central 应用程序中，可以看到在 Raspberry Pi 上运行的代码如何与应用程序交互：

    * 在真实设备的“度量”页上，可以看到从 Raspberry Pi 发送的遥测数据。 如果使用的是 Sense HAT 仿真器，则可在 Raspberry Pi 上的 GUI 中修改遥测值。
    * 在“属性”页上，可以看到报告的“模具号”属性的值。
    * 在“设置”页中，可以更改 Raspberry Pi 上的各种设置，例如电压和风扇速度。 当 Raspberry Pi 确认所做的更改时，该设置会在 Azure IoT Central 中显示为“已同步”。


## <a name="raspberry-pi-device-template-details"></a>Raspberry PI 设备模板详细信息

从“示例 Devkit”应用程序模板创建的应用程序包含一个具有以下特征的 Raspberry Pi 设备模板：

### <a name="telemetry-measurements"></a>遥测数据度量

| 字段名     | 单位  | 最小值 | 最大值 | 小数位数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| 压力       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>设置

数字设置

| 显示名称 | 字段名 | 单位 | 小数位数 | 最小值 | 最大值 | 初始 |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 电压      | setVoltage | 伏 | 0              | 0       | 240     | 0       |
| 当前      | setCurrent | 安培  | 0              | 0       | 100     | 0       |
| 风扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

切换设置

| 显示名称 | 字段名 | 打开文本 | 关闭文本 | 初始 |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | OFF      | 关闭     |

### <a name="properties"></a>属性

| 类型            | 显示名称 | 字段名 | 数据类型 |
| --------------- | ------------ | ---------- | --------- |
| 设备属性 | 模具号   | dieNumber  | 数字    |
| 文本            | 位置     | 位置   | 不适用       |

## <a name="next-steps"></a>后续步骤

了解如何将 Raspberry Pi 连接到 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

* [将泛型 Node.js 客户端应用程序连接到 Azure IoT Central](howto-connect-nodejs.md)
