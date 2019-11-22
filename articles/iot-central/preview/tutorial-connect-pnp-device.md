---
title: 将 IoT 即插即用预览版设备连接到 Azure IoT Central | Microsoft Docs
description: 使用设备功能模型生成设备代码。 然后运行设备代码，查看设备是否可连接到 IoT Central 应用程序，并使用自动生成的视图。
author: dominicbetts
ms.author: dobett
ms.date: 08/08/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 5d5815467444afeb5f08380eea6868336044d237
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73892323"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-device-and-connect-it-to-your-iot-central-application"></a>教程：使用设备功能模型创建 IoT 即插即用设备并将其连接到 IoT Central 应用程序

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

_设备功能模型_ (DCM) 描述 [IoT 即插即用](../../iot-pnp/overview-iot-plug-and-play.md)设备的功能。 当设备首次进行连接时，IoT Central 可以使用 DCM 为设备创建设备模板和可视化效果。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 Visual Studio Code 中使用 DCM 创建 IoT 即插即用设备。
> * 在 Windows 中运行设备代码，并查看它是否可连接到 IoT Central 应用程序。
> * 查看设备发送的模拟遥测数据。

## <a name="prerequisites"></a>先决条件

完成[创建 Azure IoT Central 应用程序（预览功能）](./quick-deploy-iot-central.md)快速入门，使用“自定义应用”>“预览应用程序”模板创建 IoT Central 应用程序  。

若要完成本教程，需在本地计算机上安装以下软件：

