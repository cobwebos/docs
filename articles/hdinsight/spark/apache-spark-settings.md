---
title: 配置 Spark 设置 - Azure HDInsight | Microsoft Docs
description: 如何为 HDInsight 群集配置 Spark。
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 01/26/2018
ms.author: maxluk
ms.openlocfilehash: 2ee496eae0767de22d070a0c5689692f0200515b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="configure-spark-settings"></a>配置 Spark 设置

HDInsight Spark 群集包含 Apache Spark 库的安装。  每个 HDInsight 群集包含其所有已安装服务（包括 Spark）的默认配置参数。  管理 HDInsight Hadoop 群集的一个重要方面是监视工作负荷（包括 Spark 作业），确保作业以可预测的方式运行。 若要最合理地运行 Spark 作业，请在确定如何优化群集的逻辑配置时考虑物理群集配置。

默认的 HDInsight Apache Spark 群集包括以下节点：三个 ZooKeeper 节点、两个头节点和一个或多个工作节点：

![Spark HDInsight 体系结构](./media/apache-spark-settings/spark-hdinsight-arch.png)

HDInsight 群集中节点的 VM 数目和 VM 大小也可能影响 Spark 配置。 非默认的 HDInsight 配置值通常需要非默认的 Spark 配置值。 在创建 HDInsight Spark 群集时，系统会显示每个组件的建议 VM 大小。 目前，Azure 的[内存优化 Linux VM 大小](../../virtual-machines/linux/sizes-memory.md)为 D12 v2 或更大。

## <a name="spark-versions"></a>Spark 版本

使用适合你的群集的最佳 Spark 版本。  HDInsight 服务本身包含 Spark 和 HDInsight 的多个版本。  每个 Spark 版本包含一组默认群集设置。  

创建新群集时，可从以下最新 Spark 版本中做出选择：

![Spark 版本](./media/apache-spark-settings/spark-version.png)

Spark 2.x 的运行性能比 Spark 1.x 要好得多。 Spark 2.x 包含大量的性能优化功能，例如 Tungsten、Catalyst Query Optimization，等等。  

> [!NOTE]
> HDInsight 服务中的默认 Apache Spark 版本可随时更改，恕不另行通知。 如果你依赖某个版本，Microsoft 建议在使用 .NET SDK/Azure PowerShell 和 Azure CLI 创建群集时指定该特定版本。

Apache Spark 有三个系统配置位置：

* Spark 属性控制大多数应用程序参数，可以使用 `SparkConf` 对象或通过 Java 系统属性进行设置。
* 可以通过每个节点上的 `conf/spark-env.sh` 脚本，使用环境变量来配置每台计算机的设置，例如 IP 地址。
* 可以通过 `log4j.properties` 配置日志记录。

选择特定的 Spark 版本时，群集将包含默认的配置设置。  可以通过使用自定义的 Spark 配置文件来更改默认的 Spark 配置值。  下面显示了一个示例。

```
    spark.hadoop.io.compression.codecs org.apache.hadoop.io.compress.GzipCodec
    spark.hadoop.mapreduce.input.fileinputformat.split.minsize 1099511627776
    spark.hadoop.parquet.block.size 1099511627776
    spark.sql.files.maxPartitionBytes 1099511627776
    spark.sql.files.openCostInBytes 1099511627776
```

上面所示的示例替代了五个 Spark 配置参数的多个默认值。  这些值是压缩编解码器、Hadoop MapReduce 拆分最小大小和 parquet 块大小，以及 Spar SQL 分区和打开的文件大小默认值。  之所以选择这些配置更改，是因为关联的数据和作业（在此示例中为基因组数据）具有特定的特征，使用这些自定义配置设置可以更好地完成这些作业。

---

## <a name="view-cluster-configuration-settings"></a>查看群集配置设置

在群集上执行性能优化之前，请验证当前的 HDInsight 群集配置设置。 单击 Spark 群集窗格中的“仪表板”链接，从 Azure 门户启动 HDInsight 仪表板。 使用群集管理员的用户名和密码登录。

此时会显示 Ambari Web UI，其中的仪表板视图显示了重要的群集资源利用率指标。  Ambari 仪表板显示 Apache Spark 配置，以及安装的其他服务。 仪表板包含“配置历史记录”选项卡，可在其中查看所有已安装服务（包括 Spark）的配置信息。

若要查看 Apache Spark 的配置值，请依次选择“配置历史记录”、“Spark2”。  选择“配置”选项卡，然后在服务列表中选择 `Spark` 或 `Spark2`（取决于版本）链接。  此时会显示群集的配置值列表：

![Spark 配置](./media/apache-spark-settings/spark-config.png)

若要查看和更改单个 Spark 配置值，请在链接标题中选择包含单词“spark”的任何链接。  Spark 配置包括以下类别的自定义配置值和高级配置值：

* 自定义 Spark2-defaults
* 自定义 Spark2-metrics-properties
* 高级 Spark2-defaults
* 高级 Spark2-env
* 高级 spark2-hive-site-override

如果创建了一组非默认的配置值，则还可以看到配置更新历史记录。  借助此配置历史记录可以确定哪个非默认配置具有最佳性能。

> [!NOTE]
> 若要查看但不更改常用的 Spark 群集配置设置，请在顶层“Spark 作业 UI”界面上选择“环境”选项卡。

## <a name="configuring-spark-executors"></a>配置 Spark 执行器

下图显示了关键的 Spark 对象：驱动程序及其关联的 Spark 上下文，以及群集管理器及其 *n* 个工作节点。  每个工作节点包括执行器、缓存和 *n* 个任务实例。

