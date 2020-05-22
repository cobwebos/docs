---
title: CREATE EXTERNAL STREAM (Transact-SQL) - Azure SQL Edge（预览版）
description: 了解 Azure SQL Edge（预览版）中的 CREATE EXTERNAL STREAM 语句
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: d4ad446d43c90eee1c48ee2ba94585242805fa7d
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83595396"
---
# <a name="create-external-stream-transact-sql"></a>CREATE EXTERNAL STREAM (Transact-SQL)

EXTERNAL STREAM 对象的输入和输出都具有双重目的。 它可用作从事件引入服务（如 Azure 事件中心或 IoT 中心）查询流式处理数据的输入，或者用作输出，用于指定在何处以及如何存储流式处理查询的结果。

此外，还可以指定 EXTERNAL STREAM，并将其创建为事件中心或 Blob 存储等服务的输出和输入。 这适用于链接场景：一个流式处理查询将结果作为输出保存到外部流，另一个流式处理查询将从相同的外部流读取结果作为输入。 


## <a name="syntax"></a>语法

```sql
CREATE EXTERNAL STREAM {external_stream_name}  
(column definition [,column definitions ] * ) --Used for input - optional 
WITH  
(  
  DATA_SOURCE = <data_source_name>, 
  LOCATION = <location_name>, 
  EXTERNAL_FILE_FORMAT = <external_file_format_name>, --Used for input - optional 
   
  INPUT_OPTIONS =  
    ‘CONSUMER_GROUP=<consumer_group_name>; 
    ‘TIME_POLICY=<time_policy>; 
    LATE_EVENT_TOLERANCE=<late_event_tolerance_value>; 
    OUT_OF_ORDER_EVENT_TOLERANCE=<out_of_order_tolerance_value> 
     
    /* Edge options */ 
  , 
  OUTPUT_OPTIONS =  
    ‘REJECT_POLICY=<reject_policy>; 
    MINIMUM_ROWS=<row_value>; 
    MAXIMUM_TIME=<time_value_minutes>; 
    PARTITION_KEY_COLUMN=<partition_key_column_name>; 
    PROPERTY_COLUMNS=(); 
    SYSTEM_PROPERTY_COLUMNS=(); 
    PARTITION_KEY=<partition_key_name>; 
    ROW_KEY=<row_key_name>; 
    BATCH_SIZE=<batch_size_value>; 
    MAXIMUM_BATCH_COUNT=<batch_value>;  
    STAGING_AREA=<blob_data_source>’ 
     
    /* Edge options */ TAGS=<tag_column_value> 
); 
```


## <a name="arguments"></a>参数


- [EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql/)
- [EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql/)
- LOCATION：指定数据源中的实际数据或位置的名称。 对于 Edge 中心或 Kafka 流对象，location 指定要从中读取或向其写入的 Edge 中心或 Kafka 主题的名称。
- INPUT_OPTIONS：将选项指定为事件中心和 IOT 中心（这些中心作为流式处理查询的输入）等服务的键值对
    - CONSUMER_GROUP：事件中心和 IoT 中心限制一个使用者组中的读者数量（最多 5 个）。 将此字段留空将使用“$Default”使用者组。
      - 适用于事件中心和 IOT 中心
    - TIME_POLICY：描述当延迟事件或无序事件超过其容错值时，是删除事件还是调整事件时间。
      - 适用于事件中心和 IOT 中心
    - LATE_EVENT_TOLERANCE：最大可接受的时间延迟。 延迟表示事件时间戳与系统时钟之间的差异。
      - 适用于事件中心和 IOT 中心
    - OUT_OF_ORDER_EVENT_TOLERANCE：事件在经历从输入到流式处理查询的行程后，可能会乱序。 可以按原样接受这些事件，也可以选择在设定的时间段内暂停以对它们重新排序。
      - 适用于事件中心和 IOT 中心
