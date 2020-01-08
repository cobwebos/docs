---
title: PowerShell：将 SQL Server 迁移到 SQL 托管实例
titleSuffix: Azure Database Migration Service
description: 了解如何使用 Azure PowerShell 和 Azure 数据库迁移服务从本地 SQL Server 迁移到 Azure SQL 数据库托管实例。
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 04/29/2019
ms.openlocfilehash: 227ef72b53b7334cffcb485e23c3e4227613b344
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437919"
---
# <a name="migrate-sql-server-to-sql-database-managed-instance-with-powershell--azure-database-migration-service"></a>通过 PowerShell & Azure 数据库迁移服务将 SQL Server 迁移到 SQL 数据库托管实例
本文介绍如何使用 Microsoft Azure PowerShell 将还原到 SQL Server 2005 或更高版本的本地实例的**Adventureworks2016**数据库迁移到 Azure SQL 数据库托管实例。 可以使用 Microsoft Azure PowerShell 中的 `Az.DataMigration` 模块将数据库从本地 SQL Server 实例迁移到 Azure SQL 数据库托管实例。

在本文中，学习如何：
> [!div class="checklist"]
>
> * 创建资源组。
> * 创建 Azure 数据库迁移服务的实例。
> * 在 Azure 数据库迁移服务的实例中创建迁移项目。
> * 运行迁移。

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

本文详细介绍了如何执行联机和脱机迁移。

## <a name="prerequisites"></a>必备组件

若要完成这些步骤，需满足以下条件：

