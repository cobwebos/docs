---
title: 备份 Azure Stack | Microsoft Docs
description: 在 Azure Stack 上使用备份就地执行按需备份。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/08/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: c9e7ffae1b988d0940d10acdb1b387a25e0466ec
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242880"
---
# <a name="back-up-azure-stack"></a>备份 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

在 Azure Stack 上使用备份就地执行按需备份。 有关配置 PowerShell 环境的说明，请参阅[安装适用于 Azure Stack 的 PowerShell](azure-stack-powershell-install.md)。 若要登录到 Azure Stack，请参阅[配置操作员环境并登录到 Azure Stack](azure-stack-powershell-configure-admin.md)。

## <a name="start-azure-stack-backup"></a>启动 Azure Stack 备份

使用 Start-azsbackup 启动新的备份-AsJob 变量来跟踪进度。 

```powershell
    $backupjob = Start-AzsBackup -Force -AsJob
    "Start time: " + $backupjob.PSBeginTime;While($backupjob.State -eq "Running"){("Job is currently: " + $backupjob.State+" ;Duration: " + (New-TimeSpan -Start ($backupjob.PSBeginTime) -End (Get-Date)).Minutes);Start-Sleep -Seconds 30};$backupjob.Output
```

## <a name="confirm-backup-completed-via-powershell"></a>确认已通过 PowerShell 完成备份

```powershell
    if($backupjob.State -eq "Completed"){Get-AzsBackup | where {$_.BackupId -eq $backupjob.Output.BackupId}}
```

- 结果应类似于以下输出：

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

## <a name="confirm-backup-completed-in-the-administration-portal"></a>在管理门户中确认已完成的备份

1. 打开 Azure Stack 管理门户 ([https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external))。
2. 选择“更多服务” > “基础结构备份”。 在“基础结构备份”边栏选项卡中选择“配置”。
3. 在“可用备份”列表中查找备份的**名称**和**完成日期**。
4. 验证**状态**是否为“成功”。

## <a name="next-steps"></a>后续步骤

- 详细了解从数据丢失事件恢复的工作流程。 请参阅[从灾难性数据丢失中恢复](azure-stack-backup-recover-data.md)。