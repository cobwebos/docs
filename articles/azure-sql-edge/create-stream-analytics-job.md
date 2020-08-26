---
title: 在 Azure SQL Edge 中创建 T-sql 流式处理作业（预览）
description: 了解如何在 Azure SQL Edge （预览版）中创建流分析作业。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: 346a59f085e766fef09d73b9e7baa03dad510148
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321711"
---
# <a name="create-an-azure-stream-analytics-job-in-azure-sql-edge-preview"></a>在 Azure SQL Edge （预览版）中创建 Azure 流分析作业 

本文介绍如何在 Azure SQL Edge （预览版）中创建 T-sql 流式处理作业。 创建外部流输入和输出对象，然后将流式处理作业查询定义为流式处理作业的一部分。

> [!NOTE]
> 若要在 Azure SQL Edge 中启用 T-sql 流式处理功能，请启用 TF 11515 作为启动选项，或使用[DBCC TRACEON]( https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-traceon-transact-sql)命令。 有关如何使用 mssql. 会议文件启用跟踪标志的详细信息，请参阅[使用 mssql. 会议文件进行配置](configure.md#configure-by-using-an-mssqlconf-file)。

## <a name="configure-the-external-stream-input-and-output-objects"></a>配置外部流输入和输出对象

T-sql 流式处理使用 SQL Server 的外部数据源功能定义与流式处理作业的外部流输入和输出相关联的数据源。 使用以下 T-sql 命令创建外部流输入或输出对象：

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

- [CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

此外，如果将 Azure SQL Edge、SQL Server 或 Azure SQL 数据库用作输出流，则需要[创建数据库作用域凭据（transact-sql）](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)。 此 T-sql 命令定义用于访问数据库的凭据。

### <a name="supported-input-and-output-stream-data-sources"></a>支持的输入和输出流数据源

Azure SQL Edge 目前仅支持以下数据源作为流输入和输出。

| 数据源类型 | 输入 | 输出 | 说明 |
|------------------|-------|--------|------------------|
| Azure IoT Edge 中心 | 是 | 是 | 用于读取流数据并将其写入 Azure IoT Edge 中心的数据源。 有关详细信息，请参阅[IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)。|
| SQL 数据库 | N | Y | 将流式处理数据写入 SQL 数据库的数据源连接。 数据库可以是 Azure SQL Edge 中的本地数据库，也可以是 SQL Server 或 Azure SQL 数据库中的远程数据库。|
| Kafka | Y | N | 从 Kafka 主题读取流式处理数据的数据源。 此适配器目前仅适用于 Azure SQL Edge 的 Intel 或 AMD 版本。 它不适用于 Azure SQL Edge 的 ARM64 版本。|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>示例：为 Azure IoT Edge 集线器创建外部流输入/输出对象

下面的示例为 Azure IoT Edge 集线器创建外部流对象。 若要为 Azure IoT Edge 中心创建外部流输入/输出数据源，首先需要为要读取或写入的数据布局创建外部文件格式。

1. 创建类型为 JSON 的外部文件格式。

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. 为 Azure IoT Edge 中心创建外部数据源。 以下 T-sql 脚本创建与在 Azure SQL Edge 相同 Docker 主机上运行的 IoT Edge 集线器的数据源连接。

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. 为 Azure IoT Edge 中心创建外部流对象。 以下 T-sql 脚本为 IoT Edge 中心创建流对象。 对于 IoT Edge 中心流对象，LOCATION 参数是要读取或写入的 IoT Edge 中心主题或通道的名称。

    ```sql
    CREATE EXTERNAL STREAM MyTempSensors 
    WITH 
    (
        DATA_SOURCE = EdgeHubInput,
        FILE_FORMAT = InputFileFormat,
        LOCATION = N'TemperatureSensors',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    go
    ```

### <a name="example-create-an-external-stream-object-to-azure-sql-database"></a>示例：创建 Azure SQL 数据库的外部流对象

下面的示例在 Azure SQL Edge 中创建本地数据库的外部流对象。 

1. 在数据库上创建主密钥。 这是加密凭据密钥所必需的。

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. 创建用于访问 SQL Server 源的数据库范围的凭据。 下面的示例使用 IDENTITY = username 和 SECRET = password 为外部数据源创建凭据。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. 使用 CREATE EXTERNAL DATA SOURCE 创建外部数据源。 下面的示例：

    * 创建名为*LocalSQLOutput*的外部数据源。
    * 标识外部数据源 (LOCATION = '<vendor>://<server>[:<port>]')。 在此示例中，它指向 Azure SQL Edge 的本地实例。
    * 使用前面创建的凭据。

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. 创建外部流对象。 下面的示例创建一个指向表 dbo 的外部流对象 *。TemperatureMeasurements*，在数据库*MySQLDatabase*中。

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurements 
    WITH 
    (
        DATA_SOURCE = LocalSQLOutput,
        LOCATION = N'MySQLDatabase.dbo.TemperatureMeasurements',
        INPUT_OPTIONS = N'',
        OUTPUT_OPTIONS = N''
    );
    ```

### <a name="example-create-an-external-stream-object-for-kafka"></a>示例：创建 Kafka 的外部流对象

下面的示例在 Azure SQL Edge 中创建本地数据库的外部流对象。 此示例假设已将 kafka 服务器配置为使用匿名访问。 

1. 使用 CREATE EXTERNAL DATA SOURCE 创建外部数据源。 下面的示例：

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. 为 kafka 输入创建外部文件格式。 下面的示例使用 Gzip 压缩过压缩创建了 JSON 文件格式。 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```
    
3. 创建外部流对象。 下面的示例创建一个指向 Kafka 主题的外部流对象 `*TemperatureMeasurement*` 。

    ```sql
    CREATE EXTERNAL STREAM TemperatureMeasurement 
    WITH 
    (  
        DATA_SOURCE = KafkaInput, 
        FILE_FORMAT = JsonGzipped,
        LOCATION = 'TemperatureMeasurement',     
        INPUT_OPTIONS = 'PARTITIONS: 10' 
    ); 
    ```

## <a name="create-the-streaming-job-and-the-streaming-queries"></a>创建流式处理作业和流式处理查询

使用 `sys.sp_create_streaming_job` 系统存储过程来定义流查询并创建流式处理作业。 该 `sp_create_streaming_job` 存储过程采用以下参数：

- `job_name`：流式处理作业的名称。 流式处理作业名称在实例中是唯一的。
- `statement`：[流分析查询基于语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)的流式处理查询语句。

以下示例创建一个具有一个流式处理查询的简单流式处理作业。 此查询从 IoT Edge 中心读取输入，并 `dbo.TemperatureMeasurements` 在数据库中将写入到中。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

下面的示例使用多个不同的查询来创建更复杂的流式处理作业。 这些查询包括一个使用内置 `AnomalyDetection_ChangePoint` 函数标识温度数据中的异常的查询。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob2', @statement=
N' Select * INTO TemperatureMeasurements1 from MyEdgeHubInput1

Select * Into TemperatureMeasurements2 from MyEdgeHubInput2

Select * Into TemperatureMeasurements3 from MyEdgeHubInput3

SELECT
Timestamp as [Time],
[Temperature] As [Temperature],
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''Score'') as ChangePointScore,
GetRecordPropertyValue(AnomalyDetection_ChangePoint(Temperature, 80, 1200) OVER(LIMIT DURATION(minute, 20)), ''IsAnomaly'') as IsChangePointAnomaly
INTO TemperatureAnomalies FROM MyEdgeHubInput2;
'
go
```

## <a name="start-stop-drop-and-monitor-streaming-jobs"></a>启动、停止、删除和监视流式处理作业

若要在 Azure SQL Edge 中启动流式处理作业，请运行 `sys.sp_start_streaming_job` 存储过程。 存储过程需要启动流式处理作业的名称作为输入。

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

若要停止流式处理作业，请运行 `sys.sp_stop_streaming_job` 存储过程。 存储过程需要停止的流式处理作业的名称作为输入。

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

若要删除（或删除）流式处理作业，请运行该 `sys.sp_drop_streaming_job` 存储过程。 存储过程需要删除流式处理作业的名称作为输入。

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

若要获取流式处理作业的当前状态，请运行 `sys.sp_get_streaming_job` 存储过程。 存储过程需要删除流式处理作业的名称作为输入。 它输出流式处理作业的名称和当前状态。

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

流式处理作业可以具有以下状态之一：

| 状态 | 描述 |
|--------| ------------|
| 创建 | 流式处理作业已创建，但尚未启动。 |
| 正在启动 | 流式处理作业处于开始阶段。 |
| 空闲 | 流式处理作业正在运行，但没有要处理的输入。 |
| Processing | 流式处理作业正在运行，且正在处理输入。 此状态指示流式处理作业的正常运行状态。 |
| 已降级 | 流式处理作业正在运行，但在输入处理期间出现一些非致命错误。 输入作业将继续运行，但将删除遇到错误的输入。 |
| 已停止 | 流式处理作业已停止。 |
| 失败 | 流式处理作业失败。 这通常表示在处理过程中出现灾难性错误。 |

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL Edge（预览版）中查看与流式处理作业关联的元数据](streaming-catalog-views.md) 
- [创建外部流](create-external-stream-transact-sql.md)
