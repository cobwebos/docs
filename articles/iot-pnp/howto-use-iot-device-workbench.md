---
title: 使用 Azure IoT 设备工作台构建 IoT 即插即用预览版设备 |Microsoft Docs
description: 在 Visual Studio Code 中使用 Azure IoT 设备工作台扩展，加速创作 IoT 即插即用设备型号和实现设备代码。
author: liydu
ms.author: liydu
ms.date: 07/25/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b9f091caaaf85cd2b999db7781b14a497474f564
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881258"
---
# <a name="use-azure-iot-device-workbench-extension-in-visual-studio-code"></a>在 Visual Studio Code 中使用 Azure IoT 设备工作台扩展

Azure IoT 设备工作台 Visual Studio Code 扩展提供了一个集成环境，用于创作设备功能模型（DCM）和接口、发布到模型存储库，以及生成用于实现设备应用程序的框架 C 代码。

本文介绍以下操作：

- 生成设备代码和应用程序项目。
- 使用设备项目中生成的代码。
- 通过重新生成主干代码进行迭代。

若要了解有关使用设备工作台扩展来开发 IoT 设备的详细信息[https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)，请参阅。

## <a name="prerequisites"></a>先决条件

安装 [Visual Studio Code](https://code.visualstudio.com/)。

使用以下步骤在 VS Code 中安装扩展。

1. 在 VS Code 中，选择 "**扩展**" 选项卡。
1. 从 marketplace 搜索并安装**Azure IoT 设备工作台**。

## <a name="generate-device-code-and-project"></a>生成设备代码和项目

在 VS Code 中，使用**Ctrl + Shift + P**打开命令面板，输入**IoT 即插即用**，然后选择 "**生成设备代码存根**" 以配置主干代码和项目类型。 以下列表详细介绍了每个步骤：

- **要用于生成代码的 DCM 文件**。 若要生成主干设备代码，请打开包含其实现的 DCM 和接口文件的文件夹。 如果代码生成器找不到 DCM 所需的接口，它会根据需要从模型存储库中下载它。

- **设备代码语言**。 目前，代码生成器仅支持 ANSI C。

- **项目名称**。 项目名称用作生成的代码和其他项目文件的文件夹名称。 默认情况下，该文件夹位于 DCM 文件旁边。 若要避免在每次更新 DCM 和重新生成设备代码时都手动复制生成的代码文件夹，请将 DCM 文件保存在与项目文件夹相同的文件夹中。

- **项目类型**。 代码生成器还会生成项目文件，以便您可以将代码集成到您自己的项目或设备 SDK 项目中。 目前支持的项目类型为：

    - **CMake 项目**：对于使用[CMake](https://cmake.org/)作为生成系统的设备项目。 此选项在与`CMakeLists.txt` C 代码相同的文件夹中生成文件。
    - **MXChip Iot DevKit project**：用于在[MXChip IoT DevKit](https://aka.ms/iot-devkit)设备上运行的设备项目。 此选项生成可[在 VS Code](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)或 Arduino IDE 中使用的 Arduino 项目，以在 IoT DevKit 设备上生成和运行。

- 用于**连接到 Azure IoT 的方法**。 生成的文件还包含用于将设备配置为连接到 Azure IoT 中心的代码。 你可以选择直接连接到[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)或使用[设备预配服务](https://docs.microsoft.com/azure/iot-dps)。

    - **通过 IoT 中心设备连接字符串**：指定设备应用程序的设备连接字符串，以便直接连接到 IoT 中心。
    - **通过 dps 对称密钥**：指定使用 DPS 连接到 IoT 中心或 IoT Central 所需的设备应用程序的**作用域 ID**、**注册 ID**和**SaS 密钥**。

代码生成器尝试使用位于本地文件夹中的 DCM 和接口文件。 如果接口文件不在本地文件夹中，则代码生成器会在公共模型存储库或公司模型存储库中查找它们。 [公共接口文件](./concepts-common-interfaces.md)存储在公共模型存储库中。

代码生成完成后，扩展将使用代码打开一个新的 VS Code 窗口。 如果打开生成的文件（如**main. c**），则可能会发现 IntelliSense 会报告它无法打开 c SDK 源文件。 若要启用正确的 IntelliSense 和代码导航，请使用以下步骤包括 C SDK 源：

1. 在 VS Code 中，使用**Ctrl + Shift + P**打开命令面板，键入并选择**C/：C++编辑配置（JSON）** 以打开**c_cpp_properties**文件。

1. 在`includePath`部分中添加设备 SDK 的路径：

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. 保存该文件。

## <a name="use-the-generated-code"></a>使用生成的代码

以下说明介绍了如何在不同的开发计算机平台上使用自己的设备项目中生成的代码。 说明假定你将 IoT 中心设备连接字符串与生成的代码一起使用：

### <a name="linux"></a>Linux

若要在 Linux 环境（如 Ubuntu 或 Debian）中使用 CMake，将设备代码与设备 C SDK 一起生成：

1. 打开终端应用程序。

1. `cmake`使用命令`apt-get`安装 GCC、 **Git**、和所有依赖项：

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    验证`cmake`的版本是否高于**2.8.12** ，并且**GCC**的版本高于**4.4.7**。

    ```sh
    cmake --version
    gcc --version
    ```

1. 克隆[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)存储库：

    ```sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    应该预料到此操作需要几分钟才能完成。

1. 将包含生成的代码的文件夹复制到设备 SDK 根文件夹中。

1. 在 VS Code 中，打开`CMakeLists.txt`设备 SDK 根文件夹中的文件。

1. 在`CMakeLists.txt`文件末尾添加以下行，以在编译 SDK 时包含设备代码存根文件夹：

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. 在设备 SDK 根`cmake`文件夹中创建一个名为的文件夹，然后导航到该文件夹。

    ```sh
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令，以使用 CMake 生成设备 SDK 和生成的代码存根：

    ```cmd\sh
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON ..
    cmake --build .
    ```

1. 生成成功后，请运行将 IoT 中心设备连接字符串指定为参数的应用程序。

    ```cmd\sh
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

若要在 Windows 上使用 CMake 和 Visual Studio C/C++编译器通过命令行使用设备 C SDK 生成设备代码，请参阅[IoT 即插即用快速入门](./quickstart-create-pnp-device.md)。 以下步骤演示如何在 Visual Studio 中将设备代码和设备 C SDK 一起生成为 CMake 项目。

1. 安装[Visual Studio 2019 （社区版、专业版或企业版）](https://visualstudio.microsoft.com/downloads/) -确保包括**NuGet 包管理器**组件和具有**C++** 工作负载的桌面开发。

1. 打开 Visual Studio，在 "**开始**" 页上，选择 "**克隆" 或 "签出代码**"：

1. 在 "**存储库位置**" 中，克隆[AZURE IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)存储库：

    ```txt
    https://github.com/Azure/azure-iot-sdk-c
    ```

    你应预计此操作需要几分钟才能完成，你可以在 "**团队资源管理器**" 窗格中查看进度。

1. 在**团队资源管理器**中打开 " **azure iot-c**存储库"，选择 "**分支**"，搜索 "**公共-预览**" 分支，并将其签出。

    ![公开预览版](media/howto-use-iot-device-workbench/vs-public-preview.png)

1. 将包含生成的代码的文件夹复制到设备 SDK 根文件夹中。

1. 在 VS `azure-iot-sdk-c`中打开该文件夹。

1. 打开设备 SDK 根文件夹中的文件。`CMakeLists.txt`

1. 在`CMakeLists.txt`文件末尾添加以下行，以在编译 SDK 时包含设备代码存根文件夹：

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. 在**常规**工具栏中，查找 "**配置**" 下拉列表。 选择 "**管理配置**"，为你的项目添加 CMake 设置。

    ![管理配置](media/howto-use-iot-device-workbench/vs-manage-config.png)

1. 在**CMake 设置**中，添加新配置，并选择 " **x64-发布**为目标"。

1. 在**CMake 命令参数**中添加以下行：

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. 保存该文件。

1. 在**解决方案资源管理器**中，右键单击根文件夹中`CMakeLists.txt`的，然后从上下文菜单中选择 "**生成**" 以生成设备 SDK 和生成的代码存根。

1. 生成成功后，在命令提示符处运行应用程序，并将 IoT 中心设备连接字符串指定为参数。

    ```cmd
    cd %USERPROFILE%\CMakeBuilds\{workspaceHash}\build\x64-Release\{generated_code_folder_name}\
    {generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> 若要了解有关在 Visual Studio 中使用 CMake 的详细信息，请参阅[生成 CMake 项目](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects)。

### <a name="macos"></a>macOS

以下步骤演示如何使用 CMake 在 macOS 上使用设备 C SDK 生成设备代码：

1. 打开终端应用程序。

1. 使用[Homebrew](https://homebrew.sh)安装所有依赖项：

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. 验证[CMake](https://cmake.org/)的版本是否至少为**2.8.12**：

    ```bash
    cmake --version
    ```

1. [修补](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os)到可用的最新版本。

1. 克隆[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)存储库：

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    应该预料到此操作需要几分钟才能完成。

1. 将包含生成的代码的文件夹复制到设备 SDK 根文件夹中。

1. 在 VS Code 中，打开`CMakeLists.txt`设备 SDK 根文件夹中的文件。

1. 在`CMakeLists.txt`文件末尾添加以下行，以在编译 SDK 时包含设备代码存根文件夹：

    ```txt
    add_subdirectory({generated_code_folder_name})
    ```

1. 在设备 SDK 根`cmake`文件夹中创建一个名为的文件夹，然后导航到该文件夹。

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令，以使用 CMake 生成设备 SDK 和生成的代码存根：

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. 生成成功后，请运行将 IoT 中心设备连接字符串指定为参数的应用程序。

    ```bash
    cd azure-iot-sdk-c/cmake/{generated_code_folder_name}/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>通过重新生成主干代码进行迭代

如果更新 DCM 或接口文件，代码生成器可以重新生成代码。 假设你已从 DCM 文件生成了设备代码，请重新生成代码：

1. 打开包含 DCM 文件的文件夹后，按 **Ctrl+Shift+P** 打开命令面板，输入“IoT 即插即用”，然后选择“生成设备代码存根”。

1. 选择已更新的 DCM 文件。

1. 选择 "**为 {项目名称} 重新生成代码**"。

1. 代码生成器使用之前配置的设置并重新生成代码。 但是，它不会覆盖可能包含用户代码的文件，例如`main.c`和`{project_name}_impl.c`。

> [!NOTE]
> 如果在接口文件中更新 URN id，则会将其视为新接口。 当你重新生成代码时，代码生成器将为接口生成代码，但不会覆盖`{project_name}_impl.c`文件中的原始代码。

## <a name="problems-and-feedback"></a>问题和反馈

Azure IoT 设备工作台扩展是 GitHub 上的开源项目。 对于任何问题和功能请求，你可以[在 GitHub 上创建问题](https://github.com/microsoft/vscode-iot-workbench/issues)。

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了如何使用 Azure IoT 设备工作台创作 DCM 和接口文件。 还了解了如何生成用于实现设备应用程序的框架 C 代码。 建议的下一步是了解如何[安装和使用 Azure IoT 浏览器](./howto-install-iot-explorer.md)工具。
