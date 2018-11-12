---
title: 'Azure Toolkit for IntelliJ：为 HDInsight 群集创建 Spark 应用程序 '
description: 使用用于 IntelliJ 的 Azure 工具包开发以 Scala 编写的 Spark 应用程序，并将其提交到 HDInsight Spark 群集。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: maxluk
ms.openlocfilehash: 90c84130902420dcaa2ace74a5743fc9719622b0
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014255"
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

- HDInsight Linux 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
- Oracle Java 开发工具包。 可以从 [Oracle 网站](https://aka.ms/azure-jdks)安装它。
- IntelliJ IDEA。 本文使用版本 2017.1。 可以从 [JetBrains 网站](https://www.jetbrains.com/idea/download/)安装它。

## <a name="install-azure-toolkit-for-intellij"></a>安装用于 IntelliJ 的 Azure 工具包
有关安装说明，请参阅[安装用于 IntelliJ 的 Azure 工具包](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)。

## <a name="get-started"></a>入门
用户可以[登录到 Azure 订阅](#sign-in-to-your-azure-subscription)，或者使用 Ambari 用户名/密码或已加入域的凭据[链接 HDInsight 群集](#link-a-cluster)来启动。


## <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅

1. 启动 IntelliJ IDE 并打开 Azure 资源管理器。 在“视图”菜单中，依次选择“工具窗口”、“Azure 资源管理器”。
       
   ![“Azure 资源管理器”链接](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

1. 右键单击“Azure”节点，并选择“登录”。

1. 在“Azure 登录”对话框中，选择“登录”并输入 Azure 凭据。

    ![“Azure 登录”对话框](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

1. 登录之后，“选择订阅”对话框会列出与凭据关联的所有 Azure 订阅。 选择“选择”按钮。

    ![“选择订阅”对话框](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

1. 在“Azure 资源管理器”选项卡中展开“HDInsight”，查看订阅中的 HDInsight Spark 群集。
   
    ![Azure 资源管理器中的 HDInsight Spark 群集](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

1. 若要查看与群集关联的资源（例如存储帐户），可以进一步展开群集名称节点。
   
    ![展开的群集名称节点](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>链接群集
可以使用 Ambari 管理的用户名链接标准 HDInsight 群集。 同样，对于已加入域的 HDInsight 群集，也可使用这种域和用户名（例如 user1@contoso.com）进行链接。 也可链接 Livy 服务群集。

1. 从“Azure 资源管理器”选择“链接群集”。

   ![链接群集上下文菜单](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. 有两种链接群集的选项。 

   * 要链接 HDInsight 群集，请在“群集信息”字段中选择“HDInsight 群集”，输入“群集名/URL”、“用户名”和“密码”。

      ![链接 hdinsight 群集对话框](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

   * 要链接 Livy 服务群集，请在“群集信息”字段中选择“Livy 服务”，输入“Livy 终结点”和“群集名”。 “Yarn 终结点”可选。 “身份验证”字段中提供了两个选项。 分别为“基本身份验证”和“无身份验证”。 选择“基本身份验证”时，应提供“用户名”和“密码”。 如果身份验证失败，需要检查用户名和密码。
      
      ![链接 livy 群集对话框](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)
   
3. 如果输入信息正确，可以在 **HDInsight** 节点中看到链接的群集。 现在可以将应用程序提交到此链接群集。

   ![链接的群集](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. 还可以从 **Azure 资源管理器**取消链接群集。
   
   ![取消链接的群集](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="create-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 群集中创建 Spark Scala 应用程序

1. 启动 IntelliJ IDEA 并创建一个项目。 在“新建项目”对话框中，遵循以下步骤： 

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 选择“HDInsight” > “Spark on HDInsight (Scala)”

   b. 在“生成工具”列表中，根据需要选择以下选项之一：

      * 用于支持 Scala 项目创建向导的“Maven”
      * 用于管理依赖项和生成 Scala 项目的“SBT”

    ![“新建项目”对话框](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

1. 选择“**下一步**”。

1. Scala 项目创建向导会自动检测是否安装了 Scala 插件。 选择“安装”。

   ![Scala 插件检查](./media/apache-spark-intellij-tool-plugin/Scala-Plugin-check-Reminder.PNG) 

1. 若要下载 Scala 插件，请选择“确定”。 按照说明重新启动 IntelliJ。 

   ![Scala 插件安装对话框](./media/apache-spark-intellij-tool-plugin/Choose-Scala-Plugin.PNG)

1. 在“新建项目”窗口中执行以下操作：  

    ![选择 Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 输入项目名称和位置。

   b. 在“项目 SDK”下拉列表中，选择适用于 Spark 2.x 群集的“Java 1.8”，或选择适用于 Spark 1.x 群集的“Java 1.7”。

   c. 在“Spark 版本”下拉列表中，Scala 项目创建向导集成了 Spark SDK 和 Scala SDK 的适当版本。 如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”。 否则，请选择“Spark 2.x”。 本示例使用“Spark 2.0.2 (Scala 2.11.8)”。

1. 选择“完成”。

1. Spark 项目会自动创建一个项目。 若要查看项目，请执行以下操作：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“文件”菜单中，选择“项目结构”。

   b. 在“项目结构”对话框中，选择“项目”查看创建的默认项目。 也可以选择加号 (**+**) 图标创建自己的项目。

      ![对话框中的项目信息](./media/apache-spark-intellij-tool-plugin/default-artifact.png)
      
1. 执行以下操作来添加应用程序源代码：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“项目资源管理器”中，右键单击“src”，指向“新建”，并选择“Scala 类”。
      
      ![项目资源管理器中用于创建 Scala 类的命令](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   b. 在“新建 Scala 类”对话框中提供名称，在“种类”对话框中选择“对象”，并选择“确定”。
      
      ![“新建 Scala 类”对话框](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

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

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 群集中运行 Spark Scala 应用程序
创建 Scala 应用程序后，可将其提交到群集。

1. 在项目资源管理器中，找到 Java 或 Scala 文件，然后在右键单击菜单中选择“将 Spark 应用程序提交到 HDInsight”。
    
      ![“将 Spark 应用程序提交到 HDInsight”命令](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. 在配置对话框窗口中，提供以下值，然后单击“SparkJobRun”。

      ![“Spark 提交”对话框](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)
      
    * 对于“Spark 群集(仅限 Linux)”，选择要在其上运行应用程序的 HDInsight Spark 群集。

    * 从 IntelliJ 项目或硬盘中选择一个项目。

    * **主类名**字段：默认值是所选文件中的主类。 可选择省略号 (...) 并选择其他类来更改类。   

    * **作业配置**字段：默认值设置如上图所示。 可更改值或为作业提交添加新的键/值。 有关详细信息：[Apache Livy REST API](http://livy.incubator.apache.org./docs/latest/rest-api.html)

      ![Spark 提交对话框作业配置含义](./media/apache-spark-intellij-tool-plugin/submit-job-configurations.png)

    * **命令行参数**字段：如有需要，可为主类输入按空格分隔的参数值。

    * **引用的 Jars** 和**引用的文件**字段：可输入引用的 Jars 和文件的路径（如果有）。 有关详细信息，请参阅 [Spark 配置](https://spark.apache.org/docs/latest/configuration.html#runtime-environment) 

      ![Spark 提交对话框 jar 文件含义](./media/apache-spark-intellij-tool-plugin/jar-files-meaning.png)

       > [!NOTE]
       > 若要上传引用的 JAR 和引用的文件，请参阅：[如何将资源上传到群集](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)
                         
    * **上传路径**：可指示 Jar 或 Scala 项目资源提交的存储位置。 支持三种存储类型：Azure Blob、使用 Spark 交互式会话上传项目，以及使用群集默认存储帐户。 下面的屏幕截图是 Azure Blob 的示例。

        ![“Spark 提交”对话框](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-types.png)

        ![“Spark 提交”对话框](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-upload-storage-blob.png)

3. 单击“SparkJobRun”将项目提交到所选群集。 “群集中的远程 Spark 作业”选项卡在底部显示作业执行进度。 可通过单击红色按钮来停止应用程序。 若要了解如何访问作业输出，请参阅本文稍后的“使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集”部分。
      
     ![“Spark 提交”窗口](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)


## <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>本地或远程调试 HDInsight 群集上的 Spark 应用程序 
我们还建议以另一种方式将 Spark 应用程序提交到群集。 为此，可在“运行/调试配置”IDE 中设置参数。 有关详细信息，请参阅[使用用于 IntelliJ 的 Azure 工具包通过 SSH 本地或远程调试 HDInsight 群集上的 Spark 应用程序](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh)。



## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集
可以使用用于 IntelliJ 的 Azure 工具包执行各种操作。

### <a name="access-the-job-view"></a>访问作业视图
1. 在“Azure 资源管理器”中依次展开“HDInsight”和 Spark 群集名称，并选择“作业”。  

    ![“作业视图”节点](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

1. 在右窗格中，“Spark 作业视图”选项卡显示了群集上运行的所有应用程序。 选择想要查看其详细信息的应用程序的名称。

    ![应用程序详细信息](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)
    >注意
    >

1. 若要显示正在运行的作业的基本信息，请将鼠标悬停在作业图上。 若要查看每个作业生成的阶段图和信息，请在作业图中选择一个节点。

    ![作业阶段详细信息](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

1. 若要查看常用的日志，例如“驱动程序 Stderr”、“驱动程序 Stdout”和“目录信息”，请选择“日志”选项卡。

    ![日志详细信息](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

1. 还可以选择窗口顶部的相应链接查看 Spark 历史记录 UI 和 YARN UI（应用程序级别）。

### <a name="access-the-spark-history-server"></a>访问 Spark 历史记录服务器
1. 在“Azure 资源管理器”中，展开“HDInsight”，右键单击 Spark 群集名称，并选择“打开 Spark 历史记录 UI”。 

1. 出现提示时，请输入群集的管理员凭据（在设置群集时已指定）。

1. 在“Spark 历史记录服务器”仪表板上，可以使用应用程序名称查找刚运行完的应用程序。 在上述代码中，已使用 `val conf = new SparkConf().setAppName("MyClusterApp")` 设置了应用程序名称。 因此，Spark 应用程序名称为 **MyClusterApp**。

### <a name="start-the-ambari-portal"></a>启动 Ambari 门户
1. 在“Azure 资源管理器”中，展开“HDInsight”，右键单击 Spark 群集名称，并选择“打开群集管理门户(Ambari)”。 

1. 出现提示时，请输入群集的管理员凭据。 在群集设置过程中已指定这些凭据。

### <a name="manage-azure-subscriptions"></a>管理 Azure 订阅
默认情况下，用于 IntelliJ 的 Azure 工具包将列出所有 Azure 订阅中的 Spark 群集。 如果需要，可以指定想要访问的订阅。 

1. 在“Azure 资源管理器”中，右键单击“Azure”根节点，并选择“管理订阅”。 

1. 在对话框中，清除不想访问的订阅旁边的复选框，并选择“关闭”。 如果想要从 Azure 订阅注销，可以选择“注销”。

## <a name="spark-console"></a>Spark Console
可运行 Spark Local Console(Scala) 或运行 Spark Livy Interactive Session Console(Scala)。

### <a name="spark-local-consolescala"></a>Spark Local Console(Scala)
1. 需手动添加依赖项。 在菜单中，依次选择“文件”->“项目结构”->“项目设置”->“库”，然后单击 +，选择“从 Maven...”。然后在弹出式对话框中输入 org.apache.spark:spark-repl_2.11:2.1.0。 在库中添加依赖项之后，需要将依赖项移动到“项目结构”窗口的“模块”中的第一行。 在移动之前，将“范围”更改为“运行时”。

    ![Local Console 添加依赖项库](./media/apache-spark-intellij-tool-plugin/local-console-add-dependency-libraries.png)

    ![Local Console 移动到第一行](./media/apache-spark-intellij-tool-plugin/local-console-move-first-line.png)

2. 如果之前未设置配置，请设置配置。 在“运行/调试配置”窗口中，单击 +->“Azure HDInsight Spark”，选择“本地运行”选项卡，并选择主类，然后单击“确定”。

    ![Local Console 设置配置](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)
 
3. 打开与主类文件对应的文件，右键单击“Spark Console”，然后单击“运行 Spark Local Console(Scala)”。 或转到“工具”菜单->“Spark Console”->“运行 Spark Local Console(Scala)”以启动控制台。

    ![Spark Local 入口点](./media/apache-spark-intellij-tool-plugin/spark-console-local-entry-script.png)

4. 成功启动 Local Console 后。 外观如下所示。 你可执行所需的操作。 例如，输入 sc.appName，按 Ctrl+Enter，系统将显示结果。

    ![Local Console 结果](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interactive Session Console(Scala)
仅可用于 IntelliJ 2018.2。

1. 如果之前未设置配置，请设置配置。 在“运行/调试配置”窗口中，单击+->“Azure HDInsight Spark”，选择“在群集中远程运行”选项卡，选择群集名和主类，然后单击“确定”。

    ![Interactive Console 添加配置输入](./media/apache-spark-intellij-tool-plugin/interactive-console-add-config-entry.png)

    ![Interactive Console 设置配置](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

2. 打开与主类对应的文件，右键单击“Spark 控制台”，然后单击“运行 Spark Livy Interactive Session Console(Scala)”。 或转到“工具”菜单，然后依次单击“Spark Console”和“运行 Spark Livy Interactive Session Console(Scala)”以启动控制台。

3. 成功启动控制台后，可执行所需的操作。 例如，输入 sc.appName，按 Ctrl+Enter，系统将显示结果。

    ![Interactive Console 结果](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>转换现有 IntelliJ IDEA 应用程序以使用用于 IntelliJ 的 Azure 工具包
可以转换在 IntelliJ IDEA 中创建的现有 Spark Scala 应用程序，使其与用于 IntelliJ 的 Azure 工具包兼容。 然后，可以使用该插件将应用程序提交到 HDInsight Spark 群集。

1. 对于通过 IntelliJ IDEA 创建的现有 Spark Scala 应用程序，打开关联的 .iml 文件。

1. 在根级别有一个 **module** 元素，如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   编辑该元素以添加 `UniqueKey="HDInsightTool"`，使 **module** 元素如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. 保存更改。 现在，应用程序应与用于 IntelliJ 的 Azure 工具包兼容。 可以通过右键单击项目资源管理器中的项目名称来测试此项。 弹出菜单现在将包含选项“将 Spark 应用程序提交到 HDInsight”。

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

![将选项添加到 IntelliJ 中的“VM 选项”对话框](./media/apache-spark-intellij-tool-plugin/change-heap-size.png)

## <a name="faq"></a>常见问题解答
链接群集时，建议提供存储的凭据。

![链接群集, 提供存储凭据](./media/apache-spark-intellij-tool-plugin/link-cluster-with-storage-credential-intellij.png)

可通过两种模式提交作业。 如果提供存储凭据，则将使用批处理模式提交作业。 否则，将使用交互模式。 如果群集正忙，可能会收到以下错误。

![Intellij 在群集忙时收到错误](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij 在群集忙时收到错误](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>反馈和已知问题
目前不支持直接查看 Spark 输出。

如有任何建议或反馈，或使用此插件时遇到任何问题，请向 hdivstool@microsoft.com 发送电子邮件。

## <a name="seealso"></a>后续步骤
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>演示
* 创建 Scala 项目（视频）：[Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Spark Scala 应用程序）
* 远程调试（视频）：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用用于 IntelliJ 的 Azure 工具包远程调试 HDInsight 群集上的 Spark 应用程序）

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
