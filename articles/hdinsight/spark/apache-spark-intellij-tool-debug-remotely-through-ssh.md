---
title: Azure Toolkit for IntelliJ：通过 SSH 调试 Spark 应用
description: 逐步介绍如何使用用于 IntelliJ 的 Azure 工具包中的 HDInsight 工具通过 SSH 远程调试 HDInsight 群集上的应用程序
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/23/2019
ms.openlocfilehash: c032e900cd2f58581517b08905d5b0660ed8bbda
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82857813"
---
# <a name="debug-apache-spark-applications-on-an-hdinsight-cluster-with-azure-toolkit-for-intellij-through-ssh"></a>使用用于 IntelliJ 的 Azure 工具包通过 SSH 调试 HDInsight 群集上的 Apache Spark 应用程序

本文提供了有关如何使用[Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij)中的 hdinsight 工具在 hdinsight 群集上远程调试应用程序的分步指南。 要调试项目，还可以观看 [Debug HDInsight Spark applications with Azure Toolkit for IntelliJ](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)（使用用于 IntelliJ 的 Azure 工具包调试 HDInsight Spark 应用程序）视频。

## <a name="prerequisites"></a>先决条件

* HDInsight 上的 Apache Spark 群集。 请参阅[创建 Apache Spark 群集](../spark/apache-spark-jupyter-spark-sql-use-portal.md)。

