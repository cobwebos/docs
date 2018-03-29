---
title: 使用更改跟踪和 Azure 数据工厂以增量方式复制数据 | Microsoft Docs
description: '在本教程中，请创建一个 Azure 数据工厂管道，将增量数据以增量方式从本地 SQL Server 数据库中的多个表复制到 Azure SQL 数据库。 '
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: d8299778ce5b713f4275a28c7f174a300197a6a2
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information"></a>根据更改跟踪信息，以增量方式将 Azure SQL 数据库中的数据加载到 Azure Blob 存储 
在本教程中，请创建一个带管道的 Azure 数据工厂，以便根据源 Azure SQL 数据库中的**更改跟踪**信息将增量数据加载到 Azure Blob 存储。  

在本教程中执行以下步骤：

> [!div class="checklist"]
> * 准备源数据存储
> * 创建数据工厂。
> * 创建链接服务。 
> * 创建源、接收器和更改跟踪数据集。
> * 创建、运行和监视完整复制管道
> * 在源表中添加或更新数据
> * 创建、运行和监视增量复制管道

> [!NOTE]
> 本文适用于目前处于预览状态的数据工厂版本 2。 如果使用数据工厂服务版本 1（即正式版 (GA)），请参阅[数据工厂版本 1 文档](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)。

## <a name="overview"></a>概述
在数据集成解决方案中，一种广泛使用的方案是在完成初始数据加载后以增量方式加载数据。 在某些情况下，可以通过某种方式（例如，使用 LastModifyTime、CreationTime 等属性）将源数据存储中某个时段的更改数据轻松地进行切分。 在某些情况下，没有明确的方式可以将增量数据从上一次处理过的数据中区分出来。 可以使用 Azure SQL 数据库、SQL Server 等数据存储支持的更改跟踪技术来确定增量数据。  本教程介绍如何将 Azure 数据工厂第 2 版与 SQL 更改跟踪技术配合使用，通过增量方式将增量数据从 Azure SQL 数据库加载到 Azure Blob 存储中。  有关 SQL 更改跟踪技术的更具体的信息，请参阅 [SQL Server 中的更改跟踪](/sql/relational-databases/track-changes/about-change-tracking-sql-server)。 

## <a name="end-to-end-workflow"></a>端到端工作流
下面是典型的端到端工作流步骤，用于通过更改跟踪技术以增量方式加载数据。

> [!NOTE]
> Azure SQL 数据库和 SQL Server 都支持更改跟踪技术。 本教程使用 Azure SQL 数据库作为源数据存储。 也可以使用本地 SQL Server。 

1. **首次加载历史数据**（运行一次）：
    1. 在源 Azure SQL 数据库中启用更改跟踪技术。
    2. 在 Azure SQL 数据库中获取 SYS_CHANGE_VERSION 的初始值，作为捕获更改数据的基线。
    3. 将完整数据从 Azure SQL 数据库加载到 Azure Blob 存储中。 
2. **以增量方式按计划加载增量数据**（在首次加载数据后定期运行）：
    1. 获取旧的和新的 SYS_CHANGE_VERSION 值。
    3. 将 **sys.change_tracking_tables** 中已更改行（两个 SYS_CHANGE_VERSION 值之间）的主键与**源表**中的数据联接，以便加载增量数据，然后将增量数据移到目标位置。
    4. 更新 SYS_CHANGE_VERSION，以便下次进行增量加载。

## <a name="high-level-solution"></a>高级解决方案
在本教程中，请创建两个管道来执行下述两项操作：  

