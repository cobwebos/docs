---
title: 将 Visual Studio Code Tools for AI 扩展用于 Azure 机器学习
description: 了解 Visual Studio Code Tools for AI，以及如何借助 VS Code 中的 Azure 机器学习服务开始培训与部署机器学习和深度学习模型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: jmartens
author: j-martens
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: a2f37cffb37ce7008c3e372763784240e0d4250b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49945546"
---
# <a name="vs-code-tools-for-ai-get-started-with-azure-machine-learning-from-visual-studio-code"></a>VS Code Tools for AI：通过 Visual Studio Code 开始使用 Azure 机器学习

在本文中，将了解 Visual Studio Code (VS Code) 扩展（即 Tools for AI），以及如何借助 VS Code 中的 Azure 机器学习服务开始培训与部署机器学习和深度学习模型。

使用 Visual Studio Code 中的 Tools for AI 扩展，通过 Azure 机器学习服务在本地和远程计算目标上准备数据、培训和测试 ML 模型，部署这些模型并跟踪自定义指标和试验。

## <a name="prerequisite"></a>先决条件

+ 必须安装 Visual Studio Code。 VS Code 是一个轻量级但功能强大的源代码编辑器，可在桌面上运行。 它内置了对 Python 等的支持。  [了解如何安装 VS Code](https://code.visualstudio.com/docs/setup/setup-overview)。

+ [安装 Python 3.5 或更高版本](https://www.anaconda.com/download/)。

+ 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="install-vs-code-tools-for-ai-extension"></a>安装 VS Code Tools for AI 扩展

当安装 Tools for AI 扩展时，会自动安装另外两个扩展（如果可以访问 Internet）。 它们是 [Azure 帐户](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)扩展和 [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 扩展

若要使用 Azure 机器学习，需要将 VS Code 转换为 Python IDE。 [在 Visual Studio Code 中使用 Python](https://code.visualstudio.com/docs/languages/python) 时，需要使用随 Tools for AI 自动安装的 Microsoft Python 扩展。 该扩展使得 VS Code 成为一个出色的 IDE，适用于具有各种 Python 解释器的任何操作系统。 它利用 VS Code 的所有功能提供自动完成和 IntelliSense、语法检查、调试和单元测试，并且能够在 Python 环境（包括虚拟环境和 conda 环境）之间轻松切换。 如需查看这一包含编辑、运行和调试 Python 代码的演练，请参阅 [Python Hello World 教程](https://code.visualstudio.com/docs/python/python-tutorial)

**安装 Tools for AI 扩展：**

1. 启动 VS Code。

1. 在浏览器中，访问： http://aka.ms/vscodetoolsforai。 

1. 在该网页上，单击“安装”。 

1. 在扩展选项卡中，单击“安装”。

1. 将在 VS Code 中为该扩展打开欢迎使用选项卡，并将 Azure 符号添加到活动栏中。

   ![Visual Studio Code 活动栏中的 Azure 图标](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 在对话框中，单击“登录”，并按照屏幕上的提示对 Azure 进行身份验证。 
   
   随 VS Code Tools for AI 一起安装的 Azure 帐户扩展可帮助对 Azure 帐户进行身份验证。 请参阅 [Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)页中的命令列表。

> [!Tip] 
> 请查看[适用于 VS Code 的 IntelliCode 扩展（预览）](https://go.microsoft.com/fwlink/?linkid=2006060)。 IntelliCode 采用 Python 语言为 IntelliSense 提供一组 AI 辅助功能，例如根据当前代码上下文推断最相关的自动完成。

## <a name="install-the-sdk"></a>安装 SDK

1. 确保安装 VS Code 可识别的 Python 3.5 或更高版本。 如果立即安装它，则重新启动 VS Code，并按照 https://code.visualstudio.com/docs/python/python-tutorial 的说明选择 Python 解释器。

1. 在 VS Code 中，按 Ctrl+Shift+P 打开命令面板。

1. 键入“Install Azure ML SDK”以查找 SDK 的 pip install 命令。 此时将创建一个专用的本地 Python 环境，它具备使用 Azure 机器学习的 Visual Studio Code 先决条件。
   ![安装](./media/vscode-tools-for-ai/install-sdk.png)

1. 在集成终端窗口中，指定要使用的 Python 解释器，也可以点击 Enter 使用默认 Python 解释器。

   ![安装](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>开始使用 Azure 机器学习

在使用 VS Code 开始培训和部署 ML 模型之前，需要在云中创建一个 [Azure 机器学习服务工作区](concept-azure-machine-learning-architecture.md#workspace)，以包含你的模型和资源。 了解如何创建一个工作区，并在该工作区中创建第一个试验。

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时，“Azure:机器学习”边栏显示。

   ![安装](./media/vscode-tools-for-ai/createworkspace.gif)

1. 右键单击你的 Azure 订阅，然后选择“创建工作区”。 此时将显示一个列表。 在经过动画处理的图中，订阅名称是“OpenMind Studio”，工作区是“MyWorkspace”。 

1. 从列表中选择现有资源组，或使用命令面板中的向导创建一个新资源组。

1. 在该字段中，为新工作区键入一个唯一且明确的名称。 在屏幕截图中，工作区名为“MyWorkspace”。

1. 点击 Enter 键，此时将创建新的工作区。 它显示在订阅名称下方的树中。

1. 右键单击工作区名称，并从上下文菜单中选择“创建试验”。  这些试验使用 Azure 机器学习跟踪你的运行。

1. 在该字段中，输入试验的名称。 在屏幕截图中，试验名为“MNIST”。
 
1. 点击 Enter 键，此时将创建新的试验。 它显示在工作区名称下方的树中。

1. 右键单击试验名称，并选择“附加本地文件夹”。 此文件夹应包含本地 Python 脚本。 该文件夹随后链接到云中的试验。 

   现在，每个试验都会运行你的实验，因此，所有关键指标都会存储在试验历史记录中，你培训的模型将自动上传到 Azure 机器学习并与试验指标和日志一起存储。

   [![在 VS Code 中附加文件夹](./media/vscode-tools-for-ai/attachfolder.gif)](./media/vscode-tools-for-ai/attachfolder.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>使用键盘快捷键

与大多数 VS Code 一样，可从键盘访问 VS Code 中的 Azure 机器学习功能。 需要知道的是，最重要的组合键是 Ctrl+Shift+P，它可显示命令面板。 从这里，可以访问所有 VS Code 功能，包括最常见操作的键盘快捷键。

[![用于 VS Code Tools for AI 的键盘快捷键](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>后续步骤

现在，可以通过 Visual Studio Code 来使用 Azure 机器学习。

了解如何[在 Visual Studio Code 中创建计算目标以及培训和部署模型](how-to-vscode-train-deploy.md)。