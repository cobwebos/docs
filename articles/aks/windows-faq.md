---
title: Windows Server 节点池常见问题解答
titleSuffix: Azure Kubernetes Service
description: 查看在 Azure Kubernetes 服务 (AKS) 中运行 Windows Server 节点池和应用程序工作负载时的常见问题。
services: container-service
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: 00e749a8b066f72518b38685dd7a7779e406cf74
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013961"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>AKS 中 Windows Server 节点池的常见问题

在 Azure Kubernetes 服务 (AKS) 中，可以创建在节点上将 Windows Server 作为来宾 OS 运行的节点池。 这些节点可以运行本机 Windows 容器应用程序，例如基于 .NET Framework 生成的应用程序。 Linux 和 Windows 操作系统提供容器支持的方式存在差异。 有些常见的 Linux Kubernetes 和 Pod 相关的功能目前无法用于 Windows 节点池。

本文概述了 AKS 中 Windows Server 节点的一些常见问题和操作系统概念。

## <a name="which-windows-operating-systems-are-supported"></a>支持哪些 Windows 操作系统？

AKS 使用 Windows Server 2019 作为主机 OS 版本，并且仅支持进程隔离。 不支持使用其他 Windows Server 版本生成的容器映像。 有关详细信息，请参阅 [Windows 容器版本兼容性][windows-container-compat]。

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Kubernetes 在 Windows 和 Linux 上是否有所不同？

Windows Server 节点池支持具有一些限制，Kubernetes 项目中的上游 Windows Server 也具有这些限制。 这些限制并不特定于 AKS。 有关 Kubernetes 中的 Windows Server 上游支持的详细信息，请参阅 Kubernetes 项目的 [Kubernetes 中的 Windows 支持简介][intro-windows]文档的[支持的功能和限制][upstream-limitations]部分。

Kubernetes 历来以 Linux 为中心。 上游 [Kubernetes.io][kubernetes] 网站中使用的许多示例旨在用于 Linux 节点。 创建使用 Windows Server 容器的部署时，OS 级别的以下注意事项适用：

- **标识** - Linux 通过整数用户标识符 (UID) 标识用户。 用户还具有用于登录的字母数字用户名，Linux 会将其转换为用户的 UID。 同样，Linux 通过整数组标识符 (GID) 标识用户组，并将组名转换为其相应的 GID。
    - Windows Server 使用更大的二进制安全标识符 (SID)，该标识符存储在 Windows Security Access Manager (SAM) 数据库中。 此数据库不在主机与容器之间或容器之间共享。
- **文件权限** - Windows Server 使用基于 SID 的访问控制列表，而不是权限和 UID + GID 的位掩码
- **文件路径** - Windows Server 上的约定是使用 \，而不是 /。
    - 在装载卷的 Pod 规范中，为 Windows Server 容器正确指定路径。 例如，不要在 Linux 容器中指定装入点 /mnt/volume，而是将要装载的驱动器号和位置（例如 */K/Volume*）指定为 K: 驱动器。

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows 支持哪种磁盘？

Azure 磁盘和 Azure 文件存储是受支持的卷类型。 它们在 Windows Server 容器中是作为 NTFS 卷访问的。

## <a name="can-i-run-windows-only-clusters-in-aks"></a>是否可以在 AKS 中运行仅限 Windows 的群集？

AKS 群集中的主节点（控制平面）由 AKS 服务托管，不会向你公开托管主组件的节点的操作系统。 所有的 AKS 群集在创建时都带有默认的第一个节点池（基于 Linux）。 此节点池包含系统服务，群集需要这些服务才能运行函数。 建议在第一个节点池中至少运行两个节点，以确保群集的可靠性和执行群集操作的能力。 除非删除 AKS 群集本身，否则无法删除第一个基于 Linux 的节点池。

## <a name="how-do-i-patch-my-windows-nodes"></a>如何修补 Windows 节点？

若要获取用于 Windows 节点的最新补丁，可以[升级节点池][nodepool-upgrade]或[升级节点映像][upgrade-node-image]。 AKS 中的节点上未启用 Windows 更新。 一有补丁可用，AKS 就会发布新节点池映像，用户自己负责升级节点池来保持使用最新的补丁和修补程序。 对于所使用的 Kubernetes 版本也是如此。 [AKS 发行说明][aks-release-notes]将指示何时有新版本可用。 若要详细了解如何升级整个 Windows Server 节点池，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。 如果只需要更新节点映像，请参阅 [AKS 节点映像升级][upgrade-node-image]。

> [!NOTE]
> 只有在升级节点池之前已经执行了群集升级（控制平面升级）的情况下，才会使用更新的 Windows Server 映像。
>

## <a name="what-network-plug-ins-are-supported"></a>支持哪些网络插件？

具有 Windows 节点池的 AKS 群集必须使用 Azure CNI（高级）网络模型。 不支持 Kubenet（基本）网络。 有关网络模型差异的详细信息，请参阅[适用于 AKS 中的应用程序的网络概念][azure-network-models]。 Azure CNI 网络模型需要对 IP 地址管理进行其他规划和考量。 有关如何规划和实现 Azure CNI 的详细信息，请参阅[在 AKS 中配置 Azure CNI 网络][configure-azure-cni]。

## <a name="is-preserving-the-client-source-ip-supported"></a>是否支持保留客户端源 IP？

