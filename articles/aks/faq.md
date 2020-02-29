---
title: 有关 Azure Kubernetes 服务 (AKS) 的常见问题解答
description: 查找有关 Azure Kubernetes 服务（AKS）的一些常见问题的解答。
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: 1531308a8d0bd5a09952d8ad8ccd03c92f2f99eb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914596"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 的常见问题解答

本文解答有关 Azure Kubernetes 服务 (AKS) 的常见问题。

## <a name="which-azure-regions-currently-provide-aks"></a>哪些 Azure 区域当前提供 AKS？

有关可用区域的完整列表，请参阅[AKS 区域和可用性][aks-regions]。

## <a name="does-aks-support-node-autoscaling"></a>AKS 是否支持节点自动缩放？

是的，当前在预览版中提供自动缩放 AKS 中的代理节点的功能。 有关说明，请参阅[自动缩放群集以满足 AKS 中的应用程序需求][aks-cluster-autoscaler]。 AKS 自动缩放基于[Kubernetes 自动缩放程序][auto-scaler]。

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>是否可以将 AKS 部署到现有虚拟网络？

是的，可以使用[高级网络功能][aks-advanced-networking]将 AKS 群集部署到现有虚拟网络。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>能否限制谁有权访问 Kubernetes API 服务器？

是的，你可以使用[API 服务器授权的 IP 范围][api-server-authorized-ip-ranges]限制对 Kubernetes API 服务器的访问。

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>能否在虚拟网络中仅访问 Kubernetes API 服务器？

目前不是，但这是计划的。 可以跟踪[AKS GitHub][private-clusters-github-issue]存储库的进度。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>单个群集中是否可以有不同的 VM 大小？

是的，可以通过创建[多个节点池][multi-node-pools]，在 AKS 群集中使用不同的虚拟机大小。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？

Azure 会按夜间计划自动将安全修补程序应用到群集中的 Linux 节点。 但是，你需要负责确保根据需要重新启动这些 Linux 节点。 可以使用多个选项来重新启动节点：

