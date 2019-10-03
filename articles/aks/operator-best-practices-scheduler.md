---
title: 操作员最佳做法 - Azure Kubernetes 服务 (AKS) 中的基本计划程序功能
description: 了解有关使用 Azure Kubernetes 服务 (AKS) 中的基本计划程序功能（例如资源配额和 pod 中断预算）的群集操作员最佳做法
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: mlearned
ms.openlocfilehash: 3ce59784b2c7c1d145d99786b10927c230146c8b
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614625"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的基本计划程序功能的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，通常需要隔离团队和工作负荷。 Kubernetes 计划程序提供所需的功能让你控制计算资源的分配，或限制维护事件造成的影响。

本最佳做法文章重点介绍面向群集操作员的基本 Kubernetes 计划功能。 在本文中，学习如何：

> [!div class="checklist"]
> * 使用资源配额向团队或工作负荷提供固定的资源量
> * 使用 pod 中断预算限制计划性维护造成的影响
> * 使用 `kube-advisor` 工具检查是否缺少 pod 资源请求和限制

## <a name="enforce-resource-quotas"></a>强制实施资源配额

**最佳做法指导** - 在命名空间级别规划和应用资源配额。 如果 pod 未定义资源请求和限制，则拒绝部署。 监视资源用量并根据需要调整配额。

在 pod 规范中放置资源请求和限制。 在部署时，Kubernetes 计划程序使用这些限制在群集中查找可用的节点。 这些限制和请求在单个 pod 级别应用。 有关如何定义这些值的详细信息，请参阅[定义 pod 资源请求和限制][resource-limits]

若要通过某个方式来保留和限制整个开发团队或项目的资源，应使用资源配额。  这些配额在命名空间中定义，可用于根据以下条件设置配额：

* **计算资源**：例如 CPU 和内存，或 GPU。
* **存储资源**：包括给定存储类的总卷数或磁盘空间量。
* **对象计数**：例如，可创建的最大机密、服务或作业数。

Kubernetes 不会过度使用资源。 一旦资源请求或限制的累积总数超过分配的配额，则所有后续部署都不会成功。

定义资源配额时，命名空间中创建的所有 pod 必须在其 pod 规范中提供限制或请求。 如果它们未提供这些值，则你可以拒绝部署。 相反，你可以[配置默认请求和命名空间的限制][configure-default-quotas]。

以下名为 *dev-app-team-quotas.yaml* 的示例 YAML 清单设置了总共 *10* 个 CPU、*20Gi* 内存和 *10* 个 pod 的硬限制：

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

可以通过指定命名空间（例如 *dev-apps*）来应用此资源配额：

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

请咨询应用程序开发人员和所有者以了解其需求，并应用适当的资源配额。

有关可用的资源对象、 范围和优先级的详细信息，请参阅[在 Kubernetes 中的资源配额][k8s-resource-quotas]。

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>使用 pod 中断预算进行可用性规划

**最佳做法指导** - 若要维持应用程序的可用性，请定义 Pod 中断预算 (PDB)，以确保群集中有最少量的 pod 可供使用。

有两个中断性事件会导致 pod 被删除：

* 非自愿性中断是群集操作员或应用程序所有者无法以一般方式进行控制的事件。 
  * 这些非自愿性中断包括物理机上的硬件故障、内核崩溃或删除节点 VM
* 自愿性中断是群集操作员或应用程序所有者请求的事件。 
  * 这些自愿性中断包括群集升级、部署模板更新，或意外删除 pod。

在部署中使用 pod 的多个副本可以缓解非自愿性中断。 在 AKS 群集中运行多个节点也有助于缓解这些非自愿性中断。 Kubernetes 针对自愿性中断提供 pod 中断预算，让群集操作员定义最小可用资源计数或最大不可用资源计数。  使用这些 pod 中断预算可以规划当发生自愿性中断事件时，部署或副本集如何做出响应。

如果要升级群集或更新部署模板，Kubernetes 计划程序会确保在其他节点上计划其他 pod，然后，自愿性中断事件可以继续。 在重新启动节点之前，计划程序将一直等到在群集中的其他节点上成功计划了定义的 pod 数为止。

让我们探讨一个副本集示例，其中包含五个运行 NGINX 的 pod。 为该副本集中的 pod 分配了标签 `app: nginx-frontend`。 在发生自愿性中断事件（例如群集升级）期间，你想要确保至少有三个 pod 可继续运行。 *PodDisruptionBudget* 对象的以下 YAML 清单定义了这些要求：

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
   matchLabels:
      app: nginx-frontend
```

还可以定义一个百分比（例如 *60%* ），以便在扩展 pod 数目时可以自动补偿副本集。

可在副本集中定义最大不可用实例数。 同样，也可以定义最大不可用 pod 数的百分比。 以下 pod 中断预算 YAML 清单定义副本集中不可用的 pod 数不能超过两个：

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
   matchLabels:
      app: nginx-frontend
```

定义 pod 中断预算后，可以像创建其他任何 Kubernetes 对象一样，在 AKS 群集中创建中断预算：

```console
kubectl apply -f nginx-pdb.yaml
```

请咨询应用程序开发人员和所有者以了解其需求，并应用适当的 pod 中断预算。

有关使用 pod 中断预算的详细信息，请参阅[指定你的应用程序在中断预算][k8s-pdbs]。

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>定期使用 kube-advisor 检查群集问题

**最佳做法指导** - 定期运行最新版本的 `kube-advisor` 开放源代码工具，以检测群集中的问题。 如果针对现有 AKS 群集应用资源配额，请先运行 `kube-advisor`，以查找未定义资源请求和限制的 pod。

[Kube 顾问][kube-advisor]工具是一个相关联的 AKS 开放源代码项目，扫描的 Kubernetes 群集并报告它找到的问题。 一项有用的检查是识别未应用资源请求和限制的 pod。

Kube 顾问工具可报告资源请求和 PodSpecs 的 Windows 应用程序，以及 Linux 应用程序中缺少的限制，但 kube 顾问工具本身必须安排在 Linux pod。 您可以计划在特定 OS 使用的节点池上运行的 pod[节点选择器][k8s-node-selector]pod 的配置中。

在托管多个开发团队和应用程序的 AKS 群集中，可能很难跟踪未设置这些资源请求和限制的 pod。 最佳做法是定期针对 AKS 群集运行 `kube-advisor`，尤其是未向命名空间分配资源配额时。

## <a name="next-steps"></a>后续步骤

本文重点介绍了基本 Kubernetes 计划程序功能。 有关 AKS 中的群集操作的详细信息，请参阅以下最佳做法：

* [多租户和群集的隔离][aks-best-practices-cluster-isolation]
* [高级的 Kubernetes 计划程序功能][aks-best-practices-advanced-scheduler]
* [身份验证和授权][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
