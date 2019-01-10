---
title: 在 Visual Studio 中开发和调试 C# 模块 - Azure IoT Edge | Microsoft Docs
description: 使用 Visual Studio 2017 开发和调试适用于 Azure IoT Edge 的 C# 模块
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 12/21/2018
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 954fb0fd4c8b9773edad904fa82a0a90111a66fa
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754588"
---
# <a name="use-visual-studio-2017-to-develop-and-debug-c-modules-for-azure-iot-edge-preview"></a>使用 Visual Studio 2017 开发和调试适用于 Azure IoT Edge 的 C# 模块（预览版）

可以将业务逻辑转变为用于 Azure IoT Edge 的模块。 本文展示了如何使用 Visual Studio 2017 作为主要工具来开发和调试 C# 模块。

用于 Visual Studio 的 Azure IoT Edge 工具提供以下优势：

- 在本地开发计算机上创建、编辑、构建、运行和调试 Azure IoT Edge 解决方案和模块。
- 通过 Azure IoT 中心将 Azure IoT Edge 解决方案部署到 Azure IoT Edge 设备。
- 在 C# 中编写 Azure IoT 模块的代码，同时兼具 Visual Studio 开发的所有优势。
- 使用 UI 管理 Azure IoT Edge 设备和模块。

本文介绍了如何通过用于 Visual Studio 2017 的 Azure IoT Edge 工具在 C# 中部署 IoT Edge 模块。 你还将了解如何将项目部署到 Azure IoT Edge 设备。

## <a name="prerequisites"></a>先决条件

本文假设你使用运行 Windows 的计算机或虚拟机作为开发计算机。 IoT Edge 设备可以是另一台物理设备。

本文使用 Visual Studio 2017 作为主要开发工具，因此请安装 Visual Studio。 请确保你在安装 Visual Studio 2017 时随附了 Azure 开发工作负载。 你可[修改 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2017) 并添加 Azure 开发工作负载。

Visual Studio 2017 准备就绪后，还需要以下工具和组件：

- 从 Visual Studio Marketplace 中下载并安装 [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)，以在 Visual Studio 2017 中创建 IoT Edge 项目。

