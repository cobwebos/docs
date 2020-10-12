---
title: 高可用性、灾难恢复和业务连续性
description: 了解 Azure Vm 上适用于 SQL Server 的高可用性、灾难恢复 (HADR) 和业务连续性选项，如 Always On 可用性组、故障转移群集实例、数据库镜像、日志传送以及备份 & 还原到 Azure 存储。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: mathoma
ms.openlocfilehash: 8459ab364fc0af15dd1a1b0035e4ce27d192f7a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91293452"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上的业务连续性和 HADR SQL Server
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

业务连续性意味着在发生灾难时继续业务，规划恢复，并确保数据高度可用。 Azure 虚拟机上的 SQL Server 有助于降低高可用性和灾难恢复 (HADR) 数据库解决方案的成本。 

大多数 SQL Server HADR 解决方案都支持 (Vm) 虚拟机，同时作为 Azure 和混合解决方案。 在仅包含 Azure 的解决方案中，整个 HADR 系统都在 Azure 中运行。 而在混合配置中，解决方案的一部分在 Azure 中运行，另一部分则在组织的本地运行。 Azure 环境具有灵活性，允许部分或完全迁移至 Azure，以满足 SQL Server 数据库系统对于预算和 HADR 的要求。

本文对 Azure Vm 上的 SQL Server 可用的业务连续性解决方案进行比较和对比。 

## <a name="overview"></a>概述

你需要确保数据库系统具有服务级别协议 (的 HADR 功能，) 需要该服务级别协议。 Azure 提供了高可用性机制，例如云服务的服务修复和虚拟机的故障恢复检测，并不保证可以满足 SLA。 尽管这些机制可帮助保护虚拟机的高可用性，但它们并不能保护在 VM 内部运行 SQL Server 的可用性。 

VM 处于联机状态且正常运行时，SQL Server 实例可能会失败。 即使是 Azure 提供的高可用性机制，也会导致 Vm 停机，因为诸如从软件或硬件故障和操作系统升级中恢复之类的事件。

