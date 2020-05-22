---
title: 备份和还原数据库 - Azure SQL Edge（预览版）
description: 了解 Azure SQL Edge（预览版）中的备份和还原功能
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5c7bdbc49d8f1c6af7f38911919c660b03e1a37a
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593996"
---
# <a name="backup-and-restore-databases-in-azure-sql-edge-preview"></a>Azure SQL Edge（预览版）中的备份和还原数据库功能 

Azure SQL Edge 基于 Linux 上的 Microsoft SQL Server 数据库引擎的最新版本而构建，提供的备份和还原数据库功能与 Linux 上的 SQL Server 和容器中运行的 SQL Server 类似。 备份和还原组件为保护存储在 Azure SQL Edge 数据库中的数据提供了基本安全保障。 为了尽量降低灾难性数据丢失的风险，建议定期备份数据库，以便定期保存对数据的修改。 计划良好的备份和还原策略有助于保护数据库，使之免受各种故障导致的数据丢失的威胁。 测试策略，方法是先还原一组备份，然后恢复数据库，以便准备好对灾难进行有效的响应。

若要详细了解备份的重要性，请参阅 [SQL Server 数据库的备份和还原](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)。

Azure SQL Edge 支持备份到本地存储或 Azure blob 并从中还原。 有关备份到 Azure Blob 存储并从中还原的详细信息，请参阅[使用 Microsoft Azure Blob 存储服务进行 SQL Server 备份和还原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/)和 [SQL Server 备份到 URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)。

## <a name="backing-up-a-database-in-azure-sql-edge"></a>在 Azure SQL Edge 中备份数据库

Azure SQL Edge 支持的备份类型与 SQL Server 相同。 有关 SQL Server 中支持的备份类型的完整列表，请参阅[备份概述](/sql/relational-databases/backup-restore/backup-overview-sql-server/)。

> [!IMPORTANT] 
> 默认情况下，在 Azure SQL Edge 中创建的数据库使用简单恢复模式。 因此，无法在这些数据库上执行日志备份。 如果需要在这些数据库上执行日志备份，则管理员需要将数据库恢复模式更改为完全恢复模式。 有关 SQL Server 支持的恢复模式的完整列表，请参阅[恢复模式概述](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)。

### <a name="backup-to-local-disk"></a>备份到本地磁盘

在下面提供的示例中，使用“BACKUP DATABASE”Transact-SQL 命令在容器中创建了数据库备份。 在这个例子中，要创建一个名为“备份”的新文件夹来存储备份文件。

1. 创建用于备份的文件夹。 此命令需要在运行 Azure SQL Edge 容器的主机上执行。 在下面的命令中，将 <AzureSQLEdge_Container_Name> 替换为部署中的 Azure SQL Edge 容器的名称。

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. 使用 SQL Server Management Studio (SSMS) 或使用 Azure Data Studio (ADS) 连接到 Azure SQL Edge 实例，并运行备份数据库命令以备份用户数据库。 在下面的示例中，我们将对 IronOreSilicaPrediction 数据库进行备份。

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. 运行该命令后，如果数据库备份成功，将在 SSMS 或 ADS 的“结果”部分中看到类似以下内容的消息。

    ```txt
    10 percent processed.
    20 percent processed.
    30 percent processed.
    40 percent processed.
    50 percent processed.
    60 percent processed.
    70 percent processed.
    80 percent processed.
    90 percent processed.
    100 percent processed.
    Processed 51648 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction', file 'IronOreSilicaPrediction_log' on file 1.
    BACKUP DATABASE successfully processed 51650 pages in 3.588 seconds (112.461 MB/sec).

    Completion time: 2020-04-09T23:54:48.4957691-07:00
    ```

### <a name="backup-to-url"></a>备份到 URL

