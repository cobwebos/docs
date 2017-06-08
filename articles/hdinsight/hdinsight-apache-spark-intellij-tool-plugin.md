---
title: "用于 IntelliJ 的 Azure 工具包 - 为 HDInsight Spark 创建 Scala 应用程序 | Microsoft Docs"
description: "使用用于 IntelliJ 的 Azure 工具包开发以 Scala 编写的 Spark 应用程序，并将其提交到 HDInsight Spark 群集。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 73304272-6c8b-482e-af7c-cd25d95dab4d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 44eac1ae8676912bc0eb461e7e38569432ad3393
ms.openlocfilehash: e6e005e07b35bbaffebf1efa216feebafc99df8b
ms.contentlocale: zh-cn
ms.lasthandoff: 05/17/2017


---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-hdinsight-cluster"></a>使用适用于 IntelliJ 的 Azure 工具包创建 HDInsight 群集的 Spark 应用程序

使用用于 IntelliJ 的 Azure 工具包插件开发以 Scala 编写的 Spark 应用程序，并直接从 IntelliJ IDE 将其提交到 HDInsight Spark 群集。 可以按照多种不同的方式使用该插件：

* 在 HDInsight Spark 群集中开发和提交 Scala Spark 应用程序
* 访问 Azure HDInsight Spark 群集资源
* 本地开发和运行 Scala Spark 应用程序

也可以按照[此视频](https://mix.office.com/watch/1nqkqjt5xonza)来开始使用。

> [!IMPORTANT]
> 此插件仅可用于在 Linux 上的 HDInsight Spark 群集中创建和提交应用程序。
> 
> 

## <a name="prerequisites"></a>先决条件

* HDInsight Linux 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](hdinsight-apache-spark-jupyter-spark-sql.md)。

* Oracle Java 开发工具包。 可以从 [Oracle 网站](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)安装它。

