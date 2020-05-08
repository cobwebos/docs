---
title: 在 Azure Kubernetes 服务 (AKS) 中使用多个节点池
description: 了解如何为 Azure Kubernetes 服务 (AKS) 中的群集创建和管理多个节点池
services: container-service
ms.topic: article
ms.date: 04/08/2020
ms.openlocfilehash: bf7e767f1a7b0c657c744c96b308160393e3f326
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82610915"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>为 Azure Kubernetes 服务 (AKS) 中的群集创建和管理多个节点池

在 Azure Kubernetes 服务 (AKS) 中，采用相同配置的节点分组成节点池。  这些节点池包含运行应用程序的底层 VM。 初始节点数及其大小（SKU）是在创建 AKS 群集时定义的，该群集创建[系统节点池][use-system-pool]。 若要支持具有不同计算或存储需求的应用程序，可以创建其他*用户节点池*。 系统节点池提供托管关键系统箱（例如 CoreDNS 和 tunnelfront）的主要目的。 用户节点池服务于托管应用程序箱的主要目的。 但是，如果你想要在 AKS 群集中只有一个池，可以在系统节点池上计划应用程序盒。 用户节点池用于放置应用程序特定的 pod。 例如，使用这些附加的用户节点池为计算密集型应用程序或高性能 SSD 存储提供 Gpu。

> [!NOTE]
> 利用此功能可以更好地控制如何创建和管理多个节点池。 因此，需要使用单独的命令来执行创建/更新/删除操作。 以前，通过 `az aks create` 或 `az aks update` 执行的群集操作使用 managedCluster API，并且只能通过这些操作更改控制平面和单个节点池。 此功能通过 agentPool API 为代理池公开单独的操作集，并要求使用 `az aks nodepool` 命令集对单个节点池执行操作。

本文介绍如何在 AKS 群集中创建和管理多个节点池。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 版本2.2.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

创建和管理支持多个节点池的 AKS 群集时存在以下限制：

* 请参阅[Azure Kubernetes Service （AKS）中的配额、虚拟机大小限制和区域可用性][quotas-skus-regions]。
* 你可以删除系统节点池，前提是你有另一个要在 AKS 群集中使用的系统节点池。
* 系统池必须至少包含一个节点，并且用户节点池可能包含零个或多个节点。
* AKS 群集必须通过标准 SKU 负载均衡器来使用多个节点池，而基本 SKU 负载均衡器并不支持该功能。
* AKS 群集必须对节点使用虚拟机规模集。
* 节点池的名称只能包含小写字母数字字符，且必须以小写字母开头。 Linux 节点池的名称长度必须为 1 到 12 个字符；Windows 节点池的名称长度必须为 1 到 6 个字符。
* 所有节点池必须位于同一虚拟网络中。
* 在创建群集的过程中创建多个节点池时，节点池使用的所有 Kubernetes 版本都必须与已为控制平面设置的版本相匹配。 这可以在已使用每节点池操作预配了群集后更新。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

> [!Important]
> 如果在生产环境中为 AKS 群集运行单个系统节点池，则建议使用至少三个节点作为节点池。

若要开始，请创建包含单个节点池的 AKS 群集。 以下示例使用[az group create][az-group-create]命令在*eastus*区域中创建名为*myResourceGroup*的资源组。 然后使用 [az AKS create][az-aks-create] 命令创建名为 *myAKSCluster* 的 AKS 群集。 *1.15.7*的*kubernetes 版本*用于说明如何在以下步骤中更新节点池。 可以指定任何[支持的 Kubernetes 版本][supported-versions]。

> [!NOTE]
> 使用多个节点池时，**不支持**“基本”负载均衡器 SKU。** 默认情况下，AKS 群集是使用*标准*负载均衡器 SKU 从 Azure CLI 和 Azure 门户中创建的。

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --kubernetes-version 1.15.7 \
    --load-balancer-sku standard
