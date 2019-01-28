---
title: Azure IoT 中心设备流 C 快速入门（预览）| Microsoft Docs
description: 在本快速入门中，你将运行一个 C 服务端应用程序，以便通过设备流与 IoT 设备通信。
author: rezasherafat
manager: briz
ms.service: iot-hub
services: iot-hub
ms.devlang: c
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: 149d3e3515c8bbc10798ad26e58dcefbe830f9bc
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54830729"
---
# <a name="quickstart-communicate-to-a-device-application-in-c-via-iot-hub-device-streams-preview"></a>快速入门：通过 IoT 中心设备流在 C 中与设备应用程序通信（预览）

[!INCLUDE [iot-hub-quickstarts-3-selector](../../includes/iot-hub-quickstarts-3-selector.md)]

服务和设备应用程序可以使用 [IoT 中心设备流](./iot-hub-device-streams-overview.md)以安全且防火墙友好的方式进行通信。 在公共预览期，C SDK 仅支持设备端的设备流。 因此，本快速入门只介绍如何运行设备端应用程序。 应该运行 [C# 快速入门](./quickstart-device-streams-echo-csharp.md)或 [Node.js 快速入门](./quickstart-device-streams-echo-nodejs.md)指南中随附的服务端应用程序。

本快速入门中的设备端 C 应用程序具有以下功能：

* 建立发往 IoT 设备的设备流。

* 接收从服务端发送的数据并将其回显。

代码将演示设备流的启动过程，以及如何用其来发送和接收数据。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* 安装 [Visual Studio 2017](https://www.visualstudio.com/vs/) 并启用[“使用 C++ 的桌面开发”](https://www.visualstudio.com/vs/support/selecting-workloads-visual-studio-2017/)工作负荷。
* [安装最新版本的 Git](https://git-scm.com/download/)。

## <a name="prepare-the-development-environment"></a>准备开发环境

针对本快速入门，你将使用[适用于 C 的 Azure IoT 设备 SDK](iot-hub-device-sdk-c-intro.md)。准备一个用于从 GitHub 克隆和生成 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 的开发环境。 GitHub 上的 SDK 包括在本快速入门中使用的示例代码。 


1. 下载 3.11.4 版的 [CMake 生成系统](https://cmake.org/download/)。 使用相应的加密哈希值验证下载的二进制文件。 以下示例使用了 Windows PowerShell 来验证 x64 MSI 分发版本 3.11.4 的加密哈希：

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

2. 打开命令提示符或 Git Bash shell。 执行以下命令克隆 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库：
    
    ```
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
    ```

    此存储库的大小目前大约为 220 MB。

3. 在 git 存储库的根目录中创建 `cmake` 子目录，并导航到该文件夹。 

    ```
    cd azure-iot-sdk-c
    git checkout public-preview
    mkdir cmake
    cd cmake
    ```

4. 运行以下命令，生成特定于你的开发客户端平台的 SDK 版本。 在 Windows 中，模拟设备的 Visual Studio 解决方案将在 `cmake` 目录中生成。 

```
    # In Linux
    cmake -Denable_streaming=ON ..
    make -j
```

在 Windows 中，请在 Visual Studio 2015 或 2017 的开发人员命令提示符下运行以下命令：

```
    # In Windows
    # For VS2015
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2015"
    
    # Or for VS2017
    cmake -Denable_streaming=ON .. -G "Visual Studio 15 2017

    # Then build the project
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


## <a name="communicate-between-device-and-service-via-device-streams"></a>通过设备流在设备和服务之间通信

### <a name="run-the-device-side-application"></a>运行设备端应用程序

若要运行设备端应用程序，需要执行以下步骤：
- 根据这篇[有关设备流的文章](https://github.com/Azure/azure-iot-sdk-c-tcpstreaming/blob/master/iothub_client/readme.md#compiling-the-device-sdk-for-c)中的说明设置开发环境。

- 通过编辑源文件 `iothub_client/samples/iothub_client_c2d_streaming_sample/iothub_client_c2d_streaming_sample.c` 来提供设备凭据，并提供设备连接字符串。
```C
  /* Paste in the your iothub connection string  */
  static const char* connectionString = "[device connection string]";
```

- 按如下所示编译代码：

```
  # In Linux
  # Go to the sample's folder cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  make -j


  # In Windows
  # Go to the cmake folder at the root of repo
  cmake --build . -- /m /p:Configuration=Release
```

- 运行编译的程序：

```
  # In Linux
  # Go to sample's folder
  cmake/iothub_client/samples/iothub_client_c2d_streaming_sample
  ./iothub_client_c2d_streaming_sample


  # In Windows
  # Go to sample's release folder
  cmake\iothub_client\samples\iothub_client_c2d_streaming_sample\Release
  iothub_client_c2d_streaming_sample.exe
```

### <a name="run-the-service-side-application"></a>运行服务端应用程序

如前所述，IoT 中心 C SDK 仅支持设备端的设备流。 对于服务端应用程序，请使用 [C# 快速入门](./quickstart-device-streams-echo-csharp.md)或 [Node.js 快速入门](./quickstart-device-streams-echo-nodejs.md)指南中随附的服务程序。


## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources-device-streams.md)]

## <a name="next-steps"></a>后续步骤

本快速入门设置了 IoT 中心、注册了设备、使用 C 应用程序发送了模拟遥测数据到中心，并使用 Azure Cloud Shell 读取了中心的遥测数据。

请使用以下链接详细了解设备流：

> [!div class="nextstepaction"]
> [设备流概述](./iot-hub-device-streams-overview.md)