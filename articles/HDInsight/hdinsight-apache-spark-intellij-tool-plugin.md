<!-- not suitable for Mooncake -->

 <properties
	pageTitle="使用适用于 IntelliJ IDEA 的 HDInsight 插件创建 Spark Scala 应用程序 | Azure"
	description="了解如何创建要在 HDInsight Spark 群集中运行的独立 Spark 应用程序。"
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.date="06/09/2016"
	wacn.date=""/>


# 使用 IntelliJ IDEA 的 HDInsight 工具插件为 HDInsight Spark Linux 群集创建 Spark 应用程序

本文提供有关使用适用于 IntelliJ IDEA 的 HDInsight 插件开发用 Scala 编写的 Spark 应用程序并将其提交到 HDInsight Spark 群集的分步指导。可以用多种不同方式使用该插件：

* 在 HDInsight Spark 群集中开发和提交 Scala Spark 应用程序
* 访问 Azure HDInsight Spark 群集资源
* 本地开发和运行 Scala Spark 应用程序

也可以按照[此处](https://mix.office.com/watch/1nqkqjt5xonza)的视频来入门。

>[AZURE.IMPORTANT] 此工具仅可用于在 Linux 上的 HDInsight Spark 群集中创建和提交应用程序。


##先决条件

* Azure 订阅。请参阅[获取 Azure 试用版](/pricing/1rmb-trial/)。
* HDInsight Linux 上的 Apache Spark 群集。有关说明，请参阅 [Create Apache Spark clusters in Azure HDInsight](/documentation/articles/hdinsight-apache-spark-jupyter-spark-sql)（在 Azure HDInsight 中创建 Apache Spark 群集）。
* Oracle Java 开发工具包。可以从[此处](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)安装它。
* IntelliJ IDEA。本文使用版本 15.0.1。可以从[此处](https://www.jetbrains.com/idea/download/)安装它。

## 安装适用于 IntelliJ IDEA 的 HDInsight 工具插件

1. 如果你位于 IntelliJ IDEA 欢迎屏幕，请单击“配置”，然后单击“插件”。

2. 在下一屏幕中，单击左下角的“浏览存储库”。在打开的“浏览存储库”对话框中，搜索“HDInsight”，选择“IntelliJ 的 Azure HDInsight 工具”，然后单击“安装”。该插件依赖于 Scala 插件，因此，如果未安装 Scala 插件，系统还会提示你安装 Scala 插件。

	![安装 HDInsight 插件](./media/hdinsight-apache-spark-intellij-tool-plugin/install-hdinsight-plugin.png)

3. 出现提示时，请单击“重新启动 IntelliJ IDEA”按钮以重新启动 IDE。


## 在 HDInsight Spark 群集中运行 Spark Scala 应用程序

1. 启动 IntelliJ IDEA 并创建一个新项目。在“新建项目”对话框中，进行以下选择，然后单击“下一步”。

	![创建 Spark Scala 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

	* 在左窗格中，选择“HDInsight”。
	* 在右窗格中，选择“Spark on HDInsight (Scala)”。
	* 单击**“下一步”**。

2. 在下一窗口中，提供项目详细信息。

	* 提供项目名称和项目位置。
	* 对于“项目 SDK”，请确保提供大于 7 的 Java 版本。
	* 对于“Scala SDK”，请依次单击“创建”、“下载”，然后选择要使用的 Scala 版本。**确保未使用版本 2.11.x**。本示例使用版本 **2.10.6**。

		![创建 Spark Scala 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* 对于“Spark SDK”，请从[此处](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)下载并使用该 SDK。你也可以忽略过此字段并改用“[Spark Maven 存储库](http://mvnrepository.com/search?q=spark)”，不过请确保已安装正确的 Maven 存储库，以便能够开发 Spark 应用程序。（例如，如果你使用 Spark Streaming，则需要确保已安装 Spark Streaming 部件；另请确保使用标记为 Scala 2.10 的存储库 - 不要使用标记为 Scala 2.11 的存储库。）

		![创建 Spark Scala 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)

	* 单击“完成”。

3. Spark 项目将自动为你创建一个项目。若要查看该项目，请遵循以下步骤。

	1. 在“文件”菜单中，单击“项目结构”。
	2. 在“项目结构”对话框中，单击“项目”以查看创建的默认项目。

		![创建 JAR](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)

	你也可以通过单击上图中突出显示的“+”图标创建自己的项目。

4. 在“项目结构”对话框中，单击“项目”。如果“项目 SDK”设置为 1.8，请确保“项目语言级别”设置为“7 - Diamonds、ARM、Multi-Catch 等”。

	![设置项目语言级别](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)

5. 添加应用程序源代码。

	1. 在“项目资源管理器”中，右键单击“src”，指向“新建”，然后单击“Scala 类”。

		![添加源代码](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

	2. 在“新建 Scala 类”对话框中，提供名称，对于“种类”，选择“对象”，然后单击“确定”。

		![添加源代码](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

	3. 在 **MyClusterApp.scala** 文件中，粘贴以下代码。此代码从 HVAC.csv（适用于所有 HDInsight Spark 群集）中读取数据，检索在 CSV 的第七列中只有一个数字的行，并将输出写入群集的默认存储容器下的 **/HVACOut**。


			import org.apache.spark.SparkConf
			import org.apache.spark.SparkContext

			object MyClusterApp{
			  def main (arg: Array[String]): Unit = {
			    val conf = new SparkConf().setAppName("MyClusterApp")
			    val sc = new SparkContext(conf)

			    val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

			    //find the rows which have only one digit in the 7th column in the CSV
				val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

			    rdd1.saveAsTextFile("wasb:///HVACOut")
			  }

			}

5. 在 HDInsight Spark 群集中运行该应用程序。

	1. 在“项目资源管理器”中，右键单击项目名称，然后选择“将 Spark 应用程序提交到 HDInsight”。

		![提交 Spark 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

	2. 系统将提示你输入 Azure 订阅凭据。在“Spark 提交”对话框框中，提供以下值。

		* 对于“Spark 群集(仅限 Linux)”，选择要在其上运行应用程序的 HDInsight Spark 群集。

		* 需要从 IntelliJ 项目或硬盘中选择一个项目。

		* 针对“主类名”文本框，单击省略号 (![省略号](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png))，选择应用程序源代码中的主类，然后单击“确定”。

			![提交 Spark 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

		* 由于本示例中的应用程序代码不需要任何命令行参数，也不需要引用 JAR 或文件，你可以将其余的文本框留空。

		* 提供所有输入后，对话框应如下所示。

			![提交 Spark 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

		* 单击“提交”。

	3. 窗口底部的“Spark 提交”选项卡应开始显示进度。也可以通过单击“Spark 提交”窗口中的红色按钮停止应用程序。

        ![Spark 应用程序结果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

    在下一部分中，你将学习如何使用适用于 IntelliJ IDEA 的 HDInsight 插件访问作业输出。


## 使用适用于 IntelliJ 的 HDInsight 插件访问和管理 HDInsight Spark 群集

可以使用 HDInsight 插件执行各种操作。

### 访问群集的存储容器

1. 从“视图”菜单，指向“工具窗口”，然后单击“HDInsight 资源管理器”。如果出现提示，请输入用于访问 Azure 订阅的凭据。

2. 展开 **HDInsight** 根节点以查看可用的 HDInsight Spark 群集的列表。

3. 展开群集名称以查看群集的存储帐户和默认存储容器。

	![访问群集存储](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-access-storage.png)

4. 单击与群集关联的存储容器名称。在右窗格中，应看到名为 **HVACOut** 的文件夹。双击以打开该文件夹，你将看到 **part-*** 文件。打开其中一个文件可查看应用程序的输出。

### 直接在插件中访问作业视图

1. 在“HDInsight 资源管理器”中，展开 Spark 群集名称，然后单击“作业”。

2. 在右窗格中，“Spark 作业视图”选项卡显示了群集上运行的所有应用程序。单击想要查看其详细信息的应用程序名称。

	![访问作业视图](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

3. “错误消息”、“作业输出”、“Livy 作业日志”和“Spark 驱动程序日志”对应的框中已根据所选应用程序填充了信息。

4. 也可以单击屏幕顶部的相应按钮来打开“Spark 历史记录 UI”和“YARN UI”（应用程序级别）。

### 访问 Spark 历史记录服务器

1. 在“HDInsight 资源管理器”中，右键单击 Spark 群集名称，然后选择“打开 Spark 历史记录 UI”。出现提示时，输入群集的管理员凭据。在预配群集时，你必须已指定这些凭据。

2. 在“Spark 历史记录服务器”仪表板中，你可以使用应用程序名称查找刚运行完的应用程序。在上面的代码中，你使用 `val conf = new SparkConf().setAppName("MyClusterApp")` 设置应用程序名称。因此，Spark 应用程序名称为 **MyClusterApp**。

### 启动 Ambari 门户

在“HDInsight 资源管理器”中，右键单击 Spark 群集名称，然后选择“打开群集管理门户(Ambari)”。出现提示时，输入群集的管理员凭据。在预配群集时，你必须已指定这些凭据。

### 管理 Azure 订阅

默认情况下，HDInsight 插件将列出你的所有 Azure 订阅中的 Spark 群集。如果需要，可以指定你想要访问其群集的订阅。在“HDInsight 资源管理器”中，右键单击“HDInsight”根节点，然后单击“管理订阅”。在对话框中，清除你不想访问的订阅所对应的复选框，然后单击“关闭”。如果你想要从 Azure 订阅注销，还可以单击“注销”。


## 本地运行 Spark Scala 应用程序

你可以使用适用于 IntelliJ IDEA 的 HDInsight 工具插件在工作站上本地运行 Spark Scala 应用程序。通常情况下，此类应用程序不需要访问群集资源（如存储容器），并可以在本地运行和测试。

### 先决条件

在 Windows 计算机上运行本地 Spark Scala 应用程序时，可能会发生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的异常，发生该异常是由于在 Windows 中缺少 WinUtils.exe。若要解决此错误，必须[从此处下载该可执行文件](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)到相应位置（例如 **C:\\WinUtils\\bin**）。然后必须添加环境变量 **HADOOP\_HOME**，并将该变量的值设置为 **C\\WinUtils**。

### 运行本地的 Spark Scala 应用程序	 

1. 启动 IntelliJ IDEA 并创建一个新项目。在“新建项目”对话框中，进行以下选择，然后单击“下一步”。

	![创建 Spark Scala 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

	* 在左窗格中，选择“HDInsight”。
	* 在右窗格中，选择“Spark on HDInsight 本地运行示例(Scala)”。
	* 单击**“下一步”**。

2. 在下一窗口中，提供项目详细信息。

	* 提供项目名称和项目位置。
	* 对于“项目 SDK”，请确保提供大于 7 的 Java 版本。
	* 对于“Scala SDK”，请依次单击“创建”、“下载”，然后选择要使用的 Scala 版本。**确保未使用版本 2.11.x**。本示例使用版本 **2.10.6**。

		![创建 Spark Scala 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)

	* 对于“Spark SDK”，请从[此处](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)下载并使用该 SDK。你也可以忽略过此字段并改用“[Spark Maven 存储库](http://mvnrepository.com/search?q=spark)”，不过请确保已安装正确的 Maven 存储库，以便能够开发 Spark 应用程序。（例如，如果你使用 Spark Streaming，则需要确保已安装 Spark Streaming 部件；另请确保使用标记为 Scala 2.10 的存储库 - 不要使用标记为 Scala 2.11 的存储库。）

		![创建 Spark Scala 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)

	* 单击“完成”。

3. 模板将在 **src** 文件夹下面添加可在计算机上本地运行的示例代码 (**LogQuery**)。

	![本地 Scala 应用程序](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)

4.  右键单击 **LogQuery** 应用程序，然后单击“运行 'LogQuery'”。你将在底部的“运行”选项卡中看到如下输出。

	![Spark 应用程序本地运行结果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## 转换现有 IntelliJ IDEA 应用程序以使用 HDInsight 工具插件

也可以转换在 IntelliJ IDEA 中创建的现有 Spark Scala 应用程序，使其与 HDInsight 工具插件兼容。这将使你可以使用该工具将应用程序提交到 HDInsight Spark 群集。为此，可以执行以下步骤：

1. 对于使用 IntelliJ IDEA 创建的现有 Spark Scala 应用程序，打开关联的 .iml 文件。
2. 在根级别，你将看到 **module** 元素，如下所示：

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

3. 编辑该元素以添加 `UniqueKey="HDInsightTool"`，使 **module** 元素如下所示：

		<module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

4. 保存更改。现在，你的应用程序应该已与 HDInsight 工具插件兼容。可以通过右键单击项目资源管理器中的项目名称来测试此项。弹出菜单现在应具有选项“将 Spark 应用程序提交到 HDInsight”。


## 故障排除

### 本地运行期间出现“请使用更大的堆大小”错误

在 Spark 1.6 中，如果在本地运行期间使用 32 位 Java SDK，你可能会遇到以下错误：

    Exception in thread "main" java.lang.IllegalArgumentException: System memory 259522560 must be at least 4.718592E8. Please use a larger heap size.
    	at org.apache.spark.memory.UnifiedMemoryManager$.getMaxMemory(UnifiedMemoryManager.scala:193)
    	at org.apache.spark.memory.UnifiedMemoryManager$.apply(UnifiedMemoryManager.scala:175)
    	at org.apache.spark.SparkEnv$.create(SparkEnv.scala:354)
    	at org.apache.spark.SparkEnv$.createDriverEnv(SparkEnv.scala:193)
    	at org.apache.spark.SparkContext.createSparkEnv(SparkContext.scala:288)
    	at org.apache.spark.SparkContext.<init>(SparkContext.scala:457)
    	at LogQuery$.main(LogQuery.scala:53)
    	at LogQuery.main(LogQuery.scala)
    	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
    	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
    	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
    	at java.lang.reflect.Method.invoke(Method.java:606)
    	at com.intellij.rt.execution.application.AppMain.main(AppMain.java:144)

这是因为堆大小不足，导致 Spark 无法运行，因为 Spark 至少需要 471MB（如果需要，可以从 [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) 获取更多详细信息）。一个简单的解决方法就是使用 64 位 Java SDK。也可以通过添加以下选项来更改 IntelliJ 中的 JVM 设置：

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![Spark 应用程序本地运行结果](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## 反馈和已知问题

目前不支持直接查看 Spark 输出，我们正在解决此问题。

如果你有任何建议或反馈，或使用此工具时遇到任何问题，欢迎向 hdivstool@microsoft.com 发送电子邮件。

## <a name="seealso"></a>另请参阅


* [概述：Azure HDInsight 上的 Apache Spark](/documentation/articles/hdinsight-apache-spark-overview)

### 方案

* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](/documentation/articles/hdinsight-apache-spark-use-bi-tools)

* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](/documentation/articles/hdinsight-apache-spark-ipython-notebook-machine-learning)

* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](/documentation/articles/hdinsight-apache-spark-machine-learning-mllib-ipython)

* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](/documentation/articles/hdinsight-apache-spark-eventhub-streaming)

* [使用 HDInsight 中的 Spark 分析网站日志](/documentation/articles/hdinsight-apache-spark-custom-library-website-log-analysis)

### 创建和运行应用程序

* [使用 Scala 创建独立的应用程序](/documentation/articles/hdinsight-apache-spark-create-standalone-application)

* [使用 Livy 在 Spark 群集中远程运行作业](/documentation/articles/hdinsight-apache-spark-livy-rest-interface)

### 工具和扩展

* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely](/documentation/articles/hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely)（使用 IntelliJ IDEA 的 HDInsight 工具插件远程调试 Spark 应用程序）

* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](/documentation/articles/hdinsight-apache-spark-use-zeppelin-notebook)

* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](/documentation/articles/hdinsight-apache-spark-jupyter-notebook-kernels)

* [Use external packages with Jupyter notebooks](/documentation/articles/hdinsight-apache-spark-jupyter-notebook-use-external-packages)（将外部包与 Jupyter 笔记本配合使用）

* [Install Jupyter on your computer and connect to an HDInsight Spark cluster](/documentation/articles/hdinsight-apache-spark-jupyter-notebook-install-locally)（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）

### 管理资源

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](/documentation/articles/hdinsight-apache-spark-resource-manager)

* [Track and debug jobs running on an Apache Spark cluster in HDInsight](/documentation/articles/hdinsight-apache-spark-job-debugging)（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）

<!---HONumber=Mooncake_0711_2016-->