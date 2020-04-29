---
title: Azure Cosmos DB Cassandra API 帐户中编制索引
description: 了解 Azure Azure Cosmos DB Cassandra API 帐户中的辅助索引的工作原理。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80758022"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB 中的辅助索引 Cassandra API

Azure Cosmos DB 中的 Cassandra API 利用基础索引基础结构来公开平台所固有的索引强度。 但是，与核心 SQL API 不同，Azure Cosmos DB 中的 Cassandra API 默认情况下不会为所有属性编制索引。 相反，它支持辅助索引为某些属性创建索引，其行为方式与 Apache Cassandra 相同。  

通常，不建议对未分区的列执行筛选查询。 必须显式使用 "允许筛选语法"，这会导致操作可能无法正常执行。 在 Azure Cosmos DB 可以对低基数属性运行此类查询，因为它们会在分区间扇出以检索结果。

不建议对经常更新的列创建索引。 定义表时创建索引是明智之举。 这可确保数据和索引处于一致的状态。 如果对现有数据创建新索引，当前无法跟踪表的索引进度更改。 如果需要跟踪此操作的进度，则必须通过[支持票证]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)请求进度更改。


> [!NOTE]
> 以下对象不支持辅助索引：
> - 数据类型，如冻结的集合类型、decimal 和 variant 类型。
> - 静态列
> - 聚集键

## <a name="indexing-example"></a>索引示例

首先，通过在 CQL shell 提示符下运行以下命令来创建示例密钥空间和表：

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

然后，在下面的命令中插入示例用户数据：

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

如果尝试执行以下语句，将遇到错误，要求你使用`ALLOW FILTERING`： 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

尽管 Cassandra API 支持 "允许筛选" （如前一部分中所述），但不建议这样做。 应改为在中创建索引，如以下示例中所示：

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
在 "lastname" 字段上创建索引后，现在可以成功运行以前的查询。 使用 Azure Cosmos DB 中的 Cassandra API 时，无需提供索引名称。 使用格式`tablename_columnname_idx`的默认索引。 例如， ` t1_lastname_idx`是上一个表的索引名称。

## <a name="dropping-the-index"></a>删除索引 
您需要知道索引名称将删除索引。 运行`desc schema`命令以获取表的说明。 此命令的输出包含格式`CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`的索引名称。 然后，可以使用索引名称删除索引，如以下示例中所示：

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>后续步骤
* 了解 Azure Cosmos DB 中[自动索引](index-overview.md)的工作原理
* [Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能](cassandra-support.md)
