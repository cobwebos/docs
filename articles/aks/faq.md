---
title: 有关 Azure Kubernetes 服务 (AKS) 的常见问题解答
description: 查找有关 Azure Kubernetes 服务 (AKS) 的某些常见问题的解答。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 5ba776424462b3a8b586b1f90e83f409770e5597
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123813"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 的常见问题解答

本文解答有关 Azure Kubernetes 服务 (AKS) 的常见问题。

## <a name="which-azure-regions-currently-provide-aks"></a>哪些 Azure 区域目前提供 AKS？

有关可用区域的完整列表，请参阅[AKS 区域和可用性][aks-regions]。

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>能否跨区域分散 AKS 群集？

不是。 AKS 群集是区域资源，不能跨区域。 请参阅[业务连续性和灾难恢复的最佳实践][bcdr-bestpractices]，了解有关如何创建包含多个区域的体系结构的指南。

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>是否可以将 AKS 群集分散到不同的可用性区域？

是的。 可以在[支持其的区域][az-regions]中的一个或多个[可用性区域][availability-zones]中部署 AKS 群集。

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>能否限制谁有权访问 Kubernetes API 服务器？

是的。 有两个选项可用于限制对 API 服务器的访问：

- 如果要为 API 服务器维护公用终结点但限制对一组受信任的 IP 范围的访问，请使用[Api 服务器授权的 Ip 范围][api-server-authorized-ip-ranges]。
- 如果要将 API 服务器限制为*只能*从虚拟网络内部访问，请使用[专用群集][private-clusters]。

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>单个群集中是否可以有不同的 VM 大小？

是的，可以通过创建[多个节点池][multi-node-pools]，在 AKS 群集中使用不同的虚拟机大小。

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>安全更新是否可应用于 AKS 代理节点？

Azure 会按照夜间计划自动将安全修补程序应用于群集中的 Linux 节点。 但是，你有责任确保这些 Linux 节点根据需要进行重新启动。 可以使用多个选项来重新启动节点：