目前，Windows 节点不支持[客户端源 IP 保留][client-source-ip]。

## <a name="can-i-change-the-max--of-pods-per-node"></a>是否可以更改每个节点的最大 Pod 数量？

是的。 有关影响和可用选项，请参阅[最大 Pod 数][maximum-number-of-pods]。

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>尝试创建新的 Windows 代理池时，为什么会发生错误？

如果群集是在 2020 年 2 月之前创建的，且从未执行过任何群集升级操作，该群集仍将使用旧的 Windows 映像。 你可能会看到类似于以下内容的错误：

“找不到从部署模板中引用的下列映像:发布者：MicrosoftWindowsServer，产品/服务：WindowsServer, Sku:2019-datacenter-core-smalldisk-2004，版本：最新版本。 有关如何查找可用映像的说明，请参阅 https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage 。”

修复此错误的方法：

1. 升级[群集控制平面][upgrade-cluster-cp]以更新映像产品/服务和发布者。
1. 创建新的 Windows 代理池。
1. 将 Windows Pod 从现有 Windows 代理池移动到新的 Windows 代理池。
1. 删除旧的 Windows 代理池。

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>如何轮换 Windows 节点池的服务主体？

Windows 节点池不支持服务主体轮换。 若要更新服务主体，请创建新的 Windows 节点池，并将 Pod 从旧池迁移到新池。 完成此操作后，删除旧节点池。

请改为使用托管标识，它们本质上是服务主体的包装器。 有关详细信息，请参阅[在 Azure Kubernetes 服务中使用托管标识][managed-identity]。

## <a name="how-many-node-pools-can-i-create"></a>我可以创建多少个节点池？

AKS 群集最多可以包含 10 个节点池。 这些节点池中最多可以有 1000 个节点。 [节点池限制][nodepool-limitations]。

## <a name="what-can-i-name-my-windows-node-pools"></a>我可以如何为 Windows 节点池命名？

必须将名称保持在最多 6（六）个字符。 这是 AKS 的当前限制。

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows 节点是否支持所有功能？

Windows 节点当前不支持网络策略和 Kubenet。

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>我是否可以在 Windows 节点上运行入口控制器？

是，支持 Windows Server 容器的入口控制器可以在 AKS 中的 Windows 节点上运行。

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>我是否可以将 Azure Dev Spaces 与 Windows 节点配合使用？

Azure Dev Spaces 当前仅可用于基于 Linux 的节点池。

## <a name="can-my-windows-server-containers-use-gmsa"></a>我的 Windows Server 容器是否可以使用 gMSA？

AKS 当前不提供组托管服务帐户 (gMSA) 支持。

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>是否可以将 Azure Monitor 用于包含 Windows 节点和容器的容器？

可以，但 Azure Monitor 现为公共预览版，用于从 Windows 容器收集日志（stdout，stderr）和指标。 你仍可从 Windows 容器附加到 stdout 日志的实时传送流。

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>在具有 Windows 节点的群集上，服务的数量是否有限制？

具有 Windows 节点的群集可以有大约 500 个服务，超过它就会导致端口耗尽。

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>是否可以将 Azure 混合权益用于 Windows 节点？

是的。 Windows Server Azure 混合权益通过使你能够将本地 Windows Server 许可证用于 AKS Windows 节点，降低了运营成本。

可以在整个 AKS 群集或单个节点上使用 Azure 混合权益。 对于各个节点，你需要导航到 [节点资源组][resource-groups] ，并直接将 Azure 混合权益应用到节点。 有关将 Azure 混合权益应用到各个节点的详细信息，请参阅 [Windows Server Azure 混合权益][hybrid-vms]。 

若要在新 AKS 群集上使用 Azure 混合权益，请使用 `--enable-ahub` 参数。

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

若要在现有 AKS 群集上使用 Azure 混合权益，请使用参数更新群集 `--enable-ahub` 。

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

若要检查是否在群集上设置了 Azure 混合权益，请使用以下命令：

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

如果 Azure 混合权益启用了群集，则的输出 `az vmss show` 将如下所示：

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>是否可以将 Kubernetes Web 仪表板用于 Windows 容器？

是的，你可以使用 [Kubernetes Web 仪表板][kubernetes-dashboard]来访问有关 Windows 容器的信息，但目前不能直接从 Kubernetes Web 仪表板将 kubectl exec 运行到正在运行的 Windows 容器中。 若要详细了解如何连接到正在运行的 Windows 容器，请参阅[使用 RDP 连接到 Azure Kubernetes 服务 (AKS) 群集 Windows Server 节点以进行维护或故障排除][windows-rdp]。

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>如果需要使用不受支持的功能，应该怎么办？

我们致力于在 AKS 中引入你需要的所有 Windows 功能，但如果确实遇到功能差距，开源的上游 [aks-engine][aks-engine] 项目提供了在 Azure 中运行 Kubernetes 的完全可自定义的简便方法，其中包括 Windows 支持。 请确保查看即将推出的 [AKS 路线图][aks-roadmap]中的功能路线图。

## <a name="next-steps"></a>后续步骤

若要开始在 AKS 中使用 Windows Server 容器，[请创建一个在 AKS 中运行 Windows Server 的节点池][windows-node-cli]。

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/insights/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
