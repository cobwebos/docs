---
title: "PowerShell：将 Azure SQL 数据库导出到 BACPAC 文件 | Microsoft Docs"
description: "使用 PowerShell 将 Azure SQL 数据库导出到 BACPAC 文件"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 9439dd83-812f-4688-97ea-2a89a864d1f3
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: 162147607baa36de0487cebc06e7ada20f3dd0c0


---
# <a name="export-an-azure-sql-database-or-a-sql-server-to-a-bacpac-file-by-using-powershell"></a>使用 PowerShell 将 Azure SQL 数据库或 SQL Server 导出到 BACPAC 文件

本文说明了如何使用 PowerShell 将 Azure SQL 数据库或 SQL Server 数据库导出到 BACPAC 文件（位于 Azure Blob 存储）。 有关导出到 BACPAC 文件的概述，请参阅[导出到 BACPAC](sql-database-export.md)。

> [!NOTE]
> 还可使用 [Azure 门户](sql-database-export-portal.md)、[SQL Server Management Studio](sql-database-export-ssms.md) 或 [SQLPackage](sql-database-export-sqlpackage.md) 将 Azure SQL 数据库文件导出到 BACPAC 文件。
>

## <a name="prerequisites"></a>先决条件

若要完成本文，需要以下各项：

* Azure 订阅。
* Azure SQL 数据库。
* [Azure 标准存储帐户](../storage/storage-create-storage-account.md)，具有可在标准存储中存储 BACPAC 的 blob 容器。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="export-your-database"></a>导出数据库
[New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx) cmdlet 向服务提交导出数据库请求。 根据数据库的大小，导出操作可能需要一些时间才能完成。

> [!IMPORTANT]
> 若要确保获得事务处理一致的 BACPAC 文件，应首先[创建数据库的副本](sql-database-copy-powershell.md)，然后导出该数据库副本。
> 
> 

     $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password


## <a name="monitor-the-progress-of-the-export-operation"></a>监视导出操作的进度
运行 [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx) 后，可运行 [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx) 来查看请求的状态。 如果请求后立即运行，通常会返回“**状态: 处理中**”。 显示“状态: 成功”时，表示导出完毕。

    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink



## <a name="export-sql-database-example"></a>导出 SQL 数据库示例
以下示例将现有 SQL 数据库导出到 BACPAC，随后显示如何查看导出操作的状态。

若要运行示例，需将几个变量替换为数据库和存储帐户中的特定值。 在 [Azure 门户](https://portal.azure.com)中，浏览到存储帐户，以获取存储帐户名称、blob 容器名称和密钥值。 可单击存储帐户边栏选项卡上的“**访问密钥**”查找密钥。

将以下 `VARIABLE-VALUES` 替换为特定 Azure 资源中的值。 数据库名称就是要导出的现有数据库。

    $subscriptionId = "YOUR AZURE SUBSCRIPTION ID"

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    # Database to export
    $DatabaseName = "DATABASE-NAME"
    $ResourceGroupName = "RESOURCE-GROUP-NAME"
    $ServerName = "SERVER-NAME"
    $serverAdmin = "ADMIN-NAME"
    $serverPassword = "ADMIN-PASSWORD" 
    $securePassword = ConvertTo-SecureString -String $serverPassword -AsPlainText -Force
    $creds = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $serverAdmin, $securePassword

    # Generate a unique filename for the BACPAC
    $bacpacFilename = $DatabaseName + (Get-Date).ToString("yyyyMMddHHmm") + ".bacpac"

    # Storage account info for the BACPAC
    $BaseStorageUri = "https://STORAGE-NAME.blob.core.windows.net/BLOB-CONTAINER-NAME/"
    $BacpacUri = $BaseStorageUri + $bacpacFilename
    $StorageKeytype = "StorageAccessKey"
    $StorageKey = "YOUR STORAGE KEY"

    $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
       -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
       -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
    $exportRequest

    # Check status of the export
    Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink

## <a name="automate-export-using-azure-automation"></a>使用 Azure 自动化自动导出

Azure SQL 数据库自动导出现在处于预览状态，将在 2017 年 3 月 1 日停用。 从 2016 年 12 月 1 日开始，你将无法再对任何 SQL 数据库配置自动导出。 所有现有自动导出作业将继续正常运行，直到 2017 年 3 月 1 日。 2016 年 12 月 1 日之后，你可以使用[长期备份保留](sql-database-long-term-retention.md)或 [Azure 自动化](../automation/automation-intro.md)，以根据所选计划定期使用 PowerShell 存档 SQL 数据库。 对于示例脚本，可以从 [Github](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) 下载示例脚本。 


## <a name="next-steps"></a>后续步骤
* 若要了解如何使用 Powershell 导入 Azure SQL 数据库，请参阅[使用 PowerShell 导入 BACPAC](sql-database-import-powershell.md)。
* 若要了解如何使用 SQLPackage 导入 BACPAC，请参阅[使用 SqlPackage 将 BACPCAC 导入到 Azure SQL 数据库](sql-database-import-sqlpackage.md)
* 若要了解如何使用 Azure 门户导入 BACPAC，请参阅[使用 Azure 门户将 BACPCAC 导入到 Azure SQL 数据库](sql-database-import-portal.md)
* 有关对于整个 SQL Server 数据库迁移进程（包括性能建议）的讨论，请参阅[将 SQL Server 数据库迁移到 Azure SQL 数据库](sql-database-cloud-migrate.md)。
* 若要了解 Azure SQL 数据库备份的长期备份保留 - 作为导出数据库进行存档的替代方法，请参阅[长期备份保留](sql-database-long-term-retention.md)
* 若要了解如何将 BACPAC 导入到 SQL 数据库，请参阅[将 BACPCAC 导入 SQL 数据库](https://msdn.microsoft.com/library/hh710052.aspx)



## <a name="additional-resources"></a>其他资源
* [New-AzureRmSqlDatabaseExport](https://msdn.microsoft.com/library/azure/mt707796\(v=azure.300\).aspx)
* [Get-AzureRmSqlDatabaseImportExportStatus](https://msdn.microsoft.com/library/azure/mt707794\(v=azure.300\).aspx)




<!--HONumber=Feb17_HO2-->


