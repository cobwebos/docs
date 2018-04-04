---
title: Azure HDInsight 中的 Phoenix 性能 | Microsoft Docs
description: 有关优化 Phoenix 性能的最佳做法。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: ashishth
ms.openlocfilehash: 42b95d6b67f3449a2de2619f0a25b3b8f798950d
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="phoenix-performance-best-practices"></a>Phoenix 性能最佳实践

Phoenix 性能的最重要方面是优化基础 HBase。 Phoenix 在 HBase 的顶层创建一个关系数据模型，用于将 SQL 查询转换为 HBase 操作，例如扫描。 表架构的设计、主键中字段的选择和排序，以及索引的用法都会影响 Phoenix 的性能。

## <a name="table-schema-design"></a>表架构设计

在 Phoenix 中创建一个表时，该表将存储在 HBase 表中。 HBase 表包含可统一访问的列组（列系列）。 Phoenix 表中的行是 HBase 表中的行，其中每个行由版本受控的单元格构成，这些单元格与一个或多个列相关联。 在逻辑上，单个 HBase 行是键值对的集合，这些键值对具有相同的行键值。 这就是说，每个键值对具有一个行键属性，特定行的该行键属性值是相同的。

Phoenix 表的架构设计包括主键设计、列系列设计、单个列的设计，以及数据分区方式。

### <a name="primary-key-design"></a>主键设计

在 Phoenix 中的表上定义的主键确定如何将数据存储在基础 HBase 表的行键中。 在 HBase 中，访问特定行的唯一方法就是使用行键。 此外，存储在 HBase 表中的数据已按行键排序。 Phoenix 根据主键中定义的列顺序串联行中的每列，以此生成行键值。

例如，联系人表包含名字、姓氏、电话号码和地址，所有这些数据都包含在同一个列系列中。 可以基于不断递增的序列号定义主键：

|rowkey|       地址|   phone| firstName| lastName|
|------|--------------------|--------------|-------------|--------------|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole|
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji|

但是，如果经常按 lastName 执行查询，则使用此主键可能性能不佳，因为每个查询需要扫描整个表才能读取每个 lastName 的值。 此时，可以基于 lastName、firstName 和社会安全号码列定义主键。 最后一列用于区分位于同一地址、使用相同姓名的两位居民（例如父亲和儿子）。

|rowkey|       地址|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  1000|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  8396|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

Phoenix 使用此新主键生成的行键是：

|rowkey|       地址|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

在上面的第一行中，行键的数据按如下方式表示：

|rowkey|       key|   值| 
|------|--------------------|---|
|  Dole-John-111|地址 |1111 San Gabriel Dr.|  
|  Dole-John-111|phone |1-425-000-0002|  
|  Dole-John-111|firstName |John|  
|  Dole-John-111|lastName |Dole|  
|  Dole-John-111|socialSecurityNum |111| 

现在，此行键存储了数据的重复副本。 请考虑要包含在主键中的列大小和数目，因为此值将与基础 HBase 表中的每个单元格包含在一起。

