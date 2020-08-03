---
title: 教程：将数据库添加到故障转移组
description: 使用 Azure 门户、PowerShell 或 Azure CLI 将 Azure SQL 数据库中的数据库添加到自动故障转移组。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 4caad36d21d3facb97dc358fdfee61e89c420213
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496332"
---
# <a name="tutorial-add-an-azure-sql-database-to-an-autofailover-group"></a>教程：将 Azure SQL 数据库添加到自动故障转移组
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[故障转移组](auto-failover-group-overview.md)是一种声明性抽象层，可用于对多个异地复制数据库进行分组。 了解如何使用 Azure 门户、PowerShell 或 Azure CLI 为 Azure SQL 数据库配置故障转移组并测试故障转移。  本教程介绍以下操作：

> [!div class="checklist"]
>
> - 在 Azure SQL 数据库中创建数据库
> - 为数据库创建两个服务器之间的故障转移组。
> - 测试故障转移。

## <a name="prerequisites"></a>先决条件

# <a name="the-portal"></a>[门户](#tab/azure-portal)

若要完成本教程，请确保做好以下准备：

- Azure 订阅。 如果还没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

若要完成本教程，请确保准备好以下各项：

- Azure 订阅。 如果还没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- [Azure PowerShell](/powershell/azure/)

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要完成本教程，请确保准备好以下各项：

