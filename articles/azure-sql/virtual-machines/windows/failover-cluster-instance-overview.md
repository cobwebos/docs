---
title: 故障转移群集实例
description: 了解 Azure 虚拟机上的 SQL Server 故障转移群集实例 (FCI)。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: a8bfa91ac9b70c0ff4f461bd9e10899d1170b24d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91272499"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上的 SQL Server 故障转移群集实例 (FCI)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文介绍了在 Azure 虚拟机 (VM) 上使用 SQL Server 故障转移群集实例 (FCI) 时的功能差异。 

## <a name="overview"></a>概述

Azure VM 上的 SQL Server 使用 Windows Server 故障转移群集 (WSFC) 功能通过冗余在服务器实例级别（故障转移群集实例）上提供本地高可用性。 FCI 是跨 WSFC（或只是群集）节点和（可能）跨多个子网安装的 SQL Server 的单个实例。 在网络上，FCI 显示为在单台计算机上运行的 SQL Server 的一个实例。 但是，如果当前节点变得不可用，则 FCI 提供从一个 WSFC 节点到另一个 WSFC 节点的故障转移。

本文的其余部分重点介绍在将故障转移群集实例与 Azure VM 上的 SQL Server 一起使用时这些实例的区别。 若要详细了解故障转移群集技术，请参阅： 

