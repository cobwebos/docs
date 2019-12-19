---
title: 适用于 Visual Studio Code 的 Azure HDInsight
description: 了解如何使用适用于 Visual Studio Code 的 Spark 和 Hive 工具 (Azure HDInsight) 来创建、提交查询和脚本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/11/2019
ms.openlocfilehash: 03d0d26a21e710c07019d3ffcb13a1482a96af50
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311733"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>使用适用于 Visual Studio Code 的 Spark 和 Hive 工具

了解如何使用适用于 Visual Studio Code 的 Spark 和 Hive 工具，为 Apache Spark 创建和提交 Apache Hive 批处理作业、交互式 Hive 查询和 PySpark 脚本。 本文首先介绍如何在 Visual Studio Code 中安装 Spark 和 Hive 工具，然后演练如何向 Spark 和 Hive 工具提交作业。  

可以在 Visual Studio Code 支持的平台（包括 Windows、Linux 和 macOS）上安装 Spark 和 Hive 工具。 注意针对不同平台的以下先决条件。

## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要具有以下项：

- 一个 Azure HDInsight 群集。 若要创建群集，请参阅 [HDInsight 入门](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)。 或使用支持 Apache Livy 终结点的 Spark 和 Hive 群集。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 只有 Linux 和 macOS 需要 Mono。
- [Visual Studio Code 的 PySpark 交互式环境](set-up-pyspark-interactive-environment.md)。
- 本地目录。 本文使用 **C:\HD\HDexample**。

## <a name="install-spark--hive-tools"></a>安装 Spark 和 Hive 工具

满足先决条件后，可遵循以下步骤安装适用于 Visual Studio Code 的 Spark 和 Hive 工具：

1. 打开 Visual Studio Code。

2. 在菜单栏中，导航到“视图” **“扩展”。**  > 

3. 在搜索框中，输入“Spark 和 Hive”。

