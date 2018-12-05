---
title: Azure 上的 IBM Db2 pureScale
description: 在本文中，我们将演示用于在 Azure 上运行 IBM Db2 pureScale 环境的一个体系结构。
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 5a7ae96a3730df2d0dae04dde6d7609ce69cdee5
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2018
ms.locfileid: "51977995"
---
# <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM Db2 pureScale

IBM Db2 pureScale 环境为 Azure 提供了一个数据库群集解决方案，在 Linux 操作系统上具有高可用性和可伸缩性。 在本文中，我们将演示用于在 Azure 上运行 Db2 pureScale 的一个体系结构。

## <a name="overview"></a>概述

企业长期以来一直使用传统的关系数据库管理系统 (RDBMS) 平台来满足联机事务处理 (OLTP) 的需求。 如今，许多企业正在将他们基于大型机的数据库环境迁移到 Azure，以此作为扩展容量、降低成本和维持稳定的运营成本结构的一种方式。

迁移通常是旧平台实现现代化的第一步。 例如，一家企业最近将他们在 z/OS 上运行的 IBM Db2 环境重新托管到了 Azure 上的 IBM Db2 pureScale。 虽然与原始环境不同，但 Linux 上的 IBM Db2 pureScale 提供了与在大型机上的 Parallel Sysplex 配置中运行的 IBM Db2 for z/OS 类似的高可用性和可伸缩性功能。

