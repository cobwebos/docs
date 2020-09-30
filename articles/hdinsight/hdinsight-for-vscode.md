---
title: 适用于 Visual Studio Code 的 Azure HDInsight
description: 了解如何使用适用于 Visual Studio Code 的 Spark 和 Hive 工具 (Azure HDInsight)。 使用这些工具创建查询和脚本并提交它们。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2020
ms.custom: devx-track-python
ms.openlocfilehash: bb2fff699b31d8b3b311180c4b85e2bfd1da892c
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2020
ms.locfileid: "91530147"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>使用适用于 Visual Studio Code 的 Spark 和 Hive 工具

了解如何使用适用于 Visual Studio Code 的 Apache Spark 和 Hive 工具。 使用工具为 Apache Spark 创建和提交 Apache Hive 批处理作业、交互式 Hive 查询和 PySpark 脚本。 首先，本文将介绍如何在 Visual Studio Code 中安装 Spark 和 Hive 工具。 随后介绍如何将作业提交到 Spark 和 Hive 工具。  

可将 Spark 和 Hive 工具安装在 Visual Studio Code 支持的平台上。 注意针对不同平台的以下先决条件。

## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要以下各项：

- 一个 Azure HDInsight 群集。 若要创建群集，请参阅 [HDInsight 入门](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)。 或使用支持 Apache Livy 终结点的 Spark 和 Hive 群集。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 只有 Linux 和 macOS 需要 Mono。
- [Visual Studio Code 的 PySpark 交互式环境](set-up-pyspark-interactive-environment.md)。
- 本地目录。 本文使用 C:\HD\HDexample。

## <a name="install-spark--hive-tools"></a>安装 Spark 和 Hive 工具

满足先决条件后，可遵循以下步骤安装适用于 Visual Studio Code 的 Spark 和 Hive 工具：

1. 打开 Visual Studio Code。

2. 从菜单栏中，导航到“查看” > “扩展” 。

3. 在搜索框中，输入“Spark 和 Hive”。

