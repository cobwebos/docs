---
title: 使用 Visual Studio Code 进行机器学习
titleSuffix: Azure Machine Learning service
description: 了解如何安装 Azure Machine Learning for Visual Studio Code 并在 Azure 机器学习中创建试验。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: ronglu
author: ronglums
ms.date: 7/12/2019
ms.custom: seodec18
ms.openlocfilehash: b1d18006e6ec3ff1659f8fb80f8cf6766797d76b
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114432"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Azure Machine Learning for Visual Studio Code 入门

本文介绍如何使用用于 Azure 机器学习的扩展来 Visual Studio Code 训练和部署机器学习和深度学习模型。

[Azure 机器学习服务](overview-what-is-azure-ml.md)为本地和远程计算目标上运行的试验提供支持。 对于每个试验，可跟踪多次运行，因为通常需要以迭代方式尝试不同的技术、超参数等。 Azure 机器学习可用于跟踪自定义指标和试验运行，以确保数据科学可再现性和可审核性。

你还可以部署这些模型, 以满足测试和生产需求。

## <a name="prerequisites"></a>先决条件

+ 如果没有 Azure 订阅，请在开始之前创建一个免费帐户。 试用 [Azure 机器学习服务免费版或付费版](https://aka.ms/AMLFree)。

+ 必须安装 Visual Studio Code。 Visual Studio Code 是在桌面上运行的轻型但功能强大的源代码编辑器。 它附带了对 Python 和其他编程语言的内置支持。 如果尚未安装 Visual Studio Code, 请了解操作[方法](https://code.visualstudio.com/docs/setup/setup-overview)。

+ [安装 Python 3.5 或更高版本](https://www.anaconda.com/download/)。


## <a name="install-the-extension-for-azure-machine-learning-for-visual-studio-code"></a>为 Visual Studio Code 安装 Azure 机器学习的扩展

安装 Azure 机器学习扩展时, 会自动安装两个扩展 (如果你有 internet 访问权限)。 它们是[Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)和[Microsoft Python 扩展](https://marketplace.visualstudio.com/items?itemName=ms-python.python)。

若要使用 Azure 机器学习, 需要将 Visual Studio Code 变成 Python 集成开发环境 (IDE)。 需要 Microsoft Python 扩展才能[在 Visual Studio Code 中使用 Python](https://code.visualstudio.com/docs/languages/python)。 此扩展会自动安装 Azure 机器学习扩展。 此扩展使 Visual Studio Code 成为一个优秀的 IDE, 并且它可以在具有各种 Python 解释器的任何操作系统上运行。 Microsoft Python 扩展使用 Visual Studio Code 的所有功能来提供自动完成、IntelliSense、linting、调试和单元测试。 扩展还允许在 Python 环境 (包括虚拟环境和 conda 环境) 之间轻松切换。 有关编辑、运行和调试 Python 代码的详细信息, 请参阅[python hello world 教程](https://code.visualstudio.com/docs/python/python-tutorial)。

安装 Azure 机器学习扩展:

1. 打开 Visual Studio Code。

1. 在 web 浏览器中转到[Azure 机器学习 Visual Studio Code 扩展 (预览)](https://aka.ms/vscodetoolsforai)。

1. 在该网页上, 选择 "**安装**"。 

1. 在 "扩展" 选项卡上, 选择 "**安装**"。

1. 此时会在 Visual Studio Code 中打开扩展的 "欢迎" 选项卡, 并将 Azure 符号 (在以下屏幕截图中以红色突出显示) 添加到活动栏。

   ![Visual Studio Code 活动栏上的 Azure 图标](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 在对话框中, 选择 "**登录**", 然后按照提示进行 Azure 身份验证。 
   
   与 Visual Studio Code 扩展的 Azure 机器学习一起安装的 Azure 帐户扩展可帮助你通过 Azure 帐户进行身份验证。 有关命令列表, 请参阅[Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)的页面。

> [!Tip] 
> 查看[Visual Studio Code (预览版) 的 IntelliCode 扩展](https://go.microsoft.com/fwlink/?linkid=2006060)。 IntelliCode 为 Python 中的 IntelliSense 提供一组 AI 辅助功能, 例如基于当前代码上下文推断最相关的 autocompletions。

## <a name="install-the-azure-machine-learning-sdk"></a>安装 Azure 机器学习 SDK

1. 请确保安装了 Python 3.5 或更高版本, 并 Visual Studio Code 将其识别出来。 如果现在安装, 请重新启动 Visual Studio Code 并[选择一个 Python 解释](https://code.visualstudio.com/docs/python/python-tutorial)器。

1. 在 "集成终端" 窗口中, 指定要使用的 Python 解释器。 或者选择 Enter 以使用默认 Python 解释器。

   ![选择解释器](./media/vscode-tools-for-ai/python.png)

1. 在窗口的右下角, 会出现一个通知, 指示正在自动安装[AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 。 新创建的 Python 环境是本地的和私有的, 它具有使用 Azure 机器学习服务的 Visual Studio Code 先决条件。

   ![安装用于 Python 的 Azure 机器学习 SDK](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>开始使用 Azure 机器学习

在 Visual Studio Code 中开始训练和部署机器学习模型之前, 需要在云中创建一个[Azure 机器学习服务工作区](concept-workspace.md)。 此工作区将包含模型和资源。 

若要创建工作区并添加第一个试验:

1. 在 "Visual Studio Code" 活动栏上, 选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

   [![创建工作区](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. 右键单击你的 Azure 订阅，然后选择“创建工作区”。 此时将显示一个列表。 在示例动画图像中, 订阅名称为**免费试用版**, 工作区为**TeamWorkspace**。 

1. 从列表中选择一个资源组, 或使用 "命令" 面板中的向导创建一个新的资源组。

1. 在该字段中，为新工作区键入一个唯一且明确的名称。 在示例图像中, 工作区名为 " **TeamWorkspace**"。

1. 选择 Enter 以创建新工作区。 它显示在树中的订阅名称下方。

1. 右键单击 "**试验**" 节点, 然后从上下文菜单中选择 "**创建试验**"。  这些试验使用 Azure 机器学习跟踪你的运行。

1. 在 "名称" 字段中, 输入实验的名称。 在示例屏幕截图中, 试验命名为**MNIST**。
 
1. 选择 Enter 以创建新试验。 实验显示在树中的工作区名称下方。

1. 在工作区中, 可以右键单击某个试验, 将其设置为**活动**试验。 **活动**试验是您当前的实验。 Visual Studio Code 中打开的文件夹将链接到云中的此试验。 此文件夹应包含本地 Python 脚本。

现在, 关键指标将存储在试验历史记录内。 同样, 训练的模型将自动上传到 Azure 机器学习, 并与实验指标和日志一起存储。 

[![在 Visual Studio Code 中附加文件夹](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>创建和管理计算目标

利用 Azure 机器学习 Visual Studio Code, 你可以准备你的数据、训练模型, 并在本地和远程计算目标上部署它们。

扩展插件支持 Azure 机器学习的多个远程计算目标。 有关详细信息, 请参阅 Azure 机器学习支持的[计算目标](how-to-set-up-training-targets.md)的完整列表。

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>为中的 Azure 机器学习创建计算目标 Visual Studio Code

创建计算目标:

1. 在 "Visual Studio Code" 活动栏上, 选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

2. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。 在下面的示例图像中, 订阅名称为**免费试用版**, 工作区为**TeamWorkspace**。 

3. 在工作区节点下，右键单击“计算”节点，再选择“创建计算”。

4. 从列表中选择计算目标类型。 

5. 在命令面板中, 选择 "虚拟机大小"。

6. 在 "命令" 面板上的 "字段" 中, 输入计算目标的名称。 

7. 在新选项卡上打开的 JSON 配置文件中, 指定任何高级属性。 可以指定属性 (例如最大节点数)。

8. 完成计算目标配置后, 在窗口右下角, 选择 "**提交**"。

下面是有关如何创建 Azure 机器学习计算 (AMLCompute) 的示例:

[![在 Visual Studio Code 中创建 AML 计算](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>运行配置文件

Visual Studio Code 扩展会自动创建本地计算目标, 并为本地计算机上的本地和 docker 环境运行配置。 可以在关联的计算目标节点下找到运行配置文件。 

## <a name="train-and-tune-models"></a>定型和优化模型

使用 Azure 机器学习 Visual Studio Code (预览版) 可快速循环访问代码, 逐步调试和调试, 并将解决方案用于源代码管理。 

使用 Azure 机器学习在本地运行试验:

1. 在 "Visual Studio Code" 活动栏上, 选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。 

1. 在 "工作区" 节点下, 展开 "**计算**" 节点, 然后右键单击要使用的计算的**运行配置**。 

1. 选择“运行试验”。

1. 从 "文件资源管理器" 中, 选择要运行的脚本。 

1. 选择 "**查看试验运行**" 以查看集成的 Azure 机器学习门户, 以监视您的运行并查看您的定型模型。

下面是如何在本地运行试验的示例:

[![在本地运行试验](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-visual-studio-code"></a>在 Visual Studio Code 中使用远程计算进行试验

若要为定型使用远程计算目标, 需要创建运行配置文件。 此文件不仅指示 Azure 机器学习在何处运行试验，还指示如何准备环境。

#### <a name="the-conda-dependencies-file"></a>conda 依赖项文件

默认情况下, 将为你创建新的 conda 环境, 并管理你的安装依赖项。 但是, 必须在*aml_config/conda_dependencies*文件中指定依赖项及其版本。 

默认*aml_config/conda_dependencies*中的以下代码片段指定`tensorflow=1.12.0`了。 如果未指定依赖项的版本, 将使用最新版本。 可以在配置文件中添加其他依赖项。

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

使用远程计算目标上的 Azure 机器学习运行试验:

1. 在 "Visual Studio Code" 活动栏上, 选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。 

1. 在编辑器窗口中, 右键单击 Python 脚本, 然后选择 **"AML:在 Azure 中作为试验运行**。 

1. 在命令面板中, 选择计算目标。 

1. 在 "命令" 面板上的 "字段" 中, 输入运行配置名称。 

1. 编辑*conda_dependencies*文件以指定试验的运行时依赖项。 然后, 在窗口右下角, 选择 "**提交**"。 

1. 选择 "**查看试验运行**" 以查看集成的 Azure 机器学习门户, 以监视您的运行并查看您的定型模型。

下面是有关如何对远程计算目标运行试验的示例:

[![对远程目标运行试验](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>部署和管理模型
在 Azure 机器学习中, 可以在云中和边缘部署和管理机器学习模型。 

### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>将模型注册到 Azure 机器学习 Visual Studio Code

现在您已训练了模型, 接下来可以在工作区中注册它。 您可以跟踪和部署已注册的模型。

注册模型:

1. 在 "Visual Studio Code" 活动栏上, 选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。

1. 在工作区节点下，右键单击“模型”，再选择“注册模型”。

1. 在 "命令" 面板的 "字段" 中, 输入模型名称。 

1. 从列表中, 选择是上载**模型文件**(适用于单个模型) 还是**模型文件夹**(对于包含多个文件的模型, 如 TensorFlow)。 

1. 选择你的文件夹或文件。

1. 完成模型属性配置后, 在窗口右下角, 选择 "**提交**"。 

下面的示例演示如何将模型注册到 Azure 机器学习:

[![将模型注册到 AML](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-visual-studio-code"></a>从 Visual Studio Code 部署服务

在 Visual Studio Code 中, 你可以将 web 服务部署到:
+ 用于测试的 Azure 容器实例 (ACI)。
+ 用于生产的 Azure Kubernetes 服务 (AKS)。

不需要事先创建 ACI 容器来进行测试, 因为会动态创建 ACI 容器。 但是, 您需要提前配置 AKS 群集。 有关详细信息, 请参阅[部署具有 Azure 机器学习服务的模型](how-to-deploy-and-where.md)。

部署 web 服务:

1. 在 "Visual Studio Code" 活动栏上, 选择 "Azure" 图标。 此时会显示“Azure 机器学习”边栏。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。

1. 在工作区节点下，展开“模型”节点。

1. 右键单击要部署的模型, 然后从上下文菜单中选择 "**从已注册的模型部署服务**"。

1. 在 "命令" 面板中, 选择要部署到的计算目标。 

1. 在 "命令" 面板上的 "字段" 中, 输入此服务的名称。  

1. 在 "命令" 面板中, 选择键盘上的 Enter 键以浏览并选择该脚本文件。

1. 在命令面板中, 选择键盘上的 Enter 键以浏览并选择 conda 依赖项文件。

1. 完成服务属性配置后, 请在窗口右下角选择 "**提交**" 以进行部署。 在服务属性文件中, 可以指定本地 docker 文件或架构文件。

此时，Web 服务已部署。

下面是有关如何部署 web 服务的示例:

[![部署 web 服务](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>使用键盘快捷键

您可以使用键盘访问 Visual Studio Code 中的 Azure 机器学习功能。 要了解的最重要的键盘快捷方式是 Ctrl + Shift + P, 其中显示命令面板。 在命令面板中, 您可以访问 Visual Studio Code 的所有功能, 包括最常见操作的键盘快捷方式。

[![Visual Studio Code 的 Azure 机器学习的键盘快捷方式](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>后续步骤

* 有关如何在 Visual Studio Code 外训练 Azure 机器学习的演练, 请参阅[教程:使用 Azure 机器学习训练模型](tutorial-train-models-with-aml.md)。
* 有关如何在本地编辑、运行和调试代码的演练, 请参阅[Python hello world 教程](https://code.visualstudio.com/docs/python/python-tutorial)。
