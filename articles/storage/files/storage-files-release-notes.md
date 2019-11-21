---
title: Azure 文件同步代理发行说明 | Microsoft Docs
description: Azure 文件同步代理发行说明。
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 10/8/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 8328170d73d75e8e2eb1a84881375ce21c72a61b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227836"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 文件同步代理发行说明
借助 Azure 文件同步，既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 Windows Server 安装可转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上提供的任意协议（包括 SMB、NFS 和 FTPS）以本地方式访问数据， 并且可以根据需要在世界各地设置多个缓存。

本文提供受支持的 Azure 文件同步代理版本的发行说明。

## <a name="supported-versions"></a>支持的版本
以下版本是 Azure 文件同步代理支持的：

| 里程碑 | 代理版本号 | 发行日期 | 状态 |
|----|----------------------|--------------|------------------|
| V8 Release - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | October 8, 2019 | 受支持 |
| July 2019 update rollup - [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 2019 年 7 月 24 日 | 受支持 |
| July 2019 update rollup - [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | July 12, 2019 | 受支持 |
| V7 Release - [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | June 19, 2019 | 受支持 |
| June 2019 update rollup - [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | June 27, 2019 | 受支持 |
| June 2019 update rollup - [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | June 13, 2019 | 受支持 |
| May 2019 update rollup - [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | May 7, 2019 | 受支持 |
| V6 Release - [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | April 21, 2019 | 受支持 |
| April 2019 update rollup - [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | April 4, 2019 | 受支持 |
| March 2019 update rollup - [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | March 7, 2019 | 受支持 |
| V5 版本 - [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 2019 年 2 月 12 日 | 受支持 |
| V4 版本 | 4.0.1.0 - 4.3.0.0 | N/A | Not Supported - Agent versions expired on November 6, 2019 |
| V3 Release | 3.1.0.0 - 3.4.0.0 | N/A | Not Supported - Agent versions expired on August 19, 2019 |
| Pre-GA agents | 1.1.0.0 - 3.0.13.0 | N/A | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-8000"></a>Agent version 8.0.0.0
The following release notes are for version 8.0.0.0 of the Azure File Sync agent (released October 8, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- Restore performance Improvements
    - Faster recovery times for recovery done through Azure Backup. Restored files will sync back down to Azure File Sync servers much faster. 
- Improved cloud tiering portal experience  
    - If you have tiered files that are failing to recall, you can now view the recall errors in the server endpoint properties. Also, the server endpoint health will now show an error and mitigation steps if the cloud tiering filter driver is not loaded on the server.
- Simpler agent installation
    - The Az\AzureRM PowerShell module is no longer required to register the server making installation simpler and fast.
- Miscellaneous performance and reliability improvements

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- The agent is not supported on Nano Server deployment option.
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

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
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可访问。 若要还原对文件的访问权限，请重新创建服务器终结点。 如果自删除服务器终结点后已过去了 30 天或者如果删除了云终结点，则未撤回的分层文件将不可用。 To learn more, see [Tiered files are not accessible on the server after deleting a server endpoint](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点位置中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 To immediately sync files that are changed in the Azure file share, the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。

## <a name="agent-version-7200"></a>Agent version 7.2.0.0
The following release notes are for version 7.2.0.0 of the Azure File Sync agent released July 24, 2019. These notes are in addition to the release notes listed for version 7.0.0.0.

此版本修复的问题列表：  
- Storage Sync Agent (FileSyncSvc) crashes if the proxy configuration is null.
- Server endpoint will start BCDR (error 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) if multiple endpoints on the server have the same name.
- Cloud tiering reliability improvements.

## <a name="agent-version-7100"></a>Agent version 7.1.0.0
The following release notes are for version 7.1.0.0 of the Azure File Sync agent released July 12, 2019. These notes are in addition to the release notes listed for version 7.0.0.0.

此版本修复的问题列表：  
- Accessing or browsing a server endpoint location over SMB is slow on Windows Server 2012 R2. 
- Increased CPU utilization after installing the Azure File Sync v6 agent.
- Cloud tiering telemetry improvements.
- 针对云分层和同步的其他可靠性改进。

## <a name="agent-version-7000"></a>Agent version 7.0.0.0
The following release notes are for version 7.0.0.0 of the Azure File Sync agent (released June 19, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- Support for larger file share sizes
    - With the preview of larger Azure file shares, we are increasing our support limits for file sync as well. In this first step, Azure File Sync now supports up to 25 TB and 50 million files in a single, syncing namespace. To apply for the large file share preview, fill in this form https://aka.ms/azurefilesatscalesurvey. 
- Support for firewall and virtual network setting on storage accounts
    - Azure File Sync now supports the firewall and virtual network setting on storage accounts. To configure your deployment to work with the firewall and virtual network setting, see [Configure firewall and virtual network settings](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- PowerShell cmdlet to immediately sync files changed in the Azure file share
    - To immediately sync files that are changed in the Azure file share, the Invoke-AzStorageSyncChangeDetection PowerShell cmdlet can be used to manually initiate the detection of changes in the Azure file share. This cmdlet is intended for scenarios where some type of automated process is making changes in the Azure file share or the changes are done by an administrator (like moving files and directories into the share). For end-user changes, the recommendation is to install the Azure File Sync agent in an IaaS VM and have end users access the file share through the IaaS VM. This way all changes will quickly sync to other agents without the need to use the Invoke-AzStorageSyncChangeDetection cmdlet. To learn more, see the [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) documentation.
- Improved portal experience if you encounter files that are not syncing
    - If you have files that are failing to sync, we now differentiate between transient and persistent errors in the portal. Transient errors usually resolve themselves without the need for admin action. For example, a file that is currently in use will not sync until the file handle is closed. For persistent errors, we now show the number of files impacted by each error. The persistent error count is also displayed in the files not syncing column of all server endpoints in a sync group.
- Improved Azure Backup file-level restore
    - Individual files restored using Azure Backup are now detected and synced to the server endpoint faster.
- Improved cloud tiering recall cmdlet reliability 
    - The Invoke-StorageSyncFileRecall cmdlet now allows customers to specify per file retry count and per file retry delay similar to robocopy. Previously, this cmdlet would recall all tiered files under a given path in random order. With the new -Order parameter, this cmdlet will recall the hottest data first and honor the cloud tiering policy (stop recalling if the date policy is met or the volume free space is met; whichever happens first).
- Support for TLS 1.2 only (TLS 1.0 and 1.1 is disabled)
    - Azure File Sync now supports using TLS 1.2 only on servers that have TLS 1.0 and 1.1 disabled. Prior to this improvement, server registration would fail if TLS 1.0 and 1.1 was disabled on the server.
- Miscellaneous performance and reliability improvements for sync and cloud tiering
    - There are several reliability and performance improvements in this release. Some of them are targeted to make cloud tiering more efficient and Azure File Sync as a whole work better in those situations when you have a bandwidth throttling schedule set.

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- The agent is not supported on Nano Server deployment option.
- 只有 Windows Server 2019、Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- Running sysprep on a server that has the Azure File Sync agent installed is not supported and can lead to unexpected results. 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。

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

## <a name="agent-version-6300"></a>Agent version 6.3.0.0
The following release notes are for version 6.3.0.0 of the Azure File Sync agent released June 27, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

此版本修复的问题列表：  
- Accessing or browsing a server endpoint location over SMB is slow on Windows Server 2012 R2 
- Increased CPU utilization after installing the Azure File Sync v6 agent
- Cloud tiering telemetry improvements

## <a name="agent-version-6200"></a>Agent version 6.2.0.0
The following release notes are for version 6.2.0.0 of the Azure File Sync agent released June 13, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

此版本修复的问题列表：  
- After creating a server endpoint, High CPU usage may occur when background recall is downloading files to the server
- Sync and cloud tiering operations may fail with error ECS_E_SERVER_CREDENTIAL_NEEDED due to token expiration
- Recalling a file may fail if the URL to download the file contains reserved characters 

## <a name="agent-version-6100"></a>Agent version 6.1.0.0
The following release notes are for version 6.1.0.0 of the Azure File Sync agent released May 6, 2019. These notes are in addition to the release notes listed for version 6.0.0.0.

此版本修复的问题列表：  
- Windows Admin Center fails to display the agent version and server endpoint configuration on servers which have Azure File Sync agent version 6.0 installed.

## <a name="agent-version-6000"></a>Agent version 6.0.0.0
The following release notes are for version 6.0.0.0 of the Azure File Sync agent (released April 22, 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- Agent auto-update support
  - We have heard your feedback and added an auto-update feature into the Azure File Sync server agent. For more information, see [Azure File Sync agent update policy](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Support for Azure file share ACLs
  - Azure File Sync has always supported syncing ACLs between server endpoints but the ACLs were not synced to the cloud endpoint (Azure file share). This release adds support for syncing ACLs between server and cloud endpoints.
- Parallel upload and download sync sessions for a server endpoint 
  - Server endpoints now support uploading and downloading files at the same time. No more waiting for a download to complete so files can be uploaded to the Azure file share. 
- New Cloud Tiering cmdlets to get volume and tiering status
  - Two new, server-local PowerShell cmdlets can now be used to obtain cloud tiering and file recall information. They make logging information from two event channels on the server available:
    - Get-StorageSyncFileTieringResult will list all files and their paths that haven't tiered and reports on the reason why.
    - Get-StorageSyncFileRecallResult reports all file recall events. It lists every file recalled and its path as well as success or error for that recall.
  - By default, both event channels can store up to 1 MB each – you can increase the amount of files reported by increasing the event channel size.
- Support for FIPS mode
  - Azure File Sync now supports enabling FIPS mode on servers which have the Azure File Sync agent installed.
    - Prior to enabling FIPS mode on your server, install the Azure File Sync agent and [PackageManagement module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) on your server. If FIPS is already enabled on the server, [manually download](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) the [PackageManagement module](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) to your server.
- Miscellaneous reliability improvements for cloud tiering and sync

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- The agent is not supported on Nano Server deployment option.
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

## <a name="agent-version-5200"></a>Agent version 5.2.0.0
The following release notes are for version 5.2.0.0 of the Azure File Sync agent released April 4, 2019. These notes are in addition to the release notes listed for version 5.0.2.0.

此版本修复的问题列表：  
- Reliability improvements for offline data transfer and data transfer resume features
- Sync telemetry improvements

## <a name="agent-version-5100"></a>Agent version 5.1.0.0
The following release notes are for version 5.1.0.0 of the Azure File Sync agent released March 7, 2019. These notes are in addition to the release notes listed for version 5.0.2.0.

此版本修复的问题列表：  
- Files may fail to sync with error 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) if change enumeration is failing on the server
- If a sync session or file receives an error 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), sync will now retry the operation
- Files may fail to sync with error 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- High memory usage may occur when recalling files
- Cloud tiering telemetry improvements 

## <a name="agent-version-5020"></a>代理版本 5.0.2.0
以下发行说明适用于 Azure 文件同步代理版本 5.0.2.0（2019 年 2 月 12 日发布）。

### <a name="improvements-and-issues-that-are-fixed"></a>改进和已解决的问题

- 支持 Azure 政府云
  - 我们增加了针对 Azure 政府云的预览版支持。 这需要将订阅加入允许列表，并从 Microsoft 下载特殊代理。 若要访问预览版，请将电子邮件直接发送至 [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com)。
- 支持重复数据删除
    - Windows Server 2016 和 Windows Server 2019 现在完全支持重复数据删除并启用了云分层功能。 在启用了云分层的卷上启用重复数据删除以后，即可在本地缓存更多的文件，不需预配更多的存储。
- 支持脱机数据传输（例如，通过 Data Box 进行的脱机数据传输）
    - 轻松地通过任何所选手段将大量数据迁移到 Azure 文件同步。 You can choose Azure Data Box, AzCopy and even third-party migration services. 不需消耗大量带宽将数据传输到 Azure。如果使用 Data Box，只需直接将其邮寄过去就可以了！ 若要了解详细信息，请参阅[脱机数据传输文档](https://aka.ms/AFS/OfflineDataTransfer)。
- 改进同步性能
    - 在此版本发布之前，如果在同一卷上有多个服务器终结点，客户会体验到同步性能下降。 Azure 文件同步每天在服务器上创建临时 VSS 快照一次，以同步包含开放句柄的文件。 现在，在 VSS 同步会话时处于活动状态时，Azure 文件同步支持多个服务器终结点在一个卷上进行同步。 完成 VSS 同步会话不再需要等待，因此可以在该卷的其他服务器终结点上继续同步。
- 改进在门户中进行的监视
    - 在存储同步服务门户中添加了图表，用于查看：
        - 已同步的文件数
        - 已传输数据的大小
        - 未同步文件的数目
        - 回调数据的大小
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