- [Windows 群集技术](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 故障转移群集实例](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

## <a name="quorum"></a>Quorum

Azure 虚拟机上的 SQL Server 故障转移群集实例支持对群集仲裁使用磁盘见证、云见证或文件共享见证。

若要了解详细信息，请参阅 [Azure 中 SQL Server VM 的仲裁最佳做法](hadr-cluster-best-practices.md#quorum)。 


## <a name="storage"></a>存储

在传统本地群集环境中，Windows 故障转移群集将可供两个节点访问的存储区域网络 (SAN) 用作共享存储。 SQL Server 文件托管在共享存储上，且只有活动节点可同时访问这些文件。 

Azure VM 上的 SQL Server 提供各种选项用作共享存储解决方案，它适用于 SQL Server 故障转移群集实例的部署： 

||[Azure 共享磁盘](../../../virtual-machines/windows/disks-shared.md)|[高级文件共享](../../../storage/files/storage-how-to-create-premium-fileshare.md) |[存储空间直通 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**最低操作系统版本**| All |Windows Server 2012|Windows Server 2016|
|**最低 SQL Server 版本**|All|SQL Server 2012|SQL Server 2016|
|**受支持的 VM 可用性** |具有邻近放置组的可用组集 |可用性集和可用性区域|可用性集 |
|**支持文件流**|是|否|是 |
|**Azure Blob 缓存**|否|否|是|

此部分的其余内容列出了可用于 Azure VM 上的 SQL Server 的每个存储选项的优点和限制。 

### <a name="azure-shared-disks"></a>Azure 共享磁盘

[Azure 共享磁盘](../../../virtual-machines/windows/disks-shared.md)是 [Azure 托管磁盘](../../../virtual-machines/managed-disks-overview.md)的一项功能。 Windows Server 故障转移群集支持将 Azure 共享磁盘与故障转移群集实例一起使用。 

**支持的操作系统**：All   
**支持的 SQL 版本**：All     

**优点：** 
- 适用于希望迁移到 Azure，同时保持其高可用性和灾难恢复 (HADR) 体系结构不变的应用程序。 
- 得益于 SCSI 持久性预留 (SCSI PR) 支持，可按原样将群集应用程序迁移到 Azure。 
- 支持共享的 Azure 高级 SSD 和 Azure 超级磁盘存储。
- 可使用单个共享磁盘或对多个共享磁盘设置带区来创建共享存储池。 
- 支持文件流。


**限制**： 
- 虚拟机必须位于同一可用性集和邻近放置组中。
- 不支持可用性区域。
- 不支持高级 SSD 磁盘缓存。
 
首先，请参阅 [SQL Server 故障转移群集实例与 Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md)。 

### <a name="storage-spaces-direct"></a>存储空间直通

[存储空间直通](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)是一项 Windows Server 功能，可用于 Azure 虚拟机上的故障转移群集。 它提供基于软件的虚拟 SAN。

**支持的操作系统**：Windows Server 2016 及更高版本   
**支持的 SQL 版本**：SQL Server 2016 及更高版本   


**优点：** 
- 充足的网络带宽可实现可靠且性能高的共享存储解决方案。 
- 支持 Azure Blob 缓存，因此可从缓存中本地提供读取。 （更新同步复制到两个节点。） 
- 支持文件流。 

**限制：**
- 仅适用于 Windows Server 2016 及更高版本。 
- 不支持可用性区域。
- 要求对两个虚拟机附加相同的磁盘容量。 
- 由于仍在进行的磁盘复制，需要高网络带宽来实现高性能。 
- 由于对每个 VM 附加了存储，因此需要更大的 VM 大小且存储费用加倍。 

首先，请参阅 [SQL Server 故障转移群集实例与存储空间直通](failover-cluster-instance-storage-spaces-direct-manually-configure.md)。 

### <a name="premium-file-share"></a>高级文件共享

[高级文件共享](../../../storage/files/storage-how-to-create-premium-fileshare.md)是 [Azure 文件存储](../../../storage/files/index.yml)的一项功能。 高级文件共享由 SSD 提供支持，始终具有低延迟。 它们完全支持在 Windows Server 2012 或更高版本上用于 SQL Server 2012 或更高版本的故障转移群集实例。 高级文件共享提供更高的灵活性，使你能在不停机的情况下对文件共享进行大小调整和缩放。

**支持的操作系统**：Windows Server 2012 和更高版本   
**支持的 SQL 版本**：SQL Server 2012 和更高版本   

**优点：** 
- 只有适合虚拟机的共享存储解决方案跨多个可用性区域分布。 
- 完全托管的文件系统，具有单位数的延迟和可突发的 I/O 性能。 

**限制：**
- 仅适用于 Windows Server 2012 及更高版本。 
- 不支持文件流。 


首先，请参阅 [SQL Server 故障转移群集实例与高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)。 

### <a name="partner"></a>Partner

有一些合作伙伴群集解决方案采用受支持的存储。 

**支持的操作系统**：All   
**支持的 SQL 版本**：All   

一个示例是将 SIOS DataKeeper 用作存储。 有关详细信息，请参阅[故障转移群集和 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/) 博客文章。

### <a name="iscsi-and-expressroute"></a>iSCSI 和 ExpressRoute

还可通过 Azure ExpressRoute 公开 iSCSI 目标共享块存储。 

**支持的操作系统**：All   
**支持的 SQL 版本**：All   

例如，NetApp 专用存储 (NPS) 使用 Equinix 通过 ExpressRoute 向 Azuer VM 公开 iSCSI 目标。

对于 Microsoft 合作伙伴提供的共享存储和数据复制解决方案，如有任何关于在故障转移时访问数据的问题，请联系供应商。

## <a name="connectivity"></a>连接

Azure 虚拟机上的 SQL Server 故障转移群集实例使用[分布式网络名称 (DNN)](hadr-distributed-network-name-dnn-configure.md) 或 [Azure 负载均衡器虚拟网络名称 (VNN)](hadr-vnn-azure-load-balancer-configure.md) 将流量路由到 SQL Server 实例，而不管哪个节点当前拥有群集资源。 在将某些功能和 DNN 与 SQL Server FCI 一起使用时，还有其他注意事项。 有关详细信息，请参阅 [DNN 互操作性与 SQL Server FCI](failover-cluster-instance-dnn-interoperability.md)。 

有关群集连接选项的更多详细信息，请参阅[将 HADR 连接路由到 Azure VM 上的 SQL Server](hadr-cluster-best-practices.md#connectivity)。 

## <a name="limitations"></a>限制

使用 Azure 虚拟机上的 SQL Server 故障转移群集实例时，请考虑以下限制。 

### <a name="lightweight-resource-provider"></a>轻量级资源提供程序   
目前，仅支持将 Azure 虚拟机上的 SQL Server 故障转移群集实例用于 [SQL Server IaaS 代理扩展](sql-server-iaas-agent-extension-automate-management.md)的[轻型管理模式](sql-vm-resource-provider-register.md#management-modes)。 要从完全扩展模式更改为轻型模式，请先删除相应 VM 的 SQL 虚拟机资源，再在轻型模式下向 SQL VM 资源提供程序注册它们。 使用 Azure 门户删除 SQL 虚拟机资源时，请取消勾选相应虚拟机旁边的复选框。 

完全扩展支持自动备份、修补和高级门户管理等功能。 在轻型管理模式中重新安装代理后，这些功能将不适用于 SQL Server VM。

### <a name="msdtc"></a>MSDTC 

Azure 虚拟机支持 Windows Server 2019 上的 Microsoft 分布式事务处理协调器 (MSDTC)，其中存储位于群集共享卷 (CSV) 和[标准负载均衡器](../../../load-balancer/load-balancer-standard-overview.md)上，或者位于正在使用 Azure 共享磁盘的 SQL Server VM 上。 

在 Azure 虚拟机上，具有群集共享卷的 Windows Server 2016 或更早版本不支持 MSDTC，因为：

- 无法将群集 MSDTC 资源配置为使用共享存储。 在 Windows Server 2016 上，如果创建 MSDTC 资源，即使存储可用，它也不会显示任何可用的共享存储。 Windows Server 2019 中已修复此问题。
- 基本负载均衡器不处理 RPC 端口。


## <a name="next-steps"></a>后续步骤

请参阅[群集配置最佳做法](hadr-cluster-best-practices.md)，然后可[为 FCI 准备 SQL Server VM](failover-cluster-instance-prepare-vm.md)。 

有关详细信息，请参阅： 

- [Windows 群集技术](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server 故障转移群集实例](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

