---
title: "用于 Eclipse 的 Azure 工具包 - 为 HDInsight Spark 创建 Scala 应用程序 | Microsoft Docs"
description: "在 Azure Toolkit for Eclipse 中使用 HDInsight 工具开发以 Scala 编写的 Spark 应用程序，并直接从 Eclipse IDE 将其提交到 HDInsight Spark 群集。"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 013a3175d5e19689629d1d0ea3b413184e71c485
ms.contentlocale: zh-cn
ms.lasthandoff: 06/07/2017


---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>使用用于 Eclipse 的 Azure 工具包为 HDInsight 群集创建 Spark 应用程序

在用于 Eclipse 的 Azure 工具包中使用 HDInsight 工具开发以 Scala 编写的 Spark 应用程序，并直接从 Eclipse IDE 将其提交到 Azure HDInsight Spark 群集。 可以通过多种不同方式使用 HDInsight 工具插件：

* 在 HDInsight Spark 群集中开发和提交 Scala Spark 应用程序
* 访问 Azure HDInsight Spark 群集资源
* 本地开发和运行 Scala Spark 应用程序

> [!IMPORTANT]
> 此工具仅可用于在 Linux 上的 HDInsight Spark 群集中创建和提交应用程序。
> 
> 

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Apache Spark 群集。 有关说明，请参阅[在 Azure HDInsight 中创建 Apache Spark 群集](hdinsight-apache-spark-jupyter-spark-sql.md)。
* Oracle Java 开发工具包版本 8，用于 Eclipse IDE 运行时。 可以从 [Oracle 网站](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载它。
* Eclipse IDE。 本文使用 Eclipse Neon。 可以从 [Eclipse 网站](https://www.eclipse.org/downloads/)安装它。
* Scala IDE for Eclipse。 
  
  * **如果已安装 Eclipse IDE**，可以转到“帮助” > “安装新软件”来添加 Scala IDE 插件，然后将 [http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site](http://download.scala-ide.org/sdk/lithium/e46/scala211/stable/site) 添加为下载适用于 Eclipse 的 Scala 插件的来源。 
  * **如果没有安装 Eclipse IDE**，可以直接从 [Scala 网站](http://scala-ide.org/download/sdk.html)安装 Scala IDE。 下载 .zip 文件，将其解压缩，浏览到 **/eclipse** 文件夹，然后从该文件夹运行 **eclipse.exe** 文件。
    
    > [!NOTE]
    > 本文中的步骤以使用安装了 Scala 插件的 Eclipse IDE 为基础。
    > 
    > 
* Spark SDK。 可以从 [GitHub](http://go.microsoft.com/fwlink/?LinkID=723585&clcid=0x409) 下载它。
* e(fx)clipse。 可以从 [Eclipse 网站的下载页](https://www.eclipse.org/efxclipse/install.html)安装它。

## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>安装 Azure Toolkit for Eclipse 中的 HDInsight 工具
用于 Eclipse 的 HDInsight 工具作为用于 Eclipse 的 Azure 工具包的一部分提供。 有关安装说明，请参阅[安装用于 Eclipse 的 Azure 工具包](../azure-toolkit-for-eclipse-installation.md)。

## <a name="sign-in-to-your-azure-subscription"></a>登录到 Azure 订阅
1. 启动 Eclipse IDE 并打开 Azure 资源管理器。 在“窗口”菜单中单击“显示视图”，然后单击“其他”。 从打开的对话框中展开“Azure”，单击“Azure 资源管理器”，然后单击“确定”。

    ![“显示视图”对话框](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. 右键单击“Azure”节点，然后单击“登录”。
3. 在“Azure 登录”对话框框中，选择“身份验证模式”，单击“登录”并输入 Azure 凭据。
   
    ![Azure 登录对话框](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. 登录之后，“选择订阅”对话框会列出与凭据关联的所有 Azure 订阅。 单击“选择”关闭对话框。

    ![“选择订阅”对话框](./media/hdinsight-apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. 在“Azure 资源管理器”选项卡中展开“HDInsight”，查看订阅下的 HDInsight Spark 群集。
   
    ![Azure 资源管理器中的 HDInsight Spark 群集](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. 可以进一步展开群集名称节点，查看与群集关联的资源（例如存储帐户）。
   
    ![展开群集名称可查看资源](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>为 HDInsight Spark 群集设置 Spark Scala 项目

1. 从 Eclipse IDE 工作区中，依次单击“文件”、“新建”和“项目”。 
2. 在“新建项目”向导中，展开“HDInsight”，选择“Spark on HDInsight (Scala)”，然后单击“下一步”。

    ![选择 Spark on HDInsight (Scala) 项目](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. 在“新建 HDInsight Scala 项目”对话框中提供以下值，然后单击“下一步”：
   * 输入项目的名称。
   * 在“JRE”区域中，确保“使用执行环境 JRE”已设置为“JavaSE-1.7”。
   * 确保 Spark SDK 已设置为你下载 SDK 的位置。 下载位置的链接包含在本文前面的[先决条件](#prerequisites)中。 也可以从包含在此对话框中的链接下载 SDK。

    ![“新建 HDInsight Scala 项目”对话框](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
4.  在下一个对话框中，单击“库”选项卡并保留默认值，然后单击“完成”。 
   
    ![“库”选项卡](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-hdi-scala-app-4.png)


## <a name="run-a-spark-scala-application-on-an-azure-data-lake-store-cluster"></a>在 Azure Data Lake Store 群集上运行 Spark Scala 应用程序
若要将应用程序提交到 Azure Data Lake Store，必须在 Azure 登录过程中选择“交互”模式。 

   ![登录时的“交互”选项](./media/hdinsight-apache-spark-eclipse-tool-plugin/Interactive-Authentication.png)

## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>为 HDInsight Spark 群集创建 Scala 应用程序

1. 在 Eclipse IDE 中，从“包资源管理器”展开之前创建的项目，右键单击“src”，指向“新建”，然后单击“其他”。
2. 在“选择向导”对话框中，展开“Scala 向导”，单击“Scala 对象”，然后单击“下一步”。
   
    ![选择向导对话框](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. 在“创建新文件”对话框中，输入对象的名称，然后单击“完成”。
   
    ![“创建新文件”对话框](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. 在文本编辑器中粘贴以下代码：
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasbs:///HVACOut")
          }        
        }
5. 在 HDInsight Spark 群集中运行该应用程序：
   
   1. 在“包资源管理器”中，右键单击项目名称，然后选择“将 Spark 应用程序提交到 HDInsight”。        
   2. 在“Spark 提交”对话框中提供以下值，然后单击“提交”：
      
      * 对于“群集名称”，选择要在其上运行应用程序的 HDInsight Spark 群集。
      * 从 Eclipse 项目或硬盘中选择一个项目。
      * 在“主类名称”文本框中，输入在代码中指定的对象名称。
      * 由于本示例中的应用程序代码不需要任何命令行参数，也不需要引用 JAR 或文件，因此可以将其余的文本框留空。
        
       ![“Spark 提交”对话框](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
   3. “Spark 提交”选项卡应开始显示进度。 可以通过单击“Spark 提交”窗口中的红色按钮停止应用程序。 也可以单击全球图标（以图中的蓝色框表示），查看此特定应用程序运行的日志。
      
       ![“Spark 提交”窗口](./media/hdinsight-apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)
      
## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>使用 Azure Toolkit for Eclipse 中的 HDInsight 工具访问和管理 HDInsight Spark 群集
可以使用 HDInsight 工具执行各种操作，包括访问作业输出。

### <a name="access-the-storage-container-for-the-cluster"></a>访问群集的存储容器
1. 在 Azure 资源管理器中展开“HDInsight”根节点，查看可用 HDInsight Spark 群集的列表。
2. 展开群集名称以查看群集的存储帐户和默认存储容器。
   
    ![存储帐户和默认存储容器](./media/hdinsight-apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. 单击与群集关联的存储容器名称。 在右窗格中，双击“HVACOut”文件夹。 打开其中一个 **part-** 文件可查看应用程序的输出。

### <a name="access-the-spark-history-server"></a>访问 Spark 历史记录服务器
1. 在“Azure 资源管理器”中，右键单击 Spark 群集名称，然后选择“打开 Spark 历史记录 UI”。 出现提示时，请输入群集的管理员凭据。 在预配群集时，你必须已指定这些凭据。
2. 在“Spark 历史记录服务器”仪表板中，可以使用应用程序名称查找刚运行完的应用程序。 在上述代码中，已使用 `val conf = new SparkConf().setAppName("MyClusterApp")` 设置了应用程序名称。 因此，Spark 应用程序名称为 **MyClusterApp**。

### <a name="start-the-ambari-portal"></a>启动 Ambari 门户
1. 在“Azure 资源管理器”中，右键单击 Spark 群集名称，然后选择“打开群集管理门户(Ambari)”。 
2. 出现提示时，请输入群集的管理员凭据。 在预配群集时，你必须已指定这些凭据。

### <a name="manage-azure-subscriptions"></a>管理 Azure 订阅
默认情况下，Azure Toolkit for Eclipse 中的 HDInsight 工具将列出所有 Azure 订阅中的 Spark 群集。 如果需要，可以指定想要访问其群集的订阅。 

1. 在“Azure 资源管理器”中，右键单击“Azure”根节点，然后单击“管理订阅”。 
2. 在对话框中，清除不想访问的订阅对应的复选框，然后单击“关闭”。 如果想要从 Azure 订阅注销，可以单击“注销”。

## <a name="run-a-spark-scala-application-locally"></a>本地运行 Spark Scala 应用程序
可以使用 Azure Toolkit for Eclipse 中的 HDInsight 工具在工作站上本地运行 Spark Scala 应用程序。 通常，这些应用程序不需要访问群集资源（如存储容器），并可以在本地运行和测试。

### <a name="prerequisite"></a>先决条件
在 Windows 计算机上运行本地 Spark Scala 应用程序时，可能会发生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的异常。 发生这些异常的原因是 Windows 中缺少 **WinUtils.exe**。 

若要解决此错误，必须[下载该可执行文件](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)到相应位置（例如 **C:\WinUtils\bin**）。 然后必须添加环境变量 **HADOOP_HOME**，并将其值设置为 **C\WinUtils**。

### <a name="run-a-local-spark-scala-application"></a>运行本地的 Spark Scala 应用程序
1. 启动 Eclipse 并创建项目。 在“新建项目”对话框中做出以下选择，然后单击“下一步”。
   
   * 在左窗格中，选择“HDInsight”。
   * 在右窗格中，选择“Spark on HDInsight 本地运行示例(Scala)”。

    ![“新建项目”对话框](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
2. 若要提供项目详细信息，请执行前面部分[为 HDInsight Spark 群集设置 Spark Scala 项目](#set-up-a-spark-scala-project-for-an-hdinsight-spark cluster)中的步骤 3 到 6。
3. 模板将在 **src** 文件夹下面添加可在计算机上本地运行的示例代码 (**LogQuery**)。
   
    ![LogQuery 的位置](./media/hdinsight-apache-spark-eclipse-tool-plugin/local-app.png)
4. 右键单击 **LogQuery** 应用程序，指向“运行方式”，然后单击“1 Scala 应用程序”。 底部的“控制台”选项卡中会显示如下输出：
   
   ![Spark 应用程序本地运行结果](./media/hdinsight-apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="feedback-and-known-issues"></a>反馈和已知问题
目前不支持直接查看 Spark 输出。

如果你有任何建议或反馈，或使用此工具时遇到任何问题，欢迎向 hdivstool@microsoft.com 发送电子邮件。

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
* [使用用于 IntelliJ 的 Azure 工具包创建和提交 Spark Scala 应用程序](hdinsight-apache-spark-intellij-tool-plugin.md)
* [使用用于 IntelliJ 的 Azure 工具包远程调试 Spark 应用程序](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](hdinsight-apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](hdinsight-apache-spark-job-debugging.md)


