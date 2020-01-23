---
title: 用于 ML 试验的 MLflow 跟踪
titleSuffix: Azure Machine Learning
description: 设置 MLflow 与 Azure 机器学习，以记录 Databricks 群集、本地环境或 VM 环境中创建的 ML 模型的指标和项目。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 01/27/2020
ms.custom: seodec18
ms.openlocfilehash: a1263ecacc2af0559c726fb12c799d0e6d2f1014
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543335"
---
# <a name="track-models-metrics-with-mlflow-and-azure-machine-learning-preview"></a>通过 MLflow 和 Azure 机器学习跟踪模型指标（预览）

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文演示如何启用 MLflow 的跟踪 URI 和日志记录 API （统称为[MLflow 跟踪](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api)），以连接 MLflow 试验和 Azure 机器学习。 这样，便可以在[Azure 机器学习工作区](https://docs.microsoft.com/azure/machine-learning/concept-azure-machine-learning-architecture#workspaces)中跟踪和记录试验指标和项目。 如果已使用 MLflow 跟踪进行试验，工作区将提供一个集中、安全和可缩放的位置来存储定型指标和模型。

<!--
+ Deploy your MLflow experiments as an Azure Machine Learning web service. By deploying as a web service, you can apply the Azure Machine Learning monitoring and data drift detection functionalities to your production models. 
-->

[MLflow](https://www.mlflow.org)是一个开源库，用于管理机器学习试验的生命周期。 MLFlow 跟踪是 MLflow 的一个组件，它记录和跟踪你的培训运行指标和模型项目，无论你的实验环境如何-在本地计算机上、远程计算目标、虚拟机或 Azure Databricks 群集上。 

下图说明，使用 MLflow 跟踪，可以跟踪试验的运行度量值并将模型项目存储在 Azure 机器学习工作区中。

![mlflow 与 azure 机器学习示意图](./media/how-to-use-mlflow/mlflow-diagram-track.png)

> [!TIP]
> 本文档中的信息主要面向需要监视模型定型过程的数据科学家和开发人员。 如果你是对监视资源使用情况和 Azure 机器学习中的事件（如配额、已完成的培训运行或已完成的模型部署）感兴趣的管理员，请参阅[监视 Azure 机器学习](monitor-azure-machine-learning.md)。

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>比较 MLflow 和 Azure 机器学习客户端

 下表汇总了可使用 Azure 机器学习的不同客户端及其各自的功能。

 MLflow 跟踪只提供指标日志记录和项目存储功能，这些功能仅在[Azure 机器学习 PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)中可用。


| | MLflow&nbsp;跟踪 <!--& Deployment--> | Azure 机器学习 Python SDK |  Azure 机器学习 CLI | Azure 机器学习工作室|
|---|---|---|---|---|
| 管理工作区 |   | ✓ | ✓ | ✓ |
| 使用数据存储  |   | ✓ | ✓ | |
| 日志指标      | ✓ | ✓ |   | |
| 上传项目 | ✓ | ✓ |   | |
| 查看指标     | ✓ | ✓ | ✓ | ✓ |
| 管理计算   |   | ✓ | ✓ | ✓ |

<!--| Deploy models    | ✓ | ✓ | ✓ | ✓ |
|Monitor model performance||✓|  |   |
| Detect data drift |   | ✓ |   | ✓ |
-->
## <a name="prerequisites"></a>必备组件

* [安装 MLflow。](https://mlflow.org/docs/latest/quickstart.html)
* 在本地计算机上[安装 AZURE 机器学习 sdk](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 。 Sdk 为 MLflow 提供连接以访问工作区。
* [创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

## <a name="track-local-runs"></a>跟踪本地运行

通过 Azure 机器学习跟踪，你可以将记录的指标和项目从本地运行存储到 Azure 机器学习工作区中。

安装 `azureml-mlflow` 包，以便在 Jupyter Notebook 或代码编辑器中本地运行的试验 Azure 机器学习使用 MLflow 跟踪。

```shell
pip install azureml-mlflow
```

>[!NOTE]
>Contrib 命名空间经常更改，因为我们将努力改进服务。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。

导入 `mlflow` 和[`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py)类，以访问 MLflow 的跟踪 URI 并配置你的工作区。

在下面的代码中，`get_mlflow_tracking_uri()` 方法为工作区指定唯一的跟踪 URI 地址，`ws`，`set_tracking_uri()` 将 MLflow 跟踪 URI 指向该地址。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>跟踪 URI 的有效时间最大为1小时或更短。 如果在某一空闲时间后重新启动脚本，请使用 get_mlflow_tracking_uri API 获取新的 URI。

在 `set_experiment()` 中设置 MLflow 试验名称，并开始运行包含 `start_run()`的培训。 然后使用 `log_metric()` 激活 MLflow 日志记录 API 并开始记录定型运行指标。

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

## <a name="track-remote-runs"></a>跟踪远程运行

MLflow 跟踪与 Azure 机器学习使你可以将已记录的指标和项目从远程运行存储到 Azure 机器学习工作区中。

远程运行使你可以在更强大的计算（例如启用 GPU 的虚拟机或机器学习计算群集）上训练模型。 请参阅[设置模型定型的计算目标](how-to-set-up-training-targets.md)，了解不同的计算选项。

用[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)类配置计算和定型运行环境。 在环境的[`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)部分中包括 `mlflow` 和 `azure-contrib-run` pip 包。 然后，将作为计算目标的远程计算构造[`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py) 。

```Python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies
from azureml.core import ScriptRunConfig

exp = Experiment(workspace = 'my_workspace',
                 name='my_experiment')

mlflow_env = Environment(name='mlflow-env')

cd = CondaDependencies.create(pip_packages=['mlflow', 'azureml-contrib-run'])

mlflow_env.python.conda_dependencies = cd

src = ScriptRunConfig(source_directory='./my_script_location', script='my_training_script.py')

src.run_config.target = 'my-remote-compute-compute'
src.run_config.environment = mlflow_env
```

在训练脚本中，导入 `mlflow` 以使用 MLflow 日志记录 Api，并开始记录运行指标。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

使用此计算和训练运行配置时，请使用 `Experiment.submit('train.py')` 方法提交运行。 此方法自动设置 MLflow 跟踪 URI，并将日志记录从 MLflow 定向到你的工作区。

```Python
run = exp.submit(src)
```

## <a name="track-azure-databricks-runs"></a>跟踪 Azure Databricks 运行

通过 Azure 机器学习跟踪，你可以将 Azure Databricks 中记录的指标和项目存储在 Azure 机器学习工作区中。

若要使用 Azure Databricks 运行 Mlflow 试验，需要首先创建[Azure Databricks 工作区和群集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 在群集中，请确保从 PyPi 安装*mlflow*库，以确保群集有权访问所需的函数和类。

在此处导入实验笔记本，将其附加到 Azure Databricks 群集并运行试验。 

### <a name="install-libraries"></a>安装库

若要在群集上安装库，请导航到 "**库**" 选项卡，然后单击 "**安装新**"

 ![mlflow 与 azure 机器学习示意图](./media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

在 "**包**" 字段中，键入 mlflow，然后单击 "安装"。 根据需要重复此步骤，以将其他附加包安装到群集中进行试验。

 ![mlflow 与 azure 机器学习示意图](./media/how-to-use-mlflow/install-libraries.png)

### <a name="set-up-your-notebook-and-workspace"></a>设置笔记本和工作区

设置群集后，请导入你的实验笔记本，将其打开，并将你的群集附加到其中。

下面的代码应在实验笔记本中。 此代码将获取用于实例化你的工作区的 Azure 订阅的详细信息。 此代码假定你已有一个资源组和 Azure 机器学习工作区，否则你可以[创建它们](how-to-manage-workspace.md)。 

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

在[Azure 门户](https://ms.portal.azure.com)上，可以将 AZURE DATABRICKS （ADB）工作区链接到新的或现有的 Azure 机器学习工作区。 为此，请导航到 ADB 工作区，并选择右下角**Azure 机器学习 "工作区**" 按钮的链接。 通过链接工作区，你可以在 Azure 机器学习工作区中跟踪实验数据。 

### <a name="link-mlflow-tracking-to-your-workspace"></a>将跟踪链接 MLflow 到你的工作区

实例化工作区后，设置 MLflow 跟踪 URI。 这样，便可以将 MLflow 跟踪链接到 Azure 机器学习工作区。 链接后，所有实验都将位于托管 Azure 机器学习跟踪服务中。

#### <a name="directly-set-mlflow-tracking-in-your-notebook"></a>直接在笔记本中设置 MLflow 跟踪

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

在训练脚本中，导入 mlflow，以使用 MLflow 日志记录 Api，并开始记录运行指标。 下面的示例记录了 epoch 丢失指标。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

#### <a name="automate-setting-mlflow-tracking"></a>自动设置 MLflow 跟踪

不要在群集上的每个后续试验笔记本会话中手动设置跟踪 URI，而是使用此[Azure 机器学习跟踪群集初始化脚本](https://github.com/Azure/MachineLearningNotebooks/blob/3ce779063b000e0670bdd1acc6bc3a4ee707ec13/how-to-use-azureml/azure-databricks/linking/README.md)来自动执行此操作。

正确配置后，可以通过 MLflow 用户界面或使用 MLflow 客户端查看 Azure 机器学习 REST API 和所有客户端中的 MLflow 跟踪数据，以及 Azure Databricks 中的跟踪数据。

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>查看工作区中的指标和项目

MLflow 日志记录中的指标和项目将保存在工作区中。 若要随时查看它们，请导航到工作区，并在[Azure 机器学习 studio](https://ml.azure.com)中的工作区中按名称查找试验。  或运行以下代码。 

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

### Create Docker image

The `mlflow.azureml.build_image()` function builds a Docker image from the saved model in a framework-aware manner. It automatically creates the framework-specific inferencing wrapper code and specifies package dependencies for you. Specify the model path, your workspace, run ID and other parameters.

The following code builds a docker image using *runs:/<run.id>/model* as the model_uri path for a Scikit-learn experiment.

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
The creation of the Docker image can take several minutes. 

### Deploy the Docker image 

After the image is created, use the Azure Machine Learning SDK to deploy the image as a web service.

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

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### Deploy to AKS

To deploy to AKS, first create an AKS cluster and bring over the Docker image you want to deploy. For this example, bring over the previously created image from the ACI deployment.

To get the image from the previous ACI deployment use the [Image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py) class. 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

Create an AKS cluster using the [ComputeTarget.create()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-) method. It may take 20-25 minutes to create a new cluster.

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
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

Then, deploy the image by using the Azure Machine Learning SDK [deploy_from_image()](/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none--overwrite-false-) method. 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
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

[MLflow With AZURE ML 笔记本](https://aka.ms/azureml-mlflow-examples)演示并扩展本文中介绍的概念。

## <a name="next-steps"></a>后续步骤
* [管理模型](concept-model-management-and-deployment.md)。
* 监视生产模型的[数据偏移](how-to-monitor-data-drift.md)。
