---
title: ML 试验的 MLflow 跟踪
titleSuffix: Azure Machine Learning
description: 设置 MLflow 和 Azure 机器学习，以记录 Databricks 群集、本地环境或 VM 环境中创建的 ML 模型的指标和项目。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 02/03/2020
ms.custom: seodec18
ms.openlocfilehash: 95567a177635dc7d7ed03404487e62c76db8bdac
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779121"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>使用 MLflow 和 Azure 机器学习跟踪模型指标（预览版）

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文演示如何启用 MLflow 的跟踪 URI 和日志记录 API（统称为 [MLflow 跟踪](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)），以连接 MLflow 试验和 Azure 机器学习。 执行此操作可以在 [Azure 机器学习工作区](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)中跟踪和记录试验指标与项目。 如果你已对试验使用 MLflow 跟踪，则工作区会提供一个安全且可缩放的集中位置，用于存储训练指标和模型。

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org) 是用于管理机器学习试验生命周期的开源库。 MLFlow 跟踪是 MLflow 的一个组件，可以记录和跟踪训练运行指标和模型项目，而不管试验环境是位于本地计算机、远程计算目标、虚拟机还是 Azure Databricks 群集上。 

根据下图中的演示，使用 MLflow 跟踪可以在 Azure 机器学习工作区中跟踪试验的运行指标并存储模型项目。

