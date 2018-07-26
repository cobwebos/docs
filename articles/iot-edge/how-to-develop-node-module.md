---
title: 调试用于 Azure IoT Edge 的 Node.js 模块 | Microsoft Docs
description: 使用 Visual Studio Code 开发和调试用于 Azure IoT Edge 的 Node.js 模块
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/26/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8032fd2a0150597c55178648511c80233e63a911
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054720"
---
# <a name="develop-and-debug-nodejs-modules-with-azure-iot-edge-for-visual-studio-code"></a>使用 Visual Studio Code 开发和调试用于 Azure IoT Edge 的 Node.js 模块

可以发送业务逻辑，通过将其转换为用于 Azure IoT Edge 的模块来在边缘进行操作。 本文提供了有关使用 Visual Studio Code (VS Code) 作为主要开发工具来开发 C# 模块的详细说明。

## <a name="prerequisites"></a>先决条件
本文假设使用运行 Windows 或 Linux 的计算机或虚拟机作为开发计算机。 IoT Edge 设备可以是另一个物理设备，也可以在开发计算机上模拟 IoT Edge 设备。

> [!NOTE]
> 本调试教程介绍如何在模块容器中附加进程并使用 VS Code 对它进行调试。 可以调试 linux-amd64、windows 和 arm32 容器中的 Node.js 模块。 如果你不熟悉 Visual Studio Code 的调试功能，请阅读有关[调试](https://code.visualstudio.com/Docs/editor/debugging)的信息。 

由于本文使用 Visual Studio Code 作为主要开发工具，因此，请安装 VS Code，然后添加必要的扩展：
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [Docker 扩展](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

若要创建模块，需要采用一个 Node.js，并在其中包括用来生成项目文件夹的 npm、用来生成模块映像的 Docker 以及用来存放模块映像的容器注册表：
* [Node.js](https://nodejs.org)
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   >[!TIP]
   >对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。 

若要在设备上测试模块，你至少需要一个 IoT Edge 设备和一个活动的 IoT 中心。 如果想要将计算机用作 IoT Edge 设备，可以按照适用于 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 的教程中的步骤进行操作。 

## <a name="create-a-new-solution-template"></a>创建新的解决方案模板

以下步骤介绍了如何使用 Visual Studio Code 和 Azure IoT Edge 扩展来创建基于 .NET Core 2.0 的 IoT Edge 模块。 首先创建一个解决方案，然后生成该解决方案中的第一个模块。 每个解决方案可以包含多个模块。 

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
9. 为模块提供映像存储库。 VS Code 会自动填充模块名称，因此，只需将 **localhost:5000** 替换为你自己的注册表信息。 如果使用本地 Docker 注册表进行测试，那么可以使用 localhost。 如果使用 Azure 容器注册表，那么请从注册表的设置中使用登录服务器。 登录服务器如下所示：\<registry name\>.azurecr.io。

VS Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。

在解决方案中，你将具有以下三项： 
* 一个 **.vscode** 文件夹，包含调试配置。
* 一个 **modules** 文件夹，包含每个模块的子文件夹。 现在，你只有一个模块，但是可以使用命令“Azure IoT Edge: Add IoT Edge Module”在命令面板中添加更多模块。 
* 一个 **.env** 文件，列出环境变量。 如果将 ACR 作为注册表，那么，注册表中会有 ACR 用户名和密码。 

   >[!NOTE]
   >仅当为模块提供映像存储库时，才会创建环境文件。 如果接受 localhost 默认值在本地进行测试和调试，则不需要声明环境变量。 

* 一个 **deployment.template.json** 文件，列出新模块以及模拟可用于测试的数据的示例 **tempSensor** 模块。 若要详细了解部署清单的工作原理，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

## <a name="devlop-your-module"></a>开发模块

解决方案附带的默认 Azure 函数代码位于**模块** > **\<你的模块名称\>** > **app.js**。 设置模块和 deployment.template.json 文件，以便可以生成解决方案，将其推送到容器注册表，然后将其部署到设备以开始测试而无需触及任何代码。 该模块构建为只需从源（在此示例中，为模拟数据的 tempSensor 模块）获取输入并通过管道将其传送到 IoT Hub。 

当你准备使用自己的代码自定义 Node.js 模板时，请使用 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以满足 IoT 解决方案的关键需求（例如安全性、设备管理和可靠性）。 

## <a name="build-and-deploy-your-module-for-debugging"></a>生成并部署模块以便调试

每个模块文件夹中都有适用于不同容器类型的多个 Docker 文件。 可以使用任何以扩展名 **.debug** 结尾的文件来生成用于测试的模块。 目前，C# 模块仅支持在 linux-amd64 容器中进行调试。

1. 在 VS Code 中，导航到 `deployment.template.json` 文件。 将 **deployment.template.json** 中的 Node.js 模块 createOptions 替换为以下内容并保存此文件： 
    ```json
    "createOptions": "{\"ExposedPorts\":{\"9229/tcp\":{}},\"HostConfig\":{\"PortBindings\":{\"9229/tcp\":[{\"HostPort\":\"9229\"}]}}}"
    ```

2. 在 VS Code 命令面板中，键入并运行命令“Azure IoT Edge: Build IoT Edge solution”。
3. 从命令面板中，选择你的解决方案的 `deployment.template.json` 文件。 
4. 在 Azure IoT 中心设备资源管理器中，右键单击 IoT Edge 设备 ID，然后选择“为 IoT Edge 设备创建部署”。 
5. 打开解决方案的 **config** 文件夹，然后选择 `deployment.json` 文件。 单击“选择 Edge 部署清单”。 

然后可以在 VS Code 集成终端中看到部署已成功创建且具有一个部署 ID。

可在 VS Code Docker 资源管理器中检查容器状态，也可通过终端运行 `docker ps` 命令进行检查。

## <a name="start-debugging-nodejs-module-in-vs-code"></a>开始在 VS Code 中调试 Node.Js 模块

VS Code 将调试配置信息保存在 `launch.json` 文件中，该文件位于工作区的 `.vscode` 文件夹中。 新建 IoT Edge 解决方案时就会生成此 `launch.json` 文件。 每次添加支持调试的新模块时，它都会随之更新。 

1. 导航至 VS Code 调试视图，并选择模块的调试配置文件。

2. 导航到 `app.js`。 在此文件中添加一个断点。

3. 单击“开始调试”按钮或按 F5，然后选择要附加到的进程。

4. 在 VS Code 调试视图中，在左侧面板中可以看到变量。 

上面的示例展示了如何调试容器上的 Node.js IoT Edge 模块。 它在模块容器 createOptions 中添加了公开的端口。 完成 Node.js 模块的调试后，建议你为就绪可用于生产的 IoT Edge 模块删除那些公开的端口。

## <a name="next-steps"></a>后续步骤

生成模块后，了解如何[从 Visual Studio Code 部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)

若要为 IoT Edge 设备开发模块，请参阅[了解并使用 Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)。
