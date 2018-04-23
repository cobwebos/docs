---
title: 使用 psql 将数据批量载入 Phoenix - Azure HDInsight | Microsoft Docs
description: 使用 psql 工具将数据批量载入 Phoenix 表。
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: ashishth
ms.openlocfilehash: 2c192707c6cf8f84d2ca1c0307770cadd5cdb8bd
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2018
---
# <a name="bulk-load-data-into-phoenix-using-psql"></a>使用 psql 将数据大容量加载到 Phoenix

[Apache Phoenix](http://phoenix.apache.org/) 是构建在 [HBase](../hbase/apache-hbase-overview.md) 基础之上的开源大规模并行关系数据库。 Phoenix 通过 HBase 提供类似于 SQL 的查询。 Phoenix 使用 JDBC 驱动程序，可让用户创建、删除和更改 SQL 表、索引、视图与序列，以及单独或批量更新插入行。 Phoenix 使用 noSQL 本机编译而不是 MapReduce 来编译查询，以及在 HBase 的顶层创建低延迟的应用程序。 Phoenix 添加了协处理器，支持在服务器的地址空间中运行客户端提供的代码，执行与数据共置的代码。 这样可将客户端/服务器数据传输延迟降到最低。  若要在 HDInsight 中使用 Phoenix 处理数据，请先创建表，然后将数据载入这些表。

## <a name="bulk-loading-with-phoenix"></a>使用 Phoenix 批量加载数据

可通过多种方法将数据载入 HBase，包括使用客户端 API、结合 TableOutputFormat 的 MapReduce 作业，或使用 HBase  Shell 手动输入数据。 Phoenix 提供两种方法用于将 CSV 数据载入 Phoenix 表：一个名为 `psql` 的客户端加载工具，以及一个基于 MapReduce 的批量加载工具。

`psql` 是单线程工具，最适合用于加载 MB 甚至 GB 量级的数据。 所有要加载的 CSV 文件都必须采用“.csv”扩展名。  也可以在 `psql` 命令行中指定包含“.sql”扩展名的 SQL 脚本文件。

由于 MapReduce 使用多个线程，对于极大型的数据卷（通常用于生产方案），可以使用 MapReduce 执行批量加载。

在开始加载数据之前，请先确认是否已启用 Phoenix，并且查询超时设置是否符合预期。  请访问 HDInsight 群集的 Ambari 仪表板，然后依次选择“HBase”和“配置”选项卡。向下滚动，确认 Apache Phoenix 是否已设置为 `enabled`，如下所示：

![Apache Phoenix HDInsight 群集设置](./media/apache-hbase-phoenix-psql/ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>使用 `psql` 批量加载表

1. 创建新表，然后以 `createCustomersTable.sql` 文件名保存查询。

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

2. 将 CSV 文件（图中显示了示例内容）作为 `customers.csv` 复制到 `/tmp/` 目录，以便载入到新建的表中。  使用 `hdfs` 命令将该 CSV 文件复制到所需的源位置。

    ```
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,Norway
    ... 4997 more rows 
    ```

    ```bash
    hdfs dfs -copyToLocal /example/data/customers.csv /tmp/
    ```

3. 创建 SQL SELECT 查询来确认是否已正确加载输入数据，然后以 `listCustomers.sql` 文件名保存该查询。 可以使用任何 SQL 查询。
     ```sql
    SELECT Name, Income from Customers group by Country;
    ```

4. 打开一个新的 Hadoop 命令窗口，以批量加载数据。 首先，使用 `cd` 命令切换到执行目录位置，然后使用 `psql` 工具（Python `psql.py` 命令）。 

    以下示例要求已使用 `hdfs` 将 `customers.csv` 文件从存储帐户复制到本地临时目录，如上面的步骤 2 所示。

    ```bash
    cd /usr/hdp/current/phoenix-client/bin

    python psql.py ZookeeperQuorum createCustomersTable.sql /tmp/customers.csv listCustomers.sql
    ```

    > [!NOTE] 
    > 若要确定 `ZookeeperQuorum` 名称，请在 `/etc/hbase/conf/hbase-site.xml` 文件中，使用属性名称 `hbase.zookeeper.quorum` 找到 Zookeeper 仲裁字符串。

5. 完成 `psql` 操作后，命令窗口中应会显示一条消息：

    ```
    CSV Upsert complete. 5000 rows upserted
    Time: 4.548 sec(s)
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>使用 MapReduce 批量加载表

若要进行遍布整个群集的更高吞吐量加载，可以使用 MapReduce 加载工具。 此加载程序先将所有数据转换为 HFile，然后将所创建的 HFile 提供给 HBase。

1. 使用 `hadoop` 命令配合 Phoenix 客户端 jar 来启动 CSV MapReduce 加载程序：

    ```bash
    hadoop jar phoenix-<version>-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table CUSTOMERS --input /data/customers.csv
    ```

2. 使用 SQL 语句创建新表，如同在上述步骤 1 中使用 `CreateCustomersTable.sql` 一样。

3. 若要验证表的架构，请运行 `!describe inputTable`。

4. 确定输入数据（例如示例 `customers.csv` 文件）的位置路径。 输入文件可能在 WASB/ADLS 存储帐户中。 在此示例方案中，输入文件在 `<storage account parent>/inputFolderBulkLoad` 目录中。

5. 切换到 MapReduce 批量加载命令的执行目录：

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

6. 在 `/etc/hbase/conf/hbase-site.xml` 中，使用属性名称 `hbase.zookeeper.quorum` 找到 `ZookeeperQuorum` 值。

7. 设置 classpath，然后运行 `CsvBulkLoadTool` 工具命令：

    ```bash
    /usr/hdp/current/phoenix-client$ HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /inputFolderBulkLoad/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure
    ```

8. 若要将 MapReduce 与 ADLS 配合使用，请找到 ADLS 根目录，即 `hbase-site.xml` 中的 `hbase.rootdir` 值。 在以下命令中，ADLS 根目录是 `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`。 在此命令中，指定 ADLS 输入和输出文件夹作为参数：

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

## <a name="recommendations"></a>建议

* 对于输入和输出文件夹，请使用相同的存储媒体（WASB 或 ADLS）。 若要将数据从 WASB 传输到 ADLS，可以使用 `distcp` 命令：

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* 使用更大的工作节点。 MapReduce 批量复制过程的映射进程会生成大量的暂时输出，导致填满可用的非 DFS 空间。 若要批量加载大量数据，请使用更多且更大的工作节点。 分配给群集的工作节点数直接影响处理速度。

* 将输入文件拆分成大约 10 GB 的区块。 批量加载是存储密集型操作，因此将输入文件拆分成多个区块可以提高性能。

* 避免区域服务器作用点。 如果行键单调递增，HBase 顺序写入可能导致生成区域服务器作用点。 对行键进行加盐处理可以减少顺序写入。 Phoenix 提供一种可在后台以加盐字节对特定表的行键进行加盐处理的方法，如以下参考文档中所述。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Phoenix 进行批量数据加载](http://phoenix.apache.org/bulk_dataload.html)
* [将 Apache Phoenix 与 HDInsight 中基于 Linux 的 HBase 群集配合使用](../hbase/apache-hbase-phoenix-squirrel-linux.md)
* [加盐表](https://phoenix.apache.org/salted.html)
* [Phoenix 语法](http://phoenix.apache.org/language/index.html)
