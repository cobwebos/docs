---
title: "连接到云的 Raspberry Pi (Node.js) - 将 Raspberry Pi 连接到 Azure IoT 中心 | Microsoft Docs"
description: "将 Raspberry Pi 连接到 Azure IoT 中心，以供 Raspberry Pi 将数据发送到 Azure 云。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "Azure IoT Raspberry Pi, Raspberry Pi IoT 中心, Raspberry Pi 将数据发送到云, 连接到云的 Raspberry Pi"
experiment_id: xshi-happypathemu-20161202
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/14/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 9ae7e129b381d3034433e29ac1f74cb843cb5aa6
ms.openlocfilehash: 867914b78022e3ac288df079c973692c922158be
ms.contentlocale: zh-cn
ms.lasthandoff: 05/08/2017


---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>将 Raspberry Pi 连接到 Azure IoT 中心 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教程中，用户首先将学习使用运行 Raspbian 的 Raspberry Pi 的基础知识。 然后将学习如何使用 [Azure IoT 中心](iot-hub-what-is-iot-hub.md)将设备无缝连接到云。 有关 Windows 10 IoT Core 的示例，请访问 [Windows 开发人员中心](http://www.windowsondevices.com/)。

还没有工具包？ 试用 [Raspberry Pi 3 仿真器](https://blogs.msdn.microsoft.com/iliast/2016/11/10/how-to-emulate-raspberry-pi/)。 或在[此处](https://azure.microsoft.com/develop/iot/starter-kits)购买新工具包。

## <a name="what-you-do"></a>准备工作

* 设置 Raspberry Pi。
* 创建 IoT 中心。
* 在 IoT 中心内为 Pi 注册设备。
* 在 Pi 上运行示例应用程序，以将传感器数据发送到 IoT 中心。

将 Raspberry Pi 连接到创建的 IoT 中心。 然后，在 Pi 上运行示例应用程序，以从 BME280 传感器收集温度和湿度数据。 最后，将传感器数据发送到 IoT 中心。

## <a name="what-you-learn"></a>学习内容

* 如何创建 Azure IoT 中心以及如何获取新的设备连接字符串。
* 如何通过 BME280 传感器连接 Pi。
* 如何通过在 Pi 上运行示例应用程序来收集传感器数据。
* 如何将传感器数据发送到 IoT 中心。

## <a name="what-you-need"></a>所需条件

![所需条件](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 或 Raspberry Pi 3 电路板。
* 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需几分钟时间就能[创建一个免费的 Azure 试用帐户](https://azure.microsoft.com/free/)。
* 监视器、USB 键盘和连接到 Pi 的鼠标。
* 运行 Windows 或 Linux 的 Mac 或 PC。
* Internet 连接。
* 16 GB 或更大内存的 microSD 卡。
* USB-SD 适配器或 microSD 卡，用于将操作系统映像刻录到 microSD 卡中。
* 带有 6 英尺微型 USB 电缆的 5 伏 2 安电源。

以下项是可选项：

* 已装配的 Adafruit BME280 温度、压力和湿度传感器。
* 试验板。
* 6 根 F/M 跳线。
* 散射的 10 毫米 LED 灯。

  > [!NOTE] 
  这些项是可选项，因为代码示例支持模拟的传感器数据。

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>设置 Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>为 Pi 安装 Raspbian 操作系统

准备用于安装 Raspbian 映像的 microSD 卡。

1. 下载 Raspbian。
   1. [下载带 Pixel 的 Raspbian Jessie](https://www.raspberrypi.org/downloads/raspbian/)（.zip 文件）。
   1. 将 Raspbian 映像提取到计算机上的一个文件夹中。
1. 将 Raspbian 安装到 microSD 卡。
   1. [下载并安装 Etcher SD 卡刻录机实用工具](https://etcher.io/)。
   1. 运行 Etcher 并选择你在步骤 1 中提取的 Raspbian 映像。
   1. 选择 microSD 卡驱动器。 注意，Etcher 可能已选择了正确的驱动器。
   1. 单击“刷机”，将 Raspbian 安装到 microSD 卡。
   1. 在安装完成后，从计算机中移除 microSD 卡。 可以安全地直接移除 microSD 卡，因为在完成时 Etcher 会自动弹出或卸载 microSD 卡。
   1. 将 microSD 卡插入到 Pi 中。

### <a name="enable-ssh-and-i2c"></a>启用 SSH 和 I2C

1. 将 Pi 连接到监视器、键盘和鼠标，启动 Pi，然后通过将 `pi` 用作用户名并将 `raspberry` 用作密码来登录 Raspbian。
1. 依次单击 Raspberry 图标 >“首选项” > “Raspberry Pi 配置”。

   ![Raspbian 首选项菜单](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. 在“接口”选项卡上，将“I2C”和“SSH”设置为“启用”，然后单击“确定”。

   ![在 Raspberry Pi 上启用 I2C 和 SSH](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
若要启用 SSH 和 I2C，可在 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 和 [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c) 中找到更多参考文档。

### <a name="connect-the-sensor-to-pi"></a>将传感器连接到 Pi

使用试验板和跳线，将 LED 灯和 BME280 连接到 Pi，如下所示。 如果没有该传感器，请跳过此部分。

![Raspberry Pi 和传感器连接](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)


对于传感器引脚，请使用以下接线：

| 启动（传感器和 LED 灯）     | 结束（开发板）            | 线缆颜色   |
| -----------------------  | ---------------------- | ------------: |
| VDD（引脚 5G）             | 3.3 伏 PWR（引脚 1）       | 白线   |
| GND（引脚 7G）             | GND（引脚 6）            | 棕色电缆   |
| SCK（引脚 8G）             | I2C1 SDA（引脚 3）       | 橙色电缆  |
| SDI（引脚 10G）            | I2C1 SCL（引脚 5）       | 红线     |
| LED VDD（引脚 18F）        | GPIO 24（引脚 18）       | 白线   |
| LED GND（引脚 17F）        | GND（引脚 20）           | 黑线   |

单击查看 [Raspberry Pi 2 和 3 引脚映射](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi)以供参考。

成功将 BME280 连接到 Raspberry Pi 后，它应如下图所示。

![连接在一起的 Pi 和 BME280](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

### <a name="connect-pi-to-the-network"></a>将 Pi 连接到网络

通过使用微型 USB 电缆和电源为 Pi 通电。 使用以太网电缆将 Pi 连接到有线网络，或者按照 [Raspberry Pi Foundation 中的说明](https://www.raspberrypi.org/learning/software-guide/wifi/)将 Pi 连接到无线网络。 将 Pi 成功连接到网络后，需要记下 [Pi 的 IP 地址](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)。

![已连接到有线网络](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)

> [!NOTE]
> 请确保将 Pi 与计算机连接到同一网络。 例如，如果计算机连接到无线网络，而 Pi 连接到有线网络，则在 devdisco 输出中可能看不到 IP 地址。

## <a name="run-a-sample-application-on-pi"></a>在 Pi 上运行示例应用程序

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>克隆示例应用程序，并安装必备组件包

1. 使用主计算机的以下任一 SSH 客户端连接到 Raspberry Pi。
    - [PuTTY](http://www.putty.org/) for Windows。 需要 Pi 的 IP 地址，以便通过 SSH 连接它。
    - Ubuntu 或 macOS 上的内置 SSH 客户端。 可能需要运行 `ssh pi@<ip address of pi>`，以便通过 SSH 连接 Pi。

   > [!NOTE] 
   默认用户名是 `pi`，密码是 `raspberry`。

1. 将 Node.js 和 NPM 安装到 Pi。
   
   首先应使用以下命令查看 Node.js 版本。 
   
   ```bash
   node -v
   ```

   如果版本低于 4.x，或者 Pi 上没有 Node.js，则运行以下命令以安装或更新 Node.js。

   ```bash
   curl -sL http://deb.nodesource.com/setup_4.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

1. 通过运行以下命令，克隆示例应用程序：

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. 通过运行以下命令，安装所有程序包。 它包括 Azure IoT 设备 SDK、BME280 传感器库和接线 Pi 库。

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```
   > [!NOTE] 
   完成此安装过程可能需要几分钟，具体取决于网络连接情况。

### <a name="configure-the-sample-application"></a>配置示例应用程序

1. 通过运行以下命令，打开配置文件：

   ```bash
   nano config.json
   ```

   ![配置文件](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   此文件中有两个可以配置的项。 第一个宏项是 `interval`，它确定发送到云的两条消息之间的时间间隔。 第二个是 `simulatedData`，它是一个布尔值，指示是否使用模拟的传感器数据。

   如果没有传感器，请将 `simulatedData` 值设置为 `true`，使示例应用程序创建和使用模拟的传感器数据。

1. 通过按“Control-O”>“Enter”>“Control-X”保存并退出。

### <a name="run-the-sample-application"></a>运行示例应用程序

1. 通过运行以下命令，生成示例应用程序：

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   确保将设备连接字符串复制并粘贴到单引号中。


应看到以下输出，该输出显示传感器数据和发送到 IoT 中心的消息。

![输出 - 从 Raspberry Pi 发送到 IoT 中心的传感器数据](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>后续步骤

此时已运行示例应用程序，以收集传感器数据并将其发送到 IoT 中心。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