* [Visual Studio（社区版、专业版或企业版）](https://visualstudio.microsoft.com/downloads/)- 安装 Visual Studio 时，请确保包括“NuGet 包管理器”组件和“使用 C++ 的桌面开发”工作负荷。  
* [Git](https://git-scm.com/download/)。
* [CMake](https://cmake.org/download/) - 安装 **CMake** 时，请选择“将 CMake 添加到系统 PATH”选项。 
* [Visual Studio Code](https://code.visualstudio.com/)。
* [Node.js](https://nodejs.org/)
* `dps-keygen` 实用工具：

    ```cmd/sh
    npm i -g dps-keygen
    ```

### <a name="install-azure-iot-tools"></a>安装 Azure IoT Tools

使用以下步骤在 VS Code 中安装 Azure IoT Tools 扩展包：

1. 在 VS Code 中选择“扩展”选项卡。 
1. 搜索“Azure IoT Tools”  。
1. 选择“安装”  。

## <a name="prepare-the-development-environment"></a>准备开发环境

### <a name="get-azure-iot-device-sdk-for-c"></a>获取适用于 C 的 Azure IoT 设备 SDK

准备一个用于生成 Azure IoT C 设备 SDK 的开发环境。

1. 打开命令提示符。 执行以下命令克隆 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库：

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    应该预料到此操作需要几分钟才能完成。

1. 在存储库本地副本的根目录中创建一个 `central_app` 文件夹。 使用此文件夹来存储设备模型文件和设备代码存根。

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir central_app
    ```

## <a name="generate-device-key"></a>生成设备密钥

若要将设备连接到 IoT Central 应用程序，需要生成一个设备密钥。 若要生成设备密钥：

1. 使用[创建 Azure IoT Central 应用程序（预览功能）](./quick-deploy-iot-central.md)快速入门中的“自定义应用”>“预览应用程序”模板登录所创建的 IoT Central 应用程序  。

1. 转到“管理”页并选择“设备连接”。  

1. 记下选择“查看密钥”时显示的“ID 范围”和“主键”    。 本教程后面会用到这些值。

    ![设备连接](./media/tutorial-connect-pnp-device/device-connection.png)

1. 打开命令提示符，并运行以下命令生成设备密钥：

    ```cmd/sh
    dps-keygen  -di:mxchip-01 -mk:{Primary Key from previous step}
    ```

    请记下生成的设备密钥，本教程稍后的步骤中将要用到它  。

## <a name="download-your-model"></a>下载模型

本教程对 MxChip IoT DevKit 设备使用公共 DCM。 不需要使用实际的 DevKit 设备来运行代码，在本教程中，请将代码编译为在 Windows 上运行。

1. 使用 VS Code 打开 `azure-iot-sdk-c\central_app` 文件夹。

1. 按 **Ctrl+Shift+P** 打开命令面板，输入“IoT 即插即用”，然后选择“打开模型存储库”。   选择“公共存储库”。  VS Code 将显示公共模型存储库中的 DCM 列表。

1. 选择 ID 为 `urn:mxchip:mxchip_iot_devkit:1` 的“MXChip IoT DevKit”DCM。  然后选择“下载”。  现在，`central_app` 文件夹中已有一个 DCM 副本。

![模型存储库和 DCM](./media/tutorial-connect-pnp-device/public-repository.png)

> [!NOTE]
> 若要使用 IoT Central，必须在同一文件中以内联方式定义设备功能模型的所有接口。

## <a name="generate-the-c-code-stub"></a>生成 C 代码存根

现已获得一个 **MXChip IoT DevKit** DCM 及其关联的接口，接下来可以生成用于实现该模型的设备代码。 若要在 VS Code 中生成 C 代码存根：

1. 打开包含 DCM 文件的文件夹后，按 **Ctrl+Shift+P** 打开命令面板，输入“IoT 即插即用”，然后选择“生成设备代码存根”。  

    > [!NOTE]
    > 首次使用 IoT 即插即用代码生成器实用工具时，需要花费几秒钟时间来下载组件。

1. 选择刚刚下载的 **MXChip IoT DevKit** DCM 文件。

1. 输入项目名称 **devkit_device**。

1. 选择“ANSI C”作为语言。 

1. 选择“CMake 项目”作为项目类型。  请不要选择“MXChip IoT DevKit 项目”，只有在使用实际的 DevKit 设备时，此选项才可用。 

1. 选择“通过 DPS (设备预配服务)对称密钥”作为连接方法。 

1. VS Code 将打开一个新窗口，其中包含 `devkit_device` 文件夹中生成的设备代码存根文件。

![生成的设备代码](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>生成代码

使用设备 SDK 来生成设备代码存根。 生成的应用程序将模拟 **MXChip IoT DevKit** 设备，并连接到 IoT Central 应用程序。 该应用程序将发送遥测数据和属性，并接收命令。

1. 在 VS Code 中，打开 `azure-iot-sdk-c` 文件夹中的 `CMakeLists.txt` 文件。 请务必打开 `azure-iot-sdk-c` 文件夹（而不是 `devkit_device` 文件夹）中的 `CMakeLists.txt` 文件。

1. 在 `CMakeLists.txt` 文件的底部添加以下行，以在编译时包含设备代码存根文件夹：

    ```txt
    add_subdirectory(central_app/devkit_device)
    ```

1. 在 `azure-iot-sdk-c` 文件夹中创建一个 `cmake` 文件夹，并在命令提示符下导航到该文件夹：

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令以生成设备 SDK 和代码存根：

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

1. 生成成功完成后，在同一命令提示符下运行该应用程序。 将 `scopeid` 和 `primarykey` 替换为之前记下的值：

    ```cmd\sh
    .\central_app\devkit_device\Release\devkit_device.exe scopeid primarykey mxchip-001
    ```

1. 设备应用程序将开始向 IoT Central 应用程序发送数据。

## <a name="view-the-device"></a>查看设备

将设备代码连接到 IoT Central 后，可以查看其发送的属性和遥测数据：

1. 在 IoT Central 应用程序中，转到“设备”页并选择“mxchip-01”设备。   连接设备代码后，会自动添加此设备：

    ![概述页](./media/tutorial-connect-pnp-device/overview-page.png)

    几分钟后，此页将显示设备正在发送的遥测数据的图表。

1. 选择“关于”页查看设备发送的属性值。 

1. 选择“命令”页调用设备上的命令。  可以在运行设备代码的命令提示符下查看设备是否做出响应。

1. 转到“设备模板”页，查看 IoT Central 基于公共存储库中的 DCM 创建的模板： 

    ![“设备模板”页](./media/tutorial-connect-pnp-device/device-template.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何连接基于公共模型存储库中的 DCM 生成的 IoT 即插即用设备。

若要详细了解 DCM 以及如何创建自己的模型，请继续学习操作指南：

> [!div class="nextstepaction"]
> [创建设备组](./tutorial-use-device-groups.md)
