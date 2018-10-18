---
title: Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能和命令
description: 了解 Azure Cosmos DB Cassandra API 中的 Apache Cassandra 功能支持
services: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.component: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: e3de78bdf38a326498b984dc2a9f8eaa42233d22
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091277"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能 

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 你可以通过 Cassandra 查询语言 (CQL) v4 [线路协议](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec)兼容的开放源代码 Cassandra 客户端[驱动程序](http://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)与 Azure Cosmos DB Cassandra API 进行通信。 

通过使用 Azure Cosmos DB Cassandra API，你可以尽享 Apache Cassandra ApI 带来的诸多优势，以及 Azure Cosmos DB 提供的各项企业功能。 企业功能包括[全局分发](distribute-data-globally.md)、[自动横向扩展分区](partition-data.md)、可用性和延迟保证、空闲时加密、备份等。

## <a name="cassandra-protocol"></a>Cassandra 协议 

Azure Cosmos DB Cassandra API 与 CQL 版本 v4 兼容。 下面列出了支持的 CQL 命令、工具、限制和例外。 任何理解这些协议的客户端驱动程序应该都能够连接到 Azure Cosmos DB Cassandra API。

## <a name="cassandra-driver"></a>Cassandra 驱动程序

Azure Cosmos DB Cassandra API 支持以下版本的 Cassandra 驱动程序：

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>CLR 数据类型 

Azure Cosmos DB Cassandra API 支持以下 CQL 数据类型：

* ascii  
* bigint  
* Blob  
* 布尔值  
* counter  
* 日期  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* text  
* time  
* timestamp  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udts  
* map  

## <a name="cql-functions"></a>CQL 函数

Azure Cosmos DB Cassandra API 支持以下 CQL 函数：

* 令牌  
* Blob 转换函数 
  * typeAsBlob(value)  
  * blobAsType(value)
* UUID 和 timeuuid 函数 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date)  


## <a name="cassandra-query-language-limits"></a>Cassandra 查询语言限制

Azure Cosmos DB Cassandra API 对表中存储的数据大小没有任何限制。 在确保遵循分区键限制的同时，可以存储数百 TB 或 PB 的数据。 同样，每个实体或等效行对列数也没有任何限制，但实体的总大小不应超过 2 MB。

## <a name="tools"></a>工具 

Azure Cosmos DB Cassandra API 是一个托管的服务平台。 它不需要任何管理开销或实用程序（如垃圾回收器、Java 虚拟机 (JVM) 和 nodetool）来管理群集。 它支持利用二进制 CQLv4 兼容性的工具（如 cqlsh）。 

* Azure 门户的数据资源管理器、指标、日志诊断、PowerShell 和 cli 是其他用来管理帐户的受支持机制。

## <a name="cql-shell"></a>CQL Shell  

CQLSH 命令行实用程序随 Apache Cassandra 3.1.1 一起提供，开箱即用并默认启用了以下环境变量：

在运行以下命令之前，[将 Baltimore 根证书添加到 cacerts 存储](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store)。 

**Windows**： 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```
**Unix/Linux/Mac：**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> –ssl 
```

## <a name="cql-commands"></a>CQL 命令

Azure Cosmos DB 在 Cassandra API 帐户上支持以下数据库命令。

* CREATE KEYSPACE 
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - 仅支持未记录的命令 
* 删除

通过 CQLV4 兼容的 SDK 执行的所有 crud 操作都会返回有关错误、使用的请求单位、活动 ID 等的额外信息。 删除和更新命令在使用时需考虑资源调控，以避免过度使用预配的资源。 
* 请注意：如果指定，gc_grace_seconds 值必须为零。

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            string value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>一致性映射 

Azure Cosmos DB Cassandra API 为读取操作提供了一致性选择。 无论帐户的一致性如何，所有写入操作都始终会使用写入性能 SLA 进行编写。

## <a name="permission-and-role-management"></a>权限和角色管理

Azure Cosmos DB 支持基于角色的访问控制 (RBAC) 以及读写和只读密码/密钥（可通过 [Azure 门户](https://portal.azure.com 获取）。 Azure Cosmos DB 在数据平面活动中尚不支持用户和角色。 

## <a name="planned-support"></a>计划的支持 
* 将时间戳和 TTL 一起使用  
* 目前忽略 create keypace 命令中的区域名称 - 数据分配在底层 Cosmos DB 平台中实现，并通过门户或 powershell 向帐户公开。 





## <a name="next-steps"></a>后续步骤

- 通过使用 Java 应用程序开始[创建 Cassandra API 帐户、数据库和表](create-cassandra-api-account-java.md)