Azure SQL Edge 支持备份到页 blob 和块 blob。 有关页 blob 和块 blob 的详细信息，请参阅[备份到块 blob 和页 blob](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-ver15#blockbloborpageblob)。 在下面的示例中，正在将数据库 IronOreSilicaPrediction 备份到块 blob。 

1. 将备份配置为块 blob 的第一步是生成共享访问签名 (SAS) 令牌，该令牌可用于在 Azure SQL Edge 上创建 SQL Server 凭据。 该脚本创建了与存储访问策略关联的共享访问签名。 有关详细信息，请参阅[共享访问签名，第 1 部分：了解 SAS 模型](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)。 此脚本还编写在 SQL Server 上创建凭据时所需的 T-SQL 命令。 以下脚本假设你已有一个 Azure 订阅，并有一个用于备份的存储帐户和存储容器。

    ```PowerShell
    # Define global variables for the script  
    $subscriptionName='<your subscription name>'   # the name of subscription name you will use
    $resourcegroupName = '<your resource group name>' # the name of resource group you will use
    $storageAccountName= '<your storage account name>' # the storage account name you will use for backups
    $containerName= '<your storage container name>'  # the storage container name to which you will attach the SAS policy with its SAS token  
    $policyName = 'SASPolicy' # the name of the SAS policy  

    # adds an authenticated Azure account for use in the session
    Login-AzAccount

    # set the tenant, subscription and environment for use in the rest of
    Select-AzSubscription -Subscription $subscriptionName

    # Generate the SAS token
    $sa = Get-AzStorageAccount -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storagekey = Get-AzStorageAccountKey -ResourceGroupName $resourcegroupName -Name $storageAccountName 
    $storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storagekey[0].Value
    $cbc = Get-AzStorageContainer -Name $containerName -Context $storageContext
    $policy = New-AzStorageContainerStoredAccessPolicy -Container $containerName -Policy $policyName -Context $storageContext -ExpiryTime $(Get-Date).ToUniversalTime().AddYears(10) -Permission "rwld"
    $sas = New-AzStorageContainerSASToken -Policy $policyName -Context $storageContext -Container $containerName
    Write-Host 'Shared Access Signature= '$($sas.Substring(1))''

    # Outputs the Transact SQL to the clipboard and to the screen to create the credential using the Shared Access Signature  
    Write-Host 'Credential T-SQL'  
    $tSql = "CREATE CREDENTIAL [{0}] WITH IDENTITY='Shared Access Signature', SECRET='{1}'" -f $cbc.CloudBlobContainer.Uri.AbsoluteUri,$sas.Substring(1)
    $tSql | clip  
    Write-Host $tSql
    ```

    成功运行脚本后，将 CREATE CREDENTIAL 命令复制到查询工具，连接到一个 SQL Server 实例并运行该命令以创建使用共享访问签名的凭据。

2. 使用 SQL Server Management Studio (SSMS) 或使用 Azure Data Studio (ADS) 连接到 Azure SQL Edge 实例，并使用上一步骤中的命令创建凭据。 确保将 CREATE CREDENTIAL 命令替换为上一步骤中的实际输出。

    ```sql
    IF NOT EXISTS  
    (SELECT * FROM sys.credentials
    WHERE name = 'https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>')  
    CREATE CREDENTIAL [https://<mystorageaccountname>.blob.core.windows.net/<mystorageaccountcontainername>]
       WITH IDENTITY = 'SHARED ACCESS SIGNATURE',  
       SECRET = '<SAS_TOKEN>';
    ```

3. 以下命令会将 IronOreSilicaPrediction 备份到 Azure 存储容器。

    ```sql
    BACKUP DATABASE IronOreSilicaPrediction
    TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/IronOreSilicaPrediction.bak'
    With MAXTRANSFERSIZE = 4194304,BLOCKSIZE=65536;  
    GO
    ```

## <a name="restoring-a-database-in-azure-sql-edge"></a>在 Azure SQL Edge 中还原数据库

Azure SQL Edge 支持从本地磁盘、网络位置或从 Azure Blob 存储帐户还原。 若要大致了解 SQL Server 中的还原和恢复，请参阅[还原和恢复概述](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server?view=sql-server-ver15)。 若要大致了解 SQL Server 中的简单恢复模式，请参阅[完整数据库还原（简单恢复模式）](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model?view=sql-server-ver15)。

### <a name="restore-from-local-disk"></a>从本地磁盘还原

此示例使用在上一个示例中执行的 IronOreSilicaPrediction 备份作为具有不同名称的新数据库进行还原。

1. 如果容器中还没有数据库备份文件，可以使用下面的命令将文件复制到容器中。 下面的示例假定备份文件存在于本地主机上，并且被复制到名为 sql1 的 Azure SQL Edge 容器的 /var/opt/mssql/backup 文件夹中。

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. 使用 SQL Server Management Studio (SSMS) 或使用 Azure Data Studio (ADS) 连接到 Azure SQL Edge 实例，以执行还原命令。 在下面的示例中，将还原 IronOrePredictDB.bak，以创建新数据库 IronOreSilicaPrediction_2

    ```sql
    Restore FilelistOnly from disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'

    Restore Database IronOreSilicaPrediction_2
    From disk = N'/var/opt/mssql/backup/IronOrePredictDB.bak'
    WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.mdf',
    MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_2.ldf'
    ```

3. 运行还原命令后，如果还原操作成功，则会在输出窗口中看到类似于以下内容的消息。 

    ```txt
    Processed 51648 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction' on file 1.
    Processed 2 pages for database 'IronOreSilicaPrediction_2', file 'IronOreSilicaPrediction_log' on file 1.
    RESTORE DATABASE successfully processed 51650 pages in 6.543 seconds (61.670 MB/sec).

    Completion time: 2020-04-13T23:49:21.1600986-07:00
    ```

### <a name="restore-from-url"></a>从 URL 还原

Azure SQL Edge 还支持从 Azure 存储帐户还原数据库。 可以从块 blob 或页 blob 备份执行还原。 在下面提供的示例中，将还原块 blob 上的 IronOreSilicaPrediction_2020_04_16.bak 数据库备份文件，以创建数据库 IronOreSilicaPrediction_3。

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


