---
title: 在 Azure Kubernetes 服务中使用多个节点池 (AKS)
description: 了解如何在 Azure Kubernetes Service (AKS) 中创建和管理群集的多个节点池
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 08/9/2019
ms.author: mlearned
ms.openlocfilehash: 514098368c38c6d61bc192f5ba0f0450dc05776c
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533473"
---
# <a name="preview---create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>预览-创建和管理 Azure Kubernetes 服务中群集的多个节点池 (AKS)

在 Azure Kubernetes Service (AKS) 中, 相同配置的节点组合在一起成为*节点池*。 这些节点池包含运行应用程序的基础 Vm。 初始节点数及其大小 (SKU) 是在创建 AKS 群集时定义的, 该群集创建*默认节点池*。 若要支持具有不同计算或存储需求的应用程序, 可以创建其他节点池。 例如, 使用这些附加的节点池为计算密集型应用程序或高性能 SSD 存储提供 Gpu。

> [!NOTE]
> 利用此功能, 可以更好地控制如何创建和管理多个节点池。 因此, 创建/更新/删除需要单独的命令。 以前通过`az aks create`或`az aks update`使用 managedCluster API 进行群集操作, 并且是更改控制平面和单节点池的唯一选项。 此功能通过 agentPool API 为代理池公开单独的操作集, 并要求使用`az aks nodepool`命令集在单个节点池上执行操作。

本文介绍如何在 AKS 群集中创建和管理多个节点池。 此功能目前处于预览状态。

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供, 并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此, 这些功能并不用于生产。 有关其他信息, 请参阅以下支持文章:
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="before-you-begin"></a>开始之前

需要安装并配置 Azure CLI 2.0.61 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI][install-azure-cli]。

### <a name="install-aks-preview-cli-extension"></a>安装 aks-preview CLI 扩展

若要使用多个节点池, 需要*aks* CLI 扩展版本0.4.1 或更高版本。 使用[az extension add][az-extension-add]命令安装*aks-preview* Azure CLI 扩展, 然后使用[az extension update][az-extension-update]命令检查是否有任何可用更新:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-multiple-node-pool-feature-provider"></a>注册多节点池功能提供程序

若要创建可使用多个节点池的 AKS 群集, 请先在订阅上启用两项功能标志。 多节点池群集使用虚拟机规模集 (VMSS) 管理 Kubernetes 节点的部署和配置。 使用[az feature register][az-feature-register]命令注册*MultiAgentpoolPreview*和*VMSSPreview*功能标志, 如以下示例中所示:

> [!CAUTION]
> 在订阅上注册功能时, 当前无法注册该功能。 启用某些预览功能后, 默认值可用于在订阅中创建的所有 AKS 群集。 不要对生产订阅启用预览功能。 使用单独的订阅来测试预览功能并收集反馈。

```azurecli-interactive
az feature register --name MultiAgentpoolPreview --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

> [!NOTE]
> 成功注册*MultiAgentpoolPreview*之后创建的任何 AKS 群集均使用此预览版群集体验。 若要继续创建常规且完全受支持的群集, 请不要对生产订阅启用预览功能。 使用单独的测试或开发 Azure 订阅来测试预览功能。

状态显示为“已注册”需要几分钟时间。 您可以使用[az feature list][az-feature-list]命令检查注册状态:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MultiAgentpoolPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

准备就绪后, 请使用[az provider register][az-provider-register]命令刷新*ContainerService*资源提供程序的注册:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>限制

创建和管理支持多个节点池的 AKS 群集时, 有以下限制:

* 只有已成功为订阅注册了*MultiAgentpoolPreview*和*VMSSPreview*功能后, 多个节点池才可用于创建的群集。 在成功注册这些功能之前, 无法添加或管理已创建的现有 AKS 群集的节点池。
* 不能删除第一个节点池。
* 无法使用 HTTP 应用程序路由加载项。
* 与大多数操作一样, 不能使用现有的资源管理器模板来添加/更新/删除节点池。 请改用[单独的资源管理器模板](#manage-node-pools-using-a-resource-manager-template)来更改 AKS 群集中的节点池。

此功能处于预览阶段, 但以下附加限制适用:

* AKS 群集最多可以有8个节点池。
* AKS 群集在这八个节点池中最多可以有400个节点。
* 所有节点池必须位于同一子网中

## <a name="create-an-aks-cluster"></a>创建 AKS 群集

若要开始, 请创建具有单个节点池的 AKS 群集。 以下示例使用[az group create][az-group-create]命令在*eastus*区域中创建名为*myResourceGroup*的资源组。 然后, 使用[az AKS create][az-aks-create]命令创建名为*myAKSCluster*的 AKS 群集。 *1.13.9*的*kubernetes 版本*用于说明如何在以下步骤中更新节点池。 可以指定任何[受支持的 Kubernetes 版本][supported-versions]。

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --generate-ssh-keys \
    --kubernetes-version 1.13.9
```

