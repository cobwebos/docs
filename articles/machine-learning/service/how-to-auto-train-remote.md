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
ms.openlocfilehash: 4276a713e62f96cc5340fc7be0e8391939d32342
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497314"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>在云中使用自动化机器学习对模型进行训练

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

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

在工作区中创建 AmlCompute 目标（`ws`）（如果尚未存在）。

**估计时间**： AmlCompute 目标的创建时间大约为5分钟。

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget

amlcompute_cluster_name = "automlcl"  # Name your cluster
provisioning_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                            # for GPU, use "STANDARD_NC6"
                                                            # vm_priority = 'lowpriority', # optional
                                                            max_nodes=6)
compute_target = ComputeTarget.create(
    ws, amlcompute_cluster_name, provisioning_config)

# Can poll for a minimum number of nodes and for a specific timeout.
# If no min_node_count is provided, it will use the scale settings for the cluster.
compute_target.wait_for_completion(
    show_output=True, min_node_count=None, timeout_in_minutes=20)
```

现在，可以使用 `compute_target` 对象作为远程计算目标。

群集名称限制包括：
+ 必须小于 64 个字符。
+ 不得包含以下任何字符：`\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

## <a name="access-data-using-tabulardataset-function"></a>使用 TabularDataset 函数访问数据

将 X 和 y 定义为 `TabularDataset`s，它们将传递到 AutoMLConfig 中的自动 ML。 默认情况下，`from_delimited_files` 将 `infer_column_types` 设置为 true，这将自动推断列类型。 

如果要手动设置列类型，可以将 `set_column_types` 参数设置为手动设置每个列的类型。 在下面的代码示例中，数据来自 sklearn 包。

```python
# Create a project_folder if it doesn't exist
if not os.path.isdir('data'):
    os.mkdir('data')
    
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

from sklearn import datasets
from azureml.core.dataset import Dataset
from scipy import sparse
import numpy as np
import pandas as pd

data_train = datasets.load_digits()

pd.DataFrame(data_train.data[100:,:]).to_csv("data/X_train.csv", index=False)
pd.DataFrame(data_train.target[100:]).to_csv("data/y_train.csv", index=False)

ds = ws.get_default_datastore()
ds.upload(src_dir='./data', target_path='digitsdata', overwrite=True, show_progress=True)

X = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/X_train.csv'))
y = Dataset.Tabular.from_delimited_files(path=ds.path('digitsdata/y_train.csv'))

```

## <a name="create-run-configuration"></a>创建运行配置

若要使依赖项可用于 get_data. py 脚本，请使用定义的 `CondaDependencies`定义 `RunConfiguration` 对象。 对 `AutoMLConfig`中的 `run_configuration` 参数使用此对象。

```python
from azureml.core.runconfig import RunConfiguration
from azureml.core.conda_dependencies import CondaDependencies

run_config = RunConfiguration(framework="python")
run_config.target = compute_target
run_config.environment.docker.enabled = True
run_config.environment.docker.base_image = azureml.core.runconfig.DEFAULT_CPU_IMAGE

dependencies = CondaDependencies.create(
    pip_packages=["scikit-learn", "scipy", "numpy"])
run_config.environment.python.conda_dependencies = dependencies
```

有关此设计模式的其他示例，请参阅此[示例笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb)。

## <a name="configure-experiment"></a>配置试验
为 `AutoMLConfig` 指定设置。  （请参阅[完整参数列表](how-to-configure-auto-train.md#configure-experiment)及其可能值。）

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             )
```

### <a name="enable-model-explanations"></a>启用模型说明

在 `model_explainability` 构造函数中设置可选的 `AutoMLConfig` 参数。 另外，若要使用模型说明功能，必须将验证 dataframe 对象作为参数 `X_valid` 进行传递。

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target=compute_target,
                             run_configuration=run_config,
                             X = X,
                             y = y,
                             **automl_settings,
                             model_explainability=True,
                             X_valid=X_test
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

### <a name="view-logs"></a>查看日志

在 `/tmp/azureml_run/{iterationid}/azureml-logs` 下的 DSVM 上查找日志。

## <a name="explain"></a>最佳模型说明

检索模型说明数据可以详细了解这些模型，更好地了解在后端运行的内容。 在此示例中，我们仅为最佳拟合模型运行模型说明。 如果为管道中的所有模型运行该说明，则会导致运行时间显著增加。 模型说明信息包括：

* shap_values： shap lib 生成的解释信息。
* expected_values：应用于 X_train 数据集的模型的预期值。
* overall_summary：模型级别特征重要性值以降序排序。
* overall_imp：功能名称按与 overall_summary 中相同的顺序进行排序。
* per_class_summary：按降序排序的类级别特征重要性值。 仅适用于分类案例。
* per_class_imp：功能名称按与 per_class_summary 中相同的顺序进行排序。 仅适用于分类案例。

使用以下代码，从迭代中选择最佳管道。 `get_output` 方法针对上次拟合调用返回最佳运行和拟合的模型。

```python
best_run, fitted_model = remote_run.get_output()
```

导入 `retrieve_model_explanation` 函数，在最佳模型上运行。

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

输出要查看的 `best_run` 说明变量的结果。

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

在以下输出中输出 `best_run` 说明摘要变量结果。

![模型可说明性控制台输出](./media/how-to-auto-train-remote/expl-print.png)

还可以通过小组件 UI 或[Azure 机器学习 studio](https://ml.azure.com)中的工作区直观显示功能重要性。 

![模型可说明性 UI](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>示例

[How-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-amlcompute/auto-ml-remote-amlcompute.ipynb)笔记本演示了本文中的概念。

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>后续步骤

了解[如何为自动培训配置设置](how-to-configure-auto-train.md)。
