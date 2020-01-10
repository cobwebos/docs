---
title: 自动 ML 远程计算目标
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习的 Azure 机器学习远程计算目标上的自动机器学习生成模型
services: machine-learning
author: cartacioS
ms.author: sacartac
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bad957a70079a5513f103968066e2ff6a436cd77
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754160"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>在云中使用自动化机器学习对模型进行训练

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

在 Azure 机器学习中，我们在所管理的不同类型的计算资源上训练模型。 计算目标可以是本地计算机，也可以是云中的资源。

可以通过添加更多计算目标（例如 Azure 机器学习计算（AmlCompute））来轻松增加或减少机器学习试验。 AmlCompute 是一种托管计算基础结构，可让你轻松创建单个或多节点计算。

本文介绍如何通过 AmlCompute 使用自动 ML 构建模型。

## <a name="how-does-remote-differ-from-local"></a>远程与本地有何区别？

本教程 "使用[自动机器学习训练分类模型](tutorial-auto-train-models.md)" 教程介绍了如何使用本地计算机通过自动 ML 训练模型。 本地培训的工作流同样适用于远程目标。 但是，使用远程计算，能够以异步方式执行自动化机器学习试验迭代。 此功能允许你取消特定迭代，观察执行状态，或继续在 Jupyter 笔记本的其他单元格上处理。 若要进行远程训练，请先创建一个远程计算目标，如 AmlCompute。 然后，配置远程资源，并在那里提交代码。

本文介绍了在远程 AmlCompute 目标上运行自动 ML 实验所需的额外步骤。 本教程中的工作区对象 `ws` 将会在此处的整个代码中使用。

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>创建资源

在工作区中创建[`AmlCompute`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute%28class%29?view=azure-ml-py)目标（`ws`）（如果尚未存在）。

**估计时间**： AmlCompute 目标的创建时间大约为5分钟。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpu-cluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size = vm_size,
                                                                min_nodes = compute_min_nodes, 
                                                                max_nodes = compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(ws, compute_name, provisioning_config)
    
    # can poll for a minimum number of nodes and for a specific timeout. 
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(show_output=True, min_node_count=None, timeout_in_minutes=20)
    
     # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

现在，可以使用 `compute_target` 对象作为远程计算目标。

群集名称限制包括：
+ 必须小于 64 个字符。
+ 不得包含以下任何字符：`\` ~ ! @ # $% ^ & * （） = + _ [] {} \\\\ |;： \' \\"，< >/？。`

## <a name="access-data-using-tabulardataset-function"></a>使用 TabularDataset 函数访问数据

定义 training_data 作为[`TabularDataset`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py)和标签，在[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py)中传递到自动 ML。 默认情况下，`TabularDataset` 方法 `from_delimited_files`将 `infer_column_types` 设置为 true，这将自动推断列类型。 

如果要手动设置列类型，可以将 `set_column_types` 参数设置为手动设置每个列的类型。 在下面的代码示例中，数据来自 sklearn 包。

```python
from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd
import os

# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists('project_folder'):
    os.makedirs('project_folder')

X = pd.DataFrame(data_train.data[100:,:])
y = pd.DataFrame(data_train.target[100:])

# merge X and y
label = "digit"
X[label] = y

training_data = X

training_data.to_csv('data/digits.csv')
ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

training_data = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/digits.csv'))
```

## <a name="configure-experiment"></a>配置试验
为 `AutoMLConfig` 指定设置。  （请参阅[完整参数列表](how-to-configure-auto-train.md#configure-experiment)及其可能值。）

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "experiment_timeout_minutes" : 20,
    "enable_early_stopping" : True,
    "iteration_timeout_minutes": 10,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "max_concurrent_iterations": 10,
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             training_data=training_data,
                             label_column_name=label,
                             **automl_settings,
                             )
```

## <a name="submit-training-experiment"></a>提交训练试验

现在，请提交配置，以自动选择算法、超参数并定型模型。

```python
from azureml.core.experiment import Experiment
experiment = Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

将会看到类似于以下示例的输出：

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************

     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>浏览结果

您可以使用[培训教程](tutorial-auto-train-models.md#explore-the-results)中所示的相同[Jupyter 小组件](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py)来查看结果图和表。

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```

下面是小组件的静态图像。  在笔记本中，可以单击表格中的任意一行，查看运行属性和该运行的输出日志。   此外，还可以使用图表上方的下拉列表来查看每个迭代的每个可用指标的图表。

![小组件表](./media/how-to-auto-train-remote/table.png)
![小组件绘图](./media/how-to-auto-train-remote/plot.png)

小组件将显示可用于查看和浏览单个运行详细信息的 URL。  

如果你不在 Jupyter 笔记本中，可以从运行本身显示 URL：

```
remote_run.get_portal_url()
```

你的工作区中提供了相同的信息。  若要了解有关这些结果的详细信息，请参阅[了解自动化机器学习结果](how-to-understand-automated-ml.md)。

## <a name="example"></a>示例

以下[笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression/auto-ml-regression.ipynb)演示了本文中的概念。

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

* 了解[如何为自动培训配置设置](how-to-configure-auto-train.md)。
* 请参阅[如何](how-to-machine-learning-interpretability-automl.md)在自动 ML 试验中启用 model interpretability 功能。
