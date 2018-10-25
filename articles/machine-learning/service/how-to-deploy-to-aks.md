---
title: 将模型从 Azure 机器学习服务部署到 Kubernetes | Microsoft 文档
description: 了解如何将模型从 Azure 机器学习服务部署到 Azure Kubernetes 服务。 模型作为 Web 服务进行部署。 Azure Kubernetes 服务适用于大规模的生产工作负载。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.author: raymondl
author: raymondlaghaeian
manager: cgronlun
ms.reviewer: larryfr
ms.date: 09/24/2018
ms.openlocfilehash: 3ab32388e0a35f4abf3866aa0a84ee0628b0570c
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318191"
---
# <a name="how-to-deploy-models-from-azure-machine-learning-service-to-azure-kubernetes-service"></a>如何将模型从 Azure 机器学习服务部署到 Azure Kubernetes 服务

对于大规模生产方案，可以将模型部署到 Azure Kubernetes 服务 (AKS)。 Azure 机器学习可以使用现有的 AKS 群集或在部署期间创建的新群集。 模型作为 Web 服务部署到 ASK。

通过部署到 AKS，可以为 Web 服务提供自动缩放、日志记录、模型数据收集和快速响应时间功能。

## <a name="prerequisites"></a>先决条件

- Azure 订阅。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

- 一个 Azure 机器学习服务工作区、一个包含脚本的本地目录，以及所安装的用于 Python 的 Azure 机器学习 SDK。 了解如何通过[如何配置开发环境](how-to-configure-environment.md)一文满足这些先决条件。

- 已定型的机器学习模型。 如果没有模型，请参阅[训练图像分类模型](tutorial-train-models-with-aml.md)教程。

## <a name="initialize-the-workspace"></a>初始化工作区

若要初始化工作区，请加载包含工作区信息的 `config.json` 文件。

```python
from azureml.coreazureml import Workspace

# Load existing workspace from the config file info.
ws = Workspace.from_config()
```

## <a name="register-the-model"></a>注册模型

若要注册现有模型，请指定模型路径、说明和标记。

```python
from azureml.core.model import Model

model = Model.register(model_path = "model.pkl", # this points to a local file
                        model_name = "best_model", # this is the name the model is registered as
                        tags = {"data": "diabetes", "type": "regression"},
                        description = "Ridge regression model to predict diabetes",
                        workspace = ws)
```

## <a name="create-a-docker-image"></a>创建 Docker 映像

Azure Kubernetes 服务使用 Docker 映像。 若要创建映像，请使用以下步骤：

1. 若要配置映像，必须创建评分脚本和环境文件。 有关创建脚本和环境文件的示例，请参阅以下部分的图像分类示例：

    * [创建评分脚本 (score.py)](tutorial-deploy-models-with-aml.md#create-scoring-script)

    * [创建环境文件 (myenv.yml)](tutorial-deploy-models-with-aml.md#create-environment-file) 

   以下示例使用这些文件来配置映像：

    ```python
    from azureml.core.image import ContainerImage

    # Image configuration
    image_config = ContainerImage.image_configuration(execution_script = "score.py",
                                                        runtime = "python",
                                                        conda_file = "myenv.yml",
                                                        description = "Image with ridge regression model",
                                                        tags = {"data": "diabetes", "type": "regression"}
                                                        )
    ```

1. 若要创建映像，请使用模型和映像配置。 此操作可能需要大约 5 分钟才能完成：

    ```python
    image = ContainerImage.create(name = "myimage1",
                                    # this is the model object
                                    models = [model],
                                    image_config = image_config,
                                    workspace = ws)

    # Wait for the create process to complete
    image.wait_for_creation(show_output = True)
    ```

## <a name="create-the-aks-cluster"></a>创建 AKS 群集

以下代码片段演示如何创建 AKS 群集。 此过程需要大约 20 分钟才能完成：

> [!IMPORTANT]
> 创建 AKS 群集是工作区的一次性过程。 创建后，即可重复使用此群集进行多个部署。 如果删除该群集或包含该群集的资源组，则在下次需要进行部署时必须创建新群集。


```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'aml-aks-1' 
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws, 
                                    name = aks_name, 
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
print(aks_target.provisioning_state)
print(aks_target.provisioning_errors)
```

### <a name="attach-existing-aks-cluster-optional"></a>附加现有的 AKS 群集（可选）

如果 Azure 订阅中已有 AKS 群集，则可以用其部署映像。 以下代码片段演示如何将群集附加到工作区。 

> [!IMPORTANT]
> 仅支持 AKS 版本 1.11.2。

```python
# Get the resource id from https://porta..azure.com -> Find your resource group -> click on the Kubernetes service -> Properties
resource_id = '/subscriptions/<your subscription id>/resourcegroups/<your resource group>/providers/Microsoft.ContainerService/managedClusters/<your aks service name>'

# Set to the name of the cluster
cluster_name='my-existing-aks' 

# Attatch the cluster to your workgroup
aks_target = AksCompute.attach(workspace=ws, name=cluster_name, resource_id=resource_id)

# Wait for the operation to complete
aks_target.wait_for_completion(True)
```

## <a name="deploy-your-web-service"></a>部署 Web 服务

以下代码片段演示如何将映像部署到 AKS 群集：

```python
from azureml.core.webservice import Webservice, AksWebservice

# Set configuration and service name
aks_config = AksWebservice.deploy_configuration()
aks_service_name ='aks-service-1'
# Deploy from image
aks_service = Webservice.deploy_from_image(workspace = ws, 
                                            name = aks_service_name,
                                            image = image,
                                            deployment_config = aks_config,
                                            deployment_target = aks_target)
# Wait for the deployment to complete
aks_service.wait_for_deployment(show_output = True)
print(aks_service.state)
```

> [!TIP]
> 如果在部署期间出现错误，请使用 `aks_service.get_logs()` 查看 AKS 服务日志。 记录的信息可能指示错误的原因。

## <a name="test-the-web-service"></a>测试 Web 服务

使用 `aks_service.run()` 测试 Web 服务。 以下代码片段演示如何将数据传递给该服务并显示预测：

```python
import json

test_sample = json.dumps({'data': [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]})
test_sample = bytes(test_sample,encoding = 'utf8')

prediction = aks_service.run(input_data = test_sample)
print(prediction)
```

## <a name="cleanup"></a>清理

若要删除服务、映像和模型，请使用以下代码片段：

```python
aks_service.delete()
image.delete()
model.delete()
```
