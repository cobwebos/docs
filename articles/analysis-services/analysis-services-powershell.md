---
title: "使用 PowerShell 管理 Azure Analysis Services | Microsoft Docs"
description: "使用 PowerShell 管理 Azure Analysis Services。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2017
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: dbe1dba78fb2597dc595f9e6895d0a15181c1f05
ms.lasthandoff: 04/27/2017


---

# <a name="manage-azure-analysis-services-with-powershell"></a>使用 PowerShell 管理 Azure Analysis Services

本文介绍用于执行 Azure Analysis Services 服务器和数据管理任务的 PowerShell cmdlet。 

服务器管理任务，如创建或删除服务器、挂起或恢复服务器操作，或更改服务级别（层），都要使用 Azure Resource Manager (AzureRM) cmdlet。 用于管理数据库的其他任务（如添加或删除角色成员、处理或分区）在 [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) 模块中使用与 SQL Server Analysis Services 所用相同的 cmdlet。

## <a name="permissions"></a>权限
大多数 PowerShell 任务要求用户在所管理的 Analysis Services 服务器上具有管理员权限。 已计划的 PowerShell 任务是无人参与操作。 运行计划程序的帐户必须具有对 Analysis Services 服务器的管理特权。 

对于使用 AzureRm cmdlet 进行的服务器操作，你的帐户或运行计划程序的帐户还必须属于 [Azure 基于角色的访问控制 (RBAC)](../active-directory/role-based-access-control-what-is.md) 中资源的所有者角色。 

## <a name="server-operations"></a>服务器操作 
Azure Analysis Services cmdlet 包含在 [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices) 组件模块中。 若要安装 AzureRM cmdlet 模块，请参阅 PowerShell 库中的 [Azure Resource Manager cmdlet](/powershell/azure/overview)。

|Cmdlet|说明| 
|------------|-----------------| 
|[Get-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/get-azurermanalysisservicesserver)|获取服务器实例的详细信息。|  
|[New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver)|创建新的服务器实例。|
|[Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/remove-azurermanalysisservicesserver)|删除服务器实例。|  
|[Suspend-AzureRmAnalysisServicesServe](/powershell/module/azurerm.analysisservices/suspend-azurermanalysisservicesserver)|挂起服务器实例。| 
|[Resume-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/resume-azurermanalysisservicesserver)|恢复服务器实例。|  
|[Set-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver)|修改服务器实例。|   
|[Test-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/test-azurermanalysisservicesserver)|测试服务器实例的存在。| 

## <a name="database-operations"></a>数据库操作
Azure Analysis Services 数据库操作使用与 SQL Server Analysis Services 相同的 [SQLASCMDLETS](https://msdn.microsoft.com/library/hh758425.aspx) 模块。 但是，Azure Analysis Services 并非支持所有 cmdlet。 

SQLASCMDLETS 模块提供任务特定的数据库管理 cmdlet，以及接受表格模型脚本语言 (TMSL) 查询或脚本的常规用途 Invoke-ASCmd cmdlet。 Azure Analysis Services 支持 SQLASCMDLETS 模块中的以下 cmdlet。
  
|Cmdlet|说明|
|------------|-----------------| 
|[Add-RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|向数据库角色添加成员。| 
|[Backup-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/backup-asdatabase-cmdlet)|备份 Analysis Services 数据库。|  
|[Remove-RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|从数据库角色删除成员。|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|执行 TMSL 脚本。|
|[Invoke-ProcessASDatabase](https://msdn.microsoft.com/library/mt651773.aspx)|处理数据库。|  
|[Invoke-ProcessPartition](https://msdn.microsoft.com/library/hh510164.aspx)|处理分区。| 
|[Invoke-ProcessTable](https://msdn.microsoft.com/library/mt651774.aspx)|处理表。|  
|[Merge-Partition](https://msdn.microsoft.com/library/hh479576.aspx)|合并分区。|  
|[Restore-ASDatabase](https://docs.microsoft.com/sql/analysis-services/powershell/restore-asdatabase-cmdlet)|还原 Analysis Services 数据库。| 
  

## <a name="related-information"></a>相关信息
* [PowerShell scripting in Analysis Services](https://msdn.microsoft.com/library/hh213141.aspx)（Analysis Services 中的 PowerShell 脚本）。
* [Tabular Model Programming for Compatibility Level 1200](https://msdn.microsoft.com/library/mt712541.aspx)（适用于兼容级别 1200 的表格模型编程）

