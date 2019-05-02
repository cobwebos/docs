---
title: IoT DevKit 到云 -- 将 IoT DevKit AZ3166 连接到 Azure IoT 中心 | Microsoft Docs
description: 在本教程中了解如何设置 IoT DevKit AZ3166 并将其连接到 Azure IoT 中心，使其能够将数据发送到 Azure 云平台。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/17/2019
ms.author: wesmc
ms.openlocfilehash: 2f86b74299b5d47a87ed0b8e89a992f0f91a84be
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924629"
---
# <a name="connect-iot-devkit-az3166-to-azure-iot-hub"></a>将 IoT DevKit AZ3166 连接到 Azure IoT 中心

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

[MXChip IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/) 可用于开发物联网 (IoT) 解决方案和构建其原型，以利用 Microsoft Azure 服务。 它包括一个具有多种外设和传感器、 一个开源板包，以及丰富的与 Arduino 兼容板[示例库](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)。

## <a name="what-you-learn"></a>学习内容

* 如何创建 IoT 中心以及注册 MXChip IoT DevKit 的设备。
* 如何将 IoT DevKit 连接到的 Wi-fi 和配置 IoT 中心连接字符串。
* 如何将 DevKit 传感器的遥测数据发送到 IoT 中心。
* 如何准备开发环境和开发 IoT DevKit 的应用程序。

