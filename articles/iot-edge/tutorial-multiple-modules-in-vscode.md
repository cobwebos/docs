---
title: 在 VS Code 中管理多个 Azure IoT Edge 模块 | Microsoft 文档
description: 使用 Visual Studio Code 开发使用多个模块的 IoT Edge 解决方案。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 4d9caa7aa95c99fa30e8ec76c5b6362615725622
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34166346"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code---preview"></a>使用 Visual Studio Code 中的多个模块开发 IoT Edge 解决方案 - 预览版
可以使用 Visual Studio Code 中的多个模块开发 IoT Edge 解决方案 本文将指导你完成创建、更新和部署一个 IoT Edge 解决方案，该解决方案在 Visual Studio Code 中的模拟 IoT Edge 设备上通过管道传送传感器数据。 

## <a name="prerequisites"></a>先决条件

若要完成本文中的所有步骤，需具备以下先决条件：

- [Visual Studio Code](https://code.visualstudio.com/) 
- [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
- [适用于 Visual Studio Code 的 C# 扩展（由 OmniSharp 提供支持）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
- AzureIoTEdgeModule 模板 (`dotnet new -i Microsoft.Azure.IoT.Edge.Module`)
- 具有至少一个 IoT Edge 设备的有效 IoT 中心


* 用于管理映像和容器的集成了资源管理器的 [Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)。


## <a name="prepare-your-first-iot-edge-solution"></a>准备你的第一个 IoT Edge 解决方案
1. 在 VS Code 命令面板中，键入并运行命令 **Edge: New IoT Edge solution**。 然后，选择你的工作区文件夹，提供解决方案名称（默认名称为 **EdgeSolution**），并创建一个 C# 模块 (**SampleModule**) 作为此解决方案中的第一个用户模块。 还需要为你的第一个模块指定 Docker 映像存储库。 默认映像存储库基于本地 Docker 注册表 (`localhost:5000/<first module name>`)。 还可以将其更改为 Azure 容器注册表或 Docker 中心。

   > [!NOTE]
   > 如果使用的是本地 Docker 注册表，请通过在控制台窗口中键入命令 `docker run -d -p 5000:5000 --restart=always --name registry registry:2` 来确保注册表正在运行。

2. VS Code 窗口将加载你的 IoT Edge 解决方案空间。 根文件夹包含一个 `modules` 文件夹、一个 `.vscode` 文件夹和一个部署清单模板文件。 可以在 `.vscode` 文件夹中查看调试配置。 所有用户模块代码都将成为文件夹 `modules` 下的子文件夹。 `deployment.template.json` 是部署清单模板。 此文件中的某些参数将通过 `module.json` 进行分析，它存在于每个模块文件夹中。

3. 向此解决方案项目中添加第二个模块。 这次键入并运行 **Edge: Add IoT Edge module** 并选择要更新的部署模板文件。 然后，选择名为 SampleFunction 的 Azure 函数 - C# 及其 Docker 映像存储库。

4. 打开 `deployment.template.json` 文件，并确认它除了运行时之外还声明三个模块。 消息将从 `tempSensor` 模块生成，并且将通过 `SampleModule` 和 `SampleFunction` 直接输送，然后发送到 IoT 中心。 
5. 使用以下内容更新这些模块的路由：
   ```json
   "routes": {
      "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/SampleModule/inputs/input1\")",
      "PipeModuleToPipeFunction": "FROM /messages/modules/SampleModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/SampleFunction/inputs/input1\")",
      "PipeFunctionToIoTHub": "FROM /messages/modules/SampleFunction/outputs/output1 INTO $upstream"
   },
   ```

6. 保存此文件。

## <a name="build-and-deploy-your-iot-edge-solution"></a>生成并部署 IoT Edge 解决方案
1. 在 VS Code 命令面板中，键入并运行命令 **Edge: Build IoT Edge solution**。 根据每个模块文件夹中的 `module.json` 文件，此命令将开始生成、收容和推送每个模块 Docker 映像。 然后，它将所需的值传递到 `deployment.template.json`，并使用 `config` 文件夹中的信息生成 `deployment.json` 文件。 可以查看在 VS Code 集成终端中查看生成进度。

2. 在 Azure IoT 中心设备资源管理器中，右键单击 IoT Edge 设备 ID，然后选择“为 Edge 设备创建部署”。 选择 `config` 文件夹下的 `deployment.json`。 然后可以在 VS Code 集成终端中看到部署已成功创建且具有一个部署 ID。

3. 如果你正在开发计算机上模拟 IoT Edge 设备，会看到所有模块映像容器将在几分钟内启动。

## <a name="view-generated-data"></a>查看生成的数据

1. 要监视到达 IoT 中心的数据，请选择“视图” > “命令面板...”，然后搜索“IoT: 开始监视 D2C 消息”。 
2. 要停止监视数据，请在命令面板中使用“IoT: Stop monitoring D2C message”命令。 

## <a name="next-steps"></a>后续步骤

了解在 Visual Studio Code 中开发 Azure IoT Edge 的其他方案：

* [在 VS Code 中调试 C# 模块](how-to-vscode-debug-csharp-module.md)
* [在 VS Code 中调试 C# 函数](how-to-vscode-debug-azure-function.md)