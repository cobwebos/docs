---
title: 教程：将弹性池添加到故障转移组
description: 使用 Azure 门户、PowerShell 或 Azure CLI 将 Azure SQL 数据库弹性池添加到故障转移组。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/27/2019
ms.openlocfilehash: e2ae9afaf7c1dcc1794b90d4851fdd60298b5ad6
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823876"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>教程：将 Azure SQL 数据库弹性池添加到故障转移组

使用 Azure 门户为 Azure SQL 数据库弹性池配置故障转移组并测试故障转移。  在本教程中，将了解如何：

> [!div class="checklist"]
> - 创建 Azure SQL 数据库单一数据库。
> - 将单一数据库添加到弹性池中。 
> - 为两个逻辑 SQL server 之间的两个弹性池创建[故障转移组](sql-database-auto-failover-group.md)。
> - 测试故障转移。

## <a name="prerequisites"></a>先决条件

要完成本教程，请确保具有： 

- Azure 订阅。 如果还没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。


## <a name="1---create-a-single-database"></a>1 - 创建单一数据库 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2 - 将单一数据库添加到弹性池中
在此步骤中，你将创建一个弹性池，并向其添加你的单一数据库。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户创建弹性池。 


1. 在 Azure 门户的左侧菜单中选择“Azure SQL”。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择“+ 添加”以打开“选择 SQL 部署选项”页。 通过选择 "数据库" 磁贴上的 "显示详细信息"，可以查看有关不同数据库的其他信息。
1. 从 " **SQL 数据库**" 磁贴的 "**资源类型**" 下拉选择 "**弹性池**"。 选择 "**创建**" 来创建弹性池。 

    ![选择弹性池](media/sql-database-elastic-pool-failover-group-tutorial/select-azure-sql-elastic-pool.png)

