---
title: 将 ML 模型部署到 Kubernetes 服务
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure Kubernetes 服务将 Azure 机器学习模型部署为 Web 服务。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: 03477fa46aaec04c0563ed38b085605dce5b87a1
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751745"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>将模型部署到 Azure Kubernetes 服务群集
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

了解如何使用 Azure 机器学习将模型部署为 Azure Kubernetes 服务 (AKS) 中的 Web 服务。 Azure Kubernetes 服务适用于大规模的生产部署。 如果需要以下一项或多项功能，请使用 Azure Kubernetes 服务：

- 快速响应时间____。
- 自动缩放已部署的服务____。
- 硬件加速选项，如 GPU 和现场可编程门阵列 (FPGA)____。

> [!IMPORTANT]
> 群集缩放并非通过 Azure 机器学习 SDK 提供。 有关缩放 AKS 群集中节点的详细信息，请参阅 
- [手动缩放 AKS 群集中的节点计数](../aks/scale-cluster.md)
- [在 AKS 中设置群集自动缩放程序](../aks/cluster-autoscaler.md)

部署到 Azure Kubernetes 服务时，将部署到连接到工作区的 AKS 群集____。 有两种方法可将 AKS 群集连接到工作区：

* 使用 Azure 机器学习 SDK、机器学习 CLI 或 [Azure 机器学习工作室](https://ml.azure.com)创建 AKS 群集。 此过程会自动将群集连接到工作区。
* 将现有的 AKS 群集附加到 Azure 机器学习工作区。 可使用 Azure 机器学习 SDK、机器学习 CLI 或 Azure 机器学习工作室来附加群集。

AKS 群集和 AML 工作区可以位于不同的资源组中。

> [!IMPORTANT]
> 创建或附加过程是一次性任务。 将 AKS 群集连接到工作区后，便可将其用于部署。 如果不再需要 AKS 群集，可将其拆离或删除。 拆离或删除后，将无法再部署到该群集。

> [!IMPORTANT]
> 建议你在部署到 web 服务之前本地进行调试。 有关详细信息，请参阅 [本地调试](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally)
>
> 还可参阅 Azure 机器学习 - [部署到本地笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)

## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- 工作区中注册的机器学习模型。 如果尚未注册模型，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 或 [Azure 机器学习 Visual Studio Code 扩展](tutorial-setup-vscode-extension.md)。

- 本文中的 Python 代码片段假设设置了以下变量：

    * `ws` - 设置为工作区。
    * `model` - 设置为注册的模型。
    * `inference_config` - 设置为模型的推理配置。

    有关如何设置这些变量的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- 本文中的 CLI 片段假设已创建 `inferenceconfig.json` 文档。 有关如何创建此文档的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

- 如果需要标准负载均衡器 (SLB) 部署在群集中，而不是 (BLB) 的基本负载均衡器，请在 AKS 门户/CLI/SDK 中创建群集，并将其附加到 AML 工作区。

- 如果你的 Azure 策略限制了公共 IP 的创建，则 AKS 群集的创建将失败。 AKS 需要公共 IP 才能 [传出流量](https://docs.microsoft.com/azure/aks/limit-egress-traffic)。 本文还提供有关通过公共 IP 锁定群集传出流量的指导，但少数 FQDN 除外。 可通过两种方法启用公共 IP：
  - 群集可以将默认创建的公共 IP 与 BLB 或 SLB 一起使用，或者
  - 可以不使用公共 IP 创建群集，然后使用具有用户定义的路由的防火墙配置一个公共 IP，如[此处](https://docs.microsoft.com/azure/aks/egress-outboundtype)所述 
  
  AML 控制平面不与此公共 IP 通信。 它与部署的 AKS 控制平面通信。 

- 如果附加 AKS 群集（已[启用授权 IP 范围以访问 API 服务器](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges)），请为该 AKS 群集启用 AML 控制平面 IP 范围。 AML 控制平面是跨配对区域部署的，并且会在 AKS 群集上部署推理 Pod。 如果没有 API 服务器的访问权限，则无法部署推理 Pod。 在 AKS 群集中启用 IP 范围时，请对两个[配对区域]( https://docs.microsoft.com/azure/best-practices-availability-paired-regions)都使用 [IP 范围](https://www.microsoft.com/en-us/download/confirmation.aspx?id=56519)。


  授权 IP 范围仅适用于标准负载均衡器。
 
 - 计算名称在工作区内必须唯一
   - 名称是必须提供的，且长度必须介于 3 到 24 个字符之间。
   - 有效字符为大小写字母、数字和 - 字符。
   - 名称必须以字母开头
   - 名称必须在 Azure 区域内的全部现有计算中都是唯一的。 如果选择的名称不是唯一的，则会显示警报
   
 - 如果要将模型部署到 GPU 节点或 FPGA 节点（或任何特定 SKU），则必须使用该特定 SKU 创建群集。 不支持在现有群集中创建辅助节点池以及在辅助节点池中部署模型。

## <a name="create-a-new-aks-cluster"></a>创建新的 AKS 群集

**时间估计**：大约 10 分钟。

对于工作区而言，创建或附加 AKS 群集是一次性过程。 可以将此群集重复用于多个部署。 如果删除该群集或包含该群集的资源组，则在下次需要进行部署时必须创建新群集。 可将多个 AKS 群集附加到工作区。
 
Azure 机器学习现在支持使用启用了专用链接的 Azure Kubernetes 服务。
若要创建专用 AKS 群集，请[在此处](https://docs.microsoft.com/azure/aks/private-clusters)使用文档

> [!TIP]
> 如果要使用 Azure 虚拟网络保护 AKS 群集，则必须先创建虚拟网络。 有关详细信息，请参阅 [Azure 虚拟网络中的安全试验和推理](how-to-enable-virtual-network.md#aksvnet)。

如果要创建 AKS 群集以用于开发、验证和测试而非生产，则可以将“群集用途”指定为“开发测试”____ ____ ____ ____ ____。

> [!WARNING]
> 如果设置了 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`，则所创建的群集不适用于生产级别的流量，并且可能会增加推理时间。 开发/测试群集也不保证容错能力。 对于开发/测试群集，建议至少拥有 2 个虚拟 CPU。

以下示例演示如何使用 SDK 和 CLI 创建新的 AKS 群集：

**使用 SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()
# Example configuration to use an existing virtual network
# prov_config.vnet_name = "mynetwork"
# prov_config.vnet_resourcegroup_name = "mygroup"
# prov_config.subnet_name = "default"
# prov_config.service_cidr = "10.0.0.0/16"
# prov_config.dns_service_ip = "10.0.0.10"
# prov_config.docker_bridge_cidr = "172.17.0.1/16"

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> 对于 [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py)，如果为 `agent_count` 和 `vm_size` 选择自定义值，并且 `cluster_purpose` 不是 `DEV_TEST`，则需要确保 `agent_count` 乘以 `vm_size` 的结果大于或等于 12 个虚拟 CPU。 例如，如果对 `vm_size` 使用“Standard_D3_v2”（拥有 4 个虚拟 CPU），则应该为 `agent_count` 选择 3 或更大的数字。
>
> Azure 机器学习 SDK 不支持缩放 AKS 群集。 要缩放群集中的节点，请在 Azure 机器学习工作室中使用 AKS 群集的 UI。 只能更改节点计数，不能更改群集的 VM 大小。

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**使用 CLI**

```azurecli
az ml computetarget create aks -n myaks
```

有关详细信息，请参阅 [az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) 参考文档。

## <a name="attach-an-existing-aks-cluster"></a>附加现有的 AKS 群集

时间估计****：大约 5 分钟。

如果 Azure 订阅中已有 AKS 群集并且其版本为 1.17 或更低版本，则可以使用该群集来部署映像。

> [!TIP]
> 现有的 AKS 群集除了位于 Azure 机器学习工作区，还可位于 Azure 区域中。
>
> 如果要使用 Azure 虚拟网络保护 AKS 群集，则必须先创建虚拟网络。 有关详细信息，请参阅 [Azure 虚拟网络中的安全试验和推理](how-to-enable-virtual-network.md#aksvnet)。

将 AKS 群集附加到工作区时，可以通过设置 `cluster_purpose` 参数来定义使用群集的方式。

如果未设置 `cluster_purpose` 参数或设置了 `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`，则群集必须至少具有 12 个可用的虚拟 CPU。

如果设置了 `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`，则群集不必具有 12 个虚拟 CPU。 对于开发/测试，建议至少具有 2 个虚拟 CPU。 但是，针对开发/测试配置的群集不适用于生产级别的流量，并且可能会增加推理时间。 开发/测试群集也不保证容错能力。

> [!WARNING]
> 请勿在工作区中为同一 AKS 群集创建多个同步附件。 例如，使用两个不同的名称将一个 AKS 群集附加到工作区。 每个新附件都会破坏先前存在的附件。
>
> 如果要重新附加 AKS 群集（例如，更改 TLS 或其他群集配置设置），则必须先使用 [AksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--) 删除现有附件。

有关如何使用 Azure CLI 或门户创建 AKS 群集的详细信息，请参阅以下文章：

* [创建 AKS 群集 (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [创建 AKS 群集（门户）](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [创建 AKS 群集（Azure 快速入门模板上的 ARM 模板）](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

以下示例演示如何将现有 AKS 群集附加到工作区：

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

# Wait for the attach process to complete
aks_target.wait_for_completion(show_output = True)
```

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [AksCompute.attach_configuration()](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**使用 CLI**

要使用 CLI 附加现有群集，需要获取现有群集的资源 ID。 请使用以下命令要获取该值。 将 `myexistingcluster` 替换为 AKS 群集的名称。 将 `myresourcegroup` 替换为包含该群集的资源组：

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

此命令返回类似于以下文本的值：

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

要将现有群集附加到工作区，请使用以下命令。 将 `aksresourceid` 替换为上一命令返回的值。 将 `myresourcegroup` 替换为包含工作区的资源组。 将 `myworkspace` 替换为工作区名称。

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

有关详细信息，请参阅 [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks) 参考文档。

## <a name="deploy-to-aks"></a>部署到 AKS

要将模型部署到 Azure Kubernetes 服务，请创建一个描述所需计算资源的部署配置____。 例如，核心和内存的数量。 此外，还需要一个推理配置，描述托管模型和 Web 服务所需的环境。 有关如何创建推理配置的详细信息，请参阅[部署模型的方式和位置](how-to-deploy-and-where.md)。

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
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>使用 CLI

要使用 CLI 进行部署，请使用以下命令。 将 `myaks` 替换为 AKS 计算目标的名称。 将 `mymodel:1` 替换为注册的模型的名称和版本。 将 `myservice` 替换为要赋予此服务的名称：

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

有关详细信息，请参阅 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) 参考文档。

### <a name="using-vs-code"></a>使用 VS Code

有关如何使用 VS Code 的信息，请参阅[通过 VS Code 扩展部署到 AKS](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)。

> [!IMPORTANT]
> 通过 VS Code 进行部署要求提前创建 AKS 群集或将其附加到工作区。

### <a name="understand-the-deployment-processes"></a>了解部署过程

在 Kubernetes 和 Azure 机器学习中都会用到“部署”一词。 "部署" 在这两个上下文中具有不同的含义。 在 Kubernetes 中，`Deployment` 是使用声明性 YAML 文件指定的具体实体。 Kubernetes `Deployment` 具有明确的生命周期，并与其他 Kubernetes 实体（如 `Pods` 和 `ReplicaSets`）有具体的关系。 可以从[什么是 Kubernetes？](https://aka.ms/k8slearning)中的文档和视频了解 Kubernetes。

在 Azure 机器学习中，“部署”在更普遍的意义上用于提供和清理项目资源。 Azure 机器学习认为属于部署的步骤包括：

1. 将项目文件夹中的文件压缩，忽略那些在 .amlignore 或 .gitignore 中指定的文件
1. 纵向扩展计算群集（与 Kubernetes 相关）
1. 构建 dockerfile 或将其下载到计算节点（与 Kubernetes 相关）
    1. 系统计算以下各项的哈希： 
        - 基础映像 
        - 自定义 Docker 步骤（请参阅[使用自定义 Docker 基础映像部署模型](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-custom-docker-image)）
        - Conda 定义 YAML（请参阅[在 Azure 机器学习中创建和使用软件环境](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments)）
    1. 在工作区 Azure 容器注册表 (ACR) 中查找时，系统使用此哈希作为键
    1. 如果找不到，它会在全局 ACR 中寻找匹配项
    1. 如果找不到，系统会生成新映像（该映像会被缓存并注册到工作区 ACR）
1. 将压缩的项目文件下载到计算节点上的临时存储
1. 解压缩项目文件
1. 执行 `python <entry script> <arguments>` 的计算节点
1. 将写入 `./outputs` 的日志、模型文件和其他文件保存到与工作区关联的存储帐户
1. 纵向缩减计算，包括删除临时存储（与 Kubernetes 相关）

使用 AKS 时，Kubernetes 使用按上述方法生成或找到的 dockerfile 来控制计算的纵向扩展和缩减。 

## <a name="deploy-models-to-aks-using-controlled-rollout-preview"></a>使用受控推出（预览版）将模型部署到 AKS

使用终结点以受控的方式分析和提升模型版本。 最多可以在一个终结点后方部署六个版本。 终结点提供以下功能：

* 配置__发送到每个终结点的评分流量百分比__。 例如，将 20% 的流量路由到终结点“test”，将 80% 路由到“production”。

    > [!NOTE]
    > 如果不按 100% 的流量计算，则所有剩余百分比的流量将路由到默认终结点版本____。 例如，如果将终结点版本“test”配置为获取 10% 的流量，将“prod”配置为 30%，则剩余的 60% 将发送到默认终结点版本。
    >
    > 创建的第一个终结点版本将自动配置为默认版本。 可通过在创建或更新终结点版本时设置 `is_default=True` 来更改此设置。
     
* 将终结点版本标记为“对照”或“实验”____ ____。 例如，当前的生产终结点版本可能为“对照”版本，而可能的新模型将部署为“实验”版本。 评估“实验”版本的性能后，如果该版本优于当前的“对照”版本，则其可能会提升为新的生产/对照版本。

    > [!NOTE]
    > 只能有一个“对照”版本____。 可以有多个“实验”版本。

可以启用 App Insights 来查看终结点和已部署版本的操作指标。

### <a name="create-an-endpoint"></a>创建终结点
做好部署模型的准备后，请创建一个评分终结点，并部署第一个版本。 以下示例演示如何使用 SDK 部署和创建终结点。 将第一个部署定义为默认版本，这意味着所有版本中未指定的百分比的流量都将流向默认版本。  

> [!TIP]
> 在下面的示例中，所作配置将初始终结点版本设置为处理 20% 的流量。 由于这是第一个终结点，因此它也是默认版本。 而且，由于我们没有用于处理其余 80% 流量的其他版本，因此这些流量也将其路由到默认版本。 在部署了可处理一定百分比流量的其他版本以前，此版本实际将接收 100% 的流量。

```python
import azureml.core,
from azureml.core.webservice import AksEndpoint
from azureml.core.compute import AksCompute
from azureml.core.compute import ComputeTarget
# select a created compute
compute = ComputeTarget(ws, 'myaks')
namespace_name= endpointnamespace
# define the endpoint and version name
endpoint_name = "mynewendpoint"
version_name= "versiona"
# create the deployment config and define the scoring traffic percentile for the first deployment
endpoint_deployment_config = AksEndpoint.deploy_configuration(cpu_cores = 0.1, memory_gb = 0.2,
                                                              enable_app_insights = True,
                                                              tags = {'sckitlearn':'demo'},
                                                              description = "testing versions",
                                                              version_name = version_name,
                                                              traffic_percentile = 20)
 # deploy the model and endpoint
 endpoint = Model.deploy(ws, endpoint_name, [model], inference_config, endpoint_deployment_config, compute)
 # Wait for he process to complete
 endpoint.wait_for_deployment(True)
 ```

### <a name="update-and-add-versions-to-an-endpoint"></a>更新版本并将其添加到终结点

将其他版本添加到终结点，并配置流向该版本的评分流量的百分比。 有两种类型的版本：控制版本和处理版本。 可设置多个“实验”版本来帮助进行与单个“对照”版本之间的比较。

> [!TIP]
> 由以下代码段创建的第二个版本可接受 10% 的流量。 第一个版本配置为 20%，因此总共仅为特定版本配置了 30% 的流量。 剩余的 70% 将发送到第一个终结点版本，因为它也是默认版本。

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
endpoint.wait_for_deployment(True)
```

更新或删除终结点中的现有版本。 可更改版本的默认类型、控件类型和流量百分比。 在下面的示例中，第二个版本会将其流量增加到 40% 且其现在为默认版本。

> [!TIP]
> 运行以下代码段之后，现在第二个版本变为默认版本。 它现在配置为 40%，而原始版本仍配置为 20%。 这意味着，还有 40% 的流量未计入版本配置。 剩余的流量将路由到第二个版本，因为它现在为默认版本。 它实际上接收了 80% 的流量。

 ```python
from azureml.core.webservice import AksEndpoint

# update the version's scoring traffic percentage and if it is a default or control type
endpoint.update_version(version_name=endpoint.versions["versionb"].name,
                        description="my second version update",
                        traffic_percentile=40,
                        is_default=True,
                        is_control_version_type=True)
# Wait for the process to complete before deleting
endpoint.wait_for_deployment(true)
# delete a version in an endpoint
endpoint.delete_version(version_name="versionb")

```


## <a name="web-service-authentication"></a>Web 服务身份验证

部署到 Azure Kubernetes 服务时，默认会启用基于密钥的身份验证____。 此外，还可以启用基于令牌的身份验证____。 基于令牌的身份验证要求客户端使用 Azure Active Directory 帐户来请求身份验证令牌，该令牌用于向已部署的服务发出请求。

要禁用身份验证，请在创建部署配置时设置 `auth_enabled=False` 参数____。 下面的示例使用 SDK 来禁用身份验证：

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

有关如何从客户端应用程序进行身份验证的信息，请参阅[使用部署为 Web 服务的 Azure 机器学习模型](how-to-consume-web-service.md)。

### <a name="authentication-with-keys"></a>使用密钥进行身份验证

如果已启用密钥身份验证，可以使用 `get_keys` 方法来检索主要和辅助身份验证密钥：

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> 如需重新生成密钥，请使用 [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)

### <a name="authentication-with-tokens"></a>使用令牌进行身份验证

要启用令牌身份验证，请在创建或更新部署时设置 `token_auth_enabled=True` 参数。 下面的示例使用 SDK 来启用令牌身份验证：

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

如果启用了令牌身份验证，可以使用 `get_token` 方法来检索 JWT 令牌以及该令牌的到期时间：

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> 需要在令牌的 `refresh_by` 时间后请求一个新令牌。
>
> Microsoft 强烈建议在 Azure Kubernetes 服务群集所在的相同区域中创建 Azure 机器学习工作区。 要使用令牌进行身份验证，Web 服务将调用创建 Azure 机器学习工作区的区域。 如果工作区区域不可用，即使群集和工作区不在同一区域，也将无法获取 Web 服务的令牌。 这实际上会导致在工作区的区域再次可用之前，基于令牌的身份验证不可用。 此外，群集区域和工作区区域的距离越远，获取令牌所需的时间就越长。
>
> 若要检索令牌，必须使用 Azure 机器学习 SDK 或 [az ml service get-access-token](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-get-access-token) 命令。

## <a name="next-steps"></a>后续步骤

* [虚拟网络中的安全试验和推理](how-to-enable-virtual-network.md)
* [如何使用自定义 Docker 映像部署模型](how-to-deploy-custom-docker-image.md)
* [部署疑难解答](how-to-troubleshoot-deployment.md)
* [更新 Web 服务](how-to-deploy-update-web-service.md)
* [使用 TLS 通过 Azure 机器学习保护 Web 服务](how-to-secure-web-service.md)
* [使用部署为 Web 服务的机器学习模型](how-to-consume-web-service.md)
* [使用 Application Insights 监视 Azure 机器学习模型](how-to-enable-app-insights.md)
* [为生产环境中的模型收集数据](how-to-enable-data-collection.md)
