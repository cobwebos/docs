---
title: 通过 Azure 备份来备份文件和文件夹-常见问题
description: 解决有关在 Azure 备份中备份文件和文件夹的常见问题。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: c30b918be5e4185d6fb4fdd2fcfc47f8dd4d25ef
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969169"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>有关备份文件和文件夹的常见问题

本文提供了 Microsoft Azure 恢复服务有关在[Azure 备份](backup-overview.md)服务中备份文件和文件夹 ABOUND （MARS）代理的常见问题的解答。

## <a name="general"></a>一般信息

## <a name="configure-backups"></a>配置备份

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>在哪里可以下载最新版本的 MARS 代理？

备份 Windows Server 计算机、System Center DPM 和 Microsoft Azure 备份服务器时使用的最新 MARS 代理可供[下载](https://aka.ms/azurebackup_agent)。

### <a name="how-long-are-vault-credentials-valid"></a>保管库凭据的有效期有多长？

保管库凭据会在 48 小时后过期。 如果凭据文件过期，请从 Azure 门户再次下载该文件。

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>可以从哪些驱动器备份文件和文件夹？

无法备份以下类型的驱动器和卷：

* 可移动媒体：所有备份项源必须报告为固定。
* 只读卷：要使卷影复制服务（VSS）正常工作，卷必须可写。
* 脱机卷：若要运行 VSS，卷必须处于联机状态。
* 网络共享：要使用联机备份进行备份的服务器的本地卷必须是本地的。
* 受 BitLocker 保护的卷：必须先解锁卷，然后才能进行备份。
* 文件系统标识：NTFS 是受支持的唯一文件系统。

### <a name="what-file-and-folder-types-are-supported"></a>支持哪些文件和文件夹类型？

[详细了解](backup-support-matrix-mars-agent.md#supported-file-types-for-backup)支持备份的文件和文件夹的类型。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>能否使用 MARS 代理来备份 Azure VM 上的文件和文件夹？  

可以。 Azure 备份使用 Azure VM 代理的 VM 扩展为 Azure Vm 提供 VM 级别备份。 如果你想要在 VM 上备份来宾 Windows 操作系统中的文件和文件夹，可以安装 MARS 代理来执行此操作。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>是否可以使用 MARS 代理来备份 Azure VM 的临时存储中的文件和文件夹？

可以。 安装 MARS 代理，并将来宾 Windows 操作系统上的文件和文件夹备份到临时存储。

* 擦除临时存储数据后，备份作业将失败。
* 如果删除临时存储数据，则只能还原到非易失性存储。

### <a name="how-do-i-register-a-server-to-another-region"></a>如何实现将服务器注册到另一个区域？

将备份数据发送到在其中注册服务器的保管库的数据中心。 更改数据中心的最简单方法是卸载并重新安装代理，然后将该计算机注册到所需的区域中的新保管库。

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 代理是否支持 Windows Server 2012 重复数据删除？

可以。 MARS 代理在准备备份操作时将删除了重复数据的数据转换为常规数据。 然后，它会优化用于备份的数据，对数据进行加密，然后将加密的数据发送到保管库。

## <a name="manage-backups"></a>管理备份

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>如果重命名为备份配置的 Windows 计算机，会发生什么情况？

重命名 Windows 计算机时，所有当前配置的备份都将停止。

* 需要向备份保管库注册新的计算机名称。
* 当你向保管库注册新名称时，第一个操作是*完整*备份。
* 如果需要恢复使用旧服务器名称备份到保管库的数据，请使用 "恢复数据" 向导中的 "还原到备用位置" 选项。 [了解详细信息](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>备份的最大文件路径长度是多少？

MARS 代理依赖 NTFS，并使用[WINDOWS API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths)限制的 filepath 长度规范。 如果要保护的文件比允许的值长，请备份父文件夹或磁盘驱动器。  

### <a name="what-characters-are-allowed-in-file-paths"></a>文件路径中允许哪些字符？

MARS 代理依赖 NTFS，并允许文件名称/路径中[支持的字符](/windows/desktop/FileIO/naming-a-file#naming-conventions)。

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>此时会显示警告 "尚未为此服务器配置 Azure 备份"。

即使在本地服务器上存储的备份计划设置与备份保管库中存储的设置不相同，也会出现此警告。

* 将服务器或设置恢复到已知良好状态后，备份计划可能会变得不同步。
* 如果收到此警告，请重新[配置](backup-azure-manage-windows-server.md)备份策略，然后运行按需备份，将本地服务器与 Azure 重新同步。

## <a name="manage-the-backup-cache-folder"></a>管理备份缓存文件夹

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>针对缓存文件夹的最小大小要求是什么？

缓存文件夹的大小由你正在备份的数据量确定。

* 缓存文件夹卷的可用空间应等于备份数据总大小的至少5-10%。
* 如果卷的可用空间少于5%，请增加卷大小，或者将缓存文件夹移动到具有足够空间的卷。
* 如果备份 Windows 系统状态，则在包含缓存文件夹的卷上将需要额外的 30-35 GB 可用空间。

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>如何检查暂存文件夹是否有效并可访问？

1. 默认情况下，暂存文件夹位于 `\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. 确保暂存文件夹位置的路径与下面显示的注册表项项的值匹配：

  | 注册表路径 | 注册表项 | Value |
  | --- | --- | --- |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新缓存文件夹位置* |
  | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新缓存文件夹位置* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>如何实现更改 MARS 代理的缓存位置？

1. 在提升的命令提示符下运行此命令以停止备份引擎：

    ```PS C:\> Net stop obengine```

2. 如果已配置系统状态备份，请打开 "磁盘管理"，然后卸载其名称格式为 "`"CBSSBVol_<ID>"`" 的磁盘。
3. 请勿移动文件。 而是将缓存空间文件夹复制到具有足够空间的其他驱动器。
4. 用新缓存文件夹的路径更新以下注册表项。<br/>

    | 注册表路径 | 注册表项 | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新缓存文件夹位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新缓存文件夹位置* |

5. 在提升的命令提示符下重新启动备份引擎：

    ```PS C:\> Net stop obengine```

    ```PS C:\> Net start obengine```

6. 运行即席备份。 使用新位置成功完成备份后，可以删除原始缓存文件夹。

### <a name="where-should-the-cache-folder-be-located"></a>缓存文件夹应位于何处？

不建议将缓存文件夹放在以下位置：

* 网络共享/可移动媒体：缓存文件夹必须位于需要使用联机备份进行备份的服务器本地。 不支持网络位置或可移动媒体，例如 U 盘
* 脱机卷：缓存文件夹必须联机才能使用 Azure 备份代理执行预期的备份

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>缓存文件夹是否有任何不受支持的属性？

缓存文件夹不支持以下属性或其组合：

* 加密
* 已删除重复数据
* 已压缩
* 稀疏
* 重分析点

缓存文件夹和元数据 VHD 没有 Azure 备份代理所需的属性。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>是否有办法调整用于备份的带宽量？

是的，可以使用 MARS 代理中的 "**更改属性**" 选项来调整带宽和计时。 [了解详细信息](backup-configure-vault.md#enable-network-throttling)。

## <a name="restore"></a>还原

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果取消正在进行的还原作业，会发生什么情况？

如果取消正在进行的还原作业，还原过程将停止。 在取消之前还原的所有文件都保留在配置的目标（原始或备用位置）中，无需任何回滚。

## <a name="next-steps"></a>后续步骤

[了解](tutorial-backup-windows-server-to-azure.md)如何备份 Windows 计算机。
