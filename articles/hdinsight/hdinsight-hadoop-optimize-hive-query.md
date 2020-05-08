---
title: 优化 Azure HDInsight 中的 Hive 查询
description: 本文介绍如何优化 HDInsight 中的 Hadoop 的 Apache Hive 查询。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: c81d70577c5e7b852d315bdb91993d15624a7336
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791487"
---
# <a name="optimize-apache-hive-queries-in-azure-hdinsight"></a>优化 Azure HDInsight 中的 Apache Hive 查询

在 Azure HDInsight 中，有多种群集类型和技术可以运行 Apache Hive 查询。 选择适当的群集类型，以根据工作负荷需求优化性能。

例如，选择`ad hoc`"**交互式查询**群集类型" 可优化交互式查询。 选择 Apache **Hadoop** 群集类型可以优化用作批处理的 Hive 查询。 **Spark** 和 **HBase** 群集类型也可以运行 Hive 查询。 有关针对不同 HDInsight 群集类型运行 Hive 查询的详细信息，请参阅[ Azure HDInsight 中的 Apache Hive 和 HiveQL 是什么？](hadoop/hdinsight-use-hive.md)。

默认情况下，Hadoop 群集类型的 HDInsight 群集未针对性能进行优化。 本文介绍可应用于查询的一些最常见 Hive 性能优化方法。

## <a name="scale-out-worker-nodes"></a>向外缩放辅助节点

增加 HDInsight 群集中的辅助角色节点数允许工作使用更多映射器和化简器并行运行。 在 HDInsight 中，可通过两种方式增加扩大的数目：

* 创建群集时，可以使用 Azure 门户、Azure PowerShell 或命令行接口指定辅助角色节点的数目。  有关详细信息，请参阅[创建 HDInsight 群集](hdinsight-hadoop-provision-linux-clusters.md)。 以下屏幕截图显示了 Azure 门户上的工作节点配置：
  
    ![Azure 门户群集大小节点](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-cluster-configuration.png "scaleout_1")

* 创建后，还可以通过编辑工作节点的数目来进一步横向扩展群集，而无需重新创建：

    ![Azure 门户缩放群集大小](./media/hdinsight-hadoop-optimize-hive-query/azure-portal-settings-nodes.png "scaleout_2")

有关缩放 HDInsight 的详细信息，请参阅[缩放 HDInsight 群集](hdinsight-scaling-best-practices.md)

## <a name="use-apache-tez-instead-of-map-reduce"></a>使用 Apache Tez 而不是 Map Reduce

