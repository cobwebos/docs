---
title: 在 Microsoft Azure PowerShell 中使用 Azure 数据库迁移服务模块从本地 SQL Server 迁移到 Azure SQL DB MI | Microsoft Docs
description: 了解如何使用 Azure PowerShell 从本地 SQL Server 迁移到 Azure SQL DB MI。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: ''
ms.reviewer: ''
ms.service: database-migration
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 08/13/2018
ms.openlocfilehash: 7bd7e7a4cb78cf8a9f818936c980b47a2e7865e7
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2018
ms.locfileid: "40099629"
---
# <a name="migrate-sql-server-on-premises-to-azure-sql-db-using-azure-powershell"></a>使用 Azure PowerShell 从本地 SQL Server 迁移到 Azure SQL DB
在本文中，我们将使用 Microsoft Azure PowerShell 将还原为 SQL Server 2005 或更高版本的本地实例的 **Adventureworks2012** 数据库迁移到 Azure SQL 数据库。 可以使用 Microsoft Azure PowerShell 中的 `AzureRM.DataMigration` 模块，将数据库从本地 SQL Server 实例迁移到 Azure SQL 数据库。

在本文中，学习如何：
> [!div class="checklist"]
> * 创建资源组。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务实例中创建迁移项目。
> * 运行迁移。

## <a name="prerequisites"></a>先决条件
若要完成这些步骤，需满足以下条件：