此外，如果主键包含单调递增的值，则应使用盐桶创建表，以帮助避免产生写入热点 - 请参阅[将分区数据](#partition-data)。

### <a name="column-family-design"></a>列系列设计

如果某些列的访问频率比其他列更高，应创建多个列系列，将经常访问的列与极少访问列区分开来。

此外，如果某些列往往是一起访问的，可将这些列放在同一个列系列中。

### <a name="column-design"></a>列设计

* 由于大型列的 I/O 开销较大，请将 VARCHAR 列保持为大约 1 MB 以下。 处理查询时，HBase 会将单元格作为一个整体具体化，然后将其发送到客户端。客户端会作为一个整体接收这些单元格，然后将其转交到应用程序代码。
* 使用 protobuf、Avro、msgpack 或 BSON 等紧凑格式存储列值。 不建议使用 JSON，因为它比较庞大。
* 在存储之前考虑压缩数据，以降低延迟和 I/O 开销。

### <a name="partition-data"></a>将数据分区

使用 Phoenix 可以控制数据分发到的区域数目，从而大幅提高读/写性能。 创建 Phoenix 表时，可以将数据加盐或预先拆分。

若要在创建过程中给表加盐，请指定盐桶数目：

    CREATE TABLE CONTACTS (...) SALT_BUCKETS = 16

此加盐过程将连同主键值一起拆分表，并自动选择值。 

若要控制表的拆分位置，可以通过提供拆分所要遵循的范围值，来预先拆分表。 例如，若要创建一个沿着三个区域拆分的表：

    CREATE TABLE CONTACTS (...) SPLIT ON ('CS','EU','NA')

## <a name="index-design"></a>索引设计

Phoenix 索引是一个 HBase 表，存储索引表中的部分或全部数据的副本。 索引可以提高特定类型的查询的性能。

如果定义多个索引后查询表，Phoenix 会自动选择查询的最佳索引。 主索引是根据所选的主键自动创建的。

对于预见性查询，还可以通过指定查询的列来创建辅助索引。

设计索引时：

* 只创建所需的索引。
* 限制频繁更新表的索引数。 对的表更新将解释为同时写入主表和索引表。

## <a name="create-secondary-indexes"></a>创建辅助索引

辅助索引将完整表扫描转化为点查找，因此可以提高读取性能，代价是消耗更多的存储空间和降低写入速度。 可以在创建表之后添加或删除辅助索引，它们不需要对现有查询进行更改 – 查询速度只会更快。 请考虑根据需要创建涵盖索引和/或功能索引。

### <a name="use-covered-indexes"></a>使用涵盖索引

涵盖索引是包含行中的数据以及已编制索引的值的索引。 找到所需的索引条目后，不需要访问主表。

例如，在示例联系人表中，可以只是基于 socialSecurityNum 列创建辅助索引。 此辅助索引可以加速按 socialSecurityNum 值执行筛选的查询，但检索其他字段值需要针对主表执行另一次读取。

|rowkey|       地址|   phone| firstName| lastName| socialSecurityNum |
|------|--------------------|--------------|-------------|--------------| ---|
|  Dole-John-111|1111 San Gabriel Dr.|1-425-000-0002|    John|Dole| 111 |
|  Raji-Calvin-222|5415 San Gabriel Dr.|1-230-555-0191|  Calvin|Raji| 222 |

但是，在指定 socialSecurityNum 的情况下，如果你往往还要查找 firstName 和 lastName，则可以创建一个涵盖索引，并在其中包含 firstName 和 lastName 作为索引表中的实际数据：

    CREATE INDEX ssn_idx ON CONTACTS (socialSecurityNum) INCLUDE(firstName, lastName);

通过此涵盖索引，以下查询只需从包含辅助索引的表中读取数据，即可获取所有数据：

    SELECT socialSecurityNum, firstName, lastName FROM CONTACTS WHERE socialSecurityNum > 100;

### <a name="use-functional-indexes"></a>使用功能索引

使用功能索引可以基于预期要在查询中使用的任意表达式创建索引。 创建功能索引以及使用该表达式的查询后，该索引可用于检索结果而不是数据表。

例如，可以创建一个索引，以便根据某人的名字和姓氏组合来执行不区分大小写的搜索：

     CREATE INDEX FULLNAME_UPPER_IDX ON "Contacts" (UPPER("firstName"||' '||"lastName"));

## <a name="query-design"></a>查询设计

查询设计的主要考虑因素是：

* 了解查询计划并验证其预期行为。
* 有效联接。

### <a name="understand-the-query-plan"></a>了解查询计划

在 [SQLLine](http://sqlline.sourceforge.net/) 中，依次使用 EXPLAIN 和 SQL 查询来查看 Phoenix 将要执行的操作计划。 检查该计划：

* 是否在适当的情况下使用主键。
* 是否使用适当的辅助索引而不是数据表。
* 是否尽量 RANGE SCAN 或 SKIP SCAN，而不是 TABLE SCAN。

#### <a name="plan-examples"></a>计划示例

举个例子，假设有一个名为 FLIGHTS 的表，其中存储了航班延迟信息。

若要选择 airlineid 为 `19805` 的所有航班（其中的 airlineid 是不在主键或任何索引中的字段）：

    select * from "FLIGHTS" where airlineid = '19805';

按如下所示运行 explain 命令：

    explain select * from "FLIGHTS" where airlineid = '19805';

查询计划如下所示：

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN FULL SCAN OVER FLIGHTS
        SERVER FILTER BY AIRLINEID = '19805'

在此计划中，请注意短语 FULL SCAN OVER FLIGHTS。 此短语表示针对表中的所有行执行了 TABLE SCAN，而没有使用更高效的 RANGE SCAN 或 SKIP SCAN。

现在，假设你要查询 carrier（航空公司）`AA` 在 2014 年 1 月 2 日 flightnum（航班号）大于 1 的航班。 假设 year、month、dayofmonth、carrier 和 flightnum 列在示例表中存在，并且都包含在复合主键中。 查询如下所示：

    select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

使用以下代码检查此查询的计划：

    explain select * from "FLIGHTS" where year = 2014 and month = 1 and dayofmonth = 2 and carrier = 'AA' and flightnum > 1;

生成的计划为：

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER FLIGHTS [2014,1,2,'AA',2] - [2014,1,2,'AA',*]

方括号中的值是主键的值范围。 在本例中，范围值是固定的，即年份 2014、月份 1 和月份日期 2，但允许航班号 2 和更大的值 (`*`)。 此查询计划确认已按预期使用主键。

接下来，基于名为 `carrier2_idx` 的 FLIGHTS 表创建一个索引，该索引只出现在 carrier 字段中。 此索引还包含 flightdate、tailnum、origin 和 flightnum 作为涵盖列，这些列的数据也存储在索引中。

    CREATE INDEX carrier2_idx ON FLIGHTS (carrier) INCLUDE(FLIGHTDATE,TAILNUM,ORIGIN,FLIGHTNUM);

假设你要获取航空公司以及航日期和机尾编号，如以下查询所示：

    explain select carrier,flightdate,tailnum from "FLIGHTS" where carrier = 'AA';

应会看到使用了此索引：

    CLIENT 1-CHUNK PARALLEL 1-WAY ROUND ROBIN RANGE SCAN OVER CARRIER2_IDX ['AA']

有关 explain 计划结果中可能显示的完整项列表，请参阅 [Apache Phoenix 优化指南](https://phoenix.apache.org/tuning_guide.html)中的“Explain 计划”部分。

### <a name="join-efficiently"></a>有效联接

一般而言，除非一侧较小，否则应避免联接，尤其是针对频繁查询。

如果需要，可以结合 `/*+ USE_SORT_MERGE_JOIN */` 提示执行大型联接，但是，针对大量的行执行大型联接会产生很高的开销。 如果所有右侧表的总体大小超过了可用内存，请使用 `/*+ NO_STAR_JOIN */` 提示。

## <a name="scenarios"></a>方案

以下指导原则描述了一些常用模式。

### <a name="read-heavy-workloads"></a>读取密集型工作负荷

对于读取密集型的用例，请确保使用索引。 此外，为了节省读取时间开销，请考虑创建涵盖索引。

### <a name="write-heavy-workloads"></a>写入密集型工作负荷

对于包含单调递增主键的写入密集型工作负荷，请创建盐桶来帮助避免产生写入热点，代价是由于需要执行更多扫描，因此总体读取吞吐量会降低。 此外，在使用 UPSERT 写入大量记录时，请关闭 autoCommit 并批处理记录。

### <a name="bulk-deletes"></a>批量删除

删除大型数据集时，请先打开 autoCommit，然后再发出 DELETE 查询，使客户端不需要记住所有已删除行的行键。 AutoCommit 会阻止客户端缓冲受 DELETE 影响的行，因此，Phoenix 可以直接在区域服务器上删除这些行，且无需将其返回到客户端。

### <a name="immutable-and-append-only"></a>不可变和仅限追加

如果方案更看重写入速度而不是数据完整性，请考虑在创建表时禁用预写日志：

    CREATE TABLE CONTACTS (...) DISABLE_WAL=true;

有关此选项和其他选项的详细信息，请参阅 [Phoenix 语法](http://phoenix.apache.org/language/index.html#options)。

## <a name="next-steps"></a>后续步骤

* [Phoenix 优化指南](https://phoenix.apache.org/tuning_guide.html)
* [辅助索引](http://phoenix.apache.org/secondary_indexing.html)