---
title: Azure 库伯奈斯服务 （AKS） 中 Windows 服务器节点池的限制
description: 在 Azure Kubernetes 服务 （AKS） 中运行 Windows Server 节点池和应用程序工作负载时，了解已知的限制
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: f4e9f63d0da1797b92c123034e6775f5b07bd4b3
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366401"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure 库伯奈斯服务 （AKS） 中 Windows 服务器节点池和应用程序工作负载的当前限制

在 Azure 库伯奈斯服务 （AKS） 中，您可以创建一个节点池，该节点池在节点上作为来宾操作系统运行 Windows 服务器。 这些节点可以运行本机 Windows 容器应用程序，例如在 .NET 框架上构建的应用程序。 由于 Linux 和 Windows 操作系统在提供容器支持的方式上存在重大差异，因此一些常见的 Kubernetes 和 pod 相关功能目前不适用于 Windows 节点池。

本文概述了 AKS 中 Windows 服务器节点的一些限制和操作系统概念。 Windows 服务器的节点池当前处于预览状态。

> [!IMPORTANT]
> AKS 预览功能是自助式选择加入功能。 预览版“按原样”提供，并且仅在“可用情况下”提供，不包含在服务级别协议和有限保障中。 AKS 预览版的内容部分包含在客户支持中，我们只能尽力提供支持。 因此，这些功能不应用于生产。 有关详细信息，请参阅以下支持文章：
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>支持哪些 Windows 操作系统？

