---
title: 连接到云的 Raspberry Pi (Node.js) - 将 Raspberry Pi 连接到 Azure IoT 中心 | Microsoft Docs
description: 了解如何设置和 Raspberry Pi 连接到 Azure IoT 中心的 Raspberry Pi 将在本教程中将数据发送到 Azure 云平台。
author: wesmc7777
manager: philmea
keywords: Azure IoT Raspberry Pi, Raspberry Pi IoT 中心, Raspberry Pi 将数据发送到云, 连接到云的 Raspberry Pi
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: wesmc
ms.openlocfilehash: 72d7956036404d13849f7c2b08f9df4210f12aa7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65597271"
---
# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>将 Raspberry Pi 连接到 Azure IoT 中心 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教程中，用户首先将学习使用运行 Raspbian 的 Raspberry Pi 的基础知识。 然后将学习如何使用 [Azure IoT 中心](about-iot-hub.md)将设备无缝连接到云。 有关 Windows 10 IoT Core 的示例，请访问 [Windows 开发人员中心](https://www.windowsondevices.com/)。

还没有工具包？ 请尝试 [Raspberry Pi 联机模拟器](iot-hub-raspberry-pi-web-simulator-get-started.md)。 或在[此处](https://azure.microsoft.com/develop/iot/starter-kits)购买新工具包。

## <a name="what-you-do"></a>准备工作

* 创建 IoT 中心。

* 在 IoT 中心内为 Pi 注册设备。

* 设置 Raspberry Pi。

* 在 Pi 上运行示例应用程序，以将传感器数据发送到 IoT 中心。

## <a name="what-you-learn"></a>学习内容

* 如何创建 Azure IoT 中心以及如何获取新的设备连接字符串。

* 如何通过 BME280 传感器连接 Pi。

* 如何通过在 Pi 上运行示例应用程序来收集传感器数据。

* 如何将传感器数据发送到 IoT 中心。

## <a name="what-you-need"></a>所需条件

![所需条件](./media/iot-hub-raspberry-pi-kit-node-get-started/0-starter-kit.png)

* 一个 Raspberry Pi 2 或 Raspberry Pi 3 电路板。

* Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

* 连接到 Pi 的监视器、USB 键盘和鼠标。

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
> 如果没有可选项，可以使用模拟的传感器数据。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>检索 IoT 中心的连接字符串

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="set-up-raspberry-pi"></a>设置 Raspberry Pi

### <a name="install-the-raspbian-operating-system-for-pi"></a>为 Pi 安装 Raspbian 操作系统

准备用于安装 Raspbian 映像的 microSD 卡。

1. 下载 Raspbian。

   a. [下载 Raspbian Stretch](https://downloads.raspberrypi.org/raspbian/images/raspbian-2017-07-05/)（.zip 文件）。

   > [!WARNING]
   > 请使用上面的链接来下载 `raspbian-2017-07-5` zip 映像。 Raspbian 映像的最新版本具有与接线 Pi 节点相关的已知问题，这可能会导致在接下来的步骤中发生故障。

   b. 将 Raspbian 映像提取到计算机上的一个文件夹中。

2. 将 Raspbian 安装到 microSD 卡。

   a. [下载并安装 Etcher SD 卡刻录机实用工具](https://etcher.io/)。

   b. 运行 Etcher 并选择你在步骤 1 中提取的 Raspbian 映像。

   c. 选择 microSD 卡驱动器。 Etcher 可能已选择了正确的驱动器。

   d. 单击“刷机”，将 Raspbian 安装到 microSD 卡。

   e. 在安装完成后，从计算机中移除 microSD 卡。 可以安全地直接移除 microSD 卡，因为在完成时 Etcher 会自动弹出或卸载 microSD 卡。

   f. 将 microSD 卡插入到 Pi 中。

### <a name="enable-ssh-and-i2c"></a>启用 SSH 和 I2C

1. 将 Pi 连接到监视器、键盘和鼠标。

2. 启动 Pi，然后使用 `pi` 作为用户名并使用 `raspberry` 作为密码来登录 Raspbian。

3. 依次单击 Raspberry 图标 >“首选项”   > “Raspberry Pi 配置”  。

   ![Raspbian 首选项菜单](./media/iot-hub-raspberry-pi-kit-node-get-started/1-raspbian-preferences-menu.png)

4. 在“接口”  选项卡上，将“I2C”  和“SSH”  设置为“启用”  ，然后单击“确定”  。 如果没有物理传感器并且想要使用模拟的传感器数据，则此步骤是可选的。

   ![在 Raspberry Pi 上启用 I2C 和 SSH](./media/iot-hub-raspberry-pi-kit-node-get-started/2-enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE]
> 若要启用 SSH 和 I2C，可在 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 和 [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2c) 中找到更多参考文档。

### <a name="connect-the-sensor-to-pi"></a>将传感器连接到 Pi

使用试验板和跳线，将 LED 灯和 BME280 连接到 Pi，如下所示。 如果没有该传感器，请[跳过此部分](#connect-pi-to-the-network)。

![Raspberry Pi 和传感器连接](./media/iot-hub-raspberry-pi-kit-node-get-started/3-raspberry-pi-sensor-connection.png)

BME280 传感器可收集温度和湿度数据。 当设备向云发送消息云时，LED 将闪烁。 

对于传感器引脚，请使用以下接线：

| 启动（传感器和 LED 灯）     | 结束（开发板）            | 线缆颜色   |
| -----------------------  | ---------------------- | ------------: |
| VDD（引脚 5G）             | 3.3 伏 PWR（引脚 1）       | 白线   |
| GND（引脚 7G）             | GND（引脚 6）            | 棕色电缆   |
| SDI（引脚 10G）            | I2C1 SDA（引脚 3）       | 红线     |
| SCK（引脚 8G）             | I2C1 SCL（引脚 5）       | 橙色电缆  |
| LED VDD（引脚 18F）        | GPIO 24（引脚 18）       | 白线   |
| LED GND（引脚 17F）        | GND（引脚 20）           | 黑线   |

单击以查看 [Raspberry Pi 2 和 3 引脚映射](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi)以供参考。

成功将 BME280 连接到 Raspberry Pi 后，它应如下图所示。

![连接在一起的 Pi 和 BME280](./media/iot-hub-raspberry-pi-kit-node-get-started/4-connected-pi.png)

### <a name="connect-pi-to-the-network"></a>将 Pi 连接到网络

使用 USB 微电缆和电源开启 Pi。 使用以太网电缆将 Pi 连接到有线网络，或者按照 [Raspberry Pi Foundation 中的说明](https://www.raspberrypi.org/learning/software-guide/wifi/)将 Pi 连接到无线网络。 将 Pi 成功连接到网络后，需要记下 [Pi 的 IP 地址](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-3-network-setup/finding-your-pis-ip-address)。

![已连接到有线网络](./media/iot-hub-raspberry-pi-kit-node-get-started/5-power-on-pi.png)

> [!NOTE]
> 确保 Pi 与计算机连接到同一网络。 例如，如果计算机连接到无线网络，而 Pi 连接到有线网络，则在 devdisco 输出中可能看不到 IP 地址。

## <a name="run-a-sample-application-on-pi"></a>在 Pi 上运行示例应用程序

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>克隆示例应用程序，并安装必备组件包

1. 从主计算机使用以下 SSH 客户端之一连接到 Raspberry Pi：

   **Windows 用户**
  
   a. 下载并安装 [PuTTY](https://www.putty.org/) for Windows。 

   b. 将 Pi 的 IP 地址复制到主机名（或 IP 地址）部分，并选择 SSH 作为连接类型。

   ![PuTTy](./media/iot-hub-raspberry-pi-kit-node-get-started/7-putty-windows.png)

   **Mac 和 Ubuntu 用户**

   使用 Ubuntu 或 macOS 上的内置 SSH 客户端。 可能需要运行 `ssh pi@<ip address of pi>`，以通过 SSH 连接 Pi。

   > [!NOTE]
   > 默认用户名是 `pi`，密码是 `raspberry`。

2. 将 Node.js 和 NPM 安装到 Pi。

   首先检查 Node.js 版本。

   ```bash
   node -v
   ```

   如果版本低于 10.x，或者 Pi 上没有 Node.js，请安装最新版本。

   ```bash
   curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash
   sudo apt-get -y install nodejs
   ```

3. 克隆示例应用程序。

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

4. 安装示例的所有包。 安装包括 Azure IoT 设备 SDK、BME280 传感器库和接线 Pi 库。

   ```bash
   cd iot-hub-node-raspberrypi-client-app
   sudo npm install
   ```

   > [!NOTE]
   >完成此安装过程可能需要几分钟，具体取决于网络连接情况。

### <a name="configure-the-sample-application"></a>配置示例应用程序

1. 通过运行以下命令，打开配置文件：

   ```bash
   nano config.json
   ```

   ![配置文件](./media/iot-hub-raspberry-pi-kit-node-get-started/6-config-file.png)

   此文件中有两个可以配置的项。 第一个是 `interval`，它定义了发送到云的消息之间的时间间隔（以毫秒为单位）。 第二个是 `simulatedData`，它是一个布尔值，指示是否使用模拟的传感器数据。

   如果没有传感器  ，请将 `simulatedData` 值设置为 `true`，使示例应用程序创建和使用模拟的传感器数据。

2. 通过按“Control-O”>“Enter”>“Control-X”保存并退出。

### <a name="run-the-sample-application"></a>运行示例应用程序

通过运行以下命令，生成示例应用程序：

   ```bash
   sudo node index.js '<YOUR AZURE IOT HUB DEVICE CONNECTION STRING>'
   ```

   > [!NOTE]
   > 确保将设备连接字符串复制并粘贴到单引号中。

应看到以下输出，该输出显示传感器数据和发送到 IoT 中心的消息。

![输出 - 从 Raspberry Pi 发送到 IoT 中心的传感器数据](./media/iot-hub-raspberry-pi-kit-node-get-started/8-run-output.png)

## <a name="read-the-messages-received-by-your-hub"></a>读取 IoT 中心收到的消息

若要监视 IoT 中心从设备收到的消息，一种方法是使用适用于 Visual Studio Code 的 Azure IoT Tools。 若要了解详细信息，请参阅[使用适用于 Visual Studio Code 的 Azure IoT Tools 在设备和 IoT 中心之间发送和接收消息](iot-hub-vscode-iot-toolkit-cloud-device-messaging.md)。

若要了解如何通过更多方式来处理设备发送的数据，请转到下一部分。

## <a name="next-steps"></a>后续步骤

此时已运行示例应用程序，收集传感器数据并将其发送到 IoT 中心。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
