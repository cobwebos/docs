---
title: 使用 PowerShell 管理 Azure Analysis Services | Microsoft Docs
description: 使用 PowerShell 管理 Azure Analysis Services。
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 12/19/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 177d74a54e4ab4de698cbb63091656cc8b584e2b
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010678"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>使用 PowerShell 管理 Azure Analysis Services

本文介绍用于执行 Azure Analysis Services 服务器和数据管理任务的 PowerShell cmdlet。 

服务器管理任务，如创建或删除服务器、挂起或恢复服务器操作，或更改服务级别（层），都要使用 Azure 资源管理器（资源）cmdlet 和 Analysis Services（服务器）cmdlet。 用于管理数据库的其他任务（如添加或删除角色成员、处理或分区）使用与 SQL Server Analysis Services 相同的 SqlServer 模块中包含的 cmdlet。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>权限

大多数 PowerShell 任务要求用户在所管理的 Analysis Services 服务器上具有管理员权限。 已计划的 PowerShell 任务是无人参与操作。 运行计划程序的帐户或服务主体必须具有对 Analysis Services 服务器的管理权限。 

对于使用 Azure PowerShell cmdlet 的服务器操作，你的帐户或运行计划程序的帐户还必须属于中的资源的所有者角色[azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md)。 

## <a name="resource-management-operations"></a>资源管理操作 

Module - [Az.AnalysisServices](/powershell/module/az.analysisservices)

|Cmdlet|描述| 
|------------|-----------------| 
|[Get-AzAnalysisServicesServer](/powershell/module/az.analysisservices/get-azanalysisservicesserver)|获取服务器实例的详细信息。|  
|[New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver)|创建服务器实例。|   
|[New-AzAnalysisServicesFirewallConfig](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallconfig)|创建新的 Analysis Services 防火墙配置。|   
|[New-AzAnalysisServicesFirewallRule](/powershell/module/az.analysisservices/new-azanalysisservicesfirewallrule)|创建新的 Analysis Services 防火墙规则。|   
|[Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/remove-azanalysisservicesserver)|删除服务器实例。|  
|[Resume-AzAnalysisServicesServer](/powershell/module/az.analysisservices/resume-azanalysisservicesserver)|恢复服务器实例。|  
|[Suspend-AzAnalysisServicesServer](/powershell/module/az.analysisservices/suspend-azanalysisservicesserver)|挂起服务器实例。| 
|[Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver)|修改服务器实例。|   
|[Test-AzAnalysisServicesServer](/powershell/module/az.analysisservices/test-azanalysisservicesserver)|测试服务器实例的存在。| 

## <a name="server-management-operations"></a>服务器管理操作

模块 - [Azure.AnalysisServices](https://www.powershellgallery.com/packages/Azure.AnalysisServices)

|Cmdlet|描述| 
|------------|-----------------| 
|[Add-AzAnalysisServicesAccount](/powershell/module/az.analysisservices/add-AzAnalysisServicesaccount)|添加一个经身份验证的帐户，以用于 Azure Analysis Services 服务器 cmdlet 请求。| 
|[Export-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/export-AzAnalysisServicesinstancelog)|将日志导出从 Analysis Services 服务器中当前登录的环境添加 AzAnalysisServicesAccount 命令中指定的实例|  
|[Restart-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|当前已登录的环境中; 在重新启动 Analysis Services 服务器的实例添加 AzAnalysisServicesAccount 命令中指定。|  
|[Sync-AzAnalysisServicesInstance](/powershell/module/az.analysisservices/restart-AzAnalysisServicesinstance)|同步 Analysis Services 服务器中当前登录的环境添加 AzAnalysisServicesAccount 命令中指定的所有查询横向扩展实例的指定实例上指定的数据库|  

## <a name="database-operations"></a>数据库操作

Azure Analysis Services 数据库操作与 SQL Server Analysis Services 使用相同的 [SqlServer 模块](https://www.powershellgallery.com/packages/SqlServer)。 但是，Azure Analysis Services 并非支持所有 cmdlet。 若要了解详细信息，请参阅 [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)。

SqlServer 模块提供任务特定的数据库管理 cmdlet，以及接受表格模型脚本语言 (TMSL) 查询或脚本的常规用途 Invoke-ASCmd cmdlet。 Azure Analysis Services 支持 SqlServer 模块中的以下 cmdlet。

  
|Cmdlet|描述|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|向数据库角色添加成员。| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|备份 Analysis Services 数据库。|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|从数据库角色删除成员。|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|执行 TMSL 脚本。|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|处理数据库。|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|处理分区。| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|处理表。|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|合并分区。|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|还原 Analysis Services 数据库。| 
  

## <a name="related-information"></a>相关信息

* [下载 SQL Server PowerShell 模块](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [下载 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [PowerShell 库中的 SqlServer 模块](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200 and higher](https://msdn.microsoft.com/library/mt712541.aspx)（适用于兼容级别 1200 或更高级别的表格模型编程）
