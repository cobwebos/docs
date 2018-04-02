---
title: 使用 PowerShell 将 SSIS 包部署到 Azure | Microsoft Docs
description: 本文介绍如何使用 PowerShell 将 SSIS 包部署到 Azure 并创建 Azure-SSIS 集成运行时。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: douglasl
ms.openlocfilehash: f9487067ae77fbb261fb683ddd15207670f9576f
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="deploy-sql-server-integration-services-packages-to-azure-with-powershell"></a>使用 PowerShell 将 SQL Server Integration Services 包部署到 Azure
本教程提供了在 Azure 数据工厂中预配 Azure-SSIS 集成运行时 (IR) 的步骤。 然后，可以使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 将 SQL Server Integration Services (SSIS) 包部署到 Azure 上的此运行时。 在本教程中，将执行以下步骤：

> [!NOTE]
> 本文使用 Azure PowerShell 来预配 Azure SSIS IR。 若要使用数据工厂用户界面 (UI) 来预配 Azure SSIS IR，请参阅[教程：创建 Azure SSIS 集成运行时](tutorial-create-azure-ssis-runtime-portal.md)。 

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建 Azure-SSIS 集成运行时
> * 启动 Azure-SSIS 集成运行时
> * 部署 SSIS 包
> * 查看完整脚本

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。



