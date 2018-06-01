---
title: Azure 堆栈虚拟机中 SQL Server 的性能最佳实践
description: 提供有关优化 Microsoft Azure 堆栈虚拟机中的 SQL Server 性能最佳实践。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2018
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 00c67503f5b9e0027cbb62520e392f56420a75e6
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34701454"
---
# <a name="optimize-sql-server-performance"></a>优化 SQL Server 性能

本文提供了有关优化 Microsoft Azure 堆栈虚拟机中的 SQL Server 性能的指南。 在 Azure 堆栈虚拟机中运行 SQL Server，使用相同数据库性能优化选项适用于 SQL Server 在本地服务器环境中。 在 Azure 堆栈云中关系数据库的性能取决于许多因素。 因素包括虚拟机，系列大小和数据磁盘的配置。

创建 SQL Server 映像时[预配 Azure 堆栈门户中的虚拟机，请考虑](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision)。 下载 SQL IaaS 扩展插件从在 Azure 堆栈管理门户中的应用商店管理并下载你选择的 SQL 虚拟机的虚拟硬盘驱动器 (Vhd)。 其中包括 SQL2014SP2、 SQL2016SP1 和 SQL2017。

> [!NOTE]  
> 本文介绍如何设置使用全局 Azure 门户的 SQL Server 虚拟机，尽管本指南也适用于 Azure 堆栈具有以下差异： SSD 不是可用于操作系统磁盘，被管理的磁盘不可用，和没有存储配置中的细微差别。

获取*最佳*Azure 堆栈的虚拟机上的 SQL Server 的性能是本文的重点。 如果你的工作负荷要求较低，可能不需要每个建议的优化。 评估这些建议时应考虑性能需求和工作负荷模式。

> [!NOTE]  
> 有关 Azure 虚拟机中 SQL Server 的性能指南，请参阅[本文](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)。

## <a name="before-you-begin"></a>开始之前
以下清单可用于 SQL Server 的 Azure 堆栈的虚拟机上的最佳性能：


|区域|优化|
|-----|-----|
|虚拟机大小 |[DS3](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)或更高版本的 SQL Server Enterprise 版本。<br><br>[DS2](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)或更高版本的 SQL Server Standard edition 和 Web edition。|
|存储 |使用支持虚拟机系列[高级存储](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-acs-differences)。|
|磁盘 |使用最少两个数据磁盘 （一个用于日志文件），一个用于数据文件和 TempDB，然后选择基于你的容量需求的磁盘大小。 在 SQL Server 安装过程中的这些磁盘设置的默认数据文件位置。<br><br>避免使用操作系统或临时磁盘进行数据库存储或日志记录。<br>条带化多个 Azure 数据磁盘，以获取使用存储空间的更高的 IO 吞吐量。<br><br>使用规定的分配大小格式化。|
|I/O|对数据文件启用即时文件初始化。<br><br>限制相当小固定增量 (以 64 MB 的 256 MB) 的数据库上的自动增长。<br><br>禁用数据库自动收缩。<br><br>设置数据磁盘，不是操作系统磁盘上的默认备份和数据库文件的位置。<br><br>启用锁定页面。<br><br>应用 SQL Server service pack 和累积更新。|
|特定于功能的|直接备份到 blob 存储 （如果正在使用的 SQL Server 版本支持）。|
|||

有关详细信息*如何*和*为什么*若要使这些优化，应请检查详细信息和以下各节中提供的指南。

## <a name="virtual-machine-size-guidance"></a>虚拟机大小指南

对于性能敏感型应用程序，以下[虚拟机大小](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)建议：

- **SQL Server Enterprise edition:** DS3 或更高版本

- **SQL Server Standard edition 和 Web edition:** DS2 或更高版本

具有 Azure 堆栈是 DS 和 DS_v2 虚拟机家庭系列之间没有性能差异。

## <a name="storage-guidance"></a>存储指导原则

（以及 DSv2-系列） 的 DS 系列 Azure 堆栈中的虚拟机提供最高操作系统磁盘和数据磁盘吞吐量 (IOPS)。 DS 或 DSv2 系列中的虚拟机提供达 1000 个 IOPS，操作系统磁盘和多达每个数据磁盘，无论类型或选择的磁盘的大小的 2300 IOPS。

数据磁盘吞吐量取决于唯一基于虚拟机家庭系列。 你可以[，请参阅此文章](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)来标识每个虚拟机系列序列的数据磁盘吞吐量。