* 对于 Windows 用户：在 Windows 计算机上运行本地 Spark Scala 应用程序时，可能会出现异常，如[Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356)中所述。 发生此异常的原因是 Windows 中缺少 WinUtils.exe。

    若要解决此错误，请将[winutils.exe](https://github.com/steveloughran/winutils)下载到某个位置，例如**C:\WinUtils\bin**。 然后，必须添加环境变量 HADOOP_HOME  ，并将其值设置为 C:\WinUtils  。

* [IntelliJ](https://www.jetbrains.com/idea/download/#section=windows) （社区版免费）。

* [Azure Toolkit for IntelliJ](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/installation)。

* [用于 IntelliJ 的 Scala 插件](../spark/apache-spark-intellij-tool-plugin.md#install-scala-plugin-for-intellij-idea)。

* SSH 客户端。 有关详细信息，请参阅[使用 SSH 连接到 HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md)。

## <a name="create-a-spark-scala-application"></a>创建 Spark Scala 应用程序

1. 启动 IntelliJ IDEA，选择“创建新项目”打开“新建项目”窗口。  

1. 从左侧窗格中选择“Apache Spark/HDInsight”  。

1. 从主窗口中选择**包含示例的 Spark 项目（Scala）** 。

1. 在“生成工具”下拉列表中选择下列其中一项：****

    * **Maven**：支持 Scala 项目创建向导。
    * **SBT**：用于管理依赖项和生成 Scala 项目。

     ![Intellij 创建新项目 Spark](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-create-projectfor-debug-remotely.png)

1. 选择“下一步”  。

1. 在下一个**新项目**窗口中，提供以下信息：

    |properties |说明 |
    |---|---|
    |项目名称|输入名称。 本演练通过使用`myApp`。|
    |项目位置|输入所需的位置用于保存项目。|
    |项目 SDK|如果为空，请选择 "**新建 ...** "，然后导航到 JDK。|
    |Spark 版本|创建向导集成了适当版本的 Spark SDK 和 Scala SDK。 如果 Spark 群集版本低于 2.0，请选择“Spark 1.x”  。 否则，请选择 " **Spark**2.x"。 本示例使用“Spark 2.3.0 (Scala 2.11.8)”。 |

   ![Intellij 新项目选择 Spark 版本](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-new-project.png)

1. 选择“完成”  。 可能需要在几分钟后才会显示该项目。 观看右下角的进度。

1. 展开你的项目，然后导航到 " **src** > **main** > **scala** > "**示例**。 双击**SparkCore_WasbIOTest**。

## <a name="perform-local-run"></a>执行本地运行

1. 在**SparkCore_WasbIOTest**脚本中，右键单击脚本编辑器，然后选择 " **SparkCore_WasbIOTest"** 选项来执行本地运行。

1. 本地运行完成后，可以看到输出文件保存到当前的项目资源管理器**数据** > **__默认值__**。

    ![Intellij 项目本地运行结果](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-local-run-result.png)

1. 执行本地运行和本地调试时，工具已自动设置默认的本地运行配置。 打开右上角的“[HDInsight 上的 Spark] XXX”配置，可以看到已在“HDInsight 上的 Apache Spark”下创建了“[HDInsight 上的 Spark]XXX”************。 切换到“本地运行”选项卡****。

    ![Intellij - 运行/调试配置 - 本地运行](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/local-run-configuration.png)

    - [环境变量](#prerequisites)：如果已将系统环境变量“HADOOP_HOME”**** 设置为“C:\WinUtils”****，则它可自动检测到此设置，而无需手动添加此变量。
    - [WinUtils.exe 位置](#prerequisites)：如果尚未设置此系统环境变量，则可单击其按钮找到它的位置。
    - 只需选择两个选项之一，在 MacOS 和 Linux 上不需要它们。

1. 也可在执行本地运行和本地调试前手动设置此配置。 在上面的屏幕截图中，选择加号（**+**）。 然后选择“HDInsight 上的 Apache Spark”选项。**** 输入“名称”、“主类名称”******** 信息并保存，然后单击本地运行按钮。

## <a name="perform-local-debugging"></a>执行本地调试

1. 打开“SparkCore_wasbloTest”脚本，设置断点。****

1. 右键单击脚本编辑器，然后选择“调试‘[HDInsight 上的 Spark]XXX’”选项来执行本地调试****。

## <a name="perform-remote-run"></a>执行远程运行

1. 导航到 "**运行** > **编辑配置 ...**"。从此菜单中，可以创建或编辑用于远程调试的配置。

1. 在“运行/调试配置”对话框中，选择加号 (+)********。 然后选择“HDInsight 上的 Apache Spark”选项。****

   ![Intellij 添加新配置](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-add-new-Configuration.png)

1. 切换到 "**在群集中远程运行**" 选项卡。输入**名称**、 **Spark 群集**和**主类名**的信息。 然后单击“高级配置(远程调试)”。**** 工具支持使用“执行器”**** 进行调试。 numExectors 默认值为 5****。 设置的值最好不要大于 3。

   ![Intellij 运行调试配置](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-run-debug-configurations.png)

1. 在“高级配置(远程调试)”部分，选择“启用 Spark 远程调试”。******** 输入 SSH 用户名，然后输入密码或使用私钥文件。 若要执行远程调试，则需要设置它。 若只想使用远程运行，则无需设置它。

   ![Intellij 高级配置启用 spark 远程调试](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-enable-spark-remote-debug.png)

1. 该配置现在以用户提供的名称保存。 若要查看配置详细信息，请选择配置名称。 若要进行更改，请选择“编辑配置”****。

1. 完成配置设置后，可以对远程群集运行项目，或执行远程调试。

   ![Intellij 调试远程 Spark 作业远程运行按钮](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/perform-remote-run-button.png)

1. 单击“断开连接”按钮，这样，提交日志就不显示在左面板中****。 但是，它仍在在后端上运行。

   ![Intellij 调试远程 Spark 作业远程运行结果](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/spark-remote-run-result.png)

## <a name="perform-remote-debugging"></a>执行远程调试

1. 设置断点，然后单击“远程调试”图标。**** 与远程提交的区别是需要配置 SSH 用户名/密码。

   ![Intellij 调试远程 Spark 作业调试图标](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-icon.png)

1. 程序执行到达断点时，会在“调试程序”窗格中显示一个“驱动程序”选项卡和两个“执行程序”选项卡。************ 选择“恢复程序”图标继续运行代码，这会到达下一个断点。**** 需要切换到正确的“执行程序”选项卡，以找到目标执行程序**** 来进行调试。 可以在相应的“控制台”选项卡上查看执行日志。****

   ![Intellij 调试远程 Spark 作业调试选项卡](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debugger-tab.png)

### <a name="perform-remote-debugging-and-bug-fixing"></a>执行远程调试和 bug 修复

1. 设置两个断点，然后选择“调试”图标启动远程调试过程。****

1. 代码在第一个断点处停止，“变量”窗格中显示参数和变量信息。****

1. 选择“恢复程序”图标继续运行。**** 代码在第二个断点处停止。 按预期方式捕获异常。

   ![Intellij 调试远程 Spark 作业引发错误](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-throw-error.png)

1. 再次选择“恢复程序”图标。**** “HDInsight Spark 提交”窗口随即显示“作业运行失败”错误。****

   ![Intellij 调试远程 Spark 作业错误提交](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-error-submission.png)

1. 要使用 IntelliJ 调试功能动态更新变量值，请再次选择“调试”****。 “变量”窗格会再次出现。****

1. 右键单击“调试”**** 选项卡上的目标，然后选择“设置值”****。 接下来，输入变量的新值。 然后选择 **Enter** 保存该值。

   ![Intellij 调试远程 Spark 作业集值](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-set-value1.png)

1. 选择“恢复程序”图标继续运行程序。**** 这一次不会捕获到异常。 可以看到项目成功运行，无任何异常。

   ![Intellij 调试远程 Spark 作业而不例外](./media/apache-spark-intellij-tool-debug-remotely-through-ssh/hdinsight-debug-without-exception.png)

## <a name="next-steps"></a>后续步骤

* [概述：Azure HDInsight 上的 Apache Spark](apache-spark-overview.md)

### <a name="demo"></a>演示

* 创建 Scala 项目（视频）：[Create Apache Spark Scala Applications](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)（创建 Apache Spark Scala 应用程序）
* 远程调试（视频）：[使用 Azure Toolkit for IntelliJ 远程调试 HDInsight 群集上的 Apache Spark 应用程序](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>方案

* [Apache Spark 与 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](apache-spark-use-bi-tools.md)
* [Apache Spark 和机器学习：使用 HDInsight 中的 Spark 结合 HVAC 数据分析建筑物温度](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>创建和运行应用程序

* [使用 Scala 创建独立的应用程序](../hdinsight-apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>工具和扩展

* [使用 Azure Toolkit for IntelliJ 为 HDInsight 群集创建 Apache Spark 应用程序](apache-spark-intellij-tool-plugin.md)
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Apache Spark 应用程序](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Apache Spark 应用程序](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](apache-spark-jupyter-notebook-kernels.md)
* [将外部包与 Jupyter 笔记本配合使用](apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>管理资源

* [管理 Azure HDInsight 中 Apache Spark 群集的资源](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](apache-spark-job-debugging.md)