1. 使用以下值配置弹性池：
   - **名称**：为弹性池提供唯一的名称，如 `myElasticPool`。 
   - **订阅**：从下拉菜单中选择你的订阅。
   - **资源组：从**下拉部分中选择 "`myResourceGroup`"，在第1部分中创建的资源组。 
   - **服务器**：从下拉部分中选择在第1部分中创建的服务器。  

       ![为弹性池创建新服务器](media/sql-database-elastic-pool-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **计算 + 存储**：选择 "**配置弹性池**" 以配置计算和存储，并将你的单一数据库添加到弹性池。 在“池设置”选项卡上，保留默认值“第 5 代”、2 个 vCore 和 32GB 存储。 

1. 在“配置”页上选择“数据库”选项卡，然后选择“添加数据库”。 选择在第 1 部分创建的数据库，然后选择“应用”将其添加到弹性池中。 再次选择“应用”以应用弹性池设置并关闭“配置”页。 

    ![将 SQL 数据库添加到弹性池中](media/sql-database-elastic-pool-failover-group-tutorial/add-database-to-elastic-pool.png)

1. 选择“查看 + 创建”以检查弹性池设置，然后选择“创建”以创建弹性池。 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
使用 PowerShell 创建弹性池和辅助服务器。 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # The ip address range that you want to allow to access your server 
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"
   
   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 为 Azure SQL 数据库创建弹性数据库池。| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 设置数据库的属性，或将现有数据库移到弹性池中。 | 

---

## <a name="3---create-the-failover-group"></a>3 - 创建故障转移组 
此步骤在现有的 Azure SQL 服务器与另一区域中的新 Azure SQL 服务器之间创建一个[故障转移组](sql-database-auto-failover-group.md)。 然后，将弹性池添加到该故障转移组。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户创建故障转移组。 

1. 在 **Azure 门户**的左侧菜单中选择“Azure SQL”[](https://portal.azure.com)。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择在上一部分中创建的弹性池，例如 `myElasticPool`。 
1. 在“概述”窗格上，选择**服务器名称**下的服务器名称以打开服务器的设置。
  
    ![打开弹性池的服务器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. 在“设置”窗格下选择“故障转移组”，然后选择“添加组”以创建新的故障转移组。 

    ![添加新的故障转移组](media/sql-database-elastic-pool-failover-group-tutorial/elastic-pool-failover-group.png)

1. 在“故障转移组”页上输入或选择以下值，然后选择“创建”：
    - **故障转移组名称**：键入唯一的故障转移组名称，如 `failovergrouptutorial`。 
    - **辅助服务器**：选择 "*配置所需设置*" 选项，然后选择 "**创建新服务器**"。 或者，可以选择现有的服务器作为辅助服务器。 为新的辅助服务器输入以下值之后，选择“选择”。 
        - **服务器名称**：为辅助服务器键入唯一名称，如 `mysqlsecondary`。 
        - **服务器管理员登录名**： Type `azureuser`
        - **密码**：键入符合密码要求的复杂密码。
        - **位置**：从下拉项中选择一个位置，例如 "`East US`"。 此位置不能与主服务器的位置相同。

       > [!NOTE]
       > 服务器登录名和防火墙设置必须与主服务器相匹配。 
    
       ![为故障转移组创建辅助服务器](media/sql-database-elastic-pool-failover-group-tutorial/create-secondary-failover-server.png)

1. 选择“组中的数据库”，然后选择在第 2 部分创建的弹性池。 此时应会出现一条警告，提示你将在辅助服务器上创建弹性池。 选择该警告，然后选择“确定”以在辅助服务器上创建弹性池。 
        
    ![将弹性池添加到故障转移组中](media/sql-database-elastic-pool-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. 选择“选择”以将弹性池设置应用到故障转移组，然后选择“创建”以创建故障转移组。 将弹性池添加到故障转移组的操作会自动启动异地复制过程。


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建故障转移组。 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary logical server =" $drServerName
   
   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary logical server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured" 
   
   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool
   
   # Create a failover group between the servers
   Write-host "Creating failover group..." 
   New-AzSqlDatabaseFailoverGroup `
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully." 
   
   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...." 
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..." 
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases 
   $failoverGroup
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 创建托管单一数据库和弹性池的 SQL 数据库服务器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 为逻辑服务器创建防火墙规则。 | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 为 Azure SQL 数据库创建弹性数据库池。| 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 新建故障转移组。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 将一个或更多个 Azure SQL 数据库添加到故障转移组。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 获取或列出 Azure SQL 数据库故障转移组。 |

---


## <a name="4---test-failover"></a>4 - 测试故障转移 
此步骤将故障转移组故障转移到辅助服务器，然后使用 Azure 门户故障回复。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

使用 Azure 门户测试故障转移组的故障转移。 

1. 在 **Azure 门户**的左侧菜单中选择“Azure SQL”[](https://portal.azure.com)。 如果 **Azure SQL** 不在列表中，请选择“所有服务”，然后在搜索框中键入 Azure SQL。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。 
1. 选择在上一部分中创建的弹性池，例如 `myElasticPool`。 
1. 在**服务器名称**下选择服务器的名称以打开服务器的设置。

    ![打开弹性池的服务器](media/sql-database-elastic-pool-failover-group-tutorial/server-for-elastic-pool.png)

1. 在“设置”窗格下选择“故障转移组”，然后选择在第 2 部分创建的故障转移组。 
  
   ![在门户中选择故障转移组](media/sql-database-elastic-pool-failover-group-tutorial/select-failover-group.png)

1. 查看哪个服务器是主服务器，哪个服务器是辅助服务器。 
1. 在“任务”窗格中选择“故障转移”，以故障转移包含弹性池的故障转移组。 
1. 在告知将会断开 TDS 会话连接的警告中选择“是”。 

   ![故障转移包含 SQL 数据库的故障转移组](media/sql-database-elastic-pool-failover-group-tutorial/failover-sql-db.png)

1. 查看哪个服务器是主服务器，哪个服务器是辅助服务器。 如果故障转移成功，这两个服务器的角色应会交换。 
1. 再次选择“故障转移”，将故障转移组故障回复到原始设置。 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 测试故障转移组的故障转移。 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName 
   ```

故障转移组故障转移到辅助服务器，然后使用 PowerShell 进行故障回复。 

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary" 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName 
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 获取或列出 Azure SQL 数据库故障转移组。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 执行 Azure SQL 数据库故障转移组的故障转移。 |


---

## <a name="clean-up-resources"></a>清理资源 

通过删除资源组来清理资源。 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)


1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组。
1. 选择“删除资源组”即可删除该资源组中的所有资源以及该组本身。 
1. 在文本框中键入资源组的名称 `myResourceGroup`，然后选择“删除”以删除该资源组。 


# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 清理资源。 

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   
   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```
---

本教程的此部分使用以下 PowerShell cmdlet：

| 命令 | 说明 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组 | 

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

## <a name="full-script"></a>完整脚本

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| 命令 | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 创建托管单一数据库和弹性池的 SQL 数据库服务器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 为逻辑服务器创建防火墙规则。 | 
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 新建 Azure SQL 数据库单一数据库。 | 
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | 为 Azure SQL 数据库创建弹性数据库池。| 
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | 设置数据库的属性，或将现有数据库移到弹性池中。 | 
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 新建故障转移组。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 获取一个或更多个 SQL 数据库。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 将一个或更多个 Azure SQL 数据库添加到故障转移组。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 获取或列出 Azure SQL 数据库故障转移组。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 执行 Azure SQL 数据库故障转移组的故障转移。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组 | 


# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
没有适用于 Azure 门户的脚本。

---

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 Azure SQL 数据库弹性池添加到故障转移组，以及如何测试故障转移。 你已了解如何：

> [!div class="checklist"]
> - 创建 Azure SQL 数据库单一数据库。
> - 将单一数据库添加到弹性池中。 
> - 为两个逻辑 SQL server 之间的两个弹性池创建[故障转移组](sql-database-auto-failover-group.md)。
> - 测试故障转移。

转到下一教程，其中介绍了如何使用 DMS 进行迁移。

> [!div class="nextstepaction"]
> [教程：使用 DMS 将 SQL Server 迁移到共用数据库](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
