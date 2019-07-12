---
title: 操作员最佳做法 - Azure Kubernetes 服务 (AKS) 中的群集安全性
description: 了解有关如何在 Azure Kubernetes 服务 (AKS) 中管理群集安全性和升级的群集操作员最佳做法
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mlearned
ms.openlocfilehash: d4a77fc1756b0fa9decb6d3a84760beb1e700863
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614897"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>有关 Azure Kubernetes 服务 (AKS) 中的群集安全性和升级的最佳做法

在 Azure Kubernetes 服务 (AKS) 中管理群集时，关键是要确保工作负荷和数据的安全性。 特别是在使用逻辑隔离运行多租户群集时，需要保护对资源和工作负荷的访问。 为了尽量减少攻击风险，还需确保应用最新的 Kubernetes 和节点 OS 安全更新。

本文重点介绍如何保护 AKS 群集。 学习如何：

> [!div class="checklist"]
> * 使用 Azure Active Directory 和基于角色的访问控制来保护 API 服务器访问
> * 保护容器对节点资源的访问
> * 将 AKS 群集升级到最新的 Kubernetes 版本
> * 使节点保持最新状态并自动应用安全修补程序

您还可以阅读的最佳实践[容器映像管理][best-practices-container-image-management] and for [pod security][best-practices-pod-security]。

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>保护对 API 服务器和群集节点的访问

**最佳做法指南** - 若要保护群集，保护对 Kubernetes API 服务器的访问是你能够做的最重要的事情之一。 将 Kubernetes 基于角色的访问控制 (RBAC) 与 Azure Active Directory 集成，以控制对 API 服务器的访问。 借助这些控制，可以像保护对 Azure 订阅的访问一样保护 AKS。

Kubernetes API 服务器为在群集中执行操作的请求提供单一连接点。 若要保护和审核对 API 服务器的访问，请限制访问权限并提供所需的最低特权访问权限。 这种方法并不是 Kubernetes 独有的，但它在将 AKS 群集进行逻辑隔离以供多租户使用时特别重要。

Azure Active Directory (AD) 提供可与 AKS 群集集成的企业级标识管理解决方案。 由于 Kubernetes 不提供标识管理解决方案，因此，若不与 Azure AD 集成，可能很难以精细的方式限制对 API 服务器的访问。 借助 AKS 中与 Azure AD 集成的群集，可以使用现有用户和组帐户向 API 服务器验证用户身份。

![用于 AKS 群集的 Azure Active Directory 集成](media/operator-best-practices-cluster-security/aad-integration.png)

通过使用 Kubernetes RBAC 和 Azure AD 集成，可保护 API 服务器并提供限定范围的资源集（例如单个命名空间）所需的最少权限。 可以向 Azure AD 中不同的用户或组授予不同的 RBAC 角色。 借助这些细化的权限，可以限制对 API 服务器的访问，并提供已执行操作的清晰审核线索。

建议的最佳做法是使用组（而非单个标识）提供对文件和文件夹的访问，使用 Azure AD *组*成员身份将用户绑定到 RBAC 角色，而不是将单个*用户*绑定到 RBAC 角色。 当某个用户的组成员身份发生变化时，该用户对 AKS 群集的访问权限也会相应发生变化。 如果将该用户直接绑定到某个角色，则其工作职能可能会发生变化。 Azure AD 组成员身份会更新，但这一更新不会反映在对 AKS 群集的权限上。 在这种情况下，该用户最终被授予的权限将超过一个用户所需的权限。

有关 Azure AD 的集成和 RBAC 的详细信息，请参阅[身份验证和授权在 AKS 中的最佳做法][aks-best-practices-identity]。

## <a name="secure-container-access-to-resources"></a>保护容器对资源的访问

**最佳做法指南** - 限制对容器可以执行的操作的访问。 提供最少的权限，并避免使用 root/特权提升。

与应该向用户或组授予所需最少权限的方式一样，也应将容器限制为只能访问它们所需的操作和进程。 为了尽量减少攻击风险，请勿配置需要提升的权限或 root 访问权限的应用程序和容器。 例如，在 Pod 清单中设置 `allowPrivilegeEscalation: false`。 这些 *Pod 安全性上下文*内置于 Kubernetes 中，可用于定义其他权限（例如要以其身份运行的用户或组）或者要公开的 Linux 功能。 有关更多的最佳实践，请参阅[对资源的安全 pod 访问][pod-security-contexts]。

