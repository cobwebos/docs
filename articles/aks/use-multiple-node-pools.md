---
title: 在 Azure Kubernetes 服务 (AKS) 中使用多个节点池
description: 了解如何为 Azure Kubernetes 服务 (AKS) 中的群集创建和管理多个节点池
services: container-service
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 607419787bc0bab243d6cc2b8cbaa0ec22921e87
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422311"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>为 Azure Kubernetes 服务 (AKS) 中的群集创建和管理多个节点池

在 Azure Kubernetes 服务 (AKS) 中，采用相同配置的节点分组成节点池。** 这些节点池包含运行应用程序的底层 VM。 创建 AKS 群集时定义节点的初始数量及其大小 （SKU），该群集将创建*默认节点池*。 若要支持具有不同计算或存储需求的应用程序，可以创建额外的节点池。 例如，使用这些额外节点池可为计算密集型应用程序提供 GPU，或访问高性能 SSD 存储。

> [!NOTE]
> 利用此功能可以更好地控制如何创建和管理多个节点池。 因此，需要使用单独的命令来执行创建/更新/删除操作。 以前，通过 `az aks create` 或 `az aks update` 执行的群集操作使用 managedCluster API，并且只能通过这些操作更改控制平面和单个节点池。 此功能通过 agentPool API 为代理池公开单独的操作集，并要求使用 `az aks nodepool` 命令集对单个节点池执行操作。

本文介绍如何在 AKS 群集中创建和管理多个节点池。

## <a name="before-you-begin"></a>在开始之前

您需要 Azure CLI 版本 2.2.0 或更高版本安装和配置。 运行 `az --version` 即可查找版本。 如果需要安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

创建和管理支持多个节点池的 AKS 群集时存在以下限制：

* 请参阅[Azure 库伯奈斯服务 （AKS） 中的配额、虚拟机大小限制和地区可用性][quotas-skus-regions]。
* 默认情况下，无法删除系统节点池，而无法删除第一个节点池。
* AKS 群集必须通过标准 SKU 负载均衡器来使用多个节点池，而基本 SKU 负载均衡器并不支持该功能。
* AKS 群集必须对节点使用虚拟机规模集。
* 节点池的名称只能包含小写字母数字字符，且必须以小写字母开头。 Linux 节点池的名称长度必须为 1 到 12 个字符；Windows 节点池的名称长度必须为 1 到 6 个字符。
* 所有节点池必须驻留在同一虚拟网络中。
* 在创建群集的过程中创建多个节点池时，节点池使用的所有 Kubernetes 版本都必须与已为控制平面设置的版本相匹配。 这可以在已使用每节点池操作预配了群集后更新。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

若要开始，请创建包含单个节点池的 AKS 群集。 下面的示例使用[az 组创建][az-group-create]命令在*东部*区域创建名为*myResourceGroup*的资源组。 然后使用 [az AKS create][az-aks-create] 命令创建名为 *myAKSCluster* 的 AKS 群集。 *1.15.7* *的 --kubernetes 版本*用于演示如何在以下步骤中更新节点池。 可以指定任何[支持的 Kubernetes 版本][supported-versions]。

> [!NOTE]
> 使用多个节点池时，**不支持**“基本”负载均衡器 SKU。** 默认情况下，AKS 群集是在 Azure CLI 和 Azure 门户中使用“标准”负载均衡器 SKU 创建的。**

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
> 如果在添加节点池时未指定*VmSize，**则*Standard_DS2_v3 Windows 节点池的默认*大小，Standard_DS2_v2* Linux 节点池。 如果未指定*协调器版本*，则默认为与控制平面相同的版本。

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>添加具有唯一子网的节点池（预览）

工作负载可能需要将群集的节点拆分为单独的池，以便进行逻辑隔离。 此隔离可以通过专用于群集中每个节点池的单独子网进行支持。 这可以解决诸如具有非连续虚拟网络地址空间以跨节点池拆分等要求。

#### <a name="limitations"></a>限制

* 分配给节点池的所有子网必须属于同一虚拟网络。
* 系统 pod 必须有权访问群集中的所有节点，才能通过 coreDNS 提供关键功能，如 DNS 解析。
* 在预览期间，每个节点池的唯一子网的分配仅限于 Azure CNI。
* 预览期间不支持将网络策略与每个节点池的唯一子网使用。

