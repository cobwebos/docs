---
title: "备份 Azure 文件常见问题解答"
description: "本文详述如何在 Azure 中保护 Azure 文件。 此摘要显示在搜索结果中。"
services: backup
keywords: "未咨询 SEO 专家的情况下，请不要添加或编辑关键字。"
author: markgalioto
ms.author: markgal
ms.date: 2/21/2018
ms.topic: tutorial
ms.service: backup
ms.workload: storage-backup-recovery
manager: carmonm
ms.openlocfilehash: 3d09914c93d0f48b8f6bed405202682aaf925a5f
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/24/2018
---
# <a name="questions-about-backing-up-azure-files"></a>有关如何备份 Azure 文件的问题
本文回答了有关如何备份 Azure 文件的常见问题。 某些答案提供内含全面信息的文章的链接。 也可以在 [论坛](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup)中发布有关 Azure 备份服务的问题。

若要快速浏览本文的各个部分，请使用右侧“本文内容”下的链接。

## <a name="configuring-the-backup-job-for-azure-files"></a>为 Azure 文件配置备份作业

### <a name="why-cant-i-see-some-of-my-storage-accounts-i-want-to-protect-that-contain-valid-file-shares-br"></a>为什么我看不到需要保护的部分存储帐户？这些帐户中包含的文件共享都是有效的。 <br/>
在预览期间，Azure 文件共享的备份并不支持所有类型的存储帐户。 请参阅[此处](troubleshoot-azure-files.md#preview-boundaries)的列表，了解一系列受支持的存储帐户。

### <a name="why-cant-i-see-some-of-my-file-shares-in-the-storage-account-when-im-trying-to-configure-backup-br"></a>尝试配置备份时，为什么看不到存储帐户中的部分文件共享？ <br/>
检查是否已在同一恢复服务保管库中对文件共享进行保护，或者已在最近将其删除。

### <a name="why-cant-i-protect-file-shares-connected-to-a-sync-group-in-azure-files-sync-br"></a>为什么无法保护已连接到 Azure 文件同步中的某个同步组的文件共享？ <br/>
保护连接到同步组的 Azure 文件共享这一功能为“有限预览”功能。 若要请求访问权限，请向 [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) 发送邮件并附上你的订阅 ID。 

### <a name="in-which-geos-can-i-back-up-azure-file-shares-br"></a>可以在哪些地理区域备份 Azure 文件共享？ <br/>
Azure 文件共享备份目前为预览版，只在以下地理区域提供： 
-   澳大利亚东南部 (ASE) 
- 巴西南部 (BRS)
- 加拿大中部 (CNC)
-   加拿大东部 (CE)
-   美国中部 (CUS)
-   东亚 (EA)
-   东澳大利亚 (AE) 
-   美国东部 (EUS)
-   美国东部 2 (EUS2)
-   印度中部 (INC) 
-   美国中北部 (NCUS) 
-   北欧 (NE) 
-   美国中南部 (SCUS) 
-   东南亚 (SEA)
-   英国南部 (UKS) 
-   英国西部 (UKW) 
-   西欧 (WE) 
-   美国西部 (WUS)
-   美国中西部 (WCUS)
-   美国西部 2 (WUS 2)

如果需要在上面没有列出的特定地理区域使用，请向 [AskAzureBackupTeam@microsoft.com](email:askazurebackupteam@microsoft.com) 发送邮件。

### <a name="how-many-file-shares-can-i-protect-in-a-vaultbr"></a>可以在保管库中保护多少文件共享？<br/>
在预览期间，一个保管库中最多可以保护 25 个存储帐户的文件共享。 也可在单个保管库中保护多达 200 个文件共享。 

## <a name="backup"></a>备份

### <a name="how-many-on-demand-backups-can-i-take-per-file-share-br"></a>每个文件共享可以进行多少个按需备份？ <br/>
在任何时间点，最多可以有一个文件共享的 200 个快照。 此限制包括由 Azure 备份根据策略的定义创建的快照。 如果在达到此限制后无法进行备份，请删除按需还原点，以便将来能够成功地进行备份。

### <a name="after-enabling-virtual-networks-on-my-storage-account-the-backup-of-file-shares-in-the-account-started-failing-why"></a>在存储帐户上启用虚拟网络以后，就无法在帐户中备份文件共享。 为什么？
Azure 文件共享的备份不支持启用了虚拟网络的存储帐户。 在存储帐户中禁用虚拟网络即可成功地进行备份。 

## <a name="restore"></a>还原

### <a name="can-i-recover-from-a-deleted-file-share-br"></a>能否从已删除的文件共享进行恢复？ <br/>
删除文件共享时，系统会显示也会被删除的备份的列表，并会要求你进行确认。 无法还原已删除的文件共享。

### <a name="can-i-restore-from-backups-if-i-stopped-protection-on-a-file-share-br"></a>在停止对文件共享进行保护的情况下，是否能从备份还原？ <br/>
是的。 如果在停止保护时选择了“保留备份数据”，则可从所有现有的还原点还原。

## <a name="manage-backup"></a>管理备份

### <a name="can-i-access-the-snapshots-taken-by-azure-backups-and-mount-it-br"></a>能否访问 Azure 备份生成的快照并将其装载？ <br/>
可以访问 Azure 备份生成的所有快照，只需在门户、PowerShell 或 CLI 中查看快照即可。 可以使用[此处](../storage/files/storage-how-to-use-files-snapshots.md#mount-a-file-share)的过程装载它们。

### <a name="what-is-the-maximum-retention-i-can-configure-for-backups-br"></a>可以为备份配置的最长保留期是多长？ <br/>
Azure 文件共享备份允许保留每日备份长达 120 天。

### <a name="what-happens-when-i-change-the-backup-policy-for-a-file-share-br"></a>更改文件共享的备份策略时，会发生什么情况？ <br/>
在文件共享上应用新策略时，将遵循新策略的计划和保留期。 如果延长保留期，则会对现有的恢复点进行标记，按新策略要求保留它们。 如果缩短保留期，则会将其标记为在下一清理作业中删除，随后会将其删除。


## <a name="see-also"></a>另请参阅
本文的信息只是讲述如何备份 Azure 文件，若要详细了解 Azure 备份的其他领域，请选择性地参阅下面的其他备份常见问题解答：
-  [恢复服务保管库常见问题解答](backup-azure-backup-faq.md)
-  [Azure VM 备份常见问题解答](backup-azure-vm-backup-faq.md)
-  [Azure 备份代理常见问题解答](backup-azure-file-folder-backup-faq.md)
