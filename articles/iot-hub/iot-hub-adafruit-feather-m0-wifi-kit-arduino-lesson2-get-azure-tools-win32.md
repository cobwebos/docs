---
title: "获取用于 Azure IoT 初学者工具包（Windows 7 和更高版本）的 Azure 工具 | Microsoft Docs"
description: "在 Windows 7 及更高版本上安装 Python 和 Azure 命令行界面 (Azure CLI)"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure cli, iot 云服务, arduino 云"
ms.assetid: 70dfff14-4be1-468d-9919-e40f4bead308
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 9e8084fe26229ef9ed1676c0a3c34e0ee7be88b1
ms.openlocfilehash: 309694aa137b887044f52a203939462eec611d2c


---
# <a name="get-azure-tools-windows-7-and-later"></a>获取 Azure 工具（Windows 7 及更高版本）

> [!div class="op_single_selector"]
> * [Windows 7 或更高版本][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>执行的操作

安装 Python 和 Azure 命令行界面 (Azure CLI)。 如果有任何问题，请在适用于 Adafruit Feather M0 WiFi Arduino 开发板的[故障排除页](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，将学习以下内容：
* 如何安装 Python。
* 如何安装 Azure CLI。

## <a name="what-you-need"></a>所需条件
* 具有 Internet 连接的 Windows 计算机。
* 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需几分钟时间就能创建一个[免费的 Azure 试用帐户](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="install-python"></a>安装 Python
在 Windows 计算机上[安装 Python](https://www.python.org/downloads/)。 可以安装 Python 2.7、3.4 或 3.5。 本教程基于 Python 2.7。 如果已安装了 Python，请转到下一部分并安装 Azure CLI。

还需要将其中安装了 python.exe 和 pip.exe 的文件夹的路径添加到系统 `PATH` 环境变量。 默认情况下，python.exe 安装在 `C:\Python27` 中，pip.exe 安装在 `C:\Python27\Scripts` 中。

## <a name="install-the-azure-cli"></a>安装 Azure CLI
Azure CLI 为 Azure 提供了多平台命令行体验。 可以直接从命令行执行操作来预配和管理资源。

若要安装 Azure CLI，请执行以下步骤：

1. 以管理员身份打开“命令提示符”窗口。
2. 运行以下命令：

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
3. 运行以下命令来验证安装：

   ```bash
   az iot -h
   ```

如果安装成功，将会看到以下输出。

![指示成功的输出][output]

## <a name="summary"></a>摘要
你已安装了 Azure CLI。 接下来的任务是使用 Azure CLI 创建 Azure IoT 中心和设备标识。

## <a name="next-steps"></a>后续步骤
[创建 IoT 中心并注册 Arduino 开发板][create-your-iot-hub-and-register-your-arduino-board]


<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-mac.md
[output]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson2/az_iot_help_win.png
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md


<!--HONumber=Dec16_HO2-->


