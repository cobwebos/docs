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
ms.date: 11/25/2017
ms.author: jejiang
ms.openlocfilehash: 87cda776195dc93a35c6e978b18e823bf54c9ffb
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="debug-spark-applications-locally-or-remotely-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>使用用于 IntelliJ 的 Azure 工具包通过 SSH 本地或远程调试 HDInsight 群集上的 Spark 应用程序

本文逐步介绍如何使用用于 IntelliJ 的 Azure 工具包中的 HDInsight 工具远程调试 HDInsight 群集上的应用程序。 要调试项目，还可以观看 [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用用于 IntelliJ 的 Azure 工具包调试 HDInsight Spark 应用程序）视频。

**先决条件**
* **Azure Toolkit for IntelliJ 中的 HDInsight 工具**。 此工具是用于 IntelliJ 的 Azure 工具包的一部分。 有关详细信息，请参阅[安装用于 IntelliJ 的 Azure 工具包](https://docs.microsoft.com/azure/azure-toolkit-for-intellij-installation)。 **用于 IntelliJ 的 Azure 工具包**。 使用此工具包为 HDInsight 群集创建 Spark 应用程序。 有关详细信息，请遵循[使用用于 IntelliJ 的 Azure 工具包为 HDInsight 群集创建 Spark 应用程序](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-apache-spark-intellij-tool-plugin)中的说明。

* **可管理用户名和密码的 HDInsight SSH 服务**。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Hadoop)](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix) 和[使用 SSH 隧道访问 Ambari Web UI、JobHistory、NameNode、Oozie 和其他 Web UI](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-linux-ambari-ssh-tunnel)。 
 
## <a name="learn-how-to-perform-local-run-and-debugging"></a>了解如何执行本地运行和调试
### <a name="scenario-1-create-a-spark-scala-application"></a>情景 1：创建 Spark Scala 应用程序 