- Azure 订阅。 如果还没有帐户，请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- 最新版本的 [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。

---

## <a name="1---create-a-database"></a>1 - 创建数据库

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2 - 创建故障转移组

此步骤在现有的服务器与另一区域中的新服务器之间创建一个[故障转移组](auto-failover-group-overview.md)。 然后，将示例数据库添加到故障转移组。

# <a name="the-portal"></a>[门户](#tab/azure-portal)

使用 Azure 门户创建故障转移组，并将数据库添加到其中。

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“Azure SQL”。 如果**AZURE sql**不在列表中，请选择 "**所有服务**"，然后在搜索框中键入 "Azure sql"。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。
1. 选择在第1部分中创建的数据库，例如 `mySampleDatabase` 。
1. 可以在服务器级别配置故障转移组。 在**服务器名称**下选择服务器的名称以打开服务器的设置。

   ![打开服务器上的数据库](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. 在“设置”窗格下选择“故障转移组”，然后选择“添加组”以创建新的故障转移组。  

   ![添加新的故障转移组](./media/failover-group-add-single-database-tutorial/sqldb-add-new-failover-group.png)

1. 在“故障转移组”页上输入或选择以下值，然后选择“创建”： 

   - **故障转移组名称**：键入唯一的故障转移组名称，例如 `failovergrouptutorial`。
   - **辅助服务器**：选择“配置所需设置”选项，然后选择“创建新服务器”。 或者，可以选择现有的服务器作为辅助服务器。 输入以下值后，选择“选择”。
      - **服务器名称**：键入辅助服务器的唯一名称，例如 `mysqlsecondary`。
      - **服务器管理员登录名**：键入 `azureuser`
      - **密码**：键入符合密码要求的复杂密码。
      - **位置**：从下拉项中选择一个位置，例如 `East US` 。 此位置不能与主服务器的位置相同。

     > [!NOTE]
     > 服务器登录名和防火墙设置必须与主服务器相匹配。

     ![为故障转移组创建辅助服务器](./media/failover-group-add-single-database-tutorial/create-secondary-failover-server.png)

   - **组中的数据库**：选择辅助服务器后，此选项将解除锁定。 使用该选项来**选择要添加的数据库**：请选择在第 1 部分创建的数据库。 将数据库添加到故障转移组的操作会自动启动异地复制过程。

   ![将 SQL 数据库添加到故障转移组](./media/failover-group-add-single-database-tutorial/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 创建故障转移组，并将数据库添加到其中。

   > [!NOTE]
   > 服务器登录名和防火墙设置必须与主服务器相匹配。

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| Command | 注释 |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 在 Azure SQL 数据库中创建托管单一数据库和弹性池的的服务器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 为 Azure SQL 数据库中的服务器创建防火墙规则。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 在 Azure SQL 数据库中创建一个新的单一数据库。 |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 在 Azure SQL 数据库中创建一个新的故障转移组。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 获取一个和多个 Azure SQL 数据库中的数据库。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 将一个或多个数据库添加到 Azure SQL 数据库中的故障转移组。 |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 创建故障转移组，并将数据库添加到其中。

   > [!NOTE]
   > 服务器登录名和防火墙设置必须与主服务器相匹配。

   ```azurecli-interactive
   #!/bin/bash
   # set variables
   $failoverLocation = "West US"
   $failoverServer = "failoverServer-$randomIdentifier"
   $failoverGroup = "failoverGroup-$randomIdentifier"

   echo "Creating a secondary server in the DR region..."
   az sql server create --name $failoverServer --resource-group $resourceGroup --location $failoverLocation --admin-user $login --admin-password $password

   echo "Creating a failover group between the two servers..."
   az sql failover-group create --name $failoverGroup --partner-server $failoverServer --resource-group $resourceGroup --server $server --add-db $database --failover-policy Automatic
   ```

本教程的此部分使用以下 Azure CLI cmdlet：

| 命令 | 注释 |
|---|---|
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | 创建托管数据库和弹性池的服务器。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | 创建服务器的防火墙规则。 |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | 创建故障转移组。 |

---

## <a name="3---test-failover"></a>3 - 测试故障转移

此步骤将故障转移组故障转移到辅助服务器，然后使用 Azure 门户故障回复。

# <a name="the-portal"></a>[门户](#tab/azure-portal)

使用 Azure 门户测试故障转移。

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单中选择“Azure SQL”。 如果**AZURE sql**不在列表中，请选择 "**所有服务**"，然后在搜索框中键入 "Azure sql"。 （可选）选择“Azure SQL”旁边的星号将其收藏并将其添加为左侧导航栏中的项。
1. 选择在第2部分中创建的数据库，例如 `mySampleDatbase` 。
1. 在**服务器名称**下选择服务器的名称以打开服务器的设置。

   ![打开服务器上的数据库](./media/failover-group-add-single-database-tutorial/open-sql-db-server.png)

1. 在“设置”窗格下选择“故障转移组”，然后选择在第 2 部分创建的故障转移组。 
  
   ![在门户中选择故障转移组](./media/failover-group-add-single-database-tutorial/select-failover-group.png)

1. 查看哪个服务器是主服务器，哪个服务器是辅助服务器。
1. 在任务窗格中选择“故障转移”，以故障转移包含你的示例数据库的故障转移组。
1. 在告知将会断开 TDS 会话连接的警告中选择“是”。

   ![对包含数据库的故障转移组进行故障转移](./media/failover-group-add-single-database-tutorial/failover-sql-db.png)

1. 查看哪个服务器现在是主服务器，哪个服务器是辅助服务器。 如果故障转移成功，这两个服务器的角色应会交换。
1. 再次选择“故障转移”，使服务器恢复其原始角色。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 测试故障转移。

检查辅助副本的角色：

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

故障转移到辅助服务器：

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to" $drServerName
   ```

将故障转移组还原到主服务器：

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $serverName = "mysqlserver-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully back to" $serverName
   ```

本教程部分使用以下 PowerShell cmdlet：

| Command | 注释 |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 获取或列出 Azure SQL 数据库故障转移组。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 执行 Azure SQL 数据库故障转移组的故障转移。 |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 测试故障转移。

确认哪个服务器是辅助服务器：

   ```azurecli-interactive
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list --server $server --resource-group $resourceGroup
   ```

故障转移到辅助服务器：

   ```azurecli-interactive
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $failoverServer
   echo "Successfully failed failover group over to" $failoverServer
   ```

将故障转移组还原到主服务器：

   ```azurecli-interactive
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary --name $failoverGroup --resource-group $resourceGroup --server $server
   echo "Successfully failed failover group back to" $server
   ```

本教程的此部分使用以下 Azure CLI cmdlet：

| 命令 | 注释 |
|---|---|
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | 列出某个服务器中的故障转移组。 |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | 通过对当前主服务器上的所有数据库进行故障转移来设置故障转移组的主服务器。 |

---

## <a name="clean-up-resources"></a>清理资源

通过删除资源组来清理资源。

# <a name="the-portal"></a>[门户](#tab/azure-portal)

使用 Azure 门户删除资源组。

1. 在 [Azure 门户](https://portal.azure.com)中导航到你的资源组。
1. 选择“删除资源组”即可删除该资源组中的所有资源以及该组本身。
1. 在文本框中键入资源组的名称 `myResourceGroup`，然后选择“删除”以删除该资源组。  

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

使用 PowerShell 删除资源组。

   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

本教程的此部分使用以下 PowerShell cmdlet：

| Command | 注释 |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除资源组 |

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

使用 Azure CLI 删除资源组。

   ```azurecli-interactive
   echo "Cleaning up resources by removing the resource group..."
   az group delete --name $resourceGroup
   echo "Successfully removed resource group" $resourceGroup
   ```

本教程的此部分使用以下 Azure CLI cmdlet：

| 命令 | 注释 |
|---|---|
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 删除资源组，包括所有嵌套的资源。 |

---

> [!IMPORTANT]
> 若要保留资源组但删除辅助数据库，请先将其从故障转移组中移除，然后再将其删除。 如果在从故障转移组中移除辅助数据库之前将其删除，则可能会导致不可预知的行为。

## <a name="full-scripts"></a>完整脚本

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add database to a failover group")]

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 说明 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 创建用于存储所有资源的资源组。 |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | 创建在 Azure SQL 数据库中托管单一数据库和弹性池的服务器。 |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | 为 Azure SQL 数据库中的服务器创建防火墙规则。 |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | 在 Azure SQL 数据库中创建一个新的数据库。 |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | 在 Azure SQL 数据库中创建一个新的故障转移组。 |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | 获取一个和多个 Azure SQL 数据库中的数据库。 |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | 将一个或多个数据库添加到 Azure SQL 数据库中的故障转移组。 |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | 获取或列出 Azure SQL 数据库中的故障转移组。 |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| 执行 Azure SQL 数据库中的故障转移组的故障转移。 |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 删除 Azure SQL 数据库中的资源组。|

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Add database to a failover group")]

此脚本使用以下命令。 表中的每条命令均链接到特定于命令的文档。

| Command | 注释 |
|---|---|
| [az account set](/cli/azure/account?view=azure-cli-latest#az-account-set) | 将订阅设置为当前的活动订阅。 |
| [az group create](/cli/azure/group#az-group-create) | 创建用于存储所有资源的资源组。 |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | 创建在 Azure SQL 数据库中托管单一数据库和弹性池的服务器。 |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule) | 在 Azure SQL 数据库中创建服务器级别的 IP 防火墙规则。 |
| [az sql db create](/cli/azure/sql/db?view=azure-cli-latest) | 在 Azure SQL 数据库中创建数据库。 |
| [az sql failover-group create](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-create) | 在 Azure SQL 数据库中创建一个故障转移组。 |
| [az sql failover-group list](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-list) | 列出 Azure SQL 数据库中服务器中的故障转移组。 |
| [az sql failover-group set-primary](/cli/azure/sql/failover-group?view=azure-cli-latest#az-sql-failover-group-set-primary) | 通过对当前主服务器上的所有数据库进行故障转移来设置故障转移组的主服务器。 |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#az-vm-extension-set) | 删除资源组，包括所有嵌套的资源。 |

# <a name="the-portal"></a>[门户](#tab/azure-portal)

没有适用于 Azure 门户的脚本。

---

可在以下文档中找到其他 Azure SQL 数据库脚本：[Azure PowerShell](powershell-script-content-guide.md) 和 [Azure CLI](az-cli-script-samples-content-guide.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你已将一个 Azure SQL 数据库中的数据库添加到故障转移组，并测试了故障转移。 你已了解如何：

> [!div class="checklist"]
>
> - 在 Azure SQL 数据库中创建数据库
> - 为数据库创建两个服务器之间的故障转移组。
> - 测试故障转移。

请继续学习下一篇教程，了解如何将弹性池添加到故障转移组。

> [!div class="nextstepaction"]
> [教程：将 Azure SQL 数据库弹性池添加到故障转移组](failover-group-add-elastic-pool-tutorial.md)
