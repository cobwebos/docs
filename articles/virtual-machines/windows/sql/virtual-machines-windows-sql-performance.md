---
title: Azure 中 SQL Server 的性能最佳做法 | Microsoft Docs
description: 提供有关优化 Microsoft Azure VM 中的 SQL Server 性能的最佳实践。
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/19/2018
ms.author: jroth
ms.openlocfilehash: 9d3fbbab76f16a8546c431d5acf913bf419edeb4
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2018
ms.locfileid: "31798149"
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Azure 虚拟机中 SQL Server 的性能最佳实践

## <a name="overview"></a>概述

本文提供了有关在 Microsoft Azure 虚拟机中优化 SQL Server 性能的最佳做法。 在 Azure 虚拟机中运行 SQL Server 时，我们建议继续使用适用于 SQL Server 在本地服务器环境中的相同数据库性能优化选项。 但是，关系数据库在公有云中的性能取决于许多因素，如虚拟机的大小和数据磁盘的配置。

[在 Azure 门户中预配的 SQL Server 映像](quickstart-sql-vm-create-portal.md)遵循存储配置最佳做法。 有关如何配置存储的详细信息，请参阅 [SQL Server VM 的存储配置](virtual-machines-windows-sql-server-storage-configuration.md)。 在预配后，请考虑应用本文中讨论的其他优化措施。 根据你的工作负荷进行选择并通过测试进行验证。

> [!TIP]
> 本文重点介绍获得 SQL Server 在 Azure VM 上的*最佳*性能。 如果工作负荷要求较低，可能不需要下面列出的每项优化。 评估这些建议时应考虑性能需求和工作负荷模式。

## <a name="quick-check-list"></a>快速检查列表

下面是 SQL Server 在 Azure 虚拟机中的优化性能快速检查列表：

