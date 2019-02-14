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
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 409a304296d3fdff897a203177e2c150162755c6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746211"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>使用 Azure 可用性区域的 SAP 工作负荷配置

Azure 提供的用于改善 Azure 上 SAP 工作负荷总体可用性的高可用性功能之一是 [Azure 可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)。 可用性区域已在不同的 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)中推出， 今后会受更多区域的支持。 

SAP 高可用性的基本体系结构如下图所示：

![标准高可用性 (HA) 配置](./media/sap-ha-availability-zones/standard-ha-config.png)

SAP 应用层部署在一个 Azure [可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)中。 若要实现 SAP Central Services 的高可用性，可在独立的可用性集中部署两个 VM，并使用 Windows 故障转移群集服务或 Pacemaker (Linux) 来部署高可用性框架，以便在发生基础结构或软件问题时可以自动故障转移。 以下文档详细列出了此类部署：

- [使用群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例的群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [使用文件共享在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例的群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [SUSE Linux Enterprise Server for SAP Applications 上 Azure VM 中的 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure 虚拟机在 Red Hat Enterprise Linux 上为 SAP NetWeaver 提供的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

类似的体系结构适用于 SAP NetWeaver、S/4HANA 或混合系统的 DBMS 层。 使用故障转移群集解决方案以主动/被动模式部署 DBMS 层，发生基础结构或软件故障时，这种部署可使用 DBMS 特定的故障转移框架、Windows 故障转移群集服务或 Pacemaker 来启动故障转移活动。 

若要使用 Azure 可用性区域部署相同的体系结构，需要对所述的体系结构进行一些更改。 本文档的不同部分将介绍这些更改。

## <a name="considerations-deploying-across-availability-zones"></a>跨可用性区域部署的注意事项

使用可用性区域时需要注意一些事项。 注意事项列表如下：

- Azure 可用性区域不能保证一个 Azure 区域中的不同局部区域之间保持特定的距离
- Azure 可用性区域不是理想的灾难恢复 (DR) 解决方案。 有时，重大自然灾难会给全球某些区域造成广泛的损害，其中包括能源基础设施的重度损坏，不同区域之间的距离可能不够大，使 DR 解决方案不能发挥适当的作用
- 不同 Azure 区域中不同 Azure 可用性区域之间的网络延迟根据 Azure 区域的不同而异。 有时，可以运行部署在不同局部区域中的 SAP 应用层，因为从业务流程影响度来看，从一个局部区域到活动 DBMS VM 的网络延迟仍可接受。 但有时，某些 Azure 区域内一个局部区域中的活动 DBMS VM 与另一个局部区域中的 SAP 应用程序实例之间的延迟可能过高，不能被 SAP 业务流程所接受。 因此，如果跨局部区域的延迟过高，则部署体系结构需要与应用程序的主动/主动体系结构或者与主动/被动体系结构不同。
- 根据在不同区域之间测得的网络延迟， 确定可对哪种配置使用 Azure 可用性区域。 网络延迟在两个不同的方面发挥重要作用：
    - 需要建立同步复制的两个 DBMS 实例之间的延迟。 网络延迟越高，工作负荷可伸缩性受影响的可能性越大
    - 与主动 DBMS 实例所在的同一区域中运行 SAP 对话实例的 VM 与另一区域中的类似 VM 之间的网络延迟差。 此差越大，业务进程和批处理作业运行时受到的影响就越大，具体取决于它们是在 DBMS 所在的同一区域中还是不同的区域中运行


在 Azure 功能用法方面，跨区域部署 Azure VM，以及在同一 Azure 区域中的不同可用性区域之间建立故障转移解决方案时可以使用的功能存在一定的限制。 下面列出了这些限制：

- 部署到 Azure 可用性区域时必须使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/) 
- 区域枚举到物理区域的映射限定为 Azure 订阅。 如果使用不同的订阅部署 SAP 系统，则需要单独为每个订阅定义理想的区域
- 不能在 Azure 可用性区域中部署 Azure 可用性集 选择 Azure 可用性区域或 Azure 可用性集作为虚拟机的部署框架。
- 不能使用 [Azure 基本负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus)基于 Windows 故障转移群集服务或 Linux Pacemaker 创建故障转移群集解决方案。 需要使用 [Azure 标准负载均衡器 SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-zone-combination"></a>理想区域组合
若要确定如何利用可用性区域，需要执行调查以获取以下信息：

- Azure 区域的三个不同局部区域之间的网络延迟。 这样就可以选择跨区域网络流量网络延迟最低的区域
- 所选区域之一中的 VM 到 VM 延迟，与所选两个区域之间的网络延迟的差
- 规定需要在所选可用性区域之间部署的 VM 类型是否在所选的两个区域中可用。 尤其是对于 M 系列虚拟机，将会遇到这种情况：不同的 M 系列 VM SKU 只在两个（共三个）区域中可用

### <a name="network-latency-between-zones-and-within-zone"></a>区域之间和区域内部的网络延迟
若要确定不同区域之间的延迟，需要：

- 部署用于所有三个区域中的 DBMS 实例的 VM SKU。 执行此测量时，请务必启用 [Azure 加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)
- 找到网络延迟最低的两个区域后，部署该 VM SKU 的另外三个 VM，用作跨三个可用性区域的应用层 VM。 针对所选的两个不同“DBMS”区域中的两个“DBMS VM”测量网络延迟。 
- 可以使用 **niping** 作为测量工具。 也可以使用 SAP 支持说明 [#500235](https://launchpad.support.sap.com/#/notes/500235) 和 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中所述的由 SAP 提供的工具。 请重点关注 SAP 所述的延迟测量命令。 不建议使用 **ping** 工具，因为 **ping** 无法穿透 Azure 加速网络代码路径。

根据测量结果以及不同区域中 VM SKU 的可用性，需要做出以下决策：

- 定义 DBMS 层的理想区域
- 根据区域内部或区域之间的网络延迟差回答以下问题：是否可跨一个、两个或所有三个不同区域分布主动的 SAP 应用层
- 从应用程序的角度回答以下问题：是要部署主动/被动还是主动/主动配置（参阅下文）

在做出这些决策的同时，请回顾 SAP 在 SAP 说明 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E) 中所述的网络延迟建议。

### <a name="be-aware-of"></a>请注意

> [!IMPORTANT]
> 所做的测量和决策对于测量时所用的 Azure 订阅有效。 如果使用另一个 Azure 订阅，则需要重复测量，因为订阅相关的区域枚举映射可能在不同的订阅中发生变化


> [!IMPORTANT]
> 按如上述执行的测量预期会在支持[可用性区域](https://docs.microsoft.com/azure/availability-zones/az-overview)的每个 Azure 区域中显示不同的结果。 即使网络延迟的要求不会更改，也仍可能需要调整不同 Azure 区域中的不同部署策略，因为区域之间的网络延迟可能不同。 在某些 Azure 区域中，三个不同区域之间的网络延迟预期存在很大的差异。 而在其他区域中，三个不同区域之间的网络延迟较为一致。 指出区域之间**始终**存在 1 毫秒到 2 毫秒网络延迟的声明是**错误的**。 Azure 区域中可用性区域之间的网络延迟不能一般化。


## <a name="activeactive-deployment"></a>主动/主动部署
此部署体系结构之所以称为主动/主动部署，是因为要在两个或三个区域之间部署主动的 SAP 对话实例。 使用排队复制的 SAP Central Services 将部署在两个区域之间。 这同样适用于 DBMS 层，它将部署在 SAP Central Service 所在的相同区域中。

若要慎重规划此类配置，需要在区域中找到两个适当的两个可用性区域，它们的跨区域网络延迟可让工作负荷接受并满足同步 DBMS 复制的需求。 此外，所选区域中的网络延迟与跨区域网络延迟的差不能过大。 提出后一项要求的原因在于，根据作业是在 DBMS 服务器所在的区域中运行还是跨区域运行，业务进程或批处理进程的运行时差异不能过大。 有些差异是可接受的，但必须消除差异因素。

跨两个区域的主动/主动部署的简化架构如下所示：

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

以下注意事项适用于此配置：

- 将 Azure 可用性区域视为所有 VM 的容错域和更新域，因为不能在 Azure 可用性区域中部署可用性集
- 用于 SAP Central Services 故障转移群集以及 DBMS 层的 Azure 负载均衡器需是[标准 SKU 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本负载均衡器不能跨区域工作
- 所部署的用于托管 SAP 系统的 Azure 虚拟网络及其子网将跨区域延伸。 **不需要**隔离每个区域的虚拟网络
- 对于部署的所有虚拟机，请使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 区域不支持非托管磁盘
- Azure 高级存储或[超级 SSD 存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支持跨区域的任何存储复制类型。 因此，复制重要数据是应用程序（DBMS 或 SAP Central Services）的责任
- 这一点同样适用于共享的 sapmnt 目录，包括共享磁盘 (Windows)、CIFS 共享 (Windows) 或 NFS 共享 (Linux)。 需要采用某种技术来复制此类共享磁盘或者在区域之间共享。 目前支持以下技术：
    - 对于 Windows，支持使用 SIOS Datakeeper 的跨区域群集解决方案，具体请参阅[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - 对于 SUSE Linux，支持根据 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)中所述构建的 NFS 共享
    - 目前，**不支持**使用[针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所述的 Windows 横向扩展文件服务 (SOFS) 的跨区域解决方案
- 构建 [SUSE Linux Pacemaker 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或其他应用程序实例时，第三个区域用于托管 SBD 设备
- 若要实现某些关键业务事务和/或作业的运行时一致性， 可以尝试使用 SAP 批处理服务器组、登录组或 RFC 组，将特定的批处理作业和用户直接定向到包含主动 DBMS 实例的区域中的特定应用程序实例。 但是，发生区域性故障转移时，需要手动将这些组转移到剩余区域中的对话实例 
- 确定是否要在每个区域中部署一些休眠对话实例，以便在部署了一部分应用程序实例的区域服务中断时，可以立即获得以前的资源容量


## <a name="activepassive-deployment"></a>主动/被动部署
如果一个区域中的网络延迟与跨区域网络流量的网络延迟差不可接受，可部署的体系结构从 SAP 应用层的角度来看应该具有主动/被动特征。 定义一个“主动”区域，在其中部署完整的应用层，并尝试运行主动的 DBMS 实例和主动的 SAP Central Services 实例。 使用此类配置时，请根据作业是否在主动 DBMS 实例所在的同一区域中运行，确保业务事务与批处理作业的运行时差异不会过大。

此类体系结构的基本布局如下所示：

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

以下注意事项适用于此配置：

- 不能在 Azure 可用性区域中部署可用性集。 因此，在这种情况下，应用层最终只有一个更新域和容错域。 原因在于，应用层只部署在一个区域中。 与预期在 Azure 可用性集中部署应用层的参考体系结构相比，此配置略有退步。
- 操作此类体系结构时，需要进行密切的监视，并尝试使主动 DBMS 和 SAP Central Services 实例与所部署的应用层位于同一区域。 故障转移 SAP Central Services 或 DBMS 实例时，请确保能够尽早手动故障回复到包含所部署的 SAP 应用层的区域
- 用于 SAP Central Services 故障转移群集以及 DBMS 层的 Azure 负载均衡器需是[标准 SKU 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本负载均衡器不能跨区域工作
- 所部署的用于托管 SAP 系统的 Azure 虚拟网络及其子网将跨区域延伸。 **不需要**隔离每个区域的虚拟网络
- 对于部署的所有虚拟机，需要使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 区域不支持非托管磁盘
- Azure 高级存储或[超级 SSD 存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支持跨区域的任何存储复制类型。 因此，复制重要数据是应用程序（DBMS 或 SAP Central Services）的责任
- 这一点同样适用于共享的 sapmnt 目录，包括共享磁盘 (Windows)、CIFS 共享 (Windows) 或 NFS 共享 (Linux)。 需要采用某种技术来复制此类共享磁盘或者在区域之间共享。 目前支持以下技术：
    - 对于 Windows，支持使用 SIOS Datakeeper 的跨区域群集解决方案，具体请参阅[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - 对于 SUSE Linux，支持根据 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)中所述构建的 NFS 共享
    - 目前，**不支持**使用[针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所述的 Windows 横向扩展文件服务 (SOFS) 的跨区域解决方案
- 构建 [SUSE Linux Pacemaker 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或其他应用程序实例时，第三个区域用于托管 SBD 设备
- 在被动区域中部署休眠 VM（从 DBMS 角度看），以便在发生区域故障时能够启动应用程序资源
    - 不能在区域之间使用 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 将主动 VM 复制到休眠 VM。 Azure Site Recovery 目前无法实现此类功能
- 投资购买自动化功能可以在某个区域发生故障时，自动在另一区域中启动 SAP 应用层

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>高可用性和灾难恢复组合配置
尽管 Microsoft 不能提供有关在特定 Azure 区域中托管不同 Azure 可用性区域的设施之间的地理距离的任何信息，但某些客户会利用 HA 和 DR 组合配置来承诺零恢复点目标 (RPO)。 这意味着，即使部署了灾难恢复方案，也不能丢失任何提交的数据库事务。 

> [!NOTE]
> 建议仅在特定的场合采用此类配置。 例如，由于安全与合规原因，数据不能离开 Azure 区域。 

下图演示了此类配置的示例：

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

以下注意事项适用于此配置：

- 假设托管可用性区域的设施之间的距离很大。 或者不能离开特定的 Azure 区域。 不能在 Azure 可用性区域中部署可用性集。 因此，在这种情况下，应用层最终只有一个更新域和容错域。 原因在于，应用层只部署在一个区域中。 与预期在 Azure 可用性集中部署应用层的参考体系结构相比，此配置是一种倒退。
- 操作此类体系结构时，需要进行密切的监视，并尝试使主动 DBMS 和 SAP Central Services 实例与所部署的应用层位于同一区域。 故障转移 SAP Central Services 或 DBMS 实例时，请确保能够尽早手动故障回复到包含所部署的 SAP 应用层的区域
- VM 中预装了运行主动 QA 应用程序实例的生产应用程序实例。
- 发生区域性故障时，需要关闭 QA 应用程序实例并启动生产实例。 请注意，需要使用应用程序实例的虚拟名称才能进行此操作
- 用于 SAP Central Services 故障转移群集以及 DBMS 层的 Azure 负载均衡器需是[标准 SKU 负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)。 基本负载均衡器不能跨区域工作
- 所部署的用于托管 SAP 系统的 Azure 虚拟网络及其子网将跨区域延伸。 **不需要**隔离每个区域的虚拟网络
- 对于部署的所有虚拟机，需要使用 [Azure 托管磁盘](https://azure.microsoft.com/services/managed-disks/)。 区域不支持非托管磁盘
- Azure 高级存储或[超级 SSD 存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd)不支持跨区域的任何存储复制类型。 因此，复制重要数据是应用程序（DBMS 或 SAP Central Services）的责任
- 这一点同样适用于共享的 sapmnt 目录，包括共享磁盘 (Windows)、CIFS 共享 (Windows) 或 NFS 共享 (Linux)。 需要采用某种技术来复制此类共享磁盘或者在区域之间共享。 目前支持以下技术：
    - 对于 Windows，支持使用 SIOS Datakeeper 的跨区域群集解决方案，具体请参阅[使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - 对于 SUSE Linux，支持根据 [SUSE Linux Enterprise Server 上 Azure VM 中的 NFS 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)中所述构建的 NFS 共享
    - 目前，**不支持**使用[针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)中所述的 Windows 横向扩展文件服务 (SOFS) 的跨区域解决方案
- 构建 [SUSE Linux Pacemaker 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device)或其他应用程序实例时，第三个区域用于托管 SBD 设备





## <a name="next-steps"></a>后续步骤
查看跨 Azure 可用性区域进行部署的后续步骤：

- [使用 Azure 中的群集共享磁盘在 Windows 故障转移群集上组建 SAP ASCS/SCS 实例群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [针对 SAP ASCS/SCS 实例使用 Windows 故障转移群集和文件共享准备 SAP 高可用性的 Azure 基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






