---
title: Apache Hadoop & Visual Studio Data Lake 工具-Azure HDInsight
description: 了解如何安装针对 Visual Studio 的 Data Lake 工具并用其连接到 Azure HDInsight 中的 Apache Hadoop 群集，然后运行 Hive 查询。
keywords: hadoop 工具,hive 查询,visual studio,visual studio hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 4ad58bc2d61f063dce2c23f60a65dcbec48a2303
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79272781"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-apache-hive-queries"></a>使用针对 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Apache Hive 查询

了解如何使用适用于 Visual Studio 的 Microsoft Azure Data Lake 和流分析工具（也称为 Data Lake 工具）连接到[Azure HDInsight 中的 Apache Hadoop 群集](apache-hadoop-introduction.md)并提交 Hive 查询。  

有关使用 HDInsight 的详细信息，请参阅[hdinsight 入门](apache-hadoop-linux-tutorial-get-started.md)。  

有关连接到 Apache Storm 群集的详细信息，请参阅[使用C#适用于 Visual Studio 的 Data Lake 工具开发 Apache Storm 的拓扑](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。

可以使用用于 Visual Studio 的 Data Lake 工具访问 Azure Data Lake Analytics 和 HDInsight。 有关 Data Lake 工具的信息，请参阅[使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="prerequisites"></a>必备条件

若要完成本文并使用适用于 Visual Studio Data Lake 工具，需要以下项：

* 一个 Azure HDInsight 群集。 若要创建 HDInsight 群集，请参阅[在 Azure HDInsight 中使用 Apache Hadoop 入门](apache-hadoop-linux-tutorial-get-started.md)。 若要运行交互式 Apache Hive 查询，需使用 [HDInsight 交互式查询](../interactive-query/apache-interactive-query-get-started.md)群集。  

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)。 [Visual Studio 社区版](https://visualstudio.microsoft.com/vs/community/)是免费的。 此处所示的说明适用于[Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。

## <a name="install-data-lake-tools-for-visual-studio"></a>安装针对 Visual Studio 的 Data Lake 工具  

按照相应说明安装适用于你的 Visual Studio 版本的 Data Lake 工具：

- 对于 Visual Studio 2017 或 Visual Studio 2019：

    在 Visual Studio 安装过程中，请确保包括**Azure 开发**工作负荷或**数据存储和处理**工作负荷。  

    对于现有的 Visual Studio 安装，请转到 IDE 菜单栏，然后选择 "**工具**" > 获取用于打开 Visual Studio 安装程序的**工具和功能**。 在 "**工作负荷**" 选项卡中，选择至少**Azure 开发**工作负荷（在**Web & 云**下）或**数据存储和处理**工作负荷（在**其他工具集**下）。

  ![工作负荷选择，Visual Studio 安装程序](./media/apache-hadoop-visual-studio-tools-get-started/vs-installation.png)

- 对于 Visual Studio 2015：

    [下载 Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)。 请选择与 Visual Studio 版本匹配的 Data Lake 工具版本。

## <a name="update-data-lake-tools-for-visual-studio"></a>更新 Visual Studio Data Lake 工具  

接下来，请确保将 Data Lake 工具更新到最新版本。

1. 打开 Visual Studio。

2. 在 "**开始**" 窗口中，选择 "**继续而不运行代码**"。

3. 在 Visual Studio IDE 菜单栏中，选择 "**扩展**" > "**管理扩展**"。

4. 在 "**管理扩展**" 对话框中，展开 "**更新**" 节点。

5. 如果可用更新列表包括**Azure Data Lake 和流分析工具**，请将其选中。 然后选择其 "**更新**" 按钮。 "**下载并安装**" 对话框出现后消失后，Visual Studio 会将**Azure Data Lake 和流分析工具**扩展添加到更新计划。

6. 关闭所有 Visual Studio 窗口。 此时将显示 " **VSIX 安装程序**" 对话框。

7. 选择 "**许可证**" 以阅读许可条款，然后选择 "**关闭**" 以返回到 " **VSIX 安装程序**" 对话框。

8. 选择“修改”。 扩展更新的安装开始。 经过一段时间后，对话框将发生变化，显示已完成修改。 选择 "**关闭**"，然后重新启动 Visual Studio 以完成安装。

> [!NOTE]  
> 只能使用 Data Lake 工具 2.3.0.0 或更高版本连接到交互式查询群集，然后运行交互式 Hive 查询。

## <a name="connect-to-azure-subscriptions"></a>连接到 Azure 订阅

可以使用用于 Visual Studio Data Lake 工具连接到 HDInsight 群集，执行一些基本的管理操作，并运行 Hive 查询。

> [!NOTE]  
> 有关连接到通用 Hadoop 群集的信息，请参阅[如何使用 Visual Studio 编写和提交 Hive 查询](https://blogs.msdn.microsoft.com/xiaoyong/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio/)。

### <a name="connect-to-an-azure-subscription"></a>连接到 Azure 订阅

若要连接到 Azure 订阅，请执行以下操作：

1. 打开 Visual Studio。

2. 在 "**开始**" 窗口中，选择 "**继续而不运行代码**"。

3. 在 IDE 菜单栏中，选择 "**查看** > **服务器资源管理器**"。

4. 在**服务器资源管理器**中，右键单击 " **Azure**"，选择 "**连接到 Microsoft Azure 订阅**"，并完成身份验证过程。 在**服务器资源管理器**中，展开 " **Azure** > **HDInsight** " 以查看现有 HDInsight 群集的列表。

5. 如果没有任何群集，请使用 Azure 门户、Azure PowerShell 或 HDInsight SDK 创建一个。 有关详细信息，请参阅[在 HDInsight 中设置群集](../hdinsight-hadoop-provision-linux-clusters.md)。

   ![HDInsight 群集列表，服务器资源管理器，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-server-explorer.png)

6. 展开 HDInsight 群集。 群集包含**Hive 数据库**的节点、默认存储帐户、任何其他链接的存储帐户和**Hadoop 服务日志**。 可以进一步展开条目。

连接到 Azure 订阅后，可以执行以下任务。

### <a name="connect-to-azure-from-visual-studio"></a>从 Visual Studio 连接到 Azure

若要从 Visual Studio 连接到 Azure 门户，请执行以下操作：

1. 在**服务器资源管理器**中，展开 " **Azure** > **HDInsight** " 并选择群集。

2. 右键单击 HDInsight 群集，然后**在 Azure 门户中选择 "管理群集**"。

### <a name="offer-questions-and-feedback-from-visual-studio"></a>通过 Visual Studio 提供问题和反馈

若要从 Visual Studio 提出问题并/或提供反馈：

1. 在服务器资源管理器中，选择 " **Azure** > **HDInsight**"。

2. 右键单击 " **HDInsight** "，选择 " **MSDN 论坛**" 提出问题，或**提供反馈**以提供反馈。

## <a name="link-to-or-edit-a-cluster"></a>链接到或编辑群集

> [!NOTE]
> 当前只能链接到的 HDInsight 群集类型为 Hive 类型。

若要链接 HDInsight 群集：

1. 右键单击 " **hdinsight**"，然后选择 "**链接 hdinsight 群集**" 以显示 "**链接 hdinsight 群集**" 对话框。

2. 以*https\://\<群集&nbsp;> 名称*格式输入**连接 Url** 。 当你切换到另一个字段时，**群集名称**会自动填充你的 URL 的 "群集名称" 部分。 然后输入**用户名**和**密码**，然后选择 "**下一步**"。

    ![链接群集，HDInsight，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-dialog.png)

3. 选择“完成”。 如果群集链接成功，则群集会在**HDInsight**节点下列出。

若要更新链接群集，请右键单击该群集，然后选择 "**编辑**"。 然后，你可以更新群集信息。

![编辑链接的群集，HDInsight，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-link-cluster-update.png)

## <a name="explore-linked-resources"></a>浏览链接的资源
在“服务器资源管理器”中，可以看到默认存储帐户和任何链接的存储帐户。 如果展开默认存储帐户，则可以看到存储帐户中的容器。 默认存储帐户和默认容器将处于标记状态。

![适用于 Visual Studio 的 Data Lake 工具服务器资源管理器中的链接资源](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-linked-resources.png)

右键单击容器，然后选择 "**查看容器**" 以查看容器的内容。 打开容器后，可使用工具栏按钮来**刷新**内容列表、**上传 blob**、**删除所选 blob**、**打开 Blob**，以及下载（**另存为**）选定的 blob。

![容器列表和 blob 操作，HDInsight 群集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-blob-operations.png)

## <a name="run-interactive-apache-hive-queries"></a>运行交互式 Apache Hive 查询
[Apache Hive](https://hive.apache.org) 是构建于 Hadoop 之上的数据仓库基础结构。 Hive 用于数据汇总、查询和分析。 可以使用用于 Visual Studio 的 Data Lake 工具从 Visual Studio 运行 Hive 查询。 有关 Hive 的详细信息，请参阅[Azure HDInsight 上的 Apache Hive 和 HiveQL 是什么？](hdinsight-use-hive.md)。

[Azure HDInsight 中的交互式查询](../interactive-query/apache-interactive-query-get-started.md)在 Apache Hive 2.1 的 LLAP 中使用[Hive](https://cwiki.apache.org/confluence/display/Hive/LLAP) 。 交互式查询为大型存储数据集上的复杂数据仓库样式查询带来了交互性。 与传统的 Hive 批处理作业相比，在交互式查询上运行 Hive 查询的速度要快得多。 

> [!NOTE]  
> 仅当连接到 [HDInsight 交互式查询](../interactive-query/apache-interactive-query-get-started.md)群集时，才能运行交互式 Hive 查询。

也可使用用于 Visual Studio 的 Data Lake 工具来查看 Hive 作业中的内容。 用于 Visual Studio 的 Data Lake 工具可收集和显示某些 Hive 作业的 Yarn 日志。

在**服务器资源管理器**中，选择 " **Azure** > **HDInsight** " 并选择群集。  此节点是**服务器资源管理器**中要遵循的部分的起始点。

### <a name="view-hivesampletable"></a>查看 hivesampletable

所有 HDInsight 群集都有一个名为 `hivesampletable`的默认示例 Hive 表。  

在群集中，选择 " **Hive 数据库**" > **默认** > **hivesampletable**。

- 查看 `hivesampletable` 架构：

    展开**hivesampletable**。 显示 `hivesampletable` 列的名称和数据类型。

- 查看 `hivesampletable` 数据：

    右键单击**hivesampletable**，然后选择 "**查看前100行**"。 100结果的列表显示在**Hive 表： hivesampletable**窗口中。 此操作等效于使用 Hive ODBC 驱动程序运行以下 Hive 查询：

    `SELECT * FROM hivesampletable LIMIT 100`

    您可以通过更改**行数**自定义行计数;可以从下拉列表中选择50、100、200或1000行。

### <a name="create-hive-tables"></a>创建 Hive 表
若要创建 Hive 表，可以使用 GUI，也可以使用 Hive 查询。 有关使用 Hive 查询的信息，请参阅[创建和运行 hive 查询](#create-and-run-hive-queries)。

1. 在群集中，选择 " **Hive 数据库**" > **默认值**。

2. 右键单击 "**默认**"，然后选择 "**创建表**"。

3. 配置该表。

4. 选择 "**创建表**" 按钮，以提交创建新 Hive 表的作业。

    ![创建表窗口，Hive，HDInsight 群集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-create-hive-table.png)

### <a name="create-and-run-hive-queries"></a>创建并运行 Hive 查询
可以使用两个选项来创建并运行 Hive 查询：

* 创建临时查询
* 创建 Hive 应用程序

#### <a name="create-an-ad-hoc-query"></a>创建即席查询

若要创建并运行即席查询，请执行以下操作：

1. 右键单击要运行查询的群集，然后选择 "**编写 Hive 查询**"。  

2. 输入 Hive 查询。

    Hive 编辑器支持 IntelliSense。 用于 Visual Studio 的 Data Lake 工具支持在编辑 Hive 脚本时加载远程元数据。 例如，如果键入 `SELECT * FROM`，IntelliSense 将列出所有建议的表名称。 在指定表名称后，IntelliSense 会列出列名称。 这些工具支持大多数 Hive DML 语句、子查询和内置 UDF。

    ![IntelliSense 示例1，Hive 即席查询，HDInsight 群集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-table-names.png)

    ![IntelliSense 示例2，Hive 即席查询，HDInsight 群集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-intellisense-column-names.png)

    > [!NOTE]  
    > IntelliSense 只建议 HDInsight 工具栏中所选群集的元数据。

    下面是可以使用的示例查询：

    ```sql
    SELECT devicemodel, COUNT(devicemodel) AS deviceCount
    FROM hivesampletable
    GROUP BY devicemodel
    ORDER BY devicemodel
    ```

3. 选择执行模式：

    * **交互式**  

        在第一个下拉列表中，选择 "**交互式**"，然后选择 "**执行**"。

        ![交互模式，Hive 即席查询，HDInsight 群集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-execute.png)  

    * **批处理**  

        在第一个下拉列表中，选择 "**批处理**"，然后选择 "**提交**" （或选择 "**提交**" 旁边的下拉图标，然后选择 "**高级**"）。

        ![批处理模式，Hive 即席查询，HDInsight 群集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-query-batch.png)

        如果选择 "高级提交" 选项，则会出现 "**提交脚本**" 对话框。 为脚本配置 "**作业名称**"、"**参数**"、"**其他配置**" 和 "**状态目录**"。

        !["提交脚本" 对话框，Hive 即席查询，HDInsight 群集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-submit-jobs-advanced.png)

      > [!NOTE]  
      > 无法将批提交到交互式查询群集。  必须使用交互模式。

#### <a name="create-a-hive-application"></a>创建 Hive 应用程序

若要创建并运行 Hive 解决方案，请执行以下操作：

1. 从菜单栏中，选择 "**文件**" > **新建** > **项目**"。

2. 在 "**新建项目**" 窗口中，选择 "搜索" 框并键入**Hive**。 然后选择 " **Hive 应用程序**" 并选择 "**下一步**"。

3. 在 "**配置新项目**" 窗口中，输入**项目名称**，选择或创建项目**位置**，然后选择 "**创建**"。

    ![新 Hive 应用程序，配置新的项目窗口，HDInsight Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-new-hive-project.png)

4. 在“解决方案资源管理器”中，双击“Script.hql”将该脚本打开。

### <a name="view-job-summary-and-output"></a>查看作业摘要和输出

作业摘要在**批处理**模式和**交互**模式之间略有不同。

![Hive 作业摘要窗口，批处理和交互模式，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-summary.png)

使用**刷新**图标更新状态，直到作业状态更改为 "**已完成**"。  

* 对于**批处理**模式下的作业详细信息，请选择底部的链接以查看**作业查询**、**作业输出**、**作业日志**或**查看 Yarn 日志**。

* 对于**交互式**模式下的作业详细信息，请参阅**Output**和**HiveServer2 output**窗格。

    ![Hive 交互作业输出，HDInsight 群集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-job-details.png)

### <a name="view-job-graph"></a>查看作业图形

目前，作业图形仅适用于使用 Tez 作为执行引擎的 Hive 作业。  有关启用 Tez 的信息，请参阅[Azure HDInsight 上 Apache Hive 和 HiveQL 是什么？](hdinsight-use-hive.md)。  另请参阅，[使用 Apache Tez 而不是 Map 化简](../hdinsight-hadoop-optimize-hive-query.md#use-apache-tez-instead-of-map-reduce)。  

若要查看顶点中的所有运算符，请双击作业图的顶点。 也可通过指向特定运算符来查看该运算符的更多详细信息。

即使将 Tez 指定为执行引擎，如果未启动 Tez 应用程序，也可能不会显示作业图形。  出现这种情况的原因可能是作业不包含 DML 语句，或者 DML 语句可以返回而不启动 Tez 应用程序。 例如，`SELECT * FROM table1` 不会启动 Tez 应用程序。

![Apache Hive 作业图形，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-fast-path-hive-execution.png)

### <a name="view-task-execution-detail"></a>查看任务执行详细信息

在作业图中，可以选择**任务执行详细**信息，以获取 Hive 作业的结构化和可视化信息。 你还可以获取更多作业详细信息。 如果出现性能问题，可以使用此视图来获取该问题的更多详细信息。 例如，您可以检索有关每个任务的运行方式的信息，以及有关每个任务的详细信息（数据读取/写入、计划/开始/结束时间等）。 以便根据可视化信息优化作业配置或系统体系结构。

![任务执行视图窗口，Data Lake Visual Studio Tools](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-task-execution-view.png)

### <a name="view-hive-jobs"></a>查看 Hive 作业

可以查看作业查询、作业输出、作业日志和 Hive 作业的 Yarn 日志。

在最新版本的工具中，可以通过收集和显示 Yarn 日志来查看 Hive 作业的内容。 Yarn 日志有助于调查性能问题。 有关 HDInsight 如何收集 Yarn 日志的详细信息，请参阅[Access APACHE HADOOP Yarn 应用程序日志](../hdinsight-hadoop-access-yarn-app-logs-linux.md)。

若要查看 Hive 作业，请执行以下操作：

1. 右键单击 HDInsight 群集，然后选择 "**查看作业**"。

    ![查看作业，Apache Hive，HDInsight 群集，Visual Studio](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight-visual-studio-tools-view-hive-jobs.png)

    此时会显示群集上运行的 Hive 作业的列表。  

2. 选择作业。 在 " **Hive 作业摘要**" 窗口中，选择以下链接之一：
    - **作业查询**
    - **作业输出**
    - **作业日志**  
    - **Yarn 日志**

## <a name="run-apache-pig-scripts"></a>运行 Apache Pig 脚本

1. 从菜单栏中，选择 "**文件**" > **新建** > **项目**"。

2. 在 "**开始**" 窗口中，选择 "搜索" 框并输入**Pig**。 然后选择 " **Pig 应用程序**" 并选择 "**下一步**"。

3. 在 "**配置新项目**" 窗口中，输入**项目名称**，然后选择或创建项目的**位置**。 然后选择“创建”。

4. 在 IDE**解决方案资源管理器**窗格中，双击 " **pig** " 以打开脚本。

## <a name="feedback-and-known-issues"></a>反馈和已知问题

* 解决了以 null 值开头的结果不会显示的问题。 如果受此问题困扰，请与支持团队联系。

* Visual Studio 创建的 HQL 脚本会根据用户的本地区域设置进行编码。 如果以二进制文件形式将脚本上传到群集，脚本将无法正常执行。

## <a name="next-steps"></a>后续步骤

本文介绍了如何使用用于 Visual Studio 的 Data Lake 工具包从 Visual Studio 连接到 HDInsight 群集， 以及如何运行 Hive 查询。 有关详细信息，请参阅以下文章：

* [使用针对 Visual Studio 的 Data Lake 工具运行 Apache Hive 查询](apache-hadoop-use-hive-visual-studio.md)
* [Azure HDInsight Apache Hive 和 HiveQL 是什么？](hdinsight-use-hive.md)
* [创建 Apache Hadoop 群集-模板](apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中提交 Apache Hadoop 作业](submit-apache-hadoop-jobs-programmatically.md)
* [使用 HDInsight 上的 Apache Hive 和 Apache Hadoop 分析 Twitter 数据](../hdinsight-analyze-twitter-data-linux.md)
