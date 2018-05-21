---
title: 使用 Ambari 视图操作 HDInsight (Hadoop) 上的 Hive — Azure | Microsoft Docs
description: 了解如何在 Web 浏览器中使用 Hive 视图提交 Hive 查询。 Hive 视图是基于 Linux 的 HDInsight 群集随附提供的 Ambari Web UI 的一部分。
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: larryfr
ms.openlocfilehash: 6c07e9a45cbfbc5e6ed6787277dcfa1bf4cf4b2b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="use-ambari-hive-view-with-hadoop-in-hdinsight"></a>将 Ambari Hive 视图与 HDInsight 中的 Hadoop 配合使用

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

了解如何使用 Ambari Hive 视图运行 Hive 查询。 Hive 视图允许从 Web 浏览器创作、优化和运行 Hive 查询。

## <a name="prerequisites"></a>先决条件

* 基于 Linux 的 Hadoop on HDInsight 群集版本 3.4 或更高版本。

  > [!IMPORTANT]
  > Linux 是 HDInsight 3.4 或更高版本上使用的唯一操作系统。 有关详细信息，请参阅 [HDInsight 在 Windows 上停用](../hdinsight-component-versioning.md#hdinsight-windows-retirement)。

* Web 浏览器

## <a name="run-a-hive-query"></a>运行 Hive 查询

1. 打开 [Azure 门户](https://portal.azure.com)。

2. 选择 HDInsight 群集，然后从“快速链接”部分选择“Ambari 视图”。

    ![门户快速链接部分](./media/apache-hadoop-use-hive-ambari-view/quicklinks.png)

    当提示进行身份验证时，请使用在创建群集时所提供的群集登录名（默认为 `admin`）帐户名称和密码。

3. 在视图列表中，选择“Hive 视图”。

    ![已选中 Hive 视图](./media/apache-hadoop-use-hive-ambari-view/select-hive-view.png)

    Hive 视图页面类似于下图：

    ![Hive 视图查询工作表图像](./media/apache-hadoop-use-hive-ambari-view/ambari-hive-view.png)

4. 将以下 HiveQL 语句从“查询”选项卡粘贴到工作表中：

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(
        t1 string,
        t2 string,
        t3 string,
        t4 string,
        t5 string,
        t6 string,
        t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS loglevel, COUNT(*) AS count FROM log4jLogs 
        WHERE t4 = '[ERROR]' 
        GROUP BY t4;
    ```

    这些语句将执行以下操作：

   * `DROP TABLE`：删除表和数据文件（如果该表已存在）。

   * `CREATE EXTERNAL TABLE`：在 Hive 中创建一个新的“外部”表。
   外部表仅在 Hive 中存储表定义。 数据将保留在原始位置。

   * `ROW FORMAT`：演示如何设置数据格式。 在此情况下，每个日志中的字段以空格分隔。

   * `STORED AS TEXTFILE LOCATION`：显示数据的存储位置，并且数据已存储为文本。

   * `SELECT`：选择 t4 列包含值 [ERROR] 的所有行的计数。

    > [!IMPORTANT]
    > 将“数据库”选择保留为“默认”。 本文档中的示例使用 HDInsight 附带的默认数据库。

5. 要启动查询，请使用工作表下方的“执行”按钮。 按钮变为橙色，文本更改为“停止”。

6. 完成查询后，“结果”选项卡显示操作结果。 以下文本是查询结果：

        loglevel       count
        [ERROR]        3

    可使用“日志”选项卡查看作业创建的日志记录信息。

   > [!TIP]
   > 通过位于“查询处理结果”部分左上角的“保存结果”下拉对话框，可下载或保存结果。

### <a name="visual-explain"></a>可视化说明

要显示查询计划的可视化效果，选择工作表下方的“可视化说明”选项卡。

查询的**可视化说明**视图可帮助理解复杂查询的流。 可使用查询编辑器上的“说明”按钮查看此视图的等效文本。

### <a name="tez-ui"></a>Tez UI

要显示查询的 Tez UI，选择工作表下方的“Tez”选项卡。

> [!IMPORTANT]
> Tez 不用于解析所有查询。 无需使用 Tez 即可解析许多查询。 

如果使用 Tez 来解析查询，会显示有向无环图 (DAG)。 若要查看之前运行的查询的 DAG，或调试 Tez 进程，请改用 [Tez 视图](../hdinsight-debug-ambari-tez-view.md)。

## <a name="view-job-history"></a>查看作业历史记录

“作业”选项卡显示 Hive 查询的历史记录。

![作业历史记录图像](./media/apache-hadoop-use-hive-ambari-view/job-history.png)

## <a name="database-tables"></a>数据库表

可使用“表”选项卡处理 Hive 数据库内的表。

![表选项卡图像](./media/apache-hadoop-use-hive-ambari-view/tables.png)

## <a name="saved-queries"></a>已保存的查询

在“查询”选项卡中，可以按需要保存查询。 保存查询后，可通过“已保存的查询”选项卡对其重复进行使用。

![“保存的查询”选项卡图像](./media/apache-hadoop-use-hive-ambari-view/saved-queries.png)

> [!TIP]
> 保存的查询存储在默认群集存储中。 可在路径 `/user/<username>/hive/scripts` 下找到保存的查询。 它们存储为纯文本 `.hql` 文件。
>
> 如果删除群集但保留存储，可使用 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)或 Data Lake 存储资源管理器（通过 [Azure 门户](https://portal.azure.com)）等实用工具来检索查询。

## <a name="user-defined-functions"></a>用户定义的函数

可以通过用户定义函数 (UDF) 扩展 Hive。 使用 UDF 实现 HiveQL 中不容易建模的功能或逻辑。

使用 Hive 视图顶部的“UDF”选项卡，声明并保存一组 UDF。 可以在**查询编辑器**中使用这些 UDF。

![UDF 选项卡图像](./media/apache-hadoop-use-hive-ambari-view/user-defined-functions.png)

将 UDF 添加到 Hive 视图后，“插入 UDF”按钮将显示在“查询编辑器”底部。 选择此项会显示 Hive 视图中定义的 UDF 的下拉列表。 选择 UDF 会将 HiveQL 语句添加到查询以启用 UDF。

例如，如果已将 UDF 定义为具有以下属性：

* 资源名称：myudfs

* 资源路径：/myudfs.jar

* UDF 名称：myawesomeudf

* UDF 类名：com.myudfs.Awesome

使用“插入 UDF”按钮将显示名为 myudfs 的条目，以及为该资源定义的每个 UDF 的另一下拉列表。 本例中为 myawesomeudf。 选择此条目会将以下各项添加到查询的开头部分：

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

然后可在查询中使用 UDF。 例如，`SELECT myawesomeudf(name) FROM people;`。

有关如何在 HDInsight 中将 UDF 与 Hive 配合使用的详细信息，请参阅以下文章：

* [在 HDInsight 中将 Python 与 Hive 和 Pig 配合使用](python-udf-hdinsight.md)
* [如何将自定义 Hive UDF 添加到 HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="hive-settings"></a>Hive 设置

可以更改各种 Hive 设置，例如将 Hive 的执行引擎从 Tez（默认）更改为 MapReduce。

## <a id="nextsteps"></a>后续步骤

有关 HDInsight 中 Hive 的常规信息：

* [将 Hive 与 Hadoop on HDInsight 配合使用](hdinsight-use-hive.md)

有关 HDInsight 上的 Hadoop 的其他使用方法的信息：

* [将 Pig 与 Hadoop on HDInsight 配合使用](hdinsight-use-pig.md)
* [将 MapReduce 与 HDInsight 上的 Hadoop 配合使用](hdinsight-use-mapreduce.md)
