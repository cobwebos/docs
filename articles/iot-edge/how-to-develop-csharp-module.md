---
title: 调试 Azure IoT Edge 的 C# 模块 | Microsoft Docs
description: 使用 Visual Studio Code 开发、生成和调试 Azure IoT Edge 的 C# 模块
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 92e476d38e6e56edca19afe78bed2705feadd0bb
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39040902"
---
# <a name="use-visual-studio-code-to-develop-and-debug-c-modules-for-azure-iot-edge"></a>使用 Visual Studio Code 开发和调试 Azure IoT Edge 的 C# 模块

可以将业务逻辑转变为用于 Azure IoT Edge 的模块。 本文展示了如何使用 Visual Studio Code (VS Code) 作为主要工具来开发和调试 C# 模块。

## <a name="prerequisites"></a>先决条件
本文假设使用运行 Windows 或 Linux 的计算机或虚拟机作为开发计算机。 IoT Edge 设备可以是另一台物理设备。 或者，可以在开发计算机上模拟 IoT Edge 设备。

> [!NOTE]
> 本调试文章演示了如何在模块容器中附加进程并使用 VS Code 对它进行调试。 只能调试 linux-amd64 容器中的 C# 函数。 如果你不熟悉 Visual Studio Code 的调试功能，请阅读有关[调试](https://code.visualstudio.com/Docs/editor/debugging)的信息。 

