---
title: "M0 到云：将 Feather M0 WiFi 连接到 Azure IoT 中心 | Microsoft Docs"
description: "在本教程中了解如何设置 Adafruit Feather M0 WiFi 并将其连接到 Azure IoT 中心，以便将数据发送到 Azure 云平台。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.assetid: 51befcdb-332b-416f-a6a1-8aabdb67f283
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/12/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 19be73fd0aec3a8f03a7cd83c12cfcc060f6e5e7
ms.openlocfilehash: 8eee4b2eea165176bddf0896685636bbac4db321
ms.contentlocale: zh-cn
ms.lasthandoff: 07/13/2017

---

# <a name="connect-adafruit-feather-m0-wifi-to-azure-iot-hub-in-the-cloud"></a>将 Adafruit Feather M0 WiFi 连接到云中的 Azure IoT 中心
[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

![BME280、Feather M0 WiFi 与 IoT 中心之间的连接](media/iot-hub-adafruit-feather-m0-wifi-get-started/1_connection-m0-feather-m0-iot-hub.png)

在本教程中，从学习如何使用 Arduino 开发板的基础知识开始。 然后将学习如何使用 [Azure IoT 中心](iot-hub-what-is-iot-hub.md)将设备无缝连接到云。

## <a name="what-you-do"></a>准备工作

将 Adafruit Feather M0 WiFi 连接到创建的 IoT 中心。 然后，在 M0 WiFi 上运行一个示例应用程序，用于从 BME280 收集温度和湿度数据。 最后，将传感器数据发送到 IoT 中心。


## <a name="what-you-learn"></a>学习内容

* 如何创建 IoT 中心以及注册 Feather M0 WiFi 的设备
* 如何将 Feather M0 WiFi 与传感器和计算机相连接
* 如何在 Feather M0 WiFi 上运行示例应用程序来收集传感器数据
* 如何将传感器数据发送到 IoT 中心

## <a name="what-you-need"></a>所需条件

![本教程所需的部件](media/iot-hub-adafruit-feather-m0-wifi-get-started/2_parts-needed-for-the-tutorial.png)

若要完成此操作，需要使用 Feather M0 WiFi 初学者工具包中的以下部件：

* Feather M0 WiFi 开发板
* Micro USB 转 Type A USB 线缆

还需要为开发环境做好以下准备：

* 一个有效的 Azure 订阅。 如果没有 Azure 帐户，只需几分钟时间就能[创建一个免费的 Azure 试用帐户](https://azure.microsoft.com/free/)。
* 运行 Windows 或 Ubuntu 的 Mac 或 PC。
* Feather M0 WiFi 要连接到的无线网络。
* 建立 Internet 连接，以便下载配置工具。
* [Arduino IDE](https://www.arduino.cc/en/main/software) 1.6.8 版或更高版本。 早期版本不适用于 Azure IoT 中心库。

如果没有传感器，以下各项是可选的。 也可以使用模拟的传感器数据：

* BME280 温度和湿度传感器
* 试验板
* M/M 跳线

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="connect-feather-m0-wifi-with-the-sensor-and-your-computer"></a>将 Feather M0 WiFi 与传感器和计算机相连接
在此部分中，将传感器连接到开发板。 然后，将设备插入计算机以供进一步使用。

### <a name="connect-a-dht22-temperature-and-humidity-sensor-to-feather-m0-wifi"></a>将 DHT22 温度和湿度传感器连接到 Feather M0 WiFi

使用试验板和跳线建立连接。 如果没有传感器，请跳过本部分，因为可以改用模拟的传感器数据。

![连接参考](media/iot-hub-adafruit-feather-m0-wifi-get-started/3_connections_on_breadboard.png)


对于传感器引脚，请使用以下接线：


| 启动（传感器）           | 结束（开发板）            | 线缆颜色   |
| -----------------------  | ---------------------- | ------------: |
| VDD（引脚 27A）            | 3V（引脚 3A）            | 红线     |
| GND（引脚 29A）            | GND（引脚 6A）           | 黑线   |
| SCK（引脚 30A）            | SCK（引脚 12A）          | 黄色电缆  |
| SDO（引脚 31A）            | MI（引脚 14A）           | 白线   |
| SDI（引脚 32A）            | M0（引脚 13A）           | 蓝线    |
| CS（引脚 33A）             | GPIO 5（引脚 15J）       | 橙色电缆  |

有关详细信息，请参阅 [Adafruit BME280 湿度 + 大气压力 + 温度传感器分类](https://learn.adafruit.com/adafruit-bme280-humidity-barometric-pressure-temperature-sensor-breakout/wiring-and-test?view=all)和 [Adafruit Feather M0 WiFi 引出线](https://learn.adafruit.com/adafruit-feather-m0-wifi-atwinc1500/pinouts)。



现在，Feather M0 WiFi 应已连接到正常工作的传感器。

![将 DHT22 与 Feather Huzzah 相连接](media/iot-hub-adafruit-feather-m0-wifi-get-started/4_connect-bme280-feather-m0-wifi.png)

### <a name="connect-feather-m0-wifi-to-your-computer"></a>将 Feather M0 WiFi 连接到计算机

按如下所示，使用 Micro USB 转 Type A USB 线缆将 Feather M0 WiFi 连接到计算机：

![将 Feather Huzzah 连接到计算机](media/iot-hub-adafruit-feather-m0-wifi-get-started/5_connect-feather-m0-wifi-computer.png)

### <a name="add-serial-port-permissions-ubuntu-only"></a>添加串行端口权限（仅适用于 Ubuntu）

如果使用 Ubuntu，确保有权在 Feather M0 WiFi 的 USB 端口上操作。 若要添加串行端口权限，请执行以下步骤：


1. 在终端运行以下命令：

   ```bash
   ls -l /dev/ttyUSB*
   ls -l /dev/ttyACM*
   ```

   将返回以下输出之一：

   * crw-rw---- 1 root uucp xxxxxxxx
   * crw-rw---- 1 root dialout xxxxxxxx

   请注意，在输出中，`uucp` 或 `dialout` 是 USB 端口的组所有者名称。

2. 若要将用户添加到该组中，请运行以下命令：

   ```bash
   sudo usermod -a -G <group-owner-name> <username>
   ```

   在上一步骤中已获取组所有者名称 `<group-owner-name>`。 你的 Ubuntu 用户名为 `<username>`。

3. 若要显示更改，请注销 Ubuntu 并重新登录。

## <a name="collect-sensor-data-and-send-it-to-your-iot-hub"></a>收集传感器数据并将其发送到 IoT 中心

在本部分中，将在 Feather M0 WiFi 上部署并运行一个示例应用程序。 该示例应用程序会使 Feather M0 WiFi 上的 LED 闪烁。 然后，它将从 BME280 传感器收集的温度和湿度数据发送到 IoT 中心。

### <a name="get-the-sample-application-from-github-and-prepare-the-arduino-ide"></a>从 GitHub 获取示例应用程序并准备 Arduino IDE

该示例应用程序托管在 GitHub 中。 从 GitHub 中克隆包含该示例应用程序的示例存储库。 若要克隆示例存储库，请执行以下步骤：

1. 打开命令提示符或终端窗口。

2. 转到用于存储示例应用程序的文件夹。
3. 运行以下命令：

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-Feather-M0-WiFi-client-app.git
   ```

### <a name="install-the-package-for-feather-m0-wifi-in-the-arduino-ide"></a>在 Arduino IDE 中安装 Feather M0 WiFi 的程序包

1. 打开存储示例应用程序的文件夹。

2. 在 Arduino IDE 中打开 app 文件夹中的 app.ino 文件。

   ![在 Arduino IDE 中打开示例应用程序](media/iot-hub-adafruit-feather-m0-wifi-get-started/6_arduino-ide-open-sample-app.png)


1. 单击“文件” > “首选项”(Windows/Linux) 或“Arduino” > “首选项”(Mac)，将以下链接复制并粘贴到 Arduino IDE 首选项中的“其他开发板管理器 URL”选项中。
   
   ```
   https://adafruit.github.io/arduino-board-index/package_adafruit_index.json
   ```

1. 依次单击“工具” > “开发板” > “开发板管理器”，然后安装 `Arduino SAMD Boards` 版本 `1.6.2` 或更高版本。 

1. 然后，在同一个窗口中安装 `Adafruit SAMD Boards` 包以添加开发板文件定义。

   ![已安装 esp8266 包](media/iot-hub-adafruit-feather-m0-wifi-get-started/7_arduino-ide-package-url.png)

4. 依次单击“工具” > “开发板” > “Adafruit M0 WiFi”。

5. 安装驱动程序（仅适用于 Windows）。 插入 Feather M0 WiFi 时，可能需要安装驱动程序。 单击[网页上的下载链接](https://github.com/adafruit/Adafruit_Windows_Drivers/releases/download/1.1/adafruit_drivers.exe)下载驱动程序安装程序。 按照以下步骤安装所需的驱动程序。

### <a name="install-necessary-libraries"></a>安装所需的库

1. 在 Arduino IDE 中，单击“Sketch” > “Include Library”（包含库） > “Manage Libraries”（管理库）。

2. 逐个搜索以下库名称。 对于找到的每个库，单击“安装”：

   * `RTCZero`
   * `NTPClient`
   * `AzureIoTHub`
   * `AzureIoTUtility`
   * `AzureIoTProtocol_HTTP`
   * `ArduinoJson`
   * `Adafruit BME280 Library`
   * `Adafruit Unified Sensor`

3. 手动安装 `Adafruit_WINC1500`。 转到[此网站](https://github.com/adafruit/Adafruit_WINC1500)，单击“克隆或下载” > “下载 ZIP”。 然后，在 Arduino IDE 中，转到“草图” > “包含库” > “添加 .zip 库”并添加该 zip 文件。

### <a name="use-the-sample-application-if-you-dont-have-a-real-bme280-sensor"></a>如果没有真正的 BME280 传感器，请使用示例应用程序

如果没有真正的 BME280 传感器，示例应用程序可以模拟温度和湿度数据。 若要设置示例应用程序以使用模拟数据，请按照以下步骤操作：

1. 打开 `app` 文件夹中的 `config.h` 文件。

2. 找到以下代码行并将值从 `false` 更改为 `true`：

   ```c
   define SIMULATED_DATA true
   ```
   ![将示例应用程序配置为使用模拟数据](media/iot-hub-adafruit-feather-m0-wifi-get-started/8_arduino-ide-configure-app-use-simulated-data.png)

3. 使用 `Control-s` 保存文件。

### <a name="deploy-the-sample-application-to-feather-m0-wifi"></a>将示例应用程序部署到 Feather M0 WiFi

1. 在 Arduino IDE 中，依次单击“工具” > “端口”，然后单击 Feather M0 WiFi 的串行端口。

2. 依次单击“草图” > “上传”，生成示例应用程序并将其部署到 Feather M0 WiFi。

### <a name="enter-your-credentials"></a>输入凭据

上传成功完成后，按照以下步骤输入凭据：

1. 在 Arduino IDE 中，单击“Tools”（工具） > “Serial Monitor”（串行监视器）。

2. 在串行监视器窗口右下角，选择左侧下拉列表中的“无行尾”。
3. 选择右侧下拉列表中的“115200 波特率”。
4. 在顶部的输入框中输入以下信息（如果系统要求提供），单击“发送”：

   * Wi-Fi SSID
   * Wi-Fi 密码
   * 设备连接字符串

> [!Note]
> 凭据信息将存储在 Feather M0 WiFi 的 EEPROM 中。 如果在 Feather M0 WiFi 开发板上单击重置按钮，示例应用程序将询问是否要擦除这些信息。 输入 `Y` 以擦除信息。 按提示再次提供这些信息。

### <a name="verify-that-the-sample-application-is-running-successfully"></a>验证示例应用程序是否成功运行

如果串行监视器窗口中显示以下输出并且 Feather M0 WiFi 上的 LED 闪烁，则表明示例应用程序已成功运行：

![Arduino IDE 中的最终输出](media/iot-hub-adafruit-feather-m0-wifi-get-started/9_arduino-ide-final-output.png)

## <a name="next-steps"></a>后续步骤

现已成功将 Feather M0 WiFi 连接到 IoT 中心，并将捕获的传感器数据发送到了 IoT 中心。 

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]


