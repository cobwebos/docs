---
title: 在 VS Code 中调试多个 Azure IoT Edge 模块 | Microsoft Docs
description: 使用 Visual Studio Code 通过 Azure IoT Edge 调试多个模块
author: shizn
manager: ''
ms.author: xshi
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 38c66129ac8244d18b0f94c1485c785015e29ab4
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37049585"
---
# <a name="use-visual-studio-code-to-debug-multiple-modules-with-azure-iot-edge"></a>使用 Visual Studio Code 通过 Azure IoT Edge 调试多个模块
本文详细介绍如何使用 [Visual Studio (VS) Code](https://code.visualstudio.com/) 在 IoT Edge 上调试多个模块。

## <a name="prerequisites"></a>先决条件
完成[使用 Visual Studio Code 中的多个模块开发 IoT Edge 解决方案](tutorial-multiple-modules-in-vscode.md)教程，并确保在 IoT Edge 设备上至少运行两个模块。

## <a name="multi-target-and-remote-debugging-in-vs-code"></a>VS Code 中的多目标和远程调试
通过 VS Code 和 Azure IoT Edge 扩展，可将模块进程附加到容器中，无论容器是在开发计算机上运行还是在远程物理 IoT Edge 设备上运行。 调试容器中运行的多个模块，实际上这是在不同的容器中附加多个进程。 调试多个模块时，可使用 VS Code [多目标调试](https://code.visualstudio.com/docs/editor/debugging#_multitarget-debugging)。

   > [!TIP]
   > 如果模块容器在远程物理 IoT Edge 设备中运行，则可能需要设置 [Docker 计算机](https://docs.docker.com/machine/overview/)，以便开发计算机上的 Docker 引擎可以与远程 Docker 主机通信。

### <a name="build-your-iot-edge-modules-for-debugging-purpose"></a>生成用于调试的 IoT Edge 模块
1. 要开始多模块调试，需要使用 **Dockerfile.amd64.debug** 重新生成 docker 映像并重新部署 Edge 解决方案。 在 VS Code 资源管理器中，导航到 `deployment.template.json` 文件。 通过将 `.debug` 添加到末尾，更新映像 URL。 至少需要两个带 `.debug` 的模块映像。 如果使用上一教程中的解决方案，则应拥有 C# 函数模块和 C# 模块。 通过将 `.debug` 添加到末尾来更新这两个映像 URL，并保存此文件。 
2. 重新生成解决方案。 在 VS Code 命令面板中，键入并运行命令“Azure IoT Edge: Build IoT Edge solution”。
3. 在 Azure IoT 中心设备资源管理器中，右键单击 IoT Edge 设备 ID，然后选择“为 Edge 设备创建部署”。 选择 `config` 文件夹下的 `deployment.json` 文件。 然后可以在 VS Code 集成终端中看到部署已成功创建且具有一个部署 ID。

可在 VS Code Docker 资源管理器中检查容器状态，也可通过终端运行 `docker ps` 命令进行检查。

### <a name="start-debugging-c-function-in-vs-code"></a>开始在 VS Code 中调试 C# 函数
1. VS Code 将调试配置信息保存在 `launch.json` 文件中，该文件位于工作区的 `.vscode` 文件夹中。 新建 IoT Edge 解决方案时就会生成此 `launch.json` 文件。 每次添加支持调试的新模块时，它都会随之更新。 导航至调试视图，并为 C# 函数模块远程调试选择对应的调试配置文件。
2. 导航到 `run.csx`。 在函数中添加一个断点。
3. 单击“开始调试”按钮或按 F5，然后选择要附加到的进程。
4. 在 VS Code 调试视图中，在左侧面板中可以看到变量。 

### <a name="start-debugging-c-module-at-the-same-time-in-vs-code"></a>同时开始在 VS Code 中调试 C# 模块
1. 在 VS Code 命令面板中，键入并运行命令“Workspace: Duplicate Workspace in New Window”。 新的 VS Code 窗口将以相同的工作区开始。
2. 导航至调试视图，并为 C# 模块远程调试选择对应的调试配置文件。
3. 导航到 `program.cs`。 在 C# 模块中添加一个断点。
4. 单击“开始调试”按钮或按 F5，然后选择要附加到的进程。
5. 在 VS Code 调试视图中，在左侧面板中可以看到变量。 

### <a name="see-variables-in-multiple-debugging-windows"></a>查看多个调试窗口中的变量
1. 现在，两个 VS Code 窗口中至少运行了两个调试会话。 应命中其中一个断点。
2. 按 `F10` 或在“调试工具栏”中单击“单步跳过”按钮。
3. 应命中另一个 VS Code 窗口中的断点。 
4. 继续上述两个步骤，可以在多个 VS Code 调试窗口中查看来自多个模块的变量。

> [!NOTE]
> 以上示例演示了如何使用 Azure IoT Edge 调试多个模块。 它基于 `Dockerfile.amd64.debug` 的调试版本，在生成容器映像时这将在容器映像中包括 VSDBG（.NET Core 命令行调试器）。 对于生产就绪的 IoT Edge 函数，建议在调试完 C# 函数后在不使用 VSDBG 的情况下直接使用或自定义 `Dockerfile`。

## <a name="next-steps"></a>后续步骤

生成模块后，了解如何[从 Visual Studio Code 部署 Azure IoT Edge 模块](how-to-deploy-modules-vscode.md)
