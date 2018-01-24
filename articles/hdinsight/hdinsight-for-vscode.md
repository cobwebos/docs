---
title: "Azure HDInsight 工具 -将 Visual Studio Code 用于 Hive、LLAP 或 pySpark | Microsoft Docs"
description: "了解如何使用用于 Visual Studio Code 的 Azure HDInsight 工具来创建、提交查询和脚本。"
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
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
ms.date: 10/27/2017
ms.author: jejiang
ms.openlocfilehash: 89e83dc02f32f6f2a781cf2e35040b29cc3d3c06
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2018
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>使用用于 Visual Studio Code 的 Azure HDInsight 工具

学习如何使用用于 Visual Studio Code (VS Code) 的 Azure HDInsight 工具来创建和提交 Hive 批处理作业、交互式 Hive 查询和 pySpark 脚本。 可以在 VS Code 支持的平台上安装 Azure HDInsight 工具。 这些平台包括 Windows、Linux 和 macOS。 可以找到不同平台的必备组件。


## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要具有以下项：

- HDInsight 群集。  若要创建群集，请参阅 [HDInsight 入门]( hdinsight-hadoop-linux-tutorial-get-started.md)。
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。
- [Mono](http://www.mono-project.com/docs/getting-started/install/)。 只有 Linux 和 macOS 需要 Mono。

## <a name="install-the-hdinsight-tools"></a>安装 HDInsight 工具
   
安装必备组件后，可以安装用于 VS Code 的 Azure HDInsight 工具。 

**安装 Azure HDInsight 工具**

1. 打开 Visual Studio Code。

2. 在左窗格中，选择“扩展”。 在搜索框中输入 **HDInsight**。

3. 选择“Azure HDInsight 工具”旁边的“安装”。 数秒后，“安装”按钮会变为“重载”。

4. 选择“重载”以激活“Azure HDInsight 工具”扩展。

5. 选择“重载窗口”以进行确认。 在“扩展”窗格中，可看到“Azure HDInsight 工具”。

   ![用于 Visual Studio Code 的 HDInsight - Python 安装](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-workspace"></a>打开 HDInsight 工作区

需要在 VS Code 中创建工作区，然后才能连接到 Azure。

**打开工作区**

1. 在“文件”菜单中，选择“打开文件夹”。 然后指定一个 现有文件夹或者创建一个新文件夹作为工作文件夹。 该文件夹将出现在左窗格中。

2. 从左窗格中，选择工作文件夹旁边的“新建文件”图标。

   ![新建文件](./media/hdinsight-for-vscode/new-file.png)

3. 为新文件命名，以 .hql（Hive 查询）或 .py（Spark 脚本）作为文件扩展名。 请注意，一个 **XXXX_hdi_settings.json** 配置文件会自动添加到工作文件夹。

4. 从“资源管理器”中打开 **XXXX_hdi_settings.json**，或者右键单击脚本编辑器以选择“设置配置”。 可以配置登录入口、默认群集和作业提交参数，如文件中的示例所示。 还可以将其余参数保留为空。

## <a name="connect-to-azure"></a>连接到 Azure

需要连接到 Azure 帐户，然后才能从 VS Code 将脚本提交到 HDInsight 群集。

**连接到 Azure 的方法**

1. 创建一个新的工作文件夹和一个新的脚本文件（如果还没有）。

2. 右键单击脚本编辑器，然后在上下文菜单中选择“HDInsight: Login”。 也可以按 **Ctrl+Shift+P** 并输入“HDInsight: Login”。

    ![用于 Visual Studio Code 的 HDInsight 工具登录](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. 按照“输出”窗格中的登录说明进行登录。

    Azure：![用于 Visual Studio Code 的 HDInsight 工具登录信息](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-Azurelogin-info.png)

    连接后，VS Code 窗口左下角的状态栏上会显示 Azure 帐户名称。 

    > [!NOTE]
    > 由于已知的 Azure 身份验证问题，需要以专用模式或匿名模式打开浏览器。 如果 Azure 帐户已启用双重身份验证，我们建议使用手机身份验证，而不要使用 PIN 身份验证。
  

4. 右键单击脚本编辑器打开上下文菜单。 从上下文菜单中，可以执行以下任务：

    - 注销
    - 列出群集
    - 设置默认群集
    - 提交交互式 Hive 查询
    - 提交 Hive 批处理脚本
    - 提交交互式 PySpark 查询
    - 提交 PySpark 批处理脚本
    - 设置配置

## <a name="list-hdinsight-clusters"></a>列出 HDInsight 群集

若要测试连接，可以列出 HDInsight 群集：

**列出 Azure 订阅下的 HDInsight 群集**
1. 打开一个工作区并连接到 Azure。 有关详细信息，请参阅[打开 HDInsight 工作区](#open-hdinsight-workspace)和[连接到 Azure](#connect-to-azure)。

2. 右键单击脚本编辑器，然后从上下文菜单中选择“HDInsight: List Cluster”。 

3. Hive 和 Spark 群集将显示在“输出”窗格中。

    ![设置默认群集配置](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>设置默认群集
1. 打开一个工作区并连接到 Azure。 请参阅[打开 HDInsight 工作区](#open-hdinsight-workspace)和[连接到 Azure](#connect-to-azure)。

2. 右键单击脚本编辑器，然后选择“HDInsight: Set Default Cluster”。 

3. 选择一个群集作为当前脚本文件的默认群集。 工具将自动更新配置文件 **XXXX_hdi_settings.json**。 

   ![设置默认群集配置](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>设置 Azure 环境 
1. 按 **CTRL+SHIFT+P** 打开命令面板。

2. 输入“HDInsight: Set Azure Environment”。

3. 从 Azure 和 AzureChina 中选择一个作为默认登录入口。

4. 同时，工具已将选择的默认登录入口保存到 **XXXX_hdi_settings.json** 中。 还可以在此配置文件中直接更新默认登录入口。 

   ![设置默认登录入口配置](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries"></a>提交交互式 Hive 查询

通过用于 VS Code 的 HDInsight 工具，可以将交互式 Hive 查询提交到 HDInsight 交互式查询群集。

1. 创建一个新的工作文件夹和一个新的 Hive 脚本文件（如果还没有）。

2. 连接到 Azure 帐户，然后配置默认群集（如果尚未执行此操作）。

3. 将以下代码复制并粘贴到 Hive 文件中，然后保存该文件。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. 右键单击脚本编辑器，然后选择“HDInsight: Hive Interactive”以提交查询。 工具还允许使用上下文菜单提交代码块而非整个脚本文件。 很快，查询结果将显示在新选项卡中。

   ![交互式 Hive 结果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - “结果”面板：可以将整个结果作为 CSV、JSON、Excel 保存到本地路径，也可以只选择多个行。

    - “消息”面板：选择**行**号会跳转到运行的脚本的第一行。

与[运行 Hive 批处理作业](#submit-hive-batch-scripts)相比，运行交互式查询花费的时间要少得多。

## <a name="submit-hive-batch-scripts"></a>提交 Hive 批处理脚本

1. 创建一个新的工作文件夹和一个新的 Hive 脚本文件（如果还没有）。

2. 连接到 Azure 帐户，然后配置默认群集（如果尚未执行此操作）。

3. 将以下代码复制并粘贴到 Hive 文件中，然后保存该文件。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
3. 右键单击脚本编辑器，然后选择“HDInsight: Hive Batch”以提交 Hive 作业。 

4. 选择想要提交的群集。  

    提交 Hive 作业后，“输出”面板中将显示提交成功信息和作业 ID。 Hive 作业还会打开“WEB 浏览器”，其中显示了实时作业日志和状态。

   ![提交 Hive 作业结果](./media/hdinsight-for-vscode/submit-Hivejob-result.png)

[提交交互式 Hive 查询](#submit-interactive-hive-queries)所花费的时间比提交批处理作业要少得多。

## <a name="submit-interactive-pyspark-queries"></a>提交交互式 PySpark 查询
使用用于 VS Code 的 HDInsight 工具还可以将交互式 PySpark 查询提交到 Spark 群集。
1. 创建一个新的工作文件夹和一个扩展名为 .py 的新脚本文件（如果没有）。

2. 连接到 Azure 帐户（如果尚未这样做）。

3. 将以下代码复制并粘贴到脚本文件中：
   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```
4. 突出显示这些脚本。 右键单击脚本编辑器，然后选择“HDInsight: PySpark Interactive”。

5. 如果尚未在 VS Code 中安装 **Python** 扩展，请选择下图所示的“安装”按钮。

    ![用于 Visual Studio Code 的 HDInsight - Python 安装](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

6. 在系统中安装 Python 环境（如果尚未安装）。 
   - 对于 Windows，请下载并安装 [Python](https://www.python.org/downloads/)。 然后确保 `Python` 和 `pip` 已在系统 PATH 中。

   - 有关 macOS 和 Linux 的说明，请参阅[为 Visual Studio Code 设置 PySpark 交互式环境](set-up-pyspark-interactive-environment.md)。

7. 选择要将 PySpark 查询提交到的群集。 很快，查询结果将显示在右侧的新选项卡中：

   ![提交 python 作业结果](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
8. 工具还支持查询 **SQL 子句**查询。

   ![提交 python 作业结果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)运行查询时，提交状态将显示在底部状态栏的左侧。 当状态为“PySpark 内核(忙)”时，请不要提交其他查询。 

>[!NOTE]
>群集可以保留会话信息。 定义的变量、函数和相应值将保留在会话中，因此，可在针对同一群集发出的多个服务调用中引用这些信息。 
 

## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批处理作业

1. 创建一个新的工作文件夹和一个扩展名为 .py 的新脚本文件（如果没有）。

2. 连接到 Azure 帐户（如果尚未这样做）。

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
4. 右键单击脚本编辑器，然后选择“HDInsight: PySpark Batch”。 

5. 选择要将 PySpark 作业提交到的群集。 

   ![提交 python 作业结果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

提交 Python 作业后，提交日志会显示在 VS Code 的“输出”窗口中。 同时还会显示 **Spark UI URL** 和 **Yarn UI URL**。 可以在 Web 浏览器中打开 URL 来跟踪作业状态。


## <a name="additional-features"></a>其他功能

用于 VS Code 的 HDInsight 支持以下功能：

- **IntelliSense 自动完成**。 弹出关键字、方法、变量等相关建议。 不同图标表示不同类型的对象。

    ![用于 Visual Studio Code 的 HDInsight 工具 IntelliSense 对象类型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 错误标记**。 语言服务会为 Hive 脚本的编辑错误添加下划线。     
- **语法突出显示**。 语言服务使用不同颜色来区分变量、关键字、数据类型、函数等等。 

    ![用于 Visual Studio Code 的 HDInsight 工具语法突出显示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="next-steps"></a>后续步骤

### <a name="demo"></a>演示
* 用于 VS Code 的 HDInsight：[视频](https://go.microsoft.com/fwlink/?linkid=858706)

### <a name="tools-and-extensions"></a>工具和扩展

* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Spark 应用程序](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Spark 应用程序](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Spark 应用程序](spark/apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Spark 群集中使用 Zeppelin 笔记本](spark/apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Spark 群集中可用于 Jupyter 笔记本的内核](spark/apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](spark/apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 可视化 Hive 数据](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Hive 数据](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [为 Visual Studio Code 设置 PySpark 交互式环境](set-up-pyspark-interactive-environment.md)
* [在 Azure HDInsight 中使用 Zeppelin 运行 Hive 查询](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>方案
* [Spark 和 BI：使用 HDInsight 中的 Spark 和 BI 工具执行交互式数据分析](spark/apache-spark-use-bi-tools.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 对使用 HVAC 数据生成温度进行分析](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Spark 和机器学习：使用 HDInsight 中的 Spark 预测食品检查结果](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Spark 流式处理：使用 HDInsight 中的 Spark 生成实时流式处理应用程序](spark/apache-spark-eventhub-streaming.md)
* [使用 HDInsight 中的 Spark 分析网站日志](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](spark/apache-spark-create-standalone-application.md)
* [使用 Livy 在 Spark 群集中远程运行作业](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](spark/apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](spark/apache-spark-job-debugging.md)



