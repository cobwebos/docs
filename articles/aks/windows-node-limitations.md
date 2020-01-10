---
title: Azure Kubernetes Service （AKS）中 Windows Server 节点池的限制
description: 了解在 Azure Kubernetes Service （AKS）中运行 Windows Server 节点池和应用程序工作负荷时的已知限制
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 3dd7399b68388d92d38b0f64c6e816cb94b3f295
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75768565"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service （AKS）中 Windows Server 节点池和应用程序工作负荷的当前限制

在 Azure Kubernetes Service （AKS）中，可以创建在节点上运行 Windows Server 作为来宾 OS 的节点池。 这些节点可以运行本机 Windows 容器应用程序，如在 .NET Framework 上构建的应用程序。 由于 Linux 和 Windows OS 提供容器支持的方式有很大的不同，因此，一些常见的 Kubernetes 和 pod 相关功能当前不适用于 Windows 节点池。

本文概述了 AKS 中 Windows Server 节点的一些限制和操作系统概念。 Windows Server 的节点池目前处于预览阶段。

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供，并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此，这些功能并不用于生产。 有关其他信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>支持哪些 Windows 操作系统？

AKS 使用 Windows Server 2019 作为主机操作系统版本，仅支持进程隔离。 不支持使用其他 Windows Server 版本生成的容器映像。 [Windows 容器版本兼容性][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Windows 和 Linux 上的 Kubernetes 是否不同？

Windows Server 节点池支持在 Kubernetes 项目中包含属于上游 Windows Server 的一些限制。 这些限制并不特定于 AKS。 有关 Kubernetes 中对 Windows Server 的此上游支持的详细信息，请参阅 Kubernetes 项目中的 Kubernetes 文档[简介中][intro-windows]的[支持的功能和限制][upstream-limitations]部分。

Kubernetes 一直面向 Linux。 上游[Kubernetes.io][kubernetes]网站中使用的很多示例都适用于 Linux 节点。 当你创建使用 Windows Server 容器的部署时，适用于操作系统级别的下列注意事项：

- **标识**-Linux 通过整数用户标识符（UID）来标识用户。 用户还具有用于登录的字母数字用户名，Linux 转换为用户的 UID。 同样，Linux 按整数组标识符（GID）标识用户组，并将组名称转换为其相应的 GID。
    - Windows Server 使用较大的二进制安全标识符（SID），该标识符存储在 Windows 安全访问管理器（SAM）数据库中。 此数据库不会在主机和容器之间共享，也不会在容器之间共享。
- **文件权限**-Windows Server 使用基于 sid 的访问控制列表，而不是权限位掩码和 UID + GID
- **文件路径**-Windows Server 上的约定是使用 \ 而不是/。
    - 在 pod 中装入卷的规格中，正确指定 Windows Server 容器的路径。 例如，请指定驱动器号和位置（如 */K/Volume* ）作为*K：* 驱动器进行装载，而不是在 Linux 容器中指定 */mnt/volume*的装入点。

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows 支持哪种类型的磁盘？

Azure 磁盘和 Azure 文件是支持的卷类型，在 Windows Server 容器中作为 NTFS 卷进行访问。

## <a name="can-i-run-windows-only-clusters-in-aks"></a>能否在 AKS 中运行仅限 Windows 的群集？

AKS 群集中的主节点（控制平面）由 AKS 服务托管，不会向宿主主组件的节点的操作系统公开。 所有 AKS 群集都是使用默认的第一个节点池创建的，它是基于 Linux 的。 此节点池包含群集正常运行所需的系统服务。 建议在第一个节点池中至少运行两个节点，以确保群集的可靠性和执行群集操作的能力。 除非删除 AKS 群集本身，否则不能删除第一个基于 Linux 的节点池。

## <a name="what-network-plug-ins-are-supported"></a>支持哪些网络插件？

使用 Windows 节点池的 AKS 群集必须使用 Azure CNI （高级）网络模型。 Kubenet （基本）网络不受支持。 有关网络模型中的差异的详细信息，请参阅[AKS 中应用程序的网络概念][azure-network-models]。 -Azure CNI 网络模型需要用于 IP 地址管理的其他规划和注意事项。 有关如何规划和实现 Azure CNI 的详细信息，请参阅[Configure AZURE CNI 联网 IN AKS][configure-azure-cni]。

## <a name="can-i-change-the-min--of-pods-per-node"></a>能否更改每个节点的每个节点的最小数目。

目前需要将它设置为最少30个箱，以确保群集的可靠性。

## <a name="how-do-patch-my-windows-nodes"></a>如何修补我的 Windows 节点？

必须*升级*AKS 中的 Windows Server 节点，才能获取最新修补程序和更新。 未在 AKS 中的节点上启用 Windows 更新。 AKS 在修补程序可用时立即发布新的节点池映像，客户负责升级节点池以保持最新的修补程序和修补程序。 对于正在使用的 Kubernetes 版本也是如此。 AKS 发行说明将指示何时有新版本可用。 有关升级 Windows Server 节点池的详细信息，请参阅[在 AKS 中升级节点池][nodepool-upgrade]。

> [!NOTE]
> 仅当在升级节点池之前已执行群集升级（控制平面升级）时，才会使用更新的 Windows Server 映像
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>如何实现轮换 Windows 节点池的服务主体？

在预览期间，Windows 节点池不支持将服务主体轮换作为预览限制。 若要更新服务主体，请创建新的 Windows 节点池，并将其从较旧的池迁移到新池。 完成此工作后，删除旧的节点池。

## <a name="how-many-node-pools-can-i-create"></a>可以创建多少个节点池？

AKS 群集最多可以有8个（8）节点池。 在这些节点池中最多可以有400个节点。 [节点池限制][nodepool-limitations]。

## <a name="what-can-i-name-my-windows-node-pools"></a>我可以将我的 Windows 节点池命名为哪些？

您必须将名称保留为最多6（六）个字符。 这是 AKS 的当前限制。

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows 节点是否支持所有功能？

Windows 节点目前不支持网络策略和 kubenet。 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>能否在 Windows 节点上运行入口控制器？

是的，支持 Windows Server 容器的入口控制器可以在 AKS 中的 Windows 节点上运行。

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>是否可以将 Azure Dev Spaces 用于 Windows 节点？

Azure Dev Spaces 目前仅适用于基于 Linux 的节点池。

## <a name="can-my-windows-server-containers-use-gmsa"></a>我的 Windows Server 容器可以使用 gMSA 吗？

组托管服务帐户（gMSA）支持目前在 AKS 中不可用。

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>能否为包含 Windows 节点和容器的容器使用 Azure Monitor？

是的，但是 Azure Monitor 不从 Windows 容器收集日志（stdout）。 你仍可以从 Windows 容器附加到 stdout 日志的实时流。

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>如果我需要一个不受支持的功能，该怎么办？

我们很难在 AKS 中引入 Windows 所需的所有功能，但如果确实遇到了缺口，则开源的上游[AKS][aks-engine]的项目可提供一种简单且可完全自定义的方式，在 Azure 中运行 Kubernetes，包括 Windows 支持。 请确保查看我们的[AKS 路线图][aks-roadmap]发展路线图。

## <a name="next-steps"></a>后续步骤

若要在 AKS 中开始使用 Windows Server 容器，请[在 AKS 中创建运行 Windows server 的节点池][windows-node-cli]。

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
