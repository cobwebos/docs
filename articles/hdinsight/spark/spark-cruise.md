---
title: 使用 Azure HDInsight 上的 SparkCruise 加速 Apache Spark 查询
description: 了解如何使用 SparkCruise 优化平台来提高 Apache Spark 查询的效率。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 1a73b4707f83d6a23dffc20d95aa7b8a0fa465b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88649051"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>Azure HDInsight 上的 SparkCruise

本文档介绍 Azure HDInsight 的功能 SparkCruise，它自动重用 Apache Spark 计算以提高查询效率。

## <a name="overview"></a>概述

在分析平台（如 Apache Spark）上运行的查询被分解成包含较小子查询的查询计划。 这些子查询可能在多个查询的查询计划中重复出现。 每次出现时，都将重新执行它们以返回结果。 但是，重新执行相同的查询可能效率低下，并会导致不必要的计算开销。

SparkCruise 是一个工作负载优化平台，它可以重用常见的计算，从而减少总体查询执行时间和数据传输成本。 该平台使用具体化视图的概念，这是一个以预计算的形式存储结果的查询。 当该查询稍后再次出现时，可以重用这些结果，而不是全部重新计算结果。

## <a name="setup-and-installation"></a>设置和安装

所有具有 Spark 2.3 或 2.4 的 HDInsight 4.0 群集都提供 SparkCruise。 SparkCruise 库文件安装在 HDInsight 集群的 `/opt/peregrine/` 目录中。 SparkCruise 需要以下配置属性才能正常运行，默认已设置这些属性。

* `spark.sql.queryExecutionListeners` 设置为 `com.microsoft.peregrine.spark.listeners.PlanLogListener`，这会启用查询计划的日志记录。
* `spark.sql.extensions` 设置为 `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi`，这会启用在线具体化和重用的优化程序规则。

## <a name="computation-reuse-in-spark-sql"></a>Spark SQL 中的配置重用

下面的示例场景演示了如何使用 SparkCruise 优化 Apache Spark 查询。 

1. 使用 SSH 连接到 Spark 群集的头节点。
1. 键入 `spark-shell`。
1. 运行以下代码片段，以使用群集上的示例数据运行一些基本查询。

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. 使用 SparkCruise 查询分析工具分析前面的查询的查询计划，这些查询存储在 Spark 应用程序日志中。 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. 查看分析过程的输出，这是一个反馈文件。 此反馈文件包含将来 Spark SQL 查询的注释。 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`analyze` 命令分析查询计划并创建工作负载的表格表示法。 可以使用 [HDInsight SparkCruise 示例](https://github.com/Azure-Samples/azure-sparkcruise-samples)存储库中包含的 WorkloadInsights 笔记本来查询此工作负载表。 然后，`views` 命令会识别常见的子计划表达式，并选择感兴趣的子计划表达式以供将来具体化和重用。 输出是一个反馈文件，其中包含未来 Spark SQL 查询的注释。 

`show` 命令显示的输出类似于以下文本：

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 4 items
-rw-r--r--   1 sshuser sshuser     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 sshuser sshuser     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 sshuser sshuser      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 sshuser sshuser        536 2020-07-24 16:46 /peregrine/views/views.stp
```

反馈文件包含以下格式的条目：`subplan-identifier [Materialize|Reuse] input/path/to/action`。 在本例中，有两个独特的签名：一个代表前两个重复的查询，另一个代表最后两个查询中的筛选器谓词。 有了此反馈文件，下面的查询在再次提交时将自动具体化并重用公共子计划。 

若要测试优化，请执行另一组示例查询。

1. 键入 `spark-shell`。
1. 执行以下代码片段

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct state, country from hivesampletable where querytime like '12%'").show
    spark.sql("select distinct market from hivesampletable where querytime like '12%'").show
    :quit
    ```

1. 再次查看反馈文件以查看已重用的查询的签名。

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

`show` 命令生成的输出类似于以下文本：

```bash
Feedback file -->

1593761760087311271 Materialize /peregrine/views/1593761760087311271
1593761760087311271 Reuse /peregrine/views/1593761760087311271
18446744073621796959 Materialize /peregrine/views/18446744073621796959
18446744073621796959 Reuse /peregrine/views/18446744073621796959
11259615723090744908 Materialize /peregrine/views/11259615723090744908
11259615723090744908 Reuse /peregrine/views/11259615723090744908
9409467400931056980 Materialize /peregrine/views/9409467400931056980
9409467400931056980 Reuse /peregrine/views/9409467400931056980

Materialized subexpressions -->

Found 8 items
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/11259615723090744908
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/1593761760087311271
drwxr-xr-x   - root root          0 2020-07-24 17:22 /peregrine/views/18446744073621796959
drwxr-xr-x   - root root          0 2020-07-24 17:21 /peregrine/views/9409467400931056980
-rw-r--r--   1 root root     113445 2020-07-24 16:46 /peregrine/views/logical_ir.csv
-rw-r--r--   1 root root     169458 2020-07-24 16:46 /peregrine/views/physical_ir.csv
-rw-r--r--   1 root root      25730 2020-07-24 16:46 /peregrine/views/views.csv
-rw-r--r--   1 root root        536 2020-07-24 16:46 /peregrine/views/views.stp

```

尽管查询中的文本值已从 `'11%'` 更改为 `'12%'`，但 SparkCruise 仍可将以前的查询与具有微小变化（如文本值和数据集版本略有变化）的新查询匹配。 如果查询有重大更改，则可以重新运行分析工具来标识可重用的新查询。

在后台，SparkCruise 会触发一个子查询，从包含它的第一个查询具体化所选的子计划。 之后的查询可以直接读取已具体化的子计划，而不是重新计算它们。 在此工作负载中，子计划将通过第一个和第三个查询以在线方式进行具体化。 在公共子计划具体化后，我们可以看到查询的计划更改。

你可以看到现在有四个新的具体化子表达式可在后续查询中重用。

## <a name="clean-up"></a>清理

反馈文件、已具体化的子计划和查询日志在 Spark 会话中保持不变。 若要删除这些文件，请运行以下命令：

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>后续步骤

* [使用 Workload Insights Notebook 确定 SparkCruise 的权益](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [使用 Azure HDInsight IO 缓存提高 Apache Spark 工作负载的性能](apache-spark-improve-performance-iocache.md)
* [在 HDInsight 中优化 Apache Spark 作业](./apache-spark-perf.md)
* [SparkCruise:Spark SQL 中的免提配置重用](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Azure HDInsight 上的 Apache Spark 准则](./spark-best-practices.md)
