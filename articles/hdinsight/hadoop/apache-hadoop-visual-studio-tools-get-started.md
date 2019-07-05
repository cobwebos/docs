---
title: 使用用于 Visual Studio 的 Data Lake 工具连接到 Apache Hadoop - Azure HDInsight
description: 了解如何安装针对 Visual Studio 的 Data Lake 工具并用其连接到 Azure HDInsight 中的 Apache Hadoop 群集，然后运行 Hive 查询。
keywords: hadoop 工具,hive 查询,visual studio,visual studio hadoop
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 485f3a4b6a5fde532229873fe1f3feaa30ece523
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450194"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Apache Hive 查询

了解如何使用[用于 Visual Studio 的 Microsoft Azure Data Lake 和流分析工具](https://www.microsoft.com/download/details.aspx?id=49504)（也称为 Data Lake 工具）连接到 [Azure HDInsight](../hdinsight-hadoop-introduction.md) 中的 Apache Hadoop 群集并提交 Hive 查询。  

有关使用 HDInsight 的详细信息，请参阅 [HDInsight 简介](../hdinsight-hadoop-introduction.md)和 [HDInsight 入门](apache-hadoop-linux-tutorial-get-started.md)。  

有关连接到 Apache Storm 群集的详细信息，请参阅[使用 Visual Studio 为 Apache Storm on HDInsight 开发 C# 拓扑](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。

可以使用用于 Visual Studio 的 Data Lake 工具访问 Azure Data Lake Analytics 和 HDInsight。 有关 Data Lake 工具的信息，请参阅[使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="prerequisites"></a>必备组件

若要完成这篇文章，并使用用于 Visual Studio 的 Data Lake 工具，需备齐以下项目：

* 一个 Azure HDInsight 群集。 若要创建 HDInsight 群集，请参阅[在 Azure HDInsight 中使用 Apache Hadoop 入门](apache-hadoop-linux-tutorial-get-started.md)。 若要运行交互式 Apache Hive 查询，需使用 [HDInsight 交互式查询](../interactive-query/apache-interactive-query-get-started.md)群集。  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)（2013 或更高版本）。  [Visual Studio Community Edition](https://visualstudio.microsoft.com/vs/community/) 是免费的。  另请参阅[安装 Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) 和 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。 有轻微的接口与 Visual Studio 2019 的变体。

  > [!IMPORTANT]  
  > Visual Studio 2013 不再支持 Data Lake 工具。

## <a name="install-data-lake-tools-for-visual-studio"></a>安装针对 Visual Studio 的 Data Lake 工具  
<a name="install-or-update-data-lake-tools-for-visual-studio"></a>

* Visual Studio 2017 或 Visual Studio 2019  
  在安装期间，请确保至少包含工作负荷“Azure 开发”或“数据存储和处理”。    

  对于现有安装，请在菜单栏中导航到“工具” > “获取工具和功能...”打开 Visual Studio 安装程序。    然后至少选择工作负荷“Azure 开发”或“数据存储和处理”。  

  ![Visual Studio 安装程序的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/vs2017_installation.png)

* Visual Studio 2013 和 2015  
  [下载 Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)。 请选择与 Visual Studio 版本匹配的 Data Lake 工具版本。  

> [!NOTE]  
> 目前，用于 Visual Studio 的 Data Lake 工具仅提供英文版。

## <a name="update-data-lake-tools-for-visual-studio"></a>更新用于 Visual Studio 的 Data Lake 工具  

1. 打开 Visual Studio。

2. 在菜单栏中，导航到“工具” > “扩展和更新...”。  

3. 在“扩展和更新”窗口中，展开左侧的“更新”。  

4. 如果有可用的更新，“Azure Data Lake 和流分析工具”会显示在主窗口中。   选择“更新”  。

> [!NOTE]  
> 只能使用 Data Lake 工具 2.3.0.0 或更高版本连接到交互式查询群集，然后运行交互式 Hive 查询。

## <a name="connect-to-azure-subscriptions"></a>连接到 Azure 订阅
可以使用用于 Visual Studio 的 Data Lake 工具连接到 HDInsight 群集，执行一些基本管理操作，以及运行 Hive 查询。

> [!NOTE]  
> 有关连接到常规 Hadoop 群集的信息，请参阅 [Write and submit Hive queries by using Visual Studio](https://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)（使用 Visual Studio 编写和提交 Hive 查询）。

若要连接到 Azure 订阅，请执行以下操作：

1. 打开 Visual Studio。

2. 在菜单栏中，导航到“视图” > “服务器资源管理器”。  

3. 在服务器资源管理器中右键单击“Azure”并选择“连接到 Microsoft Azure 订阅...”，然后完成登录过程。  

4. 在服务器资源管理器中，会显示现有 HDInsight 群集的列表。 如果没有任何群集，可以使用 Azure 门户、Azure PowerShell 或 HDInsight SDK 创建一个群集。 有关详细信息，请参阅[创建 HDInsight 群集](../hdinsight-hadoop-provision-linux-clusters.md)。

   ![服务器资源管理器中的用于 Visual Studio 的 Data Lake 工具群集列表的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png "服务器资源管理器中的用于 Visual Studio 的 Data Lake 工具群集列表")

5. 展开 HDInsight 群集。 **Hive 数据库**，默认存储帐户、 链接的存储帐户，并**Hadoop 服务日志**出现。 可以进一步展开条目。

连接到 Azure 订阅后，可执行以下任务。

若要从 Visual Studio 连接到 Azure 门户，请执行以下操作：

1. 在服务器资源管理器中，导航到“Azure” > “HDInsight”并选择你的群集。  

2. 右键单击 HDInsight 群集，然后选择 **[原文如此] 在 Azure 门户中管理群集**。

通过 Visual Studio 提问和/或提供反馈：

1. 在服务器资源管理器中，导航到“Azure” > “HDInsight”。  

2. 右键单击“HDInsight”，并选择“MSDN 论坛”以提问，或选择“提供反馈”以提供反馈。   

## <a name="link-a-cluster"></a>链接群集
可以通过右键单击链接群集**HDInsight**然后选择**HDInsight 群集链接**。 输入**连接 URL**、**用户名**和**密码**，依次单击“下一步”  、“完成”  ，群集应在“HDInsight”节点下成功列出。

![针对 Visual Studio 的 Data Lake 工具“链接群集”对话框的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

右键单击链接的群集，选择“编辑”  ，用户可以更新群集信息。 添加 HDInsight 群集目前仅支持 Hive。

![针对 Visual Studio 的 Data Lake 工具“链接群集更新”的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>浏览链接的资源
在“服务器资源管理器”中，可以看到默认存储帐户和任何链接的存储帐户。 如果展开默认存储帐户，则可以看到存储帐户中的容器。 默认存储帐户和默认容器将处于标记状态。 右键单击任何容器即可查看容器内容。

![服务器资源管理器中用于 Visual Studio 的 Data Lake 工具列表列表链接资源的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png "列表链接资源")

打开一个容器后，可以使用以下按钮来上传、删除和下载 Blob：

![服务器资源管理器中用于 Visual Studio 的 Data Lake 工具 Blob 操作的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png "在服务器资源管理器中上传、删除和下载 Blob")

## <a name="run-interactive-apache-hive-queries"></a>运行交互式 Apache Hive 查询
[Apache Hive](https://hive.apache.org) 是构建于 Hadoop 之上的数据仓库基础结构。 Hive 用于数据汇总、查询和分析。 可以使用用于 Visual Studio 的 Data Lake 工具从 Visual Studio 运行 Hive 查询。 有关 Hive 的详细信息，请参阅[将 Apache Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)。

[交互式查询](../interactive-query/apache-interactive-query-get-started.md)使用 Apache Hive 2.1 中的 [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP)。 交互式查询为在存储的大型数据集上进行的复杂数据仓库样式查询带来了交互性。 相对于传统的 Hive 批处理作业，在交互式查询上运行 Hive 查询速度要快得多。 

> [!NOTE]  
> 仅当连接到 [HDInsight 交互式查询](../interactive-query/apache-interactive-query-get-started.md)群集时，才能运行交互式 Hive 查询。

也可使用用于 Visual Studio 的 Data Lake 工具来查看 Hive 作业中的内容。 用于 Visual Studio 的 Data Lake 工具可收集和显示某些 Hive 作业的 Yarn 日志。

在服务器资源管理器中，导航到“Azure” > “HDInsight”并选择你的群集。    后续部分所述的操作将在服务器资源管理器中的此位置着手。

### <a name="view-hivesampletable"></a>查看 hivesampletable
所有 HDInsight 群集都有一个名为 `hivesampletable` 的默认示例 Hive 表。  

在群集中，导航到“Hive 数据库” > “默认” > “hivesampletable”。   

* 查看 `hivesampletable` 架构：  
展开“hivesampletable”。 

* 查看 `hivesampletable` 数据：  
右键单击“hivesampletable”并选择“查看前 100 行”。    这相当于使用 Hive ODBC 驱动程序运行以下 Hive 查询：

   `SELECT * FROM hivesampletable LIMIT 100`

  可以自定义行计数。

  ![HDInsight Hive Visual Studio 架构查询的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-hive-schema.png "Hive 查询结果")

### <a name="create-hive-tables"></a>创建 Hive 表
若要创建 Hive 表，可以使用 GUI，也可以使用 Hive 查询。 有关使用 Hive 查询的信息，请参阅 [运行 Apache Hive 查询](#run.queries)。

1. 在群集中，导航到“Hive 数据库” > “默认”。  

2. 右键单击“默认”并选择“创建表”。  

3. 根据需要配置表。  

4. 选择“创建表”  ，以便提交创建新 Hive 表的作业。

    ![HDInsight Visual Studio 工具“创建表”窗口的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png "创建 Hive 表")

### <a name="run.queries"></a>创建并运行 Hive 查询
可以使用两个选项来创建并运行 Hive 查询：

* 创建临时查询
* 创建 Hive 应用程序

创建并运行临时查询：

1. 右键单击要运行查询的群集，然后选择“编写 Hive 查询”。   

2. 输入以下 Hive 查询：

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

    Hive 编辑器支持 IntelliSense。 用于 Visual Studio 的 Data Lake 工具支持在编辑 Hive 脚本时加载远程元数据。 例如，如果键入 `SELECT * FROM`，则 IntelliSense 会列出所有建议的表名称。 在指定表名称后，IntelliSense 会列出列名称。 这些工具支持大多数 Hive DML 语句、子查询和内置 UDF。

    ![HDInsight Visual Studio Tools IntelliSense 示例 1 的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png "U-SQL IntelliSense")

    ![HDInsight Visual Studio Tools IntelliSense 示例 2 的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png "U-SQL IntelliSense")

   > [!NOTE]  
   > IntelliSense 只建议 HDInsight 工具栏中所选群集的元数据。

3. 选择执行模式：

    * **交互式**  

      确保“交互式”已选中，然后选择“执行”。  

      ![查询和“执行”的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/execute.png)  

    * **批处理**  

      确保“批处理”已选中，然后选择“提交”。    如果选择高级提交选项，请为脚本配置“作业名称”、“参数”、“其他配置”和“状态目录”。    

      ![查询和“批处理”的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/batch.png)  

      ![HDInsight Hadoop Hive 查询的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png "提交查询")

      > [!NOTE]  
      > 无法将批提交到交互式查询群集。  必须使用交互模式。

若要创建并运行 Hive 解决方案，请执行以下操作：

1. 在菜单栏中，导航到“文件” > “新建” > “项目...”。   

2. 在左窗格中，导航到“已安装” > “Azure Data Lake” > “HIVE (HDInsight)”。     

3. 在中间窗格中，选择“Hive 应用程序”。  输入属性，然后选择“确定”  。

    ![HDInsight Visual Studio 工具新建 Hive 项目的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png "从 Visual Studio 创建 Hive 应用程序")

4. 在“解决方案资源管理器”中，双击“Script.hql”将该脚本打开。  

### <a name="view-job-summary-and-output"></a>查看作业摘要和输出

作业摘要根据选择的是“批处理”还是“交互式”模式而略有不同。  

![作业摘要](./media/apache-hadoop-visual-studio-tools-get-started/jobSummary.png "Hive 作业摘要")

使用“刷新”按钮更新状态，直到作业状态更改为“已完成”。    

* 如需“批处理”模式下的作业详细信息，请选择底部的链接以查看“作业查询”、“作业输出”、“作业日志”或“Yarn 日志”。     

* 如需“交互式”模式下的作业详细信息，请查看“输出”和“HiveServer2 输出”选项卡。   

  ![作业详细信息](./media/apache-hadoop-visual-studio-tools-get-started/tabs.png "Hive 作业详细信息")

### <a name="view-job-graph"></a>查看作业图

目前，仅显示使用 Tez 作为执行引擎的 Hive 作业的作业图。  有关启用 Tez 的信息，请参阅[使用 HDInsight 中的 Apache Hive](hdinsight-use-hive.md)。  另请参阅[使用 Apache Tez 而不是 Map Reduce](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)。  

若要查看顶点中的所有运算符，请双击作业图的相应顶点。 也可通过指向特定运算符来查看该运算符的更多详细信息。

即使已将 Tez 指定为执行引擎，但如果未启动 Tez 应用程序，则也可能不会显示作业图。  可能的原因是作业不包含 DML 语句，或者在未启动 Tez 应用程序的情况下 DML 语句可以返回。 例如，`SELECT * FROM table1` 不会启动 Tez 应用程序。

![作业图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png "Hive 作业摘要")


### <a name="task-execution-detail"></a>任务执行详细信息

在作业图中，可以选择“任务执行详细信息”获取 Hive 作业的结构化和可视化信息。  也可以获取更多的作业详细信息。 如果出现性能问题，可以使用此视图来获取该问题的更多详细信息。 例如，可以了解每个任务的运行方式和详细信息（数据读取/写入、计划/开始/结束时间，等等）， 以便根据可视化信息优化作业配置或系统体系结构。

![Data Lake Visual Studio 工具“任务执行视图”窗口的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png "任务执行视图")


### <a name="view-hive-jobs"></a>查看 Hive 作业
可以查看作业查询、作业输出、作业日志和 Hive 作业的 Yarn 日志。

在最新版本的工具中，可以通过收集和显示 Yarn 日志来查看 Hive 作业的内容。 Yarn 日志有助于调查性能问题。 有关 HDInsight 如何收集 Yarn 日志的详细信息，请参阅[以编程方式访问 HDInsight 应用程序日志](../hdinsight-hadoop-access-yarn-app-logs.md)。

若要查看 Hive 作业，请执行以下操作：

1. 右键单击某个 HDInsight 群集，并选择“查看作业”。  此时会显示群集上运行的 Hive 作业的列表。  

2. 选择作业。 在“Hive 作业摘要”  窗口中，选择以下项目之一：
    - **作业查询**
    - **作业输出**
    - **作业日志**  
    - **Yarn 日志**

    ![HDInsight Visual Studio 工具“查看 Hive 作业”窗口的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png "查看 Hive 作业")


## <a name="run-apache-pig-scripts"></a>运行 Apache Pig 脚本

1. 在菜单栏中，导航到“文件” > “新建” > “项目...”。   

2. 在左窗格中，导航到“已安装” > “Azure Data Lake” > “Pig (HDInsight)”。     

3. 在中间窗格中选择“Pig 应用程序”。  输入属性，然后选择“确定”  。

4. 在“解决方案资源管理器”中，双击“Script.pig”打开脚本。  

## <a name="feedback-and-known-issues"></a>反馈和已知问题
* 解决了以 null 值开头的结果不会显示的问题。 如果受此问题困扰，请与支持团队联系。
* Visual Studio 创建的 HQL 脚本会根据用户的本地区域设置进行编码。 如果以二进制文件形式将脚本上传到群集，脚本将无法正常执行。

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用用于 Visual Studio 的 Data Lake 工具包从 Visual Studio 连接到 HDInsight 群集， 以及如何运行 Hive 查询。 有关详细信息，请参阅以下文章：

* [使用用于 Visual Studio 的 Data Lake 工具运行 Apache Hive 查询](apache-hadoop-use-hive-visual-studio.md)
* [在 HDInsight 中使用 Hadoop Hive](hdinsight-use-hive.md)
* [开始在 HDInsight 中使用 Apache Hadoop](apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中提交 Apache Hadoop 作业](submit-apache-hadoop-jobs-programmatically.md)
* [使用 HDInsight 中的 Apache Hadoop 分析 Twitter 数据](../hdinsight-analyze-twitter-data.md)