4. 从搜索结果中选择“Spark 和 Hive 工具”，然后选择“安装”：

   ![适用于 Visual Studio Code 的 Spark 和 Hive Python 安装](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 根据需要选择“重载”。

## <a name="open-a-work-folder"></a>打开工作文件夹

若要打开工作文件夹并在 Visual Studio Code 中创建文件，请执行以下步骤：

1. 在菜单栏中，导航到“文件” **“打开文件夹...”** “C:\HD\HDexample”，然后选择“选择文件夹”按钮。 >  >  该文件夹将显示在左侧的“资源管理器”视图中。

2. 在“资源管理器”视图中选择“HDexample”文件夹，然后选择工作文件夹旁边的“新建文件”图标：

   ![visual studio code“新建文件”图标](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. 为新文件命名，使用 `.hql`（Hive 查询）或 `.py`（Spark 脚本）作为文件扩展名。 本示例使用 **HelloWorld.hql**。

## <a name="set-the-azure-environment"></a>设置 Azure 环境

对于国家云用户，请按照以下步骤先设置 Azure 环境，然后使用**azure：登录**命令登录到 Azure：

1. 导航到“文件” **“首选项”** “设置”。 >  > 
2. 搜索以下字符串： **Azure： Cloud**。
3. 从列表中选择国家云：

   ![设置默认登录入口配置](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>连接到 Azure 帐户

将脚本从 Visual Studio Code 提交到群集之前，必须连接到 Azure 帐户，或链接群集（使用 Apache Ambari 用户名和密码凭据或已加入域的帐户）。 遵循以下步骤连接到 Azure：

1. 从菜单栏中，导航到 "**查看** > **命令面板 ...** "，然后输入**Azure：登录**：

    ![适用于 Visual Studio Code 的 Spark 和 Hive 工具登录](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 按照登录说明登录到 Azure。 连接后，Visual Studio Code 窗口底部的状态栏上会显示 Azure 帐户名称。  

## <a name="link-a-cluster"></a>链接群集

### <a name="link-azure-hdinsight"></a>链接： Azure HDInsight

可以使用 [Apache Ambari](https://ambari.apache.org/) 管理的用户名链接标准群集，也可以使用域用户名（例如：`user1@contoso.com`）链接 Enterprise Security Pack 安全 Hadoop 群集。

1. 从菜单栏中，导航到 "**查看**" " > **命令" 面板 ...** "，然后输入**Spark/Hive：链接群集**。

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

### <a name="link-generic-livy-endpoint"></a>链接：泛型 Livy 终结点

1. 从菜单栏中，导航到 "**查看**" " > **命令" 面板 ...** "，然后输入**Spark/Hive：链接群集**。

2. 选择链接的群集类型“通用 Livy 终结点”。

3. 输入通用 livy 终结点。 例如：http\://10.172.41.42:18080。

4. 选择授权类型“基本”或“无”。  如果选择“基本”：  
    &emsp;a. 输入 Ambari 用户名，默认为 **admin**。  
    &emsp;b. 输入 Ambari 密码。

5. 查看“输出”视图以进行验证。

## <a name="list-clusters"></a>列出群集

1. 从菜单栏中，导航到 "**查看** > **命令面板 ...** "，然后输入**Spark/Hive： List Cluster**。

2. 选择所需的订阅。

3. 查看“输出”视图。 此视图显示你的链接群集（或多个群集），以及你的 Azure 订阅下的所有群集：

    ![设置默认群集配置](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>设置默认群集

1. 重新打开**前面**所述的 [HDexample](#open-a-work-folder) 文件夹（如果已关闭）。  

2. 选择**前面**创建的 [HelloWorld.hql](#open-a-work-folder) 文件。 它将在脚本编辑器中打开。

3. 右键单击脚本编辑器，然后选择 " **Spark/Hive：设置默认群集**"。  

4. [连接](#connect-to-an-azure-account)到 Azure 帐户或链接某个群集（如果尚未这样做）。

5. 选择一个群集作为当前脚本文件的默认群集。 工具将自动更新 **.VSCode\settings.json** 配置文件：

   ![设置默认群集配置](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>提交交互式 Hive 查询和 Hive 批处理脚本

通过 Visual Studio Code 的 Spark 和 Hive 工具，可将交互式 Hive 查询和 Hive 批处理脚本提交到群集。

1. 重新打开**前面**所述的 [HDexample](#open-a-work-folder) 文件夹（如果已关闭）。  

2. 选择**前面**创建的 [HelloWorld.hql](#open-a-work-folder) 文件。 它将在脚本编辑器中打开。

3. 将以下代码复制并粘贴到 Hive 文件中，然后保存该文件：

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [连接](#connect-to-an-azure-account)到 Azure 帐户或链接某个群集（如果尚未这样做）。

5. 右键单击脚本编辑器，然后选择 " **Hive： Interactive** " 以提交查询，或使用 Ctrl + Alt + I 键盘快捷方式。  选择**Hive： Batch**以提交脚本，或使用 Ctrl + Alt + H 键盘快捷方式。  

6. 如果尚未指定默认群集，请选择群集。 工具还允许使用上下文菜单提交代码块而非整个脚本文件。 不久之后，查询结果将显示在新选项卡中：

   ![交互式 Apache Hive 查询结果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - "**结果**" 面板：可以将整个结果作为 CSV、JSON 或 Excel 文件保存到本地路径，也可以只选择多个行。

    - "**消息**" 面板：选择**行**号时，它将跳到正在运行的脚本的第一行。

## <a name="submit-interactive-pyspark-queries"></a>提交交互式 PySpark 查询

若要提交交互式 PySpark 查询，请执行以下步骤：

1. 重新打开**前面**所述的 [HDexample](#open-a-work-folder) 文件夹（如果已关闭）。  

2. 遵循**前面**所述的步骤创建新的 [HelloWorld.py](#open-a-work-folder) 文件。

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

4. [连接](#connect-to-an-azure-account)到 Azure 帐户或链接某个群集（如果尚未这样做）。

5. 选择所有代码，右键单击脚本编辑器，然后选择 " **Spark： PySpark Interactive** " 以提交查询。 或者，使用 Ctrl+Alt+I 快捷键。

   ![Pyspark Interactive 上下文菜单](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 如果尚未指定默认群集，请选择群集。 几分钟后， **Python 交互式**结果将显示在新的选项卡中。使用这些工具，还可以使用上下文菜单提交代码块而非整个脚本文件：

   ![pyspark interactive - python interactive 窗口](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png)

7. 输入 **%%info**，然后按 Shift+Enter 查看作业信息（可选）：

   ![pyspark 交互查看作业信息](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. 工具还支持 **Spark SQL** 查询：

   ![Pyspark Interactive - 查看结果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   运行查询时，提交状态将显示在下方状态栏的左侧。 当状态为“PySpark 内核(忙)”时，请不要提交其他查询。  

   > [!NOTE]
   >
   > 如果设置中的“已启用 Python 扩展”处于清除状态（默认设置已选中），则提交的 pyspark 交互结果将使用旧窗口：
   >
   > ![已禁用 pyspark interactive python 扩展](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批处理作业

1. 重新打开**前面**所述的 [HDexample](#open-a-work-folder) 文件夹（如果已关闭）。  

2. 遵循**前面**所述的步骤创建新的 [BatchFile.py](#open-a-work-folder) 文件。

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

5. 右键单击脚本编辑器，然后选择 " **Spark： PySpark Batch**"，或使用 Ctrl + Alt + H 键盘快捷方式。

6. 选择要将 PySpark 作业提交到的群集：

   ![提交 Python 作业结果输出](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

提交 Python 作业后，提交日志会显示在 Visual Studio Code 的“输出”窗口中。 同时还会显示 Spark UI URL 和 Yarn UI URL。 可以在 Web 浏览器中打开 URL 来跟踪作业状态。

## <a name="apache-livy-configuration"></a>Apache Livy 配置

支持 [Apache Livy](https://livy.incubator.apache.org/) 配置。 可在工作空间文件夹中的 **.VSCode\settings.json** 内配置 Apache Livy。 目前，Livy 配置仅支持 Python 脚本。 有关更多详细信息，请参阅 [Livy README](https://github.com/cloudera/livy/blob/master/README.rst )。

<a id="triggerlivyconf"></a>**如何触发 Livy 配置**

方法 1  
1. 在菜单栏中，导航到“文件” **“首选项”** “设置”。 >  > 
2. 在 "**搜索设置**" 框中，输入**HDInsight 作业提交： Livy 会议**。  
3. 在相关搜索结果中选择“在 settings.json 中编辑”。

方法2：提交文件，请注意，vscode 文件夹自动添加到工作文件夹。 可以通过选择“.vscode\settings.json”来查看 Livy 配置。

+ 项目设置：

    ![HDInsight Apache Livy 配置](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >对于 **driverMemory** 和 **executorMemory** 设置，请设置值和单位。 例如：1g 或1024m。

+ 支持的 Livy 配置：

    **POST/batches**请求正文

    | name | 说明 | type |
    | :- | :- | :- |
    | file | 包含要执行的应用程序的文件 | Path（必需） |
    | proxyUser | 运行作业时要模拟的用户 | String |
    | className | 应用程序 Java/Spark main 类 | String |
    | args | 应用程序的命令行参数 | 字符串列表 |
    | jars | 此会话中要使用的 Jar | 字符串列表 | 
    | pyFiles | 此会话中要使用的 Python 文件 | 字符串列表 |
    | 文件 | 此会话中要使用的文件 | 字符串列表 |
    | driverMemory | 要用于驱动程序进程的内存量 | String |
    | driverCores | 要用于驱动程序进程的内核数 | int |
    | executorMemory | 每个执行程序进程要使用的内存量 | String |
    | executorCores | 用于每个执行程序的内核数 | int |
    | numExecutors | 为此会话启动的执行程序数量 | int |
    | archives | 此会话中要使用的存档 | 字符串列表 |
    | 队列 | 要提交到的 YARN 队列的名称| String |
    | name | 此会话的名称 | String |
    | conf | Spark 配置属性 | key=val 的映射 |

    响应正文：创建的批处理对象。

    | name | 说明 | type |
    | :- | :- | :- |
    | id | 会话 ID | int |
    | appId | 此会话的应用程序 ID | String |
    | appInfo | 详细的应用程序信息 | key=val 的映射 |
    | log | 日志行 | 字符串列表 |
    | state |批处理状态 | String |

    > [!NOTE]
    > 提交脚本时，分配的 Livy 配置将显示在输出窗格中。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>通过资源管理器集成 Azure HDInsight

可以通过 **Azure HDInsight** 资源管理器直接在群集中预览 Hive 表：

1. [连接](#connect-to-an-azure-account)到 Azure 帐户（如果尚未这样做）。

2. 选择最左侧列中的“Azure”图标。

3. 在左侧窗格中，展开 " **AZURE： HDINSIGHT**"。 此时会列出可用的订阅和群集。

4. 展开群集以查看 Hive 元数据数据库和表架构。

5. 右键单击 Hive 表。 例如：**hivesampletable**。 选择“预览”。

   ![适用于 Visual Studio code 的 Spark 和 Hive - 预览 Hive 表](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. 此时会打开“预览结果”窗口：

   ![适用于 visual studio code 的 Spark 和 Hive - 预览结果窗口](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- “结果”面板

   可以将整个结果作为 CSV、JSON、Excel 保存到本地路径，也可以只选择多个行。

- “消息”面板
   1. 当表中的行数大于100时，将看到以下消息： "将为 Hive 表显示前100行"。
   2. 如果表中的行数小于或等于100，则会看到如下所示的消息： "为 Hive 表显示60行"。
   3. 如果表中没有内容，你将看到以下消息： "为 Hive 表显示0行"。

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

具有群集“仅限读取者”角色的用户不再可以将作业提交到 HDInsight 群集，也不可以查看 Hive 数据库。 需在 [Azure 门户](https://ms.portal.azure.com/)中联系群集管理员将你的角色升级到 [HDInsight 群集操作员](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。 如果你有有效的 Ambari 凭据，可遵循以下指导手动链接群集。

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

选择 Azure HDInsight 资源管理器展开 Data Lake Storage Gen2 帐户时，如果你的 Azure 帐户对 Gen2 存储没有访问权限，系统会提示你输入存储访问密钥。 验证访问密钥后，Data Lake Storage Gen2 帐户将自动展开。

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>将作业提交到使用 Data Lake Storage Gen2 的 HDInsight 群集

将作业提交到使用 Data Lake Storage Gen2 的 HDInsight 群集时，如果你的 Azure 帐户对 Gen2 存储没有写入访问权限，系统会提示你输入存储访问密钥。 验证访问密钥后，作业将成功提交。

![适用于 Visual Studio Code 的 Spark 和 Hive 工具 - 访问密钥](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> 可以在 Azure 门户中获取存储帐户的访问密钥。 有关详细信息，请参阅[查看和复制访问密钥](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys)。

## <a name="unlink-cluster"></a>取消链接群集

1. 从菜单栏中转到 "**查看** > **命令面板**"，然后输入**Spark/Hive：取消链接群集**。  

2. 选择要取消链接的群集。  

3. 查看“输出”视图以进行验证。  

## <a name="sign-out"></a>注销  

从菜单栏中转到 "**查看** > **命令面板**"，然后输入 " **Azure：注销**"。

## <a name="next-steps"></a>后续步骤

有关演示如何对 Visual Studio Code 使用 Spark & Hive 的视频，请参阅[spark & hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)。
