---
title: 故障转移群集实例
description: 了解有关 Azure 虚拟机上的 SQL Server (Fci) 的故障转移群集实例。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: e5862daa21f8bf0075bb1dee567cbe887ec32d72
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653267"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>在 Azure 虚拟机上具有 SQL Server 的故障转移群集实例
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍了在 Azure 虚拟机 (Vm) 中使用故障转移群集实例时的功能差异 (FCI SQL Server) 。 

## <a name="overview"></a>概述

Azure Vm 上的 SQL Server 使用 Windows Server 故障转移群集 (WSFC) 功能通过冗余在服务器实例级别提供本地高可用性：故障转移群集实例。 FCI 是跨 (WSFC 安装的 SQL Server 的单个实例，或者只是群集) 节点，甚至在多个子网中。 在网络中，FCI 显示为在单台计算机上运行 SQL Server 的实例。 但如果当前节点不可用，则 FCI 提供从一个 WSFC 节点到另一个 WSFC 节点的故障转移。

本文的其余部分重点介绍在 Azure Vm 上与 SQL Server 一起使用故障转移群集实例时，它们之间的差异。 若要详细了解故障转移群集技术，请参阅： 

- [Windows 群集技术](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 故障转移群集实例](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

使用 Azure 虚拟机上 SQL Server 的故障转移群集实例支持使用磁盘见证、云见证或群集仲裁的文件共享见证。

若要了解详细信息，请参阅 [Azure 中的 SQL Server vm 的仲裁最佳实践](hadr-cluster-best-practices.md#quorum)。 


## <a name="storage"></a>存储

在传统的本地群集环境中，Windows 故障转移群集使用存储区域网络 (SAN) ，这两个节点都可作为共享存储进行访问。 SQL Server 文件位于共享存储上，并且仅活动节点可以一次访问文件。 

Azure Vm 上的 SQL Server 提供各种选项作为部署 SQL Server 故障转移群集实例的共享存储解决方案： 

||[Azure 共享磁盘](../../../virtual-machines/windows/disks-shared.md)|[高级文件共享](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[存储空间直通 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**最低操作系统版本**| 全部 |Windows Server 2012|Windows Server 2016|
|**最低 SQL Server 版本**|全部|SQL Server 2012|SQL Server 2016|
|**支持的 VM 可用性** |具有邻近位置组的可用性集 |可用性集和可用性区域|可用性集 |
|**支持 FileStream**|是|否|是 |
|**Azure blob 缓存**|否|否|是|

本部分的其余部分列出了可用于 Azure Vm 上的 SQL Server 的每个存储选项的优点和限制。 

### <a name="azure-shared-disks"></a>Azure 共享磁盘

[Azure 共享磁盘](../../../virtual-machines/windows/disks-shared.md) 是 [azure 托管磁盘](../../../virtual-machines/managed-disks-overview.md)的一项功能。 Windows Server 故障转移群集支持将 Azure 共享磁盘与故障转移群集实例一起使用。 

**支持的操作系统**：全部   
**支持的 SQL 版本**：全部     

**优势**： 
- 适用于希望迁移到 Azure，同时保持高可用性和灾难恢复 (HADR) 体系结构的应用程序。 
- 由于 SCSI 永久保留 (SCSI PR) 支持，因此可将群集的应用程序迁移到 Azure。 
- 支持共享 Azure 高级 SSD 适用于所有版本的 SQL Server 并共享 Azure Ultra 磁盘存储 SQL Server 2019。 
- 可以使用单个共享磁盘或条带化多个共享磁盘来创建共享的存储池。 
- 支持 Filestream。


**限制**： 
- 虚拟机必须位于同一可用性集和邻近位置组中。
- 不支持可用性区域。
- 不支持高级 SSD 磁盘缓存。
 
若要开始，请参阅 [SQL Server Azure 共享磁盘的故障转移群集实例](failover-cluster-instance-azure-shared-disks-manually-configure.md)。 

### <a name="storage-spaces-direct"></a>Storage Spaces Direct

[存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) 是 Azure 虚拟机上的故障转移群集支持的 Windows Server 功能。 它提供基于软件的虚拟 SAN。

**支持的操作系统**： Windows Server 2016 及更高版本   
**支持的 SQL 版本**： SQL Server 2016 及更高版本   


**便利** 
- 具有足够的网络带宽，实现了强大且高性能的共享存储解决方案。 
- 支持 Azure blob 缓存，因此可以从缓存本地提供读取。  (更新同时复制到这两个节点。 )  
- 支持 FileStream。 

**限制**
- 仅适用于 Windows Server 2016 及更高版本。 
- 不支持可用性区域。
- 需要将相同的磁盘容量同时附加到这两个虚拟机。 
- 由于正在进行磁盘复制，需要高网络带宽才能实现高性能。 
- 由于存储已附加到每个 VM，因此需要更大的 VM 大小和双倍的存储费用。 

若要开始，请参阅 [存储空间直通 SQL Server 故障转移群集实例](failover-cluster-instance-storage-spaces-direct-manually-configure.md)。 

### <a name="premium-file-share"></a>高级文件共享

[高级文件共享](../../../storage/files/storage-how-to-create-premium-fileshare.md) 是 [Azure 文件](../../../storage/files/index.yml)的一项功能。 高级文件共享是 SSD 支持的，并且一直延迟较低。 它们完全支持在 Windows Server 2012 或更高版本上用于 SQL Server 2012 或更高版本的故障转移群集实例。 高级文件共享为你带来了更大的灵活性，因为你可以在不停机的情况下调整文件共享大小并进行缩放。

**支持的操作系统**： Windows Server 2012 及更高版本   
**支持的 SQL 版本**： SQL Server 2012 及更高版本   

**便利** 
- 仅适用于虚拟机的共享存储解决方案分布在多个可用性区域中。 
- 完全托管的文件系统，具有一位数的延迟和可突增 i/o 性能。 

**限制**
- 仅适用于 Windows Server 2012 及更高版本。 
- 不支持 FileStream。 


若要开始，请参阅 [SQL Server 具有高级文件共享的故障转移群集实例](failover-cluster-instance-premium-file-share-manually-configure.md)。 

### <a name="partner"></a>合作伙伴

有一些合作伙伴群集解决方案支持存储。 

**支持的操作系统**：全部   
**支持的 SQL 版本**：全部   

一个示例使用 SIOS DataKeeper 作为存储。 有关详细信息，请参阅博客文章 [故障转移群集和 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)。

### <a name="iscsi-and-expressroute"></a>iSCSI 和 ExpressRoute

还可以通过 Azure ExpressRoute 公开 iSCSI 目标共享块存储。 

**支持的操作系统**：全部   
**支持的 SQL 版本**：全部   

例如，NetApp 专用存储 (NPS) 使用 Equinix 通过 ExpressRoute 向 Azuer VM 公开 iSCSI 目标。

对于 Microsoft 合作伙伴提供的共享存储和数据复制解决方案，请与供应商联系，以了解在故障转移时访问数据相关的任何问题。

## <a name="connectivity"></a>连接

在 Azure 虚拟机上使用 SQL Server 的故障转移群集实例使用 [分布式网络名称 (DNN) ](hadr-distributed-network-name-dnn-configure.md) 或 [虚拟网络名称 (使用 Azure 负载均衡器](hadr-vnn-azure-load-balancer-configure.md) 将流量路由到) 实例，无论哪个节点当前拥有群集资源。 将某些功能和 DNN 与 SQL Server FCI 结合使用时，还有其他注意事项。 若要了解详细信息，请参阅 [DNN 互操作性与 SQL SERVER FCI](failover-cluster-instance-dnn-interoperability.md) 。 

有关群集连接选项的详细信息，请参阅 [在 Azure vm 上路由 HADR 连接到 SQL Server](hadr-cluster-best-practices.md#connectivity)。 

## <a name="limitations"></a>限制

请考虑以下对 Azure 虚拟机上 SQL Server 的故障转移群集实例的限制。 

### <a name="lightweight-resource-provider"></a>轻型资源提供程序   
目前，Azure 虚拟机上 SQL Server 故障转移群集实例仅受[SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)的[轻型管理模式](sql-vm-resource-provider-register.md#management-modes)支持。 若要从完全扩展模式更改为轻型，请删除相应 Vm 的 **sql 虚拟机** 资源，然后在轻型模式下向 sql VM 资源提供程序注册这些虚拟机资源。 使用 Azure 门户删除 **SQL 虚拟机** 资源时，请清除正确虚拟机旁边的复选框。 

完全扩展支持自动备份、修补和高级门户管理等功能。 在轻型管理模式下重新安装代理后，这些功能对 SQL Server Vm 将不起作用。

### <a name="msdtc"></a>MSDTC   
Azure 虚拟机支持 Windows Server 2019 上的 MSDTC 与群集共享卷上的存储 (CSV) 和 [Azure 标准负载均衡器](../../../load-balancer/load-balancer-standard-overview.md)。

在 Azure 虚拟机上，Windows Server 2016 或更早版本不支持 MSDTC，原因如下：

- 无法将群集 MSDTC 资源配置为使用共享存储。 在 Windows Server 2016 上，如果创建 MSDTC 资源，即使存储可用，它也不会显示任何可用的共享存储。 Windows Server 2019 中已修复此问题。
- 基本负载均衡器不会处理 RPC 端口。


## <a name="next-steps"></a>后续步骤

查看 [群集配置的最佳实践](hadr-cluster-best-practices.md)，然后可以 [为 FCI 准备 SQL Server VM](failover-cluster-instance-prepare-vm.md)。 

有关详细信息，请参阅： 

- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

