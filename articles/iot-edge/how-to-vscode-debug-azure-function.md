---
title: "使用 Visual Studio Code 通过 Azure IoT Edge 调试 Azure Functions | Microsoft Docs"
description: "在 VS Code 中使用 Azure IoT Edge 调试 C# Azure Functions"
services: iot-edge
keywords: 
author: shizn
manager: timlt
ms.author: xshi
ms.date: 12/20/2017
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: db86a08a19e97f8f415849aa060fe87d77cccf68
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="use-visual-studio-code-to-debug-azure-functions-with-azure-iot-edge"></a>使用 Visual Studio Code 通过 Azure IoT Edge 调试 Azure Functions

本文提供有关使用 [Visual Studio Code](https://code.visualstudio.com/) 作为主要开发工具在 IoT Edge 上调试 Azure Functions 的详细说明。

## <a name="prerequisites"></a>先决条件
本教程假设使用运行 Windows 或 Linux 的计算机或虚拟机作为开发计算机。 IoT Edge 设备可以是另一个物理设备，或者，你可以在开发计算机上模拟 IoT Edge 设备。

在开始学习本指南之前，请确保已完成了下列教程。
- [使用 Visual Studio Code 开发 Azure Functions 并将其部署到 Azure IoT Edge](how-to-vscode-develop-azure-function.md)

完成前面的教程后，你应该已准备好了下列项：
- 在开发计算机上运行的一个本地 Docker 注册表。 对于原型和测试用途，建议使用本地 Docker 注册表。
- 包含最新筛选器函数代码的 `run.csx` 文件。
- 适用于传感器模块和筛选器函数模块的已更新 `deployment.json` 文件。
- 在开发计算机上运行的 Edge 运行时。

## <a name="build-your-iot-edge-module-for-debugging-purpose"></a>生成用于调试的 IoT Edge 模块
1. 若要开始调试，需要使用 **dockerfile.debug** 来重新生成 docker 映像并重新部署 Edge 解决方案。 在 VS Code 资源管理器中，单击“Docker”文件夹以将其打开。 然后，单击 `linux-x64` 文件夹，右键单击 **Dockerfile.debug** 并单击“生成 IoT Edge 模块 Docker 映像”。

    ![生成调试映像](./media/how-to-debug-csharp-function/build-debug-image.png)

2. 在“选择文件夹”窗口中，导航到“FilterFunction”项目，然后单击“选择文件夹作为 EXE_DIR”。
3. 在 VS Code 窗口顶部弹出的文本框中，输入映像名称。 例如：`<your container registry address>/filterfunction:latest`。 若要部署到本地注册表，则它应为 `localhost:5000/filterfunction:latest`。

    ![推送映像](./media/how-to-debug-csharp-function/push-image.png)

4. 将映像推送到 Docker 存储库。 使用 **Edge: Push IoT Edge module Docker image** 命令，并在 VS Code 窗口顶部的弹出文本框中输入映像 URL。 使用上一步骤中所用的同一映像 URL。
5. 可以重复使用 `deployment.json` 进行重新部署。 在命令面板中，键入并选择 **Edge: Restart Edge** 来使筛选器函数以调试版本运行。

## <a name="start-debugging-in-vs-code"></a>在 VS Code 中启动调试
1. 转到 VS Code 调试窗口。 按 **F5** 并选择“IoT Edge(.Net Core)”

    ![按 F5](./media/how-to-debug-csharp-function/f5-debug-option.png)

2. 在 `launch.json` 中，导航到 **Debug IoT Edge Function (.NET Core)** 部分并在 `pipeArgs` 下填写 `<container_name>`。在本教程中，它应当是 `filterfunction`。

    ![更新 launch.json](./media/how-to-debug-csharp-function/update-launch-json.png)

3. 导航到 run.csx。 在函数中添加一个断点。
4. 导航到“调试”窗口 (Ctrl + Shift + D)，从下拉列表中选择“调试 IoT Edge 函数 (.NET Core)”。 

    ![选择“调试模式”](./media/how-to-debug-csharp-function/choose-debug-mode.png)

5. 单击“开始调试”按钮或按 **F5**，然后选择要附加到的进程。

    ![附加函数进程](./media/how-to-debug-csharp-function/attach-function-process.png)

6. 在 VS Code 调试窗口中，在左侧面板中可以看到变量。 

> [!NOTE]
> 上面的示例展示了如何调试容器上的 .Net Core IoT Edge 函数。 它基于 `Dockerfile.debug` 的调试版本，在生成容器映像时这将在容器映像中包括 VSDBG（.NET Core 命令行调试器）。 对于生产就绪的 IoT Edge 函数，建议在调试完 C# 函数后在不使用 VSDBG 的情况下直接使用或自定义 `Dockerfile`。

## <a name="next-steps"></a>后续步骤

在本教程中，你创建了一个 Azure Function 并将其部署到 IoT Edge 以用于调试，然后在 VS Code 中对其启动了调试。 接下来可以继续学习下列教程之一来了解在 VS Code 中开发 Azure IoT Edge 时的其他方案。 

> [!div class="nextstepaction"]
> [在 VS Code 中开发和部署 C# 模块](how-to-vscode-develop-csharp-module.md)