本文介绍了用于此 Azure 迁移的体系结构。 Red Hat Linux 7.4 用于对配置进行测试。 此版本现在可以从 Azure 市场下载。 在选择 Linux 发行版之前，请确保验证当前的版本是否受支持。 有关详细信息，请参阅 [IBM Db2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) 和 [GlusterFS](https://docs.gluster.org/en/latest/) 的文档。

本文只是你 Db2 实施计划的起点。 尽管你的业务要求有所不同，但均适用相同的基本模式。 此体系结构模式还可用于 Azure 上的联机分析处理 (OLAP) 应用程序。

本文不涉及将 IBM Db2 for z/OS 数据库移动到在 Linux 上运行的 IBM Db2 pureScale 存在的差异和可能的迁移任务。 也不提供从 Db2 z/OS 移动到 Db2 pureScale 的等效大小预计和工作负荷分析。 为了帮助你决定适用于环境的最佳 Db2 pureScale 体系结构，我们强烈建议你完成一个完整的大小预计试验并建立一个假设。 除其他因素外，在源系统上，请确保要将数据共享体系结构、Coupling Facility 配置和 DDF 使用情况统计信息与 Db2 z/OS Parallel Sysplex 结合在一起考虑。

> [!NOTE]
> 尽管本文介绍了进行 Db2 迁移的一种方法，但还有其他方法。 例如，Db2 pureScale 也可以在虚拟化本地环境中运行。 IBM 支持采用各种配置的 Microsoft Hyper-V 上的 Db2。 有关详细信息，请参阅 IBM Knowledge Center 中的 [Db2 pureScale 虚拟化体系结构](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html)。

## <a name="architecture"></a>体系结构

为了支持 Azure 上的高可用性和可伸缩性，可以将横向扩展的共享数据体系结构用于 Db2 pureScale。 以下示例体系结构曾用于我们客户进行的迁移。

![](media/db2-purescale-on-azure/pureScaleArchitecture.png "显示有存储和网络的 Azure 虚拟机上的 Db2 pureScale")


此体系结构图描绘出了 Db2 pureScale 群集所需的逻辑层。 这包括用于客户端、管理、缓存、数据库引擎和共享存储的虚拟机。 除了数据库引擎节点外，此图示还包括用于所谓的群集缓存设施 (CF) 的两个节点。 最少有两个节点用于数据库引擎本身；属于一个 pureScale 群集的一个 Db2 服务器称为成员。 群集通过 iSCSI 连接到三节点的 GlusterFS 共享存储群集，以提供横向扩展的存储和高可用性。 Db2 pureScale 安装在运行 Linux 的 Azure 虚拟机上。

此方法只是一个模板，你可以按照组织所需的大小和规模进行修改，此方法基于以下内容：

-   两个或多个数据库成员与至少两个 CF 节点组合，这可以管理共享内存的全局缓冲池 (GBP) 和全局锁管理器 (GLM) 服务以控制来自多个活动成员的共享访问和锁争用。 一个 CF 节点充当主节点，另一个 CF 节点充当次要故障转移 CF 节点。 为了避免创建具有单一故障点的环境，Db2 pureScale 群集至少需要四个节点。

-   高性能的共享存储（如图 1 的 P30 中所示），由每个 Gluster FS 节点使用。

-   用于数据成员和共享存储的高性能网络。

### <a name="compute-considerations"></a>计算注意事项

此体系结构在 Azure 虚拟机上运行应用程序、存储和数据层。 [部署设置脚本](http://aka.ms/db2onazure)将创建以下内容：

-   一个 Db2 pureScale 群集。 Azure 上所需的计算资源类型取决于你的设置。 一般情况下，可以使用以下两种方法：

    -   使用多个中小型实例可以在其中访问共享存储的多节点、高性能计算 (HPC) 式网络。 对于此 HPC 类型的配置，Azure 内存优化 E 系列或存储优化 L 系列[虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)可以提供所需的计算能力。

    -   为数据引擎使用较少的大型虚拟机实例。 对于大型实例，最大内存优化的 [M 系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)虚拟机非常适合繁重的内存中工作负荷，但是否需要专用实例具体取决于用于运行 Db2 的逻辑分区 (LPAR) 的大小。

-   Db2 CF 使用内存优化的虚拟机，例如 E 系列或 L 系列。

-   GlusterFS 存储使用运行 Linux 的标准 \_DS4\_ v2 虚拟机。

-   GlusterFS 跳转框是运行 Linux 的标准 \_DS2\_ v2 虚拟机。

-   客户端是运行 Windows 的标准 \_DS3\_ v2 虚拟机（用于测试）。

-   见证服务器是运行 Linux 的标准 \_DS3\_ v2 虚拟机（用于 Db2 pureScale）。

> [!NOTE]
> 一个 Db2 pureScale 群集中至少需要两个 Db2 实例。 还需要缓存实例和锁定管理器实例。

### <a name="storage-considerations"></a>存储注意事项

与 Oracle RAC 类似，Db2 pureScale 是一个高性能的块 I/O，横向扩展的数据库。 我们建议使用最适合满足你需求的 [Azure 高级存储](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage)。 例如，较小的存储选项可能适用于开发和测试环境，而生产环境通常则需要更大的存储容量。 出于 IOPS 与大小和价格比率的原因，此示例体系结构使用了 [P30](https://azure.microsoft.com/pricing/details/managed-disks/)。 不考虑大小，请使用高级存储以获得最佳性能。

Db2 pureScale 使用共享的所有体系结构，所有数据都可以从所有群集节点进行访问。 必须在多个实例之间共享高级存储 - 无论是按需还是针对专用实例。

一个大型 Db2 pureScale 群集可能需要 200 兆兆字节 (TB) 或更高的高级共享存储，且 IOPS 为 100,000。 Db2 pureScale 支持可以在 Azure 上使用的 iSCSI 块接口。 iSCSI 接口需要可以通过 GlusterFS、S2D 或其他工具实现的共享存储群集。 此类解决方案将在 Azure 中创建虚拟存储区域网络 (vSAN) 设备。 Db2 pureScale 使用 vSAN 来安装用于在多台虚拟机之间共享数据的群集文件系统。

对于此体系结构，我们使用了 GlusterFS 文件系统，这是一个免费、可缩放的开源分布式文件系统，专门针对云存储进行过优化。

### <a name="networking-considerations"></a>网络注意事项

IBM 建议为 Db2 pureScale 群集中的所有成员提供 InfiniBand 网络；对于所使用的 CF，Db2 pureScale 还可以使用远程直接内存访问 (RDMA)（如果可用）。

在安装过程中，将创建一个 Azure [资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)来包含所有虚拟机。 一般情况下，会根据资源的生存期以及它们的管理者对这些资源进行分组。 此体系结构中的虚拟机需要[加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)，这是一项 Azure 功能，可以通过单根 I/O虚拟化 (SR-IOV) 向虚拟机提供一致的超低网络延迟。

每台 Azure 虚拟机都部署到了一个虚拟网络中，此网络被细分成多个子网：主子网、Gluster FS 前端 (gfsfe)、Gluster FS 后端 (bfsbe)、Db2 pureScale (db2be) 和 Db2 purescale 前端 (db2fe)。 安装脚本还会在主子网中的虚拟机上创建主 [NIC](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)。

[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (NSG) 用于限制虚拟网络中的网络流量并隔离子网。

在 Azure 上，Db2 pureScale 需要使用 TCP/IP 作为存储的网络连接。

## <a name="next-steps"></a>后续步骤

-   [在 Azure 上部署此体系结构](deploy-ibm-db2-purescale-azure.md)
