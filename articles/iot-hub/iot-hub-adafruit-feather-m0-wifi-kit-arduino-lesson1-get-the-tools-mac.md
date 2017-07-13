---
title: "将 Arduino 连接到 Azure IoT - 第 1 课：获取工具 (macOS) | Microsoft Docs"
description: "下载并安装在 macOS 上适用于 Adafruit Feather M0 WiFi 的第一个示例应用程序的必需工具和软件。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "arduino 开发工具, iot 开发, iot 软件, 物联网软件, 在 mac 上安装 git, 安装 node js mac"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started
ms.assetid: 0262f3dd-0259-4eb0-962f-9fb534f8359e
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 41b2277a26f0e99052a699cfab8a8129a6c7ddb1
ms.contentlocale: zh-cn
ms.lasthandoff: 01/24/2017

---
<a id="get-the-tools-macos-1010" class="xliff"></a>

# 获取工具 (macOS 10.10)
> [!div class="op_single_selector"]
> * [Windows 7 或更高版本][windows]
> * [Ubuntu 16.04][ubuntu]
> * [macOS 10.10][macos]

<a id="what-you-will-do" class="xliff"></a>

## 执行的操作

下载适用于 Adafruit Feather M0 WiFi Arduino 开发板的第一个示例应用程序的开发工具和软件。 

如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

> [!NOTE]
> 尽管主逻辑的编程语言为 Arduino，课程中仍使用 Node.js 工具生成和部署示例应用程序。

<a id="what-you-will-learn" class="xliff"></a>

## 你要学习的知识
在本文中，将学习以下内容：

* 如何安装 Git 和 Node.js。
  * [Git](https://git-scm.com) 是一个开源分布式版本控制系统。 本文的第一个示例应用程序存储在 Git 中。
  * [Node.js](https://nodejs.org/en/) 是一个具有丰富的程序包生态系统的 JavaScript 运行时。
* 如何使用 NPM 安装额外的 Node.js 开发工具。
  * 需要的最低 Node.js 版本为 4.5 LTS。
  * [NPM](https://www.npmjs.com) 是 Node.js 的程序包管理器之一。

<a id="what-you-need" class="xliff"></a>

## 所需条件
若要完成此操作，需要具有以下项：
* 用于下载开发工具和软件的 Internet 连接。
* 运行 macOS Yosemite (10.10) 或更高版本的 Mac。

<a id="install-git-and-nodejs" class="xliff"></a>

## 安装 Git 和 Node.js
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

<a id="install-additional-nodejs-development-tools" class="xliff"></a>

## 安装额外的 Node.js 开发工具
使用 [gulp.js](http://gulpjs.com) 将示例应用程序自动部署到 Arduino 开发板。

在终端运行以下命令，安装 `gulp`、`device-discovery-cli`：

```bash
sudo npm install -g gulp device-discovery-cli
```

如果在 macOS 上安装 Node.js 和这些额外的开发工具时遇到问题，请参阅[故障排除指南][troubleshooting]以获取常见问题的解决方案。

<a id="install-visual-studio-code" class="xliff"></a>

## 安装 Visual Studio Code
[下载](https://code.visualstudio.com/docs/setup/osx)并安装 Visual Studio Code。 Visual Studio Code 是一个轻型但功能强大的源代码编辑器，适用于 Windows、Linux 和 macOS。 本教程的下文中将使用此编辑器来编辑示例代码。

<a id="summary" class="xliff"></a>

## 摘要
你已安装了第一个示例应用程序所需的开发工具和软件。 下一个任务是在 Arduino 开发板上创建、部署并运行示例应用程序。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[创建和部署闪烁应用程序][create-and-deploy-the-blink-application]
<!-- Images and links -->

[windows]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[ubuntu]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-ubuntu.md
[macos]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-mac.md
[troubleshooting]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[create-and-deploy-the-blink-application]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-deploy-blink-app.md
