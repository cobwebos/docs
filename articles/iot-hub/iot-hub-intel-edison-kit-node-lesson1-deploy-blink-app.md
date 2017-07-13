---
title: "将 Intel Edison（节点）连接到 Azure IoT - 第 1 课：部署应用 | Microsoft Docs"
description: "克隆 GitHub 提供的示例 C 应用程序，并使用 gulp 工具将此应用程序部署到 Intel Edison 开发板。 此示例应用程序每隔两秒让连接到板的 LED 闪烁一次。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: "arduino led 项目, arduino led 闪烁, arduino led 闪烁代码, arduino 闪烁程序, arduino 闪烁示例"
ROBOTS: NOINDEX
redirect_url: /azure/iot-hub/iot-hub-intel-edison-kit-node-get-started
ms.assetid: ed2c21d0-c72c-4ac2-9e70-347e9a0711c0
ms.service: iot-hub
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/21/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: adf5b10721a28432e6b37ef73c6a7e7ec9f93cdd
ms.openlocfilehash: 182e63d40986b61a12885799b1b33bb570437a3c
ms.contentlocale: zh-cn
ms.lasthandoff: 01/25/2017

---
<a id="create-and-deploy-the-blink-application" class="xliff"></a>

# 创建并部署 blink 应用程序
<a id="what-you-will-do" class="xliff"></a>

## 执行的操作
克隆 GitHub 提供的示例 C 应用程序，并使用 gulp 工具将该示例应用程序部署到 Intel Edison。 此示例应用程序让连接到电路板的 LED 每隔两秒闪烁一次。 如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

<a id="what-you-will-learn" class="xliff"></a>

## 你要学习的知识
* 如何在 Edison 上部署和运行示例应用程序。

<a id="what-you-need" class="xliff"></a>

## 需要什么
必须已成功完成以下操作：

* [配置设备][configure-your-device]
* [获取工具][get-the-tools]

<a id="open-the-sample-application" class="xliff"></a>

## 打开示例应用程序
若要打开示例应用程序，请执行以下步骤：

1. 通过运行以下命令克隆 GitHub 中的示例存储库：

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-edison-getting-started.git
   ```
2. 通过运行以下命令在 Visual Studio Code 中打开示例应用程序：

   ```bash
   cd iot-hub-node-edison-getting-started
   cd Lesson1
   code .
   ```

   ![存储库结构][repo-structure]

`app` 子文件夹中的文件是重要的源文件，其中包含用于控制 LED 的代码。

<a id="install-application-dependencies" class="xliff"></a>

### 安装应用程序依赖项
通过运行以下命令安装示例应用程序所需的库和其他模块：

```bash
npm install
```

<a id="configure-the-device-connection" class="xliff"></a>

## 配置设备连接
若要配置设备连接，请执行以下步骤：

1. 运行以下命令，生成设备配置文件：

   ```bash
   gulp init
   ```

   配置文件 `config-edison.json` 包含用于登录到 Edison 的用户凭据。 为了避免用户凭据泄漏，配置文件是在计算机上主文件夹的 `.iot-hub-getting-started` 子文件夹中生成的。

2. 通过运行以下命令在 Visual Studio Code 中打开设备配置文件：

   ```bash
   # For Windows command prompt
   code %USERPROFILE%\.iot-hub-getting-started\config-edison.json

   # For MacOS or Ubuntu
   code ~/.iot-hub-getting-started/config-edison.json
   ```

3. 将占位符 `[device hostname or IP address]` 和 `[device password]` 替换为上一课中记录的 IP 地址和密码。

   ![Config.json](media/iot-hub-intel-edison-lessons/lesson1/vscode-config-mac.png)

祝贺你！ Edison 的第一个示例应用程序已创建成功。

<a id="deploy-and-run-the-sample-application" class="xliff"></a>

## 部署并运行示例应用程序

<a id="deploy-and-run-the-sample-app" class="xliff"></a>

### 部署并运行示例应用
通过运行以下命令部署并运行示例应用程序：

```bash
gulp deploy && gulp run
```

<a id="verify-the-app-works" class="xliff"></a>

### 确保应用正常运行
LED 闪烁 20 次后，示例应用程序会自动终止。 如果未看到 LED 闪烁，请参阅[故障排除指南][troubleshooting]来查找常见问题的解决方案。

![LED 闪烁][led-blinking]

<a id="summary" class="xliff"></a>

## 摘要
已安装适用于 Edison 的必需工具，并已将使 LED 闪烁的示例应用程序部署到 Edison。 现在可以创建、部署以及运行其他示例应用程序，以便将 Edison 连接到发送和接收消息的 Azure IoT 中心。

<a id="next-steps" class="xliff"></a>

## 后续步骤
[获取 Azure 工具][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-node-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-node-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-node-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-intel-edison-kit-node-lesson2-get-azure-tools-win32.md

