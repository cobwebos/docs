---
title: 规划 Azure 文件同步部署 | Microsoft Docs
description: 了解规划 Azure 文件部署时应考虑的问题。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255114"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>规划 Azure 文件同步部署
[Azure 文件](storage-files-introduction.md)可通过两种主要方式进行部署：直接安装无服务器 Azure 文件共享或使用 Azure 文件同步在本地缓存 Azure 文件共享。您选择的部署选项会更改在规划部署时需要考虑的事项。 

- **Azure 文件共享的直接装载**：由于 Azure 文件提供 SMB 访问，因此可以使用 Windows、macOS 和 Linux 中可用的标准 SMB 客户端在本地或云中装载 Azure 文件共享。 由于 Azure 文件共享是无服务器的，因此为生产方案部署不需要管理文件服务器或 NAS 设备。 这意味着您不必应用软件修补程序或交换物理磁盘。 

- **使用 Azure 文件同步在本地缓存 Azure 文件共享**：Azure 文件同步使您能够在 Azure 文件中集中组织的文件共享，同时保持本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步将本地（或云）Windows 服务器转换为 Azure 文件共享的快速缓存。 

本文主要介绍部署 Azure 文件同步的部署注意事项。要计划由本地或云客户端直接装载 Azure 文件共享的部署，请参阅[规划 Azure 文件部署](storage-files-planning.md)。

## <a name="management-concepts"></a>管理概念
Azure 文件同步部署有三个基本管理对象：

- **Azure 文件共享**：Azure 文件共享是无服务器云文件共享，它提供 Azure 文件同步关系的*云终结点*。 Azure 文件共享中的文件可以直接使用 SMB 或 FileREST 协议访问，但我们鼓励您主要在 Azure 文件共享与 Azure 文件同步一起使用时通过 Windows Server 缓存访问文件。这是因为 Azure 文件今天缺少像 Windows Server 那样的有效更改检测机制，因此对 Azure 文件共享的更改需要时间才能传播回服务器终结点。
- **服务器终结点**：正在同步到 Azure 文件共享的 Windows 服务器上的路径。 这可以是卷或卷根上的特定文件夹。 如果多个服务器终结点的命名空间不重叠，则同一卷上可以存在多个服务器终结点。
- **同步组**：定义**云终结点**、 或 Azure 文件共享和服务器终结点之间的同步关系的对象。 同步组中的终结点保持彼此同步。 例如，如果您有两组不同的文件，您希望使用 Azure 文件同步进行管理，则可以创建两个同步组，并将不同的终结点添加到每个同步组。

### <a name="azure-file-share-management-concepts"></a>Azure 文件共享管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure 文件同步管理概念
同步组被部署到**存储同步服务**中，这些服务是注册服务器以与 Azure 文件同步一起使用并包含同步组关系的顶级对象。 存储同步服务资源是存储帐户资源的对等物，可按类似方式部署到 Azure 资源组。 存储同步服务可以创建包含跨多个存储帐户和多个已注册的 Windows 服务器的 Azure 文件共享的同步组。

在存储同步服务中创建同步组之前，必须先将 Windows 服务器与存储同步服务注册。 这将创建**一个注册的服务器**对象，该对象表示服务器或群集与存储同步服务之间的信任关系。 要注册存储同步服务，必须首先在服务器上安装 Azure 文件同步代理。 单个服务器或群集一次只能注册一个存储同步服务。

同步组包含一个云终结点或 Azure 文件共享，以及至少一个服务器终结点。 服务器终结点对象包含配置**云分层**功能的设置，它提供了 Azure 文件同步的缓存功能。为了与 Azure 文件共享同步，包含 Azure 文件共享的存储帐户必须与存储同步服务位于同一 Azure 区域中。

### <a name="management-guidance"></a>管理指南
部署 Azure 文件同步时，我们建议：

- 部署 Azure 文件与 Windows 文件共享共享 1：1。 服务器终结点对象在如何设置同步关系的服务器端的同步拓扑方面提供了很大的灵活性。 为了简化管理，使服务器终结点的路径与 Windows 文件共享的路径匹配。 

- 使用尽可能少的存储同步服务。 当您具有包含多个服务器终结点的同步组时，这将简化管理，因为 Windows 服务器一次只能注册到一个存储同步服务。 

