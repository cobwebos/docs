---
title: 有关 Azure Kubernetes 服务 (AKS) 的常见问题解答
description: 查找有关 Azure Kubernetes 服务 (AKS) 的某些常见问题的解答。
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 0e3a29e6e8f21658f03fb7fc059b54aa167496d4
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147177"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 的常见问题解答

本文解答有关 Azure Kubernetes 服务 (AKS) 的常见问题。

## <a name="which-azure-regions-currently-provide-aks"></a>哪些 Azure 区域目前提供 AKS？

有关可用区域的完整列表，请参阅 [AKS 区域和可用性][aks-regions]。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支持节点自动缩放？

是的, 当前在预览版中提供自动缩放 AKS 中的代理节点的功能。 有关说明, 请参阅[自动缩放群集以满足 AKS 中的应用程序需求][aks-cluster-autoscaler]。 AKS 自动缩放基于[Kubernetes 自动缩放程序][auto-scaler]。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>是否可以将 AKS 部署到现有虚拟网络？

可以，可使用[高级网络功能][aks-advanced-networking]将 AKS 群集部署到现有虚拟网络中。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>能否限制谁有权访问 Kubernetes API 服务器？

是的, 你可以使用[API 服务器授权的 IP 范围][api-server-authorized-ip-ranges](当前为预览版) 限制对 Kubernetes API 服务器的访问。

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>是否可以限制为只能在我的虚拟网络中访问 Kubernetes API 服务器？

目前不可以，但此功能正在规划中。 可以在 [AKS GitHub 存储库][private-clusters-github-issue]中跟踪此项计划的进度。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>单个群集中是否可以有不同的 VM 大小？

是的, 可以通过创建[多个节点池][multi-node-pools](当前为预览版), 在 AKS 群集中使用不同的虚拟机大小。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？

Azure 会按照夜间计划自动将安全修补程序应用于群集中的 Linux 节点。 但是，你有责任确保这些 Linux 节点根据需要进行重新启动。 可以使用多个选项来重新启动节点：

