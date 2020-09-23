---
title: 训练 scikit-learn 机器学习模型
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习上运行 scikit-learn 训练脚本。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: jordane
author: jpe316
ms.date: 07/24/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 7b63ef36d7df43168ed132a740bab026e6e00f3f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90897235"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>使用 Azure 机器学习大规模构建 scikit-learn 模型


本文介绍如何使用 Azure 机器学习运行 scikit-learn 训练脚本。

本文中的示例脚本用来对鸢尾花图像进行分类，以基于 scikit-learn 的 [iris 数据集](https://archive.ics.uci.edu/ml/datasets/iris)构建机器学习模型。

无论是从头开始训练机器学习 scikit-learn 模型，还是将现有模型引入云中，都可以通过 Azure 机器学习使用弹性云计算资源来横向扩展开源训练作业。 你可以通过 Azure 机器学习来构建、部署和监视生产级模型以及对其进行版本控制。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码：
 - Azure 机器学习计算实例 - 无需下载或安装

    - 在开始本教程之前完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)以创建预先装载了 SDK 和示例存储库的专用笔记本服务器。
    - 在笔记本服务器上的示例训练文件夹中，导航到以下目录，查找已完成且已展开的笔记本：**how-to-use-azureml > ml-frameworks > scikit-learn > training > train-hyperparameter-tune-deploy-with-sklearn** 文件夹。

 - 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。

## <a name="set-up-the-experiment"></a>设置试验

本部分将准备训练实验，包括加载所需 python 包、初始化工作区、创建实验以及上传训练数据和训练脚本。

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它提供了一个集中的位置来处理创建的所有项目。 在 Python SDK 中，可以通过创建 [`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py&preserve-view=true) 对象来访问工作区项目。

根据在[先决条件部分](#prerequisites)中创建的 `config.json` 文件创建工作区对象。

```Python
from azureml.core import Workspace

ws = Workspace.from_config()
```


### <a name="prepare-scripts"></a>准备脚本

[这里](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train_iris.py)为你提供了在本教程中使用的训练脚本 train_iris.py。 实际上，你应该能够原样获取任何自定义训练脚本，并使用 Azure ML 运行它，而无需修改你的代码。

注意：
- 提供的训练脚本展示了如何在脚本中使用 `Run` 对象将一些指标记录到 Azure ML 运行中。
- 提供的训练脚本使用了来自 `iris = datasets.load_iris()` 函数的示例数据。  对于你自己的数据，你可能需要使用[上传数据集和脚本](how-to-train-keras.md#data-upload)之类的步骤来使数据在训练期间可用。

### <a name="define-your-environment"></a>定义环境。

#### <a name="create-a-custom-environment"></a>创建自定义环境。

创作 conda 环境 (sklearn-env.yml)。
若要在笔记本中编写 conda 环境，可以在单元顶部添加 ```%%writefile sklearn-env.yml``` 行。

```yaml
name: sklearn-training-env
dependencies:
  - python=3.6.2
  - scikit-learn
  - numpy
  - pip:
    - azureml-defaults
```

基于此 Conda 环境规范创建 Azure ML 环境。 此环境将在运行时打包到 docker 容器中。
```python
from azureml.core import Environment

myenv = Environment.from_conda_specification(name = "myenv", file_path = "sklearn-env.yml")
myenv.docker.enabled = True
```

#### <a name="use-a-curated-environment"></a>使用特选环境
如果你不想生成自己的映像，可以使用 Azure ML 提供的预生成的特选容器环境。 有关详细信息，请参阅[此文](resource-curated-environments.md)。
若要使用特选环境，可以改为运行以下命令：

```python
env = Environment.get(workspace=ws, name="AzureML-Tutorial")
```

### <a name="create-a-scriptrunconfig"></a>创建 ScriptRunConfig

此 ScriptRunConfig 会提交你的在本地计算目标上执行的作业。

```python
from azureml.core import ScriptRunConfig

sklearnconfig = ScriptRunConfig(source_directory='.', script='train_iris.py')
sklearnconfig.run_config.environment = myenv
```

若要针对远程群集进行提交，可以将 run_config.target 更改为所需的计算目标。

### <a name="submit-your-run"></a>提交运行
```python
from azureml.core import Experiment

run = Experiment(ws,'train-sklearn').submit(config=sklearnconfig)
run.wait_for_completion(show_output=True)

```

> [!WARNING]
> Azure 机器学习通过复制整个源目录来运行训练脚本。 如果你有不想上传的敏感数据，请使用 [.ignore 文件](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots)或不将其包含在源目录中。 改为使用[数据存储](https://docs.microsoft.com/python/api/azureml-core/azureml.data?view=azure-ml-py&preserve-view=true)来访问数据。

有关自定义 Python 环境的详细信息，请参阅[创建和管理用于训练和部署的环境](how-to-use-environments.md)。 

## <a name="what-happens-during-run-execution"></a>在运行执行过程中发生的情况
执行运行时，会经历以下阶段：

- **准备**：根据 TensorFlow 估算器创建 Docker 映像。 将映像上传到工作区的容器注册表，缓存以用于后续运行。 还会将日志流式传输到运行历史记录，可以查看日志以监视进度。

- **缩放**：如果 Batch AI 群集执行运行所需的节点多于当前可用节点，则群集将尝试纵向扩展。

- **正在运行**：将脚本文件夹中的所有脚本上传到计算目标，装载或复制数据存储，然后执行 entry_script。 将 stdout 和 ./logs 文件夹中的输出流式传输到运行历史记录，可将其用于监视运行。

- **后期处理**：将运行的 ./outputs 文件夹复制到运行历史记录。

## <a name="save-and-register-the-model"></a>保存并注册模型

训练模型后，可以将其保存并注册到工作区。 凭借模型注册，可以在工作区中存储模型并对其进行版本控制，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

将以下代码添加到训练脚本 train_iris.py 以保存模型。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

使用以下代码将模型注册到工作区。 通过指定参数 `model_framework`、`model_framework_version` 和 `resource_configuration`，无代码模型部署将可供使用。 无代码模型部署允许你通过已注册模型直接将模型部署为 Web 服务，[`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py&preserve-view=true) 对象定义 Web 服务的计算资源。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='outputs/model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>部署

无论使用哪种估算器进行训练，都可以采用与 Azure 机器学习中任何其他已注册模型完全相同的方式部署你刚才注册的模型。 部署指南包含有关模型注册的部分，但由于你已有一个已注册的模型，因而可以直接跳到[创建计算目标](how-to-deploy-and-where.md#choose-a-compute-target)进行部署。

### <a name="preview-no-code-model-deployment"></a>（预览版）无代码模型部署

除了传统的部署路由，还可以使用无代码部署功能 (预览) 来了解 scikit-learn。 所有内置 scikit-learn 模型类型都支持无代码模型部署。 通过使用 `model_framework`、`model_framework_version` 和 `resource_configuration` 参数注册你的模型（如上所示），可以简单地使用 [`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 静态函数来部署模型。

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

注意：这些依赖项包含在预建的 scikit-learn 推理容器中。

```yaml
    - azureml-defaults
    - inference-schema[numpy-support]
    - scikit-learn
    - numpy
```

完整的[操作指南](how-to-deploy-and-where.md)更深入地介绍了 Azure 机器学习。


## <a name="next-steps"></a>后续步骤

在本文中，你训练并注册了一个 scikit-learn 模型，并了解了部署选项。 有关 Azure 机器学习的详细信息，请参阅以下其他文章。

* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [Azure 中分布式深度学习训练的参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