使用名为异地复制的功能来实现 Azure 中的异地冗余存储 (GRS) 。 GRS 可能不是适用于数据库的适当灾难恢复解决方案。 由于异地复制以异步方式发送数据，因此在发生灾难时，可能会丢失最新的更新。 [异地复制支持](#geo-replication-support)部分介绍了有关异地复制限制的详细信息。

## <a name="deployment-architectures"></a>部署体系结构
Azure 支持这些 SQL Server 技术实现业务连续性：

* [Always On 可用性组](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always On 故障转移群集实例 (Fci) ](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [日志传送](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [SQL Server Azure Blob 存储进行备份和还原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [数据库镜像](/sql/database-engine/database-mirroring/database-mirroring-sql-server) -SQL Server 2016 中弃用

你可以将这些技术组合在一起，以实现具有高可用性和灾难恢复功能的 SQL Server 解决方案。 混合部署可能需要与 Azure 虚拟网络建立 VPN 隧道，具体取决于所使用的技术。 以下部分演示了一些示例部署体系结构。

## <a name="azure-only-high-availability-solutions"></a>仅限 Azure：高可用性解决方案

使用 Always On 可用性组，可以在数据库级别为 SQL Server 提供高可用性解决方案。 你还可以使用 Always On 故障转移群集实例在实例级别创建高可用性解决方案。 为了进一步保护，可以通过在故障转移群集实例上创建可用性组，在两个级别上创建冗余。 

| 技术 | 示例体系结构 |
| --- | --- |
| **可用性组** |在同一区域的 Azure VM 中运行的可用性副本提供高可用性。 需要配置域控制器 VM，因为 Windows 故障转移群集需要 Active Directory 域。<br/><br/> 为了实现更高的冗余和可用性，Azure Vm 可以部署在不同的 [可用性区域](../../../availability-zones/az-overview.md) 中，如 [可用性组概述](availability-group-overview.md)中所述。 如果可用性组中的 SQL Server Vm 部署在可用性区域中，请将 [azure 标准负载均衡器](../../../load-balancer/load-balancer-standard-overview.md) 用于侦听器，如 [AZURE SQL VM CLI](availability-group-az-cli-configure.md) 和 [azure 快速入门模板](availability-group-quickstart-template-configure.md) 文章中所述。<br/> ![可用性组](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>有关详细信息，请参阅[在 Azure 中配置可用性组 (GUI)](availability-group-azure-marketplace-template-configure.md)。 |
| **故障转移群集实例** |SQL Server Vm 支持故障转移群集实例。 由于 FCI 功能需要共享存储，因此，五个解决方案可用于 Azure Vm 上的 SQL Server： <br/><br/> -将 [Azure 共享磁盘](failover-cluster-instance-azure-shared-disks-manually-configure.md) 用于 Windows Server 2019。 共享托管磁盘是一种允许同时将托管磁盘附加到多个虚拟机的 Azure 产品。 群集中的 Vm 可以根据群集应用程序通过 SCSI 永久保留 (SCSI PR) 选择的保留来读取或写入附加的磁盘。 SCSI PR 是一种行业标准存储解决方案，适用于在存储区域网络上运行的应用程序 (SAN) 本地运行。 启用托管磁盘上的 SCSI PR 后，可以按原样将这些应用程序迁移到 Azure。 <br/><br/>-使用[存储空间直通 \( S2D \) ](failover-cluster-instance-storage-spaces-direct-manually-configure.md)为 Windows Server 2016 和更高版本提供基于软件的虚拟 SAN。<br/><br/>-使用适用于 Windows Server 2012 和更高版本的 [高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md) 。 高级文件共享是 SSD 支持的，具有一致的延迟，并完全支持与 FCI 配合使用。<br/><br/>-使用合作伙伴解决方案支持的用于群集的存储。 有关使用 SIOS DataKeeper 的具体示例，请参阅博客文章 [故障转移群集和 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)。<br/><br/>-通过 Azure ExpressRoute 对远程 iSCSI 目标使用共享块存储。 例如，NetApp 专用存储 (NPS) 使用 Equinix 通过 ExpressRoute 向 Azuer VM 公开 iSCSI 目标。<br/><br/>对于 Microsoft 合作伙伴提供的共享存储和数据复制解决方案，如有任何关于在故障转移时访问数据的问题，请联系供应商。<br/><br/>||

## <a name="azure-only-disaster-recovery-solutions"></a>仅限 Azure：灾难恢复解决方案
可以通过使用可用性组、数据库镜像或使用存储 blob 进行备份和还原，为 Azure 中的 SQL Server 数据库提供灾难恢复解决方案。

| 技术 | 示例体系结构 |
| --- | --- |
| **可用性组** |可用性副本在 Azure VM 中跨多个数据中心运行以实现灾难恢复。 这种跨区域解决方案可以防止站点完全中断。 <br/> ![可用性组](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>在某个区域内，所有副本应该位于同一云服务和相同的虚拟网络中。 由于每个区域都有一个单独的虚拟网络，因此这些解决方案需要网络到网络连接。 有关详细信息，请参阅 [使用 Azure 门户配置网络到网络连接](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。 有关详细说明，请参阅 [在不同的 Azure 区域配置 SQL Server Always On 可用性组](availability-group-manually-configure-multiple-regions.md)。|
| **数据库镜像** |主体和镜像以及服务器在不同数据库中运行以实现灾难恢复。 必须使用服务器证书部署它们。 Azure VM 上的 SQL Server 2008 或 SQL Server 2008 R2 不支持 SQL Server 数据库镜像。 <br/>![数据库镜像](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **使用 Azure Blob 存储进行备份和还原** |生产数据库直接备份到不同数据中心内的 Blob 存储以实现灾难恢复。<br/>![备份和还原](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>有关详细信息，请参阅 [Azure vm 上的备份和还原 SQL Server](../../../azure-sql/virtual-machines/windows/backup-restore.md)。 |
| 使用 Azure Site Recovery 将 SQL Server 复制和故障转移到 Azure |一个 Azure 数据中心内的生产 SQL Server 实例直接复制到其他 Azure 数据中心中的 Azure 存储，以实现灾难恢复。<br/>![使用 Azure Site Recovery 进行复制](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>有关详细信息，请参阅[使用 SQL Server 灾难恢复和 Azure Site Recovery 来保护 SQL Server](../../../site-recovery/site-recovery-sql.md)。 |


## <a name="hybrid-it-disaster-recovery-solutions"></a>混合 IT：灾难恢复解决方案
可以通过使用可用性组、数据库镜像、日志传送以及备份和还原与 Azure Blob 存储配合使用，在混合 IT 环境中为 SQL Server 数据库提供灾难恢复解决方案。

| 技术 | 示例体系结构 |
| --- | --- |
| **可用性组** |某些可用性副本运行在 Azure VM 中，另一些则在本地运行，以实现跨站点灾难恢复。 生产站点可位于本地，也可以位于 Azure 数据中心。<br/>![可用性组](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>由于所有可用性副本必须在同一故障转移群集中，因此该群集必须同时跨越这两个网络（多子网故障转移群集）。 此配置需要在 Azure 与本地网络之间使用 VPN 连接。<br/><br/>为了成功地对数据库进行灾难恢复，还应在灾难恢复站点上安装副本域控制器。|
| **数据库镜像** |一个合作伙伴在 Azure VM 中运行，另一个则在本地运行，以实现使用服务器证书进行跨站点灾难恢复。 合作伙伴无需位于同一个 Active Directory 域中，并且不需要 VPN 连接。<br/>![数据库镜像](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>另一种数据库镜像方案是一个合作伙伴在 Azure VM 中运行，另一个则在同一 Active Directory 域中本地运行，以实现跨站点灾难恢复。 需要[在 Azure 虚拟网络与本地网络之间使用 VPN 连接](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)。<br/><br/>为了成功地对数据库进行灾难恢复，还应在灾难恢复站点上安装副本域控制器。 Azure VM 上的 SQL Server 2008 或 SQL Server 2008 R2 不支持 SQL Server 数据库镜像。 |
| **日志传送** |一个服务器在 Azure VM 中运行，另一个则在本地运行，以实现跨站点灾难恢复。 日志传送依赖于 Windows 文件共享，因此需要在 Azure 虚拟网络与本地网络之间使用 VPN 连接。<br/>![日志传送](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>为了成功地对数据库进行灾难恢复，还应在灾难恢复站点上安装副本域控制器。 |
| **使用 Azure Blob 存储进行备份和还原** |本地生产数据库直接备份到 Azure Blob 存储以实现灾难恢复。<br/>![备份和还原](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>有关详细信息，请参阅 [备份和还原 Azure 虚拟机上的 SQL Server](../../../azure-sql/virtual-machines/windows/backup-restore.md)。 |
| 使用 Azure Site Recovery 将 SQL Server 复制和故障转移到 Azure |本地生产 SQL Server 实例直接复制到 Azure 存储以实现灾难恢复。<br/>![使用 Azure Site Recovery 进行复制](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>有关详细信息，请参阅[使用 SQL Server 灾难恢复和 Azure Site Recovery 来保护 SQL Server](../../../site-recovery/site-recovery-sql.md)。 |


## <a name="free-dr-replica-in-azure"></a>Azure 中的免费 DR 副本

如果具有 [软件保障](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)，则可以使用 SQL Server 实现混合灾难恢复 (DR) 计划，而不会为被动灾难恢复实例带来额外的许可成本。

在下图中，安装程序使用在 Azure 虚拟机上运行的 SQL Server，该虚拟机使用12核作为使用12个内核的本地 SQL Server 部署的灾难恢复副本。 过去，需要为本地部署和 Azure 虚拟机部署 SQL Server 12 核的。 新权益提供了在 Azure 虚拟机上运行的被动副本权益。 现在，只要满足 Azure 虚拟机上被动副本的灾难恢复条件，就只需为本地运行的 SQL Server 12 个核心提供许可。

![Azure 中的免费灾难恢复副本](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/free-dr-replica-azure.png)

有关详细信息，请参阅[产品许可条款](https://www.microsoft.com/licensing/product-licensing/products)。 

若要启用此权益，请跳到 [SQL Server 虚拟机资源](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource)。 选择 "**设置**" 下的 "**配置**"，然后在 " **SQL Server 许可证**" 下选择 "**灾难恢复**" 选项。 选中该复选框以确认该 SQL Server VM 将用作被动副本，然后选择 " **应用** " 以保存设置。 

![在 Azure 中配置灾难恢复副本](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>有关 Azure 中的 SQL Server HADR 的重要注意事项
Azure VM、存储和网络的运行特征与本地非虚拟化的 IT 基础结构不同。 在 Azure 中成功实现 HADR SQL Server 解决方案要求你了解这些差异并设计你的解决方案以适应它们。

### <a name="high-availability-nodes-in-an-availability-set"></a>可用性集中的高可用性节点
使用 Azure 中的可用性集，可将高可用性节点放置在单独的容错域和更新域中。 Azure 平台为可用性集中的每个虚拟机分配一个更新域和一个容错域。 数据中心内的这种配置可确保在计划内或计划外维护事件期间，至少有一个虚拟机可用，并满足99.95% 的 Azure SLA 要求。 

若要配置高可用性设置，请将所有参与 SQL Server 虚拟机放置在同一可用性集中，以避免在维护事件期间应用程序或数据丢失。 只有同一云服务中的节点可加入同一可用性集。 有关详细信息，请参阅[管理虚拟机的可用性](../../../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

### <a name="high-availability-nodes-in-an-availability-zone"></a>可用性区域中的高可用性节点
可用性区域是 Azure 区域中独特的物理位置。 每个区域由一个或多个数据中心组成，这些数据中心配置了独立电源以及散热和网络设备。 区域内可用性区域的物理分离有助于保护应用程序和数据免受数据中心故障，方法是确保至少有一个虚拟机可用，并满足99.99% 的 Azure SLA 要求。 

若要配置高可用性，请将参与 SQL Server 虚拟机分散到该区域中的可用性区域。 在可用性区域之间进行网络间传输时，会产生额外的费用。 有关详细信息，请参阅[可用性区域](/azure/availability-zones/az-overview)。 


### <a name="failover-cluster-behavior-in-azure-networking"></a>故障转移群集在 Azure 网络中的行为
Azure 中的非 RFC 兼容 DHCP 服务可能会导致创建某些故障转移群集配置失败。 发生此失败的原因是，为群集网络名称分配了重复的 IP 地址，例如与其中一个群集节点相同的 IP 地址。 使用可用性组时，这是一个问题，它依赖于 Windows 故障转移群集功能。

创建两节点群集并使其联机时，请考虑此应用场景：

1. 群集将联机，然后节点1请求群集网络名称的动态分配的 IP 地址。
2. DHCP 服务不会提供除 Node1 自己的 IP 地址以外的任何 IP 地址，因为 DHCP 服务会识别该请求是否来自节点1本身。
3. Windows 检测到同时为节点1和故障转移群集的网络名称分配了重复的地址，并且默认群集组未能联机。
4. 默认群集组移到节点2。 节点2将 Node1 IP 地址视为群集 IP 地址，并使默认群集组联机。
5. 当节点2尝试与节点1建立连接时，定向到节点1的数据包永远不会离开节点2，因为它将 Node1 的 IP 地址解析为其自身 节点2无法与节点1建立连接，然后丢失仲裁并关闭群集。
6. 节点1可以向节点2发送数据包，但节点2无法答复。 NODE1 丢失仲裁并关闭群集。

可以通过将未使用的静态 IP 地址分配给群集网络名称来避免这种情况，以便使群集网络名称联机。 例如，你可以使用链接本地 IP 地址，如169.254.1.1 等。 若要简化此过程，请参阅[在 Azure 中针对可用性组配置 Windows 故障转移群集](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)。

有关详细信息，请参阅[在 Azure 中配置可用性组 (GUI)](availability-group-azure-marketplace-template-configure.md)。

### <a name="support-for-availability-group-listeners"></a>支持可用性组侦听器
运行 Windows Server 2012 及更高版本的 Azure Vm 支持可用性组侦听器。 这种支持的实现，是借助于在 Azure VM 上启用的负载均衡终结点，它们都是可用性组节点。 必须执行特殊的配置步骤，侦听器才能适用于在 Azure 中运行的客户端应用程序和本地运行的应用程序。

设置侦听器时有两个主要选项：“外部(公共)”或“内部”。 External (公有) 侦听器使用面向 internet 的负载均衡器，并与可通过 internet 访问的公共虚拟 IP 相关联。 内部侦听器使用内部负载均衡器，并且仅支持同一虚拟网络中的客户端。 对于任一负载均衡器类型，都必须启用直接服务器返回。 

如果可用性组跨多个 Azure 子网 (例如跨) Azure 区域的部署），则客户端连接字符串必须包含 `MultisubnetFailover=True` 。 这会导致与不同子网中的副本建立并行连接。 有关设置侦听器的说明，请参阅 [在 Azure 中配置可用性组的 ILB 侦听器](availability-group-listener-powershell-configure.md)。


仍可通过直接连接到服务实例，单独连接到每个可用性副本。 此外，由于可用性组与数据库镜像客户端向后兼容，因此可以连接到可用性副本（如数据库镜像伙伴），只要副本配置类似于数据库镜像即可：

* 有一个主副本和一个辅助副本。
* 辅助副本配置为不可读的 (**可读辅助** 副本选项设置为 " **无**) "。

下面是一个示例客户端连接字符串，它对应于使用 ADO.NET 或 SQL Server Native Client 的类似于数据库镜像的配置：

```console
Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;
```

有关客户端连接的详细信息，请参阅：

* [将连接字符串关键字用于 SQL Server 本机客户端](https://msdn.microsoft.com/library/ms130822.aspx)
* [将客户端连接到数据库镜像会话 (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
* [在混合 IT 环境中连接到可用性组侦听器](https://docs.microsoft.com/archive/blogs/sqlalwayson/connecting-to-availability-group-listener-in-hybrid-it)
* [可用性组侦听器、客户端连接和应用程序故障转移 (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
* [将数据库镜像连接字符串用于可用性组](https://technet.microsoft.com/library/hh213417.aspx)

### <a name="network-latency-in-hybrid-it"></a>混合 IT 环境中的网络延迟
假设本地网络和 Azure 之间可能存在较长的网络延迟，请部署 HADR 解决方案。 将副本部署到 Azure 时，请使用异步提交而非同步提交。 在本地和 Azure 中部署数据库镜像服务器时，请使用高性能模式，而非高安全模式。

### <a name="geo-replication-support"></a>异地复制支持
Azure 磁盘中的异地复制不支持将同一数据库的数据文件和日志文件各自存储在不同的磁盘上。 GRS 独立并异步地复制对每个磁盘的更改。 此机制可保证单个磁盘中异地复制副本上的写顺序，但不能保证多个磁盘的异地复制副本上的写顺序。 如果将数据库配置为将其数据文件和其日志文件存储在不同的磁盘上，则灾难后恢复的磁盘可能会包含数据文件的最新副本（而不是日志文件），这会破坏预写日志 SQL Server 和 ACID 属性 (原子性、一致性、隔离和持续性) 事务。 

如果你没有在存储帐户上禁用异地复制的选项，则将数据库的所有数据文件和日志文件保留在同一磁盘上。 如果由于数据库的大小而必须使用多个磁盘，请部署前面列出的某个灾难恢复解决方案以确保数据冗余。

## <a name="next-steps"></a>后续步骤

确定 [可用性组](availability-group-overview.md) 或 [故障转移群集实例](failover-cluster-instance-overview.md) 是否为业务的最佳业务连续性解决方案。 然后查看配置环境以实现高可用性和灾难恢复的 [最佳实践](hadr-cluster-best-practices.md) 。 