创建群集需要几分钟时间。

群集准备就绪后, 请使用[az aks get 凭据][az-aks-get-credentials]命令获取用于的群集凭据`kubectl`:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>添加节点池

在上一步中创建的群集具有单个节点池。 让我们使用[az aks nodepool add][az-aks-nodepool-add]命令添加另一个节点池。 以下示例创建一个名为*mynodepool*的节点池, 运行*3*个节点:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --kubernetes-version 1.12.7
```

若要查看节点池的状态, 请使用[az aks node pool list][az-aks-nodepool-list]命令并指定资源组和群集名称:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

以下示例输出显示已成功地在节点池中有三个节点创建了*mynodepool* 。 在上一步中创建 AKS 群集时, 创建的默认*nodepool1*的节点计数为*1*。

```console
$ az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.12.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> 如果在添加节点池时未指定*OrchestratorVersion*或*VmSize* , 则将基于 AKS 群集的默认值创建节点。 在此示例中, 为 Kubernetes 版本*1.13.9*和*Standard_DS2_v2*的节点大小。

## <a name="upgrade-a-node-pool"></a>升级节点池

> [!NOTE]
> 群集或节点池上的升级和缩放操作是互相排斥的。 不能同时升级和缩放群集或节点池。 相反, 每个操作类型必须在目标资源上完成, 然后才能在该相同资源上发出下一个请求。 有关详细信息, 请参阅[故障排除指南](https://aka.ms/aks-pending-upgrade)。

在第一步中创建 AKS 群集时, 指定了`--kubernetes-version` *1.13.9*的。 这会为控制平面和初始节点池设置 Kubernetes 版本。 可以通过不同的命令来升级控制平面和节点池的 Kubernetes 版本。 命令用于升级控制平面, `az aks nodepool upgrade`而用于升级单个节点池。 `az aks upgrade`

让我们将*mynodepool*升级到 Kubernetes *1.13.9*。 使用[az aks 节点池升级][az-aks-nodepool-upgrade]命令升级节点池, 如以下示例中所示:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version 1.13.9 \
    --no-wait
```

> [!Tip]
> 若要将控制面升级到*1.14.5*, `az aks upgrade -k 1.14.5`请运行。

使用[az aks node pool list][az-aks-nodepool-list]命令再次列出节点池的状态。 以下示例显示*mynodepool*处于*1.13.9*的*升级*状态:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

将节点升级到指定的版本需要几分钟时间。

最佳做法是, 应将 AKS 群集中的所有节点池升级到相同的 Kubernetes 版本。 升级各个节点池的功能使你能够在节点池之间执行滚动升级和计划 pod, 以维持上述约束中的应用程序正常运行时间。

> [!NOTE]
> Kubernetes 使用标准的[语义化版本控制](https://semver.org/)方案。 版本号表示为*x.x*, 其中*x*是主要版本, *y*是次版本, *z*是修补程序版本。 例如, 在版本*1.12.6*中, 1 表示主版本, 12 表示次版本, 6 表示修补程序版本。 在群集创建过程中, 会设置控制平面和初始节点池的 Kubernetes 版本。 将所有其他节点池添加到群集时, 将设置其 Kubernetes 版本。 Kubernetes 版本可能在节点池之间以及节点池和控制平面之间有所不同, 但以下限制适用:
> 
> * 节点池版本必须与控制平面具有相同的主版本。
> * 节点池版本可能比控制平面版本少1个次要版本。
> * 只要遵循其他两个约束, 节点池版本就可以是任何修补程序版本。
> 
> 若要升级控件平面的 Kubernetes 版本, 请使用`az aks upgrade`。 如果群集只有一个节点池, 则该`az aks upgrade`命令还将升级节点池的 Kubernetes 版本。

## <a name="scale-a-node-pool-manually"></a>手动缩放节点池

当应用程序工作负荷需求改变时, 可能需要扩展节点池中的节点数。 节点数可以向上或向下缩放。

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

若要缩放节点池中的节点数, 请使用[az aks node pool scale][az-aks-nodepool-scale]命令。 下面的示例将*mynodepool*中的节点数扩展到*5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

使用[az aks node pool list][az-aks-nodepool-list]命令再次列出节点池的状态。 下面的示例显示*mynodepool*处于*缩放*状态, 其新计数为*5*个节点:

```console
$ az aks nodepool list -g myResourceGroupPools --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
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

AKS 提供了一个单独的预览功能, 可使用称为[群集自动缩放程序](cluster-autoscaler.md)的功能自动缩放节点池。 此功能是一个 AKS 外接程序, 可对每个节点池启用每个节点池的唯一最小和最大刻度计数。 了解如何[使用每个节点池的群集自动缩放程序](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)。

## <a name="delete-a-node-pool"></a>删除节点池

如果不再需要某个池, 可以将其删除并删除基础 VM 节点。 若要删除节点池, 请使用[az aks node pool delete][az-aks-nodepool-delete]命令并指定节点池名称。 下面的示例删除在前面的步骤中创建的*mynoodepool* :

> [!CAUTION]
> 删除节点池时, 不存在任何可能发生的数据丢失恢复选项。 如果无法在其他节点池上计划 pod, 则这些应用程序不可用。 当使用中的应用程序没有数据备份或在群集中的其他节点池上运行时, 请确保不删除节点池。

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

以下来自[az aks node pool list][az-aks-nodepool-list]命令的示例输出显示: *mynodepool*处于*删除*状态:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
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

在上述示例中, 若要创建节点池, 请使用群集中创建的节点的默认 VM 大小。 更常见的情况是创建具有不同 VM 大小和功能的节点池。 例如, 你可以创建一个节点池, 其中包含具有大量 CPU 或内存的节点或提供 GPU 支持的节点池。 在下一步中, 你[将使用 taints 和 tolerations](#schedule-pods-using-taints-and-tolerations)来告知 Kubernetes 计划程序如何将访问权限限制为可在这些节点上运行的 pod。

在以下示例中, 创建一个使用*Standard_NC6* VM 大小的基于 GPU 的节点池。 这些 Vm 由 NVIDIA Tesla K80 卡提供支持。 有关可用 VM 大小的信息, 请参阅[Azure 中 Linux 虚拟机的大小][vm-sizes]。

再次使用[az aks node pool add][az-aks-nodepool-add]命令创建节点池。 这一次, 请指定名称*gpunodepool*, 并使用`--node-vm-size`参数指定*Standard_NC6*大小:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

以下来自[az aks node pool list][az-aks-nodepool-list]命令的示例输出显示*gpunodepool*正在*创建*具有指定*VmSize*的节点:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.13.9",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 1,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.13.9",
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

现在, 群集中有两个节点池: 最初创建的默认节点池, 以及基于 GPU 的节点池。 使用[kubectl get 节点][kubectl-get]命令查看群集中的节点。 以下示例输出显示每个节点池中的一个节点:

```console
$ kubectl get nodes

NAME                                 STATUS   ROLES   AGE     VERSION
aks-gpunodepool-28993262-vmss000000  Ready    agent   4m22s   v1.13.9
aks-nodepool1-28993262-vmss000000    Ready    agent   115m    v1.13.9
```

Kubernetes 计划程序能够使用排斥和容许来限制可在节点上运行的工作负荷。

* 将**排斥**应用到指明了只能计划特定 pod 的节点。
* 然后，将**容许**应用到可以*容许*节点排斥的 pod。

有关如何使用高级 Kubernetes 计划功能的详细信息, 请参阅[AKS 中高级计划程序功能的最佳实践][taints-tolerations]

在此示例中, 使用[kubectl 破坏 node][kubectl-taint]命令将破坏应用到基于 GPU 的节点。 从上一个`kubectl get nodes`命令的输出中指定基于 GPU 的节点的名称。 破坏将应用为*键: 值*和计划选项。 下面的示例使用*sku = gpu*对, 并定义了*NoSchedule*功能:

```console
kubectl taint node aks-gpunodepool-28993262-vmss000000 sku=gpu:NoSchedule
```

下面的基本示例 YAML 清单使用 toleration, 以允许 Kubernetes 计划程序在基于 GPU 的节点上运行 NGINX pod。 有关对 MNIST 数据集运行 Tensorflow 作业的更合适但更耗时的示例, 请参阅[AKS 上的将 gpu 用于计算密集型工作负荷][gpu-cluster]。

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

使用`kubectl apply -f gpu-toleration.yaml`命令计划 pod:

```console
kubectl apply -f gpu-toleration.yaml
```

需要几秒钟时间来计划 pod, 并请求 NGINX 图像。 使用[kubectl 说明 pod][kubectl-describe]命令查看 pod 状态。 以下精简示例输出显示了 " *sku = gpu: NoSchedule* toleration"。 在 "事件" 部分中, 计划程序已将 pod 分配到*aks-gpunodepool-28993262-vmss000000*基于 GPU 的节点:

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

只能在*gpunodepool*中的节点上计划已应用此破坏的箱。 任何其他 pod 都将在*nodepool1*节点池中进行计划。 如果创建其他节点池, 则可以使用附加的 taints 和 tolerations 来限制可以在这些节点资源上计划的 pod。

## <a name="manage-node-pools-using-a-resource-manager-template"></a>使用资源管理器模板管理节点池

使用 Azure 资源管理器模板来创建和管理资源时, 通常可以更新模板中的设置, 并重新部署以更新资源。 对于 AKS 中的节点池, 在创建 AKS 群集后, 初始节点池配置文件将无法更新。 此行为意味着你不能更新现有资源管理器模板, 更改节点池, 并重新部署。 相反, 您必须创建单独的资源管理器模板, 该模板仅更新现有 AKS 群集的代理池。

创建模板 (例如) `aks-agentpools.json` , 并粘贴下面的示例清单。 此示例模板配置以下设置:

* 将名为*myagentpool*的*Linux*代理池更新为运行三个节点。
* 将节点池中的节点设置为运行 Kubernetes 版本*1.13.9*。
* 将节点大小定义为*Standard_DS2_v2*。

根据需要编辑这些值, 以更新、添加或删除节点池:

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
      "aks": "2019-04-01"
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
      "apiVersion": "2019-04-01",
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
            "orchestratorVersion": "1.13.9"
      }
    }
  ]
}
```

使用[az group deployment create][az-group-deployment-create]命令部署此模板, 如以下示例中所示。 系统将提示你输入现有的 AKS 群集名称和位置:

```azurecli-interactive
az group deployment create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

