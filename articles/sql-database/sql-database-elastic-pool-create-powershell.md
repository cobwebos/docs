---
title: "使用 PowerShell 创建新的弹性数据库池 | Microsoft Docs"
description: "了解如何通过创建可缩放的弹性数据库池，使用 PowerShell 向外缩放 Azure SQL 数据库资源以管理多个数据库。"
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 37a707ee-9223-43ae-8c35-1ccafde8b83e
ms.service: sql-database
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 05/27/2016
ms.author: srinia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: daf8bd6421ae563e542b0874a6e7748a3ca52738


---
# <a name="create-a-new-elastic-database-pool-with-powershell"></a>使用 PowerShell 创建新的弹性数据库池
> [!div class="op_single_selector"]
> * [Azure 门户](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
> 
> 

了解如何使用 PowerShell cmdlet 创建 [弹性数据库池](sql-database-elastic-pool.md) 。 

有关常见的错误代码，请参阅 [SQL 数据库客户端应用程序的 SQL 错误代码：数据库连接错误和其他问题](sql-database-develop-error-messages.md)。

> [!NOTE]
> 弹性池在所有 Azure 区域中均已正式发布 (GA)，但美国中北部和印度西部除外，这些区域当前仅发布了预览版。  将尽快在这些区域中正式发布弹性池。 此外，弹性池当前不支持使用 [内存中 OLTP 或内存中分析](sql-database-in-memory.md)的数据库。
> 
> 

你需要运行 Azure PowerShell 1.0 或更高版本。 有关详细信息，请参阅 [如何安装和配置 Azure PowerShell](../powershell-install-configure.md)。

## <a name="create-a-new-pool"></a>创建新池
[New-AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378\(v=azure.300\).aspx) cmdlet 可创建新池。 每个池的 eDTU 值、最小和最大 DTU 受服务器层值（基本、标准或高级）的约束。 请参阅 [弹性池和弹性数据库的 eDTU 和存储限制](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases)。

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>在池中创建新的弹性数据库
使用 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) cmdlet 并将 **ElasticPoolName** 参数设置为目标池。 要将现有数据库移动到池，请参阅 [将数据库移动到弹性池](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool)。

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>创建池，并使用多个新数据库对其进行填充
使用门户或每次只能创建一个单一数据库的 PowerShell cmdlet 在池中创建大量数据库可能需要一段时间。 若要自动创建到新池中，请参阅 [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae)。   

## <a name="example-create-a-pool-using-powershell"></a>示例：使用 PowerShell 创建池
此脚本创建新的 Azure 资源组和新的服务器。 出现提示时，提供新服务器的管理员用户名和密码（不是你的 Azure 凭据）。

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>后续步骤
* [管理你的池](sql-database-elastic-pool-manage-powershell.md)
* [创建弹性作业](sql-database-elastic-jobs-overview.md) 弹性作业使你可以根据池中数据库的数目运行 T-SQL 脚本。
* [使用 Azure SQL 数据库扩展](sql-database-elastic-scale-introduction.md)：使用弹性数据库工具扩展。




<!--HONumber=Nov16_HO2-->


