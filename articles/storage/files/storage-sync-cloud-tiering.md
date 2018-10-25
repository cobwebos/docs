---
title: 了解 Azure 文件同步的云分层 | Microsoft Docs
description: 了解 Azure 文件同步的云分层功能
services: storage
author: sikoo
ms.service: storage
ms.topic: article
ms.date: 09/21/2018
ms.author: sikoo
ms.component: files
ms.openlocfilehash: a11e0a1c20617f3065d5b3f8cf59d67cf7aa0179
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2018
ms.locfileid: "48240729"
---
# <a name="cloud-tiering-overview"></a>云分层概述
云分层是 Azure 文件同步的一项可选功能，其中经常访问的文件在服务器本地缓存，而所有其他文件根据策略设置分层到 Azure 文件。 当文件分层时，Azure 文件同步文件系统筛选器 (StorageSync.sys) 将本地文件替换为指针或重分析点。 重分析点表示 Azure 文件中的文件 URL。 分层文件在 NTFS 中设置了“脱机”属性和 FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS 属性，这样第三方应用程序便能安全地识别分层文件。
 
如果用户打开分层文件，Azure 文件同步会从 Azure 文件中无缝召回文件数据，而用户无需知道文件实际存储在 Azure 中。 
 
 > [!Important]  
    > 重要提示：Windows 系统卷上的服务器终结点不支持云分层，只有大小超过 64 KiB 的文件，才能分层到 Azure 文件中。
    
Azure 文件同步不支持对小于 64 KiB 的文件进行分层，因为分层和召回此类小文件的性能开销超过了节省的空间。

## <a name="cloud-tiering-faq"></a>云分层 FAQ

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>云分层的工作原理是什么？
Azure 文件同步系统筛选器生成每个服务器终结点上命名空间的“热度地图”。 它监视一段时间内的访问情况（读取和写入操作），然后根据访问频率和新近度，向每个文件分配热度分数。 最近打开的频繁访问文件被视为“热”，而几乎无人打开且已有一段时间未获访问的文件则被视为“冷”。 如果服务器上的文件卷超过设置的卷可用空间阈值，它会将最冷的文件分层到 Azure 文件中，直到达到可用空间百分比。

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>卷可用空间分层策略的工作原理是什么？
卷可用空间是要在服务器终结点所在的卷上保留的可用空间量。 例如，如果将有一个服务器终结点的卷的卷可用空间设置为 20%，那么最近访问的文件最多占用 80% 的卷空间，而其余所有不适应此空间的文件都会被分层到 Azure 中。 卷可用空间适用于卷级别，不适用于各个目录或同步组的级别。 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>卷可用空间分层策略如何处理新服务器终结点？
如果有服务器终结点新预配并连接到 Azure 文件共享，服务器会先下拉命名空间，再下拉实际文件，直到它达到卷可用空间阈值。 此过程亦称为“快速灾难恢复”或“快速命名空间还原”。

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>如果卷上有多个服务器终结点，该如何解释卷可用空间？
如果卷上有多个服务器终结点，有效卷可用空间阈值为在该卷上的所有服务器终结点中指定的最大卷可用空间。 将根据使用模式对文件进行分层，而不考虑它们属于哪一个服务器终结点。 例如，如果卷上有两个服务器终结点 Endpoint1 和 Endpoint2，其中 Endpoint1 的卷可用空间阈值为 25%，Endpoint2 的卷可用空间阈值为 50%，那么这两个服务器终结点的卷可用空间阈值将为 50%。 

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>我该如何确定适当的卷可用空间量？
应在本地保留的数据量由以下几个因素决定：带宽、数据集的访问模式和预算。 如果连接带宽低，建议在本地保留更多数据，以确保用户滞后时间最短。 反之，可以给定时间段内的变动率为依据。 例如，如果确定 1TB 数据集每月大约有 10% 发生变化或获主动访问，建议在本地保留 100GB，以免频繁召回文件。 如果卷为 2TB，建议在本地保留 5%（或 100GB）；也就是说，剩余 95% 是卷可用空间百分比。 不过，建议添加缓冲区，以将变动率更高的时间段考虑在内；也就是说，从较低的卷可用空间百分比入手，以后再根据需要进行调整。 

