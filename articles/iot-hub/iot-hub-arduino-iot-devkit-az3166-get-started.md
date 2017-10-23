---
title: "IoT DevKit 到云：将 IoT DevKit AZ3166 连接到 Azure IoT 中心 | Microsoft Docs"
description: "在本教程中了解如何设置 IoT DevKit AZ3166 并将其连接到 Azure IoT 中心，使其能够将数据发送到 Azure 云平台。"
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: 
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2017
ms.author: xshi
ms.openlocfilehash: e8abae4d523ad537563f2c2964a3585b68fda7c1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>将 IoT DevKit AZ3166 连接到云中的 Azure IoT 中心

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 可用于开发物联网 (IoT) 解决方案和构建其原型，以利用 Microsoft Azure 服务。 它包括一个具有大量外围设备和传感器的 Arduino 兼容的板、一个开源板包和一份日益增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-do"></a>准备工作
将 [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 连接到创建的 Azure IoT 中心，从传感器收集温度和湿度数据，并将数据发送到 IoT 中心。

还没有 DevKit？ [获取一个](https://aka.ms/iot-devkit-purchase)。

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

将硬件挂接到计算机。

所需硬件包括：

* DevKit 板
* Micro-USB 电缆

![所需硬件](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

将 DevKit 连接到计算机：

1. 将 USB 端连接到计算机。
2. 将 Micro-USB 端连接到 DevKit。
3. 电源的绿色 LED 灯确认连接。

![硬件连接](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wi-fi"></a>配置 Wi-Fi

IoT 项目依赖 Internet 连接。 使用以下说明配置 DevKit 连接到 Wi-Fi。

### <a name="enter-ap-mode"></a>进入 AP 模式

长按按钮 B，然后按下重置按钮并松开，再松开按钮 B。DevKit 将进入 AP 模式以配置 Wi-Fi。 屏幕将显示 DevKit 的服务集标识符 (SSID) 和配置门户 IP 地址。

![重置按钮、按钮 B 和 SSID](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>连接到 DevKit AP

现在，使用另一台支持 Wi-Fi 的设备（计算机或手机）连接到 DevKit SSID（在上方图像中突出显示）。 将密码留空。

![网络信息和“连接”按钮](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wi-fi-for-the-devkit"></a>为 DevKit 配置 Wi-Fi

在计算机或手机浏览器上打开 DevKit 屏幕上显示的 IP 地址，选择 DevKit 要连接的 Wi-Fi 网络，然后键入密码。 选择“连接”。

![密码框和“连接”按钮](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

连接成功后，DevKit 将在几秒钟内重启。 将在屏幕上看到 Wi-Fi 名称和 IP 地址：

![Wi-Fi 名称和 IP 地址](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
> 照片中显示的 IP 地址可能与 DevKit 屏幕上分配和显示的实际 IP 地址不匹配。 这是正常的，因为 Wi-Fi 使用 DHCP 动态分配 IP。

配置 Wi-Fi 后，即使拔下设备插头，该连接的凭据也会保留在设备上。 例如，如果在家中为 DevKit 配置了 Wi-Fi，之后将 DevKit 带到办公室，则需要重新配置 AP 模式（从“进入 AP 模式”部分中的步骤开始），将 DevKit 连接到办公室 Wi-Fi。 

## <a name="start-using-the-devkit"></a>开始使用 DevKit

DevKit 上运行的默认应用会检查固件的最新版本，并显示某些传感器诊断数据。

### <a name="upgrade-to-the-latest-firmware"></a>升级到最新固件

> [!NOTE] 
> 从 1.1 版开始，DevKit 在引导加载程序中启用了 ST-SAFE。 如果运行的是低于 1.1 版的固件，则需要升级固件才能使其正确工作。

如果需要升级固件，屏幕将显示当前和最新的固件版本。 请按照[升级固件](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/)指南进行升级。

![显示当前和最新的固件版本](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
> 这是一次性操作。 开始在 DevKit 上开发并上传应用后，应用将附带最新的固件。

### <a name="test-various-sensors"></a>测试各种传感器

按下按钮 B 测试传感器。 继续按下并松开按钮 B 可逐个测试每个传感器。

![按钮 B 和传感器显示](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-the-development-environment"></a>准备开发环境

现在该设置开发环境了，开发环境是用于生成令人惊叹的 IoT 应用程序的工具和包。 可根据你的操作系统选择 Windows 或 macOS 版本。

### <a name="windows"></a>Windows

我们鼓励使用安装包来准备开发环境。 如果遇到任何问题，可按照[手动步骤](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)进行操作。

#### <a name="download-the-latest-package"></a>下载最新包

下载的 .zip 文件包含 DevKit 开发所需的所有必需工具和包。

> [!div class="button"]
[下载](https://aka.ms/devkit/prod/installpackage/latest)

.zip 文件包含以下工具和包。 如果已经安装了某些组件，脚本会检测并跳过它们。

* Node.js 和 Yarn：用于安装脚本和自动化任务的运行时。
* [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows)：用于管理 Azure 资源的跨平台命令行体验。 MSI 包含依赖的 Python 和 pip。
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code)：用于 DevKit 开发的轻量级代码编辑器。
* [适用于 Arduino 的 Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino)：在 Visual Studio Code 中启用 Arduino 开发的扩展。
* [Arduino IDE](https://www.arduino.cc/en/Main/Software)：Arduino 扩展所依赖的工具。
* DevKit 板包：DevKit 的工具链、库和项目。
* ST-Link 实用程序：基本工具和驱动程序。

#### <a name="run-the-installation-script"></a>运行安装脚本

在 Windows 文件资源管理器中，找到并解压缩 zip 文件。 找到并右键单击 `install.cmd`，然后选择“以管理员身份运行”。

![文件资源管理器](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

安装过程中，将看到每个工具或包的进度。

![安装进度](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="install-drivers"></a>安装驱动程序

适用于 Arduino 扩展的 VS Code 依赖 Arduino IDE。 如果是第一次安装 Arduino IDE，系统会提示安装相关驱动程序：

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

完成安装需要大约 10 分钟，具体取决于 Internet 速度。 安装完成后，桌面上应该会显示 Visual Studio Code 和 Arduino IDE 的快捷方式。

> [!NOTE] 
> 当启动 VS Code 时，有时系统会提示出现错误：找不到 Arduino IDE 或相关板包。 若要解决此错误，请关闭 VS Code 并重启 Arduino IDE。 然后，VS Code 应正确找到 Arduino IDE 的路径。

### <a name="macos-preview"></a>macOS（预览）

按照以下步骤在 macOS 上准备开发环境。

#### <a name="install-azure-cli-20"></a>安装 Azure CLI 2.0

1. 使用一个 `curl` 命令安装 Azure CLI 2.0：

   ```bash
   curl -L https://aka.ms/InstallAzureCli | bash
   ```

2. 然后重启命令外壳，使更改生效：

   ```bash
   exec -l $SHELL
   ```

有关安装 Azure CLI 2.0 的详细信息，请参阅[官方指南](https://docs.microsoft.com//cli/azure/install-azure-cli)。

#### <a name="install-the-arduino-ide"></a>安装 Arduino IDE

Visual Studio Code Arduino 扩展依赖 Arduino IDE。 下载并安装 [Arduino IDE for macOS](https://www.arduino.cc/en/Main/Software)。

#### <a name="install-visual-studio-code"></a>安装 Visual Studio Code

下载并安装 [Visual Studio Code for macOS](https://code.visualstudio.com/)。 这是用于生成 DevKit IoT 应用程序的主要开发工具。

####  <a name="download-the-latest-package"></a>下载最新包

1. 安装 Node.js。 可以使用常用的 macOS 包管理器 [Homebrew](https://brew.sh/) 或[预建安装程序](https://nodejs.org/en/download/)进行安装。

2. 下载包含在 VS Code 中开发 DevKit 所需任务脚本的 .zip 文件。

   > [!div class="button"]
   [下载](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

3. 找到 zip 文件并解压缩。 然后启动终端应用，并运行以下命令：

   a. 将解压文件夹移动到你的 macOS 用户文件夹：
      ```bash
      mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
      ```
  
   b. 安装 npm 包：
      ```
      npm install
      ```

#### <a name="install-the-vs-code-extension-for-arduino"></a>安装适用于 Arduino 的 VS Code 扩展

可以直接在 Visual Studio Code 中安装 Azure Marketplace 扩展。 在左窗格中选择扩展图标，搜索“Arduino”，然后选择“安装”：

![查找 Arduino 扩展](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-the-devkit-board-package"></a>安装 DevKit 板包

使用 Visual Studio Code 中的板管理器添加 DevKit 板。

1. 使用 Cmd+Shift+P 打开命令面板并键入“Arduino”，然后找到并选择“Arduino: 板管理器”。

2. 在右下角选择“其他 URL”。
   ![其他 URL 链接](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. 在 settings.json 文件中，在“用户设置”窗格底部添加行并保存。
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![添加到“用户设置”窗格的 Code](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. 在板管理器中搜索“az3166”并安装最新版本。
   ![安装 az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

现在已为 macOS 安装了所有必需的工具和包。


## <a name="open-the-project-folder"></a>打开项目文件夹

### <a name="start-vs-code"></a>启动 VS Code

确保 DevKit 未连接。 首先启动 VS Code，并将 DevKit 连接到计算机。 VS Code 将自动查找 DevKit，并打开简介页：

![“简介”页](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
> 当启动 VS Code 时，有时系统会提示出现错误：找不到 Arduino IDE 或相关板包。 请关闭 VS Code，再次启动 Arduino IDE，VS Code 应能正确找到 Arduino IDE 路径。


### <a name="open-the-arduino-examples-folder"></a>打开 Arduino 示例文件夹

展开左侧的“ARDUINO 示例”部分，浏览到“MXCHIP AZ3166 的示例 > AzureIoT”，然后选择“入门”。 这将打开一个新的 VS Code 窗口，其中包含项目文件夹。

![“Arduino 示例”选项卡](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

如果无意中关闭了窗格，可以重新打开它。 使用 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 打开命令面板，键入“Arduino”，然后找到并选择“Arduino: Examples”。

## <a name="provision-azure-services"></a>预配 Azure 服务

在解决方案窗口中，通过 `Ctrl+P` (macOS: `Cmd+P`) 并通过输入 `task cloud-provision` 来运行任务：

在 VS Code 终端中，交互式命令行指导你预配所需的 Azure 服务：

![交互式命令行](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-the-arduino-sketch"></a>生成并上传 Arduino 草图

### <a name="install-the-required-library"></a>安装所需的库

1. 按 `F1` 或 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 以打开命令面板，键入“Arduino”，然后找到并选择“Arduino: Library Manager”。

2. 搜索“ArduinoJson”库并选择“Install: Install Arduino library”![](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/arduino-json.png)

### <a name="build-and-upload-the-device-code-windows"></a>生成并上传设备代码 (Windows)
1. 使用 `Ctrl+P` 运行 `task device-upload`。
2. 终端会提示进入配置模式。 为此，请长按按钮 A，然后按下重置按钮并松开。 屏幕将显示 DevKit id 和“配置”。

这是为了设置在 `task cloud-provision` 步骤中进行检索的连接字符串。

然后终端开始验证并上传 Arduino 草图：

![验证并上传 Arduino 草图](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit 将重新启动并开始运行代码。

### <a name="build-and-upload-the-device-code-mac"></a>生成并上传设备代码 (Mac)

1. 将 DevKit 置于配置模式：按下按钮 A，然后按下重置按钮并松开。 屏幕将显示“配置”。
2. 使用 `Cmd+P` 运行 `task device-upload`。

这是为了设置在 `task cloud-provision` 步骤中进行检索的连接字符串。

然后，VS Code 将开始验证并上传 Arduino 草图：

![设备上传](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit 将重新启动并开始运行代码。

## <a name="test-the-project"></a>测试项目

在 VS Code 中，按照以下步骤打开并设置串行监视器：

1. 单击状态栏上的 `COM[X]` 字样以使用 `STMicroelectronics`: ![com-port](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/com-port.png) 设置正确的 COM 端口

2. 单击状态栏上的电源插头图标，打开串行监视器：![serial-monitor](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/serial-monitor.png)

3. 在状态栏上，单击表示波特率的数字并设置为 `115200`：![baud-rate](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/baud-rate.png)

当看到以下结果时，示例应用程序已成功运行：

* 串行监视器显示与下面截图中内容相同的信息。
* MXChip IoT DevKit 上的 LED 灯闪烁。

![VS Code 中的最终输出](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，可以参阅[常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)。 还可在此页中留言，向我们提供反馈。

## <a name="next-steps"></a>后续步骤

现已成功将 MXChip IoT DevKit 连接到 IoT 中心，并将捕获的传感器数据发送到了 IoT 中心。

若要继续了解 Azure IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

- [使用 iothub-explorer 管理云设备消息传送](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [将 IoT 中心消息保存到 Azure 数据存储](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [使用 Power BI 可视化 Azure IoT 中心的实时传感器数据](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [使用 Web 应用可视化 Azure IoT 中心的实时传感器数据](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [在 Azure 机器学习中使用 IoT 中心的传感器数据进行天气预报](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [使用 iothub-explorer 进行设备管理](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [使用逻辑应用执行远程监视和发送通知](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
