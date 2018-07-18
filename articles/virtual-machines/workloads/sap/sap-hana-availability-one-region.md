---
title: 一个 Azure 区域内的 SAP HANA 可用性 | Microsoft Docs
description: 介绍对一个 Azure 区域内的 Azure 原生 VM 执行的 SAP HANA 操作。
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47dba73a6c22d11953485a69435000d3d2fe6f55
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192792"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>一个 Azure 区域内的 SAP HANA 可用性
本文介绍一个 Azure 区域内的多种可用性场景。 Azure 已在许多区域上市，这些区域分散在世界各地。 有关 Azure 区域的列表，请查阅 [Azure 区域](https://azure.microsoft.com/regions/)。 Microsoft 将 SAP HANA 部署在一个 Azure 区域内的 VM 上，可以提供包含一个 HANA 实例的单一 VM 部署。 为了提高可用性，可将包含两个 HANA 实例的两个 VM 部署在一个 [Azure 可用性集](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)中，并使用 HANA 系统复制来实现可用性。 

Azure 目前提供 [Azure 可用性区域（预览版）](https://docs.microsoft.com/azure/availability-zones/az-overview)。 本文不会详细介绍这些可用性区域。 但是，其中包含了有关使用可用性集与可用性区域的一般性讨论。

Azure 中的可用性集与可用性区域有什么区别？ 提供可用性区域的 Azure 区域具有多个数据中心。 这些数据中心独立提供电源、冷却和网络设备。 在单个 Azure 区域中提供不同区域的原因是为了能够跨越提供的两个或三个可用性区域部署应用程序。 假设电源或网络问题只会影响一个可用性区域基础结构，在使用可用性区域的情况下，Azure 区域中的应用程序部署仍可完全正常运行。 这可能会减少一些容量。 例如，一个区域中的某些 VM 可能会丢失，但另外两个区域中的 VM 仍可保持正常运行。 
 
Azure 可用性集是一种逻辑分组功能，可帮助确保在将可用性集中的 VM 资源部署到 Azure 数据中心后，这些资源相互故障隔离。 Azure 确保可用性集中部署的 VM 能够跨多个物理服务器、计算机架、存储单元和网络交换机运行。 在某些 Azure 文档中，此配置被阐述为将 VM 放置在不同的[更新域和容错域](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)中。 这些位置通常是在 Azure 数据中心内。 假设电源或网络问题会影响部署的数据中心，则一个 Azure 区域中的所有容量都会受到影响。

代表 Azure 可用性区域的数据中心的位置既要考虑到不同区域中部署的服务之间的网络延迟是否可让大多数应用程序接受，也要考虑到数据中心之间的特定距离。 在理想情况下，自然灾害不会影响到此区域中所有可用性区域的电源、网络和基础结构。 但是，在出现非常严重的自然灾害时，可用性区域不一定总能在一个区域中提供可用性。 想像一下，2017 年 8 月 20 日玛丽亚飓风袭击了波多黎各岛。 这场飓风使方圆 90 英里的岛屿几乎完全陷入一片黑暗。

## <a name="single-vm-scenario"></a>单一 VM 场景

在单一 VM 场景中，我们将为 SAP HANA 实例创建一个 Azure VM。 使用 Azure 高级存储来托管操作系统磁盘和所有数据磁盘。 99.9% 的 Azure 正常运行时间 SLA 和其他 Azure 组件提供的 SLA 足以向客户履行可用性 SLA。 在此场景中，无需对运行 DBMS 层的 VM 使用 Azure 可用性集。 此场景依赖于两种不同的功能：

- Azure VM 自动重启（也称为 Azure 服务修复）
- SAP HANA 自动重启

Azure VM 自动重启（服务修复）是 Azure 中的一项功能，它在两个级别工作：

- Azure 服务器主机（检查服务器主机上托管的 VM 的运行状况）。
- Azure 结构控制器（监视服务器主机的运行状况和可用性）。

运行状况检查功能将会监视 Azure 服务器主机上托管的每个 VM 的运行状况。 如果 VM 的状态不正常，则检查 VM 运行状况的 Azure 主机代理可以执行 VM 重新启动。 结构控制器会通过检查指示主机硬件问题的许多不同参数，来检查主机的运行状况。 此外，它还会通过网络检查主机的可访问性。 主机出现问题迹象可能会导致以下事件：

- 如果出现主机状态不正常的迹象，则重新启动主机，并重启该主机上运行的 VM。
- 如果重新启动后主机的状态仍不正常，则重新启动主机，并重启最初托管在正常主机上的 VM。 在这种情况下，该主机将被标记为不正常。 在清除问题或更换主机之前，不会将它进一步用于部署。
- 如果不正常的主机在重新启动过程中出现问题，则立即在正常的主机上重启 VM。 

借助 Azure 提供的主机和 VM 监视功能，遇到主机问题的 Azure VM 可在正常的 Azure 主机上自动重启。 

此场景中依赖的第二项功能是，重新启动 VM 后，在重启后的 VM 中运行的 HANA 服务会自动启动。 可以通过不同 HANA 服务的监视器服务设置 [HANA 服务自动重启](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)。

在 SAP HANA 配置中添加一个冷故障转移节点可以改进这种单一 VM 场景。 在 SAP HANA 文档中，这种设置称为[主机自动故障转移](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html)。 在服务器硬件受限并且需要将单个服务器节点专门用作一组生产主机的主机自动故障转移节点的本地部署场合中，此配置可能很有效。 但在 Azure 中，如果 Azure 的底层基础结构需要提供正常的目标服务器才能成功重启 VM，则并不适合部署 SAP HANA 主机自动故障转移。 因此，我们并未提供参考体系结构用于预见 HANA 主机自动故障转移的备用节点。 这同样适用于 SAP HANA 横向扩展配置。

## <a name="availability-scenarios-for-two-different-vms"></a>两个不同 VM 的可用性场景

如果在一个 Azure 可用性集中使用两个 Azure VM，则可以提高这两个 VM 的正常运行时间，前提是将这些 VM 放置在一个 Azure 区域中的 Azure 可用性集 内。 Azure 中的基本设置如下所示：

![包含所有层的两个 VM 的示意图](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

为了演示不同的可用性场景，上图中的几个层已被省略。 图形中只显示了描绘 VM、主机、可用性集和 Azure 区域的层。 Azure 虚拟网络实例、资源组和订阅不影响本部分所述的场景。

### <a name="replicate-backups-to-a-second-virtual-machine"></a>将备份复制到第二个虚拟机

最基本的设置之一是使用备份。 具体而言，可将事务日志备份从一个 VM 传送到另一个 Azure VM。 可以选择 Azure 存储类型。 在此设置中，你需要负责编写脚本，以便将计划的备份从第一个 VM 复制到第二个 VM。 如果需要使用第二个 VM 实例，则必须将完整、增量/差异和事务日志备份还原到所需的时间点。 

体系结构如下所示：

![采用存储复制的两个 VM 的示意图](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

此设置并不是很适合用于实现优异的恢复点目标 (RPO) 和恢复时间目标 (RTO)。 RTO 甚至会受到损害，因为需要使用复制的备份来完全还原整个数据库。 但是，在主要实例上意外删除数据后，可以使用此设置进行恢复。 使用这种设置，随时可以还原到特定的时间点、提取数据，并将删除的数据导入主要实例。 因此，结合其他高可用性功能使用这种备份复制方法会很有帮助。 

在复制备份期间，可以使用一个比运行 SAP HANA 实例的主要 VM 更小的 VM。 请注意，在较小的 VM 上只能附加更少的 VHD。 有关各种 VM 类型的限制信息，请参阅 [Azure 中 Linux 虚拟机的大小](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)。

### <a name="sap-hana-system-replication-without-automatic-failover"></a>在不使用自动故障转移的情况下执行 SAP HANA 系统复制

本部分所述的场景使用 SAP HANA 系统复制。 有关 SAP 文档，请参阅[系统复制](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)。 单个 Azure 区域中的两个 Azure VM 采用不同的配置，因此，RTO 存在一定的差异。 一般而言，不采用自动故障转移的场景可能不是很适合一个 Azure 区域中的 VM。 这是原因，对于 Azure 基础结构中的大多数故障，Azure 服务修复功能会在另一台主机上重启主 VM。 只是在一些不太常见的情况下，这种配置才对故障场景有所帮助。 在客户想要提高效率的情况下，这种配置也比较有利。

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>在不使用自动故障转移和数据预先加载的情况下执行 SAP HANA 系统复制

在此场景中，使用 SAP HANA 系统复制来同步移动数据，以实现 0 RPO。 另一方面，由于 RTO 足够长，因此，无需将数据故障转移或预先加载到 HANA 实例缓存。 在这种情况下，可通过执行以下操作，进一步在配置中实现更高的经济效益：

- 在第二个 VM 中运行另一个 SAP HANA 实例。 第二个 VM 中的 SAP HANA 实例充分利用虚拟机的内存。 通常，在故障转移到第二个 VM 后，便可以看到这种效果。 可以关闭第二个 VM，以便将复制的数据载入第二个 VM 中目标 HANA 实例的缓存。
- 对第二个 VM 使用较小的 VM。 如果发生故障转移，在手动故障转移之前，需要执行一个附加的步骤。 此步骤将 VM 大小调整为源 VM 的大小。 场景如下所示：

![采用存储复制的两个 VM 的示意图](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> 即使不在 HANA 系统复制目标中使用数据预先加载，也至少需要 64 GB 内存。 除了 64 GB 内存以外，还需要足够的内存用于在目标实例的内存中保留行存储数据。

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>在不使用自动故障转移但使用数据预先加载的情况下执行 SAP HANA 系统复制

在此场景中，复制到第二个 VM 中 HANA 实例的数据会预先加载。 这样，就失去了不预先加载数据所带来的两个优势。 在这种情况下，无法在第二个 VM 上运行另一个 SAP HANA 系统。 此外，不能使用较小的 VM。 因此，客户很少实施此场景。

### <a name="sap-hana-system-replication-with-automatic-failover"></a>在使用自动故障转移的情况下执行 SAP HANA 系统复制

在一个 Azure 区域内的标准和最常见可用性配置中，运行 SLES Linux 的两个 Azure VM 上定义了故障转移群集。 SLES Linux 群集基于 [Pacemaker](http://www.linux-ha.org/wiki/Pacemaker) 框架，与 [STONITH](http://linux-ha.org/wiki/STONITH) 设备相结合。 

从 SAP HANA 的角度看，使用的复制模式是同步的，并且配置了自动故障转移。 在第二个 VM 中，SAP HANA 实例充当热备用节点。 备用节点从主 SAP HANA 实例接收同步的更改记录流。 应用程序在 HANA 主节点上提交事务后，HANA 主节点会一直等待确认提交到应用程序，直到 SAP HANA 辅助节点确认收到提交记录。 SAP HANA 提供两种同步复制模式。 有关这两种同步复制模式的详细信息和差异说明，请参阅 SAP 文章 [SAP HANA 系统复制的复制模式](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)。

总体配置如下所示：

![采用存储复制和故障转移的两个 VM 的示意图](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

选择此解决方案的可能原因是它可以实现 RPO = 0 和极低的 RTO。 配置 SAP HANA 客户端连接，使 SAP HANA 客户端能够使用虚拟 IP 地址连接到 HANA 系统复制配置。 这样，在故障转移到辅助节点时，无需重新配置应用程序。 在此场景中，主 VM 和辅助 VM 的 Azure VM SKU 必须相同。

## <a name="next-steps"></a>后续步骤

有关在 Azure 中设置这些配置的分步指导，请参阅：

- [在 Azure VM 中设置 SAP HANA 系统复制](sap-hana-high-availability.md)
- [使用系统复制实现 SAP HANA 的高可用性](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

有关跨 Azure 区域的 SAP HANA 可用性的详细信息，请参阅：

- [跨 Azure 区域的 SAP HANA 可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

