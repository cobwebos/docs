---
title: 训练 scikit-learn 机器学习模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 SKlearn 估算器类在企业范围内运行 scikit-learn 训练脚本。 示例脚本对鸢尾花图像进行分类，以基于 scikit-learn 的 iris 数据集构建机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: bdd2cc400c3df75742689258caea8cb87ee8ccc6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78942251"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>使用 Azure 机器学习大规模构建 scikit-learn 模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在本文中，你将了解如何使用 Azure 机器学习 [SKlearn 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)类在企业范围内运行 scikit-learn 训练脚本。 

本文中的示例脚本用来对鸢尾花图像进行分类，以基于 scikit-learn 的 [iris 数据集](https://archive.ics.uci.edu/ml/datasets/iris)构建机器学习模型。

无论是从头开始训练机器学习 scikit-learn 模型，还是将现有模型引入云中，都可以通过 Azure 机器学习使用弹性云计算资源来横向扩展开源训练作业。 你可以通过 Azure 机器学习来构建、部署和监视生产级模型以及对其进行版本控制。

## <a name="prerequisites"></a>先决条件

在以下任一环境中运行此代码：
 - Azure 机器学习计算实例 - 无需下载或安装

    - 在开始本教程之前完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)以创建预先装载了 SDK 和示例存储库的专用笔记本服务器。
    - 在笔记本服务器上的示例训练文件夹中，导航到以下目录，查找已完成且已展开的笔记本：**how-to-use-azureml > ml-frameworks > scikit-learn > training > train-hyperparameter-tune-deploy-with-sklearn** 文件夹。

 - 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - 下载数据集和示例脚本文件 
        - [iris 数据集](https://archive.ics.uci.edu/ml/datasets/iris)
        - [train_iris. py](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - 此外，还可以在 GitHub 示例页上找到本指南的完整 [Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。 该笔记本包含一个扩展部分，该部分包括智能超参数优化以及按主要指标检索最佳模型。

## <a name="set-up-the-experiment"></a>设置试验

本部分将准备训练实验，包括加载所需 python 包、初始化工作区、创建实验以及上传训练数据和训练脚本。

### <a name="import-packages"></a>导入包

首先，导入必需的 Python 库。

```Python
import os
import urllib
import shutil
import azureml

from azureml.core import Experiment
from azureml.core import Workspace, Run

from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException
```

### <a name="initialize-a-workspace"></a>初始化工作区

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它提供了一个集中的位置来处理创建的所有项目。 在 Python SDK 中，可以通过创建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象来访问工作区项目。

根据在[先决条件部分](#prerequisites)中创建的 `config.json` 文件创建工作区对象。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>创建机器学习试验

创建一个试验和文件夹来容纳训练脚本。 在此示例中，创建一个名为“sklearn-iris”的试验。

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>准备训练脚本

在本教程中，已为你提供了训练脚本 **train_iris.py**。 实际上，你应该能够原样获取任何自定义训练脚本，并使用 Azure ML 运行它，而无需修改你的代码。

若要使用 Azure ML 跟踪和指标功能，请在你的训练脚本内添加少量 Azure ML 代码。  训练脚本 **train_iris.py** 展示了如何在脚本中使用 `Run` 对象将一些指标记录到 Azure ML 运行中。

提供的训练脚本使用了来自 `iris = datasets.load_iris()` 函数的示例数据。  对于你自己的数据，你可能需要使用[上传数据集和脚本](how-to-train-keras.md#data-upload)之类的步骤来使数据在训练期间可用。

将训练脚本 **train_iris.py** 复制到你的项目目录中。

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>创建或获取计算目标

创建用于运行 scikit-learn 作业的计算目标。 Scikit-learn 仅支持单节点 CPU 计算。

下面的代码为你的远程训练计算资源创建 Azure 机器学习托管计算 (AmlCompute)。 创建 AmlCompute 需要花费大约 5 分钟。 如果你的工作区中已有使用该名称的 AmlCompute，则此代码会跳过创建流程。

```Python
cluster_name = "cpu-cluster"

try:
    compute_target = ComputeTarget(workspace=ws, name=cluster_name)
    print('Found existing compute target')
except ComputeTargetException:
    print('Creating a new compute target...')
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2', 
                                                           max_nodes=4)

    compute_target = ComputeTarget.create(ws, cluster_name, compute_config)

    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
```

有关计算目标的详细信息，请参阅[什么是计算目标](concept-compute-target.md)一文。

## <a name="create-a-scikit-learn-estimator"></a>创建 scikit-learn 估算器

[scikit-learn 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py)提供了一种在计算目标上启动 scikit-learn 训练作业的简单方法。 它通过[`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)类实现，该类可用于支持单节点 CPU 定型。

如果你的训练脚本需要额外的 pip 或 conda 包才能运行，则可以通过使用 `pip_packages` 和 `conda_packages` 参数传递其名称在生成的 Docker 映像中安装这些包。

```Python
from azureml.train.sklearn import SKLearn

script_params = {
    '--kernel': 'linear',
    '--penalty': 1.0,
}

estimator = SKLearn(source_directory=project_folder, 
                    script_params=script_params,
                    compute_target=compute_target,
                    entry_script='train_iris.py'
                    pip_packages=['joblib']
                   )
```


有关自定义 Python 环境的详细信息，请参阅[创建和管理用于定型和部署的环境](how-to-use-environments.md)。 

## <a name="submit-a-run"></a>提交运行

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行时和运行后提供运行历史记录的接口。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

在执行运行时，它将经历以下几个阶段：

- **准备**：按 TensorFlow 估计器创建 docker 映像。 将映像上传到工作区的容器注册表，缓存以用于后续运行。 还会将日志流式传输到运行历史记录，可以查看日志以监视进度。

- **缩放**：如果 Batch AI 群集需要的节点数多于当前可用的节点数，则群集将尝试增加。

- **正在运行**：脚本文件夹中的所有脚本都将上载到计算目标，装载或复制数据存储，然后执行 entry_script。 将 stdout 和 ./logs 文件夹中的输出流式传输到运行历史记录，可将其用于监视运行。

- **后期处理**：将运行的/outputs 文件夹复制到运行历史记录中。

## <a name="save-and-register-the-model"></a>保存并注册模型

训练模型后，可以将其保存并注册到工作区。 凭借模型注册，可以在工作区中存储模型并对其进行版本控制，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

将以下代码添加到训练脚本 train_iris.py 以保存模型。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

使用以下代码将模型注册到工作区。 通过指定参数 `model_framework`、`model_framework_version` 和 `resource_configuration`，无代码模型部署将可供使用。 这使你可以从已注册的模型直接将你的模型部署为 web 服务， [`ResourceConfiguration`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.resource_configuration.resourceconfiguration?view=azure-ml-py)该对象定义了 web 服务的计算资源。

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

### <a name="preview-no-code-model-deployment"></a>（预览）无代码模型部署

除了传统的部署路线之外，还可以为 scikit-learn 使用无代码部署功能（预览版）。 所有内置 scikit-learn 模型类型都支持无代码模型部署。 通过向上面所示`model_framework`的、 `model_framework_version`和`resource_configuration`参数注册模型，只需使用[`deploy()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model%28class%29?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)静态函数部署模型即可。

```python
web_service = Model.deploy(ws, "scikit-learn-service", [model])
```

注意：这些依赖项包含在预生成的 scikit-learn 推理容器中。

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