```

创建群集需要几分钟时间。

> [!NOTE]
> 为了确保群集可靠运行，应在默认节点池中至少运行 2（两）个节点，因为基本系统服务将在整个节点池中运行。

群集准备就绪后，请使用 [az aks get-credentials][az-aks-get-credentials] 命令获取要与 `kubectl` 结合使用的群集凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>添加节点池

在上一步骤中创建的群集包含单个节点池。 让我们使用 [az aks nodepool add][az-aks-nodepool-add] 命令添加另一个节点池。 以下示例创建名为 *mynodepool* 的节点池，该节点池运行 *3* 个节点：

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> 节点池的名称必须以小写字母开头，且只能包含字母数字字符。 Linux 节点池的名称长度必须为 1 到 12 个字符；Windows 节点池的名称长度必须为 1 到 6 个字符。

若要查看节点池的状态，请使用 [az aks node pool list][az-aks-nodepool-list] 命令并指定资源组和群集名称：

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

以下示例输出显示已成功地在节点池中创建包含三个节点的 *mynodepool*。 在上一步骤中创建 AKS 群集时，已创建包含 *2* 个节点的默认 *nodepool1*。

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.5",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> 如果在添加节点池时未指定*VmSize* ，则 Windows 节点池的默认大小和 Linux 节点池的*Standard_DS2_v2* *Standard_DS2_v3* 。 如果未指定*OrchestratorVersion* ，则它将默认为与控件平面相同的版本。

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>添加具有唯一子网的节点池（预览）

工作负荷可能需要将群集的节点拆分为单独的池，以便进行逻辑隔离。 对于群集中每个节点池专用的单独子网，可以支持此隔离。 这可以满足要求，例如，在节点池中拆分非连续的虚拟网络地址空间。

#### <a name="limitations"></a>限制

* 分配给 nodepools 的所有子网都必须属于同一虚拟网络。
* 系统箱必须有权访问群集中的所有节点，以通过 coreDNS 提供关键功能，如 DNS 解析。
* 在预览期间，按节点池分配唯一子网仅限于 Azure CNI。
* 在预览期间，不支持将网络策略用于每个节点池的唯一子网。

若要创建具有专用子网的节点池，请在创建节点池时传递子网资源 ID 作为附加参数。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>升级节点池

> [!NOTE]
> 不能对群集或节点池同时执行升级和缩放操作，否则会返回错误。 而只能先在目标资源上完成一个操作类型，然后再在同一资源上执行下一个请求。 请阅读[故障排除指南](https://aka.ms/aks-pending-upgrade)中的详细信息。

当 AKS 群集最初是在第一步中创建的时`--kubernetes-version` ，指定了*1.15.7*的。 这同时设置了控制平面和默认节点池的 Kubernetes 版本。 本部分中的命令说明如何升级单个特定的节点池。

[下一部分](#upgrade-a-cluster-control-plane-with-multiple-node-pools)将会说明升级控制平面与节点池的 Kubernetes 版本之间的关系。

> [!NOTE]
> 节点池的 OS 映像版本与群集的 Kubernetes 版本相关联。 只能先升级 OS 映像，然后再升级群集。

由于本示例包含两个节点池，因此必须使用 [az aks nodepool upgrade][az-aks-nodepool-upgrade] 来升级节点池。 让我们将*mynodepool*升级到 Kubernetes *1.15.7*。 如以下示例中所示，使用 [az aks nodepool upgrade][az-aks-nodepool-upgrade] 命令升级节点池：

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

使用 [az aks node pool list][az-aks-nodepool-list] 命令再次列出节点池的状态。 以下示例显示*mynodepool*处于*1.15.7*的*升级*状态：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

将节点升级到指定的版本需要花费几分钟时间。

最佳做法是将 AKS 群集中的所有节点池升级到相同的 Kubernetes 版本。 `az aks upgrade` 的默认行为是将所有节点池连同控制平面一起升级，以实现这种一致性。 升级单个节点池的功能可让你执行滚动升级，并在节点池之间计划 pod，使应用程序的正常运行时间保持在上述约束范围内。

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>升级包含多个节点池的群集控制平面

> [!NOTE]
> Kubernetes 使用标准的[语义化版本控制](https://semver.org/)方案。 版本号以 *x.y.z* 表示，其中，*x* 是主要版本，*y* 是次要版本，*z* 是修补程序版本。 例如，版本 *1.12.6* 中的 1 是主要版本，12 是次要版本，6 是修补程序版本。 控制平面和初始节点池的 Kubernetes 版本是在群集创建过程中设置的。 将所有附加节点池添加到群集时，将为其设置 Kubernetes 版本。 不同节点池的 Kubernetes 版本可能不同，节点池与控制平面的 Kubernetes 版本也可能不同。

AKS 群集包含两个具有关联 Kubernetes 版本的群集资源对象。

1. 群集控制平面 Kubernetes 版本。
2. 具有 Kubernetes 版本的节点池。

一个控制平面映射到一个或多个节点池。 升级操作的行为取决于所用的 Azure CLI 命令。

升级 AKS 控制平面需要使用 `az aks upgrade`。 此命令升级群集中的控制平面版本和所有节点池。

结合 `--control-plane-only` 标志发出 `az aks upgrade` 命令只会升级群集控制平面， 而不会更改群集中任何关联的节点池。

升级单个节点池需要使用 `az aks nodepool upgrade`。 此命令仅升级具有指定 Kubernetes 版本的目标节点池

### <a name="validation-rules-for-upgrades"></a>升级验证规则

群集控制平面和节点池的有效 Kubernetes 升级由以下规则集验证。

* 用于升级节点池的有效版本的规则：
   * 节点池版本的主要版本必须与控制平面相同。**
   * 节点池的次要版本必须在控制平面版本的两个次要版本范围内。****
   * 节点池版本不能大于控制平面的 `major.minor.patch` 版本。

* 提交升级操作的规则：
   * 无法降级控制平面或节点池的 Kubernetes 版本。
   * 如果未指定节点池的 Kubernetes 版本，则行为取决于所用的客户端。 资源管理器模板中的声明将回退到为节点池定义的现有版本（如果使用），如果未设置，则使用控制平面版本回退。
   * 可以在给定的时间升级或者缩放控制平面或节点池，而不能同时对单个控制平面或节点池资源提交多个操作。

## <a name="scale-a-node-pool-manually"></a>手动缩放节点池

当应用程序工作负荷需求发生变化时，你可能需要缩放节点池中的节点数。 可以增加或减少节点数。

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

若要缩放节点池中的节点数，请使用 [az aks node pool scale][az-aks-nodepool-scale] 命令。 以下示例将 *mynodepool* 中的节点数缩放为 *5*：

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

使用 [az aks node pool list][az-aks-nodepool-list] 命令再次列出节点池的状态。 以下示例显示 *mynodepool* 处于 *Scaling*（正在缩放）状态，其中的新节点计数为 *5*：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

需要花费几分钟时间来完成缩放操作。

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>通过启用群集来自动缩放特定节点池自动缩放程序

AKS 提供单独的功能，可使用称为[群集自动缩放程序](cluster-autoscaler.md)的功能自动缩放节点池。 可以为每个节点池启用此功能，每个节点池具有唯一的最小和最大刻度计数。 了解如何[使用每个节点池的群集自动缩放程序](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。

## <a name="delete-a-node-pool"></a>删除节点池

不再需要某个池时，可将其删除并删除底层 VM 节点。 若要删除节点池，请使用 [az aks node pool delete][az-aks-nodepool-delete] 命令并指定节点池名称。 以下示例删除在前面步骤中创建的 *mynoodepool*：

> [!CAUTION]
> 如果删除节点池时发生数据丢失，没有任何选项可以恢复数据。 如果无法在其他节点池中计划 pod，则这些应用程序将不可用。 当使用中的应用程序没有数据备份或者无法在群集中的其他节点池上运行时，请务必不要删除节点池。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

[az aks node pool list][az-aks-nodepool-list] 命令的以下示例输出显示 *mynodepool* 处于 *Deleting*（正在删除）状态：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

删除节点和节点池需要花费几分钟时间。

## <a name="specify-a-vm-size-for-a-node-pool"></a>指定节点池的 VM 大小

在前面的创建节点池示例中，对群集中创建的节点使用了默认 VM 大小。 一个更常见的场景是创建具有不同 VM 大小和功能的节点池。 例如，可以创建一个包含具有大量 CPU 或内存的节点的节点池，或创建一个提供 GPU 支持的节点池。 下一步骤将[使用排斥和容许](#schedule-pods-using-taints-and-tolerations)来告知 Kubernetes 计划程序如何将访问权限限制为可在这些节点上运行的 pod。

在以下示例中，创建使用*Standard_NC6* VM 大小的基于 GPU 的节点池。 这些 VM 采用 NVIDIA Tesla K80 卡。 有关可用 VM 大小的信息，请参阅 [Azure 中的 Linux 虚拟机大小][vm-sizes]。

再次使用 [az aks node pool add][az-aks-nodepool-add] 命令创建节点池。 这一次，请指定名称*gpunodepool*，并使用`--node-vm-size`参数指定*Standard_NC6*大小：

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

[az aks node pool list ][az-aks-nodepool-list] 命令的以下示例输出显示 *gpunodepool* 正在创建具有指定 *VmSize* 的节点：**

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

成功创建 *gpunodepool* 需要花费几分钟时间。

## <a name="schedule-pods-using-taints-and-tolerations"></a>使用排斥和容许计划 pod

群集现在包含两个节点池 - 最初创建的默认节点池，以及基于 GPU 的节点池。 使用 [kubectl get nodes][kubectl-get] 命令查看群集中的节点。 以下示例输出显示了节点：

```console
kubectl get nodes
```

```output
NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Kubernetes 计划程序能够使用排斥和容许来限制可在节点上运行的工作负荷。

