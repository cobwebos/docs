---
title: "Azure Toolkit for IntelliJ：通过 SSH 远程调试 Spark 应用程序 | Microsoft Docs"
description: "逐步介绍如何使用用于 IntelliJ 的 Azure 工具包中的 HDInsight 工具通过 SSH 远程调试 HDInsight 群集上的应用程序"
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
ms.date: 08/24/2017
ms.author: Jenny Jiang
ms.openlocfilehash: 19053e31d6eb097bc91a04ef9c6af5772aaa16da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="debug-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>使用用于 IntelliJ 的 Azure 工具包通过 SSH 调试 HDInsight 群集上的 Spark 应用程序

本文逐步介绍如何使用用于 IntelliJ 的 Azure 工具包中的 HDInsight 工具远程调试 HDInsight 群集上的应用程序。 要调试项目，还可以观看 [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用用于 IntelliJ 的 Azure 工具包调试 HDInsight Spark 应用程序）视频。

**先决条件**

* **Azure Toolkit for IntelliJ 中的 HDInsight 工具**。 此工具是用于 IntelliJ 的 Azure 工具包的一部分。 有关详细信息，请参阅[安装用于 IntelliJ 的 Azure 工具包](https://docs.microsoft.com/en-us/azure/azure-toolkit-for-intellij-installation)。
* **Azure Toolkit for IntelliJ**。 使用此工具包为 HDInsight 群集创建 Spark 应用程序。 有关详细信息，请遵循[使用用于 IntelliJ 的 Azure 工具包为 HDInsight 群集创建 Spark 应用程序](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)中的说明。
* **可管理用户名和密码的 HDInsight SSH 服务**。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 和[使用 SSH 隧道访问 Ambari Web UI、JobHistory、NameNode、Oozie 和其他 Web UI](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)。 
 

## <a name="create-a-spark-scala-application-and-configure-it-for-remote-debugging"></a>创建 Spark Scala 应用程序并配置它以进行远程调试

1. 启动 IntelliJ IDEA 并创建一个项目。 在“新建项目”对话框中执行以下操作：

   a. 选择“HDInsight”。 

   b. 根据偏好选择 Java 或 Scala 模板。 选择下列选项之一：

      - **Spark on HDInsight (Scala)**

      - **Spark on HDInsight (Java)**

      - **Spark on HDInsight 群集运行示例(Scala)**

      本示例使用“Spark on HDInsight 群集运行示例(Scala)”模板。

   c. 在“生成工具”列表中，根据需要选择以下选项之一：

      - 用于支持 Scala 项目创建向导的“Maven”

      -  用于管理依赖项和生成 Scala 项目的“SBT” 

      ![创建调试项目](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-create-projectfor-debug-remotely.png)

   d.单击“下一步”。 选择“下一步”。     
 
3. 在下一个“新建项目”窗口中执行以下操作：

   ![选择 Spark SDK](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-new-project.png)

   a. 输入项目名称和项目位置。

   b. 在“项目 SDK”下拉列表中，选择适用于 Spark 2.x 群集的“Java 1.8”或选择适用于 Spark 1.x 群集的“Java 1.7”。

   c. 在“Spark 版本”下拉列表中，Scala 项目创建向导集成了 Spark SDK 和 Scala SDK 的正确版本。 如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”。 否则，请选择“Spark 2.x”。 本示例使用“Spark 2.0.2 (Scala 2.11.8)”。

   d.单击“下一步”。 选择“完成”。

4. 选择“src” > “main” > “scala”，在项目中打开代码。 本示例使用“SparkCore_wasbloTest”脚本。

5. 要访问“编辑配置”菜单，请选择右上角的图标。 在此菜单中，可以创建或编辑配置以进行远程调试。

   ![编辑配置](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-edit-configurations.png) 

6. 在“运行/调试配置”对话框中，选择加号 (+)。 然后选择“提交 Spark 作业”选项。

   ![添加新配置](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-add-new-Configuration.png)
7. 为“名称”、“Spark 群集”和“Main 类名”输入信息。 然后选择“高级配置”。 

   ![运行调试配置](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-run-debug-configurations.png)

8. 在“Spark 提交高级配置”对话框中，选择“启用 Spark 远程调试”。 输入 SSH 用户名，然后输入密码或使用私钥文件。 要保存配置，请选择“确定”。

   ![启用 Spark 远程调试](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-enable-spark-remote-debug.png)

9. 该配置现在以用户提供的名称保存。 若要查看配置详细信息，请选择配置名称。 若要进行更改，请选择“编辑配置”。 

10. 完成配置设置后，可以对远程群集运行项目，或执行远程调试。

## <a name="learn-how-to-perform-remote-debugging"></a>了解如何执行远程调试
### <a name="scenario-1-perform-remote-run"></a>情景 1：执行远程运行

本部分演示如何调试驱动程序和执行程序。

    import org.apache.spark.{SparkConf, SparkContext}

    object LogQuery {
      val exampleApacheLogs = List(
        """10.10.10.10 - "FRED" [18/Jan/2013:17:56:07 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 315 "http://referall.com/" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.350 "-" - "" 265 923 934 ""
          | 62.24.11.25 images.com 1358492167 - Whatup""".stripMargin.lines.mkString,
        """10.10.10.10 - "FRED" [18/Jan/2013:18:02:37 +1100] "GET http://images.com/2013/Generic.jpg
          | HTTP/1.1" 304 306 "http:/referall.com" "Mozilla/4.0 (compatible; MSIE 7.0; Windows NT 5.1;
          | GTB7.4; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.04506.648; .NET CLR
          | 3.5.21022; .NET CLR 3.0.4506.2152; .NET CLR 1.0.3705; .NET CLR 1.1.4322; .NET CLR
          | 3.5.30729; Release=ARP)" "UD-1" - "image/jpeg" "whatever" 0.352 "-" - "" 256 977 988 ""
          | 0 73.23.2.15 images.com 1358492557 - Whatup""".stripMargin.lines.mkString
      )
      def main(args: Array[String]) {
        val sparkconf = new SparkConf().setAppName("Log Query")
        val sc = new SparkContext(sparkconf)
        val dataSet = sc.parallelize(exampleApacheLogs)
        // scalastyle:off
        val apacheLogRegex =
          """^([\d.]+) (\S+) (\S+) \[([\w\d:/]+\s[+\-]\d{4})\] "(.+?)" (\d{3}) ([\d\-]+) "([^"]+)" "([^"]+)".*""".r
        // scalastyle:on
        /** Tracks the total query count and number of aggregate bytes for a particular group. */
        class Stats(val count: Int, val numBytes: Int) extends Serializable {
          def merge(other: Stats): Stats = new Stats(count + other.count, numBytes + other.numBytes)
          override def toString: String = "bytes=%s\tn=%s".format(numBytes, count)
        }
        def extractKey(line: String): (String, String, String) = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              if (user != "\"-\"") (ip, user, query)
              else (null, null, null)
            case _ => (null, null, null)
          }
        }
        def extractStats(line: String): Stats = {
          apacheLogRegex.findFirstIn(line) match {
            case Some(apacheLogRegex(ip, _, user, dateTime, query, status, bytes, referer, ua)) =>
              new Stats(1, bytes.toInt)
            case _ => new Stats(1, 0)
          }
        }
        
        dataSet.map(line => (extractKey(line), extractStats(line)))
          .reduceByKey((a, b) => a.merge(b))
          .collect().foreach{
          case (user, query) => println("%s\t%s".format(user, query))}

        sc.stop()
      }
    }


