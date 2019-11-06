---
title: 使用 Azure 数据工厂从 Azure SQL Database Edge 同步数据 | Microsoft Docs
description: 了解如何在 Azure SQL Database Edge 与 Azure Blob 存储之间同步数据
keywords: sql database edge, 从 sql database edge 同步数据, sql database edge 数据工厂
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 0e75da9516303bb4250b6847a4b381d07b3d7dad
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73509201"
---
# <a name="tutorial-sync-data-from-sql-database-edge-to-azure-blob-storage-using-azure-data-factory"></a>教程：使用 Azure 数据工厂将数据从 SQL Database Edge 同步到 Azure Blob 存储

在本教程中，你将使用 Azure 数据工厂以增量方式将数据从 Azure SQL Database Edge 实例中的表同步到 Azure Blob 存储。

## <a name="before-you-begin"></a>开始之前

如果你尚未在 Azure SQL Database Edge 部署中创建数据库或表，请使用以下方法之一来创建一个：

* 使用 [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) 或 [Azure Data Studio](/sql/azure-data-studio/download/) 连接到 SQL Database Edge，并执行一个 SQL 脚本来创建数据库和表。
* 直接连接到 SQL Database Edge 模块后，使用 [SQLCMD](/sql/tools/sqlcmd-utility/) 创建 SQL 数据库和表。 有关详细信息，请参阅[使用 sqlcmd 连接到数据库引擎](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/)。
* 使用 SQLPackage.exe 将 dacpac 文件部署到 SQL Database Edge 容器。 可以通过指定 SQLPackage 文件 URI 作为模块所需属性配置的一部分，或直接使用 SqlPackage.exe 客户端工具将 dacpac 部署到 SQL Database Edge，来自动完成此操作。

    若要下载 sqlpackage，请参阅[下载和安装 sqlpackage](/sql/tools/sqlpackage-download/)。 我们已提供 SqlPackage.exe 的以下示例命令；有关更多信息，请查看 sqlpackage 文档。

    **创建 dacpac**：

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name> 
    ```

    **应用 dacpac**：

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>创建 SQL 表和过程用于存储和更新水印级别

水印表用于存储上次将数据同步到 Azure 存储时的时间戳。 Transact-SQL (T-SQL) 存储过程用于在每次同步后更新水印表。 

在 SQL Database Edge 实例上执行以下命令：

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp WHERE [TableName] = @TableName
    END
    Go
```

## <a name="create-a-data-factory-workflow"></a>创建数据工厂工作流

在本部分，你将创建一个 Azure 数据工厂管道，用于将数据从 Azure SQL Database Edge 中的表同步到 Azure Blob 存储。

### <a name="create-data-factory-using-the-data-factory-ui"></a>使用数据工厂 UI 创建数据工厂

按照[此教程](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)中的说明创建数据工厂。

### <a name="create-a-data-factory-pipeline"></a>创建数据工厂管道

