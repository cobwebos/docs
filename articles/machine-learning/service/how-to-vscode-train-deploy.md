---
title: 从 VS Code 训练和部署模型
titleSuffix: Azure Machine Learning service
description: 了解 Azure Machine Learning for Visual Studio Code，以及如何在 Visual Studio Code 中通过 Azure 机器学习服务开始训练和部署机器学习和深度学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 6d190e4c5d283b9f803cf7287445d7591b5465db
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753106"
---
# <a name="use-visual-studio-code-to-train-and-deploy-machine-learning-models"></a>使用 Visual Studio Code 训练和部署机器学习模型

在本文中，你将了解如何使用 **Azure Machine Learning for Visual Studio Code** 扩展，在 Visual Studio Code (VS Code) 中通过 Azure 机器学习服务训练和部署机器学习和深度学习模型。

Azure 机器学习支持在本地和远程计算目标上运行试验。 对于每个试验，可跟踪多次运行，因为通常需要以迭代方式尝试不同的技术、超参数等。 Azure 机器学习可用于跟踪自定义指标和试验运行，以确保数据科学可再现性和可审核性。

此外，还能部署这些模型，以满足测试和生产需求。

## <a name="prerequisites"></a>先决条件

+ 如果还没有 Azure 订阅，请在开始前创建免费帐户。 立即试用 [Azure 机器学习服务免费版或付费版](http://aka.ms/AMLFree)。

+ 安装 [Azure Machine Learning for VS Code](how-to-vscode-tools.md) 扩展。

+ 随 VS Code 一起安装了[适用于 Python 的 Azure 机器学习 SDK](how-to-vscode-tools.md)。

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

7. 指定在新标签页内打开的 JSON 配置文件中的所有高级属性。可以指定最大节点计数之类的属性。

8. 配置完计算目标后，单击屏幕右下角的“提交”。

下面是有关创建 Azure 机器学习计算（AMLCompute）的一个示例：[![在 VS Code 中创建 AML 计算](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>“运行配置”文件

VS Code 扩展会自动在本地计算机上为本地和 docker 环境创建本地计算目标并运行配置。 可以在关联的计算目标下找到运行配置文件。

下面是默认的本地运行配置文件中的代码片段。 默认情况下，`userManagedDependencies: True`，因此你必须自行安装所有库/依赖项，然后，本地试验运行会使用 VS Code Python 扩展指定的默认 Python 环境。

```yaml
# user_managed_dependencies = True indicates that the environment will be user managed. False indicates that Azure Machine Learning service will manage the user environment.
    userManagedDependencies: True
# The python interpreter path
    interpreterPath: python
# Path to the conda dependencies file to use for this run. If a project
# contains multiple programs with different sets of dependencies, it may be
# convenient to manage those environments with separate files.
    condaDependenciesFile: aml_config/conda_dependencies.yml
# Docker details
    docker:
# Set True to perform this run inside a Docker container.
    enabled: false
```

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

  # Currently Azure Machine Learning service only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for Azure Machine Learning service execution, history, and data preparation.

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

## <a name="next-steps"></a>后续步骤

有关在 VS Code 外使用机器学习进行训练的演练，请阅读[教程：使用 Azure 机器学习训练模型](tutorial-train-models-with-aml.md)。

有关在本地编辑、运行和调试代码的演练，请参阅 [Python Hello World 教程](https://code.visualstudio.com/docs/python/python-tutorial)
