---
title: 使用更改跟踪和 Azure 数据工厂以增量方式复制数据 | Microsoft Docs
description: '在本教程中，请创建一个 Azure 数据工厂管道，将增量数据以增量方式从本地 SQL Server 数据库中的多个表复制到 Azure SQL 数据库。 '
services: data-factory
documentationcenter: ''
author: dearandyxu
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/12/2018
ms.author: yexu
ms.openlocfilehash: 4d2339ace047a5aacda74f6b1ccb9f1eb77aab0c
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37054035"
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

## <a name="overview"></a>概述
在数据集成解决方案中，一种广泛使用的方案是在完成初始数据加载后以增量方式加载数据。 在某些情况下，可以通过某种方式（例如，使用 LastModifyTime、CreationTime 等属性）将源数据存储中某个时段的更改数据轻松地进行切分。 在某些情况下，没有明确的方式可以将增量数据从上一次处理过的数据中区分出来。 可以使用 Azure SQL 数据库、SQL Server 等数据存储支持的更改跟踪技术来确定增量数据。  本教程介绍如何将 Azure 数据工厂与 SQL 更改跟踪技术配合使用，通过增量方式将增量数据从 Azure SQL 数据库加载到 Azure Blob 存储中。  有关 SQL 更改跟踪技术的更具体的信息，请参阅 [SQL Server 中的更改跟踪](/sql/relational-databases/track-changes/about-change-tracking-sql-server)。 

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

