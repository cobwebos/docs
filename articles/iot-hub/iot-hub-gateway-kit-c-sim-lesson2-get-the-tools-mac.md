---
title: "模拟设备和 Azure IoT 网关 - 第 2 课：获取工具 (macOS) | Microsoft Docs"
description: "在 Mac 计算机上安装工具、创建 IoT 中心和在 IoT 中心注册设备。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 开发, iot 软件, iot 云服务, 物联网软件, azure cli, 安装 python mac, 在 mac 上安装 git, gulp 运行, 安装 node js mac"
ms.assetid: 42f9d186-e20c-4ef9-98cc-71d39e058b06
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 61e9a9fc7876094c04238c61cfc38efdd97b05f7
ms.openlocfilehash: 8b79b166fb16d6454fde46996e715779bf19eac7
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-macos"></a>获取工具 (macOS)
> [!div class="op_single_selector"]
> * [Windows 7 或更高版本](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-gateway-kit-c-sim-lesson2-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>执行的操作

- 安装 Git、Node.js、Gulp、Python。
- 安装 Azure 命令行界面 (Azure CLI)。 

如果有任何问题，请在[故障排除页面](iot-hub-gateway-kit-c-sim-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识

在本课程中将学习：

- 如何安装 [Git](https://git-scm.com/) 和 [Node.js](https://nodejs.org/en/)。
  - Git 是一个开源分布式版本控制系统。 本课程的示例应用程序存储在 Git 上。
  - Node.js 是一个具有丰富的程序包生态系统的 JavaScript 运行时。
- 如何使用 [NPM](https://www.npmjs.com/) 安装 Node.js 开发工具。
  - 需要的最低 Node.js 版本为 4.5 LTS。
  - NPM 是 Node.js 的程序包管理器之一。
- 如何安装 Visual Studio Code。
  - Visual Studio Code 是一个跨平台的轻型但功能强大的源代码编辑器，适用于 Windows、Linux 和 macOS。 它还对于调试、内嵌的 Git 控制、语法突出显示、智能代码完成、代码段和代码重构提供了极佳支持。
- 如何安装 Python。
  - Python 是一种广泛使用的高级、通用、解释型和动态的编程语言。
- 如何安装 Azure CLI。
  - Azure CLI 为 Azure 提供了多平台命令行体验。 可以直接从命令行执行操作以预配和管理资源。
- 如何使用 Azure CLI 创建 IoT 中心。

## <a name="what-you-need"></a>所需条件

- Internet 连接，用于下载工具和软件。
- 运行 OS X Yosemite (10.10) 或更高版本的 Mac 计算机。

## <a name="install-git-and-nodejs"></a>安装 Git 和 Node.js

若要安装 Git 和 Node.js，请按以下步骤使用 Homebrew 包管理实用工具：

1. [下载](http://brew.sh/)并安装 Homebrew。 若已安装 Homebrew，请转到步骤 2。
   1. 按 `Cmd + Space` 并输入 `Terminal` 以打开一个终端。
   2. 运行以下命令：

      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```

2. 通过运行以下命令安装 Git 和 Node.js：

    ```bash
    brew install node git
    ```

## <a name="install-nodejs-development-tools"></a>安装 Node.js 开发工具

使用 [gulp.js](http://gulpjs.com/) 自动部署和执行脚本。

在终端运行以下命令以安装 gulp：

```bash
npm install -g gulp
```

如果遇到与安装有关的问题，请参阅[故障排除指南](iot-hub-gateway-kit-c-sim-troubleshooting.md)获取常见问题的解决方案。

> [!Note]
> 需要使用 Node、NPM 和 Gulp，才能运行在 Node.js 中开发的自动化脚本。

## <a name="install-python"></a>安装 Python

虽然 Mac OS X 附带了 Python 2.7，但还是建议你通过 Homebrew 安装 Python。 请参阅 [Installing Python on Mac OS X](http://docs.python-guide.org/en/latest/starting/install/osx/)（在 Mac OS X 上安装 Python）。

通过运行以下命令安装 Python 和 pip：

```bash
brew install python
```

## <a name="install-the-azure-cli"></a>安装 Azure CLI

若要安装 Azure CLI，请执行以下步骤：

1. 在终端运行以下命令：
   ```bash
   pip install --upgrade azure-cli
   pip install --upgrade azure-cli-iot
   ```
   安装过程可能需要 5 分钟。

2. 运行以下命令来验证安装：
   ```bash
   az iot -h
   ```
   如果安装成功，应当会看到以下输出。

   ![验证 Azure CLI 安装](media/iot-hub-gateway-kit-lessons/lesson2/az_iot_help_osx.png)

## <a name="install-visual-studio-code"></a>安装 Visual Studio Code

使用本教程后面的 Visual Studio Code 编辑配置文件。

[下载](https://code.visualstudio.com/docs/setup/osx)并安装 Visual Studio Code。

## <a name="summary"></a>摘要

已在 Mac 计算机上安装所有所需的工具和软件。 下一个任务是使用 Azure CLI 创建 IoT 中心并在此 IoT 中心注册设备。

## <a name="next-steps"></a>后续步骤
[创建 IoT 中心并注册设备](iot-hub-gateway-kit-c-sim-lesson2-register-device.md)

