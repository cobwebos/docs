---
title: "将 Raspberry Pi (C) 连接到 Azure IoT - 第 2 课：Azure 工具 (Ubuntu) | Microsoft Docs"
description: "在 Ubuntu 上安装 Python 和 Azure 命令行接口 (Azure CLI)。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 云服务, azure cli"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: a03512f2-fabe-40c5-8505-b93eef8e5bec
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 4c31bb14f2b32d71f14a89576922b9701b226e66
ms.contentlocale: zh-cn
ms.lasthandoff: 01/24/2017

---
<a id="get-azure-tools-ubuntu-1604" class="xliff"></a>

# 获取 Azure 工具 (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 及更高版本](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-mac.md)

<a id="what-you-will-do" class="xliff"></a>

## 执行的操作
安装 Azure 命令行界面 (Azure CLI)。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)上查找解决方案。

<a id="what-you-will-learn" class="xliff"></a>

## 你要学习的知识
在本文中，将学习以下内容：
* 如何安装 Azure CLI。
* 如何添加  Azure CLI 的 IoT 子组。

<a id="what-you-need" class="xliff"></a>

## 所需条件
* 具有 Internet 连接的 Ubuntu 计算机。
* 一个有效的 Azure 订阅。 如果没有帐户，只需几分钟时间就能创建一个[免费的试用帐户](http://azure.microsoft.com/pricing/free-trial/)。

<a id="install-the-azure-cli" class="xliff"></a>

## 安装 Azure CLI
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

![指示成功的输出](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_ubuntu.png)

<a id="summary" class="xliff"></a>

## 摘要
你已安装了 Azure CLI。 下一个任务是使用 Azure CLI 创建 Azure IoT 中心和设备标识。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[创建 IoT 中心并注册 Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md)