## <a name="prerequisites"></a>先决条件
- **Azure 订阅**。 如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。 有关 Azure-SSIS IR 的概念性信息，请参阅 [Azure-SSIS 集成运行时概述](concepts-integration-runtime.md#azure-ssis-integration-runtime)。
- **Azure SQL 数据库服务器** 如果还没有数据库服务器，请在启动之前在 Azure 门户中创建一个。 此服务器承载着 SSIS 目录数据库 (SSISDB)。 建议在集成运行时所在的同一 Azure 区域中创建数据库服务器。 此配置允许集成运行时将执行日志写入 SSISDB 而无需跨 Azure 区域。 
    - 确认为数据库服务器启用了“允许访问 Azure 服务”设置。 有关详细信息，请参阅[保护 Azure SQL 数据库](../sql-database/sql-database-security-tutorial.md#create-a-server-level-firewall-rule-in-the-azure-portal)。 若要通过 PowerShell 来启用此设置，请参阅 [New-AzureRmSqlServerFirewallRule](/powershell/module/azurerm.sql/new-azurermsqlserverfirewallrule?view=azurermps-4.4.1)。
    - 将客户端计算机的 IP 地址或一系列包括客户端计算机 IP 地址的 IP 地址添加到数据库服务器的防火墙设置中的客户端 IP 地址列表。 有关详细信息，请参阅 [Azure SQL 数据库服务器级和数据库级防火墙规则](../sql-database/sql-database-firewall-configure.md)。 
    - 确认 Azure SQL 数据库服务器没有 SSIS 目录（SSIDB 数据库）。 预配 Azure-SSIS IR 时不支持使用现有 SSIS 目录。 
- **Azure PowerShell**。 遵循[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明。 将使用 PowerShell 运行一个脚本来在云中预配运行 SSIS 包的 Azure-SSIS 集成运行时。 

> [!NOTE]
> - 可以在下述区域创建第 2 版数据工厂：美国东部、美国东部 2、东南亚、西欧。 
> - 可以在下述区域创建 Azure-SSIS IR：美国东部、美国东部 2、美国中部、北欧、西欧、澳大利亚东部。

## <a name="launch-windows-powershell-ise"></a>启动 Windows PowerShell ISE
使用管理特权启动 **Windows PowerShell ISE**。 

## <a name="create-variables"></a>创建变量
复制并粘贴以下脚本：指定变量的值。 有关受支持的 Azure SQL 数据库定价层列表，请参阅 [SQL 数据库资源限制](../sql-database/sql-database-resource-limits.md)。

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>"
# You can create a data factory of version 2 in the following regions: East US, East US 2, Southeast Asia, and West Europe. 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS IR>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"

# You can create Azure-SSIS IR in the following regions: East US, East US 2, Central US, North Europe, West Europe, Australia East 
$AzureSSISLocation = "EastUS"
 
# In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 
```

## <a name="validate-the-connection-to-database"></a>验证与数据库的连接
添加以下脚本来验证 Azure SQL 数据库服务器 server.database.windows.net。 

```powershell
$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}
```

若要在脚本中创建 Azure SQL 数据库，请参阅以下示例： 

设置尚未定义的变量的值。 例如：FirewallIPAddress。 

```powershell
New-AzureRmSqlServer -ResourceGroupName $ResourceGroupName `
  -ServerName $SQLServerName `
    -Location $DataFactoryLocation `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SQLServerAdmin, $(ConvertTo-SecureString -String $SQLServerPass -AsPlainText -Force))

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName `
    -ServerName $SQLServerName `
    -FirewallRuleName "ClientIPAddress_$today" -StartIpAddress $FirewallIPAddress -EndIpAddress $FirewallIPAddress

New-AzureRmSqlServerFirewallRule -ResourceGroupName $ResourceGroupName -ServerName $SQLServerName -AllowAllAzureIPs
```


## <a name="log-in-and-select-subscription"></a>登录并选择订阅
在脚本中添加以下代码，以登录并选择 Azure 订阅： 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="create-a-resource-group"></a>创建资源组
使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令创建 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。 以下示例在 `westeurope` 位置创建名为 `myResourceGroup` 的资源组。

如果资源组已存在，请不要将此代码复制到脚本中。 

```powershell
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

## <a name="create-a-data-factory"></a>创建数据工厂
运行以下命令以创建数据工厂：

```powershell
Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName
```

## <a name="create-an-integration-runtime"></a>创建集成运行时
运行以下命令来在 Azure 中创建运行 SSIS 包的 Azure SSIS 集成运行时： 

```powershell
$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode 
```

## <a name="start-integration-runtime"></a>启动集成运行时
运行以下命令以启动 Azure-SSIS 集成运行时： 

```powershell
write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
此命令将花费 **20 到 30 分钟**才能完成。 

## <a name="deploy-ssis-packages"></a>部署 SSIS 包
现在，使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 来将 SSIS 包部署到 Azure。 连接到承载着 SSIS 目录 (SSISDB) 的 Azure SQL 服务器。 Azure SQL 服务器的名称采用以下格式：`<servername>.database.windows.net`（适用于 Azure SQL 数据库）。 

参阅 SSIS 文档中的以下文章： 

- [在 Azure 上部署、运行和监视 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [连接到 Azure 上的 SSIS 目录](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [在 Azure 上计划包执行](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
- [使用 Windows 身份验证连接到本地数据源](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 

## <a name="full-script"></a>完整脚本
在本版本中，若要在云中预配运行 SSIS 包的 Azure-SSIS 集成运行时实例，必须使用 PowerShell。 目前，无法使用 Azure 门户来预配此运行时。 

本部分中的 PowerShell 脚本在云中配置运行 SSIS 包的 Azure-SSIS 集成运行时实例。 成功运行此脚本后，可使用 Azure SQL 数据库中承载的 SSISDB 在 Microsoft Azure 云上部署和运行 SSIS 包。

1. 启动 Windows PowerShell 集成脚本环境 (ISE)。
2. 在 ISE 中，从命令提示符窗口运行以下命令。    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. 复制本部分中的 PowerShell 脚本，并将其粘贴到 ISE 中。
4. 为该脚本开头“Azure 规范中的 SSIS”部分中的脚本参数提供适当的值。 这些参数将在下一部分中进行说明。
5. 运行该脚本。 脚本中末尾附近的 `Start-AzureRmDataFactoryV2IntegrationRuntime` 命令将运行 **20 到 30 分钟**。

> [!NOTE]
> - 此脚本连接到 Azure SQL 数据库来准备 SSIS 目录数据库 (SSISDB)。 该脚本还配置 VNet 的权限和设置（如果已指定），并将 Azure SSIS 集成运行时的新实例加入 VNet 中。
> - 预配 Azure-SSIS IR 的实例时，还会安装 Azure Feature Pack for SSIS 和 Access Redistributable。 除了内置组件支持的数据源外，这些组件还提供与 Excel 和 Access 文件和各种 Azure 数据源的连接。 目前不能安装用于 SSIS 的第三方组件（包括 Microsoft 提供的第三方组件，如 Attunity 提供的 Oracle 和 Teradata 组件以及 SAP BI 组件）。


有关受支持的 Azure SQL 数据库定价层列表，请参阅 [SQL 数据库资源限制](../sql-database/sql-database-resource-limits.md)。 

如需 Azure 数据工厂 V2 和 Azure-SSIS Integration Runtime 所支持的区域的列表，请参阅[可用产品（按区域）](https://azure.microsoft.com/regions/services/)。 展开“数据 + 分析”即可看到“数据工厂 V2”和“SSIS Integration Runtime”。

```powershell
# Azure Data Factory version 2 information 
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$". 
$SubscriptionName = "<Azure subscription name>"
$ResourceGroupName = "<Azure resource group name>"
# Data factory name. Must be globally unique
$DataFactoryName = "<Data factory name>" 
$DataFactoryLocation = "EastUS" 

# Azure-SSIS integration runtime information. This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "<Specify a name for your Azure-SSIS (IR)>"
$AzureSSISDescription = "<Specify description for your Azure-SSIS IR"
$AzureSSISLocation = "EastUS" 
 # In public preview, only Standard_A4_v2, Standard_A8_v2, Standard_D1_v2, Standard_D2_v2, Standard_D3_v2, Standard_D4_v2 are supported
$AzureSSISNodeSize = "Standard_D3_v2"
# In public preview, only 1-10 nodes are supported.
$AzureSSISNodeNumber = 2 
# For a Standard_D1_v2 node, 1-4 parallel executions per node are supported. For other nodes, it's 1-8.
$AzureSSISMaxParallelExecutionsPerNode = 2 

# SSISDB info
$SSISDBServerEndpoint = "<Azure SQL server name>.database.windows.net"
$SSISDBServerAdminUserName = "<Azure SQL server - user name>"
$SSISDBServerAdminPassword = "<Azure SQL server - user password>"
# Remove the SSISDBPricingTier variable if you are using Azure SQL Managed Instance (private preview)
# This parameter applies only to Azure SQL Database. For the basic pricing tier, specify "Basic", not "B". For standard tiers, specify "S0", "S1", "S2", 'S3", etc.
$SSISDBPricingTier = "<pricing tier of your Azure SQL server. Examples: Basic, S0, S1, S2, S3, etc.>" 

$SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID="+ $SSISDBServerAdminUserName +";Password="+ $SSISDBServerAdminPassword
$sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
Try
{
    $sqlConnection.Open();
}
Catch [System.Data.SqlClient.SqlException]
{
    Write-Warning "Cannot connect to your Azure SQL DB logical server/Azure SQL MI server, exception: $_"  ;
    Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
    $yn = Read-Host
    if(!($yn -ieq "Y"))
    {
        Return;
    } 
}

Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                        -Location $DataFactoryLocation `
                        -Name $DataFactoryName

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)
Set-AzureRmDataFactoryV2IntegrationRuntime  -ResourceGroupName $ResourceGroupName `
                                            -DataFactoryName $DataFactoryName `
                                            -Name $AzureSSISName `
                                            -Type Managed `
                                            -CatalogServerEndpoint $SSISDBServerEndpoint `
                                            -CatalogAdminCredential $serverCreds `
                                            -CatalogPricingTier $SSISDBPricingTier `
                                            -Description $AzureSSISDescription `
                                            -Location $AzureSSISLocation `
                                            -NodeSize $AzureSSISNodeSize `
                                            -NodeCount $AzureSSISNodeNumber `
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode

write-host("##### Starting your Azure-SSIS integration runtime. This command takes 20 to 30 minutes to complete. #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="join-azure-ssis-ir-to-a-vnet"></a>将 Azure-SSIS IR 加入 VNet
如果使用 SQL Azure 托管实例（个人预览版）在虚拟网络 (VNet) 中承载 SQL Server Integration Services (SSIS) 目录，则还必须将 Azure-SSIS 集成运行时加入同一虚拟网络。 使用 Azure 数据工厂版本 2（预览版）可将 Azure-SSIS 集成运行时加入 VNet。 有关详细信息，请参阅[将 Azure-SSIS 运行时加入 VNet](join-azure-ssis-integration-runtime-virtual-network.md)。

有关用于创建可加入 VNet 的 Azure-SSIS 运行时的完整脚本，请参阅[创建 Azure-SSIS 集成运行时](create-azure-ssis-integration-runtime.md)。

## <a name="monitor-and-manage-azure-ssis-ir"></a>监视和管理 Azure-SSIS IR
请参阅以下文章，了解有关监视和管理 Azure-SSIS IR 的详细信息。 

- [监视 Azure-SSIS 集成运行时](monitor-integration-runtime.md#azure-ssis-integration-runtime)
- [管理 Azure-SSIS 集成运行时](manage-azure-ssis-integration-runtime.md)

## <a name="next-steps"></a>后续步骤
本教程介绍了如何： 

> [!div class="checklist"]
> * 创建数据工厂。
> * 创建 Azure-SSIS 集成运行时
> * 启动 Azure-SSIS 集成运行时
> * 部署 SSIS 包
> * 查看完整脚本

若要了解如何将数据从本地复制到云，请转到以下教程： 

> [!div class="nextstepaction"]
>[复制云中的数据](tutorial-copy-data-dot-net.md)