* [SQL Server 2016 或更高](https://www.microsoft.com/sql-server/sql-server-downloads)版本（任意版本）。
* **AdventureWorks2016**数据库的本地副本，可在[此处](https://docs.microsoft.com/sql/samples/adventureworks-install-configure?view=sql-server-2017)下载。
* 启用 TCP / IP 协议，使用 SQL Server Express 安装的情况下会默认禁用该协议。 遵循[启用或禁用服务器网络协议](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure)一文启用 TCP/IP 协议。
* 配置[针对数据库引擎访问的 Windows 防火墙](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)。
* Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户](https://azure.microsoft.com/free/)。
* 一个 Azure SQL 数据库托管实例。 可以遵循[创建 AZURE Sql 数据库托管实例](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)一文中的详细信息来创建 Azure sql 数据库托管实例。
* 下载并安装[数据迁移助手](https://www.microsoft.com/download/details.aspx?id=53595)3.3 或更高版本。
* 使用 Azure 资源管理器部署模型创建的 Azure 虚拟网络（VNet），该模型通过使用[ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)或[VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)向 azure 数据库迁移服务提供与本地源服务器之间的站点到站点连接。
* 使用数据迁移助手完成了本地数据库和架构迁移的评估，如[执行 SQL Server 迁移评估](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem)一文中所述。
* 使用 0.7.2 [PowerShell cmdlet](https://docs.microsoft.com/powershell/module/powershellget/Install-Module?view=powershell-5.1)从 PowerShell 库下载并安装 `Az.DataMigration` 模块（版本或更高版本）。
* 若要确保用于连接到源 SQL Server 实例的凭据具有[CONTROL Server](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql)权限。
* 为了确保用于连接到目标 Azure SQL 数据库托管实例的凭据具有对目标 Azure SQL 数据库托管实例数据库的 CONTROL DATABASE 权限。

    > [!IMPORTANT]
    > 对于联机迁移，必须已经已设置 Azure Active Directory 凭据。 有关详细信息，请参阅[使用门户创建可访问资源的 Azure AD 应用程序和服务主体一](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)文。

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>登录到 Microsoft Azure 订阅

使用 PowerShell 登录到你的 Azure 订阅。 有关详细信息，请参阅文章[Azure PowerShell 的登录](https://docs.microsoft.com/powershell/azure/authenticate-azureps)。

## <a name="create-a-resource-group"></a>创建资源组

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。

使用[`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)命令创建资源组。

以下示例在 "*美国东部*" 区域创建名为 " *myResourceGroup* " 的资源组。

```powershell
New-AzResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

## <a name="create-an-instance-of-azure-database-migration-service"></a>创建 Azure 数据库迁移服务的实例

可以通过 `New-AzDataMigrationService` cmdlet 创建 Azure 数据库迁移服务的新实例。
此 cmdlet 需要以下必需参数：

* Azure 资源组名称。 你可以使用[`New-AzResourceGroup`](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup)命令创建以前显示的 Azure 资源组，并将其名称作为参数提供。
* 服务名称。 与所需的 Azure 数据库迁移服务的唯一服务名称相对应的字符串。
* *位置*。 指定服务的位置。 指定 Azure 数据中心位置，例如 "美国西部" 或 "东南亚"。
* Sku。 此参数对应于 DMS Sku 名称。 当前支持的 Sku 名称为*Basic_1vCore*、 *Basic_2vCores* *GeneralPurpose_4vCores*。
* 虚拟子网标识符。 可以使用 cmdlet [`New-AzVirtualNetworkSubnetConfig`](https://docs.microsoft.com//powershell/module/az.network/new-azvirtualnetworksubnetconfig)来创建子网。

以下示例使用名为*MyVNET*的虚拟网络和名为*MySubnet*的子网，在*美国东部*区域的资源组*MyDMSResourceGroup*中创建名为*MyDMS*的服务。

> [!IMPORTANT]
> 下面的代码段适用于脱机迁移，无需基于高级 SKU 的 Azure 数据库迁移服务实例。 对于联机迁移，-Sku 参数的值必须包含高级 SKU。

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

您可以使用 `New-AzDmsConnInfo` cmdlet 创建数据库连接信息对象，该对象需要以下参数：

* ServerType。 请求的数据库连接的类型，例如 SQL、Oracle 或 MySQL。 将 SQL 用于 SQL Server 和 Azure SQL。
* DataSource。 SQL Server 实例或 Azure SQL 数据库实例的名称或 IP。
* AuthType。 连接的身份验证类型，可以为 SqlAuthentication 或 WindowsAuthentication。
* *TrustServerCertificate*。 此参数设置一个值，该值指示在跳过证书链以验证信任时是否对通道进行了加密。 该值可以是 `$true` 或 `$false`。

下面的示例使用 SQL 身份验证创建名为*MySourceSQLServer*的源 SQL Server 的连接信息对象：

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

创建一个 `AzDataMigrationDatabaseInfo` 对象列表，该列表将数据库指定为 Azure 数据库迁移服务项目的一部分，该项目可作为用于创建项目的参数提供。 可以使用 cmdlet `New-AzDataMigrationDatabaseInfo` 来创建 `AzDataMigrationDatabaseInfo`。

下面的示例创建**AdventureWorks2016**数据库的 `AzDataMigrationDatabaseInfo` 项目，并将其添加到要作为创建项目的参数提供的列表。

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

接下来，创建并启动 Azure 数据库迁移服务任务。 此任务需要源和目标的连接凭据信息，以及要迁移的数据库表的列表以及已作为必备项创建的项目所提供的信息。

### <a name="create-credential-parameters-for-source-and-target"></a>创建源和目标的凭据参数

创建连接安全凭据作为[PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0)对象。

下面的示例演示了如何为源连接和目标连接创建*PSCredential*对象，并将密码作为 *$sourcePassword*和 *$targetPassword*的字符串变量提供。

```powershell
$secpasswd = ConvertTo-SecureString -String $sourcePassword -AsPlainText -Force
$sourceCred = New-Object System.Management.Automation.PSCredential ($sourceUserName, $secpasswd)
$secpasswd = ConvertTo-SecureString -String $targetPassword -AsPlainText -Force
$targetCred = New-Object System.Management.Automation.PSCredential ($targetUserName, $secpasswd)
```

### <a name="create-a-backup-fileshare-object"></a>创建备份文件共享对象

现在，创建一个文件共享对象，该对象表示 Azure 数据库迁移服务可使用 `New-AzDmsFileShare` cmdlet 来执行源数据库备份的本地 SMB 网络共享。

```powershell
$backupPassword = ConvertTo-SecureString -String $password -AsPlainText -Force
$backupCred = New-Object System.Management.Automation.PSCredential ($backupUserName, $backupPassword)

$backupFileSharePath="\\10.0.0.76\SharedBackup"
$backupFileShare = New-AzDmsFileShare -Path $backupFileSharePath -Credential $backupCred
```

### <a name="create-selected-database-object"></a>创建选定的数据库对象

下一步是使用 `New-AzDmsSelectedDB` cmdlet 选择源和目标数据库。

下面的示例用于将单个数据库从 SQL Server 迁移到 Azure SQL 数据库托管实例：

```powershell
$selectedDbs = @()
$selectedDbs += New-AzDmsSelectedDB -MigrateSqlServerSqlDbMi `
  -Name AdventureWorks2016 `
  -TargetDatabaseName AdventureWorks2016 `
  -BackupFileShare $backupFileShare `
```

如果整个 SQL Server 实例需要直接转换为 Azure SQL 数据库托管实例，则下面提供了从源获取所有数据库的循环。 在下面的示例中，为 $Server、$SourceUserName 和 $SourcePassword 提供源 SQL Server 详细信息。

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

需要满足一些额外的要求，但它们会有所不同，具体取决于你是要执行脱机迁移还是联机迁移。

#### <a name="offline-migrations"></a>脱机迁移

如果仅用于脱机迁移，请执行以下附加配置任务。

* **选择 "登录名**"。 创建要迁移的登录名的列表，如以下示例中所示：

    ```powershell
    $selectedLogins = @("user1", "user2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 数据库迁移服务仅支持迁移 SQL 登录名。

* **选择 "代理作业**"。 创建要迁移的代理作业的列表，如以下示例中所示：

    ```powershell
    $selectedAgentJobs = @("agentJob1", "agentJob2")
    ```

    > [!IMPORTANT]
    > 目前，Azure 数据库迁移服务仅支持具有 T-sql 子系统作业步骤的作业。

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
* *TargetConnection*。 AzDmsConnInfo 对象，表示目标 Azure SQL 数据库托管实例连接。
* SourceCred。 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象，用于连接到源服务器。
* TargetCred。 [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?redirectedfrom=MSDN&view=powershellsdk-1.1.0) 对象，用于连接到目标服务器。
* SelectedDatabase。 AzDataMigrationSelectedDB 对象，表示源和目标数据库映射。
* *BackupFileShare*。 表示 Azure 数据库迁移服务可在其上创建源数据库备份的本地网络共享的 FileShare 对象。
* *BackupBlobSasUri*。 为 Azure 数据库迁移服务提供存储帐户容器访问权限的 SAS URI，该服务将备份文件上传到该容器。 了解如何获取 Blob 容器的 SAS URI。
* *SelectedLogins*。 要迁移的选定登录名列表。
* *SelectedAgentJobs*。 要迁移的选定代理作业列表。

##### <a name="additional-parameters"></a>其他参数

`New-AzDataMigrationTask` cmdlet 还需要所执行的迁移类型（脱机或联机）所特有的参数。

* **脱机迁移**。 对于脱机迁移，`New-AzDataMigrationTask` cmdlet 还需要以下参数：

  * *SelectedLogins*。 要迁移的选定登录名列表。
  * *SelectedAgentJobs*。 要迁移的选定代理作业列表。

* **联机迁移**。 对于联机迁移，`New-AzDataMigrationTask` cmdlet 还需要以下参数。

* *AzureActiveDirectoryApp*。 Active Directory 应用程序。
* *StorageResourceID*。 存储帐户的资源 ID。

#### <a name="create-and-start-an-offline-migration-task"></a>创建和启动脱机迁移任务

以下示例创建并启动一个名为**myDMSTask**的脱机迁移任务：

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

#### <a name="create-and-start-an-online-migration-task"></a>创建和启动联机迁移任务

以下示例创建并启动一个名为**myDMSTask**的联机迁移任务：

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

1. 将所有迁移详细信息合并到称为 $CheckTask 的变量中。

    若要合并迁移详细信息，如与迁移关联的属性、状态和数据库信息，请使用以下代码片段：

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

    若要使用 `$CheckTask` 变量获取迁移任务的当前状态，可以通过查询任务的 "状态" 属性来监视正在运行的迁移任务，如以下示例中所示：

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

## <a name="performing-the-cutover-online-migrations-only"></a>执行切换（仅限联机迁移）

通过联机迁移，将执行数据库的完整备份和还原，然后继续恢复存储在 BackupFileShare 中的事务日志。

如果使用最新的数据更新 Azure SQL 数据库托管实例中的数据库，并且该数据库与源数据库同步，则可以执行转换。

下面的示例将完成 cutover\migration。 用户自行调用此命令。

```powershell
$command = Invoke-AzDmsCommand -CommandType CompleteSqlMiSync `
                               -ResourceGroupName myResourceGroup `
                               -ServiceName $service.Name `
                               -ProjectName $project.Name `
                               -TaskName myDMSTask `
                               -DatabaseName "Source DB Name"
```

## <a name="deleting-the-instance-of-azure-database-migration-service"></a>正在删除 Azure 数据库迁移服务的实例

迁移完成后，可以删除 Azure 数据库迁移服务实例：

```powershell
Remove-AzDms -ResourceGroupName myResourceGroup -ServiceName MyDMS
```

## <a name="additional-resources"></a>其他资源

有关其他迁移方案（源/目标对）的信息，请参阅 Microsoft[数据库迁移指南](https://datamigration.microsoft.com/)。

## <a name="next-steps"></a>后续步骤

有关 Azure 数据库迁移服务的详细信息，请参阅[什么是 Azure 数据库迁移服务？](https://docs.microsoft.com/azure/dms/dms-overview)。
