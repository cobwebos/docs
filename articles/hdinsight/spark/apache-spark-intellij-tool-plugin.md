---
title: 'Azure Toolkit for IntelliJ：为 HDInsight 群集创建 Spark 应用程序 '
description: 使用用于 IntelliJ 的 Azure 工具包开发以 Scala 编写的 Spark 应用程序，并将其提交到 HDInsight Spark 群集。
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: maxluk
ms.openlocfilehash: 8fa3cd79011ab31349ec44edf52b8fd9048d0d37
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077966"
---
# <a name="use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>使用 Azure Toolkit for IntelliJ 为 HDInsight 群集创建 Apache Spark 应用程序

使用用于 IntelliJ 的 Azure 工具包插件开发以  [Scala](https://www.scala-lang.org/) 编写的 [Apache Spark](https://spark.apache.org/) 应用程序，并直接从 IntelliJ 集成开发环境 (IDE) 将其提交到 HDInsight Spark 群集。 可按多种方式使用该插件：

* 在 HDInsight Spark 群集中开发和提交 Scala Spark 应用程序。
* 访问 Azure HDInsight Spark 群集资源。
* 本地开发和运行 Scala Spark 应用程序。

## <a name="prerequisites"></a>必备组件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
* [Oracle Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。  本教程使用 Java 版本 8.0.202。
* IntelliJ IDEA。 本文使用 [IntelliJ IDEA Community 版本2018.3.4](https://www.jetbrains.com/idea/download/)。
* Azure Toolkit for IntelliJ。  请参阅[安装 Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)。
* WINUTILS.EXE。  请参阅[在 Windows 上运行 Hadoop 时遇到的问题](https://wiki.apache.org/hadoop/WindowsProblems)。

## <a name="install-scala-plugin-for-intellij-idea"></a>安装适用于 IntelliJ IDEA 的 Scala 插件
执行以下步骤安装 Scala 插件：

1. 打开 IntelliJ IDEA。

2. 在欢迎屏幕上，导航到“配置” > “插件”打开“插件”窗口。
   
    ![启用 scala 插件](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin.png)

3. 选择在新窗口中作为特色功能列出的 Scala 插件对应的“安装”。  

    ![安装 scala 插件](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. 成功安装该插件后，必须重启 IDE。


## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>为 HDInsight Spark 群集创建 Spark Scala 应用程序

1. 启动 IntelliJ IDEA，选择“创建新项目”打开“新建项目”窗口。

2. 在左窗格中选择“Azure Spark/HDInsight”。

3. 在主窗口中选择“Spark 项目(Scala)”。

4. 在“生成工具”下拉列表中选择下列其中一项：
   * **Maven**：支持 Scala 项目创建向导。
   * **SBT**：用于管理依赖项和生成 Scala 项目。

     ![“新建项目”对话框](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. 选择“**下一步**”。

6. 在“新建项目”窗口中提供以下信息：  

    |  属性   | 描述   |  
    | ----- | ----- |  
    |项目名称| 输入名称。  本教程使用 `myApp`。|  
    |项目位置&nbsp;| 输入所需的位置用于保存项目。|
    |项目 SDK| 首次使用 IDEA 时，此字段可能是空白的。  选择“新建...”并导航到 JDK。|
    |Spark 版本|创建向导集成了适当版本的 Spark SDK 和 Scala SDK。 如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”。 否则，请选择“Spark 2.x”。 本示例使用“Spark 2.3.0 (Scala 2.11.8)”。|

    ![选择 Spark SDK](./media/apache-spark-intellij-tool-plugin/hdi-new-project.png)

7. 选择“完成”。  可能需要在几分钟后才会显示该项目。

8. Spark 项目会自动创建一个项目。 若要查看项目，请执行以下操作：

   a. 在菜单栏中，导航到“文件” > “项目结构...”。

   b. 在“项目结构”窗口中选择“项目”。  

   c. 查看项目后选择“取消”。

      ![对话框中的项目信息](./media/apache-spark-intellij-tool-plugin/default-artifact.png)

9. 执行以下操作来添加应用程序源代码：

    a. 在“项目”中，导航到“myApp” > “src” > “main” > “scala”。  

    b. 右键单击“scala”，然后导航到“新建” > “Scala 类”。

   ![用于在项目中创建 Scala 类的命令](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. 在“新建 Scala 类”对话框中提供名称，在“种类”下拉列表中选择“对象”，然后选择“确定”。

     ![“新建 Scala 类”对话框](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. 主视图中随后会打开 **myApp.scala** 文件。 将默认代码替换为以下代码：  

        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
    
        object myApp{
            def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("myApp")
            val sc = new SparkContext(conf)
    
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    
            //find the rows that have only one digit in the seventh column in the CSV file
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
    
            rdd1.saveAsTextFile("wasbs:///HVACOut")
            }
    
        }

    此代码从 HVAC.csv（用于所有 HDInsight Spark 群集）中读取数据，检索在 CSV 的第七列中只有一个数字的行，并将输出写入群集的默认存储容器下的 `/HVACOut`。

## <a name="connect-to-your-hdinsight-cluster"></a>连接到 HDInsight 群集
用户可以[登录到 Azure 订阅](#sign-in-to-your-azure-subscription)，或者通过使用 Ambari 用户名/密码或已加入域的凭据[链接 HDInsight 群集](#link-a-cluster)来连接到 HDInsight 群集。

### <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅

1. 在菜单栏中，导航到“视图” > “工具窗口” > “Azure 资源管理器”。
       
   ![“Azure 资源管理器”链接](./media/apache-spark-intellij-tool-plugin/show-azure-explorer.png)

2. 在 Azure 资源管理器中右键单击“Azure”节点，然后选择“登录”。

3. 在“Azure 登录”对话框中，选择“登录”并输入 Azure 凭据。

    ![“Azure 登录”对话框](./media/apache-spark-intellij-tool-plugin/view-explorer-2.png)

4. 登录之后，“选择订阅”对话框会列出与凭据关联的所有 Azure 订阅。 选择你的订阅，然后选择“选择”按钮。

    ![“选择订阅”对话框](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

5. 在“Azure 资源管理器”中展开“HDInsight”，查看订阅中的 HDInsight Spark 群集。

    ![Azure 资源管理器中的 HDInsight Spark 群集](./media/apache-spark-intellij-tool-plugin/view-explorer-3.png)

6. 若要查看与群集关联的资源（例如存储帐户），可以进一步展开群集名称节点。

    ![展开的群集名称节点](./media/apache-spark-intellij-tool-plugin/view-explorer-4.png)

### <a name="link-a-cluster"></a>链接群集
可以使用 Apache Ambari 管理的用户名链接 HDInsight 群集。 同样，对于已加入域的 HDInsight 群集，也可使用这种域和用户名（例如 user1@contoso.com）进行链接。 也可链接 Livy 服务群集。

1. 在菜单栏中，导航到“视图” > “工具窗口” > “Azure 资源管理器”。

2. 在 Azure 资源管理器中右键单击“HDInsight”节点，然后选择“链接群集”。

   ![链接群集上下文菜单](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

3. “链接群集”窗口中提供的选项根据在“链接资源类型”下拉列表中选择的值而异。  请输入自己的值，然后选择“确定”。

    * **HDInsight 群集**。  
  
        |属性 |值 |
        |----|----|
        |链接资源类型|从下拉列表中选择“HDInsight 群集”。|
        |群集名称/URL| 输入群集名称。|
        |身份验证类型| 保留“基本身份验证”|
        |用户名| 输入群集用户名，默认为 admin。|
        |密码| 输入用户名的密码。|
    
        ![链接 hdinsight 群集对话框](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy 服务**  
  
        |属性 |值 |
        |----|----|
        |链接资源类型|从下拉列表中选择“Livy 服务”。|
        |Livy 终结点| 输入 Livy 终结点|
        |群集名称| 输入群集名称。|
        |Yarn 终结点|可选。|
        |身份验证类型| 保留“基本身份验证”|
        |用户名| 输入群集用户名，默认为 admin。|
        |密码| 输入用户名的密码。|

        ![链接 livy 群集对话框](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

4. 在“HDInsight”节点中可以看到链接的群集。

   ![链接的群集](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

5. 还可以从 **Azure 资源管理器**取消链接群集。

   ![取消链接的群集](./media/apache-spark-intellij-tool-plugin/unlink.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 群集中运行 Spark Scala 应用程序
创建 Scala 应用程序后，可将其提交到群集。

1. 在“项目”中，导航到“myApp” > “src” > “main” > “scala” > “myApp”。  右键单击“myApp”，然后选择“提交 Spark 应用程序”（可能位于列表底部）。
    
      ![“将 Spark 应用程序提交到 HDInsight”命令](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. 在“提交 Spark 应用程序”对话框窗口中，选择“1.Spark on HDInsight”。

3. 在“编辑配置”窗口中提供以下值，然后选择“确定”：

    |属性 |值 |
    |----|----|
    |Spark 群集（仅限 Linux）|选择要在其上运行应用程序的 HDInsight Spark 群集。|
    |选择要提交的项目|保留默认设置。|
    |Main 类名|默认值是所选文件中的主类。 可选择省略号 (...) 并选择其他类来更改类。|
    |作业配置|可以更改默认的键和/或值。 有关详细信息，请参阅 [Apache Livy REST API](https://livy.incubator.apache.org./docs/latest/rest-api.html)。|
    |命令行参数|如果需要，可为 main 类输入参数并以空格分隔。|
    |引用的 Jar 和引用的文件|（如果有的话）可以输入引用的 Jar 和文件的路径。 更多相关信息：[Apache Spark 配置](https://spark.apache.org/docs/latest/configuration.html#runtime-environment)。  另请参阅[如何将资源上传到群集](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)。|
    |作业上传存储|展开以显示其他选项。|
    |存储类型|从下拉列表中选择“使用 Azure Blob 上传”。|
    |存储帐户|输入存储帐户。|
    |存储密钥|输入存储密钥。|
    |存储容器|输入“存储帐户”和“存储密钥”后，从下拉列表中选择你的存储容器。|

    ![“Spark 提交”对话框](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-2.png)

4. 选择“SparkJobRun”将项目提交到所选群集。 “群集中的远程 Spark 作业”选项卡在底部显示作业执行进度。 可通过单击红色按钮来停止应用程序。 若要了解如何访问作业输出，请参阅本文稍后的“使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集”部分。  
      
    ![“Spark 提交”窗口](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>本地或远程调试 HDInsight 群集上的 Apache Spark 应用程序 
我们还建议以另一种方式将 Spark 应用程序提交到群集。 为此，可在“运行/调试配置”IDE 中设置参数。 有关详细信息，请参阅[使用用于 IntelliJ 的 Azure 工具包通过 SSH 本地或远程调试 HDInsight 群集上的 Apache Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md)。

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集
可以使用用于 IntelliJ 的 Azure 工具包执行各种操作。  大多数操作都是从“Azure 资源管理器”发起的。  在菜单栏中，导航到“视图” > “工具窗口” > “Azure 资源管理器”。

### <a name="access-the-job-view"></a>访问作业视图

1. 在 Azure 资源管理器中，导航到“HDInsight” > “\<你的群集>”>“作业”。

    ![“作业视图”节点](./media/apache-spark-intellij-tool-plugin/job-view-node.png)

2. 在右窗格中，“Spark 作业视图”选项卡显示了群集上运行的所有应用程序。 选择想要查看其详细信息的应用程序的名称。

    ![应用程序详细信息](./media/apache-spark-intellij-tool-plugin/view-job-logs.png)

3. 若要显示正在运行的作业的基本信息，请将鼠标悬停在作业图上。 若要查看每个作业生成的阶段图和信息，请在作业图中选择一个节点。

    ![作业阶段详细信息](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. 若要查看常用的日志，例如“驱动程序 Stderr”、“驱动程序 Stdout”和“目录信息”，请选择“日志”选项卡。

    ![日志详细信息](./media/apache-spark-intellij-tool-plugin/Job-log-info.png)

5. 还可以选择窗口顶部的相应链接查看 Spark 历史记录 UI 和 YARN UI（应用程序级别）。

### <a name="access-the-spark-history-server"></a>访问 Spark 历史记录服务器

1. 在 Azure 资源管理器中展开“HDInsight”，右键单击 Spark 群集名称，然后选择“打开 Spark 历史记录 UI”。  
2. 出现提示时，请输入群集的管理员凭据（在设置群集时已指定）。

3. 在“Spark 历史记录服务器”仪表板上，可以使用应用程序名称查找刚运行完的应用程序。 在上述代码中，已使用 `val conf = new SparkConf().setAppName("myApp")` 设置了应用程序名称。 因此，Spark 应用程序名为 **myApp**。

### <a name="start-the-ambari-portal"></a>启动 Ambari 门户

1. 在“Azure 资源管理器”中展开“HDInsight”，右键单击 Spark 群集名称，然后选择“打开群集管理门户(Ambari)”。  

2. 出现提示时，请输入群集的管理员凭据。 在群集设置过程中已指定这些凭据。

### <a name="manage-azure-subscriptions"></a>管理 Azure 订阅
默认情况下，用于 IntelliJ 的 Azure 工具包将列出所有 Azure 订阅中的 Spark 群集。 如果需要，可以指定想要访问的订阅。  

1. 在 Azure 资源管理器中右键单击“Azure”根节点，然后选择“选择订阅”。  

2. 在“选择订阅”窗口中，清除不想要访问的订阅旁边的复选框，然后选择“关闭”。

## <a name="spark-console"></a>Spark Console
可运行 Spark Local Console(Scala) 或运行 Spark Livy Interactive Session Console(Scala)。

### <a name="spark-local-consolescala"></a>Spark Local Console(Scala)
确保满足 WINUTILS.EXE 先决条件。

1. 在菜单栏中，导航到“运行” > “编辑配置...”。

2. 在“运行/调试配置”窗口中的左窗格内，导航到“HDInsight 上的 Apache Spark” > “[HDInsight 上的 Spark] myApp”。

3. 在主窗口中，选择“本地运行”选项卡。

4. 提供以下值，然后选择“确定”：

    |属性 |值 |
    |----|----|
    |作业 main 类|默认值是所选文件中的主类。 可选择省略号 (...) 并选择其他类来更改类。|
    |环境变量|确保 HADOOP_HOME 的值正确。|
    |WINUTILS.exe 位置|确保路径正确。|

    ![Local Console 设置配置](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. 在“项目”中，导航到“myApp” > “src” > “main” > “scala” > “myApp”。  

6. 在菜单栏中，导航到“工具” > “Spark 控制台” > “运行 Spark 本地控制台(Scala)”。

7. 此时可能会显示两个对话框，询问是否要自动修复依赖项。 如果已显示，请选择“自动修复”。

    ![Spark 自动修复1](./media/apache-spark-intellij-tool-plugin/console-auto-fix1.png)

    ![Spark 自动修复2](./media/apache-spark-intellij-tool-plugin/console-auto-fix2.png)

8. 控制台应如下图所示。 在控制台窗口中键入 `sc.appName`，然后按 Ctrl+Enter。  随后将显示结果。 可以通过单击红色按钮来终止 Local Console。

    ![Local Console 结果](./media/apache-spark-intellij-tool-plugin/local-console-result.png)


### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy Interactive Session Console(Scala)
它仅在 IntelliJ 2018.2 和 2018.3 上受支持。

1. 在菜单栏中，导航到“运行” > “编辑配置...”。

2. 在“运行/调试配置”窗口中的左窗格内，导航到“HDInsight 上的 Apache Spark” > “[HDInsight 上的 Spark] myApp”。

3. 在主窗口中，选择“在群集中远程运行”选项卡。

4. 提供以下值，然后选择“确定”：

    |属性 |值 |
    |----|----|
    |Spark 群集（仅限 Linux）|选择要在其上运行应用程序的 HDInsight Spark 群集。|
    |Main 类名|默认值是所选文件中的主类。 可选择省略号 (...) 并选择其他类来更改类。|

    ![Interactive Console 设置配置](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. 在“项目”中，导航到“myApp” > “src” > “main” > “scala” > “myApp”。  

6. 在菜单栏中，导航到“工具” > “Spark 控制台” > “运行 Spark Livy 交互式会话控制台(Scala)”。

7. 控制台应如下图所示。 在控制台窗口中键入 `sc.appName`，然后按 Ctrl+Enter。  随后将显示结果。 可以通过单击红色按钮来终止 Local Console。

    ![Interactive Console 结果](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>将选定内容发送到 Spark Console

可以通过将一些代码发送到 Local Console 或 Livy Interactive Session Console (Scala) 来方便地预测脚本结果。 可以突出显示 Scala 文件中的一些代码，然后右键单击“将选定内容发送到 Spark Console”。 选定代码将发送到控制台并执行。 结果将显示在控制台中的代码之后。 如果存在错误，则控制台将检查错误。  

   ![将选定内容发送到 Spark Console](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>转换现有 IntelliJ IDEA 应用程序以使用用于 IntelliJ 的 Azure 工具包

可以转换在 IntelliJ IDEA 中创建的现有 Spark Scala 应用程序，使其与用于 IntelliJ 的 Azure 工具包兼容。 然后，可以使用该插件将应用程序提交到 HDInsight Spark 群集。

1. 对于通过 IntelliJ IDEA 创建的现有 Spark Scala 应用程序，打开关联的 .iml 文件。

1. 在根级别有一个 **module** 元素，如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">

   编辑该元素以添加 `UniqueKey="HDInsightTool"`，使 **module** 元素如下所示：
   
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">

1. 保存更改。 现在，应用程序应与用于 IntelliJ 的 Azure 工具包兼容。 可以通过右键单击“项目”中的项目名称来测试此项。 弹出菜单现在将包含选项“将 Spark 应用程序提交到 HDInsight”。

## <a name="troubleshooting"></a>故障排除

### <a name="error-in-local-run-use-a-larger-heap-size"></a>本地运行出错：“请使用更大的堆大小”
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
如果群集正忙，可能会收到以下错误。

![Intellij 在群集忙时收到错误](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-upload.png)

![Intellij 在群集忙时收到错误](./media/apache-spark-intellij-tool-plugin/intellij-interactive-cluster-busy-submit.png)

## <a name="feedback-and-known-issues"></a>反馈和已知问题
目前不支持直接查看 Spark 输出。

如有任何建议或反馈，或使用此插件时遇到任何问题，请向 hdivstool@microsoft.com 发送电子邮件。

## <a name="seealso"></a>后续步骤
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>演示
* 创建 Scala 项目（视频）：[Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Apache Spark Scala 应用程序）
* 远程调试（视频）：[使用 Azure Toolkit for IntelliJ 远程调试 HDInsight 群集上的 Apache Spark 应用程序](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>方案
* [Apache Spark 与 BI：将 HDInsight 中的 Spark 与 BI 工具配合使用来执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 来通过 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检验结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Apache Spark 应用程序](apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
