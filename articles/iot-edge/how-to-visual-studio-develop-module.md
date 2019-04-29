---
title: 在 Visual Studio 中开发和调试模块 - Azure IoT Edge | Microsoft Docs
description: 使用 Visual Studio 2017 开发和调试适用于 Azure IoT Edge 的模块
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 04/03/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: f2228726d4edc25efe46a660d25d398959c3ea59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595862"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-modules-for-azure-iot-edge-preview"></a>使用 Visual Studio 2017 开发和调试适用于 Azure IoT Edge 的模块（预览）

可以将业务逻辑转变为用于 Azure IoT Edge 的模块。 本文介绍如何使用 Visual Studio 2017 作为主要工具来开发和调试模块。

用于 Visual Studio 的 Azure IoT Edge 工具提供以下优势：

- 在本地开发计算机上创建、编辑、构建、运行和调试 Azure IoT Edge 解决方案和模块。
- 通过 Azure IoT 中心将 Azure IoT Edge 解决方案部署到 Azure IoT Edge 设备。
- 在 C 或 C# 中编写 Azure IoT 模块的代码，同时兼具 Visual Studio 开发的所有优势。
- 使用 UI 管理 Azure IoT Edge 设备和模块。

本文介绍如何使用适用于 Visual Studio 2017 的 Azure IoT Edge 工具开发 IoT Edge 模块。 你还将了解如何将项目部署到 Azure IoT Edge 设备。

> [!TIP]
> Visual Studio 创建的 IoT Edge 项目结构与 Visual Studio Code 中的不同。
  
## <a name="prerequisites"></a>必备组件

本文假设你使用运行 Windows 的计算机或虚拟机作为开发计算机。 IoT Edge 设备可以是另一台物理设备。

本文使用 Visual Studio 2017 作为主要开发工具，因此请安装 Visual Studio。 确保在 Visual Studio 2017 安装中包含“Azure 开发”和“使用 C++ 的桌面开发”工作负荷。 可以[修改 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) 以添加所需的工作负荷。

Visual Studio 2017 准备就绪后，还需要以下工具和组件：

- 从 Visual Studio Marketplace 中下载并安装 [Azure IoT Edge 扩展（预览版）](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)，以在 Visual Studio 2017 中创建 IoT Edge 项目。

