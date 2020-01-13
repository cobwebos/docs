---
title: 将 IoT DevKit AZ3166 连接到 Azure IoT 中心
description: 在本教程中了解如何设置 IoT DevKit AZ3166 并将其连接到 Azure IoT 中心，使其能够将数据发送到 Azure 云平台。
author: wesmc7777
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 06/25/2019
ms.author: wesmc
ms.openlocfilehash: 139d1a470c67d5dab310c4fa2a9171f433df2061
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2020
ms.locfileid: "75912363"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>将 IoT DevKit AZ3166 连接到 Azure IoT 中心

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 可用于开发物联网 (IoT) 解决方案和构建其原型，以利用 Microsoft Azure 服务。 它包括一个具有丰富外设和传感器的 Arduino 兼容板、一个开源板包和一个丰富的[示例库](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-learn"></a>学习内容

* 如何创建 IoT 中心以及注册 MXChip IoT DevKit 的设备。
* 如何将 IoT DevKit 连接到 Wi-fi 并配置 IoT 中心连接字符串。
* 如何将 DevKit 传感器遥测数据发送到 IoT 中心。
* 如何为 IoT DevKit 准备开发环境并开发应用程序。

还没有 DevKit？ 请尝试 [DevKit 模拟器](https://azure-samples.github.io/iot-devkit-web-simulator/)或[购买 DevKit](https://aka.ms/iot-devkit-purchase)。

可以从[代码示例库](https://docs.microsoft.com/samples/browse/?term=mxchip)中找到所有 DevKit 教程的源代码。

## <a name="what-you-need"></a>所需条件

* 带有微 USB 电缆的 MXChip IoT DevKit 板。 [立即获取](https://aka.ms/iot-devkit-purchase)。
* 运行 Windows 10、macOS 10.10 + 或 Ubuntu 18.04 + 的计算机。
* 一个有效的 Azure 订阅。 [激活 30 天免费试用版 Microsoft Azure 帐户](https://azureinfo.microsoft.com/us-freetrial.html)。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
  
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>快速入门：将遥测从 DevKit 发送到 IoT 中心

此快速入门使用预先编译的 DevKit 固件将遥测发送到 IoT 中心。 在运行之前，请先创建一个 IoT 中心，并向该中心注册设备。

### <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，将使用 Azure Cloud Shell 来注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以创建设备标识。

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyNodeDevice**：要注册的设备的名称。 如下所示请使用 MyNodeDevice 。 如果为设备选择其他名称，则需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```

   > [!NOTE]
   > 如果在运行 `device-identity`时遇到错误，请安装[适用于 Azure CLI 的 AZURE IOT 扩展](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md)，以了解更多详细信息。
  
1. 在 Azure Cloud Shell 中运行以下命令，以获取刚注册设备的_设备连接字符串_：

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

### <a name="send-devkit-telemetry"></a>发送 DevKit 遥测

DevKit 连接到 IoT 中心上的特定于设备的终结点，并发送温度和湿度遥测数据。

1. 为 IoT DevKit 下载最新版本的[GetStarted 固件](https://aka.ms/devkit/prod/getstarted/latest)。

1. 请确保 IoT DevKit 通过 USB 连接到您的计算机。 打开文件资源管理器，有一个名为**AZ3166**的 USB 大容量存储设备。

    ![打开 Windows 资源管理器](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. 将刚刚下载的固件拖放到大容量存储设备中，它将自动闪烁。

    ![复制固件](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. 在 DevKit 上，按下按钮**b**，按下并释放 "**重置**" 按钮，然后松开按钮**B**。DevKit 进入 AP 模式。 若要进行确认，屏幕将显示 DevKit 的服务集标识符（SSID）和配置门户 IP 地址。

    ![重置按钮、按钮 B 和 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![设置 AP 模式](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. 在另一台启用 Wi-fi 的设备（计算机或移动电话）上使用 Web 浏览器连接到在上一步中显示的 IoT DevKit SSID。 如果要求输入密码，则将其留空。

    ![连接 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. 在浏览器中打开 " **192.168.0.1** "。 选择希望 IoT DevKit 连接到的 Wi-fi，键入 Wi-fi 密码，并粘贴之前记下的设备连接字符串。 然后单击“保存”。

    ![配置 UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > IoT DevKit 仅支持 2.4 GHz 网络。 有关详细信息，请查阅 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)。

1. 当你看到 "结果" 页时，WiFi 信息和设备连接字符串将存储到 IoT DevKit 中。

    ![配置结果](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > 配置 Wi-Fi 后，即使拔下设备插头，该连接的凭据也会保留在设备上。

1. IoT DevKit 将在几秒钟内重新启动。 在 "DevKit" 屏幕上，会看到 DevKit 的 IP 地址，该地址按遥测数据，其中包括 "发送到 Azure IoT 中心的消息计数" 和 "湿度" 值。

    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![发送数据](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

1. 若要验证发送到 Azure 的遥测数据，请在 Azure Cloud Shell 中运行以下命令：

    ```bash
    az iot hub monitor-events --hub-name YourIoTHubName --output table
    ```

## <a name="prepare-the-development-environment"></a>准备开发环境

按照以下步骤为 DevKit 准备开发环境：

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>随 Azure IoT 工具扩展包一起安装 Visual Studio Code

1. 安装 [Arduino IDE](https://www.arduino.cc/en/Main/Software)。 此 IDE 提供必要的工具链用于编译和上传 Arduino 代码。
    * **Windows**：使用 Windows Installer 版本。 不要从应用商店安装。
    * **macOS**：将解压缩的 **Arduino.app** 拖放到 `/Applications` 文件夹中。
    * **Ubuntu**：解压缩到某个文件夹中，例如 `$HOME/Downloads/arduino-1.8.8`

2. 安装[Visual Studio Code](https://code.visualstudio.com/)是一个跨平台源代码编辑器，其中包含功能强大的 intellisense、代码完成和调试支持以及可从 marketplace 安装丰富的扩展。

3. 启动 VS Code，在扩展市场中找到 **Arduino** 并安装它。 此扩展提供在 Arduino 平台上进行开发的增强体验。

    ![安装 Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 在扩展市场中找到 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 并安装它。

    ![安装 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    或使用此直接链接：
    > [!div class="nextstepaction"]
    > [安装 Azure IoT 工具扩展包](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Azure IoT 工具扩展包包含用于在各种 IoT devkit 设备上进行开发和调试的[Azure Iot 设备工作台](https://aka.ms/iot-workbench)。 Azure iot[中心扩展](https://aka.ms/iot-toolkit)（随附于 Azure iot 工具扩展包）用于管理 Azure iot 中心并与之进行交互。

5. 为 VS Code 配置 Arduino 设置。

    在 Visual Studio Code 中，单击 "**文件" > 首选项 > 设置**（在 macOS 上，**代码 > 首选项 > 设置**）。 然后单击 "*设置*" 页右上角的 "**打开设置（JSON）** " 图标。

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

6. 单击 `F1` 打开命令面板，键入并选择 **Arduino: Board Manager**。 搜索 **AZ3166** 并安装最新版本。

    ![安装 DevKit SDK](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-az3166-sdk.png)

### <a name="install-st-link-drivers"></a>安装 ST-Link 驱动程序

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) 是 IoT DevKit 用来与开发计算机通信的 USB 接口。 需要将其安装在 Windows 上，以将编译的设备代码刷新到 DevKit。 遵循 OS 特定的步骤，使计算机能够访问你的设备。

* **Windows**：从 [STMicroelectronics 网站](https://www.st.com/en/development-tools/stsw-link009.html)下载并安装 USB 驱动程序。
* **macOS**：macOS 无需驱动程序。
* **Ubuntu**：运行终端中的命令并注销，并登录以使组更改生效：

    ```bash
    # Copy the default rules. This grants permission to the group 'plugdev'
    sudo cp ~/.arduino15/packages/AZ3166/tools/openocd/0.10.0/linux/contrib/60-openocd.rules /etc/udev/rules.d/
    sudo udevadm control --reload-rules

    # Add yourself to the group 'plugdev'
    # Logout and log back in for the group to take effect
    sudo usermod -a -G plugdev $(whoami)
    ```

现已完成开发环境的准备和配置工作。 让我们生成刚运行的 GetStarted 示例。

## <a name="build-your-first-project"></a>生成第一个项目

### <a name="open-sample-code-from-sample-gallery"></a>从示例库中打开示例代码

IoT DevKit 包含丰富的示例库，可用于了解如何将 DevKit 连接到各种 Azure 服务。

1. 确保 IoT DevKit **未连接**到计算机。 先启动 VS Code，然后将 DevKit 连接到计算机。

1. 单击 "`F1`" 打开命令面板，键入并选择 " **Azure IoT 设备工作台：打开示例 ...** "。然后选择 " **IoT DevKit** as 板"。

1. 在 IoT Workbench 的“示例”页中，找到“入门”并单击“打开示例”。 然后选择用于下载示例代码的默认路径。

    ![打开示例](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>预配 Azure IoT 中心和设备

无需离开开发环境即可在 VS Code 中设置 Azure IoT 中心和设备，而无需离开 Azure 门户。

1. 在新的 "打开的项目" 窗口中，单击 "`F1`" 打开命令面板，键入并选择 " **Azure IoT 设备工作台：预配 Azure 服务 ...** "。按照分步指南完成 Azure IoT 中心的预配和创建 IoT 中心设备的步骤。

    ![设置命令](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > 如果尚未登录 Azure，则为。 按照弹出通知进行登录。

1. 选择要使用的订阅。

    ![选择子](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. 然后选择或创建新的[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)。

    ![选择资源组](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. 在指定的资源组中，按照指南选择或创建新的 Azure IoT 中心。

    ![选择 IoT 中心步骤](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![选择 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![所选 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. 在 "输出" 窗口中，你会看到预配的 Azure IoT 中心。

    ![已预配 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. 选择或在预配的 Azure IoT 中心内创建新设备。

    ![选择 IoT 设备步骤](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![选择已预配的 IoT 设备](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. 现在，已预配 Azure IoT 中心，并在其中创建了设备。 设备连接字符串还会保存在 VS Code 中，以便稍后配置 IoT DevKit。

    ![预配完成](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>配置和编译设备代码

1. 在右下方的状态栏中，检查选定的开发板是否为 **MXCHIP AZ3166**，以及是否使用了带有 **STMicroelectronics** 的串行端口。

    ![选择板和 COM](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-com.png)

1. 单击 "`F1`" 打开命令面板，键入并选择 " **Azure IoT 设备工作台：配置设备设置 ...** "，然后选择 " **Config 设备连接字符串" > 选择 "IoT 中心设备连接字符串**"。

1. 在 DevKit 上，按下**按钮 a**，按下 "**重置**" 按钮，然后松开**按钮 A**。DevKit 进入配置模式并保存连接字符串。

    ![连接字符串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/connection-string.png)

1. 再次单击 "`F1`"，键入并选择 " **Azure IoT 设备工作台：上传设备代码**"。 它启动编译，并将代码上传到 DevKit。

    ![Arduino 上传](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/arduino-upload.png)

DevKit 将重新启动并开始运行代码。

> [!NOTE]
> 如果出现了任何错误或中断，始终可以再次运行该命令予以恢复。

## <a name="test-the-project"></a>测试项目

### <a name="view-the-telemetry-sent-to-azure-iot-hub"></a>查看发送到 Azure IoT 中心的遥测数据

单击状态栏上的电源插头图标，打开串行监视器：

![串行监视器](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/serial-monitor.png)

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

1. 在 VS Code 中，单击 "`F1`，键入并选择" **Azure Iot 中心：设置 IoT 中心连接字符串**"。 将连接字符串复制到其中。

    ![设置 Azure IoT 中心连接字符串](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/set-iothub-connection-string.png)

1. 展开右侧的 " **AZURE IOT 中心设备**" 窗格，右键单击创建的设备名称，然后选择 "**开始监视内置事件终结点**"。

    ![监视 D2C 消息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/monitor-d2c.png)

1. 在“输出”窗格中，可以看到传入 IoT 中心的 D2C 消息。

    ![D2C 消息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/d2c-output.png)

## <a name="review-the-code"></a>查看代码

`GetStarted.ino` 是主 Arduino 草图文件。

![D2C 消息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

若要查看如何将设备遥测发送到 Azure IoT 中心，请打开同一文件夹中的 `utility.cpp` 文件。 查看[API 参考](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/)，了解如何使用 IoT DevKit 上的传感器和外围设备。

使用的 `DevKitMQTTClient` 是[Microsoft Azure IoT sdk](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client)中的**iothub_client**包装器，用于 C 的库可与 Azure IoT 中心交互。

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，可在 [IoT DevKit 常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)中查看解决方法，或者通过 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) 联系我们。 还可在此页中留言，向我们提供反馈。

## <a name="next-steps"></a>后续步骤

现已成功将 MXChip IoT DevKit 连接到 IoT 中心，并将捕获的传感器数据发送到了 IoT 中心。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
