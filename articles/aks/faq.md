---
title: 有关 Azure Kubernetes 服务 (AKS) 的常见问题解答
description: 获取有关 Azure Kubernetes 服务 (AKS) 的某些常见问题的解答。
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: 767b5b80aab7d98af92f86bf66cc2ff83242ff92
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83677784"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 的常见问题解答

本文解答有关 Azure Kubernetes 服务 (AKS) 的常见问题。

## <a name="which-azure-regions-currently-provide-aks"></a>当前哪些 Azure 区域提供 AKS？

有关可用区域的完整列表，请参阅 [AKS 区域和可用性][aks-regions]。

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>AKS 群集是否可以跨区域分布？

不可以。 AKS 群集为区域性资源，不可跨区域分布。 如需了解如何创建包含多个区域的体系结构，请参阅[业务连续性和灾难恢复最佳做法][bcdr-bestpractices]。

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>AKS 群集是否可以跨可用性区域？

可以。 在[支持可用性区域的区域][availability-zones]中，可以在一个或跨多个[可用性区域][az-regions]部署 AKS 群集。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>是否可以控制谁有权限访问 Kubernetes API 服务器？

可以。 可以通过以下两种方式限制对 API 服务器的访问：

- 如果希望保留 API 服务器的公共终结点但仅限对一组受信任的 IP 范围的访问，请使用 [API 服务器授权的 IP 范围][api-server-authorized-ip-ranges]。
- 如要仅允许从你的虚拟网络内访问 API 服务器，请使用[专用群集][private-clusters]。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>单个群集中 VM 的大小是否可以不同？

可以。通过创建多个[节点池][multi-node-pools]，可以在单个 AKS 群集中使用不同大小的虚拟机。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？

Azure 会在夜间自动将安全更新程序应用到群集中的 Linux 节点。 但是，你需要确保 Linux 节点按照要求重新启动。 可以使用以下几种方法重新启动节点：

