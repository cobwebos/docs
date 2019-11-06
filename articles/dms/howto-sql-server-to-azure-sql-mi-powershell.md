---
title: 使用数据库迁移服务和 PowerShell 将 SQL Server 迁移到 Azure SQL 数据库托管实例 | Microsoft Docs
description: 了解如何使用 Azure PowerShell 从本地 SQL Server 迁移到 Azure SQL 数据库托管实例。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: 426285340a9401aa6c84a7ee07f172eee6791d9e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163951"
---
# <a name="migrate-sql-server-on-premises-to-an-azure-sql-database-managed-instance-using-azure-powershell"></a>使用 Azure PowerShell 将本地 SQL Server 迁移到 Azure SQL 数据库托管实例
本文介绍如何使用 Microsoft Azure PowerShell 将还原到 SQL Server 2005 或更高版本的本地实例的**Adventureworks2016**数据库迁移到 Azure SQL 数据库托管实例。 可以使用 Microsoft Azure PowerShell 中的 `Az.DataMigration` 模块，将数据库从本地 SQL Server 实例迁移到 Azure SQL 数据库托管实例。

在本文中，学习如何：
> [!div class="checklist"]
>
> * 创建资源组。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务实例中创建迁移项目。
> * 运行迁移。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文包含有关如何执行联机迁移和脱机迁移的详细信息。

## <a name="prerequisites"></a>先决条件

若要完成这些步骤，需满足以下条件：

