---
title: "将 Arduino 连接到 Azure IoT - 第 1 课：部署应用 | Microsoft Docs"
description: "克隆 GitHub 提供的示例 Arduino 应用程序，并运行 gulp 将此应用程序部署到 Adafruit Feather M0 WiFi。 此示例应用程序可以使 GPIO 闪烁"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "arduino led 项目, arduino led 闪烁, arduino led 闪烁代码, arduino 闪烁程序, arduino 闪烁示例"
ms.assetid: b0a7d076-d580-4686-9f7d-c0712750b615
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/13/2016
ms.author: xshi
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: a2058611cecb33eab04de9249ad73b1c4ec6c2a5


---
# <a name="create-and-deploy-the-blink-application"></a>创建并部署 blink 应用程序
## <a name="what-you-will-do"></a>执行的操作
克隆 GitHub 提供的示例 Arduino 应用程序，并使用 gulp 工具将示例应用程序部署到 Adafruit Feather M0 WiFi Arduino 开发板。 此示例应用程序让 GPIO #13 开发板 LED 每两秒闪烁一次。

如果有任何问题，请在[故障排除页面][troubleshooting-page]上查找解决方案。

## <a name="what-you-will-learn"></a>你要学习的知识
* 如何在 Arduino 开发板上部署和运行示例应用程序。

## <a name="what-you-need"></a>所需条件
必须已成功完成以下操作：

* [配置设备][configure-your-device]
* [获取工具][get-the-tools]

## <a name="open-the-sample-application"></a>打开示例应用程序
若要打开示例应用程序，请执行以下步骤：

1. 通过运行以下命令克隆 GitHub 中的示例存储库：

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-c-feather-m0-getting-started.git
   ```
2. 通过运行以下命令在 Visual Studio Code 中打开示例应用程序：

   ```bash
   cd iot-hub-c-feather-m0-getting-started
   cd Lesson1
   code .
   ```

   ![存储库结构][repo-structure]

`app` 子文件夹中的 `app.ino` 文件是关键源文件，其中包含用于控制 LED 的代码。

### <a name="install-application-dependencies"></a>安装应用程序依赖项
通过运行以下命令安装示例应用程序所需的库和其他模块：

```bash
npm install
```

## <a name="configure-the-device-connection"></a>配置设备连接
若要配置设备连接，请执行以下步骤：

1. 使用设备发现 CLI 获取设备的串行端口：

   ```bash
   devdisco list --usb
   ```

   可以看到类似于以下内容的输出，并查找 Arduino 开发板的 usb COM 端口：![设备发现][device-discovery]

2. 打开课程文件夹中的文件 `config.json`，添加找到的 COM 端口号的值：

   ```json
   {
       "device_port" : "COM1"
   }
   ```
   ![config.json][config-json]
   > [!NOTE]
   > 在 Windows 平台上 COM 端口的格式为 `COM1, COM2, ...`。 在 macOS 或 Ubuntu 上，此端口以 `/dev/` 开头。

## <a name="deploy-and-run-the-sample-application"></a>部署并运行示例应用程序
### <a name="install-the-required-tools-for-your-arduino-board"></a>安装 Arduino 开发板的所需工具

通过运行以下命令来安装用于 Arduino 开发板的 Azure IoT 中心 SDK：

```bash
gulp install-tools
```

此任务可能需要很长时间才能完成，具体取决于网络连接。

> [!NOTE]
> 在运行 gulp 任务时，请退出正在运行的 Arduino IDE 实例：`install-tools`, `run`。

### <a name="deploy-and-run-the-sample-app"></a>部署并运行示例应用
通过运行以下命令部署并运行示例应用程序：

```bash
gulp run

# You can monitor the serial port by running listen task:
gulp listen

# Or you can combine above two gulp tasks into one:
gulp run --listen
```

### <a name="verify-the-app-works"></a>验证应用是否正常工作
如果未看到 LED 闪烁，请参阅[故障排除指南][troubleshooting-page]来查找常见问题的解决方案。

![LED 闪烁][led-blinking]

## <a name="summary"></a>摘要
你已安装了使用 Arduino 开发板时必需的工具，并已将使 LED 闪烁的示例应用程序部署到 Arduino 开发板。 现在，你可以创建、部署并运行另一个示例应用程序，以便将 Arduino 开发板连接到 Azure IoT 中心以发送和接收消息。

## <a name="next-steps"></a>后续步骤
[获取 Azure 工具][get-the-azure-tools]

<!-- Images and links -->

[troubleshooting-page]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-troubleshooting.md
[configure-your-device]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-configure-your-device.md
[get-the-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson1-get-the-tools-win32.md
[repo-structure]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-blink-arduino-mac.png
[device-discovery]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/device_discovery.png
[config-json]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/vscode-config-mac.png
[led-blinking]: media/iot-hub-adafruit-feather-m0-wifi-lessons/lesson1/led_blinking.png
[get-the-azure-tools]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-lesson2-get-azure-tools-win32.md


<!--HONumber=Jan17_HO4-->


