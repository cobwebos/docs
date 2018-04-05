---
title: 规划 Azure 文件同步（预览版）部署 | Microsoft Docs
description: 了解规划 Azure 文件部署时应考虑的问题。
services: storage
documentationcenter: ''
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2017
ms.author: wgries
ms.openlocfilehash: 401542bf61aa27138d26cce522e24078503b77e0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2018
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>规划 Azure 文件同步（预览版）部署
使用 Azure 文件同步（预览版），既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 Azure 文件同步可将 Windows Server 转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上可用的任意协议本地访问数据，包括 SMB、NFS 和 FTPS。 并且可以根据需要在世界各地具有多个缓存。

本指南介绍有关 Azure 文件同步部署的重要注意事项。 我们建议另外阅读[规划 Azure 文件部署](storage-files-planning.md)。 

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
服务器终结点代表已注册服务器上的特定位置，例如服务器卷中的文件夹。 如果命名空间不重叠（例如 `F:\sync1` 和 `F:\sync2`），多个服务器终结点可存在于同一个卷。 可为每个服务器终结点单独配置云分层策略。 当前不可为卷的根目录（例如 `F:\` 或 `C:\myvolume`，如果将卷作为装入点装入）创建服务器终结点。

> [!Note]  
> 仅支持不可删除的卷。  服务器终结点路径不支持通过远程共享映射的驱动器。  此外，服务器终结点可能位于 Windows 系统卷，然而系统卷上不支持云分层。

如果将带一组现有文件的服务器位置作为服务器终结点添加到同步组，则这些文件将与同步组中其他终结点上已有的任何其他文件进行合并。

### <a name="cloud-endpoint"></a>云终结点
云终结点是一个 Azure 文件共享，它属于同步组。 整个 Azure 文件共享同步和 Azure 文件共享只能属于一个云终结点。 因此，Azure 文件共享只能是一个同步组的成员。 如果将带一组现有文件的 Azure 文件共享作为云终结点添加到同步组中，则现有文件将与同步组中其他终结点上已有的任何其他文件进行合并。

> [!Important]  
> Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时仅针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。有关详细信息，请参阅 [Azure 文件常见问题解答](storage-files-faq.md#afs-change-detection)。

### <a name="cloud-tiering"></a>云分层 
云分层是 Azure 文件同步的一项可选功能，可用于将很少使用或访问的文件（大于 64 KiB）分层到 Azure 文件。 当文件分层时，Azure 文件同步文件系统筛选器 (StorageSync.sys) 将本地文件替换为指针或重分析点。 重分析点表示 Azure 文件中的文件 URL。 分层文件在 NTFS 中设置了“脱机”属性，因此第三方应用程序可识别分层文件。 当用户打开分层文件时，Azure 文件同步会从 Azure 文件中无缝调用此文件数据，而用户无需知道文件未存储在本地系统上。 此功能也称为分层存储管理 (HSM)。

> [!Important]  
> Windows 系统卷上不支持对服务器终结点进行云分层。

## <a name="azure-file-sync-interoperability"></a>Azure 文件同步互操作性 
本部分介绍 Azure 文件同步与 Windows Server 功能和角色以及第三方解决方案的互操作性。

### <a name="supported-versions-of-windows-server"></a>支持的 Windows Server 版本
目前，Azure 文件同步支持的 Windows Server 版本为：

| 版本 | 支持的 SKU | 支持的部署选项 |
|---------|----------------|------------------------------|
| Windows Server 2016 | 数据中心和标准版 | 完全（带 UI 的服务器） |
| Windows Server 2012 R2 | 数据中心和标准版 | 完全（带 UI 的服务器） |

将来的 Windows Server 版本将在发布后添加。 Windows 旧版本可能根据用户的反馈添加。

> [!Important]  
> 我们建议使用 Windows 更新提供的最新更新，将用于 Azure 文件同步的所有服务器保持最新。 

### <a name="file-system-features"></a>文件系统功能
| 功能 | 支持状态 | 说明 |
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

### <a name="failover-clustering"></a>故障转移群集
Windows Server 故障转移群集受 Azure 文件同步支持，用于“一般用途文件服务器”部署选项。 不可在“适用于应用程序数据的横向扩展文件服务器”(SOFS) 或群集共享卷 (CSV) 上使用故障转移群集。

> [!Note]  
> 必须在故障转移群集中的每个节点上安装 Azure 文件同步代理，才能正常进行同步。

### <a name="data-deduplication"></a>重复数据删除
对于未启用云分层的卷，Azure 文件同步支持在卷上启用 Windows Server 重复数据删除。 目前不可在启用云分层的 Azure 文件同步和重复数据删除之间进行互操作。

### <a name="distributed-file-system-dfs"></a>分布式文件系统 (DFS)
从 [Azure 文件同步代理 1.2](https://go.microsoft.com/fwlink/?linkid=864522) 开始，Azure 文件同步就支持与 DFS 命名空间 (DFS-N) 和 DFS 复制 (DFS-R) 进行互操作。

**DFS 命名空间 (DFS-N)**：Azure 文件同步在 DFS-N 服务器上完全受支持。 可以在一个或多个 DFS-N 成员上安装 Azure 文件同步代理，以在服务器终结点与云终结点之间同步数据。 有关详细信息，请参阅 [DFS 命名空间概述](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview)。
 
**DFS 复制 (DFS-R)**：因为 DFS-R 和 Azure 文件同步都是复制解决方案，所以在大多数情况下建议将 DFS-R 替换为 Azure 文件同步。不过在以下几个方案中，可能需要同时使用 DFS-R 和 Azure 文件同步：

- 从 DFS-R 部署迁移至 Azure 文件同步部署。 有关详细信息，请参阅[将 DFS 复制 (DFS-R) 部署迁移至 Azure 文件同步](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)。
- 并非需要文件数据副本的每个本地服务器都可以直接连接至 Internet。
- 分支服务器将数据合并至单个中心服务器，你希望在该服务器中使用 Azure 文件同步。

对于 Azure 文件同步和 DFS-R 并行工作的情况：

1. 必须在包含 DFS-R 复制文件夹的卷上禁用 Azure 文件同步云分层。
2. 不应在 DFS-R 只读复制文件夹上配置服务器终结点。

有关详细信息，请参阅 [DFS 复制概述](https://technet.microsoft.com/library/jj127250)。

### <a name="antivirus-solutions"></a>防病毒解决方案
由于防病毒通过扫描文件中的已知恶意代码进行工作，因此防病毒产品可能导致重新调用分层文件。 由于分层文件设置有“脱机”属性，因此建议咨询软件供应商，了解如何配置解决方案以跳过读取脱机文件。 

以下解决方案现支持跳过脱机文件：

- [Symantec 终结点保护](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee 终结点安全性](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf)（请参阅 PDF 第 90 页上的“仅扫描所需内容”）
- [Kaspersky 防病毒](https://support.kaspersky.com/4684)
- [Sophos 终结点保护](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>备份解决方案
与防病毒解决方案一样，备份解决方案可能导致重新调用分层文件。 建议使用云备份解决方案来备份 Azure文件共享，而不是使用本地备份产品。

### <a name="encryption-solutions"></a>加密解决方案
是否支持加密解决方案取决于其实现方式。 Azure 文件同步现支持：

- BitLocker 加密
- Azure 信息保护、Azure Rights Management Services (Azure RMS) 以及 Active Directory RMS

Azure 文件同步现不支持：

- NTFS 加密文件系统 (EFS)

一般情况下，Azure 文件同步应该支持与文件系统下的加密解决方案（如 BitLocker）以及在文件格式中实现的解决方案（如 BitLocker）进行互操作。 不与文件系统上的解决方案（如 NTFS EFS）进行特殊的互操作。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他分层存储管理 (HSM) 解决方案
其他 HSM 解决方案均无法使用 Azure 文件同步。

## <a name="region-availability"></a>上市区域
Azure 文件同步仅在以下区域提供预览版：

| 区域 | 数据中心位置 |
|--------|---------------------|
| 澳大利亚东部 | 新南威尔士州 |
| 加拿大中部 | 多伦多 |
| 美国中部 | 爱荷华州 |
| 东亚 | 香港特别行政区 |
| 美国东部 | 弗吉尼亚州 |
| 美国东部 2 | 弗吉尼亚州 |
| 北欧 | 爱尔兰 |
| 东南亚 | 新加坡 |
| 英国南部 | 伦敦 |
| 美国中西部 |
| 欧洲西部 | 荷兰 |
| 美国西部 | California |

在预览版中，仅支持与存储同步服务所在区域中的 Azure 文件共享进行同步。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>后续步骤
* [考虑防火墙和代理设置](storage-sync-files-firewall-and-proxy.md)
* [规划 Azure 文件部署](storage-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)
