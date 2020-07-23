---
title: 通过 Azure IoT 中心设备流使用 C 与设备应用进行通信
description: 在本快速入门中，我们将运行一个通过设备流来与 IoT 设备通信的 C 设备端应用程序。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 7b5487233e2bd0e532efad4e108c41f188b51d91
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727149"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>快速入门：通过 IoT 中心设备流在 C 中与设备应用程序通信（预览）

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

Azure IoT 中心目前支持设备流作为[预览版功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

服务和设备应用程序可以使用 [IoT 中心设备流](iot-hub-device-streams-overview.md)以安全且防火墙友好的方式进行通信。 在公共预览期，C SDK 仅支持设备端的设备流。 因此，本快速入门只介绍如何运行设备端应用程序。 若要运行相应的服务端应用程序，请参阅以下文章：

* [通过 IoT 中心设备流使用 C# 与设备应用进行通信](./quickstart-device-streams-echo-csharp.md)

* [通过 IoT 中心设备流使用 Node.js 与设备应用进行通信](./quickstart-device-streams-echo-nodejs.md)

本快速入门中的设备端 C 应用程序具有以下功能：

* 建立发往 IoT 设备的设备流。

* 接收从服务端应用程序发送的数据并将其回显。

代码将演示设备流的发起过程，以及如何用其来发送和接收数据。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

需要具备以下先决条件：

* 安装 [Visual Studio 2019](https://www.visualstudio.com/vs/) 并启用**使用 C++ 的桌面开发**工作负荷。

* [安装最新版本的 Git](https://git-scm.com/download/)。

* 运行以下命令将用于 Azure CLI 的 Azure IoT 扩展添加到 Cloud Shell 实例。 IOT 扩展会将 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 特定的命令添加到 Azure CLI。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

   [!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

目前仅以下区域中创建的 IoT 中心支持设备流预览：

  * 美国中部
  * 美国中部 EUAP
  * 北欧
  * 东南亚

## <a name="prepare-the-development-environment"></a>准备开发环境

在本快速入门中，我们将使用[适用于 C 的 Azure IoT 设备 SDK](iot-hub-device-sdk-c-intro.md)。准备一个用于从 GitHub 克隆和生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 GitHub 上的 SDK 包括在本快速入门中使用的示例代码。

   > [!NOTE]
   > 在开始此过程之前，请确保已安装了带有“使用 C++ 进行桌面开发”  工作负荷的 Visual Studio。

1. 按照下载页上的说明安装 [CMake 生成系统](https://cmake.org/download/)。

1. 打开命令提示符或 Git Bash shell。 运行以下命令克隆 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库：

    ```cmd/sh
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    此操作需要花费几分钟时间。

1. 在 git 存储库的根目录中创建 cmake  子目录，并导航到该文件夹。 从 azure-iot-sdk-c  目录运行以下命令：

    ```cmd/sh
    mkdir cmake
    cd cmake
    ```

1. 从 *cmake* 目录运行以下命令，以生成特定于开发客户端平台的 SDK 版本。

   * 在 Linux 中：

      ```bash
      cmake ..
      make -j
      ```

   * 在 Windows 中，打开 [Visual Studio 的开发人员命令提示](/dotnet/framework/tools/developer-command-prompt-for-vs)。 运行 Visual Studio 版本的命令。 本快速入门使用 Visual Studio 2019。 这些命令在 *cmake* 目录中为模拟设备创建 Visual Studio 解决方案。

      ```cmd
      rem For VS2015
      cmake .. -G "Visual Studio 14 2015"

      rem Or for VS2017
      cmake .. -G "Visual Studio 15 2017"

      rem Or for VS2019
      cmake .. -G "Visual Studio 16 2019"

      rem Then build the project
      cmake --build . -- /m /p:Configuration=Release
      ```

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本部分中，我们将使用具有 [IoT 扩展](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest)的 Azure Cloud Shell 注册模拟设备。

1. 若要创建设备标识，请在 Cloud Shell 中运行以下命令：

   > [!NOTE]
   > * 请将 *YourIoTHubName* 占位符替换为你为 IoT 中心选择的名称。
   > * 对于正在注册的设备的名称，建议使用 *MyDevice*，如下所示。 如果为设备选择其他名称，请在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id MyDevice
    ```

1. 若要获取刚刚注册的设备的*设备连接字符串*，请在 Cloud Shell 中运行以下命令：

   > [!NOTE]
   > 请将 *YourIoTHubName* 占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id MyDevice --output table
    ```

    请记下返回的设备连接字符串，以便稍后在此快速入门中使用。 如以下示例所示：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

## <a name="communicate-between-the-device-and-the-service-via-device-streams"></a>通过设备流在设备与服务之间通信

在本部分中，你将运行设备端应用程序和服务器端应用程序并在两者之间进行通信。

### <a name="run-the-device-side-application"></a>运行设备端应用程序

若要运行设备端应用程序，请执行以下步骤：

1. 通过编辑 `iothub_client/samples/iothub_client_c2d_streaming_sample` 文件夹中的 **iothub_client_c2d_streaming_sample.c** 源文件并添加设备连接字符串来提供设备凭据。

   ```C
   /* Paste in your iothub connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   ```

1. 使用以下命令编译代码：

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to the cmake folder at the root of repo
   cmake --build . -- /m /p:Configuration=Release
   ```

1. 运行编译的程序：

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
   ./iothub_client_c2d_streaming_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
   iothub_client_c2d_streaming_sample.exe
   ```

### <a name="run-the-service-side-application"></a>运行服务端应用程序

如前所述，IoT 中心 C SDK 仅支持设备端的设备流。 若要生成和运行随附的服务端应用程序，请按照以下快速入门之一中的说明进行操作：

* [通过 IoT 中心设备流使用 C# 与设备应用进行通信](./quickstart-device-streams-echo-csharp.md)

* [通过 IoT 中心设备流使用 Node.js 与设备应用进行通信](./quickstart-device-streams-echo-nodejs.md)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources-device-streams](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们设置了 IoT 中心、注册了设备、在设备上的 C 应用程序和服务端上的其他应用程序之间建立了设备流，并已使用该流在应用程序之间来回发送数据。

若要详细了解设备流，请参阅：

> [!div class="nextstepaction"]
> [设备流概述](./iot-hub-device-streams-overview.md)