- 通过 Azure 门户或 Azure CLI 手动执行。
- 通过升级 AKS 群集。 群集自动升级 [cordon 和 drain 节点][cordon-drain]，然后使用最新的 Ubuntu 映像和新修补程序版本或 Kubernetes 次要版本将新节点联机。 有关详细信息，请参阅[升级 AKS 群集][aks-upgrade]。
- 使用 [Kured](https://github.com/weaveworks/kured)：适用于 Kubernetes 的开源重新启动守护程序。 Kured 作为 [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) 运行并监视每个节点，用于确定指示需要重新启动的文件是否存在。 通过将相同的[封锁和排空进程][cordon-drain]用作群集升级跨群集管理 OS 重新启动。

有关使用 kured 的详细信息，请参阅[将安全性和内核更新应用于 AKS 中的节点][node-updates-kured]。

### <a name="windows-server-nodes"></a>Windows Server 节点

对于 Windows Server 节点，Windows 更新不会自动运行和应用最新的更新。 在围绕 Windows 更新发布周期和你自己的验证过程的定期计划中，你应在 AKS 群集中的群集和 Windows Server 节点池上执行升级。 此升级过程创建运行最新 Windows Server 映像和修补程序的节点，然后删除旧节点。 有关此过程的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

## <a name="why-are-two-resource-groups-created-with-aks"></a>为什么使用 AKS 创建两个资源组？

AKS 基于多个 Azure 基础结构资源（包括虚拟机规模集、虚拟网络和托管磁盘）构建。 这使你能够在 AKS 提供的托管 Kubernetes 环境中利用 Azure 平台的许多核心功能。 例如，大多数 Azure 虚拟机类型都可直接用于 AKS，而 Azure 保留可用于自动接收这些资源的折扣。

为了启用此体系结构，每个 AKS 部署跨越两个资源组：

1. 创建第一个资源组。 此组仅包含 Kubernetes 服务资源。 在部署过程中，AKS 资源提供程序会自动创建第二个资源组。 *MC_myResourceGroup_myAKSCluster_eastus*第二个资源组的示例。 有关如何指定这第二个资源组的名称，请参阅下一部分。
1. 第二个资源组（称为节点资源组）包含与该群集相关联的所有基础结构资源。** 这些资源包括 Kubernetes 节点 VM、虚拟网络和存储。 默认情况下，节点资源组的名称如*MC_myResourceGroup_myAKSCluster_eastus*。 每当删除群集，AKS 就会自动删除节点资源，因此，请只对生命周期与群集相同的资源使用 AKS。

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>我是否可为 AKS 节点资源组提供自己的名称？

是的。 默认情况下，AKS 将节点资源组命名为 MC_resourcegroupname_clustername_location**，但你也可以提供自己的名称。

若要指定自己的资源组名称，请安装 [aks-preview][aks-preview-cli] Azure CLI 扩展版本 *0.3.2* 或更高版本。 使用 [az aks create][az-aks-create] 命令创建 AKS 群集时，请使用 *--node-resource-group* 参数并指定资源组的名称。 如果[使用 Azure 资源管理器模板][aks-rm-template]来部署 AKS 群集，则可以使用*nodeResourceGroup*属性来定义资源组名称。

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

- NamespaceLifecycle**
- LimitRanger**
- *ServiceAccount*
- DefaultStorageClass**
- DefaultTolerationSeconds**
- MutatingAdmissionWebhook**
- ValidatingAdmissionWebhook**
- ResourceQuota**

目前无法在 AKS 中修改许可控制器列表。

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>能否在 AKS 上使用许可控制器 webhook？

是的，可以在 AKS 上使用许可控制器 webhook。 建议你排除使用**控制平面标签**标记的内部 AKS 命名空间。 例如，通过将以下内容添加到 webhook 配置：

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>许可控制器 webhook 是否会影响 kube 系统和内部 AKS 命名空间？

为了保护系统的稳定性，并防止自定义的许可控制器影响 kube 系统中的内部服务，命名空间 AKS 有一个**招生 Enforcer**，它自动排除 kube 系统和 AKS 内部命名空间。 此服务确保自定义许可控制器不会影响在 kube 系统中运行的服务。

如果你有一个用于在 kube （不推荐）上部署某些内容的关键用例，而你需要将其包含在你的自定义许可 webhook 中，则可以添加以下标签或批注，以便招生 Enforcer 将其忽略。

标签： ```"admissions.enforcer/disabled": "true"``` 或批注：```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>不是，它没有与 Azure Key Vault 集成。

AKS 目前尚未与 Azure Key Vault 本机集成。 不过，[适用于 CSI 密钥存储的 Azure Key Vault 提供程序][csi-driver]允许从 Kubernetes pod 直接集成到 Key Vault 机密。

## <a name="can-i-run-windows-server-containers-on-aks"></a>是否可以在 AKS 上运行 Windows Server 容器？

是的，Windows Server 容器在 AKS 上可用。 若要在 AKS 中运行 Windows Server 容器，请创建一个运行 Windows Server 作为来宾 OS 的节点池。 Windows Server 容器只能使用 Windows Server 2019。 若要开始，请参阅[使用 Windows Server 节点池创建 AKS 群集][aks-windows-cli]。

Windows Server 对节点池的支持在 Kubernetes 项目中包含属于上游 Windows Server 的一些限制。 有关这些限制的详细信息，请参阅[Windows Server 容器中的 AKS 限制][aks-windows-limitations]。

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS 是否提供服务级别协议？

AKS 提供了通过 [运行时间 SLA] [正常运行时间-sla.md] 实现 API 服务器的99.95% 可用性的功能。

在服务级别协议 (SLA) 中，如果未满足已发布的服务级别，提供商同意向客户偿还服务费用。 由于 AKS 是免费的，不存在偿还费用，因此也就没有正式的 SLA。 不过，AKS 会设法将 Kubernetes API 服务器的可用性维持在不小于 99.5% 的水平上。

重要的是要认识到 AKS 服务可用性（指 Kubernetes 控制平面的正常运行时间）和在 Azure 虚拟机上运行的特定工作负荷的可用性之间的区别。 尽管控制平面在控制平面未就绪时可能不可用，但在 Azure VM 上运行的群集工作负荷仍可正常工作。 鉴于 Azure VM 是付费资源，它们由财务 SLA 提供支持。 请在此处阅读有关 Azure VM SLA 的[更多详细信息](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)，以及如何通过[可用性区域][availability-zones]等功能提高该可用性。

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>是否可将 Azure 预订折扣应用于 AKS 代理节点？

AKS 代理节点按标准 Azure 虚拟机计费，因此，如果你已为在 AKS 中使用的 VM 大小购买[Azure 保留][reservation-discounts]，则会自动应用这些折扣。

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>我可以在 Azure 租户之间移动/迁移群集吗？

可以使用 `az aks update-credentials` 命令在 Azure 租户之间移动 AKS 群集。 按[选择更新或创建服务主体](https://docs.microsoft.com/azure/aks/update-credentials)中的说明操作，然后[使用新凭据更新 AKS 群集](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials)。

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>我可以在订阅之间移动/迁移群集吗？

目前不支持在订阅之间移动群集。

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>是否可以将我的 AKS 群集从当前的 Azure 订阅移到另一个？ 

不支持在 Azure 订阅之间移动 AKS 群集及其关联的资源。

## <a name="why-is-my-cluster-delete-taking-so-long"></a>为何群集删除需要如此长的时间？ 

大多数群集是按用户请求删除的；某些情况下，尤其是在客户引入自己的资源组或执行跨 RG 任务的情况下，删除操作可能需要更多的时间，或者可能会失败。 如果在删除时出现问题，请仔细检查，确保没有在 RG 上进行锁定、RG 之外的任何资源均已取消与 RG 的关联，等等。

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>如果 Pod/部署处于“NodeLost”或“未知”状态，是否仍然可以升级群集？

可以，但是 AKS 不建议这样做。 理想情况下，升级应该在群集状态已知且正常的情况下完成。

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>如果我有一个群集的一个或多个节点处于“运行不正常”状态或关闭状态，是否可以进行升级？

否。请删除/移除任何处于故障状态的节点或因为其他原因从群集中移除的节点，然后再进行升级。

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>我运行了群集删除操作，但出现错误：`[Errno 11001] getaddrinfo failed` 

这种情况最可能的原因是用户有一个或多个网络安全组 (NSG) 仍在使用并与群集相关联。  请将网络安全组删除，然后再次尝试群集删除操作。

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>我运行了升级，但现在我的 Pod 处于崩溃循环中，且就绪情况探测失败。

请确认你的服务主体尚未过期。  请参阅： [AKS 服务主体](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS 更新凭据](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>我的群集运行正常，但突然无法预配 LoadBalancers、装载 Pvc 等。 

请确认你的服务主体尚未过期。  请参阅： [AKS 服务主体](https://docs.microsoft.com/azure/aks/kubernetes-service-principal)和[AKS 更新凭据](https://docs.microsoft.com/azure/aks/update-credentials)。

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>是否可以使用虚拟机规模集 API 手动进行缩放？

否。使用虚拟机规模集 API 进行的缩放操作不受支持。 请使用 AKS API (`az aks scale`)。

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>是否可以使用虚拟机规模集手动缩放到 0 个节点？

否。使用虚拟机规模集 API 进行的缩放操作不受支持。

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>是否可以停止或解除分配我的所有 VM？

虽然 AKS 的复原机制可以经受此类配置并从其恢复，但我们建议你不要这样进行配置。

## <a name="can-i-use-custom-vm-extensions"></a>是否可以使用自定义 VM 扩展？

否。AKS 是一项托管服务，不支持操作 IaaS 资源。 若要安装自定义组件等内容， 请使用 Kubernetes Api 和机制。 例如，利用 DaemonSets 安装所需组件。

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
[运行时间-sla]./uptime-sla.mdd

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
