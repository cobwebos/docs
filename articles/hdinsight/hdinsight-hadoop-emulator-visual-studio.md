---
title: 将针对 Visual Studio 的 Data Lake 工具与 Hortonworks 沙盒配合使用 — Azure HDInsight | Microsoft Docs
description: 了解如何将针对 Visual Studio 的 Azure Data Lake 工具与本地 VM 中运行的 Hortonworks 沙盒配合使用。 使用这些工具，可以在沙盒中创建和运行 Hive 与 Pig 作业，并查看作业输出和历史记录。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: e3434c45-95d1-4b96-ad4c-fb59870e2ff0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: larryfr
ms.openlocfilehash: a4c1f5a8100d5d4017e56ef129aa4f4826746868
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2018
---
# <a name="use-the-azure-data-lake-tools-for-visual-studio-with-the-hortonworks-sandbox"></a>将针对 Visual Studio 的 Azure Data Lake 工具与 Hortonworks 沙盒配合使用

Azure Data Lake 包含用于处理常规 Hadoop 群集的工具。 本文档提供将 Data Lake 工具与本地虚拟机上运行的 Hortonworks 沙盒配合使用所要执行的步骤。

借助 Hortonworks 沙盒可以在开发环境本地使用 Hadoop。 开发一个解决方案后，如果想要大规模部署该解决方案，可以转移到 HDInsight 群集。

## <a name="prerequisites"></a>先决条件

* 在开发环境上的虚拟机中运行的 Hortonworks 沙盒。 本文档是根据 Oracle VirtualBox 中运行的沙盒编写和测试的， 有关设置沙盒的详细信息，请参阅 [Hortonworks 沙盒入门](hadoop/apache-hadoop-emulator-get-started.md) 文档。

* Visual Studio 2013、Visual Studio 2015 或 Visual Studio 2017（任何版本）。

