---
title: 规划 Azure 文件同步部署 | Microsoft Docs
description: 了解规划 Azure 文件部署时应考虑的问题。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 2/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bc9f8e29a744a3a40d17b3814c7124eb37c1543b
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2019
ms.locfileid: "67269418"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>规划 Azure 文件同步部署
使用 Azure 文件同步，即可将组织的文件共享集中在 Azure 文件中，同时又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本指南介绍有关 Azure 文件同步部署的重要注意事项。 我们建议另外阅读[规划 Azure 文件部署](storage-files-planning.md)。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Azure 文件同步的术语
在阅读 Azure 文件同步部署的规划详细信息之前，必须先了解术语。

### <a name="storage-sync-service"></a>存储同步服务
存储同步服务是 Azure 文件同步的顶级 Azure 资源。存储同步服务资源是存储帐户资源的对等物，可按类似方式部署到 Azure 资源组。 由于存储同步服务可通过多个同步组创建与多个存储帐户的同步关系，因此需要从存储帐户资源中获得一个不同的顶级资源。 一个订阅可部署有多个存储同步服务资源。

### <a name="sync-group"></a>同步组
同步组定义一组文件的同步拓扑。 同步组中的终结点保持彼此同步。 例如，如果想使用 Azure 文件同步管理两组不同文件，需创建两个同步组并在每个同步组中添加不同的终结点。 存储同步服务可承载任意数量的同步组。  

### <a name="registered-server"></a>已注册服务器
已注册服务器对象表示服务器（或群集）与存储同步服务之间的信任关系。 可在存储同步服务实例中随意注册任意数量的服务器。 但是，每次只能将服务器（或群集）注册到一个存储同步服务。

### <a name="azure-file-sync-agent"></a>Azure 文件同步代理
Azure 文件同步代理是一个可下载包，可实现 Windows 服务器与 Azure 文件共享的同步。 Azure 文件同步代理包含 3 个主要组件： 
- **FileSyncSvc.exe**：后台 Windows 服务，负责监视服务器终结点的更改并启动到 Azure 的同步会话。
- **StorageSync.sys**：Azure 文件同步系统筛选器，负责将文件分层存入 Azure 文件（若云分层已启用）。
- **PowerShell 管理 cmdlet**：PowerShell cmdlet，用于与 Microsoft.StorageSync Azure 资源提供程序进行交互。 可在以下位置（默认位置）找到这些文件：
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>服务器终结点
服务器终结点代表已注册服务器上的特定位置，例如服务器卷中的文件夹。 如果命名空间不重叠（例如 `F:\sync1` 和 `F:\sync2`），多个服务器终结点可存在于同一个卷。 可为每个服务器终结点单独配置云分层策略。 

你可以通过装入点创建服务器终结点。 请注意，服务器终结点中的装入点将被跳过。  

你可以在系统卷上创建服务器终结点，但这样做有两个限制：
* 无法启用云分层。
* 不执行快速命名空间还原（其中系统快速关闭整个命名空间，然后启动以撤回内容）。


> [!Note]  
> 仅支持不可删除的卷。  服务器终结点路径不支持通过远程共享映射的驱动器。  此外，服务器终结点可能位于 Windows 系统卷，然而系统卷上不支持云分层。

如果将带一组现有文件的服务器位置作为服务器终结点添加到同步组，则这些文件将与同步组中其他终结点上已有的任何其他文件进行合并。

### <a name="cloud-endpoint"></a>云终结点
云终结点是一个 Azure 文件共享，它属于同步组。 整个 Azure 文件共享同步和 Azure 文件共享只能属于一个云终结点。 因此，Azure 文件共享只能是一个同步组的成员。 如果将带一组现有文件的 Azure 文件共享作为云终结点添加到同步组中，则现有文件将与同步组中其他终结点上已有的任何其他文件进行合并。

