---
title: "SQL Server 存储过程活动"
description: "了解如何使用 SQL Server 存储过程活动从数据工厂管道调用 Azure SQL 数据库或 Azure SQL 数据仓库中的存储过程。"
services: data-factory
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: monicar
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
robots: noindex
ms.openlocfilehash: f6da8c18c1ac8fbcea217f41c34d50a86b001b11
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2018
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server 存储过程活动
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 活动](data-factory-hive-activity.md) 
> * [Pig 活动](data-factory-pig-activity.md)
> * [MapReduce 活动](data-factory-map-reduce.md)
> * [Hadoop 流式处理活动](data-factory-hadoop-streaming-activity.md)
> * [Spark 活动](data-factory-spark.md)
> * [机器学习批处理执行活动](data-factory-azure-ml-batch-execution-activity.md)
> * [机器学习更新资源活动](data-factory-azure-ml-update-resource-activity.md)
> * [存储过程活动](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 活动](data-factory-usql-activity.md)
> * [.NET 自定义活动](data-factory-use-custom-activities.md)

> [!NOTE]
> 本文适用于 Azure 数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（即预览版），请参阅[在数据工厂版本 2 中使用存储过程活动转换数据](../transform-data-using-stored-procedure.md)。

## <a name="overview"></a>概述
可使用数据工厂[管道](data-factory-create-pipelines.md)中的数据转换活动将原始数据转换和处理为预测和见解。 存储过程活动是数据工厂支持的转换活动之一。 本文基于[数据转换活动](data-factory-data-transformation-activities.md)一文，它概述了数据转换和数据工厂中支持的转换活动。

可以使用存储过程活动调用企业或 Azure 虚拟机 (VM) 中以下数据存储中的存储过程： 

- Azure SQL 数据库
- Azure SQL 数据仓库
- SQL Server 数据库。  如果使用 SQL Server，请在托管数据库的同一计算机上或在可以访问数据库的单独计算机上安装数据管理网关。 数据管理网关是一种以安全和托管的方式将本地/Azure VM 上的数据源与云服务进行连接的组件。 有关详细信息，请参阅[数据管理网关](data-factory-data-management-gateway.md)一文。

