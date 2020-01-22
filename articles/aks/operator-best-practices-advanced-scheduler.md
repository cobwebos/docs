---
title: 操作员最佳做法 - Azure Kubernetes 服务 (AKS) 中的高级计划程序功能
description: 了解有关使用 Azure Kubernetes 服务 (AKS) 中的高级计划程序功能（例如排斥 (taint) 和容许 (toleration)、节点选择器和关联，或 pod 间关联和反关联）的群集操作员最佳做法
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 2ac66e46d449100fcdd004627820252473f6e2f3
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293651"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的高级计划程序功能的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，通常需要隔离团队和工作负荷。 Kubernetes 计划程序提供了高级功能，可用于控制可以在特定节点上安排哪些 pod，或如何在群集上适当地分布多个 pod 应用程序。 

本最佳做法文章重点介绍面向群集操作员的高级 Kubernetes 计划功能。 在本文中，学习如何：

> [!div class="checklist"]
> * 使用排斥和容许来限制可在节点上计划的 pod
> * 使用节点选择器或节点关联为特定节点上运行的 pod 分配优先顺序
> * 使用 pod 间关联或反关联来拆离或组合 pod

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>使用排斥和容许提供专用节点

**最佳做法指导** - 限制资源密集型应用程序（例如入口控制器）对特定节点的访问。 将节点资源保留给需要它们的工作负荷使用，但不允许在节点上计划其他工作负荷。

创建 AKS 群集时，可以部署支持 GPU 的节点或具有大量强大 CPU 的节点。 这些节点通常用于大数据处理工作负荷，例如机器学习 (ML) 或人工智能 (AI)。 由于此类硬件通常是需要部署的昂贵节点资源，因此需要限制可在这些节点上计划的工作负荷。 你可能想要专门使用群集中的某些节点来运行入口服务，并阻止其他工作负荷。

这种对不同节点的支持是通过使用多个节点池来提供的。 AKS 群集提供一个或多个节点池。

Kubernetes 计划程序能够使用排斥和容许来限制可在节点上运行的工作负荷。

* 将**排斥**应用到指明了只能计划特定 pod 的节点。
* 然后，将**容许**应用到可以*容许*节点排斥的 pod。

将 pod 部署到 AKS 群集时，Kubernetes 只会在容许与排斥相符的节点上计划 pod。 例如，假设 AKS 群集中有一个节点池，其中包含 GPU 支持的节点。 你定义了名称（例如 *gpu*），然后定义了计划值。 如果将此值设置为 *NoSchedule*，当 pod 未定义相应的容许时，Kubernetes 计划程序无法在节点上计划 pod。

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

将排斥应用到节点后，在 pod 规范中定义容许，以允许在节点上进行计划。 以下示例定义 `sku: gpu` 和 `effect: NoSchedule`，以容许在上一步骤中应用到节点的排斥：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

部署此 pod（例如，使用 `kubectl apply -f gpu-toleration.yaml`）后，Kubernetes 可以成功地在应用了排斥的节点上计划 pod。 通过这种逻辑隔离，可以控制对群集中资源的访问。

应用排斥时，请与应用程序开发人员和所有者协作，让他们在其部署中定义所需的容许。

有关 taints 和 tolerations 的详细信息，请参阅[应用 taints 和 tolerations][k8s-taints-tolerations]。

有关如何在 AKS 中使用多个节点池的详细信息，请参阅[在 AKS 中为群集创建和管理多个节点池][use-multiple-node-pools]。

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>AKS 中 taints 和 tolerations 的行为

当你升级 AKS 中的节点池时，taints 和 tolerations 将在应用到新节点时遵循设置模式：

- **使用虚拟机规模集的默认群集**
  - 假设你有一个双节点群集-节点*1*和*节点 2*。 升级节点池。
  - 将创建两个附加节点： *node3*和*节点 4*，并分别传递 taints。
  - 将删除原始*节点 1*和*节点 2* 。

- **无虚拟机规模集支持的群集**
  - 同样，假设你有一个双节点群集节点*1*和*节点 2*。 升级时，会创建一个附加节点（*node3*）。
  - *节点 1*上的 taints 将应用于*node3*，然后将删除*节点 1* 。
  - 创建另一个新节点（命名*节点 1*，因为删除了上一个节点*1* ），并将*节点 2* taints 应用于新节点*1*。 然后，删除*节点 2* 。
  - 在本质上，*节点 1*变为*node3*，*节点 2*成为*节点 1*。

