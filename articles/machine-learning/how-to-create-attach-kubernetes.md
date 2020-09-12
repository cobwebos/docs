---
title: 创建并附加 Azure Kubernetes 服务
titleSuffix: Azure Machine Learning
description: 可以使用 Azure Kubernetes Service (AKS) 将机器学习模型部署为 web 服务。 了解如何通过 Azure 机器学习创建新的 AKS 群集。 你还将了解如何将现有 AKS 群集附加到 Azure 机器学习工作区。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 09/01/2020
ms.openlocfilehash: edd4cc28c6d59f1d6e0c9cabfd5855c72bd3fe73
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89661838"
---
# <a name="create-and-attach-an-azure-kubernetes-service-cluster"></a>创建并附加 Azure Kubernetes 服务群集
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure 机器学习可以将训练的机器学习模型部署到 Azure Kubernetes 服务。 但是，必须首先从 Azure ML 工作区中 __创建__ Azure Kubernetes SERVICE (AKS) 群集，或者 __附加__ 现有的 AKS 群集。 本文提供有关创建和附加群集的信息。

## <a name="prerequisites"></a>先决条件

- Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

- [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 或 [Azure 机器学习 Visual Studio Code 扩展](tutorial-setup-vscode-extension.md)。

- 如果计划使用 Azure 虚拟网络来保护 Azure ML 工作区与 AKS 群集之间的通信，请 [在训练 & 推理一文中阅读网络隔离](how-to-enable-virtual-network.md) 。

## <a name="limitations"></a>限制

- 如果需要 **标准负载均衡器 (SLB) ** 部署在群集中，而不是 (BLB) 的基本负载均衡器，请在 AKS 门户/CLI/SDK 中创建群集，并 **将其附加** 到 AML 工作区。

- 如果你的 Azure 策略限制了公共 IP 地址的创建，则 AKS 群集的创建将失败。 AKS 需要公共 IP 才能 [传出流量](/azure/aks/limit-egress-traffic)。 有关出口流量的文章还提供了通过公共 IP 锁定群集传出流量的指导，只需几个完全限定的域名。 可通过两种方法启用公共 IP：
    - 群集可以将默认创建的公共 IP 与 BLB 或 SLB 一起使用，或者
    - 可以不使用公共 IP 创建群集，然后使用具有用户定义的路由的防火墙配置公共 IP。 有关详细信息，请参阅 [使用用户定义的路由自定义群集传出](/azure/aks/egress-outboundtype)。
    
    AML 控制平面不与此公共 IP 通信。 它与部署的 AKS 控制平面通信。 

- 如果 **附加** 的 AKS 群集 [启用了授权 IP 范围以访问 API 服务器](/azure/aks/api-server-authorized-ip-ranges)，请为 AKS 群集启用 AML 控制平面 IP 范围。 AML 控制平面跨配对区域部署，并在 AKS 群集上部署推理 pod。 如果没有访问 API 服务器，则无法部署推理 pod。 在 AKS 群集中启用 IP 范围时，请对两个[配对区域](/azure/best-practices-availability-paired-regions)都使用 [IP 范围](https://www.microsoft.com/download/confirmation.aspx?id=56519)。

    授权 IP 范围仅适用于标准负载均衡器。

- 如果要使用专用 AKS 群集 (使用 Azure Private Link) ，则必须先创建群集，然后 **将其附加** 到工作区。 有关详细信息，请参阅 [创建专用 Azure Kubernetes 服务群集](/azure/aks/private-clusters)。

- AKS 群集的计算名称在 Azure ML 工作区中必须是唯一的。
    - 名称是必须提供的，且长度必须介于 3 到 24 个字符之间。
    - 有效字符为大小写字母、数字和 - 字符。
    - 名称必须以字母开头。
    - 名称必须在 Azure 区域内的全部现有计算中都是唯一的。 如果你选择的名称不唯一，你将看到一个警报。
   
 - 如果要将模型部署到 **GPU** 节点或 **FPGA** 节点 (或任何特定的 sku) ，则必须创建具有特定 sku 的群集。 不支持在现有群集中创建辅助节点池以及在辅助节点池中部署模型。
 
- 创建或附加群集时，可以选择是否为 __开发测试__ 或 __生产__创建群集。 如果要创建 AKS 群集以进行 __开发__、 __验证__和 __测试__ ，而不是生产，请将 __群集目的__ 设置为 "开发 __测试__"。 如果未指定群集目的，则创建 __生产__ 群集。 

    > [!IMPORTANT]
    > __开发测试__群集不适用于生产级别的流量，可能会增加推理时间。 开发/测试群集也不保证容错能力。

- 创建或附加群集时，如果该群集将用于 __生产__，则它必须至少包含12个 __虚拟 cpu__。 可以通过将群集中的 __节点__ 数与所选 VM 大小提供的 __内核数__ 相乘来计算虚拟 cpu 的数目。 例如，如果你使用的 VM 大小为 "Standard_D3_v2" （具有4个虚拟核心），则应选择 "3" 或 "大于" 节点数。

    对于 __开发测试__ 群集，我们建议至少2个虚拟 cpu。

- Azure 机器学习 SDK 不支持缩放 AKS 群集。 要缩放群集中的节点，请在 Azure 机器学习工作室中使用 AKS 群集的 UI。 只能更改节点计数，不能更改群集的 VM 大小。 有关缩放 AKS 群集中节点的详细信息，请参阅以下文章：

    - [手动缩放 AKS 群集中的节点计数](../aks/scale-cluster.md)
    - [在 AKS 中设置群集自动缩放程序](../aks/cluster-autoscaler.md)

## <a name="create-a-new-aks-cluster"></a>创建新的 AKS 群集

**时间估计**：大约 10 分钟。

对于工作区而言，创建或附加 AKS 群集是一次性过程。 可以将此群集重复用于多个部署。 如果删除该群集或包含该群集的资源组，则在下次需要进行部署时必须创建新群集。 可将多个 AKS 群集附加到工作区。

下面的示例演示如何使用 SDK 和 CLI 创建新的 AKS 群集：

# <a name="python"></a>[Python](#tab/python)

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

有关此示例中使用的类、方法和参数的详细信息，请参阅以下参考文档：

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute.provisioning_configuration](/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az ml computetarget create aks -n myaks
```

有关详细信息，请参阅 [az ml computetarget create aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks) 参考文档。

# <a name="portal"></a>[Portal](#tab/azure-portal)

有关在门户中创建 AKS 群集的信息，请参阅 [在 Azure 机器学习 studio 中创建计算目标](how-to-create-attach-compute-studio.md#inference-clusters)。

---

## <a name="attach-an-existing-aks-cluster"></a>附加现有的 AKS 群集

时间估计****：大约 5 分钟。

如果 Azure 订阅中已有 AKS 群集并且其版本为 1.17 或更低版本，则可以使用该群集来部署映像。

> [!TIP]
> 现有的 AKS 群集除了位于 Azure 机器学习工作区，还可位于 Azure 区域中。


> [!WARNING]
> 请勿在工作区中为同一 AKS 群集创建多个同步附件。 例如，使用两个不同的名称将一个 AKS 群集附加到工作区。 每个新附件都会破坏先前存在的附件。
>
> 如果要重新附加 AKS 群集（例如，更改 TLS 或其他群集配置设置），则必须先使用 [AksCompute.detach()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#detach--) 删除现有附件。

有关如何使用 Azure CLI 或门户创建 AKS 群集的详细信息，请参阅以下文章：

* [创建 AKS 群集 (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [创建 AKS 群集（门户）](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)
* [创建 AKS 群集（Azure 快速入门模板上的 ARM 模板）](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aks-azml-targetcompute)

以下示例演示了如何将现有的 AKS 群集附加到工作区：

# <a name="python"></a>[Python](#tab/python)

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
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py&preserve-view=true)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

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

# <a name="portal"></a>[Portal](#tab/azure-portal)

有关在门户中附加 AKS 群集的信息，请参阅 [在 Azure 机器学习 studio 中创建计算目标](how-to-create-attach-compute-studio.md#inference-clusters)。

---

## <a name="next-steps"></a>后续步骤

* [部署模型的方式和位置](how-to-deploy-and-where.md)
* [将模型部署到 Azure Kubernetes 服务群集](how-to-deploy-azure-kubernetes-service.md)