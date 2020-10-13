---
title: 备份和还原数据库 - Azure SQL Edge
description: 了解 Azure SQL Edge 中的备份和还原功能。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: f2cc8901ee3952f7d258d768e175412254ec5d1a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905954"
---
# <a name="back-up-and-restore-databases-in-azure-sql-edge"></a>在 Azure SQL Edge 中备份和还原数据库 

Azure SQL Edge 基于最新版本的 Microsoft SQL 数据库引擎而构建。 它提供的备份和还原数据库功能与 Linux 上的 SQL Server 以及在容器中运行的 SQL Server 提供的备份和还原数据库功能类似。 备份和还原组件为保护在 Azure SQL Edge 数据库中存储的数据提供了基本安全保障。 

为了尽量降低灾难性数据丢失的风险，你应当定期备份数据库，以便定期保存对数据的修改。 计划良好的备份和还原策略有助于保护数据库，使之免受各种故障导致的数据丢失的威胁。 请测试策略，方法是先还原一组备份，然后恢复数据库，以便准备好对灾难进行有效的响应。

若要详细了解备份的重要性，请参阅 [SQL Server 数据库的备份和还原](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases/)。

使用 Azure SQL Edge 可以备份到本地存储和 Azure blob，并可以从本地存储和 Azure blob 中进行还原。 有关详细信息，请参阅[使用 Azure Blob 存储执行 SQL Server 备份和还原](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service/)和[到 URL 的 SQL Server 备份](/sql/relational-databases/backup-restore/sql-server-backup-to-url)。

## <a name="back-up-a-database-in-azure-sql-edge"></a>在 Azure SQL Edge 中备份数据库

Azure SQL Edge 支持的备份类型与 SQL Server 相同。 有关完整列表，请参阅[备份概述](/sql/relational-databases/backup-restore/backup-overview-sql-server/)。

