---
title: 备份 Azure Stack | Microsoft Docs
description: 在 Azure Stack 上使用备份就地执行按需备份。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 578bb864f56b788db77d1201533e73d3b9616669
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "42139747"
---
# <a name="back-up-azure-stack"></a>备份 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

在 Azure Stack 上使用备份就地执行按需备份。 有关配置 PowerShell 环境的说明，请参阅[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 若要登录到 Azure Stack，请参阅[在 Azure Stack 中使用管理员门户](azure-stack-manage-portals.md)。

## <a name="start-azure-stack-backup"></a>启动 Azure Stack 备份

### <a name="start-a-new-backup-without-job-progress-tracking"></a>启动新的备份，而无需跟踪作业进度
使用 Start-azsbackup 立即开始新的备份跟踪没有作业进度。

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Azure Stack 备份开始跟踪作业进度
使用 Start-azsbackup 与要跟踪备份作业进度的-AsJob 变量启动新的备份。

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob 
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " `
    + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) `
    -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output

    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

## <a name="confirm-backup-has-completed"></a>确认备份已完成

### <a name="confirm-backup-has-completed-using-powershell"></a>确认备份已完成使用 PowerShell
使用以下 PowerShell 命令以确保该备份已成功完成：

```powershell
   Get-AzsBackup
```

结果应类似于以下输出：

```powershell
    BackupDataVersion : 1.0.1
    BackupId          : <backup ID>
    RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
    Status            : Succeeded
    CreatedDateTime   : 7/6/2018 6:46:24 AM
    TimeTakenToCreate : PT20M32.364138S
    DeploymentID      : <deployment ID>
    StampVersion      : 1.1807.0.41
    OemVersion        : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
    Name              : local/<local name>
    Type              : Microsoft.Backup.Admin/backupLocations/backups
    Location          : local
    Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administration-portal"></a>确认备份已完成在管理门户中
使用 Azure Stack 管理门户以验证该备份已成功完成通过执行以下步骤：

1. 打开[Azure Stack 管理门户](azure-stack-manage-portals.md)。
2. 选择“更多服务” > “基础结构备份”。 在“基础结构备份”边栏选项卡中选择“配置”。
3. 在“可用备份”列表中查找备份的**名称**和**完成日期**。
4. 验证**状态**是否为“成功”。

## <a name="next-steps"></a>后续步骤

详细了解从数据丢失事件恢复的工作流程。 请参阅[从灾难性数据丢失中恢复](azure-stack-backup-recover-data.md)。
