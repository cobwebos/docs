---
title: 快速入门：向 Azure IoT 中心发送遥测数据 (C) | Microsoft Docs
description: 在本快速入门中，你将运行两个示例 C 应用程序，向 IoT 中心发送模拟遥测数据并读取 IoT 中心的遥测数据，以在云中进行处理。
author: wesmc7777
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom:
- mvc
- mqtt
ms.date: 04/10/2019
ms.author: wesmc
ms.openlocfilehash: 4fbebfe2ec554c7c9c2f85a5657daeeee8dae31a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727074"
---
# <a name="quickstart-send-telemetry-from-a-device-to-an-iot-hub-and-read-it-with-a-back-end-application-c"></a>快速入门：将遥测数据从设备发送到 IoT 中心并使用后端应用程序读取该数据 (C)

[!INCLUDE [iot-hub-quickstarts-1-selector](../../includes/iot-hub-quickstarts-1-selector.md)]

IoT 中心是一项 Azure 服务，用于将大量遥测数据从 IoT 设备引入云中进行存储或处理。 本快速入门会将模拟设备应用程序的遥测数据通过 IoT 中心发送到后端应用程序进行处理。

本快速入门使用 [适用于 C 的 Azure IoT 设备 SDK](iot-hub-device-sdk-c-intro.md) 中的 C 示例应用程序向 IoT 中心发送遥测数据。 Azure IoT 设备 SDK 采用 [ANSI C (C99)](https://wikipedia.org/wiki/C99) 编写，具有可移植性和广泛的平台兼容性。 运行示例代码之前，需要创建 IoT 中心并向该中心注册模拟设备。

虽然本文面向 Windows，但在 Linux 上也可以完成本快速入门。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 安装 [Visual Studio 2019](https://www.visualstudio.com/vs/) 并启用[“使用 C++ 的桌面开发”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷。

* [安装最新版本的 Git](https://git-scm.com/download/)。

* 确保已在防火墙中打开端口 8883。 本快速入门中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。


* 运行以下命令将用于 Azure CLI 的 Microsoft Azure IoT 扩展添加到 Cloud Shell 实例。 IoT 扩展会将特定于 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 的命令添加到 Azure CLI。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>准备开发环境

在本快速入门中，我们将使用[适用于 C 的 Azure IoT 设备 SDK](iot-hub-device-sdk-c-intro.md)。 

对于以下环境，可以通过安装这些包和库来使用 SDK：

* **Linux**：apt-get 包适用于使用以下 CPU 体系结构的 Ubuntu 16.04 和 18.04：amd64、arm64、armhf 和 i386。 有关详细信息，请参阅[使用 apt-get 在 Ubuntu 上创建 C 设备客户端项目](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/ubuntu_apt-get_sample_setup.md)。

* **mbed**：对于在 mbed 平台上创建设备应用程序的开发人员，我们发布了可帮助在几分钟内学会使用 Azure IoT 中心的库和示例。 有关详细信息，请参阅[使用 mbed 库](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#mbed)。

* **Arduino**：如果在 Arduino 上进行开发，则可以利用 Arduino IDE 库管理器提供的 Azure IoT 库。 有关详细信息，请参阅[适用于 Arduino 的 Azure IoT 中心库](https://github.com/azure/azure-iot-arduino)。

* **iOS**：IoT 中心设备 SDK 能够以 CocoaPods 的形式提供，用于 Mac 和 iOS 设备开发。 有关详细信息，请参阅 [Microsoft Azure IoT 的 iOS 示例](https://cocoapods.org/pods/AzureIoTHubClient)。

但在本快速入门中，你将准备一个用于从 GitHub 克隆和生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 GitHub 上的 SDK 包括在本快速入门中使用的示例代码。

1. 下载 [CMake 生成系统](https://cmake.org/download/)。

    在进行 `CMake` 安装**之前**，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负荷）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。

2. 找到[最新版](https://github.com/Azure/azure-iot-sdk-c/releases/latest) SDK 的标记名称。

3. 打开命令提示符或 Git Bash shell。 运行以下命令，克隆最新版 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 使用在上一步找到的标记作为 `-b` 参数的值：

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    应该预料到此操作需要几分钟才能完成。

4. 在 git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。 从 `azure-iot-sdk-c` 目录运行以下命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

5. 运行以下命令，以生成特定于你的开发客户端平台的 SDK 版本。 将在 `cmake` 目录中生成模拟设备的 Visual Studio 解决方案。

    ```cmd
    cmake ..
    ```

    如果 `cmake` 找不到 C++ 编译器，则可能会在运行以上命令时出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)窗口中运行该命令。 

    生成成功后，最后的几个输出行如下所示：

    ```cmd/sh
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本部分中，我们将使用具有 [IoT 扩展](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)的 Azure Cloud Shell 注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以创建设备标识。

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyCDevice**：这是所注册的设备的名称。 建议使用 **MyCDevice**，如图所示。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyCDevice
    ```

2. 在 Azure Cloud Shell 中运行以下命令，以获取刚注册设备的_设备连接字符串_：

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyCDevice --output table
    ```

    记下如下所示的设备连接字符串：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyCDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。

## <a name="send-simulated-telemetry"></a>发送模拟遥测数据

模拟设备应用程序会连接到 IoT 中心上特定于设备的终结点，并发送一个字符串作为模拟遥测数据。

1. 使用文本编辑器打开 iothub_convenience_sample.c 源文件，并查看用于发送遥测数据的示例代码。 该文件位于克隆 Azure IoT C SDK 的工作目录下的以下位置：

    ```
    azure-iot-sdk-c\iothub_client\samples\iothub_convenience_sample\iothub_convenience_sample.c
    ```

2. 找到 `connectionString` 常量的声明：

    ```C
    /* Paste in your device connection string  */
    static const char* connectionString = "[device connection string]";
    ```

    将 `connectionString` 常量的值替换为之前记下的设备连接字符串。 然后将更改保存到 iothub_convenience_sample.c  。

3. 在本地终端窗口中，导航到在 Azure IoT C SDK 中创建的 CMake 目录中的 iothub_convenience_sample 项目目录  。 在工作目录中输入以下命令：

    ```cmd/sh
    cd azure-iot-sdk-c/cmake/iothub_client/samples/iothub_convenience_sample
    ```

4. 在本地终端窗口中运行 CMake 以使用更新的 `connectionString` 值生成示例：

    ```cmd/sh
    cmake --build . --target iothub_convenience_sample --config Debug
    ```

5. 在本地终端窗口中运行以下命令，以便运行模拟设备应用程序：

    ```cmd/sh
    Debug\iothub_convenience_sample.exe
    ```

    以下屏幕截图显示了模拟设备应用程序将遥测数据发送到 IoT 中心后的输出：

    ![运行模拟设备](media/quickstart-send-telemetry-c/simulated-device-app.png)

## <a name="read-the-telemetry-from-your-hub"></a>从中心读取遥测数据

在本部分中，你将使用具有 [IoT 扩展](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)的 Azure Cloud Shell 监视模拟设备发送的设备消息。

1. 通过 Azure Cloud Shell 运行以下命令以建立连接并从 IoT 中心读取消息：

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub monitor-events --hub-name {YourIoTHubName} --output table
    ```

    ![使用 Azure CLI 读取设备消息](media/quickstart-send-telemetry-c/read-device-to-cloud-messages-app.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门设置了 IoT 中心、注册了设备、使用 C 应用程序发送了模拟遥测数据到中心，并使用 Azure Cloud Shell 读取了中心的遥测数据。

要详细了解如何使用 Azure IoT 中心 C SDK 进行开发，请继续学习以下的操作指南：

> [!div class="nextstepaction"]
> [使用 Azure IoT 中心 C SDK 进行开发](iot-hub-devguide-develop-for-constrained-devices.md)