- 在部署 Azure 文件共享时，注意存储帐户的 IOPS 限制。 理想情况下，您将文件共享 1：1 映射到存储帐户，但由于组织与 Azure 的各种限制和限制，这并不总是可能的。 如果不可能在一个存储帐户中只部署一个文件共享，请考虑哪些共享将高度活跃，哪些共享将不太活跃，以确保最热门的文件共享不会放在同一个存储帐户中。

## <a name="windows-file-server-considerations"></a>Windows 文件服务器注意事项
要在 Windows 服务器上启用同步功能，必须安装 Azure 文件同步可下载代理。 Azure 文件同步代理提供两个主要组件：、`FileSyncSvc.exe`负责监视服务器终结点上的更改和启动同步会话的背景 Windows 服务，以及`StorageSync.sys`支持云分层和快速灾难恢复的文件系统筛选器。  

### <a name="operating-system-requirements"></a>操作系统要求
以下版本的 Windows 服务器支持 Azure 文件同步：

| 版本 | 支持的 SKU | 支持的部署选项 |
|---------|----------------|------------------------------|
| Windows Server 2019 | 数据中心、标准和 IoT | 完整和核心 |
| Windows Server 2016 | 数据中心、标准和存储服务器 | 完整和核心 |
| Windows Server 2012 R2 | 数据中心、标准和存储服务器 | 完整和核心 |

将来的 Windows Server 版本将在发布后添加。

> [!Important]  
> 我们建议使用 Windows 更新提供的最新更新，将用于 Azure 文件同步的所有服务器保持最新。 

### <a name="minimum-system-resources"></a>最少的系统资源
Azure 文件同步需要一台服务器（物理服务器或虚拟服务器）至少具有一个 CPU 和至少 2 GiB 的内存。

> [!Important]  
> 如果服务器在启用动态内存的虚拟机中运行，则应配置 VM 时至少具有 2048 MiB 的内存。