若要更精确地控制容器操作，还可以使用内置 Linux 安全功能，例如 *AppArmor* 和 *seccomp*。 这些功能在节点级别定义，然后通过 Pod 清单实现。 内置 Linux 安全功能才适用于 Linux 节点和 pod。

> [!NOTE]
> AKS 或其他位置中的 Kubernetes 环境并不完全安全，因为可能存在恶意的多租户使用情况。 用于节点的其他安全功能（如 *AppArmor*、*seccomp*、*Pod 安全策略*或更细粒度的基于角色的访问控制 (RBAC)）可增加攻击的难度。 但是，为了在运行恶意多租户工作负荷时获得真正的安全性，虚拟机监控程序应是你唯一信任的安全级别。 Kubernetes 的安全域成为整个群集，而不是单个节点。 对于这些类型的恶意多租户工作负荷，应使用物理隔离的群集。

### <a name="app-armor"></a>App Armor

若要限制容器可以执行的操作，可以使用[AppArmor][k8s-apparmor] Linux 内核安全模块。 AppArmor 作为基础 AKS 节点 OS 的一部分提供，默认情况下处于启用状态。 可以创建 AppArmor 配置文件来限制读取、写入或执行等操作或者装载文件系统等系统功能。 默认 AppArmor 配置文件限制对各种 `/proc` 和 `/sys` 位置的访问，并提供一种在逻辑上将容器与基础节点隔离的方法。 AppArmor 适用于 Linux 上运行的任何应用程序，而不仅仅是 Kubernetes Pod。

![AKS 群集中用来限制容器操作的 AppArmor 配置文件](media/operator-best-practices-container-security/apparmor.png)

为了通过实际操作了解 AppArmor，以下示例将创建一个阻止写入文件的配置文件。 [SSH][aks-ssh]到 AKS 节点，然后创建名为的文件*拒绝 write.profile*并粘贴以下内容：

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

使用 `apparmor_parser` 命令添加 AppArmor 配置文件。 将配置文件添加到 AppArmor 并指定在上一步中创建的配置文件的名称：

```console
sudo apparmor_parser deny-write.profile
```

如果正确分析配置文件并将其应用于 AppArmor，则不会返回任何输出。 你将返回到命令提示符处。

在本地计算机上，现在创建一个名为 *aks-apparmor.yaml* 的 Pod 清单并粘贴以下内容。 此清单用于为 `container.apparmor.security.beta.kubernetes` 定义注释并引用在前面的步骤中创建的 *deny-write* 配置文件：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

部署示例 pod 使用[kubectl 应用][kubectl-apply]命令：

```console
kubectl apply -f aks-apparmor.yaml
```

使用部署的 pod，使用[kubectl exec][kubectl-exec]命令写入到文件。 系统无法执行该命令，如以下示例输出所示：

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

AppArmor 有关详细信息，请参阅[AppArmor 在 Kubernetes 中的配置文件][k8s-apparmor]。

### <a name="secure-computing"></a>安全计算

AppArmor 适用于任何 Linux 应用程序中，虽然[seccomp (*sec*在此*comp*uting)][seccomp]在进程级别工作。 Seccomp 也是一个 Linux 内核安全模块，并由 AKS 节点所用的 Docker 运行时提供本机支持。 Seccomp 可限制容器可以执行的进程调用。 你可以创建筛选器来定义要允许或拒绝的操作，然后使用 Pod YAML 清单中的注释与 seccomp 筛选器进行关联。 这符合仅授予容器运行所需的最少权限（不授予更多权限）的最佳做法。

为了通过实际操作了解 seccomp，请创建一个筛选器来阻止更改文件权限。 [SSH][aks-ssh]到 AKS 节点，然后创建名为 seccomp 筛选器 */var/lib/kubelet/seccomp/prevent-chmod*并粘贴以下内容：

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

在本地计算机上，现在创建一个名为 *aks-seccomp.yaml* 的 Pod 清单并粘贴以下内容。 此清单用于为 `seccomp.security.alpha.kubernetes.io` 定义注释并引用在上一步中创建的 *prevent-chmod* 筛选器：

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

