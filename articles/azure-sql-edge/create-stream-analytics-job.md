---
title: 在 Azure SQL Edge 中创建 T-SQL 流式处理作业
description: 了解如何在 Azure SQL Edge 中创建流分析作业。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: f0fcdf7aab5f43a0412cd28a1c15188b19770dc6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888100"
---
# <a name="create-a-data-streaming-job-in-azure-sql-edge"></a>在 Azure SQL Edge 中创建数据流作业 

本文介绍了如何在 Azure SQL Edge 中创建 T-SQL 流式处理作业。 你将创建外部流输入和输出对象，然后在创建流式处理作业的过程中定义流式处理作业查询。

## <a name="configure-the-external-stream-input-and-output-objects"></a>配置外部流输入和输出对象

T-SQL 流式处理使用 SQL Server 的外部数据源功能，来定义与流式处理作业的外部流输入和输出相关联的数据源。 使用以下 T-SQL 命令创建外部流输入或输出对象：

- [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql)

- [CREATE EXTERNAL DATA SOURCE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql)

- [CREATE EXTERNAL STREAM (Transact-SQL)](#example-create-an-external-stream-object-to-azure-sql-database)

此外，如果将 Azure SQL Edge、SQL Server 或 Azure SQL 数据库用作输出流，则需要使用 [CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/create-database-scoped-credential-transact-sql)。 此 T-SQL 命令定义用于访问数据库的凭据。

### <a name="supported-input-and-output-stream-data-sources"></a>支持的输入和输出流数据源

Azure SQL Edge 目前仅支持以下数据源作为流输入和输出。

| 数据源类型 | 输入 | 输出 | 说明 |
|------------------|-------|--------|------------------|
| Azure IoT Edge 中心 | Y | Y | 用于将流式处理数据读取和写入到 Azure IoT Edge 中心的数据源。 有关详细信息，请参阅 [IoT Edge 中心](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)。|
| SQL 数据库 | N | Y | 将流式处理数据写入 SQL 数据库的数据源连接。 数据库可以是 Azure SQL Edge 中的本地数据库，也可以是 SQL Server 或 Azure SQL 数据库中的远程数据库。|
| Kafka | Y | N | 从 Kafka 主题读取流式处理数据的数据源。 此适配器目前仅适用于 Azure SQL Edge 的 Intel 或 AMD 版本。 它不适用于 Azure SQL Edge 的 ARM64 版本。|

### <a name="example-create-an-external-stream-inputoutput-object-for-azure-iot-edge-hub"></a>示例：为 Azure IoT Edge 中心创建外部流输入/输出对象

以下示例为 Azure IoT Edge 中心创建外部流对象。 若要为 Azure IoT Edge 中心创建外部流输入/输出数据源，首先也需针对要读取或写入的数据的布局创建一个外部文件格式。

1. 创建 JSON 类型的外部文件格式。

    ```sql
    Create External file format InputFileFormat
    WITH 
    (  
       format_type = JSON,
    )
    go
    ```

2. 为 Azure IoT Edge 中心创建外部数据源。 以下 T-SQL 脚本创建与 IoT Edge 中心的数据源连接，该中心与 Azure SQL Edge 在同一 Docker 主机上运行。

    ```sql
    CREATE EXTERNAL DATA SOURCE EdgeHubInput 
    WITH 
    (
        LOCATION = 'edgehub://'
    )
    go
    ```

3. 为 Azure IoT Edge 中心创建外部流对象。 以下 T-SQL 脚本为 IoT Edge 中心创建流对象。 对于 IoT Edge 中心流对象，LOCATION 参数是要读取或写入的 IoT Edge 中心主题或通道的名称。

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

下面的示例为 Azure SQL Edge 中的本地数据库创建了一个外部流对象。 

1. 在数据库上创建主密钥。 这是加密凭据密钥所必需的。

    ```sql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<<Strong_Password_For_Master_Key_Encryption>>';
    ```

2. 创建用于访问 SQL Server 源的、以数据库为作用域的凭据。 下面的示例为外部数据源创建一个凭据，其中 IDENTITY = username 且 SECRET = password。

    ```sql
    CREATE DATABASE SCOPED CREDENTIAL SQLCredential
    WITH IDENTITY = '<SQL_Login>', SECRET = '<SQL_Login_PASSWORD>'
    go
    ```

3. 使用 CREATE EXTERNAL DATA SOURCE 创建外部数据源。 下面的示例：

    * 创建名为 LocalSQLOutput 的外部数据源。
    * 标识外部数据源 (LOCATION = '<vendor>://<server>[:<port>]')。 在示例中，它指向 Azure SQL Edge 的本地实例。
    * 使用先前创建的凭据。

    ```sql
    CREATE EXTERNAL DATA SOURCE LocalSQLOutput 
    WITH 
    (
        LOCATION = 'sqlserver://tcp:.,1433',
        CREDENTIAL = SQLCredential
    )
    go
    ```

4. 创建外部流对象。 下面的示例创建一个指向 MySQLDatabase 数据库中的 dbo.TemperatureMeasurements 表的外部流对象。

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

### <a name="example-create-an-external-stream-object-for-kafka"></a>示例：为 Kafka 创建外部流对象

下面的示例为 Azure SQL Edge 中的本地数据库创建了一个外部流对象。 此示例假设已将 kafka 服务器配置为使用匿名访问。 

1. 使用 CREATE EXTERNAL DATA SOURCE 创建外部数据源。 下面的示例：

    ```sql
    Create EXTERNAL DATA SOURCE [KafkaInput] 
    With
    (
        LOCATION = N'kafka://<kafka_bootstrap_server_name_ip>:<port_number>'
    )
    GO
    ```
2. 为 kafka 输入创建外部文件格式。 以下示例创建了采用 GZipped 压缩的 JSON 文件格式。 

   ```sql
   CREATE EXTERNAL FILE FORMAT JsonGzipped  
    WITH 
    (  
        FORMAT_TYPE = JSON , 
        DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec' 
    )
   ```
    
3. 创建外部流对象。 以下示例创建指向 Kafka 主题 `*TemperatureMeasurement*` 的外部流对象。

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

使用 `sys.sp_create_streaming_job` 系统存储过程来定义流式处理查询并创建流式处理作业。 `sp_create_streaming_job` 存储过程采用以下参数：

- `job_name`：流式处理作业的名称。 流式处理作业名称在实例中是唯一的。
- `statement`：基于[流分析查询语言](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference?)的流式处理查询语句。

下面的示例创建一个简单的流式处理作业，其中包含一个流式处理查询。 此查询从 IoT Edge 中心读取输入，并写入到数据库中的 `dbo.TemperatureMeasurements`。

```sql
EXEC sys.sp_create_streaming_job @name=N'StreamingJob1',
@statement= N'Select * INTO TemperatureMeasurements from MyEdgeHubInput'
```

以下示例创建一个更复杂的流式处理作业，其中包含多个不同查询。 这些查询中有一个查询使用内置的 `AnomalyDetection_ChangePoint` 函数来识别温度数据中的异常。

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

若要在 Azure SQL Edge 中启动流式处理作业，请运行 `sys.sp_start_streaming_job` 存储过程。 该存储过程需要使用要启动的流式处理作业的名称作为输入。

```sql
exec sys.sp_start_streaming_job @name=N'StreamingJob1'
go
```

若要停止流式处理作业，请运行 `sys.sp_stop_streaming_job` 存储过程。 该存储过程需要使用要停止的流式处理作业的名称作为输入。

```sql
exec sys.sp_stop_streaming_job @name=N'StreamingJob1'
go
```

若要丢弃（或删除）流式处理作业，请运行 `sys.sp_drop_streaming_job` 存储过程。 该存储过程需要使用要丢弃的流式处理作业的名称作为输入。

```sql
exec sys.sp_drop_streaming_job @name=N'StreamingJob1'
go
```

若要获取流式处理作业的当前状态，请运行 `sys.sp_get_streaming_job` 存储过程。 该存储过程需要使用要丢弃的流式处理作业的名称作为输入。 它输出流式处理作业的名称和当前状态。

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

流式处理作业可以处于以下任一状态：

| 状态 | 说明 |
|--------| ------------|
| 创建 | 流式处理作业已创建，但尚未启动。 |
| 正在启动 | 流式处理作业处于开始阶段。 |
| 空闲 | 流式处理作业正在运行，但没有要处理的输入。 |
| Processing | 流式处理作业正在运行，且正在处理输入。 此状态指示流式处理作业的正常运行状态。 |
| 已降级 | 流式处理作业正在运行，但在处理输入期间出现一些非致命错误。 输入作业将继续运行，但将删除遇到错误的输入。 |
| 已停止 | 流式处理作业已停止。 |
| Failed | 流式处理作业失败。 这通常表示在处理过程中出现灾难性错误。 |

## <a name="next-steps"></a>后续步骤

- [在 Azure SQL Edge 中查看与流式处理作业关联的元数据](streaming-catalog-views.md) 
- [创建外部流](create-external-stream-transact-sql.md)
