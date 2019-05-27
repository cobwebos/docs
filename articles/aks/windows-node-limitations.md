---
title: Windows Server 节点池在 Azure Kubernetes 服务 (AKS) 的限制
description: 在运行 Windows Server 节点池和应用程序工作负荷在 Azure Kubernetes 服务 (AKS) 时了解的已知限制
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: 3d249271995d96307722dadf6b3e012e63565e6a
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956274"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Windows Server 节点池和应用程序工作负荷在 Azure Kubernetes 服务 (AKS) 的当前限制

在 Azure Kubernetes 服务 (AKS) 中，可以创建的节点作为来宾 OS 运行 Windows Server 的节点池。 这些节点可以运行本机 Windows 容器应用程序，如.NET Framework 上构建。 因为有在 Linux 和 Windows OS 如何提供容器支持的主要差异，一些常见的 Kubernetes 和 pod 相关的功能不是当前可用于 Windows 节点池。

本文概述了一些限制和 Windows Server 在 AKS 中的节点的 OS 概念。 适用于 Windows Server 的节点池目前处于预览状态。

> [!IMPORTANT]
> AKS 预览功能是自助服务和可以选择加入的功能。 提供预览是为了从我们的社区收集反馈和 bug。 但是，Azure 技术支持部门不为其提供支持。 如果你创建一个群集，或者将这些功能添加到现有群集，则除非该功能不再为预览版并升级为公开发布版 (GA)，否则该群集不会获得支持。
>
> 如果遇到预览版功能的问题，请[在 AKS GitHub 存储库中提交问题][aks-github]，并在 Bug 标题中填写预览版功能的名称。

## <a name="limitations-for-windows-server-in-kubernetes"></a>在 Kubernetes 中的 Windows Server 的限制

Windows Server 容器必须在基于 Windows 的容器主机上运行。 若要在 AKS 中运行 Windows Server 容器，你可以[创建运行 Windows Server 的节点池][ windows-node-cli]作为来宾 OS。 窗口服务器节点池支持包括属于上游 Kubernetes 项目中的 Windows 服务器的一些限制。 这些限制不是特定于 AKS 的。 在 Kubernetes 中的 Windows Server 的此上游支持的详细信息，请参阅[Kubernetes 限制中的 Windows Server 容器][upstream-limitations]。

在 Kubernetes 中的 Windows Server 容器有关的以下上游限制是与 AKS 相关：

- Windows Server 容器只能使用基础 Windows Server 节点操作系统匹配的 Windows Server 2019。
    - 作为基础操作系统不支持使用 Windows Server 2016 生成容器映像。
- 不能使用特权的容器。
- 特定于 Linux 的功能，例如 RunAsUser、 SELinux、 AppArmor 或 POSIX 功能不可用的 Windows Server 容器中。
    - 文件系统限制每个用户权限的特定于 Linux 的如 UUI/GUID，也不可用在 Windows Server 容器中。
- Azure 磁盘和 Azure 文件支持的卷类型，以在 Windows Server 容器中的 NTFS 卷形式进行访问。
    - 基于 NFS 的存储 / 卷不受支持。

## <a name="aks-limitations-for-windows-server-node-pools"></a>Windows Server 节点池的 AKS 限制

以下的其他限制适用于 Windows Server 在 AKS 中的节点池支持：

- AKS 群集始终作为第一个节点池包含 Linux 节点池。 不能删除此第一个基于 Linux 的节点池，除非删除 AKS 群集本身。
- AKS 群集必须使用 Azure CNI （高级） 网络模型。
    - 不支持 Kubenet （基本） 网络。 无法创建使用 kubenet 的 AKS 群集。 有关网络模型中存在差异的详细信息，请参阅[网络 AKS 中的应用程序的概念][azure-network-models]。
    - Azure CNI 网络模型需要额外的规划和 IP 地址管理的注意事项。 有关如何规划和实施 Azure CNI 的详细信息，请参阅[在 AKS 中的配置 Azure CNI 联网][configure-azure-cni]。
- 在 AKS 中的 Windows Server 节点都必须是*升级*到最新的 Windows Server 2019 版本保持最新修补程序修复和更新。 在 AKS 中基节点镜像中未启用 Windows 更新。 有关在 Windows 更新发布周期和验证过程按定期计划，应在 AKS 群集中执行 Windows Server 节点池上的升级。 升级 Windows Server 节点池的详细信息，请参阅[升级在 AKS 中的节点池][nodepool-upgrade]。
    - 这些 Windows Server 节点升级暂时使用虚拟网络子网中的其他 IP 地址，因为部署一个新的节点，然后删除旧节点。
    - vCPU 配额也会暂时使用订阅中部署一个新的节点，然后删除旧节点。
    - 不能自动更新和管理重新启动使用`kured`与在 AKS 中的 Linux 节点一样。
- AKS 群集可以具有最多八个节点池。
    - 最多 400 节点可以在这些八个节点的池之间。
- Windows Server 节点池名称的限制为 6 个字符。
- 在 AKS 中的预览功能，如网络策略和群集自动缩放程序，不认可的 Windows 服务器节点。
- 只应使用 NodeSelector Linux 节点上计划入口控制器。
- Azure 开发人员空间目前仅适用于基于 Linux 的节点池。

## <a name="os-concepts-that-are-different"></a>不同的 OS 概念

Kubernetes 是从历史上看面向 Linux 的。 在上游中使用的许多示例[Kubernetes.io] [ kubernetes]网站专门用于在 Linux 节点上。 当您创建使用 Windows Server 容器，以下注意事项在 OS 级别应用的部署：

- **标识**-Linux 使用用户 Id (UID) 和 groupID (GID)，表示为整数类型。 用户和组名称不规范-它们是只需在别名 */组*或 */passwd*回 UID + GID。
    - Windows Server 使用较大的二进制安全标识符 (SID) 存储在 Windows 安全访问管理器 (SAM) 数据库。 之间的主机和容器，或容器之间不共享此数据库。
- **文件权限**-Windows Server 使用基于 Sid，而不是权限和 UID + GID 的位掩码的访问控制列表
- **文件路径**-Windows Server 上的约定是使用 \ 而不是 /。
    - 在装载卷的 pod 规范，指定正确的 Windows Server 容器的路径。 例如，而不是装入点的*mnt/卷*在 Linux 容器中，指定的驱动器号和位置等 */K/卷*以作为装载*k:* 驱动器。

## <a name="next-steps"></a>后续步骤

若要开始使用 AKS 中的 Windows Server 容器[创建节点池在 AKS 中运行 Windows Server][windows-node-cli]。

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[aks-github]: https://github.com/azure/aks/issues]
[kubernetes]: https://kubernetes.io

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
