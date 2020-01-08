---
title: 将 IoT 即插即用预览示例设备代码连接到 IoT 中心 (Windows) | Microsoft Docs
description: 在连接到 IoT 中心的 Windows 上生成并运行 IoT 即插即用预览示例设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/26/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1c8b6a5d133d8838c2c7a23f8881092affa424dc
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531178"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-windows-to-iot-hub-c-windows"></a>快速入门：将 Windows 上运行的示例 IoT 即插即用预览设备应用程序连接到 IoT 中心 (C Windows)

[!INCLUDE [iot-pnp-quickstarts-2-selector.md](../../includes/iot-pnp-quickstarts-2-selector.md)]

本快速入门介绍如何生成 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送到中心的信息。 该示例应用程序以 C 编写，包含在 Azure IoT 中心设备 C SDK 中。 解决方案开发人员可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>必备条件

若要完成本快速入门，需在本地计算机上安装以下软件：

* [Visual Studio（社区版、专业版或企业版）](https://visualstudio.microsoft.com/downloads/)- 安装 Visual Studio 时，请确保包括“NuGet 包管理器”组件和“使用 C++ 的桌面开发”工作负荷。  
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="install-the-azure-iot-explorer"></a>安装 Azure IoT 资源管理器

从 Azure IoT 资源管理器的[存储库](https://github.com/Azure/azure-iot-explorer/releases)页面，选择“资产”下的 .msi 文件以查找最近更新，下载并安装该工具的最新版本  。

[!INCLUDE [iot-pnp-prepare-iot-hub.md](../../includes/iot-pnp-prepare-iot-hub.md)]

运行以下命令，获取中心的 _IoT 中心连接字符串_（记录以供稍后使用）：

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```

## <a name="prepare-the-development-environment"></a>准备开发环境

在本快速入门中，你将准备一个用于克隆和生成 Azure IoT 中心设备 C SDK 的开发环境。

在所选目录中打开命令提示符。 执行以下命令将 [Azure IoT C SDK 和库](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库克隆到此位置：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

应该预料到此操作需要几分钟才能完成。

## <a name="build-the-code"></a>生成代码

使用设备 SDK 生成包含的示例代码。 生成的应用程序将模拟连接到 IoT 中心的设备。 应用程序将发送遥测数据和属性，并接收命令。

1. 在设备 SDK 根文件夹中创建一个 `cmake` 子目录，并导航到该文件夹：

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令以生成设备 SDK 和代码存根：

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > 如果 cmake 找不到 C++ 编译器，则在运行以上命令时会出现生成错误。 如果出现这种情况，请尝试在 [Visual Studio 命令提示符](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs)下运行此命令。

## <a name="run-the-device-sample"></a>运行设备示例

在 SDK 中运行示例应用程序，以模拟将遥测发送到 IoT 中心的 IoT 即插即用设备。 若要运行示例应用程序，请使用以下命令并将设备连接字符串作为参数传递  。

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "<YourDeviceConnectionString>"
```

设备现在可以接收命令和属性更新，并已开始向中心发送遥测数据。 在执行后续步骤时，保持示例处于运行状态。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

[!INCLUDE [iot-pnp-iot-explorer-1.md](../../includes/iot-pnp-iot-explorer-1.md)]

4. 若要确保此工具可从设备中读取接口模型定义，请选择“设置”  。 在“设置”菜单中，“在已连接设备上”可能已显示在即插即用配置中；如果未显示，请依次选择“+ 添加模块定义源”和“在连接设备上”以添加此项    。

1. 回到“设备”概述页上，找到前面创建的设备标识  。 如果设备应用程序仍在命令提示符中运行，请检查设备 Azure IoT 资源管理器中的“连接状态”是否为“已连接”（如果不是，请点击“刷新”直至状态为“已连接”）    。 选择该设备可查看更多详细信息。

1. 展开 ID 为 urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1 的接口，以显示接口和 IoT 即插即用基元 - 属性、命令和遥测  。

[!INCLUDE [iot-pnp-iot-explorer-2.md](../../includes/iot-pnp-iot-explorer-2.md)]

[!INCLUDE [iot-pnp-clean-resources.md](../../includes/iot-pnp-clean-resources.md)]

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 中心。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到 IoT 即插即用预览版设备并与之交互](howto-develop-solution.md)
