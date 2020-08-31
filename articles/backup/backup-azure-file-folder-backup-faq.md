---
title: Microsoft Azure 恢复服务 (MARS) 代理– FAQ
description: 解决与使用 Azure 备份对文件和文件夹进行备份相关的常见问题。
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: ca2753e4d6da7e2e3079f234a3facac27fd5f098
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144458"
---
# <a name="frequently-asked-questions---microsoft-azure-recovery-services-mars-agent"></a>常见问题-Microsoft Azure 恢复服务 (MARS) 代理

本文解答了有关将数据备份到 [Azure 备份](backup-overview.md) 服务中的 MICROSOFT AZURE 恢复服务 (MARS) 代理的常见问题。

## <a name="configure-backups"></a>配置备份

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>可以从何处下载最新版本的 MARS 代理？

备份 Windows Server 计算机、System Center DPM 和 Microsoft Azure 备份服务器时使用的最新 MARS 代理可供[下载](https://aka.ms/azurebackup_agent)。

### <a name="where-can-i-download-the-vault-credentials-file"></a>在哪里可以下载保管库凭据文件？

在 Azure 门户中，导航到保管库的 " **属性** "。 在 " **备份凭据**" 下，选中 " **已使用最新的恢复服务代理**" 复选框。 选择“下载”。

![下载凭据](./media/backup-azure-file-folder-backup-faq/download-credentials.png)

### <a name="how-long-are-vault-credentials-valid"></a>保管库凭据的有效期是多长时间？

保管库凭据会在 10 天后过期。 如果凭据文件过期，请重新从 Azure 门户下载该文件。

### <a name="what-characters-are-allowed-for-the-passphrase"></a>密码允许使用哪些字符？

该通行短语应使用 ASCII 字符集中的字符，其 [ascii 值小于或等于 127](https://docs.microsoft.com/office/vba/language/reference/user-interface-help/character-set-0127)。

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>可以从哪些驱动器备份文件和文件夹？

无法备份以下类型的驱动器/卷：

* 可移动媒体：所有备份项源必须报告为“固定”。
* 只读卷：为使卷影复制服务 (VSS) 起作用，卷必须是可写的。
* 脱机卷：为使 VSS 起作用，卷必须是联机的。
* 网络共享：卷必须位于需要使用联机备份进行备份的服务器本地。
* BitLocker 保护的卷：必须先解锁卷，才能备份。
* 文件系统标识：NTFS 是受支持的唯一文件系统。

### <a name="what-file-and-folder-types-are-supported"></a>支持哪些文件和文件夹类型？

[详细了解](backup-support-matrix-mars-agent.md#supported-file-types-for-backup)支持备份的文件和文件夹类型。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>是否可以使用 MARS 代理在 Azure VM 上备份文件和文件夹？  

是的。 Azure 备份使用 Azure VM 代理的 VM 扩展为 Azure VM 提供 VM 级备份。 如果你希望在 VM 的来宾 Windows 操作系统上备份文件和文件夹，可以安装 MARS 代理来执行该操作。

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>是否可以使用 MARS 代理在 Azure VM 的临时存储上备份文件和文件夹？

是的。 安装 MARS 代理，将来宾 Windows 操作系统上的文件和文件夹备份到临时存储。

* 擦除临时存储数据后，备份作业会失败。
* 如果临时存储数据被删除，则你只能还原到非易失性存储。

### <a name="how-do-i-register-a-server-to-another-region"></a>如何将服务器注册到其他区域？

备份数据会发送到服务器注册到的保管库的数据中心。 更改数据中心的最简便方法是卸载并重新安装代理，然后将计算机注册到所需区域中的新保管库。

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>MARS 代理是否支持 Windows Server 2012 重复数据删除？

是的。 MARS 代理在准备备份操作时会将消除了重复的数据转换为常规数据。 然后，它将对数据进行优化以便备份、对数据进行加密，然后将已加密的数据发送到保管库。

### <a name="do-i-need-administrator-permissions-to-install-and-configure-the-mars-agent"></a>是否需要管理员权限才能安装和配置 MARS 代理？

是的，使用 MARS 控制台安装 MARS 代理和配置备份要求用户是受保护服务器上的本地管理员。

## <a name="manage-backups"></a>管理备份

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>如果我重命名了为备份配置的 Windows 计算机，将会发生什么情况？

重命名 Windows 计算机时，所有当前已配置的备份都将停止。

* 你需要向备份保管库注册新的计算机名称。
* 向保管库注册新名称时，第一个操作是“完整”备份。
* 如果需要恢复备份到采用旧服务器名称的保管库的数据，请使用“恢复数据”向导中用于还原到其他位置的选项。 [了解详细信息](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>用于备份的最大文件路径长度是多少？

MARS 代理依赖于 NTFS，并使用受 [Windows API](/windows/win32/FileIO/naming-a-file#fully-qualified-vs-relative-paths) 限制的文件路径长度规范。 如果你要保护的文件长于所允许的值，请备份父文件夹或磁盘驱动器。  

### <a name="what-characters-are-allowed-in-file-paths"></a>文件路径中允许什么字符？

MARS 代理依赖于 NTFS，并允许使用文件名/路径中[支持的字符](/windows/win32/FileIO/naming-a-file#naming-conventions)。

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>出现“尚未为此服务器配置 Azure 备份”警告

当本地服务器上存储的备份计划设置与备份保管库中存储的设置不同时，可能会出现此警告，即使你已配置了备份策略也是如此。

* 当服务器或设置恢复为已知良好状态后，备份计划可能会变得不同步。
* 如果收到此警告，请重新[配置](backup-azure-manage-windows-server.md)备份策略，并立即运行按需备份，以便将本地服务器与 Azure 重新同步。

## <a name="manage-the-backup-cache-folder"></a>管理备份缓存文件夹

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>针对缓存文件夹的最小大小要求是什么？

缓存文件夹的大小决定了要备份的数据量。

* 缓存文件夹卷的可用空间应当至少为备份数据总大小的 5-10%。
* 如果卷的可用空间少于 5%，请增大卷大小，或遵循[这些步骤](#how-do-i-change-the-cache-location-for-the-mars-agent)将缓存文件夹移到具有足够空间的卷。
* 如果备份 Windows 系统状态，则在包含缓存文件夹的卷上将需要额外的 30-35 GB 可用空间。

### <a name="how-to-check-if-scratch-folder-is-valid-and-accessible"></a>如何检查暂存文件夹是否有效并可访问？

1. 默认情况下，暂存文件夹位于 `\Program Files\Microsoft Azure Recovery Services Agent\Scratch` 中
2. 请确保暂存文件夹的路径与下面显示的注册表项的值相符：

    | 注册表路径 | 注册表项 | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新缓存文件夹位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新缓存文件夹位置* |

### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>如何更改 MARS 代理的缓存位置？

1. 通过在权限提升的命令提示符下运行以下命令来停止备份引擎：

    ```Net stop obengine```
2. 如果已配置系统状态备份，请打开“磁盘管理”，然后卸载名称采用 `"CBSSBVol_<ID>"` 格式的磁盘。
3. 默认情况下，暂存文件夹位于 `\Program Files\Microsoft Azure Recovery Services Agent\Scratch` 中
4. 将整个 `\Scratch` 文件夹复制到具有足够空间的另一驱动器。 请务必复制内容，而不是移动内容。
5. 使用最近移动的暂存文件夹的路径更新以下注册表项。

    | 注册表路径 | 注册表项 | Value |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新暂存文件夹的位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新暂存文件夹的位置* |

6. 在提升的命令提示符下重启备份引擎：

    ```command
    Net stop obengine

    Net start obengine
    ```

7. 运行按需备份。 在使用新位置成功完成备份后，可以删除原始缓存文件夹。

### <a name="where-should-the-cache-folder-be-located"></a>缓存文件夹应当位于何处？

建议不要将缓存文件夹放在以下位置：

* 网络共享/可移动媒体：缓存文件夹必须位于需要使用联机备份进行备份的服务器本地。 不支持网络位置或可移动媒体，例如 U 盘。
* 脱机卷：缓存文件夹必须联机才能使用 Azure 备份代理执行预期的备份

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>缓存文件夹是否有任何不受支持的属性？

缓存文件夹不支持以下属性或其组合：

* 加密
* 已删除重复数据
* 压缩
* 稀疏
* 重分析点

缓存文件夹和元数据 VHD 没有 Azure 备份代理所需的属性。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>是否有办法调整用于备份的带宽量？

是的，可以使用 MARS 代理中的“更改属性”选项来调整带宽和定时。 [了解详细信息](backup-windows-with-mars-agent.md#enable-network-throttling)。

## <a name="restore"></a>还原

### <a name="manage"></a>管理

#### <a name="can-i-recover-if-i-forgot-my-passphrase"></a>如果我忘记了通行短语，是否可以恢复？

在还原期间，Azure 备份代理需要通过通行短语（在注册期间提供）解密备份的数据。 请查看以下方案，了解用于处理通行短语丢失问题的选项：

| 原始计算机 <br> *（创建备份的源计算机）* | 通行短语 | 可用选项 |
| --- | --- | --- |
| 可用 |丢失 |如果原始计算机（在其中创建备份的计算机）可用且仍在同一个恢复服务保管库中注册，则你可以遵循[这些步骤](./backup-azure-manage-mars.md#re-generate-passphrase)重新生成通行短语。  |
| 丢失 |丢失 |无法恢复数据或数据不可用 |

考虑以下情况：

* 如果你在同一台计算机上卸载并重新注册该代理，
  * *相同的密码*，则可以还原已备份的数据。
  * *不同密码*，则无法还原已备份的数据。
* 如果在 *其他计算机* 上安装代理，
  * 原始计算机)  (使用*相同的密码*，则可以还原已备份的数据。
  * *不同密码*，则无法还原已备份的数据。
* 如果原始计算机已损坏（阻止你通过 MARS 控制台重新生成通行短语），但你可以还原或访问 MARS 代理使用的原始暂存文件夹，则你也许可以还原（如果忘记了密码）。 若要获取更多帮助，请联系客户支持人员。

#### <a name="how-do-i-recover-if-i-lost-my-original-machine-where-backups-were-taken"></a>如果丢失了原始计算机（在其中创建备份的计算机），如何予以恢复？

如果你持有原始计算机的相同通行短语（在注册期间提供的通行短语），可将备份的数据还原到备用计算机。 请查看以下方案了解还原选项。

| 原始计算机 | 通行短语 | 可用选项 |
| --- | --- | --- |
| 丢失 |可用 |可以在另一台计算机上安装并注册 MARS 代理，该计算机包含你在注册原始计算机期间提供的相同通行短语。 选择“恢复选项” > “另一位置”执行还原。  有关详细信息，请参阅[此文](./backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)。
| 丢失 |丢失 |无法恢复数据或数据不可用 |

### <a name="my-backup-jobs-have-been-failing-or-not-running-for-a-long-time-im-past-the-retention-period-can-i-still-restore"></a>我的备份作业运行失败或运行时间过长。 我的保留期已过。 是否仍可以还原？

作为一种安全措施，Azure 备份将保留最新的恢复点，即使该恢复点超过保留期也是如此。 备份恢复和全新恢复点变为可用后，将根据指定的保留期删除旧的恢复点。

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>如果取消正在进行的还原作业，会发生什么情况？

如果取消正在进行的还原作业，则还原过程会停止。 在取消之前还原的所有文件将保留在已配置的目标位置（原始位置或备用位置），而不进行任何回退。

### <a name="does-the-mars-agent-back-up-and-restore-acls-set-on-files-folders-and-volumes"></a>MARS 代理是否可以备份和还原针对文件、文件夹和卷设置的 ACL？

* MARS 代理可以备份针对文件、文件夹和卷上设置的 ACL
* 对于卷还原恢复选项，MARS 代理将提供一个选项，用于跳过还原所要恢复的文件或文件夹的 ACL 权限。
* 对于 "单个文件和文件夹" 恢复选项，MARS 代理将用 ACL 权限还原 (没有用于跳过 ACL 还原) 的选项。

## <a name="next-steps"></a>后续步骤

[了解](tutorial-backup-windows-server-to-azure.md)如何备份 Windows 计算机。
