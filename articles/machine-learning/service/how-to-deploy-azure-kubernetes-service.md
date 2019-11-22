---
title: 如何将模型部署到 Azure Kubernetes 服务
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure Kubernetes 服务将 Azure 机器学习模型部署为 web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 11/06/2019
ms.openlocfilehash: 72ef43c4409fcc483f0fb0c862813fe6a67954ca
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74276761"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>将模型部署到 Azure Kubernetes Service 群集
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何使用 Azure 机器学习将模型部署为 Azure Kubernetes Service （AKS）上的 web 服务。 Azure Kubernetes 服务适用于大规模生产部署。 如果需要以下一个或多个功能，请使用 Azure Kubernetes 服务：

- __快速响应时间__。
- 已部署服务的自动__缩放__。
- __硬件加速__选项，如 GPU 和现场可编程入口阵列（FPGA）。

> [!IMPORTANT]
> 不通过 Azure 机器学习 SDK 提供群集缩放。 有关缩放 AKS 群集中节点的详细信息，请参阅[缩放 AKS 群集中的节点计数](../../aks/scale-cluster.md)。

部署到 Azure Kubernetes 服务时，部署到__连接到工作区__的 AKS 群集。 可通过两种方式将 AKS 群集连接到工作区：

* 使用 Azure 机器学习 SDK、机器学习 CLI 或[Azure 机器学习 studio](https://ml.azure.com)创建 AKS 群集。 此过程会自动将群集连接到工作区。
* 将现有的 AKS 群集附加到 Azure 机器学习工作区。 可以使用 Azure 机器学习 SDK、机器学习 CLI 或 Azure 机器学习 studio 附加群集。

> [!IMPORTANT]
> 创建或附件过程是一次性任务。 将 AKS 群集连接到工作区后，可以将其用于部署。 如果不再需要 AKS 群集，可以分离或删除它。 Detatched 或删除后，将无法再部署到群集。

## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- 已在工作区中注册的机器学习模型。 如果没有已注册的模型，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、 [Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)或[Azure 机器学习 Visual Studio Code 扩展](how-to-vscode-tools.md)。

- 本文中的__Python__代码片段假设设置了以下变量：

    * `ws`-设置为你的工作区。
    * `model`-设置为已注册的模型。
    * `inference_config`-设置为模型的推理配置。

    有关设置这些变量的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- 本文中的__CLI__代码段假设你已创建 `inferenceconfig.json` 文档。 有关创建此文档的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="create-a-new-aks-cluster"></a>创建新的 AKS 群集

**估计时间**：约20分钟。

创建或附加 AKS 群集是工作区的一次过程。 可以将此群集重复用于多个部署。 如果删除群集或包含该群集的资源组，则下次需要部署时，必须创建新群集。 可以将多个 AKS 群集附加到工作区。

> [!TIP]
> 如果要使用 Azure 虚拟网络保护 AKS 群集，必须先创建虚拟网络。 有关详细信息，请参阅[通过 Azure 虚拟网络进行安全试验和推理](how-to-enable-virtual-network.md#aksvnet)。

如果要创建 AKS 群集以进行__开发__、__验证__和__测试__，而不是生产，则可以将__群集目的__指定为进行开发__测试__。

> [!WARNING]
> 如果设置 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`，则创建的群集不适用于生产级别的流量，可能会增加推理时间。 开发/测试群集也不保证容错能力。 对于开发/测试群集，建议至少2个虚拟 Cpu。

以下示例演示如何使用 SDK 和 CLI 创建新的 AKS 群集：

**使用 SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> 对于[`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果为 `agent_count` 和 `vm_size`选取了自定义值，并且 `cluster_purpose` 不 `DEV_TEST`，则需要确保 `agent_count` 乘以 `vm_size` 大于或等于12个虚拟 cpu。 例如，如果使用的是具有4个虚拟 Cpu 的 "Standard_D3_v2" `vm_size`，则应选择3个或更高的 `agent_count`。
>
> Azure 机器学习 SDK 不支持缩放 AKS 群集。 若要缩放群集中的节点，请在 Azure 机器学习 studio 中使用 AKS 群集的 UI。 只能更改节点计数，而不能更改群集的 VM 大小。

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none--load-balancer-type-none-)
* [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**使用 CLI**

```azurecli
az ml computetarget create aks -n myaks
```

有关详细信息，请参阅[az ml computetarget create ask](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference。

## <a name="attach-an-existing-aks-cluster"></a>附加现有的 AKS 群集

**估计时间：** 大约5分钟。

如果 Azure 订阅中已有 AKS 群集，并且该群集低于版本1.14，则可以使用它来部署映像。

> [!TIP]
> 现有的 AKS 群集可以位于 Azure 区域，而不是 Azure 机器学习工作区。
>
> 如果要使用 Azure 虚拟网络保护 AKS 群集，必须先创建虚拟网络。 有关详细信息，请参阅[通过 Azure 虚拟网络进行安全试验和推理](how-to-enable-virtual-network.md#aksvnet)。

将 AKS 群集附加到工作区时，可以通过设置 `cluster_purpose` 参数来定义使用群集的方式。

如果未设置 `cluster_purpose` 参数或设置 `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`，则群集必须有至少12个可用的虚拟 Cpu。

如果设置 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`，则群集不需要有12个虚拟 Cpu。 建议至少2个虚拟 Cpu 用于开发/测试。 但是，为开发/测试配置的群集不适用于生产级别的流量，可能会增加推理时间。 开发/测试群集也不保证容错能力。

> [!WARNING]
> 不要从工作区中创建多个同时从同一 AKS 群集同时同步的附件。 例如，使用两个不同的名称将一个 AKS 群集附加到工作区。 每个新附件将破坏以前的现有附件。
>
> 如果要重新附加 AKS 群集（例如，更改 SSL 或其他群集配置设置），则必须先使用[AksCompute （）](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--)删除现有附件。

有关使用 Azure CLI 或门户创建 AKS 群集的详细信息，请参阅以下文章：

* [创建 AKS 群集（CLI）](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [创建 AKS 群集（门户）](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

以下示例演示了如何将现有的 AKS 群集附加到工作区：

**使用 SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [AksCompute （） attach_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none--load-balancer-type-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**使用 CLI**

若要使用 CLI 附加现有群集，需要获取现有群集的资源 ID。 若要获取此值，请使用以下命令。 将 `myexistingcluster` 替换为 AKS 群集的名称。 将 `myresourcegroup` 替换为包含该群集的资源组：

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

此命令会返回类似于以下文本的值：

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

若要将现有群集附加到工作区，请使用以下命令。 将 `aksresourceid` 替换为上一命令返回的值。 将 `myresourcegroup` 替换为包含工作区的资源组。 将 `myworkspace` 替换为你的工作区名称。

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

有关详细信息，请参阅[az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference。

## <a name="deploy-to-aks"></a>部署到 AKS

若要将模型部署到 Azure Kubernetes 服务，请创建一个描述需要计算资源的__部署配置__。 例如，核心数和内存数。 还需要一个__推理配置__，其中描述了托管模型和 web 服务所需的环境。 有关创建推理配置的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

### <a name="using-the-sdk"></a>使用 SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [部署模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 进行部署，请使用以下命令。 将 `myaks` 替换为 AKS 计算目标的名称。 将 `mymodel:1` 替换为注册的模型的名称和版本。 将 `myservice` 替换为要为此服务提供的名称：

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

有关详细信息，请参阅[az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)参考。 

### <a name="using-vs-code"></a>使用 VS Code

有关使用 VS Code 的信息，请参阅[通过 VS Code 扩展部署到 AKS](how-to-vscode-tools.md#deploy-and-manage-models)。

> [!IMPORTANT] 
> 通过 VS Code 部署要求提前创建或将 AKS 群集附加到工作区。

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>使用受控推出（预览版）将模型部署到 AKS
使用终结点以受控方式分析和升级模型版本。 在单个终结点上部署最多6个版本，并配置到每个已部署版本的评分流量百分比。 可以启用 app insights 来查看终结点和已部署版本的操作指标。

### <a name="create-an-endpoint"></a>创建终结点
准备好部署模型后，请创建评分终结点，并部署第一个版本。 下面的步骤演示如何使用 SDK 部署和创建终结点。 第一个部署将被定义为默认版本，这意味着所有版本中未指定的流量百分比将会转为默认版本。  

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace 
# define the endpoint and version name
endpoint_name = "mynewendpoint",
version_name= "versiona",
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = true, 
                                                              tags = {'sckitlearn':'demo'},
                                                              decription = testing versions,
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>更新版本并将其添加到终结点

将另一个版本添加到终结点，并配置转到该版本的评分流量百分比。 有两种类型的版本：控件和处理版本。 可以使用多个处理版本来帮助比较单个控制版本。 

 ```python
from azureml.core.webservice import AksEndpoint

# add another model deployment to the same endpoint as above
version_name_add = "versionb" 
endpoint.create_version(version_name = version_name_add, 
                        inference_config=inference_config,
                        models=[model], 
                        tags = {'modelVersion':'b'}, 
                        description = "my second version", 
                        traffic_percentile = 10)
```

更新现有版本或在终结点中删除现有版本。 您可以更改版本的默认类型、控件类型和流量百分比。 
 
 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type 
endpoint.update_version(version_name=endpoint.versions["versionb"].name, 
                        description="my second version update", 
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)

# delete a version in an endpoint 
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Web 服务身份验证

部署到 Azure Kubernetes 服务时，默认情况下启用__基于密钥的__身份验证。 你还可以启用__基于令牌的__身份验证。 基于令牌的身份验证要求客户端使用 Azure Active Directory 帐户来请求身份验证令牌，该令牌用于向部署的服务发出请求。

若要__禁用__身份验证，请在创建部署配置时设置 `auth_enabled=False` 参数。 下面的示例使用 SDK 禁用身份验证：

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

有关从客户端应用程序进行身份验证的信息，请参阅[使用部署为 web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)。

### <a name="authentication-with-keys"></a>密钥身份验证

如果启用密钥身份验证，则可以使用 `get_keys` 方法检索主要和辅助身份验证密钥：

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果需要重新生成密钥，请使用[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>带令牌的身份验证

若要启用令牌身份验证，请在创建或更新部署时设置 `token_auth_enabled=True` 参数。 下面的示例使用 SDK 启用令牌身份验证：

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

如果启用令牌身份验证，则可以使用 `get_token` 方法检索 JWT 令牌和令牌的过期时间：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 需要在令牌的 `refresh_by` 时间之后请求新令牌。
>
> Microsoft 强烈建议在 Azure Kubernetes Service 群集所在的同一区域中创建 Azure 机器学习工作区。 若要使用令牌进行身份验证，web 服务将调用创建 Azure 机器学习工作区的区域。 如果工作区的区域不可用，则即使群集与工作区位于不同的区域，也无法获取 web 服务的令牌。 这实际上会导致在工作区的区域再次可用之前，不能使用基于令牌的身份验证。 此外，群集区域与工作区区域之间的距离越大，提取令牌所需的时间就越长。

## <a name="update-the-web-service"></a>更新 Web 服务

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>后续步骤

* [在虚拟网络中保护试验和推理](how-to-enable-virtual-network.md)
* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [部署故障排除](how-to-troubleshoot-deployment.md)
* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
