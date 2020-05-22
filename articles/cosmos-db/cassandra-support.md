---
title: Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能
description: 了解 Azure Cosmos DB Cassandra API 中的 Apache Cassandra 功能支持
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: 978dbf3d8e6a92242c0a984b26bb35cf911a3369
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590413"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能 

Azure Cosmos DB 由 Microsoft 提供，是全球分布的多模型数据库服务。 你可以通过 Cassandra 查询语言 (CQL) v4 [线路协议](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec)兼容的开放源代码 Cassandra 客户端[驱动程序](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)与 Azure Cosmos DB Cassandra API 进行通信。 

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
* blob  
* boolean  
* counter  
* date  
* Decimal  
* double  
* FLOAT  
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

* 标记  
* 聚合函数
  * min, max, avg, count
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
  


## <a name="cassandra-api-limits"></a>Cassandra API 限制

Azure Cosmos DB Cassandra API 对表中存储的数据大小没有任何限制。 在确保遵循分区键限制的同时，可以存储数百 TB 或 PB 的数据。 同样，每个实体或等效行对列数没有任何限制。 但是，实体的总大小不应超过 2 MB。 与所有其他 API 一样，每个分区键的数据都不能超过 20 GB。

## <a name="tools"></a>工具 

Azure Cosmos DB Cassandra API 是一个托管的服务平台。 它不需要任何管理开销或实用程序（如垃圾回收器、Java 虚拟机 (JVM) 和 nodetool）来管理群集。 它支持利用二进制 CQLv4 兼容性的工具（如 cqlsh）。 

* Azure 门户的数据资源管理器、指标、日志诊断、PowerShell 和 CLI 都是其他用来管理帐户的受支持机制。

## <a name="hosted-cql-shell-preview"></a>托管 CQL shell（预览版）

