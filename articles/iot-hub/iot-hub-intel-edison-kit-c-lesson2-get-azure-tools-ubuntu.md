---
title: "将 Intel Edison (C) 连接到 Azure IoT - 第 2 课：Azure 工具 (Ubuntu) | Microsoft Docs"
description: "在 Ubuntu 上安装 Python 和 Azure 命令行接口 (Azure CLI)。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "azure cli, iot 云服务, arduino 云"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-c-get-started
ms.assetid: 2463cb8e-5758-4d72-af98-62520d41f2f7
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 475b25f02715a60493e79ecd2170854019dfc4ac
ms.openlocfilehash: e3b1c4dd3ff9a361c6c32cabd04fec523f8d6578
ms.contentlocale: zh-cn
ms.lasthandoff: 01/25/2017

---
<a id="get-azure-tools-ubuntu-1604" class="xliff"></a>

# 获取 Azure 工具 (Ubuntu 16.04)
> [!div class="op_single_selector"]
> * [Windows 7 及更高版本][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## 执行的操作
安装 Azure 命令行界面 (Azure CLI)。 如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

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

![指示成功的输出](media/iot-hub-intel-edison-lessons/lesson2/az_iot_help_ubuntu.png)

<a id="summary" class="xliff"></a>

## 摘要
你已安装了 Azure CLI。 下一个任务是使用 Azure CLI 创建 Azure IoT 中心和设备标识。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[创建 IoT 中心并注册 Intel Edison][create-your-iot-hub-and-register-intel-edison]


<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[create-your-iot-hub-and-register-intel-edison]: iot-hub-intel-edison-kit-c-lesson2-prepare-azure-iot-hub.md
[windows]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-mac.md

