---
title: 用 IntelliJ 调试 Spark 作业 Azure 工具包（预览版）-HDInsight
description: 在 Azure Toolkit for IntelliJ 中使用 HDInsight 工具调试应用程序的指南
keywords: 远程调试 intellij, 远程调试 intellij, ssh, intellij, hdinsight, 调试 intellij, 调试
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: 052b12817b788ff38f0fab72a5420896b062c732
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857416"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>使用 Azure Toolkit for IntelliJ 进行失败 Spark 作业调试（预览）

本文逐步介绍如何使用 [Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij) 中的 HDInsight 工具来运行 **Spark 失败调试**应用程序。

## <a name="prerequisites"></a>先决条件

* [Oracle Java 开发工具包](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)。 本教程使用 Java 版本 8.0.202。
  
* IntelliJ IDEA。 本文使用 [IntelliJ IDEA Community 版本2019.1.3](https://www.jetbrains.com/idea/download/#section=windows)。
  
* Azure Toolkit for IntelliJ。 请参阅[安装 Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/installation)。

* 连接到 HDInsight 群集。 请参阅[连接到 HDInsight 群集](apache-spark-intellij-tool-plugin.md)。

* Microsoft Azure 存储资源管理器。 请参阅[下载 Microsoft Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)。

## <a name="create-a-project-with-debugging-template"></a>使用调试模板创建项目

请创建 spark2.3.2 项目以继续失败调试，并使用此文档中的失败任务调试示例文件。

1. 打开 IntelliJ IDEA。 打开“新建项目”窗口。 

   a. 在左窗格中选择“Azure Spark/HDInsight”  。

   b. 从主窗口中选择“Spark 项目和失败任务调试示例(预览)(Scala)”。 

     ![Intellij 创建调试项目](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. 选择“**下一步**”。

2. 在“新建项目”  窗口中执行以下步骤：

   ![Intellij 新项目选择 Spark 版本](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. 输入项目名称和项目位置。

   b. 在“项目 SDK”下拉列表中，选择适用于 **Spark 2.3.2** 群集的 **Java 1.8**。 

   c. 在“Spark 版本”下拉列表中，选择“Spark 2.3.2(Scala 2.11.8)”。  

   d. 选择“完成”。 

3. 选择 **src** > **main** > **scala** 打开项目中的代码。 此示例使用 **AgeMean_Div()** 脚本。

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>在 HDInsight 群集中运行 Spark Scala/Java 应用程序

执行以下步骤，创建一个 spark Scala/Java 应用程序，然后在 Spark 群集中运行该应用程序：

1. 单击“添加配置”，打开“运行/调试配置”窗口。  

   ![HDI Intellij 添加配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png)

2. 在“运行/调试配置”对话框中，选择加号 (+)   。 然后选择“HDInsight 上的 Apache Spark”选项。 

   ![Intellij 添加新配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. 切换到“在群集中远程运行”选项卡。  为“名称”  、“Spark 群集”  和“Main 类名”  输入信息。 工具支持使用“执行器”  进行调试。 **numExectors** 的默认值为 5，设置的值最好不要大于 3。 若要减少运行次数，可以将 **spark.yarn.maxAppAttempts** 添加到“作业配置”中并将值设置为 1。  单击“确定”按钮，保存配置。 

   ![Intellij 运行调试配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. 现已使用提供的名称保存配置。 若要查看配置详细信息，请选择配置名称。 若要进行更改，请选择“编辑配置”。 

5. 完成配置设置后，可以针对远程群集运行项目。

   ![Intellij 调试远程 Spark 作业远程运行按钮](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. 可以在输出窗口中查看应用程序 ID。

   ![Intellij 调试远程 Spark 作业远程运行结果](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)

## <a name="download-failed-job-profile"></a>下载已失败作业的配置文件

如果作业提交失败，可以将已失败作业的配置文件下载到本地计算机进行进一步的调试。

1. 打开 **Microsoft Azure 存储资源管理器**，找到已失败作业所在群集的 HDInsight 帐户，将已失败作业的资源从相应的位置 ( **\hdp\spark2-events\\.spark-failures\\\<应用程序 ID>** ) 下载到本地文件夹。 “活动”  窗口会显示下载进度。

   ![Azure 存储资源管理器下载失败](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Azure 存储资源管理器下载成功](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>配置本地调试环境并在失败时进行调试

1. 打开原始项目，或者创建一个新项目并将其与原始源代码相关联。 目前仅支持对 spark2.3.2 版本进行失败调试。

1. 在 IntelliJ IDEA 中，创建一个 **Spark 失败调试**配置文件，从以前下载的已失败作业的资源中选择与“Spark 作业失败上下文位置”字段对应的 FTD 文件。 

   ![创建故障配置](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

1. 单击工具栏中的本地运行按钮，错误就会显示在“运行”窗口中。

   ![运行失败-configuration1](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![运行失败-configuration2](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

1. 按日志指示设置断点，然后单击本地调试按钮进行本地调试，就像 IntelliJ 中的正常 Scala/Java 项目一样。

1. 调试后，如果项目成功完成，则可将失败的作业重新提交到 Spark on HDInsight 群集。

## <a name="next-steps"></a><a name="seealso"></a>后续步骤

* [概述：调试 Apache Spark 应用程序](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>演示

* 创建 Scala 项目（视频）：[Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Apache Spark Scala 应用程序）
* 远程调试（视频）：[使用 Azure Toolkit for IntelliJ 远程调试 HDInsight 群集上的 Apache Spark 应用程序](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>方案

* [使用 BI Apache Spark：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
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
* [将外部包与 Jupyter 笔记本配合使用](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
