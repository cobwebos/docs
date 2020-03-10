---
title: Azure 上的 IBM DB2 pureScale
description: 在本文中，我们将演示用于在 Azure 上运行 IBM DB2 pureScale 环境的一个体系结构。
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: d8309a69c9c38610fa7bea3fee202a60d836980c
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2020
ms.locfileid: "78945059"
---
# <a name="ibm-db2-purescale-on-azure"></a>Azure 上的 IBM DB2 pureScale

IBM DB2 pureScale 环境为 Azure 提供了一个数据库群集，在 Linux 操作系统上具有高可用性和可伸缩性。 本文将演示用于在 Azure 上运行 DB2 pureScale 的一个体系结构。

## <a name="overview"></a>概述

企业一直使用传统的关系数据库管理系统（RDBMS）平台来满足其联机事务处理（OLTP）需求。 如今，许多企业正在将他们基于大型机的数据库环境迁移到 Azure，以此作为扩展容量、降低成本和维持稳定的运营成本结构的一种方式。 迁移通常是旧平台实现现代化的第一步。 

最近，企业客户将其在 z/OS 上运行的 IBM DB2 环境重新承载到 Azure 上的 IBM DB2 pureScale。 Db2 pureScale 数据库群集解决方案在 Linux 操作系统上提供高可用性和可伸缩性。 客户在安装 Db2 pureScale 之前，在 Azure 上的较大扩展系统中的单个虚拟机（VM）上，客户成功运行 Db2。 

虽然与原始环境不同，但 Linux 上的 IBM DB2 pureScale 提供了与在大型机上的 Parallel Sysplex 配置中运行的 IBM DB2 for z/OS 类似的高可用性和可伸缩性功能。 在此方案中，群集通过 iSCSI 连接到共享存储群集。 我们使用了 GlusterFS 文件系统，它是一种针对云存储专门优化的免费、可缩放的开源分布式文件系统。 然而，IBM 不再支持此解决方案。 若要维持 IBM 的支持，需要使用受支持的 iSCSI 兼容文件系统。 Microsoft 提供存储空间直通（S2D）作为选项

