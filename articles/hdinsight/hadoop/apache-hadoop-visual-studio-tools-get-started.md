---
title: 使用用于 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight | Microsoft Docs
description: 了解如何安装用于 Visual Studio 的 Data Lake 工具并用其连接到 Azure HDInsight 中的 Hadoop 群集，然后运行 Hive 查询。
keywords: hadoop 工具,hive 查询,visual studio,visual studio hadoop
services: HDInsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: ce9c572a-1e98-46bf-9581-13a9767f1fa5
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 1707a681315fbb53f21df95664188463968bb855
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202469"
---
# <a name="use-data-lake-tools-for-visual-studio-to-connect-to-azure-hdinsight-and-run-hive-queries"></a>使用用于 Visual Studio 的 Data Lake 工具连接到 Azure HDInsight 并运行 Hive 查询

了解如何使用用于 Visual Studio 的 Data Lake 工具（也称用于 Visual Studio 的 Azure Data Lake 和流分析工具）连接到 [Azure HDInsight](../hdinsight-hadoop-introduction.md) 中的 Hadoop 群集并提交 Hive 查询。 

有关使用 HDInsight 的详细信息，请参阅 [HDInsight 简介](../hdinsight-hadoop-introduction.md)和 [HDInsight 入门](apache-hadoop-linux-tutorial-get-started.md)。 

