---
title: 将 Raspberry Pi 连接到 Azure IoT Central 应用程序 (Python) | Microsoft Docs
description: 作为设备开发人员，如何将 Raspberry Pi 连接到 Azure IoT Central 应用程序使用 Python。
author: dominicbetts
ms.author: dobett
ms.date: 04/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: eccc4100c89c971e264b9b915cd17b9f5ce4477b
ms.sourcegitcommit: fec96500757e55e7716892ddff9a187f61ae81f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59617437"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>将 Raspberry Pi 连接到 Azure IoT Central 应用程序 (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

本文介绍如何以设备开发者的身份使用 Python 编程语言将 Raspberry Pi 连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下组件：

* 基于“示例 Devkit”应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
* 运行 Raspbian 操作系统的 Raspberry Pi 设备。 在 Raspberry Pi 必须能够连接到 internet。 有关详细信息，请参阅[设置 Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)。

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

设备模板的配置的完整详细信息，请参阅[Raspberry Pi 设备模板详细信息](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)。

## <a name="add-a-real-device"></a>添加真实设备

Azure IoT Central 应用程序中添加真实的设备，从**Raspberry Pi**设备模板。 请记下的设备连接详细信息 (**作用域 ID**，**设备 ID**，并**主键**)。 有关详细信息，请参阅[将真实设备添加到 Azure IoT Central 应用程序](tutorial-add-device.md)。

### <a name="configure-the-raspberry-pi"></a>配置 Raspberry Pi

以下步骤介绍如何从 GitHub 下载示例 Python 应用程序并对其进行配置。 此示例应用程序可以：

* 将遥测数据和属性值发送到 Azure IoT Central。
* 响应在 Azure IoT Central 中做的设置更改。

若要配置的设备[按照 GitHub 上的分步说明](https://github.com/Azure/iot-central-firmware/blob/master/RaspberryPi/README.md)。

1. 配置设备后，你的设备开始将遥测度量发送到 Azure IoT 中心。
1. 在 Azure IoT Central 应用程序中，可以看到在 Raspberry Pi 上运行的代码如何与应用程序交互：

    * 在真实设备的“度量”页上，可以看到从 Raspberry Pi 发送的遥测数据。
    * 上**设置**页上，你可以如电压和风扇速度在 Raspberry Pi 上的设置。 当在 Raspberry Pi 确认更改时，该设置显示了作为**同步**。

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi 设备模板详细信息

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

现在，已了解如何将 Raspberry Pi 连接到 Azure IoT Central 应用程序，建议下一步是了解如何[设置自定义设备模板](howto-set-up-template.md)IoT 设备。