> [!IMPORTANT] 
> 默认情况下，在 Azure SQL Edge 中创建的数据库使用简单恢复模式。 因此，不能在这些数据库上执行日志备份。 如果你需要执行此操作，则需要管理员将数据库恢复模式更改为完整恢复模式。 有关 SQL Server 支持的恢复模式的完整列表，请参阅[恢复模式概述](/sql/relational-databases/backup-restore/recovery-models-sql-server#RMov)。

### <a name="back-up-to-local-disk"></a>备份到本地磁盘

在下面的示例中，你将使用 Transact-SQL 命令 `BACKUP DATABASE` 在容器中创建数据库备份。 出于此示例的目的，你将创建一个名为 backup 的新文件夹来存储备份文件。

1. 创建用于备份的文件夹。 在运行 Azure SQL Edge 容器的主机上运行此命令。 在下面的命令中，将 <AzureSQLEdge_Container_Name> 替换为你的部署中的 Azure SQL Edge 容器的名称。

    ```bash
    sudo docker exec -it <AzureSQLEdge_Container_Name> mkdir /var/opt/mssql/backup
    ```

2. 使用 SQL Server Management Studio (SSMS) 或使用 Azure Data Studio 连接到 Azure SQL Edge 实例。 运行 `BACKUP DATABASE` 命令来备份用户数据库。 在下面的示例中，你将对 IronOreSilicaPrediction 数据库进行备份。

    ```sql
    BACKUP DATABASE [IronOreSilicaPrediction] 
    TO DISK = N'/var/opt/mssql/backup/IronOrePredictDB.bak' 
    WITH NOFORMAT, NOINIT,  NAME = N'IronOreSilicaPrediction-Full Database Backup', 
    SKIP, NOREWIND, NOUNLOAD, COMPRESSION,  STATS = 10
    GO
    ```

3. 运行该命令后，如果数据库备份成功，将在 SSMS 或 Azure Data Studio 的“结果”部分中看到类似于以下内容的消息。

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

### <a name="back-up-to-url"></a>备份到 URL

Azure SQL Edge 支持备份到页 blob 和块 blob。 有关详细信息，请参阅[备份到块 blob 与备份到页 blob](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url#blockbloborpageblob)。 在下面的示例中，数据库 IronOreSilicaPrediction 将备份到块 blob。 

1. 若要配置备份到块 blob，请首先生成可用于在 Azure SQL Edge 上创建 SQL Server 凭据的共享访问签名 (SAS) 令牌。 该脚本创建与存储访问策略关联的共享访问签名。 有关详细信息，请参阅[共享访问签名，第 1 部分：了解 SAS 模型](https://azure.microsoft.com/documentation/articles/storage-dotnet-shared-access-signature-part-1/)，详细了解 SAS 以及 SAS 使用方面的最佳做法。 此脚本还编写在 SQL Server 上创建凭据时所需的 T-SQL 命令。 以下脚本假设你已有一个带有存储帐户的 Azure 订阅，并有一个用于备份的存储容器。

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

    成功运行脚本后，请将 `CREATE CREDENTIAL` 命令复制到查询工具。 然后，连接到 SQL Server 实例，并运行命令以使用 SAS 创建凭据。

2. 使用 SSMS 或 Azure Data Studio 连接到 Azure SQL Edge 实例，并使用上一步骤中的命令创建凭据。 确保将 `CREATE CREDENTIAL` 命令替换为上一步骤中的实际输出。

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

## <a name="restore-a-database-in-azure-sql-edge"></a>在 Azure SQL Edge 中还原数据库

在 Azure SQL Edge 中，可以从本地磁盘、网络位置或 Azure Blob 存储帐户进行还原。 有关 SQL Server 中的还原和恢复的详细信息，请参阅[还原和恢复概述](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-and-recovery-overview-sql-server)。 若要大致了解 SQL Server 中的简单恢复模式，请参阅[完整数据库还原（简单恢复模式）](https://docs.microsoft.com/sql/relational-databases/backup-restore/complete-database-restores-simple-recovery-model)。

> [!IMPORTANT] 
> 在 Azure SQL Edge 中创建的数据库无法在 Microsoft SQL Server 或 Azure SQL 的实例上还原。 此外，在 Microsoft SQL Server 或 Azure SQL 上创建的数据库可以在 Azure SQL Edge 上还原，前提是该数据库不包含 Azure SQL Edge 不支持的任何功能。 

### <a name="restore-from-a-local-disk"></a>从本地磁盘还原

此示例使用你在前面的示例中创建的 IronOreSilicaPrediction 备份。 现在，你将使用一个不同的名称将该备份还原为新数据库。

1. 如果容器中还没有该数据库备份文件，可以使用下面的命令将该文件复制到容器中。 下面的示例假定该备份文件存在于本地主机上，并且被复制到名为 sql1 的 Azure SQL Edge 容器的 /var/opt/mssql/backup 文件夹中。

    ```bash
    sudo docker cp IronOrePredictDB.bak sql1:/var/opt/mssql/backup
    ```

2. 使用 SSMS 或 Azure Data Studio 连接到 Azure SQL Edge 实例，以运行还原命令。 在下面的示例中，将还原 IronOrePredictDB.bak 以创建新数据库 IronOreSilicaPrediction_2。

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

Azure SQL Edge 还支持从 Azure 存储帐户还原数据库。 可以从块 blob 或页 blob 备份进行还原。 在下面的示例中，将还原块 blob 上的 IronOreSilicaPrediction_2020_04_16.bak 数据库备份文件，以创建数据库 IronOreSilicaPrediction_3。

```sql
RESTORE DATABASE IronOreSilicaPrediction_3
FROM URL = 'https://mystorageaccount.blob.core.windows.net/mysecondcontainer/IronOreSilicaPrediction_2020_04_16.bak'
WITH MOVE 'IronOreSilicaPrediction' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.mdf', 
MOVE 'IronOreSilicaPrediction_log' TO '/var/opt/mssql/data/IronOreSilicaPrediction_Primary_3.ldf',
STATS = 10;
```


