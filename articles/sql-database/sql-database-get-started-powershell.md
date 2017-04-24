---
title: "Azure PowerShell：创建 SQL 数据库 | Microsoft Docs"
description: "了解如何在 Azure 门户中创建 SQL 数据库逻辑服务器、服务器级防火墙规则和数据库。"
keywords: "SQL 数据库教程：创建 SQL 数据库"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start create
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: hero-article
ms.date: 04/17/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: bb8fcd907a03350dc21106944e72e6f06109b5f6
ms.lasthandoff: 04/19/2017

---

# <a name="create-a-single-azure-sql-database-using-powershell"></a>使用 PowerShell 创建单一 Azure SQL 数据库

PowerShell 用于从命令行或脚本创建和管理 Azure 资源。 本指南详述了如何使用 PowerShell 在 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)的 [Azure SQL 数据库逻辑服务器](sql-database-features.md)中部署 Azure SQL 数据库。

若要完成本教程，请确保已安装最新的 [Azure PowerShell](/powershell/azureps-cmdlets-docs)。 

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-azure"></a>登录 Azure

使用 [Add-AzureRmAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.profile/v2.5.0/add-azurermaccount) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Add-AzureRmAccount
```

## <a name="create-variables"></a>创建变量

定义在本快速入门的脚本中使用的变量。

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The logical server name: Use a random value or replace with your own value (do not capitalize)
$servername = "server-$(Get-Random)"
# Set an admin login and password for your database
# The login information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.1"
# The database name
$databasename = "mySampleDatabase"
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) 命令创建 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。 以下示例在 `westeurope` 位置创建名为 `myResourceGroup` 的资源组。

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>创建逻辑服务器

使用 [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) 命令创建 [Azure SQL 数据库逻辑服务器](sql-database-features.md)。 逻辑服务器包含一组作为组管理的数据库。 以下示例使用管理员用户名 `ServerAdmin` 和密码 `ChangeYourAdminPassword1` 在资源组中创建随机命名的服务器。 根据需要替换这些预定义的值。

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>配置服务器防火墙规则

使用 [New-AzureRmSqlServerFirewallRule](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserverfirewallrule) 命令创建 [Azure SQL 数据库服务器级防火墙规则](sql-database-firewall-configure.md)。 服务器级防火墙规则允许外部服务器（例如 SQL Server Management Studio 或 SQLCMD 实用程序）通过 SQL 数据库服务防火墙连接到 SQL 数据库。 在以下示例中，防火墙仅对其他 Azure 资源开放。 若要启用外部连接，请将 IP 地址更改为适合你环境的地址。 若要开放所有 IP 地址，请使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束地址。

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> 通过端口 1433 进行的 SQL 数据库通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则无法连接到 Azure SQL 数据库服务器，除非你的 IT 部门打开了端口 1433。
>

## <a name="create-a-blank-database"></a>创建空数据库

使用 [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) 命令在服务器中创建 [S0 性能级别](sql-database-service-tiers.md)的空 SQL 数据库。 以下示例创建一个名为 `mySampleDatabase` 的数据库。 根据需要替换此预定义的值。

```powershell
New-AzureRmSqlDatabase  -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName databasename `
    -RequestedServiceObjectiveName "S0"
```

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他快速入门教程是在本文的基础上制作的。 如果计划继续使用后续的快速入门或相关教程，请勿清除在本快速入门中创建的资源。 如果不打算继续，请使用以下命令删除通过本快速入门创建的所有资源。

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>后续步骤

- 若要使用 SQL Server Management Studio 进行连接和查询，请参阅[使用 SSMS 进行连接和查询](sql-database-connect-query-ssms.md)
- 若要使用 Visual Studio Code 进行连接和查询，请参阅[使用 Visual Studio Code 进行连接和查询](sql-database-connect-query-vscode.md)。
- 若要使用 .NET 进行连接和查询，请参阅[使用 .NET 进行连接和查询](sql-database-connect-query-dotnet.md)。
- 若要使用 PHP 进行连接和查询，请参阅[使用 PHP 进行连接和查询](sql-database-connect-query-php.md)。
- 若要使用 Node.js 进行连接和查询，请参阅[使用 Node.js 进行连接和查询](sql-database-connect-query-nodejs.md)。
- 若要使用 Java 进行连接和查询，请参阅[使用 Java 进行连接和查询](sql-database-connect-query-java.md)。
- 若要使用 Python 进行连接和查询，请参阅[使用 Python 进行连接和查询](sql-database-connect-query-python.md)。
- 若要使用 Ruby 进行连接和查询，请参阅[使用 Ruby 进行连接和查询](sql-database-connect-query-ruby.md)。

