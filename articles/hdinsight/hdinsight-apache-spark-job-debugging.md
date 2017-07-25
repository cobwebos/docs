---
title: "调试 Azure HDInsight 中运行的 Apache Spark 作业 | Microsoft Docs"
description: "使用 YARN UI、Spark UI 和 Spark History Server 来跟踪和调试 Azure HDInsight 中的 Spark 群集上运行的作业"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 59af05a7-2bd9-44b0-b55f-2438d294198b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 2812039649f7d2fb0705220854e4d8d0a031d31e
ms.openlocfilehash: 069f63dd43bd81c209826382d04266c2640f017e
ms.contentlocale: zh-cn
ms.lasthandoff: 07/22/2017

---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>调试 Azure HDInsight 中运行的 Apache Spark 作业

在本文中，将了解如何使用 YARN UI、Spark UI 和 Spark History Server 来跟踪和调试 HDInsight 群集中运行的 Spark 作业。 在本文中，将使用 Spark 群集中提供的笔记本启动 Spark 作业，相关信息请参阅 **Machine learning: Predictive analysis on food inspection data using MLLib**（机器学习：使用 MLLib 对食物检测数据进行预测分析）。 也可以执行以下步骤来跟踪使用任何其他方法（例如 **spark-submit**）提交的应用程序。

## <a name="prerequisites"></a>先决条件
必须满足以下条件：

* Azure 订阅。 请参阅 [获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。
* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](hdinsight-apache-spark-jupyter-spark-sql.md)。
* 你应该已开始运行笔记本，相关信息请参阅 **[Machine learning: Predictive analysis on food inspection data using MLLib](hdinsight-apache-spark-machine-learning-mllib-ipython.md)**（机器学习：使用 MLLib 对食品检测数据进行预测分析）。 有关如何运行此笔记本的说明，请单击以下链接。  

## <a name="track-an-application-in-the-yarn-ui"></a>在 YARN UI 中跟踪应用程序
1. 启动 YARN UI。 在群集边栏选项卡中，单击“群集仪表板”，然后单击“YARN”。
   
    ![启动 YARN UI](./media/hdinsight-apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > 或者，也可以从 Ambari UI 启动 YARN UI。 若要启动 Ambari UI，请在群集边栏选项卡中单击“群集仪表板”，然后单击“HDInsight 群集仪表板”。 在 Ambari UI 中依次单击“YARN”、“快速链接”、活动的 Resource Manager 和“ResourceManager UI”。    
   > 
   > 
2. 由于 Spark 作业是使用 Jupyter 笔记本启动的，因此应用程序的名称为 **remotesparkmagics**（这是从笔记本启动的所有应用程序的名称）。 单击应用程序名称旁边的应用程序 ID，以获取有关该作业的详细信息。 此时将启动应用程序视图。
   
    ![查找 Spark 应用程序 ID](./media/hdinsight-apache-spark-job-debugging/find-application-id.png)
   
    对于从 Jupyter 笔记本启动的应用程序，在退出笔记本之前，其状态始终是“正在运行”。
3. 从应用程序视图中，可以进一步深入以找到与应用程序和日志 (stdout/stderr) 关联的容器。 也可以通过单击“跟踪 URL”对应的链接来启动 Spark UI，如下所示。 
   
    ![下载容器日志](./media/hdinsight-apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>在 Spark UI 中跟踪应用程序
在 Spark UI 中，可以深入到前面启动的应用程序所产生的 Spark 作业。

1. 若要启动 Spark UI，请在应用程序视图中，单击“跟踪 URL”旁边的链接，如上面的屏幕截图中所示。 可以看到，应用程序启动的所有 Spark 作业正在 Jupyter 笔记本中运行。
   
    ![查看 Spark 作业](./media/hdinsight-apache-spark-job-debugging/view-spark-jobs.png)
2. 单击“执行器”选项卡以查看每个执行器的处理和存储信息。 还可以通过单击“线程转储”链接来检索调用堆栈。
   
    ![查看 Spark 执行器](./media/hdinsight-apache-spark-job-debugging/view-spark-executors.png)
3. 单击“阶段”选项卡以查看与应用程序关联的阶段。
   
    ![查看 Spark 阶段](./media/hdinsight-apache-spark-job-debugging/view-spark-stages.png)
   
    每个阶段可能有多个任务，你可以查看这些任务的执行统计信息，如下所示。
   
    ![查看 Spark 阶段](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-details.png) 
4. 在阶段详细信息页上，可以启动 DAG 可视化。 展开页面顶部的“DAG 可视化”链接，如下所示。
   
    ![查看 Spark 阶段 DAG 可视化](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG (Direct Aclyic Graph) 呈现了应用程序中的不同阶段。 图形中的每个蓝框表示从应用程序调用的 Spark 操作。
5. 在阶段详细信息页上，还可以启动应用程序时间线视图。 展开页面顶部的“事件时间线”链接，如下所示。
   
    ![查看 Spark 阶段事件时间线](./media/hdinsight-apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    此时将以时间线形式显示 Spark 事件。 时间线视图提供三个级别：跨作业、作业内和阶段内。 上图中捕获了指定阶段的时间线视图。
   
   > [!TIP]
   > 如果选中“启用缩放”复选框，则可以在时间线视图中左右滚动。
   > 
   > 
6. Spark UI 中的其他选项卡也提供了有关 Spark 实例的有用信息。
   
   * “存储”选项卡 - 如果应用程序创建了 RDD，你可以在“存储”选项卡中找到相关信息。
   * “环境”选项卡 - 此选项卡提供有关 Spark 实例的有用信息，例如 
     * Scala 版本
     * 与群集关联的事件日志目录
     * 应用程序的执行器核心数
     * 等等

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>使用 Spark History Server 查找有关已完成的作业的信息
完成某个作业后，有关该作业的信息将保存在 Spark History Server 中。

1. 若要启动 Spark History Server，请在群集边栏选项卡中单击“群集仪表板”，然后单击“Spark History Server”。
   
    ![启动 Spark History Server](./media/hdinsight-apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > 或者，也可以从 Ambari UI 启动 Spark History Server UI。 若要启动 Ambari UI，请在群集边栏选项卡中单击“群集仪表板”，然后单击“HDInsight 群集仪表板”。 在 Ambari UI 中，依次单击“Spark”、“快速链接”和“Spark History Server UI”。
   > 
   > 
2. 随后会看到已列出所有已完成的应用程序。 单击应用程序 ID 可深入到该应用程序以获取更多信息。
   
    ![启动 Spark History Server](./media/hdinsight-apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)



