---
title: 排查常见的 Azure Kubernetes 服务问题
description: 了解如何排查和解决在使用 Azure Kubernetes 服务 (AKS) 时遇到的常见问题
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 270dbb24d851645ff7a7f0bcf5f78bfb95bcd095
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2019
ms.locfileid: "73604732"
---
# <a name="aks-troubleshooting"></a>AKS 疑难解答

当创建或管理 Azure Kubernetes 服务 (AKS) 群集时，可能偶尔会遇到问题。 本文详细介绍了一些常见问题及其排查步骤。

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>通常在何处查看 Kubernetes 问题调试的相关信息？

请尝试 [Kubernetes 群集故障排除的官方指南](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/)。
还可尝试由 Microsoft 工程师发布的[故障排除指南](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)，用于对 Pod、节点、群集和其他功能进行故障排除。

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>在创建或升级期间遇到“超出配额”的错误。 我该怎么办？ 

需要[请求内核](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request)。

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>对 AKS 而言，每个节点设置的最大 Pod 是多少？

如果在 Azure 门户中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 30。
如果在 Azure CLI 中部署 AKS 群集，则每个节点的最大 Pod 均默认设置为 110。 （确保使用最新版本的 Azure CLI）。 可以使用 `–-max-pods` 命令中的 `az aks create` 标记来更改此默认设置。

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>在使用高级网络部署 AKS 群集时收到 insufficientSubnetSize 错误。 我该怎么办？

如果使用 Azure CNI （高级网络），则 AKS 会根据配置的每个节点的 "最大箱" 分配 IP 地址。 根据每个节点配置的最大 pod 数，子网大小必须大于节点数和每个节点的最大 pod 数的乘积。 以下公式概述了这一点：

子网大小 > 群集中的节点数（考虑未来的缩放要求） * 每个节点集的最大箱数。

有关详细信息，请参阅[规划群集的 IP 地址](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)。

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>我的 Pod 停滞在 CrashLoopBackOff 模式。 我该怎么办？

可能有多种原因导致 Pod 停滞在该模式。 可能通过以下方式查看：

* 使用 `kubectl describe pod <pod-name>` 查看 Pod 本身。
* 使用 `kubectl log <pod-name>` 查看日志。

有关如何对 Pod 的问题进行故障排除的详细信息，请参阅[调试应用程序](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)。

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>尝试在现有群集上启用 RBAC。 该如何操作？

遗憾的是，目前不支持在现有群集上启用基于角色的访问控制 (RBAC)。 必须显式创建新群集。 如果使用 CLI，则默认启用 RBAC。 如果使用 AKS 门户，则在创建工作流时可使用切换按钮来启用 RBAC。

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>使用带有默认值的 Azure CLI 或 Azure 门户创建了一个启用了 RBAC 的集群，现在 Kubernetes 仪表板上出现了许多警告。 仪表板以前在没有任何警告的情况下工作。 我该怎么办？

仪表板上收到警告的原因是群集现在启用了 RBAC，但已默认禁用了对它的访问。 一般来说，此方法比较棒，因为仪表板默认公开给群集的所有用户可能会导致安全威胁。 如果仍想要启用仪表板，请遵循此[博客文章](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/)中的步骤进行操作。

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>我无法连接到仪表板。 我该怎么办？

要访问群集外的服务，最简单的方法是运行 `kubectl proxy`，它将代理对 Kubernetes API 服务器使用 localhost 端口 8001 的请求。 在此，API 服务器可以代理服务：`http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`。

如果看不到 Kubernetes 仪表板，请检查 `kube-proxy` Pod 是否在 `kube-system` 命名空间中运行。 如果未处于运行状态，请删除 Pod，它会重启。

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>无法使用 Kubectl 日志获取日志或无法连接到 API 服务器。 我收到“来自服务器的错误：拨号后端时出错: 拨打 tcp...”。 我该怎么办？

请确保默认网络安全组未被修改并且端口 22 和 9000 已打开以连接到 API 服务器。 使用 `tunnelfront` 命令检查 *Pod是否在*kube-system`kubectl get pods --namespace kube-system` 命名空间中运行。 如果没有，请强制删除 Pod，它会重启。

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>我尝试升级或缩放，并收到 "消息：不允许更改属性 ' imageReference '" 错误。 如何修复此问题？

