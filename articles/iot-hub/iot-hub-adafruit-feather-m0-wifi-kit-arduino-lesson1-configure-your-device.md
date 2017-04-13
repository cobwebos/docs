---
title: "将 Arduino (C) 连接到 Azure IoT - 第 1 课：配置设备 |Microsoft Docs"
description: "在首次使用前配置 Adafruit Feather M0 WiFi。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 安装, 将 arduino 连接到电脑, 安装 arduino, arduino 开发板"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: f5b334f0-a148-41aa-b374-ce7b9f5b305a
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 3789c07cd6142ba5b6a58a67813c0ead6b87bd76
ms.lasthandoff: 01/24/2017


---
# <a name="configure-your-device"></a>配置设备
## <a name="what-you-will-do"></a>执行的操作
通过装配并启动 Adafruit Feather M0 WiFi Arduino 开发板，在首次使用前配置开发板。 如果有任何问题，请在[故障排除页面](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)上查找解决方案。

## <a name="what-you-need"></a>所需条件
若要完成此操作，需要使用 Adafruit Feather M0 WiFi 初学者工具包中的以下部件：

* Adafruit Feather M0 Wifi 开发板
* Micro B - Type A USB 线缆

![工具包][kit]

你还需要：

* 运行 Windows、Mac 或 Linux 的计算机。
* 适合 Arduino 开发板连接的无线连接。
* Internet 访问权限（用于下载配置工具）。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，将学习以下内容：

* 有关如何装配并启动 Arduino 开发板的信息，请参阅以下资源。
* 如何在 Ubuntu 上添加串行端口权限。

## <a name="connect-your-arduino-board-to-your-computer"></a>将 Arduino 开发板连接到计算机

1. 将 micro USB 线缆插入顶部的 micro USB 端口。

   ![顶部的 micro USB 端口][top-micro-usb-port]

2. 将 USB 线缆的另一端插入计算机。

   ![计算机 USB][computer-usb]

## <a name="add-serial-port-permissions-on-ubuntu"></a>在 Ubuntu 上添加串行端口权限

如果使用的是 Windows 或 macOS，可以跳过此部分。 对于 Ubuntu，需要执行以下步骤，确保一般的 Linux 用户有权在 Arduino 开发板的 USB 端口上执行操作。

1. 现在，作为一般的终端用户：

   ```bash
   ls -l /dev/ttyUSB*
   # Or
   ls -l /dev/ttyACM*
   ```

   将看到类似于下面的内容：

   ```bash
   crw-rw---- 1 root uucp 188, 0 5 apr 23.01 ttyUSB0
   # Or
   crw-rw---- 1 root dialout 188, 0 5 apr 23.01 ttyACM0
   ```

   “0”可能是不同的编号，或者可能返回多个条目。 在第一种情况下，所需数据为 `uucp`；在第二种情况下，所需数据为 `dialout`（即文件的组所有者）。

2. 将用户添加到组：

   ```bash
   sudo usermod -a -G group-name username
   ```

   其中 `group-name` 是在第一步中找到的数据，`username` 是 Linux 用户名。

3. 需要注销并再次登录，以使此更改生效并完成安装。

## <a name="summary"></a>摘要
通过本文，已了解如何配置 Arduino 开发板。 下一个任务是安装必需的工具和软件，从而为在 Arduino 开发板上运行示例应用程序做准备工作。

![硬件已准备就绪][hardware-is-ready]

## <a name="next-steps"></a>后续步骤
[获取工具][get-the-tools]
<!-- Images and links -->

[kit]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/kit.png
[top-micro-usb-port]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/top_usbport.jpg
[computer-usb]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/computer_usb.jpg
[hardware-is-ready]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/hardware_ready.jpg
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