1. 启动 IntelliJ IDEA 并创建一个项目。 在“新建项目”对话框中执行以下操作：

   a. 选择“HDInsight”。 

   b. 根据偏好选择 Java 或 Scala 模板。 选择下列选项之一：

      - **Spark on HDInsight (Scala)**

      - **Spark on HDInsight (Java)**

      - **Spark on HDInsight 示例 (Scala)**

      本示例使用“Spark on HDInsight 示例 (Scala)”模板。

   c. 在“生成工具”列表中，根据需要选择以下选项之一：

      - 用于支持 Scala 项目创建向导的“Maven”

      -  用于管理依赖项和生成 Scala 项目的“SBT” 

      ![创建调试项目](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

   d.单击“下一步”。 选择“下一步”。     
 
2. 在下一个“新建项目”窗口中执行以下操作：

   ![选择 Spark SDK](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

   a. 输入项目名称和项目位置。

   b. 在“项目 SDK”下拉列表中，选择适用于 Spark 2.x 群集的“Java 1.8”或选择适用于 Spark 1.x 群集的“Java 1.7”。

   c. 在“Spark 版本”下拉列表中，Scala 项目创建向导集成了 Spark SDK 和 Scala SDK 的正确版本。 如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”。 否则，请选择“Spark 2.x”。 本示例使用“Spark 2.0.2 (Scala 2.11.8)”。

   d.单击“下一步”。 选择“完成”。

3. 选择“src” > “main” > “scala”，在项目中打开代码。 本示例使用“SparkCore_wasbloTest”脚本。

### <a name="prerequisite-for-windows"></a>Windows 先决条件
在 Windows 计算机上运行本地 Spark Scala 应用程序时，可能会发生 [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) 中所述的异常。 发生此异常的原因是 Windows 中缺少 WinUtils.exe。 

若要解决此错误，请[下载该可执行文件](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe)到某个位置（例如 **C:\WinUtils\bin**）。 然后，必须添加环境变量 HADOOP_HOME，并将其值设置为 C:\WinUtils。

### <a name="scenario-2-perform-local-run"></a>情景 2：执行本地运行
1. 打开 SparkCore_wasbloTest 脚本，右键单击脚本编辑器，然后选择“运行 [Spark 作业]XXX”选项来执行本地运行。
2. 本地运行完成后，可以看到输出文件保存到当前的项目资源管理器的“数据” > “默认”中。

    ![本地运行结果](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-result.png)
3. 执行本地运行和本地调试时，工具已自动设置默认的本地运行配置。 打开右上角的“[Spark 作业] XXX”配置，可以看到已在“Azure HDInsight Spark 作业”下创建了“[Spark 作业]XXX”。 切换到“本地运行”选项卡。

    ![本地运行配置](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)
    - [环境变量](#prerequisite-for-windows)：如果已将系统环境变量“HADOOP_HOME”设置为“C:\WinUtils”，则它可自动检测到此设置，而无需手动添加此变量。
    - [WinUtils.exe 位置](#prerequisite-for-windows)：如果尚未设置此系统环境变量，则可单击其按钮找到它的位置。
    - 只需选择两个选项之一，在 MacOS 和 Linux 上不需要它们。
4. 也可在执行本地运行和本地调试前手动设置此配置。 在前面的屏幕截图中，选择加号（“+”）。 然后选择“Azure HDInsight Spark 作业”选项。 输入“名称”、“主类名称”信息并保存，然后单击本地运行按钮。

### <a name="scenario-3-perform-local-debugging"></a>情景 3：执行本地调试
1. 打开“SparkCore_wasbloTest”脚本，设置断点。
2. 右键单击脚本编辑器，然后选择“调试 [Spark 作业]XXX”选项来执行本地调试。   



## <a name="learn-how-to-perform-remote-run-and-debugging"></a>了解如何执行远程运行和调试
### <a name="scenario-1-perform-remote-run"></a>情景 1：执行远程运行

1. 要访问“编辑配置”菜单，请选择右上角的图标。 在此菜单中，可以创建或编辑配置以进行远程调试。

   ![编辑配置](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-edit-configurations.png) 

2. 在“运行/调试配置”对话框中，选择加号 (+)。 然后选择“Azure HDInsight Spark 作业”选项。

   ![添加新配置](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)
3. 切换到“在群集中远程运行”选项卡。为“名称”、“Spark 群集”和“Main 类名”输入信息。 然后选择“高级配置”。 工具支持使用“执行器”进行调试。 numExectors 默认值为 5。 设置的值最好不要大于 3。

   ![运行调试配置](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

4. 在“Spark 提交高级配置”对话框中，选择“启用 Spark 远程调试”。 输入 SSH 用户名，然后输入密码或使用私钥文件。 要保存配置，请选择“确定”。 若要执行远程调试，则需要设置它。 若只想使用远程运行，则无需设置它。

   ![启用 Spark 远程调试](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

5. 该配置现在以用户提供的名称保存。 若要查看配置详细信息，请选择配置名称。 若要进行更改，请选择“编辑配置”。 

6. 完成配置设置后，可以对远程群集运行项目，或执行远程调试。
   
   ![远程运行按钮](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run.png)

7. 单击“断开连接”按钮，提交日志未显示在左面板中。 但是，它仍在在后端上运行。

   ![远程运行按钮](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/remote-run-result.png)



### <a name="scenario-2-perform-remote-debugging"></a>情景 2：执行远程调试
1. 设置断点，然后单击“远程调试”图标。 与远程提交的区别是需要配置 SSH 用户名/密码。

   ![选择“调试”图标](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

2. 程序执行到达断点时，会在“调试程序”窗格中显示一个“驱动程序”选项卡和两个“执行程序”选项卡。 选择“恢复程序”图标继续运行代码，这会到达下一个断点。 需要切换到正确的“执行程序”选项卡，以找到目标执行程序来进行调试。 可以在相应的“控制台”选项卡上查看执行日志。

   ![“调试”选项卡](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="scenario-3-perform-remote-debugging-and-bug-fixing"></a>情景 3：执行远程调试和 bug 修复
1. 设置两个断点，然后选择“调试”图标启动远程调试过程。

2. 代码在第一个断点处停止，“变量”窗格中显示参数和变量信息。 

3. 选择“恢复程序”图标继续运行。 代码在第二个断点处停止。 按预期方式捕获异常。

   ![引发错误](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png) 

4. 再次选择“恢复程序”图标。 “HDInsight Spark 提交”窗口随即显示“作业运行失败”错误。

   ![错误提交](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png) 

5. 要使用 IntelliJ 调试功能动态更新变量值，请再次选择“调试”。 “变量”窗格会再次出现。 

6. 右键单击“调试”选项卡上的目标，然后选择“设置值”。 接下来，输入变量的新值。 然后选择 **Enter** 保存该值。 

   ![设置值](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value.png) 

7. 选择“恢复程序”图标继续运行程序。 这一次不会捕获到异常。 可以看到项目成功运行，无任何异常。

   ![调试后无异常](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="seealso"></a>后续步骤
* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>演示
* 创建 Scala 项目（视频）：[Create Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Spark Scala 应用程序）
* 远程调试（视频）：[Use Azure Toolkit for IntelliJ to debug Spark applications remotely on an HDInsight cluster](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用用于 IntelliJ 的 Azure 工具包远程调试 HDInsight 群集上的 Spark 应用程序）

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](../hdinsight-apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用用于 IntelliJ 的 Azure 工具包为 HDInsight 群集创建 Spark 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [可用于 HDInsight 中 Spark 群集上的 Jupyter 笔记本的内核](apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
