---
title: 调试用于 Azure IoT Edge 的 Node.js 模块 | Microsoft Docs
description: 使用 Visual Studio Code 开发和调试用于 Azure IoT Edge 的 Node.js 模块
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 09/21/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: b05492941defc6ac3aa252d6bb29043d55e6b66c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261768"
---
# <a name="use-visual-studio-code-to-develop-and-debug-nodejs-modules-for-azure-iot-edge"></a>使用 Visual Studio Code 开发和调试用于 Azure IoT Edge 的 Node.js 模块

可以发送业务逻辑，通过将其转换为用于 Azure IoT Edge 的模块来在边缘进行操作。 本文提供了有关使用 Visual Studio Code (VS Code) 作为主要开发工具来开发 Node.js 模块的详细说明。

## <a name="prerequisites"></a>先决条件
本文假设使用运行 Windows、macOS 或 Linux 的计算机或虚拟机作为开发计算机。 IoT Edge 设备可以是另一台物理设备。

> [!NOTE]
> 本调试文章演示了在 VS Code 中调试 Node.js 模块的两个典型方法。 一种方法是在模块容器中附加一个进程，而另一种方法是在调试模式下启动模块代码。 如果你不熟悉 Visual Studio Code 的调试功能，请阅读有关[Debugging](https://code.visualstudio.com/Docs/editor/debugging)（调试）的信息。

由于本文使用 Visual Studio Code 作为主要开发工具，因此，请安装 VS Code，然后添加必要的扩展：
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker 扩展](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

若要创建模块，需要采用一个 Node.js，并在其中包括用来生成项目文件夹的 npm、用来生成模块映像的 Docker 以及用来存放模块映像的容器注册表：
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)
   * 对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。 

