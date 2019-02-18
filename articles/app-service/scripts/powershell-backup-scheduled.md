---
title: Azure PowerShell 脚本示例 - 为 Web 应用创建计划备份 | Microsoft Docs
description: Azure PowerShell 脚本示例 - 为 Web 应用创建计划备份
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1fe21e8c7a4f4b73648eb5cf0cd3ee5e96dff760
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56107585"
---
# <a name="create-a-scheduled-backup-for-a-web-app-using-powershell"></a>使用 PowerShell 为 Web 应用创建计划备份

此示例脚本使用其相关资源，在应用服务中创建 Web 应用，然后为其创建计划备份。 

必要时，请使用 [Azure PowerShell 指南](/powershell/azure/overview)中的说明安装 Azure PowerShell，并运行 `Connect-AzAccount` 创建与 Azure 的连接。 

## <a name="sample-script"></a>示例脚本

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

## <a name="clean-up-deployment"></a>清理部署 

运行脚本示例后，可以使用以下命令删除资源组、Web 应用以及所有相关资源。

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>脚本说明

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | 创建存储帐户。 |
| [New-AzStorageContainer](/powershell/module/azure.storage/new-AzStoragecontainer) | 创建 Azure 存储容器。 |
| [New-AzStorageContainerSASToken](/powershell/module/azure.storage/new-AzStoragecontainersastoken) | 生成 Azure 存储容器的 SAS 令牌。 |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | 创建应用服务计划。 |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 创建 Web 应用。 |
| [Edit-AzWebAppBackupConfiguration](/powershell/module/az.websites/edit-azwebappbackupconfiguration) | 编辑 Web 应用的备份配置。 |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | 获取 Web 应用的备份列表。 |
| [Get-AzWebAppBackupConfiguration](/powershell/module/az.websites/get-azwebappbackupconfiguration) | 获取 Web 应用的备份配置。 |

## <a name="next-steps"></a>后续步骤

有关 Azure PowerShell 模块的详细信息，请参阅 [Azure PowerShell 文档](/powershell/azure/overview)。

可以在 [Azure PowerShell 示例](../samples-powershell.md)中找到 Azure 应用服务 Web 应用的其他 Azure Powershell 示例。
