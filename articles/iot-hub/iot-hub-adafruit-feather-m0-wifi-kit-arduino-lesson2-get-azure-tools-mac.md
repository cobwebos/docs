---
title: "将 Arduino 连接到 Azure IoT - 第 2 课：Azure 工具 (macOS) | Microsoft Docs"
description: "在 macOS 上安装 Python 和 Azure 命令行接口 (Azure CLI)。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure cli, iot 云服务, arduino 云"
ms.assetid: 9b719293-01d2-4a2d-9c49-476e67f4816d
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: ad8ee9d524dcb096ac7db4bb23ec16d56518742b


---
# <a name="get-azure-tools-macos-1010"></a>获取 Azure 工具 (macOS 10.10)

> [!div class="op_single_selector"]
> * [Windows 7 或更高版本][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>执行的操作

安装 Azure 命令行界面 (Azure CLI)。 如果遇到任何问题，请在 Adafruit Feather M0 WiFi Arduino 开发板的[故障排除页](iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md)中查找解决方法。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，将学习以下内容：
* 如何安装 Azure CLI。
* 如何添加  Azure CLI 的 IoT 子组。

## <a name="what-you-need"></a>所需条件
* 具有 Internet 连接的 Mac。
* 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需几分钟时间就能创建一个[免费的 Azure 试用帐户](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="install-python"></a>安装 Python
虽然 macOS 附带了现成的 Python 2.7，但还是建议你通过 Homebrew 安装 Python。 请参阅[在 macOS 上安装 Python](http://docs.python-guide.org/en/latest/starting/install/osx/)。

通过运行以下命令安装 Python 和 pip：

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>安装 Azure CLI
Azure CLI 为 Azure 提供了多平台命令行体验。 可以直接从命令行执行操作来预配和管理资源。

若要安装最新的 Azure CLI，请执行以下步骤：

1. 在终端窗口中运行以下命令。 安装 Azure CLI 可能需要花费大约五分钟。

   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
2. 运行以下命令来验证安装：

   ```bash
   az iot -h
   ```

如果安装成功，应当会看到以下输出。

![指示成功的输出][output]

## <a name="summary"></a>摘要
你已安装了 Azure CLI。 下一个任务是使用 Azure CLI 创建 Azure IoT 中心和设备标识。

## <a name="next-steps"></a>后续步骤
[创建 IoT 中心并注册 Arduino 开发板][create-your-iot-hub-and-register-your-arduino-board]


<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-mac.md
[output]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson2/az_iot_help_osx.png
[create-your-iot-hub-and-register-your-arduino-board]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-prepare-azure-iot-hub.md


<!--HONumber=Jan17_HO4-->


