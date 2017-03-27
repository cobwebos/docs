---
title: "将 Intel Edison（节点）连接到 Azure IoT - 第 1 课：获取工具 (Ubuntu) | Microsoft Docs"
description: "下载并安装用于 Ubuntu 上的 Edison 的第一个示例应用程序的必需工具和软件。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino 开发工具, iot 开发, iot 软件, 物联网软件, 在 ubuntu 上安装 git, 安装 node js ubuntu"
ms.assetid: 9ab5b161-7ec5-41a6-9c5f-4456e4882752
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: c56699b81d83119ca1822c2efa6b2380b3619290
ms.lasthandoff: 01/25/2017


---
# <a name="get-the-tools-ubuntu-1604"></a>获取工具 (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 或更高版本][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

## <a name="what-you-will-do"></a>执行的操作
下载用于 Intel Edison 的第一个示例应用程序的开发工具和软件。 如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
本文介绍：

* 如何安装 Git 和 Node.js
  * [Git](https://git-scm.com) 是一个开源分布式版本控制系统。 本文的第一个示例应用程序存储在 Git 中。
  * [Node.js](https://nodejs.org/en/) 是一个具有丰富的程序包生态系统的 JavaScript 运行时。
* 如何使用 NPM 安装额外的 Node.js 开发工具。
  * 需要的最低 Node.js 版本为 4.5 LTS。
  * [NPM](https://www.npmjs.com) 是 Node.js 的程序包管理器之一。

## <a name="what-you-need"></a>所需条件
若要完成此操作，需要具有以下项：
* 用于下载开发工具和软件的 Internet 连接。
* 运行 Ubuntu 16.04 或更高版本的计算机。

## <a name="install-git-nodejs-and-npm"></a>安装 Git、Node.js 和 NPM
使用键盘快捷键 `Ctrl + Alt + T` 打开一个终端并运行以下命令：

```bash
sudo apt-get update
curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt-get install git
```

## <a name="install-additional-nodejs-development-tools"></a>安装额外的 Node.js 开发工具
使用 [gulp.js](http://gulpjs.com) 将示例应用程序自动部署到 Edison。

在终端运行以下命令，安装 `gulp`：

```bash
sudo npm install -g gulp
```

如果在 Ubuntu 上安装 Node.js 和这些额外的开发工具时遇到问题，请参阅[故障排除指南][troubleshooting]来查找常见问题的解决方案。

## <a name="install-visual-studio-code"></a>安装 Visual Studio Code
[下载](https://code.visualstudio.com/docs/setup/linux)并安装 Visual Studio Code。 Visual Studio Code 是一个轻型但功能强大的源代码编辑器，适用于 Windows、Linux 和 macOS。 本教程的下文中将使用此编辑器来编辑示例代码。

## <a name="summary"></a>摘要
用户已为第一个示例应用程序安装所需的开发工具和软件。 下一任务是在 Edison 上创建、部署和运行示例应用程序。

## <a name="next-steps"></a>后续步骤
[创建并部署闪烁示例应用程序][create-and-deploy-the-blink-application]
<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-intel-edison-kit-node-lesson1-deploy-blink-app.md
[windows]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-mac.md

