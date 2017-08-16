
---
title: "Azure 备份代理常见问题解答 | Microsoft Docs"
description: "以下常见问题的解答：Azure 备份代理工作原理、备份和保留限制。"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
keywords: "备份和灾难恢复;备份服务"
ms.assetid: 778c6ccf-3e57-4103-a022-367cc60c411a
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/18/2017
ms.author: trinadhk;pullabhk;
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: b9d022c780b618825bb2416f3834e4ada280092f
ms.contentlocale: zh-cn
ms.lasthandoff: 07/20/2017

---

# <a name="questions-about-the-azure-backup-agent"></a>有关 Azure 备份代理的问题
本文提供常见问题的解答，有助于快速了解 Azure 备份代理组件。 某些答案提供内含全面信息的文章的链接。 你也可以在 [论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中发布有关 Azure 备份服务的问题。

## <a name="configure-backup"></a>配置备份
### <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>我可以在哪里下载最新的 Azure 备份代理？ <br/>
可以从 [此处](http://aka.ms/azurebackup_agent)下载最新的代理，用于备份 Windows Server、System Center DPM 或 Windows 客户端。 如果你想要备份虚拟机，请使用 VM 代理（会自动安装适当的扩展）。 从 Azure 资源库创建的虚拟机上已有 VM 代理。

### <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>在配置 Azure 备份代理时，系统提示我输入保管库凭据。 保管库凭据会过期吗？
是的，保管库凭据在 48 小时后过期。 如果文件过期，请登录 Azure 门户，然后从保管库下载保管库凭据文件。

### <a name="what-types-of-drives-can-i-back-up-files-and-folders-from-br"></a>可以从哪些类型的驱动器备份文件和文件夹？ <br/>
无法备份以下驱动器/卷：

* 可移动介质：所有备份项源必须报告为“固定”。
* 只读卷：为使卷影复制服务 (VSS) 起作用，卷必须是可写的。
* 脱机卷：为使 VSS 起作用，卷必须是联机的。
* 网络共享：若要使用联机备份进行备份，卷对于服务器而言必须是本地的。
* Bitlocker 保护的卷：必须先解锁卷，然后才能备份。
* 文件系统标识：NTFS 是受支持的唯一文件系统。

### <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>可以从我的服务器备份哪些文件和文件夹类型？<br/>
支持以下类型：

* 加密
* 压缩
* 稀疏
* 压缩 + 稀疏
* 硬链接：不支持，跳过
* 重分析点：不支持，跳过
* 加密 + 稀疏：不支持，跳过
* 压缩流：不支持，跳过
* 稀疏流：不支持，跳过

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>可以在已由 Azure 备份服务备份的 Azure VM 上使用 VM 扩展来安装 Azure 备份代理吗？ <br/>
绝对是。 Azure 备份使用 VM 扩展为 Azure VM 提供 VM 级别备份。 若要保护来宾 OS 上的文件和文件夹，请在来宾 Windows OS 上安装 Azure 备份代理。

### <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>可以在 Azure VM 上安装 Azure 备份代理来备份 Azure VM 提供的临时存储中存在的文件和文件夹吗？ <br/>
是的。 可以在来宾 Windows OS 上安装 Azure 备份代理，并将文件和文件夹备份到临时存储。 擦除临时存储数据后，备份作业将会失败。 此外，如果临时存储数据已被删除，则你只能还原到非易失性存储。

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>针对缓存文件夹的最小大小要求是什么？ <br/>
缓存文件夹的大小由你正在备份的数据量确定。 缓存文件夹应是数据存储所需空间的 5%。

### <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>如何将我的服务器注册到其他数据中心？<br/>
备份数据会发送到它所注册到的保管库的数据中心。 更改数据中心的最简便方法是卸载代理，然后将代理安装并注册到属于所需数据中心的新保管库。

### <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>Azure 备份代理是否适用于使用 Windows Server 2012 删除重复功能的服务器？ <br/>
是的。 代理服务在准备备份操作时将消除了重复的数据转换为常规数据。 然后，它将对数据进行优化以便备份、对数据进行加密，然后将已加密的数据发送到联机备份服务。

## <a name="backup"></a>备份
### <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>如何更改为 Azure 备份代理指定的缓存位置？<br/>
请使用以下列表来更改缓存位置。

1. 通过在权限提升的命令提示符下运行以下命令来停止备份引擎：

    ```PS C:\> Net stop obengine``` 
  
2. 请不要移动文件， 而是将缓存空间文件夹复制到具有足够空间的其他驱动器。 确认备份使用新的缓存空间后，可以删除原始缓存空间。
3. 更新以下注册表项，使路径指向新的缓存空间文件夹。<br/>

    | 注册表路径 | 注册表项 | 值 |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*新缓存文件夹位置* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*新缓存文件夹位置* |

4. 通过在提升的命令提示符下执行以下命令来重新启动备份引擎：

    ```PS C:\> Net start obengine```

在新缓存位置成功完成创建备份后，可以删除原始缓存文件夹。


### <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>可以将缓存文件夹放在何处，以便 Azure 备份代理按预期工作？<br/>
不建议将缓存文件夹放在以下位置：

* 网络共享或可移动媒体：缓存文件夹必须位于需要使用联机备份进行备份的服务器本地。 不支持网络位置或可移动媒体，例如 U 盘。
* 脱机卷：缓存文件夹必须联机才能使用 Azure 备份代理执行预期的备份。

### <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>缓存文件夹是否有任何不受支持的属性？<br/>
缓存文件夹不支持以下属性或其组合：

* 加密
* 已删除重复数据
* 压缩
* 稀疏
* 重分析点

缓存文件夹和元数据 VHD 没有 Azure 备份代理所需的属性。

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>是否有办法调整备份服务所用的带宽？<br/>
  是的，可以使用备份代理中的“更改属性”  选项来调整带宽。 可以调整带宽以及使用该带宽的时间。 有关分步说明，请参阅**[启用网络限制](backup-configure-vault.md#enable-network-throttling)**。

## <a name="manage-backups"></a>管理备份
### <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>如果重命名了用于将数据备份到 Azure 的 Windows 服务器，会发生什么情况？<br/>
当你重命名服务器时，所有当前配置的备份都将停止。
将向备份保管库注册服务器的新名称。 向保管库注册新名称时，第一个备份操作是*完全*备份。 如果需要恢复备份到采用旧服务器名称的保管库的数据，可以使用“恢复数据”向导中的[**其他服务器**](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine)选项。

### <a name="what-is-the-maximum-file-path-length-that-can-be-specified-in-backup-policy-using-azure-backup-agent-br"></a>可以使用 Azure 备份代理在备份策略中指定的文件路径的最大长度是多少？ <br/>
Azure 备份代理依赖于 NTFS。 [可以指定的文件路径的长度受限于 Windows API](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths)。 如果要保护的文件的文件路径超过 Windows API 允许的长度，可以备份父文件夹或磁盘驱动器。  

### <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>使用 Azure 备份代理的 Azure 备份策略的文件路径中允许哪些字符？ <br>
 Azure 备份代理依赖于 NTFS。 允许使用 [NTFS 支持的字符](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) 作为文件规范的一部分。 
 
### <a name="i-receive-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-configured-a-backup-policy-br"></a>即使配置了备份策略，仍会显示警告“尚未为此服务器配置 Azure 备份” <br/>
在本地服务器上存储的备份计划设置与备份保管库中存储的设置不同时，可能会出现此警告。 服务器或设置恢复为已知良好状态后，备份计划可能会失去同步。 如果收到此警告，请 [重新配置备份策略](backup-azure-manage-windows-server.md) ，然后 **立即运行备份** ，以便将本地服务器与 Azure 重新同步。