* 将**排斥**应用到指明了只能计划特定 pod 的节点。
* 然后，将**容许**应用到可以*容许*节点排斥的 pod。

有关如何使用 Kubernetes 高级计划功能的详细信息，请参阅[有关 AKS 中的高级计划程序功能的最佳做法][taints-tolerations]

在此示例中，使用--taints 命令将破坏应用到基于 GPU 的节点。 指定上述 `kubectl get nodes` 命令的输出中显示的基于 GPU 的节点名称。 破坏将应用为*key = value*对，然后应用于计划选项。 以下示例使用 *sku=gpu* 对，并定义具有 *NoSchedule* 功能的其他 pod：

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

以下基本示例 YAML 清单使用容许来允许 Kubernetes 计划程序在基于 GPU 的节点上运行 NGINX pod。 有关针对 MNIST 数据集运行 Tensorflow 作业的更适当但更耗时的示例，请参阅[对 AKS 上的计算密集型工作负荷使用 GPU][gpu-cluster]。

创建名为 `gpu-toleration.yaml` 的文件，并将其复制到以下示例 YAML 中：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: nginx:1.15.9
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

使用 `kubectl apply -f gpu-toleration.yaml` 命令计划 pod：

```console
kubectl apply -f gpu-toleration.yaml
```

只需花费几秒钟时间即可计划 pod 并提取 NGINX 映像。 使用 [kubectl describe pod][kubectl-describe] 命令查看 pod 状态。 以下精简示例输出显示已应用 *sku=gpu:NoSchedule* 容许。 在 events 节中，计划程序已将 pod 分配到 *aks-gpunodepool-28993262-vmss000000* 基于 GPU 的节点：

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                                          Message
  ----    ------     ----   ----                                          -------
  Normal  Scheduled  4m48s  default-scheduler                             Successfully assigned default/mypod to aks-gpunodepool-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet, aks-gpunodepool-28993262-vmss000000  pulling image "nginx:1.15.9"
  Normal  Pulled     4m43s  kubelet, aks-gpunodepool-28993262-vmss000000  Successfully pulled image "nginx:1.15.9"
  Normal  Created    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Created container
  Normal  Started    4m40s  kubelet, aks-gpunodepool-28993262-vmss000000  Started container
