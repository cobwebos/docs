---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 06/19/2019
ms.author: mathoma
ms.openlocfilehash: eff121cfaf4473607110de4553a9bb8021990caf
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444677"
---
在此步骤中, 将创建资源组和 Azure SQL 数据库单一数据库。 

> [!IMPORTANT]
> 请确保将防火墙规则设置为使用你在其上执行本文中的步骤的计算机的公共 IP 地址。 数据库级防火墙规则会自动复制到辅助服务器。
>
> 有关详细信息，请参阅[创建数据库级防火墙规则](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)，或参阅[创建服务器级防火墙](../articles/sql-database/sql-database-server-level-firewall-rule.md)，确定用于计算机的服务器级防火墙规则的 IP 地址。  

# <a name="azure-portaltabazure-portal"></a>[Azure 门户](#tab/azure-portal)
使用 Azure 门户创建资源组和单个数据库。 

1. 在 Azure 门户的左上角选择“创建资源”。
2. 选择“数据库”，然后选择“SQL 数据库”打开“创建 SQL 数据库”页。

   ![创建单一数据库](../articles/sql-database/media/sql-database-get-started-portal/create-database-1.png)

3. 在“项目详细信息”部分的“基本信息”选项卡上，键入或选择以下值：

   - **订阅**：下拉列表并选择正确的订阅（如果它没有出现）。
   - **资源组**：选择“新建”，键入 `myResourceGroup`，然后选择“确定”。

     ![“新建 SQL 数据库 - 基本信息”选项卡](../articles/sql-database/media/sql-database-get-started-portal/new-sql-database-basics.png)

4. 在“数据库详细信息”部分，键入或选择以下值：

   - **数据库名称**：输入 `mySampleDatabase` 。
   - **服务器**：选择“新建”并输入以下值，然后选择“创建”。
       - **服务器名称**：键入 `mysqlserver` 和一些数字，使名称唯一。
       - **服务器管理员登录名**：键入 `azureuser`。
       - **密码**：键入符合密码要求的复杂密码。
       - **位置**：从下拉列表中选择一个位置，例如 `West US 2`。

         ![新服务器](../articles/sql-database/media/sql-database-get-started-portal/new-server.png)

      > [!IMPORTANT]
      > 请记得记录服务器管理员登录名和密码，以便可以登录服务器和数据库以获取此快速入门和其他快速入门。 如果忘记了登录名或密码，可在“SQL 服务器”页上获取登录名或重置密码。 若要打开“SQL 服务器”页，请在创建数据库后在数据库“概述”页上选择服务器名称。

        ![SQL 数据库详细信息](../articles/sql-database/media/sql-database-get-started-portal/sql-db-basic-db-details.png)

   - **想要使用 SQL 弹性池**：选择“否”选项。
   - **计算 + 存储**：选择“配置数据库”，对于此快速入门，选择“基于 vCore 的购买选项”

     ![基于 vCore 的采购选项](../articles/sql-database/media/sql-database-get-started-portal/create-database-vcore.png)

   - 选择 "**预配**" 和 " **Gen4**"。

     ![无服务器计算层](../articles/sql-database/media/sql-database-get-started-portal/create-database-serverless.png)

   - 查看“最大 vCore 数目”、“最小 vCore 数目”、“自动暂停延迟”和“数据最大大小”的设置。 根据需要进行更改。
   - 接受预览版条款，然后单击“确定”。
   - 选择“应用”。

5. 选择“其他设置”选项卡。 
6. 在“数据源”部分的“使用现有数据”下，选择 `Sample`。 

   ![其他 SQL 数据库设置](../articles/sql-database/media/sql-database-get-started-portal/create-sql-database-additional-settings.png)

   > [!IMPORTANT]
   > 确保选择“示例(AdventureWorksLT)”数据，这样就可以轻松地按照本指南以及使用此数据的其他 Azure SQL 数据库快速入门进行操作。

7. 将剩余字段保留默认值，然后选择窗体底部的“查看 + 创建”。
8. 检查最终的设置，然后选择“创建”。

9. 在“SQL 数据库”窗体中，选择“创建”来部署和预配资源组、服务器和数据库。

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

[!INCLUDE [updated-for-az](updated-for-az.md)]

使用 PowerShell 创建资源组和单个数据库。 

   ```powershell-interactive
   # Set variables for your server and database
   $SubscriptionId = '<Your Azure subscription ID>'
   $ResourceGroupName = "myResourceGroup" # to randomize: "myResourceGroup-$(Get-Random)"
   $Location = "westus2"
   $AdminLogin = "azureuser"
   $Password = "ChangeYourAdminPassword1"
   $ServerName = "mysqlserver" # to randomize: "mysqlserver-$(Get-Random)"
   $DatabaseName = "mySampleDatabase"
   
   
   # The ip address range that you want to allow to access your server 
   #(leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"
   
   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create a resource group
   $resourceGroup = New-AzResourceGroup -Name $ResourceGroupName -Location $Location
   
   # Create a server with a system wide unique server name
   $server = New-AzSqlServer -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -Location $Location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $AdminLogin, $(ConvertTo-SecureString -String $Password -AsPlainText -Force))
   
   # Create a server firewall rule that allows access from the specified IP range
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   
   # Create General Purpose Gen4 database with 1 vCore
   $database = New-AzSqlDatabase  -ResourceGroupName $ResourceGroupName `
      -ServerName $ServerName `
      -DatabaseName $DatabaseName `
      -Edition GeneralPurpose `
      -VCore 1 `
      -ComputeGeneration Gen4  `
      -MinimumCapacity 1 `
      -SampleName "AdventureWorksLT"
   ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
使用 AZ CLI 创建资源组和单个数据库。 


   ```azurecli-interactive
   #!/bin/bash
   # Set variables
   export subscriptionID=<Your Subscription ID>
   export ResourceGroupName=myResourceGroup # to randomize: myResourceGroup-$RANDOM
   export Location=WestUS2
   export AdminLogin=azureuser
   export Password=ChangeYourAdminPassword1
   export ServerName="mysqlserver" # to randomize: mysqlserver-$RANDOM
   export DatabaseName=mySampleDatabase
   
   # The ip address range that you want to allow to access your DB. 
   # (leaving at 0.0.0.0 will prevent outside-of-azure connections to your DB)
   export startip=0.0.0.0
   export endip=0.0.0.0
  
   # Connect to Azure
   az login

   $ Set subscription ID
   az account set --subscription $SubscriptionID
   
   # Create a resource group
   az group create \
      --name $ResourceGroupName \
      --location $Location
   
   # Create a logical server in the resource group
   az sql server create \
      --name $ServerName \
      --resource-group $ResourceGroupName \
      --location $Location  \
      --admin-user $AdminLogin\
      --admin-password $Password
   
   # Configure a firewall rule for the server
   az sql server firewall-rule create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      -n AllowYourIp \
      --start-ip-address $startip \
      --end-ip-address $endip
   
   # Create a database in the server 
   az sql db create \
      --resource-group $ResourceGroupName \
      --server $ServerName \
      --name $DatabaseName \
      --sample-name AdventureWorksLT \
      --edition GeneralPurpose \
      --family Gen4 \
      --capacity 1 \
   ```

---