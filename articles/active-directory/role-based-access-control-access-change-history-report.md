---
title: "访问报告 - Azure RBAC | Microsoft Docs"
description: "生成一个列出过去 90 天内使用基于角色的访问控制对 Azure 订阅的访问权限进行的所有更改的报告。"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 2bc68595-145e-4de3-8b71-3a21890d13d9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/17/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e8028ab43ed02ef0c0a1374326b07f72f97d9d9
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="create-an-access-report-for-role-based-access-control"></a>为基于角色的访问控制创建访问报告
无论何时授予或撤销订阅中的访问权限，更改都将记录在 Azure 事件中。 可创建访问权限更改历史记录报告来查看过去 90 天的所有更改。

## <a name="create-a-report-with-azure-powershell"></a>使用 Azure PowerShell 创建报告
若要在 PowerShell 中创建访问权限更改历史记录报告，请使用 [Get-AzureRMAuthorizationChangeLog](/powershell/module/azurerm.resources/get-azurermauthorizationchangelog) 命令。

调用此命令时，可指定要列出的分配的属性，包括以下方面：

| 属性 | 说明 |
| --- | --- |
| **Action** |是授予还是撤销访问权限 |
| **Caller** |负责访问权限更改的所有者 |
| **PrincipalId** | 已分配了角色的用户、组或应用程序的唯一标识符 |
| **PrincipalName** |用户、组或应用程序的名称 |
| **PrincipalType** |分配是针对用户、组还是应用程序 |
| **RoleDefinitionId** |已授予或已撤销角色的 GUID |
| **RoleName** |已授予或已撤销的角色 |
| **范围** | 分配应用到的订阅、资源组或资源的唯一标识符 | 
| **ScopeName** |订阅、资源组或资源的名称 |
| **ScopeType** |分配是在订阅、资源组中还是在资源范围内 |
| **Timestamp** |访问权限更改的日期和时间 |

此示例命令列出过去 7 天内订阅中所有访问权限的更改：

```
Get-AzureRMAuthorizationChangeLog -StartTime ([DateTime]::Now - [TimeSpan]::FromDays(7)) | FT Caller,Action,RoleName,PrincipalType,PrincipalName,ScopeType,ScopeName
```

![PowerShell Get-AzureRMAuthorizationChangeLog - 屏幕快照](./media/role-based-access-control-configure/access-change-history.png)

## <a name="create-a-report-with-azure-cli"></a>使用 Azure CLI 创建报告
若要在 Azure 命令行界面 (CLI) 中创建访问权限更改历史记录报告，请使用 `azure role assignment changelog list` 命令。

## <a name="export-to-a-spreadsheet"></a>导出到电子表格
要保存报告或处理数据，请将访问权限更改导出至 .csv 文件。 可在电子表格中查看该报告以进行审阅。

![更改日志被视为电子表格 - 屏幕快照](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## <a name="next-steps"></a>后续步骤
* 使用 [Azure RBAC 中的自定义角色](role-based-access-control-custom-roles.md)
* 了解如何[使用 PowerShell 管理 Azure RBAC](role-based-access-control-manage-access-powershell.md)

