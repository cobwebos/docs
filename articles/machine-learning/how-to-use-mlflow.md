---
title: ML 试验的 MLflow 跟踪
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设置 MLflow 以记录 ML 模型的指标和项目，并将 ML 模型部署为 web 服务。
services: machine-learning
author: shivp950
ms.author: shipatel
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.date: 09/08/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 3fb177afa804788632f22d24bbd376d64cbe1c9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91250685"
---
# <a name="track-experiment-runs-and-deploy-ml-models-with-mlflow-and-azure-machine-learning-preview"></a>跟踪试验运行并通过 MLflow 和 Azure 机器学习 (预览版部署 ML 模型) 

本文介绍如何启用 MLflow 的跟踪 URI 和日志记录 API （统称为 [MLflow 跟踪](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)），以将 Azure 机器学习连接为 MLflow 试验的后端。 

支持的功能包括： 

+ 在 [Azure 机器学习工作区](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)中跟踪和记录试验指标及项目。 如果已为试验使用 MLflow 跟踪，工作区可提供集中、安全和可缩放的位置，用于存储训练指标和模型。

+ Azure 机器学习后端支持 (预览版) ，通过 MLflow 项目提交培训作业。 你可以在本地提交作业，Azure 机器学习通过 [Azure 机器学习计算](https://docs.microsoft.com/azure/machine-learning/how-to-create-attach-compute-sdk#amlcompute)将运行跟踪或迁移到云。

+ 在 MLflow 和 Azure 机器学习模型注册表中跟踪和管理模型。

+ 将 MLflow 试验部署为 Azure 机器学习 Web 服务。 通过部署为 Web 服务，你可以将 Azure 机器学习监视和数据偏移检测功能应用到生产模型中。 

[MLflow](https://www.mlflow.org) 是一个开放源代码库，用于管理机器学习试验的生命周期。 MLFlow 跟踪是 MLflow 的一个组件，它记录和跟踪你的培训运行指标和模型项目，无论你的实验环境如何-在本地计算机上、远程计算目标、虚拟机或 [Azure Databricks 群集](how-to-use-mlflow-azure-databricks.md)上。 

>[!NOTE]
> 作为开放源代码库，MLflow 会经常更改。 因此，通过 Azure 机器学习和 MLflow 集成提供的功能应视为预览版，Microsoft 并不完全支持它。

下图说明使用 MLflow 跟踪，你可以跟踪试验的运行指标，并将模型项目存储在 Azure 机器学习工作区中。

![使用 Azure 机器学习的 MLflow 示意图](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 本文档中的信息主要面向需要监视模型训练过程的数据科学家与开发人员。 如果你是一名管理员并想要了解如何监视 Azure 机器学习的资源使用情况和事件（例如配额、已完成的训练运行或已完成的模型部署），请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow 和 Azure 机器学习客户端的比较

 下表总结了可以使用 Azure 机器学习的不同客户端及其各自的功能。

 MLflow 跟踪提供指标记录和项目存储功能，这些功能仅通过 [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 提供。

| 功能 | MLflow 跟踪和部署 | Azure 机器学习 Python SDK |  Azure 机器学习 CLI | Azure 机器学习工作室|
|---|---|---|---|---|
| 管理工作区 |   | ✓ | ✓ | ✓ |
| 使用数据存储  |   | ✓ | ✓ | |
| 记录指标      | ✓ | ✓ |   | |
| 上传项目 | ✓ | ✓ |   | |
| 查看指标     | ✓ | ✓ | ✓ | ✓ |
| 管理计算   |   | ✓ | ✓ | ✓ |
| 部署模型    | ✓ | ✓ | ✓ | ✓ |
|监视模型性能||✓|  |   |
| 检测数据偏差 |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>先决条件

* 安装 `azureml-mlflow` 包。 
    * 此包会自动引入 `azureml-core` [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true)，该 SDK 提供 MLflow 的连接以访问工作区。
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="track-local-runs"></a>跟踪本地运行

使用 Azure 机器学习进行 MLflow 跟踪，你可以将本地运行中记录的指标和项目存储到 Azure 机器学习工作区中。

导入 `mlflow` 和 [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true) 类以访问 MLflow 的跟踪 URI 并配置工作区。

在下面的代码中，`get_mlflow_tracking_uri()` 方法会向工作区 `ws` 分配唯一的跟踪 URI 地址，并且 `set_tracking_uri()` 会将 MLflow 跟踪 URI 指向该地址。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>跟踪 URI 的有效时间不超过一小时。 如果在一段空闲时间后重新启动脚本，请使用 get_mlflow_tracking_uri API 来获取新的 URI。

使用 `set_experiment()` 设置 MLflow 试验名称，并通过 `start_run()` 启动训练运行。 然后使用 `log_metric()` 激活 MLflow 记录 API 并开始记录训练运行指标。

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>跟踪远程运行

远程运行可以让你通过更强大的计算（例如启用 GPU 的虚拟机或机器学习计算群集）训练模型。 请参阅[使用计算目标进行模型训练](how-to-set-up-training-targets.md)，了解不同的计算选项。

使用 Azure 机器学习进行 MLflow 跟踪，你可以将远程运行中记录的指标和项目存储在 Azure 机器学习工作区中。 其中包含 MLflow 跟踪代码的任何运行会将度量值自动记录到工作区。 

下面的示例 conda 环境将 `mlflow` 和 `azureml-mlflow` 作为 pip 包提供。 


```yaml
name: sklearn-example
dependencies:
  - python=3.6.2
  - scikit-learn
  - matplotlib
  - numpy
  - pip:
    - azureml-mlflow
    - numpy
```

在脚本中，用类配置计算和定型运行环境 [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true) 。 然后，将  [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py&preserve-view=true) 远程计算构造为计算目标。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

在具有此计算和训练运行配置的情况下，使用 `Experiment.submit()` 方法提交运行。 此方法会自动设置 MLflow 跟踪 URI 并将记录从 MLflow 定向到工作区。

```Python
run = exp.submit(src)
```

## <a name="train-with-mlflow-projects"></a>MLflow 项目定型

[MLflow 项目](https://mlflow.org/docs/latest/projects.html) 使你可以组织和描述你的代码，让其他数据科学家 (或自动化工具) 运行它。 利用 Azure 机器学习的 MLflow 项目，你可以在工作区中跟踪和管理你的训练运行。 

此示例演示如何 Azure 机器学习跟踪在本地提交 MLflow 项目。

安装 `azureml-mlflow` 包，以便在本地试验 Azure 机器学习使用 MLflow 跟踪。 可以通过 Jupyter 笔记本或代码编辑器运行试验。

```shell
pip install azureml-mlflow
```

导入 `mlflow` 和 [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace%28class%29?view=azure-ml-py&preserve-view=true) 类以访问 MLflow 的跟踪 URI 并配置工作区。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

使用 `set_experiment()` 设置 MLflow 试验名称，并通过 `start_run()` 启动训练运行。 然后，使用 `log_metric()` 激活 MLflow 日志记录 API 并开始记录定型运行指标。

```Python
experiment_name = 'experiment-with-mlflow-projects'
mlflow.set_experiment(experiment_name)
```

创建后端配置对象，以存储集成所需的信息，如计算目标以及要使用的托管环境的类型。

```python
backend_config = {"USE_CONDA": False}
```
将 `azureml-mlflow` 包作为 pip 依赖项添加到环境配置文件，以便在工作区中跟踪指标和关键项目。 

``` shell
name: mlflow-example
channels:
  - defaults
  - anaconda
  - conda-forge
dependencies:
  - python=3.6
  - scikit-learn=0.19.1
  - pip
  - pip:
    - mlflow
    - azureml-mlflow
```
提交本地运行，并确保设置参数 `backend = "azureml" ` 。 利用此设置，你可以在本地提交运行，并在工作区中为自动输出跟踪、日志文件、快照和打印错误提供附加支持。 

在 [Azure 机器学习 studio](overview-what-is-machine-learning-studio.md)中查看运行和指标。 


```python
local_env_run = mlflow.projects.run(uri=".", 
                                    parameters={"alpha":0.3},
                                    backend = "azureml",
                                    use_conda=False,
                                    backend_config = backend_config, 
                                    )

```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>查看工作区中的指标和项目

MLflow 记录的指标和项目保存在工作区中。 若要随时查看它们，请在 [Azure 机器学习工作室](https://ml.azure.com)中导航到你的工作区，并在该工作区中按名称找到试验。  或运行以下代码。 

```python
run.get_metrics()
ws.get_details()
```

## <a name="manage-models"></a>管理模型 

用支持 MLflow 模型注册表的 [Azure 机器学习模型注册表](concept-model-management-and-deployment.md#register-package-and-deploy-models-from-anywhere) 注册和跟踪模型。 Azure 机器学习模型与 MLflow 模型架构一致，因此可以轻松地在不同的工作流之间导出和导入这些模型。 与 MLflow 相关的元数据（例如），运行 id 还用已注册的跟踪模型进行标记。 用户可以提交定型运行、注册和部署 MLflow 运行生成的模型。 

如果要在一个步骤中部署和注册您的生产就绪模型，请参阅 [部署和注册 MLflow 模型](#deploy-and-register-mlflow-models)。

若要注册并查看运行中的模型，请执行以下步骤：

1. 运行完成后，调用 `register_model()` 方法。

    ```python
    # the model folder produced from the run is registered. This includes the MLmodel file, model.pkl and the conda.yaml.
    run.register_model(model_name = 'my-model', model_path = 'model')
    ```

1. [Azure 机器学习 studio](overview-what-is-machine-learning-studio.md)中查看工作区中已注册的模型。

    在下面的示例中，已为已 `my-model` 标记的 MLflow 跟踪元数据提供了已注册的模型。 

    ![mlflow-模型](./media/how-to-use-mlflow/registered-mlflow-model.png)

1. 选择 " **项目** " 选项卡，以查看与 MLflow 模型架构 (Yaml，MLmodel，model.pkl) 一致的所有模型文件。

    ![模型-架构](./media/how-to-use-mlflow/mlflow-model-schema.png)

1. 选择 MLmodel 以查看运行生成的 MLmodel 文件。

    ![MLmodel-架构](./media/how-to-use-mlflow/mlmodel-view.png)



## <a name="deploy-and-register-mlflow-models"></a>部署和注册 MLflow 模型 

将 MLflow 试验作为 Azure 机器学习 web 服务进行部署可让你利用 Azure 机器学习模型管理和数据偏差检测功能，将其应用到生产模型。

为此，需要

1. 注册模型。
1. 确定要用于方案的部署配置。

    1. [Azure 容器实例 (ACI) ](#deploy-to-aci) 适用于快速开发测试部署。
    1. [Azure Kubernetes Service (AKS) ](#deploy-to-aks) 适用于可缩放的生产部署。

下图演示了使用 MLflow 部署 API，你可以将现有 MLflow 模型部署为 Azure 机器学习 Web 服务，而无需考虑它们的框架（PyTorch、Tensorflow、scikit-learn、ONNX 等），并可在工作区中管理你的生产模型。

![ 将 mlflow 模型与 azure 机器学习一起部署](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)


### <a name="deploy-to-aci"></a>部署到 ACI

使用 [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 方法设置部署配置。 你还可以添加标记和说明来帮助跟踪你的 Web 服务。

```python
from azureml.core.webservice import AciWebservice, Webservice

# Set the model path to the model folder created by your run
model_path = "model"

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

然后，通过 Azure 机器学习 SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 方法一步注册并部署模型。 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```

### <a name="deploy-to-aks"></a>部署到 AKS

若要部署到 AKS，请先创建 AKS 群集。 使用 [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py&preserve-view=true#&preserve-view=truecreate-workspace--name--provisioning-configuration-) 方法创建 AKS 群集。 创建新群集可能需要 20-25 分钟。

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (can also provide parameters to customize)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aks-mlflow'

# Create the cluster
aks_target = ComputeTarget.create(workspace=ws, 
                                  name=aks_name, 
                                  provisioning_configuration=prov_config)

aks_target.wait_for_completion(show_output = True)

print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```
使用 [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) 方法设置部署配置。 你还可以添加标记和说明来帮助跟踪你的 Web 服务。

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')

```

然后，使用 Azure 机器学习 SDK [deploy ( # A1] 在一步中注册并部署模型， (然后使用 Azure 机器学习 SDK [部署](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py&preserve-view=true#&preserve-view=truedeploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) 方法注册和部署模型。 

```python

# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice

# set the model path 
model_path = "model"

(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

服务部署可能需要几分钟的时间。

## <a name="clean-up-resources"></a>清理资源

如果不打算使用工作区中记录的指标和项目，目前尚未提供单独删除它们的功能。 可以改为删除包含存储帐户和工作区的资源组，这样就不会产生任何费用：

1. 在 Azure 门户中，选择最左侧的“资源组”。

   ![在 Azure 门户中删除](./media/how-to-use-mlflow/delete-resources.png)

1. 从列表中选择已创建的资源组。

1. 选择“删除资源组”。

1. 输入资源组名称。 然后选择“删除”。

## <a name="example-notebooks"></a>示例笔记本

[将 MLflow 与 Azure ML 笔记本配合使用](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/track-and-monitor-experiments/using-mlflow)演示了本文中所述的概念，并在这些概念的基础上有所延伸。

## <a name="next-steps"></a>后续步骤

* [管理模型](concept-model-management-and-deployment.md)。
* 监视生产模型中的[数据偏移](how-to-monitor-data-drift.md)。
* [跟踪 Azure Databricks 与 MLflow 一起运行](how-to-use-mlflow-azure-databricks.md)。 
