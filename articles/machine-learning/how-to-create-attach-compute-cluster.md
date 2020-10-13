---
title: 创建计算群集
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习工作区中创建计算群集。 使用计算群集作为计算目标来进行定型或推理。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: sgilley
author: sdgilley
ms.reviewer: sgilley
ms.date: 10/02/2020
ms.openlocfilehash: d33af7a9c2d48ded84bd675364469dab09a79d3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711168"
---
# <a name="create-an-azure-machine-learning-compute-cluster"></a>创建 Azure 机器学习计算群集

了解如何在 Azure 机器学习工作区中创建和管理 [计算群集](concept-compute-target.md#azure-machine-learning-compute-managed) 。

你可以使用 Azure 机器学习计算群集在云中的 CPU 或 GPU 计算节点群集之间分布定型或批处理推理过程。 有关包括 GPU 的 VM 大小的详细信息，请参阅 [GPU 优化的虚拟机大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu)。 

在本文中，将学习以下内容：

* 创建计算群集
*  降低计算群集成本
* 设置群集的[托管标识](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="prerequisites"></a>必备条件

* Azure 机器学习工作区。 有关详细信息，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* [机器学习服务的 Azure CLI 扩展](reference-azure-machine-learning-cli.md)、[Azure 机器学习 Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 或 [Azure 机器学习 Visual Studio Code 扩展](tutorial-setup-vscode-extension.md)。

## <a name="what-is-a-compute-cluster"></a>什么是计算群集？

Azure 机器学习计算群集是一个托管的计算基础结构，可让你轻松创建单节点或多节点计算。 该计算是在工作区区域内部创建的，是可与工作区中的其他用户共享的资源。 提交作业时，计算会自动扩展，并可以放入 Azure 虚拟网络。 计算在容器化环境中执行，将模型的依赖项打包在 [Docker 容器](https://www.docker.com/why-docker)中。

计算群集可以安全地运行 [虚拟网络环境](how-to-secure-training-vnet.md)中的作业，而无需企业打开 SSH 端口。 作业在容器化环境中执行，并将模型依赖项打包到 Docker 容器中。 

## <a name="limitations"></a>限制

* 不要从工作区**创建多个同时同时与相同计算的附件**。 例如，使用两个不同的名称将一个计算群集附加到工作区。 每个新附件都会破坏先前存在的附件。

    如果要重新连接计算目标（例如更改群集配置设置），则必须先删除现有的附件。

* 本文档中列出的某些场景标记为“预览”。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负载。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

* Azure 机器学习计算对可以分配的核心数等属性实施默认限制。 有关详细信息，请参阅[管理和请求 Azure 资源的配额](how-to-manage-quotas.md)。

> [!TIP]
> 一般情况下，只要所需核心数方面的配额足够，群集就可以扩展到多达 100 个节点。 默认情况下，设置群集时会启用群集节点之间的通信（例如，为了支持 MPI 作业）。 但是，可以将群集扩展到数千个节点，只需[提交支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)并请求将你的订阅、工作区或特定群集加入允许列表以禁用节点间通信即可。 


## <a name="create"></a>创建

**估计时间**：约5分钟。

可在不同的运行中重复使用 Azure 机器学习计算。 计算可与工作区中的其他用户共享，并在每次运行之后保留，它会根据提交的运行数以及群集上设置的 max_nodes 自动纵向扩展或缩减节点。 min_nodes 设置控制可用节点数的下限。

每个区域的专用核心数（每个虚拟机系列配额）和区域总配额（适用于计算群集创建）与 Azure 机器学习定型计算实例配额统一并共享。 

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

计算在不使用时自动缩减为零个节点。   按需创建专用 VM 来运行作业。
    
# <a name="python"></a>[Python](#tab/python)

若要在 Python 中创建持久性 Azure 机器学习计算资源，请指定 **vm_size** 和 **max_nodes** 属性。 然后，Azure 机器学习将对其他属性使用智能默认值。 
    
* **vm_size**：Azure 机器学习计算创建的节点的 VM 系列。
* **max_nodes**：在 Azure 机器学习计算中运行作业时自动扩展到的最大节点数。


[!code-python[](~/aml-sdk-samples/ignore/doc-qa/how-to-set-up-training-targets/amlcompute2.py?name=cpu_cluster)]

还可以在创建 Azure 机器学习计算时配置多个高级属性。 使用这些属性可以创建固定大小的持久性群集，或者在订阅中的现有 Azure 虚拟网络内创建持久性群集。  有关详细信息，请参阅 [AmlCompute 类](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?view=azure-ml-py&preserve-view=true)。


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)


```azurecli-interactive
az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
```

有关详细信息，请参阅 [az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute&preserve-view=true)。

# <a name="studio"></a>[工作室](#tab/azure-studio)

有关在工作室中创建计算群集的信息，请参阅 [在 Azure 机器学习 studio 中创建计算目标](how-to-create-attach-compute-studio.md#amlcompute)。

---

 ## <a name="lower-your-compute-cluster-cost"></a><a id="low-pri-vm"></a> 降低计算群集成本

也可以选择使用[低优先级 VM](concept-plan-manage-cost.md#low-pri-vm) 来运行部分或全部工作负载。 这些 VM 的可用性未得到保证，在使用时可能会被占用。 对于被占用的作业，在重新可用时将重新启动，而不是直接续用。 

请使用以下任一方法指定低优先级 VM：
    
# <a name="python"></a>[Python](#tab/python)
    
```python
compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            vm_priority='lowpriority',
                                                            max_nodes=4)
```
    
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

设置 `vm-priority` ：
    
```azurecli-interactive
az ml computetarget create amlcompute --name lowpriocluster --vm-size Standard_NC6 --max-nodes 5 --vm-priority lowpriority
```

# <a name="studio"></a>[工作室](#tab/azure-studio)

在工作室中，在创建 VM 时选择“低优先级”****。

--- 

## <a name="set-up-managed-identity"></a><a id="managed-identity"></a> 设置托管标识

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-intro.md)]

# <a name="python"></a>[Python](#tab/python)

* 在预配配置中配置托管标识：  

    * 系统分配的托管标识：
        ```python
        # configure cluster with a system-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="SystemAssigned",
                                                                )
        ```
    
    * 用户分配的托管标识：
    
        ```python
        # configure cluster with a user-assigned managed identity
        compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                                max_nodes=5,
                                                                identity_type="UserAssigned",
                                                                identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
    
        cpu_cluster_name = "cpu-cluster"
        cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)
        ```

* 将托管标识添加到现有计算群集 
    
    * 系统分配的托管标识：
    
        ```python
        # add a system-assigned managed identity
        cpu_cluster.add_identity(identity_type="SystemAssigned")
        ````
    
    * 用户分配的托管标识：
    
        ```python
        # add a user-assigned managed identity
        cpu_cluster.add_identity(identity_type="UserAssigned", 
                                    identity_id=['/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'])
        ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

* 创建具有托管标识的新托管计算群集

  * 用户分配的托管标识

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
    ```

  * 系统分配的托管标识

    ```azurecli
    az ml computetarget create amlcompute --name cpu-cluster --vm-size Standard_NC6 --max-nodes 5 --assign-identity '[system]'
    ```
* 将托管标识添加到现有群集：

    * 用户分配的托管标识
        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '/subscriptions/<subcription_id>/resourcegroups/<resource_group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user_assigned_identity>'
        ```
    * 系统分配的托管标识

        ```azurecli
        az ml computetarget amlcompute identity assign --name cpu-cluster '[system]'
        ```

# <a name="studio"></a>[工作室](#tab/azure-studio)

请参阅 [在工作室中设置托管标识](how-to-create-attach-compute-studio.md#managed-identity)。

---

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-note.md)]

### <a name="managed-identity-usage"></a>托管标识用法

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-managed-identity-default.md)]

## <a name="next-steps"></a>后续步骤

使用你的计算群集执行以下操作：

* [提交定型运行](how-to-set-up-training-targets.md) 
* [运行批处理推理](how-to-use-parallel-run-step.md)。