- OUTPUT_OPTIONS：将选项指定为支持服务（此服务作为流式处理查询输出）的键值对 
  - REJECT_POLICY：DROP | RETRY 会在出现数据转换错误时，指定数据错误处理策略。 
    - 适用于所有支持的输出 
  - MINIMUM_ROWS：  
    写入输出时每批所需的最小行数。 对于 Parquet，每批都将创建一个新文件。 
    - 适用于所有支持的输出 
  - MAXIMUM_TIME：  
    每批的最长等待时间。 在此时间后，即使不满足最小行数要求，也会将该批写入输出。 
    - 适用于所有支持的输出 
  - PARTITION_KEY_COLUMN：  
    该列用于分区键。 
    - 适用于事件中心和服务总线主题 
  - PROPERTY_COLUMNS：  
    要作为自定义属性（如果提供）附加到消息的输出列的列名称逗号分隔列表。  
    - 适用于事件中心和服务总线主题及队列 
  - SYSTEM_PROPERTY_COLUMNS：  
    要在服务总线消息上填充的系统属性名称和输出列的名称/值对的 JSON 格式集合。 例如，{ "MessageId":   "column1", "PartitionKey": "column2"} 
    - 适用于服务总线主题和队列 
  - PARTITION_KEY：  
    包含分区键的输出列的名称。 分区键是某个给定表中分区的唯一标识，分区键构成了实体主键的第一部分。 分区键是一个最大为 1 KB 的字符串值。 
    - 适用于表存储和 Azure 函数 
  - ROW_KEY：  
    包含行键的输出列的名称。 行键是某个给定分区中实体的唯一标识符。 行键构成了实体主键的第二部分。 行键是一个最大为 1 KB 的字符串值。 
    - 适用于表存储和 Azure 函数 
  - BATCH_SIZE：  
    这表示最大可达 100 条记录的表存储的事务数。 对于 Azure Functions，这表示每次调用时发送到函数的批大小（以字节为单位）- 默认值为 256 kB。 
    - 适用于表存储和 Azure 函数 
  - MAXIMUM_BATCH_COUNT：  
    每次调用 Azure 函数发送给该函数的最大事件数 - 默认值为 100。 对于 SQL 数据库，这表示随每个批量插入事务一起发送的记录数上限 - 默认值为 10,000。 
    - 适用于 SQL 数据库和 Azure 函数 
  - STAGING_AREA：Blob 存储的 EXTERNAL DATA SOURCE 对象 - 用于将高吞吐量数据引入到 SQL 数据仓库的临时区域 
    - 适用于 SQL 数据仓库


## <a name="examples"></a>示例

### <a name="example-1---edgehub"></a>示例 1 - EdgeHub

键入：输入或输出<br>
参数：
- 输入或输出
  - Alias 
  - 事件序列化格式 
  - 编码 
- 仅输入： 
  - 事件压缩类型 

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
   EXTERNAL_FILE_FORMAT = myFileFormat, 
   LOCATION = ‘<mytopicname>’, 
   OUTPUT_OPTIONS =   
     ‘REJECT_TYPE: Drop’ 
);
```


### <a name="example-2---azure-sql-database-azure-sql-edge-sql-server"></a>示例 2 - Azure SQL 数据库、Azure SQL Edge、SQL Server

键入：输出<br>
参数：
- 输出别名  
- 数据库（对于 SQL 数据库是必需的） 
- 服务器（对于 SQL 数据库是必需的） 
- 用户名（对于 SQL 数据库是必需的） 
- 密码（对于 SQL 数据库是必需的） 
- 表 
- 将所有输入分区合并为上一个查询步骤或输入的单个写入或继承分区方案（对于 SQL 数据库是必需的） 
- 最大批数 

语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
-- Azure SQL Database 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Server or Azure SQL Edge
CREATE EXTERNAL DATA SOURCE MyTargetSQLTabl 
WITH 
(     
  LOCATION = ' <sqlserver://<ipaddress>,<port>’, 
  CREDENTIAL = SQLCredName 
); 
 
--SQL Database/Edge 
CREATE EXTERNAL STREAM Stream_A 
WITH   
(  
    DATA_SOURCE = MyTargetSQLTable, 
    LOCATION = ‘<DatabaseName>.<SchemaName>.<TableName>’ 
   --Note: If table is container in the database, <TableName> should be sufficient 
   --Note: Do not need external file format in this case 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop 
); 
```

### <a name="example-3---kafka"></a>示例 3 - Kafka

键入：输入<br>
参数：

- Kafka 启动服务器 
- Kafka 主题名称 
- 源分区计数 

语法：

```sql
CREATE EXTERNAL DATA SOURCE MyKafka_tweets 
WITH 
( 
  --The location maps to KafkaBootstrapServer 
  LOCATION = ' kafka://<kafkaserver>:<ipaddress>’, 
  CREDENTIAL = kafkaCredName 
 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyKafka_tweets, 
    LOCATION = ‘<KafkaTopicName>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 5’ 
); 
```