AKS 使用 Windows Server 2019 作为主机操作系统版本，仅支持进程隔离。 不支持使用其他 Windows 服务器版本生成的容器映像。 [Windows 容器版本兼容性][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>库伯内特在Windows和Linux上有什么不同吗？

窗口服务器节点池支持包括一些限制，这些限制是 Kubernetes 项目中上游 Windows Server 的一部分。 这些限制并非特定于 AKS。 有关库伯内斯中 Windows 服务器的此上游支持的详细信息，请参阅[Kubernetes 项目中 Kubernetes][intro-windows]文档中支持 Windows 支持简介的功能[和限制][upstream-limitations]部分。

库伯内斯在历史上以Linux为中心。 上游[Kubernetes.io][kubernetes]网站中使用的许多示例都用于 Linux 节点。 创建使用 Windows Server 容器的部署时，操作系统级别的以下注意事项适用：

- **标识**- Linux 通过整数用户标识符 （UID） 标识用户。 用户还有用于登录的字母数字用户名，Linux 将该用户名转换为用户的 UID。 同样，Linux 通过整数组标识符 （GID） 标识用户组，并将组名称转换为相应的 GID。
    - Windows 服务器使用存储在 Windows 安全访问管理器 （SAM） 数据库中的较大二进制安全标识符 （SID）。 此数据库不会在主机和容器之间或容器之间共享。
- **文件权限**- Windows 服务器使用基于 S ID 的访问控制列表，而不是权限和 UID_GID 的位掩码
- **文件路径**- Windows 服务器上的约定是使用 * 而不是 /。
    - 在装载卷的窗格规范中，正确指定 Windows Server 容器的路径。 例如，在 Linux 容器中指定驱动器号和位置（如 */K/Volume）* 以装载为*K：* 驱动器，而不是 Linux 容器中的 */mnt/卷*的装载点。

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Windows 支持哪种磁盘？

Azure 磁盘和 Azure 文件是受支持的卷类型，在 Windows Server 容器中作为 NTFS 卷访问。

## <a name="can-i-run-windows-only-clusters-in-aks"></a>是否可以仅在 AKS 中运行 Windows 群集？

AKS 群集中的主节点（控制平面）由 AKS 服务托管，您不会接触到托管主组件的节点的操作系统。 所有 AKS 群集都使用默认的第一个节点池创建，该节点池基于 Linux。 此节点池包含群集正常运行所需的系统服务。 建议在第一个节点池中至少运行两个节点，以确保群集的可靠性和进行群集操作的能力。 除非 AKS 群集本身被删除，否则无法删除第一个基于 Linux 的节点池。

## <a name="what-network-plug-ins-are-supported"></a>支持哪些网络插件？

具有 Windows 节点池的 AKS 群集必须使用 Azure CNI（高级）网络模型。 不支持 Kubenet（基本）网络。 有关网络模型差异的详细信息，请参阅[AKS 中应用程序的网络概念][azure-network-models]。 - Azure CNI 网络模型需要额外的 IP 地址管理规划和注意事项。 有关如何规划和实现 Azure CNI 的详细信息，请参阅在[AKS 中配置 Azure CNI 网络][configure-azure-cni]。

## <a name="can-i-change-the-max--of-pods-per-node"></a>我可以更改最大值吗？每个节点的窗格数？

是的。 有关可用的含义和选项，请参阅[最大窗格数][maximum-number-of-pods]。

## <a name="how-do-patch-my-windows-nodes"></a>如何修补我的 Windows 节点？

必须*升级*AKS 中的 Windows 服务器节点以获取最新的修补程序和更新。 在 AKS 中的节点上未启用 Windows 更新。 AKS 在修补程序可用后立即发布新的节点池映像，客户有责任升级节点池以在修补程序和修补程序上保持最新状态。 对于正在使用的库伯内斯版本也是如此。 AKS 发行说明将指示新版本何时可用。 有关升级 Windows 服务器节点池的详细信息，请参阅[升级 AKS 中的节点池][nodepool-upgrade]。

> [!NOTE]
> 仅当在升级节点池之前执行群集升级（控制平面升级）时，才会使用更新的 Windows Server 映像
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>如何旋转 Windows 节点池的服务主体？

在预览期间，Windows 节点池不支持服务主体旋转作为预览限制。 为了更新服务主体，请创建新的 Windows 节点池，并将您的窗格从较旧的池迁移到新池。 完成此任务后，删除较旧的节点池。

## <a name="how-many-node-pools-can-i-create"></a>我可以创建多少个节点池？

AKS 群集最多可以有 10 个节点池。 在这些节点池中最多可以有 1000 个节点。 [节点池限制][nodepool-limitations]。

## <a name="what-can-i-name-my-windows-node-pools"></a>我可以为 Windows 节点池命名什么？

您必须将名称保留为最多 6 （6） 个字符。 这是 AKS 的当前限制。

## <a name="are-all-features-supported-with-windows-nodes"></a>Windows 节点支持所有功能吗？

Windows 节点当前不支持网络策略和 kubenet。 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>是否可以在 Windows 节点上运行入口控制器？

是的，支持 Windows Server 容器的入口控制器可以在 AKS 中的 Windows 节点上运行。

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>是否可以将 Azure 开发空间与 Windows 节点一起使用？

Azure 开发人员空间当前仅适用于基于 Linux 的节点池。

## <a name="can-my-windows-server-containers-use-gmsa"></a>我的 Windows 服务器容器可以使用 gMSA 吗？

组托管服务帐户 （gMSA） 支持当前不在 AKS 中。

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>是否可以将 Azure 监视器用于具有 Windows 节点和容器的容器？

可以，但是 Azure 监视器不会从 Windows 容器收集日志（stdout）。 您仍然可以从 Windows 容器附加到固定日志的实时流。

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>如果我需要不支持的功能，该怎么办？

我们努力将您需要的所有功能带到 AKS 中的 Windows，但如果您确实遇到差距，开源的上游[aks 引擎][aks-engine]项目提供了一种简单且完全可自定义的方式在 Azure 中运行 Kubernetes，包括 Windows 支持。 请务必查看我们的[AKS 路线图功能路线图][aks-roadmap]。

## <a name="next-steps"></a>后续步骤

要开始在 AKS 中的 Windows 服务器容器，[请创建一个在 AKS 中运行 Windows 服务器的节点池][windows-node-cli]。

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
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