* IntelliJ IDEA。 本文使用版本 15.0.1。 可以从 [JetBrains 网站](https://www.jetbrains.com/idea/download/)安装它。

## <a name="install-azure-toolkit-for-intellij"></a>安装用于 IntelliJ 的 Azure 工具包
有关安装说明，请参阅[安装用于 IntelliJ 的 Azure 工具包](../azure-toolkit-for-intellij-installation.md)。

## <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅
1. 启动 IntelliJ IDE 并打开 Azure 资源管理器。 在“视图”菜单中，单击“工具窗口”，然后单击“Azure 资源管理器”。
   
    ![在“视图”菜单中选择的命令](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)
2. 右键单击“Azure”节点，然后单击“登录”。
3. 在“Azure 登录”对话框中，单击“登录”并输入 Azure 凭据。
   
    ![Azure 登录对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)
4. 登录之后，“选择订阅”对话框会列出与凭据关联的所有 Azure 订阅。 单击“选择”关闭对话框。

    ![“选择订阅”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/Select-Subscriptions.png)
5. 在“Azure 资源管理器”选项卡中展开“HDInsight”，查看订阅下的 HDInsight Spark 群集。
   
    ![Azure 资源管理器中的 HDInsight Spark 群集](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)
6. 可以进一步展开群集名称节点，查看与群集关联的资源（例如存储帐户）。
   
    ![展开群集名称可查看资源](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 群集中运行 Spark Scala 应用程序
1. 启动 IntelliJ IDEA 并创建一个项目。 在“新建项目”对话框中做出以下选择，然后单击“下一步”。
   
   * 在左窗格中，选择“HDInsight”。
   * 在右窗格中，选择“Spark on HDInsight (Scala)”。
   
    ![“新建项目”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)
2. 在下一个窗口中提供以下项目详细信息，然后单击“完成”。
   
   * 提供项目名称和项目位置。
   * 对于“项目 SDK”，请使用“适用于 Spark 1.6 或 Spark 2.0 群集的 Java 1.8”。
   * 对于“Scala SDK”，请依次单击“创建”、“下载”，然后选择要使用的 Scala 版本。
     * 如果要将作业提交到 Spark 2.0 群集，请选择“JDK 1.8 和 Scala 2.11.x”。
     * 如果要将作业提交到 Spark 1.6 群集，请选择“JDK 1.8 (语言级别 7)和 Scala 2.10.x”。

        ![选择 Scala 版本](./media/hdinsight-apache-spark-intellij-tool-plugin/show-scala2.11.x-select.png)
   * 对于“Spark SDK”，请下载并使用 [GitHub] 中的 SDK (http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409)。 请注意，spark-assembly-2.0.0-hadoop2.7.0-SNAPSHOT.jar 适用于 Spark 2.0 群集，spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar 适用于 Spark 1.6 群集。 

     可以改用 [Spark Maven 存储库](http://mvnrepository.com/search?q=spark)，不过请确保已安装正确的 Maven 存储库，以便能够开发 Spark 应用程序。 例如，如果使用 Spark 流式处理，请确保已安装 Spark 流式处理部分。 另请确保：对于 Spark 1.6 群集，使用标记为 Scala 2.10 的存储库，对于 Spark 2.0 群集，使用标记为 Scala 2.11 的存储库。
     
       ![选择 Spark SDK](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-project-details.png)
3. Spark 项目将自动为你创建一个项目。 若要查看该项目，请遵循以下步骤：
   
   1. 在“文件”菜单中，单击“项目结构”。
   2. 在“项目结构”对话框中，单击“项目”以查看创建的默认项目。 也可以通过 **+** 图标创建自己的项目。
      
       ![对话框中的项目信息](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
4. 在“项目结构”对话框中，单击“项目”。 如果“项目 SDK”设置为 1.8，请确保“项目语言级别”设置为“7 - Diamonds、ARM、Multi-Catch 等”。（对于 Spark 2.0 群集是可选的。）
   
    ![设置项目语言级别](./media/hdinsight-apache-spark-intellij-tool-plugin/set-project-language-level.png)
5. 添加应用程序源代码。
   
   1. 在“项目资源管理器”中，右键单击“src”，指向“新建”，然后单击“Scala 类”。
      
       ![项目资源管理器中用于创建 Scala 类的命令](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)
   2. 在“新建 Scala 类”对话框中提供名称，在“种类”对话框中选择“对象”，然后单击“确定”。
      
       ![“新建 Scala 类”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)
   3. 在 **MyClusterApp.scala** 文件中，粘贴以下代码。 此代码从 HVAC.csv（用于所有 HDInsight Spark 群集）中读取数据，检索在 CSV 的第七列中只有一个数字的行，并将输出写入群集的默认存储容器下的 **/HVACOut**。

            import org.apache.spark.SparkConf
            import org.apache.spark.SparkContext

            object MyClusterApp{
              def main (arg: Array[String]): Unit = {
                val conf = new SparkConf().setAppName("MyClusterApp")
                val sc = new SparkContext(conf)

                val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

                //find the rows that have only one digit in the seventh column in the CSV file
                val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

                rdd1.saveAsTextFile("wasbs:///HVACOut")
              }

            }

6. 在 HDInsight Spark 群集中运行该应用程序。
   
   1. 在“项目资源管理器”中，右键单击项目名称，然后选择“将 Spark 应用程序提交到 HDInsight”。
      
       ![选择“将 Spark 应用程序提交到 HDInsight”](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)
   2. 系统将提示输入 Azure 订阅凭据。 在“Spark 提交”对话框中提供以下值，然后单击“提交”。
      
      * 对于“Spark 群集(仅限 Linux)”，选择要在其上运行应用程序的 HDInsight Spark 群集。
      * 从 IntelliJ 项目或硬盘中选择一个项目。
      * 在“Main 类名”文本框中单击省略号 (![ellipsis](./media/hdinsight-apache-spark-intellij-tool-plugin/ellipsis.png))，选择应用程序源代码中的 main 类，然后单击“确定”。
        
          ![“选择 Main 类”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)
      * 由于本示例中的应用程序代码不需要任何命令行参数，也不需要引用 JAR 或文件，你可以将其余的框留空。
        提供所有输入后，对话框应如下图所示。
        
          ![“Spark 提交”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
   3. 窗口底部的“Spark 提交”选项卡应开始显示进度。 也可以通过单击“Spark 提交”窗口中的红色按钮停止应用程序。
      
       ![“Spark 提交”窗口](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      本文稍后的“使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集”部分介绍了如何访问作业输出。

## <a name="choose-azure-data-lake-store-as-spark-scala-application-storage"></a>选择 Azure Data Lake Store 作为 Spark Scala 应用程序存储
若要将应用程序提交到 Azure Data Lake Store，必须在 Azure 登录过程中选择“交互”模式。 

   ![登录时的“交互”选项](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-interactive.png)

如果选择“自动”模式，将收到以下错误：

   ![登录错误](./media/hdinsight-apache-spark-intellij-tool-plugin/authentication-error.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集
可以使用用于 IntelliJ 的 Azure 工具包执行各种操作。

### <a name="access-the-job-view"></a>访问作业视图
1. 在“Azure 资源管理器”中依次展开“HDInsight”和 Spark 群集名称，然后单击“作业”。
2. 在右窗格中，“Spark 作业视图”选项卡显示了群集上运行的所有应用程序。 单击想要查看其详细信息的应用程序的名称。
   
    ![应用程序详细信息](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

“错误”、“驱动程序日志”、“输出”和“Livy 日志”对应的框中已根据所选应用程序填充了信息。

也可以单击窗口顶部的相应按钮来打开“Spark 历史记录 UI”和“YARN UI”（应用程序级别）。

### <a name="access-the-spark-history-server"></a>访问 Spark 历史记录服务器
1. 在“Azure 资源管理器”中，展开“HDInsight”，右键单击 Spark 群集名称，然后选择“打开 Spark 历史记录 UI”。 出现提示时，请输入群集的管理员凭据。 在预配群集时，你必须已指定这些凭据。
2. 在“Spark 历史记录服务器”仪表板中，可以使用应用程序名称查找刚运行完的应用程序。 在上述代码中，已使用 `val conf = new SparkConf().setAppName("MyClusterApp")` 设置了应用程序名称。 因此，Spark 应用程序名称为 **MyClusterApp**。

### <a name="start-the-ambari-portal"></a>启动 Ambari 门户
1. 在“Azure 资源管理器”中，展开“HDInsight”，右键单击 Spark 群集名称，然后选择“打开群集管理门户(Ambari)”。 
2. 出现提示时，请输入群集的管理员凭据。 在群集预配过程中已指定这些凭据。

### <a name="manage-azure-subscriptions"></a>管理 Azure 订阅
默认情况下，用于 IntelliJ 的 Azure 工具包将列出所有 Azure 订阅中的 Spark 群集。 如果需要，可以指定想要访问其群集的订阅。 

1. 在“Azure 资源管理器”中，右键单击“Azure”根节点，然后单击“管理订阅”。 
2. 在对话框中，清除不想访问的订阅对应的复选框，然后单击“关闭”。 如果想要从 Azure 订阅注销，可以单击“注销”。

## <a name="run-a-spark-scala-application-locally"></a>本地运行 Spark Scala 应用程序
可以使用用于 IntelliJ 的 Azure 工具包在工作站上本地运行 Spark Scala 应用程序。 通常，这些应用程序不需要访问群集资源（如存储容器），并可以在本地运行和测试。

### <a name="prerequisite"></a>先决条件
在 Windows 计算机上运行本地 Spark Scala 应用程序时，可能会发生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的异常。 发生这些异常的原因是 Windows 中缺少 WinUtils.exe。 

若要解决此错误，必须[下载该可执行文件](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)到相应位置（例如 **C:\WinUtils\bin**）。 然后必须添加环境变量 **HADOOP_HOME**，并将其值设置为 **C\WinUtils**。

### <a name="run-a-local-spark-scala-application"></a>运行本地的 Spark Scala 应用程序
1. 启动 IntelliJ IDEA 并创建一个项目。 在“新建项目”对话框中做出以下选择，然后单击“下一步”。
   
   * 在左窗格中，选择“HDInsight”。
   * 在右窗格中，选择“Spark on HDInsight 本地运行示例(Scala)”。
   
    ![“新建项目”对话框中的选项](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)
2. 在下一个窗口中提供以下项目详细信息，然后单击“完成”。
   
   * 提供项目名称和项目位置。
   * 对于“项目 SDK”，请确保提供的 Java 版本大于 7。
   * 对于“Scala SDK”，请依次单击“创建”、“下载”，然后选择要使用的 Scala 版本：“适用于 Spark 2.0 的 Scala 2.11.x”和“适用于 Spark 1.6 的 Scala 2.10.x”。
     
       ![选择 Scala 版本](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-scala-version.png)
   * 对于“Spark SDK”，请从 [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) 下载并使用该 SDK。 可以改用 [Spark Maven 存储库](http://mvnrepository.com/search?q=spark)，不过请确保已安装正确的 Maven 存储库，以便能够开发 Spark 应用程序。 例如，如果使用 Spark 流式处理，请确保已安装 Spark 流式处理部分。 另请确保：对于 Spark 1.6 群集，使用标记为 Scala 2.10 的存储库，对于 Spark 2.0 群集，使用标记为 Scala 2.11 的存储库。
     
       ![选择 Spark SDK](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-create-project.png)
3. 模板将在 **src** 文件夹下面添加可在计算机上本地运行的示例代码 (**LogQuery**)。
   
    ![LogQuery 的位置](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)
4. 右键单击“LogQuery”应用程序，然后单击“运行 'LogQuery'”。 将在底部的“运行”选项卡中看到如下输出。
   
   ![Spark 应用程序本地运行结果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>转换现有 IntelliJ IDEA 应用程序以使用用于 IntelliJ 的 Azure 工具包
可以转换在 IntelliJ IDEA 中创建的现有 Spark Scala 应用程序，使其与用于 IntelliJ 的 Azure 工具包兼容。 然后，可以使用该插件将应用程序提交到 HDInsight Spark 群集。

1. 对于通过 IntelliJ IDEA 创建的现有 Spark Scala 应用程序，打开关联的 .iml 文件。
2. 在根级别，将看到 **module** 元素，如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
   编辑该元素以添加 `UniqueKey="HDInsightTool"`，使 **module** 元素如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
3. 保存更改。 现在，应用程序应与用于 IntelliJ 的 Azure 工具包兼容。 可以通过右键单击项目资源管理器中的项目名称来测试此项。 弹出菜单现在将包含选项“将 Spark 应用程序提交到 HDInsight”。

## <a name="troubleshooting"></a>故障排除
### <a name="please-use-a-larger-heap-size-error-in-local-run"></a>本地运行期间出现“请使用更大的堆大小”错误
在 Spark 1.6 中，如果在本地运行期间使用 32 位 Java SDK，可能会遇到以下错误：

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

之所以发生这些错误，是因为堆大小不足，导致 Spark 无法运行。 （Spark 至少需要 471 MB。 可以从 [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081) 获取更多详细信息）。 一个简单的解决方法就是使用 64 位 Java SDK。 也可以通过添加以下选项来更改 IntelliJ 中的 JVM 设置：

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![将选项添加到 IntelliJ 中的“VM 选项”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="feedback-and-known-issues"></a>反馈和已知问题
目前不支持直接查看 Spark 输出。

如果你有任何建议或反馈，或使用此插件时遇到任何问题，请向 hdivstool@microsoft.com 发送电子邮件。

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](hdinsight-apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用用于 IntelliJ 的 Azure 工具包远程调试 Spark 应用程序](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](hdinsight-apache-spark-job-debugging.md)


