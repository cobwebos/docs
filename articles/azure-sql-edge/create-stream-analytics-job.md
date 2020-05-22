---
title: 在 Azure SQL Edge（预览版）中创建 T-SQL 流式处理作业
description: 了解如何在 Azure SQL Edge（预览版）中创建流分析作业
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 5e0043ebba1a317dcc6798d6be74aac051d97012
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595386"
---
# <a name="create-stream-analytics-job-in-azure-sql-edge-preview"></a>在 Azure SQL Edge（预览版）中创建流分析作业 

本文介绍如何在 Azure SQL Edge（预览版）中创建 T-SQL 流式处理作业。 若要在 SQL Edge 中创建流式处理作业，需执行以下步骤

1. 创建外部流输入和输出对象
2. 在流式处理作业创建过程中定义流式处理作业查询。

## <a name="configure-an-external-stream-input-and-output-object"></a>配置外部流输入和输出对象

T-SQL 流式处理使用 SQL Server 的外部数据源功能，来定义与流式处理作业的外部流输入和输出相关联的数据源。 创建外部流输入或输出对象需要以下 T-SQL 命令。

[CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

[CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-sql-database)

此外，如果 SQL Edge（或 SQL Server、Azure SQL）作为输出流，则需要使用 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql) T-SQL 命令，来定义用于访问 SQL 数据库的凭据。

### <a name="supported-input-and-output-stream-data-sources"></a>支持的输入和输出流数据源

Azure SQL Edge 目前仅支持以下数据源作为流输入和输出。

| 数据源类型 | 输入 | 输出 | 说明 |
|------------------|-------|--------|------------------|
| Azure IoT Edge 中心 | Y | Y | 将流式处理数据读/写到 Azure IoT Edge 中心的数据源。 有关 Azure IoT Edge 中心的详细信息，请参阅 [IoT Edge 中心](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)|
| SQL 数据库 | N | Y | 将流式处理数据写入 SQL 数据库的数据源连接。 SQL 数据库可以是本地 SQL Edge 数据库，也可以是远程 SQL Server 或 Azure SQL 数据库|
| Azure Blob 存储 | N | Y | 将数据写入 Azure 存储帐户上的 blob 的数据源。 |
| Kafka | Y | N | 从 Kafka 主题读取流式处理数据的数据源。 此适配器目前仅适用于 Intel/AMD 版本的 Azure SQL Edge，不适用于 ARM64 版本的 SQL Edge。|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>示例：为 Azure IoT Edge 中心创建外部流输入/输出对象

下面的示例为 Edge 中心创建了一个外部流对象。 若要为 Azure IoT Edge 中心创建外部流输入/输出数据源，首先需要为 SQL 创建一个外部文件格式，以了解待读取/写入的数据的布局。

1. 创建格式类型为 JSON 的外部文件格式。

    ```sql
    Create External file format InputFileFormat
    WITH (  
       format_type = JSON,
    )
    go
    ```

2. 为 IoT Edge 中心创建外部数据源。 下面的 T-SQL 脚本创建了一个数据源到 Edge 中心的连接，该中心在运行 SQL Edge 的同一 docker 主机上运行。

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput WITH (
    LOCATION = 'edgehub://'
    )
    go
    ```

3. 为 IoT Edge 中心创建外部流对象。 下面的 T-SQL 脚本为 Edge 中心创建了一个流对象。 对于 Edge 中心流对象，LOCATION 参数是要读取或写入的 Edge 中心主题/通道的名称。

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors WITH (
    DATA_SOURCE = EdgeHubInput,
    FILE_FORMAT = InputFileFormat,
    LOCATION = N'TemperatureSensors',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    go
    ```

### <a name="example-create-an-external-stream-object-sql-database"></a>示例：创建外部流对象 SQL 数据库

下面的示例为本地 SQL Edge 数据库创建了一个外部流对象。 

1. 在数据库上创建主密钥。 这是加密凭据密钥所必需的。

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. 创建数据库范围凭据以访问 SQL Server 源。 下面的示例为具有 IDENTITY = 'username' 和 SECRET = 'password' 的外部数据源创建了一个凭据。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. 使用 CREATE EXTERNAL DATA SOURCE 创建外部数据源。 下面的示例：

    * 创建名为“LocalSQLOutput”的外部数据源
    * 标识外部数据源 (LOCATION = '<vendor>://<server>[:<port>]')。 在示例中，它指向 SQL Edge 的本地实例。
    * 最后，该示例使用先前创建的凭据。

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput WITH (
    LOCATION = 'sqlserver://tcp:.,1433'
    ,CREDENTIAL = SQLCredential
    )
    go
    ```

4. 创建外部流对象。 下面的示例创建了一个指向 MySQLDatabase 数据库中的 dbo.TemperatureMeasurements 表的外部流对象。

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements WITH (
    DATA_SOURCE = LocalSQLOutput,
    LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
    INPUT_OPTIONS = N'',
    OUTPUT_OPTIONS = N''
    )
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>创建流式处理作业和流式处理查询

使用 sys.sp_create_streaming_job 系统存储过程来定义流式处理查询并创建流式处理作业。 sp_create_streaming_job 存储过程采用两个参数

- job_name - 指定流式处理作业的名称。 流式处理作业名称在实例中是唯一的。
- statement - 基于流式处理查询语句的[流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)。

下面的示例创建了一个简单的流式处理作业，其中含有一个流式处理查询。 此查询从 Edge 中心读取输入并在数据库中写入 dbo.TemperatureMeasurements。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

下面的示例创建了一个更复杂的流式处理作业，包含多个不同的查询，其中一个查询使用内置 AnomalyDetection_ChangePoint 函数来标识温度数据中的异常。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly,
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>启动、停止、删除和监视流式处理作业

若要在 SQL Edge 中启动流式处理作业，请执行 sys.sp_start_streaming_job 存储过程。 存储过程需要启动相同的流式处理作业作为输入。

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

若要在 SQL Edge 中停止流式处理作业，请执行 sys.sp_stop_streaming_job 存储过程。 存储过程需要停止相同的流式处理作业作为输入。

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

若要在 SQL Edge 中移除（或删除）流式处理作业，请执行 sys.sp_drop_streaming_job 存储过程。 存储过程需要删除相同的流式处理作业作为输入。

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

若要在 SQL Edge 中获取流式处理作业的当前状态，请执行 sys.sp_get_streaming_job 存储过程。 存储过程需要删除相同的流式处理作业作为输入，并输出流式处理作业的名称和当前状态。

```sql
exec sys.sp_get_streaming_job @name=N'StreamingJob1'
        WITH RESULT SETS
(
       (
       name nvarchar(256),
       status nvarchar(256)
       )
)
```

流式处理作业可以处于以下任一状态

| 状态 | 说明 |
|--------| ------------|
| 创建 | 流式处理作业已创建，但尚未启动 |
| 正在启动 | 流式处理作业处于启动阶段 |
| 空闲 | 流式处理作业正在运行，但没有要处理的输入 |
| Processing | 流式处理作业正在运行，且正在处理输入。 此状态指示流式处理作业的正常运行状态 |
| 已降级 | 流式处理作业正在运行，但在输入处理期间出现一些非致命的输入/输出序列化/反序列化错误。 输入作业将继续运行，但将删除出错的输入 |
| 已停止 | 流式处理作业已停止 |
| 失败 | 流式处理作业失败。 这通常表示在处理过程中出现灾难性错误 |

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL Edge（预览版）中查看与流式处理作业关联的元数据](streaming-catalog-views.md) 
- [创建外部流](create-external-stream-transact-sql.md)
