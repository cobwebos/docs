---
title: 在 Azure Kubernetes 服务中使用多个节点池（AKS）
description: 了解如何在 Azure Kubernetes Service （AKS）中创建和管理群集的多个节点池
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 01/22/2020
ms.author: mlearned
ms.openlocfilehash: bbfb65c31bf6fd46cc18c9eee66086afbbff1d5f
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77157968"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service （AKS）中创建和管理群集的多个节点池

在 Azure Kubernetes Service （AKS）中，相同配置的节点组合在一起成为*节点池*。 这些节点池包含运行应用程序的基础 Vm。 初始节点数及其大小（SKU）是在创建 AKS 群集时定义的，该群集创建*默认节点池*。 若要支持具有不同计算或存储需求的应用程序，可以创建其他节点池。 例如，使用这些附加的节点池为计算密集型应用程序或高性能 SSD 存储提供 Gpu。

> [!NOTE]
> 利用此功能，可以更好地控制如何创建和管理多个节点池。 因此，创建/更新/删除需要单独的命令。 以前的群集操作通过 `az aks create` 或 `az aks update` 使用 managedCluster API，是更改控制平面和单节点池的唯一选项。 此功能通过 agentPool API 为代理池公开单独的操作集，并要求使用 `az aks nodepool` 命令集在单个节点池上执行操作。

本文介绍如何在 AKS 群集中创建和管理多个节点池。

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 版本2.0.76 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

## <a name="limitations"></a>限制

创建和管理支持多个节点池的 AKS 群集时，有以下限制：

* 请参阅[Azure Kubernetes Service （AKS）中的配额、虚拟机大小限制和区域可用性][quotas-skus-regions]。
* 不能删除默认（第一个）节点池。
* 无法使用 HTTP 应用程序路由加载项。
* AKS 群集必须使用标准 SKU 负载均衡器来使用多个节点池，但基本 SKU 负载均衡器不支持此功能。
* AKS 群集必须使用节点的虚拟机规模集。
* 节点池的名称只能包含小写字母数字字符，且必须以小写字母开头。 对于 Linux 节点池，长度必须在1到12个字符之间，对于 Windows 节点池，长度必须介于1到6个字符之间。
* 所有节点池必须位于同一 vnet 和子网中。
* 在创建群集时，在创建多个节点池时，节点池使用的所有 Kubernetes 版本都必须与控制平面的版本相匹配。 使用每个节点池操作设置群集后，可以更新此设置。

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

若要开始，请创建具有单个节点池的 AKS 群集。 以下示例使用[az group create][az-group-create]命令在*eastus*区域中创建名为*myResourceGroup*的资源组。 然后，使用[az AKS create][az-aks-create]命令创建名为*myAKSCluster*的 AKS 群集。 *1.15.7*的*kubernetes 版本*用于说明如何在以下步骤中更新节点池。 可以指定任何[受支持的 Kubernetes 版本][supported-versions]。

> [!NOTE]
> 使用多节点池时，**不支持***基本*负载平衡器 SKU。 默认情况下，使用*标准*负载均衡器 SKU Azure CLI 和 AZURE 门户创建 AKS 群集。

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
> 若要确保群集正常运行，应在默认节点池中至少运行2个节点，因为基本的系统服务在此节点池上运行。

群集准备就绪后，请使用[az aks get 凭据][az-aks-get-credentials]命令获取用于 `kubectl`的群集凭据：

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>添加节点池

