---
title: "Azure HDInsight 工具 -将 Visual Studio Code 用于 Hive、LLAP 或 pySpark | Microsoft Docs"
description: "学习如何使用用于 Visual Studio Code 的 Azure HDInsight 工具来创建、提交查询和脚本。"
Keywords: "VScode,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,交互式 Hive,交互式查询"
services: HDInsight
documentationcenter: 
author: jejiang
manager: 
editor: jgao
tags: azure-portal
ms.assetid: 
ms.service: HDInsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/26/2017
ms.author: jejiang
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: fdf1911f4f5b515592b6197ea738d808104cb43e
ms.contentlocale: zh-cn
ms.lasthandoff: 09/27/2017

---

# <a name="use-azure-hdinsight-tool-for-visual-studio-code"></a>使用用于 Visual Studio Code 的 Azure HDInsight 工具

学习如何使用用于 Visual Studio Code (VSCode) 的 Azure HDInsight 工具来创建和提交 Hive 批处理作业、交互式 Hive 查询和 pySpark 脚本。 可以在 VSCode 支持的平台（包括 Windows、Linux 和 MacOS）上安装 Azure HDInsight 工具。 可以找到不同平台的必备组件。


## <a name="prerequisites"></a>先决条件

完成本文需要具有以下项：

- HDInsight 群集。  若要创建群集，请参阅 [HDInsight 入门]( hdinsight-hadoop-linux-tutorial-get-started.md)。
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。
- [Mono](http://www.mono-project.com/docs/getting-started/install/)。 只有 Linux 和 MacOS 需要 Mono。

## <a name="install-the-hdinsight-tools"></a>安装 HDInsight 工具
   
安装必备组件后，可以安装用于 VSCode 的 Azure HDInsight 工具。 

**安装 Azure HDInsight 工具**

1. 打开 **Visual Studio Code**。
2. 单击左侧窗格中的“扩展”。 在搜索框中输入 HDInsight。
3. 单击“Azure HDInsight 工具”旁边的“安装”。 数秒后，“安装”按钮将变为“重载”。
4. 单击“重载”以激活“Azure HDInsight 工具”扩展。
5. 单击“重载窗口”以进行确认。 在“扩展”窗格中，可看到“Azure HDInsight 工具”。

   ![用于 Visual Studio Code 的 HDInsight - Python 安装](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

对于 Spark 用户，建议使用 Python 扩展以获得更好的语言服务体验。 对于 Hive 用户，Python 扩展不是必需的。 

**安装 Python**

1. 打开 **Visual Studio Code**。
2. 单击左侧窗格中的“扩展”。 在搜索框中，输入“python”。 然后，可以看到 python 扩展列表。 其中一个是 **Python**。
3. 单击“Python”旁边的“安装”。 数秒后，“安装”按钮将变为“重载”。
4. 单击“重载”以激活 **Python** 扩展。
5. 单击“重载窗口”以进行确认。 可以在“扩展”窗格中看到“Python”。

     ![用于 Visual Studio Code 的 HDInsight - Python 安装](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

## <a name="open-hdinsight-workspace"></a>打开 HDInsight 工作区

必须在 VSCode 中创建工作区，然后才能连接到 Azure。

**打开工作区**

1. 从“文件”菜单中，单击“打开文件夹”，指定一个 现有文件夹或者创建一个新文件夹作为工作文件夹。 该文件夹将出现在左窗格中。
2. 从左窗格中，单击工作文件夹旁边的“新建文件”图标。

   ![新建文件](./media/hdinsight-for-vscode/new-file.png)
3. 为新文件命名，以 .hql（Hive 查询）或 .py（Spark 脚本）作为文件扩展名。 请注意，一个 **XXXX_hdi_settings.json** 配置文件会自动添加到工作文件夹。
4. 从“浏览器”中打开 **XXXX_hdi_settings.json**，或者右键单击脚本编辑器以选择“Set Configuration”。 可以配置登录入口、默认群集和作业提交参数，如文件中的示例所示。 还可以将其余参数保留为空。

## <a name="connect-to-azure"></a>连接到 Azure

需要连接到 Azure 帐户，然后才能从 VSCode 将脚本提交到 HDInsight 群集。

**连接到 Azure 的方法**

1. 创建一个新的工作文件夹和一个新的脚本文件（如果还没有）。
2. 右键单击脚本编辑器，然后从上下文菜单中选择“HDInsight: Login”。 还可以按 **CTRL+SHIFT+P** 并输入“HDInsight: Login”。

    ![用于 Visual Studio Code 的 HDInsight 工具登录](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)
3. 按照“输出”窗格中的登录说明进行登录。

    Azure：![用于 Visual Studio Code 的 HDInsight 工具登录信息](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    连接后，VSCode 窗口左下角的状态栏上会显示 Azure 帐户名称。 

    > [!NOTE] 
    > 如果 Azure 帐户已启用双重身份验证，建议使用手机身份验证，而不使用 Pin 身份验证。
    > 这是使用 Google Chrome 进行 Azure 登录时的一个已知问题。

4. 右键单击脚本编辑器以打开上下文菜单：

    ![用于 Visual Studio Code 的 HDInsight 工具脚本编辑器上下文菜单](./media/hdinsight-for-vscode/hdinsight-for-vscode-context-menu.png)

    从上下文菜单中，可以执行以下任务：

    - logout
    - 列出群集
    - 设置默认群集
    - 提交交互式 Hive 查询
    - 提交 Hive 批处理脚本
    - 设置配置

## <a name="list-hdinsight-clusters"></a>列出 HDInsight 群集

若要测试连接，可以列出 HDInsight 群集：

**列出 Azure 订阅下的 HDInsight 群集**
1. 打开一个工作区并连接到 Azure。 请参阅[打开 HDInsight 工作区](#open-hdinsight-workspace)和[连接到 Azure](#connect-to-azure)。
2. 右键单击脚本编辑器，然后从上下文菜单中选择“HDInsight: List Cluster”。 
3. Hive 和 Spark 群集将显示在“输出”窗格中。

    ![设置默认群集配置](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>设置默认群集
1. 打开一个工作区并连接到 Azure。 请参阅[打开 HDInsight 工作区](#open-hdinsight-workspace)和[连接到 Azure](#connect-to-azure)。
2. 右键单击脚本编辑器，然后单击“HDInsight: Set Default Cluster”。 
3. 选择一个群集作为当前脚本文件的默认群集。 工具将自动更新配置文件 **XXXX_hdi_settings.json**。 

   ![设置默认群集配置](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-azure-environment"></a>设置 Azure 环境 
1. 按 **CTRL+SHIFT+P** 打开命令面板。
2. 输入“HDInsight: Set Azure Environment”。
3. 从 Azure 和 AzureChina 中选择一个作为默认登录入口。
4. 同时，我们的工具已将你选择的默认登录入口保存到 **XXXX_hdi_settings.json** 中。 还可以在此配置文件中直接更新默认登录入口。 

   ![设置默认登录入口配置](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>提交交互式 Hive 查询

通过用于 VSCode 的 HDInsight 工具，可以将交互式 Hive 查询提交到 HDInsight Interactive Query 群集。

1. 创建一个新的工作文件夹和一个新的 Hive 脚本文件（如果还没有）。
2. 连接到 Azure 帐户，然后配置默认群集（如果尚未执行此操作）。
3. 将以下代码复制并粘贴到 Hive 文件中，然后保存该文件。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. 右键单击脚本编辑器，然后单击“HDInsight: Interactive Hive”以提交查询。 工具还允许使用上下文菜单提交代码块而非整个脚本文件。 很快，查询结果将显示在一个新选项卡中：

   ![交互式 Hive 结果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - “结果”面板：可以将整个结果作为 CSV、JSON、EXCEL 保存到本地路径，也可以只选择多个行。
    - “消息”面板：单击**行**号会跳转到运行的脚本的第一行。

与[运行 Hive 批处理作业](#submit-hive-batch-scripts)相比，交互式查询花费的时间更少。

## <a name="submit-hive-batch-scripts"></a>提交 Hive 批处理脚本

1. 创建一个新的工作文件夹和一个新的 Hive 脚本文件（如果还没有）。
2. 连接到 Azure 帐户，然后配置默认群集（如果尚未执行此操作）。
3. 将以下代码复制并粘贴到 Hive 文件中，然后保存该文件。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. 右键单击脚本编辑器，然后单击“HDInsight: Submit Hive Batch Script”以提交 Hive 作业。 
4. 选择要提交到的群集。  

    提交 Hive 作业后，“输出”面板中将显示提交成功信息和作业 ID。 并且，它将打开“WEB 浏览器”，作业实时日志和状态显示在其中。

   ![提交 Hive 作业结果](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

与[提交交互式 Hive 查询](#submit-interactive-hive-queries)相比，批处理作业需要花费更长的时间。


## <a name="submit-pyspark-job"></a>提交 PySpark 作业

1. 创建一个新的工作文件夹和一个扩展名为 .py 的新脚本文件（如果尚没有）。
2. 连接到 Azure 帐户，然后配置默认群集（如果尚未执行此操作）。
3. 将以下代码复制并粘贴到脚本文件中：

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```
4. 右键单击脚本编辑器，然后单击“HDInsight: Submit PySpark Job”。 
5. 选择一个群集以提交 PySpark 作业。 

   ![提交 python 作业结果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

提交 python 作业后，会在 VSCode 中的“输出”窗口中显示提交日志。 同时还会显示 **Spark UI URL** 和 **Yarn UI URL**。 可以在 Web 浏览器中打开 URL 来跟踪作业状态。


## <a name="additional-features"></a>其他功能

用于 VSCode 的 HDInsight 支持以下功能：

- **IntelliSense 自动完成**。 弹出关键字、方法、变量等相关建议。不同图标表示不同类型的对象：

    ![用于 Visual Studio Code 的 HDInsight 工具 IntelliSense 对象类型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 错误标记**。 语言服务会为 Hive 脚本的编辑错误添加下划线。     
- **语法突出显示**。 语言服务使用不同颜色来区分变量、关键字、数据类型、函数等等。 

    ![用于 Visual Studio Code 的 HDInsight 工具语法突出显示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>后续步骤

### <a name="demo"></a>演示
* 用于 VScode 的 HDInsight：[视频](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和扩展
* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Spark 应用程序](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Spark 应用程序](hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](hdinsight-apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](hdinsight-apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](hdinsight-apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Hive 数据](./hdinsight-connect-hive-power-bi.md)。
* [在 Azure HDInsight 中使用 Zeppelin 运行 Hive 查询](./hdinsight-connect-hive-zeppelin.md)。

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](hdinsight-apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](hdinsight-apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](hdinsight-apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="managing-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](hdinsight-apache-spark-job-debugging.md)