有关连接到 Storm 群集的详细信息，请参阅[使用 Visual Studio 为 Apache Storm on HDInsight 开发 C# 拓扑](../storm/apache-storm-develop-csharp-visual-studio-topology.md)。

可以使用用于 Visual Studio 的 Data Lake 工具访问 Azure Data Lake Analytics 和 HDInsight。 有关 Data Lake 工具的信息，请参阅[使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](../../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="prerequisites"></a>先决条件

若要完成本教程并使用用于 Visual Studio 的 Data Lake 工具，需要准备以下项目：

* 一个 Azure HDInsight 群集。 若要创建 HDInsight 群集，请参阅[在 Azure HDInsight 中使用 Hadoop 入门](apache-hadoop-linux-tutorial-get-started.md)。 若要运行交互式 Hive 查询，需使用 [HDInsight 交互式查询](../interactive-query/apache-interactive-query-get-started.md)群集。
* 安装了 Visual Studio 2017、2015 或 2013 的计算机。
    
    > [!NOTE]
    > 目前，用于 Visual Studio 的 Data Lake 工具仅提供英文版。
    > 
    > 

## <a name="install-or-update-data-lake-tools-for-visual-studio"></a>安装或更新用于 Visual Studio 的 Data Lake 工具

### <a name="install-data-lake-tools"></a>安装 Data Lake 工具

默认为 Visual Studio 2017 安装 Data Lake 工具。 对于较早的 Visual Studio 版本，可以使用 [Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)来安装 Data Lake 工具。 请选择与 Visual Studio 版本匹配的 Data Lake 工具版本。 

### <a name="install-visual-studio"></a>安装 Visual Studio

如果尚未安装 Visual Studio，请使用 [Web 平台安装程序](https://www.microsoft.com/web/downloads/platform.aspx)安装最新版本的 Visual Studio Community 和 Azure SDK：

![用于 Visual Studio 的 Data Lake 工具 Web 平台安装程序的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.wpi.png "使用 Web 平台安装程序安装用于 Visual Studio 的 Data Lake 工具")

### <a name="update-the-tools"></a>更新工具

1. 打开 Visual Studio。
2. 在“工具”菜单上，选择“扩展和更新”。
3. 展开“更新”，然后选择“Azure Data Lake 和流分析工具”（如果已安装）。

> [!NOTE]
>
> 只能使用 Data Lake 工具 2.3.0.0 或更高版本连接到交互式查询群集，然后运行交互式 Hive 查询。

## <a name="connect-to-azure-subscriptions"></a>连接到 Azure 订阅
可以使用用于 Visual Studio 的 Data Lake 工具连接到 HDInsight 群集，执行一些基本管理操作，以及运行 Hive 查询。

> [!NOTE]
> 有关连接到常规 Hadoop 群集的信息，请参阅 [Write and submit Hive queries by using Visual Studio](http://blogs.msdn.com/b/xiaoyong/archive/2015/05/04/how-to-write-and-submit-hive-queries-using-visual-studio.aspx)（使用 Visual Studio 编写和提交 Hive 查询）。
> 
> 

若要连接到 Azure 订阅，请执行以下操作：

1. 打开 Visual Studio。
2. 在“视图”菜单中，选择“服务器资源管理器”。
3. 在“服务器资源管理器”中展开“Azure”，然后展开“HDInsight”。
   
   > [!NOTE]
   > 此时会打开“HDInsight 任务列表”窗口。 如果看不到该窗口，请在“视图”菜单中选择“其他窗口”，然后选择“HDInsight 任务列表窗口”。  
   > 
   > 
4. 输入 Azure 订阅凭据，然后选择“登录”。 仅当尚未从此计算机上的 Visual Studio 连接到 Azure 订阅时，才需要身份验证。
5. 在“服务器资源管理器”中，会显示现有 HDInsight 群集的列表。 如果没有任何群集，可以使用 Azure 门户、Azure PowerShell 或 HDInsight SDK 创建一个群集。 有关详细信息，请参阅[创建 HDInsight 群集](../hdinsight-hadoop-provision-linux-clusters.md)。
   
   ![服务器资源管理器中的用于 Visual Studio 的 Data Lake 工具群集列表的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.server.explorer.png "服务器资源管理器中的用于 Visual Studio 的 Data Lake 工具群集列表")
6. 展开 HDInsight 群集。 此时会显示“Hive 数据库”、默认存储帐户、链接的存储帐户，以及“Hadoop 服务日志”。 可以进一步展开条目。

连接到 Azure 订阅后，可以执行以下任务。

若要从 Visual Studio 连接到 Azure 门户，请执行以下操作：

1. 在“服务器资源管理器”中，选择“Azure” > “HDInsight”。
2. 右键单击 HDInsight 群集，然后选择“在 Azure 门户中管理群集”。

若要通过 Visual Studio 提出问题并提供反馈，请执行以下操作：

1. 在“工具”菜单中选择“HDInsight”。
2. 若要提问问题，请选择“MSDN 论坛”。 若要提供反馈，请选择“提供反馈”。

## <a name="explore-linked-resources"></a>浏览链接的资源
在“服务器资源管理器”中，可以看到默认存储帐户和任何链接的存储帐户。 如果展开默认存储帐户，则可以看到存储帐户中的容器。 默认存储帐户和默认容器将处于标记状态。 右键单击任何容器即可查看容器内容。

![服务器资源管理器中用于 Visual Studio 的 Data Lake 工具列表列表链接资源的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.linked.resources.png "列表链接资源")

打开一个容器后，可以使用以下按钮来上传、删除和下载 Blob：

![服务器资源管理器中用于 Visual Studio 的 Data Lake 工具 Blob 操作的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.blob.operations.png "在服务器资源管理器中上传、删除和下载 Blob")

## <a name="run-interactive-hive-queries"></a>运行交互式 Hive 查询
[Apache Hive](http://hive.apache.org) 是构建于 Hadoop 之上的数据仓库基础结构。 Hive 用于数据汇总、查询和分析。 可以使用用于 Visual Studio 的 Data Lake 工具从 Visual Studio 运行 Hive 查询。 有关 Hive 的详细信息，请参阅[将 Hive 与 HDInsight 配合使用](hdinsight-use-hive.md)。

[交互式查询](../interactive-query/apache-interactive-query-get-started.md)使用 Apache Hive 2.1 中的 [Hive on LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP)。 交互式查询为在存储的大型数据集上进行的复杂数据仓库样式查询带来了交互性。 相对于传统的 Hive 批处理作业，在交互式查询上运行 Hive 查询速度要快得多。 有关详细信息，请参阅[运行 Hive 批处理作业](#run-hive-batch-jobs)。

> [!NOTE]
>
> 仅当连接到 [HDInsight 交互式查询](../interactive-query/apache-interactive-query-get-started.md)群集时，才能运行交互式 Hive 查询。

也可使用用于 Visual Studio 的 Data Lake 工具来查看 Hive 作业中的内容。 用于 Visual Studio 的 Data Lake 工具可收集和显示某些 Hive 作业的 Yarn 日志。

### <a name="view-hivesampletable"></a>查看 **hivesampletable**
所有 HDInsight 群集都有一个名为 hivesampletable 的默认示例 Hive 表。 此 Hive 表定义如何列出 Hive 表、如何查看表架构，以及如何列出 Hive 表中的行。

若要列出 Hive 表和查看 Hive 表架构，请执行以下操作：

1. 若要查看表架构，请在“服务器资源管理器”中选择“Azure” > “HDInsight”。 选择群集，然后选择“Hive 数据库” > “默认设置” > “hivesampletable”。
2. 右键单击“hivesampletable”，并单击“查看前 100 行”列出行。 这相当于使用 Hive ODBC 驱动程序运行以下 Hive 查询：
   
     `SELECT * FROM hivesampletable LIMIT 100`
   
   可以自定义行计数。
   
   ![HDInsight Hive Visual Studio 架构查询的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.hive.schema.png "Hive 查询结果")

### <a name="create-hive-tables"></a>创建 Hive 表
若要创建 Hive 表，可以使用 GUI，也可以使用 Hive 查询。 有关使用 Hive 查询的信息，请参阅 [运行 Hive 查询](#run.queries)。

若要创建 Hive 表，请执行以下操作：

1. 在“服务器资源管理器”中，选择“Azure” > “HDInsight 群集”。 选择 HDInsight 群集，然后选择“Hive 数据库”。
2. 右键单击“默认设置”，然后选择“创建表”。
3. 配置该表。  
4. 选择“创建表”，以便提交创建新 Hive 表的作业。
   
    ![HDInsight Visual Studio 工具“创建表”窗口的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.create.hive.table.png "创建 Hive 表")

### <a name="run.queries"></a>验证和运行 Hive 查询
可以使用两个选项来创建并运行 Hive 查询：

* 创建临时查询
* 创建 Hive 应用程序

若要创建、验证和运行即席查询，请执行以下操作：

1. 在“服务器资源管理器”中，选择“Azure” > “HDInsight 群集”。
2. 右键单击要运行查询的群集，然后选择“编写 Hive 查询”。  
3. 输入 Hive 查询。 

    Hive 编辑器支持 IntelliSense。 用于 Visual Studio 的 Data Lake 工具支持在编辑 Hive 脚本时加载远程元数据。 例如，如果键入“SELECT * FROM”，IntelliSense 会列出所有建议的表名称。 在指定表名称后，IntelliSense 会列出列名称。 这些工具支持大多数 Hive DML 语句、子查询和内置 UDF。
   
    ![HDInsight Visual Studio Tools IntelliSense 示例 1 的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.table.names.png "U-SQL IntelliSense")
   
    ![HDInsight Visual Studio Tools IntelliSense 示例 2 的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.intellisense.column.names.png "U-SQL IntelliSense")
   
   > [!NOTE]
   > IntelliSense 只建议 HDInsight 工具栏中所选群集的元数据。
   > 
   
4. （可选）若要检查脚本语法错误，请选择“验证脚本”。
   
    ![用于 Visual Studio 的 Data Lake 工具本地验证的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.validate.hive.script.png "验证脚本")
5. 选择“提交”或“提交(高级)”。 如果选择高级提交选项，请为脚本配置“作业名称”、“参数”、“其他配置”和“状态目录”：
   
    ![HDInsight Hadoop Hive 查询的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.submit.jobs.advanced.png "提交查询")
   
    在提交作业后，会显示“Hive 作业摘要”窗口。
   
    ![HDInsight Hadoop Hive 查询摘要的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.run.hive.job.summary.png "Hive 作业摘要")
6. 使用“刷新”按钮更新状态，直到作业状态更改为“已完成”。
7. 选择底部的链接可查看“作业查询”、“作业输出”、“作业日志”或“Yarn 日志”。

若要创建并运行 Hive 解决方案，请执行以下操作：

1. 在“文件”菜单中，选择“新建”，然后选择“项目”。
2. 在左窗格中，选择“HDInsight”。 在中间窗格中，选择“Hive 应用程序”。 输入属性，然后选择“确定”。
   
    ![HDInsight Visual Studio 工具新建 Hive 项目的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.new.hive.project.png "从 Visual Studio 创建 Hive 应用程序")
3. 在“解决方案资源管理器”中，双击“Script.hql”将该脚本打开。
4. 若要验证 Hive 脚本，请选择“验证脚本”按钮。 也可在 Hive 编辑器中右键单击脚本，然后从上下文菜单中选择“验证脚本”。

### <a name="view-hive-jobs"></a>查看 Hive 作业
可以查看作业查询、作业输出、作业日志和 Hive 作业的 Yarn 日志。 有关详细信息，请查看前面的屏幕截图。

在最新版本的工具中，可以通过收集和显示 Yarn 日志来查看 Hive 作业的内容。 Yarn 日志有助于调查性能问题。 有关 HDInsight 如何收集 Yarn 日志的详细信息，请参阅[以编程方式访问 HDInsight 应用程序日志](../hdinsight-hadoop-access-yarn-app-logs.md)。

若要查看 Hive 作业，请执行以下操作：

1. 在“服务器资源管理器”中展开“Azure”，然后展开“HDInsight”。
2. 右键单击 HDInsight 群集，然后选择“查看作业”。 此时会显示群集上运行的 Hive 作业的列表。  
3. 选择作业。 在“Hive 作业摘要”窗口中，选择以下项目之一：
    - **作业查询**
    - **作业输出**
    - **作业日志**  
    - **Yarn 日志**
   
    ![HDInsight Visual Studio 工具“查看 Hive 作业”窗口的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.view.hive.jobs.png "查看 Hive 作业")

### <a name="faster-path-hive-execution-via-hiveserver2"></a>通过 HiveServer2 进行的更快速路径 Hive 执行
> [!NOTE]
> 此功能仅适用于 HDInsight 3.2 或更高版本中的群集。
 
用于 Visual Studio 的 Data Lake 工具用于通过 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)（也称 Templeton）提交 Hive 作业。 用于提交 Hive 作业的此方法返回作业详细信息和错误信息所需的时间很长。

为了解决此性能问题，用于 Visual Studio 的 Data Lake 工具可以绕过 RDP/SSH，通过 HiveServer2 直接在群集中执行 Hive 作业。

使用此方法时，除了提升性能，还可以在 Apache Tez 图形和任务详细信息中查看 Hive。

在 HDInsight 3.2 或更高版本的群集中，会显示“通过 HiveServer2 执行”按钮：

![用于 Visual Studio 的 Data Lake 工具“通过 HiveServer2 执行”选项的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.execute.via.hiveserver2.png "通过 HiveServer2 执行 Hive 查询")

也可实时查看流式传输回来的日志。 如果 Hive 查询在 Tez 中执行，还可查看作业图形。

![用于 Visual Studio 的 Data Lake 工具通过 HiveServer2 进行的更快速路径 Hive 执行的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.fast.path.hive.execution.png "查看作业图形")

### <a name="execute-queries-via-hiveserver2-vs-submit-queries-via-webhcat"></a>通过 HiveServer2 执行查询与通过 WebHCat 提交查询的比较

虽然通过 HiveServer2 执行查询有许多性能优势，但此方法仍有一些限制。 某些限制导致此方法不适用于生产。 

下表显示了通过 HiveServer2 执行查询与通过 WebHCat 提交查询的差异：

|  | 通过 HiveServer2 执行 | 通过 WebHCat 提交 |
| --- | --- | --- |
| 执行查询 |消除了 WebHCat 中的开销（WebHCat 启动名为 TempletonControllerJob 的 MapReduce 作业）。 |如果通过 WebHCat 执行查询，WebHCat 会启动一个 MapReduce 作业，从而加重延迟。 |
| 向后流式传输日志 |近实时。 |仅当作业完成时才提供作业执行日志。 |
| 查看作业历史记录 |如果通过 HiveServer2 执行查询，则不保留查询的作业历史记录（作业日志、作业输出）。 可以在 Yarn UI 中查看应用程序的有限信息。 |如果通过 WebHCat 执行查询，则保留查询的作业历史记录（作业日志、作业输出）。 可以通过 Visual Studio、HDInsight SDK 或 PowerShell 查看作业历史记录。 |
| 关闭窗口 |通过 HiveServer2 执行是同步的。 如果关闭窗口，则会取消查询的执行。 |通过 WebHCat 提交是异步的。 可以通过 WebHCat 提交查询，然后关闭 Visual Studio。 随时可以回来查看结果。 |

### <a name="tez-hive-job-performance-graph"></a>Tez Hive 作业性能图
在用于 Visual Studio 的 Data Lake 工具中，可以看到 Tez 执行引擎运行的 Hive 作业的性能图。 有关启用 Tez 的信息，请参阅[使用 HDInsight 中的 Hive](hdinsight-use-hive.md)。 

提交 Visual Studio 中的 Hive 作业后，Visual Studio 会在作业完成时显示图形。 可能需要选择“刷新”按钮来查看最新的作业状态。

> [!NOTE]
> 此功能仅适用于 HDInsight 3.2.4.593 或更高版本中的群集。 此功能只能在已完成的作业上使用。 还必须通过 WebHCat 提交作业才能使用此功能。 通过 HiveServer2 执行查询时，会显示下图： 
> 
> ![Hadoop Hive Tez 性能图的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.hive.tez.performance.graph.png "作业状态")

此版中添加了“Hive 运算符”视图，可以更好地了解 Hive 查询。 若要查看顶点中的所有运算符，请双击作业图的相应顶点。 也可通过指向特定运算符来查看该运算符的更多详细信息。

### <a name="task-execution-view-for-hive-on-tez-jobs"></a>适用于Hive on Tez 作业的任务执行视图
可以通过适用于 Hive on Tez 作业的任务执行视图获取 Hive 作业的结构化和可视化信息。 也可以获取更多的作业详细信息。 如果出现性能问题，可以使用此视图来获取该问题的更多详细信息。 例如，可以了解每个任务的运行方式和详细信息（数据读取/写入、计划/开始/结束时间，等等）， 以便根据可视化信息优化作业配置或系统体系结构。

![Data Lake Visual Studio 工具“任务执行视图”窗口的屏幕截图](./media/apache-hadoop-visual-studio-tools-get-started/hdinsight.visual.studio.tools.task.execution.view.png "任务执行视图")

## <a name="run-hive-batch-jobs"></a>运行 Hive 批处理作业
针对 HDInsight 群集测试 Hive 脚本可能很费时，交互式查询群集例外。 此过程可能至少需要数分钟的时间。 用于 Visual Studio 的 Data Lake 工具可以在本地验证 Hive 脚本，无需连接到活动群集。 有关运行交互式查询的详细信息，请参阅[运行交互式 Hive 查询](#run-interactive-hive-queries)。

可以通过用于 Visual Studio 的 Data Lake 工具收集和显示特定 Hive 作业的 Yarn 日志，以便查看 Hive 作业中的内容。

若要详细了解如何运行 Hive 批处理作业，请参阅[运行交互式 Hive 查询](#run-interactive-hive-queries)。 该部分的信息适用于运行那些运行时间较长的 Hive 批处理作业。

## <a name="run-pig-scripts"></a>运行 Pig 脚本
可以使用用于 Visual Studio 的 Data Lake 工具创建 Pig 脚本并将其提交到 HDInsight 群集。 首先，通过模板创建 Pig 项目。 然后，将脚本提交到 HDInsight 群集。

## <a name="feedback-and-known-issues"></a>反馈和已知问题
* 目前 HiveServer2 结果以纯文本格式显示，这不是很理想。 Microsoft 正在努力解决此问题。
* 解决了以 null 值开头的结果不会显示的问题。 如果受此问题困扰，请与支持团队联系。
* Visual Studio 创建的 HQL 脚本会根据用户的本地区域设置进行编码。 如果以二进制文件形式将脚本上传到群集，脚本将无法正常执行。

## <a name="next-steps"></a>后续步骤
本文介绍了如何使用用于 Visual Studio 的 Data Lake 工具包从 Visual Studio 连接到 HDInsight 群集， 以及如何运行 Hive 查询。 有关详细信息，请参阅以下文章：

* [在 HDInsight 中使用 Hadoop Hive](hdinsight-use-hive.md)
* [使用 HDInsight 中的 Hadoop 入门](apache-hadoop-linux-tutorial-get-started.md)
* [在 HDInsight 中提交 Hadoop 作业](submit-apache-hadoop-jobs-programmatically.md)
* [使用 HDInsight 中的 Hadoop 分析 Twitter 数据](../hdinsight-analyze-twitter-data.md)

