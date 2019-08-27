---
title: 适用于 Visual Studio Code 的 Azure HDInsight
description: 了解如何使用 Spark & Hive 工具 (Azure HDInsight) Visual Studio Code 创建和提交查询和脚本。
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 226862c51417e311bc39feefa9f5e860d55b7dc1
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70034108"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>为 Visual Studio Code 使用 Spark & Hive 工具

了解如何使用 Spark & Hive 工具来 Visual Studio Code 创建和提交 Apache Hive 批处理作业、交互式 Hive 查询和用于 Apache Spark 的 PySpark 脚本。 首先, 我们将介绍如何在 Visual Studio Code 中安装 Spark & Hive 工具, 然后逐步介绍如何将作业提交到 Spark & Hive 工具。  

可以在 Visual Studio Code 支持的平台 (包括 Windows、Linux 和 macOS) 上安装 Spark & Hive 工具。 请注意不同平台的以下先决条件。


## <a name="prerequisites"></a>先决条件

完成本文中的步骤需要具有以下项：

- 一个 Azure HDInsight 群集。 若要创建群集，请参阅 [HDInsight 入门](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)。 或使用支持 Apache Livy 终结点的 Spark 和 Hive 群集。
- [Visual Studio Code](https://code.visualstudio.com/)。
- [Mono](https://www.mono-project.com/docs/getting-started/install/)。 只有 Linux 和 macOS 需要 Mono。
- [用于 Visual Studio Code 的 PySpark 交互式环境](set-up-pyspark-interactive-environment.md)。
- 名为 **HDexample** 的本地目录。  本文使用 **C:\HD\HDexample**。

## <a name="install-spark--hive-tools"></a>安装 Spark & Hive 工具

满足先决条件后, 可以按照以下步骤安装 Spark & Hive 工具来 Visual Studio Code:

1. 打开 Visual Studio Code。

2. 从菜单栏中转到 "**视图** > **扩展**"。

3. 在搜索框中, 输入**Spark & Hive**。

4. 从搜索结果中选择 " **Spark & Hive 工具**", 然后选择 "**安装**":

   ![用于 Visual Studio Code Python 安装的 Spark & Hive](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. 如果需要, 请选择 "**重新加载**"。


## <a name="open-a-work-folder"></a>打开工作文件夹

若要打开工作文件夹并在 Visual Studio Code 中创建文件, 请执行以下步骤:

1. 从菜单栏中转到 "**文件** > " "**打开文件夹** > " "**C:\HD\HDexample**", 然后选择 "**选择文件夹**" 按钮。 该文件夹将显示在左侧的“资源管理器”视图中。

2. 在**资源管理器**视图中, 选择 " **HDexample** " 文件夹, 然后选择工作文件夹旁边的 "**新建文件**" 图标:

   ![新建文件](./media/hdinsight-for-vscode/new-file.png)

3. 使用`.hql` (Hive 查询) `.py`或 (Spark 脚本) 文件扩展名来命名新文件。 本示例使用 **HelloWorld.hql**。

## <a name="set-the-azure-environment"></a>设置 Azure 环境

对于国家云用户, 请按照以下步骤先设置 azure 环境, 然后使用**azure:** 登录命令以登录到 Azure:
   
1. 选择**File\Preferences\Settings**。
2. 搜索以下字符串:**Azure:形成**
3. 从列表中选择 "国家/地区" 云:

   ![设置默认登录入口配置](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>连接到 Azure 帐户

必须先连接到 Azure 帐户, 或链接群集 (使用 Apache Ambari 用户名和密码凭据或已加入域的帐户), 然后才能从 Visual Studio Code 向群集提交脚本。 请按照以下步骤连接到 Azure:

1. 从菜单栏中转到 "**查看** > " "**命令面板**", **然后输入 Azure:** 登录:

    ![Spark & 用于 Visual Studio Code 登录的 Hive 工具](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. 按照登录说明登录到 Azure。 连接后, Azure 帐户名称将显示在 "Visual Studio Code" 窗口底部的状态栏中。  
  

## <a name="link-a-cluster"></a>链接群集

### <a name="link-azure-hdinsight"></a>链接：Azure HDInsight

你可以通过使用[Apache Ambari](https://ambari.apache.org/)托管的用户名来链接常规群集, 或者可以使用域用户名 (如: `user1@contoso.com`) 链接企业安全包安全 Hadoop 群集。

1. 从菜单栏中转到 "**查看** > " "**命令面板**", **然后输入 Spark/Hive:链接群集”。**

   ![链接群集命令](./media/hdinsight-for-vscode/link-cluster-command.png)

2. 选择链接的群集类型“Azure HDInsight”。

3. 输入 HDInsight 群集 URL。

4. 输入 Ambari 用户名;默认值为**admin**。

5. 输入 Ambari 密码。

6. 选择群集类型。

7. 设置群集的显示名称 (可选)。

8. 查看“输出”视图以进行验证。

   > [!NOTE]  
   > 如果群集登录到 Azure 订阅并链接到群集, 则使用链接的用户名和密码。  


### <a name="link-generic-livy-endpoint"></a>链接：泛型 Livy 终结点

1. 从菜单栏中转到 "**查看** > " "**命令面板**", **然后输入 Spark/Hive:链接群集”。**

2. 选择链接的群集类型“通用 Livy 终结点”。

3. 输入一般的 Livy 终结点。 例如: http\://10.172.41.42: 18080。

4. 选择授权类型“基本”或“无”。  如果选择 "**基本**":  
    &emsp;a. 输入 Ambari 用户名;默认值为**admin**。  
    &emsp;b. 输入 Ambari 密码。

5. 查看“输出”视图以进行验证。

## <a name="list-clusters"></a>列出群集

1. 从菜单栏中转到 "**查看** > " "**命令面板**", **然后输入 Spark/Hive:List Cluster**。

2. 选择所需的订阅。

3. 查看“输出”视图。 此视图显示你的链接群集 (或群集) 以及 Azure 订阅下的所有群集:

    ![设置默认群集配置](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="set-the-default-cluster"></a>设置默认群集

1. 重新打开[前面](#open-a-work-folder)讨论的**HDexample**文件夹 (如果已关闭)。  

2. 选择[前面](#open-a-work-folder)创建的**HelloWorld**文件。 它将在脚本编辑器中打开。

3. 右键单击脚本编辑器, 然后选择 **"Spark/Hive:** 设置默认群集”。  

4. [连接](#connect-to-an-azure-account)到 Azure 帐户, 或链接群集 (如果尚未这样做)。

5. 选择一个群集作为当前脚本文件的默认群集。 工具会自动更新 **。VSCode\settings.json**配置文件:

   ![设置默认群集配置](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)


## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>提交交互式 Hive 查询和 Hive 批处理脚本

借助 Spark & Hive Tools for Visual Studio Code, 你可以将交互式 Hive 查询和 Hive 批处理脚本提交到群集。

1. 重新打开[前面](#open-a-work-folder)讨论的**HDexample**文件夹 (如果已关闭)。  

2. 选择[前面](#open-a-work-folder)创建的**HelloWorld**文件。 它将在脚本编辑器中打开。


3. 将以下代码复制并粘贴到 Hive 文件中, 然后保存该文件:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [连接](#connect-to-an-azure-account)到 Azure 帐户, 或链接群集 (如果尚未这样做)。

5. 右键单击脚本编辑器, 然后选择 **"Hive":"** 交互式" 以提交查询, 或使用 Ctrl + Alt + I 键盘快捷方式。  选择**Hive:用于**提交脚本的批处理, 或使用 Ctrl + Alt + H 键盘快捷方式。  

6. 如果尚未指定默认群集, 请选择群集。 使用这些工具, 还可以使用上下文菜单提交代码块而非整个脚本文件。 几分钟后, 查询结果将显示在新选项卡中:

   ![交互式 Hive 结果](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - “结果”面板：您可以将整个结果作为 CSV、JSON 或 Excel 文件保存到本地路径, 也可以只选择多个行。

    - “消息”面板：选择**行**号时, 它将跳到正在运行的脚本的第一行。

## <a name="submit-interactive-pyspark-queries"></a>提交交互式 PySpark 查询

若要提交交互式 PySpark 查询, 请执行以下步骤:

1. 重新打开[前面](#open-a-work-folder)讨论的**HDexample**文件夹 (如果已关闭)。  

2. 按照[前面](#open-a-work-folder)的步骤创建一个新的**HelloWorld.py**文件。

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

4. [连接](#connect-to-an-azure-account)到 Azure 帐户, 或链接群集 (如果尚未这样做)。

5. 选择所有代码, 右键单击脚本编辑器, 然后选择**Spark:PySpark Interactive**以提交查询。 或者, 使用 Ctrl + Alt + I 快捷方式。

   ![pyspark 交互上下文菜单](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. 如果尚未指定默认群集, 请选择该群集。 几分钟后, **Python 交互式**结果将显示在新的选项卡中。使用这些工具, 还可以使用上下文菜单提交代码块而非整个脚本文件:

   ![pyspark interactive - python interactive 窗口](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. 输入 **%% info**, 然后按 Shift + enter 查看作业信息 (可选):

   ![查看作业信息](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. 该工具还支持**SPARK SQL**查询:

   ![Pyspark Interactive - 查看结果](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   运行查询时, "提交状态" 显示在底部状态栏的左侧。 当状态为“PySpark 内核(忙)”时，请不要提交其他查询。  

   > [!NOTE] 
   >
   > 如果在设置中清除**了 "Python 扩展**" (默认情况下处于选中状态), 则提交的 pyspark 交互结果将使用旧窗口:
   >
   > ![已禁用 pyspark interactive python 扩展](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)


## <a name="submit-pyspark-batch-job"></a>提交 PySpark 批处理作业

1. 重新打开[前面](#open-a-work-folder)讨论的**HDexample**文件夹 (如果已关闭)。  

2. 按照[前面](#open-a-work-folder)的步骤创建新的**BatchFile.py**文件。

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

4. [连接](#connect-to-an-azure-account)到 Azure 帐户, 或链接群集 (如果尚未这样做)。

5. 右键单击脚本编辑器, 然后选择**Spark:PySpark 批处理**, 或使用 Ctrl + Alt + H 键盘快捷方式。 

6. 选择要将 PySpark 作业提交到的群集:

   ![提交 python 作业结果](./media/hdinsight-for-vscode/submit-pythonjob-result.png) 

提交 Python 作业后，提交日志会显示在 Visual Studio Code 的“输出”窗口中。 还显示了 Spark UI URL 和 Yarn UI URL。 可以在 Web 浏览器中打开 URL 来跟踪作业状态。

## <a name="apache-livy-configuration"></a>Apache Livy 配置

支持[Apache Livy](https://livy.incubator.apache.org/)配置。 你可以在中配置它 **。** 工作区文件夹中的 VSCode\settings.json 文件。 目前, Livy 配置仅支持 Python 脚本。 有关更多详细信息, 请参阅[LIVY 自述文件](https://github.com/cloudera/livy/blob/master/README.rst )。

<a id="triggerlivyconf"></a>**如何触发 Livy 配置**

方法 1  
1. 从菜单栏中转到 "**文件** > " "**首选项** > " "**设置**"。  
2. 在 "**搜索设置**" 框中 **, 输入 HDInsight 作业提交:** Livy 配置”。  
3. 在相关搜索结果中选择“在 settings.json 中编辑”。

方法 2   
提交一个文件, 请注意, vscode 文件夹自动添加到工作文件夹。 可以通过选择 " **vscode\settings.json**" 查看 Livy 配置。

+ 项目设置：

    ![Livy 配置](./media/hdinsight-for-vscode/hdi-livyconfig.png)

    >[!NOTE]
    >对于 " **driverMemory** " 和 " **executorMemory** " 设置, 请设置值和单位。 例如：1g 或1024m。

+ 支持的 Livy 配置:   

    **POST /batches**   
    请求正文

    | name | description | type | 
    | :- | :- | :- | 
    | 文件 | 包含要执行的应用程序的文件 | 路径 (必需) |
    | proxyUser | 运行作业时要模拟的用户 | String |
    | className | 应用程序 Java/Spark main 类 | String |
    | args | 应用程序的命令行参数 | 字符串列表 |
    | jars | 此会话中要使用的 jar | 字符串列表 | 
    | pyFiles | 此会话中要使用的 Python 文件 | 字符串列表 |
    | files | 此会话中要使用的文件 | 字符串列表 |
    | driverMemory | 要用于驱动程序进程的内存量 | String |
    | driverCores | 要用于驱动程序进程的内核数 | Int |
    | executorMemory | 每个执行程序进程要使用的内存量 | String |
    | executorCores | 用于每个执行程序的内核数 | Int |
    | numExecutors | 为此会话启动的执行程序数量 | Int |
    | archives | 此会话中要使用的存档 | 字符串列表 |
    | queue | 要提交到的 YARN 队列的名称| String |
    | name | 此会话的名称 | String |
    | conf | Spark 配置属性 | key=val 的映射 |

    响应正文   
    创建的批处理对象。

    | name | description | type |
    | :- | :- | :- | 
    | id | 会话 ID | Int | 
    | appId | 此会话的应用程序 id | String |
    | appInfo | 详细的应用程序信息 | key=val 的映射 |
    | 日志 | 日志行 | 字符串列表 |
    | 省/自治区/直辖市 |批处理状态 | String |

    >[!NOTE]
    >提交脚本时, "输出" 窗格中将显示已分配的 Livy 配置。

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>通过资源管理器集成 Azure HDInsight

**Azure HDInsight**已添加到资源管理器视图。 可以直接通过**Azure HDInsight**来浏览和管理群集。

1. [连接](#connect-to-an-azure-account)到 Azure 帐户, 或链接群集 (如果尚未这样做)。

2. 从菜单栏中转到 "**视图** > **资源管理器**"。

3. 在左窗格中，展开“AZURE HDINSIGHT”。  列出了可用的订阅和群集 (Spark、Hadoop 和 HBase):

   ![Azure HDInsight 订阅](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. 展开群集以查看 hive 元数据数据库和表架构:

   ![Azure HDInsight 群集](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)


## <a name="preview-hive-table"></a>预览 Hive 表
可以通过**Azure HDInsight**资源管理器直接预览群集中的 Hive 表:
1. [连接](#connect-to-an-azure-account)到 Azure 帐户（如果尚未这样做）。

2. 选择最左侧列中的**Azure**图标。

3. 在左窗格中，展开“AZURE HDINSIGHT”。 将列出可用的订阅和群集。

4. 展开该群集以查看 Hive 元数据数据库和表架构。

5. 右键单击 Hive 表。 例如: **hivesampletable**。 选择“预览”。 

   ![用于 Visual Studio Code 预览 hive 表的 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. 此时将打开 "**预览结果**" 窗口:

   ![用于 Visual Studio Code 预览结果窗口的 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)
   
- 结果面板

   您可以将整个结果作为 CSV、JSON 或 Excel 文件保存到本地路径, 也可以只选择多个行。

- 消息面板
   1. 当表中的行数大于100时, 将看到以下消息:"为 Hive 表显示前100行。"
   2. 如果表中的行数小于或等于 100, 则会看到如下所示的消息:为 Hive 表显示了 "60 行"。
   3. 如果表中没有内容, 将看到以下消息:"为 Hive 表显示0行"。

        >[!NOTE]
        >
        >在 Linux 中, 安装 xclip 以启用复制表数据。
        >
        >![适用于 Linux 中的 Visual Studio code 的 Spark & Hive](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
    ## <a name="additional-features"></a>其他功能

用于 Visual Studio Code 的 Spark & Hive 还支持以下功能:

- **IntelliSense 自动完成**。 建议弹出关键字、方法、变量和其他编程元素。 不同图标表示不同类型的对象：

    ![Spark & 用于 Visual Studio Code IntelliSense 对象类型的 Hive 工具](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)
- **IntelliSense 错误标记**。 语言服务在 Hive 脚本中编辑错误。     
- **语法突出显示**。 语言服务使用不同的颜色来区分变量、关键字、数据类型、函数和其他编程元素:

    ![Spark & Hive 工具来 Visual Studio Code 语法突出显示](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>仅限读取者角色

为群集分配了 "仅读者" 角色的用户无法再将作业提交到 HDInsight 群集, 也无法查看 Hive 数据库。 请与群集管理员联系, 将角色升级到[Azure 门户](https://ms.portal.azure.com/)中的[**HDInsight 群集操作员**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user)。 如果具有有效的 Ambari 凭据, 则可使用以下指南手动链接群集。

### <a name="browse-the-hdinsight-cluster"></a>浏览 HDInsight 群集  

当你选择 "Azure HDInsight 资源管理器" 来展开 HDInsight 群集时, 如果你具有群集的 "仅读取者" 角色, 则系统会提示你链接群集。 使用以下方法通过 Ambari 凭据链接到群集。 

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>将作业提交到 HDInsight 群集

将作业提交到 HDInsight 群集时, 如果你在群集的仅读取者角色中, 系统会提示你链接群集。 使用以下步骤通过 Ambari 凭据链接到该群集。

### <a name="link-to-the-cluster"></a>链接到群集

1.  请输入有效的 Ambari 用户名。
2.  输入有效密码。

   ![Spark & 用于 Visual Studio Code 用户名的 Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Spark & 用于 Visual Studio Code 密码的 Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >你可以使用`Spark / Hive: List Cluster`来检查链接的群集:
  >
  >![Spark & 用于 Visual Studio Code 读取器链接的 Hive 工具](./media/hdinsight-for-vscode/list-cluster-result.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>浏览 Data Lake Storage Gen2 帐户

当你选择 Azure HDInsight 资源管理器来展开 Data Lake Storage Gen2 帐户时, 如果你的 Azure 帐户无权访问 Gen2 存储, 则系统会提示你输入存储访问密钥。 验证访问密钥后, Data Lake Storage Gen2 帐户自动展开。

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>使用 Data Lake Storage Gen2 将作业提交到 HDInsight 群集

使用 Data Lake Storage Gen2 将作业提交到 HDInsight 群集时, 如果你的 Azure 帐户对 Gen2 存储没有写入权限, 则系统将提示你输入存储访问密钥。 验证访问密钥后, 会成功提交作业。

![Spark & 用于 Visual Studio Code AccessKey 的 Hive 工具](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)   

> [!NOTE]
> 
>可以从 Azure 门户获取存储帐户的访问密钥。 有关详细信息, 请参阅[查看和复制访问密钥](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys)。

## <a name="unlink-cluster"></a>取消链接群集

1. 从菜单栏中转到 "**查看** > " "**命令面板**", 然后**输入 Spark/Hive:Unlink a Cluster。**  

2. 选择要取消链接的群集。  

3. 请参阅**输出**视图进行验证。  

## <a name="sign-out"></a>注销  

从菜单栏中转到 "**查看** > " "**命令面板**", 然后**输入 Azure:** 注销。


## <a name="next-steps"></a>后续步骤
有关演示如何对 Visual Studio Code 使用 Spark & Hive 的视频, 请参阅[spark & hive for Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706)。
