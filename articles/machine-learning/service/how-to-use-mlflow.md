---
title: 如何使用 Azure 机器学习服务 MLflow
titleSuffix: Azure Machine Learning service
description: 了解如何记录指标和项目使用 MLflow 库连接到 Azure 机器学习服务
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/10/2019
ms.custom: seodec18
ms.openlocfilehash: 934e9b705ab5f399d29f24c915b4c60a3b06138b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67082568"
---
# <a name="how-to-use-mlflow-with-azure-machine-learning-service-preview"></a>如何使用 Azure 机器学习服务 （预览版） 使用 MLflow

本文演示如何使用 MLflow 的跟踪 URI 和日志记录 API，统称为也称为 MLflow 跟踪，与 Azure 机器学习服务来跟踪和日志实验指标和中的项目在[Azure 机器学习服务工作区](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspace)。 如果您已经使用 MLflow 跟踪用于实验，工作区提供用于存储培训度量值和模型的集中式、 安全且可缩放的位置。

[MLflow](https://www.mlflow.org)是机器学习试验的生命周期管理的开放源代码库。 [MLFlow 跟踪](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)是 MLflow，记录和跟踪您的培训运行指标的一个组件，模型项目是否在实验会在本地运行，虚拟机上或远程计算机上的计算群集。
![azure 机器学习的关系图的 mlflow](media/how-to-use-mlflow/mlflow-diagram.png)

## <a name="compare-mlflow-and-azure-machine-learning-service-clients"></a>比较 MLflow 和 Azure 机器学习服务客户端

 下表汇总了可以使用 Azure 机器学习服务和其各自函数功能的不同客户端。

 MLflow 跟踪提供指标日志记录和项目为仅可通过存储功能[Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)。

| | MLflow 跟踪 | Azure 机器学习 <br> Python SDK |  Azure 机器学习 <br> CLI | Azure 门户|
|-|-|-|-|-|
| 管理工作区 |   | ✓ |  ✓ | ✓  |
| 使用数据存储  |   | ✓ |  ✓ |    |
| 日志指标      | ✓ | ✓ |    |    |
| 将项目上传 | ✓ | ✓ |    |    |
| 查看指标     | ✓ | ✓ | ✓  | ✓ |
| 管理计算   |   | ✓ | ✓  | ✓ |
| 部署模型    |   | ✓ |   ✓ | ✓ |

## <a name="prerequisites"></a>必备组件

* [安装 MLflow。](https://mlflow.org/docs/latest/quickstart.html)
* [在本地计算机上安装 Azure 机器学习 Python SDK 并创建 Azure 机器学习工作区](setup-create-workspace.md#sdk)。 该 SDK 提供 MLflow 访问你的工作区的连接。

## <a name="track-local-runs"></a>跟踪本地运行

安装`azureml-contrib-run`包以使用 MLflow 跟踪与 Azure 机器学习上在本地运行的 Jupyter Notebook 或代码编辑器中的实验。

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Azureml.contrib 命名空间需要经常更改，我们正在改善本服务。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。

导入`mlflow`并[ `Workspace` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py)类来访问 MLflow 的跟踪 URI 和配置你的工作区。

在下面的代码中，`get_mlflow_tracking_uri()`方法将唯一的追踪 URI 地址分配给工作区中， `ws`，和`set_tracking_uri()`点 MLflow 跟踪对该地址的 URI。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>跟踪 URI 是有效最多一小时或更少。 如果在一段空闲时间后重新启动您的脚本，使用 get_mlflow_tracking_uri API 来获取新的 URI。

设置与 MLflow 实验名称`set_experiment()`并开始运行与培训`start_run()`。 然后，使用`log_metric()`激活 MLflow 日志记录 API 并开始记录您运行指标的培训。

```Python
experiment_name = "experiment_with_mlflow"
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>跟踪远程运行

远程运行，可以训练模型更强大的计算资源，如启用 GPU 的虚拟机或机器学习计算群集。 请参阅[设置用于为模型定型的计算目标](how-to-set-up-training-targets.md)若要了解不同的计算选项。

配置你的计算和培训运行环境[ `Environment` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)类。 包括`mlflow`并`azure-contrib-run`pip 中环境的包[ `CondaDependencies` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)部分。 然后构建[ `ScriptRunConfig` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)与你为计算目标的远程计算。

```Python

from azureml.core import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = "my_workspace",
                 name = "my_experiment")

mlflow_env = Environment(name="mlflow-env")

cd = CondaDependencies.create(pip_packages = ["mlflow", "azureml-contrib-run"])

mlflow_env.python.conda_dependencies=cd

src = ScriptRunConfig(source_directory="./my_script_location", script="my_training_script.py")

src.run_config.target = "my-remote-compute-compute"
src.run_config.environment = mlflow_env
```

在训练脚本中，导入`mlflow`使用日志记录 Api，MLflow 并开始记录你运行的度量值。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric("example", 1.23)
```

通过此计算和培训运行配置，使用`Experiment.submit("train.py")`方法来提交运行。 这将自动设置跟踪 URI MLflow 并将定向到你的工作区从 MLflow 的日志记录。

```Python
run = exp.submit(src)
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>在工作区中查看度量值和项目

度量值和从 MLflow 日志记录的项目工作区中保留[Azure 门户](https://portal.azure.com)。 若要随时查看它们，请导航到你的工作区，并按名称查找实验。

## <a name="clean-up-resources"></a>清理资源

如果不打算使用你的工作区中的记录的指标和项目，将它们单独删除的功能是当前不可用。 相反，删除包含的存储帐户和工作区中，因此不会产生任何费用的资源组：

1. 在 Azure 门户中，选择最左侧的“资源组”  。

   ![在 Azure 门户中删除](media/how-to-use-mlflow/delete-resources.png)

1. 从列表中选择已创建的资源组。

1. 选择“删除资源组”  。

1. 输入资源组名称。 然后选择“删除”  。

## <a name="example-notebooks"></a>示例笔记本

[使用 Azure ML notebook MLflow](https://github.com/Azure/MachineLearningNotebooks/blob/master/contrib/mlflow)演示这篇文章中的概念。

## <a name="next-steps"></a>后续步骤

* [如何将模型部署](how-to-deploy-and-where.md)。