---
title: 了解 Azure 文件同步的云分层 | Microsoft Docs
description: 了解云分层，这是一项可选的 Azure 文件同步功能。 经常访问的文件以本地方式缓存在服务器上;其他内容分层为 Azure 文件。
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e5aafaa02f503582bd0050f8a6389d78b52eaa76
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939147"
---
# <a name="cloud-tiering-overview"></a>云分层概述
云分层是 Azure 文件同步的一项可选功能，其中经常访问的文件在服务器本地缓存，而所有其他文件根据策略设置分层到 Azure 文件。 当文件分层时，Azure 文件同步文件系统筛选器 (StorageSync.sys) 将本地文件替换为指针或重分析点。 重分析点表示 Azure 文件中的文件 URL。 分层文件在 NTFS 中设置了“脱机”属性和 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 属性，这样第三方应用程序便能安全地识别分层文件。
 
当用户打开分层文件时，Azure 文件同步无需知道该文件存储在 Azure 中，即可从 Azure 文件中无缝地撤回文件数据。 
 
 > [!Important]  
 > Windows 系统卷上不支持云分层。
    
 > [!Important]  
 > 若要撤回已分层的文件，网络带宽应至少为 1 Mbps。 如果网络带宽小于 1 Mbps，则文件可能无法重新调用，出现超时错误。

## <a name="cloud-tiering-faq"></a>云分层 FAQ

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>云分层的工作原理是什么？
Azure 文件同步系统筛选器生成每个服务器终结点上命名空间的“热度地图”。 它监视一段时间内的访问情况（读取和写入操作），然后根据访问频率和新近度，向每个文件分配热度分数。 最近打开的频繁访问文件被视为“热”，而几乎无人打开且已有一段时间未获访问的文件则被视为“冷”。 如果服务器上的文件卷超过设置的卷可用空间阈值，它会将最冷的文件分层到 Azure 文件中，直到达到可用空间百分比。

此外，还可以在每个服务器终结点上指定一个日期策略，该策略将对在指定天数内未访问的任何文件进行分层，而不考虑本地存储容量。 如果你知道该服务器终结点中的文件不需要在本地保留一段特定的时间，则这是一个不错的选择。 这为同一卷上的其他终结点释放了宝贵的本地磁盘容量，以缓存其更多的文件。

云分层热度地图实质上是一个排序列表，其中包含正在同步的所有文件，并且位于启用了云分层的位置。 为了确定热度地图中单个文件的相对位置，系统将按顺序使用以下两个时间戳中的最大值：最大 (上次访问时间、上次修改时间、创建时间) 。 通常，会跟踪和提供上次访问时间。 但是，当创建新的服务器终结点时，如果启用了云分层，最初没有足够的时间来观察文件访问。 在没有上次访问时间的情况下，上次修改时间用于计算热度地图中的相对位置。 同一回退适用于日期策略。 如果没有上次访问时间，则日期策略将作用于上次修改时间。 如果不可用，它将回退到文件的创建时间。 随着时间的推移，系统会发现更多的文件访问请求，并透视到主要使用自跟踪的上次访问时间。

云分层不依赖 NTFS 功能跟踪上次访问时间。 默认情况下，此 NTFS 功能处于关闭状态，因此，由于性能方面的原因，我们不建议您手动启用此功能。 云分层分别和高效地跟踪上次访问时间。

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>文件对层的最小文件大小是多少？

对于代理版本9和更高版本，文件到层级的最小文件大小基于文件系统群集大小。 适用于云分层的最小文件大小以2倍的群集大小计算，最低 8 KB 计算。 下表根据卷群集大小说明了可以分层的最小文件大小：

|卷群集大小 (字节)  |此大小或更大的文件可以分层  |
|----------------------------|---------|
|4 KB 或更小的 (4096)       | 8 KB    |
|8 KB (8192)                  | 16 KB   |
|16 KB (16384)                | 32 KB   |
|32 KB (32768)                | 64 KB   |
|64 KB (65536)     | 128 KB  |

