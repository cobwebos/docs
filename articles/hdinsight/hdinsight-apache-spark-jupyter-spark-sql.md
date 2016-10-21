<properties
	pageTitle="在 HDInsight Linux 上创建 Spark 群集并从 Jupyter 使用 Spark SQL 执行交互式分析 | Microsoft Azure"
	description="逐步说明如何在 HDInsight 群集中快速创建 Apache Spark 群集，然后从 Jupyter 笔记本使用 Spark SQL 来运行交互式查询。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="jhubbard"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="07/25/2016"
	ms.author="nitinme"/>


# 入门：在 HDInsight Linux 上创建 Apache Spark 群集并使用 Spark SQL 运行交互式查询

了解如何在 HDInsight 中创建 Apache Spark 群集，然后使用 [Jupyter](https://jupyter.org) 笔记本在 Spark 群集上运行 Spark SQL 交互式查询。

   ![开始使用 HDInsight 中的 Apache Spark](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png "HDInsight 中的 Apache Spark 入门教程。演示的步骤：创建存储帐户；创建群集；运行Spark SQL 语句")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

**先决条件：**

- **一个 Azure 订阅**。在开始学习本教程之前，你必须有一个 Azure 订阅。请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)。

- **安全外壳 (SSH) 客户端**：Linux、Unix 和 OS X 系统可通过 `ssh` 命令提供 SSH 客户端。对于 Windows 系统，我们建议使用 [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)。
    
- **安全外壳 (SSH) 密钥（可选）**：可以使用密码或公钥来保护用于连接群集的 SSH 帐户。使用密码可以快速开始，如果想要快速创建群集并运行一些测试作业，则应使用此选项。使用密钥更安全，但是需要额外的设置。创建生产群集时，可能需要使用此方法。在本文中，我们将使用密码方法。有关如何创建 SSH 密钥并将其用于 HDInsight 的说明，请参阅以下文章：

	-  在 Linux 计算机中 - [从 Linux、Unix 或 OS X 搭配使用 SSH 与基于 Linux 的 HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)
    
	-  在 Windows 计算机中 - [在 Windows 中将 SSH 与基于 Linux 的 HDInsight (Hadoop) 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)。

>[AZURE.NOTE] 本文通过 ARM 模板创建使用 [Azure 存储 Blob 作为群集存储](hdinsight-hadoop-use-blob-storage.md)的 Spark 群集。除了使用 Azure 存储 Blob 作为默认存储外，还可以创建使用 [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md) 作为附加存储的 Spark 群集。有关说明，请参阅 [Create an HDInsight cluster with Data Lake Store](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md)（创建包含 Data Lake Store 的 HDInsight 群集）。


## 创建 Spark 群集

在本部分中，你将使用 Azure ARM 模板创建 HDInsight 3.4 版群集（Spark 1.6.1 版）。有关 HDInsight 版本及其 SLA 的信息，请参阅 [HDInsight 组件版本](hdinsight-component-versioning.md)。有关其他群集创建方法，请参阅 [Create HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md)（创建 HDInsight 群集）。

1. 单击下面的图像可在 Azure 门户中打开 ARM 模板。

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/zh-CN/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    ARM 模板位于公共 blob 容器中，*https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*。
   
