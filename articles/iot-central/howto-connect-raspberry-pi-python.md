---
title: 将 Raspberry Pi 连接到 Azure IoT Central 应用程序 (Python) | Microsoft Docs
description: 如何使用 Python 以设备开发人员的身份将 Raspberry Pi 连接到 Azure IoT Central 应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 01/23/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: e9c2d18a518bd5c98fcc35efdb0dff36970a49b2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34629059"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-python"></a>将 Raspberry Pi 连接到 Azure IoT Central 应用程序 (Python)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

本文介绍如何使用 Python 编程语言以设备开发人员的身份将 Raspberry Pi 连接到 Microsoft Azure IoT Central 应用程序。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下各项：

* 基于“示例 Devkit”应用程序模板创建的 Azure IoT Central 应用程序。 有关详细信息，请参阅[创建 Azure IoT Central 应用程序](howto-create-application.md)。
* 运行 Raspbian 操作系统的 Raspberry Pi 设备。 需要将监视器、键盘和鼠标连接到 Raspberry Pi 才能访问 GUI 环境。 Raspberry Pi 必须能够[连接到 Internet](https://www.raspberrypi.org/learning/software-guide/wifi/)。
* （可选）一个适用于 Raspberry Pi 的 [Sense Hat](https://www.raspberrypi.org/products/sense-hat/) 加载板。 此板从各种传感器收集遥测数据，然后将其发送到 Azure IoT Central 应用程序。 如果没有 **Sense Hat** 板，可以改用模拟器。

从“示例 Devkit”应用程序模板创建的应用程序包含一个具有以下特征的 **Raspberry Pi** 设备模板：

### <a name="telemetry-measurements"></a>遥测度量

| 字段名称     | 单位  | 最小值 | 最大值 | 小数位数 |
| -------------- | ------ | ------- | ------- | -------------- |
| 湿度       | %      | 0       | 100     | 0              |
| temp           | °C     | -40     | 120     | 0              |
| 压强       | hPa    | 260     | 1260    | 0              |
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

| 显示名称 | 字段名称 | 单位 | 小数位数 | 最小值 | 最大值 | Initial |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| 电压      | setVoltage | 伏 | 0              | 0       | 240     | 0       |
| Current      | setCurrent | 安培  | 0              | 0       | 100     | 0       |
| 风扇速度    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

切换设置

| 显示名称 | 字段名称 | 打开文本 | 关闭文本 | Initial |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | 亮起      | 熄灭      | 关闭     |

### <a name="properties"></a>属性

| Type            | 显示名称 | 字段名称 | 数据类型 |
| --------------- | ------------ | ---------- | --------- |
| 设备属性 | 模具编号   | dieNumber  | 数字    |
| 文本            | 位置     | location   | 不适用       |

### <a name="add-a-real-device"></a>添加真实设备

在 Azure IoT Central 应用程序中，从 **Raspberry Pi** 设备模板添加真实设备，并记下设备连接字符串。 有关详细信息，请参阅[向 Azure IoT Central 应用程序添加真实设备](tutorial-add-device.md)。

## <a name="configure-the-raspberry-pi"></a>配置 Raspberry Pi

以下步骤介绍如何从 GitHub 下载示例 Python 应用程序并对其进行配置。 此示例应用程序可以：

* 将遥测数据和属性值发送到 Azure IoT Central。
* 响应在 Azure IoT Central 中做的设置更改。

> [!NOTE]
> 有关 Raspberry Pi Python 示例的详细信息，请参阅 GitHub 上的[自述](https://github.com/Microsoft/microsoft-iot-central-firmware/blob/master/RaspberryPi/README.md)文件。

1. 在 Raspberry Pi 桌面上使用 Web 浏览器导航到 [Azure IoT Central 固件版本](https://github.com/Microsoft/microsoft-iot-central-firmware/releases)页面。

1. 将包含最新固件的 zip 文件下载到 Raspberry Pi 上的主文件夹。 文件名类似于 `RaspberryPi-IoTCentral-X.X.X.zip`。

1. 若要解压缩固件文件，请使用 Raspberry Pi 桌面中的“文件管理器”。 右键单击 zip 文件并选择“提取到此处”。 此操作在主文件夹中创建名为 `RaspberryPi-IoTCentral-X.X.X` 的文件夹。

1. 如果未将 **Sense Hat** 板附加到 Raspberry Pi，则需启用模拟器：
    1. 在“文件管理器”的 `RaspberryPi-IoTCentral-X.X.X` 文件夹中右键单击“config.iot”文件，然后选择“文本编辑器”。
    1. 将 `"simulateSenseHat": false,` 行更改为 `"simulateSenseHat": true,`。
    1. 保存所做的更改，然后关闭“文本编辑器”。

1. 启动一个“终端”会话，使用 `cd` 命令导航到上一步创建的文件夹。

1. 若要启动示例应用程序，请在“终端”窗口中键入 `./start.sh`。 如果使用的是 **Sense HAT 模拟器**，则会显示其 GUI。 可以使用此 GUI 更改发送到 Azure IoT Central 应用程序的遥测值。

1. “终端”窗口显示的消息类似于 `Device information being served at http://192.168.0.60:8080`。 你环境中的 URL 可能有所不同。 复制该 URL，然后使用 Web 浏览器导航到配置页：

    ![配置设备](media/howto-connect-raspberry-pi-python/configure.png)

1. 输入在将真实设备添加到 Azure IoT Central 应用程序时记下的设备连接字符串。 然后选择“配置设备”。 此时会看到消息“设备已配置，你的设备应该很快就会开始将数据发送到 Azure IoT Central”。

1. 在 Azure IoT Central 应用程序中，可以看到在 Raspberry Pi 上运行的代码如何与应用程序交互：

    * 在真实设备的“度量”页上，可以看到从 Raspberry Pi 发送的遥测数据。 如果使用的是 **Sense HAT 模拟器**，则可在 Raspberry Pi 上的 GUI 中修改遥测值。
    * 在“属性”页上，可以看到报告的“模具编号”属性的值。
    * 在“设置”页中，可以更改 Raspberry Pi 上的各种设置，例如电压和风扇速度。 当 Raspberry Pi 确认所做的更改时，该设置会在 Azure IoT Central 中显示为“已同步”。

## <a name="next-steps"></a>后续步骤

了解如何将 Raspberry Pi 连接到 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

* [将泛型 Node.js 客户端应用程序连接到 Azure IoT Central](howto-connect-nodejs.md)