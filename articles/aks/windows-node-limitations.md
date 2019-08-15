---
title: Azure Kubernetes Service (AKS) 中 Windows Server 节点池的限制
description: 了解在 Azure Kubernetes Service (AKS) 中运行 Windows Server 节点池和应用程序工作负荷时的已知限制
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 501aeb60eba1d94b4c5882a7c6cbfa8d0359e44d
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033904"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) 中 Windows Server 节点池和应用程序工作负荷的当前限制

在 Azure Kubernetes Service (AKS) 中, 可以创建在节点上运行 Windows Server 作为来宾 OS 的节点池。 这些节点可以运行本机 Windows 容器应用程序, 如在 .NET Framework 上构建的应用程序。 由于 Linux 和 Windows OS 提供容器支持的方式有很大的不同, 因此, 一些常见的 Kubernetes 和 pod 相关功能当前不适用于 Windows 节点池。

本文概述了 AKS 中 Windows Server 节点的一些限制和操作系统概念。 Windows Server 的节点池目前处于预览阶段。

> [!IMPORTANT]
> AKS 预览功能是可选的自助服务。 预览按 "原样" 提供, 并从服务级别协议和有限担保中排除。 AKS 预览版是以最大努力为基础的客户支持部分覆盖的。 因此, 这些功能并不用于生产。 有关其他信息, 请参阅以下支持文章:
>
> * [AKS 支持策略][aks-support-policies]
> * [Azure 支持常见问题][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Windows Server 在 Kubernetes 中的限制

Windows Server 容器必须在基于 Windows 的容器主机上运行。 若要在 AKS 中运行 Windows Server 容器, 可以[创建一个运行 Windows server][windows-node-cli]作为来宾 OS 的节点池。 Windows Server 节点池支持在 Kubernetes 项目中包含属于上游 Windows Server 的一些限制。 这些限制并不特定于 AKS。 有关 Kubernetes 中对 Windows Server 的此上游支持的详细信息, 请参阅[Kubernetes 限制中的 Windows server 容器](https://docs.microsoft.com/azure/aks/windows-node-limitations)。

Kubernetes 中 Windows Server 容器的以下上游限制与 AKS 相关:

- Windows Server 容器只能使用与基础 Windows Server 节点操作系统匹配的 Windows Server 2019。
    - 不支持使用 Windows Server 2016 生成的容器映像作为基准操作系统。
- 不能使用特权容器。
- Windows Server 容器中不提供特定于 Linux 的功能, 如 RunAsUser、SELinux、AppArmor 或 POSIX 功能。
    - 特定于 Linux 的文件系统限制, 如 UUI/GUID、每个用户的权限也不能用于 Windows Server 容器。
- Azure 磁盘和 Azure 文件是支持的卷类型, 在 Windows Server 容器中作为 NTFS 卷进行访问。
    - 不支持基于 NFS 的存储/卷。

## <a name="aks-limitations-for-windows-server-node-pools"></a>Windows Server 节点池的 AKS 限制

以下附加限制适用于 AKS 中的 Windows Server 节点池支持:

- AKS 群集始终包含一个 Linux 节点池作为第一个节点池。 除非删除 AKS 群集本身, 否则不能删除此第一个基于 Linux 的节点池。
- AKS 群集必须使用 Azure CNI (高级) 网络模型。
    - Kubenet (基本) 网络不受支持。 不能创建使用 kubenet 的 AKS 群集。 有关网络模型中的差异的详细信息, 请参阅[AKS 中应用程序的网络概念][azure-network-models]。
    - Azure CNI 网络模型需要额外规划和考虑 IP 地址管理。 有关如何规划和实现 Azure CNI 的详细信息, 请参阅[Configure AZURE CNI 联网 IN AKS][configure-azure-cni]。
- AKS 中的 windows Server 节点必须*升级*到最新的 Windows server 2019 版本才能维护修补程序的最新修补程序和更新。 未在 AKS 的基本节点映像中启用 Windows 更新。 在围绕 Windows 更新发布周期和你自己的验证过程的定期计划中, 你应在 AKS 群集中的 Windows Server 节点池上执行升级。 有关升级 Windows Server 节点池的详细信息, 请参阅[在 AKS 中升级节点池][nodepool-upgrade]。
    - 在删除旧节点之前, 这些 Windows Server 节点升级将在虚拟网络子网中暂时地使用其他 IP 地址作为新节点。
    - 在部署新节点后, 还会在订阅中暂时使用 vCPU 配额, 然后删除旧节点。
    - 使用`kured` AKS 中的 Linux 节点时, 无法自动更新和管理重启。
- AKS 群集最多可以有8个节点池。
    - 在这八个节点池中最多可以有400个节点。
- Windows Server 节点池名称的限制为6个字符。
- AKS 中的预览功能 (如网络策略和群集自动缩放程序) 未认可 Windows Server 节点。
- 入口控制器仅应使用 NodeSelector 在 Linux 节点上计划。
- Azure Dev Spaces 目前仅适用于基于 Linux 的节点池。
- 当 Windows Server 节点未加入 Active Directory 域时, 组托管服务帐户 (gMSA) 支持当前在 AKS 中不可用。
    - 如果需要使用此功能, 则开源的上游[aks][aks-engine]项目当前提供 gMSA 支持。

## <a name="os-concepts-that-are-different"></a>不同的操作系统概念

Kubernetes 一直面向 Linux。 上游[Kubernetes.io][kubernetes]网站中使用的很多示例都适用于 Linux 节点。 当你创建使用 Windows Server 容器的部署时, 适用于操作系统级别的下列注意事项:

- **标识**-Linux 使用以整数类型表示的 USERID (UID) 和 GROUPID (GID)。 用户名和组名不是规范的, 它们只是 */etc/groups*或 */ETC/PASSWD*返回到 UID + GID 的别名。
    - Windows Server 使用较大的二进制安全标识符 (SID), 该标识符存储在 Windows 安全访问管理器 (SAM) 数据库中。 此数据库不会在主机和容器之间共享, 也不会在容器之间共享。
- **文件权限**-Windows Server 使用基于 sid 的访问控制列表, 而不是权限位掩码和 UID + GID
- **文件路径**-Windows Server 上的约定是使用 \ 而不是/。
    - 在 pod 中装入卷的规格中, 正确指定 Windows Server 容器的路径。 例如, 请指定驱动器号和位置 (如 */K/Volume* ) 作为*K:* 驱动器进行装载, 而不是在 Linux 容器中指定 */mnt/volume*的装入点。

## <a name="next-steps"></a>后续步骤

若要在 AKS 中开始使用 Windows Server 容器, 请[在 AKS 中创建运行 Windows server 的节点池][windows-node-cli]。

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
