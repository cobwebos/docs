---
title: "用于 IntelliJ 的 Azure 工具包：为 HDInsight 群集创建 Spark 应用程序 | Microsoft Docs"
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
ms.date: 08/24/2017
ms.author: nitinme
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 19cb8f436fa4d86f323013a5d4b3b50bf6c80a1a
ms.contentlocale: zh-cn
ms.lasthandoff: 08/28/2017

---
# <a name="use-azure-toolkit-for-intellij-to-create-spark-applications-for-an-hdinsight-cluster"></a>使用用于 IntelliJ 的 Azure 工具包为 HDInsight 群集创建 Spark 应用程序

使用用于 IntelliJ 的 Azure 工具包插件开发以 Scala 编写的 Spark 应用程序，并直接从 IntelliJ 集成开发环境 (IDE) 将其提交到 HDInsight Spark 群集。 可按多种方式使用该插件：

* 在 HDInsight Spark 群集中开发和提交 Scala Spark 应用程序。
* 访问 Azure HDInsight Spark 群集资源。
* 本地开发和运行 Scala Spark 应用程序。

若要创建项目，请观看 [Create Spark Applications with the Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（使用用于 IntelliJ 的 Azure 工具包创建 Spark 应用程序）视频。

> [!IMPORTANT]
> 此插件仅可用于在 Linux 上的 HDInsight Spark 群集中创建和提交应用程序。
> 

## <a name="prerequisites"></a>先决条件

- HDInsight Linux 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](hdinsight-apache-spark-jupyter-spark-sql.md)。
- Oracle Java 开发工具包。 可以从 [Oracle 网站](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)安装它。
- IntelliJ IDEA。 本文使用版本 2017.1。 可以从 [JetBrains 网站](https://www.jetbrains.com/idea/download/)安装它。

## <a name="install-azure-toolkit-for-intellij"></a>安装用于 IntelliJ 的 Azure 工具包
有关安装说明，请参阅[安装用于 IntelliJ 的 Azure 工具包](../azure-toolkit-for-intellij-installation.md)。

## <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅

1. 启动 IntelliJ IDE 并打开 Azure 资源管理器。 在“视图”菜单中，依次选择“工具窗口”、“Azure 资源管理器”。
       
   ![“Azure 资源管理器”链接](./media/hdinsight-apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. 右键单击“Azure”节点，并选择“登录”。

3. 在“Azure 登录”对话框中，选择“登录”并输入 Azure 凭据。

    ![“Azure 登录”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. 登录之后，“选择订阅”对话框会列出与凭据关联的所有 Azure 订阅。 选择“选择”按钮。

    ![“选择订阅”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. 在“Azure 资源管理器”选项卡中展开“HDInsight”，查看订阅中的 HDInsight Spark 群集。
   
    ![Azure 资源管理器中的 HDInsight Spark 群集](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. 若要查看与群集关联的资源（例如存储帐户），可以进一步展开群集名称节点。
   
    ![展开的群集名称节点](./media/hdinsight-apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 群集中运行 Spark Scala 应用程序

1. 启动 IntelliJ IDEA 并创建一个项目。 在“新建项目”对话框中执行以下操作： 

   a. 选择“HDInsight” > “Spark on HDInsight (Scala)”

   b. 在“生成工具”列表中，根据需要选择以下选项之一：

      * 用于支持 Scala 项目创建向导的“Maven”
      * 用于管理依赖项和生成 Scala 项目的“SBT”

    ![“新建项目”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

2. 选择“下一步”。

3. Scala 项目创建向导会自动检测是否安装了 Scala 插件。 选择“安装”。

   ![Scala 插件检查](./media/hdinsight-apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

4. 若要下载 Scala 插件，请选择“确定”。 按照说明重新启动 IntelliJ。 

   ![Scala 插件安装对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

5. 在“新建项目”窗口中执行以下操作：  

    ![选择 Spark SDK](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a. 输入项目名称和位置。

   b. 在“项目 SDK”下拉列表中，选择适用于 Spark 2.x 群集的“Java 1.8”，或选择适用于 Spark 1.x 群集的“Java 1.7”。

   c. 在“Spark 版本”下拉列表中，Scala 项目创建向导集成了 Spark SDK 和 Scala SDK 的适当版本。 如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”。 否则，请选择“Spark 2.x”。 本示例使用“Spark 2.0.2 (Scala 2.11.8)”。

6. 选择“完成”。

7. Spark 项目会自动创建一个项目。 若要查看项目，请执行以下操作：

   a. 在“文件”菜单中，选择“项目结构”。

   b. 在“项目结构”对话框中，选择“项目”查看创建的默认项目。 也可以选择加号 (**+**) 图标创建自己的项目。

      ![对话框中的项目信息](./media/hdinsight-apache-spark-intellij-tool-plugin/default-artifact.png)
      
8. 执行以下操作来添加应用程序源代码：

   a. 在“项目资源管理器”中，右键单击“src”，指向“新建”，并选择“Scala 类”。
      
      ![项目资源管理器中用于创建 Scala 类的命令](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. 在“新建 Scala 类”对话框中提供名称，在“种类”对话框中选择“对象”，并选择“确定”。
      
      ![“新建 Scala 类”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   c. 在 **MyClusterApp.scala** 文件中，粘贴以下代码。 此代码从 HVAC.csv（用于所有 HDInsight Spark 群集）中读取数据，检索在 CSV 的第七列中只有一个数字的行，并将输出写入群集的默认存储容器下的 **/HVACOut**。

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object MyClusterApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasb:///HVACOut")
            }
    
        }

9. 执行以下操作，在 HDInsight Spark 群集中运行该应用程序：

   a. 在“项目资源管理器”中，右键单击项目名称，然后选择“将 Spark 应用程序提交到 HDInsight”。
      
      ![“将 Spark 应用程序提交到 HDInsight”命令](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

   b. 系统会提示输入 Azure 订阅凭据。 在“Spark 提交”对话框中提供以下值，并选择“提交”。
      
      * 对于“Spark 群集(仅限 Linux)”，选择要在其上运行应用程序的 HDInsight Spark 群集。

      * 从 IntelliJ 项目或硬盘中选择一个项目。

      * 在“Main 类名”文本框中选择省略号 (**...**)，选择应用程序源代码中的 main 类，并选择“确定”。

        ![“选择 Main 类”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-3.png)

      * 由于本示例中的应用程序代码不需要命令行参数，也不需要引用 JAR 或文件，可将其余的框留空。 提供所有信息后，对话框应如下图所示。
        
        ![“Spark 提交”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

   c. 窗口底部的“Spark 提交”选项卡应开始显示进度。 也可以通过选择“Spark 提交”窗口中的红色按钮停止应用程序。
      
      ![“Spark 提交”窗口](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)
      
      若要了解如何访问作业输出，请参阅本文稍后的“使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集”部分。

## <a name="run-or-debug-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 群集上运行或调试 Spark Scala 应用程序
我们还建议以另一种方式将 Spark 应用程序提交到群集。 为此，可在“运行/调试配置”IDE 中设置参数。 有关详细信息，请参阅[使用用于 IntelliJ 的 Azure 工具包通过 SSH 远程调试 HDInsight 群集上的 Spark 应用程序](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)。

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集
可以使用用于 IntelliJ 的 Azure 工具包执行各种操作。

### <a name="access-the-job-view"></a>访问作业视图
1. 在“Azure 资源管理器”中依次展开“HDInsight”和 Spark 群集名称，并选择“作业”。  

    ![“作业视图”节点](./media/hdinsight-apache-spark-intellij-tool-plugin/job-view-node.png)

2. 在右窗格中，“Spark 作业视图”选项卡显示了群集上运行的所有应用程序。 选择想要查看其详细信息的应用程序的名称。

    ![应用程序详细信息](./media/hdinsight-apache-spark-intellij-tool-plugin/view-job-logs.png)

3. 若要显示正在运行的作业的基本信息，请将鼠标悬停在作业图上。 若要查看每个作业生成的阶段图和信息，请在作业图中选择一个节点。

    ![作业阶段详细信息](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. 若要查看常用的日志，例如“驱动程序 Stderr”、“驱动程序 Stdout”和“目录信息”，请选择“日志”选项卡。

    ![日志详细信息](./media/hdinsight-apache-spark-intellij-tool-plugin/Job-log-info.png)

5. 还可以选择窗口顶部的相应链接查看 Spark 历史记录 UI 和 YARN UI（应用程序级别）。

### <a name="access-the-spark-history-server"></a>访问 Spark 历史记录服务器
1. 在“Azure 资源管理器”中，展开“HDInsight”，右键单击 Spark 群集名称，并选择“打开 Spark 历史记录 UI”。 

2. 出现提示时，请输入群集的管理员凭据（在设置群集时已指定）。

3. 在“Spark 历史记录服务器”仪表板上，可以使用应用程序名称查找刚运行完的应用程序。 在上述代码中，已使用 `val conf = new SparkConf().setAppName("MyClusterApp")` 设置了应用程序名称。 因此，Spark 应用程序名称为 **MyClusterApp**。

### <a name="start-the-ambari-portal"></a>启动 Ambari 门户
1. 在“Azure 资源管理器”中，展开“HDInsight”，右键单击 Spark 群集名称，并选择“打开群集管理门户(Ambari)”。 

2. 出现提示时，请输入群集的管理员凭据。 在群集设置过程中已指定这些凭据。

### <a name="manage-azure-subscriptions"></a>管理 Azure 订阅
默认情况下，用于 IntelliJ 的 Azure 工具包将列出所有 Azure 订阅中的 Spark 群集。 如果需要，可以指定想要访问的订阅。 

1. 在“Azure 资源管理器”中，右键单击“Azure”根节点，并选择“管理订阅”。 

2. 在对话框中，清除不想访问的订阅旁边的复选框，并选择“关闭”。 如果想要从 Azure 订阅注销，可以选择“注销”。

## <a name="run-a-spark-scala-application-locally"></a>本地运行 Spark Scala 应用程序
可以使用用于 IntelliJ 的 Azure 工具包在工作站上本地运行 Spark Scala 应用程序。 通常，这些应用程序不需要访问群集资源（如存储容器），并可以在本地运行和测试。

### <a name="prerequisite"></a>先决条件
在 Windows 计算机上运行本地 Spark Scala 应用程序时，可能会发生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的异常。 发生此异常的原因是 Windows 中缺少 WinUtils.exe。 

若要解决此错误，请[下载该可执行文件](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)到某个位置（例如 **C:\WinUtils\bin**）。 然后必须添加环境变量 **HADOOP_HOME**，并将其值设置为 **C\WinUtils**。

### <a name="run-a-local-spark-scala-application"></a>运行本地的 Spark Scala 应用程序
1. 启动 IntelliJ IDEA 并创建一个项目。 

2. 在“新建项目”对话框中执行以下操作：
   
    a. 选择“HDInsight” > “Spark on HDInsight 本地运行示例(Scala)”。

    b. 在“生成工具”列表中，根据需要选择以下选项之一：

      * 用于支持 Scala 项目创建向导的“Maven”
      * 用于管理依赖项和生成 Scala 项目的“SBT”

    ![“新建项目”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run.png)

3. 选择“下一步”。
 
4. 在下一个窗口中执行以下操作：
   
    a. 输入项目名称和位置。

    b. 在“项目 SDK”下拉列表中，选择低于 1.7 的 Java 版本。

    c. 在“Spark 版本”下拉列表中，选择想要使用的 Scala 版本：适用于 Spark 2.0 的 Scala 2.11.x，或适用于 Spark 1.6 的 Scala 2.10.x。

    ![“新建项目”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/Create-local-project.PNG)

5. 选择“完成”。

6. 模板会在 **src** 文件夹下面添加可在计算机上本地运行的示例代码 (**LogQuery**)。
   
    ![LogQuery 的位置](./media/hdinsight-apache-spark-intellij-tool-plugin/local-app.png)

7. 右键单击 **LogQuery** 应用程序，并选择“运行 'LogQuery'”。 底部的“运行”选项卡中会显示如下输出：
   
   ![Spark 应用程序本地运行结果](./media/hdinsight-apache-spark-intellij-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>转换现有 IntelliJ IDEA 应用程序以使用用于 IntelliJ 的 Azure 工具包
可以转换在 IntelliJ IDEA 中创建的现有 Spark Scala 应用程序，使其与用于 IntelliJ 的 Azure 工具包兼容。 然后，可以使用该插件将应用程序提交到 HDInsight Spark 群集。

1. 对于通过 IntelliJ IDEA 创建的现有 Spark Scala 应用程序，打开关联的 .iml 文件。

2. 在根级别有一个 **module** 元素，如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   编辑该元素以添加 `UniqueKey="HDInsightTool"`，使 **module** 元素如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

3. 保存更改。 现在，应用程序应与用于 IntelliJ 的 Azure 工具包兼容。 可以通过右键单击项目资源管理器中的项目名称来测试此项。 弹出菜单现在将包含选项“将 Spark 应用程序提交到 HDInsight”。

## <a name="troubleshooting"></a>故障排除

### <a name="error-in-local-run-please-use-a-larger-heap-size"></a>本地运行出错：“请使用更大的堆大小”
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

之所以发生这些错误，是因为堆大小不足，导致 Spark 无法运行。 Spark 至少需要 471 MB。 （有关详细信息，请参阅 [SPARK-12081](https://issues.apache.org/jira/browse/SPARK-12081)。）一个简单的解决方法就是使用 64 位 Java SDK。 也可以通过添加以下选项来更改 IntelliJ 中的 JVM 设置：

    -Xms128m -Xmx512m -XX:MaxPermSize=300m -ea

![将选项添加到 IntelliJ 中的“VM 选项”对话框](./media/hdinsight-apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>常见问题
若要将应用程序提交到 Azure Data Lake Store，请在 Azure 登录过程中选择“交互”模式。 如果选择“自动”模式，可能会收到错误。

![interative-signin](./media/hdinsight-apache-spark-intellij-tool-plugin/interative-signin.png)

此问题现已解决。 可以选择 Azure Data Lake 群集以使用任何登录方法提交应用程序。

## <a name="feedback-and-known-issues"></a>反馈和已知问题
目前不支持直接查看 Spark 输出。

如有任何建议或反馈，或使用此插件时遇到任何问题，请向 hdivstool@microsoft.com 发送电子邮件。

## <a name="seealso"></a>后续步骤
* [概述：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>演示
* 创建 Scala 项目（视频）：[Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Spark Scala 应用程序）
* 远程调试（视频）：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用用于 IntelliJ 的 Azure 工具包远程调试 HDInsight 群集上的 Spark 应用程序）

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](hdinsight-apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Spark 应用程序](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Spark 应用程序](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](hdinsight-apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](hdinsight-apache-spark-job-debugging.md)


