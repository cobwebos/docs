---
title: "将 Raspberry Pi（节点）连接到 Azure IoT - 第 2 课：获取工具 (Ubuntu) | Microsoft 文档"
description: "在 Ubuntu 上安装 Python 和 Azure 命令行界面 (Azure CLI)。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 云服务, azure cli"
ms.assetid: 2f98923a-5274-4220-87d4-77ac8beb4d0f
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: b9557037bcaec02b4cc8e61418bd194b7a869033
ms.lasthandoff: 03/10/2017


---
# <a name="get-azure-tools-ubuntu-1604"></a>获取 Azure 工具 (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 及更高版本](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson2-get-azure-tools-mac.md)

## <a name="what-you-will-do"></a>执行的操作
安装 Azure 命令行界面 (Azure CLI)。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，将学习以下内容：
* 如何安装 Azure CLI。
* 如何添加  Azure CLI 的 IoT 子组。

## <a name="what-you-need"></a>所需条件
* 具有 Internet 连接的 Ubuntu 计算机。
* 一个有效的 Azure 订阅。 如果没有帐户，只需几分钟时间就能创建一个[免费的试用帐户](http://azure.microsoft.com/pricing/free-trial/)。

## <a name="install-the-azure-cli"></a>安装 Azure CLI
Azure CLI 提供适用于 Azure 的多平台命令行体验，让用户能够直接通过命令行预配和管理资源。

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

![指示成功的输出](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_ubuntu.png)

## <a name="summary"></a>摘要
你已安装了 Azure CLI。 下一个任务是使用 Azure CLI 创建 Azure IoT 中心和设备标识。

## <a name="next-steps"></a>后续步骤
[创建 IoT 中心并注册 Raspberry Pi 3](iot-hub-raspberry-pi-kit-node-lesson2-prepare-azure-iot-hub.md)


