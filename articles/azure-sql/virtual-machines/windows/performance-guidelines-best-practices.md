---
title: Azure 中 SQL Server 的性能准则 | Microsoft Docs
description: 提供有关在 Microsoft Azure 虚拟机中优化 SQL Server 性能的准则。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/18/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 0e840a9f78a4d6a9fef83abd7b0f011b700f985f
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231931"
---
# <a name="performance-guidelines-for-sql-server-on-azure-virtual-machines"></a>Azure 虚拟机上的 SQL Server 的性能准则
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

本文提供了有关在 Microsoft Azure 虚拟机中优化 SQL Server 性能的指南。

## <a name="overview"></a>概述

 在 Azure 虚拟机上运行 SQL Server 时，建议你继续使用适用于本地服务器环境中的 SQL Server 的相同数据库性能优化选项。 但是，关系数据库在公有云中的性能取决于许多因素，如虚拟机的大小和数据磁盘的配置。

[在 Azure 门户中预配的 SQL Server 映像](sql-vm-create-portal-quickstart.md)遵循一般的存储配置最佳做法（有关存储配置情况的详细信息，请参阅 [SQL Server 虚拟机 (VM) 的存储配置](storage-configuration.md)）。 在预配后，请考虑应用本文中讨论的其他优化措施。 根据你的工作负荷进行选择并通过测试进行验证。

> [!TIP]
> 通常需要在针对成本优化和针对性能优化之间进行权衡。 本文重点介绍了如何使 Azure 虚拟机上的 SQL Server 实现最佳性能。 如果工作负荷要求较低，可能不需要下面列出的每项优化。 评估这些建议时应考虑性能需求、成本和工作负荷模式。

## <a name="quick-checklist"></a>快速核对清单

下面是一个快速核对清单，用于优化 Azure 虚拟机上的 SQL Server 性能：

