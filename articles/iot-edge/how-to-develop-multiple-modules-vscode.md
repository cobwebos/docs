---
title: 在 VS Code 中处理多个 Azure IoT Edge 模块 | Microsoft Docs
description: 使用 Visual Studio Code 的 IoT 扩展为 Azure IoT Edge 同时开发多个模块
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: 4e9aac5f19fa75613dee2aba3853a0243d7d966b
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048254"
---
# <a name="develop-an-iot-edge-solution-with-multiple-modules-in-visual-studio-code"></a>在 Visual Studio Code 中使用多个模块开发 IoT Edge 解决方案

可以使用 Visual Studio Code 中的多个模块开发 Azure IoT Edge 解决方案。 本文介绍如何创建、更新和部署 IoT Edge 解决方案，以便在 VS Code 中的模拟 IoT Edge 设备上通过管道传送传感器数据。 

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需具备以下先决条件：

- [Visual Studio Code](https://code.visualstudio.com/)
- [适用于 Visual Studio Code 的 Azure IoT Edge 扩展](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)
- [适用于 Visual Studio Code 的 C#（由 OmniSharp 提供支持）扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)
- [Docker](https://docs.docker.com/engine/installation/)
- [.NET Core 2.1 SDK](https://www.microsoft.com/net/download)
- 具有至少一个 IoT Edge 设备的有效 IoT 中心

此外还需将 [Docker for VS Code](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker) 与 Azure IoT 中心设备资源管理器集成配合使用，以便管理映像和容器。

## <a name="create-your-iot-edge-solution"></a>创建 IoT Edge 解决方案

1. 在 Visual Studio Code 中，依次选择“视图” > “集成终端”，打开集成终端。 

1. 在 VS Code 命令面板中，输入并运行“Azure IoT Edge: 新建 IoT Edge 解决方案”命令。 选择工作区文件夹，提供解决方案名称（默认为 EdgeSolution）。 创建一个 C# 模块（名为 PipeModule）作为此解决方案中的第一个用户模块。 C# 模块的默认模板为管道模板，此模板会将消息直接从上游传送至下游。 还需要为你的第一个模块指定 Docker 映像存储库。 默认映像存储库基于本地 Docker 注册表 (**localhost:5000/<first module name>**)。 可以将其更改为 Azure 容器注册表或 Docker 中心。 

2. VS Code 窗口将加载你的 IoT Edge 解决方案空间。 根文件夹包含一个 **modules** 文件夹、一个 **.vscode** 文件夹和一个部署清单模板文件。 调试配置位于 .vscode 文件夹中。 所有用户模块代码都是 modules 文件夹的子文件夹。 deployment.template.json 文件是部署清单模板。 此文件中的某些参数将通过 module.json 文件进行分析，该文件存在于每个模块文件夹中。

3. 向此解决方案项目添加第二个模块。 有多种方法可以将新模块添加到当前解决方案。 输入并运行命令“Azure IoT Edge: 添加 IoT Edge 模块”。 选择要更新的部署模板文件。 或者右键单击模块文件夹或右键单击 deployment.template.json 文件并选择“添加 IoT Edge 模块”。 随即会显示一个用于选择模块类型的下拉列表。 选择名为 PipeFunction 的“Azure Functions - C#”模块及其 Docker 映像存储库。 C# 函数模块的默认模板为管道模板，此模板会将消息直接从上游传送至下游。

4. 打开 deployment.template.json 文件。 验证文件是否声明了三个模块和一个运行时。 此消息从 tempSensor 模块生成。 此消息通过 SampleModule 和 SampleFunction 模块进行直接的管道传送，然后发送到 IoT 中心。 

5. 使用以下内容更新这些模块的路由：

   ```json
        "routes": {
          "SensorToPipeModule": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/PipeModule/inputs/input1\")",
          "PipeModuleToPipeFunction": "FROM /messages/modules/PipeModule/outputs/output1 INTO BrokeredEndpoint(\"/modules/PipeFunction/inputs/input1\")",
          "PipeFunctionToIoTHub": "FROM /messages/modules/PipeFunction/outputs/output1 INTO $upstream"
        },
   ```

5. 保存此文件。

## <a name="build-and-deploy-your-iot-edge-solution"></a>生成并部署 IoT Edge 解决方案

1. 在 VS Code 命令面板中，输入并运行“Azure IoT Edge: 生成 IoT Edge 解决方案”命令。 根据每个模块文件夹中的 module.json 文件，此命令将开始生成、收容和推送每个模块 Docker 映像。 然后，此命令将所需的值传递到 deployment.template.json 文件，并使用 config 文件夹中的信息生成 deployment.json 文件。 VS Code 中的集成终端显示生成进度。 

2. 在 Azure IoT 中心设备资源管理器中，右键单击 IoT Edge 设备 ID，然后选择“为 Edge 设备创建部署”命令。 选择 config 文件夹中的 deployment.json 文件。 VS Code 中的集成终端显示部署已成功创建，并且有一个部署 ID。

3. 如果是在开发计算机上模拟 IoT Edge 设备，则会看到所有模块映像容器在数分钟内启动。

## <a name="view-the-generated-data"></a>查看生成的数据

1. 若要监视到达 IoT 中心的数据，请选择“视图” > “命令面板”， 然后搜索“IoT: 开始监视 D2C 消息”命令。 
2. 若要停止监视数据，请在“命令面板”中使用“IoT: 停止监视 D2C 消息”命令。 

## <a name="next-steps"></a>后续步骤

了解在 Visual Studio Code 中使用 Azure IoT Edge 进行开发的其他方案：

* [在 VS Code 中开发 C# 模块](how-to-develop-csharp-module.md)
* [在 VS Code 中开发 C# 函数](how-to-develop-csharp-function.md)