在本地保留更多数据意味着降低流出费用，因为从 Azure 召回的文件变少；但还必须维护更大的本地存储，这需要自己支付费用。 部署 Azure 文件同步实例后，便可以查看存储帐户的流出情况，以大致判断卷可用空间设置是否适合自己使用。 假设存储帐户仅包含 Azure 文件同步云终结点（即同步共享），那么高流出意味着要从云中召回许多文件，应考虑增加本地缓存。

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>如何判断文件是否已分层？
有多种查看文件是否已被分层到 Azure 文件共享的方法：
    
   *  **查看文件上的文件属性。**
     右键单击文件，转到“详细信息”，再向下滚动到“属性”属性。 分层的文件具有以下属性集：     
        
        | 属性字母 | 属性 | 定义 |
        |:----------------:|-----------|------------|
        | A | 存档 | 指示备份软件应备份此文件。 无论文件被分层还是完全存储在磁盘上，始终都会设置此属性。 |
        | P | 稀疏文件 | 指示该文件为稀疏文件。 稀疏文件是 NTFS 提供的专用化文件类型，用于在占用空间流上的文件几乎为空时提高使用效率。 Azure 文件同步将使用稀疏文件，因为文件会被完全分层或部分召回。 在完全分层文件中，文件流存储在云中。 在部分召回的文件中，文件的一部分已在磁盘上。 如果文件被完全召回到磁盘，Azure 文件同步会将其从稀疏文件转换为常规文件。 |
        | L | 重分析点 | 指示该文件包含重分析点。 重分析点是供文件系统筛选器使用的特殊指针。 Azure 文件同步使用重分析点来定义 Azure 文件同步文件系统筛选器 (StorageSync.sys) 存储文件的云位置。 这样即可支持无缝访问。 用户无需知道是否使用了 Azure 文件同步或如何获取在 Azure 文件共享中的文件访问权限。 如果文件被完全召回，则 Azure 文件同步将从此文件中删除重分析点。 |
        | O | 脱机 | 指示文件的部分或全部内容未存储在磁盘上。 如果文件被完全召回，Azure 文件同步将删除此属性。 |

        ![文件的“属性”对话框（“详细信息”选项卡被选中）](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        你可以通过向文件资源管理器的表显示添加“属性”字段，查看文件夹中所有文件的属性。 若要执行此操作，请右键单击现有的列（例如“大小”），选择“更多”，然后从下拉列表中选择“属性”。
        
   * “使用 `fsutil` 检查文件上的重分析点”。
       如前面的选项中所述，分层的文件始终具有重分析点集。 重分析指针是 Azure 文件同步文件系统筛选器 (StorageSync.sys) 的特殊指针。 若要查看文件是否包含重分析点，你可以在提升的命令提示符或 PowerShell 窗口中运行 `fsutil` 实用程序：
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        如果文件包含重分析点，应能看到“重分析标记值: 0x8000001e”。 该十六进制值是 Azure 文件同步拥有的重分析点值。输出还会包含表示 Azure 文件共享中文件路径的重分析数据。

        > [!WARNING]  
        > 同时，`fsutil reparsepoint` 实用程序命令还包含删除重分析点的功能。 除非 Azure 文件同步工程团队要求，否则请不要执行此命令。 运行此命令可能会导致数据丢失。 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>我要使用的文件已分层。 如何将文件召回到磁盘以供本地使用？
将文件召回到磁盘的最简单方法是打开此文件。 Azure 文件同步文件系统筛选器 (StorageSync.sys) 将会从 Azure 文件共享中无缝下载此文件，你无需执行任何操作。 对于可被部分读取的文件类型（如多媒体文件或 .zip 文件），打开文件后不会下载整个文件。

此外，你也可以使用 PowerShell 来强制召回文件。 在需要同时召回多个文件（例如，一个文件夹内的所有文件）的情况下，可能更适合使用此选项。 打开已安装 Azure 文件同步的服务器节点的 PowerShell 会话，然后运行以下 PowerShell 命令：
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>使用 Azure 文件同步后，为什么文件的“磁盘占用空间”属性与“大小”属性不一致？ 
Windows 文件资源管理器公开了两个属性来表示文件的大小：即“大小”和“占用空间”。 这些属性的含义略有不同。 “大小”表示文件的完整大小。 “占用空间”表示存储在磁盘上的文件流的大小。 这些属性的值存在差异的原因有多种，例如压缩、使用重复数据删除，或使用 Azure 文件同步进行的云分层。如果文件被分层到 Azure 文件共享，则占用空间为零，因为该文件流存储在 Azure 文件共享中，而未存储在磁盘上。 另外，文件也可能会被部分分层（或部分召回）。 在部分分层文件中，该文件的一部分位于磁盘上。 应用程序（例如，多媒体播放器或压缩工具）对文件进行了部分读取时可能会发生此情况。 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>如何强制对文件或目录进行分层？
启用云分层功能后，它会根据上次访问和修改时间自动分层文件，以实现云终结点上指定的卷可用空间百分比， 但有时你可能需要手动强制分层文件。 如果你要保存在长时间内计划不再次使用的大型文件，并且想要将卷上现有可用空间用于其他文件和文件夹，则可使用此方法。 可使用以下 PowerShell 命令强制分层：

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件同步部署](storage-sync-files-planning.md)
