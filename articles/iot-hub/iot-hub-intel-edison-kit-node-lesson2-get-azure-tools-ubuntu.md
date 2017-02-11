---
title: "获取用于 Azure IoT 初学者工具包的 Azure 工具 (Ubuntu 16.04) | Microsoft Docs"
description: "在 Ubuntu 上安装 Python 和 Azure 命令行接口 (Azure CLI)。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure cli, iot 云服务, arduino 云"
ms.assetid: 6dcb34bf-54a3-4af0-ba89-95d5cfafceff
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 12158a53a2c570fe5d10e7e01a03e54b04117215
ms.openlocfilehash: eee0370eeb89971c37ff49ac87af96f95d5aef6d


---
# <a name="get-azure-tools-ubuntu-1604"></a>获取 Azure 工具 (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 及更高版本][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>执行的操作
安装 Azure 命令行界面 (Azure CLI)。 如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，将学习以下内容：
* 如何安装 Azure CLI。
* 如何添加  Azure CLI 的 IoT 子组。

## <a name="what-you-need"></a>所需条件
* 具有 Internet 连接的 Ubuntu 计算机。
* 一个有效的 Azure 订阅。 如果没有帐户，只需几分钟时间就能创建一个[免费的试用帐户](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="install-the-azure-cli"></a>安装 Azure CLI
Azure CLI 针对 Azure 提供了多平台命令行体验，使得你可以直接从命令行工作来预配和管理资源。

若要安装最新的 Azure CLI，请执行以下步骤：

1. 在终端窗口中运行以下命令。 安装 Azure CLI 可能需要花费大约五分钟。

   ```bash
   sudo apt-get update
   sudo apt-get install -y libssl-dev libffi-dev
   sudo apt-get install -y python-dev
   sudo apt-get install -y build-essential
   sudo apt-get install -y python-pip
   sudo pip install --upgrade azure-cli
   sudo pip install --upgrade azure-cli-iot
   ```
2. 运行以下命令来验证安装：

   ```bash
   az iot -h
   ```

如果安装成功，应当会看到以下输出。

![指示成功的输出](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_ubuntu.png)

## <a name="summary"></a>摘要
你已安装了 Azure CLI。 下一个任务是使用 Azure CLI 创建 Azure IoT 中心和设备标识。

## <a name="next-steps"></a>后续步骤
[创建 IoT 中心并注册 Intel Edison][create-your-iot-hub-and-register-intel-edison]


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-node-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-mac.md



<!--HONumber=Dec16_HO2-->