1. 在数据工厂 UI 的“开始”页上，选择“创建管道”磁贴。  

    ![数据工厂 - 创建管道](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. 在管道“属性”窗口的“常规”页上，输入名称 **PeriodicSync**。  

3. 添加“查找”活动用于获取旧水印值。  在“活动”工具箱中展开“常规”，将“查找”活动拖放到管道设计器图面。    将活动的名称更改为 *OldWatermark*。

    ![旧水印查找](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. 切换到“设置”选项卡，选择“源数据集”对应的“+ 新建”。    此步骤将创建一个代表水印表中的数据的数据集。 此表包含在前一复制操作中使用过的旧水印。

5. 在“新建数据集”窗口中，依次选择“Azure SQL Server”、“继续”。     

6. 在数据集的“设置属性”属性窗口中，输入 *WatermarkDataset* 作为名称。 

7. 对于“链接服务”  ，请选择“新建”  ，然后执行以下步骤：

    1. 输入 *SQLDBEdgeLinkedService* 作为**名称**。

    2. 在“服务器名称”中输入 SQL Database Edge 服务器详细信息。 

    3. 通过下拉列表输入你的**数据库名称**。

    4. 输入**用户名**和**密码**。

    5. 若要测试与 SQL Database Edge 实例的连接，请选择“测试连接”。 

    6. 选择“创建”  。

    ![创建链接服务](media/tutorial-sync-data-factory/create-linked-service.png)

    7. 选择“确定” 

8. 在“设置”选项卡中选择“编辑”。  

9. 在“连接”  选项卡中，对于“表”，选择“[dbo].[watermarktable]”。   若要预览表中的数据，请选择“预览数据”。 

10. 通过选择顶部的管道选项卡，或者选择左侧树状视图中管道的名称，切换到管道编辑器。 在“查找”活动的属性窗口中，确认为“源数据集”字段选择了“WatermarkDataset”。   

11. 在“活动”工具箱中展开“常规”，将另一个“查找”活动拖放到管道设计器图面，然后在属性窗口的“常规”选项卡中将名称设置为 **NewWatermark**。     此“查找”活动从特定表获取新的水印值，该表包含的源数据可以复制到目标。

12. 在第二个“查找”活动的属性窗口中切换到“设置”选项卡，然后选择“新建”以创建一个数据集，该数据集指向包含新水印值的源表。   

13. 在“新建数据集”窗口中选择 SQL Database Edge 实例，然后选择“继续”。  

    1. 在属性窗口的“常规”选项卡中，对于“名称”输入“SourceDataset”   。  为“链接服务”选择“SQLDBEdgeLinkedService”。 

    2. 为“表”选择要同步的表。 还可以按本教程稍后所述，为此数据集指定查询。 此查询优先于在此步骤中指定的表。

    3. 选择“确定”  。

14. 通过选择顶部的管道选项卡，或者选择左侧树状视图中管道的名称，切换到管道编辑器。 在**查找**活动的属性窗口中，确认对于“源数据集”字段，是否已选择 **SourceDataset**。 

15. 为“使用查询”字段选择“查询”，并在查询中更新表名称后输入以下查询：仅选择表中时间戳的最大值。   请确保还选中了“仅第一行”  。

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![选择查询](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. 在“活动”工具箱中展开“移动和转换”，从“活动”工具箱拖放“复制”活动，并将名称设置为 **IncrementalCopy**。   

17. 通过将附加到“查找”活动的**绿色按钮**拖放到“复制”活动，将两个“查找”活动都连接到“复制”活动。     看到“复制”活动的边框颜色变为蓝色时，松开鼠标按键。

18. 选择“复制”活动，确认“属性”窗口中已显示该活动的属性。  

19. 在“属性”窗口中切换到“源”选项卡，然后执行以下步骤：  

    1. 对于“源数据集”字段，请选择“SourceDataset”。  

    2. 对于“使用查询”字段，请选择“查询”。  

    3. 为“查询”字段输入 SQL 查询。  下面为示例查询

    4. SQL 查询：

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. 切换到“接收器”选项卡，选择“接收器数据集”字段对应的“+ 新建”。   

21. 在本教程中，接收器数据存储的类型为“Azure Blob 存储”。  选择“Azure Blob 存储”，然后在“新建数据集”窗口中选择“继续”。   

22. 在“选择格式”窗口中选择数据的格式类型，然后选择“继续”。  

23. 在“设置属性”窗口中，输入 **SinkDataset** 作为名称。  选择“链接服务”对应的“+新建”。  此步骤创建一个连接（链接服务），用于连接到 **Azure Blob 存储**。

24. 在“新建链接服务(Azure Blob 存储)”窗口中执行以下步骤： 

    1. 输入 *AzureStorageLinkedService* 作为名称。

    2. 为“存储帐户名称”选择你的 Azure 存储帐户和 Azure 订阅。 

    3. 测试**连接**并选择“完成”。 

25. 在“设置属性”窗口中，对于“链接服务”，确认选择了“AzureStorageLinkedService”。    然后依次选择“创建”、“确定”。  

26. 在“接收器”选项卡中选择“编辑”。  

27. 转到“SinkDataset”的“连接”选项卡，然后执行以下步骤：  

    1. 对于“文件路径”字段，请输入 *asdedatasync/incrementalcopy*，其中 **adftutorial** 是 Blob 容器名称，**incrementalcopy** 是文件夹名称。  创建容器（如果不存在），或者将容器设置为现有容器的名称。 Azure 数据工厂自动创建输出文件夹 *incrementalcopy*（如果不存在）。 对于“文件路径”，也可使用“浏览”按钮导航到 Blob 容器中的某个文件夹。  

    2. 对于“文件路径”字段的“文件”部分，请选择“添加动态内容 [Alt+P]”，然后在打开的窗口中输入 *@CONCAT('Incremental-', pipeline().RunId, '.txt')* 。    然后选择“完成”。  文件名是使用表达式动态生成的。 每次管道运行都有唯一的 ID。 “复制”活动使用运行 ID 生成文件名。

28. 通过选择顶部的**管道**选项卡，或者选择左侧树状视图中管道的名称，切换到管道编辑器。

29. 在“活动”工具箱中，展开“常规”  ，然后将**存储过程**活动从“活动”工具箱拖放到管道设计器图面。   将**复制**活动的绿色（成功）输出**连接**到**存储过程**活动。

30. 在管道设计器中选择“存储过程活动”，将其名称更改为 *SPtoUpdateWatermarkActivity*。 

31. 切换到“SQL 帐户”选项卡，为“链接服务”选择“SQLDBEdgeLinkedService”。   

32. 切换到“存储过程”  选项卡，然后执行以下步骤：

    1. 为“存储过程名称”选择“[dbo].[usp_write_watermark]”。  

    2. 若要指定存储过程参数的值，请选择“导入参数”，然后为参数输入以下值：

    |Name|类型|值|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}|
    |TableName|String|@{activity('OldWaterMark').output.firstRow.TableName}|

33. 若要验证管道设置，请选择工具栏中的“验证”  。 确认没有任何验证错误。 若要关闭“管道验证报告”窗口，请选择 **>>** 。 

34. 选择“全部发布”按钮，将实体（链接服务、数据集和管道）发布到 Azure 数据工厂服务。  等待“发布成功”消息出现。

## <a name="trigger-a-pipeline-on-schedule"></a>按计划触发管道

1. 在管道工具栏中选择“添加触发器”，然后依次选择“新建/编辑”、“+ 新建”。   

2. 将触发器命名为 *HourlySync*，选择“计划”作为类型，并将“重复周期”设置为每 1 小时。  

3. 选择“确定”  。

4. 选择“全部发布”。 

5. 选择“立即触发”。 

6. 在左侧切换到“监视”选项卡。  可以看到手动触发器触发的管道运行的状态。 选择“刷新”按钮以刷新列表。 

## <a name="next-steps"></a>后续步骤

本教程中的 Azure 数据工厂管道每隔一小时将数据从 SQL Database Edge 实例上的表复制到 Azure Blob 存储中的某个位置。 若要了解如何在更多方案中使用数据工厂，请浏览[这些教程](../data-factory/tutorial-copy-data-portal.md)。