![群集对象](./media/apache-spark-settings/spark-arch.png)

Spark 作业使用辅助角色资源（具体而言是内存），因此，我们往往会调整工作节点执行器的 Spark 配置值。

我们经常调整 `spark.executor.instances`、`spark.executor.cores` 和 `spark.executor.memory` 这三个关键参数来优化 Spark 配置，以改善应用程序要求。 执行器是针对 Spark 应用程序启动的进程。 执行器在工作节点上运行，负责执行应用程序的任务。 对于每个群集而言，执行器的默认数目和执行器大小是根据工作节点数目和工作节点大小计算的。 这些值存储在群集头节点上的 `spark-defaults.conf` 中。  可以通过在 Ambari Web UI 中选择“自定义 spark-defaults”链接，在运行的群集中编辑这些值。  做出更改后，UI 会提示**重启**所有受影响的服务。

> [!NOTE]
> 这三个配置参数可在群集级别配置（适用于群集中运行的所有应用程序），也可以针对每个应用程序指定。

Spark 应用程序 UI 是有关 Spark 执行器使用的资源的另一个信息源。  在 Spark UI 中，选择“执行器”选项卡可以显示执行器使用的配置和资源的“摘要”与“详细信息”视图。  借助这些视图可以确定是要更改整个群集的 Spark 执行器的默认值，还是更改一组特定的作业执行。

![Spark 执行器](./media/apache-spark-settings/spark-executors.png)

或者，可以使用 Ambari REST API 以编程方式验证 HDInsight 和 Spark 群集的配置设置。  [GitHub 上的 Ambari API 参考](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)提供了详细信息。

根据 Spark 群集工作负荷，可以确定某个非默认 Spark 配置是否提供更优化的 Spark 作业执行。  应该使用示例工作负荷执行基准测试，以验证任何非默认群集配置。  下面是可以考虑调整的一些常见参数：

* `--num-executors` 设置执行器数量。
* `--executor-cores` 设置每个执行程序的内核数。 我们建议使用中等大小的执行器，因为其他进程也会占用一部分可用内存。
* `--executor-memory` 控制 YARN 上每个执行器的内存大小（堆大小）。需要为执行开销留出一些内存。

下面是使用不同配置值的两个工作节点的示例：

![双节点配置](./media/apache-spark-settings/executor-config.png)

以下列表显示关键的 Spark 执行器内存参数。

* `spark.executor.memory` 定义执行器可用的内存总量。
* `spark.storage.memoryFraction`（默认为大约 60%）定义可用于存储持久性 RDD 的内存量。
* `spark.shuffle.memoryFraction`（默认为大约 20%）定义保留给随机操作的内存量。
* `spark.storage.unrollFraction` 和 `spark.storage.safetyFraction`（合计为总内存的大约 30%）- 这些值由 Spark 在内部使用，不应更改。

YARN 控制每个 Spark 节点上的容器使用的最大内存量总计。 下图显示了 YARN 配置对象与 Spark 对象之间的节点关系。

![YARN Spark 内存管理](./media/apache-spark-settings/yarn-spark-memory.png)

## <a name="change-parameters-for-an-application-running-in-jupyter-notebook"></a>更改 Jupyter Notebook 中运行的应用程序的参数

HDInsight 中的 Spark 群集默认包含许多组件。 其中每个组件包含可按需替代的默认配置值。

* Spark Core - Spark Core、Spark SQL、Spark 流式处理 API、GraphX 和 MLlib
* Anaconda - python 包管理器
* Livy - Apache Spark REST API，用于将远程作业提交到 HDInsight Spark 群集
* Jupyter 和 Zeppelin Notebook - 用来与 Spark 群集交互的基于浏览器的交互式 UI
* ODBC 驱动程序 - 将 HDInsight 中的 Spark 群集连接到 Microsoft Power BI 和 Tableau 等商业智能 (BI) 工具

对于 Jupyter Notebook 中运行的应用程序，可以使用 `%%configure` 命令从 Notebook 本身内部进行配置更改。 这些配置更改将应用到从 Notebook 实例运行的 Spark 作业。 应该先在应用程序的开头进行此类更改，然后再运行第一个代码单元。 创建 Livy 会话时，会将更改的配置应用到该会话。

> [!NOTE]
> 若要更改处于应用程序中后面某个阶段的配置，请使用 `-f` (force) 参数。 但是，应用程序中的所有进度将会丢失。

以下代码演示如何更改 Jupyter Notebook 中运行的应用程序的配置。

```
    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}
```

## <a name="conclusion"></a>结束语

为了确保 Spark 作业以可预测、高性能的方式运行，需要监视和调整大量的核心配置设置。 这些设置可帮助确定特定工作负荷的最佳 Spark 群集配置。  此外，还需要监视长时间运行的和/或消耗大量资源的 Spark 作业执行。  最常见的难题不当的配置（尤其是大小不当的执行器）、长时间运行的操作以及导致笛卡尔操作的任务所造成的内存压力。

## <a name="next-steps"></a>后续步骤

* [HDInsight 提供哪些 Hadoop 组件和版本？](../hdinsight-component-versioning.md)
* [管理 HDInsight 上 Spark 群集的资源](apache-spark-resource-manager.md)
* [使用 Hadoop、Spark、Kafka 等在 HDInsight 中设置群集](../hdinsight-hadoop-provision-linux-clusters.md)
* [Apache Spark 配置](https://spark.apache.org/docs/latest/configuration.html)
* [在 YARN 上运行 Spark](https://spark.apache.org/docs/latest/running-on-yarn.html)
