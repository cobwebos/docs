---
title: 训练 scikit-learn-了解机器学习模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习 Spark-sklearn 估计器类在企业范围内运行 scikit-learn-了解培训脚本。 示例脚本对 iris 花卉图像进行分类，以便基于 scikit-learn 的 iris 数据集构建机器学习模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: maxluk
author: maxluk
ms.date: 08/02/2019
ms.custom: seodec18
ms.openlocfilehash: 60b5b47b305ae809304f86b8f6e12d06b2acbf21
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75536585"
---
# <a name="build-scikit-learn-models-at-scale-with-azure-machine-learning"></a>构建 scikit-learn-通过 Azure 机器学习的规模了解模型
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何使用 Azure 机器学习[spark-sklearn 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)类在企业范围内运行 scikit-learn-了解培训脚本。 

本文中的示例脚本用于对 iris 花卉图像进行分类，以便基于 scikit-learn 的[iris 数据集](https://archive.ics.uci.edu/ml/datasets/iris)构建机器学习模型。

无论你是要从头开始培训机器学习 scikit-learn 模型，还是将现有模型引入到云中，都可以使用 Azure 机器学习来使用弹性云计算资源来横向扩展开源培训作业。 可以通过 Azure 机器学习来构建、部署、版本和监视生产级模型。

## <a name="prerequisites"></a>必备组件

在以下任一环境中运行此代码：
 - Azure 机器学习计算实例-无需下载或安装

    - 完成[教程：设置环境和工作区](tutorial-1st-experiment-sdk-setup.md)，创建随 SDK 和示例存储库预先加载的专用笔记本服务器。
    - 在笔记本服务器上的 "示例训练" 文件夹中，通过导航到以下目录查找已完成且扩展的笔记本 **>： scikit-learn >-了解 > 培训 > spark-sklearn**文件夹中的 "超参数"。

 - 你自己的 Jupyter 笔记本服务器

    - [安装 AZURE 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。
    - [创建工作区配置文件](how-to-configure-environment.md#workspace)。
    - 下载数据集和示例脚本文件 
        - [iris 数据集](https://archive.ics.uci.edu/ml/datasets/iris)
        - [`train_iris.py`](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn)
    - 你还可以在 GitHub 示例页上找到本指南的已完成[Jupyter Notebook 版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/scikit-learn/training/train-hyperparameter-tune-deploy-with-sklearn/train-hyperparameter-tune-deploy-with-sklearn.ipynb)。 笔记本包含一个扩展部分，涵盖智能超参数优化，并按主要指标检索最佳模型。

## <a name="set-up-the-experiment"></a>设置试验

本部分通过加载所需的 python 包、初始化工作区、创建试验以及上传定型数据和训练脚本来设置训练实验。

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

[Azure 机器学习工作区](concept-workspace.md)是服务的顶级资源。 它为您提供了一个集中的位置来处理您创建的所有项目。 在 Python SDK 中，可以通过创建[`workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象来访问工作区项目。

从 "[先决条件" 部分](#prerequisites)创建的 `config.json` 文件创建工作区对象。

```Python
ws = Workspace.from_config()
```

### <a name="create-a-machine-learning-experiment"></a>创建机器学习试验

创建试验和文件夹来保存训练脚本。 在此示例中，创建一个名为 "spark-sklearn-iris" 的试验。

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="prepare-training-script"></a>准备训练脚本

在本教程中，已为你提供训练脚本**train_iris。** 在实践中，您应该能够按原样获取任何自定义训练脚本，并使用 Azure ML 运行它，而无需修改您的代码。

若要使用 Azure ML 跟踪和指标功能，请在训练脚本中添加少量的 Azure ML 代码。  训练脚本**train_iris。 py**演示了如何使用脚本中的 `Run` 对象将一些指标记录到 Azure ML 运行中。

提供的训练脚本使用 `iris = datasets.load_iris()` 函数中的示例数据。  对于您自己的数据，您可能需要使用 "[上传数据集" 和 "脚本](how-to-train-keras.md#data-upload)" 等步骤，使数据在训练过程中可用。

将训练脚本**train_iris py**复制到项目目录中。

```
import shutil
shutil.copy('./train_iris.py', project_folder)
```

## <a name="create-or-get-a-compute-target"></a>创建或获取计算目标

为要在其上运行的 scikit-learn 作业创建计算目标。 Scikit-learn-了解仅支持单节点 CPU 计算。

下面的代码创建了一个用于远程定型计算资源的 Azure 机器学习托管计算（AmlCompute）。 AmlCompute 的创建时间大约为5分钟。 如果工作区中已存在具有该名称的 AmlCompute，则此代码将跳过创建进程。

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

有关计算目标的详细信息，请参阅[什么是计算目标一](concept-compute-target.md)文。

## <a name="create-a-scikit-learn-estimator"></a>创建 scikit-learn-了解估计器

[Scikit-learn 估计器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn?view=azure-ml-py)提供了一种简单的方法来启动计算目标上的 scikit-learn 培训作业。 它通过[`SKLearn`](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)类实现，该类可用于支持单节点 CPU 定型。

如果训练脚本需要额外的 pip 或 conda 包来运行，则可以通过将包的名称通过 `pip_packages` 和 `conda_packages` 参数传递这些包，从而将包安装在生成的 docker 映像中。

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

## <a name="submit-a-run"></a>提交运行

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)在作业运行和完成后，为运行历史记录提供接口。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

在执行运行时，它将经历以下几个阶段：

- **准备**：按 TensorFlow 估计器创建 docker 映像。 该映像将上传到工作区的容器注册表中，并进行缓存以供稍后运行。 还会将日志流式传输到运行历史记录，并可以查看日志来监视进度。

- **缩放**：如果 Batch AI 群集需要的节点数多于当前可用的节点数，则群集将尝试增加。

- **正在运行**：脚本文件夹中的所有脚本都将上载到计算目标，装载或复制数据存储，然后执行 entry_script。 输出从 stdout 开始，/logs 文件夹将流式传输到运行历史记录，并可用于监视运行情况。

- **后期处理**：将运行的/outputs 文件夹复制到运行历史记录中。

## <a name="save-and-register-the-model"></a>保存并注册模型

对模型进行定型后，可以将其保存并注册到工作区。 利用模型注册，可以在工作区中存储模型并对模型进行版本管理，从而简化[模型管理和部署](concept-model-management-and-deployment.md)。

将以下代码添加到训练脚本（train_iris py）以保存模型。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

用以下代码将模型注册到工作区。 通过指定参数 `model_framework`、`model_framework_version`和 `resource_configuration`，无代码模型部署将变为可用。 这使你可以从已注册的模型直接将你的模型部署为 web 服务，`ResourceConfiguration` 对象定义 web 服务的计算资源。

```Python
from azureml.core import Model
from azureml.core.resource_configuration import ResourceConfiguration

model = run.register_model(model_name='sklearn-iris', 
                           model_path='model.joblib',
                           model_framework=Model.Framework.SCIKITLEARN,
                           model_framework_version='0.19.1',
                           resource_configuration=ResourceConfiguration(cpu=1, memory_in_gb=0.5))
```

## <a name="deployment"></a>部署

你刚注册的模型可以采用与 Azure 机器学习中任何其他已注册模型完全相同的方式进行部署，无论你使用哪种估计器进行定型。 部署操作方法包含有关注册模型的部分，但你可以直接跳到创建用于部署的[计算目标](how-to-deploy-and-where.md#choose-a-compute-target)，因为你已有一个已注册的模型。

### <a name="preview-no-code-model-deployment"></a>效果无代码模型部署

除了传统的部署路由，还可以使用无代码部署功能（预览版）进行 scikit-learn。 所有内置 scikit-learn 模型类型都支持非代码模型部署。 通过向上面所示的 `model_framework`、`model_framework_version`和 `resource_configuration` 参数注册模型，只需使用 `deploy()` 静态函数部署模型即可。

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

完整的操作[方法](how-to-deploy-and-where.md)涵盖 Azure 机器学习中更深入的部署。


## <a name="next-steps"></a>后续步骤

本文介绍了如何培训和注册了 scikit-learn 模型，并了解了部署选项。 请参阅以下文章，详细了解 Azure 机器学习。

* [在训练期间跟踪运行指标](how-to-track-experiments.md)
* [优化超参数](how-to-tune-hyperparameters.md)
* [Azure 中的分布式深层学习培训参考体系结构](/azure/architecture/reference-architectures/ai/training-deep-learning)