对于大多数生产工作负荷，我们不建议仅配置具有最低要求的 Azure 文件同步同步服务器。 有关详细信息，请参阅[建议的系统资源](#recommended-system-resources)。

### <a name="recommended-system-resources"></a>推荐的系统资源
与任何服务器功能或应用程序一样，Azure 文件同步的系统资源要求由部署的规模决定;服务器上的较大部署需要更大的系统资源。 对于 Azure 文件同步，缩放由服务器终结点中的对象数和数据集上的改动决定。 单个服务器可以在多个同步组中具有服务器终结点，并且下表中列出的对象数用于服务器附加到的完整命名空间。 

例如，服务器终结点 A 具有 1000 万个对象 = 服务器终结点 B，其中 1000 万个对象 = 2000 万个对象。 对于该示例部署，我们建议使用 8 个 CPU，为稳定状态建议内存 16 GiB，（如果可能）48 GiB 内存用于初始迁移。
 
出于性能原因，命名空间数据存储在内存中。 因此，更大的命名空间需要更多的内存来保持良好的性能，而更多的改动需要更多的 CPU 来处理。 
 
在下表中，我们提供了命名空间的大小以及转换为典型通用文件共享的容量，其中平均文件大小为 512 KiB。 如果文件大小较小，请考虑为相同容量添加额外内存。 基于命名空间的大小来配置内存。

| 命名空间大小 - 文件&目录（百万）  | 典型容量 （TiB）  | CPU 内核数  | 推荐内存 （GiB） |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8（初始同步）/2（典型改动）      |
| 5        | 2.3     | 2        | 16 （初始同步）/ 4 （典型改动）    |
| 10       | 4.7     | 4        | 32 （初始同步）/ 8 （典型改动）   |
| 30       | 14.0    | 8        | 48 （初始同步）/ 16 （典型改动）   |
| 50       | 23.3    | 16       | 64 （初始同步）/ 32 （典型改动）  |
| 100*     | 46.6    | 32       | 128 （初始同步）/ 32 （典型改动）  |

\*目前不建议将超过 1 亿个文件&目录同步。 这是基于我们测试的阈值的软限制。 有关详细信息，请参阅[Azure 文件可伸缩性和性能目标](storage-files-scale-targets.md#azure-file-sync-scale-targets)。

> [!TIP]
> 命名空间的初始同步是一项密集的操作，我们建议在初始同步完成之前分配更多内存。 这不是必需的，但可能会加快初始同步。 
> 
> 典型的改动是每天更改命名空间的 0.5%。 对于更高的改动级别，请考虑添加更多 CPU。 

- 使用 NTFS 文件系统进行了格式化的本地附加卷。

### <a name="evaluation-cmdlet"></a>评估 cmdlet
在部署 Azure 文件同步之前，应使用 Azure 文件同步评估 cmdlet 评估它是否与系统兼容。 此 cmdlet 检查文件系统和数据集的潜在问题，例如不支持的字符或不支持的操作系统版本。 其检查涵盖以下提到的大多数（但不是所有功能）;我们建议您仔细阅读本节的其余部分，以确保部署顺利进行。 

可通过安装 Az PowerShell 模块来安装评估 cmdlet，该模块可通过此处的说明进行安装：[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

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
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
若要以 CSV 格式显示结果，请使用以下命令：
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>文件系统兼容性
Azure 文件同步仅在直接附加的 NTFS 卷上受支持。 Windows 服务器上的直接连接存储或 DAS 表示 Windows Server 操作系统拥有文件系统。 DAS 可以通过物理将磁盘附加到文件服务器、将虚拟磁盘附加到文件服务器 VM（例如由 Hyper-V 托管的 VM）甚至通过 ISCSI 提供。

仅支持 NTFS 卷;不支持 ReFS、FAT、FAT32 和其他文件系统。

下表显示了 NTFS 文件系统的功能的互操作状态： 

| Feature | 支持状态 | 说明 |
|---------|----------------|-------|
| 访问控制列表 (ACL) | 完全支持 | Windows 风格的可自由访问控制列表由 Azure 文件同步保留，并由 Windows 服务器在服务器终结点上强制执行。 在直接安装 Azure 文件共享时，也可以强制执行 ACL，但这需要额外的配置。 有关详细信息，请参阅[标识部分](#identity)。 |
| 硬链接 | 已跳过 | |
| 符号链接 | 已跳过 | |
| 装入点 | 部分支持 | 装入点可能是服务器终结点的根，但如果包含在服务器终结点的命名空间内，则跳过此装入点。 |
| 交接点 | 已跳过 | 例如，分布式文件系统中的 DfrsrPrivate 和 DFSRoots 文件夹。 |
| 重分析点 | 已跳过 | |
| NTFS 压缩 | 完全支持 | |
| 稀疏文件 | 完全支持 | 稀疏文件会进行同步（不受阻），但作为完整文件同步到云。 如果在云中（或在其他服务器上）更改文件内容，则下载更改时，文件不再是稀疏文件。 |
| 备用数据流 (ADS) | 保留但不同步 | 例如，由文件分类基础结构创建的分类标记就不会同步。 每个服务器终结点的文件上的现有分类标记都保留不变。 |

<a id="files-skipped"></a>Azure 文件同步还将跳过某些临时文件和系统文件夹：

| 文件/文件夹 | 注意 |
|-|-|
| pagefile.sys | 特定于系统的文件 |
| Desktop.ini | 特定于系统的文件 |
| thumbs.db | 缩略图的临时文件 |
| ehthumbs.db | 媒体缩略图的临时文件 |
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
**Windows 服务器 2016 和 Windows 服务器 2019**   
Windows Server 2016 和 Windows Server 2019 上启用了云分层的卷支持重复数据删除。 启用启用云分层的卷上的数据重复数据消除功能可让您在本地缓存更多文件，而无需预配更多存储。 

启用云分层的卷上启用数据重复数据消除后，服务器终结点位置内的 Dedup 优化文件将根据云分层策略设置与普通文件进行分层。 Dedup 优化文件分层后，数据重复数据消除垃圾回收作业将自动运行，通过删除卷上其他文件不再引用的不必要数据块来回收磁盘空间。

请注意，节省的卷仅适用于服务器;Azure 文件共享中的数据将不会被更新。

> [!Note]  
> 为了支持 Windows Server 2019 上启用了云分层的卷上的数据重复数据消除，必须安装 Windows 更新[KB4520062，](https://support.microsoft.com/help/4520062)并且需要 Azure 文件同步代理版本 9.0.0.0 或更新。

**视窗服务器 2012 R2**  
Azure 文件同步不支持 Windows Server 2012 R2 上同一卷上的数据重复数据消除和云分层。 如果在卷上启用了数据重复数据消除，则必须禁用云分层。 

**说明**
- 如果在安装 Azure 文件同步代理之前安装了数据重复数据，则需要重新启动以支持同一卷上的数据重复数据消除和云分层。
- 如果在启用云分层后在卷上启用了数据重复数据消除，则初始重复数据消除优化作业将优化卷上尚未分层的文件，并将对云分层产生以下影响：
    - 可用空间策略将继续使用热图根据卷上的可用空间对文件进行分层。
    - 日期策略将跳过由于访问文件的重复数据消除优化作业而可能以其他方式有资格分层的文件分层。
- 对于正在进行的重复数据消除优化作业，如果文件尚未分层，则使用日期策略的云分层将被数据重复数据消除[最小文件日](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps)设置延迟。 
    - 示例：如果"最小文件日"设置为 7 天，云分层日期策略为 30 天，则日期策略将在 37 天后对文件进行分层。
    - 注意：一旦文件被 Azure 文件同步分层，重复数据消除优化作业将跳过该文件。
- 如果运行安装了 Azure 文件同步代理的 Windows Server 2012 R2 服务器升级到 Windows Server 2016 或 Windows Server 2019，则必须执行以下步骤以支持同一卷上的数据重复数据消除和云分层：  
    - 卸载 Windows 服务器 2012 R2 的 Azure 文件同步代理并重新启动服务器。
    - 下载新服务器操作系统版本的 Azure 文件同步代理（Windows 服务器 2016 或 Windows 服务器 2019）。
    - 安装 Azure 文件同步代理并重新启动服务器。  
    
    注意：卸载和重新安装代理时，将保留服务器上的 Azure 文件同步配置设置。

### <a name="distributed-file-system-dfs"></a>分布式文件系统 (DFS)
Azure 文件同步支持与 DFS 命名空间 (DFS-N) 和 DFS 复制 (DFS-R) 进行互操作。

**DFS 命名空间 (DFS-N)**：Azure 文件同步在 DFS-N 服务器上完全受支持。 可以在一个或多个 DFS-N 成员上安装 Azure 文件同步代理，以在服务器终结点与云终结点之间同步数据。 有关详细信息，请参阅 [DFS 命名空间概述](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 复制 （DFS-R）**： 由于 DFS-R 和 Azure 文件同步都是复制解决方案，在大多数情况下，我们建议使用 Azure 文件同步替换 DFS-R。但是，有几个方案需要一起使用 DFS-R 和 Azure 文件同步：

- 从 DFS-R 部署迁移至 Azure 文件同步部署。 有关详细信息，请参阅[将 DFS 复制 (DFS-R) 部署迁移至 Azure 文件同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 并非所有需要文件数据副本的本地服务器都可以直接连接到互联网。
- 分支服务器将数据合并至单个中心服务器，你希望在该服务器中使用 Azure 文件同步。

对于 Azure 文件同步和 DFS-R 并排工作：

1. 必须在包含 DFS-R 复制文件夹的卷上禁用 Azure 文件同步云分层。
2. 不应在 DFS-R 只读复制文件夹上配置服务器终结点。

有关详细信息，请参阅 [DFS 复制概述](https://technet.microsoft.com/library/jj127250)。

### <a name="sysprep"></a>Sysprep
不支持在安装了 Azure 文件同步代理的服务器上使用 sysprep，并可能导致意外的结果。 应该在部署服务器映像并完成 sysprep 迷你安装后再安装代理和注册服务器。

### <a name="windows-search"></a>Windows 搜索
如果在服务器终结点上启用了云分层功能，则已分层的文件将被跳过，并且不会由 Windows 搜索进行索引。 非分层文件会适当进行索引。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他分层存储管理 (HSM) 解决方案
其他 HSM 解决方案均无法使用 Azure 文件同步。

## <a name="identity"></a>标识
Azure 文件同步适用于基于 AD 的标准标识，除了设置同步之外，没有任何特殊设置。使用 Azure 文件同步时，通常期望大多数访问都通过 Azure 文件同步缓存服务器，而不是通过 Azure 文件共享。 由于服务器终结点位于 Windows 服务器上，并且 Windows 服务器长期以来一直支持 AD 和 Windows 样式 ACL，因此除了确保与存储同步服务注册的 Windows 文件服务器已加入域之外，不需要什么。 Azure 文件同步将在 Azure 文件共享中的文件上存储 ACL，并将它们复制到所有服务器终结点。

即使直接对 Azure 文件共享所做的更改将需要更长的时间才能同步到同步组中的服务器终结点，您可能还希望确保可以直接在云中对文件共享强制执行 AD 权限。 为此，必须域将存储帐户加入本地 AD，就像 Windows 文件服务器加入域的方式一样。 要了解有关域将存储帐户加入客户拥有的活动目录的详细信息，请参阅[Azure 文件活动目录概述](storage-files-active-directory-overview.md)。

> [!Important]  
> 成功部署 Azure 文件同步不需要将存储帐户加入活动目录的域。这是一个严格可选的步骤，允许 Azure 文件共享在用户直接装载 Azure 文件共享时强制实施本地 ACL。

## <a name="networking"></a>网络
Azure 文件同步代理使用 Azure 文件同步 REST 协议和 FileREST 协议与存储同步服务和 Azure 文件共享通信，这两个协议始终在端口 443 上使用 HTTPS。 SMB 永远不会用于在 Windows 服务器和 Azure 文件共享之间上载或下载数据。 由于大多数组织允许 HTTPS 流量通过端口 443，作为访问大多数网站的要求，因此部署 Azure 文件同步通常不需要特殊的网络配置。

根据组织的策略或独特的法规要求，您可能需要与 Azure 进行更严格的通信，因此 Azure 文件同步为配置网络提供了多种机制。 根据您的要求，您可以：

- 通过 ExpressRoute 或 Azure VPN 进行隧道同步和文件上传/下载流量。 
- 使用 Azure 文件和 Azure 网络功能（如服务终结点和专用终结点）。
- 配置 Azure 文件同步以支持环境中的代理。
- 从 Azure 文件同步限制网络活动。

要了解有关配置 Azure 文件同步的网络功能的更多内容，请参阅：
- [Azure 文件同步代理和防火墙设置](storage-sync-files-firewall-and-proxy.md)
- [确保 Azure 文件同步在数据中心运作良好](storage-sync-files-server-registration.md)

## <a name="encryption"></a>加密
使用 Azure 文件同步时，需要考虑三个不同的加密层：Windows 服务器静态存储上的加密、Azure 文件同步代理和 Azure 之间传输的加密以及 Azure 文件共享中其余数据的加密。 

### <a name="windows-server-encryption-at-rest"></a>静态 Windows 服务器加密 
在 Windows 服务器上加密数据的策略通常适用于 Azure 文件同步：文件系统下方的加密，以便文件系统和写入该文件的所有数据都加密，以及文件格式本身中的加密。 这些方法不是互斥的;因此，这些方法并不相互排斥。如果需要，可以一起使用，因为加密的目的不同。

为了在文件系统下提供加密，Windows 服务器提供 BitLocker 收件箱。 BitLocker 对 Azure 文件同步是完全透明的。使用 BitLocker 等加密机制的主要原因是，防止有人窃取磁盘从本地数据中心物理exby数据，并防止侧加载未经授权的操作系统以执行未经授权的读取/写入数据。 要了解有关 BitLocker 的详细信息，请参阅[BitLocker 概述](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)。

与 BitLocker 类似的第三方产品，因为它们位于 NTFS 卷下方，因此同样应该与 Azure 文件同步完全透明地工作。 

加密数据的另一个主要方法是在应用程序保存文件时加密文件的数据流。 某些应用程序可能本机执行此操作，但通常并非如此。 加密文件数据流的方法是 Azure 信息保护 （AIP）/Azure 权限管理服务 （Azure RMS）/活动目录 RMS。 使用 AIP/RMS 等加密机制的主要原因是，用户将数据从文件共享中的数据从文件共享中复制到备用位置（如闪存驱动器）或通过电子邮件发送给未经授权的人员。" 当文件的数据流作为文件格式的一部分进行加密时，此文件将继续在 Azure 文件共享上加密。 

Azure 文件同步不与 NTFS 加密文件系统 （NTFS EFS） 或第三方加密解决方案互操作，这些解决方案在文件系统上方，但低于文件的数据流。 

### <a name="encryption-in-transit"></a>传输中加密
Azure 文件同步代理使用 Azure 文件同步 REST 协议和 FileREST 协议与存储同步服务和 Azure 文件共享通信，这两个协议始终在端口 443 上使用 HTTPS。 Azure 文件同步不会通过 HTTP 发送未加密的请求。 

Azure 存储帐户包含一个开关，用于在传输中需要加密，默认情况下启用该交换机。 即使禁用存储帐户级别的交换机，这意味着与 Azure 文件共享的未加密连接也是可能的，Azure 文件同步仍将仅使用加密通道访问文件共享。

禁用存储帐户在传输中的加密的主要原因是支持必须在较旧的操作系统（如 Windows Server 2008 R2 或较旧的 Linux 发行版）上运行的旧应用程序，直接与 Azure 文件共享对话。 如果旧应用程序与文件共享的 Windows Server 缓存进行切换，则切换此设置将不起作用。 

我们强烈建议确保启用传输中数据的加密。

有关传输中加密的详细信息，请参阅[要求在 Azure 存储中进行安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="azure-file-share-encryption-at-rest"></a>静态 Azure 文件共享加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>存储层
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>使标准文件共享跨越多达 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>区域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure 文件同步区域可用性
Azure 文件同步在以下区域可用：

| Azure 云 | 地理区域 | Azure 区域 | 区域代码 |
|-------------|-------------------|--------------|-------------|
| Public | 亚洲 | 东亚 | `eastasia` |
| Public | 亚洲 | 东南亚 | `southeastasia` |
| Public | 澳大利亚 | 澳大利亚东部 | `australiaeast` |
| Public | 澳大利亚 | 澳大利亚东南部 | `australiasoutheast` |
| Public | 巴西 | 巴西南部 | `brazilsouth` |
| Public | Canada | 加拿大中部 | `canadacentral` |
| Public | Canada | 加拿大东部 | `canadaeast` |
| Public | 欧洲 | 北欧 | `northeurope` |
| Public | 欧洲 | 西欧 | `westeurope` |
| Public | 法国 | 法国中部 | `francecentral` |
| Public | 法国 | 法国南部* | `francesouth` |
| Public | 印度 | 印度中部 | `centralindia` |
| Public | 印度 | 印度南部 | `southindia` |
| Public | 日本 | 日本东部 | `japaneast` |
| Public | 日本 | 日本西部 | `japanwest` |
| Public | 韩国 | 韩国中部 | `koreacentral` |
| Public | 韩国 | 韩国南部 | `koreasouth` |
| Public | 南非 | 南非北部 | `southafricanorth` |
| Public | 南非 | 南非西部* | `southafricawest` |
| Public | 阿拉伯联合酋长国 | 阿联酋中部* | `uaecentral` |
| Public | 阿拉伯联合酋长国 | 阿拉伯联合酋长国北部 | `uaenorth` |
| Public | 英国 | 英国南部 | `uksouth` |
| Public | 英国 | 英国西部 | `ukwest` |
| Public | 美国 | 美国中部 | `centralus` |
| Public | 美国 | 美国东部 | `eastus` |
| Public | 美国 | 美国东部 2 | `eastus2` |
| Public | 美国 | 美国中北部 | `northcentralus` |
| Public | 美国 | 美国中南部 | `southcentralus` |
| Public | 美国 | 美国中西部 | `westcentralus` |
| Public | 美国 | 美国西部 | `westus` |
| Public | 美国 | 美国西部 2 | `westus2` |
| US Gov | 美国 | US Gov 亚利桑那州 | `usgovarizona` |
| US Gov | 美国 | US Gov 德克萨斯州 | `usgovtexas` |
| US Gov | 美国 | US Gov 弗吉尼亚州 | `usgovvirginia` |

Azure 文件同步仅支持与存储同步服务所在区域中的 Azure 文件共享进行同步。

对于标有星号的区域，必须联系 Azure 支持以请求访问这些区域中的 Azure 存储。 [本文档](https://azure.microsoft.com/global-infrastructure/geographies/)概述了该过程。

## <a name="redundancy"></a>冗余
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> 地理冗余和地理区域冗余存储能够手动故障转移存储到辅助区域。 我们建议您在使用 Azure 文件同步时不要在灾难之外执行此操作，因为数据丢失的可能性增加。 如果发生灾难，希望启动存储的手动故障转移，则需要与 Microsoft 一起打开支持案例，以便 Azure 文件同步恢复与辅助终结点同步。

## <a name="migration"></a>迁移
如果您有现有的 Windows 文件服务器，则可以直接安装 Azure 文件同步，而无需将数据移动到新服务器。 如果计划迁移到新的 Windows 文件服务器作为采用 Azure 文件同步的一部分，则有几种方法可以将数据移过：

- 为旧文件共享和新文件共享创建服务器终结点，并让 Azure 文件同步服务器终结点之间的数据。 此方法的优点是，它使得在新文件服务器上超额订阅存储变得非常容易，因为 Azure 文件同步是云分层感知的。 准备就绪后，可以将最终用户剪切到新服务器上的文件共享，并删除旧文件共享的服务器终结点。

- 仅在新文件服务器上创建服务器终结点，并使用 从旧文件共享将数据复制到`robocopy`中。 根据新服务器上的文件共享的拓扑（每个卷上有多少共享、每个卷的可用程度等），您可能需要临时预配其他存储，`robocopy`因为从旧服务器到本地数据中心内的新服务器完成的速度将比 Azure 文件同步将数据移动到 Azure 的速度要快。

还可以使用数据框将数据迁移到 Azure 文件同步部署中。 大多数情况下，当客户希望使用数据框来引入数据时，他们这样做是因为他们认为这将加快部署速度，或者因为它有助于解决受限带宽方案。 虽然使用数据框将数据引入 Azure 文件同步部署会降低带宽利用率，但大多数方案通过上述方法之一进行联机数据上载可能会更快。 要了解有关如何使用数据框将数据引入 Azure 文件同步部署中，请参阅[将数据迁移到 Azure 文件同步与 Azure 数据框](storage-sync-offline-data-transfer.md)。

客户在将数据迁移到其新的 Azure 文件同步部署时犯的常见错误是将数据直接复制到 Azure 文件共享中，而不是在其 Windows 文件服务器上。 尽管 Azure 文件同步将标识 Azure 文件共享上的所有新文件，并将它们同步回 Windows 文件共享，但这通常比通过 Windows 文件服务器加载数据要慢得多。 许多 Azure 复制工具（如 AzCopy）具有不复制文件的所有重要元数据（如时间戳和 ACL）的附加缺点。

## <a name="antivirus"></a>防病毒
由于防病毒通过扫描文件中的已知恶意代码进行工作，因此防病毒产品可能导致重新调用分层文件。 在 Azure 文件同步代理 4.0 及更高版本中，分层文件已设置安全 Windows 属性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS。 我们建议你咨询软件供应商，以了解如何配置其解决方案以跳过读取已设置此属性的文件（许多解决方案会自动执行此操作）。 

Microsoft 的内部防病毒解决方案 Windows Defender 和 System Center Endpoint Protection (SCEP) 都会自动跳过读取设有此属性的文件。 我们已对这两个解决方案进行了测试并发现了一个小问题：向现有的同步组添加服务器时，在新服务器上会重新调用（下载）小于 800 字节的文件。 这些文件将保留在新服务器上并且不会分层，因为它们不符合分层大小要求 (> 64kb)。

> [!Note]  
> 防病毒供应商可以使用[Azure 文件同步防病毒兼容性测试套件](https://www.microsoft.com/download/details.aspx?id=58322)（可在 Microsoft 下载中心下载）检查其产品与 Azure 文件同步之间的兼容性。

## <a name="backup"></a>备份 
与防病毒解决方案一样，备份解决方案可能导致重新调用分层文件。 建议使用云备份解决方案来备份 Azure文件共享，而不是使用本地备份产品。

如果使用本地备份解决方案，则应在已禁用云分层的同步组中的服务器上执行备份。 执行还原时，使用卷级别或文件级还原选项。 使用文件级别还原选项还原的文件将同步到同步组中的所有终结点，现有文件将被替换为从备份还原的版本。  卷级别的还原不会替换 Azure 文件共享或其他服务器终结点中较新的文件版本。

> [!Note]  
> 祼机 (BMR) 还原可能会导致意外的结果且当前不受支持。

> [!Note]  
> 使用 Azure 文件同步代理的版本 9，现在启用了云分层的卷上支持 VSS 快照（包括早期版本选项卡）。 但是，您必须通过 PowerShell 启用以前的版本兼容性。 [了解操作方法](storage-files-deployment-guide.md)。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>后续步骤
* [考虑防火墙和代理设置](storage-sync-files-firewall-and-proxy.md)
* [规划 Azure 文件部署](storage-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
* [监视 Azure 文件同步](storage-sync-files-monitoring.md)