要使用专用子网创建节点池，在创建节点池时，将子网资源 ID 作为附加参数传递。

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

首次在第一步中创建 AKS 群集时，`--kubernetes-version`指定了*1.15.7。* 这同时设置了控制平面和默认节点池的 Kubernetes 版本。 本部分中的命令说明如何升级单个特定的节点池。

[下一部分](#upgrade-a-cluster-control-plane-with-multiple-node-pools)将会说明升级控制平面与节点池的 Kubernetes 版本之间的关系。

> [!NOTE]
> 节点池的 OS 映像版本与群集的 Kubernetes 版本相关联。 只能先升级 OS 映像，然后再升级群集。

由于本示例包含两个节点池，因此必须使用 [az aks nodepool upgrade][az-aks-nodepool-upgrade] 来升级节点池。 让我们将*我的节点池*升级到库伯内斯*1.15.7*。 如以下示例中所示，使用 [az aks nodepool upgrade][az-aks-nodepool-upgrade] 命令升级节点池：

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

使用 [az aks node pool list][az-aks-nodepool-list] 命令再次列出节点池的状态。 下面的示例显示*我的节点池*处于*升级到* *1.15.7*的状态：

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

升级 AKS 控制平面需要使用 `az aks upgrade`。 此命令升级控制平面版本和群集中的所有节点池。

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
   * 如果未指定节点池的 Kubernetes 版本，则行为取决于所用的客户端。 如果不使用，则资源管理器模板中的声明将回退到为节点池定义的现有版本（如果未设置任何版本），则控件平面版本用于回退。
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

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>通过启用群集自动缩放器自动缩放特定节点池

AKS 提供了一个单独的功能，用于使用称为[群集自动缩放器](cluster-autoscaler.md)的功能自动缩放节点池。 此功能可以启用每个节点池，每个节点池具有唯一的最小和最大比例计数。 了解如何[使用每个节点池的群集自动缩放器](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。

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

在下面的示例中，创建一个基于 GPU 的节点池，该节点池使用*Standard_NC6* VM 大小。 这些 VM 采用 NVIDIA Tesla K80 卡。 有关可用 VM 大小的信息，请参阅 [Azure 中的 Linux 虚拟机大小][vm-sizes]。

再次使用 [az aks node pool add][az-aks-nodepool-add] 命令创建节点池。 此时，指定名称*gpunodepool，* 并使用 参数`--node-vm-size`指定*Standard_NC6*大小：

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

在此示例中，使用 --node-taints 命令对基于 GPU 的节点应用污点。 指定上述 `kubectl get nodes` 命令的输出中显示的基于 GPU 的节点名称。 排斥以“键:值”的形式应用，后接计划选项。** 以下示例使用 *sku=gpu* 对，并定义具有 *NoSchedule* 功能的其他 pod：

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

只能在 *gpunodepool* 中的节点上计划已应用此排斥的 pod。 任何其他 pod 将在 *nodepool1* 节点池中计划。 如果创建额外的节点池，可以使用额外的排斥和容许来限制可在这些节点资源上计划的 pod。

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>为节点池指定污点、标签或标记

创建节点池时，可以将污点、标签或标记添加到该节点池。 添加污点、标签或标记时，该节点池中的所有节点也会获取该污点、标签或标记。

要创建具有污点的节点池，请使用[az aks 节点池添加][az-aks-nodepool-add]。 指定*污点*的名称并使用`--node-taints`参数指定*sku_gpu：无计划*。

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

以下示例输出来自[az aks 节点池列表][az-aks-nodepool-list]命令，显示*taintnp*正在*创建*具有指定*节点的节点：*

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
    "nodeTaints":  {
      "sku": "gpu:NoSchedule"
    },
    ...
  },
 ...
]
```

用于处理节点调度规则的 Kubernets 中可以看到污染信息。

您还可以在节点池创建期间向节点池添加标签。 节点池中设置的标签将添加到节点池中的每个节点。 [这些标签在 Kubernetes 中可见][kubernetes-labels]，用于处理节点的调度规则。

要创建带有标签的节点池，请使用[az aks 节点池添加][az-aks-nodepool-add]。 指定名称*标签，* 并使用`--labels`参数为标签指定*dept_IT*和*costcenter_9999。*

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
> 只能在节点池创建期间为节点池设置标签。 标签还必须是键/值对，并且具有[有效的语法][kubernetes-label-syntax]。

以下示例输出来自[az aks 节点池列表][az-aks-nodepool-list]命令，显示*labelnp*正在*创建*具有指定*节点标签的节点*：

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

您可以将 Azure 标记应用于 AKS 群集中的节点池。 应用于节点池的标记将应用于节点池中的每个节点，并通过升级进行持久化。 标记还应用于横向扩展操作期间添加到节点池的新节点。 添加标记有助于执行策略跟踪或成本估算等任务。

使用[az aks 节点池添加][az-aks-nodepool-add]创建节点池。 指定名称*标记节点池*并使用`--tag`参数为标记指定*dept_IT*和*costcenter_9999。*

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
> 在使用 az `--tags` [aks 节点池更新][az-aks-nodepool-update]命令以及在群集创建期间，也可以使用 参数。 在群集创建期间，`--tags`参数将标记应用于使用群集创建的初始节点池。 所有标记名称都必须符合["使用"标记中的限制来组织 Azure 资源][tag-limitation]。 使用`--tags`参数更新节点池会更新任何现有标记值并追加任何新标记。 例如，如果节点池的标记有*dept_IT*和*costcenter_9999，* 并且使用*团队_dev*和*costcenter=111*为标记更新了它，则节点池将具有*dept_IT、costcenter=111*和为标记的*team_dev。* *costcenter=111*

以下示例输出来自[az aks 节点池列表][az-aks-nodepool-list]命令，显示*tagnodepool*正在*创建*具有指定*标记*的节点：

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
* 将节点池中的节点设置以运行库伯内斯版本*1.15.7*。
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
> 通过在模板中添加*标记*属性，可以将标记添加到节点池，如以下示例所示。
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

## <a name="assign-a-public-ip-per-node-for-a-node-pool-preview"></a>为节点池为每个节点分配公共 IP（预览）

> [!WARNING]
> 在预览版中为每个节点分配公共 IP 期间，不能对 AKS 中的标准负载均衡器 SKU 使用此操作，因为负载均衡器规则可能与 VM 预配相冲突。** 由于此限制，此预览功能不支持 Windows 代理池。 在预览版中，如果需要为每个节点分配公共 IP，必须使用基本负载均衡器 SKU。**

AKS 节点无需使用自身的公共 IP 地址进行通信。 但是，方案可能需要节点池中的节点接收其自己的专用公共 IP 地址。 常见方案适用于游戏工作负载，其中控制台需要直接连接到云虚拟机，以尽量减少跃点。 此方案可以通过注册预览功能"节点公共 IP"（预览）在 AKS 上实现。

通过发出以下 Azure CLI 命令注册节点公共 IP 功能。

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

成功注册后，按照[上述](#manage-node-pools-using-a-resource-manager-template)相同的说明部署 Azure 资源管理器模板，并将布尔属性`enableNodePublicIP`添加到代理池配置文件。 将值设置为 `true`；如果未指定，它将默认设置为 `false`。 

此属性仅创建时间属性，需要最低 API 版本 2019-06-01。 此属性可同时应用到 Linux 和 Windows 节点池。

## <a name="clean-up-resources"></a>清理资源

在本文中，你已创建包含基于 GPU 的节点的 AKS 群集。 为了减少不必要的费用，我们建议删除 *gpunodepool* 或整个 AKS 群集。

若要删除基于 GPU 的节点池，请如以下示例中所示使用 [az aks nodepool delete][az-aks-nodepool-delete] 命令：

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

要删除群集本身，请使用[az 组删除][az-group-delete]命令删除 AKS 资源组：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

本文已介绍如何在 AKS 群集中创建和管理多个节点池。 有关如何跨节点池控制 pod 的详细信息，请参阅[有关 AKS 中的高级计划程序功能的最佳做法][operator-best-practices-advanced-scheduler]。

要创建和使用 Windows 服务器容器节点池，请参阅[在 AKS 中创建 Windows 服务器容器][aks-windows]。

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