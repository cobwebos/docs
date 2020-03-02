---
title: 教程 - 将 IoT 即插即用（预览版）设备连接到 Azure IoT Central
description: 本教程介绍如何使用设备功能模型生成设备代码。 然后运行设备代码，查看设备是否可连接到 IoT Central 应用程序，并使用自动生成的视图。
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 84db3996b49a95d1ef6a9f1e80299605cc31d669
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77602594"
---
# <a name="tutorial-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-and-connect-it-to-your-iot-central-application"></a>教程：使用设备功能模型创建 IoT 即插即用（预览版）设备并将其连接到 IoT Central 应用程序

_设备功能模型_ (DCM) 描述 [IoT 即插即用（预览版）](../../iot-pnp/overview-iot-plug-and-play.md)设备的功能。 当设备首次进行连接时，IoT Central 可以使用 DCM 为设备创建设备模板和可视化效果。

对 [IoT 即插即用](../../iot-pnp/overview-iot-plug-and-play.md)的支持处于预览状态，并且仅在选定区域受支持。

> [!NOTE]
> 请与技术支持联系，以获取创建支持 IoT 即插即用设备的预览应用程序的访问权限。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 Visual Studio Code 中使用 DCM 创建 IoT 即插即用（预览版）设备。
> * 在 Windows 中运行设备代码，并查看它是否可连接到 IoT Central 应用程序。
> * 查看设备发送的模拟遥测数据。

## <a name="prerequisites"></a>必备条件

完成[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)快速入门，使用“自定义应用”>“自定义应用程序”模板创建 IoT Central 应用程序。 

若要完成本教程，需在本地计算机上安装以下软件：

* [适用于 Visual Studio 的生成工具](https://visualstudio.microsoft.com/thank-you-downloading-visual-studio/?sku=BuildTools&rel=16)，其中包含 C++ 生成工具和 Nuget 包管理器组件工作负荷   。 安装了相同工作负荷的 [Visual Studio（Community、Professional 或 Enterprise）](https://visualstudio.microsoft.com/downloads/)2019、2017 或 2015。
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

在本教程中，你将使用 [Vcpkg](https://github.com/microsoft/vcpkg) 库管理器在开发环境中安装 Azure IoT C 设备 SDK。

1. 打开命令提示符。 执行以下命令以安装 Vcpkg：

    ```cmd
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    .\bootstrap-vcpkg.bat
    ```

    然后，若要挂接用户范围的[集成](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)，运行以下命令。 首次运行此命令需要管理权限：

    ```cmd
    .\vcpkg.exe integrate install
    ```

1. 安装 Azure IoT C 设备 SDK Vcpkg：

    ```cmd
    .\vcpkg.exe install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

## <a name="generate-device-key"></a>生成设备密钥

若要将设备连接到 IoT Central 应用程序，需要生成一个设备密钥。 若要生成设备密钥：

1. 登录到使用[创建 Azure IoT Central 应用程序](./quick-deploy-iot-central.md)快速入门中的“自定义应用程序”模板创建的 IoT Central 应用程序  。

1. 转到“管理”页并选择“设备连接”。  

1. 记下选择“查看密钥”时显示的“ID 范围”和“主键”    。 本教程后面会用到这些值。

    ![设备连接](./media/tutorial-connect-pnp-device/device-connection.png)

1. 打开命令提示符，并运行以下命令生成设备密钥：

    ```cmd/sh
    dps-keygen -di:mxchip-001 -mk:{Primary Key from previous step}
    ```

    记下生成的设备密钥，在本教程稍后的步骤中将要用到它  。

## <a name="download-your-model"></a>下载模型

在本教程中，将对 MxChip IoT DevKit 设备使用公共 DCM。 不需要使用实际的 DevKit 设备来运行代码，在本教程中，请将代码编译为在 Windows 上运行。

1. 创建一个名为 `central_app` 的文件夹，然后在 VS Code 中打开它。

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

1. 选择“通过 DPS (设备预配服务)对称密钥”作为连接方法。 

1. 选择“Windows 上的 CMake 项目”作为项目类型  。 请不要选择“MXChip IoT DevKit 项目”，只有在使用实际的 DevKit 设备时，此选项才可用。 

1. 选择“通过 Vcpkg”作为包括 SDK 的方式  。

1. VS Code 将打开一个新窗口，其中包含 `devkit_device` 文件夹中生成的设备代码存根文件。

![生成的设备代码](./media/tutorial-connect-pnp-device/generated-code.png)

## <a name="build-the-code"></a>生成代码

使用设备 SDK 来生成设备代码存根。 生成的应用程序将模拟 **MXChip IoT DevKit** 设备，并连接到 IoT Central 应用程序。 该应用程序将发送遥测数据和属性，并接收命令。

1. 在命令提示符下，于 `devkit_device` 文件夹中创建一个 `cmake` 子目录，并导航到该文件夹：

    ```cmd
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令以生成代码存根。 将 `<directory of your Vcpkg repo>` 占位符替换为 Vcpkg 存储库副本的路径  ：

    ```cmd
    cmake .. -G "Visual Studio 16 2019" -A Win32 -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"

    cmake --build . -- /p:Configuration=Release
    ```

    如果使用 Visual Studio 2017 或 2015，则需要根据所使用的生成工具指定 CMake 生成器：

    ```cmd
    # Either
    cmake .. -G "Visual Studio 15 2017" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    # or
    cmake .. -G "Visual Studio 14 2015" -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="<directory of your Vcpkg repo>\scripts\buildsystems\vcpkg.cmake"
    ```

1. 生成成功完成后，在同一命令提示符下运行该应用程序。 将 `<scopeid>` 和 `<primarykey>` 替换为之前记下的值：

    ```cmd
    .\Release\devkit_device.exe mxchip-001 <scopeid> <primarykey>
    ```

1. 设备应用程序将开始向 IoT 中心发送数据。 有时，在首次运行上一命令时会看到错误 `Error registering device for DPS`。 如果看到此错误，请重试命令。

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

本教程介绍了如何连接通过公共模型存储库中的 DCM 生成的 IoT 即插即用（预览版）设备。

若要详细了解 DCM 以及如何创建自己的模型，请继续学习操作指南：

> [!div class="nextstepaction"]
> [定义新的 IoT 设备类型](./howto-set-up-template.md)
