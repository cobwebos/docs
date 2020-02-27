---
title: Azure 中 SQL Server 的性能准则 | Microsoft Docs
description: 提供有关优化 Microsoft Azure VM 中的 SQL Server 性能的准则。
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
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
ms.openlocfilehash: 880f1c601cf4132fdec9e5d25b1bf1f2ff175ab7
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77650531"
---
# <a name="performance-guidelines-for-sql-server-in-azure-virtual-machines"></a>Azure 虚拟机中的 SQL Server 的性能准则

## <a name="overview"></a>概述

本文提供了有关在 Microsoft Azure 虚拟机中优化 SQL Server 性能的最佳做法。 在 Azure 虚拟机中运行 SQL Server 时，我们建议继续使用适用于 SQL Server 在本地服务器环境中的相同数据库性能优化选项。 但是，关系数据库在公有云中的性能取决于许多因素，如虚拟机的大小和数据磁盘的配置。

[在 Azure 门户中预配的 SQL Server 映像](quickstart-sql-vm-create-portal.md)遵循一般的存储配置最佳做法（有关存储配置情况的详细信息，请参阅 [SQL Server VM 的存储配置](virtual-machines-windows-sql-server-storage-configuration.md)）。 在预配后，请考虑应用本文中讨论的其他优化措施。 根据你的工作负荷进行选择并通过测试进行验证。

> [!TIP]
> 通常需要在针对成本优化和针对性能优化之间进行权衡。 本文重点介绍获得 SQL Server 在 Azure VM 上的*最佳*性能。 如果工作负荷要求较低，可能不需要下面列出的每项优化。 评估这些建议时应考虑性能需求、成本和工作负荷模式。

## <a name="quick-check-list"></a>快速检查列表

下面是 SQL Server 在 Azure 虚拟机中的优化性能快速检查列表：