还没有 DevKit？ 请尝试 [DevKit 模拟器](https://azure-samples.github.io/iot-devkit-web-simulator/)或[购买 DevKit](https://aka.ms/iot-devkit-purchase)。

## <a name="what-you-need"></a>所需条件

* 使用 Micro USB 电缆的 MXChip IoT DevKit 板。 [立即获取](https://aka.ms/iot-devkit-purchase)。
* 运行 macOS 10.10 以上版本或 Ubuntu 18.04 + 的 Windows 10 的计算机。
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

## <a name="quickstart-send-telemetry-from-devkit-to-an-iot-hub"></a>快速入门：将从 DevKit 的遥测数据发送到 IoT 中心

快速开始使用预编译的 DevKit 固件将遥测数据发送到 IoT 中心。 在运行之前，您创建 IoT 中心，并向中心注册设备。

### <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本快速入门中，将使用 Azure Cloud Shell 来注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以创建设备标识。

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyNodeDevice**：所注册的设备的名称。 请按显示的方法使用 MyNodeDevice。 如果为设备选择其他名称，则需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyNodeDevice
    ```
   > [!NOTE]
   > 如果收到运行错误`device-identity`，安装[Azure 适用于 Azure CLI 的 IOT 扩展](https://github.com/Azure/azure-iot-cli-extension/blob/dev/README.md)的更多详细信息。
  
1. 在 Azure Cloud Shell 中运行以下命令，以获取刚注册设备的_设备连接字符串_：

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyNodeDevice --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

### <a name="send-devkit-telemetry"></a>将 DevKit 遥测数据发送

将 DevKit 连接到 IoT 中心上的特定于设备的终结点，并将温度和湿度遥测数据发送。

1. 下载最新版[GetStarted 固件](https://aka.ms/devkit/prod/getstarted/latest)为 IoT DevKit。

1. 请确保 IoT DevKit 连接到您的计算机通过 USB。 打开文件资源管理器中没有名为 USB 大容量存储设备**AZ3166**。
    ![打开 Windows 资源管理器](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/az3166-usb.png)

1. 拖放到大容量存储设备，且该刚下载固件将自动闪烁。
    ![复制固件](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/copy-firmware.png)

1. 在 DevKit 上按下按钮**B**、 推送和释放**重置**按钮，然后松开按钮**B**。DevKit 将进入 AP 模式。 若要确认，屏幕将显示 DevKit 和配置门户 IP 地址的服务集标识符 (SSID)。
    ![重置按钮、 按钮 B 和 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ap.jpg)

    ![设置 AP 模式](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/set-ap-mode.gif)

1. 使用不同的 Wi-fi 上的 Web 浏览器启用的设备 （计算机或移动电话） 连接到在上一步中显示的 IoT DevKit SSID。 如果它要求提供密码，将保留为空。
    ![连接 SSID](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/connect-ssid.png)

1. 打开**192.168.0.1**在浏览器中。 选择 Wi-fi 所需 IoT DevKit 连接到，在键入的 Wi-fi 密码，然后粘贴你所做的以前注意的设备连接字符串。 然后单击“保存”。
    ![配置 UI](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui.png)

    > [!NOTE]
    > 将 IoT DevKit 仅支持 2.4 GHz 网络。 有关详细信息，请查阅 [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#wi-fi-configuration)。

1. 当您看到结果页时，将 IoT DevKit 到存储 WiFi 信息和设备连接字符串。
    ![配置结果](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/configuration-ui-result.png)

    > [!NOTE]
    > 配置 Wi-Fi 后，即使拔下设备插头，该连接的凭据也会保留在设备上。

1. 几秒钟后重启 IoT DevKit。 在 DevKit 屏幕上，您会看到 DevKit 的 IP 地址追踪包括温度遥测数据，消息计数湿度值发送到 Azure IoT 中心。
    ![WiFi IP](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/wifi-ip.jpg)

    ![发送数据](media/iot-hub-arduino-devkit-az3166-get-started/quickstarts/sending-data.jpg)

## <a name="prepare-the-development-environment"></a>准备开发环境

请按照下列步骤来准备开发环境以便使用 DevKit:

### <a name="install-visual-studio-code-with-azure-iot-tools-extension-package"></a>使用 Azure IoT 工具扩展包安装 Visual Studio Code

1. 安装 [Arduino IDE](https://www.arduino.cc/en/Main/Software)。 此 IDE 提供必要的工具链用于编译和上传 Arduino 代码。
    * Windows：使用 Windows Installer 版本。 不从应用商店安装。
    * **macOS**：将解压缩的 **Arduino.app** 拖放到 `/Applications` 文件夹中。
    * **Ubuntu**：解压缩到某个文件夹中，例如 `$HOME/Downloads/arduino-1.8.8`

2. 安装[Visual Studio Code](https://code.visualstudio.com/)、 跨平台源的代码编辑器，具有强大的 intellisense、 代码完成和调试支持以及丰富的扩展可从 marketplace 安装。

3. 启动 VS Code，在扩展市场中找到 **Arduino** 并安装它。 此扩展提供在 Arduino 平台上进行开发的增强体验。
    ![安装 Arduino](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-arduino.png)

4. 在扩展市场中找到 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 并安装它。
    ![安装 Azure IoT Tools](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/install-azure-iot-tools.png)

    或者，使用此直接链接：
    > [!div class="nextstepaction"]
    > [安装 Azure IoT 工具扩展包](vscode:extension/vsciot-vscode.azure-iot-tools)

    > [!NOTE]
    > Azure IoT 工具扩展包包含[Azure IoT 设备 Workbench](https://aka.ms/iot-workbench)用于开发和调试各种 IoT devkit 设备上。 [IoT 中心的 Azure 工具包](https://aka.ms/iot-toolkit)，同时也包含在 Azure IoT 工具扩展包，用于管理和使用 Azure IoT 中心进行交互。

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

[ST-Link/V2](https://www.st.com/en/development-tools/st-link-v2.html) 是 IoT DevKit 用来与开发计算机通信的 USB 接口。 需要将它安装在 Windows 上才能将编译的设备代码刷写到 DevKit。 遵循 OS 特定的步骤，使计算机能够访问你的设备。

* Windows：下载并安装 USB 驱动程序从[STMicroelectronics 网站](https://www.st.com/en/development-tools/stsw-link009.html)或[直接链接](https://aka.ms/stlink-v2-windows)。
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

现已完成开发环境的准备和配置工作。 让我们生成 GetStarted 示例只需运行。

## <a name="build-your-first-project"></a>生成第一个项目

### <a name="open-sample-code-from-sample-gallery"></a>从示例库打开示例代码

将 IoT DevKit 包含内容丰富的库可用于学习的示例将 DevKit 连接到各种 Azure 服务。

1. 确保 IoT DevKit **未连接**到计算机。 先启动 VS Code，然后将 DevKit 连接到计算机。

1. 单击 `F1` 以打开命令面板，键入并选择“Azure IoT Device Workbench:**打开示例...”** 。然后选择“IoT DevKit”作为开发板。

1. 在 IoT Workbench 的“示例”页中，找到“入门”并单击“打开示例”。 然后选择用于下载示例代码的默认路径。
    ![打开示例](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/open-sample.png)

### <a name="provision-azure-iot-hub-and-device"></a>预配 Azure IoT 中心和设备

无需预配 Azure IoT 中心和从 Azure 门户的设备，你可以执行此操作在 VS Code 中无需离开开发环境。

1. 在打开的新项目窗口中，单击 `F1` 以打开命令面板，键入并选择“Azure IoT Device Workbench:预配 Azure 服务...”。遵照分步指南完成预配 Azure IoT 中心和创建 IoT 中心设备的操作。
    ![预配命令](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision.png)

    > [!NOTE]
    > 如果你尚未登录到 Azure， 请遵循弹出的通知登录。

1. 选择要使用的订阅。
    ![选择订阅](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-subscription.png)

1. 然后选择或新建一个[资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#terminology)。
    ![选择资源组](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-resource-group.png)

1. 在指定的资源组中，遵循指南选择或新建 Azure IoT 中心。
    ![选择 IoT 中心的步骤](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provision.png)

    ![选择 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-hub.png)

    ![选定的 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-selected.png)

1. 在输出窗口中，可以看到预配的 Azure IoT 中心![预配的 IoT 中心](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-hub-provisioned.png)

1. 在预配的 Azure IoT 中心内选择或新建一个设备。
    ![选择 IoT 设备的步骤](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/iot-device-provision.png)

    ![选择预配的 IoT 设备](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/select-iot-device.png)

1. 现已预配 Azure IoT 中心并在其中创建了设备。 另外，设备连接字符串将保存在 VS Code 中，供将来配置 IoT DevKit。
    ![完成预配](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/provision-done.png)

### <a name="configure-and-compile-device-code"></a>配置并编译设备代码

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

## <a name="review-the-code"></a>查看代码

`GetStarted.ino` 是主要的 Arduino 草图文件。

![D2C 消息](media/iot-hub-arduino-devkit-az3166-get-started/getting-started/code.png)

若要查看设备遥测数据如何发送到 Azure IoT 中心，请打开同一文件夹中的 `utility.cpp` 文件。 查看 [API 参考](https://microsoft.github.io/azure-iot-developer-kit/docs/apis/arduino-language-reference/)，了解如何在 IoT DevKit 中使用传感器和外围设备。

使用的 `DevKitMQTTClient` 是[适用于 C 的 Microsoft Azure IoT SDK 和库](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client)中 **iothub_client** 的包装器，用于与 Azure IoT 中心交互。

## <a name="problems-and-feedback"></a>问题和反馈

如果遇到问题，可在 [IoT DevKit 常见问题解答](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)中查看解决方法，或者通过 [Gitter](https://gitter.im/Microsoft/azure-iot-developer-kit) 联系我们。 还可在此页中留言，向我们提供反馈。

## <a name="next-steps"></a>后续步骤

现已成功将 MXChip IoT DevKit 连接到 IoT 中心，并将捕获的传感器数据发送到了 IoT 中心。

[!INCLUDE [iot-hub-get-started-az3166-next-steps](../../includes/iot-hub-get-started-az3166-next-steps.md)]