4. 从搜索结果中选择“Spark 和 Hive 工具”，然后选择“安装”： 

   ![适用于 Visual Studio Code 的 Spark 和 Hive Python 安装](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 根据需要选择“重载”。

## <a name="open-a-work-folder"></a>打开工作文件夹

若要打开工作文件夹并在 Visual Studio Code 中创建文件，请执行以下步骤：

1. 在菜单栏中，导航到“文件” > “打开文件夹...” > “C:\HD\HDexample”，然后选择“选择文件夹”按钮。    该文件夹显示在左侧的“资源管理器”视图中。

2. 在“资源管理器”视图中选择“HDexample”文件夹，然后选择工作文件夹旁边的“新建文件”图标：  

   ![visual studio code“新建文件”图标](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. 为新文件命名，使用 `.hql`（Hive 查询）或 `.py`（Spark 脚本）作为文件扩展名。 本示例使用 **HelloWorld.hql**。

## <a name="set-the-azure-environment"></a>设置 Azure 环境

国家云用户请先遵循以下步骤设置 Azure 环境，然后使用“Azure:登录”命令登录到 Azure：

1. 导航到“文件” > “首选项” > “设置”。  
2. 搜索以下字符串：**Azure:云**。
3. 从列表中选择国家云：

   ![设置默认登录入口配置](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>连接到 Azure 帐户

在你可以从 Visual Studio Code 将脚本提交到群集之前，用户可以登录 Azure 订阅，或[链接 HDInsight 群集](#link-a-cluster)。 使用 Ambari 用户名/密码或 ESP 群集的域加入凭据连接到 HDInsight 群集。 遵循以下步骤连接到 Azure：

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入“Azure:  登录”：

    ![适用于 Visual Studio Code 的 Spark 和 Hive 工具登录](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 按照登录说明登录到 Azure。 连接后，Visual Studio Code 窗口底部的状态栏上会显示 Azure 帐户名称。  

## <a name="link-a-cluster"></a>链接群集

### <a name="link-azure-hdinsight"></a>链接：Azure HDInsight

可以使用 [Apache Ambari](https://ambari.apache.org/) 管理的用户名链接标准群集，也可以使用域用户名（例如：`user1@contoso.com`）链接 Enterprise Security Pack 安全 Hadoop 群集。

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入“Spark/Hive:  Link a Cluster”。

   ![命令面板链接群集命令](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 选择链接的群集类型“Azure HDInsight”。

3. 输入 HDInsight 群集 URL。

4. 输入 Ambari 用户名，默认为 **admin**。

5. 输入 Ambari 密码。

6. 选择群集类型。

7. 设置群集的显示名称（可选）。

8. 查看“输出”视图以进行验证。

   > [!NOTE]  
   > 如果群集已登录到 Azure 订阅中并且已链接群集，则使用链接用户名和密码。  

### <a name="link-generic-livy-endpoint"></a>链接：通用 Livy 终结点

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入“Spark/Hive:  Link a Cluster”。

2. 选择链接的群集类型“通用 Livy 终结点”。

3. 输入通用 livy 终结点。 例如：http\://10.172.41.42:18080。

4. 选择授权类型“基本”或“无”。   如果选择“基本”：  
    &emsp;a. 输入 Ambari 用户名，默认为 **admin**。  
    &emsp;b. 输入 Ambari 密码。

5. 查看“输出”视图以进行验证。

## <a name="list-clusters"></a>列出群集

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入“Spark/Hive:  List Cluster”。

2. 选择所需的订阅。

3. 检查“输出”视图。 此视图显示你的链接群集（或多个群集），以及你的 Azure 订阅下的所有群集：

    ![设置默认群集配置](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>设置默认群集

1. 重新打开[前面](#open-a-work-folder)所述的 **HDexample** 文件夹（如果已关闭）。  

2. 选择[前面](#open-a-work-folder)创建的 **HelloWorld.hql** 文件。 它将在脚本编辑器中打开。

3. 右键单击脚本编辑器，然后选择“Spark/ Hive:Set Default Cluster”。  

4. [连接](#connect-to-an-azure-account)到 Azure 帐户或链接某个群集（如果尚未这样做）。

5. 选择一个群集作为当前脚本文件的默认群集。 工具将自动更新 **.VSCode\settings.json** 配置文件：

   ![设置默认群集配置](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>提交交互式 Hive 查询和 Hive 批处理脚本

通过 Visual Studio Code 的 Spark 和 Hive 工具，可将交互式 Hive 查询和 Hive 批处理脚本提交到群集。

1. 重新打开[前面](#open-a-work-folder)所述的 **HDexample** 文件夹（如果已关闭）。  

2. 选择[前面](#open-a-work-folder)创建的 **HelloWorld.hql** 文件。 它将在脚本编辑器中打开。

3. 将以下代码复制并粘贴到 Hive 文件中，然后保存该文件：

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [连接](#connect-to-an-azure-account)到 Azure 帐户或链接某个群集（如果尚未这样做）。

5. 右键单击脚本编辑器，然后选择“Hive:Interactive”以提交查询，或使用 Ctrl+Alt+I 快捷键。  选择“Hive:批处理”以提交脚本，或使用 Ctrl+Alt+H 快捷键。  

6. 如果尚未指定默认群集，请选择群集。 工具还允许使用上下文菜单提交代码块而非整个脚本文件。 不久之后，查询结果将显示在新选项卡中：

   ![交互式 Apache Hive 查询结果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - “结果”面板：可以将整个结果作为 CSV、JSON、Excel 保存到本地路径，也可以只选择多个行。

    - “消息”面板：选择**行**号会跳转到运行的脚本的第一行。

## <a name="submit-interactive-pyspark-queries"></a>提交交互式 PySpark 查询

用户可通过以下方式执行 PySpark Interactive 命令：

### <a name="using-the-pyspark-interactive-command-in-py-file"></a>在 PY 文件中使用 PySpark Interactive 命令
使用 PySpark Interactive 命令提交查询时，请执行以下步骤：

1. 重新打开[前面](#open-a-work-folder)所述的 **HDexample** 文件夹（如果已关闭）。  

2. 遵循[前面](#open-a-work-folder)所述的步骤创建新的 **HelloWorld.py** 文件。

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

4. 有关安装 PySpark 内核的提示显示在窗口右下角。 可以单击“安装”按钮继续进行 PySpark 安装，也可以单击“跳过”按钮跳过此步骤。

   ![屏幕截图显示跳过 PySpark 安装的选项。](./media/hdinsight-for-vscode/install-the-pyspark-kernel.png)

5. 如果以后需要安装它，可以导航到“文件” > “首选项” > “设置”，然后在设置中取消选中“Hdinsight:允许跳过 Pyspark 安装”。 
    
    ![屏幕截图显示用于启用 Skip Pyspark 安装的选项。](./media/hdinsight-for-vscode/enable-skip-pyspark-installation.png)

6. 如果在步骤 4 中安装成功，则会在窗口右下角显示“已成功安装 PySpark”消息框。 单击“重载”按钮可重载此窗口。
    ![已成功安装 PySpark](./media/hdinsight-for-vscode/pyspark-kernel-installed-successfully.png)

7. [连接](#connect-to-an-azure-account)到 Azure 帐户或链接某个群集（如果尚未这样做）。

8. 选择所有代码，右键单击脚本编辑器并选择“Spark:PySpark Interactive”以提交查询。 或者，使用 Ctrl+Alt+I 快捷键。

    ![Pyspark Interactive 上下文菜单](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

9. 如果尚未指定默认群集，请选择群集。 不久之后，新选项卡中会显示“Python Interactive 结果”。单击 PySpark 可将内核切换到 **PySpark**，代码将成功运行。 工具还允许使用上下文菜单提交代码块而非整个脚本文件：

   ![pyspark interactive - python interactive 窗口](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

10. 输入 **%%info**，然后按 Shift+Enter 查看作业信息（可选）：

    ![pyspark 交互查看作业信息](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

工具还支持 **Spark SQL** 查询：

   ![pyspark 交互视图结果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)


### <a name="perform-interactive-query-in-py-file-using-a--comment"></a>使用 #%% 注释在 PY 文件中执行交互式查询

1. 将 **#%%** 添加到 PY 代码之前以获取笔记本体验。

    ![添加 #%%](./media/hdinsight-for-vscode/run-cell.png)

2. 单击“运行单元格”。 不久之后，Python Interactive 结果会显示在一个新选项卡中。

   ![“运行单元格”命令的结果](./media/hdinsight-for-vscode/run-cell-get-results.png)

   > [!NOTE]  
   > 当内核或设置出现混乱情况时，请使用“Python:选择解释器来启动 Jupyter 服务器”命令和“重启 IPython 内核”，然后重载 VSCode，即可解决问题。

## <a name="leverage-ipynb-support-from-python-extension"></a>利用 Python 扩展中的 IPYNB 支持

1. 若要创建 Jupyter Notebook，可以使用命令面板中的命令，也可以在工作区中创建新的 .ipynb 文件。 有关详细信息，请参阅[在 Visual Studio Code 中使用 Jupyter Notebook](https://code.visualstudio.com/docs/python/jupyter-support)

2. 单击“PySpark”将内核切换到 PySpark，然后单击“运行单元格”，一段时间后，就会显示结果。 

   ![运行 ipynb 的结果](./media/hdinsight-for-vscode/run-ipynb-file-results.png)


> [!NOTE]
>
>此扩展不支持 2020.5.78807 以上（含）的 Ms-python 版本，这是一个[已知问题](#known-issues)。

## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批处理作业

1. 重新打开[前面](#open-a-work-folder)所述的 **HDexample** 文件夹（如果已关闭）。  

2. 遵循[前面](#open-a-work-folder)所述的步骤创建新的 **BatchFile.py** 文件。

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

4. [连接](#connect-to-an-azure-account)到 Azure 帐户或链接某个群集（如果尚未这样做）。

5. 右键单击脚本编辑器，然后选择“Spark:PySpark 批处理”，或使用 Ctrl+Alt+H 快捷键。

6. 选择要将 PySpark 作业提交到的群集：

   ![提交 Python 作业结果输出](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

提交 Python 作业后，提交日志将显示在 Visual Studio Code 的“输出”窗口中。 同时还会显示 Spark UI URL 和 Yarn UI URL。 你可以在 Web 浏览器中打开 URL 以跟踪作业状态。

## <a name="integrate-with-hdinsight-identity-broker-hib"></a>与 HDInsight Identity Broker (HIB) 集成

### <a name="connect-to-your-hdinsight-esp-cluster-with-id-broker-hib"></a>连接带有 ID 代理的 HDInsight ESP 群集 (HIB)

可以按照常规步骤登录到 Azure 订阅，以连接带有 ID 代理的 HDInsight ESP 群集 (HIB)。 登录后，将在 Azure 资源管理器中看到群集列表。 有关详细信息，请参阅[连接到 HDInsight 群集](#connect-to-an-azure-account)。

### <a name="run-a-hivepyspark-job-on-an-hdinsight-esp-cluster-with-id-broker-hib"></a>在带有 ID 代理的 HDInsight ESP 群集 (HIB) 上运行 Hive/PySpark 作业

若要运行 hive 作业，可以按照常规步骤将作业提交到带有 ID 代理的 HDInsight ESP 群集 (HIB)。 有关更多说明，请参阅[提交交互式 Hive 查询和 Hive 批处理脚本](#submit-interactive-hive-queries-and-hive-batch-scripts)。

若要运行交互式 PySpark 作业，可以按照常规步骤将作业提交到带有 ID 代理的 HDInsight ESP 群集 (HIB)。 有关更多说明，请参阅[提交交互式 PySpark 查询](#submit-interactive-pyspark-queries)。

若要运行 PySpark 批处理作业，可以按照常规步骤将作业提交到带有 ID 代理的 HDInsight ESP 群集 (HIB)。 有关更多说明，请参阅[提交 PySpark 批处理作业](#submit-pyspark-batch-job)。


## <a name="apache-livy-configuration"></a>Apache Livy 配置

支持 [Apache Livy](https://livy.incubator.apache.org/) 配置。 可在工作空间文件夹中的 **.VSCode\settings.json** 内配置 Apache Livy。 目前，Livy 配置仅支持 Python 脚本。 有关详细信息，请参阅 [Livy 自述文件](https://github.com/cloudera/livy/blob/master/README.rst )。

<a id="triggerlivyconf"></a>**如何触发 Livy 配置**

方法 1  
1. 从菜单栏中，导航到“文件” > “首选项” > “设置”  。
2. 在“搜索设置”框中，输入“HDInsight 作业提交: Livy Conf”。  
3. 选择“在 settings.json 中编辑”以获取相关搜索结果。

方法2：提交文件，并注意 `.vscode` 文件夹自动添加到工作文件夹。 可以通过选择“.vscode\settings.json”来查看 Livy 配置。

+ 项目设置：

    ![HDInsight Apache Livy 配置](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >对于 **driverMemory** 和 **executorMemory** 设置，请设置值和单位。 例如：1g 或 1024m。

+ 支持的 Livy 配置：

    **POST/batches** 请求正文

    | name | description | type |
    | --- | --- | --- |
    | file | 包含要执行的应用程序的文件 | Path（必需） |
    | proxyUser | 运行作业时要模拟的用户 | String |
    | className | 应用程序 Java/Spark 主类 | String |
    | args | 应用程序的命令行参数 | 字符串列表 |
    | jars | 此会话中要使用的 Jar | 字符串列表 | 
    | pyFiles | 将在本次会话中使用的 Python 文件 | 字符串列表 |
    | files | 此会话中要使用的文件 | 字符串列表 |
    | driverMemory | 用于驱动程序进程的内存量 | String |
    | driverCores | 用于驱动程序进程的内核数 | int |
    | executorMemory | 每个执行程序进程使用的内存量 | String |
    | executorCores | 每个执行程序使用的内核数 | int |
    | numExecutors | 为此会话启动的执行程序数 | int |
    | archives | 将在本次会话中使用的存档 | 字符串列表 |
    | 队列 | 要提交到的 YARN 队列的名称| String |
    | name | 此会话的名称 | String |
    | conf | Spark 配置属性 | key=val 的映射 |

    响应正文：创建的批处理对象。

    | name | description | type |
    | --- | ---| --- |
    | ID | 会话 ID | int |
    | appId | 此会话的应用程序 ID | String |
    | appInfo | 详细的应用程序信息 | key=val 的映射 |
    | log | 日志行 | 字符串列表 |
    | state |批处理状态 | String |

    > [!NOTE]
    > 提交脚本时，分配的 Livy 配置将显示在输出窗格中。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>通过资源管理器与 Azure HDInsight 集成

可以通过 **Azure HDInsight** 资源管理器直接在群集中预览 Hive 表：

1. [连接](#connect-to-an-azure-account)到 Azure 帐户（如果尚未这样做）。

2. 选择最左侧列中的“Azure”图标。

3. 在左窗格中，展开“AZURE:HDINSIGHT”。 此时会列出可用的订阅和群集。

4. 展开群集以查看 Hive 元数据数据库和表架构。

5. 右键单击 Hive 表。 例如：**hivesampletable**。 选择“预览”。

   ![适用于 Visual Studio code 的 Spark 和 Hive - 预览 Hive 表](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. 此时会打开“预览结果”窗口：

   ![适用于 visual studio code 的 Spark 和 Hive - 预览结果窗口](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- “结果”面板

   可以将整个结果作为 CSV、JSON、Excel 保存到本地路径，也可以只选择多个行。

- “消息”面板
   1. 如果表中的行数大于 100，将显示消息：“显示了 Hive 表的前 100 行”。
   2. 如果表中的行数小于或等于 100，将显示以下消息：“显示了 Hive 表的 60 行”。
   3. 如果表中没有任何内容，将显示以下消息：“`0 rows are displayed for Hive table.`”

        >[!NOTE]
        >
        >在 Linux 中，安装 xclip 用于复制表数据。
        >
        >![Linux 中适用于 Visual Studio Code 的 Spark 和 Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)

## <a name="additional-features"></a>其他功能

适用于 Visual Studio Code 的 Spark 和 Hive 还支持以下功能：

- **IntelliSense 自动完成**。 弹出关键字、方法、变量和其他编程元素的相关建议。 不同图标表示不同类型的对象：

    ![Spark & 用于 Visual Studio Code IntelliSense 对象的 Hive 工具](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **IntelliSense 错误标记**。 语言服务会为 Hive 脚本的编辑错误添加下划线。     
- **语法突出显示**。 语言服务使用不同颜色来区分变量、关键字、数据类型、函数和其他编程元素：

    ![适用于 Visual Studio Code 的 Spark 和 Hive 工具 - 语法突出显示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>仅限读取者角色

分配有群集“仅限读取者”角色的用户无法将作业提交到 HDInsight 群集，也无法查看 Hive 数据库。 需在 [Azure 门户](https://ms.portal.azure.com/)中联系群集管理员将你的角色升级到 [**HDInsight 群集操作员**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。 如果你有有效的 Ambari 凭据，可遵循以下指导手动链接群集。

### <a name="browse-the-hdinsight-cluster"></a>浏览 HDInsight 群集  

选择 Azure HDInsight 资源管理器展开 HDInsight 群集时，如果你对群集拥有“仅限读取者”角色，系统会提示你链接群集。 使用 Ambari 凭据通过以下方法链接到群集。

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>将作业提交到 HDInsight 群集

将作业提交到 HDInsight 群集时，如果你对群集拥有“仅限读取者”角色，系统会提示你链接群集。 使用 Ambari 凭据通过以下步骤链接到群集。

### <a name="link-to-the-cluster"></a>链接到群集

1. 输入有效的 Ambari 用户名。
2. 输入有效的密码。

   ![Spark & 用于 Visual Studio Code 用户名的 Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![适用于 Visual Studio Code 的 Spark 和 Hive 工具 - 密码](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >可以使用 `Spark / Hive: List Cluster` 来检查链接的群集：
  >
  >![适用于 Visual Studio Code 的 Spark 和 Hive 工具 - 链接读取器](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>浏览 Data Lake Storage Gen2 帐户

选择 Azure HDInsight 资源管理器，展开 Data Lake Storage Gen2 帐户。 如果 Azure 帐户对 Gen2 存储没有访问权限，则系统会提示你输入存储访问密钥。 验证访问密钥后，Data Lake Storage Gen2 帐户将自动展开。

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>将作业提交到使用 Data Lake Storage Gen2 的 HDInsight 群集

使用 Data Lake Storage Gen2 将作业提交到 HDInsight 群集。 如果 Azure 帐户对 Gen2 存储没有写入权限，则系统会提示你输入存储访问密钥。 验证访问密钥后，作业将成功提交。

![适用于 Visual Studio Code 的 Spark 和 Hive 工具 - 访问密钥](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> 可以在 Azure 门户中获取存储帐户的访问密钥。 有关详细信息，请参阅[管理存储帐户访问密钥](../storage/common/storage-account-keys-manage.md)。

## <a name="unlink-cluster"></a>取消链接群集

1. 在菜单栏中，转到“视图” > “命令面板”，然后输入“Spark/Hive:  Unlink a Cluster”。  

2. 选择要取消链接的群集。  

3. 查看“输出”视图以进行验证。  

## <a name="sign-out"></a>注销  

在菜单栏中，转到“视图” > “命令面板”，然后输入“Azure:  注销”。

## <a name="known-issues"></a>已知问题
### <a name="ms-python-2020578807-version-is-not-supported-on-this-extention"></a>此扩展不支持 2020.5.78807 以上（含）的 ms-python 版本 

“无法连接到 Jupyter 笔记本。” 是 2020.5.78807 以上（含）的 python 版本的一个已知问题。 建议用户使用 **[2020.4.76186](https://github.com/microsoft/vscode-python/releases/download/2020.4.76186/ms-python-release.vsix)** 版 ms-python 以避免此问题。

![已知问题](./media/hdinsight-for-vscode/known-issue.png)

## <a name="next-steps"></a>后续步骤

有关演示了如何使用适用于 Visual Studio Code 的 Spark 和 Hive 的视频，请观看[适用于 Visual Studio Code 的 Spark 和 Hive](https://go.microsoft.com/fwlink/?linkid=858706)。
