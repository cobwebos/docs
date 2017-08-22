---
title: "Azure Toolkit for IntelliJ — 通过 ssh 远程调试 Spark 应用程序 | Microsoft Docs"
description: "逐步介绍如何使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具通过 ssh 远程调试 HDInsight 群集上的应用程序"
keywords: "远程调试 intellij, 远程调试 intellij, ssh, intellij, hdinsight, 调试 intellij, 调试"
services: hdinsight
documentationcenter: 
author: jejiang
manager: DJ
editor: Jenny Jiang
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 06/05/2017
ms.author: Jenny Jiang
ms.translationtype: HT
ms.sourcegitcommit: c999eb5d6b8e191d4268f44d10fb23ab951804e7
ms.openlocfilehash: 5ee6504cd4bf69c8f2c14a3623ff537e66030ce1
ms.contentlocale: zh-cn
ms.lasthandoff: 07/17/2017

---
# <a name="remotely-debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 HDInsight 群集上的 Spark 应用程序

本文逐步介绍如何使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具远程调试 HDInsight 群集上的应用程序。 可以按照[视频](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)调试项目。

**先决条件：**

* **Azure Toolkit for IntelliJ 中的 HDInsight 工具**。 这是 Azure Toolkit for IntelliJ 的一部分。 有关详细信息，请参阅[安装用于 IntelliJ 的 Azure 工具包](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation)。
* **Azure Toolkit for IntelliJ**。 用于为 HDInsight 群集创建 Spark 应用程序。 有关详细信息，请遵循[使用 Azure Toolkit for IntelliJ 为 HDInsight 群集创建 Spark 应用程序](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)中的说明。
* **可管理用户名和密码的 HDInsight SSH 服务。** 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 和[使用 SSH 隧道访问 Ambari Web UI、JobHistory、NameNode、Oozie 和其他 Web UI](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)。 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>创建 Spark Scala 应用程序并配置它以进行远程调试
1. 启动 IntelliJ IDEA，创建一个项目。 在“新建项目”对话框中做出以下选择，然后单击“下一步”。 本文以“Spark on HDInsight 群集运行示例(Scala)”为例。

     ![创建调试项目](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)
   - 在左窗格中，选择“HDInsight”。
   - 在右窗格中，根据偏好选择 Java 或 Scala 模板。 选择下列选项之一： 
      - **Spark on HDInsight (Scala)**
      - **Spark on HDInsight (Java)**
      - **Spark on HDInsight 群集运行示例(Scala)**
   - 生成工具：Scala 项目创建向导支持使用 Maven 或 SBT 管理依赖项和生成 scala 项目。 根据需求选择一个生成工具。
2. 在下一窗口中，提供项目详细信息。

   ![选择 Spark SDK](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)
   - 提供项目名称和项目位置。
   - 对于“项目 SDK”，将 **Java 1.8** 用于 **Spark 2.x** 群集，或将 **Java 1.7** 用于 **Spark 1.x** 群集。
   - 对于“Spark 版本”，Scala 项目创建向导集成了 Spark SDK 和 Scala SDK 的正确版本。 如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”。 否则，请选择“Spark 2.x”。 本文以“Spark 2.0.2 (Scala 2.11.8)”为例。
3. 选择“src” > “main” > “scala”，在项目中打开代码。 本文以“SparkCore_wasbloTest”脚本为例。
4. 若要访问“编辑配置”菜单，请选择右上角的图标。 在此菜单中，可以创建或编辑配置以进行远程调试。

   ![编辑配置](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 
5. 在“运行/调试配置”窗口中，单击加号 (**+**)。 然后选择“提交 Spark 作业”选项。

   ![编辑配置](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
6. 为“名称”、“Spark 群集”和“Main 类名”输入信息。 然后选择“高级配置”。 

   ![运行调试配置](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)
7. 在“Spark 提交高级配置”对话框中，选择“启用 Spark 远程调试”。 输入 SSH 用户名或密码，或使用私钥文件。 若要保存它，请选择“确定”。

   ![启用 Spark 远程调试](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)
8. 该配置现在以用户提供的名称保存。 若要查看配置详细信息，请选择配置名称。 若要进行更改，请选择“编辑配置”。 
9. 完成配置设置后，可以对远程群集运行项目，或执行远程调试。

## <a name="learn-how-to-perform-remote-debugging-and-remote-run"></a>了解如何执行远程调试和远程运行
### <a name="scenario-1-perform-remote-run"></a>情景 1：执行远程运行
1. 若要远程运行项目，请选择“运行”图标。

   ![单击“运行”图标](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-icon.png)

2. “HDInsight Spark 提交”窗口显示应用程序执行状态。 可以根据此处的信息监视 Scala 作业的进度。

   ![单击“运行”图标](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-result.png)

### <a name="scenario-2-perform-remote-debugging"></a>情景 2：执行远程调试
1. 设置断点，然后选择“调试”图标。

    ![单击“调试”图标](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)
2. 程序执行步骤到达断点时，会在底部窗格中显示“调试器”选项卡。 还会在“变量”窗口中显示视图参数和变量信息。 单击“单步执行(跳过过程)”图标转到下一行代码。 然后可以进一步单步调试代码。 选择“恢复程序”图标继续运行代码。 可以在“HDInsight Spark 提交”窗口中查看执行状态。 

   ![“调试”选项卡](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>情景 3：执行远程调试和 bug 修复
本部分演示如何使用 IntelliJ 调试功能动态更新变量值，以进行简单修复。 在下面的代码示例中，由于已存在目标文件而引发异常。
  
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext

        object SparkCore_WasbIOTest {
          def main(arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkCore_WasbIOTest")
            val sc = new SparkContext(conf)
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

            // Find the rows that have only one digit in the sixth column.
            val rdd1 = rdd.filter(s => s.split(",")(6).length() == 1)

            try {
              var target = "wasb:///HVACout2_testdebug1";
              rdd1.saveAsTextFile(target);
            } catch {
              case ex: Exception => {
                throw ex;
              }
            }
          }
        }


#### <a name="to-perform-remote-debugging-and-bug-fixing"></a>执行远程调试和 bug 修复
1. 设置两个断点，然后单击“调试”图标启动远程调试过程。

2. 代码在第一个断点处停止，“变量”窗口中显示参数和变量信息。 

3. 单击“恢复程序”图标继续运行。 代码在第二个断点处停止。 正在按预期方式捕获异常。

  ![引发错误](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. 再次单击“恢复程序”图标。 “HDInsight Spark 提交”窗口随即显示作业运行失败错误。

  ![错误提交](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. 若要使用 IntelliJ 调试功能动态更新变量值，请再次选择“调试”。 变量窗口再次出现。 

6. 右键单击“调试”选项卡上的目标，然后选择“设置值”。 接下来，输入变量的新值。 然后选择 **Enter** 保存该值。 

  ![设置值](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. 单击“恢复程序”图标继续运行程序。 此次未捕获到异常。 可以看到项目成功运行，无任何异常。

  ![调试后无异常](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>另请参阅
* [概述：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>演示
* 创建 Scala 项目（视频）：[Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Spark Scala 应用程序）
* 远程调试（视频）：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on HDInsight Cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用 Azure Toolkit for IntelliJ 远程调试 HDInsight 群集上的 Spark 应用程序）

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](hdinsight-apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用 Azure Toolkit for IntelliJ 中的 HDInsight 工具创建和提交 Spark Scala 应用程序](hdinsight-apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 HDInsight Spark 上的应用程序](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](hdinsight-apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](hdinsight-apache-spark-job-debugging.md)
 