> [!NOTE]  
> 对于生产工作负荷，选择要在操作系统上磁盘和数据磁盘提供的最大 IOPS 的 DS 系列或 DSv2-系列虚拟机。

在 Azure 堆栈中创建存储帐户时, 的异地复制选项没有任何作用，因为此功能不可用 Azure 堆栈中。

## <a name="disks-guidance"></a>磁盘指导原则

Azure 堆栈的虚拟机上有三种主要的磁盘类型：

- **操作系统磁盘：** 当你创建 Azure 堆栈虚拟机时，平台将附加至少一个磁盘 (标记为**C**驱动器) 到你的操作系统磁盘的虚拟机。 此磁盘是一个 VHD，在存储空间中存储为一个页 blob。

- **临时磁盘：** Azure 堆栈的虚拟机包含另一个磁盘称为临时磁盘 (标记为**D**驱动器)。 这是可用于暂存空间的节点上的一个磁盘。

- **数据磁盘：** 你可以将更多的磁盘作为数据磁盘附加到虚拟机，而这些磁盘在存储中存储为页 blob。

以下部分说明了有关使用这些不同磁盘的建议。

### <a name="operating-system-disk"></a>操作系统磁盘

操作系统磁盘是可以作为操作系统的运行版本来启动和装载的 VHD，并且标记为 **C** 驱动器。

### <a name="temporary-disk"></a>临时磁盘

临时存储驱动器，标记为**D**驱动器，不会保留。 不存储任何数据，则不会丢失，你的用户数据库文件或用户事务日志文件，如在**D**驱动器。

我们建议将 TempDB 存储的数据磁盘上，为每个数据磁盘提供最高的每个数据磁盘最多 2300 IOPS。

### <a name="data-disks"></a>数据磁盘数

- **将数据磁盘用于数据和日志文件。** 如果你未使用磁盘条带化，使用从虚拟机支持高级存储，其中一个磁盘包含日志文件，另一个包含数据和 TempDB 文件的两个数据磁盘。 每个数据磁盘提供的 IOPS 和带宽 （MB/秒），具体取决于虚拟机系列中，数中所述[支持 Azure 堆栈中的虚拟机大小](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)。 如果你使用的磁盘条带化技术，例如存储空间，将所有数据和日志文件所在的驱动器 （包括 TempDB） 上。 此配置提供的最大 IOPS 适用于 SQL Server 以供使用，无论哪个文件需要在任何特定时间数。

> [!NOTE]  
> 当您设置 SQL Server 虚拟机在门户中时，你可以编辑您的存储配置。 根据您配置，Azure 堆栈配置一个或多个磁盘。 多个磁盘组合成单个存储池。 数据文件和日志文件一起位于此配置中。

