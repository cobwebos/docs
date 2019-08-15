---
title: 如何将模型部署到 Azure Kubernetes 服务
titleSuffix: Azure Machine Learning service
description: 了解如何使用 Azure Kubernetes 服务将 Azure 机器学习服务模型部署为 web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 6949f46345a5520ec3e09508b6d81994f9a7deb5
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036204"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>将模型部署到 Azure Kubernetes Service 群集

了解如何使用 Azure 机器学习服务将模型部署为 Azure Kubernetes Service (AKS) 上的 web 服务。 Azure Kubernetes 服务适用于大规模生产部署。 如果需要以下一个或多个功能, 请使用 Azure Kubernetes 服务:

- __快速响应时间__。
- 已部署服务的自动__缩放__。
- __硬件加速__选项, 如 GPU 和现场可编程入口阵列 (FPGA)。

> [!IMPORTANT]
> 不是通过 Azure 机器学习 SDK 提供的群集缩放。 有关缩放 AKS 群集中节点的详细信息, 请参阅[缩放 AKS 群集中的节点计数](../../aks/scale-cluster.md)。

部署到 Azure Kubernetes 服务时, 部署到__连接到工作区__的 AKS 群集。 可通过两种方式将 AKS 群集连接到工作区:

* 使用 Azure 机器学习服务 SDK、机器学习 CLI 或 Azure 门户创建 AKS 群集。 此过程会自动将群集连接到工作区。
* 将现有的 AKS 群集附加到 Azure 机器学习服务工作区。 可以使用 Azure 机器学习服务 SDK、机器学习 CLI 或 Azure 门户来连接群集。

> [!IMPORTANT]
> 创建或附件过程是一次性任务。 将 AKS 群集连接到工作区后, 可以将其用于部署。 如果不再需要 AKS 群集, 可以分离或删除它。 Detatched 或删除后, 将无法再部署到群集。

## <a name="prerequisites"></a>先决条件

- Azure 机器学习服务工作区。 有关详细信息, 请参阅[创建 Azure 机器学习服务工作区](how-to-manage-workspace.md)。

- 已在工作区中注册的机器学习模型。 如果没有已注册的模型, 请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、 [Azure 机器学习 Python SDK](https://aka.ms/aml-sdk)或[Azure 机器学习 Visual Studio Code 扩展](how-to-vscode-tools.md)。

- 本文中的__Python__代码片段假设设置了以下变量:

    * `ws`-设置为你的工作区。
    * `model`-设置为已注册的模型。
    * `inference_config`-将设置为模型的推理配置。

    有关设置这些变量的详细信息, 请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- 本文中的__CLI__代码段假定您已创建一个`inferenceconfig.json`文档。 有关创建此文档的详细信息, 请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

## <a name="create-a-new-aks-cluster"></a>创建新的 AKS 群集

**时间估计**：大约 20 分钟。

创建或附加 AKS 群集是工作区的一次过程。 可以将此群集重复用于多个部署。 如果删除群集或包含该群集的资源组, 则下次需要部署时, 必须创建新群集。 可以将多个 AKS 群集附加到工作区。

如果要创建 AKS 群集以进行__开发__、__验证__和__测试__, 而不是生产, 则可以将__群集目的__指定为进行开发__测试__。

> [!WARNING]
> 如果设置`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, 则创建的群集不适用于生产级别的流量, 可能会增加推理时间。 开发/测试群集也不保证容错能力。 对于开发/测试群集, 建议至少2个虚拟 Cpu。

以下示例演示如何使用 SDK 和 CLI 创建新的 AKS 群集:

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
> 对于[`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), 如果你为`agent_count`和`vm_size`选择`agent_count` `cluster_purpose` 了自`DEV_TEST`定义值, 并且不是, 则需要确保乘以大于或等于12个虚拟cpu。`vm_size` 例如, 如果使用的是`vm_size`具有4个虚拟 cpu 的 "Standard_D3_v2", 则应`agent_count`选择3个或更多的。
>
> Azure 机器学习 SDK 不支持缩放 AKS 群集。 若要缩放群集中的节点, 请在 Azure 门户中使用 AKS 群集的 UI。 只能更改节点计数, 而不能更改群集的 VM 大小。

有关此示例中使用的类、方法和参数的详细信息, 请参阅以下参考文档:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**使用 CLI**

```azurecli
az ml computetarget create aks -n myaks
```

有关详细信息, 请参阅[az ml computetarget create ask](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) reference。

## <a name="attach-an-existing-aks-cluster"></a>附加现有的 AKS 群集

**估计时间:** 大约5分钟。

如果 Azure 订阅中已有 AKS 群集, 并且它是版本 1.12. # #, 则可以使用它来部署映像。