2. 在“参数”边栏选项卡中，输入以下内容：

    - **ClusterName**：为将创建的 Hadoop 群集输入名称。
    - **群集登录名和密码**：默认登录名是 admin。
    - **SSH 用户名和密码**。
    
    请记下这些值。本教程后面的步骤中将会用到它们。

    > [AZURE.NOTE] 使用 SSH 可通过命令行远程访问 HDInsight 群集。在通过 SSH 连接群集时，将用到此处所用的用户名和密码。此外，SSH 用户名必须唯一，因为它将在所有 HDInsight 群集节点上创建一个用户帐户。下面是一些保留给群集上的服务使用的帐户名，不能将其用作 SSH 用户名：
    >
    > root、hdiuser、storm、hbase、ubuntu、zookeeper、hdfs、yarn、mapred、hbase、hive、oozie、falcon、sqoop、admin、tez、hcat、hdinsight-zookeeper。

	> 有关如何将 SSH 与 HDInsight 配合使用的详细信息，请参阅以下文章之一：

	> * [在 Linux、Unix 或 OS X 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-unix.md)
	> * [在 Windows 中的 HDInsight 上将 SSH 与基于 Linux 的 Hadoop 配合使用](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3\. 单击“确定”以保存参数。

4\. 在“自定义部署”边栏选项卡中，单击“资源组”下拉框，然后单击“新建”以创建新资源组。资源组是对群集、依赖存储帐户和其他链接资源进行分组的容器。

5\. 单击“法律条款”，然后单击“创建”。

6\. 单击“创建”。你会看到一个标题为“为模板部署提交部署”的新磁贴。创建群集和 SQL 数据库大约需要 20 分钟时间。



## 使用 Jupyter 笔记本运行 Spark SQL 查询

在本部分中，你将使用 Jupyter 笔记本针对 Spark 群集运行 Spark SQL 查询。HDInsight Spark 群集提供了可在 Jupyter 笔记本中使用的两个内核。它们是：

* **PySpark**（适用于以 Python 编写的应用程序）
* **Spark**（适用于以 Scala 编写的应用程序）

本文将使用 PySpark 内核。在 [Kernels available on Jupyter notebooks with Spark HDInsight clusters](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels)（包含 Spark HDInsight 群集的 Jupyter 笔记本上可用的内核）一文中，可以详细了解使用 PySpark 内核带来的好处。但是，使用 PySpark 内核的主要好处包括：

* 不需要设置 Spark 和 Hive 的上下文。系统会自动为你设置。
* 可以使用 cell magic（例如 `%%sql`）直接运行 SQL 或 Hive 查询，而不需要任何前置的代码片段。
* SQL 或 Hive 查询的输出将自动可视化。

### 使用 PySpark 内核创建 Jupyter 笔记本 

1. 在 [Azure 门户](https://portal.azure.com/)上的启动板中，单击 Spark 群集的磁贴（如果已将它固定到启动板）。也可以单击“全部浏览”>“HDInsight 群集”导航到你的群集。

2. 在 Spark 群集边栏选项卡中单击“快速链接”，然后在“群集仪表板”边栏选项卡中单击“Jupyter 笔记本”。出现提示时，请输入群集的管理员凭据。

	> [AZURE.NOTE] 也可以在浏览器中打开以下 URL 来访问群集的 Jupyter 笔记本。将 __CLUSTERNAME__ 替换为群集的名称：
	>
	> `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. 创建新的笔记本。单击“新建”，然后单击“PySpark”。

	![创建新的 Jupyter 笔记本](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "创建新的 Jupyter 笔记本")

3. 新笔记本随即已创建，并以 Untitled.pynb 名称打开。在顶部单击笔记本名称，然后输入一个友好名称。

	![提供笔记本的名称](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "提供笔记本的名称")

4. 使用笔记本是使用 PySpark 内核创建的，因此不需要显式创建任何上下文。当你运行第一个代码单元格时，系统将自动为你创建 Spark 和 Hive 上下文。首先可以导入此方案所需的类型。为此，请将以下代码片段粘贴到某个单元中，然后按 **SHIFT + ENTER**。

		from pyspark.sql.types import *
		
	每当你在 Jupyter 中运行作业时，Web 浏览器窗口标题中都会显示“(繁忙)”状态以及笔记本标题。右上角 **PySpark** 文本的旁边还会出现一个实心圆。作业完成后，实心圆将变成空心圆。

	 ![Jupyter 笔记本作业的状态](./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Jupyter 笔记本作业的状态")

4. 将示例数据载入临时表。当你在 HDInsight 中创建 Spark 群集时，系统会将示例数据文件 **hvac.csv** 复制到 **\\HdiSamples\\HdiSamples\\SensorSampleData\\hvac** 下的关联存储帐户。

	将以下代码示例粘贴到空白单元格中，然后按 **SHIFT + ENTER**。此代码示例会将数据注册到名为 **hvac** 的临时表中。

		# Load the data
		hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
		
		# Create the schema
		hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
		
		# Parse the data in hvacText
		hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
		
		# Create a data frame
		hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
		
		# Register the data fram as a table to run queries against
		hvacdf.registerTempTable("hvac")

5. 由于你使用的是 PySpark 内核，因此现在可直接在刚才使用 `%%sql` magic 创建的临时表 **hvac** 上运行 SQL 查询。有关 `%%sql` magic 以及可在 PySpark 内核中使用的其他 magic 的详细信息，请参阅 [Kernels available on Jupyter notebooks with Spark HDInsight clusters](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels)（包含 Spark HDInsight 群集的 Jupyter 笔记本上可用的内核）。
		
		%%sql
		SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = "6/1/13"

5. 作业成功完成后，默认情况下会显示以下表格输出。

 	![查询结果的表输出](./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "查询结果的表输出")

	你也可以在其他视觉效果中查看结果。例如，同一输出的分区图看起来如下所示。

	![查询结果分区图](./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "查询结果分区图")


6. 完成运行应用程序之后，应该要关闭笔记本以释放资源。为此，请在笔记本的“文件”菜单中，单击“关闭并停止”。这将会关闭笔记本。

##删除群集

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## 另请参阅


* [概述：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### 方案

* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](hdinsight-apache-spark-use-bi-tools.md)

* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](hdinsight-apache-spark-eventhub-streaming.md)

* [使用 HDInsight 中的 Spark 分析网站日志](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Application Insight telemetry data analysis using Spark in HDInsight（使用 HDInsight 中的 Spark 执行 Application Insight 遥测数据分析）](hdinsight-spark-analyze-application-insight-logs.md)

### 创建和运行应用程序

* [使用 Scala 创建独立的应用程序](hdinsight-apache-spark-create-standalone-application.md)

* [使用 Livy 在 Spark 群集中远程运行作业](hdinsight-apache-spark-livy-rest-interface.md)

### 工具和扩展

* [使用适用于 IntelliJ IDEA 的 HDInsight 工具插件创建和提交 Spark Scala 应用程序](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely（使用 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Spark 应用程序）](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### 管理资源

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](hdinsight-apache-spark-resource-manager.md)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0921_2016-->