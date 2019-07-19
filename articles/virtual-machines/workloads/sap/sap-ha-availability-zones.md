---
title: 使用 Azure 可用性区域的 SAP 工作负荷配置 | Microsoft Docs
description: 使用 Azure 可用性区域的 SAP NetWeaver 的高可用性体系结构和方案
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ead1dfdce4bf3a803eee46a536dc7062626640d9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234233"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>使用 Azure 可用性区域的 SAP 工作负荷配置
[Azure 可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)是 Azure 提供的高可用性功能之一。 使用可用性区域可提高 Azure 上 SAP 工作负荷的整体可用性。 此功能已在某些 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)中推出。 今后会在更多的区域中推出。

下图显示了 SAP 高可用性的基本体系结构：

![标准高可用性配置](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP 应用层部署在一个 Azure [可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)中。 若要实现 SAP Central Services 的高可用性，可在单独的可用性集中部署两个 VM。 使用 Windows Server 故障转移群集或 Pacemaker (Linux) 作为高可用性框架，并在出现基础结构或软件问题时自动进行故障转移。 若要了解有关这些部署的详细信息，请参阅：

- [使用群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例的群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [使用文件共享在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例的群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [SUSE Linux Enterprise Server for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure 虚拟机在 Red Hat Enterprise Linux 上为 SAP NetWeaver 提供的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

类似的体系结构适用于 SAP NetWeaver、S/4HANA 或混合系统的 DBMS 层。 可以使用故障转移群集解决方案以主动/被动模式部署 DBMS 层，防止基础结构或软件出现故障。 故障转移群集解决方案可以是特定于 DBMS 的故障转移框架、Windows Server 故障转移群集或 Pacemaker。

若要使用 Azure 可用性区域部署相同的体系结构，需要对上面所述的体系结构进行一些更改。 本文将介绍这些更改。

## <a name="considerations-for-deploying-across-availability-zones"></a>跨可用性区域部署的注意事项


使用可用性区域时需要考虑以下事项：

- 不能保证一个 Azure 区域中的不同可用性区域之间保持特定的距离。
- 可用性区域不是理想的灾难恢复 (DR) 解决方案。 在全球某些区域，自然灾害可能造成广泛破坏，其中包括对电力基础设施的严重破坏。 不同区域之间的距离可能不足以用作确定合适 DR 解决方案的标准。
- 可用性区域之间的网络延迟因 Azure 区域的不同而有所差异。 有时，可以跨不同区域部署并运行 SAP 应用层，因为从一个区域到活动 DBMS VM 的网络延迟是可接受的。 但在某些 Azure 区域中，部署在不同区域中的活动 DBMS VM 与 SAP 应用程序实例之间的延迟对于 SAP 业务流程而言不可接受。 在这种情况下，如果跨局部区域网络的延迟过高，则部署体系结构需要与应用程序的主动/主动体系结构或者与主动/被动体系结构不同。
- 根据在不同区域之间测得的网络延迟，确定在何处使用可用性区域。 网络延迟在两个方面发挥重要作用：
    - 需要进行同步复制的两个 DBMS 实例之间的延迟。 网络延迟越高，工作负荷可伸缩性受影响的可能性越大。
    - 在活动 DBMS 实例所在区域中运行 SAP 对话实例的 VM 与另一区域中的类似 VM 之间的网络延迟差。 此差越大，业务进程和批处理作业运行时受到的影响就越大，具体取决于它们是在 DBMS 所在区域中运行，还是在其他区域中运行。

跨可用性区域部署 Azure VM 并在同一 Azure 区域内建立故障转移解决方案存在一些限制：

- 部署到 Azure 可用性区域时必须使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 
- 区域枚举到物理区域的映射限定为 Azure 订阅。 如果使用不同的订阅部署 SAP 系统，则需要为每个订阅定义理想的区域。
- 除非使用[Azure 邻近度放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), 否则无法在 Azure 可用性区域内部署 azure 可用性集。 如何跨区域部署 SAP DBMS 层和中心服务, 同时部署 SAP 应用程序层 (使用可用性集), 并仍要实现 Vm 的近距离, 请参阅[Azure 邻近性定位一文适用于 SAP 应用程序的最佳网络延迟的组](sap-proximity-placement-scenarios.md)。 如果不使用 Azure 邻近性放置组, 则需要选择其中一项作为虚拟机的部署框架。
- 不能使用 [Azure 基本负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus)基于 Windows Server 故障转移群集或 Linux Pacemaker 创建故障转移群集解决方案。 需要使用 [Azure 标准负载均衡器 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。



## <a name="the-ideal-availability-zones-combination"></a>理想的可用性区域组合
在决定如何使用可用性区域之前，需要确定：

- Azure 区域的三个局部区域之间的网络延迟。 这样就可以选择跨区域网络流量网络延迟最低的区域。
- 所选区域之一中的 VM 到 VM 延迟，与所选两个区域之间的网络延迟的差。
- 确定需要部署的 VM 类型是否在所选的两个区域中可用的声明。 对于某些 VM（尤其是 M 系列 VM），可能会遇到这种情况：某些 SKU 只在两个（共三个）区域中可用。

## <a name="network-latency-between-and-within-zones"></a>区域之间和区域内部的网络延迟
若要确定不同区域之间的延迟，需要：

- 部署用于所有三个区域中的 DBMS 实例的 VM SKU。 执行此测量时，请务必启用 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。
- 找到网络延迟最低的两个区域后，部署该 VM SKU 的另外三个 VM，用作跨三个可用性区域的应用层 VM。 针对所选的两个 DBMS 区域中的两个 DBMS VM 测量网络延迟。 
- 使用 **niping** 作为测量工具。 SAP 支持说明 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中介绍了 SAP 提供的此工具。 请重点关注所述的延迟测量命令。 由于 **ping** 无法穿透 Azure 加速网络代码路径，因此我们不建议使用它。

根据测量结果以及可用性区域中 VM SKU 的可用性，需要做出一些决策：

- 定义 DBMS 层的理想区域。
- 根据区域内部或区域之间的网络延迟差，确定是否要跨一个、两个或全部三个区域分布主动 SAP 应用层。
- 从应用程序的角度确定是要部署主动/被动还是主动/主动配置。 （本文稍后会解释这些配置。）

在做出这些决策的同时，请考虑 SAP 在 SAP 说明 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中所述的网络延迟建议。

> [!IMPORTANT]
> 所做的测量和决策对于测量时所用的 Azure 订阅有效。 如果使用其他 Azure 订阅，则需要重复测量。 枚举区域的映射可能因另一个 Azure 订阅而异。


> [!IMPORTANT]
> 按如上述执行的测量预期会在支持[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)的每个 Azure 区域中显示不同的结果。 即使网络延迟要求不变，也仍可能需要在不同 Azure 区域中采用不同的部署策略，因为区域之间的网络延迟可能不同。 在某些 Azure 区域中，三个不同区域之间的网络延迟可能会存在很大的差异。 在其他区域中，三个不同区域之间的网络延迟可能较为一致。 指出区域之间始终存在 1 毫秒到 2 毫秒网络延迟的声明是错误的。 Azure 区域中可用性区域之间的网络延迟不能一般化。

## <a name="activeactive-deployment"></a>主动/主动部署
此部署体系结构称为活动/活动, 因为你将活动的 SAP 应用程序服务器部署到两个或三个区域。 使用排队复制的 SAP Central Services 实例将部署在两个区域之间。 这同样适用于 DBMS 层，它将部署在 SAP Central Service 所在的相同区域中。

考虑此配置时，需要在区域中找到两个适当的两个可用性区域，它们的跨区域网络延迟可让工作负荷接受并满足同步 DBMS 复制的需求。 此外，请确保所选区域中的网络延迟与跨区域网络延迟的差不会过大。 原因在于，根据作业是在 DBMS 服务器所在的区域中运行还是跨区域运行，业务进程或批处理作业的运行时差异不能过大。 有些差异是可接受的，但必须消除差异因素。

跨两个区域的主动/主动部署的简化架构如下所示：

![主动/主动区域部署](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

以下注意事项适用于此配置：

- 如果不使用[Azure 邻近性放置组](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), 则会将 Azure 可用性区域视为所有 vm 的容错域和更新域, 因为可用性集不能部署在 Azure 可用性区域中。
- 如果要合并 DBMS 层和中心服务的区域部署, 但要为应用程序层使用 Azure 可用性集, 则需要使用 azure 邻近性组, 如[最佳SAP 应用程序的网络延迟](sap-proximity-placement-scenarios.md)。
- 对于 SAP Central Services 故障转移群集以及 DBMS 层的负载均衡器，需要使用[标准 SKU Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本负载均衡器不能跨区域工作。
- 所部署的用于托管 SAP 系统的 Azure 虚拟网络及其子网将跨区域延伸。 不需要隔离每个区域的虚拟网络。
- 对于部署的所有虚拟机，需要使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 区域部署不支持非托管磁盘。
- Azure 高级存储和[超级 SSD 存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支持跨区域的任何存储复制类型。 应用程序（DBMS 或 SAP Central Services）必须复制重要数据。
- 这一点同样适用于共享的 sapmnt 目录，包括共享磁盘 (Windows)、CIFS 共享 (Windows) 或 NFS 共享 (Linux)。 需要采用某种技术来复制此类共享磁盘或者在区域之间共享。 支持以下技术：
  - 对于 Windows，支持使用 SIOS DataKeeper 的群集解决方案，具体请参阅[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)。
  - 对于 SUSE Linux，支持根据 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)中所述构建的 NFS 共享。
    
    目前，不支持使用[针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所述的 Microsoft 横向扩展文件服务的跨区域解决方案。
- 构建 [SUSE Linux Pacemaker 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或其他应用程序实例时，第三个区域用于托管 SBD 设备。
- 若要为关键业务流程实现运行时一致性, 可以尝试使用 SAP batch 服务器组、SAP 登录组或 RFC 组将某些批处理作业和用户定向到具有活动 DBMS 实例的区域中的应用程序实例。 但是，发生区域性故障转移时，需要手动将这些组移动到在活动 DB VM 所在区域内的 VM 上运行的实例。  
- 你可能想要在每个区域中部署一些休眠对话实例。 这样，在使用了一部分应用程序实例的区域出现服务中断时，可以立即恢复以前的资源容量。


## <a name="activepassive-deployment"></a>主动/被动部署
如果发现一个区域中的网络延迟与跨区域网络流量的网络延迟差不可接受，可部署的体系结构从 SAP 应用层的角度来看应该具有主动/被动特征。 定义一个主动区域，在其中部署完整的应用层，并尝试运行主动 DBMS 实例和主动 SAP Central Services 实例  。 使用此类配置时，需要根据作业是否在主动 DBMS 实例所在区域中运行，确保业务事务与批处理作业的运行时差异不会过大。

该体系结构的基本布局如下所示：

![主动/被动区域部署](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

以下注意事项适用于此配置：

- 不能在 Azure 可用性区域中部署可用性集。 若要弥补这一点, 可以使用 azure 近程放置组一文中所述的 Azure 邻近性组, 以[实现 SAP 应用程序的最佳网络延迟](sap-proximity-placement-scenarios.md)。
- 使用此体系结构时，需要进行密切监视状态，并尝试使主动 DBMS 和 SAP Central Services 实例与所部署的应用层位于同一区域。 故障转移 SAP Central Services 或 DBMS 实例时，请确保能够尽快手动故障回复到包含所部署的 SAP 应用层的区域。
- 对于 SAP Central Services 故障转移群集以及 DBMS 层的负载均衡器，需要使用[标准 SKU Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本负载均衡器不能跨区域工作。
- 所部署的用于托管 SAP 系统的 Azure 虚拟网络及其子网将跨区域延伸。 不需要隔离每个区域的虚拟网络。
- 对于部署的所有虚拟机，需要使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 区域部署不支持非托管磁盘。
- Azure 高级存储和[超级 SSD 存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支持跨区域的任何存储复制类型。 应用程序（DBMS 或 SAP Central Services）必须复制重要数据。
- 这一点同样适用于共享的 sapmnt 目录，包括共享磁盘 (Windows)、CIFS 共享 (Windows) 或 NFS 共享 (Linux)。 需要采用某种技术来复制此类共享磁盘或者在区域之间共享。 支持以下技术：
    - 对于 Windows，支持使用 SIOS DataKeeper 的群集解决方案，具体请参阅[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)。
    - 对于 SUSE Linux，支持根据 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)中所述构建的 NFS 共享。
    
  目前，不支持使用[针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所述的 Microsoft 横向扩展文件服务的跨区域解决方案。
- 构建 [SUSE Linux Pacemaker 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或其他应用程序实例时，第三个区域用于托管 SBD 设备。
- 应在被动区域中部署休眠 VM（从 DBMS 角度看），以便在发生区域故障时能够启动应用程序资源。
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 目前无法在区域之间将主动 VM 复制到休眠 VM。 
- 应该投资购买自动化功能，以便在某个区域发生故障时，自动在另一区域中启动 SAP 应用层。

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>高可用性和灾难恢复组合配置
Microsoft 不会共享有关托管 Azure 区域中不同 Azure 可用性区域的设施之间的地理距离的任何信息。 尽管如此，一些客户正在使用相关区域进行 HA 和 DR 组合配置，此配置承诺恢复点目标 (RPO) 为零。 这意味着，即使部署了灾难恢复方案，也不能丢失任何提交的数据库事务。 

> [!NOTE]
> 建议仅在特定的场合采用此类配置。 例如，出于安全与合规原因，数据不能离开 Azure 区域，在这种情况下可以使用此配置。 

下面是此类配置的示例：

![在区域中结合使用高可用性和灾难恢复](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

以下注意事项适用于此配置：

- 假设托管可用性区域的设施之间的距离很大，或者你不能离开特定的 Azure 区域。 不能在 Azure 可用性区域中部署可用性集。 若要弥补这一点, 可以使用 azure 近程放置组一文中所述的 Azure 邻近性组, 以[实现 SAP 应用程序的最佳网络延迟](sap-proximity-placement-scenarios.md)。
- 使用此体系结构时，需要进行密切监视状态，并尝试使主动 DBMS 和 SAP Central Services 实例与所部署的应用层位于同一区域。 故障转移 SAP Central Services 或 DBMS 实例时，请确保能够尽快手动故障回复到包含所部署的 SAP 应用层的区域。
- VM 中应该预装了运行主动 QA 应用程序实例的生产应用程序实例。
- 发生区域性故障时，需要关闭 QA 应用程序实例并启动生产实例。 请注意，需要使用应用程序实例的虚拟名称才能进行此操作。
- 对于 SAP Central Services 故障转移群集以及 DBMS 层的负载均衡器，需要使用[标准 SKU Azure 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本负载均衡器不能跨区域工作。
- 所部署的用于托管 SAP 系统的 Azure 虚拟网络及其子网将跨区域延伸。 不需要隔离每个区域的虚拟网络。
- 对于部署的所有虚拟机，需要使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 区域部署不支持非托管磁盘。
- Azure 高级存储和[超级 SSD 存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支持跨区域的任何存储复制类型。 应用程序（DBMS 或 SAP Central Services）必须复制重要数据。
- 这一点同样适用于共享的 sapmnt 目录，包括共享磁盘 (Windows)、CIFS 共享 (Windows) 或 NFS 共享 (Linux)。 需要采用某种技术来复制此类共享磁盘或者在区域之间共享。 支持以下技术：
    - 对于 Windows，支持使用 SIOS DataKeeper 的群集解决方案，具体请参阅[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)。
    - 对于 SUSE Linux，支持根据 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)中所述构建的 NFS 共享。

  目前，不支持使用[针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所述的 Microsoft 横向扩展文件服务的跨区域解决方案。
- 构建 [SUSE Linux Pacemaker 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或其他应用程序实例时，第三个区域用于托管 SBD 设备。





## <a name="next-steps"></a>后续步骤
下面是跨 Azure 可用性区域进行部署的后续步骤：

- [使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






