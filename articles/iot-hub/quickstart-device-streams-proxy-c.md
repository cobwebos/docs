---
title: 适用于 SSH 和 RDP 的 Azure IoT 中心设备流 C 快速入门
description: 在本快速入门中，我们将运行一个示例 C 应用程序，该应用程序充当代理，可以通过 IoT 中心设备流实现 SSH 和 RDP 方案。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/14/2019
ms.author: robinsh
ms.openlocfilehash: b8cba8f7a21b04dc722124eb2873c64f67fd6def
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727108"
---
# <a name="quickstart-enable-ssh-and-rdp-over-an-iot-hub-device-stream-by-using-a-c-proxy-application-preview"></a>快速入门：使用 C 代理应用程序通过 IoT 中心设备流实现 SSH 和 RDP 方案（预览）

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

Azure IoT 中心目前支持设备流作为[预览版功能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

服务和设备应用程序可以使用 [IoT 中心设备流](./iot-hub-device-streams-overview.md)以安全且防火墙友好的方式进行通信。 有关设置概述，请参阅[本地代理示例页](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)。

本快速入门介绍通过设备流以隧道方式传输安全外壳 (SSH) 流量（使用端口 22）的设置。 针对远程桌面协议 (RDP) 流量的设置与此类似，只需进行简单的配置更改即可。 由于设备流不区分应用程序和协议，因此，可以修改本快速入门来适应其他类型的应用程序流量。

## <a name="how-it-works"></a>工作原理

下图演示了设备本地和服务本地代理程序如何在 SSH 客户端与 SSH 守护程序进程之间实现端到端的连接。 在公共预览期，C SDK 仅支持设备端的设备流。 因此，本快速入门只会提供有关运行设备本地代理应用程序的说明。 若要生成和运行随附的服务端应用程序，请按照以下快速入门之一中的说明进行操作：

* [使用 C# 代理通过 IoT 中心设备流实现 SSH/RDP 方案](./quickstart-device-streams-proxy-csharp.md)
* [使用 NodeJS 代理通过 IoT 中心设备流实现 SSH/RDP 方案](./quickstart-device-streams-proxy-nodejs.md)。

![本地代理设置](./media/quickstart-device-streams-proxy-c/device-stream-proxy-diagram.png)

1. 服务本地代理连接到 IoT 中心，并启动发往目标设备的设备流。

2. 设备本地代理完成流发起握手，并通过 IoT 中心的流式处理终结点与服务端建立端到端的流式处理隧道。

3. 设备本地代理连接到在设备上侦听端口 22 的 SSH 守护程序。 可按“运行设备本地代理应用程序”部分中所述对此设置进行配置。

4. 服务本地代理通过侦听指定的端口（在本例中为端口 2222），等待用户建立新的 SSH 连接。 可按“运行设备本地代理应用程序”部分中所述对此设置进行配置。 当用户通过 SSH 客户端连接时，该隧道使 SSH 应用程序流量可在 SSH 客户端与服务器程序之间进行传输。

> [!NOTE]
> 通过设备流发送的 SSH 流量将通过 IoT 中心的流式处理终结点以隧道方式进行传输，而不是直接在服务与设备之间发送。 有关详细信息，请参阅[使用 IoT 中心设备流的好处](iot-hub-device-streams-overview.md#benefits)。 此外，下图演示了在同一台设备（或计算机）上作为设备本地代理运行的 SSH 守护程序。 在本快速入门中，提供 SSH 守护程序 IP 地址还可让设备本地代理和守护程序在不同的计算机上运行。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 目前仅以下区域中创建的 IoT 中心支持设备流预览：

  * 美国中部
  * 美国中部 EUAP
  * 北欧
  * 东南亚

* 安装 [Visual Studio 2019](https://www.visualstudio.com/vs/) 并启用[使用 C++ 的桌面开发](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷。
* [安装最新版本的 Git](https://git-scm.com/download/)。

* 运行以下命令将用于 Azure CLI 的 Azure IoT 扩展添加到 Cloud Shell 实例。 IOT 扩展会将 IoT 中心、IoT Edge 和 IoT 设备预配服务 (DPS) 特定的命令添加到 Azure CLI。

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="prepare-the-development-environment"></a>准备开发环境

在本快速入门中，我们将使用[适用于 C 的 Azure IoT 设备 SDK](iot-hub-device-sdk-c-intro.md)。准备一个用于从 GitHub 克隆和生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 GitHub 上的 SDK 包括在本快速入门中使用的示例代码。

1. 下载 [CMake 生成系统](https://cmake.org/download/)。

    在开始安装 CMake 之前，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负荷）。   安装必备组件并验证下载内容后，安装 CMake 生成系统。

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

   * 在 Windows 中，请在 Visual Studio 2015 或 2017 的开发人员命令提示下运行以下命令。 将在 *cmake* 目录中生成模拟设备的 Visual Studio 解决方案。

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

## <a name="ssh-to-a-device-via-device-streams"></a>使用 SSH 通过设备流连接到设备

在本部分中，你将建立一个端到端的流，通过隧道来传输 SSH 流量。

### <a name="run-the-device-local-proxy-application"></a>运行设备本地代理应用程序

1. 在文件夹 `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample` 中编辑源文件 **iothub_client_c2d_streaming_proxy_sample.c**，并提供设备连接字符串、目标设备 IP/主机名，以及 SSH 端口 22：

   ```C
   /* Paste in your device connection string  */
   static const char* connectionString = "{DeviceConnectionString}";
   static const char* localHost = "{IP/Host of your target machine}"; // Address of the local server to connect to.
   static const size_t localPort = 22; // Port of the local server to connect to.
   ```

1. 编译示例：

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   make -j
   ```

   ```cmd
   rem In Windows
   rem Go to cmake at root of repository
   cmake --build . -- /m /p:Configuration=Release
   ```

1. 在设备上运行编译的程序：

   ```bash
   # In Linux
   # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
   ./iothub_client_c2d_streaming_proxy_sample
   ```

   ```cmd
   rem In Windows
   rem Go to the sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
   iothub_client_c2d_streaming_proxy_sample.exe
   ```

### <a name="run-the-service-local-proxy-application"></a>运行服务本地代理应用程序

若要按“工作原理”部分所述建立端到端的流以通过隧道传输 SSH 流量，需要在每端（服务端和设备端）设置一个本地代理。 在公共预览期，IoT 中心 C SDK 仅支持设备端的设备流。 若要生成并运行服务本地代理，请遵照以下快速入门之一中的说明：

   * [使用 C# 代理应用通过 IoT 中心设备流实现 SSH/RDP 方案](./quickstart-device-streams-proxy-csharp.md)
   * [使用 Node.js 代理应用通过 IoT 中心设备流实现 SSH/RDP 方案](./quickstart-device-streams-proxy-nodejs.md)

### <a name="establish-an-ssh-session"></a>从 SSH 会话建立连接

设备本地代理和服务本地代理都在运行以后，请使用 SSH 客户端程序并连接到端口 2222 上的服务本地代理（而不要直接连接到 SSH 守护程序）。

```cmd/sh
ssh {username}@localhost -p 2222
```

此时，SSH 登录窗口会提示输入凭据。

下图显示了通过 `IP_address:22` 连接到 SSH 守护程序的设备本地代理中的控制台输出：

![设备本地代理输出](./media/quickstart-device-streams-proxy-c/device-console-output.png)

下图显示了 SSH 客户端程序的控制台输出。 SSH 客户端通过连接到服务本地代理侦听的端口 22 来与 SSH 守护程序通信：

![SSH 客户端输出](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png)

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们已设置一个 IoT 中心、注册了一个设备、部署了一个设备和一个用于通过 IoT 中心建立设备流的服务本地代理程序，并已使用代理通过隧道传输了 SSH 流量。

若要详细了解设备流，请参阅：

> [!div class="nextstepaction"]
> [设备流概述](./iot-hub-device-streams-overview.md)