### <a name="example-4---blob-storage"></a>示例 4 - Blob 存储

键入：输入或输出<br>
参数：
- 输入或输出：
  - Alias 
  - 存储帐户 
  - 存储帐户密钥 
  - 容器 
  - 路径模式 
  - 日期格式 
  - 时间格式 
  - 事件序列化格式 
  - 编码 
- 仅输入： 
  - 分区（输入） 
  - 事件压缩类型（输入） 
- 仅输出： 
  - 最小行数（输出） 
  - 最长时间（输出） 
  - 身份验证模式（输出） 

语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = 'AccountKey'; 
 
CREATE DATABASE SCOPED CREDENTIAL StorageAcctCredNameMSI 
WITH IDENTITY = 'Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyBlobStorage_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATE_FORMAT = 'YYYY/MM/DD HH', --Both Date and Time format 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyBlobStorage_tweets, 
    LOCATION = ‘<path_pattern>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘PARTITIONS: 1’, 
  
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: (), 
      MINUMUM_ROWS: 100000, 
      MAXIMUM_TIME: 60’ 
); 
```

### <a name="example-5---event-hub"></a>示例 5 - 事件中心

键入：输入或输出<br>
参数：
- 输入或输出：
  - Alias 
  - 服务总线命名空间 
  - 事件中心名称 
  - 事件中心策略名称 
  - 事件中心策略密钥 
  - 事件序列化格式 
  - 编码 
- 仅输入： 
  - 事件中心使用者组 
  - 事件压缩类型 
- 仅输出： 
  - 分区键列 
  - 属性列 

语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL eventHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyEventHub_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = eventHubCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’, 
    DELIMITER = ‘|’ 
); 
 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyEventHub_tweets, 
    LOCATION = ‘<topicname>’, 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
 
    INPUT_OPTIONS =  
      ‘CONSUMER_GROUP: FirstConsumerGroup’, 
          
    OUTPUT_OPTIONS =  
      ‘REJECT_TYPE: Drop, 
      PARTITION_KEY_COLUMN: , 
      PROPERTY_COLUMNS: ()’ 
);
```

### <a name="example-6---iot-hub"></a>示例 6 - IOT 中心

键入：输入<br>
参数：

- 输入别名 
- IoT 中心 
- 端点 
- 共享访问策略名称 
- 共享访问策略密钥 
- 使用者组 
- 事件序列化格式 
- 编码 
- 事件压缩类型 

语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL IoTHubCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyIoTHub_tweets 
WITH 
(     
  LOCATION = ' iot://iot_hub_name.azure-devices.net’, 
  CREDENTIAL = IoTHubCredName 
);  

CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM Stream_A (user_id VARCHAR, tweet VARCHAR) 
WITH   
(  
    DATA_SOURCE = MyIoTHub_tweets, 
    LOCATION = ‘<name>’, 
    EXTERNAL_FILE_FORMAT = myFileFormat,  
    INPUT_OPTIONS =  
      ‘ENDPOINT: Messaging, 
      CONSUMER_GROUP: ‘FirstConsumerGroup’ 
); 
```

### <a name="example-7---azure-synapse-analytics-formerly-sql-data-warehouse"></a>示例 7 - Azure Synapse Analytics（旧称为 SQL 数据仓库）

键入：输出<br>
参数：
- 输出别名 
- 数据库 
- 服务器 
- 用户名 
- 密码 
- 表 
- 临时区域（适用于副本） 

语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL SQLCredName 
WITH IDENTITY = '<user>’, 
SECRET = '<password>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetSQLTable 
WITH 
(     
  LOCATION = ' <my_server_name>.database.windows.net’, 
  CREDENTIAL = SQLCredName 
); 
 
CREATE EXTERNAL STREAM MySQLTableOutput 
WITH ( 
   DATA_SOURCE = MyTargetSQLTable, 
   LOCATION = ‘<TableName>’ 
   --Note: Do not need external file format in this case 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     STAGING_AREA: staging_area_data_source’, 
); 
```


### <a name="example-8---table-storage"></a>示例 8 - 表存储

键入：输出<br>
参数：
- 输出别名 
- 存储帐户 
- 存储帐户密钥 
- 表名称 
- 分区键 
- 行键 
- 批大小 

语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL TableStorageCredName 
WITH IDENTITY = ‘Storage account Key’, 
SECRET = '<storage_account_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY: <column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


