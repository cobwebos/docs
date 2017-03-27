---
title: "将 Raspberry Pi（节点）连接到 Azure IoT - 第 1 课：配置设备 | Microsoft Docs"
description: Configure Raspberry Pi 3 for first-time use and install the Raspbian OS, a free operating system that is optimized for the Raspberry Pi hardware.
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "安装, raspbian 下载, 如何安装 raspbian, raspbian 设置, raspberry pi 安装 raspbian, raspberry pi 安装 os, raspberry pi sd 卡安装, raspberry pi 连接, 连接到 raspberry pi, raspberry pi 连接"
ms.assetid: 43f7c2cf-f1a5-4dd5-93f0-7e546c6dc91e
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 5b80d471c78973f8c2f6b1ad4e1240c66f0505ef
ms.openlocfilehash: 5afe1110944641daaeace5ef02b2c9fa8d2a8e05
ms.lasthandoff: 02/21/2017


---
# <a name="configure-your-device"></a>配置设备
## <a name="what-you-will-do"></a>执行的操作
为首次使用配置 Pi 并安装 Raspbian 操作系统。 Raspbian 是一个针对 Raspberry Pi 硬件进行了优化的免费操作系统。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，将学习以下内容：

* 如何在 Pi 上安装 Raspbian。
* 如何使用 USB 电缆为 Pi 供电。
* 如何使用以太网电缆或无线网络将 Pi 连接到网络。
* 如何将 LED 添加到试验板并将其连接到 Pi。

## <a name="what-you-will-need"></a>所需的项目
若要完成此操作，你需要 Raspberry Pi 3 初学者工具包中的以下部件：

* Raspberry Pi 3 电路板
* 16-GB microSD 卡
* 带有 6 英尺微型 USB 电缆的 5 伏 2 安电源
* 试验板
* 连接器电线
* 一个 560 欧姆的电阻器
* 一个散射的 10 毫米 LED
* 以太网电缆

![初学者工具包中的物品](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

你还需要：

* Pi 要连接到的有线或无线连接。
* USB-SD 适配器或 miniSD 卡，用于将操作系统映像刻录到 microSD 卡中。
* 运行 Windows、Mac 或 Linux 的计算机。 该计算机用来在 microSD 卡上安装 Raspbian。
* 用于下载必需的工具和软件的 Internet 连接。

## <a name="install-raspbian-on-the-microsd-card"></a>在 microSD 卡上安装 Raspbian
为安装 Raspbian 映像准备 microSD 卡。

1. 下载 Raspbian。
   1. [下载](https://www.raspberrypi.org/downloads/raspbian/)带 Pixel 的 Raspbian Jessie 的 .zip 文件。
   2. 将 Raspbian 映像提取到计算机上的一个文件夹中。
2. 将 Raspbian 安装到 microSD 卡。
   1. [下载](https://www.etcher.io)并安装 Etcher SD 卡刻录机实用工具。
   2. 运行 Etcher 并选择你在步骤 1 中提取的 Raspbian 映像。
   3. 选择 microSD 卡驱动器。
      注意，Etcher 可能已选择了正确的驱动器。
   4. 单击 **Flash** 来将 Raspbian 安装到 microSD 卡。
   5. 在安装完成后，从计算机中移除 microSD 卡。
      可以安全地直接移除 microSD 卡，因为在完成时 Etcher 会自动弹出或卸载 microSD 卡。
   6. 将 microSD 卡插入到 Pi 中。

![插入 SD 卡](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="turn-on-pi"></a>为 Pi 通电
通过使用微型 USB 电缆和电源为 Pi 通电。

![通电](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [!NOTE]
> 必须使用工具包中至少为 2A 的电源以确保 Raspberry 具有足够的电能来正常工作。

## <a name="enable-ssh"></a>启用 SSH
自 2016 年 11 月版本起，Raspbian 在默认情况下禁用了 SSH 服务器。 需要手动启用它。 可以参阅[正式说明](https://www.raspberrypi.org/documentation/remote-access/ssh/)或连接监视器并转到“首选项”->“Raspberry Pi 配置”以启用 SSH。

## <a name="connect-raspberry-pi-3-to-the-network"></a>将 Raspberry Pi 3 连接到网络
可以将 Pi 连接到有线网络或无线网络。 请确保将 Pi 与计算机连接到同一网络。 例如，可以将 Pi 连接到计算机所连接到的同一交换机。

### <a name="connect-to-a-wired-network"></a>连接到有线网络
使用以太网电缆将 Pi 连接到有线网络。 如果建立了连接，则 Pi 上的两个 LED 会亮起。

![使用以太网电缆进行连接](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="connect-to-a-wireless-network"></a>连接到无线网络
根据 Raspberry Pi Foundation 中的[说明](https://www.raspberrypi.org/learning/software-guide/wifi/)将 Pi 连接到无线网络。 这些说明要求你首先将监视器和键盘连接到 Pi。

## <a name="connect-the-led-to-pi"></a>将 LED 连接到 Pi
若要完成此任务，请使用[试验板](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard)、连接器电线、LED 和电阻器。 将它们连接到 Pi 的[通用输入/输出](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) 端口。

![试验板、LED 和电阻器](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. 将 LED 的较短针脚连接到 **GPIO GND（引脚 6）**。
2. 将 LED 的较长针脚连接到电阻器的一个针脚。
3. 将电阻器的另一个针脚连接到 **GPIO 4（引脚 7）**。

请注意，LED 极性很重要。 此极性设置通常称为“低电平有效”。

![引出线](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

祝贺你！ 你已成功配置了 Pi。

## <a name="summary"></a>摘要
在本文中，你已学习了如何通过执行以下步骤来配置 Pi：安装 Raspbian，将 Pi 连接到网络并将 LED 连接到 Pi。 注意，LED 仍然没有亮起。 下一个任务是安装必需的工具和软件，从而为在 Pi 上运行示例应用程序做准备工作。

![硬件已准备就绪](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>后续步骤
[获取工具](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)