当前支持的群集大小为 64 KB，但对于较大的大小，云分层不起作用。

Windows 使用的所有文件系统将基于群集大小的硬盘组织 (也称为分配单元大小) 。 群集大小表示可用于保存文件的最小磁盘空间量。 如果文件大小不能超过群集大小的偶数倍，则必须使用额外的空间来保存到群集大小的下一个多个空间。

Windows Server 2012 R2 和更高版本的 NTFS 卷支持 Azure 文件同步。 下表描述了创建新的 NTFS 卷时的默认群集大小。 

|卷大小    |Windows Server 2012R2 和更高版本 |
|---------------|---------------|
|7 MB – 16 TB   | 4 KB          |
|16TB – 32 TB   | 8 KB          |
|32TB – 64 TB   | 16 KB         |
|64TB – 128 TB  | 32 KB         |
|128TB – 256 TB | 64 KB         |
|> 256 TB       | 不支持 |

创建卷时，可以使用其他群集大小手动格式化该卷。 如果卷源自较早版本的 Windows，则默认群集大小也可能不同。 [本文详细介绍了默认群集大小。](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) 即使选择了小于 4 KB 的群集大小，也可以分层的最小文件大小的 8 KB 限制。 即使在技术上，2 KB 的群集大小将等于小于 8 KB，也 (。 ) 

NTFS 存储极小的文件（1 KB 到 4 KB 大小的文件）的方式中提供了绝对最小值的原因。 根据卷的其他参数，可能不会在磁盘上的群集中存储较小的文件。 将此类文件直接存储在卷的主文件表或 "MFT 记录" 中可能更有效。 云分层重新分析点始终存储在磁盘上，并且仅占用一个群集。 分层此类小文件最终可能无空间节约。 极端情况下，可能会在启用云分层的情况下使用更多空间。 为避免这种情况，云分层将分层的文件的最小大小为 4 kb 或更小的群集大小的 8 KB。

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>卷可用空间分层策略的工作原理是什么？
卷可用空间是要在服务器终结点所在的卷上保留的可用空间量。 例如，如果将有一个服务器终结点的卷的卷可用空间设置为 20%，那么最近访问的文件最多占用 80% 的卷空间，而其余所有不适应此空间的文件都会被分层到 Azure 中。 卷可用空间适用于卷级别，不适用于各个目录或同步组的级别。 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>卷可用空间分层策略如何处理新服务器终结点？
如果有服务器终结点新预配并连接到 Azure 文件共享，服务器会先下拉命名空间，再下拉实际文件，直到它达到卷可用空间阈值。 此过程亦称为“快速灾难恢复”或“快速命名空间还原”。

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>如果卷上有多个服务器终结点，该如何解释卷可用空间？
如果卷上有多个服务器终结点，有效卷可用空间阈值为在该卷上的所有服务器终结点中指定的最大卷可用空间。 将根据使用模式对文件进行分层，而不考虑它们属于哪一个服务器终结点。 例如，如果卷上有两个服务器终结点 Endpoint1 和 Endpoint2，其中 Endpoint1 的卷可用空间阈值为 25%，Endpoint2 的卷可用空间阈值为 50%，那么这两个服务器终结点的卷可用空间阈值将为 50%。 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>日期分层策略如何与卷可用空间分层策略配合工作？ 
针对服务器终结点启用云分层时，可以设置卷可用空间策略。 此策略始终优先于其他任何策略，包括日期策略。 （可选）可以为该卷上的每个服务器终结点启用日期策略。 此策略管理仅将访问 (的文件（在此策略描述的天数内读取或写入) ）保存在本地。 不会按指定的天数访问文件，将对其进行分层。 

