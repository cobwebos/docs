---
title: 快速入门：创建 Azure SQL 数据仓库 - Azure PowerShell | Microsoft Docs
description: 使用 Azure PowerShell 快速创建 SQL 数据库逻辑服务器、服务器级防火墙规则和数据仓库。
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: e0bb014ec0706d458ff2f38e409efba5d66aaf18
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="quickstart-create-and-query-an-azure-sql-data-warehouse-with-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建和查询 Azure SQL 数据仓库

使用 Azure PowerShell 快速创建 Azure SQL 数据仓库。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

本教程需要 Azure PowerShell 模块版本 5.1.1 或更高版本。 运行 `Get-Module -ListAvailable AzureRM` 查找当前版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。 


> [!NOTE]
> 创建 SQL 数据仓库可能会导致新的计费服务。  有关详细信息，请参阅 [Azure SQL 数据仓库定价](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)。
>
>

## <a name="log-in-to-azure"></a>登录 Azure

使用 [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) 命令登录到 Azure 订阅，并按照屏幕上的说明进行操作。

```powershell
Add-AzureRmAccount
```

若要查看正在使用的订阅，请运行 [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription)。

```powershell
Get-AzureRmSubscription
```

如果需要使用与默认订阅不同的订阅，请运行 [Select-AzureRmSubscription](/powershell/module/azurerm.profile/select-azurermsubscription)。

```powershell
Select-AzureRmSubscription -SubscriptionName "MySubscription"
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
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehosue"
```

## <a name="create-a-resource-group"></a>创建资源组

使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令创建 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。 以下示例在 `westeurope` 位置创建名为 `myResourceGroup` 的资源组。

```powershell
New-AzureRmResourceGroup -Name $resourcegroupname -Location $location
```
## <a name="create-a-logical-server"></a>创建逻辑服务器

使用 [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) 命令创建 [Azure SQL 逻辑服务器](../sql-database/sql-database-servers-databases.md#what-is-an-azure-sql-logical-server)。 逻辑服务器包含一组作为组管理的数据库。 以下示例使用管理员用户名 `ServerAdmin` 和密码 `ChangeYourAdminPassword1` 在资源组中创建随机命名的服务器。 根据需要替换这些预定义的值。

```powershell
New-AzureRmSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-firewall-rule"></a>配置服务器防火墙规则

使用 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule) 命令创建 [Azure SQL 服务器级防火墙规则](../sql-database/sql-database-firewall-configure.md)。 服务器级防火墙规则允许外部服务器（例如 SQL Server Management Studio 或 SQLCMD 实用程序）通过 SQL 数据仓库服务防火墙连接到 SQL 数据仓库。 在以下示例中，防火墙仅对其他 Azure 资源开放。 要启用外部连接，请将 IP 地址更改为适合你环境的地址。 若要开放所有 IP 地址，请使用 0.0.0.0 作为起始 IP 地址，使用 255.255.255.255 作为结束地址。

```powershell
New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL 数据库和 SQL 数据仓库通过端口 1433 进行通信。 如果尝试从企业网络内部进行连接，则该网络的防火墙可能不允许经端口 1433 的出站流量。 如果是这样，则将无法连接到 Azure SQL 服务器，除非 IT 部门开放了端口 1433。
>


## <a name="create-a-data-warehouse-with-sample-data"></a>使用示例数据创建数据仓库
此示例使用以前定义的变量创建数据仓库。  它将服务目标指定为 DW400，这是针对数据仓库的低成本起点。 

```Powershell
New-AzureRmSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW400" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

所需的参数有：

* **RequestedServiceObjectiveName**：请求的[数据仓库单位](what-is-a-data-warehouse-unit-dwu-cdwu.md)的数量。 增加此数量会增加计算成本。 有关支持值的列表，请参阅[内存和并发限制](memory-and-concurrency-limits.md)。
* **DatabaseName**：要创建的 SQL 数据仓库的名称。
* **ServerName**：用于创建过程的服务器名称。
* **ResourceGroupName**：要使用的资源组。 若要查找订阅中可用的资源，请使用 Get-AzureResource。
* **Edition**：必须是“DataWarehouse”才能创建 SQL 数据仓库。

可选参数有：

- **CollationName**：在不指定的情况下，默认排序规则是 SQL_Latin1_General_CP1_CI_AS。 在数据库上不能更改排序规则。
- **MaxSizeBytes**：数据库的默认最大大小为 10 GB。

有关参数选项的详细信息，请参阅 [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)。


## <a name="clean-up-resources"></a>清理资源

本系列中的其他快速入门教程是在本快速入门的基础上制作的。 

> [!TIP]
> 如果打算继续使用后续的快速入门教程，请不要清除在本快速入门中创建的资源。 如果不打算继续，请在 Azure 门户中执行以下步骤来删除此快速入门创建的所有资源。
>

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>后续步骤

现已创建数据仓库、防火墙规则、已连接到数据仓库，并运行了几个查询。 若要了解有关 Azure SQL 数据仓库的详细信息，请继续有关加载数据的教程。
> [!div class="nextstepaction"]
>[将数据加载到 SQL 数据仓库](load-data-from-azure-blob-storage-using-polybase.md)