```

只能在*gpunodepool*中的节点上计划已应用此 toleration 的箱。 任何其他 pod 将在 *nodepool1* 节点池中计划。 如果创建额外的节点池，可以使用额外的排斥和容许来限制可在这些节点资源上计划的 pod。

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>指定节点池的破坏、标签或标记

创建节点池时，可以将 taints、标签或标记添加到该节点池。 添加破坏、标签或标记时，该节点池中的所有节点也会获得该破坏、标签或标记。

若要创建具有破坏的节点池，请使用[az aks nodepool add][az-aks-nodepool-add]。 指定名称*taintnp* ，并使用`--node-taints`参数为破坏指定*sku = gpu： NoSchedule* 。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

以下来自[az aks nodepool list][az-aks-nodepool-list]命令的示例输出显示*taintnp*正在*创建*具有指定*nodeTaints*的节点：

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

破坏信息显示在 Kubernetes 中，用于处理节点的计划规则。

你还可以在创建节点池期间向节点池添加标签。 在节点池中设置的标签将添加到节点池中的每个节点。 这些[标签在 Kubernetes 中可见][kubernetes-labels]，用于处理节点的计划规则。

若要创建包含标签的节点池，请使用[az aks nodepool add][az-aks-nodepool-add]。 指定名称*labelnp* ，并使用参数`--labels`指定 "*部门 = IT* " 和 " *costcenter = 9999* " 作为标签。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> 只能在创建节点池期间为节点池设置标签。 标签还必须是键/值对，并且具有[有效的语法][kubernetes-label-syntax]。

以下来自[az aks nodepool list][az-aks-nodepool-list]命令的示例输出显示*labelnp*正在*创建*具有指定*nodeLabels*的节点：

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

可以将 Azure 标记应用于 AKS 群集中的节点池。 应用于节点池的标记将应用于节点池中的每个节点，并通过升级持久保存。 标记还适用于在扩展操作期间添加到节点池的新节点。 添加标记有助于处理策略跟踪或成本估算等任务。

使用[az aks nodepool add][az-aks-nodepool-add]创建节点池。 指定名称*tagnodepool* ，并使用`--tag`参数为标记指定*部门 = IT*和*costcenter = 9999* 。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> 还可以在使用`--tags` [az aks nodepool update][az-aks-nodepool-update]命令时以及在群集创建过程中使用该参数。 在群集创建过程中`--tags` ，参数会将标记应用到与群集一起创建的初始节点池。 所有标记名称都必须遵守[使用标记来组织 Azure 资源][tag-limitation]的限制。 使用`--tags`参数更新节点池会更新所有现有标记值并追加任何新标记。 例如，如果你的节点池具有*部门 = it*并且*costcenter = 9999*用于标记，并且已使用*team = dev*和*costcenter = 111* for 标记对其进行了更新，则 nodepool 会将*部门 = IT*、 *costcenter = 111*和*team = dev*用于标记。

以下来自[az aks nodepool list][az-aks-nodepool-list]命令的示例输出显示*tagnodepool*正在*创建*具有指定*标记*的节点：

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>使用资源管理器模板管理节点池

使用 Azure 资源管理器模板创建和管理资源时，通常可以更新该模板中的设置，然后重新部署以更新资源。 对于 AKS 中的节点池，一旦创建 AKS 群集，就无法更新初始节点池的配置文件。 此行为意味着无法更新现有的资源管理器模板，更改节点池，然后重新部署。 必须创建单独的资源管理器模板来仅更新现有 AKS 群集的节点池。

创建一个模板（例如 `aks-agentpools.json`）并粘贴以下示例清单。 此示例模板配置以下设置：

* 将名为 *myagentpool* 的 *Linux* 节点池更新为运行三个节点。
* 将节点池中的节点设置为运行 Kubernetes 版本*1.15.7*。
* 将节点大小定义为 *Standard_DS2_v2*。

根据需要编辑这些值，以更新、添加或删除节点池：

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

如以下示例中所示，使用 [az group deployment create][az-group-deployment-create] 命令部署此模板。 系统将提示输入现有的 AKS 群集名称和位置：

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> 通过在模板中添加*tag*属性，可以向节点池添加标记，如下面的示例中所示。
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

更新 AKS 群集可能需要花费几分钟时间，具体取决于资源管理器模板中定义的节点池设置和操作。

## <a name="assign-a-public-ip-per-node-for-your-node-pools-preview"></a>为节点池分配每个节点一个公共 IP （预览）

> [!WARNING]
> 必须安装 CLI 预览版扩展0.4.43 或更高版本，才能使用公共 IP 每节点功能。

AKS 节点无需使用自身的公共 IP 地址进行通信。 但是，方案可能需要节点池中的节点接收其自己的专用公共 IP 地址。 常见的情况是，游戏工作负荷，控制台需要直接连接到云虚拟机以最大程度地减少跃点。 此方案可通过注册预览功能 "节点公共 IP （预览版）" AKS 来实现。

若要安装和更新最新的 aks 扩展，请使用以下 Azure CLI 命令：

```azurecli
az extension add --name aks-preview
az extension update --name aks-preview
az extension list
```

用以下 Azure CLI 命令为 Node 公共 IP 功能注册：

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```
注册该功能可能需要几分钟时间。  可以通过以下命令检查状态：

