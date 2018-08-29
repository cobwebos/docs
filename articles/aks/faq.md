---
title: 有关 Azure Kubernetes 服务 (AKS) 的常见问题解答
description: 提供有关 Azure Kubernetes 服务 (AKS) 的某些常见问题的解答。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 08/17/2018
ms.author: iainfou
ms.openlocfilehash: b910b6cdf55ae7c2a220543bdb555d8e9bff59a0
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "42145096"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 的常见问题解答

本文解答有关 Azure Kubernetes 服务 (AKS) 的常见问题。

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>哪些 Azure 区域现在可提供 Azure Kubernetes 服务 (AKS)？

有关可用区域的完整列表，请参阅 [AKS 区域和可用性][aks-regions]。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支持节点自动缩放？

支持，从 Kubernetes 1.10 开始，可以通过 [Kubernetes autoscaler][auto-scaler] 进行自动缩放。 要详细了解如何配置和使用群集自动缩放程序，请参阅 [AKS 上的群集自动缩放][aks-cluster-autoscale]。

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS 是否支持 Kubernetes 基于角色的访问控制 (RBAC)？

支持，使用 Azure CLI 创建群集时，默认启用 Kubernetes RBAC。 可为使用 Azure 门户或模板创建的集群启用 RBAC。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>是否可以将 AKS 部署到现有虚拟网络？

可以，可使用[高级网络功能][aks-advanced-networking]将 AKS 群集部署到现有虚拟网络中。

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>我可以将 Kubernetes API 服务器限制为仅能在我的虚拟网络中访问吗？

现在不行。 Kubernetes API 服务器公开为公共完全限定的域名 (FQDN)。 可使用 [Kubernetes 基于角色的访问控制 (RBAC) 和 Azure Active Directory (AAD)][aks-rbac-aad] 控制对群集的访问

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？

可以，Azure 会按照夜间计划自动将安全修补程序应用于群集中的节点。 但是，你有责任确保节点根据需要进行重启。 执行节点重启具有多种选择：

- 通过 Azure 门户或 Azure CLI 手动执行。
- 通过升级 AKS 群集。 群集自动升级 [cordon 和 drain 节点][cordon-drain]，然后使用最新的 Ubuntu 映像和新补丁版本或次要 Kubernetes 版本重新启动每个节点。 有关详细信息，请参阅[升级 AKS 群集][aks-upgrade]。
- 使用 [Kured](https://github.com/weaveworks/kured)，一种适用于 Kubernetes 的开放源代码重启守护程序。 Kured 作为 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 运行并监视每个节点，用于确定指示需要重启的文件是否存在。 通过将相同的 [cordon 和 drain 进程][cordon-drain]用作群集升级跨群集管理操作系统的重启。

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？

每个 AKS 部署都跨越两个资源组：

- 第一个资源组由你创建，仅包含 Kubernetes 服务资源。 AKS 资源提供程序在部署期间自动创建第二个资源组，例如 MC_myResourceGroup_myAKSCluster_eastus。
- 第二个资源组（例如 MC_myResourceGroup_myAKSCluster_eastus）包含与该群集关联的所有基础结构资源。 这些资源包括 Kubernetes 节点 VM、虚拟网络和存储。 创建这个单独资源组的目的是简化资源清理。

如果创建用于 AKS 群集的资源（例如存储帐户或保留的公用 IP 地址），请将它们放在自动生成的资源组中。

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>我可以修改 MC_* 资源组中 AKS 资源的标记和其他属性吗？

修改和删除 MC_* 资源组中资源的标记和其他属性可能会导致意外结果，例如缩放和升级错误。 修改 AKS 群集中 MC_* 下的资源会中断 SLO。

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS 支持哪些 Kubernetes 许可控制器？ 是否可以添加或删除许可控制器？

AKS 支持以下[许可控制器][admission-controllers]：

- NamespaceLifecycle
- LimitRanger
- ServiceAccount
- DefaultStorageClass
- DefaultTolerationSeconds
- MutatingAdmissionWebhook
- ValidatingAdmissionWebhook
- ResourceQuota
- DenyEscalatingExec
- AlwaysPullImages

目前无法修改 AKS 中的许可控制器列表。

## <a name="is-azure-key-vault-integrated-with-aks"></a>不是，它没有与 Azure Key Vault 集成。

AKS 目前尚未与 Azure Key Vault 本机集成。 但是，[Kubernetes 项目的 Azure Key Vault FlexVolume][keyvault-flexvolume] 实现了从 Kubernetes pod 到 KeyVault 机密的直接集成。

## <a name="can-i-run-windows-server-containers-on-aks"></a>是否可以在 AKS 上运行 Windows Server 容器？

若要运行 Windows Server 容器，需要运行基于 Windows Server 的节点。 基于 Windows Server 的节点目前在 AKS 中不可用。 但是，可以使用虚拟 Kubelet 在 Azure 容器实例上计划 Windows 容器，并将其作为 AKS 群集的一部分进行管理。 有关详细信息，请参阅[带有 AKS 的虚拟 Kubelet][virtual-kubelet]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服务级别协议？

在服务级别协议 (SLA) 中，如果未满足已发布的服务级别，提供商同意向客户偿还服务费用。 由于 AKS 本身是免费的，没有费用需要偿还，因此也就没有正式的 SLA。 不过，AKS 会设法将 Kubernetes API 服务器的可用性维持在不小于 99.5% 的水平上。

<!-- LINKS - internal -->

[aks-regions]: ./container-service-quotas.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./networking-overview.md#advanced-networking
[aks-rbac-aad]: ./aad-integration.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
