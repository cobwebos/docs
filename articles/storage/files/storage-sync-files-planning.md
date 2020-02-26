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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77598249"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>规划 Azure 文件同步部署
可以通过两种主要方式部署[Azure 文件](storage-files-introduction.md)：直接装载无服务器 Azure 文件共享，或使用 Azure 文件同步在本地缓存 azure 文件共享。你选择哪种部署选项会更改你在规划部署时需要考虑的事项。 

- **直接装载 azure 文件共享**：由于 azure 文件提供 SMB 访问，你可以在本地或在云中使用 Windows、MacOS 和 Linux 中提供的标准 SMB 客户端装载 azure 文件共享。 由于 Azure 文件共享无服务器，因此在生产方案中部署不需要管理文件服务器或 NAS 设备。 这意味着无需应用软件修补程序或交换物理磁盘。 

- **使用 Azure 文件同步在本地缓存 Azure 文件共享**： Azure 文件同步使你能够将组织的文件共享集中在 Azure 文件中，同时保持本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步将本地（或云） Windows Server 转换为 Azure 文件共享的快速缓存。 

本文主要介绍部署 Azure 文件同步的部署注意事项。若要计划将 Azure 文件共享部署为由本地或云客户端直接装载，请参阅[规划 Azure 文件部署](storage-files-planning.md)。

## <a name="management-concepts"></a>管理概念
Azure 文件同步部署具有三个基本管理对象：

- **Azure 文件共享**： azure 文件共享是无服务器云文件共享，提供 Azure 文件同步同步关系的*云终结点*。 Azure 文件共享中的文件可以直接与 SMB 或 FileREST 协议一起访问，尽管我们建议你在将 Azure 文件共享与 Azure 文件同步一起使用时，通过 Windows Server 缓存访问文件。这是因为 Azure 文件目前缺少一种高效的更改检测机制（例如 Windows Server），因此对 Azure 文件共享所做的更改将需要一段时间才能传播回服务器终结点。
- **服务器终结点**：正在同步到 Azure 文件共享的 Windows Server 上的路径。 这可以是卷上的特定文件夹或卷的根目录。 如果命名空间不重叠，多个服务器终结点可存在于同一个卷上。
- **同步组**：定义**云终结点**、Azure 文件共享和服务器终结点之间的同步关系的对象。 同步组中的终结点保持彼此同步。 例如，如果您要使用 Azure 文件同步管理两个不同的文件集，则可以创建两个同步组并向每个同步组中添加不同的终结点。

### <a name="azure-file-share-management-concepts"></a>Azure 文件共享管理概念
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure 文件同步管理概念
同步组部署到**存储同步服务**中，后者是注册服务器以与 Azure 文件同步一起使用并包含同步组关系的顶级对象。 存储同步服务资源是存储帐户资源的对等物，可按类似方式部署到 Azure 资源组。 存储同步服务可以创建包含跨多个存储帐户和多个已注册 Windows 服务器的 Azure 文件共享的同步组。

必须先向存储同步服务注册 Windows Server，然后才能在存储同步服务中创建同步组。 这会创建一个**已注册的服务器**对象，该对象表示服务器或群集与存储同步服务之间的信任关系。 若要注册存储同步服务，必须先在服务器上安装 Azure 文件同步代理。 一次只能向一个存储同步服务注册单个服务器或群集。

同步组包含一个云终结点、Azure 文件共享和至少一个服务器终结点。 服务器终结点对象包含配置**云分层**功能的设置，该功能提供 Azure 文件同步的缓存功能。为了与 Azure 文件共享同步，包含 Azure 文件共享的存储帐户必须与存储同步服务位于同一 Azure 区域。

### <a name="management-guidance"></a>管理指南
部署 Azure 文件同步时，建议执行以下操作：