- **磁盘条带化：** 为更大的吞吐量，你可以添加附加数据磁盘，并使用磁盘条带化。 若要确定你需要的数据磁盘数，分析 IOPS 和带宽和所需的日志文件的数据和 TempDB 文件的数。 请注意，IOPS 限制是每个基于虚拟机系列，并且不基于虚拟机大小的数据磁盘。 网络带宽限制，但是，基于虚拟机大小。 上会看到表[Azure 堆栈中的虚拟机大小](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)有关详细信息。 遵循以下指南：

    - Windows Server 2012 或更高版本，使用[存储空间](https://technet.microsoft.com/library/hh831739.aspx)以下指南：

        1.  针对联机事务处理 (OLTP) 工作负荷和 256 KB （262144 字节） 的数据仓库工作负荷以避免分区定位错误导致的性能影响，交错 （条带大小） 设置为 64 KB （65536 字节为单位）。 这必须使用 PowerShell 设置。

        2.  设置列计数 = 物理磁盘的数量。 使用 PowerShell 配置多于 8 个磁盘 (而不是服务器管理器 UI) 时。

            例如，以下 PowerShell 创建新的存储池具有交错大小设置为 64 KB 和为 2 的列数：

          ```PowerShell  
          $PoolCount = Get-PhysicalDisk -CanPool $True
          $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

          New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
          ```

- 根据负载预期确定与你的存储池相关联的磁盘数。 请记住，不同的虚拟机大小允许不同数量的附加数据磁盘。 有关详细信息，请参阅[支持 Azure 堆栈中的虚拟机大小](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)。
- 若要获取有关数据磁盘的最大 IOPS，请的推荐方法是以添加支持的数据磁盘最大数量你[虚拟机大小](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-sizes)并使用磁盘条带化。
- **NTFS 分配单元大小：** 时格式化数据磁盘时，建议你为数据和日志文件以及 TempDB 使用 64-k B 分配单元大小。
- **磁盘管理实践：** 时删除数据磁盘，停止 SQL Server 服务在更改过程。 它不提供任何性能改进，不要更改此外，在磁盘上的缓存设置。

> [!WARNING]  
> 无法在这些操作期间停止 SQL 服务会导致数据库损坏。

## <a name="io-guidance"></a>I/O 指导原则

- 请考虑启用即时文件初始化以减少初始文件分配所需的时间。 若要利用即时文件初始化，授予 SQL Server (MSSQLSERVER) 服务帐户与**SE_MANAGE_VOLUME_NAME**和将其添加到**执行卷维护任务**安全策略。 如果你使用用于 Azure 的 SQL Server 平台映像，默认服务帐户 (**NT Service\MSSQLSERVER**) 不会添加到**执行卷维护任务**安全策略。 换而言之，在 SQL Server Azure 平台映像中不启用即时文件初始化。 将 SQL Server 服务帐户添加到**执行卷维护任务**安全策略后，请重新启动 SQL Server 服务。 使用此功能可能有一些安全注意事项。 有关详细信息，请参阅[数据库文件初始化](https://msdn.microsoft.com/library/ms175935.aspx)。
- **自动增长**是偶然的意外增长。 自动增长不管理数据和记录每天的增长。 如果使用自动增长，则应预先增长文件使用**大小**切换。
- 请确保禁用**自动收缩**以避免可能对性能产生负面影响的不必要开销。
- 设置默认的备份和数据库文件位置。 在本文中使用的建议，并在服务器属性窗口中进行更改。 有关说明，请参阅 [View or Change the Default Locations for Data and Log Files (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx)（查看或更改数据和日志文件的默认位置 (SQL Server Management Studio)）。 下面的屏幕截图演示了如何进行这些更改：

    > ![查看或更改的默认位置](./media/sql-server-vm-considerations/image1.png)

- 建立锁定的页以减少 IO 和任何分页活动。 有关详细信息，请参阅 [Enable the Lock Pages in Memory Option (Windows)](https://msdn.microsoft.com/library/ms190730.aspx)（启用在内存中锁定页面的选项 (Windows)）。

- 请考虑在传入/传出 Azure 堆栈，包括备份时压缩所有数据文件。

## <a name="feature-specific-guidance"></a>功能特定指南

某些部署可以使用更高级的配置技术，获得更多的性能好处。 下表列出了一些可帮助你实现更好的性能的 SQL Server 功能：

- **备份到 Azure** **存储。** 执行备份时 Azure 堆栈虚拟机中运行的 SQL Server，你可以使用 SQL Server 备份到 URL。 此功能是从 SQL Server 2012 SP1 CU2 开始提供的，在备份到附加数据磁盘时建议使用。

    当你备份或还原使用 Azure 存储空间，请按照中提供的建议[SQL Server 备份到 URL 最佳实践和故障排除](https://msdn.microsoft.com/library/jj919149.aspx)和[还原从备份存储在 Microsoft Azure](https://docs.microsoft.com/en-us/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). 此外还可以使用 [Azure 虚拟机中 SQL Server 的自动备份](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup)自动执行这些备份。

-   **备份到 Azure 堆栈存储。** 你可以备份到 Azure 堆栈存储以类似的方式与备份到 Azure 存储空间。 在创建备份在 SQL Server Management Studio (SSMS) 时，你需要手动输入的配置信息。 SSMS 不能用于创建存储容器或共享访问签名。 SSMS 仅连接到 Azure 订阅，不 Azure 堆栈订阅。 相反，你需要在 Azure 堆栈门户或 PowerShell 创建存储帐户、 容器和共享访问签名。

    当你将信息放入 SQL Server 备份对话框：

    ![SQL Server 备份](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > 共享访问签名是在 Azure 堆栈门户中，没有前导的 SAS 令牌？ 在字符串中。 如果你使用从门户复制功能，你需要先删除前导？ 为要在 SQL Server 中工作的标记。

    当你有备份目标设置和配置 SQL Server 中，你可以然后备份到 Azure 堆栈 blob 存储。

## <a name="next-steps"></a>后续步骤

[使用服务或 Azure 堆栈为构建应用](azure-stack-considerations.md)