- [SQL Server 2016 或更高版本](https://www.microsoft.com/sql-server/sql-server-downloads)（任意版本）
- 启用 TCP / IP 协议，使用 SQL Server Express 安装的情况下会默认禁用该协议。 遵循[启用或禁用服务器网络协议](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文启用 TCP/IP 协议。
- 配置[针对数据库引擎访问的 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
- Azure SQL 数据库实例。 可以按照[在 Azure 门户中创建 Azure SQL 数据库](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal)一文中的详细信息来创建 Azure SQL 数据库实例。
- [数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更高版本。
- 已使用 Azure 资源管理器部署模型创建了一个 VNET，该 VNET 使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 使 Azure 数据库迁移服务与本地源服务器建立站点到站点连接。
- 已使用[执行 SQL Server 迁移评估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)一文中所述的数据迁移助手完成对本地数据库和架构迁移的评估
- 使用 [Install-Module PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1) 从 PowerShell 库下载并安装 AzureRM.DataMigration 模块
- 确保用于连接到源 SQL Server 实例的凭据具有 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 权限。
- 确保用于连接到目标 Azure SQL DB 实例的凭据具有目标 Azure SQL 数据库的 CONTROL DATABASE 权限。
- Azure 订阅。 如果没有订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-your-microsoft-azure-subscription"></a>登录到 Microsoft Azure 订阅
根据[使用 Azure PowerShell 登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps?view=azurermps-4.4.1)一文中的说明，使用 PowerShell 登录到 Azure 订阅。

## <a name="create-a-resource-group"></a>创建资源组
Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 请先创建资源组，然后才能创建虚拟机。

使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) 命令创建资源组。 

以下示例在“EastUS”区域创建名为“myResourceGroup”的资源组。

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```
## <a name="create-an-instance-of-the-azure-database-migration-service"></a>创建 Azure 数据库迁移服务的实例 
可以通过 `New-AzureRmDataMigrationService` cmdlet 创建 Azure 数据库迁移服务的新实例。 此 cmdlet 需要以下必需参数：
- Azure 资源组名称。 可以使用 [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-4.4.1) 命令创建前述 Azure 资源组，并提供其名称作为参数。
- 服务名称。 与 Azure 数据库迁移服务的所需唯一服务名称相对应的字符串 
- *位置*。 指定服务的位置。 指定 Azure 数据中心位置，例如“美国西部”或“东南亚”
- Sku。 此参数对应于 DMS Sku 名称。 目前支持的 Sku 名称为 Basic_1vCore、Basic_2vCores、GeneralPurpose_4vCores
- 虚拟子网标识符。 可以使用 cmdlet [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-4.4.1) 创建子网。 

以下示例使用名为 *MyVNET* 的虚拟网络和名为 *MySubnet* 的子网，在位于“美国东部”的资源组 *MyDMSResourceGroup* 中创建名为 *MyDMS* 的服务。

```powershell
 $vNet = Get-AzureRmVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzureRmDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>创建迁移项目
在创建 Azure 数据库迁移服务实例以后，创建迁移项目。 Azure 数据库迁移服务项目需要源和目标实例的连接信息，以及要在项目中迁移的数据库的列表。

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>创建源和目标连接的数据库连接信息对象
可以使用 `New-AzureRmDmsConnInfo` cmdlet 创建数据库连接信息对象。 此 cmdlet 需要以下参数：
- ServerType。 请求的数据库连接的类型，例如 SQL、Oracle 或 MySQL。 将 SQL 用于 SQL Server 和 Azure SQL。
- DataSource。 SQL Server 实例或 Azure SQL 数据库的名称或 IP。
- AuthType。 连接的身份验证类型，可以为 SqlAuthentication 或 WindowsAuthentication。
- TrustServerCertificate 参数设置的值用于指示在绕过验证信任的证书链时，是否对通道加密。 可以是 true 或 false。

以下示例使用 SQL 身份验证为名为 MySourceSQLServer 的源 SQL Server 创建连接信息对象： 

```powershell
$sourceConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

以下示例演示如何使用 SQL 身份验证，为名为“targetmanagedinstance.database.windows.net”的 Azure SQL 数据库托管实例服务器创建连接信息：

```powershell
$targetConnInfo = New-AzureRmDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>为迁移项目提供数据库
创建 `AzureRmDataMigrationDatabaseInfo` 对象的列表，以便指定 Azure 数据库迁移项目中的数据库（可以作为创建项目所需的参数提供）。 Cmdlet `New-AzureRmDataMigrationDatabaseInfo` 可用于创建 AzureRmDataMigrationDatabaseInfo。 

以下示例为 **AdventureWorks** 数据库创建 `AzureRmDataMigrationDatabaseInfo` 项目，并将其添加到列表，以便在创建项目时作为参数提供。

```powershell
$dbInfo1 = New-AzureRmDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
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
  -TargetType SQLMI `
  -SourceConnection $sourceConnInfo `
  -TargetConnection $targetConnInfo `
  -DatabaseInfo $dbList
```

## <a name="create-and-start-a-migration-task"></a>创建并启动迁移任务
最后，创建并启动 Azure 数据库迁移任务。 除了作为先决条件为已创建的项目提供的信息，Azure 数据库迁移任务还需要源和目标的连接凭据信息，以及要迁移的数据库表的列表。 

### <a name="create-credential-parameters-for-source-and-target"></a>创建源和目标的凭据参数
可以将连接安全凭据作为 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象创建。 

以下示例显示了如何为源和目标连接创建 PSCredential 对象，将密码作为字符串变量 $sourcePassword 和 $targetPassword 提供。 

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-backup-fileshare-object"></a>创建备份 fileshare 对象
现在，使用 New-AzureRmDmsFileShare cmdlet 创建 FileShare 对象，用于 Azure 数据库迁移服务可将源数据库备份迁移到的本地 SMB 网络共享。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzureRmDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>创建选定的数据库对象
下一步是使用 New-AzureRmDmsSelectedDB cmdlet 选择源和目标数据库，如以下示例中所示：

```powershell
$selectedDbs = @()
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure 存储容器的 SAS URI
创建包含 SAS URI 的变量，使 Azure 数据库迁移服务能够访问它要将备份文件上传到的存储帐户容器。

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="select-logins"></a>选择登录名
按以下示例中所示，创建要迁移的登录名列表：请注意，当前 DMS 仅支持迁移 SQL 登录名。 

```powershell
$selectedLogins = @("user1", "user2")
```

### <a name="select-agent-jobs"></a>选择代理作业
按以下示例中所示，创建要迁移的代理作业列表：

>[!NOTE]
>当前 DMS 仅支持包含 T-SQL 子系统作业步骤的作业。

```powershell
$selectedAgentJobs = @("agentJob1", "agentJob2")
```

### <a name="create-and-start-a-migration-task"></a>创建并启动迁移任务

使用 `New-AzureRmDataMigrationTask` cmdlet 创建并启动迁移任务。 此 cmdlet 需要以下参数：
- TaskType。 要为 SQL Server 创建的迁移任务类型，预期需要 Azure SQL 数据库托管实例迁移类型 *MigrateSqlServerSqlDbMi*。 
- ResourceGroupName。 要在其中创建任务的 Azure 资源组的名称。
- ServiceName。 要在其中创建任务的 Azure 数据库迁移服务实例。
- ProjectName。 要在其中创建任务的 Azure 数据库迁移服务项目的名称。 
- TaskName。 要创建的任务的名称。 
- *SourceConnection*。 表示源 SQL Server 连接的 AzureRmDmsConnInfo 对象。
- *TargetConnection*。 表示目标 Azure SQL 数据库托管实例连接的 AzureRmDmsConnInfo 对象。
- SourceCred。 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象，用于连接到源服务器。
- TargetCred。 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象，用于连接到目标服务器。
- SelectedDatabase。 表示源和目标数据库映射的 AzureRmDataMigrationSelectedDB 对象。
- *BackupFileShare*。 表示 Azure 数据库迁移服务可在其上创建源数据库备份的本地网络共享的 FileShare 对象。
- *BackupBlobSasUri*。 为 Azure 数据库迁移服务提供存储帐户容器访问权限的 SAS URI，该服务将备份文件上传到该容器。 了解如何获取 Blob 容器的 SAS URI。
- *SelectedLogins*。 要迁移的选定登录名列表。
- *SelectedAgentJobs*。 要迁移的选定代理作业列表。

以下示例创建并启动名为 myDMSTask 的迁移任务：

```powershell
$migTask = New-AzureRmDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs`
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
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