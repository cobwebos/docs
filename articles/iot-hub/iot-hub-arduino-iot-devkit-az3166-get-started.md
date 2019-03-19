---
title: IoT DevKit 到云 -- 将 IoT DevKit AZ3166 连接到 Azure IoT 中心 | Microsoft Docs
description: 在本教程中了解如何设置 IoT DevKit AZ3166 并将其连接到 Azure IoT 中心，使其能够将数据发送到 Azure 云平台。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 12/19/2018
ms.author: wesmc
ms.openlocfilehash: e35e669c4abc4815b932e09d369af28e42617e8c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535667"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>将 IoT DevKit AZ3166 连接到 Azure IoT 中心

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 可用于开发物联网 (IoT) 解决方案和构建其原型，以利用 Microsoft Azure 服务。 它包括一个具有大量外围设备和传感器的 Arduino 兼容的板、一个开源板包和一份日益增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-do"></a>准备工作

将 DevKit 连接到创建的 Azure IoT 中心。 然后，从传感器收集温度和湿度数据，并将数据发送到 IoT 中心。

还没有 DevKit？ 请尝试 [DevKit 模拟器](https://azure-samples.github.io/iot-devkit-web-simulator/)或[购买 DevKit](https://aka.ms/iot-devkit-purchase)。

## <a name="what-you-learn"></a>学习内容

* 如何将 IoT DevKit 连接到无线接入点并准备开发环境。
* 如何创建 IoT 中心以及注册 MXChip IoT DevKit 的设备。
* 如何通过在 MXChip IoT DevKit 上运行示例应用程序来收集传感器数据。
* 如何将传感器数据发送到 IoT 中心。

## <a name="what-you-need"></a>所需条件

* 采用 Micro-USB 电缆的 MXChip IoT DevKit 板。 [立即获取](https://aka.ms/iot-devkit-purchase)。
* 运行 Windows 10 或 macOS 10.10 以上版本的计算机。
* 一个有效的 Azure 订阅。 [激活 30 天免费试用版 Microsoft Azure 帐户](https://azureinfo.microsoft.com/us-freetrial.html)。
  
## <a name="prepare-your-hardware"></a>准备硬件

将以下硬件挂接到计算机：

* DevKit 板
* Micro-USB 电缆

![所需硬件](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

请按照以下步骤将 DevKit 连接到计算机：

1. 将 USB 端连接到计算机。

2. 将 Micro-USB 端连接到 DevKit。

3. 电源的绿色 LED 灯确认连接。

   ![硬件连接](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>配置 Wi-Fi

IoT 项目依赖 Internet 连接。 使用以下说明配置 DevKit 连接到 Wi-Fi。

### <a name="enter-ap-mode"></a>进入 AP 模式

长按按钮 B，然后按下重置按钮并松开，再松开按钮 B。DevKit 将进入 AP 模式以配置 Wi-Fi。 屏幕将显示 DevKit 的服务集标识符 (SSID) 和配置门户 IP 地址。

![重置按钮、按钮 B 和 SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

![设置 AP 模式](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-ap-mode.gif)

### <a name="connect-to-devkit-ap"></a>连接到 DevKit AP

现在，使用另一台支持 Wi-Fi 的设备（计算机或手机）连接到 DevKit SSID（在上方图像中突出显示）。 将密码留空。

![网络信息和“连接”按钮](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>为 DevKit 配置 Wi-Fi

在计算机或手机浏览器上打开 DevKit 屏幕上显示的 IP 地址，选择 DevKit 要连接的 Wi-Fi 网络，然后键入密码。 选择“连接”。

![密码框和“连接”按钮](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

连接成功后，DevKit 将在几秒钟内重启。 将在屏幕上看到 Wi-Fi 名称和 IP 地址：

![Wi-Fi 名称和 IP 地址](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE]
> 你将需要 IoT DevKit 使用 2.4 GHz 网络。 将 IoT DevKit 上的 WiFi 模块不兼容使用 5 GHz 网络。 有关详细信息，请查阅 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)。

配置 Wi-Fi 后，即使拔下设备插头，该连接的凭据也会保留在设备上。 例如，如果在家中为 DevKit 配置了 Wi-Fi，之后将 DevKit 带到办公室，则需要重新配置 AP 模式（从“进入 AP 模式”部分中的步骤开始），将 DevKit 连接到办公室 Wi-Fi。

## <a name="start-using-the-devkit"></a>开始使用 DevKit

DevKit 上运行的默认应用会检查固件的最新版本，并显示某些传感器诊断数据。

### <a name="upgrade-to-the-latest-firmware"></a>升级到最新固件

> [!NOTE]
> 从 1.1 版开始，DevKit 在引导加载程序中启用了 ST-SAFE。 如果运行 v1.1 之前的版本，则需要升级固件。

如果需要升级固件，屏幕将显示当前和最新的固件版本。 请按照[升级固件](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/)指南进行升级。

![显示当前和最新的固件版本](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE]
> 这是一次性操作。 开始在 DevKit 上开发并上传应用后，应用将附带最新的固件。

### <a name="test-various-sensors"></a>测试各种传感器

按下按钮 B 测试传感器。 继续按下并松开按钮 B 可逐个测试每个传感器。

![按钮 B 和传感器显示](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>准备开发环境

### <a name="install-azure-iot-tools"></a>安装 Azure IoT Tools

建议安装适用于 Visual Studio Code 的 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 扩展包，以便在 DevKit 上进行开发。 Azure IoT Tools 包含用于在各种 IoT 设备上进行开发和调试的 [Azure IoT Device Workbench](https://aka.ms/iot-workbench)，以及用于管理 Azure IoT 中心并与之交互的 [Azure IoT 中心工具包](https://aka.ms/iot-toolkit)。

可以观看下述 [Channel 9](https://channel9.msdn.com/) 视频，大致了解其功能：
* [Introduction to the new IoT Workbench extension for VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/IoT-Workbench-extension-for-VS-Code)（适用于 VS Code 的全新 IoT Workbench 扩展简介）
* [What's new in the IoT Toolkit extension for VS Code](https://channel9.msdn.com/Shows/Internet-of-Things-Show/Whats-new-in-the-IoT-Toolkit-extension-for-VS-Code)（适用于 VS Code 的 IoT 工具包扩展的新增功能）

遵循以下步骤准备 DevKit 的开发环境：

1. 安装 [Arduino IDE](https://www.arduino.cc/en/Main/Software)。 此 IDE 提供必要的工具链用于编译和上传 Arduino 代码。
    * Windows：使用 Windows Installer 版本。 不要从应用商店安装。
    * **macOS**：将解压缩的 **Arduino.app** 拖放到 `/Applications` 文件夹中。
    * **Ubuntu**：解压缩到某个文件夹中，例如 `$HOME/Downloads/arduino-1.8.8`

2. 安装 [Visual Studio Code](https://code.visualstudio.com/)，这是一个跨平台源代码编辑器，其中包含功能强大的开发人员工具，例如 IntelliSense 代码完成和调试。

3. 启动 VS Code，在扩展市场中找到 **Arduino** 并安装它。 此扩展提供在 Arduino 平台上进行开发的增强体验。
    ![安装 Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 在扩展市场中找到 **Azure IoT Tools** 并安装它。
    ![安装 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

5. 为 VS Code 配置 Arduino 设置。

    在 Visual Studio Code 中，单击“文件”>“首选项”>“设置”。 依次单击 **...** 和“打开 settings.json”。
    ![安装 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/user-settings-arduino.png)
    
    根据你的平台添加以下行来配置 Arduino： 

    * Windows：
      
        ```json
        "arduino.path": "C:\\Program Files (x86)\\Arduino",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **macOS**：

        ```json
        "arduino.path": "/Applications",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

    * **Ubuntu**：
    
        将下面的 **{username}** 占位符替换为你的用户名。

        ```json
        "arduino.path": "/home/{username}/Downloads/arduino-1.8.8",
        "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
        ```

6. 单击 `F1` 以打开命令面板，键入并选择 **Arduino:Board Manager**。 搜索 **AZ3166** 并安装最新版本。
    ![安装 DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>安装 ST-Link 驱动程序

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) 是 IoT DevKit 用来与开发计算机通信的 USB 接口。 需要在其上安装 Windows 以启用 flash 编译的设备代码到 DevKit。 遵循 OS 特定的步骤，使计算机能够访问你的设备。

* Windows：从 [STMicroelectronics 网站](https://www.st.com/en/development-tools/stsw-link009.html)下载并安装 USB 驱动程序。
* **macOS**：macOS 无需驱动程序。
* **Ubuntu**：在终端中运行以下命令，然后注销并登录，使组更改生效：
    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

现已完成开发环境的准备和配置工作。 让我们生成适用于 IoT 的“Hello World”示例：将温度遥测数据发送到 Azure IoT 中心。

## <a name="build-your-first-project"></a>生成第一个项目

1. 确保 IoT DevKit **未连接**到计算机。 先启动 VS Code，然后将 DevKit 连接到计算机。


1. 单击 `F1` 以打开命令面板，键入并选择“Azure IoT Device Workbench:**打开示例...”** 。然后选择“IoT DevKit”作为开发板。

1. 在 IoT Workbench 的“示例”页中，找到“入门”并单击“打开示例”。 然后选择用于下载示例代码的默认路径。
    ![打开示例](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

1. 在打开的新项目窗口中，单击 `F1` 以打开命令面板，键入并选择“Azure IoT Device Workbench:预配 Azure 服务...”。遵照分步指南完成预配 Azure IoT 中心和创建 IoT 中心设备的操作。
    ![云预配](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/cloud-provision.png)

1. 在右下方的状态栏中，检查选定的开发板是否为 **MXCHIP AZ3166**，以及是否使用了带有 **STMicroelectronics** 的串行端口。
    ![选择开发板和 COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. 单击 `F1` 以打开命令面板，键入并选择“Azure IoT Device Workbench:配置设备设置...”，然后选择“配置设备连接字符串”>“选择 IoT 中心设备连接字符串”。

1. 在 DevKit 上，按住**按钮 A** 不放，按下再松开**重置**按钮，然后松开**按钮 A**。DevKit 将进入配置模式并保存连接字符串。
    ![连接字符串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. 再次单击 `F1`，键入并选择“Azure IoT Device Workbench:上传设备代码”。 它启动编译，并将代码上传到 DevKit。
    ![Arduino 上传](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit 将重新启动并开始运行代码。

> [!NOTE]
> 如果出现了任何错误或中断，始终可以再次运行该命令予以恢复。

## <a name="test-the-project"></a>测试项目

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>查看发送到 Azure IoT 中心的遥测数据

单击状态栏上的电源插头图标，打开串行监视器：![串行监视器](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

当看到以下结果时，示例应用程序已成功运行：

* 串行监视器显示发送到 IoT 中心的消息。
* MXChip IoT DevKit 上的 LED 灯闪烁。

![串行监视器输出](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/result-serial-output.png)

### <a name="view-the-telemetry-received-by-azure-iot-hub"></a>查看 Azure IoT 中心收到的遥测数据

可以使用 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)监视 IoT 中心内的设备到云 (D2C) 消息。

1. 登录 [Azure 门户](https://portal.azure.com/)，并找到创建的 IoT 中心。
    ![Azure 门户](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-iot-hub-portal.png)

1. 在“共享访问策略”窗格中单击“iothubowner 策略”，并记下 IoT 中心的连接字符串。
    ![Azure IoT 中心连接字符串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/azure-portal-conn-string.png)

1. 在 VS Code 中，单击 `F1`，键入并选择“Azure IoT 中心:设置 IoT 中心连接字符串”。 将连接字符串复制到其中。
    ![设置 Azure IoT 中心连接字符串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 展开右侧的“Azure IoT 中心设备”窗格，右键单击创建的设备名称，然后选择“开始监视 D2C 消息”。
    ![监视 D2C 消息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. 在“输出”窗格中，可以看到传入 IoT 中心的 D2C 消息。
    ![D2C 消息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，可在 [IoT DevKit 常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)中查看解决方法，或者通过 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) 联系我们。 还可在此页中留言，向我们提供反馈。

## <a name="next-steps"></a>后续步骤

现已成功将 MXChip IoT DevKit 连接到 IoT 中心，并将捕获的传感器数据发送到了 IoT 中心。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
