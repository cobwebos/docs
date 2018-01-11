---
title: "在 Microsoft Azure PowerShell 中使用 Azure 数据库迁移服务模块从本地 SQL Server 迁移到 Azure SQL DB | Microsoft Docs"
description: "了解如何使用 Azure PowerShell 从本地 SQL Server 迁移到 Azure SQL。"
services: database-migration
author: HJToland3
ms.author: jtoland
manager: 
ms.reviewer: 
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/13/2017
ms.openlocfilehash: 9eebe8352d6a447df520c194b9906df8c2c9a83f
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/13/2017
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>使用 Azure PowerShell 从本地 SQL Server 迁移到 Azure SQL DB
在本文中，请使用 Microsoft Azure PowerShell 将还原为 SQL Server 2016 或更高版本的本地实例的 **Adventureworks2012** 数据库迁移到 Azure SQL 数据库。 可以使用 Microsoft Azure PowerShell 中的 `AzureRM.DataMigration` 模块，将数据库从本地 SQL Server 实例迁移到 Azure SQL 数据库。

在本文中，学习如何：
> [!div class="checklist"]
> * 创建资源组。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务实例中创建迁移项目。
> * 运行迁移。

## <a name="prerequisites"></a>先决条件
若要完成这些步骤，需满足以下条件：

- [SQL Server 2016 或更高版本](https://www.microsoft.com/sql-server/sql-server-downloads)（任意版本）
- 使用 SQL Server Express 安装的情况下会默认禁用 TCP/IP 协议。 请按[此文中的说明](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)来启用它。
- 配置[针对数据库引擎访问的 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- Azure SQL 数据库实例。 可以按照[在 Azure 门户中创建 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)一文中的详细信息来创建 Azure SQL 数据库实例。
- [数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更高版本。
- Azure 数据库迁移服务需要使用 Azure 资源管理器部署模型创建的 VNET，它通过使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 为本地源服务器提供站点到站点的连接。
- 已使用[ Performing a SQL Server migration assessment](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)（执行 SQL Server 迁移评估）一文中所述的数据迁移助手完成对本地数据库和架构迁移的评估
- 使用 [Install-Module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1) 从 PowerShell 库下载并安装 AzureRM.DataMigration 模块
- 用于连接到源 SQL Server 实例的凭据必须具有 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 权限。
- 用于连接到目标 Azure SQL DB 实例的凭据必须具有目标 Azure SQL 数据库的 CONTROL DATABASE 权限。
- 如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-your-microsoft-azure-subscription"></a>登录到 Microsoft Azure 订阅
根据[使用 Azure PowerShell 登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1)一文中的说明，使用 PowerShell 登录到 Azure 订阅。

## <a name="create-a-resource-group"></a>创建资源组
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 请先创建资源组，然后才能创建虚拟机。

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) 命令创建资源组。 

以下示例在“EastUS”区域创建名为“myResourceGroup”的资源组。

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-azure-database-migration-service-instance"></a>创建 Azure 数据库迁移服务实例 
可以通过 `New-AzureRmDataMigrationService` cmdlet 创建 Azure 数据库迁移服务的新实例。 此 cmdlet 需要以下必需参数：
- Azure 资源组名称。 可以使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) 命令创建前述 Azure 资源组，并提供其名称作为参数。
- 服务名称。 与 Azure 数据库迁移服务的所需唯一服务名称相对应的字符串 
- *位置*。 指定服务的位置。 指定 Azure 数据中心位置，例如“美国西部”或“东南亚”
- Sku。 此参数对应于 DMS Sku 名称。 目前支持的 Sku 名称为 Basic_1vCore、Basic_2vCores、GeneralPurpose_4vCores
- 虚拟子网标识符。 可以使用 cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) 创建子网。 

以下示例使用名为“MySubnet”的虚拟子网在位于“美国东部”区域的资源组 MyDMSResourceGroup 中创建名为“MyDMS”的服务。

