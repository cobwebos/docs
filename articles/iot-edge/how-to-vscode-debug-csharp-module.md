---
title: 使用 Visual Studio Code 通过 Azure IoT Edge 调试 C# 模块 | Microsoft Docs
description: 在 Visual Studio Code 中使用 Azure IoT Edge 调试 C# 模块。
services: iot-edge
keywords: ''
author: shizn
manager: timlt
ms.author: xshi
ms.date: 03/18/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 65f2fb4526f1048ae88193f85a552a2202afa1d9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="use-visual-studio-code-to-debug-a-c-module-with-azure-iot-edge"></a>使用 Visual Studio Code 通过 Azure IoT Edge 调试 C# 模块
本文提供了有关使用 [Visual Studio Code](https://code.visualstudio.com/) 作为主要开发工具来调试 Azure IoT Edge 模块的详细说明。

## <a name="prerequisites"></a>先决条件
本文假设使用运行 Windows 或 Linux 的计算机或虚拟机作为开发计算机。 IoT Edge 设备可以是另一个物理设备，也可以在开发计算机上模拟 IoT Edge 设备。

在按照本文中的指导进行操作之前，请先完成[使用 Visual Studio Code 中的多个模块开发 IoT Edge 解决方案](tutorial-multiple-modules-in-vscode.md)中的步骤。 然后，应准备好以下项：
- 在开发计算机上运行的一个本地 Docker 注册表。 对于原型和测试用途，建议使用本地 Docker 注册表。 可以在每个模块文件夹的 `module.json` 文件中更新容器注册表。
- 包含 C# 模块子文件夹的 IoT Edge 解决方案项目工作区。
- 包含最新模块代码的 `Program.cs` 文件。
- 在开发计算机上运行的 Edge 运行时。

## <a name="build-your-iot-edge-c-module-for-debugging"></a>生成用于调试的 IoT Edge C# 模块
1. 要开始调试，需要使用 Dockerfile.amd64.debug 来重新生成 docker 映像并重新部署 Edge 解决方案。 在 VS Code 资源管理器中，导航到 `deployment.template.json` 文件。 通过将 `.debug` 添加到末尾更新函数映像 URL。

2. 重新生成解决方案。 在 VS Code 命令面板中，键入并运行命令：Edge: Build IoT Edge solution。

3. 在 Azure IoT 中心设备资源管理器中，右键单击 IoT Edge 设备 ID，然后选择“为 Edge 设备创建部署”。 选择 `config` 文件夹下的 `deployment.json`。 然后可以在 VS Code 集成终端中看到部署已成功创建且具有一个部署 ID。

> [!NOTE]
> 可在 VS Code Docker 资源管理器中检查容器状态，也可通过终端运行 `docker images` 命令进行检查。

## <a name="start-debugging-c-module-in-vs-code"></a>开始在 VS Code 中调试 C# 模块
1. VS Code 将调试配置信息保存在 `launch.json` 文件中，该文件位于工作区的 `.vscode` 文件夹中。 新建 IoT Edge 解决方案时就会生成此 `launch.json` 文件。 每次添加支持调试的新模块时，它都会随之更新。 导航至调试视图，并选择对应的调试配置文件。
    ![选择调试配置](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. 导航到 `program.cs`。 在此文件中添加一个断点。

3. 单击“开始调试”按钮或按 **F5**，然后选择要附加到的进程。

4. 在 VS Code 调试视图中，在左侧面板中可以看到变量。 

> [!NOTE]
> 上面的示例展示了如何调试容器上的 .NET Core IoT Edge 模块。 它基于 `Dockerfile.debug` 的调试版本，在生成容器映像时这将在容器映像中包括 VSDBG（.NET Core 命令行调试器）。 对于生产就绪的 IoT Edge 模块，建议在调试完 C# 模块后在不使用 VSDBG 的情况下直接使用或自定义 `Dockerfile`。

## <a name="next-steps"></a>后续步骤

[使用 Visual Studio Code 通过 Azure IoT Edge 调试 Azure Functions](how-to-vscode-debug-azure-function.md)

