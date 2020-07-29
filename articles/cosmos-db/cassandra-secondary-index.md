---
title: Azure Cosmos DB Cassandra API 帐户中的索引编制
description: 了解 Azure Cosmos DB Cassandra API 帐户中的辅助索引编制的工作原理。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80758022"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 中的辅助索引编制

Azure Cosmos DB 中的 Cassandra API 利用底层索引基础结构来公开平台中固有的索引编制强度。 但是，与核心 SQL API 不同，Azure Cosmos DB 中的 Cassandra API 默认情况下不会为所有特性编制索引。 它支持使用辅助索引编制来为特定的特性创建索引，其行为方式与 Apache Cassandra 相同。  

一般情况下，不建议对未分区的列执行筛选查询。 必须显式使用 ALLOW FILTERING 语法，这可能会导致某个操作无法正常执行。 在 Azure Cosmos DB 中，可对低基数特征运行此类查询，因为这些查询会在不同的分区之间扇出以检索结果。

不建议为经常更新的列创建索引。 在定义表时创建索引是明智之举。 这可以确保数据和索引处于一致状态。 如果为现有数据创建新索引，目前，无法跟踪表的索引进度变化。 如果需要跟踪此操作的进度，必须通过[支持票证]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)来请求进度变化。


> [!NOTE]
> 以下对象不支持辅助索引：
> - 冻结的集合类型、小数和变体类型等数据类型。
> - 静态列
> - 聚集键

## <a name="indexing-example"></a>索引编制示例

首先，在 CQL shell 提示符下运行以下命令以创建一个示例键空间和表：

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

然后，使用以下命令插入示例用户数据：

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

如果尝试执行以下语句，将会遇到错误，要求你使用 `ALLOW FILTERING`： 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

尽管 Cassandra API 支持上一部分中所述的 ALLOW FILTERING，但不建议使用此语法。 应按以下示例中所示创建索引：

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
对“lastname”字段创建索引后，接下来可以成功运行以前的查询。 使用 Azure Cosmos DB 中的 Cassandra API 时，无需提供索引名称。 将使用格式为 `tablename_columnname_idx` 的默认索引。 例如，` t1_lastname_idx` 是上述表的索引名称。

## <a name="dropping-the-index"></a>删除索引 
要删除某个索引，需要知道其名称。 运行 `desc schema` 命令来获取表的说明。 此命令的输出包含 `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` 格式的索引名称。 然后，可以使用索引名称删除该索引，如以下示例中所示：

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>后续步骤
* 了解 Azure Cosmos DB 中的[自动索引编制](index-overview.md)的工作原理
* [Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能](cassandra-support.md)