| 区域 | 优化 |
| --- | --- |
| [VM 大小](#vm-size-guidance) |SQL Enterprise 版：[DS3](../sizes-general.md) 或更高。<br/><br/>SQL Standard 和 Web 版：[DS2](../sizes-general.md) 或更高。 |
| [存储](#storage-guidance) |使用[高级存储](../premium-storage.md)。 仅建议将标准存储用于开发/测试。<br/><br/>将[存储帐户](../../../storage/common/storage-create-storage-account.md)和 SQL Server VM 保存在相同的区域。<br/><br/>在存储帐户上禁用 Azure [异地冗余存储](../../../storage/common/storage-redundancy.md)（异地复制）。 |
| [磁盘](#disks-guidance) |最少使用 2 个 [P30 磁盘](../premium-storage.md#scalability-and-performance-targets)（1 个用于日志文件，1 个用于数据文件和 TempDB；或者将两个或更多磁盘条带化并将所有文件存储在单个卷中）。<br/><br/>避免使用操作系统或临时磁盘进行数据库存储或日志记录。<br/><br/>在托管数据文件和 TempDB 数据文件的磁盘上启用读取缓存。<br/><br/>不要在托管日志文件的磁盘上启用缓存。<br/><br/>重要说明：更改 Azure VM 磁盘的缓存设置时，请停止 SQL Server 服务。<br/><br/>条带化多个 Azure 数据磁盘，提高 IO 吞吐量。<br/><br/>使用规定的分配大小格式化。 |
| [I/O](#io-guidance) |启用数据库页面压缩。<br/><br/>对数据文件启用即时文件初始化。<br/><br/>限制数据库自动增长。<br/><br/>禁用数据库自动收缩。<br/><br/>将所有数据库（包括系统数据库）转移到数据磁盘。<br/><br/>将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。<br/><br/>设置默认的备份和数据库文件位置。<br/><br/>启用锁定页面。<br/><br/>应用 SQL Server 性能修复程序。 |
| [Feature-specific](#feature-specific-guidance) |直接备份到 blob 存储。 |

有关*如何*和*为何*进行这些优化的详细信息，请参阅以下部分提供的详细信息与指导。

## <a name="vm-size-guidance"></a>VM 大小指导原则

对于性能敏感型应用程序，建议使用以下[虚拟机大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)：

* **SQL Server Enterprise Edition**：DS3 或更高
* **SQL Server Standard 和 Web Edition**：DS2 或更高

## <a name="storage-guidance"></a>存储指导原则

DS 系列（以及 DSv2 系列和 GS 系列）VM 支持[高级存储](../premium-storage.md)。 对于生产工作负荷，建议使用高级存储。

> [!WARNING]
> 标准存储具有不同的延迟和带宽，建议仅用于开发/测试工作负荷。 生产工作负荷应使用高级存储。

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

临时存储驱动器，标记为 **D**: 驱动器，不会保留到 Azure Blob 存储区。 不要在 **D**: 驱动器中存储用户数据库文件或用户事务日志文件。

D 系列、Dv2 系列和 G 系列 VM 上的临时驱动器基于 SSD。 如果工作负荷重度使用 TempDB（例如，要处理临时对象或复杂联接），在 **D** 驱动器上存储 TempDB 可能会提高 TempDB 吞吐量并降低 TempDB 延迟。

对于支持高级存储的 VM（DS 系列、DSv2 系列与 GS 系列），建议将 TempDB 存储在支持高级存储且已启用读取缓存的磁盘上。 这项建议有一种例外情况；如果 TempDB 的使用是写入密集型的，则可以通过将 TempDB 存储在本地 **D** 驱动器（在这些计算机大小上也是基于 SSD）上来实现更高性能。

### <a name="data-disks"></a>数据磁盘数

* **将数据磁盘用于数据和日志文件**：如果不使用磁盘条带化，请使用两个高级存储 [P30 磁盘](../premium-storage.md#scalability-and-performance-targets)，一个磁盘包含日志文件，另一个包含数据和 TempDB 文件。 每个高级存储磁盘均根据其大小提供了许多 IOP 和带宽 (MB/s)，如[使用高级存储磁盘](../premium-storage.md)一文中所述。 如果使用磁盘条带化方法（例如存储空间），则建议将所有数据文件和日志文件放在同一驱动器上。

   > [!NOTE]
   > 在门户中预配 SQL Server VM 时，可以选择编辑存储配置。 根据配置，Azure 将配置一个或多个磁盘。 使用条带化，可将多个磁盘组合到单个存储池中。 数据文件和日志文件一起位于此配置中。 有关详细信息，请参阅 [SQL Server VM 的存储配置](virtual-machines-windows-sql-server-storage-configuration.md)。

* **磁盘条带化**：为提高吞吐量，可以添加更多的数据磁盘，并使用磁盘条带化。 若要确定数据磁盘的数量，需要分析日志文件以及数据和 TempDB 文件所需的 IOPS 数量和带宽。 请注意，不同的 VM 大小对受支持的 IOP 数量和带宽有不同的限制，请参阅每个 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的 IOPS 表。 遵循以下指南：

  * 对于 Windows 8/Windows Server 2012 或更高版本，按照以下指南使用[存储空间](https://technet.microsoft.com/library/hh831739.aspx)：

      1. 对于 OLTP 工作负荷，将交错（条带大小）设置为 64 KB（65536 字节），对于数据仓库工作负荷，将交错（条带大小）设置为 256 KB（262144 字节），以避免分区定位错误导致的性能影响。 这必须使用 PowerShell 设置。
      1. 设置列计数 = 物理磁盘的数量。 配置的磁盘超过 8 个时，请使用 PowerShell（而不是服务器管理器 UI）。 

    例如，以下 PowerShell 创建新的存储池时会交错大小设为 64 KB，将列数设为 2：

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * 对于 Windows 2008 R2 或更早版本，可以使用动态磁盘（操作系统条带化卷），条带大小始终为 64 KB。 请注意，从 Windows 8/Windows Server 2012 开始不推荐使用此选项。 有关信息，请参阅[虚拟磁盘服务正在过渡到 Windows 存储管理 API](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx) 中的支持声明。

  * 如果在 [SQL Server 故障转移群集实例](virtual-machines-windows-portal-sql-create-failover-cluster.md)使用[存储空间直通 (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm)，则必须配置单个池。 请注意，虽然可以在该单个池上创建不同的卷，但它们都拥有相同的特征，例如相同的缓存策略。

  * 根据负载预期确定与你的存储池相关联的磁盘数。 请记住，不同的 VM 大小允许不同数量的附加数据磁盘。 有关详细信息，请参阅[虚拟机的大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

  * 如果使用的不是高级存储（开发/测试方案），建议添加 [VM 大小](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)支持的最大数量的数据磁盘并使用磁盘条带化。

* **缓存策略**：请注意用于缓存策略的以下建议，具体取决于你的存储配置。

  * 如果为数据文件和日志文件使用不同的磁盘，请在承载着数据文件和 TempDB 数据文件的数据磁盘上启用读取缓存。 这可能会明显提高性能。 不要在存放日志文件的磁盘上启用缓存，因为这会导致性能稍微降低。

  * 如果使用磁盘条带化，则大多数工作负荷都会从读取缓存受益。 由于使用磁盘条带化可提升性能，因此，即使日志文件位于同一磁盘上，此建议也适用。 在某些高写入工作负荷中，不使用缓存时可能会获得更好的性能。 这只能通过测试来确定。

  * 前面的建议适用于高级存储磁盘。 如果使用的不是高级存储，不要在任何数据磁盘上启用任何缓存。

  * 有关配置磁盘缓存的说明，请参阅以下文章。 有关经典 (ASM) 部署模型，请参阅 [Set-AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) 和 [Set-AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx)。 有关 Azure 资源管理器部署模型，请参阅 [Set-AzureRMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-4.4.1) 和 [Set-AzureRMVMDataDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmdatadisk?view=azurermps-4.4.1)。

     > [!WARNING]
     > 请在更改 Azure VM 磁盘的缓存设置时停止 SQL Server 服务，以免出现数据库损坏的情况。

* **NTFS 分配单元大小**：当格式化数据磁盘时，建议为数据和日志文件以及 TempDB 使用 64-KB 分配单元大小。

* **磁盘管理最佳实践**：删除数据磁盘或更改其缓存类型时，请在更改过程中停止 SQL Server 服务。 在 OS 磁盘上更改缓存设置时，Azure 会先停止 VM，在更改缓存类型后再重新启动 VM。 更改数据磁盘的缓存设置时，不会停止 VM，但会在更改期间将数据磁盘从 VM 分离，完成后再重新附加该数据磁盘。

  > [!WARNING]
  > 在进行这些操作时，如果无法停止 SQL Server 服务，则会导致数据库损坏。

## <a name="io-guidance"></a>I/O 指导原则

* 并行化应用程序和请求时可实现使用高级存储的最佳结果。 高级存储专为 IO 队列深度大于 1 的方案设计，因此对于单线程串行请求（即使它们是存储密集型的），不会看到明显的性能提升。 例如，这会影响性能分析工具（如 SQLIO）的单线程测试结果。

* 请考虑使用[数据库页压缩](https://msdn.microsoft.com/library/cc280449.aspx)，因为这有助于提高 I/O 密集型工作负荷的性能。 但是，数据压缩可能会增加数据库服务器上的 CPU 消耗。

* 请考虑启用即时文件初始化以减少初始文件分配所需的时间。 要利用即时文件初始化，请将 SE_MANAGE_VOLUME_NAME 授予 SQL Server (MSSQLSERVER) 服务帐户并将其添加到**执行卷维护任务**安全策略。 如果使用的是用于 Azure 的 SQL Server 平台映像，默认服务帐户 (NT Service\MSSQLSERVER) 不会添加到**执行卷维护任务**安全策略。 换而言之，在 SQL Server Azure 平台映像中不启用即时文件初始化。 将 SQL Server 服务帐户添加到**执行卷维护任务**安全策略后，请重新启动 SQL Server 服务。 使用此功能可能有一些安全注意事项。 有关详细信息，请参阅[数据库文件初始化](https://msdn.microsoft.com/library/ms175935.aspx)。

* **自动增长**被视为只是非预期增长的偶发情况。 自动增长不管理数据和记录每天的增长。 如果使用自动增长，请使用大小开关预先增长文件。

* 请确保禁用**自动收缩**以避免可能对性能产生负面影响的不必要开销。

* 将所有数据库（包括系统数据库）转移到数据磁盘。 有关详细信息，请参阅 [Move System Databases](https://msdn.microsoft.com/library/ms345408.aspx)（移动系统数据库）。

* 将 SQL Server 错误日志和跟踪文件目录移到数据磁盘。 在 SQL Server 配置管理器中右键单击 SQL Server 实例并选择属性，即可实现此目的。 可以在“启动参数”选项卡中更改错误日志和跟踪文件设置。在“高级”选项卡中指定转储目录。以下屏幕截图显示了错误日志启动参数的位置。

    ![SQL 错误日志屏幕截图](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* 设置默认的备份和数据库文件位置。 使用本文中的建议，并在“服务器属性”窗口中进行更改。 有关说明，请参阅 [View or Change the Default Locations for Data and Log Files (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)（查看或更改数据和日志文件的默认位置 (SQL Server Management Studio)）。 以下屏幕截图演示了进行这些更改的位置。

    ![SQL 数据日志和备份文件](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* 建立锁定的页以减少 IO 和任何分页活动。 有关详细信息，请参阅 [Enable the Lock Pages in Memory Option (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)（启用在内存中锁定页面的选项 (Windows)）。

* 如果运行的是 SQL Server 2012，安装 Service Pack 1 Cumulative Update 10。 此更新包含在 SQL Server 2012 中执行“select into temporary table”语句时出现的 I/O 性能不良的修补程序。 有关信息，请参阅此[知识库文章](http://support.microsoft.com/kb/2958012)。

* 请考虑在传入/传出 Azure 时压缩所有数据文件。

## <a name="feature-specific-guidance"></a>功能特定指南

某些部署可以使用更高级的配置技术，获得更多的性能好处。 下面的列表主要介绍可帮助你实现更佳性能的一些 SQL Server 功能：

* **备份到 Azure 存储**：为在 Azure 虚拟机中运行的 SQL Server 执行备份时，可以使用 [SQL Server 备份到 URL](https://msdn.microsoft.com/library/dn435916.aspx)。 此功能是从 SQL Server 2012 SP1 CU2 开始提供的，在备份到附加数据磁盘时建议使用。 备份到 Azure 存储或从中还原时，请按照 [SQL Server 备份到 URL 最佳实践和故障排除以及从 Azure 存储中存储的备份还原](https://msdn.microsoft.com/library/jj919149.aspx)中提供的建议操作。 此外还可以使用 [Azure 虚拟机中 SQL Server 的自动备份](virtual-machines-windows-sql-automated-backup.md)自动执行这些备份。

    对于 SQL Server 2012 以前版本，可以使用 [SQL Server 备份到 Azure 工具](https://www.microsoft.com/download/details.aspx?id=40740)。 此工具可以通过使用多个备份条带目标帮助提高备份吞吐量。

* **Azure 中的 SQL Server 数据文件**：[Azure 中的 SQL Server 数据文件](https://msdn.microsoft.com/library/dn385720.aspx)这一新功能从 SQL Server 2014 开始提供。 在 SQL Server 上运行 Azure 中的数据文件，与使用 Azure 数据磁盘时的性能特征相当。

## <a name="next-steps"></a>后续步骤

有关安全最佳实践，请参阅 [Azure 虚拟机中 SQL Server 的安全注意事项](virtual-machines-windows-sql-security.md)。

查看 [Azure 虚拟机上的 SQL Server 概述](virtual-machines-windows-sql-server-iaas-overview.md)中的其他 SQL Server 虚拟机文章。 如果对 SQL Server 虚拟机有任何疑问，请参阅[常见问题解答](virtual-machines-windows-sql-server-iaas-faq.md)。
