---
title: 操作员最佳做法 - Azure Kubernetes 服务 (AKS) 中的群集隔离
description: 了解有关 Azure Kubernetes 服务 (AKS) 中的隔离的群集操作员最佳做法
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: conceptual
origin.date: 11/26/2018
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: 94aaa72497a8a5f171d6b42f59a3c5b507c71492
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465271"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的群集隔离的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，通常需要隔离团队和工作负荷。 AKS 可让你灵活运行多租户群集和隔离资源。 为了最大化 Kubernetes 的投资回报，请了解并实施这些多租户和隔离功能。

本最佳做法文章向群集操作员重点介绍隔离。 在本文中，学习如何：

> [!div class="checklist"]
> * 规划多租户群集和资源隔离
> * 在 AKS 群集中使用逻辑或物理隔离

## <a name="design-clusters-for-multi-tenancy"></a>设计多租户群集

Kubernetes 提供所需的功能让你在同一个群集中逻辑隔离团队和工作负荷。 目标应该是提供最少量的特权，特权范围限定为每个团队所需的资源。 Kubernetes 中的[命名空间][k8s-namespaces]创建逻辑隔离边界。 其他 Kubernetes 功能以及有关隔离和多租户的注意事项包括以下几个方面：

* “计划”包括资源配额和 pod 中断预算等基本功能的用法。 有关这些功能的详细信息，请参阅[有关 AKS 中基本计划程序功能的最佳做法][aks-best-practices-scheduler]。
  * 更高级的计划程序功能包括排斥 (taint) 和容许 (toleration)、节点选择器，以及节点和 pod 关联与反关联。 有关这些功能的详细信息，请参阅[有关 AKS 中高级计划程序功能的最佳做法][aks-best-practices-advanced-scheduler]。
* “网络”包括用于控制传入和传出 pod 的流量流的网络策略的用法。
* “身份验证和授权”包括基于角色的访问控制 (RBAC) 和 Azure Active Directory (AD) 集成、pod 标识以及 Azure Key Vault 中的机密的用法。 有关这些功能的详细信息，请参阅[有关 AKS 中身份验证和授权的最佳做法][aks-best-practices-identity]。
* “容器”包括 pod 安全策略、pod 安全上下文，以及扫描映像和运行时中的漏洞。 此外，还涉及到使用 App Armor 或 Seccomp（安全计算）来限制容器对基础节点的访问。

## <a name="logically-isolate-clusters"></a>逻辑隔离群集

**最佳做法指导** - 使用逻辑隔离来隔离团队和项目。 尝试尽量减少要部署的物理 AKS 群集数，以隔离团队或应用程序。

使用逻辑隔离可将单个 AKS 群集用于多个工作负荷、团队或环境。 Kubernetes [命名空间][k8s-namespaces]构成了工作负荷和资源的逻辑隔离边界。

![AKS 中 Kubernetes 群集的逻辑隔离](media/operator-best-practices-cluster-isolation/logical-isolation.png)

群集逻辑分隔提供的 pod 密度通常比物理隔离的群集更高。 群集中闲置的超额计算容量更少。 与 Kubernetes 群集自动缩放程序相结合，可根据需求增加或减少节点数目。 采用这种自动缩放最佳做法，可以只运行所需数目的节点并尽量降低成本。

AKS 或其他位置中的 Kubernetes 环境并不完全安全，因为可能存在恶意的多租户使用情况。 增加面向节点的安全功能（如 *Pod 安全策略*或更细粒度的基于角色的访问控制 (RBAC)）可增加攻击的难度。 但是，如果想真正实现可抵御恶意多租户工作负荷的安全性，虚拟机监控程序是唯一信任的安全级别。 Kubernetes 的安全域成为整个群集，而不是单个节点。 对于这些类型的恶意多租户工作负荷，应使用物理隔离的群集。

## <a name="physically-isolate-clusters"></a>物理隔离群集

**最佳做法指导** - 对于每个独立的团队或应用程序部署，尽量减少使用物理隔离。 应改用上一部分所述的逻辑隔离。

群集隔离的常用方法是使用物理上独立的 AKS 群集。 在此隔离模型中，将为团队或工作负荷分配其自身的 AKS 群集。 通常，这种方法看上去是最简单的隔离工作负荷或团队的方法，但会带来额外的管理和财务开销。 现在必须维护多个群集，并且必须单独提供访问权限和分配权限。 此外，需要为每个节点付费。

![AKS 中各个 Kubernetes 群集的物理隔离](media/operator-best-practices-cluster-isolation/physical-isolation.png)

物理上独立的群集的 pod 密度通常较低。 由于每个团队或工作负荷具有自身的 AKS 群集，因此往往会为群集过度预配计算资源。 通常在这些节点上计划少量的 pod。 节点上未使用的容量不可由其他团队用于开发中的应用程序或服务。 这些超额的资源会导致物理独立群集的成本增加。

## <a name="next-steps"></a>后续步骤

本文重点介绍了群集隔离。 有关 AKS 中的群集操作的详细信息，请参阅以下最佳做法：

* [基本 Kubernetes 计划程序功能][aks-best-practices-scheduler]
* [高级 Kubernetes 计划程序功能][aks-best-practices-advanced-scheduler]
* [身份验证和授权][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md