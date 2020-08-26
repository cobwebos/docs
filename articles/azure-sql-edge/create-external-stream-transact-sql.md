---
title: CREATE EXTERNAL STREAM (Transact-SQL) - Azure SQL Edge（预览版）
description: 了解 Azure SQL Edge（预览版）中的 CREATE EXTERNAL STREAM 语句
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/27/2020
ms.openlocfilehash: d4ad11d156fd3a672e93b5e039c82d16b2aebdc3
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321728"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

外部流对象在输入流和输出流中都具有双重目的。 它可用作从事件引入服务（如 Azure 事件中心、Azure IoT 中心（或边缘集线器）或 Kafka）查询流式处理数据的输入，也可用作输出，用于指定从流式处理查询存储结果的位置和方式。

此外，还可以指定 EXTERNAL STREAM，并将其创建为事件中心或 Blob 存储等服务的输出和输入。 这简化了在以下情况下的链接方案：流式处理查询将结果保存到外部流作为输出，另一个流式处理查询从同一外部流读取作为输入。

Azure SQL Edge 目前仅支持以下数据源作为流输入和输出。

| 数据源类型 | 输入 | 输出 | 说明 |
|------------------|-------|--------|------------------|
| Azure IoT Edge 中心 | 是 | 是 | 用于读取流数据并将其写入 Azure IoT Edge 中心的数据源。 有关详细信息，请参阅[IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)。|
| SQL 数据库 | N | Y | 将流式处理数据写入 SQL 数据库的数据源连接。 数据库可以是 Azure SQL Edge 中的本地数据库，也可以是 SQL Server 或 Azure SQL 数据库中的远程数据库。|
| Kafka | Y | N | 从 Kafka 主题读取流式处理数据的数据源。 Kafka 支持不可用于 Azure SQL Edge 的 ARM64 版本。|



## <a name="syntax"></a>语法

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
( <column_definition> [, <column_definition> ] * ) -- Used for Inputs - optional 
WITH  ( <with_options> )

<column_definition> ::=
  column_name <column_data_type>

<data_type> ::=
[ type_schema_name . ] type_name
    [ ( precision [ , scale ] | max ) ]

<with_options> ::=
  DATA_SOURCE = data_source_name, 
  LOCATION = location_name, 
  [FILE_FORMAT = external_file_format_name], --Used for Inputs - optional 
  [<optional_input_options>],
  [<optional_output_options>], 
  TAGS = <tag_column_value>

<optional_input_options> ::= 
  INPUT_OPTIONS = '[<Input_options_data>]'

<Input_option_data> ::= 
      <input_option_values> [ , <input_option_values> ]

<input_option_values> ::=
  PARTITIONS: [number_of_partitions]
  | CONSUMER_GROUP: [ consumer_group_name ] 
  | TIME_POLICY: [ time_policy ] 
  | LATE_EVENT_TOLERANCE: [ late_event_tolerance_value ] 
  | OUT_OF_ORDER_EVENT_TOLERANCE: [ out_of_order_tolerance_value ]
  
<optional_output_options> ::= 
  OUTPUT_OPTIONS = '[<output_option_data>]'

<output_option_data> ::= 
      <output_option_values> [ , <output_option_values> ]

<output_option_values> ::=
   REJECT_POLICY: [ reject_policy ] 
   | MINIMUM_ROWS: [ row_value ] 
   | MAXIMUM_TIME: [ time_value_minutes] 
   | PARTITION_KEY_COLUMN: [ partition_key_column_name ] 
   | PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | SYSTEM_PROPERTY_COLUMNS: [ ( [ output_col_name ] ) ] 
   | PARTITION_KEY: [ partition_key_name ] 
   | ROW_KEY: [ row_key_name ] 
   | BATCH_SIZE: [ batch_size_value ] 
   | MAXIMUM_BATCH_COUNT: [ batch_value ] 
   | STAGING_AREA: [ blob_data_source ]
 
