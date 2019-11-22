---
title: Azure Toolkit for Eclipse：为 HDInsight Spark 创建 Scala 应用
description: 使用 Azure Toolkit for Eclipse 中的 HDInsight 工具开发以 Scala 编写的 Spark 应用程序，并将应用程序直接从 Eclipse IDE 提交到 HDInsight Spark 群集。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.openlocfilehash: 6268256c9be26ef3e7e1061eef7cdb3b3f7d31db
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74286939"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-apache-spark-applications-for-an-hdinsight-cluster"></a>使用 Azure Toolkit for Eclipse 为 HDInsight 群集创建 Apache Spark 应用程序

在 Azure Toolkit for [Eclipse](https://www.eclipse.org/) 中使用 HDInsight 工具开发以 [Scala](https://spark.apache.org/) 编写的 [Apache Spark](https://www.scala-lang.org/) 应用程序，并直接从 Eclipse IDE 将其提交到 Azure HDInsight Spark 群集。 可以通过多种不同方式使用 HDInsight 工具插件：

* 在 HDInsight Spark 群集中开发和提交 Scala Spark 应用程序。
* 访问 Azure HDInsight Spark 群集资源。
* 在本地开发和运行 Scala Spark 应用程序。

> [!IMPORTANT]  
> 此工具可用于在 Linux 上的 HDInsight Spark 群集中创建和提交应用程序。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](apache-spark-jupyter-spark-sql.md)。
* Oracle Java 开发工具包版本 8，用于 Eclipse IDE 运行时。 可以从 [Oracle 网站](https://aka.ms/azure-jdks)下载它。
* Eclipse IDE。 本文使用 Eclipse Neon。 可以从 [Eclipse 网站](https://www.eclipse.org/downloads/)安装它。

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>安装 Azure Toolkit for Eclipse 中的 HDInsight 工具和 Scala 插件

### <a name="install-azure-toolkit-for-eclipse"></a>安装用于 Eclipse 的 Azure 工具包

用于 Eclipse 的 HDInsight 工具作为用于 Eclipse 的 Azure 工具包的一部分提供。 有关安装说明，请参阅[安装用于 Eclipse 的 Azure 工具包](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation)。

### <a name="install-the-scala-plug-in"></a>安装 Scala 插件

打开 Eclipse 时，HDInsight 工具会自动检测是否安装了 Scala 插件。 选择“确定”继续，然后按照说明从 Eclipse Marketplace 安装插件。

![自动安装 Scala 插件](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala1.png)

用户可以[登录到 Azure 订阅](#sign-in-to-your-azure-subscription)，或者使用 Ambari 用户名/密码或已加入域的凭据[链接 HDInsight 群集](#link-a-cluster)来启动。

## <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅

1. 启动 Eclipse IDE 并打开 Azure 资源管理器。 在“窗口”菜单中选择“显示视图”，并选择“其他”。 在打开的对话框中展开“Azure”，选择“Azure 资源管理器”，再选择“确定”。

   ![Apache Spark Eclispse 显示视图](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer1.png)

1. 右键单击“Azure”节点，再选择“登录”。
1. 在“Azure 登录”对话框框中，选择“身份验证方法”，选择“登录”并输入 Azure 凭据。

   ![Apache Spark Eclispse Azure 符号](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer2.png)

1. 登录之后，“选择订阅”对话框会列出与凭据关联的所有 Azure 订阅。 单击“选择”关闭对话框。

   ![“选择订阅”对话框](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)

1. 在“Azure 资源管理器”选项卡中展开“HDInsight”，查看订阅下的 HDInsight Spark 群集。

   ![Azure 资源管理器 3 中的 HDInsight Spark 群集](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer3.png)

1. 可以进一步展开群集名称节点，查看与群集关联的资源（例如存储帐户）。

   ![展开群集名称可查看资源](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer4.png)

## <a name="link-a-cluster"></a>链接群集

可以使用 Ambari 管理的用户名链接标准群集。 同样，对于已加入域的 HDInsight 群集，也可使用这种域和用户名（例如 `user1@contoso.com`）进行链接。

1. 从“Azure 资源管理器”选择“链接群集”。

   ![Azure 资源管理器链接群集菜单](./media/apache-spark-eclipse-tool-plugin/link-a-cluster-context-menu.png)

1. 输入“群集名称”、“用户名”和“密码”，然后单击“确定”按钮以链接群集。 （可选）输入“存储帐户”、“存储密钥”，然后选择“存储资源管理器”，以便存储资源管理器在左侧树状视图中工作

   !["链接新 HDInsight 群集" 对话框](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog1.png)

   > [!NOTE]  
   > 如果群集已登录到 Azure 订阅中并且已链接群集，则我们使用链接存储密钥、用户名和密码。
   > ![Azure 资源管理器存储帐户](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)
   >
   > 对于仅限键盘的用户，当当前焦点在**存储密钥**上时，需要使用**Ctrl + TAB**将焦点放在对话框中的下一个字段上。
   
   
1. 单击“确定”按钮后，如果输入信息正确，可以在 **HDInsight** 节点中看到链接的群集。 现在可以将应用程序提交到此链接群集。

   ![Azure 资源管理器 hdi 链接群集](./media/apache-spark-eclipse-tool-plugin/hdinsight-linked-cluster.png)

1. 还可以从 **Azure 资源管理器**取消链接群集。

   ![Azure 资源管理器取消链接群集](./media/apache-spark-eclipse-tool-plugin/hdi-unlinked-cluster.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>为 HDInsight Spark 群集设置 Spark Scala 项目

1. 在 Eclipse IDE 工作区中，依次选择“文件”、“新建”，然后选择“项目”。

1. 在“新建项目”向导中，展开“HDInsight”，选择“Spark on HDInsight (Scala)”，并选择“下一步”。

   ![选择 Spark on HDInsight (Scala) 项目](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)

1. Scala 项目创建向导会自动检测是否安装了 Scala 插件。 选择“确定”继续下载 Scala 插件，然后按照说明重启 Eclipse。

   ![安装缺少的插件 Scala 检查](./media/apache-spark-eclipse-tool-plugin/auto-installation-scala2.png)

1. 在“新建 HDInsight Scala 项目”对话框中提供以下值，然后选择“下一步”：
   * 输入项目的名称。
   * 在“JRE”区域中，确保“使用执行环境 JRE”设置为“JavaSE-1.7”或更高版本。
   * 在“Spark 库”区域中，可以选择“使用 Maven 配置 Spark SDK”选项。  我们的工具集成了适当版本的 Spark SDK 和 Scala SDK。 也可以选择“手动添加 Spark SDK”选项，手动下载并添加 Spark SDK。

   ![“新建 HDInsight Scala 项目”对话框](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)

1. 在下一个对话框中，选择“完成”。

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>为 HDInsight Spark 群集创建 Scala 应用程序

1. 在 Eclipse IDE 中，从“包资源管理器”展开之前创建的项目，右键单击“src”，指向“新建”，再选择“其他”。

1. 在“选择向导”对话框中，展开“Scala 向导”，选择“Scala 对象”，再选择“下一步”。

   ![选择向导创建 Scala 对象](./media/apache-spark-eclipse-tool-plugin/create-scala-project1.png)
1. 在“创建新文件”对话框中，输入对象的名称，然后选择“完成”。

   ![新建文件向导创建新文件](./media/apache-spark-eclipse-tool-plugin/create-scala-project2.png)
1. 在文本编辑器中粘贴以下代码：

    ```scala
    import org.apache.spark.SparkConf
    import org.apache.spark.SparkContext

    object MyClusterApp{
        def main (arg: Array[String]): Unit = {
        val conf = new SparkConf().setAppName("MyClusterApp")
        val sc = new SparkContext(conf)

        val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        //find the rows that have only one digit in the seventh column in the CSV
        val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

        rdd1.saveAsTextFile("wasb:///HVACOut")
        }        
    }
    ```

1. 在 HDInsight Spark 群集中运行该应用程序：

   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，并单击“添加引用”。 在“包资源管理器”中，右键单击项目名称，并选择“将 Spark 应用程序提交到 HDInsight”。

   b. 在“Spark 提交”对话框中提供以下值，然后选择“提交”：

   * 对于“群集名称”，选择要在其上运行应用程序的 HDInsight Spark 群集。
   * 从 Eclipse 项目或硬盘中选择一个项目。 默认值取决于从包资源管理器右键单击的项。
   * 在“主类名”下拉列表中，提交向导将显示项目中的所有对象名。 选择或输入要运行的对象的名称。 如果从硬盘中选择项目，必须手动输入主类名。 
   * 由于本示例中的应用程序代码不需要任何命令行参数，也不需要引用 JAR 或文件，因此可以将其余的文本框留空。

     ![Apache Spark 提交 "对话框](./media/apache-spark-eclipse-tool-plugin/create-scala-project3.png)

1. “Spark 提交”选项卡应开始显示进度。 可以通过选择“Spark 提交”窗口中的红色按钮停止应用程序。 也可以选择地球图标（以图中的蓝色框表示），查看此特定应用程序运行的日志。

   ![Apache Spark 提交窗口](./media/apache-spark-eclipse-tool-plugin/create-scala-project4.png)

## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>使用 Azure Toolkit for Eclipse 中的 HDInsight 工具访问和管理 HDInsight Spark 群集

可以使用 HDInsight 工具执行各种操作，包括访问作业输出。

### <a name="access-the-job-view"></a>访问作业视图

1. 在“Azure 资源管理器”中依次展开“HDInsight”和 Spark 群集名称，并选择“作业”。

   ![Azure 资源管理器 Eclipse 作业视图节点](./media/apache-spark-eclipse-tool-plugin/eclipse-job-view-node.png)

1. 选择“作业”节点。 如果 Java 版本低于 **1.8**，HDInsight 工具会自动提醒你安装 **E(fx)clipse** 插件。 选择“确定”继续，然后按照向导指示从 Eclipse Marketplace 安装该插件并重启 Eclipse。

   ![安装缺少的插件 E （fx） clipse](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. 从“作业”节点打开作业视图。 在右窗格中，“Spark 作业视图”选项卡显示了群集上运行的所有应用程序。 选择想要查看其详细信息的应用程序的名称。

   ![Apache Eclipse 查看作业日志详细信息](./media/apache-spark-eclipse-tool-plugin/eclipse-view-job-logs.png)

   然后，可以执行以下任一操作：

   * 将鼠标悬停在作业图上。 将显示有关运行作业的基本信息。 选择作业图，可以看到每个作业生成的阶段和信息。

     ![Apache Spark 作业图阶段信息](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * 选择“日志”选项卡查看常用的日志，包括“驱动程序 Stderr”、“驱动程序 Stdout”和“目录信息”。

     ![Apache Spark Eclipse 作业日志信息](./media/apache-spark-eclipse-tool-plugin/eclipse-job-log-info.png)

   * 选择窗口顶部的超链接打开 Spark 历史记录 UI 和 Apache Hadoop YARN UI（应用程序级别）。

### <a name="access-the-storage-container-for-the-cluster"></a>访问群集的存储容器

1. 在 Azure 资源管理器中展开“HDInsight”根节点，查看可用 HDInsight Spark 群集的列表。

1. 展开群集名称以查看群集的存储帐户和默认存储容器。

   ![存储帐户和默认存储容器](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer5.png)

1. 选择与群集关联的存储容器名称。 在右窗格中，双击“HVACOut”文件夹。 打开其中一个 **part-** 文件可查看应用程序的输出。

### <a name="access-the-spark-history-server"></a>访问 Spark 历史记录服务器

1. 在“Azure 资源管理器”中，右键单击 Spark 群集名称，然后选择“打开 Spark 历史记录 UI”。 出现提示时，请输入群集的管理员凭据。 预配群集时已指定这些凭据。

1. 在“Spark 历史记录服务器”仪表板中，可以使用应用程序名称查找刚运行完的应用程序。 在上述代码中，已使用 `val conf = new SparkConf().setAppName("MyClusterApp")` 设置了应用程序名称。 因此，Spark 应用程序名称为“MyClusterApp”。

### <a name="start-the-apache-ambari-portal"></a>启动 Apache Ambari 门户

1. 在“Azure 资源管理器”中，右键单击 Spark 群集名称，然后选择“打开群集管理门户(Ambari)”。

1. 出现提示时，请输入群集的管理员凭据。 预配群集时已指定这些凭据。

### <a name="manage-azure-subscriptions"></a>管理 Azure 订阅

默认情况下，用于 Eclipse 的 Azure 工具包中的 HDInsight 工具将列出所有 Azure 订阅中的 Spark 群集。 如果需要，可以指定想要访问其群集的订阅。

1. 在“Azure 资源管理器”中，右键单击“Azure”根节点，并选择“管理订阅”。

1. 在对话框中，清除不想访问的订阅对应的复选框，然后选择“关闭”。 如果想要从 Azure 订阅注销，可以选择“注销”。

## <a name="run-a-spark-scala-application-locally"></a>本地运行 Spark Scala 应用程序

可以使用 Azure Toolkit for Eclipse 中的 HDInsight 工具在工作站上本地运行 Spark Scala 应用程序。 通常，这些应用程序不需要访问群集资源（如存储容器），并可以在本地运行和测试。

### <a name="prerequisite"></a>先决条件

在 Windows 计算机上运行本地 Spark Scala 应用程序时，可能会发生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的异常。 发生这些异常的原因是 Windows 中缺少 **WinUtils.exe**。

若要解决此错误，需要[下载可执行文件](https://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)到所需位置（例如 **C:\WinUtils\bin**），然后添加环境变量 **HADOOP_HOME**，并将该变量的值设为 **C\WinUtils**。

### <a name="run-a-local-spark-scala-application"></a>运行本地的 Spark Scala 应用程序

1. 启动 Eclipse 并创建项目。 在“新建项目”对话框中做出以下选择，然后选择“下一步”。

   * 在左窗格中，选择“HDInsight”。
   * 在右窗格中，选择“Spark on HDInsight 本地运行示例(Scala)”。

   ![新项目选择向导对话框](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)

1. 若要提供项目详细信息，请执行前面部分[为 HDInsight Spark 群集设置 Spark Scala 项目](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster)中的步骤 3 到步骤 6。

1. 模板会在 **src** 文件夹下面添加可在计算机上本地运行的示例代码 (**LogQuery**)。

   ![LogQuery 本地 scala 应用程序的位置](./media/apache-spark-eclipse-tool-plugin/local-scala-application.png)

1. 右键单击“LogQuery”应用程序，指向“运行方式”，然后选择“1 Scala 应用程序”。 “控制台”选项卡上将出现如下所示的输出：

   ![Spark 应用程序本地运行结果](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="reader-only-role"></a>仅限读取者角色

当用户使用仅限读取者的角色权限将作业提交到群集时，必须提供 Ambari 凭据。

### <a name="link-cluster-from-context-menu"></a>从上下文菜单链接群集

1. 使用仅限读取者角色帐户登录。

2. 在“Azure 资源管理器”中展开“HDInsight”，查看订阅中的 HDInsight 群集。 标记为“角色:读取者”的群集只有仅限读取者角色权限。

    ![Azure 资源管理器中的 HDInsight Spark 群集（角色读取者）](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer6.png)

3. 右键单击具有“仅读取者”角色权限的群集。 从上下文菜单中选择“链接此群集”以链接群集。 输入 Ambari 用户名和密码。

    ![Azure 资源管理器中的 HDInsight Spark 群集（链接）](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer7.png)

4. 如果已成功链接群集，HDInsight 将会刷新。
   群集阶段将变为链接状态。
  
    ![Azure 资源管理器中的 HDInsight Spark 群集（已链接）](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer8.png)

### <a name="link-cluster-by-expanding-jobs-node"></a>通过展开“作业”节点来链接群集

1. 单击“作业”节点，此时会弹出“群集作业访问被拒绝”窗口。

2. 单击“链接此群集”以链接群集。

    ![Azure 资源管理器 9 中的 HDInsight Spark 群集](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer9.png)

### <a name="link-cluster-from-spark-submission-window"></a>从“Spark 提交”窗口链接群集

1. 创建一个 HDInsight 项目。

2. 右键单击该包。 然后选择“将 Spark 应用程序提交到 HDInsight”。

   ![Azure 资源管理器中的 HDInsight Spark 群集（提交）](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer11.png)

3. 对于**群集名称**，选择具有“仅读取者”角色权限的群集。 警告消息显示。可以单击 "将**此群集链接**到链接群集"。

   ![Azure 资源管理器中的 HDInsight Spark 群集（对此进行链接）](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer15.png)

### <a name="view-storage-accounts"></a>查看存储帐户

* 对于具有仅限读取者角色权限的群集，请单击“存储帐户”节点，此时会弹出“存储访问被拒绝”窗口。

   ![Azure 资源管理器中的 HDInsight Spark 群集（存储）](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer13.png)

   ![Azure 资源管理器中的 HDInsight Spark 群集（已拒绝）](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer12.png)

* 对于链接的群集，请单击“存储帐户”节点，此时会弹出“存储访问被拒绝”窗口。

   ![Azure 资源管理器中的 HDInsight Spark 群集（已拒绝 2）](./media/apache-spark-eclipse-tool-plugin/eclipse-view-explorer14.png)

## <a name="known-problems"></a>已知问题

链接群集时，我将建议你提供存储的凭据。

![将群集与存储凭据 eclipse 链接](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

可通过两种模式提交作业。 如果提供存储凭据，则将使用批处理模式提交作业。 否则，将使用交互模式。 如果群集正忙，可能会收到以下错误。

![当群集繁忙时 eclipse 出现错误](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png "eclipse 在群集忙时收到错误")

![eclipse 在群集忙时收到错误 yarn](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png "eclipse 在群集忙时收到错误 yarn")

## <a name="seealso"></a>另请参阅

* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="scenarios"></a>方案

* [Apache Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>创建和运行应用程序

* [使用 Scala 创建独立的应用程序](apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展

* [使用用于 IntelliJ 的 Azure 工具包创建和提交 Spark Scala 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Apache Spark 应用程序](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Apache Spark 应用程序](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理资源

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