因为本文使用 Visual Studio Code 作为主要开发工具，所以请安装 VS Code。 然后添加必要的扩展：
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker 扩展](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

要创建一个模块，需要生成项目文件夹的 .NET，生成模块映像的 Docker 以及用来保存模块映像的容器注册表：
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)。
* 开发计算机上的 [Docker 社区版](https://docs.docker.com/install/) 
* [Azure 容器注册表](https://docs.microsoft.com/azure/container-registry/)或 [Docker 中心](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

   > [!TIP]
   > 对于原型和测试用途，可以使用本地 Docker 注册表，而不使用云注册表。 

若要在设备上测试模块，你至少需要一个 IoT Edge 设备和一个活动的 IoT 中心。 若要将计算机用作 IoT Edge 设备，请按照 [Windows](quickstart.md) 或 [Linux](quickstart-linux.md) 快速入门中的步骤操作。 

## <a name="create-a-new-solution-template"></a>创建新的解决方案模板

执行以下步骤来使用 Visual Studio Code 和 Azure IoT Edge 扩展创建基于 .NET Core 2.0 的 IoT Edge 模块。 首先创建一个解决方案，然后生成该解决方案中的第一个模块。 每个解决方案可以包含多个模块。 

1. 在 Visual Studio Code 中，选择“视图” > “集成终端”。
3. 选择“视图” > “命令面板”。 
4. 在命令面板中，输入并运行“Azure IoT Edge: 新建 IoT Edge 解决方案”命令。

   ![运行新的 IoT Edge 解决方案](./media/how-to-develop-csharp-module/new-solution.png)

5. 浏览到要创建新解决方案的文件夹。 选择“选择文件夹”。 
6. 输入解决方案的名称。 
7. 选择“C# 模块”作为解决方案中第一个模块的模板。
8. 输入模块的名称。 选择一个在容器注册表中唯一的名称。 
9. 提供模块的映像存储库的名称。 VS Code 使用 **localhost:5000** 自动填充模块名称。 将其替换为你自己的注册表信息。 如果使用本地 Docker 注册表进行测试，则可以使用 **localhost**。 如果使用 Azure 容器注册表，那么请从注册表的设置中使用登录服务器。 登录服务器如下所示：\<registry name\>.azurecr.io。

VS Code 采用你提供的信息，创建一个 IoT Edge 解决方案，然后在新窗口中加载它。

   ![查看 IoT Edge 解决方案](./media/how-to-develop-csharp-module/view-solution.png)

该解决方案中有四个项： 
* 一个 **.vscode** 文件夹，包含调试配置。
* 一个 **modules** 文件夹，包含每个模块的子文件夹。 现在，只有一个模块。 但是可以在命令面板中使用“Azure IoT Edge: Add IoT Edge Module”命令添加更多模块。 
* 一个 **.env** 文件，列出环境变量。 如果 Azure 容器注册表是你的注册表，则其中将包含 Azure 容器注册表用户名和密码。 

   >[!NOTE]
   >仅当为模块提供了映像存储库时，才会创建环境文件。 如果接受 localhost 默认值在本地进行测试和调试，则不需要声明环境变量。 

* 一个 **deployment.template.json** 文件，列出新模块以及模拟可用于测试的数据的示例 **tempSensor** 模块。 有关部署清单如何工作的详细信息，请参阅[了解如何使用部署清单部署模块和建立路由](module-composition.md)。 

## <a name="devlop-your-module"></a>开发模块

解决方案附带的默认 Azure 函数代码位于**模块** > **\<你的模块名称\>** > **Program.cs**。 设置模块和 deployment.template.json 文件，以便可以生成解决方案，将其推送到容器注册表，然后将其部署到设备以开始测试而无需触及任何代码。 该模块构建为只需从源（在此示例中，为模拟数据的 tempSensor 模块）获取输入并通过管道将其传送到 IoT Hub。 

当你准备使用自己的代码自定义 C# 模板时，请使用 [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) 生成模块，以满足 IoT 解决方案的关键需求（例如安全性、设备管理和可靠性）。 

## <a name="build-and-deploy-your-module-for-debugging"></a>生成并部署模块以便调试

每个模块文件夹中都有适用于不同容器类型的多个 Docker 文件。 使用任何以扩展名 **.debug** 结尾的文件来生成用于测试的模块。 目前，C# 模块仅支持在 Linux amd64 容器中进行调试。

1. 在 VS Code 中，导航到 `deployment.template.json` 文件。 通过将 .debug 添加到末尾来更新函数映像 URL。

   ![将 **.debug** 添加到映像名称](./media/how-to-develop-csharp-module/image-debug.png)

2. 在 VS Code 命令面板中，输入并运行命令“Edge: 生成 IoT Edge 解决方案”。
3. 从命令面板中，选择你的解决方案的 `deployment.template.json` 文件。 
4. 在 Azure IoT 中心 Device Explorer 中，右键单击某个 IoT Edge 设备 ID。 然后选择“创建 IoT Edge 设备的部署”。 
5. 打开你的解决方案的 **config** 文件夹。 然后选择 `deployment.json` 文件。 选择“选择 Edge 部署清单”。 

将在 VS Code 集成终端中看到部署已成功创建且具有一个部署 ID。

在 VS Code Docker 资源管理器中检查容器状态，也可通过终端运行 `docker ps` 命令进行检查。

## <a name="start-debugging-c-module-in-vs-code"></a>开始在 VS Code 中调试 C# 模块
VS Code 将调试配置信息保存在 `launch.json` 文件中，该文件位于工作区的 `.vscode` 文件夹中。 新建 IoT Edge 解决方案时就会生成此 `launch.json` 文件。 每次添加支持调试的新模块时，它都会随之更新。 

1. 导航到 VS Code 调试视图。 选择你的模块的调试配置文件。 调试选项名称应类似于“ModuleName 远程调试(.NET Core)”

   ![选择调试配置](./media/how-to-develop-csharp-module/debug-config.png)。

2. 导航到 `program.cs`。 在此文件中添加一个断点。

3. 选择“开始调试”或选择 **F5**。 选择要附加到的进程。

4. 在 VS Code 调试视图中，将在左侧面板中看到变量。 

> [!NOTE]
> 此示例展示了如何调试容器上的 .NET Core IoT Edge 模块。 它基于 `Dockerfile.debug` 的调试版本，在生成容器映像时这将在容器映像中包括 .NET Core 命令行调试器 VSDBG。 对于生产就绪的 IoT Edge 模块，建议在调试完 C# 模块后在不使用 VSDBG 的情况下直接使用或自定义 `Dockerfile`。

## <a name="next-steps"></a>后续步骤

生成模块后，了解如何[从 Visual Studio Code 部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)。

若要开发用于 IoT Edge 设备的模块，请参阅[了解并使用 Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md)。
