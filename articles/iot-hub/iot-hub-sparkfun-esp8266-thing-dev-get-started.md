---
title: "ESP8266 到云 - 将 Sparkfun ESP8266 Thing Dev 连接到 Azure IoT 中心 | Microsoft Docs"
description: "有关将 Arduino 设备 Sparkfun ESP8266 Thing Dev 连接到 Azure IoT 中心（可帮助管理 IoT 资产的 Microsoft 云服务）的指南。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: 
ms.assetid: 587fe292-9602-45b4-95ee-f39bba10e716
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 0b7ce531c73d991897f1c35932e795a97dd33162
ms.contentlocale: zh-cn
ms.lasthandoff: 06/28/2017


---
# 将 Sparkfun ESP8266 Thing Dev 连接到云中的 Azure IoT 中心
<a id="connect-sparkfun-esp8266-thing-dev-to-azure-iot-hub-in-the-cloud" class="xliff"></a>

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![DHT22、Thing Dev 与 IoT 中心之间的连接](media/iot-hub-sparkfun-thing-dev-get-started/1_connection-hdt22-thing-dev-iot-hub.png)

## 执行的操作
<a id="what-you-will-do" class="xliff"></a>

将 Sparkfun ESP8266 Thing Dev 连接到创建的 Azure IoT 中心。 然后，在 ESP8266 上运行一个示例应用程序，用于从 DHT22 传感器收集温度和湿度数据。 最后，将传感器数据发送到 IoT 中心。