```azurecli-interactive
 az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/NodePublicIPPreview')].{Name:name,State:properties.state}"
```

注册成功后，创建新的资源组。

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

创建新的 AKS 群集并为节点附加公共 IP。 节点池中的每个节点都接收唯一的公共 IP。 可以通过查看虚拟机规模集实例来验证这一点。

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

对于现有的 AKS 群集，你还可以添加新的节点池，并为节点附加一个公共 IP。

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

> [!Important]
> 预览期间，Azure 实例元数据服务目前不支持检索标准层 VM SKU 的公共 IP 地址。 由于此限制，不能使用 kubectl 命令显示分配给节点的公共 Ip。 但是，将分配 Ip 并按预期方式工作。 节点的公共 Ip 会附加到虚拟机规模集中的实例。

可以通过多种方式找到节点的公共 Ip：

* 使用 Azure CLI 命令[az vmss-instance-public-ip][az-list-ips]
* 使用[PowerShell 或 Bash 命令][vmss-commands]。 
* 通过查看虚拟机规模集中的实例，还可以查看 Azure 门户中的公共 Ip。

> [!Important]
> [节点资源组][node-resource-group]包含节点及其公共 ip。 执行命令时，请使用节点资源组来查找节点的公共 Ip。

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>清理资源

在本文中，你已创建包含基于 GPU 的节点的 AKS 群集。 为了减少不必要的费用，我们建议删除 *gpunodepool* 或整个 AKS 群集。

若要删除基于 GPU 的节点池，请如以下示例中所示使用 [az aks nodepool delete][az-aks-nodepool-delete] 命令：

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

若要删除群集本身，请使用[az group delete][az-group-delete]命令删除 AKS 资源组：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

还可以删除为 "节点池的公共 IP" 方案创建的其他群集。

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

了解有关[系统节点池][use-system-pool]的详细信息。

本文已介绍如何在 AKS 群集中创建和管理多个节点池。 有关如何跨节点池控制 pod 的详细信息，请参阅[有关 AKS 中的高级计划程序功能的最佳做法][operator-best-practices-advanced-scheduler]。

若要创建和使用 Windows Server 容器节点池，请参阅[在 AKS 中创建 Windows server 容器][aks-windows]。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest.md#az-vmss-list-instance-public-ips
