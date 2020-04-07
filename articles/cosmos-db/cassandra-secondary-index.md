---
title: Azure 宇宙 DB Cassandra API 帐户中的索引
description: 了解辅助索引在 Azure Azure Cosmos DB Cassandra API 帐户中的工作原理。
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758022"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Azure 宇宙 DB Cassandra API 中的辅助索引

Azure Cosmos DB 中的 Cassandra API 利用基础索引基础结构来公开平台中固有的索引强度。 但是，与核心 SQL API 不同，Azure Cosmos DB 中的 Cassandra API 默认情况下不会索引所有属性。 相反，它支持辅助索引来创建对某些属性的索引，这些属性的工作方式与 Apache Cassandra 相同。  

通常，不建议对未分区的列执行筛选器查询。 您必须显式使用 ALLOW 筛选语法，这将导致操作可能无法很好地执行。 在 Azure Cosmos DB 中，您可以在低基数属性上运行此类查询，因为它们跨分区扇出以检索结果。

不建议在频繁更新的列上创建索引。 在定义表时，最好创建索引。 这可确保数据和索引处于一致状态。 如果在现有数据上创建新索引，则当前无法跟踪表的索引进度更改。 如果需要跟踪此操作的进度，必须通过[支持票证]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)请求进度更改。


> [!NOTE]
> 以下对象不支持辅助索引：
> - 数据类型，如冻结集合类型、小数次和变体类型。
> - 静态列
> - 群集键

## <a name="indexing-example"></a>索引示例

首先，通过在 CQL shell 提示符上运行以下命令来创建一个示例键空间和表：

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

如果尝试执行以下语句，则将遇到一个错误，要求您使用`ALLOW FILTERING`： 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

尽管卡桑德拉 API 支持 ALLOW 筛选（如上一节所述），但不建议这样做。 您应该在 中创建索引，如以下示例所示：

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
在"姓氏"字段上创建索引后，现在可以成功运行上一个查询。 使用 Azure Cosmos DB 中的 Cassandra API，您不必提供索引名称。 使用具有格式`tablename_columnname_idx`的默认索引。 例如，` t1_lastname_idx`是上一表的索引名称。

## <a name="dropping-the-index"></a>删除索引 
您需要知道索引名称是删除索引的。 运行该`desc schema`命令以获取表的说明。 此命令的输出包括格式`CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`中的索引名称。 然后可以使用索引名称删除索引，如以下示例所示：

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>后续步骤
* 了解[自动索引](index-overview.md)在 Azure Cosmos DB 中的工作原理
* [Azure Cosmos DB Cassandra API 支持的 Apache Cassandra 功能](cassandra-support.md)