> [!NOTE]
> 如果使用其他 ESP8266 开发板，仍可遵循这些步骤将它连接到 IoT 中心。 根据所用的 ESP8266 开发板，可能需要重新配置 `LED_PIN`。 例如，如果使用 AI-Thinker 提供的 ESP8266，可将此参数从 `0` 更改为 `2`。 没有工具包？请单击[此处](http://azure.com/iotstarterkits)

## 你要学习的知识
<a id="what-you-will-learn" class="xliff"></a>

* 如何创建 IoT 中心以及注册 Thing Dev 的设备。
* 如何将 Thing Dev 与传感器和计算机相连接。
* 如何在 Thing Dev 上运行示例应用程序来收集传感器数据。
* 如何将传感器数据发送到 IoT 中心。

## 所需的项目
<a id="what-you-will-need" class="xliff"></a>

![本教程所需的部件](media/iot-hub-sparkfun-thing-dev-get-started/2_parts-needed-for-the-tutorial.png)

若要完成此操作，需要使用 Thing Dev 初学者工具包中的以下部件：

* Sparkfun ESP8266 Thing Dev 开发板。
* Micro USB 转 Type A USB 线缆。

还需要为开发环境做好以下准备：

* 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需几分钟时间就能[创建一个免费的 Azure 试用帐户](https://azure.microsoft.com/free/)。
* 运行 Windows 或 Ubuntu 的 Mac 或 PC。
* Sparkfun ESP8266 Thing Dev 要连接到的无线网络。
* 建立 Internet 连接，以便下载配置工具。
* [Arduino IDE](https://www.arduino.cc/en/main/software) 版本 1.6.8（或更高版本），早期版本不适用于 AzureIoT 库。

如果没有传感器，以下各项是可选的。 也可以使用模拟的传感器数据。

* Adafruit DHT22 温度和湿度传感器。
* 试验板。
* M/M 跳线。

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## 将 ESP8266 Thing Dev 与传感器和计算机相连接
<a id="connect-esp8266-thing-dev-with-the-sensor-and-your-computer" class="xliff"></a>

### 将 DHT22 温度和湿度传感器连接到 ESP8266 Thing Dev
<a id="connect-a-dht22-temperature-and-humidity-sensor-to-esp8266-thing-dev" class="xliff"></a>

按如下所示，使用试验板和跳线建立连接。 如果没有传感器，请跳过本部分，因为可以改用模拟的传感器数据。

![连接参考](media/iot-hub-sparkfun-thing-dev-get-started/15_connections_on_breadboard.png)

我们使用以下传感器引脚连线方式：

| 启动（传感器）           | 结束（开发板）           | 线缆颜色   |
| -----------------------  | ---------------------- | ------------: |
| VDD（引脚 27F）            | 3V（引脚 8A）           | 红线     |
| DATA（引脚 28F）           | GPIO 2（引脚 9A）       | 白线    |
| GND（引脚 30F）            | GND（引脚 7J）          | 黑线   |


- 有关详细信息，请参阅：[DHT22 传感器设置](http://cdn.sparkfun.com/datasheets/Sensors/Weather/RHT03.pdf)和 [Sparkfun ESP8266 Thing Dev 规范](https://www.sparkfun.com/products/13711)

现在，Sparkfun ESP8266 Thing Dev 应已连接到正常工作的传感器。

![将 dht22 与 ESP8266 Thing Dev 相连接](media/iot-hub-sparkfun-thing-dev-get-started/8_connect-dht22-thing-dev.png)

### 将 Sparkfun ESP8266 Thing Dev 连接到计算机
<a id="connect-sparkfun-esp8266-thing-dev-to-your-computer" class="xliff"></a>

按如下所示，使用 Micro USB 转 Type A USB 线缆将 Sparkfun ESP8266 Thing Dev 连接到计算机。

![将 feather huzzah 连接到计算机](media/iot-hub-sparkfun-thing-dev-get-started/9_connect-thing-dev-computer.png)

### 添加串行端口权限 - 仅适用于 Ubuntu
<a id="add-serial-port-permissions--ubuntu-only" class="xliff"></a>

如果使用 Ubuntu，请确保普通用户有权在 Sparkfun ESP8266 Thing Dev 的 USB 端口上操作。 若要为普通用户添加串行端口权限，请执行以下步骤：

1. 在终端中运行以下命令：

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   将返回以下输出之一：

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   请注意，在输出中，`uucp` 或 `dialout` 是 USB 端口的组所有者名称。

1. 运行以下命令，将用户添加到该组中：

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   `<group-owner-name>` 是在上一步骤中获取的组所有者名称。 `<username>` 是你的 Ubuntu 用户名。

1. 从 Ubuntu 中注销，然后再次登录，使更改生效。

## 收集传感器数据并将其发送到 IoT 中心
<a id="collect-sensor-data-and-send-it-to-your-iot-hub" class="xliff"></a>

在本部分，你将在 Sparkfun ESP8266 Thing Dev 上部署并运行一个示例应用程序。 该示例应用程序会使 Sparkfun ESP8266 Thing Dev 上的 LED 闪烁，并将从 DHT22 传感器收集的温度和湿度数据发送到 IoT 中心。

### 从 GitHub 获取示例应用程序
<a id="get-the-sample-application-from-github" class="xliff"></a>

该示例应用程序托管在 GitHub 中。 从 GitHub 中克隆包含该示例应用程序的示例存储库。 若要克隆示例存储库，请执行以下步骤：

1. 打开命令提示符或终端窗口。
1. 转到用于存储示例应用程序的文件夹。
1. 运行以下命令：

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-SparkFun-ThingDev-client-app.git
   ```

在 Arduino IDE 中安装 Sparkfun ESP8266 Thing Dev 的程序包：

1. 打开存储示例应用程序的文件夹。
1. 在 Arduino IDE 中打开 app 文件夹中的 app.ino 文件。

   ![在 arduino ide 中打开示例应用程序](media/iot-hub-sparkfun-thing-dev-get-started/10_arduino-ide-open-sample-app.png)

1. 在 Arduino IDE 中，单击“File”（文件） > “Preferences”（首选项）。
1. 在“Preferences”（首选项）对话框中，单击“Additional Boards Manager URLs”（其他 Boards Manager URL）文本框旁边的图标。
1. 在弹出窗口中输入以下 URL，然后单击“OK”（确定）。

   `http://arduino.esp8266.com/stable/package_esp8266com_index.json`

   ![指向 arduino ide 中的包 url](media/iot-hub-sparkfun-thing-dev-get-started/11_arduino-ide-package-url.png)

1. 在“Preference”（首选项）对话框中，单击“OK”（确定）。
1. 单击“Tools”（工具） > “Board”（开发板） > “Boards Manager”，然后搜索 esp8266。
   应安装 2.2.0 或更高版本的 ESP8266。

   ![已安装 esp8266 包](media/iot-hub-sparkfun-thing-dev-get-started/12_arduino-ide-esp8266-installed.png)

1. 单击“Tools”（工具） > “Board”（开发板） > “Sparkfun ESP8266 Thing Dev”。

### 安装所需的库
<a id="install-necessary-libraries" class="xliff"></a>

1. 在 Arduino IDE 中，单击“Sketch” > “Include Library”（包含库） > “Manage Libraries”（管理库）。
1. 逐个搜索以下库名称。 对于找到的每个库，单击“Install”（安装）。
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_MQTT`
   * `ArduinoJson`
   * `DHT sensor library`
   * `Adafruit Unified Sensor`

### 没有真正的 DHT22 传感器？
<a id="dont-have-a-real-dht22-sensor" class="xliff"></a>

如果你没有真正的 DHT22 传感器，示例应用程序可以模拟温度和湿度数据。 若要让示例应用程序使用模拟的数据，请执行以下步骤：

1. 打开 `app` 文件夹中的 `config.h` 文件。
1. 找到以下代码行并将值从 `false` 更改为 `true`：
   ```c
   define SIMULATED_DATA true
   ```
   ![将示例应用程序配置为使用模拟的数据](media/iot-hub-sparkfun-thing-dev-get-started/13_arduino-ide-configure-app-use-simulated-data.png)
   
1. 使用 `Control-s` 保存该文件。

### 将示例应用程序部署到 Sparkfun ESP8266 Thing Dev
<a id="deploy-the-sample-application-to-sparkfun-esp8266-thing-dev" class="xliff"></a>

1. 在 Arduino IDE 中，单击“Tool”（工具） > “Port”（端口），然后单击 Sparkfun ESP8266 Thing Dev 的串行端口。
1. 单击 **Sketch** > **Upload** （上传），生成示例应用程序并将其部署到 Sparkfun ESP8266 Thing Dev。

### 输入凭据
<a id="enter-your-credentials" class="xliff"></a>

上传成功完成后，请执行以下步骤输入凭据：

1. 在 Arduino IDE 中，单击“Tools”（工具） > “Serial Monitor”（串行监视器）。
1. 在串行监视器窗口的右下角，可以看到两个下拉列表。
1. 在左侧下拉列表中选择“No line ending”（无行尾）。
1. 在右侧下拉列表中选择“115200 baud”（115200 波特率）。
1. 在串行监视器窗口顶部的输入框中输入以下信息（如果系统要求提供），然后单击“Send”（发送）。
   * Wi-Fi SSID
   * Wi-Fi 密码
   * 设备连接字符串

> [!Note]
> 凭据信息将存储在 Sparkfun ESP8266 Thing Dev 的 EEPROM 中。 如果在 Sparkfun ESP8266 Thing Dev 开发板上单击重置按钮，示例应用程序将询问是否要擦除这些信息。 输入 `Y` 可擦除信息，系统会再次要求提供这些信息。

### 验证示例应用程序是否成功运行
<a id="verify-the-sample-application-is-running-successfully" class="xliff"></a>

如果串行监视器窗口中显示以下输出并且 Sparkfun ESP8266 Thing Dev 上的 LED 闪烁，则表示示例应用程序已成功运行。

![arduino ide 中的最终输出](media/iot-hub-sparkfun-thing-dev-get-started/14_arduino-ide-final-output.png)

## 后续步骤
<a id="next-steps" class="xliff"></a>

现已成功将 Sparkfun ESP8266 Thing Dev 连接到 IoT 中心，并将捕获的传感器数据发送到了 IoT 中心。 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

