---
title: "有关 Azure 文件的常见问题解答 | Microsoft 文档"
description: "查看有关 Azure 文件的常见问题解答。"
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/08/2017
ms.author: renash
ms.openlocfilehash: cbe3c3a8f223a4bcc05e19b7a723cf5aff413527
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="frequently-asked-questions-about-azure-files"></a>有关 Azure 文件的常见问题解答

## <a name="general"></a>常规
* **什么是 Azure 文件？**  
    Azure 文件在云中提供可以通过行业标准的[服务器消息块 (SMB) 协议](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx)（也称为通用 Internet 文件系统，简称 CIFS）访问的完全托管文件共享。 Azure 文件共享可由云或者 Windows、Linux 和 macOS 的本地部署同时装载。

* **为何 Azure 文件很有用？**  
   借助 Azure 文件，无需应对物理服务器或设备的开销即可在云中创建文件共享。 这意味着，你可以在应用 OS 更新和替换损坏的磁盘方面花费更少的时间 - 我们会为你全部代劳这份枯燥的工作。 若要详细了解 Azure 文件适用的应用场景，请参阅[为何 Azure 文件很有用](storage-files-introduction.md#why-azure-files-is-useful)。

* **访问 Azure 文件中的文件有哪些不同方式？**  
    可以使用 SMB 3.0 协议将文件共享装载到本地计算机上，也可以使用[存储资源管理器](http://storageexplorer.com/)等工具访问文件共享中的文件。 在应用程序中，可以使用存储客户端库、REST API 或 Powershell 访问 Azure 文件共享中的文件。

* **什么是 Azure 文件同步？**  
    借助 Azure 文件同步（预览版），既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 它通过将 Windows Server 转换为 Azure 文件共享的快速缓存来实现这一点。 你可以使用 Windows Server 上的任意可用协议在本地访问数据（包括 SMB、NFS 和 FTPS），并且可以在世界各地获取所需的缓存数。

* **相对于 Azure Blob 存储，我为什么要对数据使用 Azure 文件共享？**  
    Azure 文件和 Azure Blob 存储均提供在云中存储大量数据的方法，但是用途略有不同。 Azure Blob 存储适用于需要存储非结构化数据且具有大规模缩放性的云本机应用程序。 为了更大程度地提升性能和可缩放性，相对于真实的文件系统而言，Azure Blob 存储是更简单的存储抽象。 此外，Azure Blob 存储只可通过基于 REST 的客户端库访问（或直接通过基于 REST 的协议访问）。

    而在另一方面，Azure 文件将专门致力于成为一个文件系统，让你可在多年所熟知和喜爱的本地操作系统中获取所有文件抽象。 与 Azure Blob 存储相同的是，Azure 文件提供 REST 接口和基于 REST 的客户端库，不同于 Azure Blob 存储的是，Azure 文件还提供了对 Azure 文件共享的 SMB 访问。 这意味着，无需对文件系统写入任何代码或附加任何特殊驱动程序，即可在 Windows、Linux 或 macOS 上及本地或云 VM 中直接装载 Azure 文件共享。 同时，还可以使用 Azure 文件同步将 Azure 文件共享缓存在本地文件服务器上，以加快访问速度（与在数据使用位置进行访问的速度相当）。 
   
    有关 Azure 文件和 Azure Blob 存储之间差异的深入介绍，请参阅[确定何时使用 Azure Blob 存储、Azure 文件或 Azure 磁盘](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 若要了解有关 Azure Blob 存储的详细信息，请参阅 [Blob 存储简介](../blobs/storage-blobs-introduction.md)。

* **相对于 Azure 磁盘，我为什么要使用 Azure 文件共享？**  
    Azure 磁盘只是一个磁盘。 一个独立的磁盘本身并不是非常有用 - 若要充分利用 Azure 磁盘，必须与 Azure 中运行的虚拟机关联。 Azure 磁盘适用于将在本地服务器上使用磁盘的所有内容：无论是 OS 系统磁盘、OS 的交换空间还是应用程序的专用存储。 Azure 磁盘其中一个有趣的用途是，可在云中创建一个文件服务器，以在可能使用 Azure 文件共享的相同位置使用。 当需要 Azure 文件当前不支持的部署选项（例如，NFS 协议支持或高级存储）时，在 Azure VM 中部署文件服务器则是一种非常好的获取 Azure 中文件存储的高性能方法。 

    另一方面，相比使用 Azure 文件共享，通过将 Azure 磁盘作为后端存储来运行文件服务器的方式，由于多方面的原因，其经济成本通常会更高。 首先，除了为磁盘存储付费之外，还必须为运行一个或多个 Azure VM 的成本付费。 其次，还必须要管理用于运行文件服务器的 VM，诸如负责进行 OS 升级等。最后，如果你最终需要本地缓存的数据，则还要自行安装和管理复制技术（例如，分布式文件系统复制）来实现此目的。

    同时充分利用 Azure 文件和托管在 Azure VM 中并将 Azure 磁盘作为后端存储的文件服务器，其中一种最佳的方法是：在云 VM 托管的文件服务器上安装 Azure 文件同步。 如果 Azure 文件共享与文件服务器位于同一个区域，则可启用云分层并将卷可用空间百分比设置为最大值 (99%)。 这可最大程度地减少重复数据，并允许在文件服务器上使用任何你所需要的应用程序，例如任何需要 NFS 协议支持的内容。

    有关在 Azure 中设置高性能和高可用性的方法的详细信息，请参阅[部署 Microsoft Azure 中的 IaaS VM 来宾群集](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/)。 有关 Azure 文件和 Azure 磁盘之间差异的深入介绍，请参阅[确定何时使用 Azure Blob 存储、Azure 文件或 Azure 磁盘](../common/storage-decide-blobs-files-disks.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 若要详细了解 Azure 磁盘，请参阅 [Azure 托管磁盘概述](../../virtual-machines/windows/managed-disks-overview.md)。

* **如何开始使用 Azure 文件？**  
    开始使用 Azure 文件非常简单：只需[创建文件共享](storage-how-to-create-file-share.md)，并在首选的操作系统中进行装载： 

    - [在 Windows 上装载](storage-how-to-use-files-windows.md)
    - [在 Linux 上装载](storage-how-to-use-files-linux.md)
    - [在 macOS 上装载](storage-how-to-use-files-mac.md)

    有关如何部署 Azure 文件共享以替换组织中的生产文件共享的详细指南，请参阅[规划 Azure 文件部署](storage-files-planning.md)。

* **Azure 文件支持哪些存储冗余选项？**  
    Azure 文件目前仅支持本地冗余存储 (LRS) 和异地冗余存储 (GRS)。 将来我们计划支持区域冗余存储空间 (ZRS) 和读取访问权限异地冗余存储 (RA-GRS)，但目前还没有可分享的日程表。

* **我非常希望可以将 x  功能添加到 Azure 文件，你们会添加它吗？**  
    当然会，Azure 文件团队非常乐于听取你针对我们的服务提供的任何反馈。 请在 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 上进行功能请求投票！ 希望我们提供的众多新功能能够满足你的需求。

## <a name="azure-file-sync"></a>Azure 文件同步
* **Azure 文件共享（预览版）目前支持哪些地区？**  
    Azure 文件同步目前适用于美国西部、西欧、澳大利亚东部和东南亚。 在进行公开发布的过程中，我们将添加对其他地区的支持。 有关更多信息，请参阅[地区可用性](storage-sync-files-planning.md#region-availability)。

* **是否可以在同一个同步组中同时包含已加入域的服务器和未加入域的服务器？**  
    可以，同步组可以包含具有不同的 Active Directory 成员身份的服务器终结点，其中包括未加入域的服务器。 虽然从严格意义上而言这种配置可用，但不建议将此作为常规配置，因为在某个服务器上为文件/文件夹定义的 ACL 可能无法由同步组中的其他服务器实施。 为获得最佳结果，建议在同一个 Active Directory 林中的服务器、已建立信任关系的不同 Active Directory 林中的服务器或未加入域的服务器之间进行同步，而不是在上述所有服务器的混合体之间进行同步。

* **我通过 SMB 或门户在 Azure 文件共享中直接创建了一个文件。多久后该文件才会同步到同步组中的服务器上？** 
    使用 Azure 门户、SMB 或 REST 在 Azure 文件共享中创建或修改文件时，最长可能需要 24 小时，可将更改同步到同步组中的服务器。

* **几乎在同一时间在两个服务器上对同一文件进行了更改后，会发生什么情况？**  
    Azure 文件同步会使用简单的冲突解决策略：同时保留两种更改。 最新的写入保留原始的文件名称。 较早的文件使用以下分类将“源”计算机和冲突编号追加到名称中： 
   
    `<FileNameWithoutExtension>-<MachineName>[-#].<ext>`  

    例如，如果 `CentralServer` 是较早的写入所在的位置，则 `CompanyReport.docx` 的第一个冲突将变为 `CompanyReport-CentralServer.docx`。 第二个冲突将被标识为 `CompanyReport-CentralServer-1.docx`。

* **注册服务器时，我收到了许多“网站不受信任”的响应，为什么？**  
    发生此错误的原因是在服务器注册过程中启用了增强 Internet Explorer 安全策略。 有关如何以正确的方式禁用增强 Internet Explorer 安全策略的详细信息，请参阅[准备 Windows Server 以使用 Azure 文件同步](storage-sync-files-deployment-guide.md#prepare-windows-servers-for-use-with-azure-file-sync)，以及[如何部署 Azure 文件同步（预览版）](storage-sync-files-deployment-guide.md)。

* **Azure 文件同步是否支持异地冗余存储 (GRS)？**  
    是的，Azure 文件同步同时支持本地冗余存储 (LRS) 和异地冗余存储 (GRS)。对于配对区域之间的 GRS 故障转移，建议仅将新区域视为数据的备份。 Azure 文件同步不会自动开始与新的主区域进行同步。 

* **使用 Azure 文件共享后，为什么文件的占用空间属性与大小属性不一致？**  
    Windows 文件资源管理器公开了两个属性来表示文件的大小，即大小和占用空间。 这两个属性的含义略有不同；大小表示文件的完整大小，而占用空间表示磁盘上实际存储的文件流的大小。 两者存在差异的原因有多种，例如压缩、使用重复数据删除，或本示例中使用 Azure 文件同步进行的云分层。如果文件被分层到 Azure 文件共享，则占用空间将为 0，因为该文件流存储在 Azure 文件共享中，而未存储在磁盘上。 文件也可能会被部分分层（或部分召回），这意味着该文件的一部分位于磁盘上。 应用程序（例如多媒体播放器或压缩工具）对文件进行了部分读取时可能会发生此情况。 

* **如何分辨文件是否已被分层？**  
    有多种查看文件是否已被分层到 Azure 文件共享的方法：
    
    1. **查看文件上的文件属性。** 若要执行此操作，请右键单击文件，导航到“详细信息”，然后向下滚动至“属性”属性。 分层文件将设置有以下属性：     
        
        | 属性字母 | 属性 | 定义 |
        |:----------------:|-----------|------------|
        | A | 存档 | 指示备份软件应备份此文件。 无论文件被分层还是完全存储在磁盘上，始终都会设置此属性。 |
        | P | 稀疏文件 | 指示文件是稀疏文件，这是 NTFS 提供的专用化文件类型，用于在文件的占用空间流几乎为空时提高使用效率。 Azure 文件同步将使用稀疏文件，因为 文件会被完全分层（表示它的文件流只存储在云中），或被部分召回（表示文件的一部分已在磁盘上）。 如果文件被完全召回到磁盘，Azure 文件同步会将其从稀疏文件转换为常规文件。 |
        | L | 重分析点 | 指示文件包含重分析点，这是供文件系统筛选器使用的特殊指针。 Azure 文件同步使用重分析点来定义到在云中将文件存储到的 Azure 文件同步文件系统筛选器 (StorageSync.sys)。 这样，最终用户甚至无需知道是否使用了 Azure 文件同步或如何在 Azure 文件共享中访问文件，即可进行无缝访问。 如果文件被完全召回，则 Azure 文件同步将从此文件中删除重分析点。 |
        | O | 脱机 | 指示文件的部分或全部内容未存储在磁盘上。 如果文件被完全召回，Azure 文件同步将删除此属性。 |

        ![选中“详细信息”选项卡后的文件“属性”对话框](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        另外，也可以通过向文件资源管理器的表显示添加“属性”字段来查看文件夹中所有文件的属性。 若要执行此操作，请右键单击现有的列（例如“大小”，选择“更多”，然后从下拉列表中选择“属性”。
        
    2. **使用 `fsutil` 查看文件上的重分析点** 如前面的选项所述，分层文件始终会设置重分析点（或用于 Azure 文件同步文件系统筛选器 (StorageSync.sys) 的特殊指针）。 你可以在提升的命令提示符或 PowerShell 会话上使用 `fsutil` 实用程序，查看文件是否包含重分析点：
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        如果文件包含重分析点，应看到“重分析标记值: 0x8000001e”。 该十六进制值是 Azure 文件同步拥有的重分析点值。输出还将包含表示 Azure 文件共享中文件路径的重分析数据。

        > [!Warning]  
        > 同时，`fsutil reparsepoint` 实用程序命令还包含删除重分析点的功能。 只有在 Azure 文件同步工程团队指示你执行此命令时，才可执行此命令 - 执行此命令可能会导致数据丢失。 

* **我想要使用的一个文件已被分层，如何将它召回到磁盘以在本地使用它？**  
    将文件召回到磁盘的最简单方法是打开此文件。 Azure 文件同步文件系统筛选器 (StorageSync.sys) 将会从 Azure 文件共享中无缝下载此文件，你无需执行任何操作。 对于可被部分读取的文件类型，例如多媒体或 ZIP 文件，打开文件后不会下载整个文件。

    此外，也可以使用 PowerShell 强制召回文件。 例如，当你想要同时召回多个文件（例如，一个文件夹内的所有文件）时，可使用此方法。 打开已安装 Azure 文件同步的服务器节点的 PowerShell 会话，然后运行以下 PowerShell 命令：
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

* **如何强制将文件或目录分层？**  
    启用云分层功能后，它会根据上次访问和修改时间自动将文件分层，以实现云终结点上指定的卷可用空间百分比，但有时你可能需要手动强制将文件分层。 例如，如果你要保存在长时间内计划不再次使用的大型文件，并且想要将卷上现有可用空间用于其他文件/文件夹，则可使用此方法。 可使用以下 PowerShell 命令强制分层：

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

* **某个文件或文件夹被 Azure 文件同步排除，为什么？**
    默认情况下，Azure 文件同步会排除以下文件：
    
    - desktop.ini
    - thumbs.db
    - ehthumbs.db
    - ~$\*.\*
    - \*.laccdb
    - \*.tmp
    - 635D02A9D91C401B97884B82B3BCDAEA.\*

    默认情况下，还会排除以下文件夹：

    - \系统卷信息
    - \$RECYCLE.BIN
    - \SyncShareState

* **我想要将 Azure 文件同步用于 Windows Server 2008 R2、Linux 或 NAS 设备 - Azure 文件同步是否支持它们？**
    Azure 文件同步当前仅支持 Windows Server 2016 和 Windows Server 2012 R2。 目前，我们暂无可分享的其他计划，但我们愿意根据客户需求支持其他平台。 请在 [Azure 文件 UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) 上告诉我们你想要我们支持的平台。

## <a name="security-authentication-and-access-control"></a>安全性、身份验证和访问控制
* **Azure 文件是否支持基于 Active Directory 的身份验证和访问控制？**  
    Azure 文件目前不支持基于 AD 的身份验证或 ACL，不过，我们正在积极解决此问题。 目前，有两种解决缺失基于 AD 的身份验证和访问控制的方法：

    - 使用 SAS，可以生成在指定时间间隔内有效的特定权限令牌。 例如，可以生成在 10 分钟后到期的令牌，其中包含对给定文件的只读访问权限。 只要拥有有效的此令牌，就可以在 10 分钟内拥有对给定文件的只读访问权限。 目前，只能通过 REST API 或客户端库支持 SAS 密钥，你必须使用存储帐户密钥通过 SMB 装载 Azure 文件共享。

    - Azure 文件同步将保留所有的 ASL（基于 AD 或本地的），并将它们复制到它要同步到的所有服务器终结点。 由于 Windows 服务器已可以使用 Active Directory 进行身份验证，因此，在提供对基于 AD 的身份验证的完全支持和 ACL 支持前，Azure 文件同步可以提供很好的临时措施。

* **如何确保已静态加密 Azure文件共享？**  
    无需执行任何操作来启用加密，目前所有公用区域和各国云端的 Azure 文件均默认启用[服务器端加密](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)。 

* **如何使用 Web 浏览器提供对特定文件的访问权限？**  
    使用 SAS，可以生成在指定时间间隔内有效的特定权限令牌。 例如，可以生成一个令牌，该令牌只能在特定时段内以只读方式访问特定文件。 只要拥有有效的此 URL，就可以直接通过任意 Web 浏览器访问文件。 SAS 密钥可以轻松地从 UI（例如存储资源管理器）生成。

* **是否可以对共享中的文件夹指定只读或只写权限？**  
    如果通过 SMB 装载文件共享，不具有此级别的权限控制。 但是，可以通过 REST API 或客户端库创建共享访问签名 (SAS) 来实现此控制。
    
* **什么是 Azure 文件支持的数据符合性策略？**  
   Azure 文件所依据的存储体系结构与 Azure 存储中的其他存储服务相同，并且实施的数据符合性策略也相同。 有关 Azure 存储数据符合性的详细信息，可以下载并参阅 [Microsoft Azure 数据保护文档](http://go.microsoft.com/fwlink/?LinkID=398382&clcid=0x409)。

## <a name="on-premises-access"></a>本地访问
* **必须使用 Azure ExpressRoute 才能在本地连接到 Azure 文件或使用 Azure 文件同步吗？**  
    否，ExpressRoute 不是访问 Azure 文件共享的必要条件。 如果要直接在本地装载 Azure 文件共享，你只需打开端口 445（TCP 出站）以进行 Internet 访问（这是 SMB 用于进行通信的端口）。 如果要使用 Azure 文件同步，则只需端口 443（TCP 出站）以进行 HTTPS 访问（无需 SMB）。 但是，如果需要，则可使用 ExpressRoute 执行任意一种访问选项。

* **如何才能在本地计算机上装载 Azure 文件共享？**  
    可以通过 SMB 协议装载文件共享，只要端口 445（TCP 出站）处于打开状态，且客户端支持 SMB 3.0 协议（例如，使用的是 Windows 10 或 Windows Server 2016）。 如果端口 445 被组织的策略或 ISP 阻止，则可使用 Azure 文件同步访问 Azure 文件共享。

## <a name="backup"></a>备份
* **如何备份我的 Azure 文件共享？**  
    可以使用定期共享快照（预览版）来防止意外删除。 可以使用 AzCopy、RoboCopy 或能够备份已装载文件共享的第三方备份工具。

## <a name="share-snapshots"></a>共享快照
### <a name="share-snapshots---general"></a>共享快照 - 常规问题
* **什么是文件共享快照？**
    借助 Azure 文件共享快照，你可以创建只读版本的文件共享。 借助它，你还可以将内容的早期版本复制回 Azure 或本地中的同一个共享或备用位置中，以做进一步修改。 若要了解有关共享快照的详细信息，请参阅[共享快照概述](storage-snapshots-files.md)。

* **共享快照存储在何处？**
    共享快照与文件共享存储在同一个存储帐户中。

* **是否有任何性能影响？**
    共享快照没有任何性能开销。

* **共享快照是否与应用程序一致？**
    否。 共享快照与应用程序不一致。 执行共享快照前，用户必须将应用程序中的写入刷新到共享中。

* **对共享快照数有限制吗？**
    Azure 文件可以保留的共享快照上限为 200 个。 共享快照不计入共享配额，因此，对所有共享快照使用的总空间没有单独的共享限制。 存储帐户限制仍然适用。 在 200 个共享快照之后，必须删除旧的共享快照，以便创建新的共享快照。

### <a name="create-share-snapshots"></a>创建共享快照
* **是否可以创建单个文件的共享快照？**
    可在文件共享级别上创建共享快照。 你可以从文件共享快照还原单个文件，但是不能创建文件级别的共享快照。 但是，如果你已执行共享级别共享快照，并且想要列出已更改特定文件的共享快照，在可以从 Windows 已装载共享上的“以前的版本”体验执行此操作。

* **是否可以创建加密文件共享的共享快照？**
    可以对已启用静态加密的 Azure 文件共享执行共享快照。 可以从共享快照中将文件还原到加密文件共享中。 如果共享已加密，则共享快照也将加密。

* **共享快照具有异地冗余吗？**
    共享快照与它们适用的 Azure 文件共享具有相同的冗余。 如果已为帐户选择异地冗余存储 (GRS)，则共享快照也将在配对区域中进行冗余存储。

### <a name="manage-share-snapshots"></a>管理共享快照
* **是否可以在 Linux 中浏览共享快照？**
    可以使用 Azure CLI 2.0 在 Linux 上创建、列出、浏览和还原共享快照。

* **是否可以将共享快照复制到不同的存储帐户？**
    可以将文件从共享快照复制到其他位置，但不能复制到共享快照本身中。

### <a name="restore-data-from-share-snapshots"></a>从共享快照还原数据
* **是否可以将共享快照提升到基本共享中？**
    只能将数据从共享快照复制到任何所需的目标位置。 但不能将共享快照提升到基本共享中。

* **是否可以将数据从共享快照还原到不同的存储帐户？**
    是的。 可以将共享快照中的文件复制到原始位置或备用位置，其中包括位于同一区域或不同区域的相同/不同的存储帐户。 还可以将文件复制到本地或任何其他云。    
  
### <a name="cleanup-share-snapshot"></a>清除共享快照
* **是否可以删除共享，而不删除共享快照？**
    如果共享上有活动的共享快照，则无法删除此共享。 有一个 API，可用于同时删除共享快照和共享，也可通过 Azure 门户实现同一目的。

* **删除存储帐户后，共享快照会怎样？**
    删除存储帐户后，共享快照也将被删除。

## <a name="billing-and-pricing"></a>计费和定价
* **Azure VM 与文件共享之间的网络流量是否作为外部带宽计入订阅费用？**  
    如果文件共享和 VM 位于同一 Azure 区域，它们之间的流量是免费的。 如果文件共享和 VM 位于不同的区域，它们之间的流量作为外部带宽收费。

* **共享快照的费用是多少？**
     在预览版期间，共享快照容量可免费使用。 仍将收取标准存储出口和事务费用。 公开发布后，共享快照的容量和事务均将收费。
     
     共享快照在本质上是递增的。 基本共享快照即是共享本身。 所有的后续共享快照均是递增的，并且只会存储与之前共享快照的不同之处。 你只需为更改的内容付费。 如果你的共享包含 100 GB 的数据，但在执行上次共享快照后只更改了 5 GB 数据，则共享快照将只使用其他 5 GB 数据，你只需为 105 GB 付费。 请参阅[定价页](https://azure.microsoft.com/pricing/details/storage/files/)了解有关事务和标准出口费用的详细信息。

## <a name="scale-and-performance"></a>缩放和性能
* **Azure 文件存在哪些缩放限制？**  
    若要了解 Azure 文件的可伸缩性和性能目标，请参阅 [Azure 存储可伸缩性和性能目标](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#scalability-targets-for-blobs-queues-tables-and-files)。

* **多少个客户端可以同时访问同一文件？**  
    单个文件有 2000 个打开句柄配额。 一旦有 2000 个打开句柄，就会收到错误，指示已达到配额。

* **尝试将文件解压缩到 Azure 文件中时性能降低。我该怎样做？**  
    若要将大量文件传输到 Azure 文件，建议使用 AzCopy（Windows 版、Linux/Unix 预览版）或 Azure Powershell，因为这些工具已针对网络传输进行优化。

* **我在 Windows Server 2012 R2 或 Windows 8.1 上装载了 Azure 文件共享，但性能不佳，为什么？**  
    在 Windows Server 2012 R2 和 Windows 8.1 上装载 Azure 文件共享后，有一个已知问题，2014 年 4 月的 Windows 8.1 和 Windows Server 2012 R2 累积更新已修补了此问题。 请确保 Windows Server 2012 R2 和 Windows 8.1 的所有实例均应用了此修补程序，以获得最佳性能（当然，我们始终建议你通过 Windows 更新获取所有的 Windows 修补程序）。 有关详细信息，请查看相关的知识库文章：[Slow performance when you access Azure Files from Windows 8.1 or Server 2012 R2](https://support.microsoft.com/kb/3114025)（从 Windows 8.1 或 Server 2012 R2 访问 Azure 文件时性能降低）。

## <a name="features-and-interoperability-with-other-services"></a>功能以及与其他服务的互操作性
* **故障转移群集的“文件共享见证”是 Azure 文件的用例之一吗？**  
    Azure 文件共享目前不支持此服务。 有关如何为 Azure Blob 存储设置此服务的详细信息，请参阅[部署故障转移群集的云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。

* **REST API 中是否有重命名操作？**  
    现在不行。

* **能否使用嵌套共享，换而言之就是共享下的共享？**  
    否。 文件共享是可以装载的虚拟驱动程序，因此不支持嵌套共享。

* **将 Azure 文件与 IBM MQ 配合使用**  
    IBM 已发布相关文档，指导 IBM MQ 客户通过其服务配置 Azure 文件。 有关详细信息，请查阅 [如何通过 Microsoft Azure 文件服务设置 IBM MQ 多实例队列管理器](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service)。

## <a name="see-also"></a>另请参阅
* [在 Windows 中排查 Azure 文件问题](storage-troubleshoot-windows-file-connection-problems.md)
* [在 Linux 中排查 Azure 文件问题](storage-troubleshoot-linux-file-connection-problems.md)
* [对 Azure 文件同步（预览版）进行故障排除](storage-sync-files-troubleshoot.md)
