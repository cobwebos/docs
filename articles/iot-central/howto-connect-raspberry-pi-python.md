---
title: 将 Raspberry Pi 连接到 Azure IoT Central 应用程序 (Python) | Microsoft Docs
description: 作为设备开发人员，如何使用 Python 将 Raspberry Pi 连接到 Azure IoT Central 应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 5c6e99c62d199c36081113011845a3bf2607bcb3
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842556"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>将 Raspberry Pi 连接到 Azure IoT Central 应用程序 (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

本文介绍如何以设备开发者的身份使用 Python 编程语言将 Raspberry Pi 连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下组件：

* 基于“示例 Devkit”应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
* 运行 Raspbian 操作系统的 Raspberry Pi 设备。 Raspberry Pi 必须能够连接到 internet。 有关详细信息，请参阅[设置 Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3)。

> [!TIP]
> 若要了解如何设置并连接到 Raspberry Pi 设备，请访问[Raspberry pi 入门](https://projects.raspberrypi.org/en/pathways/getting-started-with-raspberry-pi)

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

有关设备模板配置的完整详细信息，请参阅[Raspberry Pi 设备模板详细信息](howto-connect-raspberry-pi-python.md#raspberry-pi-device-template-details)。

## <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，从**Raspberry Pi**设备模板添加一个实际设备。 记下设备连接详细信息（**作用域 id**、**设备 id**和**主密钥**）。 有关详细信息，请参阅[将真实设备添加到 Azure IoT Central 应用程序](tutorial-add-device.md)。

### <a name="configure-the-raspberry-pi"></a>配置 Raspberry Pi

以下步骤介绍如何从 GitHub 下载示例 Python 应用程序并对其进行配置。 此示例应用程序可以：

* 将遥测数据和属性值发送到 Azure IoT Central。
* 响应在 Azure IoT Central 中做的设置更改。

1. 使用 Raspberry Pi 桌面连接到 Raspberry Pi 上的 shell 环境，或使用 SSH 远程连接到 shell 环境。

1. 运行以下命令以安装 IoT Central Python 客户端：

    ```bash
    pip install iotc
    ```

1. 下载示例 Python 代码：

    ```bash
    curl -O https://raw.githubusercontent.com/Azure/iot-central-firmware/master/RaspberryPi/app.py
    ```

1. 编辑下载`app.py`的文件，并将`DEVICE_ID`、 `SCOPE_ID`和`PRIMARY/SECONDARY device KEY`占位符替换为之前记下的连接值。 保存更改。

    > [!TIP]
    > 在 Raspberry Pi 上的 shell 中，可以使用**nano**或**vi**文本编辑器。

1. 使用以下命令运行示例：

    ```bash
    python app.py
    ```

    你的 Raspberry Pi 开始将遥测度量发送到 Azure IoT Central。

1. 在 Azure IoT Central 应用程序中，可以看到在 Raspberry Pi 上运行的代码如何与应用程序交互：

    * 在真实设备的“度量”页上，可以看到从 Raspberry Pi 发送的遥测数据。
    * 在 "**属性**" 页上，可以看到 "**骰子编号**设备" 属性。
    * 在 "**设置**" 页上，可以更改 Raspberry Pi 上的设置，例如电压和风扇速度。 当 Raspberry Pi 确认变化时，该设置显示为 "已**同步**"。

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

现在，你已了解如何将 Raspberry Pi 连接到 Azure IoT Central 应用程序，接下来建议的下一步是了解如何为你自己的 IoT 设备[设置自定义设备模板](howto-set-up-template.md)。