若要设置本地开发环境以调试、运行和测试 IoT Edge 解决方案，需要具有 [Azure IoT EdgeHub 开发工具](https://pypi.org/project/iotedgehubdev/)。 安装 [Python (2.7/3.6) 和 Pip](https://www.python.org/)。 Python 安装程序包含 Pip。 然后，通过在终端中运行以下命令来安装 iotedgehubdev。

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

若要在设备上测试模块，需要一个活动的 IoT 中心，其中至少创建了一个 IoT Edge 设备 ID。 如果正在开发计算机上运行 IoT Edge 守护程序，则可能需要在转到下一步之前停止 EdgeHub 和 EdgeAgent。 

## <a name="create-a-new-solution-template"></a>创建新的解决方案模板

以下步骤介绍了如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建基于 Node.js 的 IoT Edge 模块。 首先创建一个解决方案，然后生成该解决方案中的第一个模块。 每个解决方案可以包含多个模块。 

1. 在 Visual Studio Code 中，选择“视图” > “集成终端”。
2. 在集成的终端中，输入以下命令来安装（或更新）Node.js 的 Azure IoT Edge 模块模板的最新版本：

   ```cmd/sh
   npm install -g yo generator-azure-iot-edge-module
   ```

3. 在 Visual Studio Code 中，选择“视图” > “命令面板”。 
4. 在命令面板中，键入并运行“Azure IoT Edge: New IoT Edge Solution”命令。

   ![运行新的 IoT Edge 解决方案](./media/how-to-develop-csharp-module/new-solution.png)

5. 浏览到要在其中创建新解决方案的文件夹，然后单击“选择文件夹”。 
6. 为解决方案提供一个名称。 
7. 选择“Node.js 模块”作为解决方案中第一个模块的模板。
8. 为模块提供一个名称。 选择一个在容器注册表中唯一的名称。 
9. 为模块提供映像存储库。 VS Code 会自动填充模块名称，因此，只需将 **localhost:5000** 替换为你自己的注册表信息。 如果使用本地 Docker 注册表进行测试，那么可以使用 localhost。 如果使用 Azure 容器注册表，那么请从注册表的设置中使用登录服务器。 登录服务器如下所示：\<registry name\>.azurecr.io。 仅替换字符串的 localhost 部分，不要删除模块名。

   ![提供 Docker 映像存储库](./media/how-to-develop-node-module/repository.png)

VS Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。

在解决方案中，你将具有以下三项： 
* 一个 **.vscode** 文件夹，包含调试配置。
* 一个 **modules** 文件夹，包含每个模块的子文件夹。 现在，你只有一个模块，但是可以使用命令“Azure IoT Edge: Add IoT Edge Module”在命令面板中添加更多模块。 
* 一个 **.env** 文件，列出环境变量。 如果 Azure 容器注册表是注册表，则其中将包含 Azure 容器注册表用户名和密码。

   >[!NOTE]
   >仅当为模块提供了映像存储库时，才会创建环境文件。 如果接受 localhost 默认值在本地进行测试和调试，则不需要声明环境变量。 

* 一个 **deployment.template.json** 文件，列出新模块以及模拟可用于测试的数据的示例 **tempSensor** 模块。 若要详细了解部署清单的工作原理，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

## <a name="develop-your-module"></a>开发模块

解决方案附带的默认 Node.js 代码位于模块 > [你的模块名称] > app.js。 设置模块和 deployment.template.json 文件，以便可以生成解决方案，将其推送到容器注册表，然后部署到设备以开始测试而无需触及任何代码。 该模块构建为只需从源（在此示例中，为模拟数据的 tempSensor 模块）获取输入并通过管道将其传送到 IoT Hub。 

当你准备使用自己的代码自定义 Node.js 模板时，请使用 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以满足 IoT 解决方案的关键需求（例如安全性、设备管理和可靠性）。 

Visual Studio Code 支持 Node.js. 详细了解[如何在 VS Code 中使用 Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-tutorial)。

## <a name="launch-and-debug-module-code-without-container"></a>在没有容器的情况下，启动和调试模块代码

IoT Edge Node.js 模块依赖于 Azure IoT Node.js 设备 SDK。 在默认的模块代码中，使用环境设置和输入名称初始化 ModuleClient，这意味着 IoT Edge Node.js 模块需要启动和运行环境设置，并且还需要将消息发送或路由到输入通道。 默认的 Node.js 模块仅包含一个名为 input1 的输入通道。

### <a name="setup-iot-edge-simulator-for-single-module-app"></a>为单个模块应用设置 IoT Edge 模拟器

1. 若要设置和起动模拟器，请在 VS Code 命令面板中，键入并选择“Azure IoT Edge：启动单个模块的 IoT Edge 中心模拟器”。 此外，还需要为单个模块应用程序指定输入名称，键入 input1 并按 Enter。 该命令将触发 iotedgehubdev CLI 并启动 IoT Edge 模拟器并测试实用程序模块容器。 如果模拟器已成功以单模块模式启动，则可以在集成终端中看到下面的输出。 还可以看到 `curl` 命令以帮助发送消息。 稍后将使用它。

   ![为单个模块应用设置 IoT Edge 模拟器](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   可以移动到 Docker Explorer 并查看模块运行状态。

   ![模拟器模块状态](media/how-to-develop-csharp-module/simulator-status.png)

   edgeHubDev 容器是本地 IoT Edge 模拟器的核心。 它可以在没有 IoT Edge 安全守护程序的情况下在开发计算机上运行，并为本机模块应用或模块容器提供环境设置。 input 容器公开 restAPI 以帮助将消息桥接到模块上的目标输入通道。

2. 在 VS Code 命令面板中，键入并选择“Azure IoT Edge：将模块凭据设置为用户设置”，以在用户设置中将模块环境设置设为 `azure-iot-edge.EdgeHubConnectionString` 和 `azure-iot-edge.EdgeModuleCACertificateFile`。 可以在 .vscode  >  launch.json 和[ VS Code 用户设置](https://code.visualstudio.com/docs/getstarted/settings)中找到这些环境设置。

### <a name="debug-nodejs-module-in-launch-mode"></a>在启动模式下调试 Node.js 模块

1. 在集成终端中，将目录更改为 **NodeModule** 文件夹，运行以下命令以安装 Node 包

   ```cmd
   npm install
   ```

2. 导航到 `app.js`。 在此文件中添加一个断点。

3. 导航到 VS Code 调试视图。 选择调试配置 ModuleName 本地调试 (Node.js)。 

4. 单击“开始调试”或按 F5。 将启动调试会话。

5. 在 VS Code 集成终端中，运行以下命令向模块发送 Hello World 消息。 这是在前面的步骤中成功设置 IoT Edge 模拟器时所显示的命令。 如果当前的终端被阻止，可能需要创建或切换到另一个集成终端。

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > 如果使用的是 Windows，请确保 VS Code 集成终端的 shell 为 Git Bash 或 WSL Bash。 无法在 PowerShell 中或命令提示符中运行 `curl` 命令。 
   
   > [!TIP]
   > 还可以使用 [PostMan](https://www.getpostman.com/) 或其他 API 工具（而不是 `curl`）来发送消息。

6. 在 VS Code 调试视图中，将在左侧面板中看到变量。 

7. 若要停止调试会话，请单击“停止”按钮或按 Shift + F5。 在 VS Code 命令面板中，键入并选择“Azure IoT Edge: 停止 IoT Edge 模拟器”以停止并清理模拟器。


## <a name="build-module-container-for-debugging-and-debug-in-attach-mode"></a>生成用于调试的模块容器并在附加模式下进行调试

默认解决方案包含两个模块，一个是模拟的温度传感器模块，另一个是 Node.js 管道模块。 模拟的温度传感器不断向 Node.js 管道模块发送消息，然后将消息通过管道传送到 IoT 中心。 在创建的模块文件夹中，有适用于不同容器类型的多个 Docker 文件。 使用以扩展名 .debug 结尾的任何文件来生成用于测试的模块。 目前，Node.js 模块仅支持在 linux-amd64、windows-amd64 和 linux-arm32v7 容器中进行调试。

### <a name="setup-iot-edge-simulator-for-iot-edge-solution"></a>为 IoT Edge 解决方案设置 IoT Edge 模拟器

在开发计算机中，可以启动 IoT Edge 模拟器（而不是安装 IoT Edge 安全守护程序）以运行 IoT Edge 解决方案。 

1. 在左侧的设备资源管理器中，右键单击 IoT Edge 设备 ID，选择“设置 IoT Edge 模拟器”以使用设备连接字符串启动模拟器。

2. 可以看到 IoT Edge 模拟器已在集成终端中成功设置。

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>生成和运行用于调试的容器并在附加模式下进行调试

1. 在 VS Code 中，导航到 `deployment.template.json` 文件。 通过在末尾添加 .debug 来更新模块映像 URL。

2. 将 **deployment.template.json** 中的 Node.js 模块 createOptions 替换为以下内容并保存此文件： 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

3. 导航到 VS Code 调试视图。 选择模块的调试配置文件。 调试选项名称应类似于“ModuleName 远程调试(Node.js)”或“ModuleName 远程调试(Windows 容器中的 Node.js)”，这取决于开发计算机上的容器类型。

4. 选择“开始调试”或选择 F5。 选择要附加到的进程。

5. 在 VS Code 调试视图中，将在左侧面板中看到变量。

6. 若要停止调试会话，请单击“停止”按钮或按 Shift + F5。 在 VS Code 命令面板中，键入并选择“Azure IoT Edge：停止 IoT Edge 模拟器”。

> [!NOTE]
> 上面的示例展示了如何调试容器上的 Node.js IoT Edge 模块。 它在模块容器 createOptions 中添加了公开的端口。 完成 Node.js 模块的调试后，建议你为就绪可用于生产的 IoT Edge 模块删除那些公开的端口。

## <a name="next-steps"></a>后续步骤

生成模块后，了解如何[从 Visual Studio Code 部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)

若要开发用于 IoT Edge 设备的模块，请参阅[了解并使用 Azure IoT 中心 SDK](../iot-hub/iot-hub-devguide-sdks.md)。
