---
title: 使用 Visual Studio Code
titleSuffix: Azure Machine Learning service
description: 了解如何安装 Azure Machine Learning for Visual Studio Code 并在 Azure 机器学习中创建试验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995245"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Azure Machine Learning for Visual Studio Code 入门

在本文中，你将了解如何使用 **Azure Machine Learning for Visual Studio Code** 扩展，在 Visual Studio Code (VS Code) 中通过 Azure 机器学习服务训练和部署机器学习和深度学习模型。

Azure 机器学习服务为本地和远程计算目标上运行试验提供支持。 对于每个试验，可跟踪多次运行，因为通常需要以迭代方式尝试不同的技术、超参数等。 Azure 机器学习可用于跟踪自定义指标和试验运行，以确保数据科学可再现性和可审核性。

此外，还能部署这些模型，以满足测试和生产需求。

## <a name="prerequisites"></a>必备组件

+ 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

+ 必须安装 Visual Studio Code。 VS Code 是一个轻量级但功能强大的源代码编辑器，可在桌面上运行。 它内置了对 Python 等的支持。  [了解如何安装 VS Code](https://code.visualstudio.com/docs/setup/setup-overview)。

+ [安装 Python 3.5 或更高版本](https://www.anaconda.com/download/)。


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>安装 Azure Machine Learning for VS Code 扩展

当安装 **Azure机器学习**扩展时，会自动安装另外两个扩展（如果可以访问 Internet）。 它们是 [Azure 帐户](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)扩展和 [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 扩展

若要使用 Azure 机器学习，需要将 VS Code 转换为 Python IDE。 [在 Visual Studio Code 中使用 Python](https://code.visualstudio.com/docs/languages/python) 时，需要使用随 Azure 机器学习扩展自动安装的 Microsoft Python 扩展。 该扩展使得 VS Code 成为一个出色的 IDE，适用于具有各种 Python 解释器的任何操作系统。 它利用 VS Code 的所有功能提供自动完成和 IntelliSense、语法检查、调试和单元测试，并且能够在 Python 环境（包括虚拟环境和 conda 环境）之间轻松切换。 如需查看这一包含编辑、运行和调试 Python 代码的演练，请参阅 [Python Hello World 教程](https://code.visualstudio.com/docs/python/python-tutorial)

**若要安装 Azure 机器学习扩展，请执行以下操作：**

1. 启动 VS Code。

1. 在浏览器中，访问：[Azure Machine Learning for Visual Studio Code（预览版）](https://aka.ms/vscodetoolsforai)扩展

1. 在该网页上，单击“安装”。 

1. 在扩展选项卡中，单击“安装”。

1. 在 VS Code 中打开扩展的欢迎使用选项卡，（见下图中的红框） 的 Azure 符号添加到活动栏。

   ![Visual Studio Code 活动栏中的 Azure 图标](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 在对话框中，单击“登录”，并按照屏幕上的提示对 Azure 进行身份验证。 
   
   随 Azure Machine Learning for VS Code 扩展一起安装的 Azure 帐户扩展可帮助对 Azure 帐户进行身份验证。 请参阅 [Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)页中的命令列表。

> [!Tip] 
> 请查看[适用于 VS Code 的 IntelliCode 扩展（预览）](https://go.microsoft.com/fwlink/?linkid=2006060)。 IntelliCode 采用 Python 语言为 IntelliSense 提供一组 AI 辅助功能，例如根据当前代码上下文推断最相关的自动完成。

## <a name="azure-ml-sdk-installation"></a>Azure ML SDK 安装

1. 确保安装 VS Code 可识别的 Python 3.5 或更高版本。 如果立即安装它，则重新启动 VS Code，并按照 https://code.visualstudio.com/docs/python/python-tutorial 的说明选择 Python 解释器。

1. 在集成终端窗口中，指定要使用的 Python 解释器，也可以点击 **Enter** 以使用默认 Python 解释器。

   ![选择解释器](./media/vscode-tools-for-ai/python.png)

1. 在窗口右下角，一个通知会出现，指示要自动安装 Azure ML SDK。    一个专用的本地 Python 环境将创建一个带有用于使用 Azure 机器学习服务的 Visual Studio Code 先决条件。

   ![安装适用于 Python 的 Azure 机器学习 SDK](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>开始使用 Azure 机器学习

在使用 VS Code 开始训练和部署机器学习模型之前，需要在云中创建一个 [Azure 机器学习服务工作区](concept-azure-machine-learning-architecture.md#workspace)，以包含你的模型和资源。 了解如何创建一个工作区，并在该工作区中创建第一个试验。

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时会显示“Azure 机器学习”边栏。

   [![安装](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. 右键单击你的 Azure 订阅，然后选择“创建工作区”。 此时将显示一个列表。 在动画图像中，订阅名称是“Free Trial”，工作区是“TeamWorkspace”。 

1. 从列表中选择现有资源组，或使用命令面板中的向导创建一个新资源组。

1. 在该字段中，为新工作区键入一个唯一且明确的名称。 在屏幕截图中，工作区名为“TeamWorkspace”。

1. 点击 Enter 键，此时将创建新的工作区。 它显示在订阅名称下方的树中。

1. 右键单击“试验”节点，并从上下文菜单中选择“创建试验”。  这些试验使用 Azure 机器学习跟踪你的运行。

1. 在该字段中，输入试验的名称。 在屏幕截图中，试验名为“MNIST”。
 
1. 点击 Enter 键，此时将创建新的试验。 它显示在工作区名称下方的树中。

1. 可以右键单击工作区中的试验，然后选择“设置为活动试验”。 “活动”试验是你当前正在使用的试验，你在 VS Code 中打开的文件夹将链接到云中的此试验。 此文件夹应包含本地 Python 脚本。

   现在，你的每个试验都会与你的试验一起运行，因此，所有关键指标都会存储在试验历史记录中，你培训的模型将自动上传到 Azure 机器学习并与试验指标和日志一起存储。

   [![在 VS Code 中附加文件夹](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>创建和管理计算目标

使用 Azure Machine Learning for VS Code，可以准备数据、训练模型，以及在本地和远程计算目标上部署模型。

此扩展支持多个不同的 Azure 机器学习远程计算目标。 请参阅 Azure 机器学习[支持的计算目标的完整列表](how-to-set-up-training-targets.md)。

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>在 VS Code 中创建 Azure 机器学习计算目标

**若要创建计算目标，请执行以下操作：**

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时会显示“Azure 机器学习”边栏。

2. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。 在动画图像中，订阅名称是“Free Trial”，工作区是“TeamWorkspace”。 

3. 在工作区节点下，右键单击“计算”节点，再选择“创建计算”。

4. 从列表中选择计算目标类型。 

5. 在命令面板中，选择一个虚拟机大小。

6. 在命令面板中，在字段中输入计算目标的名称。 

7. 指定在新标签页内打开的 JSON 配置文件中的所有高级属性。可以指定属性，例如最大节点计数。

8. 配置完计算目标后，单击屏幕右下角的“提交”。

下面是有关创建 Azure 机器学习计算（AMLCompute）的一个示例：[![在 VS Code 中创建 AML 计算](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>“运行配置”文件

VS Code 扩展会自动在本地计算机上为本地和 docker 环境创建本地计算目标并运行配置。 运行的配置文件可以找到相关联的计算目标节点下。 

## <a name="train-and-tune-models"></a>定型和优化模型

使用 Azure Machine Learning for VS Code（预览版），可快速循环访问代码、单步执行和调试，并能使用选定源代码管理解决方案。 

**若要使用 Azure 机器学习在本地运行试验，请执行以下操作：**

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。 

1. 在工作区节点下，展开“计算”节点，再右键单击要使用的计算“运行配置”。 

1. 选择“运行试验”。

1. 选择要从文件资源管理器中运行的脚本。 

1. 单击“查看试验运行”，以查看集成的 Azure 机器学习门户，从而监视运行并查看已定型模型。

下面是有关在本地运行试验的一个示例：[![在本地运行试验](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>在 VS Code 中使用远程计算目标进行试验

必须创建运行配置文件，才能在定型时使用远程计算目标。 此文件不仅指示 Azure 机器学习在何处运行试验，还指示如何准备环境。

#### <a name="the-conda-dependencies-file"></a>conda 依赖项文件

默认情况下，系统会为你新建 conda 环境，并管理安装依赖项。 不过，必须在 `aml_config/conda_dependencies.yml` 文件中指定依赖项及其版本。 

下面是默认“aml_config/conda_dependencies.yml”中的代码片段。 例如，可以指定“tensorflow=1.12.0”，如下所示。 如果未指定依赖项版本，则会使用最新版本。  
可以在配置文件中添加其他依赖项。

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**若要在远程计算目标上使用 Azure 机器学习运行试验，请执行以下操作：**

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。 

1. 在编辑器窗口中，右键单击你的 python 脚本并选择 **AML:在 Azure 中作为试验运行**。 

1. 在命令面板中，选择计算目标。 

1. 在命令面板中，在字段中输入运行配置名称。 

1. 编辑 conda_dependencies.yml 文件以指定试验的运行时依赖项，然后单击屏幕右下角的“提交”。 

1. 单击“查看试验运行”，以查看集成的 Azure 机器学习门户，从而监视运行并查看已定型模型。

下面是有关在远程计算目标上运行试验的一个示例：[![在远程目标上运行试验](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>部署和管理模型
使用 Azure 机器学习，可以在云中和 Edge 上部署和管理机器学习模型。 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>在 VS Code 中向 Azure 机器学习注册模型

至此，已定型模型，可以在工作区中注册它了。
已注册模型可供跟踪和部署。

**若要注册模型，请执行以下操作：**

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。

1. 在工作区节点下，右键单击“模型”，再选择“注册模型”。

1. 在命令面板中，在字段中输入一个模型名称。 

1. 在列表中，选择是要上传“模型文件”（对于单个模型），还是要上传“模型文件夹”（对于包含多个文件的模型，如 Tensorflow）。 

1. 选择你的文件夹或文件。

1. 配置完模型属性后，单击屏幕右下角的“提交”。 

下面是有关将模型注册到 AML 的一个示例：[![将模型注册到 AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>在 VS Code 中部署服务

使用 VS Code，可以将 Web 服务部署到：
+ Azure 容器实例 (ACI)：用于测试目的
+ Azure Kubernetes 服务 (AKS)：用于生产目的 

无需创建 ACI 容器，即可提前测试，因为创建它们非常便捷。 不过，AKS 群集确实需要提前配置。 

详细了解[使用 Azure 机器学习进行部署](how-to-deploy-and-where.md)的常规信息。

**若要部署 Web 服务，请执行以下操作：**

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。

1. 在工作区节点下，展开“模型”节点。

1. 右键单击要部署的模型，并从关联菜单中选择“部署已注册模型中的服务”命令。

1. 在命令面板中，从列表中选择要部署到的计算目标。 

1. 在命令面板中，在字段中输入此服务的名称。  

1. 在命令面板中，在键盘上按 Enter 键以浏览并选择脚本文件。

1. 在命令面板中，在键盘上按 Enter 键以浏览并选择 Conda 依赖项文件。

1. 配置完服务属性后，单击屏幕右下角的“提交”以进行部署。 在此服务属性文件中，可以指定可能要使用的本地 Docker 文件或 schema.json 文件。

此时，Web 服务已部署。

下面是有关部署 Web 服务的一个示例：[![部署 Web 服务](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>使用键盘快捷键

与大多数 VS Code 一样，可从键盘访问 VS Code 中的 Azure 机器学习功能。 需要知道的是，最重要的组合键是 Ctrl+Shift+P，它可显示命令面板。 从这里，可以访问所有 VS Code 功能，包括最常见操作的键盘快捷键。

[![Azure Machine Learning for VS Code 的键盘快捷方式](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>后续步骤

有关在 VS Code 外使用机器学习进行训练的演练，请阅读[教程：使用 Azure 机器学习训练模型](tutorial-train-models-with-aml.md)。

有关在本地编辑、运行和调试代码的演练，请参阅 [Python Hello World 教程](https://code.visualstudio.com/docs/python/python-tutorial)