在上一步中创建的群集具有单个节点池。 让我们使用[az aks nodepool add][az-aks-nodepool-add]命令添加另一个节点池。 以下示例创建一个名为*mynodepool*的节点池，运行*3*个节点：

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.15.5
```

> [!NOTE]
> 节点池的名称必须以小写字母开头，且只能包含字母数字字符。 对于 Linux 节点池，长度必须在1到12个字符之间，对于 Windows 节点池，长度必须介于1到6个字符之间。

若要查看节点池的状态，请使用[az aks node pool list][az-aks-nodepool-list]命令并指定资源组和群集名称：

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

以下示例输出显示已成功地在节点池中有三个节点创建了*mynodepool* 。 在上一步中创建 AKS 群集时，创建的默认*nodepool1*的节点计数为*2*。

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

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

## <a name="upgrade-a-node-pool"></a>升级节点池

> [!NOTE]
> 如果尝试返回错误，则无法同时在群集或节点池上执行升级和缩放操作。 相反，每个操作类型必须在目标资源上完成，然后才能在该相同资源上发出下一个请求。 有关详细信息，请参阅[故障排除指南](https://aka.ms/aks-pending-upgrade)。

当 AKS 群集最初是在第一步中创建的时，指定了*1.15.7* `--kubernetes-version`。 这会为控制平面和默认节点池设置 Kubernetes 版本。 本部分中的命令介绍如何升级单个特定节点池。

[下面的部分](#upgrade-a-cluster-control-plane-with-multiple-node-pools)介绍了升级控制平面的 Kubernetes 版本与节点池之间的关系。

> [!NOTE]
> 节点池 OS 映像版本绑定到群集的 Kubernetes 版本。 在群集升级之后，你只会获得 OS 映像升级。

因为在此示例中有两个节点池，所以必须使用[az aks nodepool upgrade][az-aks-nodepool-upgrade]升级节点池。 让我们将*mynodepool*升级到 Kubernetes *1.15.7*。 使用[az aks nodepool upgrade][az-aks-nodepool-upgrade]命令升级节点池，如以下示例中所示：

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.15.7 \
    --no-wait
```

使用[az aks node pool list][az-aks-nodepool-list]命令再次列出节点池的状态。 以下示例显示*mynodepool*处于*1.15.7*的*升级*状态：

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

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

将节点升级到指定的版本需要几分钟时间。

最佳做法是，应将 AKS 群集中的所有节点池升级到相同的 Kubernetes 版本。 `az aks upgrade` 的默认行为是将所有节点池与控制平面一起升级以实现此对齐方式。 升级各个节点池的功能使你能够在节点池之间执行滚动升级和计划 pod，以维持上述约束中的应用程序正常运行时间。

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>升级具有多个节点池的群集控制平面

