---
title: "备份 Azure 堆栈 |Microsoft 文档"
description: "使用就地备份 Azure 堆栈上执行按需备份。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9565DDFB-2CDB-40CD-8964-697DA2FFF70A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 955b286967ca2bc8450e8988ec16c6a5c352aa8a
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2018
---
# <a name="back-up-azure-stack"></a>备份 Azure 堆栈

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

使用就地备份 Azure 堆栈上执行按需备份。 如果你需要启用基础结构备份服务，请参阅[从管理门户的 Azure 堆栈启用备份](azure-stack-backup-enable-backup-console.md)。

> [!Note]  
>  Azure 堆栈工具包含**开始 AzSBackup** cmdlet。 有关安装工具的说明，请参阅[获取启动并运行 Azure 堆栈中的 PowerShell](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart)。

## <a name="start-azure-stack-backup"></a>启动 Azure 堆栈的备份

使用提升的提示符在运算符管理环境中，打开 Windows PowerShell 并运行以下命令：

```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1

    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
    Start-AzSBackup -Location $location.Name
```

## <a name="confirm-backup-completed-in-the-administration-portal"></a>确认在管理门户中完成的备份

1. 打开 Azure 堆栈管理门户在[https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external)。
2. 选择**更多的服务** > **基础结构备份**。 选择**配置**中**基础结构备份**边栏选项卡。
3. 查找**名称**和**完成日期**中的备份**可用备份**列表。
4. 验证**状态**是**Succeeded**。

你还可以确认已完成从管理门户的备份。 导航到`\MASBackup\<datetime>\<backupid>\BackupInfo.xml`

## <a name="next-steps"></a>后续步骤

- 了解有关数据丢失事件从恢复的工作流的详细信息。 请参阅[从灾难性数据丢失中恢复](azure-stack-backup-recover-data.md)。
