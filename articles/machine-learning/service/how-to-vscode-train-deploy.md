---
title: 将 Visual Studio Code Tools for AI 扩展用于 Azure 机器学习
description: 了解 Visual Studio Code Tools for AI，以及如何在 VS Code 中通过 Azure 机器学习服务开始定型和部署机器学习和深度学习模型。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.reviewer: jmartens
ms.date: 10/1/2018
ms.openlocfilehash: 377a4bbf359b2c65136625fcef8a1093e49da728
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854299"
---
# <a name="vs-code-tools-for-ai-train-and-deploy-ml-models-from-vs-code"></a>VS Code Tools for AI：在 VS Code 中定型和部署 ML 模型
本文将介绍如何使用 VS Code Tools for AI 扩展，在 VS Code 中通过 Azure 机器学习服务定型和部署机器学习和深度学习模型。

Azure 机器学习支持在本地和远程计算目标上运行试验。 对于每个试验，可跟踪多次运行，因为通常需要以迭代方式尝试不同的技术、超参数等。 Azure 机器学习可用于跟踪自定义指标和试验运行，以确保数据科学可再现性和可审核性。

此外，还能部署这些模型，以满足测试和生产需求。

## <a name="prerequisites"></a>先决条件

+ 创建了适用于 Azure 机器学习的 [VS Code Tools for AI](how-to-vscode-tools.md)。

+ 随 VS Code 一起安装了[适用于 Python 的 Azure 机器学习 SDK](how-to-vscode-tools.md)。

+ 如果还没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://aka.ms/AMLfree)。

## <a name="create-and-manage-compute-targets"></a>创建和管理计算目标

使用 Visual Studio Code Tools for AI，可以准备数据、定型模型，并能在本地和远程计算目标上部署模型。

此扩展支持多个不同的 Azure 机器学习远程计算目标。 请参阅 Azure 机器学习[支持的计算目标的完整列表](how-to-set-up-training-targets.md)。

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>在 VS Code 中创建 Azure 机器学习计算目标

**若要创建计算目标，请执行以下操作：**

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时，“Azure:机器学习”边栏显示。

2. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。 在动画图像中，订阅名称是“OpenMind Studio”，工作区是“MyWorkspace”。 

3. 在工作区节点下，右键单击“计算”节点，再选择“创建计算”。

4. 从列表中选择计算目标类型。 

5. 在字段中，输入此计算目标的唯一名称，并指定虚拟机大小。

6. 指定在新标签页内打开的 JSON 配置文件中的所有高级属性。 

7. 配置完计算目标后，单击右下方的“完成”。