1. 设置断点，然后选择“调试”图标。

   ![选择“调试”图标](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-icon.png)

2. 程序执行到达断点时，会在“调试程序”窗格中显示一个“驱动程序”选项卡和两个“执行程序”选项卡。 选择“恢复程序”图标继续运行代码，这会到达下一个断点，并且焦点位于相应“执行程序”选项卡上。可以在相应的“控制台”选项卡上查看执行日志。

   ![“调试”选项卡](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debugger-tab.png)

### <a name="scenario-2-perform-remote-debugging-and-bug-fixing"></a>情景 2：执行远程调试和 bug 修复
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
1. 设置两个断点，然后选择“调试”图标启动远程调试过程。

2. 代码在第一个断点处停止，“变量”窗格中显示参数和变量信息。 

3. 选择“恢复程序”图标继续运行。 代码在第二个断点处停止。 按预期方式捕获异常。

  ![引发错误](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-throw-error.png) 

4. 再次选择“恢复程序”图标。 “HDInsight Spark 提交”窗口随即显示“作业运行失败”错误。

  ![错误提交](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-error-submission.png) 

5. 要使用 IntelliJ 调试功能动态更新变量值，请再次选择“调试”。 “变量”窗格会再次出现。 

6. 右键单击“调试”选项卡上的目标，然后选择“设置值”。 接下来，输入变量的新值。 然后选择 **Enter** 保存该值。 

  ![设置值](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-set-value.png) 

7. 选择“恢复程序”图标继续运行程序。 这一次不会捕获到异常。 可以看到项目成功运行，无任何异常。

  ![调试后无异常](./media/hdinsight-apache-spark-intellij-tool-debug-remotely/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>后续步骤
* [概述：Azure HDInsight 上的 Apache Spark](hdinsight-apache-spark-overview.md)

### <a name="demo"></a>演示
* 创建 Scala 项目（视频）：[Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Spark Scala 应用程序）
* 远程调试（视频）：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用用于 IntelliJ 的 Azure 工具包远程调试 HDInsight 群集上的 Spark 应用程序）

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](hdinsight-apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用用于 IntelliJ 的 Azure 工具包为 HDInsight 群集创建 Spark 应用程序](hdinsight-apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Spark 应用程序](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](hdinsight-apache-spark-zeppelin-notebook.md)
* [可用于 HDInsight 中 Spark 群集上的 Jupyter 笔记本的内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](hdinsight-apache-spark-job-debugging.md)
