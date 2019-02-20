---
title: Azure HDInsight 工具 -将 Visual Studio Code 用于 Hive、LLAP 或 PySpark | Microsoft Docs
description: 了解如何使用用于 Visual Studio Code 的 Azure HDInsight 工具来创建、提交查询和脚本。
Keywords: Visual Studio Code,Azure HDInsight Tools,Hive,Python,PySpark,Spark,HDInsight,Hadoop,LLAP,Interactive Hive,Interactive Query
services: HDInsight
documentationcenter: ''
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/04/2019
ms.openlocfilehash: 3301f3cdea1795db3b17bc2fa7da05bf362c744d
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895062"
---
# <a name="use-azure-hdinsight-tools-for-visual-studio-code"></a>使用用于 Visual Studio Code 的 Azure HDInsight 工具

了解如何使用 Visual Studio Code 的 Azure HDInsight 工具，为 Apache Spark 创建和提交 Apache Hive 批处理作业、交互式 Hive 查询和 PySpark 脚本。 本文首先介绍如何在 Visual Studio Code 中安装 HDInsight 工具，然后演练如何向 Hive 和 Spark 提交作业。  

可以在 Visual Studio Code 支持的平台（包括 Windows、Linux 和 macOS）上安装 Azure HDInsight 工具。 可以在下面找到不同平台的必备组件。


## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要具有以下项：

