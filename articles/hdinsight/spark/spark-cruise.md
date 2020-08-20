---
title: 在 Azure HDInsight 上使用 SparkCruise 加速 Apache Spark 查询
description: 了解如何使用 SparkCruise 优化平台提高 Apache Spark 查询的效率。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: d5417bfcfbaa183c34808d9017d5863506429a81
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642276"
---
# <a name="sparkcruise-on-azure-hdinsight"></a>Azure HDInsight 上的 SparkCruise

本文档讨论 Azure HDInsight 功能 *SparkCruise*，该功能可自动重用 Apache Spark 计算以提高查询效率。

## <a name="overview"></a>概述

在分析平台（如 Apache Spark）上运行的查询将分解为包含较小的子查询的查询计划。 这些子查询可能会在多个查询的查询计划中重复显示。 每次发生这些情况时，都将重新执行它们以返回结果。 但是，重新执行相同的查询可能效率低下，并导致不必要的计算成本。

*SparkCruise* 是一种工作负荷优化平台，可重复使用常见计算，从而降低整体查询执行时间和数据传输成本。 平台使用 *具体化视图*的概念，这是一个查询，其结果以预计算形式存储。 然后，在查询本身再次出现时，可以重复使用这些结果，而无需再次重新计算结果。

## <a name="setup-and-installation"></a>设置和安装

SparkCruise 在所有具有 Spark 2.3 或2.4 的 HDInsight 4.0 群集上可用。 SparkCruise 库文件安装在 `/opt/peregrine/` HDInsight 群集上的目录中。 若要正常工作， *SparkCruise* 需要以下配置属性，这些属性默认设置。

* `spark.sql.queryExecutionListeners` 设置为 `com.microsoft.peregrine.spark.listeners.PlanLogListener` ，这将对查询计划启用日志记录。
* `spark.sql.extensions` 设置为 `com.microsoft.peregrine.spark.extensions.SparkExtensionsHdi` ，这将启用联机具体化和重用的优化器规则。

## <a name="computation-reuse-in-spark-sql"></a>Spark SQL 中的计算重用

下面的示例方案演示了如何使用 *SparkCruise* 优化 Apache Spark 查询。 

1. 通过 SSH 连接到 spark 群集的头节点。
1. 键入 `spark-shell`。
1. 运行以下代码片段，这将使用群集上的示例数据运行几个基本查询。

    ```scala
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select count(*) from hivesampletable").collect
    spark.sql("select distinct market from hivesampletable where querytime like '11%'").show
    spark.sql("select distinct state, country from hivesampletable where querytime like '11%'").show
    :quit
    ```
1. 使用 *SparkCruise* 查询分析工具分析上一个查询的查询计划，这些查询存储在 Spark 应用程序日志中。 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh analyze views
    ```

1. 查看分析过程的输出，该输出是一个反馈文件。 此反馈文件包含用于将来的 Spark SQL 查询的注释。 

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

该 `analyze` 命令分析查询计划并创建工作负荷的表格表示形式。 然后，该 `views` 命令标识常见子计划表达式，并选择感兴趣的子计划表达式，以便以后具体化和重用。 输出是包含用于将来的 Spark SQL 查询的批注的反馈文件。 

该 `show` 命令显示类似于以下文本的输出：

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

反馈文件包含采用以下格式的条目： `subplan-identifier [Materialize|Reuse] input/path/to/action` 。 在此示例中，有两个唯一签名：一个表示前两个重复的查询，第二个签名表示最后两个查询中的筛选器谓词。 使用此反馈文件，以下查询会立即自动具体化并重复使用常见子。 

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

1. 再次查看反馈文件，以查看重复使用的查询的签名。

    ```
    sudo /opt/peregrine/analyze/peregrine.sh show
    ```

此 `show` 命令提供类似于以下文本的输出：

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

尽管查询中的文本值已从更改 `'11%'` 为 `'12%'` ，但 *SparkCruise* 仍可将以前的查询与具有略微变体（如文本值和数据集版本的演变）的新查询匹配。 如果对查询进行了重大更改，则可以重新运行分析工具来识别可重复使用的新查询。

在幕后， *SparkCruise* 会触发一个子查询，使其从包含它的第一个查询中具体化所选的子计划。 以后的查询可以直接读取具体化的子，而不是重新计算它们。 在此工作负荷中，第一个和第三个查询会以联机方式对子进行具体化。 在具体化常见的子后，可以看到计划更改了查询。

您可以看到现在可以在后续查询中重复使用四个新的具体化子表达式。

## <a name="clean-up"></a>清理

反馈文件、具体化子和查询日志会在 Spark 会话中持久保存。 若要删除这些文件，请运行以下命令：

```bash
sudo /opt/peregrine/analyze/peregrine.sh clean
```

## <a name="next-steps"></a>后续步骤

* [使用工作负荷见解笔记本确定 SparkCruise 的优点](https://github.com/Azure-Samples/azure-sparkcruise-samples/tree/main/SparkCruise)
* [使用 Azure HDInsight IO 缓存提高 Apache Spark 工作负载的性能](apache-spark-improve-performance-iocache.md)
* [在 HDInsight 中优化 Apache Spark 作业](./apache-spark-perf.md)
* [SparkCruise： Handsfree 计算在 Spark 中重复使用](https://people.cs.umass.edu/~aroy/sparkcruise-vldb19.pdf)
* [Azure HDInsight 上的 Apache Spark 准则](./spark-best-practices.md)
