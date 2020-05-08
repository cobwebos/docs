---
title: 排查常见的 Azure Kubernetes 服务问题
description: 了解如何排查和解决在使用 Azure Kubernetes 服务 (AKS) 时遇到的常见问题
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: 8460f4f2a66a1f545bea767cccf3aa77c9d3bff3
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82778951"
---
# <a name="aks-troubleshooting"></a>AKS 疑难解答

当创建或管理 Azure Kubernetes 服务 (AKS) 群集时，可能偶尔会遇到问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>通常在何处查看 Kubernetes 问题调试的相关信息？

请尝试 [Kubernetes 群集故障排除的官方指南](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)。
还可尝试由 Microsoft 工程师发布的[故障排除指南](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)，用于对 Pod、节点、群集和其他功能进行故障排除。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>在创建或升级期间遇到“超出配额”的错误。   应采取何种操作？ 

需要[请求内核](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>对 AKS 而言，每个节点设置的最大 Pod 是多少？

如果在 Azure 门户中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 30。
如果在 Azure CLI 中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 110。 （确保使用最新版本的 Azure CLI）。 可以使用 `az aks create` 命令中的 `–-max-pods` 标记来更改此默认设置。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>在使用高级网络部署 AKS 群集时收到 insufficientSubnetSize 错误。   应采取何种操作？

如果使用 Azure CNI（高级网络），AKS 会根据配置的每个节点的“最大 Pod 数”分配 IP 地址。 根据配置的每个节点的最大 Pod 数，子网大小必须大于“节点数和每个节点的最大 Pod 数的乘积”设置。 以下公式对此进行了概述：

子网大小 > 群集中的节点数（考虑到未来的缩放要求）* 每个节点的最大 Pod 数。

有关详细信息，请参阅[规划群集的 IP 地址](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 停滞在 CrashLoopBackOff 模式。   应采取何种操作？

可能有多种原因导致 Pod 停滞在该模式。 可能通过以下方式查看：

* 使用 `kubectl describe pod <pod-name>` 查看 Pod 本身。
* 使用 `kubectl logs <pod-name>` 查看日志。

有关如何对 Pod 的问题进行故障排除的详细信息，请参阅[调试应用程序](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)。

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>尝试在现有群集上启用 RBAC。 该如何操作？

遗憾的是，目前不支持在现有群集上启用基于角色的访问控制 (RBAC)。 必须显式创建新群集。 如果使用 CLI，则默认启用 RBAC。 如果使用 AKS 门户，则在创建工作流时可使用切换按钮来启用 RBAC。

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>使用带有默认值的 Azure CLI 或 Azure 门户创建了一个启用了 RBAC 的集群，现在 Kubernetes 仪表板上出现了许多警告。 仪表板以前在没有任何警告的情况下工作。   应采取何种操作？

仪表板上收到警告的原因是群集现在启用了 RBAC，但已默认禁用了对它的访问。 一般来说，此方法比较棒，因为仪表板默认公开给群集的所有用户可能会导致安全威胁。 如果仍想要启用仪表板，请遵循此[博客文章](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)中的步骤进行操作。

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>我无法连接到仪表板。   应采取何种操作？

要访问群集外的服务，最简单的方法是运行 `kubectl proxy`，它将代理对 Kubernetes API 服务器使用 localhost 端口 8001 的请求。 在此，API 服务器可以代理服务：`http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/`。

如果看不到 Kubernetes 仪表板，请检查 `kube-proxy` Pod 是否在 `kube-system` 命名空间中运行。 如果未处于运行状态，请删除 Pod，它会重启。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>无法使用 Kubectl 日志获取日志或无法连接到 API 服务器。 我收到“来自服务器的错误：拨号后端时出错: 拨打 tcp...”。   应采取何种操作？

请确保默认网络安全组未被修改并且端口 22 和 9000 已打开以连接到 API 服务器。 使用 `kubectl get pods --namespace kube-system` 命令检查 `tunnelfront` Pod是否在 *kube-system* 命名空间中运行。 如果没有，请强制删除 Pod，它会重启。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我尝试升级或缩放，并收到 "消息：不允许更改属性 ' imageReference '" 错误。 如何修复此问题？

收到此错误的原因可能是，你修改了 AKS 群集内代理节点中的标记。 如果修改和删除 MC_* 资源组中资源的标记和其他属性，可能会导致意外结果。 修改 AKS 群集中 MC_ * 组下的资源会中断服务级别目标 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>有错误指出，我的群集处于故障状态，在解决此解决之前无法进行升级或缩放

*此故障排除帮助是从https://aka.ms/aks-cluster-failed*

如果群集出于多种原因进入故障状态，则会发生此错误。 请遵循以下步骤解决群集故障状态，然后重试先前失败的操作：

1. 除非群集摆脱 `failed` 状态，否则 `upgrade` 和 `scale` 操作不会成功。 常见的根本问题和解决方法包括：
    * 使用**不足的计算 (CRP) 配额**进行缩放。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高计算配额](../azure-portal/supportability/resource-manager-core-quotas-request.md)，然后尝试扩展到超出初始配额限制。
    * 使用高级网络和**不足的子网（网络）资源**缩放群集。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高资源配额](../azure-resource-manager/templates/error-resource-quota.md#solution)，然后尝试扩展到超出初始配额限制。
2. 解决升级失败的根本原因后，群集应会进入成功状态。 确认处于成功状态后，重试原始操作。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>尝试升级或缩放群集时，有错误指出我的群集当前正在升级或升级失败

*此故障排除帮助是从https://aka.ms/aks-pending-upgrade*

带有单个节点池或[多个节点池](use-multiple-node-pools.md)的群集上的升级和缩放操作是互斥的。 不能让群集或节点池同时升级和缩放， 而只能先在目标资源上完成一个操作类型，然后再在同一资源上执行下一个请求。 因此，如果当前正在执行升级或缩放操作，或者曾经尝试过这些操作，但随后失败，则其他操作会受到限制。 

若要诊断此问题，请运行 `az aks show -g myResourceGroup -n myAKSCluster -o table` 检索群集上的详细状态。 根据结果：

* 如果群集正在升级，请等到该操作终止。 如果升级成功，请再次重试先前失败的操作。
* 如果群集升级失败，请按前面部分所述的步骤操作。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>是否可以将我的群集移动到其他订阅，或者说，是否可以将包含我的群集的订阅移动到新租户？

如果你已将 AKS 群集移动到其他订阅，或者将拥有订阅的群集移动到新租户，则群集将会由于失去角色分配和服务主体权限而丢失功能。 由于此约束，**AKS 不支持在订阅或租户之间移动群集**。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>尝试使用需要虚拟机规模集的功能时收到错误

*此故障排除帮助来自 aka.ms/aks-vmss-enablement*

可能会收到指示 AKS 群集不在虚拟机规模集上的错误，例如：

**AgentPool“agentpool”已将自动缩放设置为已启用，但它未在虚拟机规模集上**

若要使用群集自动缩放程序或多节点池等功能，必须创建使用虚拟机规模集的 AKS 群集。 如果尝试使用依赖于虚拟机规模集的功能，并以常规的非虚拟机规模集 AKS 群集为目标，则会返回错误。

按照相应文档中的*开始之前*步骤操作，以便正确创建 AKS 群集：

* [使用群集自动缩放程序](cluster-autoscaler.md)
* [创建和使用多个节点池](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>针对 AKS 资源和参数强制实施了什么命名限制？

*此故障排除帮助来自 aka.ms/aks-naming-rules*

Azure 平台和 AKS 都实施了命名限制。 如果资源名称或参数违反了这些限制之一，则会返回一个错误，要求你提供不同的输入。 将应用以下通用的命名准则：

* 群集名称必须为 1-63 个字符。 唯一允许的字符是字母、数字、短划线和下划线。 第一个和最后一个字符必须是字母或数字。
* AKS *MC_* 资源组名称组合了资源组名称和资源名称。 自动生成的语法 `MC_resourceGroupName_resourceName_AzureRegion` 不能超过 80 个字符。 如果需要，请缩短你的资源组名称或 AKS 群集名称的长度。
* *DnsPrefix*必须以字母数字值开头和结尾，且必须介于1-54 个字符之间。 有效字符包括字母数字值和连字符 (-)。 *dnsPrefix* 不能包含特殊字符，例如句点 (.)。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>我在尝试创建、更新、缩放、删除或升级群集时收到错误，该操作不被允许，因为另一个操作正在进行。

*此故障排除帮助是从 aka.ms/aks-pending-operation*

当上一个操作仍在进行时，群集操作会受限。 若要检索群集的详细状态，请使用 `az aks show -g myResourceGroup -n myAKSCluster -o table` 命令。 根据需要使用自己的资源组和 AKS 群集名称。

根据群集状态的输出：

* 如果群集的预配状态不是“成功”或“失败”，请等待**** 操作（升级/更新/创建/缩放/删除/迁移**）终止。 当上一操作完成后，请重试最新的群集操作。

* 如果群集的升级失败，请按[有错误指出，我的群集处于故障状态，在解决此解决之前无法进行升级或缩放](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)中概述的步骤操作。

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>尝试创建一个新群集而不是传入现有群集时，收到“找不到服务主体”错误。

创建 AKS 群集时，需要服务主体来代表你创建资源。 AKS 提供了在创建群集时创建新服务主体的功能，但这需要 Azure Active Directory 在合理的时间内完全传播新的服务主体，以便成功创建群集。 当此传播花费的时间太长时，群集将无法创建验证，因为它找不到可用的服务主体来执行此操作。 

为此，请使用以下解决方法：
1. 使用已在区域中传播并且存在的现有服务主体，并在创建群集时将其传入 AKS。
2. 如果使用自动化脚本，请在创建服务主体和创建 AKS 群集之间添加时间延迟。
3. 如果使用 Azure 门户，请在创建过程中返回到群集设置，并在几分钟后重试验证页。

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>限制出站流量后收到错误

在限制来自 AKS 群集的出口流量时，需要为 AKS[提供必需和可选](limit-egress-traffic.md)的出站端口/网络规则和 FQDN/应用程序规则。 如果你的设置与任何这些规则冲突，你可能无法运行某些`kubectl`命令。 创建 AKS 群集时，还可能会看到错误。

验证你的设置是否不与任何必需或可选的建议出站端口/网络规则和 FQDN/应用程序规则冲突。

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure 存储和 AKS 故障排除

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>适用于 Azure 磁盘的 Kubernetes 的建议稳定版本是什么？ 

| Kubernetes 版本 | 建议的版本 |
| -- | :--: |
| 1.12 | 1.12.9 或更高版本 |
| 1.13 | 1.13.6 或更高版本 |
| 1.14 | 1.14.2 或更高版本 |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>哪些 Kubernetes 版本在主权云中提供 Azure 磁盘支持？

| Kubernetes 版本 | 建议的版本 |
| -- | :--: |
| 1.12 | 1.12.0 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Azure 磁盘的 WaitForAttach 失败: 分析 "/dev/disk/azure/scsi1/lun1": 语法无效

在 Kubernetes 版本 1.10 中，MountVolume.WaitForAttach 可能会失败并出现 Azure 磁盘重装入点。

在 Linux 上，可能会出现“错误的 DevicePath 格式”错误。 例如：

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

在 Windows 上，你可能会看到错误的 DevicePath （LUN）编号错误。 例如：

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

此问题已在以下版本的 Kubernetes 中得到解决：

| Kubernetes 版本 | 已修复的版本 |
| -- | :--: |
| 1.10 | 1.10.2 或更高版本 |
| 1.11 | 1.11.0 或更高版本 |
| 1.12 和更高版本 | 不可用 |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>在 Azure 磁盘的 mountOptions 中设置 uid 和 gid 失败

Azure 磁盘默认使用 ext4,xfs 文件系统，在装载时无法设置 uid=x,gid=x 之类的 mountOptions。 例如，如果尝试设置 mountOptions uid=999,gid=999，将出现如下所示的错误：

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

可以通过执行以下操作之一来缓解此问题：

* 通过在 runAsUser 中设置 uid 并在 fsGroup 中设置 gid，来[配置 pod 的安全上下文](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)。 例如，以下设置将 pod 设置为作为根运行，使其可供任何文件访问：

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

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>删除 pod 使用的 Azure 磁盘 PersistentVolumeClaim 时出错

如果尝试删除 pod 正在使用的 Azure 磁盘 PersistentVolumeClaim，可能会出现错误。 例如：

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

在 Kubernetes 1.10 和更高版本中，默认已启用 PersistentVolumeClaim protection 功能以防止此错误。 如果使用的 Kubernetes 版本未解决此问题，可以通过在删除 PersistentVolumeClaim 之前使用 PersistentVolumeClaim 删除 pod 来缓解此问题。


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>将磁盘附加到节点时出现“找不到磁盘的 LUN”错误

将磁盘附加到节点时，可能会出现以下错误：

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

此问题已在以下版本的 Kubernetes 中得到解决：

| Kubernetes 版本 | 已修复的版本 |
| -- | :--: |
| 1.10 | 1.10.10 或更高版本 |
| 1.11 | 1.11.5 或更高版本 |
| 1.12 | 1.12.3 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 或更高版本 | 不可用 |

如果使用的 Kubernetes 版本未解决此问题，可以等待几分钟再重试，这样就可以缓解此问题。

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>在运行多个附加/分离操作期间出现 Azure 磁盘附加/分离失败、装载问题或 I/O 错误

从 Kubernetes 版本1.9.2 开始，同时运行多个附加/分离操作时，可能会出现脏 VM 缓存造成的以下磁盘问题：

* 磁盘附加/分离失败
* 磁盘 I/O 错误
* 磁盘从 VM 意外分离
* 由于附加不存在的磁盘导致 VM 在故障状态下运行

此问题已在以下版本的 Kubernetes 中得到解决：

| Kubernetes 版本 | 已修复的版本 |
| -- | :--: |
| 1.10 | 1.10.12 或更高版本 |
| 1.11 | 1.11.6 或更高版本 |
| 1.12 | 1.12.4 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 或更高版本 | 不可用 |

如果使用的 Kubernetes 版本未解决此问题，可以尝试以下方法来缓解此问题：

* 如果某个磁盘长时间等待分离，请尝试手动分离该磁盘

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>无限期等待分离的 Azure 磁盘

在某些情况下，如果 Azure 磁盘首次尝试分离操作失败，该磁盘不会重试分离操作，而是保持附加到原始节点 VM。 将磁盘从一个节点移到另一个节点时，可能会发生此错误。 例如：

```console
[Warning] AttachVolume.Attach failed for volume "pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" : Attach volume "kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance "/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0" failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code="ConflictingUserInput" Message="Disk '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9' cannot be attached as the disk is already owned by VM '/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1'."
```

此问题已在以下版本的 Kubernetes 中得到解决：

| Kubernetes 版本 | 已修复的版本 |
| -- | :--: |
| 1.11 | 1.11.9 或更高版本 |
| 1.12 | 1.12.7 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 或更高版本 | 不可用 |

如果使用的 Kubernetes 版本未解决此问题，可以通过手动分离磁盘来缓解此问题。

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Azure 磁盘分离失败导致潜在的争用条件问题和无效的数据磁盘列表

当 Azure 磁盘无法分离时，它会重试最多六次，以使用指数回退来分离磁盘。 它还会持有数据磁盘列表中的节点级锁大约 3 分钟。 如果在该时间段内手动更新磁盘列表，例如，执行手动附加或分离操作，将会导致节点级锁持有的磁盘列表过时，并导致节点 VM 不稳定。

此问题已在以下版本的 Kubernetes 中得到解决：

| Kubernetes 版本 | 已修复的版本 |
| -- | :--: |
| 1.12 | 1.12.9 或更高版本 |
| 1.13 | 1.13.6 或更高版本 |
| 1.14 | 1.14.2 或更高版本 |
| 1.15 和更高版本 | 不可用 |

如果使用的 Kubernetes 版本未解决此问题，并且节点 VM 包含过时的磁盘列表，则你可以通过一个批量操作从 VM 中分离所有不存在的磁盘，以此缓解此问题。 **单独分离不存在的磁盘可能会失败。**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>大量的 Azure 磁盘导致附加/分离速度缓慢

如果将 10 个以上的 Azure 磁盘附加到节点 VM，附加和分离操作的速度可能很慢。 这是一个已知的问题，暂时没有解决方法。

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure 磁盘分离失败可能导致节点 VM 处于故障状态

在某些极端情况下，Azure 磁盘分离操作可能部分失败，导致节点 VM 处于故障状态。

此问题已在以下版本的 Kubernetes 中得到解决：

| Kubernetes 版本 | 已修复的版本 |
| -- | :--: |
| 1.12 | 1.12.10 或更高版本 |
| 1.13 | 1.13.8 或更高版本 |
| 1.14 | 1.14.4 或更高版本 |
| 1.15 和更高版本 | 不可用 |

如果使用的 Kubernetes 版本未解决此问题，并且节点 VM 处于故障状态，可以使用以下方法之一手动更新 VM 状态，以此缓解此问题：

* 对于基于可用性集的群集：
    ```azurecli
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* 对于基于 VMSS 的群集：
    ```azurecli
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure 文件存储和 AKS 故障排除

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>适用于 Azure 文件存储的 Kubernetes 的建议稳定版本是什么？
 
| Kubernetes 版本 | 建议的版本 |
| -- | :--: |
| 1.12 | 1.12.6 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>哪些 Kubernetes 版本在主权云中提供 Azure 文件存储支持？

| Kubernetes 版本 | 建议的版本 |
| -- | :--: |
| 1.12 | 1.12.0 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>使用 Azure 文件存储时的默认 mountOptions 是什么？

建议的设置：

| Kubernetes 版本 | fileMode 和 dirMode 值|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 和更高版本 | 0777 |

如果使用 Kubernetes 版本1.8.5 或更高版本的群集，并使用存储类动态创建永久卷，则可以在存储类对象上指定装载选项。 以下示例设置 *0777*：

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
* *nobrl* 将阻止向服务器发送字节范围锁请求。 对于中断 cifs 样式强制字节范围锁的某些应用程序，必须使用此设置。 大多数 cifs 服务器尚不支持请求建议字节范围锁。 如果不使用 *nobrl*，则中断 cifs 样式强制字节范围锁的应用程序可能导致如下所示的错误消息：
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

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>在存储帐户中启用“允许从所选网络进行访问”设置时出错

如果在用于 AKS 中的动态预配的存储帐户上启用“允许从所选网络进行访问”，当 AKS 创建文件共享时会出现错误：**

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

出现此错误的原因是在设置“允许从所选网络进行访问”时，Kubernetes *persistentvolume-controller* 不在所选的网络中。**

可以通过[对 Azure 文件存储使用静态预配](azure-files-volume.md)来缓解此问题。

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure 文件无法在 Windows pod 中重新装载

如果删除了包含 Azure 文件装载的 Windows pod，然后计划在同一节点上重新创建，装载将失败。 此失败的原因是由于`New-SmbGlobalMapping` Azure 文件装载已装载到节点上，导致命令失败。

例如，你可能会看到类似于以下内容的错误：

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

此问题已在以下版本的 Kubernetes 中得到解决：

| Kubernetes 版本 | 已修复的版本 |
| -- | :--: |
| 1.12 | 1.12.6 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 或更高版本 | 不可用 |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>由于存储帐户密钥已更改，Azure 文件存储装载失败

如果存储帐户密钥已更改，可能会发生 Azure 文件存储装载失败。

若要缓解此问题，可以使用 base64 编码的存储帐户密钥手动更新 Azure 文件机密中的 *azurestorageaccountkey* 字段。

若要以 base64 编码存储帐户密钥，可以使用 `base64`。 例如：

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

若要更新 Azure 机密文件，请使用 `kubectl edit secret`。 例如：

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

几分钟后，代理节点将使用更新的存储密钥重试 Azure 文件装载。

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>群集自动缩放程序无法缩放，出现错误，无法修复节点组大小

如果群集自动缩放程序未向上/向下扩展，并且在[群集自动缩放程序日志][view-master-logs]上看到如下错误。

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

此错误是由于上游群集自动缩放程序争用条件，其中的群集自动缩放程序以与群集中实际的值不同的值结束。 若要退出此状态，只需禁用并重新启用[群集自动缩放程序][cluster-autoscaler]。

### <a name="slow-disk-attachment-getazuredisklun-takes-10-to-15-minutes-and-you-receive-an-error"></a>缓慢的磁盘连接，GetAzureDiskLun 需要10到15分钟的时间，并且你会收到一条错误消息

在**早于 1.15.0**的 Kubernetes 版本中，你可能会收到一个错误，例如**错误 WaitForAttach 找不到磁盘的 Lun**。  此问题的解决方法是等待大约15分钟，然后重试。

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