收到此错误的原因可能是，你修改了 AKS 群集内代理节点中的标记。 如果修改和删除 MC_* 资源组中资源的标记和其他属性，可能会导致意外结果。 修改 AKS 群集中 MC_ * 组下的资源会中断服务级别目标 (SLO)。

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>有错误指出，我的群集处于故障状态，在解决此解决之前无法进行升级或缩放

*此故障排除帮助是通过 https://aka.ms/aks-cluster-failed*

如果群集出于多种原因进入故障状态，则会发生此错误。 请遵循以下步骤解决群集故障状态，然后重试先前失败的操作：

1. 除非群集摆脱 `failed` 状态，否则 `upgrade` 和 `scale` 操作不会成功。 常见的根本问题和解决方法包括：
    * 使用**不足的计算 (CRP) 配额**进行缩放。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高计算配额](../azure-supportability/resource-manager-core-quotas-request.md)，然后尝试扩展到超出初始配额限制。
    * 使用高级网络和**不足的子网（网络）资源**缩放群集。 若要解决此问题，请先将群集缩放回到配额内的稳定目标状态。 遵循[这些步骤请求提高资源配额](../azure-resource-manager/resource-manager-quota-errors.md#solution)，然后尝试扩展到超出初始配额限制。
2. 解决升级失败的根本原因后，群集应会进入成功状态。 确认处于成功状态后，重试原始操作。

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>尝试升级或缩放群集时，有错误指出我的群集当前正在升级或升级失败

*此故障排除帮助是通过 https://aka.ms/aks-pending-upgrade*

具有单个节点池的群集或具有[多个节点池](use-multiple-node-pools.md)的群集的升级和缩放操作是互斥的。 不能让群集或节点池同时升级和缩放， 而只能先在目标资源上完成一个操作类型，然后再在同一资源上执行下一个请求。 因此，如果当前正在执行升级或缩放操作，或者曾经尝试过这些操作，但随后失败，则其他操作会受到限制。 

若要诊断此问题，请运行 `az aks show -g myResourceGroup -n myAKSCluster -o table` 检索群集上的详细状态。 根据结果：

* 如果群集正在升级，请等到该操作终止。 如果升级成功，请再次重试先前失败的操作。
* 如果群集升级失败，请按前面部分所述的步骤操作。

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>是否可以将我的群集移动到其他订阅，或者说，是否可以将包含我的群集的订阅移动到新租户？

如果你已将 AKS 群集移动到其他订阅，或者将拥有订阅的群集移动到新租户，则群集将会由于失去角色分配和服务主体权限而丢失功能。 由于此约束，**AKS 不支持在订阅或租户之间移动群集**。

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>尝试使用需要虚拟机规模集的功能时收到错误

*此故障排除帮助是从 aka.ms/aks-vmss-enablement*

可能会收到指示 AKS 群集不在虚拟机规模集上的错误，例如以下示例：

**AgentPool ' AgentPool ' 已启用自动缩放，但它不在虚拟机规模集上**

若要使用群集自动缩放程序或多节点池等功能，必须创建使用虚拟机规模集的 AKS 群集。 如果尝试使用依赖于虚拟机规模集的功能，并以常规的非虚拟机规模集 AKS 群集为目标，则会返回错误。

在相应文档中*开始步骤之前*，请执行相应的步骤，以正确创建 AKS 群集：

* [使用群集自动缩放程序](cluster-autoscaler.md)
* [创建和使用多个节点池](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>针对 AKS 资源和参数强制实施了什么命名限制？

*此故障排除帮助来自 aka.ms/aks-naming-rules*

Azure 平台和 AKS 都实施了命名限制。 如果资源名称或参数违反了这些限制之一，则会返回一个错误，要求你提供不同的输入。 将应用以下通用的命名准则：

* AKS *MC_* 资源组名称组合了资源组名称和资源名称。 自动生成的语法 `MC_resourceGroupName_resourceName_AzureRegion` 不能超过 80 个字符。 如果需要，请缩短你的资源组名称或 AKS 群集名称的长度。
* *dnsPrefix* 的开头和结尾必须是字母数字值。 有效字符包括字母数字值和连字符 (-)。 *dnsPrefix* 不能包含特殊字符，例如句点 (.)。

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>尝试创建、更新、缩放、删除或升级群集时收到错误，不允许执行该操作，因为正在执行其他操作。

*此故障排除帮助是从 aka.ms/aks-pending-operation*

当上一个操作仍在进行时，群集操作会受限。 若要检索群集的详细状态，请使用 `az aks show -g myResourceGroup -n myAKSCluster -o table` 命令。 根据需要使用自己的资源组和 AKS 群集名称。

根据群集状态的输出：

* 如果群集的预配状态不是“成功”或“失败”，请等待操作（升级/更新/创建/缩放/删除/迁移）终止。 当上一操作完成后，请重试最新的群集操作。

* 如果群集的升级失败，请按[有错误指出，我的群集处于故障状态，在解决此解决之前无法进行升级或缩放](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed)中概述的步骤操作。

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>尝试创建一个新群集而不是传入现有群集时，收到“找不到服务主体”错误。

创建 AKS 群集时，需要服务主体来代表你创建资源。 AKS 提供了在创建群集时创建新服务主体的功能，但这需要 Azure Active Directory 在合理的时间内完全传播新的服务主体，以便成功创建群集。 当此传播花费的时间太长时，群集将无法创建验证，因为它找不到可用的服务主体来执行此操作。 

为此，请使用以下解决方法：
1. 使用已在区域中传播并且存在的现有服务主体，并在创建群集时将其传入 AKS。
2. 如果使用自动化脚本，请在创建服务主体和创建 AKS 群集之间添加时间延迟。
3. 如果使用 Azure 门户，请在创建过程中返回到群集设置，并在几分钟后重试验证页。

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>限制出站流量后收到错误

在限制来自 AKS 群集的出口流量时，需要为 AKS[提供必需和可选](limit-egress-traffic.md)的出站端口/网络规则和 FQDN/应用程序规则。 如果你的设置与任何这些规则冲突，你可能无法运行某些 `kubectl` 命令。 创建 AKS 群集时，还可能会看到错误。

验证你的设置是否不与任何必需或可选的建议出站端口/网络规则和 FQDN/应用程序规则冲突。

## <a name="azure-storage-and-aks-troubleshooting"></a>Azure 存储和 AKS 故障排除

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Azure 磁盘的 Kubernetes 推荐稳定版本是什么？ 

| Kubernetes 版本 | 建议的版本 |
| -- | :--: |
| 1.12 | 1.12.9 或更高版本 |
| 1.13 | 1.13.6 或更高版本 |
| 1.14 | 1.14.2 或更高版本 |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>哪些版本的 Kubernetes 在主权云上支持 Azure 磁盘？

| Kubernetes 版本 | 建议的版本 |
| -- | :--: |
| 1.12 | 1.12.0 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Azure 磁盘的 WaitForAttach 失败：分析 "/dev/disk/azure/scsi1/lun1"：语法无效

在 Kubernetes 版本1.10 中，MountVolume 可能会因为 Azure 磁盘重新装载而失败。

在 Linux 上，可能会出现不正确的 DevicePath 格式错误。 例如：

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

在 Windows 上，你可能会看到错误的 DevicePath （LUN）编号错误。 例如：

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

此问题已在 Kubernetes 的以下版本中得到解决：

| Kubernetes 版本 | 已修复版本 |
| -- | :--: |
| 1.10 | 1.10.2 或更高版本 |
| 1.11 | 1.11.0 或更高版本 |
| 1.12 及更高版本 | 不适用 |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>在 Azure 磁盘的 mountOptions 中设置 uid 和 gid 时失败

默认情况下，Azure 磁盘使用 ext4、xfs filesystem 和 mountOptions，如 uid = x，gid = x 无法在装入时设置。 例如，如果你尝试设置 mountOptions uid = 999，gid = 999，将看到类似于以下内容的错误：

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

可以通过执行以下操作之一来缓解此问题：

* 通过在 fsGroup 中的 runAsUser 和 gid 中设置 uid 来[配置 pod 的安全上下文](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/)。 例如，以下设置会将 pod 设置为 root，使其可供任何文件访问：

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
  > 因为 gid 和 uid 默认装载为 root 或0。 如果 gid 或 uid 设置为非根（例如1000），则 Kubernetes 将使用 `chown` 更改该磁盘下的所有目录和文件。 此操作可能非常耗时，并且可能会导致装载磁盘的速度非常慢。

* 使用 initContainers 中的 `chown` 设置 gid 和 uid。 例如：

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

如果尝试删除 pod 使用的 Azure 磁盘 PersistentVolumeClaim，可能会看到错误。 例如：

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

在 Kubernetes 版本1.10 及更高版本中，默认情况下已启用 PersistentVolumeClaim protection 功能以防止此错误。 如果你使用的 Kubernetes 版本不能解决此问题，则可以通过在删除 PersistentVolumeClaim 前使用 PersistentVolumeClaim 删除 pod 来缓解此问题。


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>将磁盘附加到节点时出现 "找不到磁盘的 Lun" 错误

将磁盘附加到节点时，可能会看到以下错误：

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

此问题已在 Kubernetes 的以下版本中得到解决：

| Kubernetes 版本 | 已修复版本 |
| -- | :--: |
| 1.10 | 1.10.10 或更高版本 |
| 1.11 | 1.11.5 或更高版本 |
| 1.12 | 1.12.3 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 及更高版本 | 不适用 |

如果你使用的 Kubernetes 版本不能解决此问题，则可以通过等待几分钟再重试来缓解此问题。

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>在多个附加/分离操作期间出现 Azure 磁盘附加/分离故障、装载问题或 i/o 错误

从 Kubernetes 版本1.9.2 开始，并行运行多个附加/分离操作时，可能会出现以下磁盘问题，因为有更新的 VM 缓存：

* 磁盘附加/分离失败
* 磁盘 i/o 错误
* VM 中出现意外的磁盘分离
* 由于附加了不存在的磁盘，运行到的 VM 失败

此问题已在 Kubernetes 的以下版本中得到解决：

| Kubernetes 版本 | 已修复版本 |
| -- | :--: |
| 1.10 | 1.10.12 或更高版本 |
| 1.11 | 1.11.6 或更高版本 |
| 1.12 | 1.12.4 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 及更高版本 | 不适用 |

如果你使用的 Kubernetes 版本不能解决此问题，则可以尝试以下方法来缓解此问题：

* 如果有一个磁盘等待很长一段时间分离，请尝试手动分离磁盘

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Azure 磁盘等待无休止地分离

在某些情况下，如果 Azure 磁盘分离操作第一次尝试失败，则不会重试分离操作，并将保持附加到原始节点 VM。 将磁盘从一个节点移到另一个节点时，可能会发生此错误。 例如：

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

此问题已在 Kubernetes 的以下版本中得到解决：

| Kubernetes 版本 | 已修复版本 |
| -- | :--: |
| 1.11 | 1.11.9 或更高版本 |
| 1.12 | 1.12.7 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 及更高版本 | 不适用 |

如果你使用的 Kubernetes 版本不能解决此问题，则可以通过手动分离磁盘来缓解此问题。

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Azure 磁盘分离失败导致潜在的争用条件问题和无效的数据磁盘列表

当 Azure 磁盘无法分离时，它将重试最多六次，以使用指数回退分离磁盘。 它还将在数据磁盘列表上保留一个节点级锁约3分钟。 如果在该时间段内手动更新磁盘列表，例如手动附加或分离操作，这会导致节点级锁定持有的磁盘列表过时，并导致节点 VM 不稳定。

此问题已在 Kubernetes 的以下版本中得到解决：

| Kubernetes 版本 | 已修复版本 |
| -- | :--: |
| 1.12 | 1.12.9 或更高版本 |
| 1.13 | 1.13.6 或更高版本 |
| 1.14 | 1.14.2 或更高版本 |
| 1.15 及更高版本 | 不适用 |

如果你使用的 Kubernetes 版本没有此问题的修补程序，并且你的节点 VM 具有过时的磁盘列表，则可以通过将所有非现有磁盘作为单个批量操作从 VM 分离来缓解此问题。 **单独分离非现有磁盘可能会失败。**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>大量 Azure 磁盘导致附加/分离缓慢

当附加到节点 VM 的 Azure 磁盘数量大于10时，附加和分离操作可能会很慢。 此问题是已知问题，目前没有解决方法。

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Azure 磁盘分离失败导致潜在节点 VM 处于失败状态

在某些边缘情况下，Azure 磁盘分离可能部分失败并使节点 VM 处于失败状态。

此问题已在 Kubernetes 的以下版本中得到解决：

| Kubernetes 版本 | 已修复版本 |
| -- | :--: |
| 1.12 | 1.12.10 或更高版本 |
| 1.13 | 1.13.8 或更高版本 |
| 1.14 | 1.14.4 或更高版本 |
| 1.15 及更高版本 | 不适用 |

如果你使用的 Kubernetes 版本没有此问题的修补程序，并且你的节点 VM 处于故障状态，则可以通过使用以下其中一项来手动更新 VM 状态来缓解此问题：

* 对于基于可用性集的群集：
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* 对于基于 VMSS 的群集：
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Azure 文件和 AKS 故障排除

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Azure 文件的 Kubernetes 的建议稳定版本是什么？
 
| Kubernetes 版本 | 建议的版本 |
| -- | :--: |
| 1.12 | 1.12.6 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>主权云中的哪些版本的 Kubernetes 支持 Azure 文件？

| Kubernetes 版本 | 建议的版本 |
| -- | :--: |
| 1.12 | 1.12.0 或更高版本 |
| 1.13 | 1.13.0 或更高版本 |
| 1.14 | 1.14.0 或更高版本 |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>使用 Azure 文件时的默认 mountOptions 是什么？

建议的设置：

| Kubernetes 版本 | dirMode 和值|
| -- | :--: |
| 1.12.0-1.12。1 | 0755 |
| 1.12.2 及更高版本 | 0777 |

如果使用 Kuberetes 版本为 1.8.5 或更高版本的群集并使用存储类动态创建永久性卷，则可以在存储类对象上指定装载选项。 以下示例设置 *0777*：

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

其他一些有用的*mountOptions*设置：

* *mfsymlinks*将使 Azure 文件装载（cifs）支持符号链接
* *nobrl*将禁止向服务器发送字节范围锁请求。 对于与 cifs 样式的强制字节范围锁中断的某些应用程序，此设置是必需的。 大多数 cifs 服务器还不支持请求通知字节范围锁。 如果不使用*nobrl*，则与 cifs 样式的强制字节范围锁中断的应用程序可能会导致类似于以下内容的错误消息：
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>使用 Azure 文件时出现错误 "无法更改权限"

在 Azure Files 插件上运行 PostgreSQL 时，可能会看到类似于以下内容的错误：

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

此错误是由使用 cifs/SMB 协议的 Azure 文件插件导致的。 使用 cifs/SMB 协议时，无法在装载后更改文件和目录权限。

若要解决此问题，请将子*路径*与 Azure 磁盘插件结合使用。 

> [!NOTE] 
> 对于 ext3/4 磁盘类型，对磁盘进行格式化后，会丢失 + 找到的目录。

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>处理多个小型文件时，azure 文件与 Azure 磁盘相比具有较高的延迟

在某些情况下（如处理多个小文件），与 Azure 磁盘相比，在使用 Azure 文件时可能会遇到较高的延迟。

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>在存储帐户上启用 "允许从所选网络访问允许访问" 设置时出错

如果在 AKS 中启用了用于动态预配的存储帐户上的 "*允许从所选网络进行访问*"，则在 AKS 创建文件共享时，会收到错误：

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

此错误是由于设置 "*允许从所选网络访问*" 时 Kubernetes *persistentvolume*不在网络上。

可以通过[对 Azure 文件使用静态预配](azure-files-volume.md)来缓解此问题。

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Azure 文件无法在 Windows pod 中重新装载

如果删除了包含 Azure 文件装载的 Windows pod，然后计划在同一节点上重新创建，装载将失败。 此失败的原因是由于 Azure 文件装载已装载到节点上，导致 `New-SmbGlobalMapping` 命令失败。

例如，你可能会看到类似于以下内容的错误：

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

此问题已在 Kubernetes 的以下版本中得到解决：

| Kubernetes 版本 | 已修复版本 |
| -- | :--: |
| 1.12 | 1.12.6 或更高版本 |
| 1.13 | 1.13.4 或更高版本 |
| 1.14 及更高版本 | 不适用 |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>由于存储帐户密钥已更改，Azure 文件装载失败

如果你的存储帐户密钥已更改，你可能会看到 Azure 文件装载失败。

可以通过使用 base64 编码的存储帐户密钥在 Azure 文件机密中手动手动更新*azurestorageaccountkey*字段，来缓解此问题。

若要以 base64 编码存储帐户密钥，可以使用 `base64`。 例如：

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

若要更新 Azure 密钥文件，请使用 `kubectl edit secret`。 例如：

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

几分钟后，代理节点将使用更新的存储密钥重试 azure 文件装入。