- 通过 Azure 门户或 Azure CLI 手动执行。
- 通过升级 AKS 群集。 群集会自动升级[cordon 和排出节点][cordon-drain]，然后使用最新的 Ubuntu 映像和新的修补程序版本或次要 Kubernetes 版本使新节点联机。 有关详细信息，请参阅[Upgrade a AKS cluster][aks-upgrade]。
- 通过使用[Kured](https://github.com/weaveworks/kured)，Kubernetes 的开源重新启动后台程序。 Kured 以[DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)的形式运行，并监视每个节点是否存在指示需要重新启动的文件。 在群集中，操作系统重启由群集升级时的相同[cordon 和排出过程][cordon-drain]进行管理。

有关使用 kured 的详细信息，请参阅[在 AKS 中将安全和内核更新应用于节点][node-updates-kured]。

### <a name="windows-server-nodes"></a>Windows Server 节点

对于 Windows Server 节点（当前在 AKS 中为预览版），Windows 更新不会自动运行和应用最新更新。 在围绕 Windows 更新发布周期和你自己的验证过程的定期计划中，你应在 AKS 群集中的群集和 Windows Server 节点池上执行升级。 此升级过程创建运行最新 Windows Server 映像和修补程序的节点，然后删除旧节点。 有关此过程的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？

AKS 基于多个 Azure 基础结构资源（包括虚拟机规模集、虚拟网络和托管磁盘）构建。 这使你能够利用 AKS 提供的托管 Kubernetes 环境中的许多 Azure 平台核心功能。 例如，大多数 Azure 虚拟机类型都可直接用于 AKS，而 Azure 保留可用于自动接收这些资源的折扣。

若要启用此体系结构，每个 AKS 部署都跨越两个资源组：

1. 创建第一个资源组。 此组仅包含 Kubernetes 服务资源。 在部署过程中，AKS 资源提供程序会自动创建第二个资源组。 *MC_myResourceGroup_myAKSCluster_eastus*第二个资源组的示例。 有关如何指定此第二个资源组的名称的信息，请参阅下一节。
1. 第二个资源组（称为*节点资源组*）包含与该群集关联的所有基础结构资源。 这些资源包括 Kubernetes 节点 VM、虚拟网络和存储。 默认情况下，节点资源组的名称如*MC_myResourceGroup_myAKSCluster_eastus*。 删除群集时，AKS 会自动删除节点资源，因此，它只应用于共享群集生命周期的资源。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>我是否可以为 AKS 节点资源组提供自己的名称？

是的。 默认情况下，AKS 会将节点资源组命名*MC_resourcegroupname_clustername_location*，但也可以提供自己的名称。

若要指定自己的资源组名称，请安装[aks-preview][aks-preview-cli] Azure CLI extension *0.3.2*或更高版本。 使用[az AKS create][az-aks-create]命令创建 AKS 群集时，请使用 *--node.js*参数并指定资源组的名称。 如果[使用 Azure 资源管理器模板][aks-rm-template]来部署 AKS 群集，则可以使用*nodeResourceGroup*属性来定义资源组名称。

* 辅助资源组是 Azure 资源提供程序在自己的订阅中自动创建的。
* 只能在创建群集时指定自定义资源组名称。

使用节点资源组时，请记住不能：

* 为节点资源组指定现有的资源组。
* 请为节点资源组指定其他订阅。
* 创建群集后更改节点资源组名称。
* 指定节点资源组中的托管资源的名称。
* 修改或删除节点资源组中的托管资源的标记。 （请参阅下一节中的其他信息。）

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>能否修改节点资源组中的 AKS 资源的标记和其他属性？

如果修改或删除了 Azure 创建的标记以及节点资源组中的其他资源属性，可能会出现意外的结果，如缩放和升级错误。 AKS 允许您创建和修改自定义标记。 例如，你可能想要创建或修改自定义标记，例如，分配业务部门或成本中心。 通过修改 AKS 群集中节点资源组下的资源，可以中断服务级别目标（SLO）。 有关详细信息，请参阅[AKS 是否提供服务级别协议？](#does-aks-offer-a-service-level-agreement)

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

目前，无法在 AKS 中修改许可控制器的列表。

## <a name="is-azure-key-vault-integrated-with-aks"></a>不是，它没有与 Azure Key Vault 集成。

AKS 当前未与 Azure Key Vault 本地集成。 不过， [Azure Key Vault FlexVolume For Kubernetes 项目][keyvault-flexvolume]允许从 Kubernetes pod 直接集成到 Key Vault 机密。

## <a name="can-i-run-windows-server-containers-on-aks"></a>是否可以在 AKS 上运行 Windows Server 容器？

是的，Windows Server 容器在预览版中可用。 若要在 AKS 中运行 Windows Server 容器，请创建一个运行 Windows Server 作为来宾 OS 的节点池。 Windows Server 容器只能使用 Windows Server 2019。 若要开始，请参阅[使用 Windows Server 节点池创建 AKS 群集][aks-windows-cli]。

Windows Server 对节点池的支持在 Kubernetes 项目中包含属于上游 Windows Server 的一些限制。 有关这些限制的详细信息，请参阅[Windows Server 容器中的 AKS 限制][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服务级别协议？

在服务级别协议（SLA）中，如果未满足发布的服务级别，则该提供程序同意向客户支付服务费用。 由于 AKS 是免费的，因此无需支付任何费用，因此 AKS 没有正式的 SLA。 但是，AKS 设法维持 Kubernetes API 服务器至少99.5% 的可用性。

务必认识到 Kubernetes 控制面的运行时间和在 Azure 虚拟机上运行的特定工作负荷的可用性，这是 AKS 服务可用性的区别。 尽管控制平面在控制平面未就绪时可能不可用，但在 Azure Vm 上运行的群集工作负荷仍可正常工作。 给定的 Azure Vm 是由财务 SLA 提供支持的已支付资源。 请在此处阅读有关 Azure VM SLA 的[更多详细信息](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)，以及如何通过[可用性区域][availability-zones]等功能提高该可用性。

## <a name="why-cant-i-set-maxpods-below-30"></a>为什么不能将 maxPods 设置为低于30？

在 AKS 中，可以在创建群集时使用 Azure CLI 和 Azure 资源管理器模板来设置 `maxPods` 值。 但是，Kubenet 和 Azure CNI 都需要一个*最小值*（在创建时验证）：

| 网络 | 最小值 | 最大值 |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

因为 AKS 是托管服务，所以我们将加载项和 pod 作为群集的一部分进行部署和管理。 过去，用户可以定义低于托管 pod 运行所需值的 `maxPods` 值（例如，30）。 AKS 现在使用以下公式计算箱的最小数目：（（maxPods 或（maxPods * vm_count）） > 托管加载项最小值。

用户不能重写最低 `maxPods` 验证。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>是否可将 Azure 预订折扣应用于 AKS 代理节点？

AKS 代理节点按标准 Azure 虚拟机计费，因此，如果你已为在 AKS 中使用的 VM 大小购买[Azure 保留][reservation-discounts]，则会自动应用这些折扣。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>能否在 Azure 租户之间移动/迁移群集？

`az aks update-credentials` 命令可用于在 Azure 租户之间移动 AKS 群集。 按照[选择更新或创建服务主体](https://docs.microsoft.com/azure/aks/update-credentials)中的说明进行操作，然后[使用新凭据更新 aks 群集](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials)。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>是否可以在订阅之间移动/迁移群集？

当前不支持在订阅之间移动群集。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>是否可以将我的 AKS 群集从当前的 azure 订阅移到另一个？ 

不支持在 Azure 订阅之间移动 AKS 群集及其关联的资源。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>为什么群集删除花费很长时间呢？ 

大多数群集会在用户请求时删除;在某些情况下，特别是在客户引入自己的资源组或执行跨 RG 任务删除时，可能需要额外的时间或失败。 如果删除有问题，请仔细检查是否在 RG 上没有锁，RG 之外的任何资源是否与 RG 关联，等等。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>如果我的 pod/部署状态为 "NodeLost" 或 "未知"，是否仍可升级我的群集？

您可以，但 AKS 不推荐这样做。 理想情况下，应在群集状态为已知且正常时执行升级。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>如果群集中的一个或多个节点处于不正常状态或已关闭，是否可以执行升级？

不，请删除/删除处于失败状态的任何节点，或在升级之前从群集中删除。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>我运行了群集删除，但查看错误 `[Errno 11001] getaddrinfo failed` 

最常见的原因是，具有一个或多个网络安全组（Nsg）仍在使用并与群集关联的用户。  请删除它们，然后重试删除。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>我运行了升级，但现在我的 pod 处于崩溃循环，并且准备情况探测失败了？

请确认你的服务主体未过期。  请参阅： [AKS 服务主体](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS 更新凭据](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>我的群集运行正常，但突然无法预配 LoadBalancers、装载 Pvc 等。 

请确认你的服务主体未过期。  请参阅： [AKS 服务主体](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS 更新凭据](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>能否使用虚拟机规模集 Api 手动进行缩放？

不支持，不支持使用虚拟机规模集 Api 缩放操作。 使用 AKS Api （`az aks scale`）。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>能否使用虚拟机规模集手动缩放到0个节点？

不支持，不支持使用虚拟机规模集 Api 缩放操作。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>是否可以停止或取消分配所有 Vm？

尽管 AKS 具有可经受此类配置并从中恢复的复原机制，但这不是推荐的配置。

## <a name="can-i-use-custom-vm-extensions"></a>能否使用自定义 VM 扩展？

没有 AKS 是托管服务，不支持操作 IaaS 资源。 安装自定义组件等。 请使用 Kubernetes Api 和机制。 例如，利用 Daemonset 安装所需的组件。

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

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
