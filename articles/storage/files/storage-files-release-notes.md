---
title: Azure 文件同步代理发行说明 | Microsoft Docs
description: Azure 文件同步代理发行说明。
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/16/2020
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 765decb8c65254d63ef5267cbc496d2320f58f6e
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991920"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 文件同步代理发行说明
借助 Azure 文件同步，既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 Windows Server 安装可转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上提供的任意协议（包括 SMB、NFS 和 FTPS）以本地方式访问数据， 并且可以根据需要在世界各地设置多个缓存。

本文提供受支持的 Azure 文件同步代理版本的发行说明。

## <a name="supported-versions"></a>支持的版本
以下版本是 Azure 文件同步代理支持的：

| 里程碑 | 代理版本号 | 发布日期 | 状态 |
|----|----------------------|--------------|------------------|
| V10 版本 - [KB4522359](https://support.microsoft.com/en-us/help/4522409/azure-file-sync-agent-v10-release-march-2020)| 10.0.0.0 | 2020年4月9日 | 支持 |
| 2019 年 12 月更新汇总 - [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 2019 年 12 月 12 日 | 支持 |
| V9 版本 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2019 年 12 月 2 日 | 支持 |
| V8 版本 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 2019 年 10 月 8 日 | 支持 |
| 2019 年 7 月更新汇总 - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019 年 7 月 24 日 | 支持 |
| 2019 年 7 月更新汇总 - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 2019 年 7 月 12 日 | 支持 |
| V7 版本 - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 2019年6月19日 | 支持 |
| 2019 年 6 月更新汇总 - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 六月 27， 2019 | 支持 - 代理版本将于 2020 年 4 月 21 日过期 |
| 2019 年 6 月更新汇总 - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 2019 年 6 月 13 日 | 支持 - 代理版本将于 2020 年 4 月 21 日过期 |
| 2019 年 5 月更新汇总 - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 2019 年 5 月 7 日 | 支持 - 代理版本将于 2020 年 4 月 21 日过期 |
| V6 版本 - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 2019年4月21日 | 支持 - 代理版本将于 2020 年 4 月 21 日过期 |
| V5 版本 | 5.0.2.0 - 5.2.0.0 | 不可用 | 不支持 - 代理版本于 2020 年 3 月 18 日过期 |
| V4 版本 | 4.0.1.0 - 4.3.0.0 | 不可用 | 不支持 - 代理版本于 2019 年 11 月 6 日过期 |
| V3 版本 | 3.1.0.0 - 3.4.0.0 | 不可用 | 不支持 - 代理版本于 2019 年 8 月 19 日过期 |
| GA 前代理 | 1.1.0.0 - 3.0.13.0 | 不可用 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-10000"></a>代理版本 10.0.0.0.0
以下发行说明适用于 Azure 文件同步代理的版本 10.0.0.0.0（2020 年 4 月 9 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 改进门户中的同步进度
    - 使用 V10 代理版本，Azure 门户将很快开始显示正在运行的同步会话的类型。 例如 初始下载、定期下载、后台回忆（快速灾难恢复案例）等。 

- 改进的云分层门户体验
    - 如果文件无法分层或调用，现在可以查看服务器终结点属性中的分层错误。
    - 服务器终结点提供其他云分层信息：
        - 本地缓存大小
        - 缓存使用效率
        - 云分层策略详细信息：卷大小、当前可用空间或本地缓存中最旧文件的最后一次访问时间。
    - 这些更改将在初始 V10 代理版本后不久在 Azure 门户中提供。

- 支持将存储同步服务和/或存储帐户移动到其他 Azure 活动目录 （AAD） 租户
    - Azure 文件同步现在支持将存储同步服务和/或存储帐户移动到其他资源组、订阅或 Azure AD 租户。
    
- 评估工具现在标识以句点结尾的文件或目录
    - [评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)已更新，以标识以句点结尾的文件或目录。 Azure 文件同步当前不支持以句点结尾的文件或目录。要使用[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)的更新版本，请安装[Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)的最新版本。
 
- 各种性能和可靠性改进
    - 如果在存储帐户上配置了虚拟网络 （VNET） 和防火墙规则，则 Azure 文件共享上的更改检测可能会失败。
    - 不再对同步可自由访问控制列表 （DACL） 的 2KB 安全描述符限制。  
    - 减少与召回相关的内存消耗。 
    - 使用[调用-Azstorage 同步更改检测](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)cmdlet 时提高了性能。
    - 其他杂项可靠性改进。 
    
### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 纳米服务器部署选项不支持代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，并可能导致意外的结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 要立即同步 Azure 文件共享中更改的文件，可以使用[Invoke-AzStorageSyncChange 检测](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)电源 Shell cmdlet 手动启动检测 Azure 文件共享中的更改。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > 创建云终结点时，存储同步服务和存储帐户必须位于同一 Azure AD 租户中。 创建云终结点后，存储同步服务和存储帐户可以移动到不同的 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。

## <a name="agent-version-9100"></a>代理版本 9.1.0.0
以下发行说明适用于 2019 年 12 月 12 日发布的 Azure 文件同步代理版本 9.1.0.0。 这些注释是版本 9.0.0.0 列出的发行说明的补充。

此版本中修复的问题：  
- 升级到 Azure 文件同步代理版本 9.0 后，同步失败，出现以下错误之一：
    - 0x8e5e044e （JET_errWriteConflict）
    - 0x8e5e0450 （JET_errInvalidSesid）
    - 0x8e5e0442 （JET_errInstanceUnavailable）

## <a name="agent-version-9000"></a>代理版本 9.0.0.0
以下发行说明适用于 Azure 文件同步代理的版本 9.0.0.0（2019 年 12 月 2 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 自助服务恢复支持
    - 用户现在可以使用以前的版本功能还原其文件。 在 v9 版本之前，启用了云分层的卷不支持以前的版本功能。 必须为每个卷单独启用此功能，其中存在启用了云分层的终结点。 若要了解详细信息，请参阅  
[通过早期版本和 VSS（卷阴影复制服务）进行自助服务还原](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service)。 
 
- 支持更大的文件共享大小 
    - Azure 文件同步现在支持单个同步命名空间中多达 64TiB 和 1 亿个文件。  
 
- 服务器 2019 上的数据重复数据消除支持 
    - 现在，Windows Server 2019 上启用了云分层，支持数据重复数据消除。 为了支持具有云分层的卷上的数据重复数据消除，必须安装 Windows 更新[KB4520062。](https://support.microsoft.com/help/4520062) 
 
- 改进了文件到层的最小文件大小 
    - 文件到层的最小文件大小现在基于文件系统群集大小（将文件系统群集大小的两倍）。 例如，默认情况下，NTFS 文件系统群集大小为 4KB，生成的文件到层的最小文件大小为 8KB。 
 
- 网络连接测试 cmdlet 
    - 作为 Azure 文件同步配置的一部分，必须联系多个服务终结点。 它们各自都有自己的 DNS 名称，服务器需要访问该名称。 这些 URL 还特定于服务器注册到的区域。 注册服务器后，连接测试 cmdlet（PowerShell 和服务器注册实用程序）可用于测试与特定于此服务器的所有 URL 的通信。 当通信不完整阻止服务器完全使用 Azure 文件同步并且可用于微调代理和防火墙配置时，此 cmdlet 可帮助进行故障排除。  
 
        要运行网络连接测试，运行以下 PowerShell 命令： 
 
        导入模块"C：\程序文件\Azure_存储同步代理\存储同步.管理.ServerCmdlet.dll"  
        测试存储同步网络连接
 
- 启用云分层后删除服务器终结点改进 
    - 与以前一样，删除服务器终结点不会导致删除 Azure 文件共享中的文件。 但是，本地服务器上的重新分析点的行为已更改。 现在删除服务器终结点时，将删除重新分析点（指向服务器上不是本地的文件的指针）。 完全缓存的文件将保留在服务器上。 进行此改进是为了防止在删除服务器终结点时[孤立分层文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。 如果重新创建服务器终结点，将在服务器上重新创建分层文件的重新分析点。  
 
- 性能和可靠性提升 
    - 减少召回失败。 现在，根据网络带宽自动调整召回大小。 
    - 将新服务器添加到同步组时提高了下载性能。 
    - 由于约束冲突，文件未同步的数量减少。 
    - 如果服务器终结点路径是卷装载点，则文件无法分层或意外在某些情况下被调用。
    
### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 纳米服务器部署选项不支持代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，并可能导致意外的结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。 要了解更多信息，请参阅[删除服务器终结点后，服务器无法访问分层文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 要立即同步 Azure 文件共享中更改的文件，可以使用[Invoke-AzStorageSyncChange 检测](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)电源 Shell cmdlet 手动启动检测 Azure 文件共享中的更改。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。
- 如果 pagefile.sys 位于启用了云分层的卷上，则文件可能无法分层。 pagefile.sys 应位于禁用云分层的卷上。

## <a name="agent-version-8000"></a>代理版本 8.0.0.0
以下发行说明适用于 Azure 文件同步代理的版本 8.0.0.0（2019 年 10 月 8 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 恢复性能改进
    - 通过 Azure 备份完成恢复的恢复时间更快。 还原的文件将更快地同步回 Azure 文件同步服务器。 
- 改进的云分层门户体验  
    - 如果具有无法调用的分层文件，现在可以查看服务器终结点属性中的撤回错误。 此外，如果云分层筛选器驱动程序未加载到服务器上，则服务器终结点运行状况现在将显示错误和缓解步骤。
- 更简单的代理安装
    - 不再需要 Az_AzureRM PowerShell 模块来注册服务器，使安装更简单、更快速。
- 各种性能和可靠性改进

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 纳米服务器部署选项不支持代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，并可能导致意外的结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。 要了解更多信息，请参阅[删除服务器终结点后，服务器无法访问分层文件](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 要立即同步 Azure 文件共享中更改的文件，可以使用[Invoke-AzStorageSyncChange 检测](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)电源 Shell cmdlet 手动启动检测 Azure 文件共享中的更改。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。

## <a name="agent-version-7200"></a>代理版本 7.2.0.0
以下发行说明适用于 2019 年 7 月 24 日发布的 Azure 文件同步代理的版本 7.2.0.0。 这些注释是版本 7.0.0.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 如果代理配置为空，则存储同步代理 （FileSyncSvc） 崩溃。
- 如果服务器上的多个终结点具有相同的名称，服务器终结点将启动 BCDR（错误 0x80c80257 - ECS_E_BCDR_IN_PROGRESS）。
- 云分层可靠性改进。

## <a name="agent-version-7100"></a>代理版本 7.1.0.0
以下发行说明适用于 2019 年 7 月 12 日发布的 Azure 文件同步代理的版本 7.1.0.0。 这些注释是版本 7.0.0.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 在 Windows Server 2012 R2 上通过 SMB 访问或浏览服务器终结点位置的速度很慢。 
- 安装 Azure 文件同步 v6 代理后提高 CPU 利用率。
- 云分层遥测改进。
- 针对云分层和同步的其他可靠性改进。

## <a name="agent-version-7000"></a>代理版本 7.0.0.0
以下发行说明适用于 Azure 文件同步代理的版本 7.0.0.0（2019 年 6 月 19 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 支持更大的文件共享大小
    - 随着较大 Azure 文件共享的预览，我们也在增加对文件同步的支持限制。 在第一步中，Azure 文件同步现在支持单个同步命名空间中多达 25 TB 和 5000 万个文件。 要申请大型文件共享预览，请填写此表单https://aka.ms/azurefilesatscalesurvey。 
- 支持存储帐户上的防火墙和虚拟网络设置
    - Azure 文件同步现在支持存储帐户上的防火墙和虚拟网络设置。 要将部署配置为使用防火墙和虚拟网络设置，请参阅[配置防火墙和虚拟网络设置](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings)。
- PowerShell cmdlet 可立即同步 Azure 文件共享中更改的文件
    - 要立即同步 Azure 文件共享中更改的文件，可以使用 Invoke-AzStorageSyncChange 检测电源 Shell cmdlet 手动启动检测 Azure 文件共享中的更改。 此 cmdlet 适用于某些类型的自动进程在 Azure 文件共享中进行更改或由管理员执行更改（例如将文件和目录移动到共享中）的情况。 对于最终用户更改，建议在 IaaS VM 中安装 Azure 文件同步代理，并让最终用户通过 IaaS VM 访问文件共享。 这样，所有更改都将快速同步到其他代理，而无需使用 Invoke-AzStorageSyncChange检测 cmdlet。 要了解更多信息，请参阅[调用-Azstorage 同步更改检测](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection)文档。
- 如果遇到未同步的文件，则改进了门户体验
    - 如果您有无法同步的文件，我们现在会区分门户中的瞬态错误和持久性错误。 暂时性错误通常无需管理员操作即可自行解决。 例如，在关闭文件句柄之前，当前正在使用的文件不会同步。 对于持久性错误，我们现在显示受每个错误影响的文件数。 持久错误计数也显示在同步组中所有服务器终结点未同步列的文件中。
- 改进的 Azure 备份文件级还原
    - 现在，使用 Azure 备份还原的单个文件将更快地检测到并同步到服务器终结点。
- 改进了云分层召回 cmdlet 可靠性 
    - "调用-存储同步文件调用 cmdlet"现在允许客户指定每个文件重试计数和每个文件重试延迟（类似于 robocopy）。 以前，此 cmdlet 会随机调用给定路径下的所有分层文件。 使用新的 -Order 参数，此 cmdlet 将首先调用最热门的数据，并遵循云分层策略（如果满足日期策略或满足卷可用空间，则停止调用;以先发生者为准）。
- 仅支持 TLS 1.2（禁用 TLS 1.0 和 1.1）
    - Azure 文件同步现在仅支持在禁用 TLS 1.0 和 1.1 的服务器上使用 TLS 1.2。 在此改进之前，如果 TLS 1.0 和 1.1 在服务器上被禁用，则服务器注册将失败。
- 用于同步和云分层的其他性能和可靠性改进
    - 此版本中有几个可靠性和性能改进。 其中一些旨在使云分层更高效，而 Azure 文件同步作为一个整体，在您设置了带宽限制计划时，可以更好地工作。

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 纳米服务器部署选项不支持代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，并可能导致意外的结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。

## <a name="agent-version-6300"></a>代理版本 6.3.0.0
以下发行说明适用于 2019 年 6 月 27 日发布的 Azure 文件同步代理版本 6.3.0.0。 这些注释是版本 6.0.0.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 在 Windows 服务器 2012 R2 上通过 SMB 访问或浏览服务器终结点位置的速度很慢 
- 安装 Azure 文件同步 v6 代理后提高 CPU 利用率
- 云分层遥测改进

## <a name="agent-version-6200"></a>代理版本 6.2.0.0
以下发行说明适用于 2019 年 6 月 13 日发布的 Azure 文件同步代理版本 6.2.0.0。 这些注释是版本 6.0.0.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 创建服务器终结点后，后台撤回将文件下载到服务器时，可能会出现高 CPU 使用率
- 由于令牌过期，同步和云分层操作可能会失败，ECS_E_SERVER_CREDENTIAL_NEEDED错误
- 如果下载文件的 URL 包含保留字符，则撤回文件可能会失败 

## <a name="agent-version-6100"></a>代理版本 6.1.0.0
以下发行说明适用于 2019 年 5 月 6 日发布的 Azure 文件同步代理版本 6.1.0.0。 这些注释是版本 6.0.0.0 列出的发行说明的补充。

此版本修复的问题列表：  
- Windows 管理中心无法在安装了 Azure 文件同步代理版本 6.0 的服务器上显示代理版本和服务器终结点配置。

## <a name="agent-version-6000"></a>代理版本 6.0.0.0
以下发行说明适用于 Azure 文件同步代理的版本 6.0.0.0（2019 年 4 月 22 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 代理自动更新支持
  - 我们已收到您的反馈，并将自动更新功能添加到 Azure 文件同步服务器代理中。 有关详细信息，请参阅[Azure 文件同步代理更新策略](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy)。
- 支持 Azure 文件共享 ACL
  - Azure 文件同步始终支持在服务器终结点之间同步 ACL，但 ACL 未同步到云终结点（Azure 文件共享）。 此版本添加了对服务器和云终结点之间同步 ACL 的支持。
- 服务器终结点的并行上载和下载同步会话 
  - 服务器终结点现在支持同时上传和下载文件。 无需再等待下载完成，以便文件可以上载到 Azure 文件共享。 
- 新的云分层 cmdlet，以获取卷和分层状态
  - 两个新的服务器本地 PowerShell cmdlet 现在可用于获取云分层和文件撤回信息。 它们使来自服务器上两个事件通道的日志记录信息可用：
    - 获取存储同步文件分层结果将列出所有文件及其路径尚未分层，并报告原因。
    - 获取存储同步文件撤回结果报告所有文件撤回事件。 它列出了每个被召回的文件及其路径以及该调用的成功或错误。
  - 默认情况下，两个事件通道每个都可以存储多达 1 MB - 您可以通过增加事件通道大小来增加报告的文件量。
- 支持 FIPS 模式
  - Azure 文件同步现在支持在安装了 Azure 文件同步代理的服务器上启用 FIPS 模式。
    - 在服务器上启用 FIPS 模式之前，请在服务器上安装 Azure 文件同步代理和[包管理模块](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2)。 如果已在服务器上启用了 FIPS，[请手动将](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)[包管理模块](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2)下载到您的服务器。
- 云分层和同步的其他可靠性改进

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 纳米服务器部署选项不支持代理。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，那样做会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。
- 从 SMB 客户端查看文件属性时，脱机属性可能会显示为设置不正确，因为对文件元数据进行了 SMB 缓存。

## <a name="agent-version-5200"></a>代理版本 5.2.0.0
以下发行说明适用于 2019 年 4 月 4 日发布的 Azure 文件同步代理的版本 5.2.0.0。 这些注释是版本 5.0.2.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 离线数据传输和数据传输恢复功能的可靠性改进
- 同步遥测改进

## <a name="agent-version-5100"></a>代理版本 5.1.0.0
以下发行说明适用于 2019 年 3 月 7 日发布的 Azure 文件同步代理的版本 5.1.0.0。 这些注释是版本 5.0.2.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 如果更改枚举在服务器上失败，文件可能无法与错误 0x80c8031d （ECS_E_CONCURRENCY_CHECK_FAILED） 同步
- 如果同步会话或文件收到错误 0x80072f78 （WININET_E_INVALID_SERVER_RESPONSE），则同步现在将重试操作
- 文件可能无法与错误 0x80c80203 （ECS_E_SYNC_INVALID_STAGED_FILE） 同步
- 召回文件时可能会出现高内存使用率
- 云分层遥测改进 

## <a name="agent-version-5020"></a>代理版本 5.0.2.0
以下发行说明适用于 Azure 文件同步代理版本 5.0.2.0（2019 年 2 月 12 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 支持 Azure 政府云
  - 我们增加了针对 Azure 政府云的预览版支持。 这需要将订阅加入允许列表，并从 Microsoft 下载特殊代理。 要访问预览版，请直接发送电子邮件至[AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)。
- 支持重复数据删除
    - Windows Server 2016 和 Windows Server 2019 现在完全支持重复数据删除并启用了云分层功能。 在启用了云分层的卷上启用重复数据删除后，即可在本地缓存更多文件，而无需预配更多存储。
- 支持脱机数据传输（例如，通过 Data Box 进行的脱机数据传输）
    - 轻松地通过任何所选手段将大量数据迁移到 Azure 文件同步。 您可以选择 Azure 数据框、AzCopy 甚至第三方迁移服务。 不需消耗大量带宽将数据传输到 Azure。如果使用 Data Box，只需直接将其邮寄过去就可以了！ 若要了解详细信息，请参阅[脱机数据传输文档](https://aka.ms/AFS/OfflineDataTransfer)。
- 改进同步性能
    - 在此版本发布之前，如果在同一卷上有多个服务器终结点，客户会体验到同步性能下降。 Azure 文件同步每天在服务器上创建临时 VSS 快照一次，以同步包含开放句柄的文件。 现在，在 VSS 同步会话时处于活动状态时，Azure 文件同步支持多个服务器终结点在一个卷上进行同步。 完成 VSS 同步会话不再需要等待，因此可以在该卷的其他服务器终结点上继续同步。
- 改进在门户中进行的监视
    - 在存储同步服务门户中添加了图表，用于查看：
        - 已同步的文件数
        - 已传输数据的大小
        - 未同步文件的数目
        - 回调的数据的大小
        - 服务器连接状态
    - 若要了解详细信息，请参阅[监视 Azure 文件同步](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring)。
- 提高了可伸缩性和可靠性
    - 一个目录中文件系统对象（目录和文件）的最大数目已增加到 1,000,000。 以前的限制为 200,000。
    - 当传输因文件过大而中断时，Azure 文件同步会继续进行数据传输而不会重新传输。 

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 此代理在 Windows Server Core 或 Nano Server 部署选项上不受支持。
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。
- FIPS 模式不受支持，必须禁用。 

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，那样做会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
- 包含不受支持字符的文件。 有关不受支持的字符的列表，请参阅[故障排除指南](storage-sync-files-troubleshoot.md#handling-unsupported-characters)。
- 以句点结尾的文件或目录。
- 长度超过 2,048 个字符的路径。
- 安全描述符的自定义访问控制列表 (DACL) 部分（如果其大小大于 2 KB）。 （只有在单个项上的访问控制条目 (ACE) 大于某个数（大约为 40）的情况下，这才是一个问题。）
- 安全描述符的系统访问控制列表 (SACL) 部分，用于审核。
- 扩展的属性。
- 备用数据流。
- 重分析点。
- 硬链接。
- 将更改从其他终结点同步到服务器文件时，不会保留压缩（如果在该文件上设置了压缩）。
- 任何使用 EFS（或其他用户模式加密方式）加密的文件，此类加密会阻止服务读取数据。

    > [!Note]  
    > Azure 文件同步始终加密传输中的数据， 而在 Azure 中，数据始终进行静态加密。
 
### <a name="server-endpoint"></a>服务器终结点
- 服务器终结点只能在 NTFS 卷上创建。 Azure 文件同步目前不支持 ReFS、FAT、FAT32 等文件系统。
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。
- 从 SMB 客户端查看文件属性时，脱机属性可能会显示为设置不正确，因为对文件元数据进行了 SMB 缓存。