- 通过 Azure 门户或 Azure CLI 手动执行。
- 通过升级 AKS 群集。 群集自动升级 [cordon 和 drain 节点][cordon-drain]，然后使用最新的 Ubuntu 映像和新修补程序版本或 Kubernetes 次要版本将新节点联机。 有关详细信息，请参阅[升级 AKS 群集][aks-upgrade]。
- 使用 [Kured](https://github.com/weaveworks/kured)：适用于 Kubernetes 的开源重新启动守护程序。 Kured 作为 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 运行并监视每个节点，用于确定指示需要重新启动的文件是否存在。 通过将相同的[封锁和排空进程][cordon-drain]用作群集升级跨群集管理 OS 重新启动。

有关使用 kured 的详细信息，请参阅[将安全性和内核更新应用于 AKS 中的节点][node-updates-kured]。

### <a name="windows-server-nodes"></a>Windows Server 节点

对于 Windows Server 节点 (当前在 AKS 中为预览版), Windows 更新不会自动运行和应用最新更新。 在围绕 Windows 更新发布周期和你自己的验证过程的定期计划中, 你应在 AKS 群集中的群集和 Windows Server 节点池上执行升级。 此升级过程创建运行最新 Windows Server 映像和修补程序的节点, 然后删除旧节点。 有关此过程的详细信息, 请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？

每个 AKS 部署都跨越两个资源组：

1. 创建第一个资源组。 此组仅包含 Kubernetes 服务资源。 在部署过程中，AKS 资源提供程序会自动创建第二个资源组。 第二个资源组的一个示例是*MC_myResourceGroup_myAKSCluster_eastus*。 有关如何指定这第二个资源组的名称，请参阅下一部分。
1. 第二个资源组（称为节点资源组）包含与该群集相关联的所有基础结构资源。 这些资源包括 Kubernetes 节点 VM、虚拟网络和存储。 默认情况下, 节点资源组具有类似于*MC_myResourceGroup_myAKSCluster_eastus*的名称。 每当删除群集，AKS 就会自动删除节点资源，因此，请只对生命周期与群集相同的资源使用 AKS。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>我是否可为 AKS 节点资源组提供自己的名称？

是的。 默认情况下，AKS 将节点资源组命名为 *MC_clustername_resourcegroupname_location*，但你可以提供自己的名称。

若要指定自己的资源组名称，请安装 [aks-preview][aks-preview-cli] Azure CLI 扩展版本 *0.3.2* 或更高版本。 使用 [az aks create][az-aks-create] 命令创建 AKS 群集时，请使用 *--node-resource-group* 参数并指定资源组的名称。 如果[使用 Azure 资源管理器模板][aks-rm-template]来部署 AKS 群集, 则可以使用*nodeResourceGroup*属性来定义资源组名称。

* Azure 资源提供程序会在你自己的订阅中自动创建辅助资源组。
* 只能在创建群集时指定自定义资源组名称。

使用节点资源组时，请记住，不能：

* 指定现有的资源组作为节点资源组。
* 为节点资源组指定不同的订阅。
* 创建群集后更改节点资源组名称。
* 为节点资源组中的托管资源指定名称。
* 修改或删除节点资源组中托管资源的标记。 （请参阅下一部分的附加信息。）

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>是否可以修改节点资源组中 AKS 资源的标记和其他属性？

如果修改或删除节点资源组中 Azure 创建的标记和其他资源属性，可能会出现意外的结果，例如缩放和升级错误。 AKS 允许创建和修改自定义标记。 例如，可以创建或修改标记，以分配业务单位或成本中心。 修改 AKS 群集中节点资源组下的资源会影响服务级别目标 (SLO)。 有关详细信息，请参阅 [AKS 是否提供服务级别协议？](#does-aks-offer-a-service-level-agreement)

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

目前无法在 AKS 中修改许可控制器列表。

## <a name="is-azure-key-vault-integrated-with-aks"></a>不是，它没有与 Azure Key Vault 集成。

AKS 目前尚未与 Azure Key Vault 本机集成。 但是，[Kubernetes 项目的 Azure Key Vault FlexVolume][keyvault-flexvolume] 实现了从 Kubernetes pod 到 Key Vault 机密的直接集成。

## <a name="can-i-run-windows-server-containers-on-aks"></a>是否可以在 AKS 上运行 Windows Server 容器？

是的, Windows Server 容器在预览版中可用。 若要在 AKS 中运行 Windows Server 容器, 请创建一个运行 Windows Server 作为来宾 OS 的节点池。 Windows Server 容器只能使用 Windows Server 2019。 若要开始, 请参阅[使用 Windows Server 节点池创建 AKS 群集][aks-windows-cli]。

对于节点池, windows Server 支持包括 Kubernetes 项目中的上游 Windows Server 的一些限制。 有关这些限制的详细信息, 请参阅[Windows Server 容器中的 AKS 限制][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服务级别协议？

在服务级别协议 (SLA) 中，如果未满足已发布的服务级别，提供商同意向客户偿还服务费用。 由于 AKS 是免费的，不存在偿还费用，因此也就没有正式的 SLA。 不过，AKS 会设法将 Kubernetes API 服务器的可用性维持在不小于 99.5% 的水平上。

## <a name="why-cant-i-set-maxpods-below-30"></a>为何无法将 maxPods 设置为 30 以下？

在 AKS 中，使用 Azure CLI 和 Azure 资源管理器模板创建群集时可以设置 `maxPods` 值。 但是，Kubenet 和 Azure CNI 都需要一个最小值（创建时会验证该值）：

| 网络 | 最低要求 | 最大值 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

由于 AKS 是托管服务，我们会将加载项和 pod 部署为群集的一部分并对其进行管理。 过去，用户定义的 `maxPods` 值可以小于运行托管 pod 所需的值（例如 30）。 AKS 现在使用以下公式计算最小 pod 数：((maxPods or (maxPods * vm_count)) > 托管的加载项 pod 最小数目。

用户无法覆盖最小 `maxPods` 验证值。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>是否可将 Azure 预订折扣应用于 AKS 代理节点？

AKS 代理节点按标准 Azure 虚拟机计费, 因此, 如果你已为在 AKS 中使用的 VM 大小购买[Azure 保留][reservation-discounts], 则会自动应用这些折扣。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>能否在 Azure 租户之间移动/迁移群集？

`az aks update-credentials`命令可用于在 Azure 租户之间移动 AKS 群集。 按照[选择更新或创建服务主体](https://docs.microsoft.com/azure/aks/update-credentials)中的说明进行操作, 然后[使用新凭据更新 aks 群集](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials)。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>是否可以在订阅之间移动/迁移群集？

当前不支持在订阅之间移动群集。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>是否可以将我的 AKS 群集从当前的 azure 订阅移到另一个？ 

不支持在 Azure 订阅之间移动 AKS 群集及其关联的资源。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>为什么群集删除花费很长时间呢？ 

大多数群集会在用户请求时删除;在某些情况下, 特别是在客户引入自己的资源组或执行跨 RG 任务删除时, 可能需要额外的时间或失败。 如果删除有问题, 请仔细检查是否在 RG 上没有锁, RG 之外的任何资源是否与 RG 关联, 等等。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>如果我的 pod/部署状态为 "NodeLost" 或 "未知", 是否仍可升级我的群集？

您可以, 但 AKS 不推荐这样做。 理想情况下, 应在群集状态为已知且正常时执行升级。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>如果群集中的一个或多个节点处于不正常状态或已关闭, 是否可以执行升级？

不, 请删除/删除处于失败状态的任何节点, 或在升级之前从群集中删除。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>我运行了群集删除, 但出现了错误`[Errno 11001] getaddrinfo failed` 

最常见的原因是, 具有一个或多个网络安全组 (Nsg) 仍在使用并与群集关联的用户。  请删除它们, 然后重试删除。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>我运行了升级, 但现在我的 pod 处于崩溃循环, 并且准备情况探测失败了？

请确认你的服务主体未过期。  请参阅：[AKS 服务主体](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS 更新凭据](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>我的群集运行正常, 但突然无法预配 LoadBalancers、装载 Pvc 等。 

请确认你的服务主体未过期。  请参阅：[AKS 服务主体](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS 更新凭据](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>能否使用虚拟机规模集 Api 手动进行缩放？

不支持, 不支持使用虚拟机规模集 Api 缩放操作。 使用 AKS Api (`az aks scale`)。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>能否使用虚拟机规模集手动缩放到0个节点？

不支持, 不支持使用虚拟机规模集 Api 缩放操作。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>是否可以停止或取消分配所有 Vm？

尽管 AKS 具有可经受此类配置并从中恢复的复原机制, 但这不是推荐的配置。

## <a name="can-i-use-custom-vm-extensions"></a>能否使用自定义 VM 扩展？

没有 AKS 是托管服务, 不支持操作 IaaS 资源。 安装自定义组件等。 请使用 kubernetes Api 和机制。 例如, 利用 Daemonset 安装所需的组件。

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
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