- 部署包含 Windows 文件共享的 Azure 文件共享1:1。 服务器终结点对象为您提供了有关如何在同步关系的服务器端设置同步拓扑的更好的灵活性。 若要简化管理，请使服务器终结点的路径与 Windows 文件共享的路径匹配。 

- 尽可能少地使用存储同步服务。 当你具有包含多个服务器终结点的同步组时，这将简化管理，因为一次只能向一个存储同步服务注册一台 Windows Server。 

- 部署 Azure 文件共享时，请注意存储帐户的 IOPS 限制。 理想情况下，会将文件共享1:1 映射到存储帐户，但这并非总是可能的，因为不同的限制和限制均来自于你的组织和 Azure。 如果不能在一个存储帐户中部署一个文件共享，则可以考虑哪些共享将会非常活跃，哪些共享将不活动，以确保不会将最热门话题的文件共享放在同一存储帐户中。

## <a name="windows-file-server-considerations"></a>Windows 文件服务器注意事项
若要在 Windows Server 上启用同步功能，你必须安装 Azure 文件同步可下载的代理。 Azure 文件同步代理提供了两个主要组件： `FileSyncSvc.exe`、负责监视服务器终结点上的更改和启动同步会话的后台 Windows 服务，以及 `StorageSync.sys`，这是一种可实现云分层和快速灾难恢复的文件系统筛选器。  

### <a name="operating-system-requirements"></a>操作系统要求
以下 Windows Server 版本支持 Azure 文件同步：

| 版本 | 支持的 SKU | 支持的部署选项 |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter、Standard 和 IoT | 完整和核心 |
| Windows Server 2016 | Datacenter、Standard 和 Storage Server | 完整和核心 |
| Windows Server 2012 R2 | Datacenter、Standard 和 Storage Server | 完整和核心 |

将来的 Windows Server 版本将在发布后添加。

> [!Important]  
> 我们建议使用 Windows 更新提供的最新更新，将用于 Azure 文件同步的所有服务器保持最新。 

### <a name="minimum-system-resources"></a>最小系统资源
Azure 文件同步要求服务器（物理或虚拟）至少具有一个 CPU，并且至少有2个 GiB 的内存。

> [!Important]  
> 如果服务器在启用了动态内存的虚拟机中运行，则应使用至少 2048 MiB 的内存来配置 VM。

