---
title: "使用 PowerShell 管理 Azure SQL 数据库 | Microsoft Docs"
description: "使用 PowerShell 管理 Azure SQL 数据库。"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: monicar
ms.assetid: 3f21ad5e-ba99-4010-b244-5e5815074d31
ms.service: sql-database
ms.custom: overview
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/15/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: adad6b8e27e0996559d5e6dacb8dd60fbf52a631
ms.openlocfilehash: 0c1ce1c29e447d9db4ef0df7873ef89cb835abee


---
# <a name="managing-azure-sql-database-using-powershell"></a>使用 PowerShell 管理 Azure SQL 数据库
> [!div class="op_single_selector"]
> * [Azure 门户](sql-database-manage-portal.md)
> * [Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
> * [PowerShell](sql-database-manage-powershell.md)
> 
> 

本主题介绍用于执行许多 Azure SQL 数据库任务的 PowerShell cmdlet。 如需完整列表，请参阅 [Azure SQL 数据库 Cmdlet](https://msdn.microsoft.com/library/mt574084\(v=azure.300\).aspx)。

> [!TIP]
> 若需通过教程来了解如何创建服务器、创建基于服务器的防火墙、查看服务器属性、连接和查询 master 数据库、创建示例数据库和空数据库、查询数据库属性、连接和查询示例数据库，请参阅[入门教程](sql-database-get-started-powershell.md)。
>

## <a name="how-do-i-create-a-resource-group"></a>如何创建一个资源组？
若要为 SQL 数据库和相关的 Azure 资源创建资源组，请使用 [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt759837\(v=azure.300\).aspx) cmdlet。

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

有关详细信息，请参阅[将 Azure PowerShell 与 Azure Resource Manager 配合使用](../powershell-azure-resource-manager.md)。
如需完整教程，请参阅[通过 Azure PowerShell 实现 Azure SQL 数据库服务器、数据库和防火墙规则入门](sql-database-get-started-powershell.md)。

## <a name="how-do-i-create-a-sql-database-server"></a>如何创建 SQL 数据库服务器？
若要创建 SQL 数据库服务器，请使用 [New-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603715\(v=azure.300\).aspx) cmdlet。 将 *server1* 替换为服务器的名称。 服务器名称必须在所有 Azure SQL 数据库服务器中都是唯一的。 如果服务器名称已使用，将出现错误。 此命令可能需要几分钟才能完成。 资源组必须已存在于订阅中。

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

有关服务器的详细信息，请参阅 [SQL 数据库功能](sql-database-features.md)。 如需完整教程，请参阅[通过 Azure PowerShell 实现 Azure SQL 数据库服务器、数据库和防火墙规则入门](sql-database-get-started-powershell.md)。

## <a name="how-do-i-create-a-sql-database-server-firewall-rule"></a>如何创建 SQL 数据库服务器防火墙规则？
若要创建防火墙规则以访问服务器，请使用 [New-AzureRmSqlServerFirewallRule](https://msdn.microsoft.com/library/azure/mt603860\(v=azure.300\).aspx) cmdlet。 运行以下命令，将开始和结束 IP 地址替换为客户端的有效值。 资源组和服务器必须已存在于订阅中。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

若要允许其他 Azure 服务访问服务器，请创建防火墙规则，将 `-StartIpAddress`和 `-EndIpAddress` 都设置为 **0.0.0.0**。 这个特殊的防火墙规则允许所有 Azure 流量访问服务器。

有关详细信息，请参阅 [Azure SQL 数据库防火墙](https://msdn.microsoft.com/library/azure/ee621782.aspx)。 如需完整教程，请参阅[通过 Azure PowerShell 实现 Azure SQL 数据库服务器、数据库和防火墙规则入门](sql-database-get-started-powershell.md)。

## <a name="how-do-i-create-a-sql-database"></a>如何创建 SQL 数据库？
若要创建 SQL 数据库，请使用 [New-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339\(v=azure.300\).aspx) cmdlet。 资源组和服务器必须已存在于订阅中。 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

有关详细信息，请参阅 [什么是 SQL 数据库](sql-database-technical-overview.md)。 如需完整教程，请参阅[通过 Azure PowerShell 实现 Azure SQL 数据库服务器、数据库和防火墙规则入门](sql-database-get-started-powershell.md)。

## <a name="how-do-i-change-the-performance-level-of-a-sql-database"></a>如何更改 SQL 数据库的性能级别？
若要更改性能级别，请使用 [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) cmdlet 对增加或减少数据库。 资源组、服务器和数据库必须已存在于订阅中。 对于“基本”层，请将 `-RequestedServiceObjectiveName` 设置为单个空格（如以下代码段所示）。 将其设置为 *S0*、*S1*、*P1*、*P6*，等等，如其他层的前述示例所示。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

有关详细信息，请参阅 [SQL 数据库选项和性能：了解每个服务层提供的功能](sql-database-service-tiers.md)。 如需脚本示例，请参阅[用于更改 SQL 数据库的服务层和性能级别的示例 PowerShell 脚本](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database)。

## <a name="how-do-i-copy-a-sql-database-to-the-same-server"></a>如何将 SQL 数据库复制到同一服务器？
若要将 SQL 数据库复制到同一服务器，请使用 [New-AzureRmSqlDatabaseCopy](https://msdn.microsoft.com/library/azure/mt603644\(v=azure.300\).aspx) cmdlet。 将 `-CopyServerName` 和 `-CopyResourceGroupName` 设置为与源数据库服务器和资源组相同的值。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

有关详细信息，请参阅[复制 Azure SQL 数据库](sql-database-copy.md)。 如需脚本示例，请参阅[复制 SQL 数据库 PowerShell 脚本](sql-database-copy-powershell.md#example-powershell-script)。

## <a name="how-do-i-delete-a-sql-database"></a>如何删除 SQL 数据库？
若要删除 SQL 数据库， 请使用 [Remove-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619368\(v=azure.300\).aspx) cmdlet。 资源组、服务器和数据库必须已存在于订阅中。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-delete-a-sql-database-server"></a>如何删除 SQL 数据库服务器？
若要删除 SQL 数据库服务器，请使用 [Remove-AzureRmSqlServer](https://msdn.microsoft.com/library/azure/mt603488\(v=azure.300\).aspx) cmdlet。

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="how-do-i-create-and-manage-elastic-pools-using-powershell"></a>如何使用 PowerShell 创建和管理弹性池？
如需详细了解如何使用 PowerShell 创建弹性池，请参阅[使用 PowerShell 创建新的弹性池](sql-database-elastic-pool-create-powershell.md)。

如需详细了解如何使用 PowerShell 管理弹性池，请参阅[使用 PowerShell 监视和管理弹性池](sql-database-elastic-pool-manage-powershell.md)。

## <a name="related-information"></a>相关信息
* [Azure SQL 数据库 Cmdlet](https://msdn.microsoft.com/library/azure/mt574084\(v=azure.300\).aspx)
* [Azure Cmdlet 参考](https://msdn.microsoft.com/library/azure/dn708514\(v=azure.300\).aspx)




<!--HONumber=Dec16_HO3-->


