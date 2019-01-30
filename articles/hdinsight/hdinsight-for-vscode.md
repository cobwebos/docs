---
title: Azure HDInsight 工具 -将 Visual Studio Code 用于 Hive、LLAP 或 PySpark | Microsoft Docs
description: 了解如何使用用于 Visual Studio Code 的 Azure HDInsight 工具来创建、提交查询和脚本。
Keywords: VS Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/15/2018
ms.openlocfilehash: cd7c4014752fb5fa014fd8b5204206cd4efbfdce
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818517"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>使用用于 Visual Studio Code 的 Azure HDInsight 工具

了解如何使用用于 Visual Studio Code (VS Code) 的 Azure HDInsight 工具，为 Apache Spark 创建和提交 Apache Hive 批处理作业、交互式 Hive 查询和 PySpark 脚本。 首先我们将介绍如何在 VS Code 中安装 HDInsight 工具，然后我们将演练如何向 Hive 和 Spark 提交作业。 

可以在 VSCode 支持的平台（包括 Windows、Linux 和 macOS）上安装 Azure HDInsight 工具。 可以在下面找到不同平台的必备组件。


## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要具有以下项：

- HDInsight 群集。 若要创建群集，请参阅 [HDInsight 入门](hadoop/apache-hadoop-linux-tutorial-get-started.md)。
- [Visual Studio Code](https://www.visualstudio.com/products/code-vs.aspx)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 只有 Linux 和 macOS 需要 Mono。

## <a name="install-the-hdinsight-tools"></a>安装 HDInsight 工具
   
安装必备组件后，可以安装用于 VS Code 的 Azure HDInsight 工具。 

### <a name="to-install-azure-hdinsight-tools"></a>安装 Azure HDInsight 工具

1. 打开 Visual Studio Code。

2. 在左窗格中，选择“扩展”。 在搜索框中输入 **HDInsight**。

3. 选择“Azure HDInsight 工具”旁边的“安装”。 数秒后，“安装”按钮会变为“重载”。

4. 选择“重载”以激活“Azure HDInsight 工具”扩展。

5. 选择“重载窗口”以进行确认。 在“扩展”窗格中，可看到“Azure HDInsight 工具”。

   ![用于 Visual Studio Code 的 HDInsight - Python 安装](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

## <a name="open-hdinsight-work-folder"></a>打开 HDInsight 工作文件夹

需要在 VS Code 中创建工作文件夹，然后才能连接到 Azure。

### <a name="to-open-a-work-folder"></a>打开工作文件夹

1. 在“文件”菜单中，选择“打开文件夹”。 然后指定一个 现有文件夹或者创建一个新文件夹作为工作文件夹。 该文件夹将出现在左窗格中。

2. 从左窗格中，选择工作文件夹旁边的“新建文件”图标。

   ![新建文件](./media/hdinsight-for-vscode/new-file.png)

3. 为新文件命名，以 .hql（Hive 查询）或 .py（Spark 脚本）作为文件扩展名。 

## <a name="connect-to-hdinsight-cluster"></a>连接到 HDInsight 群集

将脚本从 VS Code 提交到 HDInsight 群集之前，需要连接到 Azure 帐户，或链接群集（使用 Ambari 用户名/密码或已加入域的帐户）。

### <a name="to-connect-to-azure"></a>连接到 Azure

1. 创建一个新的工作文件夹和一个新的脚本文件（如果还没有）。

2. 右键单击脚本编辑器，然后在上下文菜单中选择“HDInsight:Login”。 也可以按 Ctrl+Shift+P 并输入“HDInsight:Login”。

    ![用于 Visual Studio Code 的 HDInsight 工具 - 登录](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

3. 按照“输出”窗格中的登录说明进行登录。
    + 对于 Azure 全局环境，可使用“HDInsight:Login”命令在 HDInsight 资源管理器中触发“登录 Azure”操作，反之亦然。

        ![适用于 Azure 的登录说明](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + 在其他环境中，请遵照登录说明操作。

        ![适用于其他环境的登录说明](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

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

<h3 id="linkcluster">链接群集</h3>

可以使用 [Apache Ambari](https://ambari.apache.org/) 管理的用户名链接标准群集，也可以使用域用户名（例如：user1@contoso.com）链接 Enterprise Security Pack 安全 Hadoop 群集。
1. 按 CTRL+SHIFT+P 打开命令面板，然后输入“HDInsight:链接群集”。

   ![链接群集命令](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 输入 HDInsight 群集 URL -> 输入用户名 -> 输入密码 -> 选择群集类型 -> 如果通过验证，将显示成功信息。
   
   ![“链接群集”对话框](./media/hdinsight-for-vscode/link-cluster-process.png)

   > [!NOTE]  
   > 如果群集已登录到 Azure 订阅中并且已链接群集，则使用链接用户名和密码。 
   
3. 可以使用命令“列出群集”来查看链接的群集。 现在可以将脚本提交到此链接群集。

   ![链接的群集](./media/hdinsight-for-vscode/linked-cluster.png)

4. 还可以取消链接群集，方法是从命令面板输入“HDInsight:取消链接群集”。


### <a name="to-link-a-generic-apache-livy-endpoint"></a>链接通用 Apache Livy 终结点

1. 按 CTRL+SHIFT+P 打开命令面板，然后输入“HDInsight:链接群集”。
2. 选择“通用 Livy 终结点”。
3. 输入通用 Livy 终结点，例如 http\://10.172.41.42:18080。
4. 需要为通用 Livy 终结点授权时请选择“基本”，否则请选择“无”。
5. 在步骤 4 中选择“基本”时请输入用户名。
6. 在步骤 4 中选择“基本”时请输入密码。
7. 通用 livy 终结点已成功链接。

   ![链接的通用 livy 群集](./media/hdinsight-for-vscode/link-cluster-process-generic-livy.png)

## <a name="list-hdinsight-clusters"></a>列出 HDInsight 群集

若要测试连接，可以列出 HDInsight 群集：

### <a name="to-list-hdinsight-clusters-under-your-azure-subscription"></a>列出 Azure 订阅下的 HDInsight 群集
1. 打开一个工作文件夹并连接到 Azure。 有关详细信息，请参阅[打开 HDInsight 工作文件夹](#open-hdinsight-work-folder)和[连接到 Azure](#connect-to-hdinsight-cluster)。

2. 右键单击脚本编辑器，然后选择“HDInsight：** 列出群集”。 

3. HDInsight 群集将显示在“输出”窗格中。

    ![设置默认群集配置](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-a-default-cluster"></a>设置默认群集
1. 打开工作文件夹并连接到 Azure。 请参阅[打开 HDInsight 工作文件夹](#open-hdinsight-work-folder)和[连接到 Azure](#connect-to-hdinsight-cluster)。

2. 右键单击脚本编辑器，然后选择“HDInsight：** 设置默认群集”。 

3. 选择一个群集作为当前脚本文件的默认群集。 工具将自动更新配置文件 **.VSCode\settings.json**。 

   ![设置默认群集配置](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>设置 Azure 环境
1. 按 **CTRL+SHIFT+P** 打开命令面板。

2. 输入“HDInsight:设置 Azure 环境”。

3. 选择一个环境（例如“Azure”或“Azure 中国”）作为默认登录入口。

4. 同时，工具已将选择的默认登录入口保存到 **.VSCode\settings.json** 中。 还可以在此配置文件中直接更新默认登录入口。 

   ![设置默认登录入口配置](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>提交交互式 Hive 查询和 Hive 批处理脚本

通过用于 VS Code 的 HDInsight 工具，可将交互式 Hive 查询和 Hive 批处理脚本提交到 HDInsight 群集。

1. 创建一个新的工作文件夹和一个新的 Hive 脚本文件（如果还没有）。

2. 连接到 Azure 帐户或链接群集。

3. 将以下代码复制并粘贴到 Hive 文件中，然后保存该文件。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```
4. 右键单击脚本编辑器，选择“HDInsight:Hive 交互式”提交查询，或使用快捷方式 Ctrl + Alt + I。选择“HDInsight:Hive 批处理”以提交脚本，或使用快捷键 Ctrl + Alt + H。 

5. 如果尚未指定默认群集，请选择群集。 工具还允许使用上下文菜单提交代码块而非整个脚本文件。 不久之后，查询结果将显示在新选项卡中。

   ![交互式 Hive 结果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - “结果”面板：可以将整个结果作为 CSV、JSON、Excel 保存到本地路径，也可以只选择多个行。

    - “消息”面板：选择“行”号会跳转到运行的脚本的第一行。

## <a name="submit-interactive-pyspark-queries"></a>提交交互式 PySpark 查询

### <a name="to-submit-interactive-pyspark-queries-to-hdinsight-spark-clusters"></a>将交互式 PySpark 查询提交到 HDInsight Spark 群集。

1. 创建一个新的工作文件夹和一个新的 .py 文件（如果还没有）。

    > [!NOTE]
    > VSCode 建议为 .py 文件安装 Python 扩展。 可以安装该扩展或关闭对话框。
    > 
    >![用于 Visual Studio Code 的 HDInsight - Python 安装](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

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
4. 如果尚未安装 Python 环境，请参阅[为 Visual Studio Code 设置 PySpark 交互式环境](set-up-pyspark-interactive-environment.md)。

5. 突出显示此脚本。 然后右键单击脚本编辑器，选择“HDInsight:PySpark 交互式”，或使用快捷键 Ctrl + Alt + I。

6. 选择要将 PySpark 查询提交到的群集。 很快，查询结果将显示在右侧的新选项卡中：

   ![提交 python 作业结果](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 
7. 工具还支持查询 **SQL 子句**查询。

   ![提交 python 作业结果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)运行查询时，提交状态将显示在底部状态栏的左侧。 当状态为“PySpark 内核(忙)”时，请不要提交其他查询。 

>[!NOTE]  
>群集可以保留会话信息。 定义的变量、函数和相应值将保留在会话中，因此，可在针对同一群集发出的多个服务调用中引用这些信息。 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>Spark2.2/2.3 不支持 PySpark3

Spark 2.2 群集和 Spark2.3 群集不再支持 PySpark3，只有 Python 支持“PySpark”。 在 Python3 中提交到 spark 2.2/2.3 会失败，这是已知的问题。

   ![提交到 python3 出错](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

遵循以下步骤来使用 Python2.x： 

1. 在本地计算机上安装 Python 2.7 并将其添加到系统路径。

2. 重启 VSCode。

3. 在状态栏中单击“Python XXX”，然后选择目标 Python，以切换到 Python 2。

   ![选择 Python 版本](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)

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
4. 右键单击脚本编辑器，然后从上下文菜单中选择“HDInsight:PySpark 批处理”，或使用快捷键 Ctrl + Alt + H。 

5. 选择要将 PySpark 作业提交到的群集。 

   ![提交 python 作业结果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

提交 Python 作业后，提交日志会显示在 VS Code 的“输出”窗口中。 同时还会显示 **Spark UI URL** 和 **Yarn UI URL**。 可以在 Web 浏览器中打开 URL 来跟踪作业状态。

## <a name="apache-livy-configuration"></a>Apache Livy 配置

支持 [Apache Livy](https://livy.incubator.apache.org/) 配置，可在工作空间文件夹中的 **.VSCode\settings.json** 内设置此配置。 目前，livy 配置仅支持 Python 脚本。 详细信息请参阅 [Livy README](https://github.com/cloudera/livy/blob/master/README.rst )。

<a id="triggerlivyconf"></a>**如何触发 livy 配置**
   
可在“文件”菜单中选择“首选项”，然后在上下文菜单中选择“设置”。 单击“工作区设置”选项卡可以开始设置 livy 配置。

还可以提交一个文件，然后观察 .vscode 文件夹是否已自动添加到工作文件夹。 可以单击“.vscode\settings.json”来查找 livy 配置。

+ 项目设置：

    ![Livy 配置](./media/hdinsight-for-vscode/hdi-livyconfig.png)

>[!NOTE]
>对于“driverMomory”和“executorMomry”设置，请结合单位设置值，例如 1g 或 1024 m。 

+ 支持的 Livy 配置：   

    **POST /batches**   
    请求正文

    | 名称 | description | type | 
    | :- | :- | :- | 
    | file | 包含要执行的应用程序的文件 | path（必需） | 
    | proxyUser | 运行作业时要模拟的用户 | 字符串 | 
    | className | 应用程序 Java/Spark main 类 | 字符串 |
    | args | 应用程序的命令行参数 | 字符串列表 | 
    | jars | 此会话中要使用的 jars | 字符串列表 | 
    | pyFiles | 此会话中要使用的 Python 文件 | 字符串列表 |
    | 文件 | 此会话中要使用的文件 | 字符串列表 |
    | driverMemory | 要用于驱动程序进程的内存量 | 字符串 |
    | driverCores | 要用于驱动程序进程的内核数 | int |
    | executorMemory | 每个执行程序进程要使用的内存量 | 字符串 |
    | executorCores | 用于每个执行程序的内核数 | int |
    | numExecutors | 为此会话启动的执行程序数量 | int |
    | archives | 此会话中要使用的存档 | 字符串列表 |
    | 队列 | 要提交的 YARN 队列的名称 | 字符串 |
    | 名称 | 此会话的名称 | 字符串 |
    | conf | Spark 配置属性 | key=val 的映射 |

    响应正文   
    创建的批处理对象。

    | 名称 | description | type | 
    | :- | :- | :- | 
    | id | 会话 id | int | 
    | appId | 此会话的应用程序 id |  String |
    | appInfo | 应用程序详细信息 | key=val 的映射 |
    | log | 日志行 | 字符串列表 |
    | state |   批处理状态 | 字符串 |

>[!NOTE]
>提交脚本时，分配的 livy 配置将在输出窗格中显示。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>通过资源管理器集成 Azure HDInsight

Azure HDInsight 已添加到左侧面板。 可以直接浏览和管理群集。

1. 展开“AZURE HDINSIGHT”，如果尚未登录，此时会显示“登录到 Azure...”链接。

    ![登录链接图像](./media/hdinsight-for-vscode/hid-azure-hdinsight-sign-in.png)

2. 单击“登录到 Azure”，此时右下角会弹出登录链接和代码。

    ![适用于其他环境的登录说明](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin-code.png)

3. 单击“复制并打开”按钮打开浏览器，粘贴代码，单击“继续”按钮，然后会看到有关成功登录的提示。

4. 登录后，可用的订阅和群集（支持的 Spark、Hadoop 和 HBase）将列在“AZURE HDINSIGHT”中。 

   ![Azure HDInsight 订阅](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

5. 展开群集以查看 hive 元数据数据库和表架构。

   ![Azure HDInsight 群集](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

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

* [使用 Azure Toolkit for IntelliJ 通过 VPN 远程调试 Apache Spark 应用程序](spark/apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [使用 Azure Toolkit for IntelliJ 通过 SSH 远程调试 Apache Spark 应用程序](spark/apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [将用于 IntelliJ 的 HDInsight 工具与 Hortonworks 沙盒配合使用](hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [使用 Azure Toolkit for Eclipse 中的 HDInsight 工具创建 Apache Spark 应用程序](spark/apache-spark-eclipse-tool-plugin.md)
* [在 HDInsight 上的 Apache Spark 群集中使用 Apache Zeppelin 笔记本](spark/apache-spark-zeppelin-notebook.md)
* [在 HDInsight 的 Apache Spark 群集中可用于 Jupyter Notebook 的内核](spark/apache-spark-jupyter-notebook-kernels.md)
* [Use external packages with Jupyter notebooks（将外部包与 Jupyter 笔记本配合使用）](spark/apache-spark-jupyter-notebook-use-external-packages.md)
* [Install Jupyter on your computer and connect to an HDInsight Spark cluster（在计算机上安装 Jupyter 并连接到 HDInsight Spark 群集）](spark/apache-spark-jupyter-notebook-install-locally.md)
* [在 Azure HDInsight 中使用 Microsoft Power BI 直观显示 Apache Hive 数据](hadoop/apache-hadoop-connect-hive-power-bi.md)
* [在 Azure HDInsight 中使用 Power BI 直观显示交互式查询 Hive 数据](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)。
* [为 Visual Studio Code 设置 PySpark 交互式环境](set-up-pyspark-interactive-environment.md)
* [在 Azure HDInsight 中使用 Apache Zeppelin 运行 Apache Hive 查询](./hdinsight-connect-hive-zeppelin.md)

### <a name="scenarios"></a>方案
* [Apache Spark 与 BI：将 HDInsight 中的 Spark 与 BI 工具配合使用来执行交互式数据分析](spark/apache-spark-use-bi-tools.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 来通过 HVAC 数据分析建筑物温度](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark 与机器学习：使用 HDInsight 中的 Spark 预测食品检验结果](spark/apache-spark-machine-learning-mllib-ipython.md)
* [使用 HDInsight 中的 Apache Spark 分析网站日志](spark/apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-running-applications"></a>创建和运行应用程序
* [使用 Scala 创建独立的应用程序](spark/apache-spark-create-standalone-application.md)
* [使用 Apache Livy 在 Apache Spark 群集中远程运行作业](spark/apache-spark-livy-rest-interface.md)

### <a name="manage-resources"></a>管理资源
* [管理 Azure HDInsight 中 Apache Spark 群集的资源](spark/apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight（跟踪和调试 HDInsight 中的 Apache Spark 群集上运行的作业）](spark/apache-spark-job-debugging.md)



