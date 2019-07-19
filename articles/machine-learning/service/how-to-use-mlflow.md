---
title: 将 MLflow 与 Azure 机器学习服务结合使用
titleSuffix: Azure Machine Learning service
description: 将 MLflow 与 Azure 机器学习 service 结合使用, 记录指标和项目并将模型部署到生产。
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 07/15/2019
ms.custom: seodec18
ms.openlocfilehash: 2030365cd78480c25e224edfea9e395aafa6661c
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227915"
---
# <a name="track-metrics-and-deploy-models-with-mlflow-and-azure-machine-learning-service-preview"></a>通过 MLflow 和 Azure 机器学习 service (预览版) 跟踪指标和部署模型

本文演示如何使用 Azure 机器学习服务启用 MLflow 的跟踪 URI 和日志记录 API (统称为[MLflow 跟踪](https://mlflow.org/docs/latest/quickstart.html#using-the-tracking-api))。 这样做使您能够:

+ 在[Azure 机器学习服务工作区](https://docs.microsoft.com/azure/machine-learning/service/concept-azure-machine-learning-architecture#workspaces)中跟踪和记录试验指标和项目。 如果已使用 MLflow 跟踪进行试验, 工作区将提供一个集中、安全和可缩放的位置来存储定型指标和模型。

+ 将 MLflow 试验部署为 Azure 机器学习 web 服务。 通过将部署为 web 服务, 可以将 Azure 机器学习监视和数据偏差检测功能应用到生产模型。 

[MLflow](https://www.mlflow.org)是一个开源库, 用于管理机器学习试验的生命周期。 MLFlow 跟踪是 MLflow 的一个组件, 无论是在本地还是在虚拟机上 (即使在 Azure Databricks 上), 都可记录和跟踪你的培训运行指标和模型项目, 无论是在本地还是在虚拟机上进行测试。

![mlflow 与 azure 机器学习示意图](media/how-to-use-mlflow/mlflow-diagram-track.png)

## <a name="compare-mlflow-and-azure-machine-learning-clients"></a>比较 MLflow 和 Azure 机器学习客户端

 下表汇总了可以使用 Azure 机器学习服务的不同客户端及其各自的功能。

 MLflow 跟踪只提供指标日志记录和项目存储功能, 这些功能仅在[Azure 机器学习 PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)中可用。

| | MLflow 跟踪 & 部署 | Azure 机器学习 <br> Python SDK |  Azure 机器学习 <br> CLI | Azure 门户|
|-|-|-|-|-|-|
| 管理工作区 |   | ✓ | ✓ | ✓ |
| 使用数据存储  |   | ✓ | ✓ | |
| 日志指标      | ✓ | ✓ |   | |
| 上传项目 | ✓ | ✓ |   | |
| 查看指标     | ✓ | ✓ | ✓ | ✓ |
| 管理计算   |   | ✓ | ✓ | ✓ |
| 部署模型    | ✓ | ✓ | ✓ | ✓ |
|监视模型性能||✓|  |   |
| 检测数据偏差 |   | ✓ |   | ✓ |

## <a name="prerequisites"></a>先决条件

* [安装 MLflow。](https://mlflow.org/docs/latest/quickstart.html)
* [在本地计算机上安装 Azure 机器学习 PYTHON SDK, 并创建 Azure 机器学习工作区](setup-create-workspace.md#sdk)。 SDK 为 MLflow 提供连接以访问工作区。

## <a name="track-experiment-runs"></a>跟踪试验运行

通过 Azure 机器学习 service 的 MLflow 跟踪, 你可以将记录的指标和项目从本地和远程运行存储到 Azure 机器学习工作区中。

### <a name="local-runs"></a>本地运行

`azureml-contrib-run`安装包以使用 MLflow 跟踪, 并在 Jupyter Notebook 或代码编辑器中本地运行试验 Azure 机器学习。

```shell
pip install azureml-contrib-run
```

>[!NOTE]
>Contrib 命名空间经常更改, 因为我们将努力改进服务。 因此，此命名空间中的任何内容都应被视为预览版，Microsoft 并不完全支持。

导入[`Workspace`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace(class)?view=azure-ml-py)和类以访问 MLflow 的跟踪 URI 并配置你的工作区。 `mlflow`

在下面的代码中, `get_mlflow_tracking_uri()`方法将唯一的跟踪 uri 地址分配给工作区`ws`, 并`set_tracking_uri()`将 MLflow 跟踪 uri 指向该地址。

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
>跟踪 URI 的有效时间最大为1小时或更短。 如果在某一空闲时间后重新启动脚本, 请使用 get_mlflow_tracking_uri API 来获取新的 URI。

设置 MLflow 试验名称`set_experiment()` , 并开始`start_run()`运行定型。 然后使用`log_metric()`激活 MLflow 日志记录 API 并开始记录定型运行指标。

```Python
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    mlflow.log_metric('alpha', 0.03)
```

### <a name="remote-runs"></a>远程运行

远程运行使你可以在更强大的计算 (例如启用 GPU 的虚拟机或机器学习计算群集) 上训练模型。 请参阅[设置模型定型的计算目标](how-to-set-up-training-targets.md), 了解不同的计算选项。

通过[`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)类配置计算和定型运行环境。 在`mlflow`环境[`CondaDependencies`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies?view=azure-ml-py)部分中包括和`azure-contrib-run` pip 包。 然后, [`ScriptRunConfig`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig?view=azure-ml-py)将远程计算作为计算目标进行构造。

```Python
from azureml.core import Environment
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

在训练脚本中, 导`mlflow`入以使用 MLflow 日志记录 api, 并开始记录运行指标。

```Python
import mlflow

with mlflow.start_run():
    mlflow.log_metric('example', 1.23)
```

使用此计算和训练运行配置时, 请`Experiment.submit('train.py')`使用方法提交运行。 这会自动设置 MLflow 跟踪 URI, 并将日志记录从 MLflow 定向到你的工作区。

```Python
run = exp.submit(src)
```

### <a name="mlflow-with-azure-databricks-runs"></a>Azure Databricks 运行的 MLflow

若要使用 Azure Databricks 运行 Mlflow 试验, 需要首先创建[Azure Databricks 工作区和群集](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)。 在群集中, 请确保从 PyPi 安装*mlflow*库, 以确保群集有权访问所需的函数和类。

#### <a name="install-libraries"></a>安装库

若要在群集上安装库, 请导航到 "**库**" 选项卡, 然后单击 "**安装新**"

 ![mlflow 与 azure 机器学习示意图](media/how-to-use-mlflow/azure-databricks-cluster-libraries.png)

在 "**包**" 字段中, 键入 mlflow, 然后单击 "安装"。 根据需要重复此步骤, 以将其他附加包安装到群集中进行试验。

 ![mlflow 与 azure 机器学习示意图](media/how-to-use-mlflow/install-libraries.png)

#### <a name="notebook-and-workspace-set-up"></a>笔记本和工作区设置

设置群集后, 请导入你的实验笔记本, 将其打开, 并将你的群集附加到其中。

下面的代码应在实验笔记本中。 这将获取用于实例化你的工作区的 Azure 订阅的详细信息。 这假定你已有一个资源组和 Azure 机器学习工作区, 否则你可以[创建它们](setup-create-workspace.md#portal)。 

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
#### <a name="set-mlflow-tracking-uri"></a>设置 MLflow 跟踪 URI
实例化工作区后, 设置 MLflow 跟踪 URI。 这样, 便可以将 MLflow 跟踪链接到 Azure 机器学习工作区。 此后, 你的所有实验都将位于托管 Azure 机器学习跟踪服务中。

```python
uri = ws.get_mlflow_tracking_uri()
mlflow.set_tracking_uri(uri)
```

在训练脚本中, 导入 mlflow, 以使用 MLflow 日志记录 Api, 并开始记录运行指标。 下面的示例记录了 epoch 丢失指标。 

```python
import mlflow 
mlflow.log_metric('epoch_loss', loss.item()) 
```

## <a name="view-metrics-and-artifacts-in-your-workspace"></a>查看工作区中的指标和项目

MLflow 日志记录中的指标和项目将保存在工作区中。 若要随时查看, 请导航到工作区, 并按名称查找[Azure 门户](https://portal.azure.com)上的试验, 或者运行以下代码。 

```python
run.get_metrics()
ws.get_details()
```

## <a name="deploy-mlflow-models-as-a-web-service"></a>将 MLflow 模型部署为 web 服务

将 MLflow 试验作为 Azure 机器学习 web 服务进行部署, 可以利用 Azure 机器学习模型管理和数据偏差检测功能, 并将其应用于生产模型。

![mlflow 与 azure 机器学习示意图](media/how-to-use-mlflow/mlflow-diagram-deploy.png)

### <a name="log-your-model"></a>记录模型
部署之前, 请确保已保存模型, 以便可以引用它及其部署的路径位置。 在训练脚本中, 应存在类似于以下[mlflow _model ()](https://www.mlflow.org/docs/latest/python_api/mlflow.sklearn.html)方法的代码, 以将模型保存到指定的输出目录。 

```python
# change sklearn to pytorch, tensorflow, etc. based on your experiment's framework 
import mlflow.sklearn

# Save the model to the outputs directory for capture
mlflow.sklearn.log_model(regression_model, model_save_path)
```
>[!NOTE]
> `conda_env`包括参数以传递此模型应在其中运行的依赖项和环境的字典表示形式。

### <a name="retrieve-model-from-previous-run"></a>检索以前运行的模型

若要检索所需的运行, 我们需要运行 ID 和在其中保存模型的运行历史记录中的路径。 

```python
# gets the list of runs for your experiment as an array
experiment_name = 'experiment-with-mlflow'
exp = ws.experiments[experiment_name]
runs = list(exp.get_runs())

# get the run ID and the path in run history
runid = runs[0].id
model_save_path = 'model'
```

### <a name="create-docker-image"></a>创建 Docker 映像

`mlflow.azureml.build_image()`函数以框架感知的方式从保存的模型生成 Docker 映像。 它会自动创建框架特定的推断包装器代码, 并为您指定包依赖关系。 指定模型路径、工作区、运行 ID 和其他参数。

在下面的代码中, 我们使用*运行:/< >/model*作为 scikit-learn 学习试验的 model_uri 路径, 生成一个 docker 映像。

```python
import mlflow.azureml

azure_image, azure_model = mlflow.azureml.build_image(model_uri='runs:/{}/{}'.format(runid, model_save_path),
                                                      workspace=ws,
                                                      model_name='sklearn-model',
                                                      image_name='sklearn-image',
                                                      synchronous=True)
```
创建 Docker 映像可能需要几分钟的时间。 

### <a name="deploy-the-docker-image"></a>部署 Docker 映像 

创建映像后, 使用 Azure 机器学习 SDK 将映像部署为 web 服务。

首先, 指定部署配置。 Azure 容器实例 (ACI) 适用于快速开发测试部署, 而 Azure Kubernetes 服务 (AKS) 适用于可缩放的生产部署。

#### <a name="deploy-to-aci"></a>部署到 ACI

通过[deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)方法设置部署配置。 你还可以添加标记和说明以帮助跟踪你的 web 服务。

```python
from azureml.core.webservice import AciWebservice, Webservice

# Configure 
aci_config = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                memory_gb=1, 
                                                tags={'method' : 'sklearn'}, 
                                                description='Diabetes model',
                                                location='eastus2')
```

然后, 使用 Azure 机器学习 SDK 的[deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-)方法部署该映像。 

```python
webservice = Webservice.deploy_from_image( image=azure_image, 
                                           workspace=ws, 
                                           name='diabetes-model-1', 
                                           deployment_config=aci_config)

webservice.wait_for_deployment(show_output=True)
```
#### <a name="deploy-to-aks"></a>部署到 AKS

若要部署到 AKS, 你需要创建一个 AKS 群集, 并选择要部署的 Docker 映像。 在此示例中, 我们从 ACI 部署中引入了之前创建的映像。

若要从以前的 ACI 部署获取映像, 我们使用[image](https://docs.microsoft.com/python/api/azureml-core/azureml.core.image.image.image?view=azure-ml-py)类。 

```python
from azureml.core.image import Image

# Get the image by name, you can change this based on the image you want to deploy
myimage = Image(workspace=ws, name='sklearn-image') 
```

创建 AKS 计算可能需要20-25 分钟才能创建新群集

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
通过[deploy_configuration ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none-)方法设置部署配置。 你还可以添加标记和说明以帮助跟踪你的 web 服务。

```python
from azureml.core.webservice import Webservice, AksWebservice
from azureml.core.image import ContainerImage

# Set the web service configuration (using default here with app insights)
aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)

# Unique service name
service_name ='aks-service'
```

然后, 使用 Azure 机器学习 SDK 的[deploy_from_image ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice(class)?view=azure-ml-py#deploy-from-image-workspace--name--image--deployment-config-none--deployment-target-none-)方法部署该映像。 

```python
# Webservice creation using single command
aks_service = Webservice.deploy_from_image( workspace=ws, 
                                            name=service_name,
                                            deployment_config = aks_config
                                            image = myimage,
                                            deployment_target = aks_target)

aks_service.wait_for_deployment(show_output=True)
```

服务部署可能需要几分钟的时间。

## <a name="clean-up-resources"></a>清理资源

如果你不打算在工作区中使用记录的指标和项目, 则单独删除它们的功能当前不可用。 而是删除包含存储帐户和工作区的资源组, 这样就不会产生任何费用:

1. 在 Azure 门户中，选择最左侧的“资源组”  。

   ![在 Azure 门户中删除](media/how-to-use-mlflow/delete-resources.png)

1. 从列表中选择已创建的资源组。

1. 选择“删除资源组”  。

1. 输入资源组名称。 然后选择“删除”  。


## <a name="example-notebooks"></a>示例笔记本

[MLflow With AZURE ML 笔记本](https://aka.ms/azureml-mlflow-examples)演示并扩展本文中介绍的概念。

## <a name="next-steps"></a>后续步骤
* [管理模型](concept-model-management-and-deployment.md)。
* 监视生产模型的[数据偏移](how-to-monitor-data-drift.md)。