* [用于 .NET 的 Azure SDK](https://azure.microsoft.com/downloads/) 2.7.1 或更高版本。

* [针对 Visual Studio 的 Azure Data Lake 工具](https://www.microsoft.com/download/details.aspx?id=49504)。

## <a name="configure-passwords-for-the-sandbox"></a>配置沙盒的密码

确保 Hortonworks 沙盒正在运行。 然后按照 [Hortonworks 沙盒入门](hadoop/apache-hadoop-emulator-get-started.md#set-sandbox-passwords)文档中的步骤进行操作。 这些步骤配置 SSH `root` 帐户和 Ambari `admin` 帐户的密码。 从 Visual Studio 连接到沙盒时，将使用这些密码。

## <a name="connect-the-tools-to-the-sandbox"></a>将工具连接到沙盒

1. 打开 Visual Studio，选择“视图”，然后选择“服务器资源管理器”。

2. 在“服务器资源管理器”中，右键单击“HDInsight”项，并选择“连接到 HDInsight Emulator”。

    ![突出显示“连接到 HDInsight 模拟器”的服务器资源管理器屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/connect-emulator.png)

3. 在“连接到 HDInsight 模拟器”对话框中，输入为 Ambari 配置的密码。

    ![突出显示密码文本框的对话框屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/enter-ambari-password.png)

    选择“下一步”继续。

4. 使用“密码”字段输入为 `root` 帐户配置的密码。 将其他字段保留默认值。

    ![突出显示密码文本框的对话框屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/enter-root-password.png)

    选择“下一步”继续。

5. 等待服务验证完成。 在某些情况下，验证可能失败，并提示更新配置。 如果验证失败，请选择“更新”，然后等待服务的配置和验证完成。

    ![突出显示“更新”按钮的对话框屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/fail-and-update.png)

    > [!NOTE]
    > 更新过程使用 Ambari 将 Hortonworks 沙盒配置修改为针对 Visual Studio 的 Data Lake 工具所需的配置。

6. 验证完成后，选择“完成”以完成配置。
    ![突出显示“完成”按钮的对话框屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/finished-connect.png)

     >[!NOTE]
     > 根据开发环境的速度以及分配给虚拟机的内存量，可能需要几分钟时间才能完成服务的配置和验证。

完成这些步骤后，服务器资源管理器中的“HDInsight”部分下面会出现“HDInsight 本地群集”项。

## <a name="write-a-hive-query"></a>编写 Hive 查询

Hive 提供类似于 SQL 的查询语言 (HiveQL) 来处理结构化数据。 按照以下步骤了解如何针对本地群集运行按需查询。

1. 在“服务器资源管理器”中，右键单击前面添加的本地群集所对应的项，然后选择“编写 Hive 查询”。

    ![突出显示“编写 Hive 查询”的服务器资源管理器屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/write-hive-query.png)

    此时会显示一个新的查询窗口。 在其中可以快速编写查询并将其提交到本地群集。

2. 在“新建查询”窗口中，输入以下命令：

        select count(*) from sample_08;

    要运行查询，请选择窗口顶部的“提交”。 将其他值（“Batch”和服务器名称）保留为默认值。

    ![突出显示“提交”按钮的查询窗口屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/submit-hive.png)

    还可以使用“提交”旁边的下拉菜单选择“高级”。 使用高级选项，可以在提交作业时提供其他选项。

    ![“提交脚本”对话框的屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/advanced-hive.png)

3. 提交查询后，会显示作业状态。 作业状态显示 Hadoop 处理作业时有关作业的信息。 “作业状态”提供作业的状态。 状态会定期更新，也可以使用刷新图标手动刷新状态。

    ![突出显示“作业状态”的“作业视图”对话框屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/job-state.png)

    “作业状态”更改为“已完成”后，会显示有向无环图 (DAG)。 此图描述 Tez 处理 Hive 查询时确定的执行路径。 Tez 是用于本地群集上 Hive 的默认执行引擎。

    > [!NOTE]
    > 使用基于 Linux 的 HDInsight 群集时，Tez 也是默认引擎。 它不是基于 Windows 的 HDInsight 上的默认引擎。 若要在这种群集上使用 Tez，必须在 Hive 查询的开头处添加 `set hive.execution.engine = tez;` 行。

    使用“作业输出”链接查看输出。 在本例中，输出为 823，即 sample_08 表中的行数。 可以使用“作业日志”和“下载 YARN 日志”链接查看有关作业的诊断信息。

4. 还可以按交互方式运行 Hive 作业，方法是将“批处理”字段更改为“交互”。 然后选择“执行”。

    ![突出显示“交互”和“执行”按钮的屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/interactive-query.png)

    交互式查询会将处理期间生成的输出日志流式传输到“HiveServer2 输出”窗口。

    > [!NOTE]
    > 此信息与完成作业后使用“作业日志”链接所看到的信息相同。

    ![输出日志的屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/hiveserver2-output.png)

## <a name="create-a-hive-project"></a>创建 Hive 项目

还创建包含多个 Hive 脚本的项目。 当具有相关脚本或希望将脚本存储在版本控制系统中时，请使用该项目。

1. 在 Visual Studio 中，依次选择“文件”、“新建”、“项目”。

2. 在项目列表中，展开“模板”、“Azure Data Lake”，然后选择“HIVE (HDInsight)”。 在模板列表中，选择“Hive 示例”。 输入名称和位置，然后选择“确定”。

    ![突出显示“Azure Data Lake”、“HIVE”、“Hive 示例”和“确定”的“新建项目”窗口屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/new-hive-project.png)

**Hive 示例**项目包含两个脚本：**WebLogAnalysis.hql** 和 **SensorDataAnalysis.hql**。 可以使用窗口顶部的同一个“提交”按钮提交这些脚本。

## <a name="create-a-pig-project"></a>创建 Pig 项目

Hive 提供了类似 SQL 的语言用于处理结构化数据，Pig 通过对数据执行转换来工作。 Pig 提供了一种语言 (Pig Latin)，可用于开发转换管道。 若要在本地群集上使用 Pig，请执行以下步骤：

1. 打开 Visual Studio，依次选择“文件”、“新建”、“项目”。 在项目列表中，展开“模板”、“Azure Data Lake”，然后选择“Pig (HDInsight)”。 在模板列表中，选择“Pig 应用程序”。 输入名称和位置，并选择“确定”。

    ![突出显示“Azure Data Lake”、“Pig”、“Pig 应用程序”和“确定”的“新建项目”窗口屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/new-pig.png)

2. 输入以下文本作为使用此项目创建的 **script.pig** 文件内容。

        a = LOAD '/demo/data/Website/Website-Logs' AS (
            log_id:int,
            ip_address:chararray,
            date:chararray,
            time:chararray,
            landing_page:chararray,
            source:chararray);
        b = FILTER a BY (log_id > 100);
        c = GROUP b BY ip_address;
        DUMP c;

    尽管 Pig 使用的语言与 Hive 不同，但通过“提交”按钮运行作业的方式在这两种语言之间是一致的。 选择“提交”旁边的下拉列表会显示 Pig 的高级提交对话框。

    ![“提交脚本”对话框的屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/advanced-pig.png)

3. 显示的作业状态和输出也与 Hive 查询相同。

    ![已完成 Pig 作业的屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/completed-pig.png)

## <a name="view-jobs"></a>查看作业

使用 Data Lake 工具还可以轻松查看有关 Hadoop 上运行的作业的信息。 使用以下步骤可以查看本地群集上运行的作业。

1. 在“服务器资源管理器”中，右键单击本地群集，然后选择“查看作业”。 此时会显示已提交到群集的作业列表。

    ![突出显示“查看作业”的服务器资源管理器屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/view-jobs.png)

2. 在作业列表中，选择一个作业查看其详细信息。

    ![突出显示其中一个作业的作业浏览器屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/view-job-details.png)

    显示的信息类似于运行 Hive 或 Pig 查询后看到的信息，其中包括用于查看输出和日志信息的链接。

3. 还可以在此处修改和重新提交作业。

## <a name="view-hive-databases"></a>查看 Hive 数据库

1. 在“服务器资源管理器”中，展开“HDInsight 本地群集”项，并展开“Hive 数据库”。 此时会显示本地群集上的“默认”和“xademo”数据库。 展开数据库可显示该数据库中的表。

    ![展开了数据库的服务器资源管理器屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/expanded-databases.png)

2. 展开一个表可显示该表的列。 若要快速查看数据，请右键单击某个表并选择“查看前 100 行”。

    ![展开了表并选定了“查看前 100 行”的服务器资源管理器屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/view-100.png)

### <a name="database-and-table-properties"></a>数据库和表属性

可以查看数据库或表的属性。 选择“属性”会在属性窗口中显示选定项的详细信息。 有关示例，请参阅以下屏幕截图中显示的信息：

![“属性”窗口的屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/properties.png)

### <a name="create-a-table"></a>创建表

若要创建表，请右键单击某个数据库，然后选择“创建表”。

![突出显示“创建表”的服务器资源管理器屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/create-table.png)

然后，可以使用表单创建表。 在以下屏幕截图的底部，可以看到用于创建表的原始 HiveQL。

![用于创建表的表单屏幕截图](./media/hdinsight-hadoop-emulator-visual-studio/create-table-form.png)

## <a name="next-steps"></a>后续步骤

* [学习 Hortonworks 沙盒的重要知识](http://hortonworks.com/hadoop-tutorial/learning-the-ropes-of-the-hortonworks-sandbox/)
* [Hadoop 教程 - HDP 入门](http://hortonworks.com/hadoop-tutorial/hello-world-an-introduction-to-hadoop-hcatalog-hive-and-pig/)