1. **首次加载：**创建一个包含复制活动的管道，将完整数据从源数据存储（Azure SQL 数据库）复制到目标数据存储（Azure Blob 存储）。

    ![完整地加载数据](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **增量加载：**创建一个包含以下活动的管道并定期运行。 
    1. 创建**两项查找活动**，从 Azure SQL 数据库获取旧的和新的 SYS_CHANGE_VERSION，然后将其传递至复制活动。
    2. 创建**一项复制活动**，将两个 SYS_CHANGE_VERSION 值之间的插入/更新/删除数据从 Azure SQL 数据库复制到 Azure Blob 存储。
    3. 创建**一项存储过程活动**，更新 SYS_CHANGE_VERSION 的值，以便进行下一次的管道运行。

    ![增量加载流程图](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件
* Azure PowerShell。 按[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明安装最新的 Azure PowerShell 模块。
* **Azure SQL 数据库**。 将数据库用作**源**数据存储。 如果没有 Azure SQL 数据库，请参阅[创建 Azure SQL 数据库](../sql-database/sql-database-get-started-portal.md)一文获取创建步骤。
* **Azure 存储帐户**。 将 Blob 存储用作**接收器**数据存储。 如果没有 Azure 存储帐户，请参阅[创建存储帐户](../storage/common/storage-create-storage-account.md#create-a-storage-account)一文获取创建步骤。 创建名为 **adftutorial** 的容器。 

### <a name="create-a-data-source-table-in-your-azure-sql-database"></a>在 Azure SQL 数据库中创建数据源表
1. 启动 **SQL Server Management Studio**，连接到 Azure SQL Server。 
2. 在“服务器资源管理器”中，右键单击你的**数据库**，然后选择“新建查询”。
3. 针对 Azure SQL 数据库运行以下 SQL 命令，创建名为 `data_source_table` 的表作为数据源存储。  
    
    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. 通过运行以下 SQL 查询，在数据库和源表 (data_source_table) 上启用**更改跟踪**机制： 

    > [!NOTE]
    > - 将 &lt;your database name&gt; 替换为你的 Azure SQL 数据库的名称，其中包含 data_source_table。 
    > - 在当前的示例中，更改的数据保留两天。 如果每隔三天或三天以上加载更改的数据，则不会包括某些更改的数据。  需将 CHANGE_RETENTION 的值更改为更大的值。 或者，确保在两天内加载一次更改的数据。 有关详细信息，请参阅[对数据库启用更改跟踪](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database)
 
    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  
  
    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. 通过运行以下查询，创建一个新表并存储带默认值的 ChangeTracking_version： 

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```
    
    > [!NOTE]
    > 如果对 SQL 数据库启用更改跟踪后数据并未更改，则更改跟踪版本的值为 0。
6. 运行以下查询，在 Azure SQL 数据库中创建存储过程。 管道会调用此存储过程，以便更新上一步创建的表中的更改跟踪版本。 

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS
    
    BEGIN
    
        UPDATE table_store_ChangeTracking_version
        SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName
    
    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
按[如何安装和配置 Azure PowerShell](/powershell/azure/install-azurerm-ps) 中的说明安装最新的 Azure PowerShell 模块。

## <a name="create-a-data-factory"></a>创建数据工厂
1. 为资源组名称定义一个变量，稍后会在 PowerShell 命令中使用该变量。 将以下命令文本复制到 PowerShell，在双引号中指定 [Azure 资源组](../azure-resource-manager/resource-group-overview.md)的名称，然后运行命令。 例如：`"adfrg"`。 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    如果该资源组已存在，请勿覆盖它。 为 `$resourceGroupName` 变量分配另一个值，然后再次运行命令
2. 定义一个用于数据工厂位置的变量： 

    ```powershell
    $location = "East US"
    ```
3. 若要创建 Azure 资源组，请运行以下命令： 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 
    如果该资源组已存在，请勿覆盖它。 为 `$resourceGroupName` 变量分配另一个值，然后再次运行命令。 
3. 定义一个用于数据工厂名称的变量。 

    > [!IMPORTANT]
    >  更新数据工厂名称，使之全局唯一。  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. 若要创建数据工厂，请运行以下 **Set-AzureRmDataFactoryV2** cmdlet： 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

请注意以下几点：

* Azure 数据工厂的名称必须全局唯一。 如果收到以下错误，请更改名称并重试。

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* 若要创建数据工厂实例，用于登录到 Azure 的用户帐户必须属于**参与者**或**所有者**角色，或者是 Azure 订阅的**管理员**。
* 目前，数据工厂版本 2 仅允许在“美国东部”、“美国东部 2”和“西欧”区域创建数据工厂。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。


## <a name="create-linked-services"></a>创建链接服务
可在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在本部分中，创建 Azure 存储帐户和 Azure SQL 数据库的链接服务。 

### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务。
在此步骤中，请将 Azure 存储帐户链接到数据工厂。

1. 在 **C:\ADFTutorials\IncCopyChangeTrackingTutorial** 文件夹（如果此文件夹不存在，请创建）中，创建包含以下内容的名为 **AzureStorageLinkedService.json** 的 JSON 文件。 将 `<accountName>` 和 `<accountKey>` 分别替换为 Azure 存储帐户的名称和密钥，然后保存文件。

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. 在 **Azure PowerShell** 中切换到 **C:\ADFTutorials\IncCopyChgTrackingTutorial** 文件夹。
3. 运行 **Set-AzureRmDataFactoryV2LinkedService** cmdlet 创建链接服务：**AzureStorageLinkedService**。 在以下示例中，传递 **ResourceGroupName** 和 **DataFactoryName** 参数的值。 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    下面是示例输出：

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>创建 Azure SQL 数据库链接服务
在此步骤中，将 Azure SQL 数据库链接到数据工厂。

1. 在 **C:\ADFTutorials\IncCopyChangeTrackingTutorial** 文件夹中创建包含以下内容的名为 **AzureSQLDatabaseLinkedService.json** 的 JSON 文件：将 **&lt;server&gt;、&lt;database name&gt;、&lt;user id&gt; 和 &lt;password&gt;** 替换为你的 Azure SQL Server 名称、数据库名称、用户 ID 和密码，然后保存文件。 

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": {
                    "value": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;",
                    "type": "SecureString"
                }
            }
        }
    }
    ```
2. 在 **Azure PowerShell** 中运行 **Set-AzureRmDataFactoryV2LinkedService** cmdlet，以便创建链接服务 **AzureSQLDatabaseLinkedService**。 

    ```powershell
    Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    下面是示例输出：

    ```json
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>创建数据集
在此步骤中，请创建表示数据源和数据目标的数据集， 并创建用于存储 SYS_CHANGE_VERSION 的位置。

### <a name="create-a-source-dataset"></a>创建源数据集
在此步骤中，请创建一个代表源数据的数据集。 

1. 在同一文件夹中，创建包含以下内容的名为 SourceDataset.json 的 JSON 文件： 

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

2.  运行 Set-AzureRmDataFactoryV2Dataset cmdlet 创建数据集：SourceDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    下面是该 cmdlet 的示例输出：
    
    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>创建接收器数据集
在此步骤中，请创建一个数据集，代表从源数据存储复制的数据。 

1. 在同一文件夹中，创建包含以下内容的名为 SinkDataset.json 的 JSON 文件： 

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    在 Azure Blob 存储中创建 adftutorial 容器，这是先决条件的部分要求。 创建容器（如果不存在），或者将容器设置为现有容器的名称。 在本教程中，输出文件名是使用以下表达式动态生成的：@CONCAT('Incremental-', pipeline().RunId, '.txt')。
2.  运行 Set-AzureRmDataFactoryV2Dataset cmdlet 创建数据集：SinkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    下面是该 cmdlet 的示例输出：
    
    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>创建更改跟踪数据集
在此步骤中，请创建用于存储更改跟踪版本的数据集。  

1. 在同一文件夹中，创建包含以下内容的名为 ChangeTrackingDataset.json 的 JSON 文件： 

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    创建 table_store_ChangeTracking_version 表，这是先决条件的部分要求。
2.  运行 Set-AzureRmDataFactoryV2Dataset cmdlet 创建数据集：WatermarkDataset
    
    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    下面是该 cmdlet 的示例输出：
    
    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>创建用于完整复制的管道
在这一步，请创建一个包含复制活动的管道，将完整数据从源数据存储（Azure SQL 数据库）复制到目标数据存储（Azure Blob 存储）。

1. 在同一文件夹中，创建包含以下内容的 JSON 文件 FullCopyPipeline.json： 

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
    
                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. 运行 Set-AzureRmDataFactoryV2Pipeline cmdlet，以便创建管道 FullCopyPipeline。
    
   ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ``` 

   下面是示例输出： 

   ```json
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```
 
### <a name="run-the-full-copy-pipeline"></a>运行完整的复制管道
使用 **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet 运行管道 **FullCopyPipeline**。 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
``` 

### <a name="monitor-the-full-copy-pipeline"></a>监视完整的复制管道

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 单击“所有服务”，使用关键字 `data factories` 进行搜索，然后选择“数据工厂”。 

    ![数据工厂菜单](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-data-factories-menu-1.png)
3. 在数据工厂列表中搜索**你的数据工厂**，然后选择它来启动“数据工厂”页。 

    ![搜索你的数据工厂](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-search-data-factory-2.png)
4. 在“数据工厂”页中，单击“监视和管理”磁贴。 

    ![“监视和管理”磁贴](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-monitor-manage-tile-3.png)    
5. **数据集成应用程序**在单独的选项卡中启动。可以看到所有**管道运行**及其状态。 请注意，在以下示例中，管道运行的状态为“成功”。 单击“参数”列中的链接即可查看传递至管道的参数。 如果有错误，在“错误”列可以看到链接。 单击“操作”列中的链接。 

    ![管道运行](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-pipeline-runs-4.png)    
6. 单击“操作”列中的链接时，可以看到以下页面，其中显示管道的所有**活动运行**。 

    ![活动运行](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-activity-runs-5.png)
7. 若要切换回“管道运行”视图，请单击“管道”，如图所示。 


### <a name="review-the-results"></a>查看结果
可以在 `adftutorial` 容器的 `incchgtracking` 文件夹中看到名为 `incremental-<GUID>.txt` 的文件。 

![来自完整复制的输出文件](media\tutorial-incremental-copy-change-tracking-feature-powershell\full-copy-output-file.png)

该文件应该包含 Azure SQL 数据库中的数据：

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>向源表中添加更多数据

对 Azure SQL 数据库运行以下查询，以便添加行和更新行。 

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

``` 

## <a name="create-a-pipeline-for-the-delta-copy"></a>创建用于增量复制的管道
在此步骤中，请创建一个包含以下活动的管道并定期运行。 **查找活动**从 Azure SQL 数据库获取旧的和新的 SYS_CHANGE_VERSION，然后将其传递至复制活动。 **复制活动**将两个 SYS_CHANGE_VERSION 值之间的插入/更新/删除数据从 Azure SQL 数据库复制到 Azure Blob 存储。 **存储过程活动**更新 SYS_CHANGE_VERSION 的值，以便进行下一次的管道运行。

1. 在同一文件夹中，创建包含以下内容的 JSON 文件 IncrementalCopyPipeline.json： 

    ```json
    {
            "name": "IncrementalCopyPipeline",
            "properties": {
                "activities": [
                {
                        "name": "LookupLastChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                            },
        
                            "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },
                    {
                        "name": "LookupCurrentChangeTrackingVersionActivity",
                        "type": "Lookup",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
                        },
    
                            "dataset": {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                            }
                        }
                    },
    
                    {
                        "name": "IncrementalCopyActivity",
                        "type": "Copy",
                        "typeProperties": {
                            "source": {
                                "type": "SqlSource",
                                "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                            },
                            "sink": {
                                "type": "BlobSink"
                            }
                        },
                        "dependsOn": [
                            {
                                "activity": "LookupLastChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                            },
                            {
                                "activity": "LookupCurrentChangeTrackingVersionActivity",
                                "dependencyConditions": [
                                    "Succeeded"
                                ]
                        }
                        ],
        
                        "inputs": [
                            {
                            "referenceName": "SourceDataset",
                                "type": "DatasetReference"
                        }
                        ],
                        "outputs": [
                            {
                                "referenceName": "SinkDataset",
                                "type": "DatasetReference"
                            }
                        ]
                    },
        
                {
                        "name": "StoredProceduretoUpdateChangeTrackingActivity",
                        "type": "SqlServerStoredProcedure",
                        "typeProperties": {
    
                            "storedProcedureName": "Update_ChangeTracking_Version",
                            "storedProcedureParameters": {
                            "CurrentTrackingVersion": {"value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}", "type": "INT64" },
                                "TableName":  { "value":"@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}", "type":"String"}
                            }
                    },
        
                        "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                            "type": "LinkedServiceReference"
                        },
        
                        "dependsOn": [
                        {
                                "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                    "Succeeded"
                                ]
                            }
                        ]
                    }
                ]
        
            }
    }
    
    ```
2. 运行 Set-AzureRmDataFactoryV2Pipeline cmdlet，以便创建管道 FullCopyPipeline。
    
   ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ``` 

   下面是示例输出： 

   ```json
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>运行增量复制管道
使用 **Invoke-AzureRmDataFactoryV2Pipeline** cmdlet 运行管道：**IncrementalCopyPipeline**。 

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
``` 


### <a name="monitor-the-incremental-copy-pipeline"></a>监视增量复制管道
1. 在**数据集成应用程序**中，刷新“管道运行”视图。 确认在列表中看到 IncrementalCopyPipeline。 单击“操作”列中的链接。  

    ![管道运行](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-pipeline-runs-6.png)    
2. 单击“操作”列中的链接时，可以看到以下页面，其中显示管道的所有**活动运行**。 

    ![活动运行](media\tutorial-incremental-copy-change-tracking-feature-powershell\monitor-activity-runs-7.png)
3. 若要切换回“管道运行”视图，请单击“管道”，如图所示。 

### <a name="review-the-results"></a>查看结果
可以在 `adftutorial` 容器的 `incchgtracking` 文件夹中看到第二个文件。 

![来自增量复制的输出文件](media\tutorial-incremental-copy-change-tracking-feature-powershell\incremental-copy-output-file.png)

该文件应该只包含 Azure SQL 数据库中的增量数据。 带 `U` 的记录是数据库中的更新行，带 `I` 的记录是添加的行。 

```
1,update,10,2,U
6,new,50,1,I
```
前三个列是 data_source_table 中的更改数据。 最后两个列是更改跟踪系统表中的元数据。 第四列是每个更改行的 SYS_CHANGE_VERSION。 第五列是操作：U = update（更新），I = insert（插入）。  如需详细了解更改跟踪信息，请参阅 [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql)。 

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10     2                     U
6        new     50     1                     I
```

    
## <a name="next-steps"></a>后续步骤
请转到下一篇教程，了解如何在 Azure 上使用 Spark 群集转换数据：

> [!div class="nextstepaction"]
>[在云中使用 Spark 群集转换数据](tutorial-transform-data-spark-powershell.md)