| 区域 | 优化 |
| --- | --- |
| [VM 大小](#vm-size-guidance) | -使用具有4个或更多 vCPU （如[E4S_v3](../../ev3-esv3-series.md)或更高版本）的 VM 大小，或者[DS12_v2](../../dv2-dsv2-series-memory.md)或更高版本。<br/><br/> - [Es、Eas、Ds 和 Das 系列](../../sizes-general.md)提供对 OLTP 工作负荷性能所需的 vCPU 比率的最佳内存。 <br/><br/> - [M 系列](../../m-series.md)提供关键任务性能所需的最高内存，非常适合用于数据仓库工作负荷。 <br/><br/> -按照[应用程序性能要求清单](../premium-storage-performance.md#application-performance-requirements-checklist)，在高峰时间收集目标工作负荷的[IOPS](../premium-storage-performance.md#iops)、[吞吐量](../premium-storage-performance.md#throughput)和[延迟](../premium-storage-performance.md#latency)要求，然后选择可根据工作负荷的性能要求扩展的[VM 大小](../sizes-general.md)。|
| [存储](#storage-guidance) | -有关具有 TPC E 和 TPC_C 基准的 Azure Vm 上 SQL Server 性能的详细测试，请参阅博客[优化 OLTP 性能](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)。 <br/><br/> -使用[高级 ssd](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)以获得最佳的价格/性能优势。 为数据文件配置[ReadOnly 缓存](../premium-storage-performance.md#disk-caching)，而不为日志文件配置缓存。 <br/><br/> -如果工作负荷需要的存储延迟少于1毫秒，则使用[超磁盘](../disks-types.md#ultra-disk)。 <br/><br/> -通过[监视应用程序](../premium-storage-performance.md#application-performance-requirements-checklist)，然后选择磁盘类型，收集 SQL Server 数据、日志和临时数据库文件的存储延迟要求。 如果需要 < 1ms 存储延迟，则使用超磁盘，否则使用高级 SSD。 如果只是日志文件需要较低的延迟时间，而不是数据文件的延迟，则仅为日志文件预配所需 IOPS 和吞吐量级别的[超磁盘](../disks-enable-ultra-ssd.md)。 <br/><br/> 建议将 -  [高级文件共享](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)作为 SQL Server 故障转移群集实例的共享存储。 高级文件共享不支持缓存，与高级 SSD 磁盘相比，性能有所限制。 为独立 SQL 实例通过高级文件共享选择高级 SSD 托管磁盘;但利用高级文件共享来实现故障转移群集实例共享存储，以简化维护和灵活的可伸缩性。 <br/><br/> -标准存储只推荐用于开发和测试目的或备份文件，不应用于生产工作负荷。 <br/><br/> - 将[存储帐户](../../../storage/common/storage-create-storage-account.md)和 SQL Server VM 保存在相同的区域。<br/><br/> -在存储帐户上禁用 Azure[异地冗余存储](../../../storage/common/storage-redundancy.md)（异地复制）。  |
| [磁盘](#disks-guidance) | -至少使用2个[高级 SSD 磁盘](../disks-types.md#premium-ssd)（1表示日志文件，1用于数据文件）。 <br/><br/> -对于需要 < 1 ms IO 延迟的工作负荷，请为 M 系列启用写入加速器，并考虑对 Es 和 DS 系列使用超级 SSD 磁盘。 <br/><br/> -在托管数据文件的磁盘上启用[只读缓存](../premium-storage-performance.md#disk-caching)。<br/><br/> -在[为 SQL Server 数据、日志和 TempDB 文件配置存储时，](virtual-machines-windows-sql-server-storage-configuration.md)添加比你的工作负荷所需的额外的20% 高级 IOPS/吞吐量容量 <br/><br/> - 避免使用操作系统或临时磁盘进行数据库存储或日志记录。<br/><br/> - 请勿在托管日志文件的磁盘上启用缓存。  **重要说明**：更改 Azure VM 磁盘的缓存设置时，停止 SQL Server 服务。<br/><br/> -条带化多个 Azure 数据磁盘，以提高存储吞吐量。<br/><br/> - 使用规定的分配大小格式化。 <br/><br/> -将 TempDB 置于本地 SSD `D:\` 驱动器上，用于任务关键型 SQL Server 工作负荷（在选择正确的 VM 大小之后）。 如果从 Azure 门户或 Azure 快速入门模板创建 VM，并[将 TEMP DB 置于本地磁盘上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，则无需执行任何进一步操作;对于所有其他情况，请按照博客中的步骤[使用 ssd 存储 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)来防止在重新启动后发生故障。 如果本地驱动器的容量不足以满足临时数据库大小，请将 Temp DB 置于使用[只读缓存](../premium-storage-performance.md#disk-caching)在高级 SSD 磁盘上[剥离](../premium-storage-performance.md)的存储池中。 |
| [I/O](#io-guidance) |- 启用数据库页面压缩。<br/><br/> - 对数据文件启用即时文件初始化。<br/><br/> - 限制数据库自动增长。<br/><br/> - 禁用数据库自动收缩。<br/><br/> - 将所有数据库（包括系统数据库）转移到数据磁盘。<br/><br/> - 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。<br/><br/> -配置默认备份和数据库文件位置。<br/><br/> - [在内存中启用锁定页面](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows?view=sql-server-2017)。<br/><br/> - 应用 SQL Server 性能修复程序。 |
| [Feature-specific](#feature-specific-guidance) | - 直接备份到 blob 存储。<br/><br/>-使用大于 12 TB 的数据库的[文件快照备份](/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)。 <br/><br/>-使用多个 Temp DB 文件，每个核心1个文件，最多8个文件。<br/><br/>-将操作系统的最大服务器内存设置为90% 或更高，最多为 50 GB。 <br/><br/>-启用软 NUMA。 |

有关*如何*和*为何*进行这些优化的详细信息，请查看以下部分中提供的详细信息和指南。

## <a name="vm-size-guidance"></a>VM 大小指导原则

首先收集高峰时间工作负荷的 cpu、内存和存储吞吐量要求。 \LogicalDisk\Disk Reads/Sec 和 \LogicalDisk\Disk Writes/Sec 性能计数器可用于收集读取和写入 IOPS 要求，并可使用 \LogicalDisk\Disk Bytes/Sec 计数器来收集数据、日志和临时数据库文件的[存储吞吐量要求](../premium-storage-performance.md#disk-caching)。 定义了峰值的 IOPS 和吞吐量要求后，评估 VM 大小会提供该容量。 例如，如果你的工作负荷在高峰时间要求 20 K 读取 IOPS 和10K 写入 IOPS，则可以选择 E16s_v3 （最多 32 K 缓存和25600未缓存 IOPS），也可以选择包含2个 P30 磁盘的 M16_s （最多 20 K 缓存和10K 未缓存 IOPS）。 请确保了解工作负荷的吞吐量和 IOPS 要求，因为 Vm 具有不同的 IOPS 和吞吐量的规模限制。<br/><br/>[DSv_3](../../dv3-dsv3-series.md)和[Es_v3 系列](../../ev3-esv3-series.md)在具有 Intel Haswell 或 Broadwell 处理器的通用硬件上托管。 [M 系列](../../m-series.md)为最大的 SQL Server 工作负荷提供最高的 vCPU 计数和内存，并在 Skylake 处理器系列上托管在内存优化硬件上。 这些 VM 系列支持高级存储，建议使用主机级别的读取缓存来实现最佳性能。 Es_v3 和 M 系列也可用于[受约束的核心大小](../../windows/constrained-vcpu.md)，这为具有较低计算和高存储容量需求的工作负荷节省资金。 

## <a name="storage-guidance"></a>存储指导原则

若要详细测试具有 TPC E 和 TPC_C 基准的 Azure Vm 上的 SQL Server 性能，请参阅博客[优化 OLTP 性能](https://techcommunity.microsoft.com/t5/SQL-Server/Optimize-OLTP-Performance-with-SQL-Server-on-Azure-VM/ba-p/916794)。 

对于所有生产工作负荷，建议使用高级 Ssd 的 Azure blob 缓存。 

> [!WARNING]
> 标准 HDD 和 SSD 具有不同的延迟和带宽，建议仅用于开发/测试工作负荷。 生产工作负荷应使用高级 SSD。

此外，我们建议创建 Azure 存储帐户与 SQL Server 虚拟机在同一数据中心中，以减小传输延迟。 创建存储帐户时应禁用异地复制，因为无法保证在多个磁盘上的写入顺序一致。 相反，请考虑在两个 Azure 数据中心之间配置一个 SQL Server 灾难恢复技术。 有关详细信息，请参阅 [Azure 虚拟机中 SQL Server 的高可用性和灾难恢复](virtual-machines-windows-sql-high-availability-dr.md)。

## <a name="disks-guidance"></a>磁盘指导原则

Azure VM 上有三种主要磁盘类型：

* **OS 磁盘**：创建 Azure 虚拟机时，该平台至少将一个磁盘（标记为 **C** 驱动器）附加到 VM 作为操作系统磁盘。 此磁盘是一个 VHD，在存储空间中存储为一个页 blob。
* **临时磁盘**：Azure 虚拟机包含另一个称为临时磁盘的磁盘（标记为 **D**: 驱动器）。 这是可用于暂存空间的节点上的一个磁盘。
* **数据磁盘**：还可以将其他磁盘作为数据磁盘附加到虚拟机，这些磁盘会在存储空间中存储为页 Blob。

以下部分说明了有关使用这些不同磁盘的建议。

### <a name="operating-system-disk"></a>操作系统磁盘

操作系统磁盘是可以作为操作系统的运行版本来启动和装载的 VHD，并且标记为 **C** 驱动器。

操作系统磁盘上的默认缓存策略是**读/写**。 对于性能敏感型应用程序，我们建议使用数据磁盘而不是操作系统磁盘。 请参阅下面有关数据磁盘的部分。

### <a name="temporary-disk"></a>临时磁盘

临时存储驱动器（标记为**D**驱动器）不会保留到 Azure blob 存储。 不要在 **D**: 驱动器中存储用户数据库文件或用户事务日志文件。

将 TempDB 置于本地 SSD `D:\` 驱动器上，用于任务关键型 SQL Server 工作负荷（在选择正确的 VM 大小之后）。 如果从 Azure 门户或 Azure 快速入门模板创建 VM，并[将 TEMP DB 置于本地磁盘上](https://techcommunity.microsoft.com/t5/SQL-Server/Announcing-Performance-Optimized-Storage-Configuration-for-SQL/ba-p/891583)，则无需执行任何其他操作;对于所有其他情况，请按照博客中的步骤[使用 ssd 存储 TempDB](https://cloudblogs.microsoft.com/sqlserver/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)来防止在重新启动后发生故障。 如果本地驱动器的容量不足以满足临时数据库大小，请将 Temp DB 置于使用[只读缓存](../premium-storage-performance.md#disk-caching)在高级 SSD 磁盘上[剥离](../premium-storage-performance.md)的存储池中。

对于支持高级 Ssd 的 Vm，还可以将 TempDB 存储在支持高级 Ssd 且启用了读取缓存的磁盘上。


### <a name="data-disks"></a>数据磁盘数

* **将高级 ssd 磁盘用于数据和日志文件**：如果不使用磁盘条带化，请使用两个高级 SSD 磁盘，其中一个磁盘包含日志文件，另一个磁盘包含数据。 每个高级 SSD 根据其大小提供了许多 IOPS 和带宽（MB/s），如文章中所述，[选择磁盘类型](../disks-types.md)。 如果使用磁盘条带化技术，例如存储空间，则可实现最佳性能，因为将具有两个池，一个用于日志文件，另一个用于数据文件。 但是，如果你计划使用 SQL Server 故障转移群集实例（FCI），则必须配置一个池，或者改为使用[高级文件共享](virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share.md)。

   > [!TIP]
   > - 有关针对各种磁盘和工作负荷配置的测试结果，请参阅以下博客文章：[Azure VM 上的 SQL Server 的存储配置准则](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)。
   > - 对于需要约 50,000 IOPS 的 SQL Server 的任务关键型性能，请考虑使用超级 SSD 替换 10 -P30 磁盘。 有关详细信息，请参阅以下博客文章：[与超级 SSD 的任务关键型性能](https://azure.microsoft.com/blog/mission-critical-performance-with-ultra-ssd-for-sql-server-on-azure-vm/)。

   > [!NOTE]
   > 在门户中预配 SQL Server VM 时，可以选择编辑存储配置。 根据配置，Azure 将配置一个或多个磁盘。 使用条带化，可将多个磁盘组合到单个存储池中。 数据文件和日志文件一起位于此配置中。 有关详细信息，请参阅 [SQL Server VM 的存储配置](virtual-machines-windows-sql-server-storage-configuration.md)。

* **磁盘条带化**：为提高吞吐量，可以添加更多的数据磁盘，并使用磁盘条带化。 若要确定数据磁盘的数量，需要分析日志文件以及数据和 TempDB 文件所需的 IOPS 数量和带宽。 请注意，不同的 VM 大小对受支持的 IOP 数量和带宽有不同的限制，请参阅每个 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的 IOPS 表。 遵循以下指南：

  * 对于 Windows 8/Windows Server 2012 或更高版本，按照以下指南使用[存储空间](https://technet.microsoft.com/library/hh831739.aspx)：

      1. 对于数据仓库工作负荷，请将 "交错（条带大小）" 设置为 64 KB （65536个字节），对于数据仓库工作负荷，请将其设置为 "256 KB （262144字节）"，以避免由于分区 这必须使用 PowerShell 设置。
      2. 设置列计数 = 物理磁盘的数量。 配置的磁盘超过 8 个时，请使用 PowerShell（而不是服务器管理器 UI）。 

    例如，以下 PowerShell 创建新的存储池时会交错大小设为 64 KB，将列数设为 2：

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * 对于 Windows 2008 R2 或更早版本，可以使用动态磁盘（操作系统条带化卷），条带大小始终为 64 KB。 在 Windows 8/Windows Server 2012 中，此选项已被弃用。 有关信息，请参阅[虚拟磁盘服务正在过渡到 Windows 存储管理 API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx) 中的支持声明。

  * 如果将[存储空间直通 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm) 与 [SQL Server 故障转移群集实例](virtual-machines-windows-portal-sql-create-failover-cluster.md)配合使用，则必须配置单个池。 尽管可以在该单一池上创建不同的卷，但它们将共享相同的特征，例如相同的缓存策略。

  * 根据负载预期确定与你的存储池相关联的磁盘数。 请记住，不同的 VM 大小允许不同数量的附加数据磁盘。 有关详细信息，请参阅[虚拟机的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

  * 如果使用的不是高级 SSD（开发/测试方案），建议添加 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)支持的最大数量的数据磁盘并使用磁盘条带化。

* **缓存策略**：请注意用于缓存策略的以下建议，具体取决于你的存储配置。

  * 如果为数据文件和日志文件使用不同的磁盘，请在承载着数据文件和 TempDB 数据文件的数据磁盘上启用读取缓存。 这可能会明显提高性能。 不要在存放日志文件的磁盘上启用缓存，因为这会导致性能稍微降低。

  * 如果在单个存储池中使用磁盘条带化，则大多数工作负荷都会从读取缓存受益。 如果日志文件和数据文件分别具有单独的存储池，请仅在数据文件的存储池上启用读取缓存。 在某些高写入工作负荷中，不使用缓存时可能会获得更好的性能。 这只能通过测试来确定。

  * 前面的建议适用于高级 SSD。 如果使用的不是高级 SSD，不要在任何数据磁盘上启用任何缓存。

  * 有关配置磁盘缓存的说明，请参阅以下文章。 有关经典 (ASM) 部署模型，请参阅 [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) 和 [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)。 对于 Azure 资源管理器部署模型，请参阅： [AzOSDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) and [AzVMDataDisk](https://docs.microsoft.com/powershell/module/az.compute/set-azvmdatadisk)。

     > [!WARNING]
     > 请在更改 Azure VM 磁盘的缓存设置时停止 SQL Server 服务，以免出现数据库损坏的情况。

* **NTFS 分配单元大小**：当格式化数据磁盘时，建议为数据和日志文件以及 TempDB 使用 64-KB 分配单元大小。 如果将 TempDB 放置在临时磁盘上（D:\驱动器）通过利用此驱动器获得的性能超出了对64个分配单元大小的需要。 

* **磁盘管理最佳实践**：删除数据磁盘或更改其缓存类型时，请在更改过程中停止 SQL Server 服务。 在 OS 磁盘上更改缓存设置时，Azure 会先停止 VM，在更改缓存类型后再重新启动 VM。 更改数据磁盘的缓存设置时，不会停止 VM，但会在更改期间将数据磁盘从 VM 分离，完成后再重新附加该数据磁盘。

  > [!WARNING]
  > 在进行这些操作时，如果无法停止 SQL Server 服务，则会导致数据库损坏。


## <a name="io-guidance"></a>I/O 指导原则

* 并行化应用程序和请求时可实现使用高级 SSD 的最佳结果。 高级 SSD 专为 IO 队列深度大于 1 的方案设计，因此对于单线程串行请求（即使它们是存储密集型），不会看到明显的性能提升。 例如，这会影响性能分析工具（如 SQLIO）的单线程测试结果。

* 请考虑使用[数据库页压缩](https://msdn.microsoft.com/library/cc280449.aspx)，因为这有助于提高 I/O 密集型工作负荷的性能。 但是，数据压缩可能会增加数据库服务器上的 CPU 消耗。

* 请考虑启用即时文件初始化以减少初始文件分配所需的时间。 要利用即时文件初始化，请将 SE_MANAGE_VOLUME_NAME 授予 SQL Server (MSSQLSERVER) 服务帐户并将其添加到**执行卷维护任务**安全策略。 如果使用的是用于 Azure 的 SQL Server 平台映像，默认服务帐户 (NT Service\MSSQLSERVER) 不会添加到**执行卷维护任务**安全策略。 换而言之，在 SQL Server Azure 平台映像中不启用即时文件初始化。 将 SQL Server 服务帐户添加到**执行卷维护任务**安全策略后，请重新启动 SQL Server 服务。 使用此功能可能有一些安全注意事项。 有关详细信息，请参阅 [数据库文件初始化](https://msdn.microsoft.com/library/ms175935.aspx)。

* **自动增长**被视为只是非预期增长的偶发情况。 自动增长不管理数据和记录每天的增长。 如果使用自动增长，请使用大小开关预先增长文件。

* 请确保禁用**自动收缩**以避免可能对性能产生负面影响的不必要开销。

* 将所有数据库（包括系统数据库）转移到数据磁盘。 有关详细信息，请参阅 [移动系统数据库](https://msdn.microsoft.com/library/ms345408.aspx)。

* 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。 在 SQL Server 配置管理器中右键单击 SQL Server 实例并选择属性，即可实现此目的。 可以在 "**启动参数**" 选项卡中更改错误日志和跟踪文件设置。在 "**高级**" 选项卡中指定转储目录。以下屏幕截图显示错误日志启动参数的位置。

    ![SQL 错误日志屏幕截图](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* 设置默认的备份和数据库文件位置。 使用本文中的建议，并在“服务器属性”窗口中进行更改。 有关说明，请参阅 [View or Change the Default Locations for Data and Log Files (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)（查看或更改数据和日志文件的默认位置 (SQL Server Management Studio)）。 以下屏幕截图演示了进行这些更改的位置。

    ![SQL 数据日志和备份文件](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* 建立锁定的页以减少 IO 和任何分页活动。 有关详细信息，请参阅 [Enable the Lock Pages in Memory Option (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)（启用在内存中锁定页面的选项 (Windows)）。

* 如果运行的是 SQL Server 2012，安装 Service Pack 1 Cumulative Update 10。 此更新包含在 SQL Server 2012 中执行“select into temporary table”语句时出现的 I/O 性能不良的修补程序。 有关信息，请参阅此[知识库文章](https://support.microsoft.com/kb/2958012)。

* 请考虑在传入/传出 Azure 时压缩所有数据文件。

## <a name="feature-specific-guidance"></a>功能特定指南

某些部署可以使用更高级的配置技术，获得更多的性能好处。 下面的列表主要介绍可帮助你实现更佳性能的一些 SQL Server 功能：

### <a name="back-up-to-azure-storage"></a>备份到 Azure 存储
为在 Azure 虚拟机中运行的 SQL Server 执行备份时，可以使用 [SQL Server 备份到 URL](https://msdn.microsoft.com/library/dn435916.aspx)。 此功能是从 SQL Server 2012 SP1 CU2 开始提供的，在备份到附加数据磁盘时建议使用。 备份到 Azure 存储或从中还原时，请按照 [SQL Server 备份到 URL 最佳实践和故障排除以及从 Azure 存储中存储的备份还原](https://msdn.microsoft.com/library/jj919149.aspx)中提供的建议操作。 此外还可以使用 [Azure 虚拟机中 SQL Server 的自动备份](virtual-machines-windows-sql-automated-backup.md)自动执行这些备份。

对于 SQL Server 2012 以前版本，可以使用 [SQL Server 备份到 Azure 工具](https://www.microsoft.com/download/details.aspx?id=40740)。 此工具可以通过使用多个备份条带目标帮助提高备份吞吐量。

### <a name="sql-server-data-files-in-azure"></a>SQL Server Azure 中的数据文件

[Azure 中的 SQL Server 数据文件](https://msdn.microsoft.com/library/dn385720.aspx)这一新功能从 SQL Server 2014 开始提供。 在 SQL Server 上运行 Azure 中的数据文件，与使用 Azure 数据磁盘时的性能特征相当。

### <a name="failover-cluster-instance-and-storage-spaces"></a>故障转移群集实例和存储空间

如果使用的是存储空间，则在 "**确认**" 页上将节点添加到群集时，请清除标记为 **"将所有符合条件的存储添加到群集**" 的复选框。 

![取消选中符合条件的存储](media/virtual-machines-windows-sql-performance/uncheck-eligible-cluster-storage.png)

如果正在使用存储空间，且选中了“将所有符合条件的存储添加到群集”，Windows 会在群集进程中分离虚拟磁盘。 这样一来，这些虚拟磁盘将不会出现在磁盘管理器或资源管理器之中，除非从群集中删除存储空间，并使用 PowerShell 将其重新附加。 存储空间将多个磁盘集合到存储池中。 有关详细信息，请参阅[存储空间](/windows-server/storage/storage-spaces/overview)。

## <a name="next-steps"></a>后续步骤

有关存储和性能的详细信息，请参阅 [Azure VM 上的 SQL Server 的存储配置准则](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/09/25/storage-configuration-guidelines-for-sql-server-on-azure-vm/)

有关安全最佳实践，请参阅 [Azure 虚拟机中 SQL Server 的安全注意事项](virtual-machines-windows-sql-security.md)。

查看 [Azure 虚拟机上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)。
