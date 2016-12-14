---
title: "使用 PowerShell 更改 Azure SQL 数据库的服务层和性能级别 | Microsoft 文档"
description: "“更改 Azure SQL 数据库的服务层和性能级别”介绍如何使用 PowerShell 扩展和缩减 SQL 数据库。 使用 PowerShell 更改 Azure SQL 数据库定价层。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 6f016c17-b048-4968-b82b-d2dcec954e54
ms.service: sql-database
ms.custom: monitor and tune; how to
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: aea3bcbd6ac73a05b00b7b79b2dc47bf06d67f6f


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-with-powershell"></a>使用 PowerShell 更改 SQL 数据库的服务层和性能级别（定价层）
> [!div class="op_single_selector"]
> * [Azure 门户](sql-database-scale-up.md)
> * [**PowerShell**](sql-database-scale-up-powershell.md)
> 
> 

服务层和性能级别描述了适用于你的 SQL 数据库的功能和资源，并且可以随着应用程序更改的需要进行更新。 有关详细信息，请参阅“[服务层](sql-database-service-tiers.md)”。

请注意，更改数据库的服务层和/或性能级别将在新的性能级别创建原始数据库的副本，然后将连接切换到副本。 当我们切换到副本时，在此过程中不会丢失任何数据，但在短暂的瞬间，将禁用与数据库的连接，因此可能回滚某些处于进行状态的事务。 此窗口会有所不同，但平均起伏少于 4 秒，而在超过 99%的情况下为不足 30 秒。 极少情况下，尤其有很大数量的事务在连接禁用的瞬间正在进行中，则此窗口停留时间可能会更长。  

整个扩展过程的持续时间同时取决于更改前后数据库的大小和服务层。 例如，一个正在更改到标准服务层、从标准服务层更改或在标准服务层内更改的 250 GB 的数据库应在 6 小时内完成。 对于与正在高级服务层内更改性能级别的大小相同的数据库，它应在 3 小时内完成。

* 若要对数据库进行降级，数据库应小于目标服务层允许的最大大小。 
* 在启用了[异地复制](sql-database-geo-replication-portal.md)的情况下升级数据库时，必须先将辅助数据库升级到所需的性能层，然后再升级主数据库。
* 从高级服务层降级时，必须先终止所有异地复制关系。 可以按照[在中断后恢复](sql-database-disaster-recovery.md)主题中所述的步骤停止主数据库与活动次要数据库之间的复制过程。
* 各服务层提供的还原服务各不相同。 如果进行降级，你可能无法再还原到某个时间点，或者备份保留期变短。 有关详细信息，请参阅 [Azure SQL 数据库备份和还原](sql-database-business-continuity.md)。
* 所做的更改完成之前不会应用数据库的新属性。

**若要完成本文，需要以下各项：**

* Azure 订阅。 如果需要 Azure 订阅，只需单击本页顶部的“免费帐户”  ，然后再返回完成本文中的相关操作即可。
* Azure SQL 数据库。 如果你没有 SQL 数据库，请按照[创建你的第一个 Azure SQL 数据库](sql-database-get-started.md)文章中的步骤创建一个。
* Azure PowerShell。

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="change-the-service-tier-and-performance-level-of-your-sql-database"></a>更改 SQL 数据库的服务层和性能级别
运行 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) cmdlet 并将 **-RequestedServiceObjectiveName** 设置为所需定价层的性能级别；例如 *S0*、*S1*、*S2*、*S3*、*P1*、*P2*...

```
$ResourceGroupName = "resourceGroupName"

$ServerName = "serverName"
$DatabaseName = "databaseName"

$NewEdition = "Standard"
$NewPricingTier = "S2"

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```






## <a name="sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database"></a>用于更改 SQL 数据库的服务层和性能级别的示例 PowerShell 脚本
将 ```{variables}``` 替换为自己的值（不要包含大括号）。

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```



## <a name="next-steps"></a>后续步骤
* [扩大和缩小](sql-database-elastic-scale-get-started.md)
* [使用 SSMS 连接和查询 SQL 数据库](sql-database-connect-query-ssms.md)
* [导出 Azure SQL 数据库](sql-database-export-powershell.md)

## <a name="additional-resources"></a>其他资源
* [业务连续性概述](sql-database-business-continuity.md)
* [SQL 数据库文档](http://azure.microsoft.com/documentation/services/sql-database/)
* [Azure SQL 数据库 Cmdlet](http://msdn.microsoft.com/library/azure/mt574084https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx.aspx)




<!--HONumber=Nov16_HO3-->


