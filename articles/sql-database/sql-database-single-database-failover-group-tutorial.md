---
title: 教程：向故障转移组添加 Azure SQL 数据库单一数据库 |Microsoft Docs
description: 使用 Azure 门户、PowerShell 或 Azure CLI, 将 Azure SQL 数据库单一数据库添加到故障转移组。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
manager: jroth
ms.date: 06/19/2019
ms.openlocfilehash: 6125fd087d40f9ac6ae24d9200fc0cd1e7843f82
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444976"
---
# <a name="tutorial-add-an-azure-sql-database-single-database-to-a-failover-group"></a>教程：将 Azure SQL 数据库单一数据库添加到故障转移组

为 Azure SQL 数据库单一数据库配置故障转移组, 并使用 Azure 门户、PowerShell 或 Azure CLI 来测试故障转移。  在本教程中，将了解如何：

> [!div class="checklist"]
> - 创建 Azure SQL 数据库单一数据库。
> - 在两个逻辑 SQL 服务器之间为单一数据库创建[故障转移组](sql-database-auto-failover-group.md)。
> - 测试故障转移。

## <a name="prerequisites"></a>系统必备

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)
若要完成本教程，请确保做好以下准备： 

- Azure 订阅。 如果还没有帐户, 请[创建一个免费帐户](https://azure.microsoft.com/free/)。


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
若要完成本教程, 请确保具有以下各项:

- Azure 订阅。 如果还没有帐户, 请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- [Azure PowerShell](/powershell/azureps-cmdlets-docs)


# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
若要完成本教程, 请确保具有以下各项:

- Azure 订阅。 如果还没有帐户, 请[创建一个免费帐户](https://azure.microsoft.com/free/)。
- 最新版本的[Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest)。 

---

## <a name="1---create-a-single-database"></a>1-创建单个数据库 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---create-the-failover-group"></a>2-创建故障转移组 
在此步骤中, 将在现有的 Azure SQL server 和另一个区域中的新 Azure SQL server 之间创建[故障转移组](sql-database-auto-failover-group.md)。 然后将示例数据库添加到故障转移组。 

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)
创建故障转移组, 并使用 Azure 门户向其添加单个数据库。 


1. 选择[Azure 门户](https://portal.azure.com)左上角的 "**所有服务**"。 
1. 在`sql servers`搜索框中键入。 
1. 可有可无选择 "SQL Server" 旁的星形图标, 将**sql server**放在最上方, 并将其添加到左侧导航窗格中。 
    
    ![查找 SQL Server](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. 选择 " **SQL 服务器**", 然后选择在第1部分中创建的`mysqlserver`服务器, 例如。
1. 选择 "**设置**" 窗格下的 "**故障转移组**", 然后选择 "**添加组**" 以创建新的故障转移组。 

    ![添加新的故障转移组](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. 在 "**故障转移组**" 页上, 输入或选择以下值, 然后选择 "**创建**":
    - **故障转移组名称**:键入一个唯一的故障转移组名称, 如`failovergrouptutorial`。 
    - **辅助服务器**:选择 "*配置所需设置*" 选项, 然后选择 "**创建新服务器**"。 或者, 您可以选择已有的服务器作为辅助服务器。 输入以下值后, 选择 "**选择**"。 
        - **服务器名称**：键入辅助服务器的唯一名称, 例如`mysqlsecondary`。 
        - **服务器管理员登录名**：类别`azureuser`
        - **密码**：键入符合密码要求的复杂密码。
        - **位置**：从下拉菜单中选择一个位置, 例如 "美国东部 2"。 此位置不能与主服务器相同。

    > [!NOTE]
    > 服务器登录名和防火墙设置必须与主服务器的设置相匹配。 
    
      ![为故障转移组创建辅助服务器](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

   - **组中的数据库**:选择辅助服务器后, 此选项将解除锁定。 选择它以**选择要添加的数据库**, 然后选择在第1部分中创建的数据库。 将数据库添加到故障转移组会自动启动异地复制过程。 
        
    ![将 SQL DB 添加到故障转移组](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)
        

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
使用 PowerShell 创建故障转移组, 并将你的单一数据库添加到其中。 

   > [!NOTE]
   > 服务器登录名和防火墙设置必须与主服务器的设置相匹配。 

   ```powershell-interactive
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "West US 2"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $databaseName = "mySampleDatabase"
   $drLocation = "East US 2"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName 
   Write-host "Failover group name is" $failoverGroupName
   
   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   
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

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
使用 AZ CLI 创建故障转移组, 并将你的单一数据库添加到其中。 

   > [!NOTE]
   > 服务器登录名和防火墙设置必须与主服务器的设置相匹配。 

   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   # subscriptionID=<SubscriptionID>
   # resourceGroupName=myResourceGroup-$RANDOM
   # location=SouthCentralUS
   # adminLogin=azureuser
   # password="PWD27!"+`openssl rand -base64 18`
   # serverName=mysqlserver-$RANDOM
   # databaseName=mySampleDatabase
   drLocation=NorthEurope
   drServerName=mysqlsecondary-$RANDOM
   failoverGroupName=failovergrouptutorial-$RANDOM

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --failover-policy Automatic
   ```

---

## <a name="3---test-failover"></a>3-测试故障转移 
在此步骤中, 你将故障转移组故障转移到辅助服务器, 然后使用 Azure 门户故障回复。 

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)
使用 Azure 门户的测试故障转移。 

1. 导航到[Azure 门户](https://portal.azure.com)中的**SQL** server 服务器。 
1. 选择 "**设置**" 窗格下的 "**故障转移组**", 然后选择在第2部分中创建的故障转移组。 
  
   ![从门户中选择故障转移组](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. 查看哪个服务器是主服务器, 哪台服务器是辅助服务器。 
1. 从任务窗格中选择 "**故障转移**", 以故障转移包含示例单一数据库的故障转移组。 
1. 在警告消息中选择 **"是**", 通知您 TDS 会话将断开连接。 

   ![故障转移包含 SQL 数据库的故障转移组](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. 查看现在是主服务器, 哪台服务器是辅助服务器。 如果故障转移成功, 这两台服务器应该已经交换角色。 
1. 再次选择 "**故障转移**", 将服务器故障回复到其最初的角色。 

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
使用 PowerShell 进行测试故障转移。 


检查辅助副本的角色: 

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


故障转移到辅助服务器: 

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
   Write-host "Failed failover group to sucessfully to" $drServerName 
   ```

将故障转移组还原为主服务器：

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
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
使用 AZ CLI 测试故障转移。 


验证哪个服务器是辅助服务器:

   
   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

故障转移到辅助服务器: 

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # drServerName=mysqlsecondary-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

将故障转移组还原为主服务器：

   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   # serverName=mysqlserver-$RANDOM
   # failoverGroupName=failovergrouptutorial-$RANDOM
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## <a name="clean-up-resources"></a>清理资源 
通过删除资源组来清理资源。 

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)
使用 Azure 门户删除资源组。 


1. 导航到[Azure 门户](https://portal.azure.com)中的资源组。
1. 选择 "**删除资源组**" 以删除组中的所有资源以及资源组本身。 
1. 在文本框中键入资源组`myResourceGroup`的名称, 然后选择 "**删除**" 以删除该资源组。  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
使用 PowerShell 删除资源组。 


   ```powershell-interactive
   # Set variables
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Remove the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
使用 AZ CLI 删除资源组。 


   ```azurecli-interactive
   # Set variables
   # resourceGroupName=myResourceGroup-$RANDOM
   
   # Clean up resources by removing the resource group
   echo "Cleaning up resources by removing the resource group..."
   az group delete \
     --name $resourceGroupName
   echo "Successfully removed resource group" $resourceGroupName
   ```

---


## <a name="full-scripts"></a>完整脚本

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!code-powershell-interactive[main](../../powershell_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-ps.ps1 "Add single database to a failover group")]

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

[!code-azurecli-interactive[main](../../cli_scripts/sql-database/failover-groups/add-single-db-to-failover-group-az-cli.sh "Create SQL Database")]

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)
没有可用于 Azure 门户的脚本。
 
---

可在此处找到其他 Azure SQL 数据库脚本:[Azure PowerShell](sql-database-powershell-samples.md)和[Azure CLI](sql-database-cli-samples.md)。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何将 Azure SQL 数据库单一数据库添加到故障转移组, 以及如何测试故障转移。 你已了解如何：

> [!div class="checklist"]
> - 创建 Azure SQL 数据库单一数据库。 
> - 在两个逻辑 SQL 服务器之间为单一数据库创建[故障转移组](sql-database-auto-failover-group.md)。
> - 测试故障转移。

转到下一教程, 了解如何将弹性池添加到故障转移组。 

> [!div class="nextstepaction"]
> [教程：将 Azure SQL 数据库弹性池添加到故障转移组](sql-database-elastic-pool-failover-group-tutorial.md)
