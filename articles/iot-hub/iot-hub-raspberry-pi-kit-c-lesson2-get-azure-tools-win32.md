---
title: "将 Raspberry Pi (C) 连接到 Azure IoT - 第 2 课：Azure 工具 (Windows) | Microsoft Docs"
description: "在 Windows 7 及更高版本上安装 Python 和 Azure 命令行界面 (Azure CLI)"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 云服务, azure cli"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-c-get-started
ms.assetid: a3c083b5-0d76-46af-bc77-2ad7d8aadc1e
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 1ae210489b8d369a5fca33e1824b9dc224ad33d2
ms.contentlocale: zh-cn
ms.lasthandoff: 01/24/2017

---
<a id="get-azure-tools-windows-7-and-later" class="xliff"></a>

# 获取 Azure 工具（Windows 7 及更高版本）
> [!div class="op_single_selector"]
> * [Windows 7 及更高版本](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson2-get-azure-tools-mac.md)

<a id="what-you-will-do" class="xliff"></a>

## 执行的操作
安装 Python 和 Azure 命令行界面 (Azure CLI)。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)上查找解决方案。

<a id="what-you-will-learn" class="xliff"></a>

## 你要学习的知识
在本文中，将学习以下内容：
* 如何安装 Python。
* 如何安装 Azure CLI。

<a id="what-you-need" class="xliff"></a>

## 所需条件
* 具有 Internet 连接的 Windows 计算机。
* 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需几分钟时间就能创建一个[免费的 Azure 试用帐户](http://azure.microsoft.com/pricing/free-trial/)。

<a id="install-python" class="xliff"></a>

## 安装 Python
在 Windows 计算机上[安装 Python](https://www.python.org/downloads/)。 可以安装 Python 2.7、3.4 或 3.5。 本教程基于 Python 2.7。 如果已安装了 Python，请转到下一部分并安装 Azure CLI。

还需要将其中安装了 python.exe 和 pip.exe 的文件夹的路径添加到系统 `PATH` 环境变量。 默认情况下，python.exe 安装在 `C:\Python27` 中，pip.exe 安装在 `C:\Python27\Scripts` 中。

<a id="install-the-azure-cli" class="xliff"></a>

## 安装 Azure CLI
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

![指示成功的输出](media/iot-hub-raspberry-pi-lessons/lesson2/az_iot_help_win.png)

<a id="summary" class="xliff"></a>

## 摘要
你已安装了 Azure CLI。 接下来的任务是使用 Azure CLI 创建 Azure IoT 中心和设备标识。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[创建 IoT 中心并注册 Raspberry Pi 3](iot-hub-raspberry-pi-kit-c-lesson2-prepare-azure-iot-hub.md)


