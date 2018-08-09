---
title: 管理 Azure HDInsight 上 Apache Spark 群集的资源
description: 了解如何管理 Azure HDInsight 上 Spark 群集的资源以提高性能。
services: hdinsight
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: jasonh
ms.openlocfilehash: 3f85ed1f2e0ce8345a71a725b363d718fc980a07
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2018
ms.locfileid: "39622953"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>管理 Azure HDInsight 上 Apache Spark 群集的资源 

了解如何访问与 Spark 群集关联的界面（如 Ambari UI、YARN UI 和 Spark History Server），以及如何优化群集配置以达到最佳性能。

**先决条件：**

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

## <a name="open-the-ambari-web-ui"></a>打开 Ambari Web UI

Apache Ambari 用于监视群集和进行配置更改。 有关详细信息，请参阅[使用 Azure 门户在 HDInsight 中管理 Hadoop 群集](../hdinsight-administer-use-portal-linux.md#open-the-ambari-web-ui)

## <a name="open-the-spark-history-server"></a>打开 Spark History Server

Spark History Server 是已完成和正在运行的 Spark 应用程序的 Web UI。 它是 Spark Web UI 的扩展。

**打开 Spark History Server Web UI**

1. 从 [Azure 门户](https://portal.azure.com/)打开 Spark 群集。 有关详细信息，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters)。
2. 在“快速链接”中，单击“群集仪表板”，并单击“Spark History Server”。

    ![Spark History Server](./media/apache-spark-resource-manager/launch-history-server.png "Spark History Server")

    出现提示时，输入 Spark 群集的管理员凭据。 也可以通过浏览到以下 URL 打开 Spark History Server：

    ```
    https://<ClusterName>.azurehdinsight.net/sparkhistory
    ```

    将 <ClusterName> 替换为 Spark 群集名称。

Spark History Server Web UI 如下所示：

![HDInsight Spark History Server](./media/apache-spark-resource-manager/hdinsight-spark-history-server.png)

## <a name="open-the-yarn-ui"></a>打开 YARN UI
可以使用 YARN UI 来监视 Spark 群集上当前运行的应用程序。

1. 从 [Azure 门户](https://portal.azure.com/)打开 Spark 群集。 有关详细信息，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#list-and-show-clusters)。
2. 在“快速链接”中，单击“群集仪表板”，并单击“YARN”。

    ![启动 YARN UI](./media/apache-spark-resource-manager/launch-yarn-ui.png)

   > [!TIP]
   > 或者，也可以从 Ambari UI 启动 YARN UI。 要启动 Ambari UI，请单击“群集仪表板”，并单击“HDInsight 群集仪表板”。 在 Ambari UI 中依次单击“YARN”、“快速链接”、活动的资源管理器和“资源管理器 UI”。
   >
   >

## <a name="optimize-clusters-for-spark-applications"></a>针对 Spark 应用程序优化群集
根据应用程序的要求，可用于 Spark 配置的三个关键参数为 `spark.executor.instances`、`spark.executor.cores` 和 `spark.executor.memory`。 执行器是针对 Spark 应用程序启动的进程。 它在辅助角色节点上运行，负责执行应用程序的任务。 执行器的默认数目和每个群集的执行器大小是根据辅助角色节点数目和辅助角色节点大小计算的。 这些信息存储在群集头节点上的 `spark-defaults.conf` 中。

这三个配置参数可在群集级别配置（适用于群集上运行的所有应用程序），也可以针对每个应用程序指定。

### <a name="change-the-parameters-using-ambari-ui"></a>使用 Ambari UI 更改参数
1. 在 Ambari UI 中单击“Spark”，单击“配置”，并展开“自定义 spark-defaults”。

    ![使用 Ambari 设置参数](./media/apache-spark-resource-manager/set-parameters-using-ambari.png)
2. 默认值（在群集上并发运行 4 个 Spark 应用程序）是合理的。 可以从用户界面更改这些值，如以下屏幕截图所示：

    ![使用 Ambari 设置参数](./media/apache-spark-resource-manager/set-executor-parameters.png)
3. 单击“保存”以保存配置更改。 在页面顶部，系统会提示是否重启所有受影响的服务。 请单击“重启”。

    ![重新启动服务](./media/apache-spark-resource-manager/restart-services.png)

### <a name="change-the-parameters-for-an-application-running-in-jupyter-notebook"></a>更改 Jupyter 笔记本中运行的应用程序的参数
对于在 Jupyter 笔记本中运行的应用程序，可以使用 `%%configure` magic 进行配置更改。 理想情况下，必须先在应用程序开头进行此类更改，再运行第一个代码单元。 这可以确保在创建 Livy 会话时会配置应用到该会话。 如果想要更改处于应用程序中后面某个阶段的配置，必须使用 `-f` 参数。 但是，这样做会使应用程序中的所有进度丢失。

以下代码片段演示如何更改 Jupyter 中运行的应用程序的配置。

    %%configure
    {"executorMemory": "3072M", "executorCores": 4, "numExecutors":10}

配置参数必须以 JSON 字符串传入，并且必须位于 magic 后面的下一行，如示例列中所示。

### <a name="change-the-parameters-for-an-application-submitted-using-spark-submit"></a>使用 spark-submit 更改已提交应用程序的参数
以下命令示范了如何更改使用 `spark-submit` 提交的批处理应用程序的配置参数。

    spark-submit --class <the application class to execute> --executor-memory 3072M --executor-cores 4 –-num-executors 10 <location of application jar file> <application parameters>

### <a name="change-the-parameters-for-an-application-submitted-using-curl"></a>使用 cURL 更改已提交应用程序的参数
以下命令示范了如何更改使用 cURL 提交的批处理应用程序的配置参数。

    curl -k -v -H 'Content-Type: application/json' -X POST -d '{"file":"<location of application jar file>", "className":"<the application class to execute>", "args":[<application parameters>], "numExecutors":10, "executorMemory":"2G", "executorCores":5' localhost:8998/batches

### <a name="change-these-parameters-on-a-spark-thrift-server"></a>在 Spark Thrift 服务器上更改这些参数
Spark Thrift 服务器提供对 Spark 群集的 JDBC/ODBC 访问，用来为 Spark SQL 查询提供服务。 Power BI、Tableau 等工具 使用 ODBC 协议与 Spark Thrift 服务器通信，以便将 Spark SQL 查询作为 Spark 应用程序执行。 创建 Spark 群集时，将启动 Spark Thrift 服务器的两个实例（每个头节点上各有一个实例）。 在 YARN UI 中，每个 Spark Thrift 服务器显示为一个 Spark 应用程序。

Spark Thrift 服务器使用 Spark 动态执行器分配，因此未使用 `spark.executor.instances`。 相反，Spark Thrift 服务器使用 `spark.dynamicAllocation.minExecutors` 和 `spark.dynamicAllocation.maxExecutors` 来指定执行器计数。 使用配置参数 `spark.executor.cores` 和 `spark.executor.memory` 可以修改执行器大小。 可按以下步骤所示更改这些参数：

* 展开“高级 spark-thrift-sparkconf”类别可更新参数 `spark.dynamicAllocation.minExecutors`、`spark.dynamicAllocation.maxExecutors` 和 `spark.executor.memory`。

    ![配置 Spark Thrift 服务器](./media/apache-spark-resource-manager/spark-thrift-server-1.png)    
* 展开“自定义 spark-thrift-sparkconf”类别可更新参数 `spark.executor.cores`。

    ![配置 Spark Thrift 服务器](./media/apache-spark-resource-manager/spark-thrift-server-2.png)

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>更改 Spark Thrift 服务器的驱动程序内存
Spark Thrift 服务器驱动程序内存配置为头节点 RAM 大小的 25%，前提是头节点的 RAM 总大小大于 14 GB。 可以使用 Ambari UI 更改驱动程序内存配置，如以下屏幕截图所示：

* 在 Ambari UI 中单击“Spark”，单击“配置”，展开“高级 spark-env”，并提供“spark_thrift_cmd_opts”的值。

    ![配置 Spark Thrift 服务器 RAM](./media/apache-spark-resource-manager/spark-thrift-server-ram.png)

## <a name="reclaim-spark-cluster-resources"></a>回收 Spark 群集资源
由于 Spark 动态分配，因此 Thrift 服务器使用的唯一资源是两个应用程序主机的资源。 若要回收这些资源，必须停止群集上运行的 Thrift 服务器服务。

1. 在 Ambari UI 的左窗格中，单击“Spark”。
2. 在下一页中，单击“Spark Thrift 服务器”。

    ![重新启动 Thrift 服务器](./media/apache-spark-resource-manager/restart-thrift-server-1.png)
3. 应会看到正在运行 Spark Thrift 服务器的两个头节点。 单击其中一个头节点。

    ![重新启动 Thrift 服务器](./media/apache-spark-resource-manager/restart-thrift-server-2.png)
4. 下一页将列出该头节点上运行的所有服务。 在该列表中单击 Spark Thrift 服务器旁边的下拉按钮，并单击“停止”。

    ![重新启动 Thrift 服务器](./media/apache-spark-resource-manager/restart-thrift-server-3.png)
5. 对其他头节点重复上述步骤。

## <a name="restart-the-jupyter-service"></a>重新启动 Jupyter 服务
启动 Ambari Web UI，如本文开头所示。 在左侧导航窗格中，依次单击“Jupyter”、“服务操作”和“全部重启”。 这会在所有头节点上启动 Jupyter 服务。

![重启 Jupyter](./media/apache-spark-resource-manager/restart-jupyter.png "重启 Jupyter")

## <a name="monitor-resources"></a>监视资源
启动 Yarn UI，如本文开头所示。 在屏幕顶部的“群集指标”表中，选中“已用内存”和“内存总计”列的值。 如果这 2 个值很接近，则可能资源不足，无法启动下一个应用程序。 这同样适用于“已用 VCore”和“VCore 总计”列。 此外，在主视图中，如果有应用程序保持“已接受”状态，而不转换为“正在运行”或“失败”状态，这也可能指示该应用程序未获得足够的资源来启动。

![资源限制](./media/apache-spark-resource-manager/resource-limit.png "资源限制")

## <a name="kill-running-applications"></a>终止正在运行的应用程序
1. 在 Yarn UI 中，从左侧面板中，单击“正在运行”。 在正在运行的应用程序的列表中，确定要终止的应用程序，并单击 **ID**。

    ![终止 App1](./media/apache-spark-resource-manager/kill-app1.png "终止 App1")

2. 单击右上角的“终止应用程序”，并单击“确定”。

    ![终止 App2](./media/apache-spark-resource-manager/kill-app2.png "终止 App2")

## <a name="see-also"></a>另请参阅
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>适用于数据分析师

* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)
* [Application Insight telemetry data analysis using Spark in HDInsight（使用 HDInsight 中的 Spark 执行 Application Insight 遥测数据分析）](apache-spark-analyze-application-insight-logs.md)
* [使用 Caffe on Azure HDInsight Spark 进行分布式深度学习](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>适用于 Spark 开发人员

* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely（使用 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Spark 应用程序）](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)
