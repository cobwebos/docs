---
title: 训练和注册 scikit-了解模型
titleSuffix: Azure Machine Learning service
description: 本文介绍如何训练和注册 scikit-了解使用 Azure 机器学习服务的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: minxia
author: mx-iao
ms.date: 06/30/2019
ms.custom: seodec18
ms.openlocfilehash: d2c9b104d1fe9333221bc20e7e23b436358c9ece
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514019"
---
# <a name="train-and-register-scikit-learn-models-at-scale-with-azure-machine-learning-service"></a>训练和大规模 scikit-learn 模型注册到 Azure 机器学习服务

本文介绍如何训练和注册使用 Azure 机器学习服务对 scikit-learn 模型。 它使用热门[鸢尾花数据集](https://archive.ics.uci.edu/ml/datasets/iris)分类 iris 鲜花图像使用的自定义[scikit-了解](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)类。

Scikit 了解是常用于机器学习的开放源代码计算框架。 使用 Azure 机器学习服务时，你可以快速横向扩展使用灵活的云计算资源的开放源代码培训作业。 您还可以跟踪训练运行、 版本模型部署模型，以及更多。

无论要开发思路加以 scikit-learn 模型还是正在将现有模型到云中，Azure 机器学习服务可以帮助你生成生产的模型。

## <a name="prerequisites"></a>必备组件

在两种环境上运行此代码：
 - Azure 机器学习 Notebook VM-无下载或安装有必要

    - 完成[基于云的笔记本快速入门](quickstart-run-cloud-notebook.md)来使用 SDK 和示例存储库创建的专用的笔记本服务器预加载。
    - 在 notebook 服务器上的示例文件夹中，通过导航到此目录查找已完成和展开 notebook:**说明-到-使用-azureml > 培训 > train-hyperparameter-tune-deploy-with-sklearn**文件夹。

 - 你自己的 Jupyter 笔记本服务器

    - [安装 Azure 机器学习的 Python SDK](setup-create-workspace.md#sdk)
    - [创建工作区配置文件](setup-create-workspace.md#write-a-configuration-file)
    - [下载示例脚本文件](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-sklearn) `train_iris.py`
    - 您还可以查找已完成[Jupyter 笔记本版本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-hyperparameter-tune-deploy-with-keras/train-hyperparameter-tune-deploy-with-sklearn.ipynb)本指南的 GitHub 示例页。 此 notebook 包括涵盖智能超参数优化和检索最佳的模型的主要度量值已展开的部分。

## <a name="set-up-the-experiment"></a>设置的实验

本部分会通过加载所需的 python 包、 初始化工作区、 创建试验，和上传的定型数据和训练脚本来设置训练实验。

### <a name="import-packages"></a>导入包

首先，导入所需的 Python 库。

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

[Azure 机器学习服务工作区](concept-workspace.md)是服务的顶级资源。 它为您提供集中的位置来使用您创建的所有内容。 在 Python SDK 中，您可以通过创建访问工作区项目[ `workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py)对象。

创建工作区对象从`config.json`文件中创建[先决条件部分](#prerequisites)。

```Python
ws = Workspace.from_config()
```

### <a name="create-an-experiment"></a>创建试验

创建试验和用于保存训练脚本的文件夹。 在此示例中，创建名为"sklearn 鸢尾花"的试验。

```Python
project_folder = './sklearn-iris'
os.makedirs(project_folder, exist_ok=True)

exp = Experiment(workspace=ws, name='sklearn-iris')
```

### <a name="upload-dataset-and-scripts"></a>上传数据集和脚本

[数据存储](how-to-access-data.md)是一个位置可以存储和访问装载或将数据复制到计算目标的数据。 每个工作区提供了默认数据存储。 将数据和训练脚本上传到数据存储，这样用户可以轻松地访问在定型期间。

1. 创建你的数据的目录。

    ```Python
    os.makedirs('./data/iris', exist_ok=True)
    ```

1. 将鸢尾花数据集上传到默认数据存储。

    ```Python
    ds = ws.get_default_datastore()
    ds.upload(src_dir='./data/iris', target_path='iris', overwrite=True, show_progress=True)
    ```

1. 上传 scikit-learn 训练脚本`train_iris.py`。

    ```Python
    shutil.copy('./train_iris.py', project_folder)
    ```

## <a name="create-or-get-a-compute-target"></a>创建或获取计算目标

创建 scikit-learn 作业上运行的计算目标。 Scikit 了解仅支持单个节点，CPU 计算。

下面的代码中，创建远程训练计算资源托管的 Azure 机器学习计算 (AmlCompute)。 创建的 AmlCompute 大约需要 5 分钟。 如果具有该名称 AmlCompute 已在工作区中，此代码将跳过创建过程。

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

计算目标的详细信息，请参阅[什么是计算目标](concept-compute-target.md)一文。

## <a name="create-a-scikit-learn-estimator"></a>创建 scikit-learn 估算器

[Scikit-learn 估算器](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)提供简单的方法，启动 scikit-learn 的计算目标上的训练作业。 通过实现[ `SKLearn` ](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py)类，该类可用于支持单节点 CPU 培训。

如果其他 pip 或 conda 包在运行，需要训练脚本，您可以通过传递通过其名称的结果 docker 映像上安装的程序包`pip_packages`和`conda_packages`参数。

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

[运行对象](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py)运行作业时，完成后提供的运行历史记录的接口。

```Python
run = experiment.submit(estimator)
run.wait_for_completion(show_output=True)
```

执行运行时，它将经历以下阶段：

- **正在准备**:TensorFlow 估算器根据创建 docker 映像。 图像上传到工作区的容器注册表并缓存的更高版本运行。 日志还流式传输到运行历史记录，可以查看，以监视进度。

- **缩放**：群集会尝试纵向扩展如果 Batch AI 群集需要更多的节点不是当前可执行运行。

- **Running**：脚本文件夹中的所有脚本都上载到计算目标、 数据存储已装载或复制，并执行 entry_script。 从 stdout 的输出和。 日志文件夹的流式传输到运行历史记录和可用于监视运行。

- **后期处理**：。 / 输出运行的文件夹复制到运行历史记录。

## <a name="save-and-register-the-model"></a>保存并注册模型

一旦已训练模型，您可以保存并将其注册到你的工作区。 模型注册允许您存储和版本来简化工作区中的模型[管理和部署的模型](concept-model-management-and-deployment.md)。

将以下代码添加到训练脚本，train_iris.py，若要保存该模型。 

``` Python
import joblib

joblib.dump(svm_model_linear, 'model.joblib')
```

用下面的代码注册到你的工作区模型。

```Python
model = run.register_model(model_name='sklearn-iris', model_path='model.joblib')
```

## <a name="next-steps"></a>后续步骤

在本文中，将训练和注册 Azure 机器学习服务的 scikit-learn 模型。

* 若要了解如何将模型部署，继续到我们[模型部署](how-to-deploy-and-where.md)一文。

* [优化超参数](how-to-tune-hyperparameters.md)

* [在训练期间跟踪运行指标](how-to-track-experiments.md)