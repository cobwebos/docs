---
title: 教程-Azure Toolkit for IntelliJ： Spark 应用-HDInsight
description: 教程 - 使用 Azure Toolkit for IntelliJ 开发以 Scala 编写的 Spark 应用程序，并将其提交到 HDInsight Spark 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 09/04/2019
ms.openlocfilehash: 2631a0906a0f0886bdc106f1afef99860a6fe00b
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381635"
---
# <a name="tutorial-use-azure-toolkit-for-intellij-to-create-apache-spark-applications-for-hdinsight-cluster"></a>教程：使用 Azure Toolkit for IntelliJ 为 HDInsight 群集创建 Apache Spark 应用程序

本教程演示如何使用适用于 IntelliJ IDE 的**Azure 工具包**插件在 azure HDInsight 上开发 Apache Spark 应用程序。 [Azure HDInsight](../hdinsight-overview.md)是云中托管的开源分析服务，可以使用 Hadoop、Apache Spark、Apache Hive 和 Apache Kafka 等开源框架。

可以通过以下几种方式使用**Azure 工具包**插件：

* 开发 Scala Spark 应用程序并将其提交到 HDInsight Spark 群集。
* 访问 Azure HDInsight Spark 群集资源。
* 本地开发和运行 Scala Spark 应用程序。

在本教程中，你将了解如何执行以下操作：
> [!div class="checklist"]
> * 使用 Azure Toolkit for IntelliJ 插件
> * 开发 Apache Spark 应用程序
> * 将应用程序提交到 Azure HDInsight 群集

## <a name="prerequisites"></a>必备条件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。

