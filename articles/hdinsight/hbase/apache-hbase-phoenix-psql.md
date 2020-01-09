---
title: 使用 psql 将数据批量载入 Apache Phoenix - Azure HDInsight
description: 使用 psql 工具将数据批量加载到 Azure HDInsight 中的 Apache Phoenix 表中
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: 845c4a62aee04a8acdc645ba4c41f1f5496537c3
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552604"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>使用 psql 将数据批量加载到 Apache Phoenix

[Apache Phoenix](https://phoenix.apache.org/) 是构建在 [Apache HBase](../hbase/apache-hbase-overview.md) 基础之上的开源大规模并行关系数据库。 Phoenix 通过 HBase 提供类似于 SQL 的查询。 Phoenix 使用 JDBC 驱动程序，可让用户创建、删除和更改 SQL 表、索引、视图与序列，以及单独或批量更新插入行。 Phoenix 使用 noSQL 本机编译而不是 MapReduce 来编译查询，以及在 HBase 的顶层创建低延迟的应用程序。 Phoenix 添加了协处理器，支持在服务器的地址空间中运行客户端提供的代码，执行与数据共置的代码。 这样可将客户端/服务器数据传输延迟降到最低。  若要在 HDInsight 中使用 Phoenix 处理数据，请先创建表，然后将数据载入这些表。

## <a name="bulk-loading-with-apache-phoenix"></a>使用 Apache Phoenix 大容量加载数据

可通过多种方法将数据载入 HBase，包括使用客户端 API、结合 TableOutputFormat 的 MapReduce 作业，或使用 HBase  Shell 手动输入数据。 Phoenix 提供两种方法用于将 CSV 数据载入 Phoenix 表：一个名为 `psql` 的客户端加载工具，以及一个基于 MapReduce 的批量加载工具。

`psql` 是单线程工具，最适合用于加载 MB 甚至 GB 量级的数据。 所有要加载的 CSV 文件都必须采用“.csv”扩展名。  也可以在 `psql` 命令行中指定包含“.sql”扩展名的 SQL 脚本文件。

由于 MapReduce 使用多个线程，对于极大型的数据卷（通常用于生产方案），可以使用 MapReduce 执行批量加载。

在开始加载数据之前，请先确认是否已启用 Phoenix，并且查询超时设置是否符合预期。  访问 HDInsight 群集[Apache Ambari](https://ambari.apache.org/)仪表板，选择 "HBase"，然后选择 "配置" 选项卡。 向下滚动以验证 Apache Phoenix 设置为 `enabled`，如下所示：

![Apache Phoenix HDInsight 群集设置](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>使用 `psql` 批量加载表

1. 创建一个名为 `createCustomersTable.sql`的文件，并将下面的代码复制到该文件中。 然后保存并关闭该文件。

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. 创建一个名为 `listCustomers.sql`的文件，并将下面的代码复制到该文件中。 然后保存并关闭该文件。

    ```sql
    SELECT * from Customers;
    ```

1. 创建一个名为 `customers.csv`的文件，并将下面的代码复制到该文件中。 然后保存并关闭该文件。

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. 创建一个名为 `customers2.csv`的文件，并将下面的代码复制到该文件中。 然后保存并关闭该文件。

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. 打开命令提示符并将目录更改为新创建的文件的位置。 将下面的 CLUSTERNAME 替换为 HBase 群集的实际名称。 然后执行代码，将文件上传到群集的头节点：

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. 使用[ssh 命令](../hdinsight-hadoop-linux-use-ssh-unix.md)连接到群集。 将 CLUSTERNAME 替换为群集名称，然后输入以下命令，以编辑以下命令：

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. 从 ssh 会话中，将目录更改为**psql**工具的位置。 执行以下命令：

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. 大容量加载数据。 下面的代码将创建**Customers**表，然后上传数据。

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    `psql` 操作完成后，应会看到类似于下面的消息：

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. 您可以继续使用 `psql` 查看 Customers 表的内容。 执行下面的代码：

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    或者，可以使用[HBase shell](./query-hbase-with-hbase-shell.md)或[Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md)来查询数据。

1. 上传其他数据。 既然该表已经存在，则该命令将指定该表。 执行以下命令：

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>使用 MapReduce 批量加载表

若要进行遍布整个群集的更高吞吐量加载，可以使用 MapReduce 加载工具。 此加载程序先将所有数据转换为 HFile，然后将所创建的 HFile 提供给 HBase。

1. 此部分继续执行 ssh 会话和前面创建的对象。 使用上面的步骤根据需要创建**customers**表和**customers .csv**文件。 如有必要，请重新建立 ssh 连接。

1. 截断**Customers**表的内容。 在打开的 ssh 会话中，执行以下命令：

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. 将 `customers.csv` 文件从头节点复制到 Azure 存储。

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. 切换到 MapReduce 批量加载命令的执行目录：

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. 使用 `hadoop` 命令配合 Phoenix 客户端 jar 来启动 CSV MapReduce 加载程序：

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    上传完成后，应会看到类似如下的消息：

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. 若要配合使用 MapReduce 和 Azure Data Lake Storage，请查找 Data Lake Storage 根目录，即 `hbase-site.xml` 中的 `hbase.rootdir` 值。 在以下命令中，Data Lake Storage 根目录是 `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1`。 在此命令中，指定 Data Lake Storage 输入和输出文件夹作为参数：

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. 若要查询和查看数据，可以使用前面所述的**psql** 。 你还可以使用[HBase shell](./query-hbase-with-hbase-shell.md)或[Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md)。

## <a name="recommendations"></a>建议

* 对于输入和输出文件夹，请使用相同的存储媒体，Azure 存储 (WASB) 或者 Azure Data Lake Storage (ADL)。 若要将数据从 Azure 存储转移到 Data Lake Storage，可以使用 `distcp` 命令：

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* 使用更大的工作节点。 MapReduce 批量复制过程的映射进程会生成大量的暂时输出，导致填满可用的非 DFS 空间。 若要批量加载大量数据，请使用更多且更大的工作节点。 分配给群集的工作节点数直接影响处理速度。

* 将输入文件拆分成大约 10 GB 的区块。 批量加载是存储密集型操作，因此将输入文件拆分成多个区块可以提高性能。

* 避免区域服务器作用点。 如果行键单调递增，HBase 顺序写入可能导致生成区域服务器作用点。 对行键进行加盐处理可以减少顺序写入。 Phoenix 提供一种可在后台以加盐字节对特定表的行键进行加盐处理的方法，如以下参考文档中所述。

## <a name="next-steps"></a>后续步骤

* [使用 Apache Phoenix 进行批量数据加载](https://phoenix.apache.org/bulk_dataload.html)
* [将 Apache Phoenix 与 HDInsight 中基于 Linux 的 Apache HBase 群集配合使用](../hbase/apache-hbase-query-with-phoenix.md)
* [加盐表](https://phoenix.apache.org/salted.html)
* [Apache Phoenix 语法](https://phoenix.apache.org/language/index.html)