![有关将 MLflow 与 Azure 机器学习配合使用的示意图](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 本文档中的信息主要面向需要监视模型训练过程的数据科学家与开发人员。 如果你是一名管理员并想要了解如何监视 Azure 机器学习的资源使用情况和事件（例如配额、已完成的训练运行或已完成的模型部署），请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>MLflow 和 Azure 机器学习客户端的比较

 下表汇总了可使用 Azure 机器学习的不同客户端及其各自的函数功能。

 MLflow 跟踪提供指标日志记录和项目存储功能，如果没有 MLflow 跟踪，这些功能就只能通过 [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 提供。


| | MLflow&nbsp;跟踪 <!--& Deployment--> | Azure 机器学习 Python SDK |  Azure 机器学习 CLI | Azure 机器学习工作室|
|---|---|---|---|---|
| 管理工作区 |   | ✓ | ✓ | ✓ |
| 使用数据存储  |   | ✓ | ✓ | |
| 记录指标      | ✓ | ✓ |   | |
| 上传项目 | ✓ | ✓ |   | |
| 查看指标     | ✓ | ✓ | ✓ | ✓ |
| 管理计算   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>先决条件

* [安装 MLflow。](https://mlflow.org/docs/latest/quickstart.html)
* 在本地计算机上[安装 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)。该 SDK 为 MLflow 提供用于访问工作区的连接。
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="track-local-runs"></a>跟踪本地运行

将 MLflow 跟踪与 Azure 机器学习配合使用，可将从本地运行记录的指标和项目存储到 Azure 机器学习工作区中。

安装 `azureml-mlflow` 包，以便在 Jupyter Notebook 或代码编辑器中本地运行的试验上，将 MLflow 跟踪与 Azure 机器学习配合使用。

```shell
pip install azureml-mlflow
```

>[!NOTE]
>azureml.contrib 命名空间会频繁更改，因为我们正在努力改进该服务。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。

导入 `mlflow` 和 [`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py) 类，以访问 MLflow 的跟踪 URI 并配置工作区。

在以下代码中，`get_mlflow_tracking_uri()` 方法为工作区 `ws` 分配唯一的跟踪 URI 地址，并且 `set_tracking_uri()` 将 MLflow 跟踪 URI 指向该地址。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>该跟踪 URI 的有效时间最长为 1 小时。 如果在空闲一段时间后重启脚本，请使用 get_mlflow_tracking_uri API 获取新的 URI。

使用 `set_experiment()` 设置 MLflow 试验名称，并使用 `start_run()` 启动训练运行。 然后使用 `log_metric()` 激活 MLflow 日志记录 API，并开始记录训练运行指标。

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>跟踪远程运行

将 MLflow 跟踪与 Azure 机器学习配合使用，可将从远程运行记录的指标和项目存储到 Azure 机器学习工作区中。

远程运行可让你在更强大的计算（例如已启用 GPU 的虚拟机，或机器学习计算群集）上训练模型。 请参阅[设置模型训练的计算目标](how-to-set-up-training-targets.md)了解不同的计算选项。

使用 [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 类配置计算和训练运行环境。 在环境的 [`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py) 节中包含 `mlflow` 和 `azureml-mlflow` pip 包。 然后使用远程计算作为计算目标来构造 [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)。

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-mlflow'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

在训练脚本中，导入 `mlflow` 以使用 MLflow 日志记录 API，然后开始记录运行指标。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

完成此计算和训练运行配置后，使用 `Experiment.submit('train.py')` 方法提交运行。 此方法自动设置 MLflow 跟踪 URI，并将日志记录从 MLflow 定向到你的工作区。

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>跟踪 Azure Databricks 运行

将 MLflow 跟踪与 Azure 机器学习配合使用，可将从 Azure Databricks 记录的指标和项目存储到 Azure 机器学习工作区中。

若要使用 Azure Databricks 运行 Mlflow 试验，首先需要创建一个 [Azure Databricks 工作区和群集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 在你的群集中，请务必从 PyPi 安装 *azureml-mlflow* 库，以确保你的群集能够访问所需的函数和类。

在此处导入试验笔记本，将其附加到 Azure Databricks 群集，然后运行试验。 

### <a name="install-libraries"></a>安装库

若要在群集上安装库，请导航到“库”选项卡并单击“新安装” 

 ![有关将 MLflow 与 Azure 机器学习配合使用的示意图](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

在“包”字段中键入 azureml-mlflow，然后单击“安装”。 根据需要重复此步骤，以将其他包安装到用于试验的群集中。

 ![有关将 MLflow 与 Azure 机器学习配合使用的示意图](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>设置笔记本和工作区

设置群集后，请导入试验笔记本，将其打开，然后将群集附加到其中。

试验笔记本中应包含以下代码。 此代码获取 Azure 订阅的详细信息以实例化工作区。 此代码假设你有一个现有的资源组和 Azure 机器学习工作区，如果你没有，你可以[创建它们](how-to-manage-workspace.md)。 

```python
import mlflow
import mlflow.azureml
import azureml.mlflow
import azureml.core

from azureml.core import Workspace
from azureml.mlflow import get_portal_url

subscription_id = 'subscription_id'

# Azure Machine Learning resource group NOT the managed resource group
resource_group = 'resource_group_name' 

#Azure Machine Learning workspace name, NOT Azure Databricks workspace
workspace_name = 'workspace_name'  

# Instantiate Azure Machine Learning workspace
ws = Workspace.get(name=workspace_name,
                   subscription_id=subscription_id,
                   resource_group=resource_group)
```

#### <a name="connect-your-azure-databricks-and-azure-machine-learning-workspaces"></a>连接 Azure Databricks 和 Azure 机器学习工作区

在 [Azure 门户](https://ms.portal.azure.com)上，可将 Azure Databricks (ADB) 工作区链接到新的或现有的 Azure 机器学习工作区。 为此，请导航到 ADB 工作区，并选择右下角的“链接 Azure 机器学习工作区”按钮。 链接工作区后，可以在 Azure 机器学习工作区中跟踪试验数据。 

### <a name="link-mlflow-tracking-to-your-workspace"></a>将 MLflow 跟踪链接到工作区

实例化工作区后，请设置 MLflow 跟踪 URI。 这样，便可以将 MLflow 跟踪链接到 Azure 机器学习工作区。 链接后，所有试验将会进入托管的 Azure 机器学习跟踪服务。

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>直接在笔记本中设置 MLflow 跟踪

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

在训练脚本中，导入 mlflow 以使用 MLflow 日志记录 API，然后开始记录运行指标。 以下示例记录时期损失指标。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>自动设置 MLflow 跟踪

无需在群集上的每个后续试验笔记本会话中手动设置跟踪 URI，可以使用此 [Azure 机器学习跟踪群集 Init 脚本](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)来自动执行此操作。

正确配置后，可以在 Azure 机器学习 REST API 和所有客户端中查看 MLflow 跟踪数据，并且可以通过 MLflow 用户界面或使用 MLflow 客户端在 Azure Databricks 中查看 MLflow 跟踪数据。

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>查看工作区中的指标和项目

MLflow 记录的指标和项目保存在工作区中。 若要随时查看它们，请在 [Azure 机器学习工作室](https://ml.azure.com)中导航到你的工作区，并在该工作区中按名称找到试验。  或运行以下代码。 

```python
run.get_metrics()
ws.get_details()
```

<!-- ## Deploy MLflow models as a web service

Deploying your MLflow experiments as an Azure Machine Learning web service allows you to leverage the Azure Machine Learning model management and data drift detection capabilities and apply them to your production models.

The following diagram demonstrates that with the MLflow deploy API you can deploy your existing MLflow models as an Azure Machine Learning web service, despite their frameworks--PyTorch, Tensorflow, scikit-learn, ONNX, etc., and manage your production models in your workspace.

![mlflow with azure machine learning diagram](./media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### Log your model

Before you can deploy, be sure that your model is saved so you can reference it and its path location for deployment. In your training script, there should be code similar to the following [mlflow.sklearn.log_model()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html) method, that saves your model to the specified outputs directory. 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> Include the `conda_env` parameter to pass a dictionary representation of the dependencies and environment this model should be run in.

### Retrieve model from previous run

To retrieve the run, you need the run ID and the path in run history of where the model was saved. 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### Deploy the model

Use the Azure Machine Learning SDK to deploy the model as a web service.

First, specify the deployment configuration. Azure Container Instance (ACI) is a suitable choice for a quick dev-test deployment, while Azure Kubernetes Service (AKS) is suitable for scalable production deployments.

#### Deploy to ACI

Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
(webservice,model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='diabetes-model-1', 
                      deployment_config=aci_config, 
                      tags=None, mlflow_home=None, synchronous=True)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster. Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
Set up your deployment configuration with the [deploy_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-) method. You can also add tags and descriptions to help keep track of your web service.

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True, compute_target_name='aks-mlflow')


Then, deploy the image by using the Azure Machine Learning SDK [deploy()](Then, register and deploy the model by using the Azure Machine Learning SDK [deploy](/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
from azureml.core.webservice import AksWebservice, Webservice
(webservice, model) = mlflow.azureml.deploy( model_uri='runs:/{}/{}'.format(run.id, model_path),
                      workspace=ws,
                      model_name='sklearn-model', 
                      service_name='my-aks', 
                      deployment_config=aks_config, 
                      tags=None, mlflow_home=None, synchronous=True)


webservice.wait_for_deployment()
```

The service deployment can take several minutes.

## Clean up resources

If you don't plan to use the logged metrics and artifacts in your workspace, the ability to delete them individually is currently unavailable. Instead, delete the resource group that contains the storage account and workspace, so you don't incur any charges:

1. In the Azure portal, select **Resource groups** on the far left.

   ![Delete in the Azure portal](./media/how-to-use-mlflow/delete-resources.png)

1. From the list, select the resource group you created.

1. Select **Delete resource group**.

1. Enter the resource group name. Then select **Delete**.

 -->

 ## <a name="example-notebooks"></a>示例笔记本

[将 MLflow 与 Azure ML 笔记本配合使用](https://aka.ms/azureml-mlflow-examples)演示了本文中所述的概念，并在这些概念的基础上有所延伸。

## <a name="next-steps"></a>后续步骤
* [管理模型](concept-model-management-and-deployment.md)。
* 监视生产模型中的[数据偏移](how-to-monitor-data-drift.md)。
