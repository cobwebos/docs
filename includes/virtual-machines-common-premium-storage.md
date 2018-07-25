---
title: include 文件
description: include 文件
services: storage
author: ramankumarlive
ms.service: storage
ms.topic: include
ms.date: 06/05/2018
ms.author: ramankum
ms.custom: include file
ms.openlocfilehash: 4c14bfbad58849acefdc8c3a5513f681aba84ab8
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2018
ms.locfileid: "37910048"
---
# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>VM 的高性能高级存储和托管磁盘
Azure 高级存储为运行输入/输出 (I/O) 密集型工作负荷的虚拟机 (VM) 提供高性能、低延迟的磁盘支持。 使用高级存储的 VM 磁盘在固态硬盘 (SSD) 上存储数据。 要利用高级存储磁盘的速度和性能优势，可将现有的 VM 磁盘迁移到高级存储。

在 Azure 中，可将多个高级存储磁盘附加到 VM。 使用多个磁盘可以让应用程序的存储上限为每个 VM 256 TB。 如果使用高级存储，每个 VM 上的应用程序可实现 80,000 次 I/O 操作/秒 (IOPS)，每个 VM 可实现高达 2,000 MB/秒的磁盘吞吐量。 执行读取操时延迟极低。

使用高级存储时，Azure 提供的功能可真正将要求苛刻的企业应用程序（例如 Dynamics AX、Dynamics CRM、Exchange Server、SAP Business Suite 和 SharePoint 场）直接转移到云中。 可以在要求保持一贯高性能和低延迟的应用程序（例如 SQL Server、Oracle、MongoDB、MySQL 和 Redis）中运行性能密集型数据库工作负荷。

> [!NOTE]
> 为了实现应用程序的最佳性能，我们建议将需要高 IOPS 的 VM 磁盘迁移到高级存储。 如果磁盘不需要高 IOPS，可以通过将其保留在标准 Azure 存储中来帮助限制成本。 在标准存储中，VM 磁盘数据存储在硬盘驱动器 (HDD) 而不是 SSD 上。
> 

Azure 支持使用两种方法为 VM 创建高级存储磁盘：

* **非托管磁盘**

    原始方法是使用非托管磁盘。 在非托管磁盘中，需要管理存储帐户，这些帐户用于存储对应于 VM 磁盘的虚拟硬盘 (VHD) 文件。 VHD 文件作为页 Blob 存储在 Azure 存储帐户中。 

* **托管磁盘**

    选择 [Azure 托管磁盘](../articles/virtual-machines/windows/managed-disks-overview.md)时，Azure 将管理 VM 磁盘使用的存储帐户。 需要指定所需的磁盘类型（高级或标准）和磁盘大小。 Azure 将创建和管理该磁盘。 为了确保符合存储帐户的伸缩性限制，无需煞费苦心，将磁盘放置在多个存储帐户中。 Azure 会处理这一切。

我们建议选择托管磁盘以利用其许多功能。