| 区域 | 优化 |
| --- | --- |
| [VM 大小](#vm-size-guidance) | - 使用具有 4 个或更多 vCPU 的 VM 大小，例如 [E4S_v3](../../../virtual-machines/ev3-esv3-series.md) 或更大，或者 [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md) 或更大。<br/><br/> - [Es、Eas、Ds 和 Das 系列](../../../virtual-machines/sizes-general.md)提供 OLTP 工作负载性能所需的最佳内存与 vCPU 之比。 <br/><br/> - [M 系列](../../../virtual-machines/m-series.md)提供最高的内存-vCPU 比，可满足任务关键型工作负荷的性能需求，非常适合用于数据仓库工作负荷。 <br/><br/> - 遵循[应用程序性能要求查检表](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist)收集目标工作负荷在高峰期的 [IOPS](../../../virtual-machines/windows/premium-storage-performance.md#iops)、[吞吐量](../../../virtual-machines/windows/premium-storage-performance.md#throughput)和[延迟](../../../virtual-machines/windows/premium-storage-performance.md#latency)要求，然后选择可以根据工作负荷性能要求进行缩放的 [VM 大小](../../../virtual-machines/sizes-general.md)。|
| [存储](#storage-guidance) | - 若要详细了解根据 TPC-E 和 TPC_C 基准在 Azure 虚拟机上进行的 SQL Server 性能测试，请参阅博客：[优化 OLTP 性能](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)。 <br/><br/> - 使用[高级 SSD](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794) 可获得最高性价比。 为数据文件配置[只读缓存](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)，不要为日志文件配置缓存。 <br/><br/> - 如果工作负载要求存储延迟低于 1 毫秒，请使用 [超级磁盘](../../../virtual-machines/windows/disks-types.md#ultra-disk)。 若要了解详细信息，请参阅[迁移到超磁盘](storage-migrate-to-ultradisk.md)。 <br/><br/> - 先通过[监视应用程序](../../../virtual-machines/windows/premium-storage-performance.md#application-performance-requirements-checklist)的方式收集 SQL Server 数据、日志和 TempDB 文件的存储延迟要求，再选择磁盘类型。 如果要求存储延迟低于 1 毫秒，使用超级磁盘；否则使用高级 SSD。 如果只有日志文件需要低延迟，数据文件不需要，请仅为日志文件在所需的 IOPS 和吞吐量级别[预配超级磁盘](../../../virtual-machines/windows/disks-enable-ultra-ssd.md)。 <br/><br/> -  建议将[高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)作为 SQL Server 故障转移群集实例的共享存储。 高级文件共享不支持缓存，与高级 SSD 盘相比，它提供的性能有限。 为独立的 SQL 实例选择高级 SSD 托管磁盘，而不是高级文件共享；但将高级文件共享用作故障转移群集实例的共享存储，以简化维护并灵活缩放。 <br/><br/> - 建议仅将标准存储用于开发和测试目的，或用于备份文件，不应该用于生产工作负载。 <br/><br/> - 将[存储帐户](../../../storage/common/storage-create-storage-account.md)和 SQL Server VM 保存在相同的区域。<br/><br/> - 在存储帐户中禁用 Azure [异地冗余存储](../../../storage/common/storage-redundancy.md)（异地复制）。  |
| [磁盘](#disks-guidance) | - 使用至少 2 个[高级 SSD 盘](../../../virtual-machines/windows/disks-types.md#premium-ssd)（一个用于日志文件，一个用于数据文件）。 <br/><br/> - 对于 IO 延迟要求为低于 1 毫秒的工作负载，请为 M 系列启用写入加速器，并考虑为 Es 和 DS 系列使用超级 SSD 盘。 <br/><br/> - 在托管数据文件的磁盘上启用[只读缓存](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)。<br/><br/> - [为 SQL Server 数据、日志和 TempDB 文件配置存储](storage-configuration.md)时，请在工作负荷的要求以外额外添加 20% 的高级 IOPS/吞吐容量。 <br/><br/> - 避免使用操作系统或临时磁盘进行数据库存储或日志记录。<br/><br/> - 请勿在托管日志文件的磁盘上启用缓存。  **重要说明**：更改 Azure 虚拟机磁盘的缓存设置时，请停止 SQL Server 服务。<br/><br/> - 条带化多个 Azure 数据磁盘，以提高存储吞吐量。<br/><br/> - 使用规定的分配大小格式化。 <br/><br/> - 将 TempDB 放在本地 SSD `D:\` 驱动器上，用于任务关键型 SQL Server 工作负荷（在选择正确的 VM 大小后）。 如果使用 Azure 门户或 Azure 快速入门模板创建 VM，并[将临时数据库置于本地磁盘上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，则无需执行任何其他操作；对于所有其他情况，请按博客文章[使用 SSD 存储 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) 中的步骤操作，以防止重启后发生故障。 如果本地驱动器的容量对于 TempDB 而言不足，请将 TempDB 放在位于具有[只读缓存](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)的高级 SSD 盘上的[条带化](../../../virtual-machines/windows/premium-storage-performance.md)存储池上。 |
| [I/O](#io-guidance) |- 启用数据库页面压缩。<br/><br/> - 对数据文件启用即时文件初始化。<br/><br/> - 限制数据库自动增长。<br/><br/> - 禁用数据库自动收缩。<br/><br/> - 将所有数据库（包括系统数据库）转移到数据磁盘。<br/><br/> - 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。<br/><br/> - 配置默认的备份和数据库文件位置。<br/><br/> - [在内存中启用锁定页面](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017)。<br/><br/> - 应用 SQL Server 性能修复程序。 |
| [Feature-specific](#feature-specific-guidance) | - 直接备份到 Azure Blob 存储。<br/><br/>- 对于大于 12 TB 的数据库，请使用[文件快照备份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)。 <br/><br/>- 使用多个临时数据库文件，每个核心处理 1 个文件，最多处理 8 个文件。<br/><br/>- 将最大服务器内存设置为操作系统剩余内存的 90% 或最大 50 GB。 <br/><br/>- 启用软 NUMA。 |

有关如何和为何进行这些优化的详细信息，请参阅以下部分提供的详细信息与指导 。

## <a name="vm-size-guidance"></a>VM 大小指导原则

首先收集工作负荷在高峰期的 CPU、内存和存储吞吐量要求。 可以使用 \LogicalDisk\Disk Reads/Sec 和 \LogicalDisk\Disk Writes/Sec 性能计数器收集读取和写入 IOPS 要求，可以使用 \LogicalDisk\Disk Bytes/Sec 计数器收集数据、日志和临时数据库文件的[存储吞吐量要求](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)。 定义高峰期的 IOPS 和吞吐量要求后，评估 VM 大小是否可提供这么多的容量。 例如，如果工作负荷在高峰期需要 20K 次读取 IOPS 和 10K 次写入 IOPS，则可以选择 E16s_v3（最高提供 32K 次缓存的 IOPS 和 25600 次未缓存的 IOPS），或包含 2 个 P30 磁盘的 M16_s（最高提供 20K 次缓存的 IOPS 和 10K 次未缓存的 IOPS）。 请确保了解工作负载的吞吐量和 IOPS 要求，因为 VM 具有不同的 IOPS 和吞吐量缩放限制。<br/><br/>[DSv_3](../../../virtual-machines/dv3-dsv3-series.md) 和 [Es_v3 系列](../../../virtual-machines/ev3-esv3-series.md)托管在通用硬件上，其中配备了 Intel Haswell 或 Broadwell 处理器。 [M 系列](../../../virtual-machines/m-series.md)为最大的 SQL Server 工作负载提供最大的 vCPU 计数和内存，并托管在使用 Skylake 处理器系列的内存优化硬件上。 这些 VM 系列支持高级存储，建议在其上配置主机级读取缓存，以实现最佳性能。 还可以在[受约束的核心大小](../../../virtual-machines/windows/constrained-vcpu.md)中选择 Es_v3 和 M 系列，这样能为计算需求低、存储容量需求高的工作负荷节省成本。 

## <a name="storage-guidance"></a>存储指导原则

若要详细了解根据 TPC-E 和 TPC_C 基准在 Azure 虚拟机上进行的 SQL Server 性能测试，请参阅博客：[优化 OLTP 性能](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)。 

对于所有生产工作负荷，建议结合使用 Azure Blob 缓存和高级 SSD。 

> [!WARNING]
> 标准 HDD 和 SSD 具有不同的延迟和带宽，建议仅用于开发/测试工作负荷。 生产工作负荷应使用高级 SSD。

此外，我们建议在 SQL Server 虚拟机所在的数据中心内创建 Azure 存储帐户，以减小传输延迟。 创建存储帐户时应禁用异地复制，因为无法保证在多个磁盘上的写入顺序一致。 相反，请考虑在两个 Azure 数据中心之间配置一个 SQL Server 灾难恢复技术。 有关详细信息，请参阅 [《High Availability and Disaster Recovery for SQL Server on Azure Virtual Machines》](business-continuity-high-availability-disaster-recovery-hadr-overview.md)（Azure 虚拟机中 SQL Server 的高可用性和灾难恢复）。

## <a name="disks-guidance"></a>磁盘指导原则

Azure 虚拟机上有三种主要磁盘类型：

* **OS 磁盘**：创建 Azure 虚拟机时，该平台至少将一个磁盘（标记为 **C** 驱动器）附加到 VM 作为操作系统磁盘。 此磁盘是一个 VHD，在存储空间中存储为一个页 blob。
* **临时磁盘**：Azure 虚拟机包含另一个称为临时磁盘的磁盘（标记为 **D**: 驱动器）。 这是可用于暂存空间的节点上的一个磁盘。
* **数据磁盘**：还可以将其他磁盘作为数据磁盘附加到虚拟机，这些磁盘在存储空间中存储为页 Blob。

以下部分说明了有关使用这些不同磁盘的建议。

### <a name="operating-system-disk"></a>操作系统磁盘

操作系统磁盘是可以作为操作系统的运行版本来启动和装载的 VHD，标记为 **C** 驱动器。

操作系统磁盘上的默认缓存策略是 **读/写**。 对于性能敏感型应用程序，我们建议使用数据磁盘而不是操作系统磁盘。 请参阅下面有关数据磁盘的部分。

### <a name="temporary-disk"></a>临时磁盘

临时存储驱动器，标记为 **D** 驱动器，不会持久保存到 Azure Blob 存储中。 不要在 **D**: 驱动器中存储用户数据库文件或用户事务日志文件。

将 TempDB 放在本地 SSD `D:\` 驱动器上，用于任务关键型 SQL Server 工作负荷（在选择正确的 VM 大小后）。 如果从 Azure 门户或 Azure 快速入门模板创建 VM，并[将临时数据库置于本地磁盘上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，则无需执行任何其他操作；对于所有其他情况，请按博客文章[使用 SSD 存储 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/) 中的步骤操作，以防止重启后发生故障。 如果本地驱动器的容量对于 TempDB 而言不足，请将 TempDB 放在位于具有[只读缓存](../../../virtual-machines/windows/premium-storage-performance.md#disk-caching)的高级 SSD 盘上的[条带化](../../../virtual-machines/windows/premium-storage-performance.md)存储池上。

对于支持高级 SSD 的 VM，你也可以将 TempDB 存储在支持高级 SSD 且已启用读缓存的磁盘上。


### <a name="data-disks"></a>数据磁盘数

* **将高级 SSD 盘用于数据和日志文件**：如果不使用磁盘条带化，请使用两个高级 SSD 盘，一个包含日志文件，另一个包含数据。 每个高级 SSD 均根据其大小提供许多 IOPS 和带宽 (MB/s)，如[选择磁盘类型](../../../virtual-machines/windows/disks-types.md)一文中所述。 如果使用磁盘条带化技术，例如存储空间，则可实现最佳性能，因为将具有两个池，一个用于日志文件，另一个用于数据文件。 但是，如果你打算使用 SQL Server 故障转移群集实例 (FCI)，则必须配置一个池，或者改用[高级文件共享](failover-cluster-instance-premium-file-share-manually-configure.md)。

   > [!TIP]
   > - 有关针对各种磁盘和工作负荷配置的测试结果，请参阅以下博客文章：[Azure 虚拟机上的 SQL Server 的存储配置准则](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)。
   > - 对于需要约 50,000 IOPS 的 SQL Server 的任务关键型性能，请考虑使用超级 SSD 替换 10 -P30 磁盘。 有关详细信息，请参阅以下博客文章：[具有超级 SSD 的任务关键型性能](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)。

   > [!NOTE]
   > 在门户中预配 SQL Server VM 时，可以选择编辑存储配置。 Azure 将根据你所做的配置来配置一个或多个磁盘。 将使用条带化功能将多个磁盘组合到单个存储池中。 数据文件和日志文件一起位于此配置中。 有关详细信息，请参阅 [SQL Server VM 的存储配置](storage-configuration.md)。

* **磁盘条带化**：为提高吞吐量，可以添加更多的数据磁盘，并使用磁盘条带化。 若要确定数据磁盘的数量，需要分析日志文件以及数据和 TempDB 文件所需的 IOPS 数量和带宽。 请注意，不同的 VM 大小对受支持的 IOP 数量和带宽有不同的限制，请参阅每个 [VM 大小](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的 IOPS 表。 遵循以下指南：

  * 对于 Windows 8/Windows Server 2012 或更高版本，按照以下指南使用[存储空间](https://technet.microsoft.com/library/hh831739.aspx)：

      1. 对于 OLTP 工作负荷，将交错（条带大小）设置为 64 KB（65,536 字节），对于数据仓库工作负荷，将交错（条带大小）设置为 256 KB（262,144 字节），以避免分区定位错误导致的性能影响。 这必须使用 PowerShell 设置。
      2. 设置列计数 = 物理磁盘的数量。 配置的磁盘超过 8 个时，请使用 PowerShell（而不是服务器管理器 UI）。 

    例如，以下 PowerShell 创建新的存储池时将交错大小设为 64 KB，将列数设为 2：

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * 对于 Windows 2008 R2 或更早版本，可以使用动态磁盘（操作系统条带化卷），条带大小始终为 64 KB。 从 Windows 8/Windows Server 2012 开始不再提供此选项。 有关信息，请参阅[虚拟磁盘服务正在过渡到 Windows 存储管理 API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx) 中的支持声明。

  * 如果将[存储空间直通 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) 与 [SQL Server 故障转移群集实例](failover-cluster-instance-storage-spaces-direct-manually-configure.md)配合使用，则必须配置单个池。 虽然可以在该单个池上创建不同的卷，但它们都拥有相同的特征，例如相同的缓存策略。

  * 根据负载预期确定与你的存储池相关联的磁盘数。 请记住，不同的 VM 大小允许不同数量的附加数据磁盘。 有关详细信息，请参阅[虚拟机的大小](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

  * 如果使用的不是高级 SSD（开发/测试方案），建议添加 [VM 大小](../../../virtual-machines/windows/sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)支持的最大数量的数据磁盘并使用磁盘条带化。

* **缓存策略**：请注意用于缓存策略的以下建议，具体取决于你的存储配置。

  * 如果为数据文件和日志文件使用不同的磁盘，请在承载着数据文件和 TempDB 数据文件的数据磁盘上启用读取缓存。 这可能会明显提高性能。 不要在存放日志文件的磁盘上启用缓存，因为这会导致性能稍微降低。

  * 如果在单个存储池中使用磁盘条带化，则大多数工作负荷都会从读取缓存受益。 如果日志文件和数据文件分别具有单独的存储池，请仅在数据文件的存储池上启用读取缓存。 在某些高写入工作负荷中，不使用缓存时可能会获得更好的性能。 这只能通过测试来确定。

  * 前面的建议适用于高级 SSD。 如果使用的不是高级 SSD，不要在任何数据磁盘上启用任何缓存。

  * 有关配置磁盘缓存的说明，请参阅以下文章。 有关经典 (ASM) 部署模型，请参阅：[Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) 和 [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)。 有关 Azure 资源管理器部署模型，请参阅：[Set-AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 和 [Set-AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk)。

     > [!WARNING]
     > 请在更改 Azure 虚拟机磁盘的缓存设置时停止 SQL Server 服务，以免出现数据库损坏的情况。

* **NTFS 分配单元大小**：格式化数据磁盘时，建议为数据和日志文件以及 TempDB 使用 64-KB 分配单元大小。 如果将 TempDB 放置在临时磁盘上（D:\ 驱动器），利用此驱动器获得的性能使我们不需要使用 64K 分配单元大小。 

* **磁盘管理最佳做法**：删除数据磁盘或更改其缓存类型时，请在更改过程中停止 SQL Server 服务。 在 OS 磁盘上更改缓存设置时，Azure 会先停止 VM，在更改缓存类型后再重新启动 VM。 更改数据磁盘的缓存设置时，不会停止 VM，但会在更改期间将数据磁盘从 VM 分离，完成后再重新附加该数据磁盘。

  > [!WARNING]
  > 在进行这些操作时，如果无法停止 SQL Server 服务，则会导致数据库损坏。


## <a name="io-guidance"></a>I/O 指导原则

* 并行化应用程序和请求时可实现使用高级 SSD 的最佳结果。 高级 SSD 专为 IO 队列深度大于 1 的方案设计，因此对于单线程串行请求（即使它们是存储密集型），不会看到明显的性能提升。 例如，这会影响性能分析工具（如 SQLIO）的单线程测试结果。

* 请考虑使用[数据库页压缩](https://msdn.microsoft.com/library/cc280449.aspx)，因为这有助于提高 I/O 密集型工作负荷的性能。 但是，数据压缩可能会增加数据库服务器上的 CPU 消耗。

* 请考虑启用即时文件初始化以减少初始文件分配所需的时间。 要利用即时文件初始化，请将 SE_MANAGE_VOLUME_NAME 授予 SQL Server (MSSQLSERVER) 服务帐户并将其添加到**执行卷维护任务**安全策略。 如果使用的是用于 Azure 的 SQL Server 平台映像，默认服务帐户 (NT Service\MSSQLSERVER) 不会添加到**执行卷维护任务**安全策略。 换而言之，在 SQL Server Azure 平台映像中不启用即时文件初始化。 将 SQL Server 服务帐户添加到“执行卷维护任务”安全策略后，请重启 SQL Server 服务。 使用此功能可能有一些安全注意事项。 有关详细信息，请参阅[数据库文件初始化](https://msdn.microsoft.com/library/ms175935.aspx)。

* 请注意，**自动增长**被视为只是非预期增长的偶发情况。 请勿使用自动增长来管理数据和日志每天的增长。 如果使用自动增长，请使用大小开关预先增长文件。

* 请确保禁用 **自动收缩** 以避免可能对性能产生负面影响的不必要开销。

* 将所有数据库（包括系统数据库）转移到数据磁盘。 有关详细信息，请参阅 [Move System Databases](https://msdn.microsoft.com/library/ms345408.aspx)（移动系统数据库）。

* 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。 在 SQL Server 配置管理器中右键单击 SQL Server 实例并选择属性，即可实现此目的。 可以在“启动参数”选项卡中更改错误日志和跟踪文件设置。在“高级”选项卡中指定转储目录。以下屏幕截图显示了错误日志启动参数的位置。

    ![SQL 错误日志屏幕截图](./media/performance-guidelines-best-practices/sql_server_error_log_location.png)

* 设置默认的备份和数据库文件位置。 使用本文中的建议，并在“服务器属性”窗口中进行更改。 有关说明，请参阅 [View or Change the Default Locations for Data and Log Files (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)（查看或更改数据和日志文件的默认位置 (SQL Server Management Studio)）。 以下屏幕截图演示了进行这些更改的位置。

    ![SQL 数据日志和备份文件](./media/performance-guidelines-best-practices/sql_server_default_data_log_backup_locations.png)
* 建立锁定的页以减少 IO 和任何分页活动。 有关详细信息，请参阅 [Enable the Lock Pages in Memory Option (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)（启用在内存中锁定页面的选项 (Windows)）。

* 如果运行的是 SQL Server 2012，安装 Service Pack 1 Cumulative Update 10。 此更新包含修复程序，适用于在 SQL Server 2012 中执行“select into temporary table”语句时出现 I/O 性能不良的情况。 有关信息，请参阅此 [知识库文章](https://support.microsoft.com/kb/2958012)。

* 请考虑在传入/传出 Azure 时压缩所有数据文件。

## <a name="feature-specific-guidance"></a>功能特定指南

某些部署可以使用更高级的配置技术，获得更多的性能好处。 下面的列表主要介绍可帮助你实现更佳性能的一些 SQL Server 功能：

### <a name="back-up-to-azure-storage"></a>备份到 Azure 存储
为在 Azure 虚拟机中运行的 SQL Server 执行备份时，可以使用 [SQL Server 备份到 URL](https://msdn.microsoft.com/library/dn435916.aspx)。 此功能从 SQL Server 2012 SP1 CU2 开始提供，建议在备份到附加数据磁盘时使用。 备份/还原到 Azure 存储或从中备份/还原时，请按照 [SQL Server 备份到 URL 的最佳做法和故障排除以及从 Azure 存储中存储的备份还原](https://msdn.microsoft.com/library/jj919149.aspx)中提供的建议操作。 此外还可以使用 [Azure 虚拟机上 SQL Server 的自动备份](../../../azure-sql/virtual-machines/windows/automated-backup-sql-2014.md)自动执行这些备份。

对于 SQL Server 2012 以前版本，可以使用 [SQL Server 备份到 Azure 工具](https://www.microsoft.com/download/details.aspx?id=40740)。 此工具可以通过使用多个备份条带目标帮助提高备份吞吐量。

### <a name="sql-server-data-files-in-azure"></a>Azure 中的 SQL Server 数据文件

[Azure 中的 SQL Server 数据文件](https://msdn.microsoft.com/library/dn385720.aspx)这一新功能从 SQL Server 2014 开始提供。 使用 Azure 中的数据文件运行 SQL Server，与使用 Azure 数据磁盘时的性能特征相当。

### <a name="failover-cluster-instance-and-storage-spaces"></a>故障转移群集实例和存储空间

如果使用的是存储空间，则在“确认”页上向群集添加节点时，请清除标记为“将所有符合条件的存储添加到群集”的复选框。 

![取消选中符合条件的存储](./media/performance-guidelines-best-practices/uncheck-eligible-cluster-storage.png)

如果正在使用存储空间，且选中了“将所有符合条件的存储添加到群集”，Windows 将在群集进程中分离虚拟磁盘。 这样一来，这些虚拟磁盘不会出现在磁盘管理器或资源管理器之中，除非从群集中删除存储空间，并使用 PowerShell 将其重新附加。 存储空间会将多个磁盘分组到存储池。 有关详细信息，请参阅[存储空间](/windows-server/storage/storage-spaces/overview)。

## <a name="next-steps"></a>后续步骤

有关存储和性能的详细信息，请参阅 [Azure 虚拟机上的 SQL Server 的存储配置准则](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

有关安全最佳做法，请参阅 [Azure 虚拟机上 SQL Server 的安全注意事项](security-considerations-best-practices.md)。

查看 [Azure 虚拟机上的 SQL Server 概述](sql-server-on-azure-vm-iaas-what-is-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](frequently-asked-questions-faq.md)。
