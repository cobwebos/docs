---
title: 调试 Azure HDInsight 中运行的 Apache Spark 作业
description: 使用 YARN UI、Spark UI 和 Spark History Server 来跟踪和调试 Azure HDInsight 中的 Spark 群集上运行的作业
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: 817b8976f5d014d990945816c2df1c7e8ed729fb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084879"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>调试 Azure HDInsight 中运行的 Apache Spark 作业

本文介绍如何跟踪和调试在 HDInsight 群集上运行的 Apache Spark 作业。 使用 Apache Hadoop YARN UI、Spark UI 和 Spark History Server 进行调试。 使用 Spark 群集中提供的笔记本启动 Spark 作业，相关信息请参阅“机器学习：使用 MLLib 对食物检测数据进行预测分析”。 按以下步骤跟踪使用任何其他方法（例如 spark-submit）提交的应用程序。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

* 你应当已开始运行笔记本，相关信息请参阅[机器学习：使用 MLLib 对食物检测数据进行预测分析](apache-spark-machine-learning-mllib-ipython.md)。 有关如何运行此 Notebook 的说明，请单击以下链接。  

## <a name="track-an-application-in-the-yarn-ui"></a>在 YARN UI 中跟踪应用程序

1. 启动 YARN UI。 在“群集仪表板”下选择“Yarn” 。

    ![Azure 门户启动 YARN UI](./media/apache-spark-job-debugging/launch-apache-yarn-ui.png)

   > [!TIP]  
   > 或者，也可以从 Ambari UI 启动 YARN UI。 若要启动 Ambari UI，请在“群集仪表板”下选择“Ambari 主页” 。 在 Ambari UI 中，导航到“YARN” > “快速链接”>“活动资源管理器”>“资源管理器 UI”。

2. 由于 Spark 作业是使用 Jupyter 笔记本启动的，因此应用程序的名称为“remotesparkmagics”（从笔记本启动的所有应用程序的名称）。 根据应用程序名称选择应用程序 ID，以获取有关该作业的详细信息。 此操作会启动应用程序视图。

    ![Spark History Server - 查找 Spark 应用程序 ID](./media/apache-spark-job-debugging/find-application-id1.png)

    对于从 Jupyter Notebook 启动的应用程序，在退出 Notebook 之前，其状态始终是“正在运行”。

3. 从应用程序视图中，可以进一步深入以找到与应用程序和日志 (stdout/stderr) 关联的容器。 也可以通过单击“跟踪 URL”对应的链接来启动 Spark UI，如下所示。

    ![Spark History Server - 下载容器日志](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>在 Spark UI 中跟踪应用程序

在 Spark UI 中，可以深入到前面启动的应用程序所产生的 Spark 作业。

1. 若要启动 Spark UI，请在应用程序视图中选择针对“跟踪 URL”的链接，如上面的屏幕截图所示。 可以看到，应用程序启动的所有 Spark 作业正在 Jupyter Notebook 中运行。

    ![Spark History Server 的“作业”选项卡](./media/apache-spark-job-debugging/view-apache-spark-jobs.png)

2. 选择“执行程序”选项卡以查看每个执行程序的处理和存储信息。 还可以通过选择“线程转储”链接来检索调用堆栈。

    ![Spark History Server 的“执行程序”选项卡](./media/apache-spark-job-debugging/view-spark-executors.png)

3. 选择“阶段”选项卡以查看与应用程序关联的阶段。

    ![Spark History Server 的“阶段”选项卡](./media/apache-spark-job-debugging/view-apache-spark-stages.png "查看 Spark 阶段")

    每个阶段可能有多个任务，你可以查看这些任务的执行统计信息，如下所示。

    ![Spark History Server 的“阶段”选项卡详细信息](./media/apache-spark-job-debugging/view-spark-stages-details.png "查看 Spark 阶段详细信息")

4. 在阶段详细信息页上，可以启动 DAG 可视化。 展开页面顶部的“DAG 可视化”链接，如下所示。

    ![查看 Spark 阶段 DAG 可视化](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)

    DAG (Direct Aclyic Graph) 呈现了应用程序中的不同阶段。 图形中的每个蓝框表示从应用程序调用的 Spark 操作。

5. 在阶段详细信息页上，还可以启动应用程序时间线视图。 展开页面顶部的“事件时间线”链接，如下所示。

    ![查看 Spark 阶段事件时间线](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)

    此图像以时间线形式显示 Spark 事件。 时间线视图提供三个级别：跨作业、作业内和阶段内。 上图中捕获了指定阶段的时间线视图。

   > [!TIP]  
   > 如果选中“启用缩放”复选框，则可以在时间线视图中左右滚动。

6. Spark UI 中的其他选项卡也提供了有关 Spark 实例的有用信息。

   * “存储”选项卡 - 如果应用程序创建了 RDD，则可以在“存储”选项卡中找到相关信息。
   * “环境”选项卡 - 此选项卡提供有关 Spark 实例的有用信息，例如：
     * Scala 版本
     * 与群集关联的事件日志目录
     * 应用程序的执行器核心数

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>使用 Spark History Server 查找有关已完成的作业的信息

完成某个作业后，有关该作业的信息将保存在 Spark History Server 中。

1. 若要启动 Spark History Server，请在“概览”页的“群集仪表板”下选择“Spark History Server”  。

    ![在 Azure 门户中启动 Spark History Server](./media/apache-spark-job-debugging/launch-spark-history-server.png "启动 Spark History Server1")

   > [!TIP]  
   > 或者，也可以从 Ambari UI 启动 Spark History Server UI。 若要启动 Ambari UI，请在“概览”边栏选项卡的“群集仪表板”下选择“Ambari 主页” 。 在 Ambari UI 中，导航到“Spark2” > “快速链接” > “Spark2 History Server UI”。

2. 随后会看到已列出所有已完成的应用程序。 选择应用程序 ID 可深入到该应用程序中获取更多信息。

    ![Spark History Server 的已完成应用程序](./media/apache-spark-job-debugging/view-completed-applications.png "启动 Spark History Server2")

## <a name="see-also"></a>另请参阅

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [使用扩展的 Spark 历史记录服务器调试 Apache Spark 作业](apache-azure-spark-history-server.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 调试 Apache Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