[Apache Tez](https://tez.apache.org/) 是 MapReduce 引擎的替代执行引擎。 基于 Linux 的 HDInsight 群集在默认情况下会启用 Tez。

![HDInsight Apache Tez 概述关系图](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-tez-engine.png)

Tez 速度更快，因为：

* **作为 MapReduce 引擎中的单个作业执行有向无环图 (DAG)** 。 DAG 要求每组映射器后接一组化简器。 此要求会导致为每个 Hive 查询旋转多个 MapReduce 作业。 Tez 没有此类限制，并且可以将复杂的 DAG 作为一个作业处理，从而最大程度地减少作业启动开销。
* **避免不必要的写入**。 多个作业用于处理 MapReduce 引擎中的同一 Hive 查询。 每个 MapReduce 作业的输出将作为中间数据写入 HDFS。 由于 Tez 会将每个 Hive 查询的作业数降到最低，因此可以避免不必要的写入。
* **最大限度地降低启动延迟**。 Tez 可以减少需要启动的映射器数目，同时还能提高优化吞吐量，因此，更有利于最大限度地降低启动延迟。
* **重复使用容器**。 只要可能，Tez 将重复使用容器，以确保降低启动容器的延迟。
* **连续优化技术**。 传统上，优化是在编译阶段完成的。 但是，这可以提供有关输入的详细信息，以便在运行时更好地进行优化。 Tez 使用连续优化技术，从而可以在运行时阶段进一步优化计划。

有关这些概念的详细信息，请参阅 [Apache TEZ](https://tez.apache.org/)。

可以使用以下 set 命令设置查询的前缀，来执行 Tez 支持的任何 Hive 查询：

```hive
set hive.execution.engine=tez;
```

## <a name="hive-partitioning"></a>Hive 分区

I/O 操作是运行 Hive 查询的主要性能瓶颈。 如果可以减少需要读取的数据量，即可改善性能。 默认情况下，Hive 查询扫描整个 Hive 表。 但是，对于只需扫描少量数据的查询（例如，使用筛选进行查询），此行为会产生不必要的开销。 使用 Hive 分区，Hive 查询只需访问 Hive 表中必要的数据量。

Hive 分区的实现方法是将原始数据重新组织成新目录。 每个分区都有自身的文件目录。 分区由用户定义。 下图说明如何根据年  列来分区 Hive 表。 每年都会创建新的目录。

![HDInsight Apache Hive 分区](./media/hdinsight-hadoop-optimize-hive-query/hdinsight-partitioning.png)

一些分区注意事项：

* **不要在分区上分区**-对只包含几个值的列进行分区可能会导致少数几个分区。 例如，按性别分区只会创建两个要创建的分区（男和女），因此，最多可将延迟减到一半。
* **不超过分区**-另一种极端情况是，对具有唯一值的列（例如，userid）创建分区将导致多个分区。 创建过多分区会给群集 namenode 带来很大压力，因为它必须处理大量的目录。
* **避免数据偏斜** - 明智选择分区键，以便所有分区的大小均等。 例如，按“州”列分区可能会导致数据分布出现偏斜。  因为加利福尼亚州的人口几乎是佛蒙特州的 30 倍，分区大小可能会出现偏差，性能可能有极大的差异。

若要创建分区表，请使用 *Partitioned By* 子句：

```sql
CREATE TABLE lineitem_part
      (L_ORDERKEY INT, L_PARTKEY INT, L_SUPPKEY INT,L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT STRING)
PARTITIONED BY(L_SHIPDATE STRING)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
STORED AS TEXTFILE;
```

创建分区表后，可以创建静态分区或动态分区。

* **静态分区**表示已在相应目录中创建了分片数据。 使用静态分区可以根据目录位置手动添加 Hive 分区。 以下代码片段是一个示例。
  
   ```sql
   INSERT OVERWRITE TABLE lineitem_part
   PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   SELECT * FROM lineitem
   WHERE lineitem.L_SHIPDATE = '5/23/1996 12:00:00 AM'

   ALTER TABLE lineitem_part ADD PARTITION (L_SHIPDATE = '5/23/1996 12:00:00 AM')
   LOCATION 'wasb://sampledata@ignitedemo.blob.core.windows.net/partitions/5_23_1996/'
   ```

* **动态分区**表示希望 Hive 自动创建分区。 由于您已经从临时表创建了分区表，因此您只需将数据插入到已分区表中：
  
   ```hive
   SET hive.exec.dynamic.partition = true;
   SET hive.exec.dynamic.partition.mode = nonstrict;
   INSERT INTO TABLE lineitem_part
   PARTITION (L_SHIPDATE)
   SELECT L_ORDERKEY as L_ORDERKEY, L_PARTKEY as L_PARTKEY,
       L_SUPPKEY as L_SUPPKEY, L_LINENUMBER as L_LINENUMBER,
       L_QUANTITY as L_QUANTITY, L_EXTENDEDPRICE as L_EXTENDEDPRICE,
       L_DISCOUNT as L_DISCOUNT, L_TAX as L_TAX, L_RETURNFLAG as L_RETURNFLAG,
       L_LINESTATUS as L_LINESTATUS, L_SHIPDATE as L_SHIPDATE_PS,
       L_COMMITDATE as L_COMMITDATE, L_RECEIPTDATE as L_RECEIPTDATE,
       L_SHIPINSTRUCT as L_SHIPINSTRUCT, L_SHIPMODE as L_SHIPMODE,
       L_COMMENT as L_COMMENT, L_SHIPDATE as L_SHIPDATE FROM lineitem;
   ```

有关详细信息，请参阅[分区表](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-PartitionedTables)。

## <a name="use-the-orcfile-format"></a>使用 ORCFile 格式

Hive 支持不同的文件格式。 例如：

* **文本**：默认的文件格式，适用于大多数情况。
* **Avro**：非常适合互操作性方案。
* **ORC/Parquet**：最适合用于提高性能。

ORC（优化行纵栏式）格式是存储 Hive 数据的高效方式。 与其他格式相比，ORC 具有以下优点：

* 支持复杂类型（包括 DateTime）和复杂的半结构化类型。
* 高达 70% 的压缩率。
* 每 10,000 行编制一次索引并允许跳过行。
* 大幅减少运行时执行时间。

若要启用 ORC 格式，请先使用 *Stored as ORC* 子句创建一个表：

```sql
CREATE TABLE lineitem_orc_part
      (L_ORDERKEY INT, L_PARTKEY INT,L_SUPPKEY INT, L_LINENUMBER INT,
      L_QUANTITY DOUBLE, L_EXTENDEDPRICE DOUBLE, L_DISCOUNT DOUBLE,
      L_TAX DOUBLE, L_RETURNFLAG STRING, L_LINESTATUS STRING,
      L_SHIPDATE_PS STRING, L_COMMITDATE STRING, L_RECEIPTDATE STRING,
      L_SHIPINSTRUCT STRING, L_SHIPMODE STRING, L_COMMENT      STRING)
PARTITIONED BY(L_SHIPDATE STRING)
STORED AS ORC;
```

接下来，从暂存表向 ORC 表插入数据。 例如：

```sql
INSERT INTO TABLE lineitem_orc
SELECT L_ORDERKEY as L_ORDERKEY,
         L_PARTKEY as L_PARTKEY ,
         L_SUPPKEY as L_SUPPKEY,
         L_LINENUMBER as L_LINENUMBER,
         L_QUANTITY as L_QUANTITY,
         L_EXTENDEDPRICE as L_EXTENDEDPRICE,
         L_DISCOUNT as L_DISCOUNT,
         L_TAX as L_TAX,
         L_RETURNFLAG as L_RETURNFLAG,
         L_LINESTATUS as L_LINESTATUS,
         L_SHIPDATE as L_SHIPDATE,
         L_COMMITDATE as L_COMMITDATE,
         L_RECEIPTDATE as L_RECEIPTDATE,
         L_SHIPINSTRUCT as L_SHIPINSTRUCT,
         L_SHIPMODE as L_SHIPMODE,
         L_COMMENT as L_COMMENT
FROM lineitem;
```

可在 [Apache Hive 语言手册](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC)中阅读有关 ORC 格式的详细信息。

## <a name="vectorization"></a>向量化

向量化可让 Hive 以批的形式同时处理 1024 行，而不是一次处理一行。 这意味着，简单的操作可以更快地完成，因为需要运行的内部代码更少。

若要启用向量化，请在 Hive 查询的前面加上以下设置作为前缀：

```hive
set hive.vectorized.execution.enabled = true;
```

有关详细信息，请参阅[向量化查询执行](https://cwiki.apache.org/confluence/display/Hive/Vectorized+Query+Execution)。

## <a name="other-optimization-methods"></a>其他优化方法

还可以考虑使用其他一些高级优化方法，例如：

* **Hive 存储桶：** 将大型数据集群集化或分段以优化查询性能的技术。
* **联接优化**：Hive 的查询执行计划优化，可改善联接的效率并减少用户提示的需要。 有关详细信息，请参阅[联接优化](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+JoinOptimization#LanguageManualJoinOptimization-JoinOptimization)。
* **增加化简器**。

## <a name="next-steps"></a>后续步骤

在本文中，已学习了几种常见的 Hive 查询优化方法。 若要了解详细信息，请参阅以下文章：

* [使用 HDInsight 中的 Apache Hive](hadoop/hdinsight-use-hive.md)
* [优化 Apache Hive](./optimize-hive-ambari.md)
* [使用 HDInsight 中的交互式查询分析航班延误数据](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [使用 HDInsight 中的 Apache Hive 分析 Twitter 数据](hdinsight-analyze-twitter-data-linux.md)