- 在开发计算机上下载并安装 [Docker 社区版](https://docs.docker.com/install/)，以生成和运行模块映像。 需要将 Docker CE 设置为在 Linux 容器模式或 Windows 容器模式下运行。

- 设置本地开发环境，通过安装 [Azure IoT EdgeHub 开发工具](https://pypi.org/project/iotedgehubdev/)，调试、运行和测试 IoT Edge 解决方案。 请安装 [Python (2.7/3.6) 和 Pip](https://www.python.org/)，然后在终端中运行以下命令安装 iotedgehubdev 包。 确保 Azure IoT EdgeHub 开发工具版本高于 0.3.0。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)。

  > [!TIP]
  > 对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。

- 若要在设备上测试模块，至少需要一个 IoT Edge 设备和一个活动的 IoT 中心。 若要将计算机用作 IoT Edge 设备，请按照 [Linux](quickstart-linux.md) 或 [Windows](quickstart.md) 快速入门中的步骤进行操作。 如果正在开发计算机上运行 IoT Edge 守护程序，则可能需要停止 EdgeHub 和 EdgeAgent，然后再在 Visual Studio 中开始开发。

### <a name="check-your-tools-version"></a>检查工具版本

1. 在“工具”菜单上，选择“扩展和更新”。 依次展开“已安装”>“工具”，可在此处找到“Azure IoT Edge 工具”和“Cloud Explorer for Visual Studio”。

1. 请记下所安装的版本。 可将此版本与 Visual Studio Marketplace 中的最新版本（[Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS)、[Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)）进行比较

1. 如果你的版本比 Visual Studio Marketplace 上的版本旧，则可在 Visual Studio 中更新工具，如以下部分中所示。

### <a name="update-your-tools"></a>更新工具

1. 在“扩展和更新”对话框中，依次展开“更新”>“Visual Studio Marketplace”，选择“Azure IoT Edge 工具”或“Cloud Explorer for Visual Studio”，再选择“更新”。

1. 下载工具更新后，请关闭 Visual Studio 以触发使用 VSIX 安装程序的工具更新。

1. 在安装程序中，选择“确定”进行启动，然后选择“修改”以更新工具。

1. 更新完成后，请选择“关闭”并重启 Visual Studio。

### <a name="create-an-azure-iot-edge-project"></a>创建 Azure IoT Edge 项目

Visual Studio 中的 Azure IoT Edge 项目模板创建了一个项目，它可部署到 Azure IoT 中心的 Azure IoT Edge 设备。 首先创建一个 Azure IoT Edge 解决方案，然后在该解决方案中生成第一个 C# 模块。 每一个 IoT Edge 解决方案都可包含多个模块。

1. 在 Visual Studio 中，从“文件”菜单中选择“新建” > “项目”。

1. 在“新建项目”对话框中，选择“已安装”，依次展开“Visual C#”>“云”，再选择“Azure IoT Edge”，键入项目的名称并指定位置，然后选择“确定”。 默认的项目名称为 AzureIoTEdgeApp1。

   ![新建项目](./media/how-to-visual-studio-develop-csharp-module/create-new.jpg)

1. 在“IoT Edge 模块配置”窗口中，选择“C# 模块”，并指定模块名和模块映像存储库。 Visual Studio 使用“localhost:5000/<模块名\>自动填充模块名。 将其替换为你自己的注册表信息。 如果使用本地 Docker 注册表进行测试，则可以使用 localhost。 如果使用 Azure 容器注册表，那么请从注册表的设置中使用登录服务器。 登录服务器如下所示：\<注册表名\>.azurecr.io。 仅替换字符串的“localhost:5000”部分，以使最终结果看起来像*\<注册表名\>.azurecr.io/*\<模块名\>****。 默认的模块名称为 IoTEdgeModule1

1. 选择“确定”，通过 C# 模块创建 Azure IoT Edge 项目。

现在，你的解决方案中有一个 AzureIoTEdgeApp1 项目和一个 IoTEdgeModule1 项目。 AzureIoTEdgeApp1 项目具有一个 deployment.template.json 文件。 此文件定义了要为 IoT Edge 解决方案生成和部署的模块，还定义了模块之间的路由。 默认解决方案自带一个 tempSensor 模块和一个 IoTEdgeModule1 模块。 tempSensor 模块向 IoTEdgeModule1 模块生成模拟数据，而 IoTEdgeModule1 模块中的默认代码直接将收到的消息传输到 Azure IoT 中心。

IoTEdgeModule1 项目是一个 .NET Core 2.1 控制台应用程序。 它包含 IoT Edge 设备通过 Windows 容器或 Linux 容器运行时所需的 Docker 文件。 module.json 文件描述了模块的元数据。 program.cs 是实际的模块代码，它将 Azure IoT 设备 SDK 作为依赖项。

## <a name="develop-your-module"></a>开发模块

解决方案附带的默认 C# 模块代码位于 IoTEdgeModule1  >  Program.cs。 设置模块和 deployment.template.json 文件，以便可以生成解决方案，将其推送到容器注册表，然后部署到设备以开始测试而无需触及任何代码。 生成该模块仅为简单从源（在此示例中，为模拟数据的 tempSensor 模块）获取输入并通过管道将其传送到 Azure IoT 中心。

当你准备使用自己的代码自定义 C# 模板时，请使用 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以满足 IoT 解决方案的关键需求（例如安全性、设备管理和可靠性）。

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>使用 IoT Edge 设备连接字符串初始化iotegehubdev

1. 从 Visual Studio Cloud Explorer 的“主连接字符串”中复制 IoT Edge 设备的连接字符串。 请勿复制非 Egde 设备的连接字符串，IoT Edge 设备的图标不同于非 Edge 设备的图标。

   ![复制 Edge 设备连接字符串](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. 右键单击“AzureIoTEdgeApp1”项目，然后单击“设置 Edge 设备连接字符串”，以打开 Azure IoT Edge 设置窗口。

   ![打开设置 Edge 连接字符串窗口](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. 输入第一步中获取的连接字符串，然后选择“确定”。

> [!NOTE]
> 只需在开发计算机上执行一次这些步骤，所得结果将自动应用于所有后续 Azure IoT Edge 解决方案。 如果需要更改为另一个连接字符串，可以再次执行此过程。

## <a name="build-and-debug-single-c-module"></a>构建和调试单个 C# 模块

通常，需要先测试和调试每个模块，然后再在具有多个模块的整个解决方案中运行此模块。

1. 右键单击“IoTEdgeModule1”并从上下文菜单中选择“设为启动项目”。

   ![设置启动项目](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. 按 F5 或单击下方的按钮以运行模块，首次运行时可能耗时 10&ndash;20 秒。

   ![运行模块](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. 如果模块已成功初始化，你应会看到 .NET Core 控制台应用已启动。

   ![运行中的模块](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. 在 Program.cs 的 `PipeMessage()` 函数中设置断点，然后在 Git Bash 或 WSL Bash shell 中运行以下命令，发送消息，测试断点。 （无法从 PowerShell 或命令提示符运行 `curl` 命令。）

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

1. 右键单击“AzureIoTEdgeApp1”并选择“添加” > “新 IoT Edge 模块”，向解决方案添加第二个 C# 模块。 第二个模块的默认名称为 IoTEdgeModule2，用于充当另一个管道模块。

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

1. 创建断点，然后按 F5 以同时运行和调试多个模块。 随即显示多个 .NET Core 控制台应用窗口，每个窗口表示一个不同的 C# 模块。

   ![调试多个模块](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. 按 Ctrl+F5 或选择“停止”按钮可停止调试。

## <a name="build-and-push-images"></a>生成并推送映像

1. 请确保启动项目是 AzureIoTEdgeApp1。 选择“调试”或“发布”作为配置，以生成模块映像。

    > [!NOTE]
    > 如果选择“调试”，Visual Studio 使用 `Dockerfile.debug` 生成 Docker 映像。 生成容器映像时，它将在该映像中包含 .NET Core 命令行调试器 VSDBG。 对于生产就绪的 IoT Edge 模块，建议使用“发布”配置，此模块可在没有 VSDBG 的情况下使用 `Dockerfile`。

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