- HDInsight 群集。 若要创建群集，请参阅 [HDInsight 入门](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 只有 Linux 和 macOS 需要 Mono。
- 适用于 Visual Studio Code 的 [Azure 帐户扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)。
- [为 Visual Studio Code 设置 PySpark 交互式环境](set-up-pyspark-interactive-environment.md)。
- 名为 **HDexample** 的本地目录。  本文使用 **C:\HD\HDexample**。

## <a name="install-azure-hdinsight-tools"></a>安装 Azure HDInsight 工具

满足先决条件后，可以安装 Visual Studio Code 的 Azure HDInsight 工具。  完成以下步骤来安装 Azure HDInsight 工具：

1. 打开 Visual Studio Code。

2. 在菜单栏中，导航到“视图” > “扩展”。

3. 在搜索框中输入 **HDInsight**。

4. 从搜索结果中选择“Azure HDInsight 工具”，然后选择“安装”。  

   ![用于 Visual Studio Code 的 HDInsight - Python 安装](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 选择“重载”以激活安装的“Azure HDInsight 工具”扩展。


## <a name="open-hdinsight-work-folder"></a>打开 HDInsight 工作文件夹

完成以下步骤打开工作文件夹，并在 Visual Studio Code 中创建一个文件：

1. 在菜单栏中，导航到“文件” > “打开文件夹...” > “C:\HD\HDexample”，然后选择“选择文件夹”按钮。 该文件夹将显示在左侧的“资源管理器”视图中。

2. 在“资源管理器”视图中选择文件夹“HDexample”，然后选择工作文件夹旁边的“新建文件”图标。

   ![新建文件](./media/hdinsight-for-vscode/new-file.png)

3. 为新文件命名，以 .hql（Hive 查询）或 .py（Spark 脚本）作为文件扩展名。  本示例使用 **HelloWorld.hql**。

## <a name="connect-to-hdinsight-cluster"></a>连接到 HDInsight 群集

将脚本从 Visual Studio Code 提交到 HDInsight 群集之前，需要连接到 Azure 帐户，或链接群集（使用 Ambari 用户名/密码或已加入域的帐户）。  完成以下步骤以连接到 Azure：

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入 **HDInsight:Login**。

    ![用于 Visual Studio Code 的 HDInsight 工具 - 登录](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 遵照“输出”窗格中的登录说明操作。
    + 对于 Azure 全局环境，可使用“HDInsight:Login”命令在 HDInsight 资源管理器中触发“登录 Azure”操作，反之亦然。

        ![适用于 Azure 的登录说明](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-signin.png)

    + 在其他环境中，请遵照登录说明操作。

        ![适用于其他环境的登录说明](./media/hdinsight-for-vscode/hdi-azure-hdinsight-hdinsight-signin.png)

 连接后，Visual Studio Code 窗口左下角的状态栏上会显示 Azure 帐户名称。  
  

<h2 id="linkcluster">创建链接：Azure HDInsight</h2>

可以使用 [Apache Ambari](https://ambari.apache.org/) 管理的用户名链接标准群集，也可以使用域用户名（例如：user1@contoso.com）链接 Enterprise Security Pack 安全 Hadoop 群集。

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入 **HDInsight:** 链接群集”。

   ![链接群集命令](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 选择链接的群集类型“Azure HDInsight”。

3. 输入 HDInsight 群集 URL。

4. 输入 Ambari 用户名，默认为 **admin**。

5. 输入 Ambari 密码。

6. 选择群集类型。

7. 查看“输出”视图以进行验证。

   > [!NOTE]  
   > 如果群集已登录到 Azure 订阅中并且已链接群集，则使用链接用户名和密码。  


## <a name="create-link-generic-livy-endpoint"></a>创建链接：通用 Livy 终结点

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入 **HDInsight:** 链接群集”。

2. 选择链接的群集类型“通用 Livy 终结点”。

3. 输入通用 Livy 终结点，例如 http\://10.172.41.42:18080。

4. 选择授权类型“基本”或“无”。  如果选择“基本”，则：  
    &emsp;a. 输入 Ambari 用户名，默认为 **admin**。  
    &emsp;b. 输入 Ambari 密码。

5. 查看“输出”视图以进行验证。

## <a name="list-hdinsight-clusters"></a>列出 HDInsight 群集

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入 **HDInsight:List Cluster**。

2. 选择所需的订阅。

3. 查看“输出”视图。  该视图将显示链接的群集，以及 Azure 订阅下的所有群集。

    ![设置默认群集配置](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-default-cluster"></a>设置默认群集

1. 重新打开[前面](#open-hdinsight-work-folder)创建的文件夹 **HDexample**（如果已关闭）。  

2. 选择[前面](#open-hdinsight-work-folder)创建的文件 **HelloWorld.hql**，它将在脚本编辑器中打开。

3. [连接](#connect-to-hdinsight-cluster)到 Azure 帐户（如果尚未这样做）。

4. 右键单击脚本编辑器并选择“HDInsight:设置默认群集”。  

5. 选择一个群集作为当前脚本文件的默认群集。 工具将自动更新配置文件 **.VSCode\settings.json**。 

   ![设置默认群集配置](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="set-the-azure-environment"></a>设置 Azure 环境

1. [连接](#connect-to-hdinsight-cluster)到 Azure 帐户（如果尚未这样做）。

2. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入 **HDInsight:** 设置 Azure 环境”。

3. 选择一个环境作为默认登录入口。

4. 同时，工具已将选择的默认登录入口保存到 **.VSCode\settings.json** 中。 还可以在此配置文件中直接更新默认登录入口。 

   ![设置默认登录入口配置](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)


## <a name="submit-interactive-hive-queries-hive-batch-scripts"></a>提交交互式 Hive 查询和 Hive 批处理脚本

通过 Visual Studio Code 的 HDInsight 工具，可将交互式 Hive 查询和 Hive 批处理脚本提交到 HDInsight 群集。

1. 重新打开[前面](#open-hdinsight-work-folder)创建的文件夹 **HDexample**（如果已关闭）。  

2. 选择[前面](#open-hdinsight-work-folder)创建的文件 **HelloWorld.hql**，它将在脚本编辑器中打开。

3. [连接](#connect-to-hdinsight-cluster)到 Azure 帐户（如果尚未这样做）。

4. 将以下代码复制并粘贴到 Hive 文件中，然后保存该文件。

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

5. 右键单击脚本编辑器，选择“HDInsight:Hive 交互式”提交查询，或使用快捷方式 Ctrl + Alt + I。选择“HDInsight:Hive 批处理”以提交脚本，或使用快捷键 Ctrl + Alt + H。  

6. 如果尚未指定默认群集，请选择群集。 工具还允许使用上下文菜单提交代码块而非整个脚本文件。 不久之后，查询结果将显示在新选项卡中。

   ![交互式 Hive 结果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - “结果”面板：可以将整个结果作为 CSV、JSON、Excel 保存到本地路径，也可以只选择多个行。

    - “消息”面板：选择“行”号会跳转到运行的脚本的第一行。

## <a name="submit-interactive-pyspark-queries"></a>提交交互式 PySpark 查询

1. 重新打开[前面](#open-hdinsight-work-folder)创建的文件夹 **HDexample**（如果已关闭）。  

2. 遵循[前面](#open-hdinsight-work-folder)所述的步骤创建新文件 **HelloWorld.py**。

3. 如果未根据先决条件中所述安装 Python，将会看到 Python 扩展建议对话框。  安装并重载 Visual Studio Code 以完成安装。

    >![用于 Visual Studio Code 的 HDInsight - Python 安装](./media/hdinsight-for-vscode/hdinsight-vscode-install-python.png)

4. [连接](#connect-to-hdinsight-cluster)到 Azure 帐户（如果尚未这样做）。

5. 将以下代码复制并粘贴到脚本文件中：
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

6. 右键单击脚本编辑器，选择“HDInsight:PySpark Interactive”提交查询，或使用快捷键 **Ctrl + Alt + I**。  

7. 如果尚未指定默认群集，请选择群集。 工具还允许使用上下文菜单提交代码块而非整个脚本文件。 不久之后，查询结果将显示在新选项卡中。

   ![提交 python 作业结果](./media/hdinsight-for-vscode/pyspark-interactive-result.png) 

8. 工具还支持查询 **SQL 子句**查询。

   ![提交 python 作业结果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)运行查询时，提交状态将显示在底部状态栏的左侧。 当状态为“PySpark 内核(忙)”时，请不要提交其他查询。  

>[!NOTE]  
>群集可以保留会话信息。 定义的变量、函数和相应值将保留在会话中，因此，可在针对同一群集发出的多个服务调用中引用这些信息。 

### <a name="pyspark3-is-not-supported-with-spark2223"></a>Spark2.2/2.3 不支持 PySpark3

Spark 2.2 群集和 Spark2.3 群集不再支持 PySpark3，只有 Python 支持“PySpark”。 在 Python3 中提交到 spark 2.2/2.3 会失败，这是已知的问题。

   ![提交到 python3 出错](./media/hdinsight-for-vscode/hdi-azure-hdinsight-py3-error.png)

遵循以下步骤来使用 Python2.x： 

1. 在本地计算机上安装 Python 2.7 并将其添加到系统路径。

2. 重新启动 Visual Studio Code。

3. 在状态栏中单击“Python XXX”，然后选择目标 Python，以切换到 Python 2。

   ![选择 Python 版本](./media/hdinsight-for-vscode/hdi-azure-hdinsight-select-python.png)


## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批处理作业

1. 重新打开[前面](#open-hdinsight-work-folder)创建的文件夹 **HDexample**（如果已关闭）。  

2. 遵循[前面](#open-hdinsight-work-folder)所述的步骤创建新文件 **BatchFile.py**。

3. [连接](#connect-to-hdinsight-cluster)到 Azure 帐户（如果尚未这样做）。

4. 将以下代码复制并粘贴到脚本文件中：

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

提交 Python 作业后，提交日志会显示在 Visual Studio Code 的“输出”窗口中。 同时还会显示 **Spark UI URL** 和 **Yarn UI URL**。 可以在 Web 浏览器中打开 URL 来跟踪作业状态。

## <a name="apache-livy-configuration"></a>Apache Livy 配置

支持 [Apache Livy](https://livy.incubator.apache.org/) 配置，可在工作空间文件夹中的 **.VSCode\settings.json** 内设置此配置。 目前，livy 配置仅支持 Python 脚本。 详细信息请参阅 [Livy README](https://github.com/cloudera/livy/blob/master/README.rst )。

<a id="triggerlivyconf"></a>**如何触发 livy 配置**

方法 1  
1. 在菜单栏中，导航到“文件” > “首选项” > “设置”。  
2. 在“搜索设置”文本框中，输入“HDInsight 作业提交：Livy 配置”。  
3. 在相关搜索结果中选择“在 settings.json 中编辑”。

方法 2   
提交一个文件，然后观察 .vscode 文件夹是否已自动添加到工作文件夹。 可以单击“.vscode\settings.json”来查找 livy 配置。

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

**Azure HDInsight** 已添加到“资源管理器”视图中。 可以直接通过 **Azure HDInsight** 浏览和管理群集。

1. [连接](#connect-to-hdinsight-cluster)到 Azure 帐户（如果尚未这样做）。

2. 在菜单栏中，导航到“视图” > “资源管理器”。

3. 在左窗格中，展开“AZURE HDINSIGHT”。  将列出可用的订阅和群集（支持 Spark、Hadoop 和 HBase）。 

   ![Azure HDInsight 订阅](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. 展开群集以查看 hive 元数据数据库和表架构。

   ![Azure HDInsight 群集](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="additional-features"></a>其他功能

Visual Studio Code 的 HDInsight 支持以下功能：

- **IntelliSense 自动完成**。 弹出关键字、方法、变量等相关建议。 不同图标表示不同类型的对象。

    ![用于 Visual Studio Code 的 HDInsight 工具 IntelliSense 对象类型](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 错误标记**。 语言服务会为 Hive 脚本的编辑错误添加下划线。     
- **语法突出显示**。 语言服务使用不同颜色来区分变量、关键字、数据类型、函数等等。 

    ![用于 Visual Studio Code 的 HDInsight 工具语法突出显示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)


## <a name="unlink-cluster"></a>取消链接群集

1. 在菜单栏中，导航到“视图” > “命令面板...”，然后输入 **HDInsight:Unlink a Cluster**。  

2. 选择要取消链接的群集。  

3. 查看“输出”视图以进行验证。  


## <a name="logout"></a>注销  

在菜单栏中，导航到“视图” > “命令面板...”，然后输入 **HDInsight:Logout** 命令。  右下角会弹出一个窗口，指出“注销成功!”。


## <a name="next-steps"></a>后续步骤
有关使用 Visual Studio Code 的 HDInsight 的演示视频，请参阅 [Visual Studio Code 的 HDInsight](https://go.microsoft.com/fwlink/?linkid=858706)
