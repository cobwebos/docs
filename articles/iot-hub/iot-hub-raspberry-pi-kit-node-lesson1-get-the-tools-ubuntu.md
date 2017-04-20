---
title: "将 Raspberry Pi（节点）连接到 Azure IoT - 第 1 课：获取工具 (Ubuntu) | Microsoft Docs"
description: "下载并安装在 Ubuntu 上适用于 Pi 的第一个示例应用程序的必需工具和软件。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "iot 开发, iot 软件, 物联网软件, 在 ubuntu 上安装 git, gulp 运行, 安装 node js ubuntu"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started
ms.assetid: 4d5e45c0-1db9-4662-a039-99ba26333085
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a43690c4fd35a701472b9a1c747e969d1d3a2ea4
ms.lasthandoff: 01/24/2017


---
# <a name="get-the-tools-ubuntu-1604"></a>获取工具 (Ubuntu 16.04)

> [!div class="op_single_selector"]
> * [Windows 7 或更高版本](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
> * [Ubuntu 16.04](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-ubuntu.md)
> * [macOS 10.10](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-mac.md)


## <a name="what-you-will-do"></a>执行的操作
为 Raspberry Pi 3 的第一个示例应用程序下载开发工具和软件。 如果有任何问题，请在[故障排除页面](iot-hub-raspberry-pi-kit-node-troubleshooting.md)上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
在本文中，将学习以下内容：

* 如何安装 Git 和 Node.js。
  * [Git](https://git-scm.com) 是一个开源分布式版本控制系统。 本文的第一个示例应用程序存储在 Git 中。
  * [Node.js](https://nodejs.org/en/) 是一个具有丰富的程序包生态系统的 JavaScript 运行时。
* 如何使用 NPM 安装额外的 Node.js 开发工具。
  * 需要的最低 Node.js 版本为 4.5 LTS。
  * [NPM](https://www.npmjs.com) 是 Node.js 的程序包管理器之一。

## <a name="what-do-you-need"></a>所需条件
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
可使用 [gulp.js](http://gulpjs.com) 将示例应用程序自动部署到 Pi。 还可以使用 [device-discovery-cli](https://github.com/Azure/device-discovery-cli) 检索关于 IoT 设备的网络信息。

通过在终端中运行以下命令安装 `gulp` 和 `device-discovery-cli`：

```bash
sudo npm install -g device-discovery-cli gulp
```

如果在 Ubuntu 上安装 Node.js 和这些额外的开发工具时，请参阅[故障排除指南](iot-hub-raspberry-pi-kit-node-troubleshooting.md)来查找常见问题的解决方案。

## <a name="install-visual-studio-code"></a>安装 Visual Studio Code
[下载](https://code.visualstudio.com/docs/setup/linux)并安装 Visual Studio Code。 Visual Studio Code 是一个轻型但功能强大的源代码编辑器，适用于 Windows、Linux 和 macOS。 本教程的下文中将使用此编辑器来编辑示例代码。

## <a name="summary"></a>摘要
你已安装了第一个示例应用程序所需的开发工具和软件。 下一个任务是在 Pi 上创建、部署并运行示例应用程序。

## <a name="next-steps"></a>后续步骤
[创建并部署 blink 示例应用程序](iot-hub-raspberry-pi-kit-node-lesson1-deploy-blink-app.md)


