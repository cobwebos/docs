---
title: 备份文件和文件夹 - 常见问题
description: 解决与使用 Azure 备份对文件和文件夹进行备份相关的常见问题。
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 55819ce7ec5196812d935a21c096c132144d78af
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421309"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>与对文件和文件夹进行备份相关的常见问题

本文在[Azure 备份](backup-overview.md)服务中使用 Microsoft Azure 恢复服务 （MARS） 代理大量备份文件和文件夹的常见问题。

## <a name="configure-backups"></a>配置备份

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>可以从何处下载最新版本的 MARS 代理？

备份 Windows Server 计算机、System Center DPM 和 Microsoft Azure 备份服务器时使用的最新 MARS 代理可供[下载](https://aka.ms/azurebackup_agent)。

### <a name="how-long-are-vault-credentials-valid"></a>保管库凭据的有效期是多长时间？

保管库凭据会在 48 小时后过期。 如果凭据文件过期，请重新从 Azure 门户下载该文件。

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>可以从哪些驱动器备份文件和文件夹？

无法备份以下类型的驱动器/卷：

* 可移动介质：所有备份项源都必须报告为固定。
* 只读卷：卷卷必须可写，卷卷卷复制服务 （VSS） 才能正常工作。
* 脱机卷：卷必须联机才能 VSS 正常工作。
* 网络共享：卷必须是服务器的本地，才能使用联机备份进行备份。
* BitLocker 保护的卷：必须先解锁卷，然后才能进行备份。
* 文件系统标识：NTFS 是受支持的唯一文件系统。

### <a name="what-file-and-folder-types-are-supported"></a>支持哪些文件和文件夹类型？

[详细了解](backup-support-matrix-mars-agent.md#supported-file-types-for-backup)支持备份的文件和文件夹类型。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>是否可以使用 MARS 代理在 Azure VM 上备份文件和文件夹？  

是的。 Azure 备份使用 Azure VM 代理的 VM 扩展为 Azure VM 提供 VM 级备份。 如果你希望在 VM 的来宾 Windows 操作系统上备份文件和文件夹，可以安装 MARS 代理来执行该操作。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>是否可以使用 MARS 代理在 Azure VM 的临时存储上备份文件和文件夹？

是的。 安装 MARS 代理，将来宾 Windows 操作系统上的文件和文件夹备份到临时存储。

* 清除临时存储数据时，备份作业将失败。
* 如果临时存储数据被删除，则你只能还原到非易失性存储。

### <a name="how-do-i-register-a-server-to-another-region"></a>如何将服务器注册到其他区域？

备份数据会发送到服务器注册到的保管库的数据中心。 更改数据中心的最简单方法是卸载并重新安装代理，然后将计算机注册到所需的区域中的新保管库。

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 代理是否支持 Windows Server 2012 重复数据删除？

是的。 MARS 代理在准备备份操作时会将消除了重复的数据转换为常规数据。 然后，它将对数据进行优化以便备份、对数据进行加密，然后将已加密的数据发送到保管库。

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>安装和配置 MARS 代理是否需要管理员权限？

是的，使用 MARS 控制台安装 MARS 代理和配置备份需要用户成为受保护服务器上的本地管理员。

## <a name="manage-backups"></a>管理备份

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>如果我重命名了为备份配置的 Windows 计算机，将会发生什么情况？

重命名 Windows 计算机时，所有当前已配置的备份都将停止。

* 你需要向备份保管库注册新的计算机名称。
* 向保管库注册新名称时，第一个操作是“完整”** 备份。
* 如果需要恢复备份到采用旧服务器名称的保管库的数据，请使用“恢复数据”向导中用于还原到其他位置的选项。 [了解详细信息](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>用于备份的最大文件路径长度是多少？

MARS 代理依赖于 NTFS，并使用受 [Windows API](/windows/desktop/FileIO/naming-a-file#fully-qualified-vs-relative-paths) 限制的文件路径长度规范。 如果你要保护的文件长于所允许的值，请备份父文件夹或磁盘驱动器。  

### <a name="what-characters-are-allowed-in-file-paths"></a>文件路径中允许什么字符？

MARS 代理依赖于 NTFS，并允许使用文件名/路径中[支持的字符](/windows/desktop/FileIO/naming-a-file#naming-conventions)。

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>出现警告"尚未为此服务器配置 Azure 备份"

即使您配置了备份策略，当存储在本地服务器上的备份计划设置与存储在备份保管库中的设置不同时，也会显示此警告。

* 当服务器或设置恢复为已知良好状态后，备份计划可能会变得不同步。
* 如果收到此警告，请重新[配置](backup-azure-manage-windows-server.md)备份策略，并立即运行按需备份，以便将本地服务器与 Azure 重新同步。

## <a name="manage-the-backup-cache-folder"></a>管理备份缓存文件夹

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>针对缓存文件夹的最小大小要求是什么？

缓存文件夹的大小由你正在备份的数据量确定。

* 缓存文件夹卷的可用空间应当至少为备份数据总大小的 5-10%。
* 如果卷的可用空间小于 5%，请增加卷大小，或者按照[以下步骤](#how-do-i-change-the-cache-location-for-the-mars-agent)将缓存文件夹移动到具有足够空间的卷。
* 如果备份 Windows 系统状态，则需要在包含缓存文件夹的卷中增加 30-35 GB 的可用空间。

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>如何检查临时文件夹是否有效且可访问？

1. 默认情况下，暂存文件夹位于`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
2. 确保暂存文件夹位置的路径与下面所示的注册表键条目的值匹配：

    | 注册表路径 | 注册表项 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新缓存文件夹位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新缓存文件夹位置* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>如何更改 MARS 代理的缓存位置？

1. 通过在权限提升的命令提示符下运行以下命令来停止备份引擎：

    ```Net stop obengine```
2. 如果已配置系统状态备份，则打开磁盘管理并卸载具有格式`"CBSSBVol_<ID>"`名称的磁盘。
3. 默认情况下，临时文件夹位于`\Program Files\Microsoft Azure Recovery Services Agent\Scratch`
4. 将整个`\Scratch`文件夹复制到具有足够空间的不同驱动器。 确保复制内容，而不是移动内容。
5. 使用新移动的暂存文件夹的路径更新以下注册表项。

    | 注册表路径 | 注册表项 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新的临时文件夹位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新的临时文件夹位置* |

6. 在提升的命令提示符下重启备份引擎：

    ```command
    Net stop obengine

    Net start obengine
    ```

7. 运行按需备份。 在使用新位置成功完成备份后，可以删除原始缓存文件夹。

### <a name="where-should-the-cache-folder-be-located"></a>缓存文件夹应当位于何处？

不建议缓存文件夹的以下位置：

* 网络共享/可移动媒体：缓存文件夹必须是需要使用联机备份进行备份的服务器的本地文件夹。 不支持网络位置或可移动媒体（如 USB 驱动器）。
* 脱机卷：缓存文件夹必须联机才能使用 Azure 备份代理进行预期备份

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>缓存文件夹是否有任何不受支持的属性？

缓存文件夹不支持以下属性或其组合：

* 加密
* 已删除重复数据
* Compressed
* 稀疏
* 重分析点

缓存文件夹和元数据 VHD 没有 Azure 备份代理的必要属性。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>是否有办法调整用于备份的带宽量？

是的，可以使用 MARS 代理中的“更改属性”**** 选项来调整带宽和定时。 [了解详细信息](backup-windows-with-mars-agent.md#enable-network-throttling)。

## <a name="restore"></a>还原

### <a name="manage"></a>管理

**如果我忘记了密码，我可以恢复吗？**
Azure 备份代理需要密码（您在注册期间提供）才能在还原期间解密备份数据。 查看以下方案以了解处理丢失密码的选项：

| 原始机器 <br> *（执行备份的源计算机）* | 通行短语 | 可用选项 |
| --- | --- | --- |
| 可用 |Lost |如果原始计算机（执行备份的位置）可用，并且仍注册在同一恢复服务保管库中，则可以按照[以下步骤](https://docs.microsoft.com/azure/backup/backup-azure-manage-mars#re-generate-passphrase)重新生成密码。  |
| Lost |Lost |无法恢复数据或数据不可用 |

考虑以下情况：

* 如果在同一原始计算机上卸载和重新注册代理，
  * *相同的密码，* 然后您将能够恢复备份的数据。
  * *不同的密码*，那么你将无法恢复备份的数据。
* 如果将代理安装在*其他计算机上*，
  * *相同的密码（* 在原始计算机中使用），然后您将能够还原备份的数据。
  * *不同的密码，* 您将无法还原备份的数据。
* 如果原始计算机已损坏（阻止您通过 MARS 控制台重新生成密码），但您可以还原或访问 MARS 代理使用的原始暂存文件夹，则您可能能够还原（如果您忘记了密码）。 如需更多帮助，请联系客户支持。

**如果我丢失了原始计算机（备份所在的位置），如何恢复？**

如果原始计算机具有相同的密码（注册期间提供），则可以将备份的数据还原到备用计算机。 查看以下方案以了解还原选项。

| 原始机器 | 通行短语 | 可用选项 |
| --- | --- | --- |
| Lost |可用 |您可以在另一台计算机上安装和注册 MARS 代理，其密码与注册原始计算机时提供的相同密码相同。 选择 **"恢复选项** > **""其他位置**以执行还原。 有关详细信息，请参阅[本文](https://docs.microsoft.com/azure/backup/backup-azure-restore-windows-server#use-instant-restore-to-restore-data-to-an-alternate-machine)。
| Lost |Lost |无法恢复数据或数据不可用 |


### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果取消正在进行的还原作业，会发生什么情况？

如果取消正在进行的还原作业，则还原过程会停止。 在取消之前还原的所有文件将保留在已配置的目标位置（原始位置或备用位置），而不进行任何回退。

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>MARS 代理是否备份和恢复在文件、文件夹和卷上设置的 ACL？

* MARS 代理备份在文件、文件夹和卷上设置的 ACL
* 对于卷还原恢复选项，MARS 代理提供了一个选项，用于跳过对正在恢复的文件或文件夹还原 ACL 权限
* 对于单个文件和文件夹恢复选项，MARS 代理将使用 ACL 权限进行还原（没有跳过 ACL 还原的选项）。

## <a name="next-steps"></a>后续步骤

[了解](tutorial-backup-windows-server-to-azure.md)如何备份 Windows 计算机。
