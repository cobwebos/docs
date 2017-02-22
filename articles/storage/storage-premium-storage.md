---
title: "高性能高级存储和 Azure VM 磁盘 | Microsoft 文档"
description: "讨论高性能高级存储以及非托管和托管 Azure VM 磁盘。 Azure DS 系列、DSv2 系列和 GS 系列 VM 支持高级存储。"
services: storage
documentationcenter: 
author: ramankumarlive
manager: aungoo-msft
editor: tysonn
ms.assetid: e2a20625-6224-4187-8401-abadc8f1de91
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: ramankum
translationtype: Human Translation
ms.sourcegitcommit: 3a353bc874c1827f8a0fc85352894ad96cff16b5
ms.openlocfilehash: c9e43df37784999036c6cf250f27a808f79ebe2f


---
# <a name="high-performance-premium-storage-and-unmanaged-and-managed-azure-vm-disks"></a>高性能高级存储以及非托管和托管 Azure VM 磁盘
Microsoft Azure 高级存储为运行 I/O 密集型工作负荷的虚拟机 (VM) 提供高性能、低延迟的磁盘支持。 在固态硬盘 (SSD) 上使用高级存储存储数据的 VM 磁盘。 可以将应用程序的 VM 磁盘迁移到 Azure 高级存储，以充分利用这些磁盘的速度和性能。

Azure VM 支持附加多个高级存储磁盘，使应用程序可以具有每个 VM 多达 64 TB 的存储空间。 借助高级存储，应用程序对于每个 VM 可以实现 80,000 每秒输入/输出操作数 (IOPS) 和高达 2000 MB/秒的磁盘吞吐量，并且读取操作的延迟非常低。

使用高级存储，Azure 提供的功能可真正将要求苛刻的企业应用程序（如 Dynamics AX、Dynamics CRM、Exchange Server、SharePoint 场和 SAP Business Suite）直接迁移到云。 可以运行各种需要高级存储的一致高性能和低延迟的性能密集型数据库工作负荷，如 SQL Server、Oracle、MongoDB、MySQL 和 Redis。

> [!NOTE]
> 建议将任何需要高 IOPS 的虚拟机磁盘迁移到高级存储，以便应用程序实现最佳性能。 如果磁盘不需要高 IOPS，可以通过在标准存储（将虚拟机磁盘数据存储在硬盘驱动器 (HDD) 上而不是 SSD 上）中对其进行维护来限制成本。
> 

可通过两种方式为 Azure VM 创建高级磁盘：

**非托管磁盘**：这是管理用于存储与 VM 磁盘对应的 VHD 文件的存储帐户的原始方法。 VHD 文件作为页 blob 存储在存储帐户中。 

**[Azure 托管磁盘](storage-managed-disks-overview.md)**：此功能为你管理用于 VM 磁盘的存储帐户。 你指定所需的类型（高级或标准）和磁盘大小，Azure 将为你创建和管理磁盘。 你不必煞费苦心地将磁盘放置在多个存储帐户中，以确保保持在存储帐户的可伸缩性限制内 - Azure 会为你处理这一切。

尽管这两种类型的磁盘都可用，但是我们建议使用托管磁盘以利用其许多功能。

