---
title: 排查常见的 Azure Kubernetes 服务问题
description: 了解如何排查和解决在使用 Azure Kubernetes 服务 (AKS) 时遇到的常见问题
services: container-service
ms.topic: troubleshooting
ms.date: 06/20/2020
ms.openlocfilehash: 4a28ebd047e4d5e610ea0c895063eb87ce051d45
ms.sourcegitcommit: 4a7a4af09f881f38fcb4875d89881e4b808b369b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2020
ms.locfileid: "89460314"
---
# <a name="aks-troubleshooting"></a>AKS 疑难解答

在创建或管理 Azure Kubernetes 服务 (AKS) 群集时，可能偶尔会遇到问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>通常在何处查看 Kubernetes 问题调试的相关信息？

请尝试 [Kubernetes 群集故障排除的官方指南](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)。
还可尝试由 Microsoft 工程师发布的[故障排除指南](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)，用于对 Pod、节点、群集和其他功能进行故障排除。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>在创建或升级期间遇到“超出配额”的错误。 我该怎么办？ 

 [请求更多核心](../azure-portal/supportability/resource-manager-core-quotas-request.md)。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>对 AKS 而言，每个节点设置的最大 Pod 是多少？

如果在 Azure 门户中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 30。
如果在 Azure CLI 中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 110。 （确保使用最新版本的 Azure CLI）。 可以使用 `az aks create` 命令中的 `–-max-pods` 标记来更改此设置。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>在使用高级网络部署 AKS 群集时收到 insufficientSubnetSize 错误。 我该怎么办？

此错误表示用于群集的子网在其 CIDR 中不再具有用于成功分配资源的可用 IP。 对于 Kubenet 群集，需要为群集中的每个节点提供足够的 IP 空间。 对于 Azure CNI 群集，需要为群集中的每个节点和 Pod 提供足够的 IP 空间。
阅读并详细了解如何[设计为 Pod 分配 IP 的 Azure CNI](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)。

这些错误还会在 [AKS 诊断](./concepts-diagnostics.md) 中出现，这会主动地显示子网大小不足等问题。

以下三 (3) 种情况会导致子网大小不足的错误：

1. AKS 缩放或 AKS Nodepool 缩放
   1. 如果使用的是 Kubenet，当 `number of free IPs in the subnet` 小于 `number of new nodes requested` 时就会发生这种情况。
   1. 如果使用的是 Azure CNI，当 `number of free IPs in the subnet` 小于 `number of nodes requested times (*) the node pool's --max-pod value` 时就会发生这种情况。

