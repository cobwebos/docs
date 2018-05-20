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
ms.openlocfilehash: c2a6727692a7a74b3e5fe32de8800722a9ed91b5
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2018
---
# <a name="back-up-azure-stack"></a>备份 Azure Stack

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

在 Azure Stack 上使用备份就地执行按需备份。 如果需要启用基础结构备份服务，请参阅[从管理门户为 Azure Stack 启用备份](azure-stack-backup-enable-backup-console.md)。

> [!Note]  
>  有关配置 PowerShell 环境的说明，请参阅[安装适用于 Azure 堆栈的 PowerShell ](azure-stack-powershell-install.md)。

## <a name="start-azure-stack-backup"></a>启动 Azure Stack 备份

在操作员管理环境中使用权限提升的提示符打开 Windows PowerShell，并运行以下命令：

```powershell
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>在管理门户中确认已完成的备份

1. 打开 Azure 堆栈管理门户在[ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external)。
2. 选择“更多服务” > “基础结构备份”。 在“基础结构备份”边栏选项卡中选择“配置”。
3. 在“可用备份”列表中查找备份的**名称**和**完成日期**。
4. 验证**状态**是否为“成功”。

<!-- You can also confirm the backup completed from the administration portal. Navigate to `\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

In ‘Confirm backup completed’ section, the path at the end doesn’t make sense (ie relative to what, datetime format, etc?)
\MASBackup\<datetime>\<backupid>\BackupInfo.xml -->


## <a name="next-steps"></a>后续步骤

- 详细了解从数据丢失事件恢复的工作流程。 请参阅[从灾难性数据丢失中恢复](azure-stack-backup-recover-data.md)。