当你在 AKS 中缩放节点池时，taints 和 tolerations 不会按设计进行。

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>使用节点选择器和关联控制 pod 计划

**最佳做法指导** - 使用节点选择器、节点关联或 pod 间关联来控制节点上的 pod 计划。 这些设置可让 Kubernetes 计划程序以逻辑方式（例如，按节点中的硬件）隔离工作负荷。

使用排斥和容许能够以硬分割的形式逻辑隔离资源 - 如果 pod 不容许某个节点的排斥，则不会在该节点上计划该 pod。 另一种方法是使用节点选择器。 例如，可以标记节点来指示本地附加的 SSD 存储或大量内存，并在 pod 规范中定义节点选择器。 然后，Kubernetes 会在匹配的节点上计划这些 pod。 与容许不同，没有匹配的节点选择器的 pod 可在标记的节点上计划。 通过这种行为可以使用节点上未用的资源，但会将优先级分配给定义匹配的节点选择器的 pod。

让我们查看具有大量内存的节点示例。 这些节点可向请求大量内存的 pod 分配优先顺序。 为确保资源不会闲置，它们还允许运行其他 pod。

```console
kubectl label node aks-nodepool1 hardware:highmem
```

然后，pod 规范添加 `nodeSelector` 属性，以定义与节点上设置的标签匹配的节点选择器：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
    nodeSelector:
      hardware: highmem
```

使用这些计划程序选项时，请与应用程序开发人员和所有者协作，让他们正确定义其 pod 规范。

有关使用节点选择器的详细信息，请参阅[将 Pod 分配到节点][k8s-node-selector]。

### <a name="node-affinity"></a>节点关联

节点选择器是将 pod 分配到给定节点的基本方法。 使用节点关联可以获得更高的灵活性。 使用节点关联可以定义当 pod 无法与节点匹配时发生的情况。 可以要求 Kubernetes 计划程序与包含标记主机的 pod 相匹配。 或者，可以优先选择匹配，但如果不匹配，则允许在其他主机上计划 pod。

以下示例将节点关联设置为 *requiredDuringSchedulingIgnoredDuringExecution*。 这种关联要求 Kubernetes 计划使用具有匹配标签的节点。 如果没有可用的节点，则 pod 必须等待计划继续。 若要允许在其他节点上计划 pod，可将值设置为 *preferredDuringScheduledIgnoreDuringExecution*：

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: microsoft/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

该设置的 *IgnoredDuringExecution* 部分表示当节点标签更改时，不应从节点中逐出 pod。 Kubernetes 计划程序仅对所要计划的新 pod 使用更新的节点标签，对于已在节点上计划的 pod 则不使用。

有关详细信息，请参阅[相关性和反相关性][k8s-affinity]。

### <a name="inter-pod-affinity-and-anti-affinity"></a>pod 间关联和反关联

Kubernetes 计划程序逻辑隔离工作负荷的最终方法之一是使用 pod 间关联或反关联。 这些设置定义不应在具有现有匹配 pod 的节点上计划 pod，或者应该计划 pod。 默认情况下，Kubernetes 计划程序会尝试在跨节点的副本集3中计划多个 pod。 可围绕此行为定义更具体的规则。

同时使用 Azure Redis 缓存的 Web 应用程序就是一个很好的例子。 可以使用 pod 反关联规则来请求 Kubernetes 计划程序跨节点分配副本。 然后，你可以使用关联规则来确保每个 web 应用组件与相应的缓存在同一主机上计划。 跨节点的 pod 分配如以下示例所示：

| **节点 1** | **节点 2** | **节点 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

与使用节点选择器或节点关联相比，此示例是一种更复杂的部署。 部署可让你控制 Kubernetes 如何在节点上计划 pod，并可以逻辑隔离资源。 有关适用于 Redis 的 Azure 缓存的此 web 应用程序的完整示例，请参阅[同一节点上的归置][k8s-pod-affinity]pod。

## <a name="next-steps"></a>后续步骤

本文重点介绍了高级 Kubernetes 计划程序功能。 有关 AKS 中的群集操作的详细信息，请参阅以下最佳做法：

* [多租户和群集隔离][aks-best-practices-scheduler]
* [基本 Kubernetes 计划程序功能][aks-best-practices-scheduler]
* [身份验证和授权][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
