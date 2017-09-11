---
title: "IoT DevKit 到云 - 将 IoT DevKit AZ3166 连接到 Azure IoT 中心 | Microsoft Docs"
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
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 122fac584ac5b954ef7b33a3121bee2c502ebc63
ms.contentlocale: zh-cn
ms.lasthandoff: 08/16/2017

---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub-in-the-cloud"></a>将 IoT DevKit AZ3166 连接到云中的 Azure IoT 中心

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 可用于开发和原型物联网 (IoT) 解决方案，以利用 Microsoft Azure 服务。 它包括一个具有大量外围设备和传感器的 Arduino 兼容板、一个开源板包和一份日益增长的[项目目录](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-do"></a>准备工作
将 [DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 连接到创建的 Azure IoT 中心，从传感器收集温度和湿度数据，并将数据发送到 IoT 中心。

还没有 DevKit？ 从[此处](https://aka.ms/iot-devkit-purchase)获取新设备。

## <a name="what-you-learn"></a>学习内容

* 如何将 IoT DevKit 连接到无线接入点并准备开发环境。
* 如何创建 IoT 中心以及注册 MXChip IoT DevKit 的设备。
* 如何通过在 MXChip IoT DevKit 上运行示例应用程序来收集传感器数据。
* 如何将传感器数据发送到 IoT 中心。

## <a name="what-you-need"></a>所需条件

* 采用 micro USB 电缆的 MXChip IoT DevKit 板。 [立即获取](https://aka.ms/iot-devkit-purchase)
* 运行 Windows 10 或 macOS 10.10 以上版本的计算机
* 一个有效的 Azure 订阅
  * 激活 [30 天免费试用版 Microsoft Azure 帐户](https://azureinfo.microsoft.com/us-freetrial.html)

## <a name="prepare-your-hardware"></a>准备硬件

将硬件挂接到计算机。

### <a name="hardware-you-need"></a>所需硬件

* DevKit 板
* Micro USB 电缆

![getting-started-hardware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/hardware.jpg)

### <a name="connect-devkit-to-your-computer"></a>将 DevKit 连接到计算机

1. 将 USB 端连接到电脑
2. 将 Micro USB 端连接到 DevKit
3. 电源旁边的绿色 LED 灯确认连接

![getting-started-connect](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect.jpg)

## <a name="configure-wifi"></a>配置 WiFi

IoT 项目依赖 Internet 连接。 使用以下说明配置 DevKit 连接到 WiFi。

### <a name="enter-ap-mode"></a>进入 AP 模式

长按按钮 B，然后按下重置按钮并松开，然后松开按钮 B。DevKit 将进入 AP 模式以配置 WiFi。 屏幕将显示 DevKit 的服务集标识符 (SSID) 和配置门户 IP 地址：

![getting-started-wifi-ap](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ap.jpg)

### <a name="connect-to-devkit-ap"></a>连接到 DevKit AP

现在，使用另一台支持 WiFi 的设备（电脑或手机）连接到 DevKit SSID（在上方截图中突出显示），将密码留空。

![getting-started-ssid](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connect-ssid.png)

### <a name="configure-wifi-for-devkit"></a>为 DevKit 配置 WiFi

在电脑或手机浏览器上打开 DevKit 屏幕上显示的 IP 地址，选择 DevKit 要连接的 WiFi 网络，然后键入密码。 单击“连接”完成操作：

![getting-started-wifi-portal](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-portal.png)

连接成功后，DevKit 将在几秒钟内重启。 如果成功，将会在屏幕上看到 WiFi 名称和 IP 地址：

![getting-started-wifi-ip](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/wifi-ip.jpg)

> [!NOTE] 
照片中显示的 IP 地址可能与 DevKit 屏幕上分配和显示的实际 IP 不匹配。 这是正常的，因为 WiFi 使用 DHCP 动态分配 IP。

配置 WiFi 后，即使拔下电缆，该连接的凭据也会保留在设备上。 例如，如果在家中为 DevKit 配置了 WiFi，之后将 DevKit 带到办公室，则需要重新配置 AP 模式（从步骤“进入 AP 模式”开始），将 DevKit 连接到办公室 WiFi。 

## <a name="start-using-devkit"></a>开始使用 DevKit

在 DevKit 上运行的默认应用会检查最新版本的固件，并显示某些传感器诊断数据。

### <a name="upgrade-to-the-latest-firmware"></a>升级到最新固件

如果需要升级，屏幕上会提示固件的当前版本和最新版本。 请按照[升级固件](https://microsoft.github.io/azure-iot-developer-kit/docs/upgrading/)指南进行升级。

![getting-started-firmware](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/firmware.jpg)

> [!NOTE] 
该操作只需进行一次，开始在 DevKit 上开发应用并上传后，这些应用将附带最新的固件。

### <a name="test-various-sensors"></a>测试各种传感器

按下按钮 B 测试传感器，继续按下 B 按钮并松开可逐个测试每个传感器。

![getting-started-sensors](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/sensors.jpg)

## <a name="prepare-development-environment"></a>准备开发环境

现在该设置开发环境了，开发环境是用于生成令人惊叹的 IoT 应用程序的工具和包。 可根据你的操作系统选择 Windows 或 macOS 版本。


### <a name="windows"></a>Windows

我们鼓励使用安装包来准备开发环境。 如果遇到任何问题，可按照[手动步骤](https://microsoft.github.io/azure-iot-developer-kit/docs/installation/)进行操作。

#### <a name="download-latest-package"></a>下载最新的包

下载的 `.zip` 文件包含 DevKit 开发所需的所有必需工具和包。

> [!div class="button"]
[下载](https://azureboard.azureedge.net/prod/installpackage/devkit_install_1.0.2.zip)


> [!NOTE] 
> `.zip` 文件包含以下工具和包。 如果已经安装了某些组件，脚本会检测并跳过它们。
> * Node.js 和 Yarn：用于安装脚本和自动化任务的运行时
> * [Azure CLI 2.0 MSI](https://docs.microsoft.com//cli/azure/install-azure-cli#windows) - 用于管理 Azure 资源的跨平台命令行体验，MSI 包含依赖型 Python 和 pip。
> * [Visual Studio Code](https://code.visualstudio.com/)：用于 DevKit 开发的轻量级代码编辑器
> * [适用于 Arduino 的 Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-arduino)：在 VS Code 中启用 Arduino 开发
> * [Arduino IDE](https://www.arduino.cc/en/Main/Software)：Arduino 扩展依赖此工具
> * DevKit 板包：DevKit 的工具链、库和项目
> * ST-Link 实用程序：基本实用程序和驱动程序

#### <a name="run-installation-script"></a>运行安装脚本

在 Windows 文件资源管理器中，找到 `.zip` 并解压，找到 `install.cmd`，右键单击并选择“以管理员身份运行”开始。

![getting-started-run-admin](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/run-admin.png)

在安装过程中，将会看到每个工具或包的进度。

![getting-started-install](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install.png)

#### <a name="confirm-to-install-drivers"></a>确认安装驱动程序

适用于 Arduino 扩展的 VS Code 依赖 Arduino IDE。 如果是第一次安装 Arduino IDE，系统会提示安装相关驱动程序：

![getting-started-driver](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/driver.png)

根据你的 Internet 速度，完成安装需要大约 10 分钟。 安装完成后，桌面上应该会显示 Visual Studio Code 和 Arduino IDE 的快捷方式。

> [!NOTE] 
当启动 VS Code 时，有时系统会提示出现错误：找不到 Arduino IDE 或相关板包。 若要解决此问题，请关闭 VS Code，再次启动 Arduino IDE，VS Code 应正确找到 Arduino IDE 路径。


### <a name="macos-preview"></a>macOS（预览版）

按照以下步骤在 macOS 上准备开发环境。

#### <a name="install-azure-cli-20"></a>安装 Azure CLI 2.0

按照[官方指南](https://docs.microsoft.com//cli/azure/install-azure-cli)安装 Azure CLI 2.0：

使用一个 `curl` 命令安装 Azure CLI 2.0：

```bash
curl -L https://aka.ms/InstallAzureCli | bash
```

然后重启命令外壳，使更改生效：

```bash
exec -l $SHELL
```

#### <a name="install-arduino-ide"></a>安装 Arduino IDE

Visual Studio Code Arduino 扩展依赖 Arduino IDE。 下载并安装 [Arduino IDE for macOS](https://www.arduino.cc/en/Main/Software)。

#### <a name="install-visual-studio-code"></a>安装 Visual Studio Code

下载并安装 [Visual Studio Code for macOS](https://code.visualstudio.com/)。 这是用于生成 DevKit IoT 应用程序的主要开发工具。

####  <a name="download-latest-package"></a>下载最新的包

1. 安装 Node.js。 可以使用常用的 macOS 包管理器 [Homebrew](https://brew.sh/) 或[预建安装程序](https://nodejs.org/en/download/)进行安装。

2. 下载包含在 VS Code 中开发 DevKit 所需任务脚本的 `.zip` 文件。

   > [!div class="button"]
   [下载](https://azureboard.azureedge.net/installpackage/devkit_tasks_1.0.2.zip)

   找到 `.zip` 并解压缩。 然后启动终端应用，并运行以下命令进行配置：

   将解压文件夹移动到你的 macOS 用户文件夹：
   ```bash
   mv [.zip extracted folder]/azure-board-cli ~/. ; cd ~/azure-board-cli
   ```
  
   安装 `npm` 包：
   ```
   npm install
   ```

#### <a name="install-vs-code-extension-for-arduino"></a>安装适用于 Arduino 的 VS Code 扩展

Visual Studio Code 允许直接在工具中安装 Marketplace 扩展，只需单击左侧菜单窗格中的扩展图标，然后搜索 `Arduino` 即可安装：

![installation-extensions](media/iot-hub-arduino-devkit-az3166-get-started/installation-extensions-mac.png)

#### <a name="install-devkit-board-package"></a>安装 DevKit 板包

需要使用 Visual Studio Code 中的板管理器添加 DevKit 板。

1. 使用 `Cmd+Shift+P` 调用命令面板并键入“Arduino”，然后找到并选择“Arduino: 板管理器”。

2. 点击右下角的“其他 URL”。
   ![installation-additional-urls](media/iot-hub-arduino-devkit-az3166-get-started/installation-additional-urls-mac.png)

3. 在 `settings.json` 文件中，在 `USER SETTINGS` 窗格底部添加一行并保存。
   ```json
   "arduino.additionalUrls": "https://raw.githubusercontent.com/VSChina/azureiotdevkit_tools/master/package_azureboard_index.json"
   ```
   ![installation-settings-json](media/iot-hub-arduino-devkit-az3166-get-started/installation-settings-json-mac.png)

4. 现在在板管理器中搜索“az3166”并安装最新版本。
   ![installation-az3166](media/iot-hub-arduino-devkit-az3166-get-started/installation-az3166-mac.png)

现在已为 macOS 安装了所有必需的工具和包。


## <a name="open-project-folder"></a>打开项目文件夹

### <a name="launch-vs-code"></a>启动 VS Code

确保 DevKit 未连接。 首先启动 VS Code，并将 DevKit 连接到计算机。 VS Code 会自动找到它并弹出一个介绍页面：

![mini-solution-vscode](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-vscode.png)

> [!NOTE] 
当启动 VS Code 时，有时系统会提示出现错误：找不到 Arduino IDE 或相关板包。 若要解决此问题，请关闭 VS Code，再次启动 Arduino IDE，VS Code 应正确找到 Arduino IDE 路径。

### <a name="open-arduino-examples-folder"></a>打开 Arduino 示例文件夹

切换到“Arduino 示例”选项卡，导航到 `Examples for MXCHIP AZ3166 > AzureIoT` 并单击 `GetStarted`。

![mini-solution-examples](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution-examples.png)

如果不小心关闭了窗格，若要重新加载，请使用 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 调用命令面板，并键入“Arduino”，查找并选择“Arduino: 示例”。

## <a name="provision-azure-services"></a>预配 Azure 服务

在解决方案窗口中，键入“task cloud-provision”，按 `Ctrl+P` (macOS: `Cmd+P`) 运行任务：

在 VS Code 终端中，交互式命令行将指导你预配所需的 Azure 服务：

![mini-solution-cloud-provision](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/cloud-provision.png)

## <a name="build-and-upload-arduino-sketch"></a>生成并上传 Arduino 草图

### <a name="install-required-library"></a>安装所需的库

1. 按 `F1` 或 `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) 调用命令面板并键入“Arduino”，然后找到并选择“Arduino: 库管理器”。

2. 搜索 `ArduinoJson` 库，然后单击“安装”

### <a name="build-and-upload-the-device-code"></a>生成并上传设备代码

使用 `Ctrl+P` (macOS: `Cmd+P`) 运行“task device-upload”。 终端会提示进入配置模式。 为此，请长按按钮 A，然后按下重置按钮并松开。 屏幕将显示“配置”。 这是为了设置从“task cloud-provision”步骤检索的连接字符串。

然后它将开始验证并上传 Arduino 草图：

![mini-solution-device-upload](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/device-upload.png)

DevKit 将重启并开始运行代码。

## <a name="test-the-project"></a>测试项目

在 VS Code 中，单击状态栏上的电源插头图标，打开串行监视器。

当看到以下结果时，示例应用程序已成功运行：

* 串行监视器显示与下面截图中内容相同的信息。
* MXChip IoT DevKit 上的 LED 灯闪烁。

![VS Code 中的最终输出](media/iot-hub-arduino-devkit-az3166-get-started/mini-solution/connect-iothub/result-serial-output.png)

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，可以查看[常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)，或从以下渠道与我们联系。

## <a name="next-steps"></a>后续步骤

现已成功将 MXChip IoT DevKit 连接到 IoT 中心，并将捕获的传感器数据发送到了 IoT 中心。

若要继续了解 IoT 中心入门知识并浏览其他 IoT 方案，请参阅：

- [使用 iothub-explorer 管理云设备消息传送](https://docs.microsoft.com/azure/iot-hub/iot-hub-explorer-cloud-device-messaging)
- [将 IoT 中心消息保存到 Azure 数据存储](https://docs.microsoft.com//azure/iot-hub/iot-hub-store-data-in-azure-table-storage)
- [使用 Power BI 可视化 Azure IoT 中心的实时传感器数据](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-power-bi)
- [使用 Azure Web 应用可视化 Azure IoT 中心的实时传感器数据](https://docs.microsoft.com//azure/iot-hub/iot-hub-live-data-visualization-in-web-apps)
- [在 Azure 机器学习中使用 IoT 中心的传感器数据进行天气预报](https://docs.microsoft.com/azure/iot-hub/iot-hub-weather-forecast-machine-learning)
- [使用 iothub-explorer 进行设备管理](https://docs.microsoft.com/azure/iot-hub/iot-hub-device-management-iothub-explorer)
- [使用逻辑应用执行远程监视和发送通知](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps)