- 在开发计算机上下载并安装 [Docker 社区版](https://docs.docker.com/install/)，以生成和运行模块映像。 需要将 Docker CE 设置为在 Linux 容器模式或 Windows 容器模式下运行。

- 设置本地开发环境，通过安装 [Azure IoT EdgeHub 开发工具](https://pypi.org/project/iotedgehubdev/)，调试、运行和测试 IoT Edge 解决方案。 请安装 [Python (2.7/3.6) 和 Pip](https://www.python.org/)，然后在终端中运行以下命令安装 iotedgehubdev 包。 确保 Azure IoT EdgeHub 开发工具版本高于 0.3.0。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- 克隆存储库并安装 Vcpkg 库管理器，然后安装适用于 Windows 的 **azure-iot-sdk-c 包**。

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。

  > [!TIP]
  > 对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。

- 若要在设备上测试模块，至少需要一个 IoT Edge 设备和一个活动的 IoT 中心。 若要将计算机用作 IoT Edge 设备，请按照 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入门中的步骤进行操作。 如果正在开发计算机上运行 IoT Edge 守护程序，则可能需要停止 EdgeHub 和 EdgeAgent，然后再在 Visual Studio 中开始开发。

### <a name="check-your-tools-version"></a>检查工具版本

1. 在“工具”菜单上，选择“扩展和更新”。 依次展开“已安装”>“工具”，可在此处找到“Azure IoT Edge 工具”和“Cloud Explorer for Visual Studio”。

1. 请记下所安装的版本。 可将此版本与 Visual Studio Marketplace 中的最新版本（[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)）进行比较

1. 如果你的版本比 Visual Studio Marketplace 上的版本旧，则可在 Visual Studio 中更新工具，如以下部分中所示。

### <a name="update-your-tools"></a>更新工具

1. 在“扩展和更新”对话框中，依次展开“更新”>“Visual Studio Marketplace”，选择“Azure IoT Edge 工具”或“Cloud Explorer for Visual Studio”，再选择“更新”。

1. 下载工具更新后，请关闭 Visual Studio 以触发使用 VSIX 安装程序的工具更新。

1. 在安装程序中，选择“确定”进行启动，然后选择“修改”以更新工具。

1. 更新完成后，请选择“关闭”并重启 Visual Studio。

### <a name="create-an-azure-iot-edge-project"></a>创建 Azure IoT Edge 项目

Visual Studio 中的 Azure IoT Edge 项目模板创建了一个项目，它可部署到 Azure IoT 中心的 Azure IoT Edge 设备。 首先创建一个 Azure IoT Edge 解决方案，并在该解决方案中生成第一个模块。 每一个 IoT Edge 解决方案都可包含多个模块。

1. 在 Visual Studio 中，从“文件”菜单中选择“新建” > “项目”。

1. 在“新建项目”对话框中，依次选择“已安装”、“Azure IoT”、“Azure IoT Edge”，输入项目的名称并指定位置，然后选择“确定”。 默认的项目名称为 AzureIoTEdgeApp1。

   ![新建项目](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. 在“添加 IoT Edge 应用程序和模块”窗口中，选择“Linux Amd64”和/或“Windows Amd64”作为应用程序平台。 如果选择两者，请创建包含两个项目的解决方案，其中的每个项目引用默认代码模块。

   > [!TIP]
   > 适用于 Visual Studio 的 Azure IoT Edge 扩展目前不支持创建适用于 ARM 平台的项目。 有关使用 Visual Studio Code 开发适用于 ARM32v7/armhf 的解决方案的示例，请参阅此 [IoT 开发人员博客文章](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)。

1. 选择“C# 模块”或“C 模块”，然后指定模块名称和模块映像存储库。 Visual Studio 使用“localhost:5000/<模块名\>自动填充模块名。 将其替换为你自己的注册表信息。 如果使用本地 Docker 注册表进行测试，则可以使用 localhost。 如果使用 Azure 容器注册表，那么请从注册表的设置中使用登录服务器。 登录服务器如下所示：*\<注册表名称\>*.azurecr.io。 仅替换字符串的**localhost:5000”** 部分，以使最终结果看起来像**\<* 注册表名*\>.azurecr.io/*\<模块名\>***。 默认的模块名称为 IoTEdgeModule1

1. 选择“确定”创建包含使用 C# 或 C 的模块的 Azure IoT Edge 解决方案。

现已在解决方案中创建 **AzureIoTEdgeApp1.Linux.Amd64** 项目和/或 **AzureIoTEdgeApp1.Windows.Amd64** 项目，以及一个 **IoTEdgeModule1** 项目。 每个 **AzureIoTEdgeApp1** 项目包含一个 `deployment.template.json` 文件，此文件定义了要为 IoT Edge 解决方案生成和部署的模块，并定义了模块之间的路由。 默认解决方案自带一个 tempSensor 模块和一个 IoTEdgeModule1 模块。 tempSensor 模块向 IoTEdgeModule1 模块生成模拟数据，而 IoTEdgeModule1 模块中的默认代码直接将收到的消息传输到 Azure IoT 中心。

IoTEdgeModule1 项目是一个 .NET Core 2.1 控制台应用程序。 它包含 IoT Edge 设备通过 Windows 容器或 Linux 容器运行时所需的 Docker 文件。 `module.json` 文件描述模块的元数据。 在 `Program.cs` 或 `main.c` 文件中可以找到将 Azure IoT 设备 SDK 用作依赖项的实际模块代码。

## <a name="develop-your-module"></a>开发模块

解决方案附带的默认模块代码位于 **IoTEdgeModule1** > **Program.cs** (C#) 或 **main.c** (C) 中。 将设置模块和 `deployment.template.json` 文件，使你能够生成解决方案，将其推送到容器注册表，然后部署到设备以开始测试，而无需修改任何代码。 生成该模块仅为简单从源（在此示例中，为模拟数据的 tempSensor 模块）获取输入并通过管道将其传送到 Azure IoT 中心。

准备好使用自己的代码自定义模块模板时，请使用 [Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以解决 IoT 解决方案的关键需求，例如安全性、设备管理和可靠性。

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>使用 IoT Edge 设备连接字符串初始化iotegehubdev

1. 从 Visual Studio Cloud Explorer 的“主连接字符串”中复制 IoT Edge 设备的连接字符串。 请勿复制非 Egde 设备的连接字符串，IoT Edge 设备的图标不同于非 Edge 设备的图标。

   ![复制 Edge 设备连接字符串](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. 右键单击“AzureIoTEdgeApp1”项目，然后单击“设置 Edge 设备连接字符串”，以打开 Azure IoT Edge 设置窗口。

   ![打开设置 Edge 连接字符串窗口](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 输入第一步中获取的连接字符串，然后选择“确定”。

> [!NOTE]
> 只需在开发计算机上执行一次这些步骤，所得结果将自动应用于所有后续 Azure IoT Edge 解决方案。 如果需要更改为另一个连接字符串，可以再次执行此过程。

## <a name="build-and-debug-single-module"></a>生成和调试单个模块

通常，需要先测试和调试每个模块，然后再在具有多个模块的整个解决方案中运行此模块。

1. 右键单击“IoTEdgeModule1”并从上下文菜单中选择“设为启动项目”。

   ![设置启动项目](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. 按 F5 或单击下方的按钮以运行模块，首次运行时可能耗时 10&ndash;20 秒。

   ![运行模块](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 如果模块已成功初始化，你应会看到 .NET Core 控制台应用已启动。

   ![运行中的模块](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. 如果以 C# 进行开发，请在 **Program.cs** 中的 `PipeMessage()` 函数内设置一个断点；如果使用 C，请在 **main.c** 中的 `InputQueue1Callback()` 函数内设置断点。 然后，可以通过在 **Git Bash** 或 **WSL Bash** shell 中运行以下命令发送消息，来测试断点。 （无法从 PowerShell 或命令提示符运行 `curl` 命令。）

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![调试单个模块](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    应触发断点。 可在 Visual Studio“局部变量”窗口中监视变量。

   > [!TIP]
   > 还可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具（而非 `curl`）发送消息。

1. 按 Ctrl + F5 或单击“停止”按钮以停止调试。

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>构建并调试具有多个模块的 IoT Edge 解决方案

开发完单个模块之后，需要运行并调试具有多个模块的整个解决方案。

1. 右键单击“AzureIoTEdgeApp1”并选择“添加” > “新 IoT Edge 模块”，向解决方案添加另一个模块。 第二个模块的默认名称为 IoTEdgeModule2，用于充当另一个管道模块。

1. 打开文件 `deployment.template.json`，会看到 IoTEdgeModule2 已添加到“模块”部分中。 将 routes 部分替换为以下内容。 如果自定义了模块名，请确保更新这些名称以匹配模块名。

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. 右键单击“AzureIoTEdgeApp1”并从上下文菜单中选择“设置为启动项目”。

1. 创建断点，然后按 F5 以同时运行和调试多个模块。 此时应会看到多个 .NET Core 控制台应用窗口，每个窗口表示一个不同的模块。

   ![调试多个模块](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. 按 Ctrl+F5 或选择“停止”按钮可停止调试。

## <a name="build-and-push-images"></a>生成并推送映像

1. 请确保启动项目是 AzureIoTEdgeApp1。 选择“调试”或“发布”作为配置，以生成模块映像。

    > [!NOTE]
    > 如果选择“调试”，Visual Studio 使用 `Dockerfile.(amd64|windows-amd64).debug` 生成 Docker 映像。 生成容器映像时，它将在该映像中包含 .NET Core 命令行调试器 VSDBG。 对于生产就绪的 IoT Edge 模块，建议使用“发布”配置，此模块可在没有 VSDBG 的情况下使用 `Dockerfile.(amd64|windows-amd64)`。

1. 如果使用的是专用注册表（如 Azure 容器注册表），请使用以下 Docker 命令登录。 如果使用的是本地注册表，可[运行本地注册表](https://docs.docker.com/registry/deploying/#run-a-local-registry)。

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. 如果使用的是专用注册表（如 Azure 容器注册表），则需要将注册表登录信息添加到文件 `deployment.template.json` 中的运行时设置。 将占位符替换为实际 ACR 管理员用户名、密码和注册表名。

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. 右键单击“AzureIoTEdgeApp1”，选择“构建并推送 Edge 解决方案”，为每个模块生成和推送 Docker 映像。

   ![生成并推送映像](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>部署解决方案

在用于设置 IoT Edge 设备的快速入门文章中，已使用 Azure 门户部署了一个模块。 还可使用 Cloud Explorer for Visual Studio 部署模块。 为方案和 `deployment.json` 文件准备好部署清单后，接下来只需要选择一个设备来接收部署即可。

1. 通过单击“视图” > “Cloud Explorer”打开 Cloud Explorer。 确保已登录到 Visual Studio 2017。

1. 在 Cloud Explorer 中，展开订阅并找到要部署的 Azure IoT 中心和 Azure IoT Edge 设备。

1. 右键单击 IoT Edge 设备，为其创建部署。你需要选择 `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` 下的部署清单文件。

   > [!NOTE]
   > 不得选择 `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json`

1. 单击刷新按钮以查看新模块与 TempSensor 模块和 $edgeAgent 及 $edgeHub 一并运行。

## <a name="view-generated-data"></a>查看生成的数据

1. 若要监视特定设备的 D2C 消息，请选择列表中的设备，然后单击“操作”窗口中的“开始监视 D2C 消息”。

1. 若要停止监视数据，请选择列表中的设备，然后选择“操作”窗口中的“停止监视 D2C 消息”。

## <a name="next-steps"></a>后续步骤

若要开发用于 IoT Edge 设备的自定义模块，请参阅[了解并使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