1. **首次加载：** 创建一个包含复制活动的管道，将完整数据从源数据存储（Azure SQL 数据库）复制到目标数据存储（Azure Blob 存储）。

    ![完整地加载数据](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **增量加载：** 创建一个包含以下活动的管道并定期运行。 
    1. 创建**两项查找活动**，从 Azure SQL 数据库获取旧的和新的 SYS_CHANGE_VERSION，然后将其传递至复制活动。
    2. 创建**一项复制活动**，将两个 SYS_CHANGE_VERSION 值之间的插入/更新/删除数据从 Azure SQL 数据库复制到 Azure Blob 存储。
    3. 创建**一项存储过程活动**，更新 SYS_CHANGE_VERSION 的值，以便进行下一次的管道运行。

    ![增量加载流程图](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件
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

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。 
   
   ![新建 -> DataFactory](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory-menu.png)
2. 在“新建数据工厂”页中，输入 **ADFTutorialDataFactory** 作为**名称**。 
      
     ![“新建数据工厂”页](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果收到错误，请更改数据工厂的名称（例如改为 yournameADFTutorialDataFactory），并重新尝试创建。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
       `Data factory name “ADFTutorialDataFactory” is not available`
3. 选择要在其中创建数据工厂的 Azure **订阅**。 
4. 对于**资源组**，请执行以下步骤之一：
     
      - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。 
      - 选择“新建”，并输入资源组的名称。   
         
        若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/resource-group-overview.md)。  
4. 选择“V2 (预览)”作为**版本**。
5. 选择数据工厂的**位置**。 下拉列表中仅显示支持的位置。 数据工厂使用的数据存储（Azure 存储、Azure SQL 数据库，等等）和计算资源（HDInsight 等）可以位于其他区域中。
6. 选择“固定到仪表板”。     
7. 单击“创建”。      
8. 在仪表板上，会看到状态为“正在部署数据工厂”的以下磁贴。 

    ![“正在部署数据工厂”磁贴](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. 创建完成后，可以看到图中所示的“数据工厂”页。
   
   ![数据工厂主页](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. 单击“创作和监视”磁贴，在单独的选项卡中启动 Azure 数据工厂用户界面 (UI)。
11. 在“入门”页的左侧面板中，切换到“编辑”选项卡，如下图所示： 

    ![“创建管道”按钮](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>创建链接服务
可在数据工厂中创建链接服务，将数据存储和计算服务链接到数据工厂。 在本部分中，创建 Azure 存储帐户和 Azure SQL 数据库的链接服务。 

### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务。
在此步骤中，请将 Azure 存储帐户链接到数据工厂。

1. 依次单击“连接”、“+ 新建”。

   ![“新建连接”按钮](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. 在“新建链接服务”窗口中，选择“Azure Blob 存储”，然后单击“继续”。 

   ![选择“Azure Blob 存储”](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. 在“新建链接服务”窗口中执行以下步骤： 

    1. 输入 **AzureStorageLinkedService** 作为**名称**。 
    2. 对于“存储帐户名称”，请选择自己的 Azure 存储帐户。 
    3. 单击“ **保存**”。 
    
   ![Azure 存储帐户设置](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>创建 Azure SQL 数据库链接服务
在此步骤中，将 Azure SQL 数据库链接到数据工厂。

1. 依次单击“连接”、“+ 新建”。
2. 在“新建链接服务”窗口中，选择“Azure SQL 数据库”，然后单击“继续”。 
3. 在“新建链接服务”窗口中执行以下步骤： 

    1. 对于“名称”字段，请输入 **AzureSqlDatabaseLinkedService**。 
    2. 对于“服务器名称”字段，请选择 Azure SQL Server。
    4. 对于“数据库名称”字段，请选择 Azure SQL 数据库。 
    5. 对于“用户名”字段，请输入用户的名称。 
    6. 对于“密码”字段，请输入用户的密码。 
    7. 单击“测试连接”以测试连接。
    8. 单击“保存”保存链接服务。 
    
       ![Azure SQL 数据库链接服务设置](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>创建数据集
在此步骤中，请创建表示数据源和数据目标的数据集， 并创建用于存储 SYS_CHANGE_VERSION 的位置。

### <a name="create-a-dataset-to-represent-source-data"></a>创建用于表示源数据的数据集 
在此步骤中，请创建一个代表源数据的数据集。 

1. 在树状视图中，依次单击“+”（加号）、“数据集”。 

   ![“新建数据集”菜单](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. 选择“Azure SQL 数据库”，然后单击“完成”。 

   ![源数据集类型 - Azure SQL 数据库](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. 此时会显示用于配置数据集的新选项卡。 树状视图中也会显示数据集。 在“属性”窗口中，将数据集的名称更改为 **SourceDataset**。

   ![源数据集名称](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. 切换到“连接”选项卡，然后执行以下步骤： 
    
    1. 为“链接服务”选择“AzureSqlDatabaseLinkedService”。 
    2. 为“表”选择“[dbo].[data_source_table]”。 

   ![源连接](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>创建一个数据集，用于表示复制到接收器数据存储的数据。 
在此步骤中，请创建一个数据集，代表从源数据存储复制的数据。 在执行先决条件中的步骤时，你已在 Azure Blob 存储中创建了 adftutorial 容器。 创建容器（如果不存在），或者将容器设置为现有容器的名称。 在本教程中，输出文件名是使用表达式 `@CONCAT('Incremental-', pipeline().RunId, '.txt')` 动态生成的。

1. 在树状视图中，依次单击“+”（加号）、“数据集”。 

   ![“新建数据集”菜单](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. 选择“Azure Blob 存储”，然后单击“完成”。 

   ![接收器数据集类型 - Azure Blob 存储](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. 此时会显示用于配置数据集的新选项卡。 树状视图中也会显示数据集。 在“属性”窗口中，将数据集的名称更改为 **SinkDataset**。

   ![接收器数据集 - 名称](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. 在“属性”窗口中切换到“连接”选项卡，然后执行以下步骤：

    1. 为“链接服务”选择“AzureStorageLinkedService”。
    2. 输入 **adftutorial/incchgtracking** 作为 **filePath** 的**文件夹**部分。
    3. 输入 **@CONCAT('Incremental-', pipeline().RunId, '.txt')** 作为 **filePath** 的**文件**部分。  

       ![接收器数据集 - 连接](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>创建用于表示更改跟踪数据的数据集 
在此步骤中，请创建用于存储更改跟踪版本的数据集。  在执行先决条件中的步骤时，你已创建了 table_store_ChangeTracking_version 表。

1. 在树状视图中，依次单击“+”（加号）、“数据集”。 
2. 选择“Azure SQL 数据库”，然后单击“完成”。 
3. 此时会显示用于配置数据集的新选项卡。 树状视图中也会显示数据集。 在“属性”窗口中，将数据集的名称更改为 **ChangeTrackingDataset**。
4. 切换到“连接”选项卡，然后执行以下步骤： 
    
    1. 为“链接服务”选择“AzureSqlDatabaseLinkedService”。 
    2. 为“表”选择“[dbo].[table_store_ChangeTracking_version]”。 

## <a name="create-a-pipeline-for-the-full-copy"></a>创建用于完整复制的管道
在这一步，请创建一个包含复制活动的管道，将完整数据从源数据存储（Azure SQL 数据库）复制到目标数据存储（Azure Blob 存储）。

1. 依次单击左窗格中的“+”（加号）、“管道”。 

    ![“新建管道”菜单](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. 此时会显示用于配置管道的新选项卡。 树状视图中也会显示管道。 在“属性”窗口中，将管道的名称更改为 **FullCopyPipeline**。

    ![“新建管道”菜单](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. 在“活动”工具箱中展开“数据流”，将“复制”活动拖放到管道设计器图面，然后设置名称 **FullCopyActivity**。 

    ![完整复制活动 - 名称](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. 切换到“源”选项卡，为“源数据集”字段选择“SourceDataset”。 

    ![复制活动 - 源](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. 切换到“接收器”选项卡，为“接收器数据集”字段选择“SinkDataset”。 

    ![复制活动 - 接收器](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. 若要验证管道定义，请单击工具栏中的“验证”。 确认没有任何验证错误。 单击 **>>** 关闭“管道验证报告”。 

    ![验证管道](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. 若要发布实体（链接服务、数据集和管道），请单击“发布”。 等待发布成功。 

    ![发布按钮](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. 等待“已成功发布”消息出现。 

    ![发布成功](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. 此外，可以通过单击左侧的“显示通知”按钮来查看通知。 若要关闭通知窗口，请单击“X”。

    ![显示通知](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>运行完整的复制管道
在工具栏中单击管道对应的“触发器”，然后单击“立即触发”。 

![“立即触发”菜单](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>监视完整的复制管道

1. 单击左侧的“监视”选项卡。 可以在列表中查看管道运行及其状态。 若要刷新列表，请单击“刷新”。 使用“操作”列中的链接可以查看与管道运行关联的活动运行，以及重新运行管道。 

    ![管道运行](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. 若要查看与管道运行关联的活动运行，请单击“操作”列中的“查看活动运行”链接。 该管道中只有一个活动，因此列表中只显示了一个条目。 若要切换回到管道运行视图，请单击顶部的“管道”链接。 

    ![活动运行](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>查看结果
可以在 `adftutorial` 容器的 `incchgtracking` 文件夹中看到名为 `incremental-<GUID>.txt` 的文件。 

![来自完整复制的输出文件](media\tutorial-incremental-copy-change-tracking-feature-portal\full-copy-output-file.png)

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

1. 在数据工厂 UI 中，切换到“编辑”选项卡。依次单击左窗格中的“+”（加号）、“管道”。 

    ![“新建管道”菜单](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. 此时会显示用于配置管道的新选项卡。 树状视图中也会显示管道。 在“属性”窗口中，将管道的名称更改为 **IncrementalCopyPipeline**。

    ![管道名称](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. 在“活动”工具箱中展开“常规”，将**查找**活动拖放到管道设计器图面。 将活动的名称设置为 **LookupLastChangeTrackingVersionActivity**。 此活动获取在上次复制操作中使用的、存储在 **table_store_ChangeTracking_version** 表中的更改跟踪版本。

    ![查找活动 - 名称](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. 在“属性”窗口中切换到“设置”，为“源数据集”字段选择“ChangeTrackingDataset”。 

    ![查找活动 - 设置](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. 将“查找”活动从“活动”工具箱拖放到管道设计器图面。 将活动的名称设置为 **LookupCurrentChangeTrackingVersionActivity**。 此活动获取当前的更改跟踪版本。

    ![查找活动 - 名称](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. 在“属性”窗口中切换到“设置”选项卡，然后执行以下步骤：

    1. 为“源数据集”字段选择“SourceDataset”。
    2. 为“使用查询”选择“查询”。 
    3. 为“查询”输入以下 SQL 查询。 

        ```sql
        SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
        ```

    ![查找活动 - 设置](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. 在“活动”工具箱中展开“数据流”，将“复制”活动拖放到管道设计器图面。 将活动的名称设置为 **IncrementalCopyActivity**。 此活动将上次跟踪版本与当前更改跟踪版本之间的数据复制到目标数据存储。 

    ![复制活动 - 名称](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. 在“属性”窗口中切换到“源”选项卡，然后执行以下步骤：

    1. 为“源数据集”选择“SourceDataset”。 
    2. 为“使用查询”选择“查询”。 
    3. 为“查询”输入以下 SQL 查询。 

        ```sql
        select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
        ```
    
    ![复制活动 - 源设置](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. 切换到“接收器”选项卡，为“接收器数据集”字段选择“SinkDataset”。 

    ![复制活动 - 接收器设置](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. 逐个地**将两个“查找”活动连接到“复制”活动**。 将附加到“查找”活动的**绿色**按钮拖放到“复制”活动。 

    ![连接“查找”和“复制”活动](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. 将“存储过程”活动从“活动”工具箱拖放到管道设计器图面。 将活动的名称设置为 **StoredProceduretoUpdateChangeTrackingActivity**。 此活动更新 **table_store_ChangeTracking_version** 表中的更改跟踪版本。

    ![存储过程活动 - 名称](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. 切换到“SQL 帐户”选项卡，为“链接服务”选择“AzureSqlDatabaseLinkedService”。 

    ![存储过程活动 - SQL 帐户](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. 切换到“存储过程”选项卡，然后执行以下步骤： 

    1. 至于“存储过程名称”，请选择 **Update_ChangeTracking_Version**。  
    2. 选择“导入参数”。 
    3. 在“存储过程参数”部分，指定以下参数值： 

        | 名称 | Type | 值 | 
        | ---- | ---- | ----- | 
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} | 
        | TableName | String | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} | 
    
        ![存储过程活动 - 参数](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **将“复制”活动连接到存储过程活动**。 将附加到“复制”活动的**绿色**按钮拖放到“存储过程”活动。 

    ![连接“复制”和“存储过程”活动](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. 在工具栏中单击“验证”。 确认没有任何验证错误。 单击 **>>** 关闭“管道验证报告”窗口。 

    ![“验证”按钮](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16.  单击“全部发布”按钮，将实体（链接服务、数据集和管道）发布到数据工厂服务。 等到“发布成功”消息出现。 

        ![发布按钮](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>运行增量复制管道
1. 在工具栏中单击管道对应的“触发器”，然后单击“立即触发”。 

    ![“立即触发”菜单](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. 在“管道运行”窗口中选择“完成”。

### <a name="monitor-the-incremental-copy-pipeline"></a>监视增量复制管道
1. 单击左侧的“监视”选项卡。 可以在列表中查看管道运行及其状态。 若要刷新列表，请单击“刷新”。 使用“操作”列中的链接可以查看与管道运行关联的活动运行，以及重新运行管道。 

    ![管道运行](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. 若要查看与管道运行关联的活动运行，请单击“操作”列中的“查看活动运行”链接。 该管道中只有一个活动，因此列表中只显示了一个条目。 若要切换回到管道运行视图，请单击顶部的“管道”链接。 

    ![活动运行](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>查看结果
可以在 `adftutorial` 容器的 `incchgtracking` 文件夹中看到第二个文件。 

![来自增量复制的输出文件](media\tutorial-incremental-copy-change-tracking-feature-portal\incremental-copy-output-file.png)

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
>[在云中使用 Spark 群集转换数据](tutorial-transform-data-spark-portal.md)