> [!NOTE]
> Kubernetes 使用标准[语义版本](https://semver.org/)控制方案。 版本号表示为*x.x*，其中*x*是主要版本， *y*是次版本， *z*是修补程序版本。 例如，在版本*1.12.6*中，1表示主版本，12表示次版本，6表示修补程序版本。 在群集创建过程中，会设置控制平面和初始节点池的 Kubernetes 版本。 将所有其他节点池添加到群集时，将设置其 Kubernetes 版本。 Kubernetes 版本可能在节点池之间以及节点池和控制平面之间有所不同。

AKS 群集具有两个与 Kubernetes 版本关联的群集资源对象。

1. 群集控制平面 Kubernetes 版本。
2. 具有 Kubernetes 版本的节点池。

控件平面映射到一个或多个节点池。 升级操作的行为取决于所使用的 Azure CLI 命令。

升级 AKS 控制平面需要使用 `az aks upgrade`。 这会升级群集中的控制平面版本和所有节点池。 

发出带 `--control-plane-only` 标志的 `az aks upgrade` 命令仅升级群集控制平面。 不会更改群集中的任何关联节点池。

升级各个节点池需要使用 `az aks nodepool upgrade`。 这仅升级具有指定 Kubernetes 版本的目标节点池

### <a name="validation-rules-for-upgrades"></a>升级的验证规则

群集的控制平面和节点池的有效 Kubernetes 升级由以下规则集验证。

* 用于升级节点池的有效版本的规则：
   * 节点池版本必须与控制平面具有相同的*主*版本。
   * 节点池的*次*版本必须位于控制平面版本的两个*次要*版本中。
   * 节点池版本不能大于控件 `major.minor.patch` 版本。

* 提交升级操作的规则：
   * 不能降级控制平面或节点池 Kubernetes 版本。
   * 如果未指定节点池 Kubernetes 版本，则行为取决于所使用的客户端。 资源管理器模板中的声明将回退到为节点池定义的现有版本（如果使用），如果未设置，则使用控制平面版本回退。
   * 您可以在给定时间升级或缩放控制面或节点池，而不能同时在单个控制平面或节点池资源上提交多个操作。

## <a name="scale-a-node-pool-manually"></a>手动缩放节点池

当应用程序工作负荷需求改变时，可能需要扩展节点池中的节点数。 节点数可以向上或向下缩放。

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

若要缩放节点池中的节点数，请使用[az aks node pool scale][az-aks-nodepool-scale]命令。 下面的示例将*mynodepool*中的节点数扩展到*5*：

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

使用[az aks node pool list][az-aks-nodepool-list]命令再次列出节点池的状态。 下面的示例显示*mynodepool*处于*缩放*状态，其新计数为*5*个节点：

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

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

完成缩放操作需要几分钟时间。

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>通过启用群集来自动缩放特定节点池自动缩放程序

AKS 提供单独的功能，可使用称为[群集自动缩放程序](cluster-autoscaler.md)的功能自动缩放节点池。 可以为每个节点池启用此功能，每个节点池具有唯一的最小和最大刻度计数。 了解如何[使用每个节点池的群集自动缩放程序](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。

## <a name="delete-a-node-pool"></a>删除节点池

如果不再需要某个池，可以将其删除并删除基础 VM 节点。 若要删除节点池，请使用[az aks node pool delete][az-aks-nodepool-delete]命令并指定节点池名称。 下面的示例删除在前面的步骤中创建的*mynoodepool* ：

> [!CAUTION]
> 删除节点池时，不存在任何可能发生的数据丢失恢复选项。 如果无法在其他节点池上计划 pod，则这些应用程序不可用。 当使用中的应用程序没有数据备份或在群集中的其他节点池上运行时，请确保不删除节点池。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

以下来自[az aks node pool list][az-aks-nodepool-list]命令的示例输出显示： *mynodepool*处于*删除*状态：

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

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

删除节点和节点池需要几分钟时间。

## <a name="specify-a-vm-size-for-a-node-pool"></a>指定节点池的 VM 大小

在上述示例中，若要创建节点池，请使用群集中创建的节点的默认 VM 大小。 更常见的情况是创建具有不同 VM 大小和功能的节点池。 例如，你可以创建一个节点池，其中包含具有大量 CPU 或内存的节点或提供 GPU 支持的节点池。 在下一步中，你[将使用 taints 和 tolerations](#schedule-pods-using-taints-and-tolerations)来告知 Kubernetes 计划程序如何将访问权限限制为可在这些节点上运行的 pod。

在以下示例中，创建使用*Standard_NC6* VM 大小的基于 GPU 的节点池。 这些 Vm 由 NVIDIA Tesla K80 卡提供支持。 有关可用 VM 大小的信息，请参阅[Azure 中 Linux 虚拟机的大小][vm-sizes]。

再次使用[az aks node pool add][az-aks-nodepool-add]命令创建节点池。 这一次，请指定名称*gpunodepool*，并使用 `--node-vm-size` 参数指定*Standard_NC6*大小：

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

以下来自[az aks node pool list][az-aks-nodepool-list]命令的示例输出显示*gpunodepool*正在*创建*具有指定*VmSize*的节点：

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

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

成功创建*gpunodepool*需要几分钟时间。

## <a name="schedule-pods-using-taints-and-tolerations"></a>使用 taints 和 tolerations 计划箱

现在，群集中有两个节点池：最初创建的默认节点池，以及基于 GPU 的节点池。 使用[kubectl get 节点][kubectl-get]命令查看群集中的节点。 以下示例输出显示节点：

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.15.7
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.15.7
```

Kubernetes 计划程序能够使用排斥和容许来限制可在节点上运行的工作负荷。

* 将**排斥**应用到指明了只能计划特定 pod 的节点。
* 然后，将**容许**应用到可以*容许*节点排斥的 pod。

有关如何使用高级 Kubernetes 计划功能的详细信息，请参阅[AKS 中高级计划程序功能的最佳实践][taints-tolerations]

在此示例中，使用--taints 命令将破坏应用到基于 GPU 的节点。 从上一个 `kubectl get nodes` 命令的输出中指定基于 GPU 的节点的名称。 破坏将应用为*键：值*和计划选项。 下面的示例使用*sku = gpu*对，并定义了*NoSchedule*功能：

```console
az aks nodepool add --node-taints aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

下面的基本示例 YAML 清单使用 toleration，以允许 Kubernetes 计划程序在基于 GPU 的节点上运行 NGINX pod。 有关对 MNIST 数据集运行 Tensorflow 作业的更合适但更耗时的示例，请参阅[AKS 上的将 gpu 用于计算密集型工作负荷][gpu-cluster]。

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

需要几秒钟时间来计划 pod，并请求 NGINX 图像。 使用[kubectl 说明 pod][kubectl-describe]命令查看 pod 状态。 以下精简示例输出显示了 " *sku = gpu： NoSchedule* toleration"。 在 "事件" 部分中，计划程序已将 pod 分配到*aks-gpunodepool-28993262-vmss000000*基于 GPU 的节点：

```console
$ kubectl describe pod mypod

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

只能在*gpunodepool*中的节点上计划已应用此破坏的箱。 任何其他 pod 都将在*nodepool1*节点池中进行计划。 如果创建其他节点池，则可以使用附加的 taints 和 tolerations 来限制可以在这些节点资源上计划的 pod。

## <a name="manage-node-pools-using-a-resource-manager-template"></a>使用资源管理器模板管理节点池

使用 Azure 资源管理器模板来创建和管理资源时，通常可以更新模板中的设置，并重新部署以更新资源。 对于 AKS 中的节点池，在创建 AKS 群集后，初始节点池配置文件将无法更新。 此行为意味着你不能更新现有资源管理器模板，更改节点池，并重新部署。 相反，您必须创建单独的资源管理器模板，该模板仅更新现有 AKS 群集的节点池。

创建一个模板（如 `aks-agentpools.json`），并粘贴下面的示例清单。 此示例模板配置以下设置：

* 将名为*myagentpool*的*Linux*节点池更新为运行三个节点。
* 将节点池中的节点设置为运行 Kubernetes 版本*1.15.7*。
* 将节点大小定义为*Standard_DS2_v2*。

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

使用[az group deployment create][az-group-deployment-create]命令部署此模板，如以下示例中所示。 系统将提示你输入现有的 AKS 群集名称和位置：

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

根据资源管理器模板中定义的节点池设置和操作，更新 AKS 群集可能需要几分钟时间。

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>为节点池中的每个节点分配公共 IP

> [!WARNING]
> 在为每个节点分配公共 IP 的预览过程中，由于可能存在与 VM 预配冲突的负载均衡器规则，因此不能将其与*AKS 中的标准负载均衡器 SKU*一起使用。 由于此限制，此预览功能不支持 Windows 代理池。 在预览中，如果需要为每个节点分配一个公共 IP，则必须使用*基本负载均衡器 SKU* 。

AKS 节点不需要自己的公共 IP 地址进行通信。 但某些情况下，可能需要节点池中的节点具有其自己的公共 IP 地址。 例如游戏，控制台需要直接连接到云虚拟机以最大程度地减少跃点。 为此，可以注册单独的预览功能 "节点公共 IP （预览版）"。

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

注册成功后，按照[上述](#manage-node-pools-using-a-resource-manager-template)相同说明部署 Azure 资源管理器模板，并将 "布尔值" 属性 `enableNodePublicIP` 添加到 "agentPoolProfiles"。 将值设置为 `true` 默认情况下，如果未指定，则将其设置为 `false`。 这只是一个创建时的属性，需要的最低 API 版本为2019-06-01。 这可同时适用于 Linux 和 Windows 节点池。

## <a name="clean-up-resources"></a>清理资源

本文创建了包含基于 GPU 的节点的 AKS 群集。 为了降低不必要的成本，你可能想要删除*gpunodepool*或整个 AKS 群集。

若要删除基于 GPU 的节点池，请使用[az aks nodepool delete][az-aks-nodepool-delete]命令，如以下示例中所示：

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

若要删除群集本身，请使用[az group delete][az-group-delete]命令删除 AKS 资源组：

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 AKS 群集中创建和管理多个节点池。 有关如何跨节点池控制 pod 的详细信息，请参阅[AKS 中高级计划程序功能的最佳实践][operator-best-practices-advanced-scheduler]。

若要创建和使用 Windows Server 容器节点池，请参阅[在 AKS 中创建 Windows server 容器][aks-windows]。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[quotas-skus-regions]: quotas-skus-regions.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-list
[az-aks-nodepool-upgrade]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-delete
[vm-sizes]: ../virtual-machines/linux/sizes.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[gpu-cluster]: gpu-cluster.md
[az-group-delete]: /cli/azure/group#az-group-delete
[install-azure-cli]: /cli/azure/install-azure-cli
[supported-versions]: supported-kubernetes-versions.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-windows]: windows-container-cli.md
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
