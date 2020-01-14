---
title: 管理 Azure HDInsight 上 Apache Spark 群集的资源
description: 了解如何在 Azure HDInsight 上管理 Spark 群集的资源以提高性能。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 3aab89f86dcd48328771cd0fda03d1c9de4bc2c2
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/14/2020
ms.locfileid: "75932107"
---
# <a name="manage-resources-for-apache-spark-cluster-on-azure-hdinsight"></a>管理 Azure HDInsight 上 Apache Spark 群集的资源

了解如何访问[Apache Ambari](https://ambari.apache.org/) ui、 [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html) ui 以及与[Apache Spark](https://spark.apache.org/)群集关联的[Spark 历史记录服务器](./apache-azure-spark-history-server.md)等接口，以及如何优化群集配置以获得最佳性能。

## <a name="open-the-spark-history-server"></a>打开 Spark History Server

Spark History Server 是已完成和正在运行的 Spark 应用程序的 Web UI。 它是 Spark Web UI 的扩展。 有关完整信息，请参阅[Spark History Server](./apache-azure-spark-history-server.md)。

## <a name="open-the-yarn-ui"></a>打开 YARN UI

可以使用 YARN UI 来监视 Spark 群集上当前运行的应用程序。

1. 从 [Azure 门户](https://portal.azure.com/)打开 Spark 群集。 有关详细信息，请参阅[列出和显示群集](../hdinsight-administer-use-portal-linux.md#showClusters)。

2. 在**群集仪表板**中，选择 " **Yarn**"。 出现提示时，输入 Spark 群集的管理员凭据。

    ![启动 YARN UI](./media/apache-spark-resource-manager/azure-portal-dashboard-yarn.png)

   > [!TIP]  
   > 或者，也可以从 Ambari UI 启动 YARN UI。 在 Ambari UI 中，导航到 " **YARN** > "**快速链接**" > **活动** > **资源管理器" ui**"。

## <a name="optimize-clusters-for-spark-applications"></a>针对 Spark 应用程序优化群集

根据应用程序的要求，可用于 Spark 配置的三个关键参数为 `spark.executor.instances`、`spark.executor.cores` 和 `spark.executor.memory`。 执行器是针对 Spark 应用程序启动的进程。 它在辅助角色节点上运行，负责执行应用程序的任务。 执行器的默认数目和每个群集的执行器大小是根据辅助角色节点数目和辅助角色节点大小计算的。 这些信息存储在群集头节点上的 `spark-defaults.conf` 中。

这三个配置参数可在群集级别配置（适用于群集上运行的所有应用程序），也可以针对每个应用程序指定。

### <a name="change-the-parameters-using-ambari-ui"></a>使用 Ambari UI 更改参数

1. 在 Ambari UI 中，导航到 custom-spark2-defaults ** > 配置** > **自定义 custom-spark2-defaults**。

    ![使用 Ambari 自定义设置参数](./media/apache-spark-resource-manager/ambari-ui-spark2-configs.png "使用 Ambari 自定义设置参数")

1. 默认值（在群集上并发运行 4 个 Spark 应用程序）是合理的。 可以从用户界面更改这些值，如以下屏幕截图所示：

    ![使用 Ambari 设置参数](./media/apache-spark-resource-manager/ambari-ui-spark2-defaults.png "使用 Ambari 设置参数")

1. 选择 "**保存**" 以保存配置更改。 在页面顶部，系统会提示重新启动所有受影响的服务。 选择“重启”。

    ![重新启动服务](./media/apache-spark-resource-manager/apache-ambari-restart-services.png)

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

Spark Thrift 服务器提供对 Spark 群集的 JDBC/ODBC 访问，用来为 Spark SQL 查询提供服务。 Power BI、Tableau 等工具使用 ODBC 协议与 Spark Thrift 服务器进行通信，以便将 Spark SQL 查询作为 Spark 应用程序执行。 创建 Spark 群集时，将启动 Spark Thrift 服务器的两个实例（每个头节点上各有一个实例）。 在 YARN UI 中，每个 Spark Thrift 服务器显示为一个 Spark 应用程序。

Spark Thrift 服务器使用 Spark 动态执行器分配，因此不使用 `spark.executor.instances`。 相反，Spark Thrift 服务器使用 `spark.dynamicAllocation.maxExecutors` 和 `spark.dynamicAllocation.minExecutors` 来指定执行器计数。 配置参数 `spark.executor.cores`和 `spark.executor.memory` 用于修改执行器大小。 可按以下步骤所示更改这些参数：

* 展开 " **Advanced custom-spark2-defaults-thrift-sparkconf** " 类别，更新 `spark.dynamicAllocation.maxExecutors`和 `spark.dynamicAllocation.minExecutors`的参数。

    ![配置 Spark thrift 服务器](./media/apache-spark-resource-manager/ambari-ui-advanced-thrift-sparkconf.png "配置 Spark Thrift 服务器")

* 展开 "**自定义 custom-spark2-defaults-thrift-sparkconf** " 类别，更新 `spark.executor.cores`和 `spark.executor.memory`的参数。

    ![配置 Spark thrift 服务器参数](./media/apache-spark-resource-manager/ambari-ui-custom-thrift-sparkconf.png "配置 Spark thrift 服务器参数")

### <a name="change-the-driver-memory-of-the-spark-thrift-server"></a>更改 Spark Thrift 服务器的驱动程序内存

Spark Thrift 服务器驱动程序内存配置为头节点 RAM 大小的 25%，前提是头节点的 RAM 总大小大于 14 GB。 可以使用 Ambari UI 更改驱动程序内存配置，如以下屏幕截图所示：

在 Ambari UI 中，导航到 " **custom-spark2-defaults** " ** >  > 配置**"**高级 custom-spark2-defaults**"。 然后为**spark_thrift_cmd_opts**提供值。

## <a name="reclaim-spark-cluster-resources"></a>回收 Spark 群集资源

由于 Spark 动态分配，因此 Thrift 服务器使用的唯一资源是两个应用程序主机的资源。 若要回收这些资源，必须停止群集上运行的 Thrift 服务器服务。

1. 在 Ambari UI 中，从左窗格中选择 " **custom-spark2-defaults**"。

2. 在下一页中，选择 " **Custom-spark2-defaults Thrift 服务器**"。

    ![重新启动 thrift server1](./media/apache-spark-resource-manager/ambari-ui-spark2-thrift-servers.png "重新启动 thrift server1")

3. 应该会看到运行 Custom-spark2-defaults Thrift 服务器的两个头节点。 选择其中一个头节点。

    ![重新启动 thrift server2](./media/apache-spark-resource-manager/restart-thrift-server-2.png "重新启动 thrift server2")

4. 下一页将列出该头节点上运行的所有服务。 从列表中，选择 Custom-spark2-defaults Thrift 服务器旁边的下拉按钮，然后选择 "**停止**"。

    ![重启 thrift server3](./media/apache-spark-resource-manager/ambari-ui-spark2-thriftserver-restart.png "重启 thrift server3")
5. 对其他头节点重复上述步骤。

## <a name="restart-the-jupyter-service"></a>重新启动 Jupyter 服务

启动 Ambari Web UI，如本文开头所示。 从左侧导航窗格中，选择 " **Jupyter**"，选择 "**服务操作**"，然后选择 "**全部重启**"。 这会在所有头节点上启动 Jupyter 服务。

![重新启动 Jupyter](./media/apache-spark-resource-manager/apache-ambari-restart-jupyter.png "重新启动 Jupyter")

## <a name="monitor-resources"></a>监视资源

启动 Yarn UI，如本文开头所示。 在屏幕顶部的“群集指标”表中，选中“已用内存”和“内存总计”列的值。 如果这 2 个值很接近，则可能资源不足，无法启动下一个应用程序。 这同样适用于“已用 VCore”和“VCore 总计”列。 此外，在主视图中，如果有应用程序保持 "已**接受**" 状态，而不转换为 "**正在运行**" 或 "**失败**" 状态，这也可能表示它没有足够的资源来启动。

![资源限制](./media/apache-spark-resource-manager/apache-ambari-resource-limit.png "资源限制")

## <a name="kill-running-applications"></a>终止正在运行的应用程序

1. 在 Yarn UI 中，从左侧面板中选择 "**正在运行**"。 从正在运行的应用程序列表中，确定要终止的应用程序并选择该**ID**。

    ![终止 App1](./media/apache-spark-resource-manager/apache-ambari-kill-app1.png "终止 App1")

2. 在右上角选择 "**终止应用程序**"，然后选择 **"确定"** 。

    ![终止 App2](./media/apache-spark-resource-manager/apache-ambari-kill-app2.png "终止 App2")

## <a name="see-also"></a>另请参阅

* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)

### <a name="for-data-analysts"></a>适用于数据分析师

* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)
* [使用 HDInsight 中的 Apache Spark 执行 Application Insight 遥测数据分析](apache-spark-analyze-application-insight-logs.md)

### <a name="for-apache-spark-developers"></a>适用于 Apache Spark 开发人员

* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)
