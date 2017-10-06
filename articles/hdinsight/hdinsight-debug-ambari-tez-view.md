---
title: "将 Ambari Tez 视图与 HDInsight 配合使用 — Azure | Microsoft Docs"
description: "了解如何使用 Ambari Tez 视图来调试 HDInsight 上的 Tez 作业。"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 9c39ea56-670b-4699-aba0-0f64c261e411
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: 818f7756189ed4ceefdac9114a0b89ef9ee8fb7a
ms.openlocfilehash: 65d89309b9eea8544b85d16687baa90d49688d77
ms.contentlocale: zh-cn
ms.lasthandoff: 07/14/2017

---
# <a name="use-ambari-views-to-debug-tez-jobs-on-hdinsight"></a>使用 Ambari 视图来调试 HDInsight 上的 Tez 作业

适用于 HDInsight 的 Ambari Web UI 所含的 Tez 视图可用于了解和调试使用 Tez 的作业。 利用 Tez 视图，可以将作业显示为包含已连接项目的图形，深入了解每个项目并检索统计信息和日志记录信息。

> [!IMPORTANT]
> 本文档中的步骤需要使用 Linux 的 HDInsight 群集。 Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 组件版本控制](hdinsight-component-versioning.md#hdinsight-windows-retirement)。

## <a name="prerequisites"></a>先决条件

* 基于 Linux 的 HDInsight 群集。 有关创建群集的步骤，请参阅[开始使用基于 Linux 的 HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)。
* 支持 HTML5 的现代 Web 浏览器。

## <a name="understanding-tez"></a>了解 Tez

Tez 是 Hadoop 中的一种可扩展数据处理框架，其处理速度比传统的 MapReduce 处理要快。 对于基于 Linux 的 HDInsight 群集来说，它是 Hive 的默认引擎。

Tez 会创建一个有向无环图 (DAG)，用于描述作业所需的操作顺序。 单独的操作称为顶点，每个顶点执行完整作业的一部分。 实际执行顶点所描述的工作称为完成任务，任务可以分布在群集的多个节点中。

### <a name="understanding-the-tez-view"></a>了解 Tez 视图

Tez 视图提供进程的历史信息，还提供正在运行的进程的相关信息。 此信息显示作业在各群集上的分发情况。 还显示了任务和顶点所用的计数器，以及与作业有关的错误信息。 它可以为以下情况提供有用的信息：

* 监视长时间运行的进程、查看映射的进度以及精简任务。
* 分析成功进程或失败进程的历史数据，了解处理过程的改进方式或其失败的原因。

## <a name="generate-a-dag"></a>生成 DAG

如果使用 Tez 引擎的作业当前正在运行或之前运行过，则 Tez 视图仅包含数据。 无需使用 Tez 即可解析简单的 Hive 查询。 要进行筛选、分组、排序、联接等操作的更复杂查询。 使用 Tez 引擎。

使用以下步骤，来运行使用 Tez 的 Hive 查询：

1. 在 Web 浏览器中导航到 https://CLUSTERNAME.azurehdinsight.net，其中“CLUSTERNAME”是 HDInsight 群集的名称。

2. 从页面顶部的菜单中选择“视图”图标。 该图标看起来像一系列方块。 在显示的下拉列表中，选择“Hive 视图”。

    ![选择 Hive 视图](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. 在 Hive 视图加载后，将以下查询粘贴到查询编辑器中，并单击“执行”。

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

    完成该作业后，将看到输出显示在“查询处理结果”部分。 结果应类似以下文本：

        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica

4. 选择“日志”选项卡。显示的信息类似以下文本：

        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    保存“App id”值，因为下一部分会用到该值。

## <a name="use-the-tez-view"></a>使用 Tez 视图

1. 从页面顶部的菜单中选择“视图”图标。 在显示的下拉列表中，选择“Tez 视图”。

    ![选择 Tez 视图](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. 在 Tez 视图加载后，会看到群集中当前正在运行或过去曾经运行过的 hive 查询的列表。

    ![所有 DAG](./media/hdinsight-debug-ambari-tez-view/tez-view-home.png)

3. 如果只有一个条目，则该条目是前一部分运行的查询的结果。 如果有多个条目，可以使用页面顶部的字段进行搜索。

4. 选择 Hive 查询的“查询 ID”。 随即显示查询的相关信息。

    ![DAG 详细信息](./media/hdinsight-debug-ambari-tez-view/query-details.png)

5. 可通过此页上的选项卡查看以下信息：

    * **查询详细信息**：有关 Hive 查询的详细信息。
    * **时间线**：有关每个处理阶段所花时间的信息。
    * **配置**：用于此查询的配置。

    在“查询详细信息”中，可以通过链接查找有关此查询的“应用程序”或“DAG”的信息。
    
    * “应用程序”链接显示有关此查询的 YARN 应用程序的信息。 从此处可以访问 YARN 应用程序日志。
    * “DAG”链接显示有关此查询的有向无环图的信息。 从此处可以查看 DAG 的图形表示形式。 还可以找到有关 DAG 顶点的信息。

## <a name="next-steps"></a>后续步骤

既然已了解如何使用 Tez 视图，接下来将详细了解如何[使用 Hive on HDInsight](hdinsight-use-hive.md)。

有关 Tez 的更多详细的技术信息，请参阅 [Hortonworks 的 Tez 页](http://hortonworks.com/hadoop/tez/)。

有关如何将 Ambari 与 HDInsight 配合使用的详细信息，请参阅[使用 Ambari Web UI 管理 HDInsight 群集](hdinsight-hadoop-manage-ambari.md)

