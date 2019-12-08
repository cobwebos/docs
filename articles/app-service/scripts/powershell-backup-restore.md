---
title: PowerShell：还原应用备份
description: 了解如何使用 Azure PowerShell 自动部署和管理应用服务。 此示例演示如何从备份还原应用。
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 026c275ce4bc85ff3f964bfbb175cc01ff611df0
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685771"
---
# <a name="restore-a-web-app-from-a-backup-using-azure-powershell"></a>使用 PowerShell 从备份中还原 Web 应用

此示例脚本从现有的 Web 应用中检索以前已完成的备份，然后通过重写其内容将其还原。 

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount` 创建与 Azure 的连接。 

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore/backup-restore.ps1?highlight=1-2 "Restore a web app from a backup")]

## <a name="clean-up-deployment"></a>清理部署 

如果不再需要 Web 应用，请使用以下命令删除资源组、Web 应用和所有相关的资源。

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | 获取 Web 应用的备份列表。 |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | 从以前完成的备份中还原 Web 应用。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