<tag_column_value> ::= -- Reserved for Future Usage
); 
```

## <a name="arguments"></a>参数

- [DATA_SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [FILE_FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- LOCATION：指定数据源中的实际数据或位置的名称。 
   - 对于 Edge 中心或 Kafka 流对象，location 指定要读取或写入的 Edge 中心或 Kafka 主题的名称。
   - 对于 SQL 流对象（SQL Server、Azure SQL 数据库或 Azure SQL Edge），指定表的名称。 如果在与目标表相同的数据库和架构中创建流，则仅表名后缀。 否则，需要完全限定表名（<database_name schema_name table_name）。
   - 对于 Azure Blob 存储流对象位置，指的是要在 Blob 容器中使用的路径模式。 有关此功能的详细信息，请参阅（/articles/stream-analytics/stream-analytics-define-outputs.md # blob-gen2）

- **INPUT_OPTIONS**：为作为流式处理查询的输入的服务（如 Kafka、IoT Edge 集线器）指定选项作为键值对
    - 分区：为主题定义的分区数
      - 适用于 Kafka 输入流
    - CONSUMER_GROUP：事件中心和 IoT 中心限制一个使用者组中的读者数量（最多 5 个）。 将此字段留空将使用“$Default”使用者组。
      - 保留供将来使用。 不适用于 Azure SQL Edge。  
    - TIME_POLICY：描述当延迟事件或无序事件超过其容错值时，是删除事件还是调整事件时间。
      - 保留供将来使用。 不适用于 Azure SQL Edge。
    - LATE_EVENT_TOLERANCE：最大可接受的时间延迟。 延迟表示事件时间戳与系统时钟之间的差异。
      - 保留供将来使用。 不适用于 Azure SQL Edge。
    - OUT_OF_ORDER_EVENT_TOLERANCE：事件在经历从输入到流式处理查询的行程后，可能会乱序。 可以按原样接受这些事件，也可以选择在设定的时间段内暂停以对它们重新排序。
      - 保留供将来使用。 不适用于 Azure SQL Edge。
        
- OUTPUT_OPTIONS：将选项指定为支持服务（此服务作为流式处理查询输出）的键值对 
  - REJECT_POLICY：DROP | RETRY 会在出现数据转换错误时，指定数据错误处理策略。 
    - 适用于所有支持的输出 
  - MINIMUM_ROWS：  
    写入输出时每批所需的最小行数。 对于 Parquet，每批都将创建一个新文件。 
    - 适用于所有支持的输出 
  - MAXIMUM_TIME：  
    每批的最长等待时间（分钟）。 在此时间后，即使不满足最小行数要求，也会将该批写入输出。 
    - 适用于所有支持的输出 
  - PARTITION_KEY_COLUMN：  
    该列用于分区键。 
    - 保留供将来使用。 不适用于 Azure SQL Edge。
  - PROPERTY_COLUMNS：  
    要作为自定义属性（如果提供）附加到消息的输出列的列名称逗号分隔列表。  
    - 保留供将来使用。 不适用于 Azure SQL Edge。 
  - SYSTEM_PROPERTY_COLUMNS：  
    要在服务总线消息上填充的系统属性名称和输出列的名称/值对的 JSON 格式集合。 例如，{ "MessageId":   "column1", "PartitionKey": "column2"} 
    - 保留供将来使用。 不适用于 Azure SQL Edge。 
  - PARTITION_KEY：  
    包含分区键的输出列的名称。 分区键是某个给定表中分区的唯一标识，分区键构成了实体主键的第一部分。 分区键是一个最大为 1 KB 的字符串值。 
    - 保留供将来使用。 不适用于 Azure SQL Edge。
  - ROW_KEY：  
    包含行键的输出列的名称。 行键是某个给定分区中实体的唯一标识符。 行键构成了实体主键的第二部分。 行键是一个最大为 1 KB 的字符串值。 
    - 保留供将来使用。 不适用于 Azure SQL Edge。
  - BATCH_SIZE：  
    这表示最大可达 100 条记录的表存储的事务数。 对于 Azure Functions，这表示每次调用时发送到函数的批大小（以字节为单位）- 默认值为 256 kB。 
    - 保留供将来使用。 不适用于 Azure SQL Edge。 
  - MAXIMUM_BATCH_COUNT：  
    每次调用 Azure 函数发送给该函数的最大事件数 - 默认值为 100。 对于 SQL 数据库，这表示随每个批量插入事务一起发送的记录数上限 - 默认值为 10,000。 
    - 适用于所有基于 SQL 的输出 
  - STAGING_AREA：Blob 存储的 EXTERNAL DATA SOURCE 对象 - 用于将高吞吐量数据引入到 SQL 数据仓库的临时区域 
    - 保留供将来使用。 不适用于 Azure SQL Edge。


## <a name="examples"></a>示例

### <a name="example-1---edgehub"></a>示例 1 - EdgeHub

键入：输入或输出<br>

语法：

```sql
CREATE EXTERNAL DATA SOURCE MyEdgeHub 
WITH  
(      
  LOCATION = 'edgehub://'       
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat  
WITH (  
   FORMAT_TYPE = 'JSON', 
); 
 
CREATE EXTERNAL STREAM Stream_A  
WITH    
(   
   DATA_SOURCE = MyEdgeHub, 
   FILE_FORMAT = myFileFormat, 
   LOCATION = '<mytopicname>', 
   OUTPUT_OPTIONS =   
     'REJECT_TYPE: Drop'
);
```

### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>示例 2 - Azure SQL 数据库、Azure SQL Edge、SQL Server

键入：输出<br>

语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>', 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = '<my_server_name>.database.windows.net', 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>', 
  CREDENTIAL = SQLCredName 
); 

CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = '<DatabaseName>.<SchemaName>.<TableName>' ,
   --Note: If table is contained in the database, <TableName> should be sufficient 
    OUTPUT_OPTIONS =  
      'REJECT_TYPE: Drop'
); 
```

### <a name="example-3---kafka"></a>示例 3 - Kafka

键入：输入<br>

语法：
```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = 'kafka://<kafkaserver>:<ipaddress>', 
  CREDENTIAL = kafkaCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = JSON, 
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
); 

CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = '<KafkaTopicName>', 
    FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      'PARTITIONS: 5'
); 
```

## <a name="see-also"></a>另请参阅

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 