对于大多数生产工作负荷，我们不建议仅使用最低要求配置 Azure 文件同步同步服务器。 有关详细信息，请参阅[建议的系统资源](#recommended-system-resources)。

### <a name="recommended-system-resources"></a>推荐的系统资源
与任何服务器功能或应用程序一样，Azure 文件同步的系统资源要求取决于部署的规模;服务器上的较大部署需要更多的系统资源。 对于 Azure 文件同步，scale 由跨服务器终结点的对象数和数据集上的改动决定。 单个服务器可以在多个同步组中包含服务器终结点，并且可以在下表中列出的对象数与服务器所连接的完整命名空间的数目相同。 

例如，具有10000000对象的服务器终结点 + 带有10000000对象的服务器终结点 B = 20000000 对象。 对于该示例部署，我们建议有8个 Cpu，16 GiB 的内存用于稳定状态，（如有可能） 48 GiB 内存用于初始迁移。
 
出于性能原因，命名空间数据存储在内存中。 因此，较大的命名空间需要更多内存来保持良好的性能，并且更多的改动需要更多的 CPU 来处理。 
 
在下表中，我们提供了命名空间的大小以及转换为典型常规用途文件共享的容量，其中平均文件大小为 512 KiB。 如果文件大小较小，请考虑为同一容量增加额外的内存。 将内存配置基于命名空间的大小。

| 命名空间大小-文件 & 目录（百万）  | 典型容量（TiB）  | CPU 内核数  | 推荐的内存（GiB） |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8（初始同步）/2 （典型变动）      |
| 5        | 2.3     | 2        | 16（初始同步）/4 （典型变动）    |
| 10       | 4.7     | 4        | 32（初始同步）/8 （典型变动）   |
| 30       | 14.0    | 8        | 48（初始同步）/16 （典型变动）   |
| 50       | 23.3    | 16       | 64（初始同步）/32 （典型变动）  |
| 100*     | 46.6    | 32       | 128（初始同步）/32 （典型变动）  |

此时，不建议 \*同步超过100000000个文件 & 目录。 这是基于我们测试的阈值的软限制。 有关详细信息，请参阅[Azure 文件可伸缩性和性能目标](storage-files-scale-targets.md#azure-file-sync-scale-targets)。

> [!TIP]
> 命名空间的初始同步是一种密集型操作，我们建议在初始同步完成之前分配更多内存。 这不是必需的，但可能会加速初始同步。 
> 
> 通常，每日更改的命名空间为0.5%。 对于较高级别的改动，请考虑添加更多 CPU。 

- 使用 NTFS 文件系统进行了格式化的本地附加卷。

### <a name="evaluation-cmdlet"></a>评估 cmdlet
在部署 Azure 文件同步之前，你应该使用 Azure 文件同步评估 cmdlet 评估它是否与你的系统兼容。 此 cmdlet 将检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的操作系统版本。 它的检查涵盖了下面提到的大多数但不是所有的功能;建议你仔细阅读本部分的其余部分，以确保部署顺利完成。 

可以通过安装 Az PowerShell 模块来安装评估 cmdlet，该模块可按照此处的说明进行安装：[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps)。

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
仅支持直接连接的 NTFS 卷上的 Azure 文件同步。 Windows Server 上的直连存储（DAS）意味着 Windows Server 操作系统拥有文件系统。 可以通过物理方式将 DAS 附加到文件服务器，将虚拟磁盘附加到文件服务器 VM （例如 Hyper-v 托管的 VM），甚至通过 ISCSI 提供。

仅支持 NTFS 卷;不支持 ReFS、FAT、FAT32 和其他文件系统。

下表显示 NTFS 文件系统功能的互操作状态： 

| Feature | 支持状态 | 说明 |
|---------|----------------|-------|
| 访问控制列表 (ACL) | 完全支持 | Windows 样式随机访问控制列表由 Azure 文件同步保留，并由 Windows Server 在服务器终结点上强制实施。 当直接装载 Azure 文件共享时，还可以强制实施 Acl，但这需要其他配置。 有关详细信息，请参阅[标识部分](#identity)。 |
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
**Windows server 2016 和 Windows server 2019**   
Windows Server 2016 和 Windows Server 2019 上启用了云分层的卷支持重复数据删除。 启用启用了云分层的卷上的重复数据删除可让你在本地缓存更多文件，而无需预配更多存储。 

在启用了云分层的卷上启用重复数据删除时，将根据云分层策略设置，将服务器终结点位置中的重复数据删除优化后的文件与普通文件类似。 将重复数据删除优化文件分层后，重复数据删除垃圾回收作业将自动运行，以通过删除卷上的其他文件不再引用的不必要的区块来回收磁盘空间。

请注意，卷节省仅适用于服务器;Azure 文件共享中的数据将不会被重复数据。

> [!Note]  
> 若要支持在 Windows Server 2019 上启用云分层的卷上的重复数据删除，则必须安装 Windows update [KB4520062](https://support.microsoft.com/help/4520062) ，并要求 Azure 文件同步代理版本9.0.0.0 或更新版本。

**Windows Server 2012 R2**  
Azure 文件同步不支持在 Windows Server 2012 R2 上的同一卷上进行重复数据删除和云分层。 如果在卷上启用了重复数据删除，则必须禁用云分层。 

**说明**
- 如果在安装 Azure 文件同步代理之前安装了重复数据删除，则需要重新启动以支持在同一卷上进行重复数据删除和云分层。
- 如果在启用云分层之后在卷上启用了重复数据删除，则初始重复数据删除优化作业将优化尚未分层的卷上的文件，并将对云分层产生以下影响：
    - 可用空间策略将根据卷上的可用空间，使用热度地图继续对文件进行分层。
    - 由于对文件进行重复数据删除优化作业，日期策略将跳过可能已有资格进行分层的文件分层。
- 对于正在进行的重复[数据删除优化](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps)作业，如果尚未对文件进行分层，则使用日期策略的云分层将会延迟。 
    - 示例：如果 MinimumFileAgeDays 设置为7天，而云分层日期策略为30天，则日期策略将在37天后对文件进行分级。
    - 注意：按 Azure 文件同步对文件进行分层后，重复数据删除优化作业将跳过该文件。
- 如果运行 Windows Server 2012 R2 的服务器将安装 Azure 文件同步代理升级到 Windows Server 2016 或 Windows Server 2019，则必须执行以下步骤以支持在同一卷上进行重复数据删除和云分层：  
    - 卸载适用于 Windows Server 2012 R2 的 Azure 文件同步代理并重新启动服务器。
    - 下载新服务器操作系统版本（Windows Server 2016 或 Windows Server 2019）的 Azure 文件同步代理。
    - 安装 Azure 文件同步代理并重新启动服务器。  
    
    注意：卸载并重新安装代理时，会保留服务器上的 Azure 文件同步配置设置。

### <a name="distributed-file-system-dfs"></a>分布式文件系统 (DFS)
Azure 文件同步支持与 DFS 命名空间 (DFS-N) 和 DFS 复制 (DFS-R) 进行互操作。

**DFS 命名空间 (DFS-N)** ：Azure 文件同步在 DFS-N 服务器上完全受支持。 可以在一个或多个 DFS-N 成员上安装 Azure 文件同步代理，以在服务器终结点与云终结点之间同步数据。 有关详细信息，请参阅 [DFS 命名空间概述](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 复制（dfs-r）** ：由于 DFS-r 和 Azure 文件同步都是复制解决方案，因此在大多数情况下，我们建议用 AZURE 文件同步替换 dfs。但在某些情况下，你可能想要同时使用 DFS R 和 Azure 文件同步：

- 从 DFS-R 部署迁移至 Azure 文件同步部署。 有关详细信息，请参阅[将 DFS 复制 (DFS-R) 部署迁移至 Azure 文件同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 并非需要文件数据副本的每个本地服务器都可以直接连接到 internet。
- 分支服务器将数据合并至单个中心服务器，你希望在该服务器中使用 Azure 文件同步。

对于 Azure 文件同步和 DFS 并行工作：

1. 必须在包含 DFS-R 复制文件夹的卷上禁用 Azure 文件同步云分层。
2. 不应在 DFS-R 只读复制文件夹上配置服务器终结点。

有关详细信息，请参阅 [DFS 复制概述](https://technet.microsoft.com/library/jj127250)。

### <a name="sysprep"></a>Sysprep
不支持在安装了 Azure 文件同步代理的服务器上使用 sysprep，这可能会导致意外的结果。 应该在部署服务器映像并完成 sysprep 迷你安装后再安装代理和注册服务器。

### <a name="windows-search"></a>Windows 搜索
如果在服务器终结点上启用了云分层功能，则已分层的文件将被跳过，并且不会由 Windows 搜索进行索引。 非分层文件会适当进行索引。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他分层存储管理 (HSM) 解决方案
其他 HSM 解决方案均无法使用 Azure 文件同步。

## <a name="identity"></a>标识
Azure 文件同步适用于基于 AD 的标准标识，无需进行任何特殊设置，只需设置同步。使用 Azure 文件同步时，一般情况下，大多数访问都是通过 Azure 文件同步缓存服务器而不是通过 Azure 文件共享来完成的。 由于服务器终结点位于 Windows Server 上，并且 Windows Server 支持 AD 和 Windows 样式 Acl 的时间非常长，因此，除了确保向存储同步服务注册的 Windows 文件服务器已加入域外，还无需任何操作。 Azure 文件同步将 Acl 存储在 Azure 文件共享中的文件上，并将其复制到所有服务器终结点。

即使直接对 Azure 文件共享所做的更改需要更长的时间才能同步到同步组中的服务器终结点，你可能还需要确保你可以在云中直接在文件共享上强制实施 AD 权限。 为此，你必须将你的存储帐户加入到本地 AD 中，就像 Windows 文件服务器加入域的方式一样。 若要详细了解有关将存储帐户加入到客户拥有的 Active Directory 的域，请参阅[Azure 文件 Active Directory 概述](storage-files-active-directory-overview.md)。

> [!Important]  
> 要成功部署 Azure 文件同步，无需将存储帐户加入到 Active Directory 域。这是一个严格的可选步骤，可让 Azure 文件共享在用户直接装载 Azure 文件共享时强制实施本地 Acl。

## <a name="networking"></a>网络
Azure 文件同步代理使用 Azure 文件同步 REST 协议和 FileREST 协议与存储同步服务和 Azure 文件共享通信，这两种协议都始终在端口443上使用 HTTPS。 SMB 从不用于在 Windows Server 和 Azure 文件共享之间上载或下载数据。 由于大多数组织允许端口443上的 HTTPS 流量，因此在访问大多数网站时，通常不需要特殊网络配置来部署 Azure 文件同步。

根据组织的策略或独特的法规要求，你可能需要与 Azure 进行更严格的通信，因此 Azure 文件同步提供了用于配置网络的多种机制。 根据你的要求，你可以：

- 通过 ExpressRoute 或 Azure VPN 进行隧道同步和文件上传/下载流量。 
- 使用 Azure 文件和 Azure 网络功能（如服务终结点和专用终结点）。
- 配置 Azure 文件同步以支持你的环境中的代理。
- 限制 Azure 文件同步的网络活动。

若要详细了解如何配置 Azure 文件同步的网络功能，请参阅：
- [Azure 文件同步代理和防火墙设置](storage-sync-files-firewall-and-proxy.md)
- [确保 Azure 文件同步是数据中心内的良好邻居](storage-sync-files-server-registration.md)

## <a name="encryption"></a>加密
使用 Azure 文件同步时，需要考虑三个不同的加密层：对 Windows Server 的静态存储进行加密、在 Azure 文件同步代理和 Azure 之间传输加密，以及在 Azure 文件共享中对数据进行静态加密。 

### <a name="windows-server-encryption-at-rest"></a>Windows Server 静态加密 
对于通常使用 Azure 文件同步：在文件系统下加密的 Windows Server 上的数据，可以使用两种策略来加密，以便对文件系统和写入它的所有数据进行加密，并在文件格式本身中进行加密。 这些方法不相互排斥;如果需要，可以将它们一起使用，因为加密的用途不同。

为了在文件系统下提供加密，Windows Server 提供了 BitLocker 收件箱。 BitLocker 对 Azure 文件同步是完全透明的。使用加密机制（例如 BitLocker）的主要原因是，通过窃取磁盘来阻止本地数据中心的物理渗透数据，并防止未授权的操作系统对数据执行未经授权的读取/写入操作。 若要了解有关 BitLocker 的详细信息，请参阅[bitlocker 概述](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview)。

第三方产品的工作方式类似于 BitLocker，因为它们位于 NTFS 卷的下面，也应该与 Azure 文件同步完全透明地配合使用。 

加密数据的另一个主要方法是在应用程序保存文件时加密文件的数据流。 某些应用程序可能会以本机方式执行此操作，但这种情况通常并非如此。 用于加密文件数据流的方法的一个示例是 Azure 信息保护（AIP）/Azure Rights Management Services （Azure RMS）/Directory RMS。 使用加密机制（例如 AIP/RMS）的主要原因是，用户可以通过将数据从文件共享复制到备用位置（如闪存驱动器），或通过电子邮件将其发送给未经授权的人员。 当文件的数据流作为文件格式的一部分进行加密时，此文件将继续在 Azure 文件共享中进行加密。 

Azure 文件同步不会与 NTFS 加密文件系统（NTFS EFS）或位于文件系统但文件数据流下面的第三方加密解决方案互操作。 

### <a name="encryption-in-transit"></a>传输中加密
Azure 文件同步代理使用 Azure 文件同步 REST 协议和 FileREST 协议与存储同步服务和 Azure 文件共享通信，这两种协议都始终在端口443上使用 HTTPS。 Azure 文件同步不通过 HTTP 发送未加密的请求。 

Azure 存储帐户包含一个用于在传输过程中要求加密的开关，该开关在默认情况下是启用的。 即使在存储帐户级别上的交换机处于禁用状态，这意味着可以使用未加密的 Azure 文件共享连接，Azure 文件同步仍将仅使用加密通道来访问文件共享。

禁用存储帐户加密的主要原因是为了支持必须在较早的操作系统（例如 Windows Server 2008 R2 或更低版本的 Linux 分发）上运行的旧应用程序，以便直接与 Azure 文件共享通信。 如果旧版应用程序与文件共享的 Windows Server 缓存通信，则切换此设置将不起作用。 

强烈建议确保启用传输中数据的加密。

有关传输中加密的详细信息，请参阅[要求在 Azure 存储中进行安全传输](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。

### <a name="azure-file-share-encryption-at-rest"></a>Azure 文件共享静态加密
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>存储层
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>启用标准文件共享，跨越多达 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>区域可用性
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Azure 文件同步区域可用性
Azure 文件同步在以下区域中提供：

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
| 公共 | 法国 | 法国南部 * | `francesouth` |
| 公共 | 印度 | 印度中部 | `centralindia` |
| 公共 | 印度 | 印度南部 | `southindia` |
| 公共 | 日本 | 日本东部 | `japaneast` |
| 公共 | 日本 | 日本西部 | `japanwest` |
| 公共 | 韩国 | 韩国中部 | `koreacentral` |
| 公共 | 韩国 | 韩国南部 | `koreasouth` |
| 公共 | 南非 | 南非北部 | `southafricanorth` |
| 公共 | 南非 | 南非西部 * | `southafricawest` |
| 公共 | 阿拉伯联合酋长国 | 阿拉伯联合酋长国中部 * | `uaecentral` |
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

对于用星号标记的区域，必须与 Azure 支持部门联系，请求访问这些区域中的 Azure 存储。 [本文档](https://azure.microsoft.com/global-infrastructure/geographies/)概述了此过程。

## <a name="redundancy"></a>冗余
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> 异地冗余存储和区域冗余存储能够将存储手动故障转移到次要区域。 当你使用 Azure 文件同步时，我们建议你不要在发生灾难的情况下执行此操作，因为数据丢失的可能性会增加。 如果你想要启动存储的手动故障转移，则需要与 Microsoft 建立支持案例，以便 Azure 文件同步恢复与辅助终结点的同步。

## <a name="migration"></a>迁移
如果你有现有的 Windows 文件服务器，可以就地直接安装 Azure 文件同步，而无需将数据移到新服务器。 如果你打算迁移到新的 Windows 文件服务器作为采用 Azure 文件同步的一部分，则可以使用以下几种方法来移动数据：

- 为旧文件共享和新的文件共享创建服务器终结点，并让 Azure 文件同步同步服务器终结点之间的数据。 这种方法的优点在于，它可以非常轻松地在新的文件服务器上过度订阅存储，因为 Azure 文件同步可以感知云分层。 准备就绪后，可以将最终用户剪切到新服务器上的文件共享中，并删除旧文件共享的服务器终结点。

- 仅在新文件服务器上创建服务器终结点，并使用 `robocopy`将数据从旧文件共享复制到中。 根据新服务器上文件共享的拓扑（每个卷上有多少共享空间，每个卷的可用空间如何，等等），可能需要临时预配其他存储，因为预期在本地数据中心内从旧服务器 `robocopy` 到新服务器的速度要快于 Azure 文件同步将数据移动到 Azure。

还可以使用 Data Box 将数据迁移到 Azure 文件同步部署。 大多数情况下，当客户想要使用 Data Box 引入数据时，他们会这样做，因为他们认为它会提高部署的速度，或者因为它将有助于限制带宽方案。 尽管使用 Data Box 将数据引入到 Azure 文件同步部署中会降低带宽利用率，但大多数情况下，使用上述方法之一来实现联机数据上传可能会更快。 若要详细了解如何使用 Data Box 将数据引入 Azure 文件同步部署，请参阅[使用 Azure Data Box 将数据迁移到 Azure 文件同步](storage-sync-offline-data-transfer.md)。

客户在将数据迁移到新的 Azure 文件同步部署时，常见的错误是直接将数据复制到 Azure 文件共享，而不是复制到其 Windows 文件服务器上。 尽管 Azure 文件同步会标识 Azure 文件共享上的所有新文件，并将它们同步回 Windows 文件共享，但这通常比通过 Windows 文件服务器加载数据的速度要慢得多。 许多 Azure 复制工具（如 AzCopy）具有不复制文件的所有重要元数据的额外缺点，例如时间戳和 Acl。

## <a name="antivirus"></a>防病毒
由于防病毒通过扫描文件中的已知恶意代码进行工作，因此防病毒产品可能导致重新调用分层文件。 在 Azure 文件同步代理 4.0 及更高版本中，分层文件已设置安全 Windows 属性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS。 我们建议你咨询软件供应商，以了解如何配置其解决方案以跳过读取已设置此属性的文件（许多解决方案会自动执行此操作）。 

Microsoft 的内部防病毒解决方案 Windows Defender 和 System Center Endpoint Protection (SCEP) 都会自动跳过读取设有此属性的文件。 我们已对这两个解决方案进行了测试并发现了一个小问题：向现有的同步组添加服务器时，在新服务器上会重新调用（下载）小于 800 字节的文件。 这些文件将保留在新服务器上并且不会分层，因为它们不符合分层大小要求 (> 64kb)。

> [!Note]  
> 防病毒供应商可以使用[Azure 文件同步的防病毒兼容性测试套件](https://www.microsoft.com/download/details.aspx?id=58322)（可从 Microsoft 下载中心下载）来检查其产品与 Azure 文件同步之间的兼容性。

## <a name="backup"></a>备份 
与防病毒解决方案一样，备份解决方案可能导致重新调用分层文件。 建议使用云备份解决方案来备份 Azure文件共享，而不是使用本地备份产品。

如果使用本地备份解决方案，则应在禁用云分层的同步组中的服务器上执行备份。 执行还原时，使用卷级别或文件级还原选项。 使用文件级别还原选项还原的文件将同步到同步组中的所有终结点，现有文件将被替换为从备份还原的版本。  卷级别的还原不会替换 Azure 文件共享或其他服务器终结点中较新的文件版本。

> [!Note]  
> 祼机 (BMR) 还原可能会导致意外的结果且当前不受支持。

> [!Note]  
> 使用 Azure 文件同步代理的版本9时，现在支持在启用云分层的卷上使用 VSS 快照（包括 "以前的版本" 选项卡）。 但是，必须通过 PowerShell 启用以前版本的兼容性。 [了解操作方法](storage-files-deployment-guide.md)。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>后续步骤
* [考虑防火墙和代理设置](storage-sync-files-firewall-and-proxy.md)
* [规划 Azure 文件部署](storage-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
* [监视 Azure 文件同步](storage-sync-files-monitoring.md)