部署示例 pod 使用[kubectl 应用][kubectl-apply]命令：

```console
kubectl apply -f ./aks-seccomp.yaml
```

查看使用 pod 的状态[kubectl get pod][kubectl-get]命令。 该 Pod 报告一个错误。 Seccomp 筛选器阻止 `chmod` 命令运行，如以下示例输出所示：

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

有关可用的筛选器的详细信息，请参阅[Seccomp 安全配置文件的 Docker][seccomp]。

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>定期更新到最新的 Kubernetes 版本

**最佳做法指南** - 若要及时了解新功能和 bug 修复，请定期升级到 AKS 群集中的 Kubernetes 版本。

与更传统的基础结构平台相比，Kubernetes 发布新功能的速度更快。 Kubernetes 更新包括新功能和 bug 或安全修补程序。 新功能通常会在经历 *alpha*、*beta* 状态后变得*稳定*，这时便可公开发布，并建议用于生产环境中。 在此发布周期内，可对 Kubernetes 进行更新，而不会经常遇到中断性变更，也无需调整部署和模板。

AKS 支持四个 Kubernetes 次要版本。 这意味着，在引入新的次要修补程序版本后，将停止对最早次要版本和修补程序版本的支持。 系统会定期更新 Kubernetes 的次要版本。 请确保设置一个根据需要进行检查和升级的管理流程，以免失去支持。 有关详细信息，请参阅[支持 Kubernetes 版本 AKS][aks-supported-versions]

若要检查可用于群集的版本，请使用[az aks get-升级][az-aks-get-upgrades]命令，在下面的示例所示：

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

你可以升级 AKS 群集使用[az aks 升级][az-aks-upgrade]命令。 升级过程会以安全的方式逐一封锁并清空节点，在剩余的节点上计划 Pod，然后部署一个运行最新 OS 和 Kubernetes 版本的新节点。

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.8
```

在 AKS 中升级的详细信息，请参阅[在 AKS 中的支持 Kubernetes 版本][aks-supported-versions] and [Upgrade an AKS cluster][aks-upgrade]。

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>进程 Linux 节点更新并重新启动使用 kured

**最佳实践指南**-AKS 自动下载并安装安全修补程序对每个 Linux 节点，但不是会自动重新启动如有必要。 使用 `kured` 监视挂起的重启操作，然后安全地封锁并排空节点以允许节点重启，应用更新并尽可能安全地保护 OS。 Windows Server 的节点 （目前以预览版在 AKS 中），定期执行的 AKS 升级操作，可以安全地 cordon 和清空 pod 和部署已更新的节点。

每个晚上在 AKS 中的 Linux 节点获取安全修补程序可通过其发行版更新通道。 当在 AKS 群集中部署节点时，会​​自动配置此行为。 为了尽量减少对正在运行的工作负荷的中断和潜在影响，AKS 不会在安全修补程序或内核更新需要进行重启时自动重启节点。

开放源代码[kured （KUbernetes 重新启动守护程序）][kured] Weaveworks 项目监视是否挂起的节点重新启动。 在 Linux 节点应用时需要重启的更新，该节点是安全地封锁和排除移动并计划在群集中其他节点上的 pod。 重启节点后，会将其重新添加到群集中，Kubernetes 将继续在该节点上计划 Pod。 为了尽量减少中断，`kured` 一次只允许重启一个节点。

![使用 kured 的 AKS 节点重启过程](media/operator-best-practices-cluster-security/node-reboot-process.png)

如果希望以更精细的粒度控制何时进行重启，`kured` 可以与 Prometheus 集成，以防止在出现其他维护事件或群集问题时进行重启。 此集成可在你主动排查其他问题时，最大限度地减少因重启节点而导致的其他复杂情况。

有关如何处理节点重新启动的详细信息，请参阅[应用于 AKS 节点安全性和内核更新][aks-kured]。

## <a name="next-steps"></a>后续步骤

本文重点介绍了如何保护 AKS 群集。 若要实施其中某些做法，请参阅以下文章：

* [Azure Active Directory 与 AKS 集成][aks-aad]
* [升级到最新版本的 Kubernetes AKS 群集][aks-upgrade]
* [进程的安全更新和节点重新启动与 kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
