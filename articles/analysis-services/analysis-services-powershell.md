---
title: 使用 PowerShell 管理 Azure Analysis Services | Microsoft Docs
description: 使用 PowerShell 管理 Azure Analysis Services。
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 07/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: bfe1bfc2828347e34fa92564c26d005998e14ece
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294737"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>使用 PowerShell 管理 Azure Analysis Services

本文介绍用于执行 Azure Analysis Services 服务器和数据管理任务的 PowerShell cmdlet。 

服务器资源管理任务，如创建或删除服务器、暂停或恢复服务器操作，或使用 Azure Analysis Services cmdlet 更改服务级别（层级）。 用于管理数据库的其他任务（如添加或删除角色成员、处理或分区）使用与 SQL Server Analysis Services 相同的 SqlServer 模块中包含的 cmdlet。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>权限

大多数 PowerShell 任务要求用户在所管理的 Analysis Services 服务器上具有管理员权限。 已计划的 PowerShell 任务是无人参与操作。 运行计划程序的帐户或服务主体必须具有对 Analysis Services 服务器的管理权限。 

对于使用 Azure PowerShell cmdlet 的服务器操作，你的帐户或运行计划程序的帐户还必须属于 [Azure 基于角色的访问控制 (RBAC)](../role-based-access-control/overview.md) 中资源的所有者角色。 

## <a name="resource-and-server-operations"></a>资源和服务器操作 

安装模块 - [Az.AnalysisServices](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
文档 - [Az.AnalysisServices reference](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>数据库操作

Azure Analysis Services 数据库操作使用与 SQL Server Analysis Services 相同的 SqlServer 模块。 但是，Azure Analysis Services 并非支持所有 cmdlet。 

SqlServer 模块提供任务特定的数据库管理 cmdlet，以及接受表格模型脚本语言 (TMSL) 查询或脚本的常规用途 Invoke-ASCmd cmdlet。 Azure Analysis Services 支持 SqlServer 模块中的以下 cmdlet。

安装模块 - [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
文档 - [SqlServer 参考](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>受支持的 cmdlet

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

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [下载 SQL Server PowerShell 模块](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [下载 SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [PowerShell 库中的 SqlServer 模块](https://www.powershellgallery.com/packages/SqlServer)    
* [Tabular Model Programming for Compatibility Level 1200 and higher](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)（适用于兼容级别 1200 或更高级别的表格模型编程）
