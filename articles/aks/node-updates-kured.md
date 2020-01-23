---
title: 用 Azure Kubernetes Service （AKS）中的 kured 更新和重新启动 Linux 节点
description: 了解如何更新 Linux 节点并通过 Azure Kubernetes 服务（AKS）中的 kured 自动重新启动它们
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 02/28/2019
ms.author: mlearned
ms.openlocfilehash: c9e7c23806d4a0a0e2c0b36122d9eb087c986556
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2020
ms.locfileid: "76549166"
---
# <a name="apply-security-and-kernel-updates-to-linux-nodes-in-azure-kubernetes-service-aks"></a>在 Azure Kubernetes Service （AKS）中将安全和内核更新应用于 Linux 节点

为了保护群集，安全更新会自动应用到 AKS 中的 Linux 节点。 这些更新包括 OS 安全修复项或内核更新。 其中的部分更新需要重启节点才能完成更新进程。 AKS 不会自动重启这些 Linux 节点来完成更新过程。

保持 Windows Server 节点（当前在 AKS 中处于预览状态）的过程稍有不同。 Windows Server 节点不接收每日更新。 相反，你执行 AKS 升级，该升级使用最新的基本窗口服务器映像和修补程序来部署新节点。 对于使用 Windows Server 节点的 AKS 群集，请参阅[在 AKS 中升级节点池][nodepool-upgrade]。

本文介绍如何使用开源[kured （KUbernetes REboot 守护程序）][kured]来监视需要重新启动的 Linux 节点，然后自动处理正在运行的 pod 和节点重新启动过程的重新计划。

> [!NOTE]
> `Kured` 是 Weaveworks 提供的一个开源项目。 我们尽可能地在 AKS 中提供对该项目的支持。 可在 #weave 社区可宽

## <a name="before-you-begin"></a>开始之前

本文假定你拥有现有的 AKS 群集。 如果需要 AKS 群集，请参阅 AKS 快速入门，并[使用 Azure CLI][aks-quickstart-cli]或[使用 Azure 门户][aks-quickstart-portal]。

还需要安装并配置 Azure CLI 版本2.0.59 或更高版本。 运行  `az --version` 即可查找版本。 如果需要安装或升级，请参阅 [安装 Azure CLI][install-azure-cli]。

## <a name="understand-the-aks-node-update-experience"></a>了解 AKS 节点更新体验

在 AKS 群集中，Kubernetes 节点作为 Azure 虚拟机 (VM) 运行。 这些基于 Linux 的虚拟机使用 Ubuntu 映像，其 OS 配置为每晚自动检查更新。 如果有可用的安全更新或内核更新，则会自动下载并进行安装。

![使用 kured 进行的 AKS 节点更新和重启进程](media/node-updates-kured/node-reboot-process.png)

部分安全更新（如内核更新）需要重启节点才能完成更新进程。 需要重新启动的 Linux 节点会创建一个名为 " */var/run/reboot-required*" 的文件。 此重启进程不会自动进行。

你可以使用自己的工作流和进程来重启节点，或使用 `kured` 安排该进程。 在 `kured`中，部署了[DaemonSet][DaemonSet] ，用于在群集中的每个 Linux 节点上运行 pod。 DaemonSet 中的这些 pod 会监视 */var/run/reboot-required*文件是否存在，然后启动一个进程来重新启动节点。

### <a name="node-upgrades"></a>节点升级

AKS 中还有额外的进程，可通过该进程升级群集。 升级通常是指移动到 Kubernetes 的较新版本，而不仅是应用节点安全更新。 AKS 升级执行以下操作：

* 部署新节点，应用最新的安全更新和 Kubernetes 版本。
* 封锁并排除旧节点。
* 将 Pod 调度到新节点上。
* 删除旧节点。

在升级事件期间，无法继续使用同一 Kubernetes 版本。 必须指定 Kubernetes 的较新版本。 若要升级到 Kubernetes 的最新版本，可以[升级 AKS 群集][aks-upgrade]。

## <a name="deploy-kured-in-an-aks-cluster"></a>在 AKS 群集中部署 kured

要部署 `kured` DaemonSet，请从以下示例 YAML 清单的 GitHub 项目页应用它们。 此清单创建角色、群集角色、绑定和服务帐户，然后使用支持 AKS 群集 1.9 或更高版本的 `kured` 1.1.0 版部署 DaemonSet。

```console
kubectl apply -f https://github.com/weaveworks/kured/releases/download/1.2.0/kured-1.2.0-dockerhub.yaml
```

也可以为 `kured` 配置其他参数，例如与 Prometheus 或 Slack 集成。 有关其他配置参数的详细信息，请参阅[kured 安装文档][kured-install]。

## <a name="update-cluster-nodes"></a>更新群集节点

默认情况下，AKS 中的 Linux 节点每晚上检查更新。 如果不想等待，可以手动执行更新以检查 `kured` 是否正常运行。 首先，按照以下步骤将[SSH 连接到某个 AKS 节点][aks-ssh]。 与 Linux 节点建立 SSH 连接后，检查更新并将其应用如下：

```console
sudo apt-get update && sudo apt-get upgrade -y
```

如果所应用的更新需要重启节点，则会将文件写入 /var/run/reboot-required。 默认情况下，`Kured` 每 60 分钟检查一次需要重启的节点。

## <a name="monitor-and-review-reboot-process"></a>监视和查看重启进程

如果 DaemonSet 中的某个副本检测到某个节点需要重启，系统将通过 Kubernetes API 对该节点进行锁定。 该锁定可防止将其他 pod 调度到此节点上。 该锁定还指示一次只应重启一个节点。 封锁节点后，将从节点中排除运行中的 pod，并重启节点。

可以使用[kubectl get 节点][kubectl-get-nodes]命令来监视节点的状态。 以下示例输出显示了一个在节点准备进行重启时状态为 SchedulingDisabled 的节点：

```
NAME                       STATUS                     ROLES     AGE       VERSION
aks-nodepool1-28993262-0   Ready,SchedulingDisabled   agent     1h        v1.11.7
```

更新过程完成后，可以使用[kubectl get 节点][kubectl-get-nodes]命令和 `--output wide` 参数查看节点的状态。 通过此附加输出，可发现基础节点的 KERNEL-VERSION 会有所差异，如以下示例输出所示。 *Aks-nodepool1-28993262-0*已在上一步中更新，并显示内核版本 4.15.0- *1039-azure*。 尚未更新的节点*aks nodepool1-1*显示内核版本*4.15.0-1037*。

```
NAME                       STATUS    ROLES     AGE       VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-28993262-0   Ready     agent     1h        v1.11.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS   4.15.0-1039-azure   docker://3.0.4
aks-nodepool1-28993262-1   Ready     agent     1h        v1.11.7   10.240.0.5    <none>        Ubuntu 16.04.6 LTS   4.15.0-1037-azure   docker://3.0.4
```

## <a name="next-steps"></a>后续步骤

本文详细介绍了如何使用 `kured` 在安全更新过程中自动重新启动 Linux 节点。 若要升级到 Kubernetes 的最新版本，可以[升级 AKS 群集][aks-upgrade]。

对于使用 Windows Server 节点的 AKS 群集，请参阅[在 AKS 中升级节点池][nodepool-upgrade]。

<!-- LINKS - external -->
[kured]: https://github.com/weaveworks/kured
[kured-install]: https://github.com/weaveworks/kured#installation
[kubectl-get-nodes]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[DaemonSet]: concepts-clusters-workloads.md#statefulsets-and-daemonsets
[aks-ssh]: ssh.md
[aks-upgrade]: upgrade-cluster.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