### <a name="example-9---service-bus-topic-same-properties-as-queue"></a>示例 9 - 服务总线主题（与队列的属性相同）

键入：输出<br>
参数：
- 输出别名 
- 服务总线命名空间 
- 主题名称 
- 主题策略名称 
- 主题策略密钥 
- 属性列 
- 系统属性列 
- 事件序列化格式 
- 编码 

语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL serviceBusCredName 
WITH IDENTITY = 'Shared Access Signature’, 
SECRET = '<policyName>'; 
 
CREATE EXTERNAL DATA SOURCE MyServiceBus_tweets 
WITH 
(     
  LOCATION = 'sb://my-sb-namespace.servicebus.windows.net’, 
  CREDENTIAL = serviceBusCredName 
); 
 
CREATE EXTERNAL FILE FORMAT myFileFormat 
WITH ( 
    FORMAT_TYPE = 'CSV', --Event serialization format 
    DATA_COMPRESSION = 'GZIP', 
    ENCODING = ‘UTF-8’ 
); 
 
CREATE EXTERNAL STREAM MyServiceBusOutput 
WITH ( 
   DATA_SOURCE = MyServiceBus_tweets, 
   LOCATION = ‘<topic_name>’, 
   EXTERNAL_FILE_FORMAT = myFileFormat 
       OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop, 
     PARTITION_KEY_COLUMN: , 
     PROPERTY_COLUMNS: (), 
     SYSTEM_PROPERTY_COLUMNS: ()’ 
   --JSON: Format, CSV: Delimiter and Encoding, AVRO: None 
           
); 
```


### <a name="example-10---cosmos-db"></a>示例 10 - Cosmos DB

键入：输出<br>
参数：
- 输出别名 
- 帐户 ID 
- 帐户密钥 
- 数据库 
- 容器名称 
- 文档 ID 


语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL cosmosDBCredName 
WITH IDENTITY = ‘Storage Account Key’, 
SECRET = '<accountKey>'; 
 
CREATE EXTERNAL DATA SOURCE MyCosmosDB_tweets 
WITH 
(     
  LOCATION = ' cosmosdb://accountid.documents.azure.com:443/ database’, 
  CREDENTIAL = cosmosDBCredName 
); 
 
CREATE EXTERNAL STREAM MyCosmosDBOutput 
WITH ( 
   DATA_SOURCE = MyCosmosDB_tweets, 
   LOCATION = ‘<container/documentID>’ 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop', 
     --Note: Do not need external file format in this case 
          
);
```



### <a name="example-11---power-bi"></a>示例 11 - Power BI

键入：输出<br>
参数：
- 输出别名 
- 数据集名称 
- 表名称 


语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL PBIDBCredName 
WITH IDENTITY = ‘Managed Identity’; 
 
CREATE EXTERNAL DATA SOURCE MyPbi_tweets 
WITH 
( 
  LOCATION = 'pbi://dataset/’, 
  CREDENTIAL = PBIDBCredName 
 
); 
 
CREATE EXTERNAL STREAM MyPbiOutput 
WITH ( 
   DATA_SOURCE = MyPbi_tweets, 
   LOCATION = 'tableName', 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: Drop' 
        
);
```

### <a name="example-12---azure-function"></a>示例 12 - Azure 函数

键入：输出<br>
参数：
- 函数应用 
- 函数 
- 密钥 
- 最大批大小 
- 最大批数 

语法：

```sql
CREATE DATABASE SCOPED CREDENTIAL AzureFunctionCredName 
WITH IDENTITY = ‘Function Key’, 
SECRET = '<function_key>'; 
 
CREATE EXTERNAL DATA SOURCE MyTargetTableStorage 
WITH 
(     
  LOCATION = 'abfss://<storage_account>.dfs.core.windows.net’, 
  CREDENTIAL = TableStorageCredName 
); 
 
CREATE EXTERNAL STREAM MyTargetTableStorageOutput 
WITH ( 
   DATA_SOURCE = MyTargetTableStorage, 
   LOCATION = ‘<TableName>’, 
   OUTPUT_OPTIONS =  
     ‘REJECT_TYPE: 'Drop'      
     PARTITION_KEY: ‘<column_partition_key>, 
     ROW_KEY: <column_row_key>, 
     BATCH_SIZE: 100’ 
); 
```


## <a name="see-also"></a>另请参阅

- [ALTER EXTERNAL STREAM (Transact-SQL)](alter-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 