若要开始使用高级存储，请[创建免费的 Azure 帐户](https://azure.microsoft.com/pricing/free-trial/)。 

有关将现有 VM 迁移到高级存储的信息，请参阅[将 Windows VM 从非托管磁盘转换为托管磁盘](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md)或[将 Linux VM 从非托管磁盘转换为托管磁盘](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md)。

> [!NOTE]
> 大多数区域推出了高级存储。 有关可用区域的列表，请参阅[可用 Azure 产品(按区域)](https://azure.microsoft.com/regions/#services) 中的**磁盘存储**所在的行。
> 

## <a name="features"></a>功能

下面是高级存储的一些功能：

* **高级存储磁盘**

    高级存储支持可附加到特定大小系列 VM 的 VM 磁盘。 高级存储支持各种 Azure VM。 可以选择七种磁盘大小：P4 (32 GB)、P6 (64 GB)、P10 (128 GB)、P20 (512 GB)、P30 (1024 GB)、P40 (2048 GB)、P50 (4095 GB)。 不过，仅托管磁盘支持 P4 和 P6 磁盘大小。 每种磁盘大小都有自身的性能规范。 根据应用程序的要求，可将一个或多个磁盘附加到 VM。 [高级存储的可伸缩性和性能目标](#scalability-and-performance-targets)中更详细介绍了规范。

* **高级页 Blob**

    高级存储支持页 Blob。 使用页 Blob 可在高级存储中存储 VM 的持久性非托管磁盘。 与标准 Azure 存储不同，高级存储不支持块 Blob、追加 Blob、文件、表或队列。 高级页 Blob 支持从 P10 至 P50 和 P60 (8191GiB) 这六种大小。 不支持将 P60 高级页 Blob 作为 VM 磁盘进行连接。 

    放在高级存储帐户中的任何对象都是页 Blob。 页 Blob 对应于某种受支持的预配大小。 因此，高级存储帐户不适合用于存储微型 Blob。

* **高级存储帐户**

    若要开始使用高级存储，请为非托管磁盘创建一个高级存储帐户。 若要在 [Azure 门户](https://portal.azure.com)中创建高级存储帐户，请选择“高级”性能层。 选择“本地冗余存储(LRS)”复制选项。 也可以通过将性能层设置为 **Premium_LRS** 来创建高级存储帐户。 若要更改性能层，请使用以下方法之一：
     
    - [对 Azure 存储使用 PowerShell](../articles/storage/common/storage-powershell-guide-full.md#manage-the-storage-account)
    - [对 Azure 存储使用 Azure CLI](../articles/storage/common/storage-azure-cli.md#manage-storage-accounts)
    - [Azure 存储资源提供程序 REST API](https://docs.microsoft.com/rest/api/storagerp)（适用于 Azure 资源管理器部署），或某个 Azure 存储资源提供程序客户端库

    若要了解高级存储帐户的限制，请参阅[高级存储的可伸缩性和性能目标](#premium-storage-scalability-and-performance-targets)。

* **高级本地冗余存储**

    高级存储帐户仅支持使用本地冗余存储作为复制选项。 本地冗余存储在单个区域中保留三个数据副本。 对于区域性灾难恢复，必须使用 [Azure 备份](../articles/backup/backup-introduction-to-azure-backup.md)在不同区域中备份 VM 磁盘。 此外，必须使用异地冗余存储 (GRS) 帐户作为备份保管库。 

    Azure 使用存储帐户作为非托管磁盘的容器。 如果使用非托管磁盘创建支持高级存储的 Azure VM 并选择高级存储帐户，则操作系统和数据磁盘会存储在该存储帐户中。

## <a name="supported-vms"></a>支持的 VM

各种 Azure VM 均支持高级存储。 可将标准和高级存储磁盘用于这些 VM 类型。 不能在不兼容高级存储的 VM 系列中使用高级存储磁盘。


有关 Azure 中适用于 Windows 的 VM 类型和大小的信息，请参阅 [Windows VM 大小](../articles/virtual-machines/windows/sizes.md)。 有关 Azure 中适用于 Linux 的 VM 类型和大小的信息，请参阅 [Linux VM 大小](../articles/virtual-machines/linux/sizes.md)。

以下是启用高级存储的 VM 上支持的一些功能：

* 可用性集

    使用 DS 系列 VM 的示例，可以将 DS 系列 VM 添加到仅具有 DS 系列 VM 的云服务。 不要将 DS 系列虚拟机添加到类型不是 DS 系列 VM 的现有云服务。 可将现有 VHD 迁移到只运行 DS 系列 VM 的新云服务。 如果想要对托管 DS 系列 VM 的新云服务使用相同的虚拟 IP 地址，请使用[保留 IP 地址](../articles/virtual-network/virtual-networks-instance-level-public-ip.md)。

* **操作系统磁盘**

    可将高级存储 VM 设置为使用高级或标准操作系统磁盘。 为获得最佳体验，我们建议使用基于高级存储的操作系统磁盘。

* **数据磁盘**

    可在同一高级存储 VM 中使用高级和标准磁盘。 使用高级存储时，可以预配 VM 并将多个持久性数据磁盘附加到 VM。 如果需要，可以跨磁盘条带化，以增加卷的容量与性能。

    > [!NOTE]
    > 如果使用[存储空间](http://technet.microsoft.com/library/hh831739.aspx)条带化高级存储数据磁盘，请为使用的每个磁盘设置包含 1 列的存储空间。 否则，条带化卷的整体性能可能会低于预期，因为磁盘之间的通信分配不平均。 默认情况下，在服务器管理器中可以设置最多包含 8 个磁盘的列。 如果磁盘超过 8 个，请使用 PowerShell 来创建卷。 手动指定列数。 否则，即使有更多磁盘，服务器管理器 UI 仍会继续使用 8 个列。 例如，如果一个带区集中有 32 个磁盘，请指定 32 列。 若要指定虚拟磁盘使用的列数，请在 [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) PowerShell cmdlet 中使用 *NumberOfColumns* 参数。 有关详细信息，请参阅[存储空间概述](http://technet.microsoft.com/library/hh831739.aspx)和[存储空间常见问题解答](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx)。
    >
    > 

* **缓存**

    支持高级存储的虚拟机 (VM) 具有独特的缓存功能，可实现较高级别的吞吐量和更短的延迟。 它们的缓存功能可以在底层高级存储磁盘性能的基础上提升性能。 但是，并非所有 VM 都支持缓存，因此请查看 VM 说明了解你感兴趣的 VM 的大小及更多信息。  支持缓存的 VM 将在其说明中以“最大缓存和临时存储吞吐量”度量进行指示。  它们还会直接在 VM 标题下指定。
    
    借助缓存，可以在高级存储磁盘上将磁盘缓存策略设置为 ReadOnly、ReadWrite 或 None。 所有高级数据磁盘的默认磁盘缓存策略都是 ReadOnly，而操作系统磁盘的磁盘缓存策略则是 ReadWrite。 为了让应用程序达到最佳性能，请务必使用正确的缓存设置。 
    
    例如，对于读取频繁或只读数据磁盘（如 SQL Server 数据文件），可将磁盘缓存策略设置为 ReadOnly。 对于写入频繁或只写数据磁盘（如 SQL Server 日志文件），可将磁盘缓存策略设置为 None。 
    
    若要详细了解如何优化高级存储的设计，请参阅[使用高级存储实现高性能设计](../articles/virtual-machines/windows/premium-storage-performance.md)。

* **分析**

    若要使用高级存储中的磁盘分析 VM 性能，请在 [Azure 门户](https://portal.azure.com)中启用 VM 诊断。 有关详细信息，请参阅[使用 Azure 诊断扩展监视 Azure VM](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/)。 

    若要查看磁盘性能，请使用基于操作系统的工具，例如适用于 Windows VM 的 [Windows 性能监视器](https://technet.microsoft.com/library/cc749249.aspx)和适用于 Linux VM 的 [iostat](http://linux.die.net/man/1/iostat) 命令。

* **VM 规模限制和性能**

    每个高级存储支持的 VM 大小都有 IOPS、带宽和每个 VM 可附加的磁盘数的规模限制和性能规范。 将高级存储磁盘用于 VM 时，请确保 VM 上有足够的 IOPS 和带宽可用于驱动磁盘流量。

    例如，STANDARD_DS1 VM 为高级存储磁盘流量提供 32 MB/秒的专用带宽。 P10 高级存储磁盘可以提供 100 MB/秒的带宽。 如果将 P10 高级存储磁盘附加到此 VM，该 VM 的带宽最高只能达到 32 MB/秒， 而无法使用 P10 磁盘提供的最高 100 MB/秒带宽。

    目前，DS 系列的最大 VM 是 Standard_DS15_v2。 Standard_DS15_v2 可以在所有磁盘上提供最高 960 MB/秒的带宽。 GS 系列的最大 VM 是 Standard_GS5。 Standard_GS5 可以在所有磁盘上提供最高 2,000 MB/秒的带宽。

    这些限制只适用于磁盘流量， 而不包括缓存命中和网络流量。 VM 网络流量可以使用单独的带宽。 网络流量使用的带宽不同于高级存储磁盘使用的专用带宽。

    有关高级存储支持的 VM 的最大 IOPS 和吞吐量（带宽）的最新信息，请参阅 [Windows VM 大小](../articles/virtual-machines/windows/sizes.md)或 [Linux VM 大小](../articles/virtual-machines/linux/sizes.md)。

    有关高级存储磁盘及其 IOPS 和吞吐量限制的详细信息，请参阅下一部分中的表格。

## <a name="scalability-and-performance-targets"></a>可伸缩性和性能目标
本部分说明在使用高级存储时要考虑的可伸缩性和性能目标。

高级存储帐户有以下可伸缩性目标：

| 总帐户容量 | 本地冗余存储帐户的总带宽 |
| --- | --- | 
| 磁盘容量：35 TB <br>快照容量：10 TB | 为入站<sup>1</sup> 和出站<sup>2</sup> 流量提供最高 50 Gbps 的带宽 |

<sup>1</sup> 发送到存储帐户的所有数据（请求）

<sup>2</sup> 从存储帐户接收的所有数据（响应）

有关详细信息，请参阅 [Azure 存储可伸缩性和性能目标](../articles/storage/common/storage-scalability-targets.md)。

如果要对非托管磁盘使用高级存储帐户并且应用程序超过了单个存储帐户的可伸缩性目标，可以考虑迁移到托管磁盘。 如果不想要迁移到托管磁盘，请将应用程序构建为使用多个存储帐户。 然后，将数据分布到这些存储帐户中。 例如，如果要将 51-TB 的磁盘附加到多个 VM，请将这些磁盘分散在两个存储帐户中。 35 TB 是单个高级存储帐户的限制。 请确保单个高级存储帐户永远不会具有超过 35 TB 的预配磁盘。

### <a name="premium-storage-disk-limits"></a>高级存储磁盘限制
预配高级存储磁盘时，磁盘的大小将确定最大 IOPS 和吞吐量（带宽）。 Azure 提供了七种类型的高级存储磁盘：P4（仅适用于托管磁盘）、P6（仅适用于托管磁盘）、P10、P20、P30、P40 和 P50。 每种高级存储磁盘类型在 IOPS 和吞吐量方面存在具体的限制。 下表描述了磁盘类型的限制：

| 高级磁盘类型  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| 磁盘大小           | 32 GB| 64 GB| 128 GB| 256 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| 每个磁盘的 IOPS       | 120   | 240   | 500   | 1100   | 2300              | 5000              | 7500              | 7500              | 
| 每个磁盘的吞吐量 | 每秒 25 MB  | 每秒 50 MB  | 每秒 100 MB | 每秒 125 MB | 每秒 150 MB | 每秒 200 MB | 每秒 250 MB | 每秒 250 MB | 

> [!NOTE]
> 请确保 VM 上有足够的带宽可用来驱动磁盘流量，如[高级存储支持的 VM](#premium-storage-supported-vms) 中所述。 否则，磁盘吞吐量和 IOPS 将限制为较小的值。 最大吞吐量和 IOPS 基于 VM 限制，而不是上表中所述的磁盘限制。  
> 
> 

下面是在高级存储可伸缩性和性能目标方面需要知道的一些重要事项：

* **预配的容量和性能**

    预配高级存储磁盘时，可以获得该磁盘的容量、IOPS 和吞吐量保证，这与标准存储不同。 例如，如果创建 P50 磁盘，Azure 将为此磁盘预配 4,095-GB 存储容量、7,500 IOPS 和 250-MB/秒的吞吐量。 应用程序可以使用全部或部分容量与性能。

* **磁盘大小**

    Azure 会将磁盘大小映射（向上舍入）到上一部分的表格中指定的最接近高级存储磁盘选项。 例如，大小为 100 GB 的磁盘将分类为 P10 选项。 该选项可执行的 IOPS 最高为 500，吞吐量最高为 100-MB/秒。 同样，大小为 400 GB 的磁盘将分类为 P20 选项。 该选项可执行的 IOPS 最高为 2,300，吞吐量最高为 150-MB/秒。
    
    > [!NOTE]
    > 可以轻松增加现有磁盘的大小。 例如，可能想要将 30-GB 大小的磁盘增加到 128 GB 甚至 1 TB。 或者，可能想要将 P20 磁盘转换为 P30 磁盘，因为需要更高的容量或更高的 IOPS 和吞吐量。 
    > 
 
* **I/O 大小**

    一个 I/O 的大小为 256 KB。 如果要传输的数据小于 256 KB，该数据会被视为 1 个 I/O 单位。 更大的 I/O 大小被视为多个 256 KB 大小的 I/O。 例如，1,100 KB 的 I/O 被视为 5 个 I/O 单位。

* **吞吐量**

    吞吐量限制包括磁盘写入，以及不是从缓存进行的磁盘读取操作。 例如，P10 磁盘有 100-MB/秒的每磁盘吞吐量。 下表显示了 P10 磁盘的一些有效吞吐量示例：

    | 每个 P10 磁盘的吞吐量上限 | 从磁盘进行的非缓存读取 | 对磁盘的非缓存写入 |
    | --- | --- | --- |
    | 100 MB/秒 | 100 MB/秒 | 0 |
    | 100 MB/秒 | 0 | 100 MB/秒 |
    | 100 MB/秒 | 60 MB/秒 | 40 MB/秒 |

* **缓存命中数**

    缓存命中数不受磁盘已分配 IOPS 或吞吐量的限制。 例如，在高级存储支持的 VM 上使用具有 ReadOnly 缓存设置的数据磁盘时，缓存提供的读取数不受磁盘的 IOPS 和吞吐量上限的约束。 如果磁盘的工作负荷以读取为主，可以获得极高的吞吐量。 缓存根据 VM 大小在 VM 级别受到不同 IOPS 和吞吐量的限制。 DS 系列 VM 大约有 4,000 IOPS，缓存与本地 SSD I/O 的吞吐量为每个核心 33-MB/秒。 GS 系列 VM 限制为 5,000 IOPS，缓存与本地 SSD I/O 的吞吐量为每个核心 50-MB/秒。 

## <a name="throttling"></a>限制
如果应用程序的 IOPS 或吞吐量超过高级存储磁盘的分配限制，可能会发生限制。 此外，如果 VM 上所有磁盘的总磁盘流量超过 VM 可用的磁盘带宽限制，也可能会发生限制。 为了避免限制，我们建议限制磁盘的挂起 I/O 请求数。 请根据预配磁盘的可伸缩性和性能目标，以及 VM 可用的磁盘带宽使用限制。  

当应用程序设计为避免限制情况时，可以达到最低延迟。 但是，如果磁盘的挂起 I/O 请求数过小，应用程序将无法利用磁盘可用的最大 IOPS 和吞吐量级别。

以下示例演示如何计算限制级别。 所有计算基于 256 KB 的 I/O 单位大小。

### <a name="example-1"></a>示例 1
在 P10 磁盘上，应用程序在一秒内已处理 495 个 16-KB 大小的 I/O 单位。 这些 I/O 单位被统计为 495 IOPS。 如果在该秒内尝试 2-MB 的 I/O，I/O 单位的总数等于 495 + 8 IOPS。 这是因为当 I/O 单位大小为 256 KB 时，2 MB I/O = 2,048 KB / 256 KB = 8 个 I/O 单位。 由于 495 + 8 的总和超出磁盘的 500 IOPS 限制，因此会发生限制。

### <a name="example-2"></a>示例 2
在 P10 磁盘上，应用程序已处理 400 个 256 KB 大小的 I/O 单位。 使用的总带宽为 (400 &#215; 256) / 1,024 KB = 100 MB/秒。 P10 磁盘的吞吐量限制为 100 MB/秒。 如果应用程序尝试在该秒内执行更多 I/O 操作，则会发生限制，因为这超出了分配的限制。

### <a name="example-3"></a>示例 3
某个 DS4 VM 附加了两个 P30 磁盘。 每个 P30 磁盘的吞吐量可达到 200-MB/秒。 但是，DS4 VM 的总磁盘带宽容量为 256 MB/秒。 无法在此 DS4 VM 上同时以最大吞吐量驱动两个附加的磁盘。 若要解决此问题，可在一个磁盘上保持 200 MB/秒的流量，在另一个磁盘上保持 56 MB/秒的流量。 如果磁盘流量的总和超过 256 MB/秒，磁盘流量将受到限制。

> [!NOTE]
> 如果磁盘流量主要包括小型 I/O，应用程序在发生吞吐量限制之前可能会先达到 IOPS 限制。 但是，如果磁盘流量主要包括大型 I/O，应用程序可能会先达到吞吐量限制，而不是 IOPS 限制。 可以使用最佳的 I/O 大小来最大化应用程序的 IOPS 和吞吐量容量。 此外，可以限制磁盘的挂起 I/O 请求数。
> 

若要详细了解如何使用高级存储实现高性能设计，请阅读[使用高级存储实现高性能设计](../articles/virtual-machines/windows/premium-storage-performance.md)一文。

## <a name="snapshots-and-copy-blob"></a>快照和复制 Blob

对于存储服务而言，VHD 文件是页 blob。 可以创建页 Blob 的快照，然后将其复制到其他位置，例如其他存储帐户。

### <a name="unmanaged-disks"></a>非托管磁盘

像使用标准存储的快照一样，为非托管高级磁盘创建[增量快照](../articles/virtual-machines/linux/incremental-snapshots.md)。 高级存储仅支持使用本地冗余存储作为复制选项。 我们建议创建快照，然后将这些快照复制到异地冗余的标准存储帐户。 有关详细信息，请参阅 [Azure 存储冗余选项](../articles/storage/common/storage-redundancy.md)。

如果将磁盘附加到 VM，该磁盘上将不允许某些 API 操作。 例如，将磁盘附加到 VM 后，无法在该 Blob 上执行[复制 Blob](/rest/api/storageservices/Copy-Blob) 操作。 应该先使用[快照 Blob](/rest/api/storageservices/Snapshot-Blob) REST API 创建该 Blob 的快照。 然后，对该快照执行[复制 Blob](/rest/api/storageservices/Copy-Blob) 以复制附加的磁盘。 或者，可以分离磁盘，并执行任何所需的操作。

以下限制适用于高级存储 Blob 快照：

| 高级存储限制 | 值 |
| --- | --- |
| 每个 Blob 的快照数上限 | 100 |
| 快照的存储帐户容量<br>（仅包括快照中的数据。 不包括基 Blob 中的数据。） | 10 TB |
| 连续快照的最小间隔时间 | 10 分钟 |

要维护快照的异地冗余副本，可以使用 AzCopy 或“复制 Blob”将高级存储帐户中的快照复制到异地冗余的标准存储帐户。 有关详细信息，请参阅[使用 AzCopy 命令行实用工具传输数据](../articles/storage/common/storage-use-azcopy.md)和[复制 Blob](/rest/api/storageservices/Copy-Blob)。

有关对高级存储帐户中的页 Blob 执行 REST 操作的详细信息，请参阅[对 Azure 高级存储执行 Blob 服务操作](http://go.microsoft.com/fwlink/?LinkId=521969)。

### <a name="managed-disks"></a>托管磁盘

托管磁盘的快照是托管磁盘的只读副本。 该快照存储为标准托管磁盘。 目前，托管磁盘不支持[增量快照](../articles/virtual-machines/windows/incremental-snapshots.md)。 若要了解如何创建托管磁盘的快照，请参阅[在 Windows 中使用托管快照创建存储为 Azure 托管磁盘的 VHD 的副本](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)或[在 Linux 中使用托管快照创建存储为 Azure 托管磁盘的 VHD 的副本](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)。

如果将托管磁盘附加到 VM，该磁盘上将不允许某些 API 操作。 例如，磁盘附加到 VM 时，无法通过生成共享访问签名 (SAS) 来执行复制操作。 请先创建磁盘快照，然后对该快照执行复制操作。 或者，可以分离磁盘，并生成 SAS 来执行复制操作。


## <a name="premium-storage-for-linux-vms"></a>Linux VM 的高级存储
可以借助以下信息在高级存储中设置 Linux VM：

若要在高级存储中实现可伸缩性目标，对于缓存设置为 ReadOnly 或 None 的所有高级存储磁盘，必须在装入文件系统时禁用“屏障”。 在此方案中不需要屏障，因为写入高级存储磁盘对于这些缓存设置是持久性的。 成功完成写入请求时，数据已写入持久存储。 若要禁用“屏障”，请使用以下方法之一： 选择适用于文件系统的方法：
  
* 对于 reiserFS，请使用 `barrier=none` 装入选项来禁用屏障。 （若要启用屏障，请使用 `barrier=flush`。）
* 对于 ext3/ext4，请使用 `barrier=0` 装入选项来禁用屏障。 （若要启用屏障，请使用 `barrier=1`。）
* 对于 XFS，请使用 `nobarrier` 装入选项来禁用屏障。 （若要启用屏障，请使用 `barrier`。）
* 对于缓存设置为 ReadWrite 的高级存储磁盘，请启用屏障来实现写入持久性。
* 若要在重新启动 VM 后保留卷标，必须在 /etc/fstab 中更新对磁盘的全局唯一标识符 (UUID) 引用。 有关详细信息，请参阅[将托管磁盘添加到 Linux VM](../articles/virtual-machines/linux/add-disk.md)。

下面是我们使用 Azure 高级存储验证过的 Linux 发行版。 为了提高高级存储的性能和稳定性，我们建议将 VM 升级到其中的至少一个版本（或更高版本）。 某些版本需要最新的适用于 Azure 的 Linux Integration Services (LIS) v4.0。 若要下载并安装某个发行版，请单击下表中所列的链接。 完成验证后，我们将陆续在该列表中添加映像。 请注意，我们的验证表明，性能根据每个映像的不同而异。 性能取决于工作负荷特征和映像设置。 不同的映像已针对不同种类的工作负荷进行优化。

| 分发 | 版本 | 支持的内核 | 详细信息 |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-server-20150123-en-us-30GB |
| Debian | 7.x、8.x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| suse-sles-12-priority-v20150213 <br> suse-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5、6.6、6.7、7.0 | &nbsp; | [需要 LIS4](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> 请参阅下一部分中的注释 |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [建议使用 LIS4](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> 请参阅下一部分中的注释 |
| Red Hat Enterprise Linux (RHEL) | 6.8+、7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+、7.2+ | &nbsp; | UEK4 或 RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 或 RHCK w/[LIS 4.1+](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>OpenLogic CentOS 的 LIS 驱动程序

运行 OpenLogic CentOS VM 时，请运行以下命令来安装最新的驱动程序：

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

若要激活新驱动程序，请重新启动计算机。

## <a name="pricing-and-billing"></a>定价和计费

使用高级存储时，请注意以下计费方式：

* **高级存储磁盘和 Blob 大小**

    高级存储磁盘或 Blob 根据磁盘或 Blob 的预配大小计费。 Azure 会将预配大小映射（向上舍入）到最接近的高级存储磁盘选项。 有关详细信息，请参阅[高级存储的可伸缩性和性能目标](#premium-storage-scalability-and-performance-targets)中的表格。 每个磁盘将映射到一种受支持的预配大小并相应地计费。 任何预配的磁盘根据每月的高级存储优惠价格按小时计费。 例如，如果在设置完 P10 磁盘的 20 小时后删除它，则会以 20 小时计算 P10 解决方案的费用。 这与写入磁盘的实际数据量或使用的 IOPS 和吞吐量无关。

* **高级非托管磁盘快照**

    高级非托管磁盘上的快照根据快照使用的额外容量计费。 有关快照的详细信息，请参阅[创建 Blob 的快照](/rest/api/storageservices/Snapshot-Blob)。

* **高级托管磁盘快照**

    托管磁盘的快照是该磁盘的只读副本。 该存储存储为标准托管磁盘。 快照的费用与标准托管磁盘相同。 例如，如果创建 128-GB 高级托管磁盘的快照，则该快照的费用等于 128-GB 标准托管磁盘的费用。  

* **出站数据传输**

    [出站数据传输](https://azure.microsoft.com/pricing/details/data-transfers/)（传出 Azure 数据中心的数据）会产生带宽使用费。

有关高级存储、高级存储支持的 VM 和托管磁盘的定价详细信息，请参阅以下文章：

* [Azure 存储定价](https://azure.microsoft.com/pricing/details/storage/)
* [虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [托管磁盘定价](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Azure 备份支持 

对于区域性灾难恢复，必须使用 [Azure 备份](../articles/backup/backup-introduction-to-azure-backup.md)和用作备份保管库的 GRS 存储帐户来备份不同区域中的 VM 磁盘。

若要创建可执行基于时间的备份、轻松实现 VM 还原并采用备份保留策略的备份作业，请使用 Azure 备份。 可对非托管磁盘和托管磁盘使用备份。 有关详细信息，请参阅[对包含非托管磁盘的 VM 使用 Azure 备份](../articles/backup/backup-azure-vms-first-look-arm.md)和[对包含托管磁盘的 VM 使用 Azure 备份](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup)。 

## <a name="next-steps"></a>后续步骤
有关高级存储的详细信息，请参阅以下文章。