若要开始使用 Azure 高级存储，请访问[开始免费试用](https://azure.microsoft.com/pricing/free-trial/)。 

有关将现有 VM 迁移到高级存储的信息，请参阅[将现有Azure Windows VM迁移到托管磁盘](../virtual-machines/virtual-machines-windows-convert-unmanaged-to-managed-disks.md)或[将现有 Azure Linux VM 迁移到托管磁盘](../virtual-machines/virtual-machines-linux-convert-unmanaged-to-managed-disks.md)。

> [!NOTE]
> 大多数区域当前支持高级存储。 你可以在 [Azure 服务（按区域）](https://azure.microsoft.com/regions/#services)中通过查看支持大小系列 VM（DS、DSV2、Fs 和 GS）的区域查找可用区域的列表。
> 

## <a name="premium-storage-features"></a>高级存储功能

让我们看一下高级存储的一些功能。

**高级存储磁盘**：Azure 高级存储支持可附加到特定大小系列 VM（包括 DS、DSv2、GS 和 Fs 系列）的 VM 磁盘。 可以选择三种磁盘大小 - P10 (128GiB)、P20 (512GiB) 和 P30 (1024GiB)）- 每种大小都有自身的性能规范。 根据应用程序的要求，可以将一个或多个此类磁盘附加到 VM。 在下一部分[高级存储的可伸缩性和性能目标](#premium-storage-scalability-and-performance-targets)中将详细介绍规范。

**高级页 Blob**：高级存储支持页 Blob（用于存储 VM 的永久性非托管磁盘）。 与标准存储不同，高级存储不支持块 blob、追加 blob、文件、表或队列。
放在高级存储帐户中的任何对象都是页 Blob，并且对应于其中一种受支持的预配大小。 因此，高级存储帐户不适合存储小型 Blob。

**高级存储帐户**：若要开始使用高级存储，请为非托管磁盘创建一个高级存储帐户。 如果你想要使用 [Azure 门户](https://portal.azure.com)，可以通过将“高级”性能层和“本地冗余存储(LRS)”指定为复制选项来创建高级存储帐户。 还可以通过指定“Premium_LRS”作为类型来创建高级存储帐户，为此，可以使用：[存储 REST API](/rest/api/storageservices/fileservices/Azure-Storage-Services-REST-API-Reference) 版本 2014-02-14 或更高版本；[服务管理 REST API](http://msdn.microsoft.com/library/azure/ee460799.aspx) 版本 2014-10-01 或更高版本（经典部署）；[Azure Storage Resource Provider REST API Reference](/rest/api/storagerp)（Azure 存储资源提供程序 REST API 参考）（Resource Manager 部署）；[Azure PowerShell](../powershell-install-configure.md) 版本 0.8.10 或更高版本。 在以下有关[高级存储的可缩放性和性能目标](#premium-storage-scalability-and-performance-targets.md)的部分中了解高级存储帐户限制。

**高级本地冗余存储**：高级存储帐户仅支持使用本地冗余存储 (LRS) 作为复制选项；这意味着它在单个区域中保留三个数据副本。 有关使用高级存储时的异地复制注意事项，请参阅本文中的[快照与复制 Blob](#snapshots-and-copy-blob) 部分。

Azure 使用存储帐户作为未托管磁盘的容器。 如果使用未托管磁盘创建 Azure DS、DSv2、GS 或 Fs VM 并选择高级存储帐户，操作系统和数据磁盘会存储在该存储帐户中。

## <a name="premium-storage-supported-vms"></a>高级存储支持的 VM
高级存储支持 DS 系列、DSv2 系列、GS 系列和 Fs 系列 VM。 你可以将标准和高级存储磁盘用于这些 VM。 但不能在不兼容高级存储的 VM 系列中使用高级存储磁盘。

有关可用 Azure VM 类型和 Windows VM 大小的信息，请参阅 [Windows VM 大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 有关 Linux VM 的 VM 类型和大小的信息，请参阅 [Linux VM 大小](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

以下是 DS、DSv2、GS 和 Fs 系列 VM 的一些功能：

**云服务**：可以将 DS 系列 VM 添加到仅包含 DS 系列 VM 的云服务。 请不要将 DS 系列虚拟机添加到包含非 DS 系列 VM 的现有云服务。 可以将现有 VHD 迁移到只运行 DS 系列 VM 的新云服务。 如果想要保留托管 DS 系列 VM 的新云服务的相同虚拟 IP 地址 (VIP)，请使用[保留 IP 地址](../virtual-network/virtual-networks-instance-level-public-ip.md)。 GS 系列 VM 可以添加到只运行 GS 系列 VM 的现有云服务。

**操作系统磁盘**：可以将可在高级存储中运行的 VM 配置为使用高级或标准操作系统 (OS) 磁盘。 建议使用基于高级存储的 OS 磁盘，以获得最佳体验。

**数据磁盘**：可以在同一个在高级存储中运行的 VM 中同时使用高级和标准磁盘。 使用高级存储时，可以预配 VM 并将多个持久性数据磁盘附加到 VM。 如有需要，可以跨磁盘条带化，以增加卷的容量与性能。

> [!NOTE]
> 如果使用[存储空间](http://technet.microsoft.com/library/hh831739.aspx)来条带化高级存储数据磁盘，应该以使用的每个磁盘一个列的方式来配置它。 否则，条带化卷的整体性能可能会低于预期，因为磁盘之间的通信分配不平均。 默认情况下，服务器管理器用户界面 (UI) 允许设置最多包含 8 个磁盘的列。 如果磁盘超过 8 个，则必须使用 PowerShell 来创建卷，并手动指定列数。 否则，即使有更多磁盘，服务器管理器 UI 仍会继续使用 8 个列。 例如，如果在一个条带集中有 32 个磁盘，则应该指定 32 列。 可以使用 [New-VirtualDisk PowerShell](http://technet.microsoft.com/library/hh848643.aspx) cmdlet 的 *NumberOfColumns* 参数来指定虚拟磁盘使用的列数。 有关详细信息，请参阅[存储空间概述](http://technet.microsoft.com/library/hh831739.aspx)和 [Storage Spaces Frequently Asked Questions](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)（存储空间常见问题）。
> 

**缓存**：支持高级存储的大小系列 VM 都有独特的缓存功能，以此获取超过基础高级存储磁盘性能的高级别吞吐量和延迟时间。 可以在高级存储磁盘上将磁盘缓存策略配置为 ReadOnly、ReadWrite 或 None。 所有高级数据磁盘的默认磁盘缓存策略都是 ReadOnly，而操作系统磁盘的磁盘缓存策略则是 ReadWrite。 请使用正确的配置设置，以达到应用程序的最佳性能。 例如，对于读取频繁或只读数据磁盘（如 SQL Server 数据文件），将磁盘缓存策略设置为“ReadOnly”。 例如，对于写入频繁或只写数据磁盘（如 SQL Server 日志文件），将磁盘缓存策略设置为“None”。 在[使用高级存储针对性能进行设计](storage-premium-storage-performance.md)中深入了解如何优化高级存储的设计。

**分析**：若要分析使用高级存储磁盘的 VM 性能，可以在 [Azure 门户](https://portal.azure.com)中启用 VM 诊断。 有关详细信息，可以参考[使用 Azure 诊断扩展监视 Microsoft Azure 虚拟机](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)。 若要查看磁盘性能，请使用基于操作系统的工具，例如适用于 Windows VM 的 [Windows 性能监视器](https://technet.microsoft.com/library/cc749249.aspx)和适用于 Linux VM 的 [IOSTAT](http://linux.die.net/man/1/iostat)。

**VM 规模限制和性能**：每个高级存储支持的 VM 大小都有 IOPS、带宽和每个 VM 可附加的磁盘数的规模限制和性能规范。 将高级存储磁盘用于 VM 时，请确保 VM 上有足够的 IOPS 和带宽可用于驱动磁盘流量。
例如，STANDARD_DS1 VM 为高级存储磁盘流量提供 32 MB/秒的专用带宽。 P10 高级存储磁盘可以提供 100 MB/秒的带宽。 如果将 P10 高级存储磁盘附加到此 VM，它最高只能达到 32 MB/秒，而不能像 P10 磁盘那样最高达到 100 MB/秒。

目前，DS 系列的最大 VM 是 Standard_DS15_v2，它可以在所有磁盘上提供的最高流速为 960 MB/秒。 GS 系列的最大 VM 是 Standard_GS5，它可以在所有磁盘上提供的最高流速为 2000 MB/秒。
请注意，这些限制只适用于磁盘流量，而不包括缓存命中和网络流量。 VM 网络流量可以使用单独的带宽，这不同于高级存储磁盘的专用带宽。

有关高级存储支持的 VM 的最大 IOPS 和吞吐量（带宽）的最新信息，请参阅 [Windows VM 大小](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)或 [Linux VM 大小](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

若要了解高级存储磁盘及其 IOPS 和吞吐量限制，请参阅本文的[高级存储的可伸缩性和性能目标](#premium-storage-scalability-and-performance-targets)部分中的表格。

## <a name="premium-storage-scalability-and-performance-targets"></a>高级存储的可伸缩性和性能目标
在本部分，我们将说明在使用高级存储时必须考虑的可缩放性和性能目标。

高级存储帐户有以下可伸缩性目标：

| 总帐户容量 | 本地冗余存储帐户的总带宽 |
| --- | --- | 
| 磁盘容量：35 TB <br>快照容量：10 TB | 入站 + 出站最高每秒 50 Gbps |

* 入站是指发送到存储帐户的所有数据（请求）。
* 出站是指从存储帐户接收的所有数据（响应）。

有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标](storage-scalability-targets.md)。

如果你对非托管磁盘使用高级存储帐户并且应用程序超过了单个存储帐户的可伸缩性目标，请考虑迁移到托管磁盘。 如果不想迁移到托管磁盘，请生成应用程序以使用多个存储帐户，并将数据分布到这些存储帐户中。 例如，如果要将 51 TB 的磁盘附加到多个 VM，请将它们分布在两个存储帐户中，因为 35 TB 是单个高级存储帐户的限制。 请确保单个高级存储帐户永远不会具有超过 35 TB 的预配磁盘。

### <a name="premium-storage-disk-limits"></a>高级存储磁盘限制
预配高级存储磁盘时，磁盘的大小将确定最大 IOPS 和吞吐量（带宽）。 有三种类型的高级存储磁盘：P10、P20 和 P30。 每种类型各有特定的 IOPS 和吞吐量限制，如下表所示：

|高级存储磁盘类型 | P10 | P20 | P30 |
| --- | --- | --- | --- |
| 磁盘大小 | 128 GiB | 512 GiB | 1024 GiB (1 TB) |
| 每个磁盘的 IOPS | 500 | 2300 | 5000 |
每个磁盘的吞吐量 | 100 MB/秒 | 150 MB/秒 | 200 MB/秒 |

> [!NOTE]
> 请确保 VM 上有足够的带宽可用来驱动磁盘流量，如本文前面的[高级存储支持的 VM](#premium-storage-supported-vms) 部分中所述。 否则，将会根据 VM 限制而不是上表中提到的磁盘限制，将磁盘吞吐量和 IOPS 约束为较小值。  
> 
> 

以下是在高级存储可缩放性和性能目标方面必须知道的一些重要事项：

* **预配的容量和性能**：当预配高级存储磁盘时，不同于标准存储的是，可以获得该磁盘的容量、IOPS 和吞吐量保证。 例如，如果要创建 P30 磁盘，Azure 将为该磁盘预配 1024 GB 存储容量、5000 IOPS 和 200 MB/秒的吞吐量。 应用程序可以使用全部或部分容量与性能。

* **磁盘大小**：Azure 会将磁盘大小映射（向上舍入）至表中指定的最接近高级存储磁盘选项。 例如，大小为 100 GiB 的磁盘会分类为 P10 选项，最多可执行 500 IOPS，吞吐量最高可达 100 MB/秒。 同样，大小为 400 GiB 的磁盘会分类为 P20 选项，最多可执行 2300 IOPS，吞吐量最高可达 150 MB/秒。
  
> [!NOTE]
> 可以轻松增加现有磁盘的大小。 例如，可能想要将 30 GB 大小的磁盘增加到 128GB 或甚至 1 TB。 或者，可能想要将 P20 磁盘转换为 P30 磁盘，因为需要更多容量或更多的 IOPS 和吞吐量。 
> 
 
* **IO 大小**：输入/输出 (I/O) 单位大小为 256 KB。 如果要传送的数据少于 256 KB，会视为单个 I/O 单位。 较大的 I/O 大小则会视为大小是 256 KB 的多个 I/O。 例如，1100 KB 的 I/O 会视为五个 I/O 单位。

* **吞吐量**：吞吐量限制包括磁盘写入，以及不是从缓存进行的磁盘读取。 例如，P10 磁盘有 100 MB/秒的每磁盘吞吐量。 P10 磁盘的有效吞吐量示例如下：

| 每个 P10 磁盘的吞吐量上限 | 从磁盘的非缓存读取 | 对磁盘的非缓存写入 |
| --- | --- | --- |
| 每秒&100; MB | 每秒&100; MB | 0 |
| 每秒&100; MB | 0 | 每秒&100; MB |
| 每秒&100; MB | 每秒&60; MB | 每秒&40; MB |

* **缓存命中数**：缓存命中数不受磁盘已分配 IOPS 或吞吐量的限制。 例如，在高级存储支持的 VM 上使用具有 ReadOnly 缓存设置的数据磁盘时，缓存提供的读取数不受磁盘的 IOPS 和吞吐量上限的约束。 因此，如果工作负荷以读取为主，可以从磁盘获得很高的吞吐量。 请注意，缓存根据 VM 大小在 VM 级别受到不同 IOPS 和吞吐量的限制。 DS 系列 VM 大约有 4000 IOPS，缓存与本地 SSD IO 是每个核心 33 MB/秒。 GS 系列 VM 的限制为 5000 IOPS，而缓存与本地 SSD IO 为每核心 50 MB/秒。 

## <a name="throttling"></a>限制
如果应用程序的 IOPS 或吞吐量超出了分配的高级存储磁盘限制，或者 VM 上所有磁盘的总磁盘流量超出了 VM 可用的磁盘带宽限制，则可能会有限制情况。 若要避免限制，建议根据预配的磁盘可伸缩性和性能目标，以及 VM 可用的磁盘带宽，来限制磁盘的挂起 I/O 请求数。  

当应用程序设计为避免限制情况时，可以达到最低延迟。 另一方面，如果磁盘的挂起 I/O 请求数过小，应用程序便无法利用磁盘可用的最大 IOPS 和吞吐量级别。

以下示例演示了如何计算限制级别。 所有计算都是基于 256 KB 的 I/O 单位大小：

### <a name="example-1"></a>示例 1：
在 P10 磁盘上，应用程序一秒内有 495 个 16 KB 大小的 I/O 单位。 这些被视为每秒 495 个 I/O 单位 (IOPS)。 如果在该秒内尝试 2 MB 的 I/O，I/O 单位的总数会等于 495 + 8。 这是因为当 I/O 单位大小是 256 KB 时，2 MB 的 I/O 会产生 2048 KB / 256 KB = 8 个 I/O 单位。 因为 495 + 8 的总和超出磁盘 500 的 IOPS 限制，因此会发生限制情况。

### <a name="example-2"></a>示例 2：
在 P10 磁盘上，应用程序有 400 个 256 KB 大小的 I/O 单位。 使用的总带宽为 (400 * 256) / 1024 = 100 MB/秒。 P10 磁盘的吞吐量限制是每秒 100 MB。 如果应用程序尝试在该秒内执行更多 I/O 便会发生限制情况，因为会超出分配的限制。

### <a name="example-3"></a>示例 3：
某个 DS4 VM 附加了两个 P30 磁盘。 每个 P30 磁盘的吞吐量可达到每秒 200 MB。 但是，DS4 VM 的总磁盘带宽容量为每秒 256 MB。 因此，无法在此 DS4 VM 上同时以最大吞吐量驱动附加的磁盘。 若要解决此问题，可以在一个磁盘上保持每秒 200 MB 的通信，在另一个磁盘上保持每秒 56 MB。 如果磁盘通信的总和超出每秒 256 MB，磁盘通信将受到限制。

> [!NOTE]
> 如果磁盘通信大多包含小型 I/O，应用程序在达到吞吐量限制前很可能会先达到 IOPS 限制。 另一方面，如果磁盘通信主要包含大型 I/O，应用程序可能会达到吞吐量限制，而非 IOPS 限制。 可以使用最佳的 I/O 大小和限制磁盘的挂起 I/O 请求数，来最大化应用程序的 IOPS 和吞吐量容量。
> 

若要了解如何使用高级存储针对高性能进行设计，请阅读[使用高级存储针对性能进行设计](storage-premium-storage-performance.md)一文。

## <a name="snapshots-and-copy-blob"></a>快照和复制 blob

对于存储服务而言，VHD 文件是页 blob。 可以创建页 blob 的快照，然后将其复制到其他位置，如不同的存储帐户。

### <a name="unmanaged-disks"></a>非托管磁盘

可以为非托管高级磁盘创建[增量快照](storage-incremental-snapshots.md)，这与对标准存储使用快照的方式相同。 由于高级存储仅支持使用本地冗余存储 (LRS) 作为复制选项，因此建议创建快照，并将那些快照复制到地域冗余的标准存储帐户。 有关详细信息，请参阅 [Azure 存储冗余选项](storage-redundancy.md)。

如果磁盘已附加到 VM，则磁盘上将不允许某些 API 操作。 例如，磁盘附加到 VM 后，无法在该 Blob 上执行 [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob)（复制 Blob）操作。 此时，必须先使用 [Snapshot Blob](/rest/api/storageservices/fileservices/Snapshot-Blob)（快照 Blob）REST API 方法创建该 Blob 的快照，然后对该快照执行 [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob)（复制 Blob）以复制附加的磁盘。 或者，可以分离磁盘，然后执行任何必要的操作。

以下限制适用于高级存储 Blob 快照：

| 高级存储限制 | 值 |
| --- | --- |
| 每个  每个 Blob 的快照数 | 100 |
| 快照的存储帐户容量（只包括快照中的数据，不包括基本 blob 中的数据） | 10 TB |
| 最小 连续快照间隔时间 | 10 分钟 |

若要维护快照的异地冗余副本，可以使用 AzCopy 或“复制 Blob”将高级存储帐户中的快照复制到异地冗余的标准存储帐户。 有关详细信息，请参阅[使用 AzCopy 命令行实用程序传输数据](storage-use-azcopy.md)和 [Copy Blob](/rest/api/storageservices/fileservices/Copy-Blob)（复制 Blob）。

有关对高级存储帐户中的页 Blob 执行 REST 操作的详细信息，请参阅 [Using Blob Service Operations with Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969)（对 Azure 高级存储使用 Blob 服务操作）。

### <a name="managed-disks"></a>托管磁盘

托管磁盘的快照是托管磁盘的只读副本，它作为标准托管磁盘进行存储。 托管磁盘当前不支持[增量快照](storage-incremental-snapshots.md)，但以后将支持。 若要了解如何创建托管磁盘的快照，请参阅[在 Windows 中使用托管快照创建作为 Azure 托管磁盘存储的 VHD 的副本](../virtual-machines/virtual-machines-windows-snapshot-copy-managed-disk.md)或[在 Linux 中使用托管快照创建作为 Azure 托管磁盘存储的 VHD 的副本](../virtual-machines/linux/virtual-machines-linux-snapshot-copy-managed-disk.md)

如果托管磁盘已附加到 VM，则磁盘上将不允许某些 API 操作。 例如，在磁盘附加到 VM 上时，你无法通过生成共享访问签名 (SAS) 来执行复制操作。 而是，请先创建磁盘快照，然后再对该快照执行复制操作。 或者，可以分离磁盘，然后生成共享访问签名 (SAS)以执行复制操作。


## <a name="using-linux-vms-with-premium-storage"></a>在高级存储中使用 Linux VM
请参考以下重要说明，以了解如何在高级存储上配置 Linux VM：

* 对于缓存设置为“ReadOnly”或“None”的所有高级存储磁盘，必须在装入文件系统时禁用“屏障”，以实现高级存储的可伸缩性目标。 对于这种情况不需要屏障，因为写入高级存储磁盘对于这些缓存设置是持久的。 在成功完成写入请求时，数据已写入到持久存储。 请根据文件系统，使用以下方法之一来禁用“屏障”：
  
* 如果使用的是 **reiserFS**，请使用装入选项“barrier=none”禁用屏障（要启用屏障，请使用“barrier=flush”）
* 如果使用的是 **ext3/ext4**，请使用装入选项“barrier=0”禁用屏障（要启用屏障，请使用“barrier=1”）
* 如果使用的是 **XFS**，请使用装入选项“nobarrier”禁用屏障（要启用屏障，请使用“barrier”）
* 对于缓存设置为“ReadWrite”的高级存储磁盘，应该启用屏障以实现写入持久性。
* 若要在重新启动 VM 后保留卷标，必须使用对磁盘的 UUID 引用来更新 /etc/fstab。 另请参阅[将托管磁盘添加到 Linux VM](../virtual-machines/virtual-machines-linux-add-disk.md)。

以下是我们使用高级存储验证过的 Linux 分发版。 建议将 VM 升级到其中至少一个版本（或更新版本），以改进高级存储的性能和稳定性。 此外，某些版本需要最新的适用于 Microsoft Azure 的 Linux Integration Services (LIS) v4.0。 请使用下面提供的链接进行下载和安装。 在我们完成其他验证后，将陆续在列表中添加更多映像。 请注意，我们的验证表明，性能根据映像而有所不同，并且还取决于工作负荷特征和映像上的设置。 不同的映像已针对不同种类的工作负荷进行优化。

| 分发 | 版本 | 支持的内核 | 详细信息 |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x、8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5、6.6、6.7、7.0 | &nbsp; | [需要使用 LIS4](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> 请参阅下方注释 |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [建议使用 LIS4](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> 请参阅下方注释 |
| RHEL | 6.8+、7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+、7.2+ | &nbsp; | UEK4 或 RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Openlogic CentOS 的 LIS 驱动程序

运行 OpenLogic CentOS VM 的客户应该运行以下命令来安装最新的驱动程序：

```
sudo rpm -e hypervkvpd  ## (may return error if not installed, that's OK)
sudo yum install microsoft-hyper-v
```

需要重新启动才能激活新的驱动程序。

## <a name="pricing-and-billing"></a>定价和计费

使用高级存储时，请注意以下计费方式：

* 高级存储磁盘/blob 大小
* 高级存储快照
* 出站数据传输

**高级存储磁盘/blob 大小**：高级存储磁盘/blob 的计费是根据磁盘/blob 的预配大小。 Azure 会将预配大小（向上舍入）映射到[使用高级存储时的可伸缩性和性能目标](#premium-storage-scalability-and-performance-targets)部分的表中指定的最接近的高级存储磁盘选项。 每个磁盘都会映射到其中一种受支持的预配大小，并据此计费。 任何已设置的磁盘都是按每月的高级存储优惠价格以每小时的方式计费。 例如，如果在设置完 P10 磁盘的 20 小时后删除它，则会以 20 小时计算 P10 解决方案的费用。 这与写入磁盘的实际数据量或使用的 IOPS/吞吐量无关。

**高级非托管磁盘快照**：高级非托管磁盘上的快照会因为使用的额外容量而产生费用。 有关快照的详细信息，请参阅 [Creating a Snapshot of a Blob](/rest/api/storageservices/fileservices/Snapshot-Blob)（创建 Blob 的快照）。

**高级托管磁盘快照**：托管磁盘的快照是磁盘的只读副本，它作为标准托管磁盘进行存储。 快照的费用与标准托管磁盘相同。 例如，如果创建 128 GB 高级托管磁盘的快照，则快照的费用将等于 128 GB 标准托管磁盘。  

**出站数据传输**：[出站数据传输](https://azure.microsoft.com/pricing/details/data-transfers/)（Azure 数据中心送出的数据）会产生带宽使用费。

有关高级存储、高级存储支持的 VM 和托管磁盘的定价的详细信息，请参阅：

* [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)
* [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-service-support"></a>Azure 备份服务支持 

可以使用 Azure 备份来备份具有非托管磁盘的虚拟机。 [更多详细信息](../backup/backup-azure-vms-first-look-arm.md)。

还可将 Azure 备份服务与托管磁盘配合使用，以创建具有基于时间备份的备份作业、轻松 VM 还原和备份保留策略。 可以在[对具有托管磁盘的 VM 使用 Azure 备份服务](../backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)中阅读详细内容。 

## <a name="next-steps"></a>后续步骤
有关 Azure 高级存储的详细信息，请参阅以下文章。

### <a name="design-and-implement-with-azure-premium-storage"></a>使用 Azure 高级存储进行设计和实现
* [使用高级存储针对性能进行设计](storage-premium-storage-performance.md)
* [Using Blob Service Operations with Azure Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969)（对 Azure 高级存储使用 Blob 服务操作）

### <a name="operational-guidance"></a>操作指南
* [迁移到 Azure 高级存储](storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>博客文章
* [Azure Premium Storage Generally Available](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)（正式推出 Azure 高级存储）
* [Announcing the GS-Series: Adding Premium Storage Support to the Largest VMs in the Public Cloud](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)（GS 系列公告：将高级存储支持添加到公有云中的最大 VM）


<!--HONumber=Feb17_HO2-->


