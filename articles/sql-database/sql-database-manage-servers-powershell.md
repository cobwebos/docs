---
title: "PowerShell：创建和管理 Azure SQL 数据库服务器 | Microsoft 文档"
description: "有关如何使用 PowerShell 创建和管理 Azure SQL 数据库服务器的快速参考。"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: servers
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 144774c9106bf5a0e389c99075c822d1c5282692
ms.openlocfilehash: c22243db06c783bc4f5b311aa4d49ce12032ab02


---
 
# <a name="create-and-manage-azure-sql-database-servers-with-powershell"></a>使用 PowerShell 创建和管理 Azure SQL 数据库服务器

可以使用 [Azure 门户](https://portal.azure.com/)、PowerShell、REST API 或 C# 创建和管理 Azure SQL 数据库服务器。 本主题说明如何使用 PowerShell。 有关 Azure 门户，请参阅[使用 Azure 门户创建和管理服务器](sql-database-manage-servers-portal.md)。 

## <a name="create-an-azure-sql-database-server-using-powershell"></a>使用 PowerShell 创建 Azure SQL 数据库服务器

若要创建 SQL 数据库服务器，请使用 [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqlserver) cmdlet。 将 *server1* 替换为服务器的名称。 服务器名称必须在所有 Azure SQL 数据库服务器中都是唯一的。 如果服务器名称已使用，将出现错误。 此命令可能需要几分钟才能完成。 资源组必须已存在于订阅中。

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
$creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword


$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

> [!TIP]
> 有关示例脚本，请参阅[使用 PowerShell 脚本创建 SQL 数据库](sql-database-get-started-powershell.md)。
>

## <a name="next-steps"></a>后续步骤
* 有关管理工具的概述，请参阅[管理工具概述](sql-database-manage-overview.md)
* 若要了解如何使用 Azure 门户执行管理任务，请参阅[使用 Azure 门户管理 Azure SQL 数据库](sql-database-manage-portal.md)。
* 若要了解如何使用 PowerShell 执行管理任务，请参阅[使用 PowerShell 管理 Azure SQL 数据库](sql-database-manage-powershell.md)。
* 若要了解如何使用 SQL Server Management Studio 执行其他任务，请参阅 [SQL Server Management Studio](sql-database-manage-azure-ssms.md)。
* 有关 SQL 数据库服务的信息，请参阅[什么是 SQL 数据库](sql-database-technical-overview.md)。 
* 有关 Azure 数据库服务器和数据库功能的信息，请参阅[功能](sql-database-features.md)。



<!--HONumber=Feb17_HO3-->