```powershell
$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId
$vnet.Id`
```

## <a name="create-a-migration-project"></a>创建迁移项目
在创建 Azure 数据库迁移服务实例以后，创建迁移项目。 Azure 数据库迁移服务项目需要源和目标实例的连接信息，以及要在项目中迁移的数据库的列表。

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>创建源和目标连接的数据库连接信息对象
可以使用 `New-AzureRmDmsConnInfo` cmdlet 创建数据库连接信息对象。  此 cmdlet 需要以下参数：
- ServerType。 请求的数据库连接的类型，例如 SQL、Oracle 或 MySQL。 将 SQL 用于 SQL Server 和 SQL Azure。
- DataSource。 SQL 实例或 SQL Azure 服务器的名称或 IP。
- AuthType。 连接的身份验证类型，可以为 SqlAuthentication 或 WindowsAuthentication。
- TrustServerCertificate 参数设置的值用于指示在绕过验证信任的证书链时，是否对通道加密。 可以是 true 或 false。

以下示例使用 SQL 身份验证为名为 MySQLSourceServer 的源 SQL Server 创建连接信息对象 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySQLSourceServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

下一示例显示如何使用 SQL 身份验证为名为 MySQLAzureTarget 的 SQL Azure 数据库服务器创建连接信息

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "mysqlazuretarget.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>为迁移项目提供数据库
创建 `AzureRmDataMigrationDatabaseInfo` 对象的列表，以便指定 Azure 数据库迁移项目中的数据库（可以作为创建项目所需的参数提供）。 Cmdlet `New-AzureRmDataMigrationDatabaseInfo` 可以用来创建 AzureRmDataMigrationDatabaseInfo。 

以下示例为数据库 AdventureWorks2016 创建 `AzureRmDataMigrationDatabaseInfo` 项目，并将其添加到列表，以便在创建项目时作为参数提供。

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks2016
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>创建项目对象
最后即可使用 `New-AzureRmDataMigrationProject` 并添加以前创建的源和目标连接以及要迁移的数据库的列表，在“美国东部”创建名为“MyDMSProject”的 Azure 数据库迁移项目。

```powershell
$project = New-AzureRmDataMigrationProject -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName MyDMSProject `
  -Location EastUS `
  -SourceType SQL `
  -TargetType SQLDB `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfos $dbList
```

## <a name="create-and-start-a-migration-task"></a>创建并启动迁移任务
最后，创建并启动 Azure 数据库迁移任务。 除了作为先决条件为已创建的项目提供的信息，Azure 数据库迁移任务还需要源和目标的连接凭据信息，以及要迁移的数据库表的列表。 

### <a name="create-credential-parameters-for-source-and-target"></a>创建源和目标的凭据参数
可以将连接安全凭据作为 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象创建。 

以下示例显示了如何为源和目标连接创建 PSCredential 对象，将密码作为字符串变量 $sourcePassword 和 $targetPassword 提供。 

```powershell
secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-table-map-and-select-source-and-target-parameters-for-migration"></a>创建表映射并选择用于迁移的源和目标参数
迁移所需的另一参数是映射，将要迁移的表从源映射到目标。 请创建表字典，以便在源和目标表之间提供用于迁移的映射。 以下示例演示了源和目标表之间的映射（针对 AdventureWorks 2016 数据库的“人力资源”架构）。

```powershell
$tableMap = New-Object 'system.collections.generic.dictionary[string,string]'
$tableMap.Add("HumanResources.Department", "HumanResources.Department")
$tableMap.Add("HumanResources.Employee","HumanResources.Employee")
$tableMap.Add("HumanResources.EmployeeDepartmentHistory","HumanResources.EmployeeDepartmentHistory")
$tableMap.Add("HumanResources.EmployeePayHistory","HumanResources.EmployeePayHistory")
$tableMap.Add("HumanResources.JobCandidate","HumanResources.JobCandidate")
$tableMap.Add("HumanResources.Shift","HumanResources.Shift")
```

下一步是使用 `New-AzureRmDmsSqlServerSqlDbSelectedDB` cmdlet 选择源和目标数据库并提供要作为参数迁移的表映射，如以下示例所示：

```powershell
$selectedDbs = New-AzureRmDmsSqlServerSqlDbSelectedDB -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -TableMap $tableMap
```

### <a name="create-and-start-a-migration-task"></a>创建并启动迁移任务

使用 `New-AzureRmDataMigrationTask` cmdlet 创建并启动迁移任务。 此 cmdlet 需要以下参数：
- TaskType。  要创建的迁移任务的类型。对于从 SQL Server 到 SQL Azure 的迁移类型，此项应为 MigrateSqlServerSqlDb。 
- ResourceGroupName。 要在其中创建任务的 Azure 资源组的名称。
- ServiceName。  要在其中创建任务的 Azure 数据库迁移服务实例。
- ProjectName。 要在其中创建任务的 Azure 数据库迁移项目的名称。 
- TaskName。 要创建的任务的名称。 
- SourceConnection。 AzureRmDmsConnInfo 对象，表示源连接。
- TargetConnection。 AzureRmDmsConnInfo 对象，表示目标连接。
- SourceCred。 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象，用于连接到源服务器。
- TargetCred。 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象，用于连接到目标服务器。
- SelectedDatabase。 AzureRmDmsSqlServerSqlDbSelectedDB 对象，表示源和目标数据库映射。

以下示例创建并启动名为 myDMSTask 的迁移任务：

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDb `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
```

## <a name="monitor-the-migration"></a>监视迁移
可以监视正在运行的迁移任务，方法是查询任务的状态属性，如以下示例所示：

```powershell
if (($mytask.ProjectTask.Properties.State -eq "Running") -or ($mytask.ProjectTask.Properties.State -eq "Queued"))
{
  write-host "migration task running"
}
```

## <a name="next-steps"></a>后续步骤
- 在 Microsoft [Database Migration Guide](https://datamigration.microsoft.com/)（Microsoft 数据库迁移指南）中查看迁移指南。