可以直接从 [Azure 门户](data-explorer.md)或 [Azure Cosmos 资源管理器](https://cosmos.azure.com/)中的数据资源管理器中打开托管的本机 Cassandra Shell (CQLSH v5.0.1)。 启用 CQL shell 之前，必须[启用帐户中的笔记本](enable-notebooks.md)功能（如果尚未启用，则在单击 `Open Cassandra Shell` 时会提示你）。 查看[为 Azure Cosmos DB 帐户启用笔记本](enable-notebooks.md)中突出显示的注解，以了解支持的 Azure 区域。

![CQLSH](./media/cassandra-support/cqlsh.png)

还可以使用安装在本地计算机上的 CQLSH 连接到 Azure Cosmos DB 中的 Cassandra API。 它随 Apache Cassandra 3.1.1 一起提供，设置一些环境变量即可直接使用。 以下部分包括使用 CQLSH 在 Windows 或 Linux 上的 Azure Cosmos DB 中安装、配置和连接到 Cassandra API 的说明。

**Windows：**

如果使用 Windows，建议启用[适用于 Linux 的 Windows 文件系统](https://docs.microsoft.com/windows/wsl/install-win10#install-the-windows-subsystem-for-linux)。 然后即可按照以下 linux 命令进行操作。

**Unix/Linux/Mac：**

```bash
# Install default-jre and default-jdk
sudo apt install default-jre
sudo apt-get update
sudo apt install default-jdk

# Import the Baltimore CyberTrust root certificate:
curl https://cacert.omniroot.com/bc2025.crt > bc2025.crt
keytool -importcert -alias bc2025ca -file bc2025.crt

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to Azure Cosmos DB API for Cassandra:
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl

```

## <a name="cql-commands"></a>CQL 命令

Azure Cosmos DB 在 Cassandra API 帐户上支持以下数据库命令。

* CREATE KEYSPACE（忽略此命令的复制设置）
* CREATE TABLE 
* CREATE INDEX（无需指定索引名称，并且还不支持完全冻结索引）
* ALLOW FILTERING
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - 仅支持未记录的命令 
* DELETE

通过兼容 CQL V4 的 SDK 执行的所有 CRUD 操作都将返回有关错误及已使用请求单位的其他信息。 处理 DELETE 和 UPDATE 命令时应考虑资源治理，以确保最有效地使用预配的吞吐量。

* 请注意：如果指定，gc_grace_seconds 值必须为零。

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($"CustomPayload:  {key}: {value}"); 
        } 
```

## <a name="consistency-mapping"></a>一致性映射 

Azure Cosmos DB Cassandra API 为读取操作提供了一致性选择。  一致性映射的信息详见[此文](consistency-levels-across-apis.md#cassandra-mapping)。

## <a name="permission-and-role-management"></a>权限和角色管理

Azure Cosmos DB 支持基于角色的访问控制 (RBAC) 用于预配、旋转密钥、查看指标以及读写和只读密码/密钥（可通过 [Azure 门户](https://portal.azure.com)获取）。 Azure Cosmos DB 不支持 CRUD 活动的角色。

## <a name="keyspace-and-table-options"></a>密钥空间和表选项

目前会忽略“创建密钥空间”命令中针对区域名称、类、replication_factor 和数据中心的选项。 系统使用基础 Azure Cosmos DB 的[全局分发](global-dist-under-the-hood.md)复制方法来添加区域。 如果需要数据跨区域存在，可以使用 PowerShell、CLI 或门户在帐户级别启用它。若要了解详细信息，请参阅[如何添加区域](how-to-manage-database-account.md#addremove-regions-from-your-database-account)一文。 Durable_writes 不能禁用，因为 Azure Cosmos DB 需确保每次写入都是持久的。 在每个区域，Azure Cosmos DB 都会跨副本集（由四个副本组成）来复制数据。该副本集[配置](global-dist-under-the-hood.md)不能修改。
 
在创建表时，会忽略所有选项，但 gc_grace_seconds 除外，后者应设置为零。
密钥空间和表有一个名为“cosmosdb_provisioned_throughput”的额外选项，该选项的最小值为 400 RU/秒。 密钥空间吞吐量允许跨多个表共享吞吐量，这适用于所有表都不利用预配的吞吐量的情况。 “更改表”命令允许跨区域更改预配的吞吐量。 

```
CREATE  KEYSPACE  sampleks WITH REPLICATION = {  'class' : 'SimpleStrategy'}   AND cosmosdb_provisioned_throughput=2000;  

CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=2000; 

ALTER TABLE gks1.t1 WITH cosmosdb_provisioned_throughput=10000 ;

```


## <a name="usage-of-cassandra-retry-connection-policy"></a>使用 Cassandra 重试连接策略

Azure Cosmos DB 是一种资源治理系统。 这意味着，你可以根据操作消耗的请求单位数在给定的秒内执行特定数目的操作。 如果应用程序在给定的秒内超出该限制，则请求会受到速率限制，并会引发异常。 Azure Cosmos DB 中的 Cassandra API 在 Cassandra 本机协议中将这些异常解释为过载错误。 为了确保应用程序在速率受限的情况下能够截获并重试请求，我们提供了 [spark](https://mvnrepository.com/artifact/com.microsoft.azure.cosmosdb/azure-cosmos-cassandra-spark-helper) 和 [Java](https://github.com/Azure/azure-cosmos-cassandra-extensions) 扩展。 当连接到 Azure Cosmos DB 中的 Cassandra API 时，另请参阅 Datastax 驱动程序[版本 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample) 和[版本 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-java-retry-sample-v4) 的 Java 代码示例。 在 Azure Cosmos DB 中，如果使用其他 SDK 来访问 Cassandra API，请创建一项连接策略，以便在出现这些异常时进行重试。

## <a name="next-steps"></a>后续步骤

- 通过使用 Java 应用程序开始[创建 Cassandra API 帐户、数据库和表](create-cassandra-api-account-java.md)
