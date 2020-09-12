---
title: 规划 Azure 文件同步部署 | Microsoft Docs
description: 使用 Azure 文件同步服务计划部署，该服务允许你在本地 Windows Server 或云 VM 上缓存多个 Azure 文件共享。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 876a96f579bff8d30e454e927054a951734f44ba
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89441093"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>规划 Azure 文件同步部署

:::row:::
    :::column:::
        [![介绍 Azure 文件同步的访谈和演示 - 单击即可播放！](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure 文件同步是一项服务，可用于在本地 Windows Server 或云 VM 上缓存多个 Azure 文件共享。 
        
        本文介绍 Azure 文件同步的相关概念和功能。 在对 Azure 文件同步有了一定了解后，可参照 [Azure 文件同步部署指南](storage-sync-files-deployment-guide.md)，试用此服务。        
    :::column-end:::
:::row-end:::

文件将存储在云中的 [Azure 文件共享](storage-files-introduction.md)中。 可以通过两种方式使用 Azure 文件共享：直接装载这些无服务器 Azure 文件共享 (SMB)，或使用 Azure 文件同步功能在本地缓存 Azure 文件共享。所选择的部署方式决定了规划部署时需要考虑的事项。 

- **直接装载 Azure 文件共享**：由于 Azure 文件存储提供 SMB 访问权限，可以使用 Windows、macOS 和 Linux 中提供的标准 SMB 客户端在本地或在云中装载 Azure 文件共享。 由于 Azure 文件共享是无服务器式的，因此在部署生产方案时不需要管理文件服务器或 NAS 设备。 这意味着无需应用软件修补程序或换出物理磁盘。 

- **使用 Azure 文件同步在本地缓存 Azure 文件共享**：借助 Azure 文件同步，可以在 Azure 文件存储中集中管理组织的文件共享，同时又能保留本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将本地（或云中的）Windows Server 转换为 Azure 文件共享的快速缓存。 

## <a name="management-concepts"></a>管理概念
Azure 文件同步部署有三个基本管理对象：

- **Azure 文件共享**：Azure 文件共享是一种无服务器云文件共享功能，它提供具有“Azure 文件同步”同步关系的云终结点。 Azure 文件共享中的文件可以直接通过 SMB 或 FileREST 协议进行访问，但在将 Azure 文件共享与 Azure 文件同步一起使用时，建议通过 Windows Server 缓存来访问文件。这是因为 Azure 文件存储目前缺少一种 Windows Server 所具有的那种高效的更改检测机制，如果直接对 Azure 文件共享进行更改，需要经过一段时间之后才能传播回服务器终结点。
- **服务器终结点**：与 Azure 文件共享进行同步的 Windows Server 上的路径。 它可以是某个卷上的特定文件夹，也可以是卷的根目录。 如果命名空间不重叠，多个服务器终结点可存在于同一个卷。
- **同步组**：用于定义**云终结点**或 Azure 文件共享与服务器终结点之间的同步关系的对象。 同步组中的终结点保持彼此同步。 例如，如果想使用 Azure 文件同步管理两组不同文件，需创建两个同步组并在每个同步组中添加不同的终结点。

### <a name="azure-file-share-management-concepts"></a>Azure 文件共享管理相关概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure 文件同步管理相关概念
**存储同步服务**中会部署同步组，这些组是顶层对象，用于注册要与 Azure 文件同步一起使用的服务器，并包含同步组关系。 存储同步服务资源是存储帐户资源的对等物，可按类似方式部署到 Azure 资源组。 存储同步服务可以创建同步组，这些组包含多个存储帐户和多个已注册的 Windows 服务器中的 Azure 文件共享。

需要先向存储同步服务注册 Windows Server，然后才能在存储同步服务中创建同步组。 该步骤会创建一个**已注册的服务器**对象，表示服务器或群集与存储同步服务之间的信任关系。 若要注册存储同步服务，需要先在服务器上安装 Azure 文件同步代理。 一个服务器或群集一次只能注册一个存储同步服务。

同步组包含一个云终结点或 Azure 文件共享，以及至少一个服务器终结点。 服务器终结点对象包含用于配置云分层功能的设置，该功能可实现 Azure 文件同步的缓存功能。为了与 Azure 文件共享进行同步，包含 Azure 文件共享的存储帐户必须与存储同步服务位于同一 Azure 区域。

### <a name="management-guidance"></a>管理指南
部署 Azure 文件同步时，建议执行以下操作：

- 部署的 Azure 文件共享与 Windows 文件共享的比例应为 1:1。 通过使用服务器终结点对象，能够在具有同步关系的服务器端非常灵活地设置同步拓扑。 为了简化管理，请使服务器终结点的路径与 Windows 文件共享的路径匹配。 

- 尽可能少地使用存储同步服务。 这在配置了包含多个服务器终结点的同步组的情况下可以简化管理，因为 Windows Server 一次只能注册一个存储同步服务。 

- 部署 Azure 文件共享时，应注意存储帐户的 IOPS 限制。 理想情况下，会将文件共享与存储帐户以 1:1 的比例进行映射，但由于存在来自于你的组织和 Azure 的各种限制，这并非总是可能的。 如果无法做到在一个存储帐户中只部署一个文件共享，可以考虑哪些共享会非常活跃，哪些共享不会那么活跃，以此确保不会将使用率最高的那些文件共享放置在同一存储帐户中。

## <a name="windows-file-server-considerations"></a>Windows 文件服务器注意事项
若要在 Windows Server 上启用同步功能，需要安装可下载的 Azure 文件同步代理。 Azure 文件同步代理提供两个主要组件：`FileSyncSvc.exe`，这是负责监视服务器终结点上所做更改和启动同步会话的后台 Windows 服务，以及 `StorageSync.sys`，这是可实现云分层功能和快速灾难恢复的文件系统筛选器。  

### <a name="operating-system-requirements"></a>操作系统要求
以下 Windows Server 版本支持 Azure 文件同步：

| 版本 | 支持的 SKU | 支持的部署选项 |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter、Standard 和 IoT | “完整”和“核心” |
| Windows Server 2016 | Datacenter、Standard 和 Storage Server | “完整”和“核心” |
| Windows Server 2012 R2 | Datacenter、Standard 和 Storage Server | “完整”和“核心” |

将来的 Windows Server 版本将在发布后添加。

> [!Important]  
> 我们建议使用 Windows 更新提供的最新更新，将用于 Azure 文件同步的所有服务器保持最新。 

### <a name="minimum-system-resources"></a>最低系统资源要求
Azure 文件同步需要使用服务器（物理或虚拟），服务器需配有至少一个 CPU 和至少 2 GiB 的内存。

> [!Important]  
> 如果服务器要在启用了动态内存的虚拟机中运行，应至少为虚拟机配置 2048 MiB 的内存。

对于大多数生产工作负荷，不建议使用按最低要求配置的“Azure 文件同步”同步服务器。 有关详细信息，请参阅[推荐使用的系统资源](#recommended-system-resources)。

### <a name="recommended-system-resources"></a>推荐使用的系统要求
与其他任何服务器功能或应用程序一样，Azure 文件同步的系统资源要求取决于部署的规模；服务器上的部署规模越大，需要的系统资源就越多。 就 Azure 文件同步而言，相应的部署规模取决于所有服务器终结点上的对象数和数据集上的代码改动情况。 一个服务器可以在多个同步组中设置服务器终结点，且下表中列出的对象的数量会影响服务器所附加到的完整命名空间的规模。 

例如，具有 1 千万个对象的服务器终结点 A + 具有 1 千万个对象的服务器终结点 B = 2 千万个对象。 对于这样一个部署，建议配置 8 个 CPU，16 GiB 的稳态内存，并为初始迁移配置 48 GiB 内存（如有可能）。
 
出于性能原因，命名空间数据存储在内存中。 因此，命名空间越大，需要的内存越多，这样才能保持良好的性能，更多的代码改动也需要更多的 CPU 来处理。 
 
下表中提供了命名空间的大小以及转换为常规用途的典型文件共享后的容量，其平均文件大小为 512 KiB。 如果你的文件大小比上述数据小，请考虑为同一容量增加额外的内存。 应根据命名空间的大小来配置内容容量。

| 命名空间大小 - 文件和目录（百万）  | 典型容量 (TiB)  | CPU 内核数  | 建议的内存 (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8（初始同步）/ 2（典型代码改动）      |
| 5        | 2.3     | 2        | 16（初始同步）/ 4（典型代码改动）    |
| 10       | 4.7     | 4        | 32（初始同步）/ 8（典型代码改动）   |
| 30       | 14.0    | 8        | 48（初始同步）/ 16（典型代码改动）   |
| 50       | 23.3    | 16       | 64（初始同步）/ 32（典型代码改动）  |
| 100*     | 46.6    | 32       | 128（初始同步）/ 32（典型代码改动）  |

\*目前不建议同步超过 1 亿个文件和目录。 这是基于我们测试的阈值的软限制。 有关详细信息，请参阅 [Azure 文件存储可伸缩性和性能目标](storage-files-scale-targets.md#azure-file-sync-scale-targets)。

> [!TIP]
> 命名空间的初始同步是一种密集型操作，建议在初始同步完成之前分配较多内存。 这不是必需的，但这样做有可能加快初始同步的速度。 
> 
> 通常，每日代码改动量占命名空间更改量的 0.5%。 如果代码改动量较大，应考虑添加更多 CPU。 

- 使用 NTFS 文件系统进行了格式化的本地附加卷。

### <a name="evaluation-cmdlet"></a>评估 cmdlet
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估 cmdlet 评估它是否与你的系统兼容。 此 cmdlet 用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的操作系统版本。 其检查涵盖了下面提到的大多数但并非全部功能；建议你仔细读完本部分的剩余内容，以确保你的部署顺利进行。 

可以通过安装 Az PowerShell 模块来安装评估 cmdlet，该模块可按照以下说明进行安装：[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

#### <a name="usage"></a>使用情况  
可以采用以下多种不同的方式调用评估工具：可以执行系统检查、数据集检查或者同时执行这两种检查。 若要同时执行系统和数据集检查，请使用以下命令： 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

若要仅测试数据集，请使用以下命令：
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
若要仅测试系统要求，请使用以下命令：
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
若要以 CSV 格式显示结果，请使用以下命令：
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>文件系统兼容性
仅支持在直接附加的 NTFS 卷上使用 Azure 文件同步。 Windows Server 上设置有直连存储 (DAS) 意味着由 Windows Server 操作系统提供文件系统。 可以通过以物理方式将磁盘附加到文件服务器、通过将虚拟磁盘附加到文件服务器虚拟机（例如由 Hyper-v 托管的虚拟机），甚至可以通过 ISCSI，来提供 DAS。

仅支持 NTFS 卷；不支持 ReFS、FAT、FAT32 和其他文件系统。

下表显示 NTFS 文件系统功能的互操作状态： 

| Feature | 支持状态 | 说明 |
|---------|----------------|-------|
| 访问控制列表 (ACL) | 完全支持 | Windows 样式随机访问控制列表由 Azure 文件同步功能负责留存，并由 Windows Server 在服务器终结点上强制实施。 当直接装载 Azure 文件共享时，也可以强制实施 ACL，但这需要额外配置。 请参阅[“标识”部分](#identity)以了解详细信息。 |
| 硬链接 | 已跳过 | |
| 符号链接 | 已跳过 | |
| 装入点 | 部分支持 | 装入点可能是服务器终结点的根，但如果包含在服务器终结点的命名空间内，则跳过此装入点。 |
| 交接点 | 已跳过 | 例如，分布式文件系统中的 DfrsrPrivate 和 DFSRoots 文件夹。 |
| 重分析点 | 已跳过 | |
| NTFS 压缩 | 完全支持 | |
| 稀疏文件 | 完全支持 | 稀疏文件会进行同步（不受阻），但作为完整文件同步到云。 如果在云中（或在其他服务器上）更改文件内容，则下载更改时，文件不再是稀疏文件。 |
| 备用数据流 (ADS) | 保留但不同步 | 例如，由文件分类基础结构创建的分类标记就不会同步。 每个服务器终结点的文件上的现有分类标记都保留不变。 |

<a id="files-skipped"></a>Azure 文件同步还会跳过某些临时文件和系统文件夹：

| 文件/文件夹 | 注意 |
|-|-|
| pagefile.sys | 特定于系统的文件 |
| Desktop.ini | 特定于系统的文件 |
| thumbs.db | 缩略图的临时文件 |
| ehthumbs.db | 媒体临时文件的缩略图 |
| ~$\*.\* | Office 临时文件 |
| \*.tmp | 临时文件 |
| \*.laccdb | Access DB 锁定文件|
| 635D02A9D91C401B97884B82B3BCDAEA.* | 内部同步文件|
| \\系统卷信息 | 特定于卷的文件夹 |
| $RECYCLE.BIN| Folder |
| \\SyncShareState | 用于同步的文件夹 |

### <a name="failover-clustering"></a>故障转移群集
Windows Server 故障转移群集受 Azure 文件同步支持，用于“一般用途文件服务器”部署选项。 不可在“适用于应用程序数据的横向扩展文件服务器”(SOFS) 或群集共享卷 (CSV) 上使用故障转移群集。

> [!Note]  
> 必须在故障转移群集中的每个节点上安装 Azure 文件同步代理，才能正常进行同步。

### <a name="data-deduplication"></a>重复数据删除
**Windows Server 2016 和 Windows Server 2019**   
Windows Server 2016 和 Windows Server 2019 上启用了云分层的卷支持重复数据删除。 在启用了云分层的卷上启用重复数据删除后，即可在本地缓存更多文件，而无需预配更多存储。 

在启用了云分层的卷上启用重复数据删除后，将根据云分层策略设置，按与普通文件类似的方式，对服务器终结点位置中经过“重复数据删除”优化的文件进行分层。 将经过“重复数据删除”优化的文件进行分层后，重复数据删除垃圾回收作业将自动运行，通过删除卷上不再被其他文件引用的、不必要的区块来回收磁盘空间。

请注意，卷空间节省效果仅适用于服务器；不会对 Azure 文件共享中的数据执行“重复数据删除”。

> [!Note]  
> 若要支持在 Windows Server 2019 上对启用了云分层的卷执行重复数据删除，需要安装 Windows 更新 [KB4520062](https://support.microsoft.com/help/4520062)，并且需要使用 9.0.0.0 版或更新版本的 Azure 文件同步代理。

**Windows Server 2012 R2**  
Azure 文件同步不支持在 Windows Server 2012 R2 上的同一卷上启用重复数据删除和云分层。 如果在卷上启用了重复数据删除，则必须禁用云分层。 

**说明**
- 如果在安装 Azure 文件同步代理之前安装了重复数据删除，则需要重新启动才能支持在同一卷上使用重复数据删除和云分层。
- 如果重复数据删除是在启用云分层之后在卷上启用的，则初始重复数据删除优化作业将优化卷上尚未分层的的文件，这会对云分层产生以下影响：
    - 可用空间策略将使用热度地图，根据卷上的可用空间，继续对文件进行分层。
    - 由于重复数据删除优化作业会访问文件，日期策略会跳过对本来可能有资格进行分层的文件进行分层。
- 对于正在进行的重复数据删除优化作业，如果文件尚未分层，启用了日期策略的云分层操作会因“重复数据删除”的 [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) 设置而延迟执行。 
    - 示例：如果 MinimumFileAgeDays 设置为 7 天，而云分层日期策略设置为 30 天，则日期策略将在 37 天后对文件分层。
    - 注意：一旦 Azure 文件同步对某个文件进行分层后，重复数据删除优化作业将立即跳过该文件。
- 如果某个服务器正在运行 Windows Server 2012 R2 且安装了 Azure 文件同步代理，当它升级到 Windows Server 2016 或 Windows Server 2019 后，需要执行以下步骤，才能支持在同一卷上启用重复数据删除和云分层：  
    - 卸载适用于 Windows Server 2012 R2 的 Azure 文件同步代理并重新启动服务器。
    - 下载适用于新服务器操作系统版本（Windows Server 2016 或 Windows Server 2019）的 Azure 文件同步代理。
    - 安装 Azure 文件同步代理并重新启动服务器。  
    
    注意：卸载并重新安装代理时，会保留服务器上的 Azure 文件同步配置设置。

### <a name="distributed-file-system-dfs"></a>分布式文件系统 (DFS)
Azure 文件同步支持与 DFS 命名空间 (DFS-N) 和 DFS 复制 (DFS-R) 进行互操作。

**DFS 命名空间 (DFS-N)** ：Azure 文件同步在 DFS-N 服务器上完全受支持。 可以在一个或多个 DFS-N 成员上安装 Azure 文件同步代理，以在服务器终结点与云终结点之间同步数据。 有关详细信息，请参阅 [DFS 命名空间概述](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 复制 (DFS-R)** ：因为 DFS-R 和 Azure 文件同步都是复制解决方案，所以在大多数情况下建议将 DFS-R 替换为 Azure 文件同步。不过在以下几个方案中，可能需要同时使用 DFS-R 和 Azure 文件同步：

- 从 DFS-R 部署迁移至 Azure 文件同步部署。 有关详细信息，请参阅[将 DFS 复制 (DFS-R) 部署迁移至 Azure 文件同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 需要文件数据副本的本地服务器并非都能直接连接到 Internet。
- 分支服务器将数据合并至单个中心服务器，你希望在该服务器中使用 Azure 文件同步。

让 Azure 文件同步和 DFS-R 并行工作：

1. 必须在包含 DFS-R 复制文件夹的卷上禁用 Azure 文件同步云分层。
2. 不应在 DFS-R 只读复制文件夹上配置服务器终结点。

有关详细信息，请参阅 [DFS 复制概述](https://technet.microsoft.com/library/jj127250)。

### <a name="sysprep"></a>Sysprep
不支持在安装了 Azure 文件同步代理的服务器上使用 sysprep，此操作会导致意外结果。 应该在部署服务器映像并完成 sysprep 迷你安装后再安装代理和注册服务器。

### <a name="windows-search"></a>Windows 搜索
如果在服务器终结点上启用了云分层功能，则已分层的文件将被跳过，并且不会由 Windows 搜索进行索引。 非分层文件会适当进行索引。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他分层存储管理 (HSM) 解决方案
其他 HSM 解决方案均无法使用 Azure 文件同步。

## <a name="identity"></a>标识
Azure 文件同步可处理基于 AD 的标准标识，且只需要设置同步，而无需任何其他特殊设置。当使用 Azure 文件同步时，一般情况下，大多数访问是通过 Azure 文件同步缓存服务器而不是通过 Azure 文件共享来完成的。 由于服务器终结点位于 Windows Server 上，并且 Windows Server 长期以来一直支持 AD 和 Windows 样式 ACL，因此，只要确保将注册了存储同步服务的 Windows 文件服务器加入域就行了，除此之外无需执行任何其他操作。 Azure 文件同步会将 ACL 存储在 Azure 文件共享中的文件上，并将其复制到所有服务器终结点。

即使直接对 Azure 文件共享所做的更改需要更长的时间才能同步到同步组中的服务器终结点，你可能还是想确保自己能够在云中直接对文件共享强制实施 AD 权限。 为此，需要将存储帐户以“域加入”的方式加入本地 AD 中，就像 Windows 文件服务器的域加入方式一样。 若要详细了解如何将存储帐户“域加入”到客户拥有的 Active Directory，请参阅 [Azure 文件存储 Active Directory 概述](storage-files-active-directory-overview.md)。

> [!Important]  
> 若要成功部署 Azure 文件同步，无需将存储帐户“域加入”到 Active Directory。这是一个可选步骤，可让 Azure 文件共享在用户直接装载 Azure 文件共享的情况下强制实施本地 ACL。

## <a name="networking"></a>网络
Azure 文件同步代理使用 Azure 文件同步 REST 协议和 FileREST 协议与存储同步服务和 Azure 文件共享功能进行通信，这两种协议始终通过端口 443 使用 HTTPS。 SMB 从不用于在 Windows Server 和 Azure 文件共享之间上载或下载数据。 由于大多数组织都允许通过端口 443 传递 HTTPS 流量，这是访问大多数网站时的一个要求，因此通常不需要特殊网络配置就能部署 Azure 文件同步。

根据组织的策略或独特的法规要求，与 Azure 的通信可能需要设置更严格的限制，因此 Azure 提供了多种网络配置机制。 根据你的要求，你可以：

- 通过 ExpressRoute 或 Azure VPN 进行隧道同步和传递文件上传/下载流量。 
- 利用 Azure 文件存储和 Azure 网络功能，如服务终结点和专用终结点。
- 配置 Azure 文件同步以支持环境中的代理。
- 限制 Azure 文件同步的网络活动。

若要详细了解 Azure 文件同步和网络，请参阅 [Azure 文件同步网络注意事项](storage-sync-files-networking-overview.md)。

## <a name="encryption"></a>加密
使用 Azure 文件同步时，需要考虑三个不同的加密层：对 Windows Server 的静态存储进行加密、在 Azure 文件同步代理与 Azure 之间的传输中进行加密，以及在 Azure 文件共享中对数据进行静态加密。 

### <a name="windows-server-encryption-at-rest"></a>Windows Server 静态加密 
对于 Azure 文件同步，有两个基本适用的关于 Windows Server 上加密数据的策略：一种是可对文件系统和写入其中的所有数据进行加密的文件系统下加密策略，另一种是文件格式内部的加密策略。 这些方法不是互斥的；如果需要，可以将它们一起使用，因为加密的目的不同。

为了在文件系统下提供加密，Windows Server 提供了 BitLocker 收件箱。 BitLocker 对 Azure 文件同步是完全透明的。使用 BitLocke 这样的加密机制的主要原因是，阻止想窃取磁盘的人造成本地数据中心数据的物理泄漏，并防止有人通过旁加载未授权的操作系统来对数据执行未经授权的读取/写入操作。 若要了解有关 BitLocker 的详细信息，请参阅 [BitLocker 概述](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)。

与 BitLocker 工作方式类似的第三方产品（即都在 NTFS 卷下运行），其工作方式也应对 Azure 文件同步完全透明。 

用于加密数据的另一个主要方法是在应用程序保存文件时加密文件的数据流。 某些应用程序可能会以本机方式执行此操作，但这并不是常见的方式。 用于加密文件数据流的方法的一个例子是 Azure 信息保护 (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RM。 使用 AIP/RMS 这类加密机制的主要原因是，阻止试图将文件共享中的数据复制到备用位置（如闪存驱动器）或试图通过电子邮件将其发送给未经授权的人员的人造成数据泄漏。 当文件的数据流加密为文件格式的一部分时，将继续在 Azure 文件共享中对此文件进行加密。 

Azure 文件同步不与位于文件系统上的而是与位于文件数据流下的 NTFS 加密文件系统 (NTFS EFS) 或第三方加密解决方案进行互操作。 

### <a name="encryption-in-transit"></a>传输中加密

> [!NOTE]
> Azure 文件同步服务将于 2020 年 8 月 1 日删除对 TLS1.0 和 1.1 的支持。 默认情况下，所有支持的 Azure 文件同步代理版本已使用 TLS 1.2。 如果在服务器上禁用了 TLS 1.2 或使用了代理，则可能会使用较早版本的 TLS。 如果使用了代理，建议检查代理配置。 2020/5/1 之后添加的 Azure 文件同步服务区域将仅支持 TLS 1.2，将于 2020 年 8 月 1 日删除现有区域中对 TLS1.0 和 1.1 的支持。  有关详细信息，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync)。

Azure 文件同步代理使用 Azure 文件同步 REST 协议和 FileREST 协议与存储同步服务和 Azure 文件共享功能进行通信，这两种协议始终通过端口 443 使用 HTTPS。 Azure 文件同步不通过 HTTP 发送未加密的请求。 

Azure 存储帐户包含一个用于要求在传输过程中加密的开关，该开关在默认情况下为启用状态。 即使在存储帐户级别上禁用了此开关，也就是说可能会存在与 Azure 文件共享之间的未加密连接，Azure 文件同步仍将仅使用加密通道来访问文件共享。

为存储帐户禁用传输中加密的主要原因是为了支持必须在更低版本的操作系统（例如，Windows Server 2008 R2 或更低版本的 Linux 发行版）上运行且直接与 Azure 文件共享通信的旧版应用程序。 如果旧版应用程序与文件共享的 Windows Server 缓存进行通信，切换此设置将不起作用。 

强烈建议确保启用了传输中数据的加密。

有关传输中加密的详细信息，请参阅[要求在 Azure 存储中进行安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="azure-file-share-encryption-at-rest"></a>Azure 文件共享静态加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>存储层
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>让标准文件共享能够承受最多 100 TiB 的容量
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>区域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure 文件同步区域可用性
Azure 文件同步在以下区域中可用：

| Azure 云 | 地理区域 | Azure 区域 | 区域代码 |
|-------------|-------------------|--------------|-------------|
| 公共 | 亚洲 | 东亚 | `eastasia` |
| 公共 | 亚洲 | 东南亚 | `southeastasia` |
| 公共 | 澳大利亚 | 澳大利亚东部 | `australiaeast` |
| 公共 | 澳大利亚 | 澳大利亚东南部 | `australiasoutheast` |
| 公共 | 巴西 | 巴西南部 | `brazilsouth` |
| 公共 | Canada | 加拿大中部 | `canadacentral` |
| 公共 | Canada | 加拿大东部 | `canadaeast` |
| 公共 | 欧洲 | 北欧 | `northeurope` |
| 公共 | 欧洲 | 西欧 | `westeurope` |
| 公共 | 法国 | 法国中部 | `francecentral` |
| 公共 | 法国 | 法国南部* | `francesouth` |
| 公共 | 印度 | 印度中部 | `centralindia` |
| 公共 | 印度 | 印度南部 | `southindia` |
| 公共 | 日本 | 日本东部 | `japaneast` |
| 公共 | 日本 | 日本西部 | `japanwest` |
| 公共 | 韩国 | 韩国中部 | `koreacentral` |
| 公共 | 韩国 | 韩国南部 | `koreasouth` |
| 公共 | 南非 | 南非北部 | `southafricanorth` |
| 公共 | 南非 | 南非西部* | `southafricawest` |
| 公共 | 阿拉伯联合酋长国 | 阿联酋中部* | `uaecentral` |
| 公共 | 阿拉伯联合酋长国 | 阿拉伯联合酋长国北部 | `uaenorth` |
| 公共 | 英国 | 英国南部 | `uksouth` |
| 公共 | 英国 | 英国西部 | `ukwest` |
| 公共 | 美国 | 美国中部 | `centralus` |
| 公共 | 美国 | 美国东部 | `eastus` |
| 公共 | 美国 | 美国东部 2 | `eastus2` |
| 公共 | 美国 | 美国中北部 | `northcentralus` |
| 公共 | 美国 | 美国中南部 | `southcentralus` |
| 公共 | 美国 | 美国中西部 | `westcentralus` |
| 公共 | 美国 | 美国西部 | `westus` |
| 公共 | 美国 | 美国西部 2 | `westus2` |
| US Gov | 美国 | US Gov 亚利桑那州 | `usgovarizona` |
| US Gov | 美国 | US Gov 德克萨斯州 | `usgovtexas` |
| US Gov | 美国 | US Gov 弗吉尼亚州 | `usgovvirginia` |

Azure 文件同步仅支持与存储同步服务所在区域中的 Azure 文件共享进行同步。

对于带星号的区域，需要与 Azure 支持部门联系，请求访问这些区域中的 Azure 存储。 [此文档](https://azure.microsoft.com/global-infrastructure/geographies/)中介绍了相关流程。

## <a name="redundancy"></a>冗余
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> 如果使用异地冗余存储和异地区域冗余存储，可以将存储手动故障转移到次要区域。 当使用 Azure 文件同步时，建议不要在未发生灾难的情况下执行此操作，因为这样会增加数据丢失的可能性。 如果发生了灾难且需要启动对存储的手动故障转移，需要向 Microsoft 开立支持事例，以使 Azure 文件同步能够继续使用辅助终结点进行同步。

## <a name="migration"></a>迁移
如果已有 Windows 文件服务器，可以直接就地安装 Azure 文件同步，而无需将数据转移到新服务器。 如果计划在采用 Azure 文件同步的过程中迁移到新的 Windows 文件服务器，可以使用以下几种方法来移动数据：

- 为旧文件共享和新文件共享创建服务器终结点，并让 Azure 文件同步在服务器终结点之间同步数据。 这种方法的优点在于，通过它可以非常轻松地超额订阅新文件服务器上的存储，因为 Azure 文件同步可以感知云分层。 准备就绪后，可以将最终用户转换为使用新服务器上的文件共享，并删除旧文件共享的服务器终结点。

- 仅在新文件服务器上创建服务器终结点，并使用 `robocopy` 将旧文件共享中的数据复制到其中。 根据新服务器上文件共享的拓扑（每个卷上有多少共享空间，每个卷上有多少可用空间等），可能需要临时预配额外的存储空间，因为预期在本地数据中心内从旧服务器 `robocopy` 到新服务器的速度要快于 Azure 文件同步将数据移动到 Azure 的速度。

还可以使用 Data Box 将数据迁移到 Azure 文件同步部署。 大多数情况下，当客户想使用 Data Box 引入数据时，他们会这样做，因为他们认为这样会提高部署的速度，或者因为这样做有助于实施带宽受限的方案。 尽管使用 Data Box 将数据引入到 Azure 文件同步部署中会降低带宽利用率，但大多数情况下，使用上述方法之一来实现联机数据上传可能会更快。 若要详细了解如何使用 Data Box 将数据引入 Azure 文件同步部署，请参阅[使用 Azure Data Box 将数据迁移到 Azure 文件同步](storage-sync-offline-data-transfer.md)。

客户在将数据迁移到新的 Azure 文件同步部署时常犯的一个错误是直接将数据复制到 Azure 文件共享，而不是复制到其 Windows 文件服务器上。 尽管 Azure 文件同步会标识 Azure 文件共享上的所有新文件，并将它们同步回 Windows 文件共享，但这通常比通过 Windows 文件服务器加载数据的速度要慢得多。 使用 Azure 复制工具（如 AzCopy）时，请务必使用最新版本。 检查 " [文件复制工具" 表](storage-files-migration-overview.md#file-copy-tools) 以获取 Azure copy 工具的概述，以确保可以复制文件的所有重要元数据，例如时间戳和 acl。

## <a name="antivirus"></a>防病毒
由于防病毒通过扫描文件中的已知恶意代码进行工作，因此防病毒产品可能导致重新调用分层文件，从而导致大量出口费用。 在 Azure 文件同步代理 4.0 及更高版本中，分层文件已设置安全 Windows 属性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS。 我们建议你咨询软件供应商，以了解如何配置其解决方案以跳过读取已设置此属性的文件（许多解决方案会自动执行此操作）。 

Microsoft 的内部防病毒解决方案 Windows Defender 和 System Center Endpoint Protection (SCEP) 都会自动跳过读取设有此属性的文件。 我们已对这两个解决方案进行了测试并发现了一个小问题：向现有的同步组添加服务器时，在新服务器上会重新调用（下载）小于 800 字节的文件。 这些文件将保留在新服务器上并且不会分层，因为它们不符合分层大小要求 (> 64kb)。

> [!Note]  
> 防病毒供应商可以使用 [Azure 文件同步防病毒兼容性测试套件](https://www.microsoft.com/download/details.aspx?id=58322)（可从 Microsoft 下载中心下载）来检查其产品与 Azure 文件同步的兼容性。

## <a name="backup"></a>备份 
如果启用了云分层，则不应使用直接备份服务器终结点的解决方案或服务器终结点所在的 VM。 云分层仅导致在服务器终结点上存储数据的一个子集，并将完整的数据集驻留在 Azure 文件共享中。 根据所使用的备份解决方案，将跳过或不备份分层文件 (因为它们 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 属性集) ，或者它们将被召回到磁盘，导致大量出口费用。 建议使用云备份解决方案直接备份 Azure 文件共享。 有关详细信息，请参阅 [关于 azure 文件共享备份](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json) 或与备份提供商联系，查看他们是否支持备份 Azure 文件共享。

如果希望使用本地备份解决方案，则应在禁用云分层的同步组中的服务器上执行备份。 执行还原时，使用卷级别或文件级还原选项。 使用文件级别还原选项还原的文件将同步到同步组中的所有终结点，现有文件将被替换为从备份还原的版本。  卷级别的还原不会替换 Azure 文件共享或其他服务器终结点中较新的文件版本。

> [!Note]  
> 祼机 (BMR) 还原可能会导致意外的结果且当前不受支持。

> [!Note]  
> 如果使用 Azure 文件同步代理版本 9，启用了云分层的卷上现在支持 VSS 快照（包括“以前的版本”选项卡）。 但是，必须通过 PowerShell 实现以前版本的兼容性。 [了解操作方法](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>后续步骤
* [考虑防火墙和代理设置](storage-sync-files-firewall-and-proxy.md)
* [规划 Azure 文件部署](storage-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
* [监视 Azure 文件同步](storage-sync-files-monitoring.md)
