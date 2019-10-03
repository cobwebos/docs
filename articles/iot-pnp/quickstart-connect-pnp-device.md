---
title: 将 IoT 即插即用预览版示例设备代码连接到 IoT 中心 | Microsoft Docs
description: 生成并运行可连接到 IoT 中心的 IoT 即插即用预览版示例设备代码。 使用 Azure IoT 资源管理器工具查看由设备发送到中心的信息。
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 4802c120f4722796fcad4ea99f25adad368451fd
ms.sourcegitcommit: b8578b14c8629c4e4dea4c2e90164e42393e8064
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/09/2019
ms.locfileid: "70806532"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>快速入门：将示例 IoT 即插即用预览版设备应用程序连接到 IoT 中心

本快速入门介绍如何生成 IoT 即插即用设备应用程序，将其连接到 IoT 中心，并使用 Azure IoT 资源管理器工具来查看它发送到中心的信息。 该示例应用程序以 C 编写，包含在适用于 C 的 Azure IoT 设备 SDK 中。解决方案开发人员可以使用 Azure IoT 资源管理器工具来了解 IoT 即插即用设备的功能，而无需查看任何设备代码。

## <a name="prerequisites"></a>先决条件

若要完成本快速入门，需在本地计算机上安装以下软件：

* [Visual Studio（社区版、专业版或企业版）](https://visualstudio.microsoft.com/downloads/)- 安装 Visual Studio 时，请确保包括“NuGet 包管理器”组件和“使用 C++ 的桌面开发”工作负荷。  
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/)。

### <a name="install-the-azure-iot-explorer"></a>安装 Azure IoT 资源管理器

从[最新版本](https://github.com/Azure/azure-iot-explorer/releases)页下载并安装 Azure IoT 资源管理器工具。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>准备 IoT 中心

Azure 订阅中还需要有一个 Azure IoT 中心才能完成本快速入门。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

> [!NOTE]
> 在公共预览版期间，IoT 即插即用功能仅适用于在美国中部、欧洲北部和日本东部区域中创建的 IoT 中心    。

添加适用于 Azure CLI 的 Microsoft Azure IoT 扩展：

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

运行以下命令，在 IoT 中心创建设备标识。 将 **YourIoTHubName** 和 **YourDevice** 占位符替换为实际名称。 如果你没有 IoT 中心，请遵照[此处的说明创建一个](../iot-hub/iot-hub-create-using-cli.md)：

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

运行以下命令，获取刚注册设备的设备连接字符串： 

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

运行以下命令，获取中心的 IoT 中心连接字符串： 

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>准备开发环境

### <a name="get-azure-iot-device-sdk-for-c"></a>获取适用于 C 的 Azure IoT 设备 SDK

在本快速入门中，你将准备一个用于克隆和生成 Azure IoT C 设备 SDK 的开发环境。

打开命令提示符。 执行以下命令克隆 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库：

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

应该预料到此操作需要几分钟才能完成。

## <a name="build-the-code"></a>生成代码

生成的应用程序将模拟连接到 IoT 中心的设备。 应用程序将发送遥测数据和属性，并接收命令。

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

通过传递 IoT 中心设备连接字符串作为参数来运行应用程序。

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

设备应用程序将开始向 IoT 中心发送数据。

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>使用 Azure IoT 资源管理器验证代码

1. 打开 Azure IoT 资源管理器，此时会看到“应用配置”页。 

1. 输入 IoT 中心连接字符串，然后单击“连接”。 

1. 连接后，将看到设备概述页。

1. 若要添加公司存储库，请依次选择“设置”、“+ 新建”、“在连接的设备上”。   

1. 在设备概述页上，找到前面创建的设备标识，然后选择该标识以查看更多详细信息。

1. 展开 ID 为 **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** 的接口，以查看 IoT 即插即用基元 - 属性、命令和遥测。

1. 选择“遥测”页查看设备正在发送的遥测数据。 

1. 选择“属性(不可写)”页查看设备报告的不可写属性。 

1. 选择“属性(可写)”页查看可以更新的可写属性。 

1. 展开属性**名称**，更新为新名称，然后选择“更新可写属性”。  

1. 若要查看“报告的属性”列中显示的新名称，请单击页面顶部的“刷新”按钮。  

1. 选择“命令”页查看设备支持的所有命令。 

1. 展开 **blink** 命令并设置新的闪烁时间间隔。 选择“发送命令”以调用设备上的命令。 

1. 转到模拟设备，验证该命令是否按预期方式执行。

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何将 IoT 即插即用设备连接到 IoT 中心。 若要详细了解如何生成可与 IoT 即插即用设备交互的解决方案，请参阅：

> [!div class="nextstepaction"]
> [操作说明：连接到 IoT 即插即用预览版设备并与之交互](howto-develop-solution.md)
