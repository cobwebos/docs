---
title: 有关 Azure Kubernetes 服务 (AKS) 的常见问题解答
description: 提供有关 Azure Kubernetes 服务 (AKS) 的某些常见问题的解答。
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: f365fcd61944fbae131ab79a1c3660aaf02fa8d7
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073929"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 的常见问题解答

本文解答有关 Azure Kubernetes 服务 (AKS) 的常见问题。

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>哪些 Azure 区域现在可提供 Azure Kubernetes 服务 (AKS)？

有关可用区域的完整列表，请参阅 [AKS 区域和可用性][aks-regions]。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支持节点自动缩放？

支持，从 Kubernetes 1.10 开始，可以通过 [Kubernetes autoscaler][auto-scaler] 进行自动缩放。 有关如何手动配置和使用群集自动缩放程序的详细信息，请参阅[AKS 群集自动缩放][aks-cluster-autoscale]。

您还可用于内置群集自动缩放程序 （目前以在 AKS 中的预览版） 管理的节点的缩放。 有关详细信息，请参阅[自动缩放以满足在 AKS 中的应用程序需求的群集][aks-cluster-autoscaler]。

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>AKS 是否支持 Kubernetes 基于角色的访问控制 (RBAC)？

支持，使用 Azure CLI 创建群集时，默认启用 Kubernetes RBAC。 可为使用 Azure 门户或模板创建的集群启用 RBAC。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>是否可以将 AKS 部署到现有虚拟网络？

可以，可使用[高级网络功能][aks-advanced-networking]将 AKS 群集部署到现有虚拟网络中。

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>我可以将 Kubernetes API 服务器限制为仅能在我的虚拟网络中访问吗？

现在不行。 Kubernetes API 服务器公开为公共完全限定的域名 (FQDN)。 可使用 [Kubernetes 基于角色的访问控制 (RBAC) 和 Azure Active Directory (AAD)][aks-rbac-aad] 控制对群集的访问

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？

Azure 会自动适用于按夜间计划在群集中的 Linux 节点的安全修补程序。 但是，您有责任确保重新启动节点作为这些 Linux 所需。 执行节点重启具有多种选择：

- 通过 Azure 门户或 Azure CLI 手动执行。
- 通过升级 AKS 群集。 群集自动升级 [cordon 和 drain 节点][cordon-drain]，然后使用最新的 Ubuntu 映像和新补丁版本或次要 Kubernetes 版本重新启动每个节点。 有关详细信息，请参阅[升级 AKS 群集][aks-upgrade]。
- 使用 [Kured](https://github.com/weaveworks/kured)，一种适用于 Kubernetes 的开放源代码重启守护程序。 Kured 作为 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 运行并监视每个节点，用于确定指示需要重启的文件是否存在。 通过将相同的 [cordon 和 drain 进程][cordon-drain]用作群集升级跨群集管理操作系统的重启。

有关使用 kured 的详细信息，请参阅[将安全性和内核更新应用于 AKS 中的节点][node-updates-kured]。

### <a name="windows-server-nodes"></a>Windows 服务器节点

Windows Server 中的节点 （当前在 AKS 中的预览版），Windows 更新不会不会自动运行，并应用最新的更新。 有关在 Windows 更新发布周期和验证过程按定期计划，应在 AKS 群集中执行 Windows Server 节点池上的升级。 此升级过程创建节点运行的最新的 Windows Server 映像和修补程序，然后删除旧节点。 此过程的详细信息，请参阅[升级在 AKS 中的节点池][nodepool-upgrade]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？

每个 AKS 部署都跨越两个资源组：

- 第一个资源组由你创建，仅包含 Kubernetes 服务资源。 AKS 资源提供程序在部署期间自动创建第二个资源组，例如 MC_myResourceGroup_myAKSCluster_eastus。 有关如何指定此第二个资源组的名称的信息，请参阅下一节。
- 第二个资源组（例如 MC_myResourceGroup_myAKSCluster_eastus）包含与该群集关联的所有基础结构资源。 这些资源包括 Kubernetes 节点 VM、虚拟网络和存储。 创建这个单独资源组的目的是简化资源清理。

如果创建用于 AKS 群集的资源（例如存储帐户或保留的公用 IP 地址），请将它们放在自动生成的资源组中。

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>可以提供我自己 AKS 基础结构资源组的名称？

可以。 默认情况下，AKS 资源提供程序会自动创建的辅助资源组在部署期间，如*于 MC_myResourceGroup_myAKSCluster_eastus*。 若要符合公司策略，您可以自己为此托管群集的名称 (*MC_*) 的资源组。

若要指定资源组名称，请安装[aks 预览版][ aks-preview-cli] Azure CLI 扩展版本*0.3.2*或更高版本。 创建 AKS 群集使用时[az aks 创建][ az-aks-create]命令，使用 *-节点资源组*参数并指定资源组的名称。 如果您[使用 Azure 资源管理器模板][ aks-rm-template]若要部署 AKS 群集，可以定义资源组名称使用*nodeResourceGroup*属性。

* 在自己的订阅中的 Azure 资源提供程序会自动创建此资源组。
* 创建群集时，仅可以指定自定义资源组名称。

不支持以下方案：

* 不能指定现有的资源组*MC_* 组。
* 不能指定不同的订阅*MC_* 资源组。
* 不能更改*MC_* 后创建群集资源组名称。
* 不能指定为中的托管资源的名称*MC_* 资源组。
* 不能修改或删除中的托管资源标记*MC_* 资源组 （请参阅下一节中的其他信息）。

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>我可以修改 MC_* 资源组中 AKS 资源的标记和其他属性吗？

修改和删除 MC_* 资源组中 Azure 创建的标记以及资源的其他属性可能会导致意外结果，例如扩大和升级错误。 支持创建和修改其他自定义标记，例如分配业务单位或成本中心。 修改 AKS 群集中 MC_* 下的资源会中断服务级别目标 (SLO)。 有关详细信息，请参阅 [AKS 是否提供服务级别协议？](#does-aks-offer-a-service-level-agreement)

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

是的 Windows Server 容器是在预览版中可用。 若要在 AKS 中运行 Windows Server 容器，你创建运行 Windows Server 作为来宾 OS 的节点池。 Windows Server 容器只能使用 Windows Server 2019。 若要开始，[与 Windows Server 节点池创建 AKS 群集][aks-windows-cli]。

窗口服务器节点池支持包括属于上游 Kubernetes 项目中的 Windows 服务器的一些限制。 有关这些限制的详细信息，请参阅[AKS 限制中的 Windows Server 容器][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服务级别协议？

在服务级别协议 (SLA) 中，如果未满足已发布的服务级别，提供商同意向客户偿还服务费用。 由于 AKS 本身是免费的，没有费用需要偿还，因此也就没有正式的 SLA。 不过，AKS 会设法将 Kubernetes API 服务器的可用性维持在不小于 99.5% 的水平上。

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