* [Oracle Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。  本教程使用 Java 版本 8.0.202。

* IntelliJ IDEA。 本文使用[INTELLIJ 创意社区版。 2018.3.4](https://www.jetbrains.com/idea/download/)。

* Azure Toolkit for IntelliJ。  请参阅[安装 Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)。

## <a name="install-scala-plugin-for-intellij-idea"></a>安装适用于 IntelliJ IDEA 的 Scala 插件

执行以下步骤安装 Scala 插件：

1. 打开 IntelliJ IDEA。

2. 在欢迎屏幕上，导航到“配置” **“插件”打开“插件”窗口。**  > 

    ![IntelliJ IDEA 启用 scala 插件](./media/apache-spark-intellij-tool-plugin/enable-scala-plugin1.png)

3. 选择在新窗口中作为特色功能列出的 Scala 插件对应的“安装”。  

    ![IntelliJ IDEA 安装 scala 插件](./media/apache-spark-intellij-tool-plugin/install-scala-plugin.png)

4. 成功安装该插件后，必须重启 IDE。

## <a name="create-a-spark-scala-application-for-an-hdinsight-spark-cluster"></a>为 HDInsight Spark 群集创建 Spark Scala 应用程序

1. 启动 IntelliJ IDEA，选择“创建新项目”打开“新建项目”窗口。

2. 在左窗格中选择“Azure Spark/HDInsight”。

3. 在主窗口中选择“Spark 项目(Scala)”。

4. 在“生成工具”下拉列表中选择下列其中一项：
   * **Maven**：支持 Scala 项目创建向导。
   * **SBT**：用于管理依赖项和生成 Scala 项目。

     ![IntelliJ IDEA“新建项目”对话框](./media/apache-spark-intellij-tool-plugin/create-hdi-scala-app.png)

5. 选择“**下一页**”。

6. 在“新建项目”窗口中提供以下信息：  

    |  properties   | 说明   |  
    | ----- | ----- |  
    |项目名称| 输入名称。  本教程使用 `myApp`。|  
    |项目位置&nbsp;| 输入所需的位置用于保存项目。|
    |项目 SDK| 首次使用 IDEA 时，此字段可能是空白的。  选择“新建...”并导航到 JDK。|
    |Spark 版本|创建向导集成了适当版本的 Spark SDK 和 Scala SDK。 如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”。 否则，请选择“Spark 2.x”。 本示例使用“Spark 2.3.0 (Scala 2.11.8)”。|

    ![选择 Apache Spark SDK](./media/apache-spark-intellij-tool-plugin/intellij-new-project.png)

7. 选择“完成”。  可能需要在几分钟后才会显示该项目。

8. Spark 项目自动为你创建项目。 若要查看项目，请执行以下操作：

   a. 在菜单栏中，导航到“文件” **“项目结构...”。**  > 

   b. 在“项目结构”窗口中选择“项目”。  

   c. 查看项目后选择“取消”。

      ![对话框中的项目信息](./media/apache-spark-intellij-tool-plugin/default-artifact-dialog.png)

9. 执行以下操作来添加应用程序源代码：

    a. 在“项目”中，导航到“myApp” **“src”** “main” > “scala”。 >  >   

    b. 右键单击“scala”，然后导航到“新建” **“Scala 类”。**  > 

   ![用于在项目中创建 Scala 类的命令](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code.png)

   c. 在“新建 Scala 类”对话框中提供名称，在“种类”下拉列表中选择“对象”，然后选择“确定”。

     ![“新建 Scala 类”对话框](./media/apache-spark-intellij-tool-plugin/hdi-spark-scala-code-object.png)

   d. 主视图中随后会打开 **myApp.scala** 文件。 将默认代码替换为以下代码：  

        ```scala
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
        ```

    此代码从 HVAC.csv（用于所有 HDInsight Spark 群集）中读取数据，检索在 CSV 的第七列中只有一个数字的行，并将输出写入群集的默认存储容器下的 `/HVACOut`。

## <a name="connect-to-your-hdinsight-cluster"></a>连接到 HDInsight 群集

用户可以[登录到 Azure 订阅](#sign-in-to-your-azure-subscription)，或者通过使用 Ambari 用户名/密码或已加入域的凭据[链接 HDInsight 群集](#link-a-cluster)来连接到 HDInsight 群集。

### <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅

1. 在菜单栏中，导航到“视图” **“工具窗口”** “Azure 资源管理器”。 >  > 

   ![IntelliJ IDEA 显示 Azure 资源管理器](./media/apache-spark-intellij-tool-plugin/show-azure-explorer1.png)

2. 在 Azure 资源管理器中右键单击“Azure”节点，然后选择“登录”。

   ![在 IntelliJ IDEA 资源管理器中右键单击“Azure”](./media/apache-spark-intellij-tool-plugin/explorer-rightclick-azure.png)

3. 在“Azure 登录”对话框中，依次选择“设备登录”、“登录”。

    ![IntelliJ IDEA Azure 登录设备登录](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer2.png)

4. 在“Azure 设备登录”对话框中单击“复制并打开”。

   ![IntelliJ IDEA Azure 设备登录](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer5.png)

5. 在浏览器界面中粘贴代码，然后单击“下一步”。

   ![Microsoft HDI 输入代码对话框](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer6.png)

6. 输入 Azure 凭据，然后关闭浏览器。

   ![Microsoft HDI 输入电子邮件对话框](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer7.png)

7. 登录之后，“选择订阅”对话框会列出与凭据关联的所有 Azure 订阅。 选择你的订阅，然后选择“选择”按钮。

    ![“选择订阅”对话框](./media/apache-spark-intellij-tool-plugin/Select-Subscriptions.png)

8. 在“Azure 资源管理器”中展开“HDInsight”，查看订阅中的 HDInsight Spark 群集。

    ![IntelliJ IDEA Azure 资源管理器主视图](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer3.png)

9. 若要查看与群集关联的资源（例如存储帐户），可以进一步展开群集名称节点。

    ![Azure 资源管理器存储帐户](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer4.png)

### <a name="link-a-cluster"></a>链接群集

可以使用 Apache Ambari 管理的用户名链接 HDInsight 群集。 同样，对于已加入域的 HDInsight 群集，也可使用这种域和用户名（例如 `user1@contoso.com`）进行链接。 也可链接 Livy 服务群集。

1. 在菜单栏中，导航到“视图” **“工具窗口”** “Azure 资源管理器”。 >  > 

1. 在 Azure 资源管理器中右键单击“HDInsight”节点，然后选择“链接群集”。

   ![Azure 资源管理器链接群集上下文菜单](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. “链接群集”窗口中提供的选项根据在“链接资源类型”下拉列表中选择的值而异。  请输入自己的值，然后选择“确定”。

    * **HDInsight 群集**。  
  
        |properties |值 |
        |----|----|
        |链接资源类型|从下拉列表中选择“HDInsight 群集”。|
        |群集名称/URL| 输入群集名称。|
        |身份验证类型| 保留“基本身份验证”|
        |用户名| 输入群集用户名，默认为 admin。|
        |密码| 输入用户名的密码。|

        ![IntelliJ IDEA 链接群集对话框](./media/apache-spark-intellij-tool-plugin/link-hdinsight-cluster-dialog.png)

    * **Livy 服务**  
  
        |properties |值 |
        |----|----|
        |链接资源类型|从下拉列表中选择“Livy 服务”。|
        |Livy 终结点| 输入 Livy 终结点|
        |群集名称| 输入群集名称。|
        |Yarn 终结点|可选。|
        |身份验证类型| 保留“基本身份验证”|
        |用户名| 输入群集用户名，默认为 admin。|
        |密码| 输入用户名的密码。|

        ![IntelliJ IDEA 链接 Livy 群集对话框](./media/apache-spark-intellij-tool-plugin/link-livy-cluster-dialog.png)

1. 在“HDInsight”节点中可以看到链接的群集。

   ![Azure 资源管理器链接群集 1](./media/apache-spark-intellij-tool-plugin/hdinsight-linked-cluster.png)

1. 还可以从 **Azure 资源管理器**取消链接群集。

   ![Azure 资源管理器取消链接群集](./media/apache-spark-intellij-tool-plugin/hdi-unlinked-cluster.png)

## <a name="run-a-spark-scala-application-on-an-hdinsight-spark-cluster"></a>在 HDInsight Spark 群集中运行 Spark Scala 应用程序

创建 Scala 应用程序后，可将其提交到群集。

1. 从项目中，导航到“myApp” **“src”** “main” > “scala” **“myApp”**  >  >  > 。  右键单击“myApp”，然后选择“提交 Spark 应用程序”（可能位于列表底部）。

      ![“将 Spark 应用程序提交到 HDInsight”命令](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-1.png)

2. 在 "**提交 Spark 应用程序**" 对话框窗口中，选择 " **1"。HDInsight 上的 Spark**。

3. 在“编辑配置”窗口中提供以下值，然后选择“确定”：

    |properties |值 |
    |----|----|
    |Spark 群集（仅限 Linux）|选择要在其上运行应用程序的 HDInsight Spark 群集。|
    |选择要提交的项目|保留默认设置。|
    |主类名|默认值是所选文件中的主类。 可以通过选择省略号 ( **...** ) 并选择其他类来更改类。|
    |作业配置|可以更改默认的键和/或值。 有关详细信息，请参阅 [Apache Livy REST API](https://livy.incubator.apache.org/docs/latest/rest-api.html)。|
    |命令行参数|如果需要，可为 main 类输入参数并以空格分隔。|
    |引用的 Jar 和引用的文件|可以输入引用的 Jar 和引用的文件的路径（如果有）。 还可以在 Azure 虚拟文件系统中浏览文件，但目前仅支持 ADLS 第 2 代群集。 有关详细信息，请[Apache Spark 配置](https://spark.apache.org/docs/latest/configuration.html#runtime-environment)。  另请参阅[如何将资源上传到群集](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-storage-explorer)。|
    |作业上传存储|展开以显示其他选项。|
    |存储类型|从下拉列表中选择“使用 Azure Blob 上传”。|
    |存储帐户|输入存储帐户。|
    |存储密钥|输入存储密钥。|
    |存储容器|输入“存储帐户”和“存储密钥”后，从下拉列表中选择你的存储容器。|

    ![“Spark 提交”对话框](./media/apache-spark-intellij-tool-plugin/hdi-submit-spark-app-02.png)

4. 选择“SparkJobRun”将项目提交到所选群集。 底部的“群集中的远程 Spark 任务”选项卡显示作业执行进度。 通过单击红色按钮，即可停止应用程序。 若要了解如何访问作业输出，请参阅本文稍后的“使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集”部分。  

    ![Apache Spark 提交窗口](./media/apache-spark-intellij-tool-plugin/hdi-spark-app-result.png)

## <a name="debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster"></a>本地或远程调试 HDInsight 群集上的 Apache Spark 应用程序

我们还建议以另一种方式将 Spark 应用程序提交到群集。 为此，可在“运行/调试配置”IDE 中设置参数。 有关详细信息，请参阅[使用用于 IntelliJ 的 Azure 工具包通过 SSH 本地或远程调试 HDInsight 群集上的 Apache Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md)。

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-azure-toolkit-for-intellij"></a>使用用于 IntelliJ 的 Azure 工具包访问和管理 HDInsight Spark 群集

可以使用用于 IntelliJ 的 Azure 工具包执行各种操作。  大多数操作都是从“Azure 资源管理器”发起的。  在菜单栏中，导航到“视图” **“工具窗口”** “Azure 资源管理器”。 >  > 

### <a name="access-the-job-view"></a>访问作业视图

1. 在 Azure 资源管理器中，导航到“HDInsight” **“** 你的群集>”>“作业”。 > \<

    ![IntelliJ Azure 资源管理器作业视图节点](./media/apache-spark-intellij-tool-plugin/intellij-job-view-node.png)

2. 在右窗格中，“Spark 作业视图”选项卡显示了群集上运行的所有应用程序。 选择想要查看其详细信息的应用程序的名称。

    ![Spark 作业视图应用程序详细信息](./media/apache-spark-intellij-tool-plugin/intellij-view-job-logs.png)

3. 若要显示正在运行的作业的基本信息，请将鼠标悬停在作业图上。 若要查看每个作业生成的阶段图和信息，请在作业图中选择一个节点。

    ![Spark 作业视图作业阶段详细信息](./media/apache-spark-intellij-tool-plugin/Job-graph-stage-info.png)

4. 若要查看常用的日志，例如“驱动程序 Stderr”、“驱动程序 Stdout”和“目录信息”，请选择“日志”选项卡。

    ![Spark 作业视图日志详细信息](./media/apache-spark-intellij-tool-plugin/intellij-job-log-info.png)

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

## <a name="spark-console"></a>Spark 控制台

可以运行 Spark 本地控制台 (Scala) 或运行 Spark Livy 交互式会话控制台 (Scala)。

### <a name="spark-local-consolescala"></a>Spark 本地控制台 (Scala)

确定自己是否满足 WINUTILS.EXE 先决条件。

1. 从菜单栏中，导航到“运行” **“编辑配置...”**  > 。

2. 在“运行/调试配置”窗口中的左窗格内，导航到“HDInsight 上的 Apache Spark” **“[HDInsight 上的 Spark] myApp”。**  > 

3. 在主窗口中，选择“在本地运行”选项卡。

4. 提供以下值，然后选择“确定”：

    |properties |值 |
    |----|----|
    |作业主类|默认值是所选文件中的主类。 可以通过选择省略号 ( **...** ) 并选择其他类来更改类。|
    |环境变量|请确认 HADOOP_HOME 的值是否正确。|
    |WINUTILS.exe 位置|请确保路径正确。|

    ![本地控制台设置配置](./media/apache-spark-intellij-tool-plugin/console-set-configuration.png)

5. 从项目中，导航到“myApp” **“src”** “main” > “scala” **“myApp”**  >  >  > 。  

6. 从菜单栏中，导航到“工具” **“Spark 控制台”** “运行 Spark 本地控制台(Scala)” >  > 。

7. 然后，系统可能会显示两个对话框，询问你是否要自动修复依赖项。 如果出现对话框，请选择“自动修复”。

    ![IntelliJ IDEA Spark 自动修复对话框 1](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix1.png)

    ![IntelliJ IDEA Spark 自动修复对话框 2](./media/apache-spark-intellij-tool-plugin/intellij-console-autofix2.png)

8. 控制台应如下图所示。 在“控制台”窗口中键入 `sc.appName`，然后按 Ctrl+Enter。  系统将显示结果。 可以通过单击红色按钮终止本地控制台。

    ![IntelliJ IDEA 本地控制台结果](./media/apache-spark-intellij-tool-plugin/local-console-result.png)

### <a name="spark-livy-interactive-session-consolescala"></a>Spark Livy 交互式会话控制台 (Scala)

1. 从菜单栏中，导航到“运行” **“编辑配置...”**  > 。

2. 在“运行/调试配置”窗口中的左窗格内，导航到“HDInsight 上的 Apache Spark” **“[HDInsight 上的 Spark] myApp”。**  > 

3. 在主窗口中，选择“在群集中远程运行”选项卡。

4. 提供以下值，然后选择“确定”：

    |properties |值 |
    |----|----|
    |Spark 群集（仅限 Linux）|选择要在其上运行应用程序的 HDInsight Spark 群集。|
    |主类名|默认值是所选文件中的主类。 可以通过选择省略号 ( **...** ) 并选择其他类来更改类。|

    ![交互式控制台设置配置](./media/apache-spark-intellij-tool-plugin/interactive-console-configuration.png)

5. 从项目中，导航到“myApp” **“src”** “main” > “scala” **“myApp”**  >  >  > 。  

6. 从菜单栏中，导航到“工具” **“Spark 控制台”** “运行 Spark Livy 交互式会话控制台(Scala)” >  > 。

7. 控制台应如下图所示。 在“控制台”窗口中键入 `sc.appName`，然后按 Ctrl+Enter。  系统将显示结果。 可以通过单击红色按钮终止本地控制台。

    ![IntelliJ IDEA 交互式控制台结果](./media/apache-spark-intellij-tool-plugin/interactive-console-result.png)

### <a name="send-selection-to-spark-console"></a>将选定内容发送到 Spark Console

可以通过将一些代码发送到 Local Console 或 Livy Interactive Session Console (Scala) 来方便地预测脚本结果。 可以在 Scala 文件中突出显示一些代码，然后右键单击“向 Spark 控制台发送所选内容”。 所选代码将被发送到控制台并在其中执行。 结果将显示在控制台中的代码后面。 控制台将检查是否存在错误。  

   ![将选定内容发送到 Spark Console](./media/apache-spark-intellij-tool-plugin/send-selection-to-console.png)

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>与 HDInsight Identity Broker (HIB) 集成 

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>连接带有 ID 代理的 HDInsight ESP 群集 (HIB)

可以按照常规步骤登录到 Azure 订阅，以连接带有 ID 代理的 HDInsight ESP 群集 (HIB)。 登录后，可以在 Azure 资源管理器中看到群集列表。 有关详细信息，请参阅[连接到 HDInsight 群集](#connect-to-your-hdinsight-cluster)。

### <a name="run-a-spark-scala-application-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>在带有 ID 代理的 HDInsight ESP 群集 (HIB) 上运行 Spark Scala 应用程序

可以按照常规步骤将作业提交到带有 ID 代理的 HDInsight ESP 群集 (HIB)。 有关更多说明，请参阅[在 HDInsight Spark 群集中运行 Spark Scala 应用程序](#run-a-spark-scala-application-on-an-hdinsight-spark-cluster)。

我们会将所需的文件上传到与你的登录帐户同名的文件夹，你还可以在配置文件中看到上传路径。

   ![配置中的上传路径](./media/apache-spark-intellij-tool-plugin/upload-path-in-the-configuration.png)

### <a name="spark-console-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>带有 ID 代理的 HDInsight ESP 群集 (HIB) 上的 Spark 控制台

可以在带有 ID 代理的 HDInsight ESP 群集 (HIB) 上运行 Spark Local Console(Scala) 或运行 Spark Livy Interactive Session Console(Scala)。 如需查看更详尽的说明，请参阅 [Spark 控制台](#spark-console)。

   > [!NOTE]  
   > 带有 ID 代理的 HDInsight ESP 群集 (HIB) 目前尚不支持[链接群集](#link-a-cluster)以及[远程调试 Apache Spark 应用程序](#debug-apache-spark-applications-locally-or-remotely-on-an-hdinsight-cluster)。

## <a name="reader-only-role"></a>仅限读取者角色

当用户使用仅限读取者的角色权限将作业提交到群集时，必须提供 Ambari 凭据。

### <a name="link-cluster-from-context-menu"></a>从上下文菜单链接群集

1. 使用仅限读取者角色帐户登录。

2. 在“Azure 资源管理器”中展开“HDInsight”，查看订阅中的 HDInsight 群集。 标记为“角色:读取者”的群集只有仅限读取者角色权限。

    ![IntelliJ Azure 资源管理器角色: 读取者](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer15.png)

3. 右键单击具有仅限读取者角色权限的群集。 从上下文菜单中选择“链接此群集”以链接群集。 输入 Ambari 用户名和密码。

    ![IntelliJ Azure 资源管理器链接此群集](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer11.png)

4. 如果已成功链接群集，HDInsight 将会刷新。
   群集阶段将变为链接状态。
  
    ![IntelliJ Azure 资源管理器链接对话框](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>通过展开“作业”节点来链接群集

1. 单击“作业”节点，此时会弹出“群集作业访问被拒绝”窗口。

2. 单击“链接此群集”以链接群集。

    ![群集作业访问被拒绝对话框](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer9.png)

### <a name="link-cluster-from-rundebug-configurations-window"></a>从“运行/调试配置”窗口链接群集

1. 创建一个 HDInsight 配置。 然后选择“在群集中远程运行”。

2. 对于“Spark 群集(仅限 Linux)”，请选择一个具有仅限读取者角色权限的群集。 警告消息显示。可以单击 "将**此群集链接**到链接群集"。

   ![IntelliJ IDEA 运行/调试配置创建](./media/apache-spark-intellij-tool-plugin/create-configuration.png)

### <a name="view-storage-accounts"></a>查看存储帐户

* 对于具有仅限读取者角色权限的群集，请单击“存储帐户”节点，此时会弹出“存储访问被拒绝”窗口。 可以单击“打开 Azure 存储资源管理器”以打开存储资源管理器。

   ![IntelliJ IDEA 存储访问被拒绝](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer14.png)

   ![IntelliJ IDEA 存储访问被拒绝按钮](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer10.png)

* 对于链接的群集，请单击“存储帐户”节点，此时会弹出“存储访问被拒绝”窗口。 可以单击“打开 Azure 存储”以打开存储资源管理器。

   ![IntelliJ IDEA 存储访问被拒绝 2](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer13.png)

   ![IntelliJ IDEA 存储访问被拒绝 2 按钮](./media/apache-spark-intellij-tool-plugin/intellij-view-explorer12.png)

## <a name="convert-existing-intellij-idea-applications-to-use-azure-toolkit-for-intellij"></a>转换现有 IntelliJ IDEA 应用程序以使用用于 IntelliJ 的 Azure 工具包

可以转换在 IntelliJ IDEA 中创建的现有 Spark Scala 应用程序，使其与用于 IntelliJ 的 Azure 工具包兼容。 然后，可以使用该插件将应用程序提交到 HDInsight Spark 群集。

1. 对于通过 IntelliJ IDEA 创建的现有 Spark Scala 应用程序，打开关联的 .iml 文件。

2. 在根级别有一个 **module** 元素，如下所示：

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4">
        ```

   编辑该元素以添加 `UniqueKey="HDInsightTool"`，使 **module** 元素如下所示：

        ```
        <module org.jetbrains.idea.maven.project.MavenProjectsManager.isMavenModule="true" type="JAVA_MODULE" version="4" UniqueKey="HDInsightTool">
        ```

3. 保存更改。 现在，应用程序应与用于 IntelliJ 的 Azure 工具包兼容。 可以通过右键单击“项目”中的项目名称来测试此项。 弹出菜单现在将包含选项“将 Spark 应用程序提交到 HDInsight”。

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此应用程序，请使用以下步骤删除创建的群集：

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 在顶部的“搜索”框中，键入 **HDInsight**。

1. 选择“服务”下的“HDInsight 群集”。

1. 在显示的 HDInsight 群集列表中，选择为本教程创建的群集旁边的“...”。

1. 选择“删除”。 请选择“是”。

![Azure 门户删除 HDInsight 群集](./media/apache-spark-intellij-tool-plugin/hdinsight-azure-portal-delete-cluster.png "删除 HDInsight 群集")

## <a name="next-steps"></a>后续步骤

本教程介绍了如何使用 Azure Toolkit for IntelliJ 插件开发以 [Scala](https://www.scala-lang.org/) 编写的 Apache Spark 应用程序，并直接从 IntelliJ 集成开发环境 (IDE) 将其提交到 HDInsight Spark 群集。 请转到下一篇文章，了解如何将在 Apache Spark 中注册的数据拉取到 Power BI 等 BI 分析工具中。

> [!div class="nextstepaction"]
> [使用 Power BI 分析 Apache Spark 数据](apache-spark-use-bi-tools.md)