- 通过 Azure 门户或 Azure CLI 手动执行。
- 通过升级 AKS 群集。 AKS 群集自动升级 [cordon 和 drain节点][cordon-drain]，然后使用最新的 Ubuntu 映像和新补丁版本或次要 Kubernetes 版本让一个新节点联机。 有关详细信息，请参阅[升级 AKS 群集][aks-upgrade]。
- 使用 [Kured](https://github.com/weaveworks/kured)，一种适用于 Kubernetes 的开放源代码重启守护程序。 Kured 作为 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 运行并监视每个节点，用于确定指示需要重启的文件是否存在。 在整个群集中，OS 重启由升级群集时所用的同一 [cordon 和 drain 进程][cordon-drain]所管理。

有关使用 Kured 的详细信息，请参阅[将安全性和内核更新应用于 AKS 中的节点][node-updates-kured]。

### <a name="windows-server-nodes"></a>Windows Server 节点

对于 Windows Server 节点，Windows 更新不会自动运行和应用最新的更新。 在 Windows 更新的发布周期和你自己的验证过程中，你需要定期升级 AKS 群集以及群集中的 Windows Server 节点池。 此升级过程会创建运行最新 Windows Server 映像和修补程序的节点，然后删除旧节点。 有关此过程的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？

AKS 基于多个 Azure 基础结构资源构建（包括虚拟机规模集、虚拟网络和托管磁盘）。 因此，你可以在 AKS 提供的托管 Kubernetes 环境内利用 Azure 平台的众多核心功能。 例如，大多数 Azure 虚拟机类型都可直接用于 AKS，而 Azure 预留可用于自动接收这些资源的折扣。

要启用此体系结构，每个 AKS 部署都需跨越两个资源组：

1. 创建第一个资源组。 该资源组仅包含 Kubernetes 服务资源。 在部署期间，AKS 资源提供程序自动创建第二个资源组， 例如 MC_myResourceGroup_myAKSCluster_eastus。 如需了解如何指定第二个资源组的名称，请参阅下一节。
1. 第二个资源组即节点资源组，包含与该群集相关联的所有基础结构资源。 这些资源包括 Kubernetes 节点 VM、虚拟网络和存储。 默认情况下，节点资源组的名称类似于 MC_myResourceGroup_myAKSCluster_eastus。 删除群集时，AKS 会自动删除节点资源，因此，应仅将节点资源组用于生命周期与群集相同的资源。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>是否可以自定义 AKS 节点资源组的名称？

可以。 默认情况下，AKS 将节点资源组命名为 MC_resourcegroupname_clustername_location，但你也可以提供一个自定义名称。

若要自行指定一个资源组名称，请安装 [aks-preview][aks-preview-cli] Azure CLI 扩展版本 0.3.2 或更高版本。 使用 [az aks create][az-aks-create] 命令创建 AKS 群集时，请使用 --node-resource-group 参数并为资源组指定一个名称。 如果[使用 Azure 资源管理器模板][aks-rm-template]部署 AKS 群集，可以使用 nodeResourceGroup 属性定义资源组名称。

* 第二个资源组由订阅中的 Azure 资源提供程序自动创建。
* 只能在创建群集时指定自定义资源组名称。

请注意，对于节点资源组，不能执行以下操作：

* 不能为节点资源组指定现有资源组。
* 不能为节点资源组指定不同的订阅。
* 不能在节点资源组创建完成后更改其名称。
* 不能为节点资源组内的受管理资源指定名称。
* 不能修改或删除节点资源组内的受管理资源的标记 （其他信息详见下一节）。

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>可以修改节点资源组中 AKS 资源的标记和其他属性吗？

修改或删除节点资源组中 Azure 创建的标记或其他资源属性可能会导致预期之外的结果，例如缩放和升级错误。 AKS 支持创建和修改自定义标记。 有时，你可能想要创建或修改自定义标记，例如，在指定业务部门或成本中心时。 修改 AKS 群集中节点资源组下的资源会中断服务级别目标 (SLO)。 有关详细信息，请参阅 [AKS 是否提供服务级别协议？](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>AKS 支持哪些 Kubernetes 许可控制器？ 是否可以添加或删除许可控制器？

AKS 支持以下[准入控制器][admission-controllers]：

- NamespaceLifecycle
- LimitRanger
- ServiceAccount
- DefaultStorageClass
- DefaultTolerationSeconds
- MutatingAdmissionWebhook
- ValidatingAdmissionWebhook
- ResourceQuota

目前无法修改 AKS 中的准入控制器列表。

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>是否可以将准入控制器 Webhook 用于 AKS？

可以将准入控制器 Webhook 用于 AKS。 建议排除带有 control-plane 标记的内部 AKS 命名空间， 例如，通过将以下内容添加到 Webhook 配置：

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>准入控制器 Webhook 是否会影响 kube 系统和内部 AKS 命名空间？

为保护系统稳定性和防止自定义准入控制器影响 kube 系统中的内部服务，命名空间 AKS 提供了可以自动排除 kube 系统和 AKS 内部命名空间的准入执行器。 此服务可确保自定义准入控制器不会影响 kube 系统中运行的服务。

如果在某个关键用例中，需要在 kube 系统上部署一些内容（不推荐），而你需要将其包含在你的自定义准入 Webhook 中，则可通过添加以下标签或注释，以便准入执行器忽略该内容。

标签：```"admissions.enforcer/disabled": "true"``` 或注释：```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>AKS 是否和 Azure Key Vault 相集成？

AKS 目前尚未与 Azure Key Vault 本机集成。 但是，[适用于 CSI 机密存储的 Azure Key Vault 提供程序][csi-driver]支持从 Kubernetes pod 到 Key Vault 机密的直接集成。

## <a name="can-i-run-windows-server-containers-on-aks"></a>是否可以在 AKS 上运行 Windows Server 容器？

是的，可以在 AKS 运行 Windows Server 容器。 若要在 AKS 中运行 Windows Server 容器，需创建一个将 Windows Server 作为来宾 OS 运行的节点池。 Windows Server 容器只能使用 Windows Server 2019。 若要开始使用，请[创建包含单个节点池的 AKS 群集][aks-windows-cli]。

Windows Server 对节点池的支持具有一些限制，Kubernetes 项目中的上游 Windows Server 也具有这些限制。 有关这些限制的详细信息，请参阅[在 AKS 中使用 Windows Server 容器的一些限制][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服务级别协议？

AKS 通过[运行时间 SLA][uptime-sla] 提供 SLA 保障（可选的附加功能）。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>是否可将 Azure 预留折扣应用于 AKS 代理节点？

AKS 代理节点按标准 Azure 虚拟机计费，因此，如果你已为在 AKS 中使用的 VM 大小购买了 [Azure 预留][reservation-discounts]，这些折扣会自动应用。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>是否可以在 Azure 租户之间移动/迁移群集？

可以使用 `az aks update-credentials` 命令在 Azure 租户之间移动 AKS 群集。 按照[选择更新或创建服务主体](https://docs.microsoft.com/azure/aks/update-credentials)中的说明进行操作，然后[使用新凭据](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials)更新 AKS 群集。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>是否可以跨订阅移动/迁移群集？

目前不支持跨订阅移动群集。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>是否可以将 AKS 群集从当前 Azure 订阅移动到另一个订阅？ 

不支持跨 Azure 订阅移动 AKS 群集及其关联的资源。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>为什么删除群集需要如此长的时间？ 

大多数群集在用户发出请求后即会被删除。在某些情况下，尤其是客户使用自己的资源组或执行跨资源组任务时，删除群集可能会花费额外的时间或失败。 如果删除群集时出现问题，请仔细检查，确保该资源组没有被锁，资源组外的所有资源均已与该资源组解除关联。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>如果我的 pod 或部署状态为“NodeLost”或“Unknown”，是否仍然可以升级群集？

可以，但 AKS 不建议这样做。 最好应在群集状态已知或正常的情况下进行升级。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>如果我的群集有一个或多个节点处于运行不正常或关闭状态，是否可以升级？

不可以，在升级前，请删除/移除处于失败状态的所有节点或将其从群集移除。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>删除群集时，为何会出现 `[Errno 11001] getaddrinfo failed` 错误？ 

导致此错误的最常见原因是一个或多个网络安全组 (NSG) 仍在使用中且与该群集相关联。  请先删除网络安全组，再重新尝试删除。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>升级后，我的 pod 总是崩溃，而且就绪情况探测失败，我该如何解决？

请确认服务主体是否已过期。  请参阅以下内容：[AKS 服务主体](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和 [AKS 更新凭据](https://docs.microsoft.com/azure/aks/update-credentials)

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>我的群集之前运行正常，但现在突然无法预配 LoadBalancer、装载 PVC 等，该怎么解决？ 

请确认服务主体是否已过期。  请参阅以下内容：[AKS 服务主体](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和 [AKS 更新凭据](https://docs.microsoft.com/azure/aks/update-credentials)

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>是否可以使用虚拟机规模集 API 手动进行缩放？

不可以，不支持使用虚拟机规模集进行缩放操作。 请使用 AKS API (`az aks scale`)。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>是否可以使用虚拟机规模集手动缩减到 0 个节点？

不可以，不支持使用虚拟机规模集进行缩放操作。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>是否可以停止或解除分配所有的 VM？

尽管 AKS 的复原机制可以承受此类配置并实现恢复，但不建议采取此类配置操作。

## <a name="can-i-use-custom-vm-extensions"></a>是否可以使用自定义 VM 扩展？

不可以，AKS 是托管服务，不支持操作 IaaS 资源。 要安装自定义组件等， 请使用 Kubernetes API 和相应的机制。 例如，使用 DaemonSet 安装所需的组件。

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/