下面的示例展示了 Azure Batch AI：[![在 VS Code 中创建 Azure Batch AI 计算](./media/vscode-tools-for-ai/createcompute.gif)](./media/vscode-tools-for-ai/createcompute.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>在 VS Code 中使用远程计算目标进行试验

必须创建运行配置文件，才能在定型时使用远程计算目标。 此文件不仅指示 Azure 机器学习在何处运行试验，还指示如何准备环境。

#### <a name="the-run-configuration-file"></a>“运行配置”文件

VS Code 扩展会自动在本地计算机上为本地和 docker 环境创建运行配置。

下面是默认运行配置文件中的代码片段。

若要自行安装所有库/依赖项，请先设置 `userManagedDependencies: True`，然后本地试验运行会使用 VS Code Python 扩展指定的默认 Python 环境。

```yaml
# user_managed_dependencies=True indicates that the environment will be user managed. False indicates that AzureML will manage the user environment.
    userManagedDependencies: False
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

#### <a name="the-conda-dependencies-file"></a>conda 依赖项文件

默认情况下，系统会为你新建 conda 环境，并管理安装依赖项。 不过，必须在 `aml_config/conda_dependencies.yml` 文件中指定依赖项。

下面是默认“aml_config/conda_dependencies.yml”中的代码片段。
可以在配置文件中添加其他依赖项。

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

## <a name="train-and-tune-models"></a>定型和优化模型

在 VS Code 中使用 Azure 机器学习，可快速循环访问代码、单步执行和调试，并能使用选定源代码管理解决方案。 

**若要使用 Azure 机器学习运行试验，请执行以下操作：**

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时，“Azure:机器学习”边栏显示。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。 在动画图像中，订阅名称是“OpenMind Studio”，工作区是“MyWorkspace”。 

1. 在工作区节点下，展开“计算”节点，再右键单击要使用的计算的“运行配置”。 

1. 选择“运行试验”。

1. 单击“查看试验运行”，以查看集成的 Azure 机器学习门户，从而监视运行并查看已定型模型。

   [![在 VS Code 中运行机器学习试验](./media/vscode-tools-for-ai/runexperiment.gif)](./media/vscode-tools-for-ai/runexperiment.gif#lightbox)

## <a name="deploy-and-manage-models"></a>部署和管理模型
使用 Azure 机器学习，可以在云中和 Edge 上部署和管理机器学习模型。 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>在 VS Code 中向 Azure 机器学习注册模型

至此，已定型模型，可以在工作区中注册它了。
已注册模型可供跟踪和部署。

**若要注册模型，请执行以下操作：**

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时，“Azure:机器学习”边栏显示。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。

1. 在工作区节点下，右键单击“模型”，再选择“注册模型”。

1. 在列表中，选择是要上传“模型文件”（对于单个模型），还是要上传“模型文件夹”（对于包含多个文件的模型，如 Tensorflow）。 

1. 使用文件选择器对话框选择文件或文件夹。

   [![计算](./media/vscode-tools-for-ai/registermodel.gif)](./media/vscode-tools-for-ai/registermodel.gif#lightbox)

> [!Warning]
> 目前，请从生成的 json 文件中删除标记。

### <a name="deploy-your-service-from-vs-code"></a>在 VS Code 中部署服务

使用 VS Code，可以将 Web 服务部署到：
+ Azure 容器实例 (ACI)：用于测试目的
+ Azure Kubernetes 服务 (AKS)：用于生产目的 

无需创建 ACI 容器，即可提前测试，因为创建它们非常便捷。 不过，AKS 群集确实需要提前配置。 

详细了解[使用 Azure 机器学习进行部署](how-to-deploy-and-where.md)的常规信息。

**若要部署 Web 服务，请执行以下操作：**

1. 单击 Visual Studio Code 活动栏中的 Azure 图标。 此时，“Azure:机器学习”边栏显示。

1. 在树视图中，展开 Azure 订阅和 Azure 机器学习服务工作区。

1. 在工作区节点下，展开“模型”节点。

1. 右键单击要部署的模型，并从关联菜单中选择“部署已注册模型中的服务”命令。

1. 在 VS Code 命令面板中，选择要部署到列表中的哪个计算目标。 

1. 在字段中，输入此服务的名称。 

1. 在右下角中的对话框中，单击“浏览”，并选择评分脚本。 此时，对话框关闭。

1. 如果有本地 Docker 文件，请在随即显示的第二个对话框中单击“浏览”。 

   如果你取消对话框且未指定本地 Docker 文件，系统默认使用的是“Azure 机器学习”。

1. 在随即显示的第三个对话框中，单击“浏览”，并选择本地 conda 文件路径，或稍后在 json 编辑器中输入文件路径。

1. 如果有要使用的 schema.json 文件，请在随即显示的第四个对话框中单击“浏览”，并选择此文件。

此时，Web 服务已部署。

下面的示例展示了部署到 Azure 容器实例：[![在 VS Code 中部署到 Azure 容器实例](./media/vscode-tools-for-ai/deploy.gif)](./media/vscode-tools-for-ai/deploy.gif#lightbox)

## <a name="next-steps"></a>后续步骤

有关在 VS Code 外使用机器学习进行定型的演练，请阅读[教程：使用 Azure 机器学习定型模型](tutorial-train-models-with-aml.md)。

有关在本地编辑、运行和调试代码的演练，请参阅 [Python Hello World 教程](https://code.visualstudio.com/docs/python/python-tutorial)