云分层使用 "上次访问时间" 来确定应分层哪些文件。 云分层筛选器驱动程序 ( # A0) 会跟踪上次访问时间，并将信息记录在云分层热存储中。 你可以使用服务器本地 PowerShell cmdlet 检索热度存储并将其保存到 CSV 文件中。

```powershell
# There is a single heat store for files on a volume / server endpoint / individual file.
# The heat store can get very large. If you only need to retrieve the "coolest" number of items, use -Limit and a number

# Import the PS module:
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'

# VOLUME FREE SPACE: To get the order in which files will be tiered using the volume free space policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrder

# DATE POLICY: To get the order in which files will be tiered using the date policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrderV2

# Find the heat store information for a particular file:
Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'
```

> [!IMPORTANT]
> 最后访问的时间戳不是 NTFS 跟踪的属性，因此默认情况下在文件资源管理器中不可见。 不要对文件使用上次修改时间戳来检查日期策略是否按预期方式工作。 此时间戳只跟踪写入，而不跟踪读取。 使用所示的 cmdlet 获取此评估的上次访问时间戳。

> [!WARNING]
> 不要打开跟踪上次访问时间戳的 NTFS 功能文件和文件夹。 此功能在默认情况下处于关闭状态，因为这会对性能产生很大影响。 Azure 文件同步将自动、高效地跟踪最近访问时间，并且不利用此 NTFS 功能。

请记住，卷可用空间策略始终优先，当卷上没有足够的可用空间来保留日期策略所述的文件数天后，Azure 文件同步将继续对 coldest 文件进行分层，直到达到卷可用空间百分比。

例如，假设基于日期的分层策略指定保留天数为 60 天，卷可用空间策略指定可用空间百分比为 20%。 如果应用日期策略后，卷上的可用空间少于20%，则卷可用空间策略将开始，并覆盖日期策略。 这会导致更多的文件分层，使服务器上保留的数据量可从 60 天数据减少为 45 天数据。 相反，此策略会强制将超出时间范围的文件分层，即使尚未达到可用空间的阈值 – 因此，即使卷是空的，也会将 61 天前的文件分层。

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>我该如何确定适当的卷可用空间量？
应在本地保留的数据量由以下几个因素决定：带宽、数据集的访问模式和预算。 如果连接带宽低，建议在本地保留更多数据，以确保用户滞后时间最短。 反之，可以给定时间段内的变动率为依据。 例如，如果确定 1TB 数据集每月大约有 10% 发生变化或获主动访问，建议在本地保留 100GB，以免频繁召回文件。 如果卷为 2TB，建议在本地保留 5%（或 100GB）；也就是说，剩余 95% 是卷可用空间百分比。 不过，建议添加缓冲区，以将变动率更高的时间段考虑在内；也就是说，从较低的卷可用空间百分比入手，以后再根据需要进行调整。 

在本地保留更多数据意味着降低流出费用，因为从 Azure 召回的文件变少；但还必须维护更大的本地存储，这需要自己支付费用。 部署 Azure 文件同步的实例后，可以查看存储帐户的出口，以大致衡量你的卷可用空间设置是否适合你的使用情况。 假设存储帐户仅包含 Azure 文件同步云终结点（即同步共享），那么高流出意味着要从云中召回许多文件，应考虑增加本地缓存。

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>我添加了一个新的服务器终结点。 要在多长时间后，我的文件会在此服务器上分层？

每个集策略是否需要进行分层，每小时计算一次。 创建新服务器终结点时，可能会出现两种情况：

1. 当你添加新的服务器终结点时，通常文件存在于该服务器位置。 需要先上传，然后才能开始云分层。 在完成所有文件的初始上传之前，卷可用空间策略将不会开始工作。 但是，只要文件已上传，可选的日期策略就会开始处理单独的文件。 此处还适用于一小时的时间间隔。 
2. 添加新的服务器终结点时，可以将空的服务器位置连接到包含数据的 Azure 文件共享。 对于第二台服务器还是在灾难恢复情况下。 如果选择在初始下载到服务器的过程中下载命名空间并撤回内容，则在命名空间关闭后，将根据上次修改的时间戳来重新调用文件。 在卷可用空间策略和可选的日期策略中，只会重新调用多少个文件。

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>如何判断文件是否已分层？
有多种查看文件是否已被分层到 Azure 文件共享的方法：
    
   *  **查看文件上的文件属性。**
     右键单击文件，转到“详细信息”****，再向下滚动到“属性”**** 属性。 分层的文件具有以下属性集：     
        
        | 属性字母 | 属性 | 定义 |
        |:----------------:|-----------|------------|
        | 包含当前请求的 URL 的 | 存档 | 指示备份软件应备份此文件。 无论文件被分层还是完全存储在磁盘上，始终都会设置此属性。 |
        | P | 稀疏文件 | 指示该文件为稀疏文件。 稀疏文件是 NTFS 提供的专用化文件类型，用于在占用空间流上的文件几乎为空时提高使用效率。 Azure 文件同步将使用稀疏文件，因为文件会被完全分层或部分召回。 在完全分层文件中，文件流存储在云中。 在部分召回的文件中，文件的一部分已在磁盘上。 如果文件被完全召回到磁盘，Azure 文件同步会将其从稀疏文件转换为常规文件。 此属性仅在 Windows Server 2016 及更低版本上设置。|
        | M | 回调数据访问 | 指示文件的数据不会在本地存储上全部显示。 读取文件将导致至少从服务器终结点所连接到的 Azure 文件共享提取一些文件内容。 仅在 Windows Server 2019 上设置此属性。 |
        | L | 重分析点 | 指示该文件包含重分析点。 重分析点是供文件系统筛选器使用的特殊指针。 Azure 文件同步使用重分析点来定义 Azure 文件同步文件系统筛选器 (StorageSync.sys) 存储文件的云位置。 这样即可支持无缝访问。 用户无需知道是否使用了 Azure 文件同步或如何获取在 Azure 文件共享中的文件访问权限。 如果文件被完全召回，则 Azure 文件同步将从此文件中删除重分析点。 |
        | O | Offline | 指示文件的部分或全部内容未存储在磁盘上。 如果文件被完全召回，Azure 文件同步将删除此属性。 |

        ![文件的“属性”对话框（“详细信息”选项卡被选中）](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        你可以通过向文件资源管理器的表显示添加“属性”**** 字段，查看文件夹中所有文件的属性。 若要执行此操作，请右键单击现有的列（例如“大小”****），选择“更多”****，然后从下拉列表中选择“属性”****。
        
   * **用于 `fsutil` 检查文件中的重新分析点。**
       如前面的选项中所述，分层的文件始终具有重分析点集。 重分析指针是 Azure 文件同步文件系统筛选器 (StorageSync.sys) 的特殊指针。 若要查看文件是否包含重分析点，你可以在提升的命令提示符或 PowerShell 窗口中运行 `fsutil` 实用程序：
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        如果文件包含重分析点，应能看到“重分析标记值: 0x8000001e”****。 此十六进制值是 Azure 文件同步拥有的重分析点值。输出还包含用于表示 Azure 文件共享上的文件路径的重分析数据。

        > [!WARNING]  
        > 同时，`fsutil reparsepoint` 实用程序命令还包含删除重分析点的功能。 除非 Azure 文件同步工程团队要求，否则请不要执行此命令。 运行此命令可能会导致数据丢失。 

<a id="afs-recall-file"></a>
### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>我要使用的文件已分层。 如何将文件召回到磁盘以供本地使用？
将文件召回到磁盘的最简单方法是打开此文件。 Azure 文件同步文件系统筛选器 (StorageSync.sys) 将会从 Azure 文件共享中无缝下载此文件，你无需执行任何操作。 对于可被部分读取的文件类型（如多媒体文件或 .zip 文件），打开文件后不会下载整个文件。

此外，你也可以使用 PowerShell 来强制召回文件。 在需要同时召回多个文件（例如，一个文件夹内的所有文件）的情况下，可能更适合使用此选项。 打开已安装 Azure 文件同步的服务器节点的 PowerShell 会话，然后运行以下 PowerShell 命令：
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
可选参数：
* `-Order CloudTieringPolicy` 将首先撤回最近修改或访问的文件，并由当前分层策略允许。 
    * 如果配置了卷可用空间策略，则在达到 "卷可用空间" 策略设置之前，将会重新调用文件。 例如，如果 "批量可用" 策略设置为20%，则一旦卷可用空间达到20%，撤回就会停止。  
    * 如果配置了卷可用空间和日期策略，则在达到 "卷可用空间或日期" 策略设置之前，将会重新调用文件。 例如，如果 "批量可用" 策略设置为20%，日期策略为7天，则当卷可用空间达到20% 或在7天内访问或修改的所有文件均为本地时，撤回将停止。
* `-ThreadCount` 确定可并行回调的文件数。
* `-PerFileRetryCount`确定尝试重新调用当前被阻止的文件的频率。
* `-PerFileRetryDelaySeconds`确定两次重试回调尝试之间的时间间隔（以秒为单位），并且应始终结合前面的参数使用。

例如：
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
> - 在将新的服务器终结点添加到现有同步组时，还可以使用 Invoke-StorageSyncFileRecall cmdlet 提高文件下载性能。  
>- 如果承载服务器的本地卷没有足够可用空间可用于召回所有分层数据，则 `Invoke-StorageSyncFileRecall` cmdlet 会失败。  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>为什么在使用 Azure 文件同步后，文件的 *磁盘上的大小* 不与 *size* 属性匹配？ 
Windows 文件资源管理器公开了两个属性来表示文件的大小：即“大小”**** 和“占用空间”****。 这些属性的含义略有不同。 “大小”**** 表示文件的完整大小。 “占用空间”**** 表示存储在磁盘上的文件流的大小。 这些属性的值可能因多种原因而异，例如压缩、使用重复数据删除或使用 Azure 文件同步进行云分层。如果将文件分层到 Azure 文件共享，则磁盘上的大小为零，因为文件流存储在 Azure 文件共享中，而不是存储在磁盘上。 另外，文件也可能会被部分分层（或部分召回）。 在部分分层文件中，该文件的一部分位于磁盘上。 应用程序（例如，多媒体播放器或压缩工具）对文件进行了部分读取时可能会发生此情况。 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>如何强制对文件或目录进行分层？

> [!NOTE]
> 选择要分层的目录时，只会对目录中当前的文件进行分层。 在该时间之后创建的任何文件不会自动分层。

启用云分层功能后，它会根据上次访问和修改时间自动分层文件，以实现云终结点上指定的卷可用空间百分比， 但有时你可能需要手动强制分层文件。 如果你要保存在长时间内计划不再次使用的大型文件，并且想要将卷上现有可用空间用于其他文件和文件夹，则可使用此方法。 可使用以下 PowerShell 命令强制分层：

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>为什么分层文件在 Windows 资源管理器中未显示缩略图或预览？
对于分层文件，不会在服务器终结点显示缩略图和预览。 此行为是预期行为，因为 Windows 中的缩略图缓存功能有意跳过读取具有脱机属性的文件。 启用云分层后，通过分层文件读取将导致下载 (撤回) 。

此行为并不特定于 Azure 文件同步，Windows 资源管理器会为设置了脱机属性的任何文件显示 "灰色 X"。 通过 SMB 访问文件时，你将看到 X 图标。 有关此行为的详细说明，请参阅 [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

<a id="afs-tiering-disabled"></a>
### <a name="i-have-cloud-tiering-disabled-why-are-there-tiered-files-in-the-server-endpoint-location"></a>我禁用了云分层，为什么服务器终结点位置中存在分层文件？

有两个原因会导致分层文件存在于服务器终结点位置：

- 向现有同步组添加新的服务器终结点时，元数据首先将同步到服务器，然后将文件下载到后台服务器。 在本地下载文件之前，这些文件将显示为已分层。 若要在将新服务器添加到同步组时提高文件下载性能，请使用 [StorageSyncFileRecall](storage-sync-cloud-tiering.md#afs-recall-file) cmdlet。

- 如果已在服务器终结点上启用云分层，然后禁用，则文件在被访问之前将保持分层。


## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
