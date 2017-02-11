---
title: "将闪烁应用程序部署到 Azure IoT 初学者工具包中 | Microsoft Docs"
description: "克隆 GitHub 提供的示例 C 应用程序，并使用 gulp 工具将此应用程序部署到 Intel Edison 开发板。 此示例应用程序每隔两秒让连接到板的 LED 闪烁一次。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino led 项目, arduino led 闪烁, arduino led 闪烁代码, arduino 闪烁程序, arduino 闪烁示例"
ms.assetid: b02dfd3f-28fd-4b52-8775-eb0eaf74d707
ms.service: iot-hub
ms.devlang: c
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/8/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 604ab48856f3fbc09ca6b566ef3f7e1d18e912c6
ms.openlocfilehash: 6c911297be4fa2c7ce6bd817233e8a8012634f42


---
# <a name="create-and-deploy-the-blink-application"></a>创建并部署 blink 应用程序
## <a name="what-you-will-do"></a>执行的操作
克隆 GitHub 提供的示例 C 应用程序，并使用 gulp 工具将该示例应用程序部署到 Intel Edison。 此示例应用程序让连接到电路板的 LED 每隔两秒闪烁一次。 如果有任何问题，请在[故障排除页面][troubleshooting]上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
* 如何在 Edison 上部署和运行示例应用程序。

## <a name="what-you-need"></a>需要什么
必须已成功完成以下操作：

* [配置设备][configure-your-device]
* [获取工具][get-the-tools]

## <a name="open-the-sample-application"></a>打开示例应用程序
若要打开示例应用程序，请执行以下步骤：

1. 通过运行以下命令克隆 GitHub 中的示例存储库：

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-edison-getting-started.git
   ```
2. 通过运行以下命令在 Visual Studio Code 中打开示例应用程序：

   ```bash
   cd iot-hub-c-edison-getting-started
   cd Lesson1
   code .
   ```

   ![存储库结构][repo-structure]

`app` 子文件夹中的文件是重要的源文件，其中包含用于控制 LED 的代码。

### <a name="install-application-dependencies"></a>安装应用程序依赖项
通过运行以下命令安装示例应用程序所需的库和其他模块：

```bash
npm install
```

## <a name="configure-the-device-connection"></a>配置设备连接
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

## <a name="deploy-and-run-the-sample-application"></a>部署并运行示例应用程序
### <a name="install-the-azure-iot-hub-sdk-on-edison"></a>在 Edison 上安装 Azure IoT 中心 SDK
通过运行以下命令，在 Edison 上安装 Azure IoT 中心 SDK：

```bash
gulp install-tools
```

此任务可能需要很长时间才能完成，具体取决于网络连接。 对一个 Edison 只需运行一次此任务。

### <a name="deploy-and-run-the-sample-app"></a>部署并运行示例应用
通过运行以下命令部署并运行示例应用程序：

```bash
gulp deploy && gulp run
```

### <a name="verify-the-app-works"></a>确保应用正常运行
LED 闪烁 20 次后，示例应用程序会自动终止。 如果未看到 LED 闪烁，请参阅[故障排除指南][troubleshooting]来查找常见问题的解决方案。

![LED 闪烁][led-blinking]

## <a name="summary"></a>摘要
已安装适用于 Edison 的必需工具，并已将使 LED 闪烁的示例应用程序部署到 Edison。 现在可以创建、部署以及运行其他示例应用程序，以便将 Edison 连接到发送和接收消息的 Azure IoT 中心。

## <a name="next-steps"></a>后续步骤
[获取 Azure 工具][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting]: iot-hub-intel-edison-kit-c-troubleshooting.md
[Configure-your-device]: iot-hub-intel-edison-kit-c-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-intel-edison-kit-c-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-intel-edison-lessons/lesson1/repo_structure_c.png
[led-blinking]: media/iot-hub-intel-edison-lessons/lesson1/led_blinking_c.jpg
[get-the-azure-tools]: iot-hub-intel-edison-kit-c-lesson2-get-azure-tools-win32.md



<!--HONumber=Dec16_HO2-->


