---
title: "在 Azure 数据工厂中创建自承载的集成运行时 | Microsoft Docs"
description: "了解如何使用 SQL Server 存储过程活动从数据工厂管道调用 Azure SQL 数据库或 Azure SQL 数据仓库中的存储过程。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: ef600fc3727f1da3203e075714282a6505bc98d3
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>在 Azure 数据工厂中创建 Azure-SSIS 集成运行时
本教程文章提供了在 Azure 数据工厂中配置 Azure-SSIS 集成运行时的步骤。 然后，可以使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 将 SQL Server Integration Services (SSIS) 包部署到 Azure 上的此运行时。 将在本教程中执行以下步骤：

## <a name="prerequisites"></a>先决条件

- **Azure 订阅**。 如果没有订阅，可以创建一个[免费试用](http://azure.microsoft.com/pricing/free-trial/)帐户。
- **Azure SQL 数据库服务器**或 **SQL Server 托管实例（专用预览）（扩展专用预览）**。 如果还没有数据库服务器，请在启动之前在 Azure 门户中创建一个。 此服务器承载 SSIS 目录数据库 (SSISDB)。 建议在集成运行时所在的同一 Azure 区域中创建数据库服务器。 此配置允许集成运行时将执行日志写入 SSISDB 而无需跨 Azure 区域。 
- **经典虚拟网络 (VNet)（可选）**。 如果至少满足以下条件之一，则必须具有 Azure 虚拟网络 (VNet)：
    - 作为 VNet 一部分的 SQL Server 托管实例（专用预览）上承载着 SSIS 目录数据库。
    - 希望从在 Azure-SSIS 集成运行时上运行的 SSIS 包连接到本地数据存储。
- **Azure PowerShell**。 遵循[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明。 使用 PowerShell 运行脚本来预配在云中运行 SSIS 包的 Azure-SSIS 集成运行时。 

## <a name="create-variables"></a>创建变量
定义在本教程的脚本中使用的变量：

```powershell
# Azure Data Factory version 2 information
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" # In public preview, only EastUS|EastUS2 are supported.

# Azure-SSIS integration runtime information - This is the Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS integration runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS integration runtime"
$AzureSSISLocation = "EastUS" # In public preview, only EastUS|NorthEurope are supported.
$AzureSSISNodeSize = "Standard_A4_v2" # In public preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported.
$AzureSSISNodeNumber = 2 # In public preview, only 1-10 nodes are supported.
$AzureSSISMaxParallelExecutionsPerNode = 2 # In public preview, only 1-8 parallel executions per node are supported.
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In public preview, only classic virtual network (VNET) is supported.
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In public preview, only classic VNet is supported.

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"
$SSISDBPricingTier = "[your Azure SQL Database pricing tier, e.g. S3, or leave it empty for Azure SQL Managed Instance (private preview)]" # Not applicable for Azure SQL Managed Instance (private preview)
```

## <a name="validate-the-connection-to-database"></a>验证与数据库的连接
添加以下脚本以验证 Azure SQL 数据库服务器 server.database.windows.net 或 Azure SQL 托管实例（专用预览）服务器端点。 

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

## <a name="login-and-select-subscription"></a>登录并选择订阅
添加以下代码脚本以登录并选择 Azure 订阅： 

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName
```

## <a name="configure-virtual-network"></a>配置虚拟网络
添加以下脚本以自动配置加入 Azure-SSIS 集成运行时的虚拟网络权限/设置。

```powershell
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}
```

## <a name="create-a-resource-group"></a>创建资源组
使用 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) 命令创建 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)。 资源组是在其中以组的形式部署和管理 Azure 资源的逻辑容器。 以下示例在 `westeurope` 位置创建名为 `myResourceGroup` 的资源组。

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
运行以下命令以创建在 Azure 中运行 SSIS 包的 Azure SSIS 集成运行时： 

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
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
```

## <a name="start-integration-runtime"></a>启动集成运行时
运行以下命令以启动 Azure-SSIS 集成运行时： 

```powershell
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")                                  
```
此命令将花费 **20 到 30 分钟**才能完成。 

## <a name="deploy-ssis-packages"></a>部署 SSIS 包
现在，使用 SQL Server Data Tools (SSDT) 或 SQL Server Management Studio (SSMS) 来将 SSIS 包部署到 Azure。 连接到承载 SSIS 目录 (SSISDB) 的 Azure SQL 服务器。 Azure SQL 服务器的名称采用以下格式：&lt;servername&gt;.database.windows.net（适用于 Azure SQL 数据库）。 有关说明，请参阅文章[部署包](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server)。 

## <a name="full-script"></a>完整脚本
在本版本中，必须使用 PowerShell 来预配在云中运行 SSIS 包的 Azure-SSIS 集成运行时实例。 目前，无法通过使用 Azure 门户来预配此运行时。 

本部分的 PowerShell 脚本配置了运行 SSIS 包的云中 Azure-SSIS 集成运行时实例。 成功运行此脚本后，可使用 Azure SQL 数据库或 SQL Server 托管实例（专用预览）上承载的 SSISDB 在 Microsoft Azure 云上部署和运行 SSIS 包。

1. 启动 Windows PowerShell 集成脚本环境 (ISE)。
2. 在 ISE 中，从命令提示符窗口运行以下命令。    
    ```powershell
    Set-ExecutionPolicy Unrestricted -Scope CurrentUser
    ```
3. 复制本部分中的 PowerShell 脚本，并将其粘贴到 ISE 中。
4. 为该脚本开头“Azure 规范中的 SSIS”部分中的脚本参数提供适当的值。 这些参数将在下一部分中进行说明。
5. 运行该脚本。 `Start-AzureRmDataFactoryV2IntegrationRuntime`接近脚本末尾的命令运行了 **20 到 30 分钟**。

> [!NOTE]
> 脚本连接到 Azure SQL 数据库或 SQL Server 托管实例（专用预览）以准备 SSIS 目录数据库 (SSISDB)。 该脚本还配置 VNet 的权限和设置（如果指定），并将 Azure SSIS 集成运行时的新实例加入 VNet 中。


```powershell
# If your inputs contain PSH special characters, e.g. "$", please precede it with the escape character "`" like "`$". 
# Azure Data Factory v2 information
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
$DataFactoryLocation = "EastUS" # In Public Preview, only EastUS|EastUS2 are supported for now

# Azure-SSIS Integration Runtime info - This is Data Factory compute resource for running SSIS packages
$AzureSSISName = "[your Azure-SSIS Integration Runtime name]"
$AzureSSISDescription = "This is my Azure-SSIS Integration Runtime"
$AzureSSISLocation = "EastUS" # In Public Preview, only EastUS|NorthEurope are supported for now
$AzureSSISNodeSize = "Standard_A4_v2" # In Public Preview, only Standard_A4_v2|Standard_A8_v2|Standard_D1_v2|Standard_D2_v2|Standard_D3_v2|Standard_D4_v2 are supported for now
$AzureSSISNodeNumber = 2 # In Public Preview, only 1-10 nodes are supported for now
$AzureSSISMaxParallelExecutionsPerNode = 2 # In Public Preview, only 1-8 parallel executions per node are supported for now
$VnetId = "[your VNet resource ID or leave it empty]" # OPTIONAL: In Public Preview, only Classic VNet is supported for now
$SubnetName = "[your subnet name or leave it empty]" # OPTIONAL: In Public Preview, only Classic VNet is supported for now

# SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or your Azure SQL Managed Instance (private preview) server endpoint]"
$SSISDBServerAdminUserName = "[your server admin username]"
$SSISDBServerAdminPassword = "[your server admin password]"
$SSISDBPricingTier = "[your Azure SQL Database pricing tier, e.g. S3, or leave it empty for Azure SQL Managed Instance (private preview)]" # Not applicable for Azure SQL Managed Instance (private preview)

##### Validate your Azure SQL Database/Managed Instance (private preview) server ##### 
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

##### Login and and select your Azure subscription #####
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

##### Automatically configure VNet permissions/settings for your Azure-SSIS Integration Runtime to join ##### 
# Register to Azure Batch resource provider
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    $BatchObjectId = (Get-AzureRmADServicePrincipal -ServicePrincipalName "MicrosoftAzureBatch").Id
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzureRmResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    # Assign VM contributor role to Microsoft.Batch
    New-AzureRmRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
}

##### Provision your Azure Data Factory  + Azure-SSIS Integration Runtime ##### 
New-AzureRmResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory

$secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
$serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

Set-AzureRmDataFactoryV2 -ResourceGroupName $ResourceGroupName `
                         -Location $DataFactoryLocation `
                         -Name $DataFactoryName
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
                                            -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                            -VnetId $VnetId `
                                            -Subnet $SubnetName
write-host("##### Starting #####")
Start-AzureRmDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
                                             -DataFactoryName $DataFactoryName `
                                             -Name $AzureSSISName `
                                             -Force
write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")

##### Query/monitor your Azure-SSIS Integration Runtime #####
#Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status

##### Reconfigure your Azure-SSIS Integration Runtime, e.g. stopping/scaling out to 5 nodes/starting #####
# Stopping your Azure-SSIS Integration Runtime will release its nodes and stop billing
#Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 

# Scaling out your Azure-SSIS Integration Runtime to 5 nodes
#Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5

# Starting your Azure-SSIS Integration Runtime will allocate its nodes and start billing
#Start-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName

##### Clean up ######
#Stop-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
#Remove-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
#Remove-AzureRmDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
#Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force

```

