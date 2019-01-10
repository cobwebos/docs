---
title: Azure 文件同步代理发行说明 | Microsoft Docs
description: Azure 文件同步代理发行说明。
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 12/10/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 87e6f2c956766011adef23ce9a8565625483a244
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53635538"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Azure 文件同步代理发行说明
借助 Azure 文件同步，既可将组织的文件共享集中在 Azure 文件中，又不失本地文件服务器的灵活性、性能和兼容性。 Windows Server 安装可转换为 Azure 文件共享的快速缓存。 可以使用 Windows Server 上提供的任意协议（包括 SMB、NFS 和 FTPS）以本地方式访问数据， 并且可以根据需要在世界各地设置多个缓存。

本文提供受支持的 Azure 文件同步代理版本的发行说明。

## <a name="supported-versions"></a>支持的版本
以下版本是 Azure 文件同步代理支持的：

| 里程碑 | 代理版本号 | 发行日期 | 状态 |
|----|----------------------|--------------|------------------|
| 12 月更新汇总 - [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 2018 年 12 月 10 日 | 支持（建议的版本） |
| 12 月更新汇总 | 4.1.0.0 | 2018 年 12 月 4 日 | 支持 |
| V4 版本 | 4.0.1.0 | 2018 年 11 月 13 日 | 支持 |
| 9 月更新汇总 | 3.3.0.0 | 2018 年 9 月 24 日 | 支持 |
| 8 月更新汇总 | 3.2.0.0 | 2018 年 8 月 15 日 | 支持 |
| 正式版 | 3.1.0.0 | 2018 年 7 月 19日 | 支持 |
| 6 月更新汇总 | 3.0.13.0 | 2018 年 6 月 29日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |
| 刷新 2 | 3.0.12.0 | 2018 年 5 月 22 日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |
| 4 月更新汇总 | 2.3.0.0 | 2018 年 5 月 8 日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |
| 3 月更新汇总 | 2.2.0.0 | 2018 年 3 月 12 日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |
| 2 月更新汇总 | 2.1.0.0 | 2018 年 2 月 28 日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |
| 刷新 1 | 2.0.11.0 | 2018 年 2 月 8 日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |
| 1 月更新汇总 | 1.4.0.0 | 2018 年 1 月 8 日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |
| 11 月更新汇总 | 1.3.0.0 | 2017 年 11 月 30 日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |
| 10 月更新汇总 | 1.2.0.0 | 2017 年 10 月 31 日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |
| 初始预览版 | 1.1.0.0 | 2017 年 9 月 26 日 | 不支持 - 代理版本于 2018 年 10 月 1 日到期 |

### <a name="azure-file-sync-agent-update-policy"></a>Azure 文件同步代理更新策略
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-4200"></a>代理版本 4.2.0.0
以下发行说明适用于 Azure 文件同步代理版本 4.2.0.0（2018 年 12 月 10 日发布）。 这些说明是对针对版本 4.0.1.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 创建 VSS 快照时，可能会发生停止错误 0x3B 或停止错误 0x1E。  
- 启用云分层时，可能会发生内存泄漏  

## <a name="agent-version-4100"></a>代理版本 4.1.0.0
以下发行说明适用于 Azure 文件同步代理版本 4.1.0.0（2018 年 12 月 4 日发布）。 这些说明是对针对版本 4.0.1.0 列出的发行说明的补充。

此版本修复的问题列表：  
- 服务器可能因云分层内存泄漏而无响应。  
- 代理安装失败，显示以下错误：错误 1921。 无法停止服务“存储同步代理”(FileSyncSvc)。  请确保有足够的权限停止系统服务。  
- 内存使用率很高时，存储同步代理 (FileSyncSvc) 服务可能会崩溃。  
- 针对云分层和同步的其他可靠性改进。

## <a name="agent-version-4010"></a>代理版本 4.0.1.0
以下发行说明适用于 Azure 文件同步代理版本 4.0.1.0（2018 年 11 月 13 日发布）。

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 此代理在 Windows Server Core 或 Nano Server 部署选项上不受支持。
- 只有 Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GiB 的内存。 如果服务器在启用了动态内存的虚拟机中运行，则至少应当为该 VM 配置 2048 MiB 内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。
- 创建 VSS 快照时，可能会发生停止错误 0x3B 或停止错误 0x1E。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 当文件因文件屏蔽而被阻止时，文件服务器资源管理器 (FSRM) 文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，那样做会导致意外结果。 应当在部署服务器映像并完成 sysprep 迷你安装后再安装 Azure 文件同步代理。
- 不支持在同一卷上进行重复数据删除和云分层操作。

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
- 如果重命名服务器，则不会更新门户中的服务器名称。 若要更新门户中的服务器名称，请注销并重新注册服务器。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 基于日期的云分层策略设置用来指定如果在指定的天数内访问过文件，则应当缓存文件。 若要了解详细信息，请参阅[云分层概述](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering)。
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 使用 robocopy 复制文件时，可使用 /MIR 选项保留文件时间戳。 这将确保较旧的文件比最近访问的文件更早分层。
- 从 SMB 客户端查看文件属性时，脱机属性可能会显示为设置不正确，因为对文件元数据进行了 SMB 缓存。

## <a name="agent-version-3300"></a>代理版本 3.3.0.0
以下发行说明适用于 Azure 文件同步代理版本 3.3.0.0（2018 年 9 月 24 日发布）。 这些说明是对针对版本 3.1.0.0 列出的发行说明的补充。

此版本修复的问题列表：
- Azure 文件同步代理升级到版本 3.1 或 3.2 后，已注册的服务器状态为“出现脱机”。
- 由于文件路径长，存储同步代理 (FileSyncSvc) 服务崩溃。
- 服务器注册失败，显示错误：无法加载文件或程序集 Kailani.Afs.StorageSyncProtocol.V3。

## <a name="agent-version-3200"></a>代理版本 3.2.0.0
以下发行说明适用于 Azure 文件同步代理版本 3.2.0.0（2018 年 8 月 15 日发布）。 这些说明是对针对版本 3.1.0.0 列出的发行说明的补充。

此版本包括以下修复：
- 由于内存泄漏，同步失败并出现内存不足错误 (0x8007000e)

## <a name="agent-version-3100"></a>代理版本 3.1.0.0
以下发行说明适用于 Azure 文件同步代理版本 3.1.0.0（2018 年 7 月 19 日发布）。

### <a name="evaluation-tool"></a>评估工具
在部署 Azure 文件同步之前，应当使用 Azure 文件同步评估工具评估它是否与你的系统兼容。 此工具是一个 Azure PowerShell cmdlet，用于检查文件系统和数据集的潜在问题，例如不受支持的字符或不受支持的 OS 版本。 有关安装和使用情况的说明，请参阅计划指南中的[评估工具](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool)部分。 

### <a name="agent-installation-and-server-configuration"></a>代理安装和服务器配置
若要详细了解如何使用 Windows Server 安装和配置 Azure 文件同步代理，请参阅[规划 Azure 文件同步部署](storage-sync-files-planning.md)和[如何部署 Azure 文件同步](storage-sync-files-deployment-guide.md)。

- 代理安装包必须使用提升的（管理员）权限进行安装。
- 此代理在 Windows Server Core 或 Nano Server 部署选项上不受支持。
- 只有 Windows Server 2016 和 Windows Server 2012 R2 上支持此代理。
- 代理需要至少 2 GB 的物理内存。
- 存储同步代理 (FileSyncSvc) 服务不支持进行了系统卷信息 (SVI) 目录压缩的卷上的服务器终结点。 此配置会导致意外结果。

### <a name="interoperability"></a>互操作性
- 防病毒应用程序、备份应用程序和其他应用程序在访问分层文件时可能会导致不需要的撤回，除非这些应用程序遵从脱机属性，在读取这些文件的内容时跳过。 有关详细信息，请参阅[对 Azure 文件同步进行故障排除](storage-sync-files-troubleshoot.md)。
- 请勿使用文件服务器资源管理器 (FSRM) 或其他文件屏蔽。 当文件因文件屏蔽而被阻止时，文件屏蔽可能导致无休止的同步故障。
- 不支持在安装了 Azure 文件同步代理的服务器上运行 sysprep，那样做会导致意外结果。 应该在部署服务器映像并完成 sysprep 迷你安装后再安装代理和注册服务器。
- 不支持在同一卷上进行重复数据删除和云分层操作。

### <a name="sync-limitations"></a>同步限制
以下各项不同步，但系统其余部分仍会继续正常运行：
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
- 如果没有在删除服务器终结点之前撤回分层的文件，则这些文件会变得不可用。
- 系统卷上不支持云分层。 要在系统卷上创建服务器终结点，请在创建服务器终结点时禁用云分层。
- 故障转移群集仅适用于群集磁盘，而不适用于群集共享卷 (CSV)。
- 服务器终结点不能嵌套， 但可以与另一终结点并行共存于同一卷上。
- 请勿在服务器终结点中存储 OS 或应用程序分页文件。
- 如果重命名服务器，则不会更新门户中的服务器名称。 若要更新门户中的服务器名称，请注销并重新注册服务器。

### <a name="cloud-endpoint"></a>云终结点
- Azure 文件同步支持直接对 Azure 文件共享进行更改。 但是，首先需要通过 Azure 文件同步更改检测作业来发现对 Azure 文件共享进行的更改。 每 24 小时针对云终结点启动一次更改检测作业。 此外，通过 REST 协议对 Azure 文件共享所做的更改将不会更新 SMB 上次修改时间，亦不会被视为同步更改。
- 可以将存储同步服务和/或存储帐户移到现有 Azure AD 租户中的其他资源组或订阅。 如果移动了存储帐户，则需要向混合文件同步服务授予对存储帐户的访问权限（请参阅[确保 Azure 文件同步可以访问存储帐户](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)）。

    > [!Note]  
    > Azure 文件同步不支持将订阅移到其他 Azure AD 租户。

### <a name="cloud-tiering"></a>云分层
- 如果使用 Robocopy 将分层的文件复制到另一位置，生成的文件不会分层。 可能会对脱机属性进行设置，因为 Robocopy 会在复制操作中错误地包括该属性。
- 从 SMB 客户端查看文件属性时，脱机属性可能会显示为设置不正确，因为对文件元数据进行了 SMB 缓存。
