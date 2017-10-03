---
title: "规划 Azure 文件同步（预览版）部署 | Microsoft Docs"
description: "了解规划 Azure 文件部署时应考虑的问题。"
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: wgries
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 3c003d498600a2cfd12ef2adfb7c16f9dfaddb37
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>规划 Azure 文件同步（预览版）部署
可使用 Azure 文件同步（预览版）将共享复制到本地或 Azure 中的 Windows Server。 然后，你和你用户即可通过 SMB 或 NFS 共享等 Windows Server 访问文件共享。 这尤其适用于要在远离 Azure 数据中心的位置访问和修改数据的方案，例如分支机构方案。 可在多个 Windows Server 终结点（例如多个分支机构）之间复制数据。 

本指南介绍部署 Azure 文件同步时需考虑的问题。建议阅读[规划 Azure 文件部署](storage-files-planning.md)指南测试。 

## <a name="understanding-azure-file-sync-terminology"></a>了解 Azure 文件同步术语
在了解 Azure 文件同步详细信息之前，请务必先了解术语。

### <a name="storage-sync-service"></a>存储同步服务
存储同步服务是表示 Azure 文件同步的顶级 Azure 资源。存储同步服务资源是存储帐户资源的对等物，可按类似方式部署到 Azure 资源组。 由于存储同步服务可通过多个同步组创建与多个存储帐户的同步关系，因此需要从存储帐户资源中获得一个不同的顶级资源。 一个订阅可部署有多个存储同步服务资源。

### <a name="sync-group"></a>同步组
同步组定义一组文件的同步拓扑。 同步组中的终结点保持彼此同步。 例如，如果想使用 AFS 管理两组不同文件，需创建两个同步组并向其添加不同的终结点。 存储同步服务可承载任意数量的同步组。  

### <a name="registered-server"></a>已注册服务器
已注册服务器对象表示服务器（或群集）与存储同步服务之间的信任关系。 可在存储同步服务实例中随意注册任意数量的服务器。 但是，服务器（或群集）在任意给定时间内仅可注册有一个存储同步服务。

### <a name="azure-file-sync-agent"></a>Azure 文件同步代理
Azure 文件同步代理是一个可下载包，可实现 Windows 服务器与 Azure 文件共享的同步。 Azure 文件同步代理包含 3 个主要组件： 
- **FileSyncSvc.exe**：背景 Windows 服务，它负责监视服务器端点的更改并启动到 Azure 的同步会话。
- **StorageSync.sys**：Azure 文件同步系统筛选器，它负责将文件分层存入 Azure 文件（若云分层已启用）。
- **PowerShell 管理 cmdlet**：PowerShell cmdlet，用于与 Microsoft.StorageSync Azure 资源提供程序进行交互。 可以下位置找到这些文件（默认情况下）：
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="cloud-endpoint"></a>云终结点
云终结点是一个 Azure 文件共享，它属于同步组。 整个 Azure 文件共享同步和 Azure 文件共享只能属于一个云终结点，因此属于一个同步组。 如果将带一组现有文件的 Azure 文件共享作为云终结点添加到同步组中，则这些文件将与同步组中其他终结点上已有的任何其他文件进行合并。

### <a name="server-endpoint"></a>服务器终结点
服务器终结点表示已注册服务器上的特定位置，例如服务器卷中的文件夹或服务器卷的根。 如果命名空间不重叠（例如 F:\sync1 和 F:\sync2），则多个服务器终结点可位于同一卷中。 可为每个服务器终结点单独配置云分层策略。 如果将带一组现有文件的服务器位置作为服务器终结点添加到同步组，则这些文件将与同步组中其他终结点上已有的任何其他文件进行合并。

### <a name="cloud-tiering"></a>云分层 
云分层是 Azure 文件同步的一项可选功能，可用于将很少使用或访问文件分层到 Azure 文件。 当文件分层时，Azure 文件同步文件系统筛选器 (storagesync . sys) 将本地文件替换为指针或重分析点（后者表示 Azure 文件中的文件 URL）。 分层文件在 NTFS 中设置了“脱机”属性，因此第三方应用程序可识别分层文件。 当用户打开分层文件时，Azure 文件同步会从 Azure 文件中无缝调用此文件数据，而用户无需知道文件未存储在本地系统上。 此功能也称为分层存储管理 (HSM)。

## <a name="azure-file-sync-interoperability"></a>Azure 文件同步互操作性 
本部分介绍 Azure 文件同步与 Windows Server 功能和角色以及第三方解决方案的互操作性。

### <a name="supported-versions-of-windows-server"></a>支持的 Windows Server 版本
目前，Azure 文件同步支持的 Windows Server 版本为：

| 版本 | 支持的 SKU | 支持的部署选项 |
|---------|----------------|------------------------------|
| Windows Server 2016 | 数据中心和标准版 | 完全（带 UI 的服务器） |
| Windows Server 2012 R2 | 数据中心和标准版 | 完全（带 UI 的服务器） |

Windows Server 的未来版本将在发布时添加，Windows 的旧版本可根据用户的反馈进行添加。