> [!IMPORTANT]
> 将数据复制到 Azure SQL 数据库或 SQL Server 中时，可以使用 sqlWriterStoredProcedureName 属性将复制活动中的 SqlSink 配置为调用存储过程。 有关详细信息，请参阅[从复制活动中调用存储过程](data-factory-invoke-stored-procedure-from-copy-activity.md)。 有关属性的详细信息，请参阅以下连接器文章：[Azure SQL 数据库](data-factory-azure-sql-connector.md#copy-activity-properties)、[SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties)。 不支持在使用复制活动将数据复制到 Azure SQL 数据仓库时调用存储过程。 但是，可使用存储过程活动来调用 SQL 数据仓库中的存储过程。 
>  
> 从 Azure SQL 数据库、SQL Server 或 Azure SQL 数据仓库复制数据时，可以使用 sqlReaderStoredProcedureName 属性将复制活动中的 SqlSource 配置为调用存储过程，以便从源数据库读取数据。 有关详细信息，请参阅以下连接器文章：[Azure SQL 数据库](data-factory-azure-sql-connector.md#copy-activity-properties)、[SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties)、[Azure SQL 数据仓库](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          


以下演练使用管道中的存储过程活动来调用 Azure SQL 数据库中的存储过程。 

## <a name="walkthrough"></a>演练
### <a name="sample-table-and-stored-procedure"></a>示例表和存储过程
1. 在 Azure SQL 数据库中，使用 SQL Server Management Studio 或其他熟悉的工具，创建以下**表**。 Datetimestamp 列是生成相应 ID 的日期和时间。

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    ID 是唯一标识，而 datetimestamp 列是生成相应 ID 的日期和时间。
    
    ![样本数据](./media/data-factory-stored-proc-activity/sample-data.png)

    在此示例中，存储过程在 Azure SQL 数据库中。 如果存储过程在 Azure SQL 数据仓库和 SQL Server 数据库中，则方法非常相似。 对于 SQL Server 数据库，必须安装[数据管理网关](data-factory-data-management-gateway.md)。
2. 创建以下**存储过程**，将数据插入 **sampletable**。

    ```SQL
    CREATE PROCEDURE sp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > 参数（在本示例中为 DateTime）的**名称**和**大小写**必须与管道/活动 JSON 中指定的参数匹配。 在存储过程定义中，请确保使用 **@** 作为参数的前缀。

### <a name="create-a-data-factory"></a>创建数据工厂
1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左侧菜单中单击“新建”，然后依次单击“智能 + 分析”、“数据工厂”。

    ![新建数据工厂](media/data-factory-stored-proc-activity/new-data-factory.png)    
3. 在“新建数据工厂”边栏选项卡中，输入 **SProcDF** 作为名称。 Azure 数据工厂名称必须**全局唯一**。 必须将你的姓名作为数据工厂的名称前缀，才能成功创建工厂。

   ![新建数据工厂](media/data-factory-stored-proc-activity/new-data-factory-blade.png)         
4. 选择 **Azure 订阅**。
5. 对于**资源组**，请执行以下步骤之一：
   1. 单击“新建”，然后为资源组输入名称。
   2. 单击“使用现有资源组”并选择一个现有的资源组。  
6. 选择数据工厂的**位置**。
7. 选择“固定到仪表板”以便在下次登录时在仪表板上看到数据工厂。
8. 在“新建数据工厂”边栏选项卡中单击“创建”。
9. 此时可在 Azure 门户的“仪表板”中看到所创建的数据工厂。 成功创建数据工厂后，将看到数据工厂页，其中显示了数据工厂的内容。

   ![数据工厂主页](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>创建 Azure SQL 链接服务
创建数据工厂后，创建 Azure SQL 链接服务，将包含 sampletable 表和 sp_sample 存储过程的 Azure SQL 数据库链接到数据工厂。

1. 在 **SProcDF** 的“数据工厂”边栏选项卡中，单击“编写和部署”，启动数据工厂编辑器。
2. 在命令栏上单击“新建数据存储”并选择“Azure SQL 数据库”。 在编辑器中，应会看到用于创建 Azure SQL 链接服务的 JSON 脚本。

   ![新建数据存储](media/data-factory-stored-proc-activity/new-data-store.png)
3. 在 JSON 脚本中，进行以下更改：

   1. 将 `<servername>` 替换为 Azure SQL 数据库服务器的名称。
   2. 将 `<databasename>` 替换为在其中创建表和存储过程的数据库。
   3. 将 `<username@servername>` 替换为有权访问数据库的用户帐户。
   4. 将 `<password>` 替换为用户帐户的密码。

      ![新建数据存储](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. 若要部署链接服务，请单击命令栏上的“部署”。 确认在左侧的树视图中已显示 AzureSqlLinkedService。

    ![包含链接服务的树视图](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>创建输出数据集
必须为存储过程活动指定输出数据集，即使存储过程不生成任何数据也是如此。 这是因为活动的计划（活动的运行频率 - 每小时、每天等）取决于输出数据集。 输出数据集必须使用**链接服务**，其指代 Azure SQL 数据库或 Azure SQL 数据仓库或要在其中运行存储过程的 SQL Server 数据库。 输出数据集可用于传递存储过程的结果，以供管道中另一活动（[链接活动](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)）进行后续处理。 但是，数据工厂不会自动将存储过程的输出写入此数据集。 它是写入输出数据集指向的 SQL 表的存储过程。 在某些情况下，输出数据集可能是虚拟数据集（这种数据集指向实际不包含存储过程输出的表）。 此虚拟数据集仅用于指定运行存储过程活动的计划。 

1. 在工具栏上单击“...更多”，然后依次单击“新建数据集”和“Azure SQL”。 单击命令栏上的“新建数据集”并选择“Azure SQL”。

    ![包含链接服务的树视图](media/data-factory-stored-proc-activity/new-dataset.png)
2. 将以下 JSON 脚本复制/粘贴到 JSON 编辑器。

    ```JSON
    {                
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. 若要部署数据集，请单击命令栏上的“部署”。 确认树视图中显示了此数据集。

    ![包含链接服务的树视图](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>使用 SqlServerStoredProcedure 活动创建管道
现在，使用存储过程活动来创建管道。 

请注意以下属性： 

- 类型属性设置为 SqlServerStoredProcedure。 
- 类型属性中的 storedProcedureName 设置为 sp_sample（存储过程的名称）。
- storedProcedureParameters 部分包含一个名为 DataTime 的参数。 采用 JSON 格式时，该参数的名称和大小写必须与存储过程定义中参数的名称和大小写相匹配。 如果需要为参数传递 null，请使用语法：`"param1": null`（全部小写）。
 
1. 单击命令栏上的“...更多”并单击“新建管道”。
2. 复制/粘贴以下 JSON 代码段：   

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "sp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
             "start": "2017-04-02T00:00:00Z",
             "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. 若要部署管道，请单击工具栏上的“部署”。  

### <a name="monitor-the-pipeline"></a>监视管道
1. 单击“X”关闭“数据工厂编辑器”边栏选项卡，导航回到“数据工厂”边栏选项卡，然后单击“图示”。

    ![图示磁贴](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. 在“图示视图”中，可以看到管道的概述，以及本教程中使用的数据集。

    ![图示磁贴](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. 在“图示视图”中，双击数据集 `sprocsampleout`。 将看到切片处于“就绪”状态。 由于切片是在 JSON 中针对开始时间和结束时间之间的每一小时生成的，因此，应该有 5 个切片。

    ![图示磁贴](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. 切片处于“就绪”状态时，对 Azure SQL 数据库运行 `select * from sampletable` 查询，以验证存储过程是否已将数据插入到表中。

   ![输出数据](./media/data-factory-stored-proc-activity/output.png)

   如需了解有关监视 Azure 数据工厂管道的详细信息，请参阅[监视管道](data-factory-monitor-manage-pipelines.md)。  


## <a name="specify-an-input-dataset"></a>指定输入数据集
在本演练中，存储过程活动没有任何输入数据集。 如果指定了输入数据集，在输入数据集的切片可用（处于“就绪”状态）前，存储过程活动不会运行。 该数据集可以是外部数据集（不是由相同管道中的另一个活动生成）或由上游活动（在此活动前运行的活动）生成的内部数据集。 可以为存储过程活动指定多个输入数据集。 如果执行此操作，仅在所有输入数据集切片可用（处于“就绪”状态）时存储过程活动才会运行。 该输入数据集无法在存储过程中用作参数。 它仅用于在开始存储过程活动前检查依赖项。

## <a name="chaining-with-other-activities"></a>与其他活动链接
如果想要将上游活动与此活动链接，请将上游活动的输出指定为此活动的输入。 执行此操作后，在上游活动完成且上游活动的输出数据集可用（处于“就绪”状态）后，存储过程活动才会运行。 可以将多个上游活动的输出数据集指定为存储过程活动的输入数据集。 执行此操作后，仅在所有输入数据集切片可用时存储过程活动才会运行。  

在下面的示例中，复制活动的输出是 OutputDataset，这是存储过程活动的输入。 因此，在复制活动完成且 OutputDataset 切片可用（处于“就绪”状态）之前，存储过程活动将不会运行。 如果指定了多个输入数据集，在所有输入数据集切片可用（处于“就绪”状态）前，存储过程活动将不会运行。 输入数据集不能直接用作存储过程活动的参数。 

有关链接活动的详细信息，请参阅[管道中的多个活动](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{

    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }

        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

同样，要将存储过程活动与下游活动（在存储过程活动完成后运行的活动）链接，需将存储过程活动的输出数据集指定为管道中下游活动的输入。

> [!IMPORTANT]
> 将数据复制到 Azure SQL 数据库或 SQL Server 中时，可以使用 sqlWriterStoredProcedureName 属性将复制活动中的 SqlSink 配置为调用存储过程。 有关详细信息，请参阅[从复制活动中调用存储过程](data-factory-invoke-stored-procedure-from-copy-activity.md)。 有关属性的详细信息，请参阅以下连接器文章：[Azure SQL 数据库](data-factory-azure-sql-connector.md#copy-activity-properties)、[SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties)。
>  
> 从 Azure SQL 数据库、SQL Server 或 Azure SQL 数据仓库复制数据时，可以使用 sqlReaderStoredProcedureName 属性将复制活动中的 SqlSource 配置为调用存储过程，以便从源数据库读取数据。 有关详细信息，请参阅以下连接器文章：[Azure SQL 数据库](data-factory-azure-sql-connector.md#copy-activity-properties)、[SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties)、[Azure SQL 数据仓库](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)          

## <a name="json-format"></a>JSON 格式
下面是用于定义存储过程活动的 JSON 格式：

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs":  [ { "name": "inputtable"  } ],
    "outputs":  [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":  
        {
            "param1": "param1Value"
            …
        }
    }
}
```

下表描述了其中的 JSON 属性：

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 名称 | 活动名称 |是 |
| description |描述活动用途的文本 |否 |
| type | 必须设置为：**SqlServerStoredProcedure** | 是 |
| inputs | 可选。 如果指定了输入数据集，则它必须可供使用（“就绪”状态），存储过程才能运行。 该输入数据集无法在存储过程中用作参数。 它仅用于在开始存储过程活动前检查依赖项。 |否 |
| outputs | 必须指定存储过程活动的输出数据集。 输出数据集为存储过程活动指定**计划**（每小时、每周、每月等）。 <br/><br/>输出数据集必须使用**链接服务**，其指代 Azure SQL 数据库或 Azure SQL 数据仓库或要在其中运行存储过程的 SQL Server 数据库。 <br/><br/>输出数据集可用于传递存储过程的结果，以供管道中另一活动（[链接活动](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)）进行后续处理。 但是，数据工厂不会自动将存储过程的输出写入此数据集。 它是写入输出数据集指向的 SQL 表的存储过程。 <br/><br/>在某些情况下，输出数据集可以是**虚拟数据集**，它仅用于指定运行存储过程活动的计划。 |是 |
| storedProcedureName |指定 Azure SQL 数据库、Azure SQL 数据仓库或 SQL Server 数据库中存储过程的名称，表示形式为输出表使用的链接服务。 |是 |
| storedProcedureParameters |指定存储过程的参数值。 如果需要为参数传递 null，请使用语法："param1": null（全部小写）。 请参阅以下示例了解如何使用此属性。 |否 |

## <a name="passing-a-static-value"></a>传递静态值
现在，我们可以考虑在包含静态值“Document sample”的表中添加名为“Scenario”的另一个列。

![示例数据 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**表：**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**存储过程：**

```SQL
CREATE PROCEDURE sp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

现在，从存储过程活动传递 **Scenario** 参数和值。 在上述示例中，**typeProperties** 部分如以下代码片段所示：

```JSON
"typeProperties":
{
    "storedProcedureName": "sp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**数据工厂数据集：**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**数据工厂管道**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```