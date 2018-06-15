---
title: Intel Edison 到云 (Node.js) - 将 Intel Edison 连接到 Azure IoT 中心 | Microsoft Docs
description: 在本教程中了解如何设置 Intel Edison 并将其连接到 Azure IoT 中心，使其能够将数据发送到 Azure 云平台。
author: rangv
manager: ''
keywords: azure iot intel edison, intel edison iot 中心, intel edison 发送数据到云, intel edison 到云
ms.service: iot-hub
services: iot-hub
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: dbbe30377564f40eb654842b8814a4d13864ecfa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634254"
---
# <a name="connect-intel-edison-to-azure-iot-hub-nodejs"></a>将 Intel Edison 连接到 Azure IoT 中心 (Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

在本教程中，从学习如何使用 Intel Edison 的基础知识开始。 然后将学习如何使用 [Azure IoT 中心](iot-hub-what-is-iot-hub.md)将设备无缝连接到云。

还没有工具包？ 从[此处](https://azure.microsoft.com/develop/iot/starter-kits)开始

## <a name="what-you-do"></a>准备工作

* 安装 Intel Edison 和 Grove 模块。
* 创建 IoT 中心。
* 在 IoT 中心内为 Edison 注册设备。
* 在 Edison 上运行示例应用程序，以将传感器数据发送到 IoT 中心。

将 Intel Edison 连接到创建的 IoT 中心。 然后，在 Edison 上运行示例应用程序，以从 Grove 温度传感器收集温度和湿度数据。 最后，将传感器数据发送到 IoT 中心。

## <a name="what-you-learn"></a>学习内容

* 如何创建 Azure IoT 中心以及如何获取新的设备连接字符串。
* 如何将 Edison 与 Grove 温度传感器连接起来。
* 如何通过在 Edison 上运行示例应用程序收集传感器数据。
* 如何将传感器数据发送到 IoT 中心。

## <a name="what-you-need"></a>所需条件

![所需条件](media/iot-hub-intel-edison-kit-node-get-started/0_kit.png)

* Intel Edison 开发板
* Arduino 扩展板
* 一个有效的 Azure 订阅。 若没有 Azure 帐户，只需几分钟即可[创建免费的 Azure 试用帐户](https://azure.microsoft.com/free/)。
* 运行 Windows 或 Linux 的 Mac 或电脑。
* Internet 连接。
* Micro B - Type A USB 线缆
* 直流 (DC) 电源。 电源应符合以下条件：
  - 7-15V DC
  - 至少 1500mA
  - 中心/内部插头应为电源的正极

以下项可选：

* Grove Base Shield V2
* Grove - 温度传感器
* Grove 线缆
* 垫条或螺钉（随附在工具包内），其中包括两颗螺钉（用于将模块固定到扩展板上）以及四组螺钉和塑料垫片。

> [!NOTE] 
这些项是可选项，因为代码示例支持模拟的传感器数据。

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-intel-edison"></a>安装 Intel Edison

### <a name="assemble-your-board"></a>组装开发板

本部分包括将 Intel® Edison 模块连接到扩展板的步骤。

1. 将 Intel® Edison 模块放在扩展板的白色区域内，将模块上的孔对准扩展板上的螺钉。

2. 将手指放在 `What will you make?` 文字上方，按压模板，直至感觉模块已就位。

   ![组装开发板 2](media/iot-hub-intel-edison-kit-node-get-started/1_assemble_board2.jpg)

3. 用两颗六角螺母（随附在工具包内）将模块固定到扩展板上。

   ![组装开发板 3](media/iot-hub-intel-edison-kit-node-get-started/2_assemble_board3.jpg)

4. 将一颗螺钉插入扩展板上的一个角孔（共四个）。 在螺钉上放置白色塑料垫片，转动并拧紧。

   ![组装开发板 4](media/iot-hub-intel-edison-kit-node-get-started/3_assemble_board4.jpg)

5. 重复上述步骤安装其他三个角垫。

   ![组装开发板 5](media/iot-hub-intel-edison-kit-node-get-started/4_assemble_board5.jpg)

现在，开发板就已组装完毕。

   ![组装开发板](media/iot-hub-intel-edison-kit-node-get-started/5_assembled_board.jpg)

### <a name="connect-the-grove-base-shield-and-the-temperature-sensor"></a>连接 Grove Base Shield 和温度传感器

1. 将 Grove Base Shield 放在板上。 确保所有引脚都紧紧插入板上。
   
   ![Grove Base Shield](media/iot-hub-intel-edison-kit-node-get-started/6_grove_base_sheild.jpg)

2. 通过 Grove 线缆将 Grove 温度传感器连接到 Grove Base Shield A0 端口。

   ![连接到温度传感器](media/iot-hub-intel-edison-kit-node-get-started/7_temperature_sensor.jpg)

   ![Edison 和传感器连接](media/iot-hub-intel-edison-kit-node-get-started/16_edion_sensor.png)

传感器现准备就绪。

### <a name="power-up-edison"></a>为 Edison 接通电源

1. 插入电源。

   ![插入电源](media/iot-hub-intel-edison-kit-node-get-started/8_plug_power.jpg)

2. 此时，绿色 LED（Arduino* 扩展板上标记为 DS1）应点亮并持续这一状态。

3. 稍等片刻，等待开发板完成启动。

   > [!NOTE]
   > 如果没有 DC 电源，仍可通过 USB 端口为开发板供电。 有关详细信息，请参阅`Connect Edison to your computer`部分。 采用这种方式为开发板供电可能会导致开发板出现异常，尤其是在使用 Wi-Fi 或驱动电机时。

### <a name="connect-edison-to-your-computer"></a>将 Edison 连接到计算机

1. 向下扳动微动开关，使之朝向两个 micro USB 端口，将 Edison 设置为设备模式。 有关设备模式与主机模式的区别，请参阅[此处](https://software.intel.com/en-us/node/628233#usb-device-mode-vs-usb-host-mode)。

   ![向下扳动微动开关](media/iot-hub-intel-edison-kit-node-get-started/9_toggle_down_microswitch.jpg)

2. 将 micro USB 线缆插入顶部的 micro USB 端口。

   ![顶部的 micro USB 端口](media/iot-hub-intel-edison-kit-node-get-started/10_top_usbport.jpg)

3. 将 USB 线缆的另一端插入计算机。

   ![计算机 USB](media/iot-hub-intel-edison-kit-node-get-started/11_computer_usb.jpg)

4. 如果计算机安装了新硬盘，则可确定开发板已完全初始化（就像将一张 SD 卡插入计算机）。

## <a name="download-and-run-the-configuration-tool"></a>下载并运行配置工具
请从[此链接](https://software.intel.com/en-us/iot/hardware/edison/downloads)（在`Installers`标题下列出）获取最新配置工具。 运行该工具，并按照屏幕上的说明进行操作，在需要时单击“下一步”

### <a name="flash-firmware"></a>刷写固件
1. 在`Set up options`页面上，单击`Flash Firmware`。
2. 执行以下操作之一选择要刷写到开发板上的映像：
   - 若要下载 Intel 提供的最新固件映像并使用该映像来刷写开发板，请选择`Download the latest image version xxxx`。
   - 若要使用计算机上已保存的映像来刷写开发板，请选择`Select the local image`。 浏览到要刷写到开发板的映像并选择。
3. 安装工具将尝试刷写开发板。 整个刷写过程最长可能需要 10 分钟。

### <a name="set-password"></a>设置密码
1. 在`Set up options`页面上，单击`Enable Security`。
2. 可为 Intel® Edison 开发板设置自定义名称。 这是可选的。
3. 为开发板键入密码，并单击`Set password`。
4. 记下密码，稍后会用到此密码。

### <a name="connect-wi-fi"></a>连接 Wi-Fi
1. 在`Set up options`页面上，单击`Connect Wi-Fi`。 计算机将扫描可用的 Wi-Fi 网络，此过程最长可能需要 1 分钟。
2. 从`Detected Networks`下拉列表中，选择网络。
3. 从`Security`下拉列表中，选择网络的安全类型。
4. 提供登录名和密码信息，并单击`Configure Wi-Fi`。
5. 记下 IP 地址，稍后会用到此地址。

> [!NOTE]
> 确保 Edison 与计算机连接到同一网络。 计算机通过 IP 地址连接到 Edison。

   ![连接到温度传感器](media/iot-hub-intel-edison-kit-node-get-started/12_configuration_tool.png)

祝贺你！ Edison 已配置成功。

## <a name="run-a-sample-application-on-intel-edison"></a>在 Intel Edison 上运行示例应用程序

### <a name="prepare-the-azure-iot-device-sdk"></a>准备 Azure IoT 设备 SDK

1. 使用主计算机的以下任一 SSH 客户端连接到 Intel Edison。 IP 地址来自配置工具，密码与该工具中设置的相同。
    - [PuTTY](http://www.putty.org/) for Windows。
    - Ubuntu 或 macOS 上的内置 SSH 客户端。

2. 将示例客户端应用克隆到设备中。 
   
   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-intel-edison-client-app
   ```

3. 然后导航至存储库文件夹，运行下列命令来安装所有程序包，该过程可能需要几分钟时间。
   
   ```bash
   cd iot-hub-node-intel-edison-client-app
   npm install
   ```


### <a name="configure-and-run-the-sample-application"></a>配置并运行示例应用程序

1. 通过运行以下命令，打开配置文件：

   ```bash
   nano config.json
   ```

   ![配置文件](media/iot-hub-intel-edison-kit-node-get-started/13_configure_file.png)

   此文件中有两个可配置的宏。 第一个是 `INTERVAL`，它确定发送到云的两条消息之间的时间间隔。 第二个是 `simulatedData`，它是一个布尔值，指示是否使用模拟的传感器数据。

   如果没有传感器，请将 `simulatedData` 值设置为 `true`，使示例应用程序创建和使用模拟的传感器数据。

1. 通过按“Control-O”>“Enter”>“Control-X”保存并退出。


1. 通过运行以下命令，生成示例应用程序：

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   确保将设备连接字符串复制并粘贴到单引号中。

应看到以下输出，其中显示传感器数据以及发至 IoT 中心的消息。

![输出 - 从 Intel Edison 发送到 IoT 中心的传感器数据](media/iot-hub-intel-edison-kit-node-get-started/15_message_sent.png)

## <a name="next-steps"></a>后续步骤

此时已运行示例应用程序，以收集传感器数据并将其发送到 IoT 中心。

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