1. AKS 升级或 AKS Nodepool 升级
   1. 如果使用的是 Kubenet，当 `number of free IPs in the subnet` 小于 `number of buffer nodes needed to upgrade` 时就会发生这种情况。
   1. 如果使用的是 Azure CNI，当 `number of free IPs in the subnet` 小于 `number of buffer nodes needed to upgrade times (*) the node pool's --max-pod value` 时就会发生这种情况。
   
   默认情况下，AKS 群集将最大冲击 (升级缓冲区) 一个 (1) 的值，但可以通过设置 [节点池的最大浪涌值](upgrade-cluster.md#customize-node-surge-upgrade-preview) 来自定义此升级行为，这将增加完成升级所需的可用 ip 的数目。

1. AKS 创建或 AKS Nodepool 添加
   1. 如果使用的是 Kubenet，当 `number of free IPs in the subnet` 小于 `number of nodes requested for the node pool` 时就会发生这种情况。
   1. 如果使用的是 Azure CNI，当 `number of free IPs in the subnet` 小于 `number of nodes requested times (*) the node pool's --max-pod value` 时就会发生这种情况。

通过创建新的子网，可以应用以下缓解措施。 由于无法更新现有子网的 CIDR 范围，因此需要获取创建新子网的权限才能应用缓解措施。

1. 使用足以实现操作目标的更大的 CIDR 范围来重建新子网：
   1. 创建具有所需的无重叠新范围的新子网。
   1. 在新子网上创建新的 nodepool。
   1. 从驻留在要替换的旧子网中的旧 nodepool 中清空 Pod。
   1. 删除旧的子网和旧的 nodepool。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 停滞在 CrashLoopBackOff 模式。 我该怎么办？

可能有多种原因导致 Pod 停滞在该模式。 可能通过以下方式查看：

* 使用 `kubectl describe pod <pod-name>` 查看 Pod 本身。
* 使用 `kubectl logs <pod-name>` 查看日志。

有关如何对 Pod 的问题进行故障排除的详细信息，请参阅[调试应用程序](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)。

## <a name="im-receiving-tcp-timeouts-when-using-kubectl-or-other-third-party-tools-connecting-to-the-api-server"></a>当我使用连接到 API 服务器的 `kubectl` 或其他第三方工具时，会收到 `TCP timeouts`
AKS 具有 HA 控制平面，可以根据内核数进行垂直缩放，以确保维持其服务级别目标 (SLO) 和服务级别协议 (SLA)。 如果遇到连接超时，请检查以下内容：

- **是所有 API 命令一致超时还是只有少数几个超时？** 如果只是少数几个超时，那么负责节点与控制平面之间通信的 `tunnelfront` Pod 或 `aks-link` Pod 可能未运行。 确保托管此 Pod 的节点没有过度利用或压力过大。 考虑将它们移动到自己的 [`system` 节点池](use-system-pools.md)。
- 是否已打开 [AKS 限制出口流量文档](limit-egress-traffic.md)中注明的所有所需端口、FQDN 和 IP？ 否则，多个命令调用可能会失败。
- 当前的 IP 是否在 [API IP 授权范围](api-server-authorized-ip-ranges.md)内？ 如果正在使用此功能，而 IP 不在范围内，会导致调用被阻止。 
- 是否有客户端或应用程序泄漏了对 API 服务器的调用？ 确保使用监视而不是频繁的 get 调用，以及第三方应用程序不会泄漏此类调用。 例如，每次在内部读取机密时，Istio mixer 中的一个 bug 会导致创建新的 API 服务器监视连接。 因为这种行为是定期发生的，所以监视连接会迅速累积，最终导致 API 服务器过载，且不管采用什么扩展模式都会如此。 https://github.com/istio/istio/issues/19481
- Helm 部署中是否有多个版本？ 这种情况会导致 tiller 在节点上使用过多的内存，同时导致大量的 `configmaps`，这会在 API 服务器上造成不必要的峰值。 请考虑在 `helm init` 配置 `--history-max`，并利用新的 Helm 3。 有关以下问题的详细信息： 
    - https://github.com/helm/helm/issues/4821
    - https://github.com/helm/helm/issues/3500
    - https://github.com/helm/helm/issues/4543
- [节点之间的内部流量是否被阻止？](#im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout)

## <a name="im-receiving-tcp-timeouts-such-as-dial-tcp-node_ip10250-io-timeout"></a>我收到 `TCP timeouts`，如 `dial tcp <Node_IP>:10250: i/o timeout`

这些超时可能与被阻止节点之间的内部流量有关。 验证此流量是否未被阻止，例如通过群集节点子网上的[网络安全组](concepts-security.md#azure-network-security-groups)来这样做。

## <a name="im-trying-to-enable-role-based-access-control-rbac-on-an-existing-cluster-how-can-i-do-that"></a>我想尝试在现有群集上启用基于角色的访问控制 (RBAC)。 该如何操作？

目前不支持在现有群集上启用基于角色的访问控制 (RBAC)，必须在创建新群集时对其进行设置。 在使用 CLI、门户或 `2020-03-01` 之后的 API 版本时，默认会启用 RBAC。

## <a name="i-created-a-cluster-with-rbac-enabled-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>我创建了启用了 RBAC 的群集，现在，我在 Kubernetes 仪表板上看到了很多警告。 仪表板以前在没有任何警告的情况下工作。 我该怎么办？

出现警告的原因是群集启用了 RBAC，并且现在默认限制对仪表板的访问。 一般来说，此方法比较棒，因为仪表板默认公开给群集的所有用户可能会导致安全威胁。 如果仍想要启用仪表板，请遵循此[博客文章](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)中的步骤进行操作。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>无法使用 Kubectl 日志获取日志或无法连接到 API 服务器。 我收到“来自服务器的错误：拨号后端时出错: 拨打 tcp...”。 我该怎么办？

确保端口 22、9000 和 1194 已打开，以便连接到 API 服务器。 使用 `kubectl get pods --namespace kube-system` 命令检查 `tunnelfront` 或 `aks-link` Pod 是否正在 kube-system 命名空间中运行。 如果没有，请强制删除 Pod，它会重启。

## <a name="im-getting-tls-client-offered-only-unsupported-versions-from-my-client-when-connecting-to-aks-api-what-should-i-do"></a>`"tls: client offered only unsupported versions"`连接到 AKS API 时，我将从客户端获得。   应采取何种操作？

AKS 中支持的最低 TLS 版本是 TLS 1.2。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我在尝试进行升级或缩放时收到 `"Changing property 'imageReference' is not allowed"` 错误。 如何修复此问题？

收到此错误的原因可能是，你修改了 AKS 群集内代理节点中的标记。 如果修改或删除 MC_* 资源组中资源的标记和其他属性，可能会导致意外结果。 更改 AKS 群集中 MC_ * 组下的资源会中断服务级别目标 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>收到错误消息，指示群集处于失败状态，在修复该错误之前，将无法执行升级或缩放操作

以下故障排除帮助参考自 https://aka.ms/aks-cluster-failed

当群集由于多种原因进入失败状态时，会发生此错误。 请遵循以下步骤解决群集故障状态，然后重试先前失败的操作：

1. 除非群集摆脱 `failed` 状态，否则 `upgrade` 和 `scale` 操作不会成功。 常见的根本问题和解决方法包括：
    * 使用**不足的计算 (CRP) 配额**进行缩放。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高计算配额](../azure-portal/supportability/resource-manager-core-quotas-request.md)，然后尝试扩展到超出初始配额限制。
    * 使用高级网络和**不足的子网（网络）资源**缩放群集。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高资源配额](../azure-resource-manager/templates/error-resource-quota.md#solution)，然后尝试扩展到超出初始配额限制。
2. 解决升级失败的根本原因后，群集应会进入成功状态。 确认成功状态后，请重试原始操作。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade"></a>在尝试升级或缩放时收到错误，指示群集正在升级或升级失败

以下故障排除帮助参考自 https://aka.ms/aks-pending-upgrade

 不能同时升级和缩放群集或节点池。 而只能先在目标资源上完成一个操作类型，然后再在同一资源上执行下一个请求。 因此，在进行或尝试进行活动升级或缩放操作时，操作会受限。 

为帮助诊断此问题，请运行 `az aks show -g myResourceGroup -n myAKSCluster -o table` 以检索群集的详细状态。 根据结果执行相应的操作：

* 如果群集正在升级，请等待操作完成。 如果群集升级成功，请再次重试先前失败的操作。
* 如果群集升级失败，请按前面部分所述的步骤操作。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>是否可以将我的群集移到不同的订阅，或将包含我的群集的订阅移动到新的租户？

如果已将 AKS 群集移动到其他订阅，或已将群集的订阅移动到新租户，群集会因缺少群集标识权限而无法正常工作。 由于存在此约束，因此，AKS 不支持跨订阅或租户移动群集。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>在尝试使用需要虚拟机规模集的功能时遇到错误

以下故障排除帮助参考自 aka.ms/aks-vmss-enablement

你可能会收到错误，指示 AKS 群集不在虚拟机规模集上，如以下示例中所示：

AgentPool `<agentpoolname>` 已将自动缩放设置为启用状态，但它不在虚拟机规模集上

群集自动缩放程序或多节点池等功能需要 `vm-set-type` 的规模集。

按照相应文档中的“准备工作”步骤操作，正确创建 AKS 群集：

* [使用群集自动缩放程序](cluster-autoscaler.md)
* [创建和使用多个节点池](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>对 AKS 资源和参数强制实施哪些命名限制？

*此故障排除帮助来自 aka.ms/aks-naming-rules*

Azure 平台和 AKS 都实施了命名限制。 如果资源名称或参数违反了这些限制之一，则会返回一个错误，要求你提供不同的输入。 将应用以下通用命名规则：

* 群集名称必须为 1-63 个字符。 仅允许使用字母、数字、短划线和下划线字符。 第一个和最后一个字符必须是字母或数字。
* AKS 节点/MC_ 资源组名称由资源组名称和资源名称组成。 自动生成的 `MC_resourceGroupName_resourceName_AzureRegion` 语法长度不能超过 80 个字符。 如果需要，请缩短资源组名称或 AKS 群集名称的长度。 也可以[自定义节点资源组名称](cluster-configuration.md#custom-resource-group-name)
* dnsPrefix 必须以字母数字值开头和结尾，并且必须介于 1-54 个字符之间。 有效字符包括字母数字值和连字符 (-)。 dnsPrefix 不能包含特殊字符，例如句点 (.)。
* AKS 节点池名称必须全部为小写形式，对于 Linux 节点池，长度为 1-11 个字符；对于 Windows 节点池，长度为 1-6 个字符。 名称必须以字母开头，并且仅允许使用字母和数字字符。
* admin-username（用于设置 Linux 节点的管理员用户名）必须以字母开头，只能包含字母、数字、连字符和下划线，其最大长度为 64 个字符。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>我在尝试创建、更新、缩放、删除或升级群集时收到错误，该操作不被允许，因为另一个操作正在进行。

以下故障排除帮助参考自 aka.ms/aks-pending-operation

当上一个操作仍在进行时，群集操作会受到限制。 若要检索群集的详细状态，请使用 `az aks show -g myResourceGroup -n myAKSCluster -o table` 命令。 根据需要使用自己的资源组和 AKS 群集名称。

根据群集状态输出执行相应操作：

* 如果群集处于除“成功”或“失败”状态以外的任何预配状态，请等待操作（升级/更新/创建/缩放/删除/迁移）完成。 在上一个操作完成后，重试最新的群集操作。

* 如果群集升级失败，请按照[收到错误消息，指示群集处于失败状态，在修复该错误之前，将无法执行升级或缩放操作](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)中所述的步骤进行操作。

## <a name="received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster"></a>在尝试创建新群集时收到错误消息，指示找不到服务主体或服务主体无效。

创建 AKS 群集时，需要使用服务主体或托管标识代表本人创建资源。 AKS 可以在创建群集时自动创建新的服务主体，也可以接收现有服务主体。 如果使用自动创建的服务主体，Azure Active Directory 需要将其传播到每个区域，以便创建成功。 如果传播时间过长，群集创建验证将失败，因为它无法找到可用的服务主体来执行此操作。 

对于此问题，请使用以下解决方法：
* 使用现有服务主体，该主体已跨区域传播，并可在群集创建时传入 AKS。
* 如果使用自动化脚本，请在创建服务主体和创建 AKS 群集之间增加延迟时间。
* 如果使用 Azure 门户，请在创建期间返回到群集设置，然后在几分钟后重试验证页面。

## <a name="im-getting-aadsts7000215-invalid-client-secret-is-provided-when-using-aks-api-what-should-i-do"></a>我正在 `"AADSTS7000215: Invalid client secret is provided."` 使用 AKS API。   应采取何种操作？

这通常是由于服务主体凭据过期引起的。 [更新 AKS 群集的凭据。](update-credentials.md)

## <a name="im-receiving-errors-after-restricting-egress-traffic"></a>在限制出口流量后收到错误消息

限制来自 AKS 群集的出口流量时，需要遵循针对 AKS 的[必需和可选的建议](limit-egress-traffic.md)出站端口/网络规则和 FQDN/应用程序规则。 如果你的设置与以上任意规则冲突，某些 `kubectl` 命令将无法正常运行。 在创建 AKS 群集时，也可能会遇到错误。

确认你的设置不与必需或可选的建议出站端口/网络规则和 FQDN/应用程序规则相冲突。

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure 存储和 AKS 疑难解答

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>适用于 Azure 磁盘的 Kubernetes 的建议稳定版本是什么？ 

| Kubernetes 版本 | 建议的版本 |
|--|:--:|
| 1.12 | 1.12.9 或更高版本 |
| 1.13 | 1.13.6 或更高版本 |
| 1.14 | 1.14.2 或更高版本 |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Azure 磁盘的 WaitForAttach 失败：分析“/dev/disk/azure/scsi1/lun1”：语法无效

在 Kubernetes 版本 1.10 中，重新装载 Azure 磁盘可能会导致 MountVolume.WaitForAttach 失败。

在 Linux 上，可能会收到 DevicePath 格式不正确的错误消息。 例如：

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

在 Windows 上，可能会收到 DevicePath(LUN) 编号出错的错误消息。 例如：

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

此问题在以下版本的 Kubernetes 中已得到修复：

| Kubernetes 版本 | 已修复的版本 |
|--|:--:|
| 1.10 | 1.10.2 或更高版本 |
| 1.11 | 1.11.0 或更高版本 |
| 1.12 和更高版本 | 空值 |


### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>在 Azure 磁盘的 mountOptions 中设置 uid 和 gid 失败

Azure 磁盘默认使用 ext4,xfs 文件系统，在装载时无法设置 uid=x,gid=x 之类的 mountOptions。 例如，如果尝试设置 mountpoptions uid=999、gid=999，将看到如下错误：

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

可以通过执行以下选项之一来缓解此问题：

* 通过在 runAsUser 中设置 uid 和在 fsGroup 中设置 gid [为 Pod 配置安全上下文](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)。 例如，以下设置将 pod 设置为作为根运行，使其可供任何文件访问：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > 因为 gid 和 uid 默认作为根或 0 装载。 如果 gid 或 uid 设置为非根（例如 1000），Kubernetes 将使用 `chown` 来更改该磁盘中的所有目录和文件。 此操作可能非常耗时，并可能导致磁盘装载速度变得很慢。

* 使用 initContainers 中的 `chown` 来设置 gid 和 uid。 例如：

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Azure 磁盘分离失败可能导致争用条件问题和数据磁盘列表无效

当 Azure 磁盘无法分离时，它会重试最多六次，以使用指数回退来分离磁盘。 它还会在数据磁盘列表上保留一个节点级锁约 3 分钟。 如果在这段时间内手动更新磁盘列表，会导致节点级锁保留的磁盘列表过时，并导致节点不稳定。

此问题在以下版本的 Kubernetes 中已得到修复：

| Kubernetes 版本 | 已修复的版本 |
|--|:--:|
| 1.12 | 1.12.9 或更高版本 |
| 1.13 | 1.13.6 或更高版本 |
| 1.14 | 1.14.2 或更高版本 |
| 1.15 和更高版本 | 空值 |

如果当前使用的 Kubernetes 版本无法修复此问题，并且你的节点具有过时的磁盘列表，可以通过批量操作将所有不存在的磁盘从 VM 中分离出来，以缓解此问题。 单独分离不存在的磁盘可能会失败。

### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Azure 磁盘数目过大导致附加/分离速度缓慢

如果针对单个节点 VM 的 Azure 磁盘附加/分离操作次数超过 10 次，或针对单个虚拟机规模集池时的操作次数超过 3 次，则速度可能会比预期要慢，因为它们是按顺序执行的。 此问题是已知限制，目前没有解决方法。 [用于支持并行附加/分离的 User Voice 项超出数量限制](https://feedback.azure.com/forums/216843-virtual-machines/suggestions/40444528-vmss-support-for-parallel-disk-attach-detach-for)。

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure 磁盘分离失败可能导致节点 VM 处于失败状态

在某些极端情况下，Azure 磁盘分离操作可能部分失败，导致节点 VM 处于故障状态。

此问题在以下版本的 Kubernetes 中已得到修复：

| Kubernetes 版本 | 已修复的版本 |
|--|:--:|
| 1.12 | 1.12.10 或更高版本 |
| 1.13 | 1.13.8 或更高版本 |
| 1.14 | 1.14.4 或更高版本 |
| 1.15 和更高版本 | 空值 |

如果当前使用的 Kubernetes 版本无法修复此问题，并且你的节点处于失败状态，可以使用以下方式手动更新 VM 状态，以缓解此问题：

* 对于基于可用性集的群集，使用以下代码：
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* 对于基于 VMSS 的群集：
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure 文件存储和 AKS 疑难解答

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>适用于 Azure 文件存储的 Kubernetes 的建议稳定版本是什么？
 
| Kubernetes 版本 | 建议的版本 |
|--|:--:|
| 1.12 | 1.12.6 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>使用 Azure 文件存储时的默认 mountOptions 是什么？

建议的设置：

| Kubernetes 版本 | fileMode 和 dirMode 值|
|--|:--:|
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 和更高版本 | 0777 |

可以对存储类对象指定装载选项。 以下示例设置 *0777*：

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

其他一些有用的 *mountOptions* 设置：

* *mfsymlinks* 将使 Azure 文件存储装入点 (cifs) 支持符号链接
* *nobrl* 将阻止向服务器发送字节范围锁请求。 对于使用 cifs 样式的强制字节范围锁中断的某些应用程序，此设置是必需的。 大多数 cifs 服务器尚不支持请求建议字节范围锁。 如果不使用 nobrl，则使用 cifs 样式的强制字节范围锁中断的应用程序可能会导致以下类似错误消息：
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>使用 Azure 文件存储时出现“无法更改权限”错误

在 Azure 文件存储插件中运行 PostgreSQL 时，可能会出现如下所示的错误：

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

此错误是由使用 cifs/SMB 协议的 Azure 文件存储插件造成的。 使用 cifs/SMB 协议时，在装载后无法更改文件和目录权限。

若要解决此问题，请结合 Azure 磁盘插件使用 *subPath*。 

> [!NOTE] 
> 对于 ext3/4 磁盘类型，格式化磁盘后会出现一个 lost+found 目录。

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>处理许多小型文件时，Azure 文件存储的延迟高于 Azure 磁盘

在某些情况下（例如处理许多的小型文件时），使用 Azure 文件存储时出现的延迟可能高于 Azure 磁盘。

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>对存储帐户启用“允许从所选网络进行访问”设置时出错

如果在 AKS 中对用于动态预配的存储帐户启用“允许从所选网络进行访问”，则在 AKS 创建文件共享时会出错：

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

出现此错误是因为当设置“允许从所选网络进行访问”时，Kubernetes persistentvolume-controller 不在所选网络上。

可以通过[使用 Azure 文件存储静态预配](azure-files-volume.md)来缓解此问题。

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure 文件存储无法在 Windows Pod 中重新装载

如果删除了包含已装载 Azure 文件存储的 Windows Pod，然后计划在同一节点上重新创建它，装载将失败。 之所以会失败，是因为 Azure 文件存储装载已装载在该节点上，这导致 `New-SmbGlobalMapping` 命令失败。

例如，你可能会看到类似以下内容的错误：

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

此问题在以下版本的 Kubernetes 中已得到修复：

| Kubernetes 版本 | 已修复的版本 |
|--|:--:|
| 1.12 | 1.12.6 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 或更高版本 | 空值 |

### <a name="azure-files-mount-fails-because-of-storage-account-key-changed"></a>由于存储帐户密钥已更改导致 Azure 文件存储装载失败

如果存储帐户密钥已更改，可能会遇到 Azure 文件存储装载失败。

可以使用 base64 编码的存储帐户密钥在 Azure 文件机密中手动更新 `azurestorageaccountkey` 字段，从而缓解此问题。

若要对存储帐户密钥进行 base64 编码，可以使用 `base64`。 例如：

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

若要更新 Azure 机密文件，请使用 `kubectl edit secret`。 例如：

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

几分钟后，代理节点将使用更新的存储密钥重新尝试装载 Azure 文件存储。


### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>群集自动缩放程序无法缩放并显示错误：无法设置固定的节点组大小

如果群集自动缩放程序无法增大/缩小，则会在[群集自动缩放程序日志][view-master-logs]上看到如下错误。

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

此错误是由于上游群集自动缩放程序争用条件导致的。 在这种情况下，集群自动缩放程序返回的值与群集中实际存在的值不同。 为此，请禁用[群集自动缩放程序][cluster-autoscaler]，然后再重新启用它。

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>磁盘连接速度缓慢，GetAzureDiskLun 需要 10 到 15 分钟的时间，并且会收到一条错误消息

在 1.15.0 之前的 Kubernetes 版本中，可能会收到错误消息，如“错误: WaitForAttach 找不到磁盘的 Lun”。  为解决此问题，请等待大约 15 分钟，然后重试。

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
