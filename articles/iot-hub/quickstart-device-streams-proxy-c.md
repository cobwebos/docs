---
title: 适用于 SSH/RDP 的 Azure IoT 中心设备流 C 快速入门（预览）| Microsoft Docs
description: 在本快速入门中，你将运行一个示例 C 应用程序，该应用程序充当代理，可以通过 IoT 中心设备流实现 SSH/RDP 方案。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 300b42c9452fc58c857d075a7fd8c42fd6a1c409
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731727"
---
# <a name="quickstart-sshrdp-over-iot-hub-device-streams-using-c-proxy-application-preview"></a>快速入门：使用 C 应用程序代理通过 IoT 中心设备流实现 SSH/RDP 方案（预览）

[!INCLUDE [iot-hub-quickstarts-4-selector](../../includes/iot-hub-quickstarts-4-selector.md)]

服务和设备应用程序可以使用 [IoT 中心设备流](./iot-hub-device-streams-overview.md)以安全且防火墙友好的方式进行通信。 有关设置概述，请参阅[此页](./iot-hub-device-streams-overview.md#local-proxy-sample-for-ssh-or-rdp)。

本文档介绍通过设备流以隧道方式传输 SSH 流量（使用端口 22）的设置。 针对 RDP 流量的设置与此类似，只需进行简单的配置更改即可。 由于设备流不区分应用程序和协议，因此，可以修改现有快速入门（通过更改通信端口）来适应其他类型的应用程序流量。

## <a name="how-it-works"></a>工作原理：
下图演示了设备本地和服务本地代理程序的设置如何在 SSH 客户端与 SSH 守护程序进程之间实现端到端的连接。 在公共预览期，C SDK 仅支持设备端中的设备流。 因此，本快速入门只会提供有关运行设备本地代理应用程序的说明。 应该运行 [C# 快速入门](./quickstart-device-streams-proxy-csharp.md)或 [Node.js 快速入门](./quickstart-device-streams-proxy-nodejs.md)指南中随附的服务本地代理应用程序。

![替代文本](./media/quickstart-device-streams-proxy-csharp/device-stream-proxy-diagram.svg "本地代理设置")


1. 服务本地代理连接到 IoT 中心，并向目标设备发起设备流。

2. 设备本地代理完成流发起握手，并通过 IoT 中心的流式处理终结点与服务端建立端到端的流式处理隧道。

3. 设备本地代理连接到侦听设备端口 22（此端口可配置，[如下](#run-the device-local-proxy-application)所述）的 SSH 守护程序 (SSHD)。

4. 服务本地代理通过侦听指定的端口（在本例中为端口 2222，此端口也可配置，[如下](#run-the-device-local-proxy-application)所述），等待用户建立新的 SSH 连接。 当用户通过 SSH 客户端连接时，该隧道使 SSH 应用程序流量可在 SSH 客户端与服务器程序之间进行传输。

> [!NOTE]
> 通过设备流发送的 SSH 流量将通过 IoT 中心的流式处理终结点以隧道方式进行传输，而不是直接在服务与设备之间发送。 这就带来了[这些优势](./iot-hub-device-streams-overview.md#benefits)。 此外，下图演示了在同一台设备（或计算机）上作为设备本地代理运行的 SSH 守护程序。 在本快速入门中，提供 SSH 守护程序 IP 地址还可让设备本地代理和守护程序在不同的计算机上运行。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 安装 [Visual Studio 2017](https://www.visualstudio.com/vs/) 并启用[“使用 C++ 的桌面开发”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷。
* [安装最新版本的 Git](https://git-scm.com/download/)。

## <a name="prepare-the-development-environment"></a>准备开发环境

针对本快速入门，你将使用[适用于 C 的 Azure IoT 设备 SDK](iot-hub-device-sdk-c-intro.md)。准备一个用于从 GitHub 克隆和生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 GitHub 上的 SDK 包括在本快速入门中使用的示例代码。 


1. 从 [GitHub](https://github.com/Kitware/CMake/releases/tag/v3.11.4) 下载 3.11.4 版的 [CMake 生成系统](https://cmake.org/download/)。 使用相应的加密哈希值验证下载的二进制文件。 以下示例使用了 Windows PowerShell 来验证 x64 MSI 分发版本 3.11.4 的加密哈希：

    ```PowerShell
    PS C:\Downloads> $hash = get-filehash .\cmake-3.11.4-win64-x64.msi
    PS C:\Downloads> $hash.Hash -eq "56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869"
    True
    ```
    
    在撰写本文时，在 CMake 站点上列出了版本 3.11.4 的以下哈希值：

    ```
    6dab016a6b82082b8bcd0f4d1e53418d6372015dd983d29367b9153f1a376435  cmake-3.11.4-Linux-x86_64.tar.gz
    72b3b82b6d2c2f3a375c0d2799c01819df8669dc55694c8b8daaf6232e873725  cmake-3.11.4-win32-x86.msi
    56e3605b8e49cd446f3487da88fcc38cb9c3e9e99a20f5d4bd63e54b7a35f869  cmake-3.11.4-win64-x64.msi
    ```

    在进行 `CMake` 安装**之前**，必须在计算机上安装 Visual Studio 必备组件（Visual Studio 和“使用 C++ 的桌面开发”工作负荷）。 满足先决条件并验证下载内容后，安装 CMake 生成系统。

2. 打开命令提示符或 Git Bash shell。 执行以下命令克隆 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库：
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```
    此存储库的大小目前大约为 220 MB。 应该预料到此操作需要几分钟才能完成。


3. 在 git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。 

    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

4. 运行以下命令，生成特定于你的开发客户端平台的 SDK 版本。 在 Windows 中，模拟设备的 Visual Studio 解决方案将在 `cmake` 目录中生成。 

```
    # In Linux
    cmake ..
    make -j
```

在 Windows 中，请在 Visual Studio 2015 或 2017 的开发人员命令提示符下运行以下命令：

```
    rem In Windows
    rem For VS2015
    cmake .. -G "Visual Studio 15 2015"

    rem Or for VS2017
    cmake .. -G "Visual Studio 15 2017"

    rem Then build the project
    cmake --build . -- /m /p:Configuration=Release
```
    

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub-device-streams.md)]

## <a name="register-a-device"></a>注册设备

必须先将设备注册到 IoT 中心，然后该设备才能进行连接。 在本部分，将使用具有 [IoT 扩展](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot?view=azure-cli-latest)的 Azure Cloud Shell 注册模拟设备。

1. 在 Azure Cloud Shell 中运行以下命令，以添加 IoT 中心 CLI 扩展并创建设备标识。 

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

   **MyDevice**：这是为注册的设备提供的名称。 如示例中所示使用 MyDevice。 如果为设备选择不同名称，则可能还需要在本文中从头至尾使用该名称，并在运行示例应用程序之前在其中更新设备名称。

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    az iot hub device-identity create --hub-name YourIoTHubName --device-id MyDevice
    ```

2. 在 Azure Cloud Shell 中运行以下命令，以获取刚注册设备的_设备连接字符串_：

   **YourIoTHubName**：将下面的占位符替换为你为 IoT 中心选择的名称。

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name YourIoTHubName --device-id MyDevice --output table
    ```

    记下设备连接字符串，如以下示例所示：

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyDevice;SharedAccessKey={YourSharedAccessKey}`

    稍后会在快速入门中用到此值。


## <a name="ssh-to-a-device-via-device-streams"></a>使用 SSH 通过设备流连接到设备

### <a name="run-the-device-local-proxy-application"></a>运行设备本地代理应用程序

- 编辑源文件 `iothub_client/samples/iothub_client_c2d_streaming_proxy_sample/iothub_client_c2d_streaming_proxy_sample.c`，并提供设备连接字符串、目标设备 IP/主机名和 RDP 端口 22：
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[Connection string of IoT Hub]";
  static const char* localHost = "[IP/Host of your target machine]"; // Address of the local server to connect to.
  static const size_t localPort = 22; // Port of the local server to connect to.
```

- 按如下所示编译示例：

```
    # In Linux
    # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    make -j
```

```
    rem In Windows
    rem Go to cmake at root of repository
    cmake --build . -- /m /p:Configuration=Release
```

- 在设备上运行编译的程序：
```
    # In Linux
    # Go to sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_proxy_sample
    ./iothub_client_c2d_streaming_proxy_sample
```

```
    rem In Windows
    rem Go to sample's release folder cmake\iothub_client\samples\iothub_client_c2d_streaming_proxy_sample\Release
    iothub_client_c2d_streaming_proxy_sample.exe
```

### <a name="run-the-service-local-proxy-application"></a>运行服务本地代理应用程序

若要按[如上](#how-it-works)所述建立端到端的流以通过隧道传输 SSH 流量，需要在每端（即服务和设备端）设置一个本地代理。 不过，在公共预览期，IoT 中心 C SDK 仅支持设备端中的设备流。 对于服务本地代理，请改用 [C# 快速入门](./quickstart-device-streams-proxy-csharp.md)或 [Node.js 快速入门](./quickstart-device-streams-proxy-nodejs.md)中随附的指南。


### <a name="establish-an-ssh-session"></a>从 SSH 会话建立连接

假设设备本地代理和服务本地代理都在运行，请使用 SSH 客户端程序并连接到端口 2222 上的服务本地代理（而不要直接连接到 SSH 守护程序）。 

```
ssh <username>@localhost -p 2222
```

此时会看到 SSH 登录提示，其中要求输入凭据。


通过 `IP_address:22` 连接到 SSH 守护程序的设备本地代理中的控制台输出：![替代文本](./media/quickstart-device-streams-proxy-c/device-console-output.PNG "设备本地代理输出")

SSH 客户端程序的控制台输出（SSH 客户端通过连接到服务本地代理侦听的端口 22 来与 SSH 守护程序通信）：![替代文本](./media/quickstart-device-streams-proxy-csharp/ssh-console-output.png "SSH 客户端输出")

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>后续步骤

在本快速入门中，你设置了一个 IoT 中心、注册了一个设备、部署了一个设备和一个用于通过 IoT 中心建立设备流的服务本地代理程序，并使用了代理通过隧道传输 SSH 流量。

请使用以下链接详细了解设备流：

> [!div class="nextstepaction"]
> [设备流概述](./iot-hub-device-streams-overview.md)