本文介绍了用于此 Azure 迁移的体系结构。 客户使用 Red Hat Linux 7.4 来测试配置。 此版本现在可以从 Azure 市场下载。 在选择 Linux 发行版之前，请确保验证当前的版本是否受支持。 有关详细信息，请参阅 [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) 和 [GlusterFS](https://docs.gluster.org/en/latest/) 的文档。

本文是 DB2 实施计划的起点。 尽管你的业务要求有所不同，但均适用相同的基本模式。 还可以将此体系结构模式用于 Azure 上的联机分析处理 (OLAP) 应用程序。

本文不涉及将 IBM DB2 for z/OS 数据库移动到 Linux 上运行的 IBM DB2 pureScale 时存在的差异和可能的迁移任务。 并且它不提供从 DB2 z/OS 迁移到 DB2 pureScale 的大小估计和工作负荷分析。 

为了帮助你决定适合环境的最佳 DB2 pureScale 体系结构，我们建议你完整估算大小并做出假设。 在源系统上，请确保要将数据共享体系结构、Coupling Facility 配置和分布式数据设备 (DDF) 使用情况统计信息与 DB2 z/OS Parallel Sysplex 结合在一起考虑。

> [!NOTE]
> 尽管本文介绍了进行 DB2 迁移的一种方法，但还有其他方法。 例如，DB2 pureScale 也可以在虚拟化本地环境中运行。 IBM 支持采用各种配置的 Microsoft Hyper-V 上的 DB2。 有关详细信息，请参阅 IBM Knowledge Center 中的 [DB2 pureScale 虚拟化体系结构](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html)。

## <a name="architecture"></a>体系结构

为了支持 Azure 上的高可用性和可伸缩性，可以将横向扩展的共享数据体系结构用于 DB2 pureScale。 客户迁移使用以下示例体系结构。

![显示存储和网络的 Azure 虚拟机上的 DB2 pureScale](media/db2-purescale-on-azure/pureScaleArchitecture.png "显示存储和网络的 Azure 虚拟机上的 DB2 pureScale")


此关系图显示了 DB2 pureScale 群集所需的逻辑层。 这包括用于客户端、管理、缓存、数据库引擎和共享存储的虚拟机。 

除了数据库引擎节点之外，该图还包括用于群集缓存设施 (CF) 的两个节点。 数据库引擎本身至少使用两个节点。 属于 pureScale 群集的 DB2 服务器称为成员。 

通过 iSCSI 将群集连接到三节点共享存储群集，以提供扩展存储和高可用性。 DB2 pureScale 安装在运行 Linux 的 Azure 虚拟机上。

此方法是一个模板，可以根据组织的大小和规模进行修改。 它基于以下条件：

-   两个或多个数据库成员与至少两个 CF 节点组合。 节点管理共享内存的全局缓冲池 (GBP) 和全局锁定管理器 (GLM) 服务以控制来自活动成员的共享访问和锁定争用。 一个 CF 节点充当主节点，另一个 CF 节点充当次要故障转移 CF 节点。 若要避免环境中的单一故障点，DB2 pureScale 群集至少需要四个节点。

-   高性能共享存储（在图中以 P30 大小显示）。 每个节点都使用此存储。

-   用于数据成员和共享存储的高性能网络。

### <a name="compute-considerations"></a>计算注意事项

此体系结构在 Azure 虚拟机上运行应用程序、存储和数据层。 [部署设置脚本](https://aka.ms/db2onazure)将创建以下内容：

-   一个 DB2 pureScale 群集。 Azure 上所需的计算资源类型取决于你的设置。 一般情况下，可以使用两种方法：

    -   使用中小型实例可以在其中访问共享存储的多节点、高性能计算 (HPC) 式网络。 对于此 HPC 类型的配置，Azure 内存优化 E 系列或存储优化 L 系列[虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)可以提供所需的计算能力。

    -   为数据引擎使用较少的大型虚拟机实例。 对于大型实例，最大的内存优化 [M 系列](https://azure.microsoft.com/pricing/details/virtual-machines/series/)虚拟机非常适合大量的内存中工作负载。 你可能需要一个专用实例，具体取决于用于运行 DB2 的逻辑分区 (LPAR) 的大小。

-   DB2 CF 使用内存优化的虚拟机，例如 E 系列或 L 系列。

-   使用标准\_DS4\_v2 运行 Linux 的虚拟机的共享存储群集。

-   管理 jumpbox 是运行 Linux 的标准\_DS2\_v2 虚拟机。  一种替代方法是使用 Azure 堡垒，该服务为虚拟网络中的所有 Vm 提供安全的 RDP/SSH 体验。

-   客户端是运行 Windows 的标准 \_DS3\_ v2 虚拟机（用于测试）。

-   *可选*。 见证服务器。 仅在某些早期版本的 Db2 pureScale 中需要。 此示例使用运行 Linux （用于 DB2 pureScale）的标准\_DS3\_2 虚拟机。

> [!NOTE]
> DB2 pureScale 群集至少需要两个 DB2 实例。 还需要缓存实例和锁定管理器实例。

### <a name="storage-considerations"></a>存储注意事项

与 Oracle RAC 类似，DB2 pureScale 是一个高性能的块 I/O，横向扩展的数据库。 我们建议使用最适合满足你需求的 [Azure 高级 SSD](disks-types.md)选项。 较小的存储选项可能适用于开发和测试环境，而生产环境通常需要更多的存储容量。 出于 IOPS 与大小和价格比率的原因，此示例体系结构使用了 [P30](https://azure.microsoft.com/pricing/details/managed-disks/)。 不考虑大小，请使用高级存储以获得最佳性能。

DB2 pureScale 使用共享的所有体系结构，所有数据都可以从所有群集节点进行访问。 高级存储必须在多个实例之间共享，无论是按需还是在专用实例上。

一个大型 DB2 pureScale 群集可能需要 200 兆兆字节 (TB) 或更高的高级共享存储，且 IOPS 为 100,000。 DB2 pureScale 支持可以在 Azure 上使用的 iSCSI 块接口。 ISCSI 接口需要可使用 S2D 或其他工具实现的共享存储群集。 此类解决方案将在 Azure 中创建虚拟存储区域网络 (vSAN) 设备。 DB2 pureScale 使用 vSAN 来安装用于在虚拟机之间共享数据的群集文件系统。

### <a name="networking-considerations"></a>网络注意事项

IBM 建议为 DB2 pureScale 群集中的所有成员提供 InfiniBand 网络。 DB2 pureScale 还为 CF 使用远程直接内存访问 (RDMA)（如果可用）。

在安装过程中，将创建一个 Azure [资源组](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)来包含所有虚拟机。 一般情况下，可根据资源的生存期及其管理者将资源分组。 此体系结构中的虚拟机需要[加速网络](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)。 这是一项 Azure 功能，可以通过单根 I/O虚拟化 (SR-IOV) 向虚拟机提供一致的超低网络延迟。

每台 Azur e虚拟机都部署到具有子网的虚拟网络中：主子网、Gluster FS 前端 (gfsfe)、Gluster FS 后端 (bfsbe)、DB2 pureScale (dB2be) 和 DB2 pureScale 前端 (db2fe)。 安装脚本还会在主子网中的虚拟机上创建主 [NIC](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)。

[网络安全组](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)用于限制虚拟网络中的流量并隔离子网。

在 Azure 上，DB2 pureScale 需要使用 TCP/IP 作为存储的网络连接。

## <a name="next-steps"></a>后续步骤

-   [在 Azure 上部署此体系结构](deploy-ibm-db2-purescale-azure.md)
