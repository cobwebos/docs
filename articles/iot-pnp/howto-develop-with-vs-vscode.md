---
title: 使用可视化工作室和视觉工作室代码构建 IoT 即插即用预览设备 |微软文档
description: 使用可视化工作室和可视化工作室代码加快创作 IoT 即插即用设备模型并实现设备代码。
author: liydu
ms.author: liydu
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 91e7b1c0be9a38c3d79440f07d944d182980dc10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159228"
---
# <a name="use-visual-studio-and-visual-studio-code-to-build-iot-plug-and-play-devices"></a>使用可视化工作室和可视化工作室代码构建 IoT 即插即用设备

适用于可视化工作室代码的 Azure IoT 工具为创作设备功能模型 （DCM） 和接口、发布到模型存储库以及生成骨架 C 代码以实现设备应用程序提供了集成环境。

本文介绍如何：

- 生成设备代码和应用程序项目。
- 在设备项目中使用生成的代码。
- 通过重新生成骨架代码进行迭代。

要了解有关使用 VS 代码开发 IoT 设备的更多详细信息[https://github.com/microsoft/vscode-iot-workbench](https://github.com/microsoft/vscode-iot-workbench)，请参阅。

## <a name="prerequisites"></a>先决条件

安装[可视化工作室代码](https://code.visualstudio.com/)。

使用以下步骤在 VS 代码中安装扩展包。

1. 在 VS 代码中，选择 **"扩展"** 选项卡。
1. 从应用商店中搜索并安装**Azure IoT 工具**。

## <a name="generate-device-code-and-project"></a>生成设备代码和项目

在 VS 代码中，使用**Ctrl_Shift_P**打开命令调色板，输入**IoT 即插即用**，然后选择 **"生成设备代码存根**"来配置骨架代码和项目类型。 下面的列表详细介绍了每个步骤：

- **用于生成代码的 DCM 文件**。 要生成骨架设备代码，请打开包含它实现的 DCM 和接口文件的文件夹。 如果代码生成器找不到 DCM 所需的接口，它将根据需要从模型存储库下载它。

- **设备代码语言**。 目前，代码生成器仅支持 ANSI C。

- **项目名称**. 项目名称用作生成的代码和其他项目文件的文件夹名称。 默认情况下，该文件夹位于 DCM 文件旁边。 为了避免在更新 DCM 并重新生成设备代码时手动复制生成的代码文件夹，请将 DCM 文件保留与项目文件夹相同的文件夹中。

- **连接到 Azure IoT 的方法**。 生成的文件还包含用于配置设备以连接到 Azure IoT 中心的代码。 您可以选择直接连接到[Azure IoT 中心](https://docs.microsoft.com/azure/iot-hub)或使用[设备预配服务](https://docs.microsoft.com/azure/iot-dps)。

    - **通过 IoT 中心设备连接字符串**：指定设备应用程序直接连接到 IoT 中心的设备连接字符串。
    - **通过 DPS 对称密钥**：为使用 DPS 连接到 IoT 中心或 IoT 中心所需的设备应用程序指定**ID 范围**、**对称密钥**和设备**ID。**

- **项目类型**. 代码生成器还生成 CMake 或 Arduino 项目。 目前，支持的项目类型包括：

    - **在 Windows 上的"制作项目**"：用于在 Windows 上使用[CMake](https://cmake.org/)作为生成系统的设备项目。 此选项与`CMakeLists.txt`C 代码在同一文件夹中的设备 SDK 配置一起生成。
    - **在Linux上的CMake项目**：用于在Linux上使用[CMake](https://cmake.org/)作为构建系统的设备项目。 此选项与`CMakeLists.txt`C 代码在同一文件夹中的设备 SDK 配置一起生成。
    - **MXChip IoT DevKit 项目**：用于在[MXChip IoT DevKit](https://aka.ms/iot-devkit)设备上运行的设备项目。 此选项生成 Arduino 项目，您可以在[VS 代码](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)或 Arduino IDE 中使用该项目在 IoT DevKit 设备上生成和运行。

- **设备 SDK 类型**。 如果选择 CMake 作为项目类型，则这是配置生成的代码将如何在 以下部分`CMakeLists.txt`中包括 Azure IoT C 设备 SDK 的步骤：

    - **通过源代码**：生成的代码依赖于设备 SDK[源代码](https://github.com/Azure/azure-iot-sdk-c)来包含在中并与之一起构建。 自定义设备 SDK 源代码时，建议这样做。
    - **通过 Vcpkg**：生成的代码依赖于[设备 SDK Vcpkg](https://github.com/microsoft/vcpkg/tree/master/ports/azure-iot-sdk-c)来包含在中并与之一起构建。 对于运行 Windows、Linux 或 macOS 的设备，这是推荐的方法。

    > [!NOTE]
    > 对 Azure IoT C 设备 SDK Vcpkg 的 macOS 支持正在进行中。

代码生成器尝试使用位于本地文件夹中的 DCM 和接口文件。 如果接口文件不在本地文件夹中，代码生成器在公共模型存储库或公司模型存储库中查找它们。 [公共接口文件](./concepts-common-interfaces.md)存储在公共模型存储库中。

代码生成完成后，扩展将打开一个带有代码的新 VS 代码窗口。 如果打开生成的文件（如**main.c，** 您可能会发现 IntelliSense）报告它无法打开 C SDK 源文件。 要启用正确的 IntelliSense 和代码导航，请使用以下步骤包括 C SDK 源：

1. 在 VS 代码中，使用**Ctrl_Shift_P**打开命令调色板，键入并选择**C/C++：编辑配置 （JSON）** 以打开**c_cpp_properties.json**文件。

1. 在以下部分中添加设备 SDK`includePath`的路径：

    ```json
    "includePath": [
        "${workspaceFolder}/**",
        "{path_of_device_c_sdk}/**"
    ]
    ```

1. 保存文件。

## <a name="use-the-generated-code"></a>使用生成的代码

以下说明描述了如何在不同开发机器平台上的设备上使用生成的代码。 这些说明假定您使用的是带有生成的代码的 IoT 中心设备连接字符串：

### <a name="linux"></a>Linux

在 Linux 环境中使用 CMake（如 Ubuntu 或 Debian）中的 CMake 与设备 C SDK Vcpkg 一起构建设备代码，请：

1. 打开终端应用程序。

1. 使用`apt-get`命令安装`cmake`**GCC** **、Git**和所有依赖项：

    ```bash
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    验证 `cmake` 的版本是否高于 2.8.12，GCC 的版本是否高于 4.4.7************。

    ```bash
    cmake --version
    gcc --version
    ```

1. 安装 Vcpkg：

    ```bash
    git clone https://github.com/Microsoft/vcpkg.git
    cd vcpkg

    ./bootstrap-vcpkg.sh
    ```

    然后，要连接用户范围[的集成](https://github.com/microsoft/vcpkg/blob/master/docs/users/integration.md)，运行：

    ```bash
    ./vcpkg integrate install
    ```

1. 安装 Azure IoT C 设备 SDK Vcpkg：

    ```bash
    ./vcpkg install azure-iot-sdk-c[public-preview,use_prov_client]
    ```

1. 在文件夹中`cmake`创建子目录包含生成的代码存根，并导航到该文件夹：

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令以使用 CMake 构建设备 SDK 和生成的代码存根：

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DCMAKE_TOOLCHAIN_FILE="{directory of your Vcpkg repo}/scripts/buildsystems/vcpkg.cmake"

    cmake --build .
    ```

1. 生成成功后，运行指定 IoT 中心设备连接字符串作为参数的应用程序。

    ```bash
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

### <a name="windows"></a>Windows

要使用 CMake 和命令行中的 Visual Studio C/C++ 编译器在 Windows 上构建设备代码，请参阅[IoT 即插即用快速入门](./quickstart-create-pnp-device-windows.md)。 以下步骤演示如何在 Visual Studio 中将设备 C SDK Vcpkg 作为 CMake 项目一起构建设备代码。

1. 按照[快速入门](https://docs.microsoft.com/azure/iot-pnp/quickstart-create-pnp-device-windows#prepare-the-development-environment)中的步骤通过 Vcpkg 安装 C 的 Azure IoT 设备 SDK。

1. 安装[Visual Studio 2019（社区、专业版或企业版）](https://visualstudio.microsoft.com/downloads/) - 确保您包括**NuGet 包管理器**组件和**具有C++工作负载的桌面开发**。

1. 打开可视化工作室，选择 **"打开文件>>"完成"，** 以`CMakeLists.txt`打开文件夹中包含生成的代码。

1. 在 **"常规**"工具栏中，查找 **"配置"** 下拉列表。 选择 **"管理配置**"可为项目添加"制作"设置。

    ![管理配置](media/howto-develop-with-vs-vscode/vs-manage-config.png)

1. 在 **"CMake 设置"** 中，添加新配置并选择**x86-Debug**作为目标。

1. 在 **"CMake 命令参数"中**，添加以下行：

    ```txt
    -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    ```

1. 保存文件。

1. 在 **"配置"** 下拉下下拉列表中切换到**x86-Debug。** 它需要几秒钟的 CMake 来为其生成缓存。 查看"输出"窗口以查看进度。

    ![CMake 输出](media/howto-develop-with-vs-vscode/vs-cmake-output.png)

1. 在**解决方案资源管理器**中，右键单击`CMakeLists.txt`根文件夹中的，然后从上下文菜单中选择 **"生成"** 以使用设备 SDK 构建生成的代码存根。

1. 生成成功后，在命令提示符下，运行应用程序，指定 IoT 中心设备连接字符串作为参数。

    ```cmd
    .\out\build\x86-Debug\{generated_code_project_name}.exe "[IoT Hub device connection string]"
    ```

> [!TIP]
> 要了解有关在可视化工作室中使用"单行"的更多详细信息，请参阅[构建"构建单行"项目](https://docs.microsoft.com/cpp/build/cmake-projects-in-visual-studio?view=vs-2019#building-cmake-projects)。

### <a name="macos"></a>macOS

以下步骤演示如何使用 CMake 在 macOS 上构建设备代码和设备 C SDK 源代码：

1. 开放式终端应用程序。

1. 使用[Homebrew](https://homebrew.sh)安装所有依赖项：

    ```bash
    brew update
    brew install git cmake pkgconfig openssl ossp-uuid
    ```

1. 验证[CMake](https://cmake.org/)是否至少是版本**2.8.12**：

    ```bash
    cmake --version
    ```

1. [将 CURL 修补](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#upgrade-curl-on-mac-os)到最新版本。

1. 在包含生成代码的文件夹中，克隆[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c)存储库：

    ```bash
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    应该预料到此操作需要几分钟才能完成。

1. 在包含生成的代码`cmake`的文件夹下创建一个文件夹，然后导航到该文件夹。

    ```bash
    mkdir cmake
    cd cmake
    ```

1. 运行以下命令以使用 CMake 构建设备 SDK 和生成的代码存根：

    ```bash
    cmake -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -DOPENSSL_ROOT_DIR:PATH=/usr/local/opt/openssl ..
    cmake --build .
    ```

1. 生成成功后，运行指定 IoT 中心设备连接字符串作为参数的应用程序。

    ```bash
    cd {generated_code_folder_name}/cmake/
    ./{generated_code_project_name} "[IoT Hub device connection string]"
    ```

## <a name="iterate-by-regenerating-the-skeleton-code"></a>通过重新生成骨架代码进行迭代

如果更新 DCM 或接口文件，代码生成器可以重新生成代码。 假设您已经从 DCM 文件生成了设备代码，以便重新生成代码：

1. 打开包含 DCM 文件的文件夹后，按 **Ctrl+Shift+P** 打开命令面板，输入“IoT 即插即用”，然后选择“生成设备代码存根”。********

1. 选择您更新的 DCM 文件。

1. 选择**重新生成 [项目名称] 的代码**。

1. 代码生成器使用您配置的上一个设置并重新生成代码。 但是，它不会覆盖可能包含用户代码（如`main.c`和`{project_name}_impl.c`） 的文件。

> [!NOTE]
> 如果更新接口文件中的 URN ID，则将其视为新接口。 重新生成代码时，代码生成器会为接口生成代码，但不会覆盖`{project_name}_impl.c`文件中的原始代码。

## <a name="problems-and-feedback"></a>问题和反馈

Azure IoT 工具是 GitHub 上的开源项目。 对于任何问题和功能请求，您可以在[GitHub 上创建问题](https://github.com/microsoft/vscode-azure-iot-tools/issues/new)。

## <a name="next-steps"></a>后续步骤

在本文中，您已经了解如何使用 Visual Studio 和 Visual Studio 代码生成骨架 C 代码来实现设备应用程序。 建议的下一步是了解如何[安装和使用 Azure IoT 资源管理器](./howto-install-iot-explorer.md)工具。
