---
title: '用 Azure Toolkit for IntelliJ 进行的 spark 作业调试失败 (预览版) '
description: 逐步介绍如何使用用于 IntelliJ 的 Azure 工具包中的 HDInsight 工具通过 SSH 远程调试 HDInsight 群集上的应用程序
keywords: 远程调试 intellij, 远程调试 intellij, ssh, intellij, hdinsight, 调试 intellij, 调试
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: aff9f0f70377ebc6e741618b22ff82bc06251521
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295914"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>用 Azure Toolkit for IntelliJ 进行的 spark 作业调试失败 (预览版)

本文提供了有关如何使用[Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable)中的 HDInsight 工具运行**Spark 故障调试**应用程序的分步指南。 

## <a name="prerequisites"></a>系统必备
* [Oracle Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。 本教程使用 Java 版本 8.0.202。
  
* IntelliJ IDEA。 本文使用 [IntelliJ IDEA Community 版本2019.1.3](https://www.jetbrains.com/idea/download/#section=windows)。
  
* Azure Toolkit for IntelliJ。 请参阅[安装 Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable)。

* 连接到 HDInsight 群集。 请参阅[连接到 HDInsight 群集](apache-spark-intellij-tool-plugin.md)。

* Microsoft Azure 存储资源管理器。 请参阅[下载 Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="create-a-project-with-debugging-template"></a>使用调试模板创建项目 

创建 spark 2.3.2 项目以继续调试失败, 执行本文档中的任务调试示例文件。

1. 打开 IntelliJ IDEA。 打开 "**新建项目**" 窗口。

   a. 在左窗格中选择“Azure Spark/HDInsight”  。 

   b. 从主窗口中选择 "**包含失败任务调试的 Spark 项目" "调试示例 (预览)" (Scala)** 。

     ![创建调试项目](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. 选择“**下一步**”。     
 
2. 在 "**新建项目**" 窗口中, 执行以下步骤:

   ![选择 Spark SDK](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. 输入项目名称和项目位置。

   b. 在 "**项目 SDK** " 下拉列表中, 选择 " **Java 1.8** for **Spark 2.3.2**群集"。

   c. 在 " **Spark 版本**" 下拉列表中, 选择 " **Spark 2.3.2 (Scala 2.11.8)** "。

   d. 选择“完成”。 

3. 选择“src”   > “main”   > “scala”  ，在项目中打开代码。 此示例使用**AgeMean_Div ()** 脚本。

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>在 HDInsight 群集上运行 Spark Scala/Java 应用程序

创建 spark Scala/Java 应用程序, 然后通过执行以下步骤在 Spark 群集上运行该应用程序:

1. 单击 "**添加配置**" 以打开 "**运行/调试配置**" 窗口。

   ![编辑配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png) 

2. 在“运行/调试配置”对话框中，选择加号 (+)   。 然后选择“HDInsight 上的 Apache Spark”选项。 

   ![添加新配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. 切换到“在群集中远程运行”选项卡。  为“名称”  、“Spark 群集”  和“Main 类名”  输入信息。 工具支持使用“执行器”  进行调试。 **NumExectors**, 默认值为 5, 并且更好的不能设置为3。 若要减少运行时间, 可以将**maxAppAttempts**添加到**作业配置**中, 并将该值设置为1。 单击 **"确定"** 按钮保存配置。

   ![运行调试配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. 该配置现在以用户提供的名称保存。 若要查看配置详细信息，请选择配置名称。 若要进行更改，请选择“编辑配置”  。 

5. 完成配置设置后, 可以针对远程群集运行项目。
   
   ![远程运行按钮](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. 你可以从 "输出" 窗口中检查应用程序 ID。
   
   ![远程运行按钮](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)   

## <a name="download-failed-job-profile"></a>下载失败的作业配置文件

如果作业提交失败, 你可以将失败的作业配置文件下载到本地计算机, 以便进一步调试。

1. 打开**Microsoft Azure 存储资源管理器**, 找到失败作业的群集的 HDInsight 帐户, 从相应位置下载失败的作业资源: **\hdp\spark2-events\\-失败\\ 应用\<程序 ID >** 到本地文件夹。 "**活动**" 窗口将显示下载进度。

   ![下载失败文件](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![下载失败文件](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)   

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>配置本地调试环境并在失败时进行调试

1. 打开原始项目, 或创建一个新项目并将其与原始源代码关联。 目前仅支持 spark 2.3.2 版本的故障调试。

2. 在 IntelliJ 中, 创建一个**Spark 故障调试**配置文件, 从上一次下载的失败作业资源中为 " **Spark 作业失败上下文位置**" 字段选择 FTD 文件。
   
   ![远程运行按钮](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

4. 单击工具栏中的 "本地运行" 按钮, 错误将显示在 "运行" 窗口中。
   
   ![远程运行按钮](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![远程运行按钮](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

5. 将断点设置为日志指示, 然后单击 "本地调试" 按钮来执行本地调试, 就像在 IntelliJ 中的普通 Scala/Java 项目一样。

5. 调试完成后, 如果项目成功完成, 则可以将失败的作业提交到 HDInsight 上的 spark 群集。

## <a name="seealso"></a>后续步骤
* [概述：调试 Apache Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>演示
* 创建 Scala 项目（视频）：[Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Apache Spark Scala 应用程序）
* 远程调试（视频）：[Use Azure Toolkit for IntelliJ to debug Apache Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用 Azure Toolkit for IntelliJ 远程调试 HDInsight 群集上的 Apache Spark 应用程序）

### <a name="scenarios"></a>方案
* [Apache Spark 与 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 来通过 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检验结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](../hdinsight-apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用 Azure Toolkit for IntelliJ 为 HDInsight 群集创建 Apache Spark 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Apache Spark 应用程序](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
