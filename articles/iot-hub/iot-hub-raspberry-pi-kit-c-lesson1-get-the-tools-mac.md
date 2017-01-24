---
title: "获取工具 (macOS 10.10) | Microsoft 文档"
description: "下载并安装在 macOS 上适用于 Pi 的第一个示例应用程序的必需工具和软件。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "iot 开发, iot 软件, 物联网软件, 在 mac 上安装 git, gulp 运行, 安装 node js mac"
ms.assetid: fc6bd2c8-a847-4bf5-818f-6f7f9a6835ee
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 155e5d6280d86b06b1718fc3032c2c224539183d
ms.openlocfilehash: bcb0b09468364cf13aff3987aeb7dd74e551cf9d


---
# <a name="get-the-tools-macos-1010"></a>获取工具 (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 或更高版本](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-c-lesson1-get-the-tools-mac.md)

## <a name="what-you-will-do"></a>执行的操作
下载适用于 Raspberry Pi 3 的第一个示例应用程序的开发工具和软件。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-c-troubleshooting.md)上查找解决方案。

> [!NOTE]
> 尽管主逻辑的编程语言为 C，课程中仍使用 Node.js 工具来发现设备，并生成和部署示例应用程序。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，将学习以下内容：

* 如何安装 Git 和 Node.js。
  * [Git](https://git-scm.com) 是一个开源分布式版本控制系统。 本文的第一个示例应用程序存储在 Git 中。
  * [Node.js](https://nodejs.org/en/) 是一个具有丰富的程序包生态系统的 JavaScript 运行时。
* 如何使用 NPM 安装额外的 Node.js 开发工具。
  * 需要的最低 Node.js 版本为 4.5 LTS。
  * [NPM](https://www.npmjs.com) 是 Node.js 的程序包管理器之一。

## <a name="what-you-need"></a>所需条件
若要完成此操作，需要具有以下项：

* 用于下载开发工具和软件的 Internet 连接。
* 运行 macOS Yosemite (10.10) 或更高版本的 Mac。

## <a name="install-git-and-nodejs"></a>安装 Git 和 Node.js
若要安装 Git 和 Node.js，请按以下步骤使用 [Homebrew](http://brew.sh) 包管理实用工具：

1. 安装 Homebrew。 若已安装 Homebrew，请转到步骤 2。
   
   1. 按 `Cmd + Space` 并输入 `Terminal` 以打开一个终端。
   2. 运行以下命令：
      
      ```bash
      /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      ```
2. 通过运行以下命令安装 Git 和 Node.js：
   
   ```bash
   brew install node git
   ```

## <a name="install-additional-nodejs-development-tools"></a>安装额外的 Node.js 开发工具
使用 [gulp.js](http://gulpjs.com) 将示例应用程序自动部署到 Pi。 使用 [device-discovery-cli](https://github.com/Azure/device-discovery-cli) 检索有关 IoT 设备的网络信息。

通过在终端中运行以下命令安装 `gulp` 和 `device-discovery-cli`：

```bash
sudo npm install -g device-discovery-cli gulp
```

如果无法在 macOS 上安装 Node.js 和这些额外的开发工具，请参阅[故障排除指南](iot-hub-raspberry-pi-kit-c-troubleshooting.md)来查找常见问题的解决方案。

## <a name="install-visual-studio-code"></a>安装 Visual Studio Code
[下载](https://code.visualstudio.com/docs/setup/osx)并安装 Visual Studio Code。 Visual Studio Code 是一个轻型但功能强大的源代码编辑器，适用于 Windows、Linux 和 macOS。 本教程的下文中将使用此编辑器来编辑示例代码。

## <a name="summary"></a>摘要
你已安装了第一个示例应用程序所需的开发工具和软件。 下一个任务是在 Pi 上创建、部署并运行示例应用程序。

## <a name="next-steps"></a>后续步骤
[创建和部署闪烁的应用程序](iot-hub-raspberry-pi-kit-c-lesson1-deploy-blink-app.md)




<!--HONumber=Dec16_HO1-->