> [!Important]  
> 建议将搭配使用 Azure 文件同步的所有 Windows Server 保持为实时跟进 Windows 更新的最新更新。 

### <a name="file-system-features"></a>文件系统功能
| 功能 | 支持状态 | 说明 |
|---------|----------------|-------|
| 访问控制列表 (ACL) | 完全支持 | Windows ACL 由 Azure 文件同步进行保留，并由 Windows Server 在服务器终结点上强制实施。 如果直接在云中访问文件，则 Azure 文件不（尚不）支持 Windows ACL。 |
| 硬链接 | 已跳过 | |
| 符号链接 | 已跳过 | |
| 装入点 | 部分支持 | 装入点可能是服务器终结点的根，但如果包含在服务器终结点的命名空间内，则跳过此装入点。 |
| 交接点 | 已跳过 | |
| 重分析点 | 已跳过 | |
| NTFS 压缩 | 完全支持 | |
| 稀疏文件 | 完全支持 | 稀疏文件会进行同步（不受阻），但作为完整文件同步到云。 如果在云端（或在其他服务器上）更改文件内容，则下载更改时，文件不再是稀疏文件 |
| 备用数据流 (ADS) | 保留但不同步 | |

> [!Note]  
> 仅支持 NTFS 卷。

### <a name="failover-clustering"></a>故障转移群集
Windows Server 故障转移群集受 Azure 文件同步支持，用于“一般用途文件服务器”部署选项。 不可在“适用于应用程序数据的横向扩展文件服务器”(SOFS) 或群集共享卷 (CSV) 上使用故障转移群集。

> [!Note]  
> 必须在故障转移集群中的每个节点上安装 Azure 文件同步代理，才能正常进行同步。

### <a name="windows-server-data-deduplication"></a>Windows Server 重复数据删除
对于未启用云分层的卷，Azure 文件同步支持在卷上启用重复数据删除。 目前不可在启用云分层的 Azure 文件同步和重复数据删除之间进行互操作。

### <a name="anti-virus-solutions"></a>防病毒解决方案
由于防病毒通过扫描文件中的已知恶意代码进行工作，因此可能导致调用分层文件。 由于分层文件设置有“脱机”属性，因此建议咨询软件供应商，了解如何配置解决方案以跳过读取脱机文件。 

以下解决方案现支持跳过脱机文件：

- [Symantec 终结点保护](https://support.symantec.com/en_US/article.tech173752.html)
- [McAfee 终结点安全](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf)（请参阅第 90 页的“仅扫描所需内容”部分）
- [Kaspersky 防病毒](https://support.kaspersky.com/4684)
- [Sophos 终结点保护](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>备份解决方案
与防病毒解决方案一样，备份解决方案可能导致调用分层文件。 建议使用云备份解决方案来备份 Azure文件共享，而不是使用本地备份产品。

### <a name="encryption-solutions"></a>加密解决方案
是否支持加密解决方案取决于其实现方式。 Azure 文件同步现支持：

- BitLocker 加密
- Azure RMS （和旧版 Active Directory RMS）

Azure 文件同步现不支持：

- NTFS 加密文件系统 (EFS)

通常，Azure 文件同步应该能与文件系统下的加密解决方案（如 BitLocker）以及在文件格式中实现的解决方案（如 BitLocker）进行互操作，但不与文件系统上的解决方案（如NTFS 加密文件系统）进行特殊的互操作。

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>其他分层存储管理 (HSM) 解决方案
其他分层存储管理解决方案均无法使用 Azure 文件同步。

## <a name="region-availability"></a>上市区域
Azure 文件同步仅在以下区域提供预览版：

| 区域 | 数据中心位置 |
|--------|---------------------|
| 美国西部 | 美国加利福尼亚 |
| 欧洲西部 | 荷兰 |
| 东南亚 | 新加坡 |
| 澳大利亚东部 | 澳大利亚新南威尔士 |

在预览版中，仅支持与存储同步服务所在区域中的 Azure 文件共享进行同步。

## <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
将定期发布 Azure 文件同步代理更新，用来添加新功能和解决任何已知问题。 建议在我们发布 Azure 文件同步代理更新时启用 Microsoft 更新来获得所有更新。 也就是说，我们明白某些组织希望严格控制更新。 对于使用旧版 Azure 文件同步代理进行的部署：

- 存储同步服务将在新的主版本发布后的 3 个月内沿用以前的主版本。 例如，在发布版本 2.\* 后 3 个月内，存储同步服务仍支持版本 1.\*。
- 3 个月后，存储同步服务将开始阻止使用过期版本的已注册服务器与同步组进行同步。
- 对于以前的主版本，在此 3 个月内，所有 bug 修复程序都仅针对当前的主版本。

## <a name="next-steps"></a>后续步骤
* [规划 Azure 文件部署](storage-files-planning.md)
* [部署 Azure 文件](storage-files-deployment-guide.md)
* [部署 Azure 文件同步](storage-sync-files-deployment-guide.md)