> [!Important]  
> Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时仅针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。有关详细信息，请参阅 [Azure 文件常见问题解答](storage-files-faq.md#afs-change-detection)。

### <a name="cloud-tiering"></a>云分层 
云分层是 Azure 文件同步的一项可选功能，其中经常访问的文件在服务器本地缓存，而所有其他文件根据策略设置分层到 Azure 文件。 有关详细信息，请参阅[了解云分层](storage-sync-cloud-tiering.md)。

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Azure 文件同步系统要求和互操作性 
本部分介绍了 Azure 文件同步代理的系统要求以及与 Windows Server 功能和角色以及第三方解决方案的互操作性。

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 请注意，其检查涵盖了下面提到的大多数但并非全部功能；建议你仔细读完本部分的剩余内容，以确保你的部署顺利进行。 

#### <a name="download-instructions"></a>下载说明
1. 请确保已安装了最新版本的 PackageManagement 和 PowerShellGet（这允许你安装预览版模块）
    
    ```powershell
        Install-Module -Name PackageManagement -Repository PSGallery -Force
        Install-Module -Name PowerShellGet -Repository PSGallery -Force
    ```
 
2. 重启 PowerShell
3. 安装模块
    
    ```powershell
        Install-Module -Name Az.StorageSync -AllowPrerelease -AllowClobber -Force
    ```

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

### <a name="system-requirements"></a>系统要求
- 运行 Windows Server 2012 R2、Windows Server 2016 或 Windows Server 2019 的服务器：

    | Version | 支持的 SKU | 支持的部署选项 |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | 数据中心和标准版 | 完全（带 UI 的服务器） |
    | Windows Server 2016 | 数据中心和标准版 | 完全（带 UI 的服务器） |
    | Windows Server 2012 R2 | 数据中心和标准版 | 完全（带 UI 的服务器） |

    将来的 Windows Server 版本将在发布后添加。 Windows 旧版本可能根据用户的反馈添加。

    > [!Important]  
    > 我们建议使用 Windows 更新提供的最新更新，将用于 Azure 文件同步的所有服务器保持最新。 

- 最少具有 2 GiB 内存的服务器。

    > [!Important]  
    > 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
    
- 使用 NTFS 文件系统进行了格式化的本地附加卷。

### <a name="file-system-features"></a>文件系统功能

| Feature | 支持状态 | 说明 |
|---------|----------------|-------|
| 访问控制列表 (ACL) | 完全支持 | Windows ACL 由 Azure 文件同步进行保留，并由 Windows Server 在服务器终结点上强制实施。 如果直接在云中访问文件，则 Azure 文件不（尚不）支持 Windows ACL。 |
| 硬链接 | 已跳过 | |
| 符号链接 | 已跳过 | |
| 装入点 | 部分支持 | 装入点可能是服务器终结点的根，但如果包含在服务器终结点的命名空间内，则跳过此装入点。 |
| 交接点 | 已跳过 | 例如，分布式文件系统中的 DfrsrPrivate 和 DFSRoots 文件夹。 |
| 重分析点 | 已跳过 | |
| NTFS 压缩 | 完全支持 | |
| 稀疏文件 | 完全支持 | 稀疏文件会进行同步（不受阻），但作为完整文件同步到云。 如果在云中（或在其他服务器上）更改文件内容，则下载更改时，文件不再是稀疏文件。 |
| 备用数据流 (ADS) | 保留但不同步 | 例如，由文件分类基础结构创建的分类标记就不会同步。 每个服务器终结点的文件上的现有分类标记都保留不变。 |

> [!Note]  
> 仅支持 NTFS 卷。 不支持 ReFS、FAT、FAT32 及其他文件系统。

### <a name="files-skipped"></a>跳过的文件

| 文件/文件夹 | 注意 |
|-|-|
| Desktop.ini | 特定于系统的文件 |
| ethumbs.db$ | 缩略图的临时文件 |
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
**代理版本 5.0.2.0**   
Windows Server 2016 和 Windows Server 2019 上启用了云分层的卷支持重复数据删除。 在启用了云分层的卷上启用重复数据删除后，即可在本地缓存更多文件，而无需预配更多存储。

**Windows Server 2012 R2 或之前的代理版本**  
对于未启用云分层的卷，Azure 文件同步支持在卷上启用 Windows Server 重复数据删除。

**说明**
- 如果在安装 Azure 文件同步代理之前安装重复数据删除，则需要重新启动以支持重复数据删除和云分层在同一个卷上。
- 如果重复数据删除的卷上启用后云分层已启用，初始的重复数据删除优化作业将优化卷上的文件，它已不分层并将对云产生以下影响分层：
    - 根据可用空间的卷上的层文件，可用空间策略将继续使用热度地图。
    - 日期策略将跳过的文件可能已否则适合分层由于重复数据删除优化作业访问文件分层。
- 对于正在进行重复数据删除优化作业，将获取云分层与日期策略延迟由重复数据删除[MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps)设置，如果没有已分层文件。 
    - 示例：如果 MinimumFileAgeDays 设置为 7 天，云分层日期策略为 30 天，策略将文件分层 37 天后的日期。
    - 请注意:一旦文件分层的 Azure 文件同步时，重复数据删除优化作业将跳过该文件。

### <a name="distributed-file-system-dfs"></a>分布式文件系统 (DFS)
Azure 文件同步支持与 DFS 命名空间 (DFS-N) 和 DFS 复制 (DFS-R) 进行互操作。

**DFS 命名空间 (DFS-N)** ：Azure 文件同步在 DFS-N 服务器上完全受支持。 可以在一个或多个 DFS-N 成员上安装 Azure 文件同步代理，以在服务器终结点与云终结点之间同步数据。 有关详细信息，请参阅 [DFS 命名空间概述](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 复制 (DFS-R)** ：因为 DFS-R 和 Azure 文件同步都是复制解决方案，所以在大多数情况下建议将 DFS-R 替换为 Azure 文件同步。不过在以下几个方案中，可能需要同时使用 DFS-R 和 Azure 文件同步：

- 从 DFS-R 部署迁移至 Azure 文件同步部署。 有关详细信息，请参阅[将 DFS 复制 (DFS-R) 部署迁移至 Azure 文件同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 并非需要文件数据副本的每个本地服务器都可以直接连接至 Internet。
- 分支服务器将数据合并至单个中心服务器，你希望在该服务器中使用 Azure 文件同步。

对于 Azure 文件同步和 DFS-R 并行工作的情况：

1. 必须在包含 DFS-R 复制文件夹的卷上禁用 Azure 文件同步云分层。
2. 不应在 DFS-R 只读复制文件夹上配置服务器终结点。

有关详细信息，请参阅 [DFS 复制概述](https://technet.microsoft.com/library/jj127250)。

### <a name="sysprep"></a>Sysprep
不支持在安装了 Azure 文件同步代理的服务器上使用 sysprep，那样做会导致意外结果。 应该在部署服务器映像并完成 sysprep 迷你安装后再安装代理和注册服务器。

### <a name="windows-search"></a>Windows 搜索
如果在服务器终结点上启用了云分层功能，则已分层的文件将被跳过，并且不会由 Windows 搜索进行索引。 非分层文件会适当进行索引。

### <a name="antivirus-solutions"></a>防病毒解决方案
由于防病毒通过扫描文件中的已知恶意代码进行工作，因此防病毒产品可能导致重新调用分层文件。 在 Azure 文件同步代理 4.0 及更高版本中，分层文件已设置安全 Windows 属性 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS。 我们建议你咨询软件供应商，以了解如何配置其解决方案以跳过读取已设置此属性的文件（许多解决方案会自动执行此操作）。

Microsoft 的内部防病毒解决方案 Windows Defender 和 System Center Endpoint Protection (SCEP) 都会自动跳过读取设有此属性的文件。 我们已对这两个解决方案进行了测试并发现了一个小问题：向现有的同步组添加服务器时，在新服务器上会重新调用（下载）小于 800 字节的文件。 这些文件将保留在新服务器上并且不会分层，因为它们不符合分层大小要求 (> 64kb)。

### <a name="backup-solutions"></a>备份解决方案
与防病毒解决方案一样，备份解决方案可能导致重新调用分层文件。 建议使用云备份解决方案来备份 Azure文件共享，而不是使用本地备份产品。

如果使用的是本地备份解决方案，则应在已禁用云分层的同步组中的服务器上执行备份。 执行还原时，使用卷级别或文件级还原选项。 使用文件级别还原选项还原的文件将同步到同步组中的所有终结点，现有文件将被替换为从备份还原的版本。  卷级别的还原不会替换 Azure 文件共享或其他服务器终结点中较新的文件版本。

> [!Note]  
> 祼机 (BMR) 还原可能会导致意外的结果且当前不受支持。

> [!Note]  
> 启用了云分层的卷当前不支持 VSS 快照（包括“以前的版本”选项卡）。 如果启用了云分层，请使用 Azure 文件共享快照从备份还原文件。

### <a name="encryption-solutions"></a>加密解决方案
是否支持加密解决方案取决于其实现方式。 Azure 文件同步现支持：

- BitLocker 加密
- Azure 信息保护、Azure Rights Management Services (Azure RMS) 以及 Active Directory RMS

Azure 文件同步现不支持：

- NTFS 加密文件系统 (EFS)

一般情况下，Azure 文件同步应该支持与文件系统下的加密解决方案（如 BitLocker）以及在文件格式中实现的解决方案（如 Azure 信息保护）进行互操作。 不与文件系统上的解决方案（如 NTFS EFS）进行特殊的互操作。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他分层存储管理 (HSM) 解决方案
其他 HSM 解决方案均无法使用 Azure 文件同步。

## <a name="region-availability"></a>上市区域
Azure 文件同步仅在以下区域中可用：

| 区域 | 数据中心位置 |
|--------|---------------------|
| 澳大利亚东部 | 新南威尔士州 |
| 澳大利亚东南部 | 维多利亚 |
| 巴西南部 | 圣保罗 Paolo 状态 |
| 加拿大中部 | 多伦多 |
| 加拿大东部 | 魁北克市 |
| 印度中部 | 浦那 |
| 美国中部 | 爱荷华州 |
| 东亚 | 中国香港特别行政区 |
| 美国东部 | 弗吉尼亚州 |
| 美国东部 2 | 弗吉尼亚州 |
| 韩国中部| 首尔 |
| 韩国南部| 釜山 |
| 日本东部 | 东京都埼玉县 |
| 日本西部 | 大阪 |
| 美国中北部 | 伊利诺斯州 |
| 北欧 | 爱尔兰 |
| 美国中南部 | Texas |
| 印度南部 | 金奈 |
| 东南亚 | 新加坡 |
| 英国南部 | 伦敦 |
| 英国西部 | 加的夫 |
| 美国亚利桑那州的政府 （预览版） | 亚利桑那 |
| 美国德克萨斯州的政府 （预览版） | Texas |
| 美国弗吉尼亚州政府 （预览版） | 弗吉尼亚州 |
| 西欧 | 荷兰 |
| 美国中西部 | 怀俄明 |
| 美国西部 | California |
| 美国西部 2 | Washington |

Azure 文件同步仅支持与存储同步服务所在区域中的 Azure 文件共享进行同步。

> [!Note]  
> Azure 文件同步目前仅在政府版区域的专用预览版中可用。 请参阅我们[发行说明](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#agent-version-5020)有关在预览程序中注册的说明。

### <a name="azure-disaster-recovery"></a>Azure 灾难恢复
为了防止 Azure 区域丢失，Azure 文件同步集成了[异地冗余存储冗余](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS) 选项。 GRS 存储的工作原理是在主要区域中的存储（你通常与之交互）和配对次要区域中的存储之间使用异步块复制。 发生导致 Azure 区域暂时或永久脱机的灾难时，Microsoft 会将存储故障转移到配对区域。 

> [!Warning]  
> 如果在 GRS 存储帐户中使用 Azure 文件共享作为云终结点，则不应启动存储帐户故障转移。 执行此操作将导致同步停止工作，并且还可能导致新分层的文件出现意外数据丢失。 对于 Azure 区域丢失，Microsoft 会以与 Azure 文件同步兼容的方式触发存储帐户故障转移。

为了支持异地冗余存储和 Azure 文件同步之间的故障转移集成，所有 Azure 文件同步区域都与一个与存储使用的次要区域匹配的次要区域配对。 这些配对如下所示：

| 主要区域      | 配对区域      |
|---------------------|--------------------|
| 澳大利亚东部      | 澳大利亚东南部|
| 澳大利亚东南部 | 澳大利亚东部     |
| 巴西南部        | 美国中南部   |
| 加拿大中部      | 加拿大东部        |
| 加拿大东部         | 加拿大中部     |
| 印度中部       | 印度南部        |
| 美国中部          | 美国东部 2          |
| 东亚           | 东南亚     |
| 美国东部             | 美国西部            |
| 美国东部 2           | 美国中部         |
| 日本东部          | 日本西部         |
| 日本西部          | 日本东部         |
| 韩国中部       | 韩国南部        |
| 韩国南部         | 韩国中部      |
| 北欧        | 西欧        |
| 美国中北部    | 美国中南部   |
| 美国中南部    | 美国中北部   |
| 印度南部         | 印度中部      |
| 东南亚      | 东亚          |
| 英国南部            | 英国西部            |
| 英国西部             | 英国南部           |
| 美国亚利桑那州政府      | 美国德克萨斯州政府       |
| US Gov 爱荷华州         | 美国政府弗吉尼亚州    |
| 美国政府 Virgini      | 美国德克萨斯州政府       |
| 西欧         | 北欧       |
| 美国中西部     | 美国西部 2          |
| 美国西部             | 美国东部            |
| 美国西部 2           | 美国中西部    |

## <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>后续步骤
* [考虑防火墙和代理设置](storage-sync-files-firewall-and-proxy.md)
* [规划 Azure 文件部署](storage-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
* [监视 Azure 文件同步](storage-sync-files-monitoring.md)