> [!TIP]
> 现有的 AKS 群集可以位于 Azure 区域中, 而不是 Azure 机器学习服务工作区。

> [!WARNING]
> 将 AKS 群集附加到工作区时, 可以通过设置`cluster_purpose`参数来定义使用群集的方式。
>
> 如果未设置`cluster_purpose`参数或设置`cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, 则群集必须有至少12个可用的虚拟 cpu。
>
> 如果设置`cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`了, 则群集不需要具有12个虚拟 cpu。 建议至少2个虚拟 Cpu 用于开发/测试。 但是, 为开发/测试配置的群集不适用于生产级别的流量, 可能会增加推理时间。 开发/测试群集也不保证容错能力。

有关使用 Azure CLI 或门户创建 AKS 群集的详细信息, 请参阅以下文章:

* [创建 AKS 群集 (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [创建 AKS 群集 (门户)](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

以下示例演示了如何将现有的 AKS 1.12. # # 群集附加到工作区:

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

有关此示例中使用的类、方法和参数的详细信息, 请参阅以下参考文档:

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**使用 CLI**

若要使用 CLI 附加现有群集, 需要获取现有群集的资源 ID。 若要获取此值, 请使用以下命令。 将`myexistingcluster`替换为 AKS 群集的名称。 将`myresourcegroup`替换为包含群集的资源组:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

此命令会返回类似于以下文本的值：

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

若要将现有群集附加到工作区, 请使用以下命令。 替换`aksresourceid`为上一命令返回的值。 将`myresourcegroup`替换为包含工作区的资源组。 将`myworkspace`替换为你的工作区名称。

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

有关详细信息, 请参阅[az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) reference。

## <a name="deploy-to-aks"></a>部署到 AKS

若要将模型部署到 Azure Kubernetes 服务, 请创建一个描述需要计算资源的__部署配置__。 例如, 核心数和内存数。 还需要一个__推理配置__, 其中描述了托管模型和 web 服务所需的环境。 有关创建推理配置的详细信息, 请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

### <a name="using-the-sdk"></a>使用 SDK

```python
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

有关此示例中使用的类、方法和参数的详细信息, 请参阅以下参考文档:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [部署模型](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [Webservice. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>使用 CLI

若要使用 CLI 进行部署, 请使用以下命令。 替换`myaks`为 AKS 计算目标的名称。 替换`mymodel:1`为注册的模型的名称和版本。 替换`myservice`为要为此服务提供的名称:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

有关详细信息, 请参阅[az ml 模型部署](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)参考。 

### <a name="using-vs-code"></a>使用 VS Code

有关使用 VS Code 的信息, 请参阅[通过 VS Code 扩展部署到 AKS](how-to-vscode-tools.md#deploy-and-manage-models)。

> [!IMPORTANT] 
> 通过 VS Code 部署要求提前创建或将 AKS 群集附加到工作区。

## <a name="web-service-authentication"></a>Web 服务身份验证

部署到 Azure Kubernetes 服务时, 默认情况下启用__基于密钥的__身份验证。 还可以启用__令牌__身份验证。 令牌身份验证要求客户端使用 Azure Active Directory 帐户来请求身份验证令牌, 该令牌用于向部署的服务发出请求。

若要__禁用__身份验证, `auth_enabled=False`请在创建部署配置时设置参数。 下面的示例使用 SDK 禁用身份验证:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

有关从客户端应用程序进行身份验证的信息, 请参阅[使用部署为 web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)。

### <a name="authentication-with-keys"></a>密钥身份验证

如果启用密钥身份验证, 则可以使用`get_keys`方法检索主要和辅助身份验证密钥:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如果需要重新生成密钥, 请使用[`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>带令牌的身份验证

若要启用令牌身份验证, `token_auth_enabled=True`请在创建或更新部署时设置参数。 下面的示例使用 SDK 启用令牌身份验证:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

如果启用令牌身份验证, 则可以使用`get_token`方法检索 JWT 令牌和令牌的过期时间:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 需要在令牌`refresh_by`时间之后请求新令牌。
>
> Microsoft 强烈建议在 Azure Kubernetes Service 群集所在的同一区域中创建 Azure 机器学习工作区。 若要使用令牌进行身份验证, web 服务将调用创建 Azure 机器学习工作区的区域。 如果工作区的区域不可用, 则即使群集与工作区位于不同的区域, 也无法获取 web 服务的令牌。 这实际上会导致 Azure AD 身份验证不可用, 直到工作区的区域再次可用。 此外, 群集区域与工作区区域之间的距离越大, 提取令牌所需的时间就越长。

## <a name="update-the-web-service"></a>更新 Web 服务

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>后续步骤

* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [部署故障排除](how-to-troubleshoot-deployment.md)
* [使用 SSL 保护 Azure 机器学习 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