* [SQL Server 2016 或更高版本](https://www.microsoft.com/sql-server/sql-server-downloads)（任意版本）。
* **AdventureWorks2016** 数据库的本地副本，可从[此处](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)下载。
* 启用 TCP / IP 协议，使用 SQL Server Express 安装的情况下会默认禁用该协议。 遵循[启用或禁用服务器网络协议](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文启用 TCP/IP 协议。
* 配置[针对数据库引擎访问的 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
* Azure SQL 数据库托管实例。 可根据[创建 Azure SQL 数据库托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)一文中的详述创建 Azure SQL 数据库托管实例。
* 下载并安装[数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595) v3.3 或更高版本。
* 使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 Azure 虚拟网络 (VNet)，它将使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 为本地源服务器提供站点到站点连接。
* 已使用[执行 SQL Server 迁移评估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)一文中所述的数据迁移助手完成对本地数据库和架构迁移的评估。
* 使用 `Az.DataMigration`Install-Module PowerShell cmdlet[ 从 PowerShell 库下载并安装 ](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1) 模块（0.7.2 或更高版本）。
* 确保用于连接到源 SQL Server 实例的凭据具有 [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql) 权限。
* 确保用于连接到目标 Azure SQL 数据库实例的凭据具有目标 Azure SQL 数据库托管实例数据库的 CONTROL DATABASE 权限。

    > [!IMPORTANT]
    > 对于联机迁移，必须事先安装 Azure Active Directory 凭据。 有关详细信息，请参阅[使用门户创建可访问资源的 Azure AD 应用程序和服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)一文。

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>登录到 Microsoft Azure 订阅

使用 PowerShell 登录到 Azure 订阅。 有关详细信息，请参阅[使用 Azure PowerShell 登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)一文。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用 [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。

以下示例在“美国东部”区域创建名为 *myResourceGroup* 的资源组。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>创建 Azure 数据库迁移服务的实例

可以通过 `New-AzDataMigrationService` cmdlet 创建 Azure 数据库迁移服务的新实例。
此 cmdlet 需要以下必需参数：

* Azure 资源组名称。 可以使用 [`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) 命令创建前述 Azure 资源组，并提供其名称作为参数。
* 服务名称。 与 Azure 数据库迁移服务的所需唯一服务名称相对应的字符串。
* *位置*。 指定服务的位置。 指定 Azure 数据中心位置，例如 "美国西部" 或 "东南亚"。
* Sku。 此参数对应于 DMS Sku 名称。 目前支持的 SKU 名称为 *Basic_1vCore*、*Basic_2vCores* 和 *GeneralPurpose_4vCores*。
* 虚拟子网标识符。 可以使用 cmdlet [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig) 创建子网。

以下示例使用名为*MyVNET*的虚拟网络和名为*MySubnet*的子网，在*美国东部*区域的资源组*MyDMSResourceGroup*中创建名为*MyDMS*的服务。

> [!IMPORTANT]
> 以下代码片段用于脱机迁移，不需要基于高级 SKU 的 Azure 数据库迁移服务实例。 对于联机迁移，-Sku 参数的值必须包含高级 SKU。

```powershell
$vNet = Get-AzVirtualNetwork -ResourceGroupName MyDMSResourceGroup -Name MyVNET

$vSubNet = Get-AzVirtualNetworkSubnetConfig -VirtualNetwork $vNet -Name MySubnet

$service = New-AzDms -ResourceGroupName myResourceGroup `
  -ServiceName MyDMS `
  -Location EastUS `
  -Sku Basic_2vCores `  
  -VirtualSubnetId $vSubNet.Id`
```

## <a name="create-a-migration-project"></a>创建迁移项目

在创建 Azure 数据库迁移服务实例以后，创建迁移项目。 Azure 数据库迁移服务项目需要源和目标实例的连接信息，以及要在项目中迁移的数据库的列表。

### <a name="create-a-database-connection-info-object-for-the-source-and-target-connections"></a>创建源和目标连接的数据库连接信息对象

可以使用 `New-AzDmsConnInfo` cmdlet 创建数据库连接信息对象，这需要使用以下参数：

* ServerType。 请求的数据库连接的类型，例如 SQL、Oracle 或 MySQL。 将 SQL 用于 SQL Server 和 Azure SQL。
* DataSource。 SQL Server 实例或 Azure SQL 数据库实例的名称或 IP。
* AuthType。 连接的身份验证类型，可以为 SqlAuthentication 或 WindowsAuthentication。
* *TrustServerCertificate*。 此参数设置的值用于指示在绕过验证信任的证书链时，是否对通道加密。 值可为 `$true` 或 `$false`。

以下示例使用 SQL 身份验证为名为 *MySourceSQLServer* 的源 SQL Server 创建连接信息对象：

```powershell
$sourceConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource MySourceSQLServer `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$true
```

下一个示例演示如何使用 SQL 身份验证创建名为 "targetmanagedinstance.database.windows.net" 的 Azure SQL 数据库托管实例服务器的连接信息：

```powershell
$targetConnInfo = New-AzDmsConnInfo -ServerType SQL `
  -DataSource "targetmanagedinstance.database.windows.net" `
  -AuthType SqlAuthentication `
  -TrustServerCertificate:$false
```

### <a name="provide-databases-for-the-migration-project"></a>为迁移项目提供数据库

创建 `AzDataMigrationDatabaseInfo` 对象的列表，以便指定 Azure 数据库迁移服务项目中的数据库（可以作为创建项目所需的参数提供）。 可以使用 cmdlet `New-AzDataMigrationDatabaseInfo` 创建 `AzDataMigrationDatabaseInfo`。

以下示例为 `AzDataMigrationDatabaseInfo`AdventureWorks2016**数据库创建** 项目，并将其添加到列表，以便在创建项目时作为参数提供。

```powershell
$dbInfo1 = New-AzDataMigrationDatabaseInfo -SourceDatabaseName AdventureWorks
$dbList = @($dbInfo1)
```

### <a name="create-a-project-object"></a>创建项目对象

最后，*可以使用 `New-AzDataMigrationProject` 创建名为* *MyDMSProject*的 Azure 数据库迁移服务项目，并添加以前创建的源和目标连接以及要迁移的数据库的列表。

```powershell
$project = New-AzDataMigrationProject -ResourceGroupName myResourceGroup `
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

接下来，创建并启动 Azure 数据库迁移服务任务。 此任务需要源和目标的连接凭据信息、要迁移的数据库表的列表，以及在作为先决条件创建的项目中提供的信息。

### <a name="create-credential-parameters-for-source-and-target"></a>创建源和目标的凭据参数

创建连接安全凭据作为 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象。

以下示例显示了如何为源和目标连接创建 *PSCredential* 对象，将密码作为字符串变量 *$sourcePassword* 和 *$targetPassword* 提供。

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>创建备份 FileShare 对象

现在，使用 `New-AzDmsFileShare` cmdlet 创建 FileShare 对象，用于 Azure 数据库迁移服务可将源数据库备份迁移到的本地 SMB 网络共享。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>创建选定的数据库对象

下一步是使用 `New-AzDmsSelectedDB` cmdlet 选择源和目标数据库。

以下示例用于将 SQL Server 中的单个数据库迁移到 Azure SQL 数据库托管实例：

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

如果整个 SQL Server 实例需要直接迁移到 Azure SQL 数据库托管实例，可以使用下面提供的从源提取所有数据库的循环。 在以下示例中，请为 $Server、$SourceUserName 和 $SourcePassword 提供源 SQL Server 详细信息。

```powershell
$Query = "(select name as Database_Name from master.sys.databases where Database_id>4)";
$Databases= (Invoke-Sqlcmd -ServerInstance "$Server" -Username $SourceUserName
-Password $SourcePassword -database master -Query $Query)
$selectedDbs=@()
foreach($DataBase in $Databases.Database_Name)
    {
      $SourceDB=$DataBase
      $TargetDB=$DataBase
      
$selectedDbs += New-AzureRmDmsSelectedDB -MigrateSqlServerSqlDbMi `
                                              -Name $SourceDB `
                                              -TargetDatabaseName $TargetDB `
                                              -BackupFileShare $backupFileShare
      }
```

### <a name="sas-uri-for-azure-storage-container"></a>Azure 存储容器的 SAS URI

创建包含 SAS URI 的变量，使 Azure 数据库迁移服务能够访问它要将备份文件上传到的存储帐户容器。

```powershell
$blobSasUri="https://mystorage.blob.core.windows.net/test?st=2018-07-13T18%3A10%3A33Z&se=2019-07-14T18%3A10%3A00Z&sp=rwdl&sv=2018-03-28&sr=c&sig=qKlSA512EVtest3xYjvUg139tYSDrasbftY%3D"
```

### <a name="additional-configuration-requirements"></a>其他配置要求

需要满足其他几项要求，但这些要求取决于执行的是脱机还是联机迁移。

#### <a name="offline-migrations"></a>脱机迁移

对于脱机迁移，请执行以下附加配置任务。

* **选择登录名**。 按以下示例中所示，创建要迁移的登录名列表：

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 数据库迁移服务仅支持迁移 SQL 登录名。

* **选择代理作业**。 按以下示例中所示，创建要迁移的代理作业列表：

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 数据库迁移服务仅支持包含 T-SQL 子系统作业步骤的作业。

#### <a name="online-migrations"></a>联机迁移

对于联机迁移，请执行以下附加配置任务。

* **配置 Azure Active Directory 应用**

    ```powershell
    $pwd = "Azure App Key"
    $appId = "Azure App Id"
    $AppPasswd = ConvertTo-SecureString $pwd -AsPlainText -Force
    $app = New-AzDmsAdApp -ApplicationId $appId -AppKey $AppPasswd
    ```

* **配置存储资源**

    ```powershell
    $storageResourceId = "Storage Resource Id"
    ```

### <a name="create-and-start-the-migration-task"></a>创建并启动迁移任务

使用 `New-AzDataMigrationTask` cmdlet 创建并启动迁移任务。

#### <a name="specify-parameters"></a>指定参数

##### <a name="common-parameters"></a>通用参数

无论是执行脱机迁移还是联机迁移，`New-AzDataMigrationTask` cmdlet 都需要以下参数：

* TaskType。 要创建的迁移任务的类型。对于从 SQL Server 到 Azure SQL 数据库托管实例的迁移类型，此项应为 *MigrateSqlServerSqlDbMi*。 
* ResourceGroupName。 要在其中创建任务的 Azure 资源组的名称。
* ServiceName。 要在其中创建任务的 Azure 数据库迁移服务实例。
* ProjectName。 要在其中创建任务的 Azure 数据库迁移服务项目的名称。 
* TaskName。 要创建的任务的名称。 
* *SourceConnection*。 表示源 SQL Server 连接的 AzDmsConnInfo 对象。
* *TargetConnection*。 表示目标 Azure SQL 数据库托管实例连接的 AzDmsConnInfo 对象。
* SourceCred。 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象，用于连接到源服务器。
* TargetCred。 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象，用于连接到目标服务器。
* SelectedDatabase。 表示源和目标数据库映射的 AzDataMigrationSelectedDB 对象。
* *BackupFileShare*。 表示 Azure 数据库迁移服务可在其上创建源数据库备份的本地网络共享的 FileShare 对象。
* *BackupBlobSasUri*。 为 Azure 数据库迁移服务提供存储帐户容器访问权限的 SAS URI，该服务将备份文件上传到该容器。 了解如何获取 Blob 容器的 SAS URI。
* *SelectedLogins*。 要迁移的选定登录名列表。
* *SelectedAgentJobs*。 要迁移的选定代理作业列表。

##### <a name="additional-parameters"></a>其他参数

`New-AzDataMigrationTask` cmdlet 还需要特定于你所执行的迁移类型（脱机或联机）的参数。

* **脱机迁移** 对于脱机迁移，`New-AzDataMigrationTask` cmdlet 还需要以下参数：

  * *SelectedLogins*。 要迁移的选定登录名列表。
  * *SelectedAgentJobs*。 要迁移的选定代理作业列表。

* **联机迁移**。 对于联机迁移，`New-AzDataMigrationTask` cmdlet 还需要以下参数：

* *AzureActiveDirectoryApp*。 Active Directory 应用程序。
* *StorageResourceID*。 存储帐户的资源 ID。

#### <a name="create-and-start-an-offline-migration-task"></a>创建并启动脱机迁移任务

以下示例创建并启动名为 **myDMSTask** 的脱机迁移任务：

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMi `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -BackupBlobSasUri $blobSasUri `
  -SelectedLogins $selectedLogins `
  -SelectedAgentJobs $selectedJobs `
```

#### <a name="create-and-start-an-online-migration-task"></a>创建并启动联机迁移任务

以下示例创建并启动名为 **myDMSTask** 的联机迁移任务：

```powershell
$migTask = New-AzDataMigrationTask -TaskType MigrateSqlServerSqlDbMiSync `
  -ResourceGroupName myResourceGroup `
  -ServiceName $service.Name `
  -ProjectName $project.Name `
  -TaskName myDMSTask `
  -SourceConnection $sourceConnInfo `
  -SourceCred $sourceCred `
  -TargetConnection $targetConnInfo `
  -TargetCred $targetCred `
  -SelectedDatabase  $selectedDbs `
  -BackupFileShare $backupFileShare `
  -SelectedDatabase $selectedDbs `
  -AzureActiveDirectoryApp $app `
  -StorageResourceId $storageResourceId
```

## <a name="monitor-the-migration"></a>监视迁移

若要监视迁移，请执行以下任务。

1. 将所有迁移详细信息合并到名为 $CheckTask 的变量中。

    若要合并迁移详细信息（例如，与迁移相关的属性、状态和数据库信息），请使用以下代码片段：

    ```powershell
    $CheckTask= Get-AzDataMigrationTask     -ResourceGroupName myResourceGroup `
                                            -ServiceName $service.Name `
                                        -ProjectName $project.Name `
                                            -Name myDMSTask `
                                            -ResultType DatabaseLevelOutput `
                        -Expand 
    Write-Host ‘$CheckTask.ProjectTask.Properties.Output’
    ```

2. 使用 `$CheckTask` 变量获取迁移任务的当前状态。

    可以使用 `$CheckTask` 变量获取迁移任务的当前状态，并可以通过查询任务的状态属性来监视正在运行的迁移任务，如以下示例所示：

    ```powershell
    if (($CheckTask.ProjectTask.Properties.State -eq "Running") -or ($CheckTask.ProjectTask.Properties.State -eq "Queued"))
    {
      Write-Host "migration task running"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Succeeded")
    { 
      Write-Host "Migration task is completed Successfully"
    }
    else if($CheckTask.ProjectTask.Properties.State -eq "Failed" -or $CheckTask.ProjectTask.Properties.State -eq "FailedInputValidation" -or $CheckTask.ProjectTask.Properties.State -eq "Faulted")
    { 
      Write-Host "Migration Task Failed"
    }
    ```

## <a name="performing-the-cutover-online-migrations-only"></a>执行交接（仅限联机迁移）

在联机迁移过程中，将执行数据库的完整备份和还原，然后继续还原 BackupFileShare 中存储的事务日志。

在 Azure SQL 数据库托管实例中的数据库已使用最新数据更新并与源数据库同步后，你可以执行交接。

以下示例将完成交接/迁移。 用户可自行调用此命令。

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>删除 Azure 数据库迁移服务的实例

完成迁移后，可以删除 Azure 数据库迁移服务实例：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>其他资源

有关其他迁移方案（源/目标对）的信息，请参阅 Microsoft[数据库迁移指南](https://datamigration.microsoft.com/)。

## <a name="next-steps"></a>后续步骤

在[什么是 Azure 数据库迁移服务？](https://docs.microsoft.com/azure/dms/dms-overview)一文中详细了解 Azure 数据库迁移服务。