根据资源管理器模板中定义的节点池设置和操作, 更新 AKS 群集可能需要几分钟时间。

## <a name="assign-a-public-ip-per-node-in-a-node-pool"></a>为节点池中的每个节点分配公共 IP

> [!NOTE]
> 在预览期间, 由于可能存在与 VM 预配冲突的负载均衡器规则, 因此*在 AKS (预览版) 中*将此功能与标准负载均衡器 SKU 一起使用的限制。 在预览中, 如果需要为每个节点分配一个公共 IP, 请使用*基本负载均衡器 SKU* 。

AKS 节点不需要自己的公共 IP 地址进行通信。 但某些情况下, 可能需要节点池中的节点具有其自己的公共 IP 地址。 例如游戏, 控制台需要直接连接到云虚拟机以最大程度地减少跃点。 为此, 可以注册单独的预览功能 "节点公共 IP (预览版)"。

```azurecli-interactive
az feature register --name NodePublicIPPreview --namespace Microsoft.ContainerService
```

注册成功后, 按照[上述](#manage-node-pools-using-a-resource-manager-template)相同说明部署 Azure 资源管理器模板, 并在 agentPoolProfiles 上添加以下布尔值属性 "enableNodePublicIP"。 将此`true`值设置为, 默认情况下它将`false`设置为 (如果未指定)。 这只是一个创建时的属性, 需要的最低 API 版本为2019-06-01。 这可同时适用于 Linux 和 Windows 节点池。

```
"agentPoolProfiles":[  
    {  
      "maxPods": 30,
      "osDiskSizeGB": 0,
      "agentCount": 3,
      "agentVmSize": "Standard_DS2_v2",
      "osType": "Linux",
      "vnetSubnetId": "[parameters('vnetSubnetId')]",
      "enableNodePublicIP":true
    }
```

## <a name="clean-up-resources"></a>清理资源

本文创建了包含基于 GPU 的节点的 AKS 群集。 为了降低不必要的成本, 你可能想要删除*gpunodepool*或整个 AKS 群集。

若要删除基于 GPU 的节点池, 请使用[az aks nodepool delete][az-aks-nodepool-delete]命令, 如以下示例中所示:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

若要删除群集本身, 请使用[az group delete][az-group-delete]命令删除 AKS 资源组:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 AKS 群集中创建和管理多个节点池。 有关如何跨节点池控制 pod 的详细信息, 请参阅[AKS 中高级计划程序功能的最佳实践][operator-best-practices-advanced-scheduler]。

若要创建和使用 Windows Server 容器节点池, 请参阅[在 AKS 中创建 Windows server 容器][aks-windows]。

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- INTERNAL LINKS -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
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
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
